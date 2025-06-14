<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Daily Transaction Tracker</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 30px;
      background-color: #f9f9f9;
      color: #333;
    }
    h1 {
      text-align: center;
      color: #005f73;
    }
    .container {
      max-width: 500px;
      margin: 0 auto;
      background: #fff;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }
    input, select, button {
      width: 100%;
      padding: 10px;
      margin: 10px 0;
      border: 1px solid #ccc;
      border-radius: 5px;
    }
    ul {
      list-style: none;
      padding: 0;
    }
    li {
      background: #e0f7fa;
      margin: 5px 0;
      padding: 10px;
      border-radius: 5px;
    }
    .total {
      font-weight: bold;
      margin-top: 20px;
      font-size: 18px;
    }
    .export-btn {
      background-color: #028090;
      color: white;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <h1>Daily Transaction Tracker</h1>
  <div class="container">
    <label for="type">Select Transaction Type:</label>
    <select id="type">
      <option value="sale">Sale</option>
      <option value="purchase">Purchase</option>
    </select>

    <label for="amount">Enter Amount (Rs.):</label>
    <input type="number" id="amount" placeholder="e.g. 500">

    <button onclick="addEntry()">Add Entry</button>
    <button class="export-btn" onclick="exportToPDF()">Export PDF Report</button>

    <h3>Daily Entries:</h3>
    <ul id="entryList"></ul>

    <div class="total">Total Sale: Rs. <span id="saleTotal">0</span></div>
    <div class="total">Total Purchase: Rs. <span id="purchaseTotal">0</span></div>
  </div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <script>
    const entryList = document.getElementById('entryList');
    const saleTotalDisplay = document.getElementById('saleTotal');
    const purchaseTotalDisplay = document.getElementById('purchaseTotal');

    function getEntries() {
      return JSON.parse(localStorage.getItem('transactionEntries') || '[]');
    }

    function saveEntries(entries) {
      localStorage.setItem('transactionEntries', JSON.stringify(entries));
    }

    function addEntry() {
      const amount = document.getElementById('amount').value;
      const type = document.getElementById('type').value;
      if (!amount || !type) return alert("Please select type and enter amount.");

      const date = new Date().toLocaleDateString();
      const entries = getEntries();
      entries.push({ date, type, amount: parseFloat(amount) });
      saveEntries(entries);

      document.getElementById('amount').value = '';
      loadEntries();
    }

    function loadEntries() {
      const entries = getEntries();
      entryList.innerHTML = '';
      let saleTotal = 0;
      let purchaseTotal = 0;

      entries.forEach(entry => {
        const li = document.createElement('li');
        li.textContent = `${entry.date}: Rs. ${entry.amount} - ${entry.type}`;
        entryList.appendChild(li);

        if (entry.type === 'sale') {
          saleTotal += entry.amount;
        } else {
          purchaseTotal += entry.amount;
        }
      });

      saleTotalDisplay.textContent = saleTotal;
      purchaseTotalDisplay.textContent = purchaseTotal;
    }

    async function exportToPDF() {
      const { jsPDF } = window.jspdf;
      const doc = new jsPDF();
      const entries = getEntries();

      let y = 10;
      doc.setFontSize(16);
      doc.text("Monthly Transaction Report", 10, y);
      y += 10;

      doc.setFontSize(12);
      let saleTotal = 0;
      let purchaseTotal = 0;

      entries.forEach(entry => {
        doc.text(`${entry.date}: Rs. ${entry.amount} - ${entry.type}`, 10, y);
        y += 8;
        if (entry.type === 'sale') {
          saleTotal += entry.amount;
        } else {
          purchaseTotal += entry.amount;
        }
      });

      y += 10;
      doc.setFontSize(14);
      doc.text(`Total Sale: Rs. ${saleTotal}`, 10, y);
      y += 8;
      doc.text(`Total Purchase: Rs. ${purchaseTotal}`, 10, y);

      doc.save("monthly_transaction_report.pdf");
    }

    window.onload = loadEntries;
  </script>
</body>
</html>
