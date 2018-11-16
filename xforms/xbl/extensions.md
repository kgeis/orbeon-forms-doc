# XBL Extensions

## Namespaces

All the generic extensions are in the namespace `http://orbeon.org/oxf/xml/xbl`, and the usual mapping of this namespace is `xmlns:xxbl="http://orbeon.org/oxf/xml/xbl"`.

## Configuration

[SINCE Orbeon Forms 2018.2]

Element filtering can be configured by providing a custom object or class.

The following property allows adding a custom extension XPath function library:

```xml
<property 
    as="xs:string" 
    name="oxf.xforms.xbl-support" 
    value="org.orbeon.oxf.fr.xbl.FormRunnerXblSupport"/>
```

When this property is present, the XForms engine attempts to load an extension XBL support implementation. It does this in two ways:

1. First, it tries to access a Scala object extending `org.orbeon.oxf.xforms.xbl.XBLSupport`.
2. If that fails, it tries to access a Java class and calls a static `instance()` method on it to obtain an `org.orbeon.oxf.xforms.xbl.XBLSupport`.

Scala example:

```scala
object FormRunnerXblSupport extends XBLSupport {
  
  override def keepElement(
    partAnalysis  : PartAnalysisImpl,
    boundElement  : Element,
    directNameOpt : Option[QName],
    elem          : Element
  ): Boolean = {
    // Implementation here 
    false
  }
}
```

Java example:

```java
class FormRunnerXblSupport {

    private static XBLSupport _instance = null;

    public static synchronized XBLSupport instance() {
        if (_instance == null)
            _instance = new FormRunnerXblSupport();
        return _instance;
    }
  
    public boolean keepElement(
        PartAnalysisImpl partAnalysis,
        Element boundElement,
        scala.Option<QName> directNameOpt,
        Element elem
    ) {
        // Implementation here
        return false;
    }
}
```

You can also turn specify this property specifically for a given form by adding an `xxf:xbl-support` attribute on the first model:

```xml
xxf:xbl-support="org.orbeon.oxf.fr.xbl.FormRunnerXblSupport"
```

## xxbl:container attribute

The `xxbl:container` attribute on `xbl:binding` allows specifying the name of the HTML element that the XBL binding uses to encapsulate content. By default, this is `xh:div`. Here is how to change it to `xh:span`:

```xml
<xbl:binding id="my-binding" element="fr|my-binding" xxbl:container="span">
```

## xxbl:attr attribute

The standard `xbl:attr` attribute does not support accessing attributes other than those positioned exactly on the bound element. This is a serious limitation. The Orbeon Forms implementation adds an extension attribute, `xxbl:attr`,  which takes an XPath expression:

```xml
xxbl:attr="xf:alert/(@context | @ref | @bind | @model)"
```

## xbl:template/xxbl:transform attribute

When more flexibility is needed than XBL can provide, the `xxbl:transform` attribute is your friend.

```xml
<xbl:template xxbl:transform="processorName">
    Transformation (inline)
</xbl:template>
```

Orbeon Forms runs the processor specified by this attribute connecting its `config` input to the content of the `xbl:template` and its `data` input to the bound element and replaces the content of the `xbl:template` by the `data` output of the processor. The most frequent expected use is to run XSLT transformations. For instance, to create a widget that alternates styles in table rows within an `xf:repeat`:

```xml
<xbl:binding id="foo-table-alternate" element="foo|table-alternate">
    <xbl:template xxbl:transform="oxf:xslt">
        <xsl:transform version="2.0">
            <xsl:template match="@*|node()">
                <xsl:copy>
                    <xsl:apply-templates select="@*[not(name() = ('style1', 'style2'))]|node()"/>
                </xsl:copy>
            </xsl:template>
            <xsl:template match="foo:table-alternate">
                <xh:table>
                    <xsl:apply-templates select="@*|node()"/>
                </xh:table>
            </xsl:template>
            <xsl:template match="xf:repeat/xh:tr" >
                <xf:var name="position" value="position()"/>
                <xf:group ref=".[$position mod 2 = 1]">
                    <xh:tr xbl:attr="style=style1">
                        <xsl:apply-templates select="@*|node()"/>
                    </xh:tr>
                </xf:group>
                <xf:group ref=".[not($position mod 2 = 1)]">
                    <xh:tr xbl:attr="style=style2">
                        <xsl:apply-templates select="@*|node()"/>
                    </xh:tr>
                </xf:group>
            </xsl:template>
        </xsl:transform>
    </xbl:template>
</xbl:binding>
```

This component can be invoked through:

```xml
<foo:table-alternate class="gridtable" style1="background: red" style2="background: white">
    <xh:tr>
        <xh:th>Label</xh:th>
        <xh:th>Value</xh:th>
    </xh:tr>
    <xf:repeat ref="item">
        <xh:tr>
            <xh:td>
                <xf:output value="@label"/>
            </xh:td>
            <xh:td>
                <xf:output value="@value"/>
            </xh:td>
        </xh:tr>
    </xf:repeat>
</foo:table-alternate>
```

The `xbl:template/@xxbl:transform="oxf:xslt"` attribute specifies that its child element (`xsl:transform`) is considered as an XSLT transformation, runs against the bound element (`foo:table-alternate`), and the result of this transformation is used as the actual content of the `xbl:template`.

The result of your transformation will often contain XBL attributes, and in this sample `xbl:attr` attributes are used to set the `style` of the rows with: `<xh:tr xbl:attr="style=style1">`.

The result of the transformation has to be a (well formed) single rooted XML fragment. This might seem obvious, but that means that you might have to encapsulate the sub elements of `xbl:template` in an XHTML `div` or  `span` compared to what you would have done if you were not applying a transformation.

The transformation has full access to the bound element and can transform any of its child nodes or attributes. To keep things as encapsulated as possible and not change the behavior of bound elements that are potentially embedded, it is a good practice to define tight transformation templates that affect only the nodes that are meant to be transformed. In the example given above, one might for instance argue that  `<xsl:template match="foo:table-alternate/xf:repeat/xh:tr">` would be safer than `<xsl:template match="xf:repeat/xh:tr">`.

You can use `<xsl:message terminate="yes">` to report to the user errors that occur during the XSLT transformation. For example:

```xml
<xsl:message terminate="yes">Terminating!</xsl:message>
```

This results in an error will be output in the log, and an error message will show in the browser.

## xxbl:global element

The `<xxbl:global>` element allows an XBL binding to place global markup that is included in a page only once.

```xml
<xbl:xbl>
    <xbl:binding element="fr|foo">
        <xxbl:global>
            <!-- The single global dialog -->
            <xxf:dialog id="my-dialog" level="modal" model="my-dialog-model">
                <xf:label>My Global Dialog</xf:label>

                <!-- Dialog model -->
                <xf:model id="my-dialog-model">
                    ...
                </xf:model>

                ...

            </xxf:dialog>
        </xxbl:global>
        <xbl:template>
            ...
        </xbl:template>
    </xbl:binding>
</xbl:xbl>
```

How this works:

* The global markup is included at the end of the top-level XForms document, as if you put it there by hand.
* If the XBL binding is not used, the global markup is not included.
* Ids on elements in global markup must be made unique by the component author, as those ids become global as well.

The component can dispatch events to global controls if the outer scope is the top-level scope, with the `xxbl:scope="outer"` attribute. For instance, if in the `<xxbl:global>` you defined an `<xxf:dialog id="my-dialog">`, then from within the component, you can run the following action:

```xml
<xxf:show dialog="my-dialog" xxbl:scope="outer"/>
```

_NOTE: A future enhancement to this feature might restrict id and XPath scope of global markup to that of the XBL binding._

## xxbl:mirror attribute

This attribute, placed on a local XForms instance, tells the XBL engine to automatically mirror changes between that instance and the XBL component's bound node.

For mirroring to work, the XBL component must either:

* if it has an XPath node binding: be bound to an element node
* if it doesn't have an XPath node binding: be in the XPath context of an element node (done for compatibility with Form Builder section templates)

At most one instance in a given XBL component may have `xxbl:mirror="true"`.

Lifecycle:

* when the XBL component becomes relevant
    * the XBL instance is first initialized as usual
    * then if the XBL binding or context points to an element, that element is extracted to become the root element of a new element, which replaces the XBL instance
* when updates (value changes, inserts, deletes) take place on the XBL instance, these changes are mirrored outside
* when updates (value changes, inserts, deletes) take place outside, these changes are mirrored on the XBL instance

Example:

```xml
<xbl:binding id="fr-gaga" element="fr|gaga" xxbl:mode="binding">
    <xbl:implementation>
        <xf:model id="gaga-model">
            <xf:instance id="gaga-instance" xxbl:mirror="true">
                <empty/>
            </xf:instance>
        </xf:model>
    </xbl:implementation>
</xbl:binding>
```

## xxbl:use-if-attr attribute

[SINCE Orbeon Forms 2018.1]

This attribute allows including a part of the resulting tree only if a particular attribute is present on the bound node.

Example:

```xml
<xf:group xxbl:use-if-attr="prefix" class="add-on" ref=".[xxf:non-blank($prefix)]">
    <xf:output value="$prefix"/>
</xf:group>

``` 

That `xf:group` element will ony be included if the bound node contains an attribute named `prefix`. Otherwise the entire
subtree will be omitted.

[Orbeon Forms 2018.1 ONLY] This also checks properties such as `oxf.xforms.xbl.fr.currency.prefix` and if the value of the property is found
and not empty, it is as if the attribute was present on the bound node.

In the context of Form Runner, use instead the `fr:keep-if-param-non-blank` attribute.

## fr:keep-if-design-time

[SINCE Orbeon Forms 2018.2]

In the context of Form Runner and Form Builder, this attribute allows including a part of the resulting tree only if the form is shown at design-time or runtime.

The value of the attribute must be either:
 
- `true`: keep the element only if the form is displayed in Form Builder at design-time (this does *not* include the `test` mode)
- `false`: keep the element only if the form is displayed in Form Runner at runtime (this includes the `test` mode)

```xml
<xbl:template>
    <xh:i class="fa fa-fw fa-eye-slash" fr:keep-if-design-time="true"/>
</xbl:template>

```

## fr:keep-if-param-non-blank

[SINCE Orbeon Forms 2018.2]

In the context of Form Runner, this attribute allows including a part of the resulting tree only if at least one of the following applies:

The value of the attribute is the name of the parameter to check.
 
- The specified parameter has a corresponding attribute on the bound node which is present and non-blank.
    - _NOTE: This does not evaluate AVTs. Only the static value of the attribute is checked._
- There is a corresponding form definition metadata element with a non-blank value.
- There is a corresponding configuration property with the current app name/form name which is present and non-blank.
- There is a corresponding configuration property without app name/form name which is present and non-blank.

```xml
<xf:group fr:keep-if-param-non-blank="prefix" class="add-on" ref=".[xxf:non-blank($prefix)]">
    <xf:output value="$prefix"/>
</xf:group>

```

