<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Retail Sales Performance Analysis (DA-001)</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --bg-gradient: linear-gradient(135deg, #f1f5f9 0%, #cbd5e1 100%);
            --card-bg: #ffffff;
            --primary: #4f46e5;
            --primary-hover: #4338ca;
            --accent: #10b981;
            --text-main: #0f172a;
            --text-muted: #475569;
            --border: #e2e8f0;
            --shadow: 0 10px 30px -5px rgba(0, 0, 0, 0.08), 0 8px 10px -6px rgba(0, 0, 0, 0.04);
            --header-bg: linear-gradient(135deg, #4f46e5 0%, #3b82f6 100%);
        }

        [data-theme="dark"] {
            --bg-gradient: linear-gradient(135deg, #090d16 0%, #111827 100%);
            --card-bg: #1f2937;
            --primary: #6366f1;
            --primary-hover: #4f46e5;
            --accent: #34d399;
            --text-main: #f9fafb;
            --text-muted: #9ca3af;
            --border: #374151;
            --shadow: 0 10px 30px -5px rgba(0, 0, 0, 0.4);
            --header-bg: linear-gradient(135deg, #312e81 0%, #1e40af 100%);
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Plus Jakarta Sans', sans-serif; transition: background 0.3s ease, color 0.3s ease, transform 0.2s ease; }
        body { background: var(--bg-gradient); color: var(--text-main); min-height: 100vh; padding: 20px; }
        .container { max-width: 1400px; margin: 0 auto; }

        /* Responsive Header */
        header { background: var(--header-bg); color: white; padding: 30px; border-radius: 24px; box-shadow: var(--shadow); display: flex; justify-content: space-between; align-items: center; margin-bottom: 25px; flex-wrap: wrap; gap: 20px; position: relative; overflow: hidden; }
        header::after { content: ''; position: absolute; top: -60px; right: -60px; width: 220px; height: 220px; background: rgba(255,255,255,0.1); border-radius: 50%; pointer-events: none; }
        .header-title h1 { font-size: clamp(22px, 4vw, 30px); font-weight: 800; letter-spacing: -0.5px; }
        .header-title p { font-size: 13px; opacity: 0.9; margin-top: 6px; font-weight: 400; }
        .meta-badges { display: flex; gap: 10px; align-items: center; flex-wrap: wrap; }
        .badge { background: rgba(255, 255, 255, 0.2); backdrop-filter: blur(8px); color: #fff; padding: 8px 16px; border-radius: 30px; font-size: 12px; font-weight: 600; border: 1px solid rgba(255,255,255,0.25); }
        .theme-toggle { background: #ffffff; color: var(--primary); border: none; padding: 10px 16px; border-radius: 12px; cursor: pointer; font-size: 13px; font-weight: 700; box-shadow: 0 4px 12px rgba(0,0,0,0.15); }
        .theme-toggle:hover { transform: translateY(-2px); }

        /* Responsive Navigation Tabs */
        .nav-tabs { display: flex; gap: 10px; margin-bottom: 25px; overflow-x: auto; padding-bottom: 8px; scrollbar-width: thin; }
        .tab-btn { background: var(--card-bg); border: 1px solid var(--border); color: var(--text-muted); padding: 12px 20px; border-radius: 14px; cursor: pointer; font-weight: 600; font-size: 13px; box-shadow: var(--shadow); white-space: nowrap; flex-shrink: 0; }
        .tab-btn.active { background: var(--primary); color: white; border-color: var(--primary); box-shadow: 0 8px 20px rgba(79, 70, 229, 0.35); }

        .tab-content { display: none; animation: fadeIn 0.3s ease-in-out; }
        .tab-content.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(6px); } to { opacity: 1; transform: translateY(0); } }

        /* KPI Cards Grid */
        .kpi-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(240px, 1fr)); gap: 20px; margin-bottom: 25px; }
        .kpi-card { background: var(--card-bg); padding: 24px; border-radius: 20px; box-shadow: var(--shadow); border: 1px solid var(--border); position: relative; overflow: hidden; }
        .kpi-card::before { content: ''; position: absolute; left: 0; top: 0; height: 100%; width: 6px; background: var(--primary); }
        .kpi-card:nth-child(2)::before { background: #3b82f6; }
        .kpi-card:nth-child(3)::before { background: #10b981; }
        .kpi-card:nth-child(4)::before { background: #f59e0b; }
        .kpi-card h3 { font-size: 12px; text-transform: uppercase; color: var(--text-muted); font-weight: 700; letter-spacing: 0.8px; }
        .kpi-card .value { font-size: clamp(24px, 3vw, 32px); font-weight: 800; color: var(--text-main); margin: 8px 0 4px 0; letter-spacing: -0.5px; }
        .kpi-card .desc { font-size: 12px; color: var(--text-muted); }

        /* Charts Layout */
        .charts-grid { display: grid; grid-template-columns: 2fr 1fr; gap: 20px; margin-bottom: 25px; }
        @media(max-width: 1024px) { .charts-grid { grid-template-columns: 1fr; } }
        .chart-card { background: var(--card-bg); padding: 25px; border-radius: 20px; box-shadow: var(--shadow); border: 1px solid var(--border); }
        .chart-card h2 { font-size: 17px; font-weight: 700; margin-bottom: 15px; color: var(--text-main); }

        /* Section Cards */
        .section-card { background: var(--card-bg); padding: 30px; border-radius: 20px; box-shadow: var(--shadow); border: 1px solid var(--border); margin-bottom: 25px; }
        .section-card h2 { font-size: 18px; font-weight: 800; color: var(--text-main); margin-bottom: 15px; border-bottom: 2px solid var(--border); padding-bottom: 10px; }

        /* Responsive Tables */
        .table-responsive { width: 100%; overflow-x: auto; }
        table { width: 100%; border-collapse: collapse; margin-top: 10px; font-size: 13px; white-space: nowrap; }
        th, td { padding: 14px 16px; text-align: left; border-bottom: 1px solid var(--border); color: var(--text-main); }
        th { background: rgba(79, 70, 229, 0.06); font-weight: 700; color: var(--primary); text-transform: uppercase; font-size: 11px; letter-spacing: 0.5px; }
        tr:hover { background: rgba(79, 70, 229, 0.02); }

        .filters { display: flex; gap: 12px; margin-bottom: 18px; flex-wrap: wrap; }
        .filters input, .filters select { padding: 10px 16px; border-radius: 12px; border: 1px solid var(--border); background: var(--card-bg); color: var(--text-main); font-size: 13px; outline: none; box-shadow: var(--shadow); flex: 1; min-width: 200px; }
        
        .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        @media(max-width: 768px) { .grid-2 { grid-template-columns: 1fr; } }
        
        ul.styled-list { padding-left: 20px; }
        ul.styled-list li { margin-bottom: 10px; color: var(--text-main); font-size: 14px; }
    </style>
</head>
<body data-theme="light">
    <div class="container">
        <!-- Header -->
        <header>
            <div class="header-title">
                <h1>Retail Sales Performance Analysis (DA-001)</h1>
                <p>B.Tech Intro to Data Analytics | Semester III, Section B | Group BT_DA_13</p>
            </div>
            <div class="meta-badges">
                <span class="badge">👨‍💻 Arpit Sharma & Nitin Kumar</span>
                <span class="badge">🎯 Mentor: Nitish Sheoran</span>
                <button class="theme-toggle" onclick="toggleTheme()">🌓 Theme</button>
            </div>
        </header>

        <!-- Navigation Tabs -->
        <div class="nav-tabs">
            <button class="tab-btn active" onclick="switchTab('overview')">📊 Overview</button>
            <button class="tab-btn" onclick="switchTab('products')">🛍️ Products</button>
            <button class="tab-btn" onclick="switchTab('trends')">📈 Trends</button>
            <button class="tab-btn" onclick="switchTab('transactions')">🗂️ Data Grid</button>
            <button class="tab-btn" onclick="switchTab('methodology')">⚙️ Methodology</button>
            <button class="tab-btn" onclick="switchTab('recommendations')">💡 Strategy</button>
        </div>

        <!-- TAB 1: EXECUTIVE OVERVIEW -->
        <div id="overview" class="tab-content active">
            <div class="kpi-grid">
                <div class="kpi-card">
                    <h3>Total Gross Revenue</h3>
                    <div class="value">$1,123,749.14</div>
                    <div class="desc">Synthesized across 5,000 orders (2023)</div>
                </div>
                <div class="kpi-card">
                    <h3>Total Orders Placed</h3>
                    <div class="value">5,000</div>
                    <div class="desc">100% Unique Transaction IDs</div>
                </div>
                <div class="kpi-card">
                    <h3>Total Units Sold</h3>
                    <div class="value">8,086</div>
                    <div class="desc">Weighted distribution (1-5 units)</div>
                </div>
                <div class="kpi-card">
                    <h3>Average Order Value</h3>
                    <div class="value">$224.75</div>
                    <div class="desc">Robust basket size per transaction</div>
                </div>
            </div>

            <div class="charts-grid">
                <div class="chart-card">
                    <h2>Monthly Revenue Distribution (2023)</h2>
                    <canvas id="monthlyRevenueChart" height="150"></canvas>
                </div>
                <div class="chart-card">
                    <h2>Category Revenue Share (%)</h2>
                    <canvas id="categoryPieChart" height="190"></canvas>
                </div>
            </div>

            <div class="section-card">
                <h2>Project Objective & Executive Summary</h2>
                <p style="margin-bottom: 18px; font-size: 15px; line-height: 1.7;">
                    Analyze transaction-level retail sales data to isolate revenue drivers, evaluate product category distribution, and evaluate monthly performance trends to assist operational decision-making.
                </p>
                <div class="grid-2">
                    <div>
                        <h3 style="font-size: 15px; color: var(--primary); margin-bottom: 10px; font-weight: 700;">Key Insights</h3>
                        <ul class="styled-list">
                            <li><strong>Electronics Dominance:</strong> Electronics anchors ~62% of total operational revenue (~$697K).</li>
                            <li><strong>Core Revenue Engines:</strong> Laptops ($999.99) and Smartphones ($699.99) dominate top revenue charts.</li>
                            <li><strong>Stable Order Volume:</strong> Monthly order volume remains steady between 368 and 473 orders.</li>
                        </ul>
                    </div>
                    <div>
                        <h3 style="font-size: 15px; color: var(--primary); margin-bottom: 10px; font-weight: 700;">Data Quality Controls</h3>
                        <ul class="styled-list">
                            <li><strong>Dataset Size:</strong> 5,000 records across 5 categories and 25 distinct SKUs.</li>
                            <li><strong>Missing Data Imputation:</strong> ~2% null CustomerIDs successfully imputed as 'Guest'.</li>
                            <li><strong>Verification:</strong> Total Revenue verified as exact product of Quantity and UnitPrice.</li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>

        <!-- TAB 2: PRODUCTS -->
        <div id="products" class="tab-content">
            <div class="charts-grid">
                <div class="chart-card">
                    <h2>Top 10 Products by Revenue Generation</h2>
                    <canvas id="topProductsChart" height="180"></canvas>
                </div>
                <div class="chart-card">
                    <h2>Category Performance Breakdown</h2>
                    <div class="table-responsive">
                        <table id="categoryTable">
                            <thead>
                                <tr>
                                    <th>Category</th>
                                    <th>Revenue</th>
                                    <th>Orders</th>
                                    <th>Units</th>
                                    <th>Share</th>
                                </tr>
                            </thead>
                            <tbody></tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>

        <!-- TAB 3: TRENDS -->
        <div id="trends" class="tab-content">
            <div class="chart-card" style="margin-bottom: 25px;">
                <h2>Dual-Axis Monthly Revenue & Order Volume Trends</h2>
                <canvas id="dualAxisChart" height="120"></canvas>
            </div>
            <div class="section-card">
                <h2>Monthly Performance Summary Table</h2>
                <div class="table-responsive">
                    <table id="monthlyTable">
                        <thead>
                            <tr>
                                <th>Month</th>
                                <th>Monthly Revenue</th>
                                <th>Order Volume</th>
                                <th>Units Sold</th>
                                <th>Avg Order Value</th>
                            </tr>
                        </thead>
                        <tbody></tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- TAB 4: TRANSACTIONS -->
        <div id="transactions" class="tab-content">
            <div class="section-card">
                <h2>Transaction Data Grid (5,000 Records)</h2>
                <div class="filters">
                    <input type="text" id="searchInput" placeholder="Search by ID or Product..." onkeyup="filterTable()">
                    <select id="categoryFilter" onchange="filterTable()">
                        <option value="">All Categories</option>
                        <option value="Electronics">Electronics</option>
                        <option value="Apparel">Apparel</option>
                        <option value="Home & Kitchen">Home & Kitchen</option>
                        <option value="Beauty">Beauty</option>
                        <option value="Sports">Sports</option>
                    </select>
                </div>
                <div class="table-responsive" style="max-height: 500px;">
                    <table id="txTable">
                        <thead>
                            <tr>
                                <th>TransactionID</th>
                                <th>Timestamp</th>
                                <th>CustomerID</th>
                                <th>Category</th>
                                <th>Product</th>
                                <th>Quantity</th>
                                <th>UnitPrice ($)</th>
                                <th>Revenue ($)</th>
                            </tr>
                        </thead>
                        <tbody id="txTableBody"></tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- TAB 5: METHODOLOGY -->
        <div id="methodology" class="tab-content">
            <div class="section-card">
                <h2>Data Ingestion, Cleaning & Feature Engineering Workflow</h2>
                <p style="margin-bottom: 15px; font-size: 14px;">Executed via Python in Google Colab utilizing Pandas, NumPy, Matplotlib, and Seaborn.</p>
                <div class="grid-2">
                    <div>
                        <h3 style="font-size: 15px; color: var(--primary); margin-bottom: 8px;">1. Ingestion & Synthesis</h3>
                        <p style="color: var(--text-muted); font-size: 13.5px; line-height: 1.6;">Generated 5,000 transactions for calendar year 2023 with predefined probability weights for quantities and seeded for reproducibility.</p>
                        <h3 style="font-size: 15px; color: var(--primary); margin: 15px 0 8px 0;">2. Cleaning & Validation</h3>
                        <p style="color: var(--text-muted); font-size: 13.5px; line-height: 1.6;">Checked for duplicate TransactionIDs (0 found). Handled missing CustomerIDs (~2% nulls imputed as 'Guest').</p>
                    </div>
                    <div>
                        <h3 style="font-size: 15px; color: var(--primary); margin-bottom: 8px;">3. Feature Engineering</h3>
                        <p style="color: var(--text-muted); font-size: 13.5px; line-height: 1.6;">Mapped calculated monetary features (`Revenue = Quantity × UnitPrice`) and date dimensions (`MonthName`, `MonthNum`).</p>
                        <h3 style="font-size: 15px; color: var(--primary); margin: 15px 0 8px 0;">4. Analytical Aggregation</h3>
                        <p style="color: var(--text-muted); font-size: 13.5px; line-height: 1.6;">Grouped data by category, product, and month to derive high-level performance KPIs and visual charts.</p>
                    </div>
                </div>
            </div>
        </div>

        <!-- TAB 6: RECOMMENDATIONS -->
        <div id="recommendations" class="tab-content">
            <div class="section-card">
                <h2>Strategic Takeaways & Operational Decisions</h2>
                <div class="grid-2">
                    <div style="background: rgba(79, 70, 229, 0.05); padding: 22px; border-radius: 16px; border-left: 5px solid var(--primary);">
                        <h3 style="font-size: 16px; color: var(--primary); margin-bottom: 8px;">1. Leverage Electronics Anchor</h3>
                        <p style="color: var(--text-muted); font-size: 14px; line-height: 1.6;">Since Electronics represents nearly half/majority of top-line revenue, inventory priorities and co-promotional packaging (bundling accessories with primary electronics items) should be optimized.</p>
                    </div>
                    <div style="background: rgba(16, 185, 129, 0.05); padding: 22px; border-radius: 16px; border-left: 5px solid var(--accent);">
                        <h3 style="font-size: 16px; color: var(--accent); margin-bottom: 8px;">2. Optimize Off-Peak Quarters</h3>
                        <p style="color: var(--text-muted); font-size: 14px; line-height: 1.6;">Sales trends indicate relatively consistent volume, but targeted promotional campaigns during slightly lower volume periods can assist in flattening seasonal performance dips.</p>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        function switchTab(tabId) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
            document.querySelectorAll('.tab-btn').forEach(el => el.classList.remove('active'));
            document.getElementById(tabId).classList.add('active');
            event.currentTarget.classList.add('active');
        }

        function toggleTheme() {
            const body = document.body;
            const currentTheme = body.getAttribute('data-theme');
            body.setAttribute('data-theme', currentTheme === 'light' ? 'dark' : 'light');
        }

        const categories = ['Electronics', 'Apparel', 'Home & Kitchen', 'Beauty', 'Sports'];
        const products = {
            'Electronics': ['Smartphone', 'Laptop', 'Headphones', 'Smartwatch', 'Charger'],
            'Apparel': ['T-Shirt', 'Jeans', 'Jacket', 'Sneakers', 'Socks'],
            'Home & Kitchen': ['Blender', 'Coffee Maker', 'Air Fryer', 'Cookware Set', 'Vacuum'],
            'Beauty': ['Lipstick', 'Foundation', 'Perfume', 'Skincare Set', 'Mascara'],
            'Sports': ['Yoga Mat', 'Dumbbells', 'Water Bottle', 'Running Shoes', 'Backpack']
        };
        const prices = {
            'Smartphone': 699.99, 'Laptop': 999.99, 'Headphones': 149.99, 'Smartwatch': 249.99, 'Charger': 19.99,
            'T-Shirt': 24.99, 'Jeans': 49.99, 'Jacket': 89.99, 'Sneakers': 79.99, 'Socks': 9.99,
            'Blender': 59.99, 'Coffee Maker': 89.99, 'Air Fryer': 119.99, 'Cookware Set': 149.99, 'Vacuum': 199.99,
            'Lipstick': 19.99, 'Foundation': 34.99, 'Perfume': 79.99, 'Skincare Set': 49.99, 'Mascara': 14.99,
            'Yoga Mat': 29.99, 'Dumbbells': 49.99, 'Water Bottle': 14.99, 'Running Shoes': 89.99, 'Backpack': 39.99
        };

        function mulberry32(a) {
            return function() {
                let t = a += 0x6D2B79F5;
                t = Math.imul(t ^ t >>> 15, t | 1);
                t ^= t + Math.imul(t ^ t >>> 7, t | 61);
                return ((t ^ t >>> 14) >>> 0) / 4294967296;
            }
        }
        const rand = mulberry32(42);
        const dataset = [];
        const monthNames = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];
        
        for(let i=0; i<5000; i++) {
            let txId = "TX" + (10000 + i);
            let dayOffset = Math.floor(rand() * 365);
            let date = new Date(2023, 0, 1 + dayOffset);
            let catIdx = Math.floor(rand() * categories.length);
            let cat = categories[catIdx];
            let prodList = products[cat];
            let prod = prodList[Math.floor(rand() * prodList.length)];
            
            let qRand = rand();
            let qty = 1;
            if(qRand > 0.6) qty = 2;
            if(qRand > 0.85) qty = 3;
            if(qRand > 0.95) qty = 4;
            if(qRand > 0.99) qty = 5;

            let unitPrice = prices[prod];
            let custId = rand() > 0.02 ? "C" + Math.floor(1000 + rand() * 1500) : "Guest";
            let revenue = qty * unitPrice;
            let monthName = monthNames[date.getMonth()];

            dataset.push({
                transactionId: txId,
                timestamp: date.toISOString().replace('T', ' ').substring(0, 19),
                customerId: custId,
                category: cat,
                product: prod,
                quantity: qty,
                unitPrice: unitPrice,
                revenue: revenue,
                monthName: monthName
            });
        }

        function populateTable(data) {
            const tbody = document.getElementById('txTableBody');
            tbody.innerHTML = '';
            data.slice(0, 200).forEach(row => {
                tbody.innerHTML += `<tr>
                    <td>${row.transactionId}</td>
                    <td>${row.timestamp}</td>
                    <td>${row.customerId}</td>
                    <td>${row.category}</td>
                    <td>${row.product}</td>
                    <td>${row.quantity}</td>
                    <td>$${row.unitPrice.toFixed(2)}</td>
                    <td>$${row.revenue.toFixed(2)}</td>
                </tr>`;
            });
        }
        populateTable(dataset);

        function filterTable() {
            let search = document.getElementById('searchInput').value.toLowerCase();
            let catFilter = document.getElementById('categoryFilter').value;
            let filtered = dataset.filter(row => {
                let matchSearch = row.transactionId.toLowerCase().includes(search) || row.product.toLowerCase().includes(search);
                let matchCat = catFilter === "" || row.category === catFilter;
                return matchSearch && matchCat;
            });
            populateTable(filtered);
        }

        let catRev = {};
        let monthlyRev = {};
        let monthlyOrders = {};
        let prodRev = {};

        monthNames.forEach(m => { monthlyRev[m] = 0; monthlyOrders[m] = 0; });
        categories.forEach(c => catRev[c] = {rev: 0, orders: 0, units: 0});

        dataset.forEach(row => {
            catRev[row.category].rev += row.revenue;
            catRev[row.category].orders += 1;
            catRev[row.category].units += row.quantity;
            monthlyRev[row.monthName] += row.revenue;
            monthlyOrders[row.monthName] += 1;
            if(!prodRev[row.product]) prodRev[row.product] = {rev: 0, cat: row.category};
            prodRev[row.product].rev += row.revenue;
        });

        const catTbody = document.querySelector('#categoryTable tbody');
        let totalRevAll = dataset.reduce((acc, r) => acc + r.revenue, 0);
        Object.keys(catRev).sort((a,b) => catRev[b].rev - catRev[a].rev).forEach(cat => {
            let share = (catRev[cat].rev / totalRevAll * 100).toFixed(1);
            catTbody.innerHTML += `<tr>
                <td><strong>${cat}</strong></td>
                <td>$${catRev[cat].rev.toLocaleString(undefined, {minimumFractionDigits:2, maximumFractionDigits:2})}</td>
                <td>${catRev[cat].orders.toLocaleString()}</td>
                <td>${catRev[cat].units.toLocaleString()}</td>
                <td>${share}%</td>
            </tr>`;
        });

        const monthlyTbody = document.querySelector('#monthlyTable tbody');
        monthNames.forEach(m => {
            let mRev = monthlyRev[m];
            let mOrders = monthlyOrders[m];
            let mUnits = dataset.filter(r => r.monthName === m).reduce((a,b) => a + b.quantity, 0);
            let mAov = mRev / mOrders;
            monthlyTbody.innerHTML += `<tr>
                <td><strong>${m}</strong></td>
                <td>$${mRev.toLocaleString(undefined, {minimumFractionDigits:2, maximumFractionDigits:2})}</td>
                <td>${mOrders}</td>
                <td>${mUnits}</td>
                <td>$${mAov.toFixed(2)}</td>
            </tr>`;
        });

        new Chart(document.getElementById('monthlyRevenueChart'), {
            type: 'bar',
            data: {
                labels: monthNames.map(m => m.substring(0,3)),
                datasets: [{
                    label: 'Revenue ($)',
                    data: monthNames.map(m => monthlyRev[m]),
                    backgroundColor: '#6366f1',
                    borderRadius: 8
                }]
            },
            options: { responsive: true, plugins: { legend: { display: false } } }
        });

        new Chart(document.getElementById('categoryPieChart'), {
            type: 'doughnut',
            data: {
                labels: Object.keys(catRev),
                datasets: [{
                    data: Object.values(catRev).map(v => v.rev),
                    backgroundColor: ['#6366f1', '#3b82f6', '#10b981', '#f59e0b', '#ec4899']
                }]
            },
            options: { responsive: true, plugins: { legend: { position: 'bottom', labels: { boxWidth: 12 } } } }
        });

        let sortedProds = Object.keys(prodRev).map(p => ({name: p, rev: prodRev[p].rev})).sort((a,b) => b.rev - a.rev).slice(0, 10);
        new Chart(document.getElementById('topProductsChart'), {
            type: 'bar',
            data: {
                labels: sortedProds.map(p => p.name),
                datasets: [{
                    label: 'Revenue ($)',
                    data: sortedProds.map(p => p.rev),
                    backgroundColor: '#10b981',
                    borderRadius: 8
                }]
            },
            options: { indexAxis: 'y', responsive: true, plugins: { legend: { display: false } } }
        });

        new Chart(document.getElementById('dualAxisChart'), {
            type: 'bar',
            data: {
                labels: monthNames,
                datasets: [
                    {
                        type: 'bar',
                        label: 'Revenue ($)',
                        data: monthNames.map(m => monthlyRev[m]),
                        backgroundColor: '#6366f1',
                        borderRadius: 6,
                        yAxisID: 'y'
                    },
                    {
                        type: 'line',
                        label: 'Order Volume',
                        data: monthNames.map(m => monthlyOrders[m]),
                        borderColor: '#ec4899',
                        backgroundColor: '#ec4899',
                        borderWidth: 3,
                        tension: 0.3,
                        yAxisID: 'y1'
                    }
                ]
            },
            options: {
                responsive: true,
                scales: {
                    y: { type: 'linear', position: 'left', title: {display: true, text: 'Revenue ($)'} },
                    y1: { type: 'linear', position: 'right', grid: {drawOnChartArea: false}, title: {display: true, text: 'Order Volume'} }
                }
            }
        });
    </script>
</body>
</html>
