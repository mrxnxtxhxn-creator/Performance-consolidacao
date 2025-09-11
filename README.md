<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard de Operações de Doca v2.0</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/html2canvas@1.4.1/dist/html2canvas.min.js"></script>
    <style>
        :root {
            --primary-bg: #f5f7fa;
            --secondary-bg: #ffffff;
            --header-bg: #2c3e50;
            --text-color: #333;
            --primary-accent: #3498db;
            --primary-accent-hover: #2980b9;
            --card-bg: #ecf0f1;
            --border-color: #ddd;
            --shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            --danger-color: #e74c3c;
            --danger-hover-color: #c0392b;
            --success-color: #27ae60;
            --transition-speed: 0.3s;
        }

        html.dark-mode {
            --primary-bg: #1a202c;
            --secondary-bg: #2d3748;
            --header-bg: #1a202c;
            --text-color: #e2e8f0;
            --primary-accent: #4299e1;
            --primary-accent-hover: #2b6cb0;
            --card-bg: #4a5568;
            --border-color: #4a5568;
        }

        body {
            background: var(--primary-bg);
            color: var(--text-color);
            font-family: 'Segoe UI', sans-serif;
            padding: 20px;
            transition: background var(--transition-speed), color var(--transition-speed);
        }

        .app-container { max-width: 1400px; margin: 0 auto; }
        
        header {
            background: var(--header-bg);
            color: white;
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 20px;
            box-shadow: var(--shadow);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        
        .control-panel, .dashboard {
            background: var(--secondary-bg);
            padding: 20px;
            border-radius: 10px;
            box-shadow: var(--shadow);
            margin-bottom: 20px;
            transition: background var(--transition-speed);
        }

        button, input, select {
            padding: 10px;
            margin: 5px;
            border-radius: 5px;
            border: 1px solid var(--border-color);
            background: var(--secondary-bg);
            color: var(--text-color);
        }

        button {
            background: var(--primary-accent);
            color: white;
            cursor: pointer;
            border: none;
            transition: background var(--transition-speed);
        }

        button:hover { background: var(--primary-accent-hover); }
        
        .kpi-cards { display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); gap: 15px; margin-bottom: 20px; }
        .kpi-card {
            background: var(--card-bg);
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            box-shadow: var(--shadow);
            transition: transform 0.2s, background var(--transition-speed);
        }
        .kpi-card:hover { transform: translateY(-5px); }
        .kpi-card h3 { font-size: 1rem; margin-bottom: 10px; }
        .kpi-card p {
            font-size: 1.5rem;
            font-weight: bold;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }
        .kpi-arrow {
            width: 1em;
            height: 1em;
        }
        .arrow-up { color: var(--success-color); }
        .arrow-down { color: var(--danger-color); }


        .charts-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
            gap: 25px;
        }

        .chart-container {
            position: relative;
            padding: 10px;
            border-radius: 8px;
            background: var(--secondary-bg);
            box-shadow: var(--shadow);
        }
        
        .chart-container h3 {
            text-align: center;
            margin-bottom: 15px;
            font-weight: 600;
            color: var(--header-bg);
        }

        html.dark-mode .chart-container h3 {
            color: var(--text-color);
        }
        
        table { width: 100%; border-collapse: collapse; margin-top: 15px; }
        th, td { padding: 12px; border-bottom: 1px solid var(--border-color); text-align: left; }
        th { background: var(--header-bg); color: white; }

        .form-controls { display: flex; flex-wrap: wrap; align-items: center; }
        .no-data-row td { text-align: center; color: #777; padding: 20px; }

        .modal-overlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.6); display: flex; justify-content: center;
            align-items: center; z-index: 1000;
        }
        .modal-content {
            background: var(--secondary-bg); padding: 30px; border-radius: 10px;
            text-align: center; max-width: 400px; box-shadow: 0 5px 15px rgba(0,0,0,0.3);
        }
        .modal-buttons { margin-top: 20px; }
        #confirmClearBtn { background-color: var(--danger-color); }
        #confirmClearBtn:hover { background-color: var(--danger-hover-color); }

        .team-legend {
            display: flex;
            flex-wrap: wrap;
            gap: 15px;
            margin-bottom: 20px;
            padding: 10px;
            background-color: var(--card-bg);
            border-radius: 8px;
        }
        .legend-item {
            display: flex;
            align-items: center;
            gap: 8px;
        }
        .legend-color-box {
            width: 15px;
            height: 15px;
            border-radius: 3px;
        }
    </style>
</head>
<body>
    <div class="app-container">
        <header>
            <div>
                <h1>Dashboard de Operações de Doca</h1>
                <p>Monitoramento em tempo real com dados persistentes</p>
            </div>
            <div class="theme-switch-wrapper">
                <span>Modo Escuro</span>
                <input type="checkbox" id="theme-switch" style="display:none;">
                <label for="theme-switch" style="cursor:pointer; font-size: 1.5em;">🌓</label>
            </div>
        </header>

        <div class="control-panel">
            <h2>Adicionar Operação</h2>
            <form id="addOperationForm" class="form-controls">
                <input type="text" id="teamInput" placeholder="Nomes da Dupla (ex: João e Maria)" list="teams-datalist" required>
                <datalist id="teams-datalist"></datalist>
                <input type="datetime-local" id="startInput" required title="Início da operação">
                <input type="datetime-local" id="endInput" required title="Fim da operação">
                <input type="number" id="packagesInput" placeholder="Pacotes" min="1" required>
                <button type="submit">Adicionar Operação</button>
            </form>
            
            <div style="display:flex; justify-content:space-between; align-items: center; margin-top: 20px; flex-wrap: wrap;">
                <div>
                    <h3>Filtros</h3>
                    <select id="timeRange">
                        <option value="all">Tudo</option>
                        <option value="today">Hoje</option>
                        <option value="yesterday">Ontem</option>
                        <option value="week">Esta Semana</option>
                        <option value="month">Este Mês</option>
                    </select>
                </div>
                <div style="display: flex; gap: 10px;">
                    <div>
                        <h3>Exportação</h3>
                        <button id="exportExcel">Exportar Excel</button>
                        <button id="captureDashboard">Capturar Dashboard</button>
                    </div>
                    <div>
                        <h3>Ações</h3>
                        <button id="clearDataBtn" style="background-color: var(--danger-color);">Limpar Dados</button>
                    </div>
                </div>
            </div>
        </div>

        <div id="dashboard-container" class="dashboard">
            <h2>Métricas</h2>
            <div class="kpi-cards">
                <div class="kpi-card"><h3>Total de Pacotes</h3><p id="totalPackages">0</p></div>
                <div class="kpi-card"><h3>Total de Caminhões</h3><p id="totalTrucks">0</p></div>
                <div class="kpi-card"><h3>Dupla Mais Rápida</h3><p id="fastestTeam">N/A</p></div>
                <div class="kpi-card"><h3>Dupla Mais Lenta</h3><p id="slowestTeam">N/A</p></div>
            </div>

            <div id="legendContainer">
                <h3>Duplas Ativas Hoje</h3>
                <div id="teamLegend" class="team-legend"></div>
            </div>
            
            <div class="charts-grid">
                <div class="chart-container">
                    <h3>Volume por Dupla</h3>
                    <canvas id="volumeChart"></canvas>
                </div>
                <div class="chart-container">
                    <h3>Eficiência por Dupla (pac/h)</h3>
                    <canvas id="efficiencyChart"></canvas>
                </div>
                <div class="chart-container">
                    <h3>Caminhões por Dupla</h3>
                     <canvas id="trucksChart"></canvas>
                </div>
                <div class="chart-container">
                    <h3>Distribuição de Volume</h3>
                    <canvas id="distributionChart"></canvas>
                </div>
                <div class="chart-container" style="grid-column: 1 / -1;">
                    <h3>Desempenho Acumulado por Hora</h3>
                    <canvas id="performanceLineChart"></canvas>
                </div>
            </div>

            <h3>Detalhes</h3>
            <table id="operationsTable">
                <thead>
                    <tr><th>Dupla</th><th>Nomes</th><th>Início</th><th>Término</th><th>Pacotes</th><th>Caminhões</th><th>Duração (h)</th><th>Eficiência (pac/h)</th></tr>
                </thead>
                <tbody></tbody>
            </table>
        </div>
    </div>
    
    <div id="confirmModal" class="modal-overlay" style="display: none;">
        <div class="modal-content">
            <h4>Confirmar Ação</h4>
            <p>Tem a certeza de que deseja apagar todos os dados das operações? Esta ação não pode ser desfeita.</p>
            <div class="modal-buttons">
                <button id="confirmClearBtn">Sim, Limpar Tudo</button>
                <button id="cancelClearBtn">Cancelar</button>
            </div>
        </div>
    </div>

    <script>
        // --- Estado da Aplicação e Dados ---
        let allOperationsData = [];
        let filteredOperationsData = [];
        let charts = {};
        let teamColors = {};
        let nextColorIndex = 0;
        const PREDEFINED_COLORS = ['#3498db', '#2ecc71', '#e74c3c', '#9b59b6', '#f1c40f', '#1abc9c', '#e67e22', '#34495e', '#7f8c8d', '#d35400'];

        const generateInitialData = () => {
            const names = ["Carlos & Bruna", "Lucas & Fernanda", "Matheus & Gabriela", "Rafael & Juliana", "Vinicius & Patricia", "Gustavo & Sandra", "Thiago & Amanda", "Felipe & Renata", "Ricardo & Camila", "Diego & Beatriz"];
            const data = [];
            let id = 1;
            const today = new Date().toISOString().slice(0, 10);

            names.forEach((name, index) => {
                const startHour = 13.5 + (index % 4) * 0.25;
                const durationMinutes = 60 + Math.floor(Math.random() * 90);
                const startTime = new Date(`${today}T${String(Math.floor(startHour)).padStart(2, '0')}:${String((startHour % 1) * 60).padStart(2, '0')}`);
                const endTime = new Date(startTime.getTime() + durationMinutes * 60000);

                const packages = 1500 + Math.floor(Math.random() * 800);
                
                data.push({
                    id: id++, team: name,
                    startTime: startTime.toISOString(),
                    endTime: endTime.toISOString(),
                    packages: packages
                });
            });
            return data;
        };

        const saveData = () => {
            localStorage.setItem('dockOperationsData', JSON.stringify(allOperationsData));
            localStorage.setItem('dockOperationsTeamColors', JSON.stringify(teamColors));
        };

        const loadData = () => {
            const data = localStorage.getItem('dockOperationsData');
            const colors = localStorage.getItem('dockOperationsTeamColors');
            
            teamColors = colors ? JSON.parse(colors) : {};
            nextColorIndex = Object.keys(teamColors).length;
            
            if (!data || data === '[]') {
                allOperationsData = generateInitialData();
                saveData();
            } else {
                allOperationsData = JSON.parse(data);
            }
            populateTeamsDatalist();
        };
        
        const getTeamInitials = (teamName) => {
            if (!teamName || typeof teamName !== 'string') return '';
            const names = teamName.replace(/&/g, 'e').split('e').map(n => n.trim());
            if (names.length > 1 && names[0] && names[1]) {
                return `${names[0][0]}${names[1][0]}`.toUpperCase();
            } else if (names.length === 1 && names[0].includes(' ')){
                 const parts = names[0].split(' ');
                 return `${parts[0][0]}${parts[parts.length - 1][0]}`.toUpperCase();
            }
            return teamName.slice(0, 2).toUpperCase();
        };
        
        const getColorForTeam = (teamName) => {
            if (!teamColors[teamName]) {
                teamColors[teamName] = PREDEFINED_COLORS[nextColorIndex % PREDEFINED_COLORS.length];
                nextColorIndex++;
                saveData();
            }
            return teamColors[teamName];
        };

        const populateTeamsDatalist = () => {
            const datalist = document.getElementById('teams-datalist');
            const uniqueTeams = [...new Set(allOperationsData.map(op => op.team))];
            datalist.innerHTML = '';
            uniqueTeams.forEach(team => {
                datalist.innerHTML += `<option value="${team}"></option>`;
            });
        };

        const renderTeamLegend = () => {
            const legend = document.getElementById('teamLegend');
            const container = document.getElementById('legendContainer');
            const activeTeams = [...new Set(filteredOperationsData.map(op => op.team))];

            if (activeTeams.length === 0) {
                container.style.display = 'none';
                return;
            }
            
            container.style.display = 'block';
            legend.innerHTML = '';
            activeTeams.forEach(team => {
                const color = getColorForTeam(team);
                const initials = getTeamInitials(team);
                legend.innerHTML += `
                    <div class="legend-item">
                        <div class="legend-color-box" style="background-color: ${color};"></div>
                        <span><strong>${initials}:</strong> ${team}</span>
                    </div>`;
            });
        };

        const renderDashboard = () => {
            const performanceMetrics = calculatePerformanceMetrics();
            updateKPIs(performanceMetrics);
            renderBarAndPieCharts(performanceMetrics);
            renderPerformanceLineChart();
            renderTeamLegend();
            renderTable();
            populateTeamsDatalist();
        };

        const calculatePerformanceMetrics = () => {
            const teams = [...new Set(filteredOperationsData.map(op => op.team))];
            const metrics = {};
            teams.forEach(team => {
                const teamData = filteredOperationsData.filter(op => op.team === team);
                const totalPackages = teamData.reduce((s, o) => s + o.packages, 0);
                const totalTrucks = teamData.length;
                const totalHours = teamData.reduce((s, o) => s + ((new Date(o.endTime) - new Date(o.startTime)) / 36e5), 0);
                metrics[team] = { totalPackages, totalTrucks, totalHours, avg: totalHours > 0 ? totalPackages / totalHours : 0 };
            });
            return metrics;
        };
        
        const updateKPIs = (metrics) => {
            const totalPackages = Object.values(metrics).reduce((s, m) => s + m.totalPackages, 0);
            const totalTrucks = Object.values(metrics).reduce((s, m) => s + m.totalTrucks, 0);

            document.getElementById('totalPackages').textContent = totalPackages.toLocaleString('pt-BR');
            document.getElementById('totalTrucks').textContent = totalTrucks;

            const fastestTeamEl = document.getElementById('fastestTeam');
            const slowestTeamEl = document.getElementById('slowestTeam');
            
            const teamsArray = Object.entries(metrics);

            if (teamsArray.length < 2) {
                fastestTeamEl.innerHTML = '<span>N/A</span>';
                slowestTeamEl.innerHTML = '<span>N/A</span>';
            } else {
                const sortedBySpeed = teamsArray.sort((a, b) => b[1].avg - a[1].avg);
                const fastest = sortedBySpeed[0];
                const slowest = sortedBySpeed[sortedBySpeed.length - 1];
                
                const upArrow = `<span class="arrow-up">▲</span>`;
                const downArrow = `<span class="arrow-down">▼</span>`;

                fastestTeamEl.innerHTML = `${upArrow} ${getTeamInitials(fastest[0])} (${fastest[1].avg.toFixed(1)})`;
                slowestTeamEl.innerHTML = `${downArrow} ${getTeamInitials(slowest[0])} (${slowest[1].avg.toFixed(1)})`;
            }
        };
        
        const initializeCharts = () => {
            const createChart = (ctx, type, data, options = {}) => new Chart(ctx, { type, data, options });
            
            const commonData = { labels: [], datasets: [{ data: [], backgroundColor: [] }] };
            charts.volume = createChart(document.getElementById('volumeChart'), 'bar', { ...commonData, datasets: [{...commonData.datasets[0], label: 'Pacotes'}] });
            charts.efficiency = createChart(document.getElementById('efficiencyChart'), 'bar', { ...commonData, datasets: [{...commonData.datasets[0], label: 'Eficiência'}] });
            charts.trucks = createChart(document.getElementById('trucksChart'), 'bar', { ...commonData, datasets: [{...commonData.datasets[0], label: 'Caminhões'}] });
            charts.distribution = createChart(document.getElementById('distributionChart'), 'pie', commonData);
            
            charts.performanceLineChart = createChart(document.getElementById('performanceLineChart'), 'line', { labels: [], datasets: [] }, {
                scales: { y: { beginAtZero: true } },
                elements: { line: { tension: 0.2 } }
            });
        };
        
        const renderBarAndPieCharts = (metrics) => {
            const teams = Object.keys(metrics);
            const teamInitials = teams.map(t => getTeamInitials(t));
            const colors = teams.map(team => getColorForTeam(team));
            
            const updateChart = (chart, labels, data, bgColors) => {
                chart.data.labels = labels;
                chart.data.datasets[0].data = data;
                chart.data.datasets[0].backgroundColor = bgColors;
                chart.update();
            };
            
            updateChart(charts.volume, teamInitials, teams.map(t => metrics[t].totalPackages), colors);
            updateChart(charts.efficiency, teamInitials, teams.map(t => metrics[t].avg), colors);
            updateChart(charts.trucks, teamInitials, teams.map(t => metrics[t].totalTrucks), colors);
            updateChart(charts.distribution, teamInitials, teams.map(t => metrics[t].totalPackages), colors);
        };
        
        const calculateHourlyPerformance = () => {
            if (filteredOperationsData.length === 0) return { labels: [], datasets: [] };
            const hourlyLabels = Array.from({length: 11}, (_, i) => `${String(i + 13).padStart(2, '0')}:00`);
            const teams = [...new Set(filteredOperationsData.map(op => op.team))];
            const datasets = teams.map(team => {
                const cumulativeData = hourlyLabels.map((_, index) => {
                    const hour = index + 13;
                    return filteredOperationsData
                        .filter(op => op.team === team && new Date(op.endTime).getHours() < hour + 1)
                        .reduce((sum, op) => sum + op.packages, 0);
                });
                return {
                    label: getTeamInitials(team), data: cumulativeData,
                    borderColor: getColorForTeam(team), fill: false,
                };
            });
            return { labels: hourlyLabels, datasets };
        };

        const renderPerformanceLineChart = () => {
            const hourlyData = calculateHourlyPerformance();
            charts.performanceLineChart.data.labels = hourlyData.labels;
            charts.performanceLineChart.data.datasets = hourlyData.datasets;
            charts.performanceLineChart.update();
        };

        const renderTable = () => {
            const tbody = document.querySelector('#operationsTable tbody');
            tbody.innerHTML = '';
            if (filteredOperationsData.length === 0) {
                tbody.innerHTML = `<tr class="no-data-row"><td colspan="8">Nenhuma operação encontrada.</td></tr>`;
                return;
            }
            filteredOperationsData.sort((a,b) => new Date(b.startTime) - new Date(a.startTime)).forEach(op => {
                const start = new Date(op.startTime);
                const end = new Date(op.endTime);
                const dur = (end - start) / 36e5;
                const eff = dur > 0 ? op.packages / dur : 0;
                tbody.innerHTML += `
                    <tr>
                        <td><strong>${getTeamInitials(op.team)}</strong></td>
                        <td>${op.team}</td>
                        <td>${start.toLocaleString('pt-BR')}</td>
                        <td>${end.toLocaleString('pt-BR')}</td>
                        <td>${op.packages.toLocaleString('pt-BR')}</td>
                        <td>1</td>
                        <td>${dur.toFixed(2)}</td>
                        <td>${eff.toFixed(1)}</td>
                    </tr>`;
            });
        };

        const applyFilter = (filterValue) => {
            const now = new Date();
            const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
            
            let startDate, endDate;
            switch(filterValue) {
                case 'today': startDate = today; endDate = new Date(today.getTime() + 864e5); break;
                case 'yesterday': endDate = today; startDate = new Date(today.getTime() - 864e5); break;
                case 'week': startDate = new Date(today); startDate.setDate(startDate.getDate() - now.getDay()); endDate = new Date(now.getTime() + 864e5); break;
                case 'month': startDate = new Date(now.getFullYear(), now.getMonth(), 1); endDate = new Date(now.getTime() + 864e5); break;
                default: filteredOperationsData = [...allOperationsData]; renderDashboard(); return;
            }
            filteredOperationsData = allOperationsData.filter(op => {
                const opDate = new Date(op.startTime);
                return opDate >= startDate && opDate < endDate;
            });
            renderDashboard();
        };
        
        const setupEventListeners = () => {
            document.getElementById('addOperationForm').addEventListener('submit', (e) => {
                e.preventDefault();
                const teamName = document.getElementById('teamInput').value.trim();
                const startTime = document.getElementById('startInput').value;
                const endTime = document.getElementById('endInput').value;
                const packages = parseInt(document.getElementById('packagesInput').value);

                if (!teamName || !startTime || !endTime || !packages) return alert("Por favor, preencha todos os campos.");
                if (new Date(endTime) <= new Date(startTime)) return alert("A hora de término deve ser posterior à hora de início.");

                allOperationsData.push({ 
                    id: Date.now(), 
                    team: teamName, 
                    startTime: new Date(startTime).toISOString(), 
                    endTime: new Date(endTime).toISOString(), 
                    packages: packages
                });
                
                getColorForTeam(teamName);
                saveData();
                document.getElementById('addOperationForm').reset();
                applyFilter(document.getElementById('timeRange').value);
            });

            document.getElementById('timeRange').addEventListener('change', (e) => applyFilter(e.target.value));
            document.getElementById('exportExcel').addEventListener('click', () => {
                const data = filteredOperationsData.map(op => ({
                    'Iniciais': getTeamInitials(op.team), 'Dupla': op.team, 
                    'Início': new Date(op.startTime).toLocaleString('pt-BR'),
                    'Término': new Date(op.endTime).toLocaleString('pt-BR'), 
                    'Pacotes': op.packages,
                    'Duração (h)': ((new Date(op.endTime) - new Date(op.startTime)) / 36e5).toFixed(2)
                }));
                const ws = XLSX.utils.json_to_sheet(data);
                const wb = XLSX.utils.book_new();
                XLSX.utils.book_append_sheet(wb, ws, 'Operações');
                XLSX.writeFile(wb, 'operacoes.xlsx');
            });
            document.getElementById('captureDashboard').addEventListener('click', () => {
                window.scrollTo(0, 0);
                setTimeout(() => {
                    html2canvas(document.getElementById('dashboard-container')).then(canvas => {
                        const link = document.createElement('a');
                        link.download = 'dashboard.png'; link.href = canvas.toDataURL(); link.click();
                    });
                }, 200);
            });

            const themeSwitch = document.getElementById('theme-switch');
            themeSwitch.addEventListener('change', () => {
                document.documentElement.classList.toggle('dark-mode');
                localStorage.setItem('theme', themeSwitch.checked ? 'dark' : 'light');
            });
            
            document.getElementById('clearDataBtn').addEventListener('click', () => {
                document.getElementById('confirmModal').style.display = 'flex';
            });
            document.getElementById('cancelClearBtn').addEventListener('click', () => {
                document.getElementById('confirmModal').style.display = 'none';
            });
            document.getElementById('confirmClearBtn').addEventListener('click', () => {
                allOperationsData = [];
                teamColors = {};
                nextColorIndex = 0;
                saveData();
                applyFilter('all');
                document.getElementById('confirmModal').style.display = 'none';
            });
        };

        document.addEventListener('DOMContentLoaded', () => {
            const savedTheme = localStorage.getItem('theme');
            if (savedTheme === 'dark') {
                document.documentElement.classList.add('dark-mode');
                document.getElementById('theme-switch').checked = true;
            }
            
            initializeCharts();
            setupEventListeners();
            loadData();
            document.getElementById('timeRange').value = 'today';
            applyFilter('today');
        });
    </script>
</body>
</html>
