<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Ibn Sina Hospital Bill Generator</title>
    <script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
    <style>
        @page {
            size: A5;
            margin: 0;
        }
        
        body {
            margin: 0;
            padding: 0;
            font-family: 'Arial', sans-serif;
            background: #f5f5f5;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            font-size: 13px;
        }
        
        .a5-container {
            width: 14.8cm;
            min-height: 21cm;
            background: white;
            margin: 10px auto;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
            position: relative;
            padding: 15px;
            box-sizing: border-box;
        }
        
        .invoice-section {
            text-align: right;
            margin-bottom: 0px;
            padding-bottom: 0px;
        }
        
        .invoice-label {
            font-weight: normal;
            color: #454545;
            font-size: 10px;
            display: inline-block;
            margin-right: 0px;
        }
        
        .invoice-input {
            display: inline-block;
            width: 30px;
            padding: 1px 4px;
            border: 0px solid #ffffff;
            border-radius: 3px;
            font-size: 9px;
            font-weight: normal;
            text-align: left;
        }
        
        /* Hospital Box - NO bottom border */
        .hospital-box {
            text-align: center;
            padding: 15px 0;
            margin-bottom: 20px;
            background: #ffffff;
            border-bottom: 0px solid #000; /* শুধু স্ক্রীনের জন্য */
        }
        
        .hospital-bangla {
            font-family: 'Arial', sans-serif;
            font-size: 20px;
            font-weight: bold;
            color: #000;
            margin-bottom: 5px;
            line-height: 1.2;
        }
        
        .hospital-divider {
            height: 2px;
            background: #000;
            margin: 5px auto;
            width: 69%;
        }
        
        .hospital-english {
            font-size: 22px;
            font-weight: bold;
            color: #dd0000;
            margin-top: 5px;
            line-height: 1.2;
        }
        
        .datetime-barcode-line {
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-top: 2px solid #3c3c3c;
            border-right: 2px solid #3c3c3c;
            border-left: 2px solid #3c3c3c;
        }
        
        .date-section, .time-section {
            display: flex;
            align-items: center;
            flex: 1;
        }
        
        .date-section {
            justify-content: flex-start;
            padding-left: 10px;
        }
        
        .time-section {
            justify-content: center;
            padding-left: 10px;
        }
        
        .barcode-section {
            flex: 2;
            text-align: center;
            margin: 0 10px;
        }
        
        .barcode-img {
            height: 35px;
            width: 150px;
            background: #f9f9f9;
        }
        
        .datetime-label {
            font-weight: normal;
            color: #333;
            font-size: 13px;
            margin-right: 5px;
        }
        
        .datetime-value {
            font-weight: normal;
            color: #000;
            font-size: 13px;
        }
        
        .patient-info {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 5px;
            margin-bottom: 8px;
            padding-bottom: 10px;
            padding-left: 10px;
            border-bottom: 2px solid #3c3c3c;
            border-left: 2px solid #3c3c3c;
            border-right: 2px solid #3c3c3c;
        }
        
        .info-item {
            display: flex;
            align-items: center;
        }
        
        .info-label {
            font-weight: normal;
            min-width: 60px;
            color: #333;
            font-size: 13px;
        }
        
        .info-value {
            color: #000;
            font-size: 13px;
            font-weight: normal;
        }
        
        .test-table {
            width: 100%;
            border-collapse: collapse;
            margin: 8px 0;
            font-size: 14px;
            border: 2px solid #3c3c3c;
        }
        
        .test-table th {
            background-color: #ffffff;
            color: #000;
            padding: 8px 5px;
            text-align: center;
            font-weight: bold;
            border: 2px solid #3c3c3c;
            font-size: 13px;
        }
        
        .test-table td {
            padding: 4px 5px;
            border: 0px solid #000;
            font-size: 12px;
            font-weight: normal;
            text-align: center;
        }
        
        .payment-container {
            width: 65%;
            margin: 15px auto;
            display: flex;
            padding-left: 60px;
            padding-top: 60px;
            gap: 12px;
            align-items: flex-start;
        }
        
        .payment-box {
            flex: 1;
            border: 1px dotted #3c3c3c;
            padding: 6px;
            border-radius: 5px;
            background: #fff;
        }
        
        /* Payment row with underline/dag */
        .payment-row {
            display: flex;
            justify-content: space-between;
            padding: 5px 0;
            position: relative;
        }
        
        /* Dag/Underline for specific rows */
        .payment-row.dag {
            border-bottom: 1px solid #000;
            margin-bottom: 2px;
            padding-bottom: 8px;
        }
        
        /* Double underline for Payable */
        .payment-row.payable-row {
            border-bottom: 2px solid #000;
            margin-bottom: 2px;
            padding-bottom: 8px;
        }
        
        .payment-label {
            font-weight: bold;
            color: #000000;
            font-size: 13px;
        }
        
        .payment-value {
            font-weight: bold;
            color: #000000;
            font-size: 13px;
        }
        
        .qr-box {
            flex-shrink: 0;
            width: 100px;
            border: 0px solid #fff;
            padding: 10px;
            border-radius: 0px;
            background: white;
            text-align: center;
        }
        
        .qr-code {
            width: 110px;
            height: 110px;
            margin: 0 auto 5px;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        
        .qr-label {
            font-size: 10px;
            font-weight: normal;
            color: #000000;
            margin-top: 5px;
        }
        
        .footer-section {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            margin-top: 20px;
            padding-top: 15px;
        }
        
        .left-footer {
            display: flex;
            flex-direction: column;
            align-items: flex-start;
            gap: 10px;
        }
        
        .paid-box {
            border: 1px dotted;
            padding: 4px 10px;
            font-size: 20px;
            font-weight: bold;
            color: #000000;
            background: #d2d2d2;
            transform: rotate(0deg);
        }
        
        .middle-footer {
            margin-left: 1cm;
        }
        
        .out-sample {
            font-weight: bold;
            font-size: 18px;
            color: #000;
            text-align: center;
            padding: 5px 6px;
            border: 1px dotted #000;
            background: #c4c4c4;
            transform: rotate(0deg);
            width: 120px;
        }
        
        .right-footer {
            display: flex;
            flex-direction: column;
            align-items: flex-end;
        }
        
        /* Room Instruction and Box - NOW IN ONE LINE */
        .room-instruction-container {
            display: flex;
            flex-direction: row;
            align-items: center;
            justify-content: flex-start;
            margin-top: 15px;
            width: 100%;
            gap: 10px;
        }
        
        .room-instruction {
            font-family: 'Arial', sans-serif;
            font-size: 12px;
            color: #000;
            font-weight: bold;
            white-space: nowrap;
        }
        
        .room-box {
            width: 50px;
            height: 30px;
            border: 1px dotted;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
            font-weight: bold;
            color: #000;
            background: white;
            flex-shrink: 0;
        }
        
        .control-panel {
            width: 14.8cm;
            background: white;
            padding: 15px;
            margin-bottom: 15px;
            border-radius: 6px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        
        .control-title {
            color: #000;
            margin-bottom: 15px;
            padding-bottom: 8px;
            border-bottom: 2px solid #000;
            text-align: center;
            font-size: 18px;
        }
        
        .form-group {
            margin-bottom: 12px;
        }
        
        .form-label {
            display: block;
            font-weight: bold;
            margin-bottom: 4px;
            color: #333;
            font-size: 13px;
        }
        
        .form-input {
            width: 100%;
            padding: 8px 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            font-size: 13px;
        }
        
        .row {
            display: flex;
            gap: 12px;
            margin-bottom: 12px;
        }
        
        .row > div {
            flex: 1;
        }
        
        .test-controls {
            background: #f8f9fa;
            padding: 15px;
            border-radius: 6px;
            margin: 15px 0;
            border: 1px solid #ddd;
        }
        
        .btn {
            padding: 10px 18px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-weight: bold;
            font-size: 13px;
            margin-right: 10px;
            margin-top: 10px;
        }
        
        .btn-primary {
            background-color: #007bff;
            color: white;
        }
        
        .btn-success {
            background-color: #28a745;
            color: white;
        }
        
        .btn-info {
            background-color: #17a2b8;
            color: white;
        }
        
        .btn-warning {
            background-color: #ffc107;
            color: black;
        }
        
        .btn:hover {
            opacity: 0.9;
        }
        
        .button-group {
            text-align: center;
            margin-top: 15px;
            padding-top: 15px;
            border-top: 1px solid #eee;
        }
        
        /* Medical Reference Footer */
        .medical-reference-footer {
            text-align: center;
            margin: 20px auto 0;
            padding: 15px;
            border-top: 2px solid #ccc;
            width: 90%;
            font-size: 10px;
            color: #555;
            font-family: Arial, sans-serif;
            background: #f9f9f9;
            border-radius: 5px;
            display: none;
        }
        
        /* Print Styles */
        @media print {
            body {
                background: white !important;
                margin: 0 !important;
                padding: 0 !important;
                width: 14.8cm !important;
                font-size: 12px !important;
            }
            
            .control-panel {
                display: none !important;
            }
            
            .a5-container {
                box-shadow: none !important;
                margin: 0 auto !important;
                width: 14.8cm !important;
                padding: 12px !important;
                min-height: 20.5cm !important;
                -webkit-print-color-adjust: exact !important;
                color-adjust: exact !important;
            }
            
            /* Hospital Box - NO bottom border for print */
            .hospital-box {
                border-bottom: none !important; /* এখানে border সরানো হয়েছে */
                padding: 15px 0 !important;
                margin-bottom: 20px !important;
                background: white !important;
            }
            
            /* Divider line should be visible in print */
            .hospital-divider {
                display: block !important;
                height: 2px !important;
                background: #000 !important;
                margin: 5px auto !important;
                width: 70% !important;
                visibility: visible !important;
                opacity: 1 !important;
            }
            
            .test-table {
                font-size: 11px !important;
            }
            
            .test-table th,
            .test-table td {
                padding: 7px 4px !important;
            }
            
            /* Payment row dag/underline for print */
            .payment-row.dag {
                border-bottom: 1px solid #000 !important;
                margin-bottom: 2px !important;
                padding-bottom: 8px !important;
            }
            
            .payment-row.payable-row {
                border-bottom: 2px solid #000 !important;
                margin-bottom: 2px !important;
                padding-bottom: 8px !important;
            }
            
            /* QR Code Box Border Removed for Print */
            .qr-box {
                border: 0px solid transparent !important;
                padding: 20px !important;
                background: white !important;
            }
            
            /* QR Code Image Styling for Print */
            .qr-code {
                width: 100px !important;
                height: 100px !important;
                margin: 0 auto 5px !important;
                border: none !important;
                background: white !important;
            }
            
            .qr-code img {
                width: 100px !important;
                height: 100px !important;
                border: none !important;
                background: white !important;
            }
            
            .invoice-section input {
                border: none !important;
                background: transparent !important;
                -webkit-print-color-adjust: exact !important;
                print-color-adjust: exact !important;
            }
            
            /* Room instruction in one line for print */
            .room-instruction-container {
                display: flex !important;
                flex-direction: row !important;
                align-items: center !important;
                margin-top: 10px !important;
            }
            
            /* Show medical reference footer only in print */
            .medical-reference-footer {
                display: block !important;
                page-break-inside: avoid;
                margin-top: 5px !important;
                padding: 10px !important;
                font-size: 9px !important;
                border-top: 2px solid #ccc !important;
            }
            
            /* Hide browser headers/footers */
            @page {
                margin: 0 !important;
                size: A5 !important;
            }
            
            /* Ensure all elements print properly */
            * {
                -webkit-print-color-adjust: exact !important;
                print-color-adjust: exact !important;
            }
        }
    </style>
</head>
<body>
    <!-- Control Panel - Hidden during print -->
    <div class="control-panel">
        <h2 class="control-title">ইবনে সিনা হাসপাতাল সিলেট লিমিটেড</h2>
        
        <div class="row">
            <div class="form-group">
                <label class="form-label">Invoice Number (Manual):</label>
                <input type="text" id="inputInvoice" class="form-input" value="45864">
            </div>
            <div class="form-group">
                <label class="form-label">Room Number (Manual):</label>
                <input type="text" id="inputRoom" class="form-input" value="307">
            </div>
        </div>
        
        <div class="row">
            <div class="form-group">
                <label class="form-label">Patient Name (Random):</label>
                <input type="text" id="inputName" class="form-input" value="Ahmed Rahman">
            </div>
            <div class="form-group">
                <label class="form-label">ID Number (Random):</label>
                <input type="text" id="inputId" class="form-input" value="Y213321">
            </div>
        </div>
        
        <div class="row">
            <div class="form-group">
                <label class="form-label">Age (Random):</label>
                <input type="text" id="inputAge" class="form-input" value="30">
            </div>
            <div class="form-group">
                <label class="form-label">Sex (Random):</label>
                <select id="inputSex" class="form-input">
                    <option value="Male">Male</option>
                    <option value="Female">Female</option>
                </select>
            </div>
            <div class="form-group">
                <label class="form-label">Mobile No:</label>
                <input type="text" id="inputMobile" class="form-input" value="01926626565">
            </div>
        </div>
        
        <div class="form-group">
            <label class="form-label">Reference Doctor (Fixed):</label>
            <input type="text" id="inputRef" class="form-input" value="BTS_SGH" readonly style="background-color: #f0f0f0; font-weight: bold;">
        </div>
        
        <div class="test-controls">
            <h4 style="color: #000; margin-bottom: 12px; font-size: 16px;">Test Details</h4>
            
            <div class="row">
                <div class="form-group">
                    <label class="form-label">Test Name (Fixed):</label>
                    <input type="text" id="inputTestName" class="form-input" value="HBsAg (E)" readonly style="background-color: #f0f0f0; font-weight: bold;">
                </div>
                <div class="form-group">
                    <label class="form-label">Quantity (Manual Input):</label>
                    <input type="number" id="inputQty" class="form-input" value="1" min="1" max="10">
                </div>
            </div>
            
            <div class="row">
                <div class="form-group">
                    <label class="form-label">Amount (Random):</label>
                    <input type="number" id="inputAmount" class="form-input" value="802" min="0">
                </div>
                <div class="form-group">
                    <label class="form-label">Discount % (Random):</label>
                    <input type="number" id="inputDiscount" class="form-input" value="40" min="0" max="100">
                </div>
            </div>
            
            <div class="row">
                <div class="form-group">
                    <label class="form-label">Delivery Date & Time (Auto):</label>
                    <input type="text" id="inputDelivery" class="form-input" value="20/12/25 02:00PM">
                </div>
            </div>
        </div>
        
        <div class="button-group">
            <button class="btn btn-primary" onclick="updateBill()">Update Bill</button>
            <button class="btn btn-success" onclick="generateRandomBill()">Generate Random Bill</button>
            <button class="btn btn-info" onclick="printBill()">Print Bill</button>
            <button class="btn btn-warning" onclick="resetForm()">Reset Form</button>
        </div>
    </div>
    
    <!-- A5 Bill Container -->
    <div class="a5-container">
        <!-- Invoice Number Section -->
        <div class="invoice-section">
            <span class="invoice-label">Invoice No:</span>
            <input type="text" id="displayInvoice" class="invoice-input" value="45864" readonly>
        </div>
        
        <!-- Hospital Name Box - NO border in print -->
        <div class="hospital-box">
            <div class="hospital-bangla">ইবনে সিনা হাসপাতাল সিলেট লিমিটেড</div>
            <div class="hospital-divider"></div>
            <div class="hospital-english">IBN SINA HOSPITAL SYLHET LTD.</div>
        </div>
        
        <!-- Date, Barcode and Time -->
        <div class="datetime-barcode-line">
            <div class="date-section">
                <span class="datetime-label">Date :</span>
                <span class="datetime-value" id="displayDate">19 Dec 25</span>
            </div>
            
            <div class="barcode-section">
                <svg class="barcode-img" id="barcode"></svg>
            </div>
            
            <div class="time-section">
                <span class="datetime-label">Time :</span>
                <span class="datetime-value" id="displayTime">06:58PM</span>
            </div>
        </div>
        
        <!-- Patient Information -->
        <div class="patient-info">
            <div class="info-item">
                <div class="info-label">Name :</div>
                <div class="info-value" id="displayName">Ahmed Rahman</div>
            </div>
            <div class="info-item">
                <div class="info-label">ID No :</div>
                <div class="info-value" id="displayId">Y213321</div>
            </div>
            <div class="info-item">
                <div class="info-label">Age :</div>
                <div class="info-value" id="displayAge">30 year(s)</div>
            </div>
            <div class="info-item">
                <div class="info-label">Sex :</div>
                <div class="info-value" id="displaySex">Male</div>
            </div>
            <div class="info-item">
                <div class="info-label">Mobile :</div>
                <div class="info-value" id="displayMobile">01926626565</div>
            </div>
            <div class="info-item">
                <div class="info-label">Ref by :</div>
                <div class="info-value" id="displayRef">BTS_SGH</div>
            </div>
        </div>
        
        <!-- Test Table -->
        <table class="test-table">
            <thead>
                <tr>
                    <th width="7%">SL</th>
                    <th width="35%">Test/Service</th>
                    <th width="8%">Qty</th>
                    <th width="12%">Amount</th>
                    <th width="10%">Disc%</th>
                    <th width="28%">Delivery : Date/Time</th>
                </tr>
            </thead>
            <tbody id="testTableBody">
                <tr>
                    <td>1</td>
                    <td id="displayTestName">HBsAg (E)</td>
                    <td id="displayQty">1</td>
                    <td id="displayTestAmount">802.00</td>
                    <td id="displayTestDiscount">40</td>
                    <td id="displayDelivery">20/12/25 02:00PM</td>
                </tr>
            </tbody>
        </table>
        
        <!-- Payment Box with QR Code -->
        <div class="payment-container">
            <div class="payment-box">
                <!-- Total Amount - with underline -->
                <div class="payment-row dag">
                    <div class="payment-label">Total amount</div>
                    <div class="payment-value" id="totalAmount">802.00</div>
                </div>
                
                <!-- Discount - with underline -->
                <div class="payment-row dag">
                    <div class="payment-label" id="discountLabel">Discount (40%)</div>
                    <div class="payment-value" id="discountAmount">320.80</div>
                </div>
                
                <!-- Extra Discount - NO underline -->
                <div class="payment-row">
                    <div class="payment-label">Extra Discount</div>
                    <div class="payment-value" id="extraDiscount">0.00</div>
                </div>
                
                <!-- Payable - with double underline -->
                <div class="payment-row payable-row">
                    <div class="payment-label">Payable</div>
                    <div class="payment-value" id="payableAmount">481.20</div>
                </div>
                
                <!-- Received - with underline -->
                <div class="payment-row dag">
                    <div class="payment-label">Received</div>
                    <div class="payment-value" id="receivedAmount">481.20</div>
                </div>
                
                <!-- Due - NO underline -->
                <div class="payment-row">
                    <div class="payment-label">Due</div>
                    <div class="payment-value" id="dueAmount">0.00</div>
                </div>
            </div>
            
            <!-- QR Code Section -->
            <div class="qr-box">
                <div class="qr-code" id="qrcode"></div>
                <div class="qr-label">Scan for Report</div>
            </div>
        </div>
        
        <!-- Footer Section -->
        <div class="footer-section">
            <div class="left-footer">
                <div class="paid-box">PAID</div>
            </div>
            
            <div class="middle-footer">
                <div class="out-sample">OUT SAMPLE</div>
            </div>
            
            <div class="right-footer">
                <!-- Right side empty for balance -->
            </div>
        </div>
        
        <!-- Room Instruction and Box - NOW IN ONE LINE -->
        <div class="room-instruction-container">
            <div class="room-instruction">যে সকল রুমে যাবেন :</div>
            <div class="room-box" id="displayRoom">307</div>
            <div>For online report visit: https://tiny.one/labreport</div>
        </div>
        
        <!-- Medical Reference Footer (Shown only in print) -->
        <div class="medical-reference-footer">
            <p>রিপোর্ট ডেলিভারির সময়: সকাল ৮ টা থেকে রাত ১০ টা,ডেলিভারি তারিখ হতে ৩০ দিনের মধ্যে রিপোর্ট সংগ্রহ করা যাবে।</p>
            <p style="margin-top: 0px; font-size: 12px; color: #373737;">
            <p>Address:Mirabazar-Subhanighat Rd, Sylhet 3100.Email:  ibnsinadiagnostic@gmail.com</p>
            <p style="margin-top: 0px; font-size: 10px; color: #707070;">
                © 2025 Ibn Sina Diagnostic Center. This is a computer-generated document.
            </p>
        </div>
    </div>

    <script>
        // Global variables
        let currentAmount = 802;
        let currentDiscountPercent = 40;
        let currentId = 'Y213321';
        let currentName = 'Ahmed Rahman';
        let currentQty = 1;
        let currentInvoice = '45864';
        let currentRoom = '307';
        
        // Initialize
        document.addEventListener('DOMContentLoaded', function() {
            setCurrentDateTime();
            setDeliveryDateTime();
            generateQRCode();
            updateBill();
        });
        
        // Set delivery date/time
        function setDeliveryDateTime() {
            const today = new Date();
            const tomorrow = new Date(today);
            tomorrow.setDate(today.getDate() + 1);
            
            const formattedDate = `${tomorrow.getDate().toString().padStart(2, '0')}/` +
                                 `${(tomorrow.getMonth() + 1).toString().padStart(2, '0')}/` +
                                 `${tomorrow.getFullYear().toString().slice(-2)}`;
            
            document.getElementById('inputDelivery').value = `${formattedDate} 02:00PM`;
        }
        
        // Generate QR Code with better error handling
        function generateQRCode() {
            const qrUrl = "https://service.ibnsinatrust.com/report/contactconfirm";
            const qrElement = document.getElementById('qrcode');
            
            // Clear previous QR code
            qrElement.innerHTML = '';
            
            // Check if QRCode library is loaded
            if (typeof QRCode === 'undefined') {
                console.error('QRCode library not loaded');
                qrElement.innerHTML = `
                    <div style="
                        width: 100px; 
                        height: 100px; 
                        background: #f0f0f0; 
                        display: flex; 
                        align-items: center; 
                        justify-content: center;
                        font-size: 9px;
                        text-align: center;
                        padding: 5px;
                        color: #000;
                    ">
                        QR Code<br>
                        Scan for Report<br>
                        ${qrUrl.substring(0, 20)}...
                    </div>
                `;
                return;
            }
            
            try {
                new QRCode(qrElement, {
                    text: qrUrl,
                    width: 100,
                    height: 100,
                    colorDark: "#000000",
                    colorLight: "#ffffff",
                    correctLevel: QRCode.CorrectLevel.H
                });
                
                console.log('QR Code generated successfully');
            } catch (error) {
                console.error('QR Code generation error:', error);
                qrElement.innerHTML = `
                    <div style="
                        width: 100px; 
                        height: 100px; 
                        background: #f0f0f0; 
                        display: flex; 
                        align-items: center; 
                        justify-content: center;
                        border: none;
                        font-size: 9px;
                        text-align: center;
                        padding: 5px;
                        color: #000;
                    ">
                        <div>
                            QR Code<br>
                            Scan for Report<br>
                            ${qrUrl}
                        </div>
                    </div>
                `;
            }
        }
        
        // Update bill
        function updateBill() {
            currentName = document.getElementById('inputName').value || 'Ahmed Rahman';
            currentId = document.getElementById('inputId').value || 'Y213321';
            const age = document.getElementById('inputAge').value || '30';
            const sex = document.getElementById('inputSex').value || 'Male';
            const mobile = document.getElementById('inputMobile').value || '01926626565';
            currentAmount = parseFloat(document.getElementById('inputAmount').value) || 802;
            currentDiscountPercent = parseFloat(document.getElementById('inputDiscount').value) || 40;
            currentQty = parseInt(document.getElementById('inputQty').value) || 1;
            currentInvoice = document.getElementById('inputInvoice').value || '45864';
            currentRoom = document.getElementById('inputRoom').value || '307';
            const delivery = document.getElementById('inputDelivery').value;
            
            document.getElementById('displayName').textContent = currentName;
            document.getElementById('displayId').textContent = currentId;
            document.getElementById('displayAge').textContent = age + ' year(s)';
            document.getElementById('displaySex').textContent = sex;
            document.getElementById('displayMobile').textContent = mobile;
            document.getElementById('displayRef').textContent = 'BTS_SGH';
            document.getElementById('displayDelivery').textContent = delivery;
            document.getElementById('displayInvoice').value = currentInvoice;
            document.getElementById('displayRoom').textContent = currentRoom;
            
            document.getElementById('testTableBody').innerHTML = `
                <tr>
                    <td>1</td>
                    <td id="displayTestName">HBsAg (E)</td>
                    <td id="displayQty">${currentQty}</td>
                    <td id="displayTestAmount">${currentAmount.toFixed(2)}</td>
                    <td id="displayTestDiscount">${currentDiscountPercent}</td>
                    <td id="displayDelivery">${delivery}</td>
                </tr>
            `;
            
            const totalAmount = currentAmount * currentQty;
            const discountAmount = totalAmount * (currentDiscountPercent / 100);
            const extraDiscount = 0.00;
            const payableAmount = totalAmount - discountAmount - extraDiscount;
            
            document.getElementById('totalAmount').textContent = totalAmount.toFixed(2);
            document.getElementById('discountLabel').textContent = `Discount (${currentDiscountPercent}%)`;
            document.getElementById('discountAmount').textContent = discountAmount.toFixed(2);
            document.getElementById('extraDiscount').textContent = extraDiscount.toFixed(2);
            document.getElementById('payableAmount').textContent = payableAmount.toFixed(2);
            document.getElementById('receivedAmount').textContent = payableAmount.toFixed(2);
            document.getElementById('dueAmount').textContent = '0.00';
            
            updateBarcode();
        }
        
        // Generate random bill
        function generateRandomBill() {
            const randomInvoice = Math.floor(Math.random() * 90000) + 10000;
            document.getElementById('inputInvoice').value = randomInvoice;
            
            const randomRoom = Math.floor(Math.random() * 10) + 301;
            document.getElementById('inputRoom').value = randomRoom;
            
            const bengaliNames = [
                'Ahmed Rahman', 'Fatima Begum', 'Abdul Karim', 'Nur Jahan', 
                'Mohammed Ali', 'Ayesha Khatun', 'Hasan Chowdhury', 'Mina Akhter',
                'Rahim Uddin', 'Salma Islam', 'Jamal Hossain', 'Taslima Banu'
            ];
            
            const randomName = bengaliNames[Math.floor(Math.random() * bengaliNames.length)];
            document.getElementById('inputName').value = randomName;
            
            const randomId = 'Y' + (Math.floor(Math.random() * 900000) + 100000);
            document.getElementById('inputId').value = randomId;
            
            document.getElementById('inputAge').value = Math.floor(Math.random() * 48) + 18;
            
            document.getElementById('inputSex').value = Math.random() > 0.5 ? 'Male' : 'Female';
            
            const mobiles = ['01926626565', '01712345678', '01898765432', '01611223344'];
            document.getElementById('inputMobile').value = mobiles[Math.floor(Math.random() * mobiles.length)];
            
            const randomQty = Math.floor(Math.random() * 3) + 1;
            document.getElementById('inputQty').value = randomQty;
            
            const randomAmount = Math.floor(Math.random() * 301) + 700;
            document.getElementById('inputAmount').value = randomAmount;
            
            const randomDiscount = Math.floor(Math.random() * 21) + 30;
            document.getElementById('inputDiscount').value = randomDiscount;
            
            const today = new Date();
            const randomDays = Math.floor(Math.random() * 7) + 1;
            const deliveryDate = new Date(today);
            deliveryDate.setDate(today.getDate() + randomDays);
            
            const formattedDate = `${deliveryDate.getDate().toString().padStart(2, '0')}/` +
                                 `${(deliveryDate.getMonth() + 1).toString().padStart(2, '0')}/` +
                                 `${deliveryDate.getFullYear().toString().slice(-2)}`;
            
            const times = ['09:00AM', '11:00AM', '02:00PM', '04:00PM'];
            const randomTime = times[Math.floor(Math.random() * times.length)];
            document.getElementById('inputDelivery').value = `${formattedDate} ${randomTime}`;
            
            updateBill();
            setCurrentDateTime();
            generateQRCode();
        }
        
        // Set current date/time
        function setCurrentDateTime() {
            const now = new Date();
            const months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];
            
            const billDate = `${now.getDate()} ${months[now.getMonth()]} ${now.getFullYear().toString().slice(-2)}`;
            document.getElementById('displayDate').textContent = billDate;
            
            let hours = now.getHours();
            const minutes = now.getMinutes().toString().padStart(2, '0');
            const ampm = hours >= 12 ? 'PM' : 'AM';
            hours = hours % 12;
            hours = hours ? hours : 12;
            const billTime = `${hours}:${minutes}${ampm}`;
            document.getElementById('displayTime').textContent = billTime;
        }
        
        // Update barcode
        function updateBarcode() {
            const barcodeData = `${currentId}-${currentName}`;
            
            document.getElementById('barcode').innerHTML = '';
            try {
                JsBarcode("#barcode", barcodeData, {
                    format: "CODE128",
                    lineColor: "#000",
                    width: 1.5,
                    height: 40,
                    displayValue: false
                });
            } catch (error) {
                console.error('Barcode generation error:', error);
                document.getElementById('barcode').innerHTML = `
                    <div style="
                        width: 150px; 
                        height: 35px; 
                        background: #f0f0f0; 
                        display: flex; 
                        align-items: center; 
                        justify-content: center;
                        font-size: 9px;
                        text-align: center;
                    ">
                        Barcode<br>
                        ${currentId}
                    </div>
                `;
            }
        }
        
        // Print function
        function printBill() {
            // Update bill before printing
            updateBill();
            
            // Generate QR code again to ensure it's loaded
            generateQRCode();
            
            // Simple window.print()
            window.print();
        }
        
        // Reset form
        function resetForm() {
            document.getElementById('inputInvoice').value = '45864';
            document.getElementById('inputRoom').value = '307';
            document.getElementById('inputName').value = 'Ahmed Rahman';
            document.getElementById('inputId').value = 'Y213321';
            document.getElementById('inputAge').value = '30';
            document.getElementById('inputSex').value = 'Male';
            document.getElementById('inputMobile').value = '01926626565';
            document.getElementById('inputQty').value = '1';
            document.getElementById('inputAmount').value = '802';
            document.getElementById('inputDiscount').value = '40';
            setDeliveryDateTime();
            updateBill();
            setCurrentDateTime();
            generateQRCode();
        }
    </script>
</body>
</html>
