TCPDI
=====

PDF importer for [TCPDF](http://www.tcpdf.org/), based on [FPDI](http://www.setasign.de/products/pdf-php-solutions/fpdi/).  Requires [pauln/tcpdi_parser](https://github.com/pauln/tcpdi_parser) and [fdpf_tpl](http://www.setasign.de/products/pdf-php-solutions/fpdi/downloads/).


Installation
------------

Add tcpdi.php to the directory containing TCPDF, along with [pauln/tcpdi_parser](https://github.com/pauln/tcpdi_parser) and [fdpf_tpl](http://www.setasign.com/products/fpdi/downloads/#package-10102).

Note that only the standalone version of fpdf_tpl is supported (v1.2.3); the version bundled with FPDI 1.5 is not currently compatible.


Usage
-----

Usage is essentially the same as FPDI, except importing TCPDI rather than FPDI.  It also has a "setSourceData()" function which accepts raw PDF data, for cases where the file does not reside on disk or is not readable by TCPDI.

    // Include the main TCPDF library and TCPDI.
    require_once('lib/tcpdf/tcpdf.php');
    require_once('lib/tcpdf/tcpdi.php');
    
    // Create new PDF document.
    $pdf = new TCPDI(PDF_PAGE_ORIENTATION, PDF_UNIT, PDF_PAGE_FORMAT, true, 'UTF-8', false);
    
    // Add a page from a PDF by file path.
    $pdf->AddPage();
    $pdf->setSourceFile('/path/to/file-to-import.pdf');
    $idx = $pdf->importPage(1);
    $pdf->useTemplate($idx);
    
    $pdfdata = file_get_contents('/path/to/other-file.pdf'); // Simulate only having raw data available.
    $pagecount = $pdf->setSourceData($pdfdata);
    for ($i = 1; $i <= $pagecount; $i++) {
        $tplidx = $pdf->importPage($i);
        $pdf->AddPage();
        $pdf->useTemplate($tplidx);
    }

As of version 1.1, TCPDI also includes additional functionality for handling PDF Annotations.  As annotations are positioned relative to the bleed box rather than the crop box, you'll need to ensure that you're importing the full bleed box; a new function has also been introduced to set the page format (the various boxes, including the crop box) from the imported page, so that the imported page matches the original better.  The following example demonstrates this:

    // Include the main TCPDF library and TCPDI.
    require_once('lib/tcpdf/tcpdf.php');
    require_once('lib/tcpdf/tcpdi.php');
    
    // Create new PDF document.
    $pdf = new TCPDI(PDF_PAGE_ORIENTATION, PDF_UNIT, PDF_PAGE_FORMAT, true, 'UTF-8', false);
    
    // Add a page from a PDF by file path.
    $pdf->setSourceFile('/path/to/file-to-import.pdf');
    
    // Import the bleed box (default is crop box) for page 1.
    $tplidx = $pdf->importPage(1, '/BleedBox');
    $size = $pdf->getTemplatesize($tplidx);
    $orientation = ($size['w'] > $size['h']) ? 'L' : 'P';
    
    $pdf->AddPage($orientation);
    
    // Set page boxes from imported page 1.
    $pdf->setPageFormatFromTemplatePage(1, $orientation);
    
    // Import the content for page 1.
    $pdf->useTemplate($tplidx);
    
    // Import the annotations for page 1.
    $pdf->importAnnotations(1);

Note: tcpdi_parser v1.1 is required in order to import annotations, though setPageFormatFromTemplatePage() should work with older versions of tcpdi_parser.