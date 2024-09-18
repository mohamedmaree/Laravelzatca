
# This extension for laravel 10

This extension allows you to use zatca phase2 in KSA develope by Eng/Mohammed alyousfy 

# Installation steps

### Installation Using Composer (Recommended)
```bash
composer require mohammadzat/zatca


```
### Manual Setup

* integration examples:

```bash
<?php

namespace Mohammad\Zatca;
use Mohammad\Zatca\ZATCA\EGS;

use DOMDocument;
use Exception;

use Endroid\QrCode\QrCode;
use Endroid\QrCode\Logo\Logo;
use Endroid\QrCode\Color\Color;
use Endroid\QrCode\Label\Label;
use Endroid\QrCode\Writer\PngWriter;
use Endroid\QrCode\Encoding\Encoding;
use Endroid\QrCode\ErrorCorrectionLevel;

const ROOT_PATH=__DIR__ ;
use Illuminate\Http\Request;

class ZatController extends Controller
{
    
    public function index()
    { 
for ($i = 1; $i <= 3; $i++) {
    $line_item = [
        'id' => (string)$i,
        'name' => 'TEST NAME ' . $i,
        'quantity' => 10,
        'tax_exclusive_price' => 10,
        'VAT_percent' => 0.15,
        'other_taxes' => [],
        'discounts' => [],
    ];
    
    $line_items[] = $line_item; // Add the line item to the array
}
$egs_unit = [
    'uuid' => '6f4d20e0-6bfe-4a80-9389-7dabe6620f12',
    'custom_id' => 'EGS1-886431145',
    'model' => 'IOS',
    'CRN_number' => '454634645645654',
    'VAT_name' => 'موسسه تسالى',
    'VAT_number' => '301121971500003',
    'location' => [
        'city' => 'الرياض',
        'city_subdivision' => 'West',
        'street' => 'شارع الملك فهد',
        'plot_identification' => '0000',
        'building' => '0000',
        'postal_zone' => '31952',
    ],
    'branch_name' => 'تسالي',
    'branch_industry' => 'Food',
    'cancelation' => [
        'cancelation_type' => 'INVOICE',
        'canceled_invoice_number' => '',
    ],
];
$invoice = [
    'invoice_counter_number' => 2,
    'invoice_serial_number' => 'EGS1-886431145-1',
    'issue_date' => '2022-03-13',
    'issue_time' => '14:40:40',
    'previous_invoice_hash' => 'NWZlY2ViNjZmZmM4NmYzOGQ5NTI3ODZjNmQ2OTZjNzljMmRiYzIzOWRkNGU5MWI0NjcyOWQ3M2EyN2ZiNTdlOQ==', // AdditionalDocumentReference/PIH
    'line_items' => [
    
    ],
];
for ($i = 0; $i < count($line_items); $i++) {
    $invoice['line_items'][] = $line_items[$i];
}

$egs = new EGS($egs_unit);


$egs->production = false;

// New Keys & CSR for the EGS
/*list($private_key, $csr) = $egs->generateNewKeysAndCSR('Qr');
echo $private_key;

echo */
list($private_key, $csr) = $egs->generateNewKeysAndCSR('Qr');

echo "\n Private Key:\n";
echo $private_key . "\n\n";
     echo "CSR:\n";
echo $csr . "\n\n\n";
 
// Issue a new compliance cert for the EGS
list($request_id, $binary_security_token, $secret) = $egs->issueComplianceCertificate('123345', $csr);
echo "secret:\n";
echo $secret. "\n";  
// Sign invoice
list($signed_invoice_string, $invoice_hash, $qr,$public_key) = $egs->signInvoice($invoice, $egs_unit, $binary_security_token, $private_key);
echo "\n\n public key:\n";
//echo $signed_invoice_string. "\n\n\n";

$base64_encoded = base64_encode($signed_invoice_string);

// Output the Base64 encoded string
echo $base64_encoded;
//$file_path =base_path().'/tmp/invoice.xml';
$file_path = base_path() . '/public/invoice/invoice.xml';

// Check if the file exists
if (!file_exists($file_path)) {
    // Create the directory if it doesn't exist
    if (!is_dir(dirname($file_path))) {
        mkdir(dirname($file_path), 0755, true);
    }
    
    // Create a new file
    file_put_contents($file_path, '<?xml version="1.0" encoding="UTF-8"?>' . PHP_EOL . '<invoices></invoices>');
    
    echo "File created: $file_path";
} else {
   }
// Save the XML string to the file
if (file_put_contents($file_path, $signed_invoice_string) !== false) {
    echo "Invoice saved successfully to $file_path.\n";
} else {
    echo "Failed to save the invoice.\n";
}


// Generate QR Code
$qrCode = QrCode::create($qr)
    ->setEncoding(new Encoding('UTF-8'))
    ->setErrorCorrectionLevel(ErrorCorrectionLevel::High)
    ->setSize(300)
    ->setMargin(10)
    ->setForegroundColor(new Color(0, 0, 0))
    ->setBackgroundColor(new Color(255, 255, 255));

// Save QR Code to file
$writer = new PngWriter();
$logo = Logo::create(ROOT_PATH. '/ZATCA/assets/logo.png')
    ->setResizeToWidth(50)
    ->setPunchoutBackground(true);

$label = Label::create('Qr Phase-2')
    ->setTextColor(new Color(255, 0, 0));

$result = $writer->write($qrCode, $logo, $label);
$result->saveToFile(ROOT_PATH . '/ZATCA/assets/phase-2.png');

header('Content-Type: ' . $result->getMimeType()); 
       // return redirect()->route('task.create');
    }
}
```
# After running above code 
go to public directory and browse folder invoice and upload to Zatca test xml to show compliance results 
https://sandbox.zatca.gov.sa/TestXML


```bash
<?xml version="1.0" encoding="UTF-8"?>
<Invoice xmlns="urn:oasis:names:specification:ubl:schema:xsd:Invoice-2" xmlns:cac="urn:oasis:names:specification:ubl:schema:xsd:CommonAggregateComponents-2" xmlns:cbc="urn:oasis:names:specification:ubl:schema:xsd:CommonBasicComponents-2" xmlns:ext="urn:oasis:names:specification:ubl:schema:xsd:CommonExtensionComponents-2">
 <ext:UBLExtensions>
  <ext:UBLExtension>
   <ext:ExtensionURI>urn:oasis:names:specification:ubl:dsig:enveloped:xades</ext:ExtensionURI>
   <ext:ExtensionContent>
    <x1:UBLDocumentSignatures xmlns:x1="urn:oasis:names:specification:ubl:schema:xsd:CommonSignatureComponents-2">
     <x2:SignatureInformation xmlns:x2="urn:oasis:names:specification:ubl:schema:xsd:SignatureAggregateComponents-2">
      <cbc:ID>urn:oasis:names:specification:ubl:signature:1</cbc:ID>
      <x3:ReferencedSignatureID xmlns:x3="urn:oasis:names:specification:ubl:schema:xsd:SignatureBasicComponents-2">urn:oasis:names:specification:ubl:signature:Invoice</x3:ReferencedSignatureID>
     </x2:SignatureInformation>
    </x1:UBLDocumentSignatures>
   </ext:ExtensionContent>
  </ext:UBLExtension>
 </ext:UBLExtensions>
 <cbc:ProfileID>reporting:1.0</cbc:ProfileID>
 <cbc:ID>EGS1-886431145-1</cbc:ID>
 <cbc:UUID>6f4d20e0-6bfe-4a80-9389-7dabe6620f12</cbc:UUID>
 <cbc:IssueDate>2024-09-17</cbc:IssueDate>
 <cbc:IssueTime>15:06:05</cbc:IssueTime>
<cbc:InvoiceTypeCode name="0100000">388</cbc:InvoiceTypeCode>
 

 <cbc:DocumentCurrencyCode>SAR</cbc:DocumentCurrencyCode>
 <cbc:TaxCurrencyCode>SAR</cbc:TaxCurrencyCode>
 <cac:AdditionalDocumentReference>
  <cbc:ID>ICV</cbc:ID>
  <cbc:UUID>2</cbc:UUID>
 </cac:AdditionalDocumentReference>
 <cac:AdditionalDocumentReference>
  <cbc:ID>PIH</cbc:ID>
  <cac:Attachment>
   <cbc:EmbeddedDocumentBinaryObject mimeCode="text/plain">NWZlY2ViNjZmZmM4NmYzOGQ5NTI3ODZjNmQ2OTZjNzljMmRiYzIzOWRkNGU5MWI0NjcyOWQ3M2EyN2ZiNTdlOQ==</cbc:EmbeddedDocumentBinaryObject>
  </cac:Attachment>
 </cac:AdditionalDocumentReference>
 <cac:AdditionalDocumentReference>
  <cbc:ID>QR</cbc:ID>
 </cac:AdditionalDocumentReference>
 <cac:Signature>
  <cbc:ID>urn:oasis:names:specification:ubl:signature:Invoice</cbc:ID>
  <cbc:SignatureMethod>urn:oasis:names:specification:ubl:dsig:enveloped:xades</cbc:SignatureMethod>
 </cac:Signature>
 <cac:AccountingSupplierParty>
  <cac:Party>
   <cac:PartyIdentification>
    <cbc:ID schemeID="CRN">454634645645654</cbc:ID>
   </cac:PartyIdentification>
   <cac:PostalAddress>
    <cbc:StreetName>شارع الملك فهد</cbc:StreetName>
    <cbc:BuildingNumber>0000</cbc:BuildingNumber>
    <cbc:PlotIdentification>0000</cbc:PlotIdentification>
    <cbc:CitySubdivisionName>West</cbc:CitySubdivisionName>
    <cbc:CityName>الرياض</cbc:CityName>
    <cbc:PostalZone>31952</cbc:PostalZone>
    <cac:Country>
     <cbc:IdentificationCode>SA</cbc:IdentificationCode>
    </cac:Country>
   </cac:PostalAddress>
   <cac:PartyTaxScheme>
    <cbc:CompanyID>301121971500003</cbc:CompanyID>
    <cac:TaxScheme>
     <cbc:ID>VAT</cbc:ID>
    </cac:TaxScheme>
   </cac:PartyTaxScheme>
   <cac:PartyLegalEntity>
    <cbc:RegistrationName>موسسه تسالى</cbc:RegistrationName>
   </cac:PartyLegalEntity>
  </cac:Party>
 </cac:AccountingSupplierParty>
 <cac:AccountingCustomerParty>
  <cac:Party>
   <cac:PartyIdentification>
    <cbc:ID schemeID="NAT">311111111111113</cbc:ID>
   </cac:PartyIdentification>
   <cac:PostalAddress>
    <cbc:StreetName>شارع الملك فهد</cbc:StreetName>
    <cbc:BuildingNumber>0000</cbc:BuildingNumber>
    <cbc:PlotIdentification>0000</cbc:PlotIdentification>
    <cbc:CitySubdivisionName>West</cbc:CitySubdivisionName>
    <cbc:CityName>الرياض</cbc:CityName>
    <cbc:PostalZone>31952</cbc:PostalZone>
    <cac:Country>
     <cbc:IdentificationCode>SA</cbc:IdentificationCode>
    </cac:Country>
   </cac:PostalAddress>
   <cac:PartyTaxScheme>
    <cac:TaxScheme>
     <cbc:ID>VAT</cbc:ID>
    </cac:TaxScheme>
   </cac:PartyTaxScheme>
   <cac:PartyLegalEntity>
    <cbc:RegistrationName>موسسه تسالى</cbc:RegistrationName>
   </cac:PartyLegalEntity>
  </cac:Party>
 </cac:AccountingCustomerParty>
 <cac:Delivery>
  <cbc:ActualDeliveryDate>2022-03-13</cbc:ActualDeliveryDate>
 </cac:Delivery>
 <cac:PaymentMeans>
  <cbc:PaymentMeansCode>10</cbc:PaymentMeansCode>
 </cac:PaymentMeans>
 <cac:AllowanceCharge>
  <cbc:ChargeIndicator>false</cbc:ChargeIndicator>
  <cbc:AllowanceChargeReason>discount</cbc:AllowanceChargeReason>
  <cbc:Amount currencyID="SAR">0.00</cbc:Amount>
  <cac:TaxCategory>
   <cbc:ID schemeID="UN/ECE 5305" schemeAgencyID="6">S</cbc:ID>
   <cbc:Percent>15</cbc:Percent>
   <cac:TaxScheme>
    <cbc:ID schemeID="UN/ECE 5153" schemeAgencyID="6">VAT</cbc:ID>
   </cac:TaxScheme>
  </cac:TaxCategory>
 </cac:AllowanceCharge>
 <cac:TaxTotal>
        <cbc:TaxAmount currencyID="SAR">45.00</cbc:TaxAmount>
        <cac:TaxSubtotal>
            <cbc:TaxableAmount currencyID="SAR">100.00</cbc:TaxableAmount>
            <cbc:TaxAmount currencyID="SAR">15.00</cbc:TaxAmount>
            <cac:TaxCategory>
                <cbc:ID schemeAgencyID="6" schemeID="UN/ECE 5305">S</cbc:ID>
                <cbc:Percent>15.00</cbc:Percent>
                <cac:TaxScheme>
                    <cbc:ID schemeAgencyID="6" schemeID="UN/ECE 5153">VAT</cbc:ID>
                </cac:TaxScheme>
            </cac:TaxCategory>
        </cac:TaxSubtotal>
        <cac:TaxSubtotal>
            <cbc:TaxableAmount currencyID="SAR">100.00</cbc:TaxableAmount>
            <cbc:TaxAmount currencyID="SAR">15.00</cbc:TaxAmount>
            <cac:TaxCategory>
                <cbc:ID schemeAgencyID="6" schemeID="UN/ECE 5305">S</cbc:ID>
                <cbc:Percent>15.00</cbc:Percent>
                <cac:TaxScheme>
                    <cbc:ID schemeAgencyID="6" schemeID="UN/ECE 5153">VAT</cbc:ID>
                </cac:TaxScheme>
            </cac:TaxCategory>
        </cac:TaxSubtotal>
        <cac:TaxSubtotal>
            <cbc:TaxableAmount currencyID="SAR">100.00</cbc:TaxableAmount>
            <cbc:TaxAmount currencyID="SAR">15.00</cbc:TaxAmount>
            <cac:TaxCategory>
                <cbc:ID schemeAgencyID="6" schemeID="UN/ECE 5305">S</cbc:ID>
                <cbc:Percent>15.00</cbc:Percent>
                <cac:TaxScheme>
                    <cbc:ID schemeAgencyID="6" schemeID="UN/ECE 5153">VAT</cbc:ID>
                </cac:TaxScheme>
            </cac:TaxCategory>
        </cac:TaxSubtotal>
    </cac:TaxTotal>
    <cac:TaxTotal>
        <cbc:TaxAmount currencyID="SAR">45.00</cbc:TaxAmount>
    </cac:TaxTotal>
    <cac:LegalMonetaryTotal>
        <cbc:LineExtensionAmount currencyID="SAR">300.00</cbc:LineExtensionAmount>
        <cbc:TaxExclusiveAmount currencyID="SAR">300.00</cbc:TaxExclusiveAmount>
        <cbc:TaxInclusiveAmount currencyID="SAR">345.00</cbc:TaxInclusiveAmount>
        <cbc:AllowanceTotalAmount currencyID="SAR">0</cbc:AllowanceTotalAmount>
        <cbc:PrepaidAmount currencyID="SAR">0</cbc:PrepaidAmount>
        <cbc:PayableAmount currencyID="SAR">345.00</cbc:PayableAmount>
    </cac:LegalMonetaryTotal>
    <cac:InvoiceLine>
        <cbc:ID>1</cbc:ID>
        <cbc:InvoicedQuantity unitCode="PCE">10</cbc:InvoicedQuantity>
        <cbc:LineExtensionAmount currencyID="SAR">100.00</cbc:LineExtensionAmount>
        <cac:TaxTotal>
            <cbc:TaxAmount currencyID="SAR">15.00</cbc:TaxAmount>
            <cbc:RoundingAmount currencyID="SAR">115.00</cbc:RoundingAmount>
        </cac:TaxTotal>
        <cac:Item>
            <cbc:Name>TEST NAME 1</cbc:Name>
            <cac:ClassifiedTaxCategory>
                <cbc:ID>S</cbc:ID>
                <cbc:Percent>15.00</cbc:Percent>
                <cac:TaxScheme>
                    <cbc:ID>VAT</cbc:ID>
                </cac:TaxScheme>
            </cac:ClassifiedTaxCategory>
        </cac:Item>
        <cac:Price>
            <cbc:PriceAmount currencyID="SAR">10</cbc:PriceAmount>
        </cac:Price>
    </cac:InvoiceLine>
    <cac:InvoiceLine>
        <cbc:ID>2</cbc:ID>
        <cbc:InvoicedQuantity unitCode="PCE">10</cbc:InvoicedQuantity>
        <cbc:LineExtensionAmount currencyID="SAR">100.00</cbc:LineExtensionAmount>
        <cac:TaxTotal>
            <cbc:TaxAmount currencyID="SAR">15.00</cbc:TaxAmount>
            <cbc:RoundingAmount currencyID="SAR">115.00</cbc:RoundingAmount>
        </cac:TaxTotal>
        <cac:Item>
            <cbc:Name>TEST NAME 2</cbc:Name>
            <cac:ClassifiedTaxCategory>
                <cbc:ID>S</cbc:ID>
                <cbc:Percent>15.00</cbc:Percent>
                <cac:TaxScheme>
                    <cbc:ID>VAT</cbc:ID>
                </cac:TaxScheme>
            </cac:ClassifiedTaxCategory>
        </cac:Item>
        <cac:Price>
            <cbc:PriceAmount currencyID="SAR">10</cbc:PriceAmount>
        </cac:Price>
    </cac:InvoiceLine>
    <cac:InvoiceLine>
        <cbc:ID>3</cbc:ID>
        <cbc:InvoicedQuantity unitCode="PCE">10</cbc:InvoicedQuantity>
        <cbc:LineExtensionAmount currencyID="SAR">100.00</cbc:LineExtensionAmount>
        <cac:TaxTotal>
            <cbc:TaxAmount currencyID="SAR">15.00</cbc:TaxAmount>
            <cbc:RoundingAmount currencyID="SAR">115.00</cbc:RoundingAmount>
        </cac:TaxTotal>
        <cac:Item>
            <cbc:Name>TEST NAME 3</cbc:Name>
            <cac:ClassifiedTaxCategory>
                <cbc:ID>S</cbc:ID>
                <cbc:Percent>15.00</cbc:Percent>
                <cac:TaxScheme>
                    <cbc:ID>VAT</cbc:ID>
                </cac:TaxScheme>
            </cac:ClassifiedTaxCategory>
        </cac:Item>
        <cac:Price>
            <cbc:PriceAmount currencyID="SAR">10</cbc:PriceAmount>
        </cac:Price>
    </cac:InvoiceLine>
</Invoice>

```
