# Submissions Extensions



## Annotating submitted XML data with xxf:annotate

### Introduction

The optional `xxf:annotate` may contain tokens which specify whether to annotate some elements in the submitted XML data.
 
This only allows annotating elements, not attributes.

### Errors, warnings and informational messages

[SINCE Orbeon Forms 4.6]

The `xxf:annotate` attribute's `error`, `warning` and `info` tokens specify that elements with failed error, warning
or information constraints must be annotated. For example, the following annotates failed error and warnings:

```xml
<xf:submission
    id="my-submission"
    method="post"
    xxf:annotate="error warning"
    .../>
```

By default, the attributes are called `xxf:error`, `xxf:warning` and `xxf:info` and are in the `http://orbeon.org/oxf/xml/xforms` namespace.

The resulting XML will contain annotations like this:

```
<my-data xmlns:xxf="http://orbeon.org/oxf/xml/xforms">
    <text xxf:error="The value must contain at most 140 characters"/>
    <number xxf:warning="The value should probably be larger"/>
</my-data>
```

[SINCE Orbeon Forms 2017.1]

You can specify a custom attribute name. For example:

```xml
<xf:submission
    id="my-submission"
    method="post"
    xxf:annotate="error=foo:error warning=bar:warning"
    .../>
```

In this example, the `foo` and `bar` prefixes must be in scope on the `<xf:submission>` element or one of its ancestors.

### Element relevance information

[SINCE Orbeon Forms 2017.1]

The `xxf:annotate` attribute's `relevant` token specifies that elements in the submitted XML data must be annotated with
an attribute when the element is not relevant.

- This only applies when the `nonrelevant` attribute on the submission is set to `keep` or `empty` (or when the backward
compatibility `relevant` attribute is set to `false`).
- The attribute is only placed when elements are not relevant. This means that the value of the attribute is always `false`.
- By default, the attribute is called `xxf:relevant` and is in the `http://orbeon.org/oxf/xml/xforms` namespace.
  
Basic example:

```xml
<xf:submission
    id="my-submission"
    method="post"
    nonrelevant="keep"
    xxf:annotate="relevant"
    ...>
```

The resulting XML will contain annotations like this for non-relevant elements:

```xml
```
<my-data xmlns:xxf="http://orbeon.org/oxf/xml/xforms">
    <street xxf:relevant="false">Main Street</street>
</my-data>
```

You can specify a custom attribute name. For example, Form Runner uses:

```xml
<xf:submission
    xmlns:fr="http://orbeon.org/oxf/xml/form-runner"
    id="my-submission"
    nonrelevant="keep"
    xxf:annotate="relevant=fr:relevant"
    method="post"
    ...>
```

In this example, the `fr` prefix must be in scope on the `<xf:submission>` element or one of its ancestors.

The resulting XML will contain annotations like this:

```xml
<my-data xmlns:fr="http://orbeon.org/oxf/xml/form-runner">
    <street fr:relevant="false">Main Street</street>
</my-data>
```

Before the document is annotated, any occurrence of the `xxf:relevant` (or custom) attribute is removed to ensure
consistency.

## Read-only XForms instances with xxf:readonly

Orbeon Forms supports an extension attribute, `xxf:readonly`, on the `<xf:instance>` and <xf:submission> elements. When set to `true`, this attribute signals that once loaded, the instance is read-only, with the following consequences:

* The instance is loaded into a smaller, more efficient, read-only data structure in memory.
* Instance values _cannot_ be updated, no Model Item Properties (MIPs) can be assigned with `<xf:bind>` to the instance, and the read-only MIP is set to `true` for all the nodes in the instance. But a read-only instance can be replaced entirely by an `<xf:submission replace="instance">`.
* When using client-side state handling, less data might be transmitted between server and client.

Read-only instances are particularly appropriate for loading internationalization resources, which can be large but don't change.

Example:

```xml
<xf:instance
  id="resources-instance"
  src="/forms/resources/en"
  xxf:readonly="true"/>
```

The `xxf:readonly` attribute on `<xf:instance>` determines if the instance is read-only until that instance is being replaced.

After an instance is replaced, it can be read-only or not irrelevant of the of `xxf:readonly` on `<xf:instance>`. When the instance is replaced, the replaced instance is read-only if and only if the `<xf:submission>` that does the replacement has a attribute `xxf:readonly="true"`.

When this attribute is set to true on `<xf:submission>` and if the `targetref` attribute is specified, the replacement target must be an instance's root element.

## HTTP authentication

### Username, password and domain

The `<xf:submission>` and` <xf:instance>` elements support optional attributes to specify HTTP authentication credentials:

* `xxf:username`: HTTP authentication username
* `xxf:password`: HTTP authentication password
* `xxf:domain`: domain for NTLM authentication

If `xxf:username` is missing or empty, the other authentication attributes are ignored.

If you specify the `xxf:username` without the `xxf:password`, the password defaults to an empty string.

```xml
<xf:submission
    id="save-submission"
    ref="instance('my-instance')"
    method="put"
    resource="/exist/rest/ops/my-file.xml"
    replace="none"
    xxf:username="admin"
    xxf:password=""/>
```

_NOTE: On  `<xf:instance>`, the attribute is statically-defined. On `<xf:submission>`, it is an AVT and can therefore be dynamic._

### Preemptive authentication

By default, the username and password are provided preemptively to the connection. In this mode, the HTTP client "will send the basic authentication response even before the server gives an unauthorized response in certain situations, thus reducing the overhead of making the connection" ([HttpClient documentation][3]).

The `xxf:preemptive-authentication` attribute on  `<xf:submission>` and `<xf:instance>` elements allows controlling this feature. You can disable this feature by setting the property to `false`:

```xml
<xf:submission xxf:preemptive-authentication="false" ...>
```
_NOTE: On  `<xf:instance>`, the attribute is statically-defined. On `<xf:submission>`, it is an AVT and can therefore be dynamic._

## HTTP headers forwarding

### What this does

HTTP requests initiated by `<xf:submission>` and `<xf:instance>` can automatically forward incoming HTTP headers.

_SECURITY NOTE: Forwarding authentication-related headers may cause a security risks when communicated with non-trusted servers. Use carefully!_

### Configuration

You do this with the global [`oxf.http.forward-headers`](../configuration/properties/general.md#headers-forwarding) property.

The property contains a space-separated list of header names to forward:

```xml
<property
  as="xs:string"
  name="oxf.http.forward-headers"
  value="My-Header-1 My-Header-2"/>
```

### Lifecycle

Whenever an XForms document initializes, it saves the incoming HTTP headers to its internal state. This means that instance or submission header forwarding always uses the HTTP headers which were present *at the time the XForms page was loaded*. HTTP headers present during subsequent Ajax requests are ignored.

Whenever an HTTP request must be performed in relation to an instance or submission, the XForms engine looks at the list of header names and it forwards the header value if the following conditions are met:

- There is an incoming header with that name.
- There is no author-specified header with the same name in an `<xf:header>` element within `<xf:submission>`.

### The Authorization header

The `Authorization` header is treated specially: if a username is specified on the submission with `xxf:username`, then this header is not forwarded.

Forwarding the `Authorization` or other authentication-related headers can be useful to propagate authentication credentials to other services, but it can also be unsafe.

### Setting headers with a servlet filter

Often it is necessary to set a header for further use by Orbeon Forms. This can be done for example by a proxy or a servlet filter. In the latter case, you have to make sure you override all the relevant methods of the Java servlet API's `HttpServletRequestWrapper`:
 
```java
getHeaderNames()
getHeader(String name)
getHeaders(String name)
```

### Compatibility notes

Since Orbeon Forms 4.9, `oxf.xforms.forward-submission-headers` is deprecated. Use `oxf.http.forward-headers` instead. For backward compatibility, header names from both properties are combined into a single set of header names. It is no longer possible to specify per-form forwarding headers using `xxf:forward-submission-headers`.

Prior to Orbeon Forms 4.9, the two properties were looked at in order:

1. The local, XForms-specific `oxf.xforms.forward-submission-headers` property. [DEPRECATED]
2. The global Orbeon Forms [`oxf.http.forward-headers`](../configuration/properties/general.md#headers-forwarding) property (used only as a default if `oxf.xforms.forward-submission-headers` is not set).

### Per-page forwarding settings (removed)

[REMOVED as of Orbeon Forms 4.9]

`oxf.xforms.forward-submission-headers` can also be set on a per-page basis on your first model element: 

```xml
<xf:model
  xxf:forward-submission-headers="Orbeon-Client Authorization SM_USER">
    ...
</xf:model>
```

## Loading indicator

When an `<xf:submission>` with `replace="all"` is executed, in general, the browser will load another page. While this happens, the loading indicator, by default shown in red at the top right of the window, is displayed. However, when the browser is served not a web page but say a ZIP file, the browser might ask you in you want to download it, and then stay in the current page. When this happens, the loading indicator does not go away.

In those cases where you know that the target page does not replace the current page, you can prevent the loading indicator from being displayed by adding the `xxf:show-progress="false"`attribute. [Since Orbeon Forms 2017.1] The value of the `xxf:target` attribute is interpreted as an AVT.

Similarly the `xxf:show-progress="false"` attribute can be used with the `xf:load` action.

## Target window or frame

You can use the `xxf:target` attribute on both `<xf:submission>` and `xf:load`. It behaves just like the [HTML target attribute][5]. When used on `<xf:submission>`, it only makes sense to use this attribute when you have `replace="all"`. Using this attribute to load a page in a new page is a case where you should add the `xxf:show-progress="false" `attribute. The value of the `xxf:target` attribute is interpreted as an AVT.

When a submission runs in response to a user action, say a click on a button, an Ajax request is sent by the browser to the server. Then, based on the Ajax response, JavaScript runs submitting a `<form>` with a `target` attribute . Browsers implement popup blockers that prevent attempt made by JavaScript to open new windows, and this unless the [JavaScript code run in response to a trusted event](https://developer.mozilla.org/en-US/docs/Web/API/Event/isTrusted). A trusted event is one that happened in response to a user action, such as clicking on a button. However, even if your submission runs in response a user action, as it happens in response to an Ajax request, some browsers lose track that it was started by a trusted event, and those browsers might prevent the form submission. This is the case with Safari and Firefox (but not with Chrome, IE, and Edge).

- [UP TO Orbeon Forms 2016.3] On those browsers, the new tab or window will fail to open.
- [SINCE Orbeon Forms 2017.1] Orbeon Forms detects that the browser prevents it from opening a new tab or window, and instead loads the resource in the current tab or window.   
 
## Replacing other instances with the xxf:instance attribute

On an `<xf:submission>` element with `replace="instance"`, the optional `instance` attribute specifies a destination instance for the result. That attribute is processed like the `instance()`function, which means that the instance specified must be in the current model.

The `xxf:instance` extension attribute can be use instead of the standard `instance` attribute. It works like `instance`, except that the instance is searched globally among all models. `xxf:instance` is to the `instance` attribute what the [`xxf:instance()`][3] function is to the standard `instance()` function.

```xml
<xf:submission 
    id="my-submission" 
    method="post" 
    resource="http://example.org/"
    replace="instance" 
    xxf:instance="my-instance"/>
```

## Enabling XInclude processing with the xxf:xinclude attribute

On an `<xf:submission>` element with `replace="instance"`, the optional `xxf:xinclude` attribute specifies whether XInclude processing should be performed on the XML document returned, before storing it into the destination instance. The default is `false`.

```xml
<xf:submission 
    id="my-submission" 
    method="post" 
    resource="http://example.org/"
    replace="instance" 
    xxf:xinclude="true"/>
```

## Preventing recalculation before a submission

_NOTE: Since Orbeon Forms 4.7, `recalculate` and `revalidate` are unified in Orbeon Forms. This means that this attribute is no longer needed._

XForms 1.1 provides two attributes to control pre-submission tasks:

- `validate`: "indicates whether or not the data validation checks of the submission are performed".
- `relevant`: "indicates whether or not the relevance pruning of the submission is performed"
    - _NOTE: XForms 2.0 introduces instead `nonrelevant`._ 

Orbeon Forms adds the following attribute:

- `xxf:calculate`: indicates whether or not recalculation must take place

The default value is `false` if the value of serialization is `none` and `true` otherwise.

The purpose of the attribute is to improve performance when multiple submission are called serially. The form author can this way completely prevent the `rebuild`, `recalculate` and `revalidate` flags from being checked before submitting data:

```xml
<xf:submission 
    ref="instance()" 
    method="post"
    validate="false" 
    xxf:calculate="false"
    nonrelevant="keep" 
    .../>
```

_WARNING: This attribute must be used with caution, as using it might mean you submit information that is out of date._

Here is how Orbeon Forms performs the `rebuild`, `recalculate` and `revalidate` operations before a submission:

* Perform rebuild if:
    * the deferred flag for `rebuild` is set
    * and the data to submit belongs to an instance (as opposed to a non-instance XML node)
    * and either of the effective value of the validate, relevant or xxf:calculate attributes is true
* Perform recalculate if:
    * the deferred flag for `recalculate` is set
    * and the data to submit belongs to an instance (as opposed to a non-instance XML node)
    * and either of the effective value of the relevant or xxf:calculate attributes is true
* Perform revalidate if:
    * the deferred flag for `revalidate` is set
    * and the data to submit belongs to an instance (as opposed to a non-instance XML node)
    * and the final effective of the validate attribute is true

The "effective value" for the `validate`, `relevant` and `xxf:calculate` attributes is the value after considering:

* each attribute's default value
* resolution of AVTs

## Submitting non-XML content

### Submitting text content

Orbeon Forms supports sending the text content of an XML document as per [XSLT 2.0 and XQuery 1.0 Serialization][6]. To perform a text submission:

* The `post` or `put` method is required.
* You must use a the `text/plain` value for the `serialization` attribute.


```xml
<xf:instance id="instance">
    <text>
        This contains some text. The<b>string value</b>of the document will be sent
    </text>
</xf:instance>
...
<xf:submission 
    id="save-submission" 
    ref="instance()" 
    method="post" 
    serialization="text/plain" 
    replace="none"
    resource="http://example.com/foo.text"/>
...
```

### Submitting HTML or XHTML content

Orbeon Forms supports sending an XML document as HTML or XHTML as per [XSLT 2.0 and XQuery 1.0 Serialization][6]. To perform a HTML or XHTML submission:

* The `post` or `put` method is required.
* You must use a the `text/html` or the `application/xhtml+xml` value for the `serialization` attribute.

```xml
<xf:instance id="instance">
    <html>
        <head>
            <title>My page</title>
        </head>
        <body>
            <p>Cool HTML!</p>
        </body>
    </html>
</xf:instance>
...
<xf:submission 
    id="save-submission"
    ref="instance()" 
    method="post" 
    serialization="text/html" 
    replace="none"
    resource="http://example.com/foo.html"/>
...
```

### Submitting binary content

XForms 1.1 does not explicitly support submitting binary content, but does not prohibit it either. Orbeon Forms supports sending the content of a binary resource specified by a URI. Such resources are easily obtained with `<xf:upload>`, for example. To perform a binary submission:

* The `post` or `put` method is required.
* You must use `application/octet-stream` as `serialization` attribute.
* The node referred to by the submission must be of type `xs:anyURI`.
* Relative URLs are supported and resolved as service URLs against the `<xf:submission>` element.

```xml
<xf:instance id="attachment">
    <attachment>
        file:/Users/jdoe/Applications/apache-tomcat-5.5.20/temp/xforms_upload_30877.tmp
    </attachment>
</xf:instance>
<xf:bind ref="instance('attachment')" type="xs:anyURI"/>
...
<xf:submission 
    id="save-submission" 
    ref="instance('attachment')" 
    method="put" 
    serialization="application/octet-stream"
    replace="none" 
    resource="http://example.com/foo.bin"/>
...
```

Alternatively, you can set the type information using the `xsi:type` attribute:

```xml
<xf:instance id="attachment">
    <attachment xsi:type="xs:anyURI">
        file:/Users/jdoe/Applications/apache-tomcat-5.5.20/temp/xforms_upload_30877.tmp
    </attachment>
</xf:instance>
```

## The "echo:" URL scheme

Submissions support a special "echo:" URL scheme which returns the data that was submitted. This is useful for tests.

_NOTE: Previously, the undocumented "test:" scheme had the same effect. It is still supported for backward compatibility._

Example:

```xml
<xf:submission
    id="my-submission"
    ref="instance()"
    method="post" 
    action="echo:" 
    replace="instance" 
    instance="result"/>
```

## Local submissions (deprecated)

### Status

Since Orbeon Forms 4.7 and newer, this feature is usually not relevant as Orbeon Forms handles service requests internally. See [issue #1363](https://github.com/orbeon/orbeon-forms/issues/1363).

### Rationale

XForms pages can make heavy use of services through the use of `<xf:submission>`. A service is primarily identified by a URL.

* Sometimes a service is remote (on a machine other than the machine on which Orbeon Forms is installed), in which case the URL is necessarily an absolute URL starting with `http://` or `https://`.
* But often services are implemented within Orbeon Forms itself, not only on the same server but within the same web application. In the case of such local submission, Orbeon Forms provides a special optimized mode which has the following benefits:
    * No actual HTTP connection is initiated, so performance is likely to be better.
    * There is no need to deal with absolute URLs such as `http://localhost:8080`, especially when proxies or firewalls are in place.

For more information, see also the [configuration properties](../configuration/properties/xforms.html) related to submissions.

### Enabling local submissions

Orbeon Forms performs a local submission if:

* The URL specified is not a absolute, i.e. does not start with `http://` or `https://`.
* The submission is not asynchronous. (This restriction may be lifted in the future.)
* In a servlet environment:
    * The submission has `replace="all"` (which is the default if no `replace` attribute is specified) and the `oxf.xforms.local-submission-forward` property is set to `true` (which is the default).
    * The submission has `replace="instance"`, `replace="text"` or `replace="none"` and the `oxf.xforms.local-submission-include` property is set to `true` (the default is `false`).
* In a portlet environment:
    * The `f:url-norewrite` attribute is not set to `true`.
    * The `f:url-type` attribute is not set to `resource`.

*NOTE: The portlet logic above is likely to be revised in the future. Also note that in the case of optimized submissions within portlets, requests are made directly to the Orbeon Forms portlet and do not use servlet forward/include.*

### Context resolution

In a servlet environment, paths are resolved as follows:

* If `f:url-norewrite` is not set to `true`, the resource is resolved against the current servlet context.
* If `f:url-norewrite` is set to `true`, the resource is resolved against the servlet container root. This allows accessing other web applications within the same servlet container.

Say your application is under context `/orbeon`, and you have a second web application under context `/foo`.

This submission calls ``/orbeon/bar`:

```xml
<xf:submission 
    replace="all" 
    method="post" 
    resource="/bar"/>
```

This submission calls ``/foo/bar`:

```xml
<xf:submission 
    replace="all" 
    method="post" 
    resource="/foo/bar"
    f:url-norewrite="true"/>
```

### Limitations of includes

With:

* `replace="instance"`
* `replace="text"`
* `replace="none"`

optimized submission are implemented using the servlet container's include mechanism, which does not automatically build path information for the included resource.

In this case, Orbeon Forms is therefore unable to provide proper "servlet path" and "path info" information. Orbeon Forms handles this situation in the following way:

* A blank (`""`) "servlet path" is provided.
* The "path info" contains the entire path provided, instead of the path following the servlet path.

This may cause some application which rely on the "servlet path" information to behave incorrectly. For example, consider the eXist REST servlet:

* It is mounted as `/exist/rest` within Orbeon Forms.
* eXist (quite properly) expects any path following `/exist/rest` to be a path into the database, e.g. `/exist/rest/db/orbeon` produces a path called `/db/orbeon`.
* If Orbeon Forms calls the eXist REST servlet with a blank servlet path and a path info containing `/exist/rest/db/orbeon`, eXist obviously obtains an incorrect database path.

*NOTE: In short you must be careful when using local includes. The good news is that if you are using servlets which do not depend on path information as explained above, or if you have control over the implementation of the services you are calling, then you can most likely work around this limitation.*

Local forwards are not subject to that limitation.

[3]: http://hc.apache.org/httpclient-3.x/authentication.html
[5]: https://www.w3.org/TR/html401/present/frames.html#adef-target
[6]: https://www.w3.org/TR/xslt-xquery-serialization/
