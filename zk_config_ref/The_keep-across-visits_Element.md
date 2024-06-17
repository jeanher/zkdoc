**Syntax:**

<keep-across-visits>`true|`**`false`**</keep-across-visits>

`[Default: ``false``]`

It specifies whether to keep the desktop when a user reloads an URL or
browses away to another URL. Since browsers won't cache HTML pages
generated by ZK, ZK removes a desktop as soon as the user reloads the
URL or browses to another URL.

However, you have to specify `keep-across-visits` with `true`, if you
use the server-side cache for the HTML pages generated by ZK. An example
of the server side cache is [OpenSymphony
CacheFilter](http://www.opensymphony.com/oscache/wiki/CacheFilter.html).

``` xml
 <client-config>
     <keep-across-visits>true</keep-across-visits>
 </client-config>
```

**Notes:**

- You rarely need to turn on this option, since the HTML page will be
  reloaded by the browser. It is meaningless not to remove the desktop
  at the server, unless
  1.  You apply a special mechanism to cache the generated HTML pages,
      such as OpenSymphony described above.
  2.  You turn on the cacheable flag with [the page
      directive](ZUML_Reference/ZUML/Processing_Instructions/page)
- Don't turn on this option, if you [reuse the
  desktops](ZK_Developer's_Reference/Performance_Tips/Reuse_Desktops)
  by use of
  <javadoc type="interface">org.zkoss.zk.ui.util.DesktopRecycle</javadoc>.
  After all, a desktop can be reused only if it has been removed (and
  turning on this option makes a desktop stays alive).
- When working with Opera, ZK always keeps the desktop (until the number
  of desktops exceed the allowed maximal number), since Opera is smart
  enough to preserve the most updated content and always reuses the
  cached page.

# Version History

| Version | Date | Content |
|---------|------|---------|
|         |      |         |