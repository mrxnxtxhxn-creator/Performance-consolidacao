<!DOCTYPE html><html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Dashboard de Operações de Doca</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/html2canvas@1.4.1/dist/html2canvas.min.js"></script>
  <style>
    :root {
      --primary-color: #2c3e50;
      --secondary-color: #3498db;
      --accent-color: #e74c3c;
      --light-color: #ecf0f1;
      --success-color: #27ae60;
      --text-color: #333;
      --card-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    }
    body { background: #f5f7fa; font-family: 'Segoe UI', sans-serif; padding: 20px; }
    .app-container { max-width: 1400px; margin: 0 auto; }
    header { background: var(--primary-color); color: white; padding: 20px; border-radius: 10px; margin-bottom: 20px; box-shadow: var(--card-shadow); }
    .control-panel, .dashboard { background: white; padding: 20px; border-radius: 10px; box-shadow: var(--card-shadow); margin-bottom: 20px; }
    button, input, select { padding: 10px; margin: 5px; border-radius: 5px; border: 1px solid #ccc; }
    button { background: var(--secondary-color); color: white; cursor: pointer; }
    button:hover { background: #2980b9; }
    .kpi-cards { display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); gap: 15px; margin-bottom: 20px; }
    .kpi-card { background: var(--light-color); padding: 20px; border-radius: 10px; text-align: center; box-shadow: var(--card-shadow); }
    .charts-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(400px, 1fr)); gap: 20px; }
    table { width: 100%; border-collapse: collapse; margin-top: 15px; }
    th, td { padding: 10px; border-bottom: 1px solid #ddd; text-align: left; }
    th { background: var(--primary-color); color: white; }
  </style>
</head>
<body>
  <div class="app-container">
    <header>
      <h1>Dashboard de Operações de Doca</h1>
      <p>Monitoramento em tempo real</p>
    </header><div class="control-panel">
  <h2>Adicionar Operação Manualmente</h2>
  <input type="text" id="teamInput" placeholder="Dupla">
  <input type="datetime-local" id="startInput">
  <input type="datetime-local" id="endInput">
  <input type="number" id="packagesInput" placeholder="Pacotes">
  <button id="addManualBtn">Adicionar</button>

  <h3>Filtros</h3>
  <select id="timeRange">
    <option value="today">Hoje</option>
    <option value="yesterday">Ontem</option>
    <option value="week">Esta Semana</option>
    <option value="month">Este Mês</option>
  </select>

  <h3>Exportação</h3>
  <button id="exportExcel">Exportar Excel</button>
  <button id="captureDashboard">Capturar Dashboard</button>
</div>

<div id="dashboard-container" class="dashboard">
  <h2>Métricas</h2>
  <div class="kpi-cards">
    <div class="kpi-card"><h3>Total de Pacotes</h3><p id="totalPackages">0</p></div>
    <div class="kpi-card"><h3>Dupla Mais Rápida</h3><p id="fastestTeam">N/A</p></div>
    <div class="kpi-card"><h3>Maior Volume</h3><p id="highestVolumeTeam">N/A</p></div>
    <div class="kpi-card"><h3>Eficiência Média</h3><p id="averageEfficiency">0</p></div>
  </div>

  <div class="charts-grid">
    <canvas id="volumeChart"></canvas>
    <canvas id="efficiencyChart"></canvas>
    <canvas id="hoursChart"></canvas>
    <canvas id="distributionChart"></canvas>
  </div>

  <h3>Detalhes</h3>
  <table id="operationsTable">
    <thead>
      <tr><th>Dupla</th><th>Início</th><th>Término</th><th>Pacotes</th><th>Duração (h)</th><th>Eficiência</th></tr>
    </thead>
    <tbody></tbody>
  </table>
</div>

  </div>  <script>
    const sampleData = [
      { team: 'Dupla A', startTime: '2025-09-10T08:00', endTime: '2025-09-10T16:00', packages: 2450 },
      { team: 'Dupla B', startTime: '2025-09-10T08:15', endTime: '2025-09-10T15:45', packages: 1980 }
    ];

    let operationsData = [...sampleData];
    let performanceMetrics = {};
    let charts = {};

    document.addEventListener('DOMContentLoaded', () => {
      calculatePerformanceMetrics();
      renderDashboard();
      setupEventListeners();
    });

    function calculatePerformanceMetrics() {
      const teams = [...new Set(operationsData.map(op => op.team))];
      const metrics = {};
      teams.forEach(team => {
        const teamData = operationsData.filter(op => op.team === team);
        const totalPackages = teamData.reduce((s, o) => s + o.packages, 0);
        const totalHours = teamData.reduce((s, o) => s + ((new Date(o.endTime) - new Date(o.startTime)) / 36e5), 0);
        metrics[team] = { totalPackages, totalHours, avg: totalHours ? totalPackages / totalHours : 0 };
      });
      performanceMetrics = metrics;
    }

    function renderDashboard() {
      updateKPIs();
      renderCharts();
      renderTable();
    }

    function updateKPIs() {
      const totalPackages = Object.values(performanceMetrics).reduce((s, m) => s + m.totalPackages, 0);
      const totalHours = Object.values(performanceMetrics).reduce((s, m) => s + m.totalHours, 0);
      const avgEff = totalHours ? totalPackages / totalHours : 0;
      const fastest = Object.entries(performanceMetrics).sort((a,b)=>b[1].avg-a[1].avg)[0];
      const highest = Object.entries(performanceMetrics).sort((a,b)=>b[1].totalPackages-a[1].totalPackages)[0];
      document.getElementById('totalPackages').textContent = totalPackages;
      document.getElementById('fastestTeam').textContent = fastest ? `${fastest[0]} (${fastest[1].avg.toFixed(1)})` : 'N/A';
      document.getElementById('highestVolumeTeam').textContent = highest ? `${highest[0]} (${highest[1].totalPackages})` : 'N/A';
      document.getElementById('averageEfficiency').textContent = avgEff.toFixed(1);
    }

    function renderCharts() {
      const teams = Object.keys(performanceMetrics);
      const vols = teams.map(t => performanceMetrics[t].totalPackages);
      const effs = teams.map(t => performanceMetrics[t].avg);
      const hrs = teams.map(t => performanceMetrics[t].totalHours);
      const colors = ['#3498db','#2ecc71','#e74c3c','#9b59b6','#f1c40f'];
      if (charts.volume) charts.volume.destroy();
      charts.volume = new Chart(document.getElementById('volumeChart'), { type: 'bar', data:{ labels:teams, datasets:[{label:'Pacotes', data:vols, backgroundColor:colors}]}});
      if (charts.efficiency) charts.efficiency.destroy();
      charts.efficiency = new Chart(document.getElementById('efficiencyChart'), { type: 'bar', data:{ labels:teams, datasets:[{label:'Eficiência', data:effs, backgroundColor:colors}]}});
      if (charts.hours) charts.hours.destroy();
      charts.hours = new Chart(document.getElementById('hoursChart'), { type: 'bar', data:{ labels:teams, datasets:[{label:'Horas', data:hrs, backgroundColor:colors}]}});
      if (charts.distribution) charts.distribution.destroy();
      charts.distribution = new Chart(document.getElementById('distributionChart'), { type: 'pie', data:{ labels:teams, datasets:[{data:vols, backgroundColor:colors}]}});
    }

    function renderTable() {
      const tbody = document.querySelector('#operationsTable tbody');
      tbody.innerHTML = '';
      operationsData.forEach(op => {
        const start = new Date(op.startTime);
        const end = new Date(op.endTime);
        const dur = (end - start) / 36e5;
        const eff = dur ? op.packages / dur : 0;
        tbody.innerHTML += `<tr><td>${op.team}</td><td>${start.toLocaleString()}</td><td>${end.toLocaleString()}</td><td>${op.packages}</td><td>${dur.toFixed(1)}</td><td>${eff.toFixed(1)}</td></tr>`;
      });
    }

    function setupEventListeners() {
      document.getElementById('addManualBtn').addEventListener('click', () => {
        const team = document.getElementById('teamInput').value;
        const start = document.getElementById('startInput').value;
        const end = document.getElementById('endInput').value;
        const packages = parseInt(document.getElementById('packagesInput').value);
        if(team && start && end && packages){
          operationsData.push({team, startTime:start, endTime:end, packages});
          calculatePerformanceMetrics();
          renderDashboard();
        }
      });

      document.getElementById('exportExcel').addEventListener('click', () => {
        const data = operationsData.map(op => ({ Dupla:op.team, Inicio:op.startTime, Termino:op.endTime, Pacotes:op.packages }));
        const ws = XLSX.utils.json_to_sheet(data);
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, 'Operações');
        XLSX.writeFile(wb, 'operacoes.xlsx');
      });

      document.getElementById('captureDashboard').addEventListener('click', () => {
        html2canvas(document.getElementById('dashboard-container')).then(canvas => {
          const link = document.createElement('a');
          link.download = 'dashboard.png';
          link.href = canvas.toDataURL();
          link.click();
        });
      });

      document.getElementById('timeRange').addEventListener('change', (e) => {
        const now = new Date();
        let startDate;
        if(e.target.value==='today') startDate=new Date(now.getFullYear(),now.getMonth(),now.getDate());
        if(e.target.value==='yesterday') startDate=new Date(now.getFullYear(),now.getMonth(),now.getDate()-1);
        if(e.target.value==='week') startDate=new Date(now.getFullYear(),now.getMonth(),now.getDate()-7);
        if(e.target.value==='month') startDate=new Date(now.getFullYear(),now.getMonth()-1,now.getDate());
        operationsData = sampleData.filter(op => new Date(op.startTime) >= startDate);
        calculatePerformanceMetrics();
        renderDashboard();
      });
    }
  </script></body>
</html>


