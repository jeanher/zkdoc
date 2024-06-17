Here we introduce some tips when you use a browser testing tool to test
ZK-based applications, e.g. [JMeter](https://jmeter.apache.org/),
[TestCafe](https://testcafe.io/), selenium.

# Deal with Randomized UUID

By default, a desktop's ID and a component's UUID are randomized for
preventing Cross-Site Request Forgery (CSRF) and allowing multiple
desktops to coexist on the same web page (such as Portlet). A
component's UUID is auto-generated by ZK and different from its ID. The
UUID is used as a component DOM elements' id in a browser. However, it
also means the DOM element's IDs will change from one test run to
another.

If your test code runs at the server (such
[ZATS](ZK_Developer's_Reference/Testing/ZATS) and JUnit), it
is not an issue at all (since DOM elements are available at the client
only). However, if your test tool runs in a browser, you have to locate
an element with one of the following approaches:

1.  Not to depend on a DOM element's ID. Rather, use a component's ID
    and/or component's parent-child-sibling relationship.
2.  Implement
    <javadoc type="interface">org.zkoss.zk.ui.sys.IdGenerator</javadoc>
    to generate UUID in a predictable and repeatable way

Let me explain them in detail.

## Approach 1: Locate by a component's ID

With [Server+client
architecture](ZK_Developer's_Reference/Overture/Architecture_Overview),
ZK maintains an *identical* world at the client. If your test tool is
able to access JavaScript at the client, your test code can depend on a
component's ID and its widget's parent-child relationship as your
application code depends on the component's ID and component's
parent-child relationship. They are *identical*, except one is
JavaScript and called <javadoc directory="jsdoc">zk.Widget</javadoc>,
while the other is Java and called
<javadoc type="interface">org.zkoss.zk.ui.Component</javadoc>.

This is a suggested approach since it is much easier to test an
application at the same abstract level -- the component level, aka., the
widget level (rather than the DOM level).

To retrieve widgets at the client, you can use one of the following
JavaScript API:

- <javadoc directory="jsdoc">\_global\_.jq</javadoc>
- <javadoc directory="jsdoc" method="$(zk.Object, _global_.Map)">zk.Widget</javadoc>

<javadoc directory="jsdoc">\_global\_.jq</javadoc> allows your test code
to access the components directly, so the test code could depend on a
component's ID
(<javadoc directory="jsdoc" method="id">zk.Widget</javadoc>) and the
widget tree
(<javadoc directory="jsdoc" method="firstChild">zk.Widget</javadoc>,
<javadoc directory="jsdoc" method="nextSibling">zk.Widget</javadoc> and
so on).

``` javascript
jq('@window[border="normal"]') //returns a list of window whose border is normal
jq('$x'); //returns the widget whose component ID is x, <div id="x"/>
jq('$x $y'); //returns the widget whose ID is y and it is in an ID space owned by x
```

With this approach, you still can verify the DOM structure if you want,
since it can be retrieved from a widget's
<javadoc directory="jsdoc" method="$n()">zk.Widget</javadoc>.

[ZTL](http://code.google.com/p/zk-ztl/) is a typical example that takes
this approach. For more information, please refer to the [ZTL
section](ZK_Developer's_Reference/Testing/ZTL).

## Approach 2: Use ID Generator

If your testing tool running in a browser cannot access JavaScript, you
can implement an ID generator to generate a desktop's ID and component's
UUID in a predictable and repeatable manner.

Since ZK 7.0.0, ZK provides a static ID generator implementation for
testing, to use
<javadoc type="class">org.zkoss.zk.ui.impl.StaticIdGenerator</javadoc>,
simply add it to zk.xml.

``` xml
<system-config>
    <id-generator-class>org.zkoss.zk.ui.impl.StaticIdGenerator</id-generator-class>
</system-config>
```

To implement a custom ID generator, you have to do the following:

- Implement a Java class that implements
  <javadoc type="interface">org.zkoss.zk.ui.sys.IdGenerator</javadoc>.
- Specify the Java class FQCN at
  [id-generator-class](ZK_Configuration_Reference/zk.xml/The_system-config_Element)
  element in `zk.xml`. For example,

``` xml
<system-config>
    <id-generator-class>my.IdGenerator</id-generator-class>
</system-config>
```

### Examples

- [ComponentIdFirstGenerator](https://github.com/zkoss/zkbooks/blob/master/developersreference/developersreference/src/main/java/org/zkoss/reference/developer/testing/ComponentIdFirstGenerator.java).
  Unlike
  [StaticIdGenerator](https://www.zkoss.org/javadoc/latest/zk/org/zkoss/zk/ui/impl/StaticIdGenerator.html),
  component creation order doesn't affect id generation.
- [StaticIdGeneratorExt](https://github.com/zkoss/zkbooks/blob/master/developersreference/developersreference/src/main/java/org/zkoss/reference/developer/testing/StaticIdGeneratorExt.java).
  It generates desktop id in a predictable way.

# Different Configuration for Different Environment

If you prefer to have a different configuration for the testing
environment (such as specifying ID generator for testing), you could put
the configuration in a separate file, say,
`WEB-INF/config/zk-testing.xml` with the following content.

``` xml
<zk>
  <system-config>
    <id-generator-class>my.IdGenerator</id-generator-class>
  </system-config>
</zk>
```

Then, you could you could specify
-Dorg.zkoss.zk.config.path=/WEB-INF/config/zk-testing.xml as one of the
arguments when starting the Web server.

# Disabled UUID recycle

If you want to generate UUID with some conditions, you might also want
to disable UUID recycling. ( It will reuse all the UUIDs from removed
components.) You could set the properties
[org.zkoss.zk.ui.uuidRecycle.disabled](ZK_Configuration_Reference/zk.xml/The_Library_Properties/org.zkoss.zk.ui.uuidRecycle.disabled)
in zk.xml.