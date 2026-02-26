<html lang="en">
<head>
    <meta charset="UTF-8">
    <script src="https://cdn.tailwindcss.com"></script>
    <title>WealthWise | Smart Investment</title>
</head>
<body class="bg-slate-900 text-white font-sans">

    <header class="p-5 flex justify-between items-center border-b border-gray-700 bg-slate-800">
        <h1 class="text-2xl font-bold text-blue-400">WealthWise</h1>
        <div class="flex items-center gap-4">
            <span class="bg-green-600/20 text-green-400 px-3 py-1 rounded-full text-sm">● Online</span>
            <div class="h-10 w-10 bg-blue-500 rounded-full flex items-center justify-center font-bold">U</div>
        </div>
    </header>

    <main class="p-6 max-w-4xl mx-auto">
        <div class="bg-gradient-to-br from-indigo-600 to-blue-700 p-8 rounded-3xl shadow-2xl mb-8">
            <p class="text-blue-100 opacity-80 mb-1 text-sm uppercase tracking-wider">Available Balance</p>
            <h2 class="text-4xl font-extrabold tracking-tight">₹12,450.00</h2>
            <div class="mt-6 flex gap-3">
                <button onclick="alert('Redirecting to Deposit...')" class="bg-white text-blue-700 px-6 py-2 rounded-xl font-bold hover:bg-gray-100">Deposit</button>
                <button onclick="alert('Withdrawal Request Sent!')" class="bg-blue-500/30 border border-white/20 text-white px-6 py-2 rounded-xl font-bold hover:bg-blue-500/50">Withdraw</button>
            </div>
        </div>

        <h3 class="text-xl font-bold mb-6">Active Investment Plans</h3>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
            <div class="bg-slate-800 p-5 rounded-2xl border border-gray-700 hover:border-blue-500 transition cursor-pointer">
                <p class="text-blue-400 font-bold uppercase text-xs">Starter</p>
                <h4 class="text-lg font-bold">8% Return</h4>
                <p class="text-gray-400 text-sm mb-4">Duration: 7 Days</p>
                <button onclick="confirm('Invest ₹500 now?')" class="w-full py-2 bg-slate-700 rounded-lg hover:bg-blue-600 transition">Invest ₹500</button>
            </div>
            <div class="bg-slate-800 p-5 rounded-2xl border border-gray-700 border-l-4 border-l-yellow-500">
                <p class="text-yellow-500 font-bold uppercase text-xs">Silver</p>
                <h4 class="text-lg font-bold">15% Return</h4>
                <p class="text-gray-400 text-sm mb-4">Duration: 15 Days</p>
                <button onclick="confirm('Invest ₹5,000 now?')" class="w-full py-2 bg-slate-700 rounded-lg hover:bg-blue-600 transition">Invest ₹5k</button>
            </div>
            <div class="bg-slate-800 p-5 rounded-2xl border border-gray-700">
                <p class="text-purple-400 font-bold uppercase text-xs">Gold</p>
                <h4 class="text-lg font-bold">25% Return</h4>
                <p class="text-gray-400 text-sm mb-4">Duration: 30 Days</p>
                <button onclick="confirm('Invest ₹20,000 now?')" class="w-full py-2 bg-slate-700 rounded-lg hover:bg-blue-600 transition">Invest ₹20k</button>
            </div>
        </div>
    </main>

    <footer class="mt-10 text-center text-gray-500 text-xs pb-10">
        &copy; 2026 WealthWise Ltd. | Secured by 256-bit Encryption
    </footer>

</body>
</html>
