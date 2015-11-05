# Overview
The client library includes `ReportDownloader` utilities for AdWords and DFP. This page describes the options each utility offers for processing report results.

For examples of how to construct and issue report requests, see the following folders:

* AdWords: [examples/adwords_axis/src/main/java/adwords/axis/v201509/reporting](https://github.com/googleads/googleads-java-lib/tree/master/examples/adwords_axis/src/main/java/adwords/axis/v201509/reporting)
* DFP: [examples/dfp_axis/src/main/java/dfp/axis/v201508/reportservice](https://github.com/googleads/googleads-java-lib/tree/master/examples/dfp_axis/src/main/java/dfp/axis/v201508/reportservice)

**Note:** Several of the examples below make use of [Guava I/O utilities](https://github.com/google/guava/wiki/IOExplained).

# Download report contents as a string
Use the approach below to download a report's contents as a string with embedded newlines. With this approach, you will hold the *entire contents of the report in memory*, so this is not the best option for large reports. Instead, consider [downloading to a file](#download-report-contents-to-a-file) or [processing the report contents as a stream](#process-the-report-contents-as-a-stream).

## AdWords

    ReportDownloadResponse reportDownloadResponse = 
        reportDownloader.downloadReport(...);
    String reportContents = reportDownloadResponse.getAsString();

## DFP

    ReportDownloadOptions options = new ReportDownloadOptions();
    options.setExportFormat(exportFormat);
    options.setUseGzipCompression(true);

    String reportContents = reportDownloader.
        getReportAsCharSource(options).read();

# Download report contents to a file
Use the approach below to download a report's contents to a file.

## AdWords

    ReportDownloadResponse reportDownloadResponse =
        reportDownloader.downloadReport(...);

    File file = File.createTempFile("report", ".csv");
    reportDownloadResponse.saveToFile(file.getPath());

## DFP

    import com.google.io.Files;
    import com.google.io.Resources;
    ...

    File file = File.createTempFile("report", ".csv.gz");

    ReportDownloadOptions options = new ReportDownloadOptions();
    options.setExportFormat(exportFormat);
    options.setUseGzipCompression(true);
    URL url = reportDownloader.getDownloadUrl(options);

    // Use the Resources and Files utilities in com.google.common.io
    // to copy the report contents to the file.
    Resources.asByteSource(url).copyTo(Files.asByteSink(file));

# Process the report contents as a stream
Use the approach below to process a report's contents as an `InputStream`. This is useful if the report output is extremely large and you do not want to hold the entire contents in memory.

The examples show how to convert the report output into a `CharSource` and then process the contents one line at a time. 

## AdWords

    import java.io.BufferedReader;
    import java.io.IOException;
    import java.io.InputStreamReader;
    ...

    final ReportDownloadResponse reportDownloadResponse =
        reportDownloader.downloadReport(...);

    BufferedReader responseReader = null;
    try {
      responseReader =
          new BufferedReader(new InputStreamReader(
              reportDownloadResponse.getInputStream()));
      String line;
      while ((line = responseReader.readLine()) != null) {
        // Process a single line of report contents...
      }
    } finally {
      if (responseReader != null) {
        responseReader.close();
      }
    }

The example above will work for [text formats](https://developers.google.com/adwords/api/docs/guides/reporting#download-formats) such as `CSV`, `TSV`, and `XML`. If you request one of the gzipped formats such as `GZIPPED_CSV` or `GZIPPED_XML`, you should wrap the stream returned by `response.getInputStream()` in a `java.util.zip.GZIPInputStream`.

      responseReader = new BufferedReader(
          new InputStreamReader(
              new GZIPInputStream(reportDownloadResponse.getInputStream())));

If you simply want to read the contents as a stream of bytes, you can just use the `InputStream` returned by `response.getInputStream()` directly.

## DFP

    import java.io.BufferedReader;
    ...

    ReportDownloadOptions options = new ReportDownloadOptions();
    options.setExportFormat(exportFormat);
    options.setUseGzipCompression(true);

    BufferedReader contentsReader = null;
    try {
      contentsReader = reportDownloader.getReportAsCharSource(options)
          .openBufferedStream();

      String line;
      while ((line = contentsReader.readLine()) != null) {
        // Process a single line of report contents...
      }
    } finally {
      if (contentsReader != null) {
        contentsReader.close();
      }
    }

If you simply want to read the contents as a stream of bytes, you can just use the `InputStream` returned by `reportDownloader.getDownloadUrl().openStream()` directly.
