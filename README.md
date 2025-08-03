# NiceHL-Lottery
NiceHL Draft Lottery Simulator
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>NiceHL Draft Lottery</title>
  <style>
    body {
      font-family: sans-serif;
      padding: 20px;
      background: #000;
      color: #fff;
    }
    table {
      border-collapse: collapse;
      margin-bottom: 20px;
      width: 100%;
    }
    th, td {
      border: 1px solid #444;
      padding: 8px 12px;
      text-align: center;
    }
    th {
      background: #222;
      color: #fff;
    }
    td {
      background: #111;
    }
    button {
      padding: 10px 18px;
      margin-right: 10px;
      background: #333;
      color: #fff;
      border: 1px solid #555;
      cursor: pointer;
    }
    button:hover {
      background: #555;
    }
    ol {
      background: #111;
      padding: 10px;
      border: 1px solid #444;
    }
    h1 {
      margin-top: 0;
      font-size: 28px;
      color: #fff;
    }
  </style>
</head>
<body>

  <h1>NiceHL Draft Lottery</h1>

  <table id="teamTable">
    <thead>
      <tr>
        <th>Team</th>
        <th>Combinations</th>
        <th>Active</th>
        <th>Current Odds %</th>
      </tr>
    </thead>
    <tbody>
      <tr><td>Driftless Hockey Club</td><td>300</td><td>1</td><td></td></tr>
      <tr><td>Rockaway Brattwursts</td><td>200</td><td>1</td><td></td></tr>
      <tr><td>Bowness Ice Beavers</td><td>150</td><td>1</td><td></td></tr>
      <tr><td>Mt Pearl Vikings</td><td>120</td><td>1</td><td></td></tr>
      <tr><td>Scotty</td><td>100</td><td>1</td><td></td></tr>
      <tr><td>Potomac Poutine</td><td>60</td><td>1</td><td></td></tr>
      <tr><td>Maplewood HC</td><td>50</td><td>1</td><td></td></tr>
      <tr><td>Cantuar Slough Sharks</td><td>20</td><td>1</td><td></td></tr>
    </tbody>
  </table>

  <button onclick="drawPick()">Draw Next Pick</button>
  <button onclick="resetLottery()">Reset</button>

  <h3>Draft Order:</h3>
  <ol id="pickList"></ol>

  <script>
    let picks = [];

    function getActiveTeams() {
      const rows = document.querySelectorAll('#teamTable tbody tr');
      const teams = [];
      rows.forEach(row => {
        const cells = row.querySelectorAll('td');
        const name = cells[0].innerText;
        const combos = parseFloat(cells[1].innerText);
        const active = parseInt(cells[2].innerText);
        if (active === 1) {
          teams.push({ name, combos, row });
        }
      });
      return teams;
    }

    function updateOddsDisplay() {
      const rows = document.querySelectorAll('#teamTable tbody tr');
      const activeTeams = getActiveTeams();
      const totalCombos = activeTeams.reduce((sum, t) => sum + t.combos, 0);

      rows.forEach(row => {
        const active = parseInt(row.cells[2].innerText);
        const combos = parseFloat(row.cells[1].innerText);
        if (active === 0) {
          row.cells[3].innerText = "0.0%";
        } else {
          const percent = ((combos / totalCombos) * 100).toFixed(1);
          row.cells[3].innerText = percent + "%";
        }
      });
    }

    function drawPick() {
      if (picks.length >= 8) {
        alert("All picks have been made!");
        return;
      }

      const teams = getActiveTeams();
      const totalCombos = teams.reduce((sum, t) => sum + t.combos, 0);
      if (totalCombos === 0) return;

      const r = Math.random() * totalCombos;
      let cumulative = 0;
      for (let t of teams) {
        cumulative += t.combos;
        if (r <= cumulative) {
          picks.push(t.name);
          document.getElementById("pickList").innerHTML += `<li>${t.name}</li>`;
          t.row.cells[2].innerText = "0"; // deactivate
          t.row.cells[3].innerText = "0.0%"; // show 0% odds
          updateOddsDisplay(); // recalculate others
          return;
        }
      }
    }

    function resetLottery() {
      const rows = document.querySelectorAll('#teamTable tbody tr');
      rows.forEach(row => {
        row.cells[2].innerText = "1"; // reactivate
        row.cells[3].innerText = "";  // clear odds
      });
      picks = [];
      document.getElementById("pickList").innerHTML = "";
      updateOddsDisplay();
    }

    window.onload = updateOddsDisplay;
  </script>

</body>
</html>
