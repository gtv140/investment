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
        
        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            background: #F8FAFC; /* Light Soft Gray-Blue */
            color: #1E293B; 
            overflow-x: hidden; 
        }

        /* Animated Gradient Background */
        .vibrant-bg {
            background: linear-gradient(-45deg, #EEF2FF, #FAF5FF, #F0FDF4, #FFFBEB);
            background-size: 400% 400%;
            animation: gradientBG 15s ease infinite;
        }

        @keyframes gradientBG {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        /* Glassmorphism Light Mode */
        .glass-light { 
            background: rgba(255, 255, 255, 0.7); 
            backdrop-filter: blur(15px); 
            border: 1px solid rgba(255, 255, 255, 0.5); 
            box-shadow: 0 10px 30px -10px rgba(0,0,0,0.05);
        }

        /* Premium Colorful Cards */
        .card-blue { background: linear-gradient(135deg, #3B82F6 0%, #2563EB 100%); color: white; }
        .card-gold { background: linear-gradient(135deg, #F59E0B 0%, #D97706 100%); color: white; }
        
        /* Navigation Styles */
        .active-tab { color: #3B82F6; transform: translateY(-3px); font-weight: 800; }
        .nav-indicator { height: 3px; width: 12px; background: #3B82F6; border-radius: 10px; margin-top: 4px; }
        
        /* Animations */
        .page { display: none; animation: slideUp 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
        .active-page { display: block; }
        @keyframes slideUp { 
            from { opacity: 0; transform: translateY(20px); filter: blur(5px); } 
            to { opacity: 1; transform: translateY(0); filter: blur(0); } 
        }

        /* Floating Animation for balance */
        .floating { animation: floating 3s ease-in-out infinite; }
        @keyframes floating {
            0%, 100% { transform: translateY(0px); }
            50% { transform: translateY(-5px); }
        }
    </style>
</head>
<body class="h-screen flex flex-col vibrant-bg">

    <header class="p-6 flex justify-between items-center sticky top-0 z-[500] bg-white/60 backdrop-blur-md border-b border-gray-200/50">
        <h1 class="text-2xl font-black italic tracking-tighter uppercase text-slate-800">MINT<span class="text-blue-600">CREST</span></h1>
        <div onclick="adminTap()" class="bg-blue-100 p-2.5 rounded-2xl border border-blue-200 transition-all active:scale-90">
            <i class="fa-solid fa-fingerprint text-blue-600 text-lg"></i>
        </div>
    </header>

    <main id="app-ui" class="flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="card-blue p-8 rounded-[2.5rem] mb-6 shadow-2xl shadow-blue-200 relative overflow-hidden floating">
                <p class="text-[10px] opacity-80 font-black mb-1 uppercase tracking-widest">Active Assets Pool</p>
                <h2 class="text-5xl font-black tracking-tighter mb-6" id="v-bal">₨ 0</h2>
                
                <div class="flex items-center gap-4 border-t border-white/20 pt-6">
                    <div class="flex-1">
                        <p class="text-[8px] opacity-70 font-black uppercase">Net Profit</p>
                        <p id="v-profit" class="text-lg font-black">₨ 0</p>
                    </div>
                    <div class="text-right">
                        <p class="text-[8px] opacity-70 font-black uppercase tracking-tighter">Current Fleet</p>
                        <p id="tier-tag" class="text-[10px] font-black uppercase italic">Scanning...</p>
                    </div>
                </div>
                <div id="countdown-display" class="mt-4 text-center py-2 bg-black/10 rounded-xl text-[9px] font-black uppercase">Syncing Protocol...</div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass-light p-6 rounded-[2rem] flex flex-col items-center gap-3 active:scale-95 transition-all border-b-4 border-blue-500">
                    <div class="w-12 h-12 bg-blue-100 rounded-2xl flex items-center justify-center">
                        <i class="fa-solid fa-plus text-xl text-blue-600"></i>
                    </div>
                    <span class="text-[9px] font-black uppercase tracking-widest text-slate-600">Add Capital</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass-light p-6 rounded-[2rem] flex flex-col items-center gap-3 active:scale-95 transition-all border-b-4 border-rose-500">
                    <div class="w-12 h-12 bg-rose-100 rounded-2xl flex items-center justify-center">
                        <i class="fa-solid fa-arrow-up-right-from-square text-xl text-rose-600"></i>
                    </div>
                    <span class="text-[9px] font-black uppercase tracking-widest text-slate-600">Payout</span>
                </button>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <h3 class="font-black text-slate-800 mb-6 uppercase text-center italic tracking-widest">Global Payout Terminals</h3>
            <div class="space-y-4 mb-8">
                <div class="glass-light p-6 rounded-[2.2rem] flex justify-between items-center border-l-8 border-yellow-500 shadow-sm">
                    <div>
                        <p class="text-[8px] font-black text-gray-400 uppercase mb-0.5">SadaPay / JazzCash</p>
                        <p class="font-black text-slate-800 text-sm tracking-widest">03705519562</p>
                    </div>
                    <button onclick="copyToClipboard('03705519562')" class="bg-yellow-100 p-3.5 rounded-2xl active:bg-yellow-200 transition-all">
                        <i class="fa-solid fa-clone text-yellow-600"></i>
                    </button>
                </div>
                <div class="glass-light p-6 rounded-[2.2rem] flex justify-between items-center border-l-8 border-green-500 shadow-sm">
                    <div>
                        <p class="text-[8px] font-black text-gray-400 uppercase mb-0.5">EasyPaisa Global</p>
                        <p class="font-black text-slate-800 text-sm tracking-widest">03379827882</p>
                    </div>
                    <button onclick="copyToClipboard('03379827882')" class="bg-green-100 p-3.5 rounded-2xl active:bg-green-200 transition-all">
                        <i class="fa-solid fa-clone text-green-600"></i>
                    </button>
                </div>
            </div>
            
            <div class="glass-light p-8 rounded-[2.5rem] space-y-4">
                <input type="number" id="dep-amount" placeholder="Input Amount (PKR)" class="w-full bg-slate-50 p-5 rounded-2xl text-center font-bold outline-none border border-slate-200 focus:border-blue-500 transition-all text-slate-800">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-slate-50 p-5 rounded-2xl text-center font-bold uppercase outline-none border border-slate-200 focus:border-blue-500 transition-all text-slate-800">
                <label class="block w-full bg-slate-50 p-5 rounded-2xl border-2 border-dashed border-slate-200 text-center cursor-pointer hover:bg-slate-100 transition-all">
                    <span id="file-label" class="text-[10px] font-black text-slate-400 uppercase italic">📸 Upload Receipt Proof</span>
                    <input type="file" id="dep-proof" accept="image/*" class="hidden" onchange="document.getElementById('file-label').innerText = 'Receipt Secured ✅'; document.getElementById('file-label').classList.add('text-green-600')">
                </label>
                <button id="dep-btn" onclick="submitDeposit()" class="w-full bg-slate-800 py-6 rounded-2xl font-black text-[11px] text-white uppercase shadow-xl shadow-slate-200 active:scale-95 transition-all">Confirm Funding</button>
            </div>
        </div>

    </main>

    <nav class="bg-white/80 backdrop-blur-xl border-t border-gray-100 p-6 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[3.5rem] shadow-[0_-10px_40px_rgba(0,0,0,0.03)]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center transition-all">
            <i class="fa-solid fa-house-chimney text-xl"></i>
            <span class="text-[9px] font-black uppercase mt-1">Vault</span>
        </button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center transition-all opacity-40">
            <i class="fa-solid fa-chart-pie text-xl"></i>
            <span class="text-[9px] font-black uppercase mt-1">Fleet</span>
        </button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center transition-all opacity-40">
            <i class="fa-solid fa-clock-rotate-left text-xl"></i>
            <span class="text-[9px] font-black uppercase mt-1">History</span>
        </button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center transition-all opacity-40">
            <i class="fa-solid fa-circle-nodes text-xl"></i>
            <span class="text-[9px] font-black uppercase mt-1">Firm</span>
        </button>
    </nav>

    </body>
</html>
