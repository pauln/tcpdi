TCPDI
=====

PDF importer for [TCPDF](http://www.tcpdf.org/), based on [FPDI](http://www.setasign.de/products/pdf-php-solutions/fpdi/).  Requires [pauln/tcpdi_parser](https://github.com/pauln/tcpdi_parser) and [fdpf_tpl](http://www.setasign.de/products/pdf-php-solutions/fpdi/downloads/).


Installation
------------

tcpdi.php to the directory containing TCPDF, along with [pauln/tcpdi_parser](https://github.com/pauln/tcpdi_parser) and [fdpf_tpl](http://www.setasign.de/products/pdf-php-solutions/fpdi/downloads/)


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