<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FIRE Cyber-Planner | Early Retirement Engine</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        cyber: {
                            bg: '#0f172a',
                            card: '#1e293b',
                            border: '#334155',
                            neonGreen: '#10b981',
                            neonCyan: '#06b6d4',
                            neonRed: '#f43f5e',
                            gold: '#f59e0b'
                        }
                    }
                }
            }
        }
    </script>
    <style>
        body { background-color: #0f172a; color: #f8fafc; font-family: 'Inter', system-ui, sans-serif; }
        .cyber-card { background: #1e293b; border: 1px solid #334155; border-radius: 0.75rem; box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.5); }
        input[type=range] { accent-color: #06b6d4; }
    </style>
</head>
<body class="p-4 md:p-8 max-w-7xl mx-auto flex flex-col gap-6">

    <header class="flex flex-col md:flex-row justify-between items-center border-b border-slate-700 pb-4">
        <div>
            <h1 class="text-3xl font-extrabold text-transparent bg-clip-text bg-gradient-to-r from-emerald-400 via-cyan-400 to-blue-500">
                FIRE Terminal & Planner
            </h1>
            <p class="text-slate-400 text-sm mt-1">Simulate accumulation DCA, asset allocation, and retirement drawdown.</p>
        </div>
        <div class="mt-4 md:mt-0 flex gap-4">
            <div class="text-right">
                <span class="text-xs text-slate-400 uppercase tracking-wider block">Target Horizon</span>
                <span id="horizonBadge" class="text-lg font-bold text-cyan-400">0 Years</span>
            </div>
            <div class="text-right border-l border-slate-700 pl-4">
                <span class="text-xs text-slate-400 uppercase tracking-wider block">Peak Assets</span>
                <span id="peakAssetBadge" class="text-lg font-bold text-emerald-400">$0</span>
            </div>
        </div>
    </header>

    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        
        <section class="cyber-card p-5">
            <h2 class="text-lg font-bold text-emerald-400 mb-4 flex items-center gap-2">
                ⏱️ 1. Timeline & DCA Settings
            </h2>
            <div class="space-y-4">
                <div class="grid grid-cols-3 gap-3">
                    <div>
                        <label class="block text-xs text-slate-400 mb-1">Current Age</label>
                        <input type="number" id="inputCurrentAge" value="38" min="18" max="90" class="w-full bg-slate-900 border border-slate-700 rounded p-2 text-cyan-400 font-bold focus:border-cyan-400 focus:outline-none" oninput="updateDashboard()">
                    </div>
                    <div>
                        <label class="block text-xs text-slate-400 mb-1">Retire Age</label>
                        <input type="number" id="inputRetireAge" value="48" min="20" max="95" class="w-full bg-slate-900 border border-slate-700 rounded p-2 text-emerald-400 font-bold focus:border-emerald-400 focus:outline-none" oninput="updateDashboard()">
                    </div>
                    <div>
                        <label class="block text-xs text-slate-400 mb-1">Plan Until</label>
                        <input type="number" id="inputUntilAge" value="88" min="30" max="110" class="w-full bg-slate-900 border border-slate-700 rounded p-2 text-slate-300 font-bold focus:border-slate-500 focus:outline-none" oninput="updateDashboard()">
                    </div>
                </div>

                <div>
                    <div class="flex justify-between">
                        <label class="text-sm text-slate-300">Monthly DCA (Pre-Retirement)</label>
                        <span id="dcaDisplay" class="text-sm font-bold text-emerald-400">$3,500</span>
                    </div>
                    <input type="range" id="sliderDCA" min="0" max="20000" step="250" value="3500" class="w-full cursor-pointer" oninput="updateDashboard()">
                </div>

                <div class="pt-2 border-t border-slate-700/50">
                    <div class="flex justify-between">
                        <label class="text-sm text-slate-300">Monthly Withdrawal (Retirement)</label>
                        <span id="withdrawalDisplay" class="text-sm font-bold text-cyan-400">$7,500</span>
                    </div>
                    <input type="range" id="sliderWithdrawal" min="1000" max="30000" step="500" value="7500" class="w-full cursor-pointer" oninput="updateDashboard()">
                </div>
            </div>
        </section>

        <section class="cyber-card p-5">
            <h2 class="text-lg font-bold text-cyan-400 mb-4 flex items-center gap-2">
                💰 2. Current Asset Portfolio
            </h2>
            <div class="space-y-3">
                <div>
                    <label class="block text-xs text-slate-400 mb-1">Equities (Stocks / Index ETFs) ($)</label>
                    <input type="number" id="inputStocks" value="1750000" class="w-full bg-slate-900 border border-slate-700 rounded p-2 text-slate-100 font-semibold focus:border-cyan-400 focus:outline-none" oninput="updateDashboard()">
                </div>
                <div>
                    <label class="block text-xs text-slate-400 mb-1">Fixed Income (Bonds / Treasuries) ($)</label>
                    <input type="number" id="inputBonds" value="500000" class="w-full bg-slate-900 border border-slate-700 rounded p-2 text-slate-100 font-semibold focus:border-cyan-400 focus:outline-none" oninput="updateDashboard()">
                </div>
                <div>
                    <label class="block text-xs text-slate-400 mb-1">Cash / Liquid Buffer ($)</label>
                    <input type="number" id="inputCash" value="250000" class="w-full bg-slate-900 border border-slate-700 rounded p-2 text-slate-100 font-semibold focus:border-cyan-400 focus:outline-none" oninput="updateDashboard()">
                </div>
                <div class="p-3 bg-slate-900/80 rounded border border-slate-700/80 flex justify-between items-center">
                    <span class="text-xs text-slate-400">Total Portfolio Base:</span>
                    <span id="totalDisplay" class="text-lg font-extrabold text-cyan-400">$0</span>
                </div>
            </div>
        </section>

        <section class="cyber-card p-5 flex flex-col justify-between">
            <div>
                <h2 class="text-lg font-bold text-gold mb-2 flex items-center gap-2">
                    ⚖️ 3. Allocation & Bond Tent
                </h2>
                <div class="w-full h-36 flex justify-center items-center">
                    <canvas id="allocationChart"></canvas>
                </div>
            </div>
            <div id="rebalanceSuggestion" class="text-xs bg-slate-900 p-3 rounded border border-slate-700 text-slate-300 mt-2 space-y-1">
                </div>
        </section>
    </div>

    <section class="cyber-card p-5">
        <div class="flex flex-col md:flex-row justify-between md:items-center border-b border-slate-700 pb-4 mb-4 gap-4">
            <div>
                <h2 class="text-xl font-bold text-slate-100">Full Lifecycle Portfolio Projection</h2>
                <p class="text-xs text-slate-400">Green zone represents DCA accumulation. Blue/Red zone shows drawdown trajectory.</p>
            </div>
            <div class="flex flex-wrap gap-4 text-xs">
                <div>
                    <span class="text-slate-400 block">Expected Return</span>
                    <div class="flex items-center gap-1">
                        <input type="range" id="sliderReturn" min="1" max="15" step="0.5" value="7" class="w-20" oninput="updateDashboard()">
                        <span id="returnDisplay" class="font-bold text-emerald-400">7.0%</span>
                    </div>
                </div>
                <div>
                    <span class="text-slate-400 block">Inflation Rate</span>
                    <div class="flex items-center gap-1">
                        <input type="range" id="sliderInflation" min="0" max="8" step="0.5" value="2.5" class="w-20" oninput="updateDashboard()">
                        <span id="inflationDisplay" class="font-bold text-gold">2.5%</span>
                    </div>
                </div>
            </div>
        </div>

        <div class="w-full h-96 relative">
            <canvas id="simulationChart"></canvas>
        </div>
    </section>

    <script>
        let allocationChart, simulationChart;

        function formatCurrency(num) {
            return '$' + Math.round(num).toLocaleString('en-US');
        }

        function initCharts() {
            // Chart 1: Asset Allocation Doughnut
            const ctxAlloc = document.getElementById('allocationChart').getContext('2d');
            allocationChart = new Chart(ctxAlloc, {
                type: 'doughnut',
                data: {
                    labels: ['Equities', 'Bonds', 'Cash'],
                    datasets: [{
                        data: [0, 0, 0],
                        backgroundColor: ['#06b6d4', '#10b981', '#f59e0b'],
                        borderColor: '#1e293b',
                        borderWidth: 2
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { position: 'right', labels: { color: '#94a3b8', font: { size: 11 } } }
                    }
                }
            });

            // Chart 2: Life Cycle Simulation Line Chart
            const ctxSim = document.getElementById('simulationChart').getContext('2d');
            
            // Gradient creation for lively graphic
            const gradientDCA = ctxSim.createLinearGradient(0, 0, 0, 400);
            gradientDCA.addColorStop(0, 'rgba(16, 185, 129, 0.4)');
            gradientDCA.addColorStop(1, 'rgba(16, 185, 129, 0.0)');

            simulationChart = new Chart(ctxSim, {
                type: 'line',
                data: {
                    labels: [],
                    datasets: [{
                        label: 'Portfolio Trajectory',
                        data: [],
                        borderColor: '#06b6d4',
                        backgroundColor: gradientDCA,
                        fill: true,
                        tension: 0.3,
                        pointRadius: 2,
                        pointHoverRadius: 6
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        x: { grid: { color: '#334155' }, ticks: { color: '#94a3b8' } },
                        y: { 
                            grid: { color: '#334155' }, 
                            ticks: { 
                                color: '#94a3b8',
                                callback: function(val) { return formatCurrency(val); }
                            }
                        }
                    },
                    plugins: {
                        legend: { display: false },
                        tooltip: {
                            backgroundColor: '#0f172a',
                            borderColor: '#334155',
                            borderWidth: 1,
                            titleColor: '#38bdf8',
                            bodyColor: '#f8fafc',
                            callbacks: {
                                label: function(context) {
                                    return ' Balance: ' + formatCurrency(context.raw);
                                }
                            }
                        }
                    }
                }
            });
        }

        function updateDashboard() {
            // 1. Inputs Parsing
            const currentAge = parseInt(document.getElementById('inputCurrentAge').value) || 30;
            let retireAge = parseInt(document.getElementById('inputRetireAge').value) || 50;
            let untilAge = parseInt(document.getElementById('inputUntilAge').value) || 85;

            // Sanity bounds checks
            if (retireAge <= currentAge) retireAge = currentAge + 1;
            if (untilAge <= retireAge) untilAge = retireAge + 1;

            const monthlyDCA = parseFloat(document.getElementById('sliderDCA').value);
            const monthlyWithdrawal = parseFloat(document.getElementById('sliderWithdrawal').value);
            const annualReturn = parseFloat(document.getElementById('sliderReturn').value) / 100;
            const inflation = parseFloat(document.getElementById('sliderInflation').value) / 100;

            const stocks = parseFloat(document.getElementById('inputStocks').value) || 0;
            const bonds = parseFloat(document.getElementById('inputBonds').value) || 0;
            const cash = parseFloat(document.getElementById('inputCash').value) || 0;
            const totalBase = stocks + bonds + cash;

            // Update Displays
            document.getElementById('totalDisplay').innerText = formatCurrency(totalBase);
            document.getElementById('dcaDisplay').innerText = formatCurrency(monthlyDCA);
            document.getElementById('withdrawalDisplay').innerText = formatCurrency(monthlyWithdrawal);
            document.getElementById('returnDisplay').innerText = (annualReturn * 100).toFixed(1) + '%';
            document.getElementById('inflationDisplay').innerText = (inflation * 100).toFixed(1) + '%';
            document.getElementById('horizonBadge').innerText = `${untilAge - currentAge} Years`;

            // 2. Allocation Doughnut Update
            allocationChart.data.datasets[0].data = [stocks, bonds, cash];
            allocationChart.update();

            // Rebalance calculation (Targeting Bond Tent model: 60% Stock, 30% Bond, 10% Cash)
            const targetS = totalBase * 0.60, targetB = totalBase * 0.30, targetC = totalBase * 0.10;
            const diffS = targetS - stocks, diffB = targetB - bonds, diffC = targetC - cash;

            document.getElementById('rebalanceSuggestion').innerHTML = `
                <div class="font-bold text-slate-200">Suggested Target Rebalance (Tent Model):</div>
                <div>• Stocks (60%): ${diffS >= 0 ? '<span class="text-emerald-400">Add ' : '<span class="text-rose-400">Reduce '}${formatCurrency(Math.abs(diffS))}</span></div>
                <div>• Bonds (30%): ${diffB >= 0 ? '<span class="text-emerald-400">Add ' : '<span class="text-rose-400">Reduce '}${formatCurrency(Math.abs(diffB))}</span></div>
                <div>• Cash (10%): ${diffC >= 0 ? '<span class="text-emerald-400">Add ' : '<span class="text-rose-400">Reduce '}${formatCurrency(Math.abs(diffC))}</span></div>
            `;

            // 3. Engine Simulation Calculations
            const monthlyReturn = Math.pow(1 + annualReturn, 1/12) - 1;
            const monthlyInflation = Math.pow(1 + inflation, 1/12) - 1;

            let balance = totalBase;
            let currentMonthlyDrawdown = monthlyWithdrawal;
            let peakValue = balance;

            const labels = [];
            const dataPoints = [];

            for (let age = currentAge; age <= untilAge; age++) {
                labels.push(`Age ${age}${age === retireAge ? ' (RETIRE)' : ''}`);
                dataPoints.push(Math.round(balance));

                if (balance > peakValue) peakValue = balance;

                // Simulate 12 months for this year
                for (let m = 0; m < 12; m++) {
                    if (age < retireAge) {
                        // Accumulation Phase: Compound Return + DCA
                        balance = balance * (1 + monthlyReturn) + monthlyDCA;
                    } else {
                        // Retirement Phase: Compound Return - Inflation adjusted withdrawal
                        balance = balance * (1 + monthlyReturn) - currentMonthlyDrawdown;
                        currentMonthlyDrawdown = currentMonthlyDrawdown * (1 + monthlyInflation);
                    }
                }
                if (balance < 0) balance = 0;
            }

            document.getElementById('peakAssetBadge').innerText = formatCurrency(peakValue);

            // Update Simulation Line Chart
            simulationChart.data.labels = labels;
            simulationChart.data.datasets[0].data = dataPoints;

            // Highlight status (Red if portfolio runs dry, Emerald/Cyan if surviving)
            const endBalance = dataPoints[dataPoints.length - 1];
            if (endBalance === 0) {
                simulationChart.data.datasets[0].borderColor = '#f43f5e'; // Neon Red
                simulationChart.data.datasets[0].backgroundColor = 'rgba(244, 63, 94, 0.15)';
            } else {
                simulationChart.data.datasets[0].borderColor = '#06b6d4'; // Neon Cyan
                simulationChart.data.datasets[0].backgroundColor = 'rgba(6, 182, 212, 0.15)';
            }

            simulationChart.update();
        }

        window.onload = function() {
            initCharts();
            updateDashboard();
        };
    </script>
</body>
</html>
