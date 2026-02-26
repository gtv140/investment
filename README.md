<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <title>WealthWise Global | Invest & Grow</title>
    <style>
        .glass { background: rgba(255, 255, 255, 0.05); backdrop-filter: blur(10px); }
        .gradient-bg { background: linear-gradient(135deg, #0f172a 0%, #1e1b4b 100%); }
    </style>
</head>
<body class="gradient-bg text-white min-h-screen font-sans">

    <nav class="p-5 flex justify-between items-center border-b border-gray-800 glass sticky top-0 z-50">
        <h1 class="text-2xl font-black tracking-tighter text-blue-400 italic">WEALTHWISE <span class="text-white">GLOBAL</span></h1>
        <select id="currency" class="bg-gray-800 border border-gray-600 text-white rounded-lg px-3 py-1 outline-none" onchange="updateCurrency()">
            <option value="PKR">PKR (₨)</option>
            <option value="USD">USD ($)</option>
            <option value="AED">AED (د.إ)</option>
            <option value="INR">INR (₹)</option>
        </select>
    </nav>

    <div class="max-w-5xl mx-auto p-6 mt-6">
        <div class="glass p-10 rounded-[2rem] border border-white/10 shadow-2xl relative overflow-hidden">
            <div class="absolute -top-10 -right-10 w-40 h-40 bg-blue-500/20 rounded-full blur-3xl"></div>
            <p class="text-gray-400 font-medium mb-1 uppercase tracking-widest text-xs">Global Portfolio Balance</p>
            <h2 class="text-5xl font-bold mb-8" id="display-balance">₨ 50,000.00</h2>
            
            <div class="flex flex-wrap gap-4">
                <button onclick="action('Deposit')" class="bg-blue-600 hover:bg-blue-500 px-8 py-3 rounded-2xl font-bold transition-all transform hover:scale-105 shadow-lg shadow-blue-900/40">Add Funds</button>
                <button onclick="action('Withdraw')" class="bg-white/10 border border-white/20 hover:bg-white/20 px-8 py-3 rounded-2xl font-bold transition-all">Withdraw</button>
            </div>
        </div>

        <h3 class="text-2xl font-bold mt-12 mb-6 border-l-4 border-blue-500 pl-4">Choose Your Investment Plan</h3>
        
        <div class="grid md:grid-cols-3 gap-6">
            <div class="glass p-6 rounded-3xl border border-gray-700 hover:border-blue-500/50 transition-all group">
                <div class="bg-blue-500/10 w-12 h-12 rounded-xl flex items-center justify-center mb-4 group-hover:bg-blue-500 transition-colors">🚀</div>
                <h4 class="text-xl font-bold">Smart Saver</h4>
                <p class="text-green-400 font-bold text-2xl mt-2">10% <span class="text-xs text-gray-400 font-normal">ROI</span></p>
                <p class="text-gray-400 text-sm mt-1 mb-6">Duration: 7 Days</p>
                <button onclick="invest('Smart Saver', 1000)" class="w-full bg-gray-800 py-3 rounded-xl font-semibold border border-gray-700 hover:bg-blue-600 transition">Invest Now</button>
            </div>

            <div class="glass p-6 rounded-3xl border border-blue-500/30 relative">
                <div class="absolute -top-3 right-4 bg-blue-500 text-[10px] font-bold px-3 py-1 rounded-full uppercase tracking-tighter">Most Popular</div>
                <div class="bg-indigo-500/10 w-12 h-12 rounded-xl flex items-center justify-center mb-4">💎</div>
                <h4 class="text-xl font-bold">Wealth Builder</h4>
                <p class="text-green-400 font-bold text-2xl mt-2">25% <span class="text-xs text-gray-400 font-normal">ROI</span></p>
                <p class="text-gray-400 text-sm mt-1 mb-6">Duration: 15 Days</p>
                <button onclick="invest('Wealth Builder', 5000)" class="w-full bg-blue-600 py-3 rounded-xl font-bold shadow-lg shadow-blue-900/20">Invest Now</button>
            </div>

            <div class="glass p-6 rounded-3xl border border-gray-700">
                <div class="bg-purple-500/10 w-12 h-12 rounded-xl flex items-center justify-center mb-4">👑</div>
                <h4 class="text-xl font-bold">Elite Fortune</h4>
                <p class="text-green-400 font-bold text-2xl mt-2">50% <span class="text-xs text-gray-400 font-normal">ROI</span></p>
                <p class="text-gray-400 text-sm mt-1 mb-6">Duration: 30 Days</p>
                <button onclick="invest('Elite Fortune', 20000)" class="w-full bg-gray-800 py-3 rounded-xl font-semibold border border-gray-700 hover:bg-blue-600 transition">Invest Now</button>
            </div>
        </div>
    </div>

    <div class="fixed bottom-0 w-full bg-black/50 py-2 overflow-hidden whitespace-nowrap text-xs text-blue-300">
        <div class="inline-block animate-pulse mx-4">● BTC: $92,450 (+2.4%)</div>
        <div class="inline-block animate-pulse mx-4">● ETH: $3,120 (+1.8%)</div>
        <div class="inline-block animate-pulse mx-4">● PKR/USD: 278.40</div>
        <div class="inline-block animate-pulse mx-4">● GOLD: $2,045 (-0.4%)</div>
    </div>

    <script>
        // Currency Conversion Logic
        const rates = { PKR: 1, USD: 0.0036, AED: 0.013, INR: 0.30 };
        const symbols = { PKR: "₨", USD: "$", AED: "د.إ", INR: "₹" };
        let baseBalance = 50000;

        function updateCurrency() {
            let currency = document.getElementById('currency').value;
            let converted = (baseBalance * rates[currency]).toLocaleString();
            document.getElementById('display-balance').innerText = `${symbols[currency]} ${converted}`;
        }

        function action(type) {
            let curr = document.getElementById('currency').value;
            alert(`${type} feature is coming soon! Please contact Admin for ${curr} transfers.`);
        }

        function invest(plan, amount) {
            let curr = document.getElementById('currency').value;
            let convAmt = (amount * rates[curr]).toFixed(2);
            if(confirm(`Confirm Investment of ${symbols[curr]} ${convAmt} in ${plan}?`)) {
                alert("Order placed! Send payment screenshot to admin.");
            }
        }
    </script>
</body>
</html>
