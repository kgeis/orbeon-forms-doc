# Extension events

## Introduction

This page documents extension events, that is events which are not part of the XForms specifications. Such events, by convention, start with the prefix `xxforms-`.

## Control events

### xxforms-visible

[SINCE Orbeon Forms 2018.1]

- __Dispatched in response to:__ control becoming relevant and not hidden, or control becoming visible following `xf:toggle`  
- __Target:__ control
- __Bubbles:__ Yes
- __Cancelable:__ No
- __Context Info:__ Same as other UI events

When using `<xf:switch>` without also setting the `xxf:xforms11-switch` attribute or property to `true`, controls in
non-visible switch cases remain relevant. This means that one cannot rely on `xforms-enabled` or `xforms-disabled` to
track whether the control is really active for the user. `xxforms-visible` and `xxforms-hidden` (below) allow making
that distinction.  

See also [UI refresh events](events-refresh.md).

### xxforms-hidden

[SINCE Orbeon Forms 2018.1]

- __Dispatched in response to:__ control becoming non-relevant while not hidden, or control becoming hidden following `xf:toggle`  
- __Target:__ control
- __Bubbles:__ Yes
- __Cancelable:__ No
- __Context Info:__ Same as other UI events

See also [UI refresh events](events-refresh.md).

## Document events

### xxforms-refresh-done

[SINCE Orbeon Forms 2018.1]

- __Dispatched in response to:__ UI refresh has completed, including dispatching refresh events 
- __Target:__ `#document`
- __Bubbles:__ Yes
- __Cancelable:__ No
- __Context Info:__ None

This event is dispatched to `#document` as the last action performed during a UI refresh.

You can listen to it with:

```xml
<xf:action 
    event="xxforms-refresh-done" 
    observer="#document">
    ...
</xf:action>
``` 

### xxforms-state-restored

[SINCE Orbeon Forms 2017.2]

- __Dispatched in response to:__ form state restored from the XForm state store 
- __Target:__ `#document`
- __Bubbles:__ No
- __Cancelable:__ No
- __Context Info:__ None

The `xxforms-state-restored` event is dispatched to the `#document` element when the form state is restored from the 
XForm state store. This can happen in the following case:

1. The form hasn't received updates from the client for a long time, has been evicted from the memory cache and stored
   into the state store, and is finally restored from the state store when the client issues a new update.
2. The form has been [replicated](../installation/replication.md) to another server.

Example: 

```xml
<xf:action
    event="xxforms-state-restored"
    observer="#document"
    target="#document">

    <xf:message>State restored.</xf:message>

</xf:action>
```

## Instance events

### xxforms-value-changed

- __Dispatched in response to:__ value changed in an instance
- __Target:__ `<xf:instance>` element  
- __Bubbles:__ Yes  
- __Cancelable:__ Yes
- __Context Info:__
    - `event('node') as node()`: element or attribute node whose value has changed
    - `event('old-value') as xs:string`: previous value
    - `event('new-value') as xs:string`: new value

The `xxforms-value-changed` event is dispatched to an instance when an element or attribute value is changed in that instance, namely through the following mechanisms:

- `calculate` or `xxf:default` MIP
- `<xf:setvalue>` action
- value of a bound control changed by the user
- submission result with `replace="text"`

Example:

```xml
<html xmlns:xforms="http://www.w3.org/2002/xforms"
      xmlns:ev="http://www.w3.org/2001/xml-events"
      xmlns="http://www.w3.org/1999/xhtml">

    <head>
        <xf:model>
            <xf:instance id="table">
                <table xmlns=""/>
            </xf:instance>
        </xf:model>
    </head>
    <body>
       <xf:message ev:event="xxforms-value-changed" ev:observer="table">Changed!</xf:message>
        <xf:input ref="instance()">
            <xf:label>Change me:</xf:label>
        </xf:input>
    </body>
</html>
```

### xxforms-valid

- __Dispatched in response to:__ instance being valid after validation
- __Target:__ `<xf:instance>` element  
- __Bubbles:__ Yes  
- __Cancelable:__ Yes  
- __Context Info:__ None

The `xxforms-valid` event is dispatched to an instance after validation if it is valid.  

### xxforms-invalid

- __Dispatched in response to:__ instance being invalid after validation
- __Target:__ `<xf:instance>` element  
- __Bubbles:__ Yes  
- __Cancelable:__ Yes  
- __Context Info:__ None

The `xxforms-invalid` event is dispatched to an instance after validation if it is invalid.  

## Model events

### xxforms-instances-ready

[SINCE Orbeon Forms 2017.1]

- __Dispatched in response to:__ model initialization
- __Target:__ `<xf:model>` element  
- __Bubbles:__ Yes  
- __Cancelable:__ No
- __Context Info:__ None

The `xxforms-instances-ready` event is dispatched to a model upon model initialization, after all model instances are
loaded but before the initial rebuild, recalculate and revalidate. Therefore this event takes place after a model's
`xforms-model-construct` but before a model's `xforms-model-construct-done`.

This event should rarely be needed.

## Dialog control events

### xxforms-dialog-open

- __Dispatched in response to:__ `xxf:show` action
- __Target:__ `<xxf:dialog>` element  
- __Bubbles:__ Yes  
- __Cancelable:__ Yes  
- __Context Info:__ None

The `xxforms-dialog-open` event is dispatched to a dialog in response to running the  `<xxf:show>` action targeting that dialog.

You can respond to this event before the dialog opens, for example to perform initialization of data:

```xml
<xxf:dialog id="hello-dialog">
  <xh:div>
    Hello!
  </xh:div>
  <xf:setvalue 
    ev:event="xxforms-dialog-open" 
    ref="..." 
    value="..."/>
</xxf:dialog>
```

You can set the focus on a specific control of the dialog with a `setfocus` action on the target or bubbling phase of this event:

```xml
<xf:setfocus 
    ev:event="xxforms-dialog-open" 
    control="my-control">
```

When the dialog opens upon receiving the `xxforms-dialog-open` event, it sets the focus on the first relevant, non-readonly control. This means that in general you don't need to use an explicit `setfocus` action.


### xxforms-dialog-close

- __Dispatched in response to:__ `xxf:hide` action
- __Target:__ `<xxf:dialog>` element  
- __Bubbles:__ Yes  
- __Cancelable:__ Yes  
- __Context Info:__ None

The `xxforms-dialog-close` event is dispatched to a dialog in response to:

- running the `<xxf:hide>` action targeting that dialog
- the user closing the dialog with the dialog close box, if present

## Repeat control events

### xxforms-nodeset-changed

- __Dispatched in response to:__ node-set changed on `<xf:repeat>`  
- __Target:__ `<xf:repeat>` element  
- __Bubbles:__ Yes  
- __Cancelable:__ Yes  
- __Context Info:__ 
    - `event('xxf:from-positions') as xs:integer*`
        - previous positions of all the iterations that moved
    - `event('xxf:to-positions') as xs:integer*`
        - new positions of all the iterations that moved, in an order matching `event('xxf:from-positions')`
    - `event('xxf:new-positions') as xs:integer*`
        - positions of all newly created iterations

The `xxforms-nodeset-changed` event allows you to detect changes to a repeat node-set:

- Nodes added to the nodeset, unless the nodeset was empty  
- Nodes reordered

This event is not dispatched when the repeat control becomes relevant or non-relevant. (A repeat control is considered non-relevant if its node-set is empty.)

Example:

```xml
<xf:group>
    <xxf:script ev:target="my-repeat" ev:event="xxforms-nodeset-changed xforms-enabled xforms-disabled">
        alert("Nodeset changed!");
    </xxf:script>
    <xf:repeat ref="record" id="my-repeat">
        ...
    </xf:repeat>
</xf:group>
```

In this example:

- The `ev:target` attribute ensures that this particular handler only catches events for `my-repeat`, in case there are some nested repeats or some other repeats within the group.
- The `ev:event` attribute lists not only `xxforms-nodeset-changed` event, but also the `xforms-enabled` and `xforms-disabled` event so the event runs when the nodeset goes from empty to non-empty or from non-empty to empty.

We recommend that you put the handler for `xxforms-nodeset-changed` outside the `` element, as shown in the example above. This ensures that, in case the repeat node-set becomes empty, actions associated with your event handler will still execute within a non-empty XPath context.

If nodes related to a repeat are inserted with `xf:insert` or `xf:delete` (including instance replacement upon submission), you could detect changes to the repeat node-set with XForms 1.1 using `xforms-insert` and `xforms-delete` events on instances. However these events are harder to use in this scenario, and will not catch situations where the repeat nodeset changes without insertions / deletions.  

Currently, we interpret handlers placed directly within `<xf:repeat>` as being attached to a particular repeat -*iteration**, not to the repeat element itself. This means you can write things like:

```xml
<xf:repeat ref="value" id="my-repeat">
    <xf:action ev:event="my-event">
        <xxf:variable name="position" select="position()"/>
        <xf:setvalue ref="." value="$position"/>
    </xf:action>
    ...
</xf:repeat>
```

The context size is the size of the repeat nodeset, and the context position that of the current iteration. Things work as if within the repeat you had an implicit group, which is in fact now how XForms 1.1 specifies repeat.

Now the question is: what happens if you dispatch an event to `<xf:repeat>` itself?  

We propose the current solution:

- if an event targets `<xf:repeat>`, then instead we dispatch it to the current repeat iteration, setting the appropriate XPath context for handlers associated with the iteration  
- in the case where there is no repeat iteration (empty repeat nodeset), the XPath context becomes empty

### xxforms-index-changed

- __Dispatched in response to:__ index changed on `<xf:repeat>`  
- __Target:__ `<xf:repeat>` element  
- __Bubbles:__ Yes  
- __Cancelable:__ Yes  
- __Context Info:__
    - `event('xxf:old-index') as xs:integer`: previous index
    - `event('xxf:new-index') as xs:integer`: new index

The `xxforms-index-changed` event allows you to detect changes to a repeat index.

```xml
<xf:repeat ref="value" id="my-repeat">
    <xf:action ev:event="my-event">
        <xxf:variable name="position" select="position()"/>
        <xf:setvalue ref="." value="$position"/>
    </xf:action>
    ...
</xf:repeat>
```

The `xxforms-index-changed` event is not dispatched during control creation, only when the index changes. In order to obtain the index during creation, you can attach a listener for `xforms-enabled` to the `<xf:repeat>` element and use the `index()` or `xxf:index()` function to obtain that repeat's initial index:

```xml
<xf:group>
    <xf:repeat ref="item" id="my-repeat">
        <!-- Test handler with ev:target="#observer" -->
        <xf:action ev:event="xforms-enabled" ev:target="#observer">
            ... use index('my-repeat') or xxf:index() here ...
        </xf:action>
    </xf:repeat>
    <!-- Test handler with ev:target -->
    <xf:action ev:event="xforms-enabled" ev:target="my-repeat">
        ... use index('my-repeat') here ...
    </xf:action>
</xf:group>
```

### xxforms-iteration-moved

- __Dispatched in response to:__ iteration containing the control has changed since the last refresh or the time the iteration was first created
- __Target:__ control element  
- __Bubbles:__ No  
- __Cancelable:__ Yes  
- __Context Info:__ None

The `xxforms-iteration-moved` event is dispatched during refresh, just after `xforms-value-changed` (if dispatched).

This event is not dispatched when the repeat control becomes relevant or non-relevant.

_NOTE: A repeat control is considered non-relevant if its node-set is empty._

The iteration in which a control is present can change when repeat node-sets change as a consequence of inserted nodes, for example.

This event is useful for example to run `xxf:script` actions to update client-side data in response to moved iterations. Here is an example from the `fr:currency` implementation:

```xml
<xf:input ref="$result">
    <xxf:script id="xf-ch" ev:event="xforms-value-changed xxforms-iteration-moved">
        YAHOO.xbl.fr.Currency.instance(this).update();
     </xxf:script>
    <xxf:script id="xf-ro" ev:event="xforms-readonly">YAHOO.xbl.fr.Currency.instance(this).readonly();</xxf:script>
    <xxf:script id="xf-rw" ev:event="xforms-readwrite">YAHOO.xbl.fr.Currency.instance(this).readwrite();</xxf:script>
</xf:input>
```

_NOTE: This event doesn't bubble, so event listeners must directly observe the controls receiving the event._

## Upload control events 

### xxforms-upload-start

- __Dispatched in response to:__ upload started on the client
- __Target:__ `<xf:upload>` element
- __Bubbles:__ Yes
- __Cancelable:__ Yes
- __Context Info:__ None

### xxforms-upload-cancel

- __Dispatched in response to:__ upload canceled by user on the client
- __Target:__ `<xf:upload>` element
- __Bubbles:__ Yes
- __Cancelable:__ Yes
- __Context Info:__ None

### xxforms-upload-done

- __Dispatched in response to:__ upload completed on the server
- __Target:__ `<xf:upload>` element
- __Bubbles:__ Yes
- __Cancelable:__ Yes
- __Context Info:__
    - `event('filename') as xs:string`
        - the *untrusted* filename sent by the browser
        - for example `MyImage.png`
    - `event('content-type') as xs:string`
        - the *untrusted* content type sent by the browser
        - for example `image/png`
    - `event('content-length') as xs:string`
        - the *untrusted* content length sent by the browser
        - for example `33270`

### xxforms-upload-error

- __Dispatched in response to:__ upload ended with an error
- __Target:__ `<xf:upload>` element
- __Bubbles:__ Yes
- __Cancelable:__ Yes
- __Context Info:__
    - `event('xxf:error-type') as xs:string`
        - `size-error` if the upload was too large
        - `upload-error` if the cause of the error is unknown
        - *NOTE: other error types may be added in the future*
    - `event('xxf:permitted') as xs:integer?`
        - if `size-error`, and if known, maximum upload size allowed by configuration
    - `event('xxf:actual') as xs:integer?`
        - if `size-error`, and if known, actual upload size detected

## See also

- [Standard support](events-standard.md)
- [UI refresh events](events-refresh.md)
- [Keyboard events](events-extensions-keyboard.md)
- [Extension context information](events-extensions-context.md)
- [Other event extensions](events-extensions-other.md)
