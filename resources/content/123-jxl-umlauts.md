title=Java Excel API (jxl), German Umlauts and Linux
subtitle=jxl and encoding
created=2013-12-22T03:24:12
tags=Java, jxl, Encoding, Umlauts, Linux
language=en

[Java Excel API](http://jexcelapi.sourceforge.net/) is a great library to generate or modify xls-files with Java. On my web application, I use a xls-template, modify some fields and stream the result through a RESTful service. This works pretty good.
But if the templates have some German umlauts, the result has 'broken' characters. With following encoding settings the umlauts works:

Loading template:

	java.io.File f = new java.io.File("d:\\out.xls");
    OutputStream out = new FileOutputStream(f);
	WorkbookSettings settings = new WorkbookSettings();
    settings.setEncoding("Cp1252");
    Workbook template = Workbook.getWorkbook(this.getClass().getResourceAsStream("/template-test.xls"), settings);

Writing Output:

	WorkbookSettings settings = new WorkbookSettings();
    settings.setWriteAccess("rw");
    settings.setEncoding("Cp1252");
    WritableWorkbook output = Workbook.createWorkbook(out, template, settings);


It is important, that also the template is loaded with encoding `Cp1252`.
    