# daily-update-<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Daily Money Tracker</title>
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
    input, button {
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
  </style>
</head>
<body>
  <h1>Daily Money Tracker</h1>
  <div class="container">
    <label for="amount">Enter Amount (Rs.):</label>
    <input type="number" id="amount" placeholder="e.g. 500">
    <button onclick="addEntry()">Add Entry</button>

    <h3>Entries:</h3>
    <ul id="entryList"></ul>

    <div class="total">Total Saved This Month: Rs. <span id="total">0</span></div>
  </div>

  <script>
    const entryList = document.getElementById('entryList');
    const totalDisplay = document.getElementById('total');

    function getEntries() {
      return JSON.parse(localStorage.getItem('moneyEntries') || '[]');
    }

    function saveEntries(entries) {
      localStorage.setItem('moneyEntries', JSON.stringify(entries));
    }

    function addEntry() {
      const amount = document.getElementById('amount').value;
      if (!amount) return alert("Please enter an amount.");
      const date = new Date().toLocaleDateString();
      const entries = getEntries();
      entries.push({ date, amount: parseFloat(amount) });
      saveEntries(entries);
      document.getElementById('amount').value = '';
      loadEntries();
    }

    function loadEntries() {
      const entries = getEntries();
      entryList.innerHTML = '';
      let total = 0;
      entries.forEach(entry => {
        const li = document.createElement('li');
        li.textContent = `${entry.date}: Rs. ${entry.amount}`;
        entryList.appendChild(li);
        total += entry.amount;
      });
      totalDisplay.textContent = total;
    }

    window.onload = loadEntries;
  </script>
</body>
</html>
