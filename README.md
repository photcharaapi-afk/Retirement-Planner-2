<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FIRE Retirement Planner</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { background-color: #f3f4f6; }
        .card { background-color: white; border-radius: 0.5rem; padding: 1.5rem; box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1); }
    </style>
</head>
<body class="text-gray-800 font-sans p-4 md:p-10 max-w-6xl mx-auto flex flex-col gap-8">

    <header class="text-center">
        <h1 class="text-4xl font-bold text-blue-900 mb-2">FIRE Retirement Planner</h1>
        <p class="text-gray-600">Enter your current assets, see rebalancing suggestions, and simulate your retirement withdrawals.</p>
    </header>

    <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
        
        <section class="card">
            <h2 class="text-2xl font-semibold mb-4 border-b pb-2">1. Current Assets (Manual Input)</h2>
            <div class="flex flex-col gap-4">
                <div>
                    <label class="block text-sm font-medium text-gray-700">Equities (Stocks / ETFs) $</label>
                    <input type="number" id="inputStocks" value="1500000" class="mt-1 block w-full rounded-md border-gray-300 border p-2 shadow-sm focus:border-blue-500 focus:ring-blue-500" oninput="updateDashboard()">
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700">Fixed Income (Bonds) $</label>
                    <input type="number" id="inputBonds" value="300000" class="mt-1 block w-full rounded-md border-gray-300 border p-2 shadow-sm focus:border-blue-500 focus:ring-blue-500" oninput="updateDashboard()">
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700">Cash & Equivalents $</label>
                    <input type="number" id="inputCash" value="50000" class="mt-1 block w-full rounded-md border-gray-300 border p-2 shadow-sm focus:border-blue-500 focus:ring-blue-500" oninput="updateDashboard()">
                </div>
                <div class="mt-4 p-4 bg-blue-50 rounded-md">
                    <h3 class="text-lg font-bold text-blue-900">Total Portfolio: <span id="totalDisplay">$1,850,000</span></h3>
                </div>
            </div>
        </section>

        <section class="card">
            <h2 class="text-2xl font-semibold mb-4 border-b pb-2">2. Allocation & Rebalancing</h2>
            <div class="w-full h-48 flex justify-center mb-4">
                <canvas id="allocationChart"></canvas>
            </div>
            <div id="rebalanceSuggestion" class="text-sm bg-yellow-50 p-4 rounded border border-yellow-200">
                </div>
        </section>

    </div>

    <section class="card w-full">
        <h2 class="text-2xl font-semibold mb-4 border-b pb-2">3. Retirement Withdrawal Simulator</h2>
        
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8 bg-gray-50 p-4 rounded-md">
            <div>
                <label class="block text-sm font-medium text-gray-700">Monthly Withdrawal: <span id="monthlyDisplay" class="font-bold text-blue-600">$5,000</span></label>
                <input type="range" id="sliderMonthly" min="1000" max="25000" step="500" value="5000" class="w-full mt-2 cursor-pointer" oninput="updateDashboard()">
            </div>
            <div>
                <label class="block text-sm font-medium text-gray-700">Expected Annual Return: <span id="returnDisplay" class="font-bold text-blue-600">6.0%</span></label>
                <input type="range" id="sliderReturn" min="0" max="15" step="0.5" value="6" class="w-full mt-2 cursor-pointer" oninput="updateDashboard()">
            </div>
            <div>
                <label class="block text-sm font-medium text-gray-700">Inflation Rate: <span id="inflationDisplay" class="font-bold text-blue-600">2.5%</span></label>
                <input type="range" id="sliderInflation" min="0" max="8" step="0.5" value="2.5" class="w-full mt-2 cursor-pointer" oninput="updateDashboard()">
            </div>
        </div>

        <div class="w-full h-80">
            <canvas id="simulationChart"></canvas>
        </div>
    </section>

    <script>
        // Target Allocation for Early Retirement (Bond Tent Model)
        const TARGET_STOCKS_PCT = 0.60;
        const TARGET_BONDS_PCT = 0.30;
        const TARGET_CASH_PCT = 0.10;

        let allocationChart, simulationChart;

        function formatCurrency(num) {
            return '$' + parseFloat(num).toLocaleString('en-US', { maximumFractionDigits: 0 });
        }

        function initCharts() {
            // Doughnut Chart Initialization
            const ctxAlloc = document.getElementById('allocationChart').getContext('2d');
            allocationChart = new Chart(ctxAlloc, {
                type: 'doughnut',
                data: {
                    labels: ['Equities', 'Bonds', 'Cash'],
                    datasets: [{
                        data: [0, 0, 0],
                        backgroundColor: ['#3b82f6', '#10b981', '#f59e0b'],
                        borderWidth: 1
                    }]
                },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { position: 'right' } } }
            });

            // Line Chart Initialization
            const ctxSim = document.getElementById('simulationChart').getContext('2d');
            simulationChart = new Chart(ctxSim, {
                type: 'line',
                data: {
                    labels: Array.from({length: 41}, (_, i) => `Year ${i}`),
                    datasets: [{
                        label: 'Portfolio Value',
                        data: [],
                        borderColor: '#3b82f6',
                        backgroundColor: 'rgba(59, 130, 246, 0.2)',
                        fill: true,
                        tension: 0.4
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: { y: { beginAtZero: true, ticks: { callback: function(value) { return formatCurrency(value); } } } },
                    plugins: { tooltip: { callbacks: { label: function(context) { return formatCurrency(context.raw); } } } }
                }
            });
        }

        function updateDashboard() {
            // 1. Get Values
            const stocks = parseFloat(document.getElementById('inputStocks').value) || 0;
            const bonds = parseFloat(document.getElementById('inputBonds').value) || 0;
            const cash = parseFloat(document.getElementById('inputCash').value) || 0;
            const total = stocks + bonds + cash;

            document.getElementById('totalDisplay').innerText = formatCurrency(total);

            // 2. Update Allocation Chart
            allocationChart.data.datasets[0].data = [stocks, bonds, cash];
            allocationChart.update();

            // 3. Rebalancing Suggestions
            const targetStocks = total * TARGET_STOCKS_PCT;
            const targetBonds = total * TARGET_BONDS_PCT;
            const targetCash = total * TARGET_CASH_PCT;

            const diffStocks = targetStocks - stocks;
            const diffBonds = targetBonds - bonds;
            const diffCash = targetCash - cash;

            let suggestionHTML = `<strong>Target Early Retirement Model:</strong> 60% Stocks, 30% Bonds, 10% Cash.<br><br>`;
            suggestionHTML += `<ul class="list-disc pl-5">`;
            suggestionHTML += `<li><strong>Equities:</strong> ${diffStocks > 0 ? 'Buy' : 'Sell'} ${formatCurrency(Math.abs(diffStocks))}</li>`;
            suggestionHTML += `<li><strong>Bonds:</strong> ${diffBonds > 0 ? 'Buy' : 'Sell'} ${formatCurrency(Math.abs(diffBonds))}</li>`;
            suggestionHTML += `<li><strong>Cash Buffer:</strong> ${diffCash > 0 ? 'Increase by' : 'Decrease by'} ${formatCurrency(Math.abs(diffCash))}</li>`;
            suggestionHTML += `</ul>`;
            
            document.getElementById('rebalanceSuggestion').innerHTML = suggestionHTML;

            // 4. Withdrawal Simulation
            const monthlyWithdrawal = parseFloat(document.getElementById('sliderMonthly').value);
            const annualReturn = parseFloat(document.getElementById('sliderReturn').value) / 100;
            const inflation = parseFloat(document.getElementById('sliderInflation').value) / 100;
            
            document.getElementById('monthlyDisplay').innerText = formatCurrency(monthlyWithdrawal);
            document.getElementById('returnDisplay').innerText = (annualReturn * 100).toFixed(1) + '%';
            document.getElementById('inflationDisplay').innerText = (inflation * 100).toFixed(1) + '%';

            let currentBalance = total;
            let currentAnnualWithdrawal = monthlyWithdrawal * 12;
            const simData = [currentBalance];

            for (let year = 1; year <= 40; year++) {
                // Add investment growth
                currentBalance = currentBalance * (1 + annualReturn);
                // Subtract inflation-adjusted withdrawal
                currentBalance = currentBalance - currentAnnualWithdrawal;
                // Adjust next year's withdrawal for inflation
                currentAnnualWithdrawal = currentAnnualWithdrawal * (1 + inflation);
                
                // Prevent negative balances in chart
                if (currentBalance < 0) currentBalance = 0; 
                simData.push(currentBalance);
            }

            simulationChart.data.datasets[0].data = simData;
            
            // Change color if portfolio runs out of money
            if (simData[40] === 0) {
                simulationChart.data.datasets[0].borderColor = '#ef4444'; // Red
                simulationChart.data.datasets[0].backgroundColor = 'rgba(239, 68, 68, 0.2)';
            } else {
                simulationChart.data.datasets[0].borderColor = '#3b82f6'; // Blue
                simulationChart.data.datasets[0].backgroundColor = 'rgba(59, 130, 246, 0.2)';
            }
            
            simulationChart.update();
        }

        // Initialize on load
        window.onload = function() {
            initCharts();
            updateDashboard();
        };
    </script>
</body>
</html>
