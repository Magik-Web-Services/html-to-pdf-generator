# Libraries available to Generate PDF from HTML

## 1. HTML to PDF

### Example 1: Generate PDF from HTML containing only text.
```html
<!DOCTYPE html>
<html lang="en">
<head>
<title>Generate PDF from HTML using html to pdf library</title>
<!-- CDN URL - html2pdf library -->
<script
	src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
</head>

<body>
	<?php require_once __DIR__ . '/template.html'; ?>

	<script>
		var buttonElement = document.querySelector("#btn-generate");
		buttonElement.addEventListener('click', function() {
			var pdfContent = document.querySelector("#pdf-content");
			html2pdf().from(pdfContent).save();
		});
	</script>
</body>
</html>
```

### Example 2: Supply library options to override defaults
```js
var pdfContent = document.querySelector("#pdf-content");
var optionArray = {
  margin:       10,
  filename:     'output.pdf',
  jsPDF:        { unit: 'in', format: 'letter', orientation: 'portrait' }
};

// html to pdf generation with the reference of PDF worker object
html2pdf().set(optionArray).from(pdfContent).save();
```

## 2. jsPDF

### Example 1: Equivalent alternative solution to generate PDF from HTML
```html
<!DOCTYPE html>
<html>
<head>
<title>jsPDF Example</title>
<script
	src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script
	src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script
	src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
</head>
<body>
	<?php require_once __DIR__ . '/template.html'; ?>

	<script>
		var buttonElement = document.querySelector("#btn-generate");
		buttonElement.addEventListener('click', function() {
			const { jsPDF } = window.jspdf;
			var doc = new jsPDF("p", "pt", 'a4');
			var pdfContent = document.querySelector("#pdf-content");

			// Generate PDF from HTML using right id-selector
			doc.html(pdfContent, {
				callback: function(doc) {
				doc.save("download.pdf");
				},
				x: 10,
				y: 19
			});
		});
	</script>
</body>
</html>
```

### Example 2: Generate multipage PDF from HTML page

```html
<!doctype html>
<HTML>
<HEAD>
<TITLE>Generate multi-page PDF from HTML - jsPDF</TITLE>
<script src="https://code.jquery.com/jquery-2.1.3.js"></script>
<script
	src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/1.3.3/jspdf.min.js"></script>
<script
	src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
<style>
.description {
    font-size: 2em;
    line-height: 4;
}
</style>
</HEAD>
<BODY>
	<div class="outer-content">
		<div id="pdf-content">

			<img src="cherry.jpeg" />
			<p class="description">Donec id leo quis felis vehicula bibendum sit amet ut tellus. Sed
	sagittis aliquet rhoncus. Pellentesque vulputate nunc ultricies,
	egestas augue ac, ullamcorper dui. Etiam diam mauris, molestie in purus
	a, venenatis pretium leo. Sed id metus eleifend, scelerisque neque id,
	posuere arcu. Nunc cursus et dui quis fringilla. In in turpis feugiat
	diam porttitor tempus. In hendrerit diam dolor, id pellentesque ante
	volutpat a. Nam tortor sapien, semper ut justo et, accumsan imperdiet
	sem. Sed euismod nunc vitae dolor porttitor ullamcorper. Donec sed
	lacinia dui. Nunc sed suscipit erat. Phasellus euismod ultrices velit,
	hendrerit tempor diam elementum ut.</p>
	
			<hr>
		</div>

		<button id="btn-generate">Generate PDF</button>
	</div>

</BODY>
<script>
$(document).ready(function(){
	$("#btn-generate").click(function(){
		var htmlWidth = $("#pdf-content").width();
		var htmlHeight = $("#pdf-content").height();
		var pdfWidth = htmlWidth + (15 * 2);
		var pdfHeight = (pdfWidth * 1.5) + (15 * 2);
		
		var doc = new jsPDF('p', 'pt', [pdfWidth, pdfHeight]);
	
		var pageCount = Math.ceil(htmlHeight / pdfHeight) - 1;
	
	
		html2canvas($("#pdf-content")[0], { allowTaint: true }).then(function(canvas) {
			canvas.getContext('2d');
	
			var image = canvas.toDataURL("image/png", 1.0);
			doc.addImage(image, 'PNG', 15, 15, htmlWidth, htmlHeight);
	
	
			for (var i = 1; i <= pageCount; i++) {
				doc.addPage(pdfWidth, pdfHeight);
				doc.addImage(image, 'PNG', 15, -(pdfHeight * i)+15, htmlWidth, htmlHeight);
			}
			
		doc.save("output.pdf");
		});
	});
});
</script>
</HTML>
```

## 3. wkHTMLtoPDF
```php
<?php
use mikehaertl\wkhtmlto\Pdf;

$pdf = new Pdf('html-source-file.html');

if (!$pdf->saveAs('output.pdf')) {
    $error = $pdf->getError();
    // return error to the request handler
}
```

## 4. pdfmake

```html
<!DOCTYPE html>
<html>
<head>
<title>PDFMake Example</title>
<script
	src="https://cdnjs.cloudflare.com/ajax/libs/pdfmake/0.2.5/pdfmake.min.js"></script>
<script
	src="https://cdnjs.cloudflare.com/ajax/libs/pdfmake/0.2.5/vfs_fonts.min.js"></script>
</head>
<body>
	<div class="outer-content">
		<h2 id="heading">PDF Page Title</h2>
		<div id="pdf-content">How to generate a pdf from the UI content description and example.
		</div>
		<img src="cherry.jpeg" id="image-preview" />
		<hr>
		<button id="btn-generate">Generate PDF</button>
	</div>

	<script>
		var buttonElement = document.querySelector("#btn-generate");
		buttonElement.addEventListener('click', function() {
			var pdfHeading = document.querySelector("#heading").innerText;
			var pdfContent = document.querySelector("#pdf-content").innerText;
            var pdfImage = getDataUrl(document.querySelector("#image-preview"));
            var docDefinition = {
            	content: [
            		{ 
            			text: pdfHeading,
            			style: 'heading'
            		},
            		{ 
            			text: pdfContent,
            			style: 'vspace'
            		},
            		{ 
            			image: pdfImage
            		}
            	],
            	styles: {
            		vspace: {
            			lineHeight: 3
            		},
            		heading: {
            			fontSize: 18,
            			lineHeight: 2
            		}
            	}
            };
            pdfMake.createPdf(docDefinition).open();
		});
		
		function getDataUrl(imgSource) {
           const canvas = document.createElement('canvas');
           const context = canvas.getContext('2d');
           canvas.width = imgSource.width;
           canvas.height = imgSource.height;
           context.drawImage(imgSource, 0, 0);
           return canvas.toDataURL('image/jpeg');
        }
	</script>
</body>
</html>
```