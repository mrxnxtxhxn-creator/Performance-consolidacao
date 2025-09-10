<!DOCTYPE html>
<html lang="pt-BR">
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
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background-color: #f5f7fa;
            color: var(--text-color);
            padding: 20px;
        }
        
        .app-container {
            max-width: 1400px;
            margin: 0 auto;
        }
        
        header {
            background-color: var(--primary-color);
            color: white;
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 20px;
            box-shadow: var(--card-shadow);
        }
        
        h1, h2, h3 {
            margin-bottom: 15px;
        }
        
        .control-panel {
            background-color: white;
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 20px;
            box-shadow: var(--card-shadow);
        }
        
        .filters, .export-options {
            margin-bottom: 20px;
        }
        
        select, button {
            padding: 10px 15px;
            border-radius: 5px;
            border: 1px solid #ddd;
            margin-right: 10px;
            margin-bottom: 10px;
            font-size: 14px;
        }
        
        button {
            background-color: var(--secondary-color);
            color: white;
            border: none;
            cursor: pointer;
            transition: background-color 0.3s;
        }
        
        button:hover {
            background-color: #2980b9;
        }
        
        .dashboard {
            background-color: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: var(--card-shadow);
        }
        
        .kpi-cards {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }
        
        .kpi-card {
            background-color: var(--light-color);
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            box-shadow: var(--card-shadow);
        }
        
        .kpi-card h3 {
            color: var(--primary-color);
            font-size: 16px;
        }
        
        .kpi-card p {
            font-size: 24px;
            font-weight: bold;
            color: var(--secondary-color);
            margin-top: 10px;
        }
        
        .charts-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(500px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }
        
        .chart-container {
            background-color: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: var(--card-shadow);
        }
        
        .operations-table {
            overflow-x: auto;
        }
        
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        
        th, td {
            padding: 12px 15px;
            text-align: left;
            border-bottom: 1px solid #ddd;
        }
        
        th {
            background-color: var(--primary-color);
            color: white;
        }
        
        tr:hover {
            background-color: #f5f5f5;
        }
        
        .status-message {
            padding: 15px;
            margin-top: 20px;
            border-radius: 5px;
            background-color: var(--success-color);
            color: white;
            text-align: center;
        }
        
        @media (max-width: 768px) {
            .charts-grid {
                grid-template-columns: 1fr;
            }
            
            .kpi-cards {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <div class="app-container">
        <header>
            <h1>Dashboard de Operações de Doca</h1>
            <p>Monitoramento de performance das equipes de docagem</p>
        </header>
        
        <div class="control-panel">
            <h2>Painel de Controle</h2>
            
            <div class="filters">
                <h3>Filtros</h3>
                <select id="timeRange">
                    <option value="today">Hoje</option>
                    <option value="yesterday">Ontem</option>
                    <option value="week">Esta Semana</option>
                    <option value="month">Este Mês</option>
                </select>
            </div>

            <div class="export-options">
                <h3>Exportação</h3>
                <button id="exportExcel">Exportar para Excel</button>
                <button id="captureDashboard">Capturar Dashboard</button>
            </div>
        </div>

        <div id="dashboard-container" class="dashboard">
            <h2>Métricas de Performance</h2>
            
            <div class="kpi-cards">
                <div class="kpi-card">
                    <h3>Total de Pacotes</h3>
                    <p id="totalPackages">0</p>
                </div>
                <div class="kpi-card">
                    <h3>Dupla Mais Rápida</h3>
                    <p id="fastestTeam">N/A</p>
                </div>
                <div class="kpi-card">
                    <h3>Dupla com Mais Volume</h3>
                    <p id="highestVolumeTeam">N/A</p>
                </div>
                <div class="kpi-card">
                    <h3>Eficiência Média (pacotes/hora)</h3>
                    <p id="averageEfficiency">0</p>
                </div>
            </div>

            <div class="charts-grid">
                <div class="chart-container">
                    <h3>Volume de Pacotes por Dupla</h3>
                    <canvas id="volumeChart"></canvas>
                </div>
                
                <div class="chart-container">
                    <h3>Eficiência (Pacotes por Hora)</h3>
                    <canvas id="efficiencyChart"></canvas>
                </div>
                
                <div class="chart-container">
                    <h3>Horas Trabalhadas por Dupla</h3>
                    <canvas id="hoursChart"></canvas>
                </div>
                
                <div class="chart-container">
                    <h3>Distribuição de Volume</h3>
                    <canvas id="distributionChart"></canvas>
                </div>
            </div>

            <div class="operations-table">
                <h3>Detalhes das Operações</h3>
                <table id="operationsTable">
                    <thead>
                        <tr>
                            <th>Dupla</th>
                            <th>Início</th>
                            <th>Término</th>
                            <th>Pacotes</th>
                            <th>Duração (h)</th>
                            <th>Eficiência (p/h)</th>
                        </tr>
                    </thead>
                    <tbody>
                        <!-- Os dados da tabela serão preenchidos via JavaScript -->
                    </tbody>
                </table>
            </div>
        </div>

        <div id="statusMessage" class="status-message" style="display: none;"></div>
    </div>

    <script>
        // Dados de exemplo para demonstração
        const sampleData = [
            { id: 1, team: 'Dupla A', startTime: '2025-09-11 08:00', endTime: '2025-09-11 16:00', packages: 2450 },
            { id: 2, team: 'Dupla B', startTime: '2025-09-11 08:15', endTime: '2025-09-11 15:45', packages: 1980 },
            { id: 3, team: 'Dupla C', startTime: '2025-09-11 08:05', endTime: '2025-09-11 16:20', packages: 3120 },
            { id: 4, team: 'Dupla D', startTime: '2025-09-11 08:20', endTime: '2025-09-11 16:10', packages: 2750 },
            { id: 5, team: 'Dupla E', startTime: '2025-09-11 08:10', endTime: '2025-09-11 15:50', packages: 2300 },
            { id: 6, team: 'Dupla A', startTime: '2025-09-10 08:10', endTime: '2025-09-10 16:30', packages: 2650 },
            { id: 7, team: 'Dupla B', startTime: '2025-09-10 08:00', endTime: '2025-09-10 15:55', packages: 2270 },
            { id: 8, team: 'Dupla C', startTime: '2025-09-10 08:05', endTime: '2025-09-10 16:25', packages: 2950 },
            { id: 9, team: 'Dupla D', startTime: '2025-09-10 08:15', endTime: '2025-09-10 16:15', packages: 2550 },
            { id: 10, team: 'Dupla E', startTime: '2025-09-10 08:10', endTime: '2025-09-10 15:45', packages: 2100 }
        ];

        let operationsData = [];
        let performanceMetrics = {};
        let charts = {};

        document.addEventListener('DOMContentLoaded', function() {
            // Inicializar com dados de exemplo
            operationsData = sampleData;
            calculatePerformanceMetrics();
            renderDashboard();
            setupEventListeners();
        });

        function calculatePerformanceMetrics() {
            const teams = [...new Set(operationsData.map(item => item.team))];
            const metrics = {};
            
            teams.forEach(team => {
                const teamData = operationsData.filter(item => item.team === team);
                const totalPackages = teamData.reduce((sum, item) => sum + item.packages, 0);
                const totalHours = teamData.reduce((sum, item) => {
                    const start = new Date(item.startTime);
                    const end = new Date(item.endTime);
                    const hours = (end - start) / (1000 * 60 * 60);
                    return sum + hours;
                }, 0);
                
                metrics[team] = {
                    totalPackages,
                    averagePackagesPerHour: totalHours > 0 ? totalPackages / totalHours : 0,
                    totalHours
                };
            });
            
            performanceMetrics = metrics;
        }

        function renderDashboard() {
            updateKPICards();
            renderCharts();
            renderOperationsTable();
        }

        function updateKPICards() {
            // Calcular totais
            const totalPackages = Object.values(performanceMetrics).reduce((sum, metric) => sum + metric.totalPackages, 0);
            const totalHours = Object.values(performanceMetrics).reduce((sum, metric) => sum + metric.totalHours, 0);
            const averageEfficiency = totalHours > 0 ? totalPackages / totalHours : 0;
            
            // Encontrar a dupla mais rápida
            const fastestTeam = Object.entries(performanceMetrics)
                .sort((a, b) => b[1].averagePackagesPerHour - a[1].averagePackagesPerHour)[0];
            
            // Encontrar a dupla com maior volume
            const highestVolumeTeam = Object.entries(performanceMetrics)
                .sort((a, b) => b[1].totalPackages - a[1].totalPackages)[0];
            
            // Atualizar os elementos da interface
            document.getElementById('totalPackages').textContent = totalPackages.toLocaleString();
            document.getElementById('fastestTeam').textContent = fastestTeam ? `${fastestTeam[0]} (${Math.round(fastestTeam[1].averagePackagesPerHour)} p/h)` : 'N/A';
            document.getElementById('highestVolumeTeam').textContent = highestVolumeTeam ? `${highestVolumeTeam[0]} (${highestVolumeTeam[1].totalPackages.toLocaleString()})` : 'N/A';
            document.getElementById('averageEfficiency').textContent = Math.round(averageEfficiency);
        }

        function renderCharts() {
            const teams = Object.keys(performanceMetrics);
            
            // Dados para os gráficos
            const volumeData = teams.map(team => performanceMetrics[team].totalPackages);
            const efficiencyData = teams.map(team => performanceMetrics[team].averagePackagesPerHour);
            const hoursData = teams.map(team => performanceMetrics[team].totalHours);
            
            // Cores para os gráficos
            const backgroundColors = [
                'rgba(54, 162, 235, 0.6)',
                'rgba(75, 192, 192, 0.6)',
                'rgba(255, 159, 64, 0.6)',
                'rgba(255, 99, 132, 0.6)',
                'rgba(153, 102, 255, 0.6)'
            ];
            
            // Configurações comuns para gráficos de barras
            const barChartOptions = {
                responsive: true,
                plugins: {
                    legend: {
                        position: 'top',
                    }
                },
                scales: {
                    y: {
                        beginAtZero: true
                    }
                }
            };
            
            // Gráfico de Volume
            if (charts.volume) charts.volume.destroy();
            charts.volume = new Chart(document.getElementById('volumeChart'), {
                type: 'bar',
                data: {
                    labels: teams,
                    datasets: [{
                        label: 'Total de Pacotes',
                        data: volumeData,
                        backgroundColor: backgroundColors,
                        borderWidth: 1
                    }]
                },
                options: barChartOptions
            });
            
            // Gráfico de Eficiência
            if (charts.efficiency) charts.efficiency.destroy();
            charts.efficiency = new Chart(document.getElementById('efficiencyChart'), {
                type: 'bar',
                data: {
                    labels: teams,
                    datasets: [{
                        label: 'Pacotes por Hora',
                        data: efficiencyData,
                        backgroundColor: backgroundColors,
                        borderWidth: 1
                    }]
                },
                options: barChartOptions
            });
            
            // Gráfico de Horas
            if (charts.hours) charts.hours.destroy();
            charts.hours = new Chart(document.getElementById('hoursChart'), {
                type: 'bar',
                data: {
                    labels: teams,
                    datasets: [{
                        label: 'Horas Trabalhadas',
                        data: hoursData,
                        backgroundColor: backgroundColors,
                        borderWidth: 1
                    }]
                },
                options: barChartOptions
            });
            
            // Gráfico de Distribuição (Pizza)
            if (charts.distribution) charts.distribution.destroy();
            charts.distribution = new Chart(document.getElementById('distributionChart'), {
                type: 'pie',
                data: {
                    labels: teams,
                    datasets: [{
                        data: volumeData,
                        backgroundColor: backgroundColors,
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        legend: {
                            position: 'top',
                        },
                        title: {
                            display: true,
                            text: 'Distribuição de Volume por Dupla'
                        }
                    }
                }
            });
        }

        function renderOperationsTable() {
            const tableBody = document.querySelector('#operationsTable tbody');
            tableBody.innerHTML = '';
            
            operationsData.forEach(op => {
                const start = new Date(op.startTime);
                const end = new Date(op.endTime);
                const duration = (end - start) / (1000 * 60 * 60);
                const efficiency = duration > 0 ? op.packages / duration : 0;
                
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${op.team}</td>
                    <td>${start.toLocaleString()}</td>
                    <td>${end.toLocaleString()}</td>
                    <td>${op.packages.toLocaleString()}</td>
                    <td>${duration.toFixed(2)}</td>
                    <td>${Math.round(efficiency)}</td>
                `;
                
                tableBody.appendChild(row);
            });
        }

        function setupEventListeners() {
            document.getElementById('timeRange').addEventListener('change', function(e) {
                // Filtrar dados com base no período selecionado
                filterDataByTimeRange(e.target.value);
                calculatePerformanceMetrics();
                renderDashboard();
            });
            
            document.getElementById('exportExcel').addEventListener('click', exportToExcel);
            document.getElementById('captureDashboard').addEventListener('click', captureDashboard);
        }

        function filterDataByTimeRange(range) {
            const now = new Date();
            let startDate;
            
            switch(range) {
                case 'today':
                    startDate = new Date(now.getFullYear(), now.getMonth(), now.getDate());
                    break;
                case 'yesterday':
                    startDate = new Date(now.getFullYear(), now.getMonth(), now.getDate() - 1);
                    break;
                case 'week':
                    startDate = new Date(now.getFullYear(), now.getMonth(), now.getDate() - 7);
                    break;
                case 'month':
                    startDate = new Date(now.getFullYear(), now.getMonth() - 1, now.getDate());
                    break;
                default:
                    startDate = new Date(0); // Desde o início dos tempos
            }
            
            operationsData = sampleData.filter(item => {
                const itemDate = new Date(item.startTime);
                return itemDate >= startDate;
            });
        }

        function exportToExcel() {
            // Preparar dados para exportação
            const dataToExport = operationsData.map(op => {
                const start = new Date(op.startTime);
                const end = new Date(op.endTime);
                const duration = (end - start) / (1000 * 60 * 60);
                const efficiency = duration > 0 ? op.packages / duration : 0;
                
                return {
                    'Dupla': op.team,
                    'Início': start.toLocaleString(),
                    'Término': end.toLocaleString(),
                    'Pacotes': op.packages,
                    'Duração (h)': duration.toFixed(2),
                    'Eficiência (p/h)': Math.round(efficiency)
                };
            });
            
            // Criar planilha
            const worksheet = XLSX.utils.json_to_sheet(dataToExport);
            const workbook = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(workbook, worksheet, "Operações de Doca");
            
            // Exportar arquivo
            XLSX.writeFile(workbook, "operacoes_doca.xlsx");
            
            // Mostrar mensagem de status
            showStatusMessage('Arquivo Excel exportado com sucesso!');
        }

        function captureDashboard() {
            html2canvas(document.getElementById('dashboard-container')).then(canvas => {
                const link = document.createElement('a');
                link.download = 'dashboard-operacoes.png';
                link.href = canvas.toDataURL('image/png');
                link.click();
                
                // Mostrar mensagem de status
                showStatusMessage('Captura de tela realizada com sucesso!');
            });
        }

        function showStatusMessage(message) {
            const statusElement = document.getElementById('statusMessage');
            statusElement.textContent = message;
            statusElement.style.display = 'block';
            
            // Ocultar a mensagem após 3 segundos
            setTimeout(() => {
                statusElement.style.display = 'none';
            }, 3000);
        }
    </script>
</body>
</html>


