# Jasperreport

- Demonstration:
  [Jasperreport](http://www.zkoss.org/zkdemo/reporting/jasperreport)

- Java API: <javadoc>org.zkoss.zkex.zul.Jasperreport</javadoc>

- JavaScript API:
  <javadoc directory="jsdoc">zkex.utl.Jasperreport</javadoc>

- Style Guide: N/A

- 

# Employment/Purpose

The JasperReport component is based on a 3rd party JasperReports
Library, a report generator (e.g. XML generator, PDF generator etc.).
This component is used to generate a Jasper report into an inline frame.

# Including Dependency

ZK doesn't include jasperreports by default. If you need to use this
component, you have to include the dependency manually like:

``` xml
<dependency>
    <groupId>net.sf.jasperreports</groupId>
    <artifactId>jasperreports</artifactId>
    <version>6.17.0</version>
</dependency>
```

## 3rd Party Vulnerability Notice

Jasperreport uses another external library called itext 2, and there are
[some
vulnerabilities](https://security.snyk.io/package/maven/org.bouncycastle:bcprov-jdk15on)
reported on `org.bouncycastle:bcprov-jdk15on`, which is a dependency of
itext 2. If you are concerned, you can exclude that dependency, it does
not affect the use or the PDF exporting of this component.
Alternatively, Jasperreport does provide a separate PDF Exporter with a
newer version of itext which does not contain the affected bouncycastle
lib. For more information, please check [JasperReports PDF Exporter Lib
Seven](https://github.com/Jaspersoft/jasperreports-pdf-lib7).

# Example

<figure>
<img src="ZKComRef_Jasperreport_Examples.PNG"
title="ZKComRef_Jasperreport_Examples.PNG" />
<figcaption>ZKComRef_Jasperreport_Examples.PNG</figcaption>
</figure>

``` xml
    <jasperreport id="report" height="360px" />
    
    <zscript>
        import org.zkoss.zkdemo.userguide.CustomDataSource;
        
            //Preparing parameters
            Map parameters = new HashMap();
            parameters.put("ReportTitle", "Address Report");
            parameters.put("DataFile", "CustomDataSource from java");
            
            report.setSrc("/data/jasperreport.jasper");
            report.setParameters(parameters);
            report.setDatasource(new CustomDataSource());
            report.setType("pdf");
    </zscript>
```

## Provide Export Parameters

The Jasperreport component API provides a way to specify export
parameters. To do so, you should put a Map containing export parameters
within the parameters Map, with key `"exportParameter"`. For example:

``` java
    Map parameters = new HashMap();
    Map exportParams = new HashMap();
    exportParams.put("net.sf.jasperreports.export.mypropertyname", true);
    parameters.put("exportParameter", exportParams);
    report.setParameters(parameters); // report is the Jasperreport component
```

### One-Page-Per-Sheet Property

In Jasperreport engine, this property is default to be false. However,
the default behavior is turned on in the Jasperreport component, in
which case when the report is exported as Excel format there will be one
sheet generated for each page. To override this setting, set it as an
export parameter as the following:

``` java
    Map parameters = new HashMap();
    Map exportParams = new HashMap();
    exportParams.put(JRXlsAbstractExporterParameter.PROPERTY_ONE_PAGE_PER_SHEET.toString(), false);
    parameters.put("exportParameter", exportParams);
    report.setParameters(parameters); // report is the Jasperreport component
```

# exportName

You can specify the export file name for the download if any, the full
file name will be exportName + "." + format.

Note: exportName can not be empty or null.

Default: "report"

``` xml
<jasperreport exportName="test"/>
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
  Iframe](ZK_Component_Reference/Essential_Components/Iframe#Supported_Events)

# Supported Children

`*NONE`

# Supported Type

## Usage

### Java Code

``` java
//Jasperreport report;
report.setType("pdf"); // report is the Jasperreport component
```

### Zul Code

``` xml
<jasperreport id="report" type="pdf" />
```

## Type List

<table>
<thead>
<tr class="header">
<th><p>Type</p></th>
<th><p>Version</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>pdf</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>xml</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>html</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>rtf</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>xls</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>jxl</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>csv</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>odt</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>xlsx</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>docx</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>graphic2d</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>ods</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>pptx</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>txt</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>xhtml</p></td>
<td></td>
</tr>
</tbody>
</table>

# Supported JasperReport Version

| ZK    | JasperReport | Transitive Dependency                                                                                                                                       |
|-------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 9.6.0 | 6.14.0       | optional ([net.sf.jasperreports:jasperreports](https://mvnrepository.com/artifact/net.sf.jasperreports/jasperreports) needs to be added manually on demand) |
| 9.5.0 | 6.14.0       | added by default                                                                                                                                            |
| 9.0.0 | 6.6.0        | added by default                                                                                                                                            |
| 8.6.0 | 6.5.1        | added by default                                                                                                                                            |
| 7.0.0 | 4.5.1        | added by default                                                                                                                                            |
| 6.0.0 | 4.0.1        | added by default                                                                                                                                            |

# Use Cases

| Version | Description                                                                                                                                                           |
|---------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 5.0.2   | [How to use Dynamic Jasper Reports](http://www.zkoss.org/forum/listComment/10873)                                                                                     |
| 6/5.0.8 | [Create a Report with ZK using iReport and JasperReports](http://books.zkoss.org/wiki/Small_Talks/2012/April/Create_a_Report_with_ZK_using_iReport_and_JasperReports) |

# Troubleshooting

## Linux

Jasperreport depends on the fonts you use in the report. For more
information, please refer to [ZK Installation Guide:
Linux](ZK_Installation_Guide/Setting_up_OS/Linux).

# Version History

| Version | Date                                                                     | Content                               |
|---------|--------------------------------------------------------------------------|---------------------------------------|
| 5.0.1   | March 2010                                                               | Support Hibernate and SQL connections |
| 5.0.8   | Upgrade JaserReport version to 4.0 and support new JasperReport exporter |                                       |