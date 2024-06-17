# Image

- Demonstration:
  [Image](http://www.zkoss.org/zkdemo/multimedia/dynamic_image)
- Java API: <javadoc>org.zkoss.zul.Image</javadoc>
- JavaScript API: <javadoc directory="jsdoc">zul.wgt.Image</javadoc>
- Style Guide: N/A

# Employment/Purpose

An `image` component is used to display an image at the browser. There
are two ways to assign an image to an `image` component. First, you
could use the `src` property to specify a URI where the image is
located. This approach is similar to what HTML supports. It is useful

if you want to display a static image, or any image that can be
identified by URL.

``` xml
 <image src="/my.png">
```

# Locale Dependent Image

Like using any other properties that accept an URI, you can specify "\*"
for identifying a Locale dependent image. For example, if you have
different images for different Locales, you could use the following
code.

``` xml
<image src="/my*.png"/>
```

Assuming one of your users is visiting your page with *de_DE* as the
preferred Locale. Zk will try to locate the image file called
`/my_de_DE.png`. If it is not found, it will try `/my_de.png` and
finally `/my.png`.

Please refer to
[ZK%20Developer's%20Reference/Internationalization/Locale-Dependent%20Resources](ZK%20Developer's%20Reference/Internationalization/Locale-Dependent%20Resources)
for more details.

Secondly, you could use the `setContent` method to set the content of an
image to an `image` component directly. Once assigned, the image
displayed at the browser will be updated automatically. This approach is
useful if an image is generated dynamically.

For example, you can generate a map for the location specified by a user
as demonstrated below.

``` xml
<zk>
    Location: <textbox onChange="updateMap(self.value)"/>
    Map: <image id="image"/>
    <zscript><![CDATA[  
        void updateMap(String location) {
            if (location.length() > 0) {
                org.zkoss.image.AImage img = new org.zkoss.image.AImage(location);
                image.setContent(img);
            }
        }
    ]]>
    </zscript>
</zk>
```

In the above example, we assume that you have a class named `MapImage`
for generating a map of the specified location.

Notice that the image component accepts the content encapsulated by the
`org.zkoss.image.Image` format. If the image generated by your tool is
not in this format, you can use the `org.zkoss.image.AImage` class to
wrap a binary array of data, a file or an input stream into the `Image`
interface.

In traditional Web applications, caching a dynamically generated image
is complicated, however with the `image` component, you don't need to
worry about it. Once the content of an image is assigned, it belongs to
the `image` component, and the memory it occupies will be released
automatically when the `image` component is no longer used.

**Tip:** If you want to display the content other than an image, say a
PDF, you can use the `iframe` component. Please refer to the relevant
section for details.

# Image Supports javax.awt.image.RenderedImage

Since version 3.0.7 ZK allows image, button and related components to
support RenderedImage directly without a format conversion. Here is the
example code,

``` xml
<window title="Test of Live Image">
    <image id="img"/>
    <zscript>
    import java.awt.*;
    import java.awt.image.*;
    import java.awt.geom.*;
    int x = 10, y = 10;

    void draw(int x1, int y1, int x2, int y2) {
        BufferedImage bi = new BufferedImage(400, 300, BufferedImage.TYPE_INT_RGB);
        Graphics2D g2d = bi.createGraphics();
        Line2D line = new Line2D.Double(x1, y1, x2, y2);
        g2d.setColor(Color.blue);
        g2d.setStroke(new BasicStroke(3));
        g2d.draw(line);
        img.setContent(bi);
    }
    draw(x, y, x += 10, y += 10);
    </zscript>
    <button label="change" onClick="draw(x, y, x += 10, y += 10)"/>
</window>
```

# Preload Image

The feature is applied to all of the LabelImageElement and Image
components.

By default the preload function is disabled, so users have to specify
the *custom-attributes* to be true. For example,

``` xml
<image src="xxx.png">
    <custom-attributes org.zkoss.zul.image.preload="true" />
</image>
```

Or specify just below the root component.

For example,

``` xml
<window>
    <custom-attributes org.zkoss.zul.image.preload="true" />
    <button image="xxx.png" />
    <image src="xxx.png" />
</window>
```

As you can see, the *custom-attributes* will be checked recursively

The feature can also applied from zk.xml as a library properity.

For example,

``` xml
<!-- zk.xml -->
<zk>
    <library-property>
        <name>org.zkoss.zul.image.preload</name>
        <value>true</value>
    </library-property>
</zk>
```

# Specifying Alt Attribute

``` xml
<zk xmlns:c="client/attribute">
    <image src="/multimedia/zklogo3.png" c:alt="zk logo"/>
</zk>
```

# Supported Events

<table>
<thead>
<tr class="header">
<th><center>
<p>Name</p>
</center></th>
<th><center>
<p>Event Type</p>
</center></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>None</p></td>
<td><p>None</p></td>
</tr>
</tbody>
</table>

- Inherited Supported Events: [
  XulElement](ZK_Component_Reference/Base_Components/XulElement#Supported_Events)

# Supported Children

`*None`

# Use Cases

| Version | Description | Example Location |
|---------|-------------|------------------|
|         |             |                  |

# Version History

| Version | Date           | Content                                                                                                          |
|---------|----------------|------------------------------------------------------------------------------------------------------------------|
| 6.0.0   | September 2011 | [A way to pre-load images since many UIs depend on the size of an image](http://tracker.zkoss.org/browse/ZK-314) |