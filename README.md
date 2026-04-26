<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Elite Asset Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #020617; color: white; overflow-x: hidden; }
        
        /* Premium Glassmorphism */
        .glass { background: rgba(30, 41, 59, 0.5); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.05); }
        .gold-card { background: linear-gradient(135deg, #1e293b 0%, #0f172a 100%); border-left: 6px solid #fbbf24; }
        .blue-card { background: linear-gradient(135deg, #1e293b 0%, #0f172a 100%); border-left: 6px solid #3b82f6; }
        
        /* Navigation Styles */
        .active-tab { color: #3b82f6; transform: translateY(-5px); }
        .nav-indicator { height: 4px; width: 20px; background: #3b82f6; border-radius: 10px; margin-top: 4px; display: none; }
        .active-tab .nav-indicator { display: block; }
        
        /* Animations */
        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; filter: blur(10px); } to { opacity: 1; filter: blur(0); } }
        
        .copy-btn { cursor: pointer; transition: 0.2s; }
        .copy-btn:active { transform: scale(0.9); opacity: 0.7; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <header class="p-6 flex justify-between items-center sticky top-0 z-[500] bg-[#020617]/80 backdrop-blur-lg border-b border-white/5">
        <h1 class="text-2xl font-black italic tracking-tighter uppercase">MINT<span class="text-blue-500">CREST</span></h1>
        <div onclick="adminTap()" class="bg-blue-500/10 p-2 rounded-full border border-blue-500/20">
            <i class="fa-solid fa-shield-halved text-blue-500 text-sm"></i>
        </div>
    </header>

    <main id="app-ui" class="flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="blue-card glass p-8 rounded-[2.5rem] mb-6 shadow-2xl relative overflow-hidden">
                <div class="absolute top-[-20%] right-[-10%] w-40 h-40 bg-blue-500/10 rounded-full blur-3xl"></div>
                <p class="text-[10px] text-blue-400 font-black mb-1 uppercase tracking-widest">Global Liquid Balance</p>
                <h2 class="text-5xl font-black tracking-tighter mb-6" id="v-bal">₨ 0</h2>
                
                <div class="flex items-center gap-4 border-t border-white/5 pt-6">
                    <div class="flex-1">
                        <p class="text-[8px] text-gray-500 font-black uppercase">Daily Yield</p>
                        <p id="v-profit" class="text-lg font-black text-green-400">₨ 0</p>
                    </div>
                    <div class="text-right">
                        <p class="text-[8px] text-gray-500 font-black uppercase tracking-tighter">Fleet Status</p>
                        <p id="tier-tag" class="text-[10px] font-black text-blue-400 uppercase italic">Idle</p>
                    </div>
                </div>
                <div id="countdown-display" class="mt-4 text-center py-2 bg-white/5 rounded-xl text-[9px] font-black text-yellow-500 uppercase">Syncing...</div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-6 rounded-[2rem] flex flex-col items-center gap-3 active:scale-95 transition-transform border-b-4 border-blue-500">
                    <i class="fa-solid fa-circle-plus text-2xl text-blue-500"></i>
                    <span class="text-[9px] font-black uppercase tracking-widest">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-6 rounded-[2rem] flex flex-col items-center gap-3 active:scale-95 transition-transform border-b-4 border-red-500">
                    <i class="fa-solid fa-money-bill-transfer text-2xl text-red-500"></i>
                    <span class="text-[9px] font-black uppercase tracking-widest">Withdraw</span>
                </button>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <h3 class="font-black text-blue-500 mb-6 uppercase text-center italic tracking-widest">Funding Terminal</h3>
            <div class="space-y-4 mb-8">
                <div class="glass p-6 rounded-[2rem] flex justify-between items-center border-l-4 border-blue-500">
                    <div>
                        <p class="text-[7px] font-black text-gray-500 uppercase mb-1">JazzCash / SadaPay</p>
                        <p class="font-black text-sm tracking-widest">03705519562</p>
                    </div>
                    <button onclick="copyToClipboard('03705519562')" class="bg-blue-500/10 p-3 rounded-xl copy-btn"><i class="fa-solid fa-copy text-blue-500"></i></button>
                </div>
                <div class="glass p-6 rounded-[2rem] flex justify-between items-center border-l-4 border-green-500">
                    <div>
                        <p class="text-[7px] font-black text-gray-500 uppercase mb-1">EasyPaisa Official</p>
                        <p class="font-black text-sm tracking-widest">03379827882</p>
                    </div>
                    <button onclick="copyToClipboard('03379827882')" class="bg-green-500/10 p-3 rounded-xl copy-btn"><i class="fa-solid fa-copy text-green-500"></i></button>
                </div>
            </div>
            
            <div class="glass p-8 rounded-[2.5rem] space-y-4">
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl text-center font-bold outline-none border border-white/5 focus:border-blue-500">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl text-center font-bold uppercase outline-none border border-white/5 focus:border-blue-500">
                <label class="block w-full bg-white/5 p-5 rounded-2xl border border-dashed border-white/10 text-center cursor-pointer">
                    <span id="file-label" class="text-[9px] font-bold opacity-40 uppercase italic">📸 Upload Screenshot</span>
                    <input type="file" id="dep-proof" accept="image/*" class="hidden" onchange="document.getElementById('file-label').innerText = 'Proof Attached ✅'">
                </label>
                <button id="dep-btn" onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-xl active:scale-95 transition-all">Verify Transaction</button>
            </div>
        </div>

        </main>

    <nav class="glass border-t border-white/5 p-6 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[3rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center active-tab">
            <i class="fa-solid fa-vault text-xl"></i>
            <span class="text-[8px] font-black uppercase mt-1">Vault</span>
            <div class="nav-indicator"></div>
        </button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center">
            <i class="fa-solid fa-chart-line text-xl"></i>
            <span class="text-[8px] font-black uppercase mt-1">Fleet</span>
            <div class="nav-indicator"></div>
        </button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center">
            <i class="fa-solid fa-receipt text-xl"></i>
            <span class="text-[8px] font-black uppercase mt-1">Ledger</span>
            <div class="nav-indicator"></div>
        </button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center">
            <i class="fa-solid fa-bars-staggered text-xl"></i>
            <span class="text-[8px] font-black uppercase mt-1">Firm</span>
            <div class="nav-indicator"></div>
        </button>
    </nav>

    <script>
        // [Existing Firebase Logic]
        function copyToClipboard(text) {
            const el = document.createElement('textarea');
            el.value = text;
            document.body.appendChild(el);
            el.select();
            document.execCommand('copy');
            document.body.removeChild(el);
            alert("Number Copied: " + text + " sweetie! 😘");
        }
        
        // Ensure to keep your existing startSync, login, and changePage functions.
    </script>
</body>
</html>
