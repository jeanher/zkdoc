# DHtmlLayoutFilter

`[Optional] Class: `<javadoc>`org.zkoss.zk.ui.http.DHtmlLayoutFilter`</javadoc>

ZK Filter is a filter to post-process the output generated by other
servlets, such as JSP pages. Its role is similar to the ZK Loader.
Unlike the ZK Loader, which loads static ZUML pages from Web
applications directly, the ZK filter is designed to process dynamic
pages generated by other servlets, say JSP or JSF. It enables developers
to add rich user interfaces to existent servlets written in any
technology.

**Note:** the output must be in XHTML (or ZUML) syntax. If you encounter
any problem, you can save the generated output into a ZHTML page and
then browse the URL whether the ZHTML page is stored.

# The Initial Parameters

<table>
<thead>
<tr class="header">
<th><center>
<p>init-param</p>
</center></th>
<th><center>
<p>Descriptions</p>
</center></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>extension</p></td>
<td><p>[Optional][Default: <code>html</code>]</p>
<p>It specifies how to process the response generated by other
servlets.</p>
<p>If <code>html</code> or <code>zhtml</code>, XHTML is assumed to be
the default namespace. If <code>xul</code> or <code>zul</code>, XUL is
assumed to be the default namespace.</p></td>
</tr>
<tr class="even">
<td><p>charset</p></td>
<td><p>[Optional][Default: <code>UTF-8</code>]</p>
<p>It specifies the default charset for the output of this filter.</p>
<p>If an empty string is specified as follows, the container's default
is used. In other words, the <code>setCharacterEncoding</code> method of
<em>javax.servlet.ServletResponse</em> is not called.</p>
<p><param-value></param-value></p></td>
</tr>
<tr class="odd">
<td><p>compress</p></td>
<td><p>[Optional][Default: <code>true</code>]</p>
<p>It specifies whether to compress the output if the browser supports
the compression (<code>Accept-Encoding</code>) and this filter is not
included by other Servlets.</p></td>
</tr>
</tbody>
</table>

# Map URL to ZK Filter

ZK Filter can be mapped to any servlet or JSP page you want. For
example,

``` xml
    <filter>
        <filter-name>zkFilter</filter-name>
        <filter-class>org.zkoss.zk.ui.http.DHtmlLayoutFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>zkFilter</filter-name>
        <url-pattern>/foo/whatever.jsp</url-pattern>
    </filter-mapping>
    <filter-mapping>
        <filter-name>zkFilter</filter-name>
        <url-pattern>/foo2/*</url-pattern>
    </filter-mapping>
```

# Version History

| Version | Date | Content |
|---------|------|---------|
|         |      |         |