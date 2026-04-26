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
        
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #F8FAFC; color: #1E293B; overflow-x: hidden; }

        /* Smooth Background Animation */
        .vibrant-bg {
            background: linear-gradient(-45deg, #EEF2FF, #FAF5FF, #F0FDF4, #FFFBEB);
            background-size: 400% 400%;
            animation: gradientBG 12s ease infinite;
        }
        @keyframes gradientBG { 0% { background-position: 0% 50%; } 50% { background-position: 100% 50%; } 100% { background-position: 0% 50%; } }

        .glass-light { background: rgba(255, 255, 255, 0.7); backdrop-filter: blur(15px); border: 1px solid rgba(255, 255, 255, 0.5); box-shadow: 0 10px 30px -10px rgba(0,0,0,0.03); }
        .card-blue { background: linear-gradient(135deg, #3B82F6 0%, #2563EB 100%); color: white; box-shadow: 0 20px 40px -10px rgba(59, 130, 246, 0.3); }
        .active-tab { color: #3B82F6; font-weight: 800; }
        .page { display: none; animation: slideUp 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); filter: blur(5px); } to { opacity: 1; transform: translateY(0); filter: blur(0); } }
        .badge-pending { background: #FEF3C7; color: #92400E; padding: 2px 6px; border-radius: 6px; font-size: 8px; }
        .badge-approved { background: #DCFCE7; color: #166534; padding: 2px 6px; border-radius: 6px; font-size: 8px; }
        .badge-rejected { background: #FEE2E2; color: #991B1B; padding: 2px 6px; border-radius: 6px; font-size: 8px; }
    </style>
</head>
<body class="h-screen flex flex-col vibrant-bg">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-white flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-4xl font-black italic tracking-tighter mb-2 uppercase text-slate-800">MINT<span class="text-blue-600">CREST</span></h1>
            <p class="text-slate-400 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold italic">Official Digital Asset Vault</p>
            <div class="glass-light p-10 rounded-[3rem] border-t-4 border-blue-600">
                <input type="text" id="user-name" placeholder="Legal Full Name" class="w-full bg-slate-50 p-5 rounded-2xl border border-slate-100 outline-none text-center font-bold mb-5 focus:border-blue-500 transition-all text-slate-800">
                <button onclick="login()" class="w-full bg-slate-900 text-white py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 transition-all shadow-xl">Secure Access</button>
            </div>
            <p class="mt-8 text-[7px] text-slate-400 uppercase font-bold tracking-widest">© 2026 MintCrest Gold Enterprise</p>
        </div>
    </section>

    <header class="p-6 flex justify-between items-center sticky top-0 z-[500] bg-white/60 backdrop-blur-md border-b border-slate-200/50">
        <h1 class="text-xl font-black italic tracking-tighter uppercase text-slate-800">MINT<span class="text-blue-600">CREST</span></h1>
        <div onclick="adminTap()" class="bg-blue-50 p-2.5 rounded-xl border border-blue-100"><i class="fa-solid fa-fingerprint text-blue-600"></i></div>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="card-blue p-8 rounded-[2.5rem] mb-6 relative overflow-hidden">
                <p class="text-[10px] opacity-80 font-black mb-1 uppercase tracking-widest">Capital Holdings</p>
                <h2 class="text-5xl font-black tracking-tighter mb-6" id="v-bal">₨ 0</h2>
                <div class="flex items-center justify-between border-t border-white/20 pt-6">
                    <div><p class="text-[8px] opacity-70 font-black uppercase">Net Profit</p><p id="v-profit" class="text-lg font-black">₨ 0</p></div>
                    <div class="text-right"><p class="text-[8px] opacity-70 font-black uppercase">Fleet</p><p id="tier-tag" class="text-[10px] font-black italic">SCANNING...</p></div>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass-light p-6 rounded-[2rem] flex flex-col items-center gap-2 border-b-4 border-blue-500"><i class="fa-solid fa-plus text-blue-600"></i><span class="text-[9px] font-black uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass-light p-6 rounded-[2rem] flex flex-col items-center gap-2 border-b-4 border-rose-500"><i class="fa-solid fa-paper-plane text-rose-600"></i><span class="text-[9px] font-black uppercase">Payout</span></button>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="space-y-3 mb-8">
                <div class="glass-light p-5 rounded-3xl flex justify-between items-center border-l-4 border-yellow-500"><div><p class="text-[7px] font-black opacity-40 uppercase">JazzCash/SadaPay</p><p class="font-black text-sm">03705519562</p></div><button onclick="copyToClipboard('03705519562')" class="bg-yellow-100 p-3 rounded-xl"><i class="fa-solid fa-copy text-yellow-600"></i></button></div>
                <div class="glass-light p-5 rounded-3xl flex justify-between items-center border-l-4 border-green-500"><div><p class="text-[7px] font-black opacity-40 uppercase">EasyPaisa</p><p class="font-black text-sm">03379827882</p></div><button onclick="copyToClipboard('03379827882')" class="bg-green-100 p-3 rounded-xl"><i class="fa-solid fa-copy text-green-600"></i></button></div>
            </div>
            <div class="glass-light p-8 rounded-[2.5rem] space-y-4 text-center">
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-slate-50 p-4 rounded-xl border border-slate-100 text-center font-bold">
                <input type="text" id="dep-trx" placeholder="Transaction ID" class="w-full bg-slate-50 p-4 rounded-xl border border-slate-100 text-center font-bold uppercase">
                <button id="dep-btn" onclick="submitDeposit()" class="w-full bg-slate-900 text-white py-5 rounded-xl font-black text-[10px] uppercase">Verify Funding</button>
            </div>
        </div>

        <div id="p-invest" class="page p-6"><div id="plans-list" class="space-y-3"></div></div>
        <div id="p-activity" class="page p-6"><div id="user-history" class="space-y-2"></div></div>
    </main>

    <nav id="bottom-nav" class="hidden bg-white/90 backdrop-blur-xl border-t border-slate-100 p-6 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[3rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center active-tab"><i class="fa-solid fa-house"></i><span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center opacity-40"><i class="fa-solid fa-chart-simple"></i><span class="text-[8px] font-black uppercase mt-1">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center opacity-40"><i class="fa-solid fa-list-ul"></i><span class="text-[8px] font-black uppercase mt-1">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center opacity-40"><i class="fa-solid fa-ellipsis"></i><span class="text-[8px] font-black uppercase mt-1">Firm</span></button>
    </nav>

    <script>
        // FIREBASE CONFIG (SAME AS YOURS)
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const d = await ref.get();
            if(!d.exists) await ref.set({ name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Idle" });
            startSync(name); document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + user.balance.toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + user.profit.toLocaleString(); document.getElementById('tier-tag').innerText = user.tierName; } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(d => { const data = d.data(); list.innerHTML += `<div class="glass-light p-4 rounded-2xl flex justify-between items-center mb-2 font-black text-[9px] uppercase"><div>${data.type}</div><div class="text-right">₨ ${data.amount}<br><span class="badge-${data.status}">${data.status}</span></div></div>`; });
            });
        }

        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); 
            document.querySelectorAll('nav button').forEach(b=>b.classList.add('opacity-40')); 
            document.getElementById('p-'+p).classList.add('active-page');
            document.getElementById('n-'+p).classList.remove('opacity-40');
            document.getElementById('n-'+p).classList.add('active-tab');
        }

        function copyToClipboard(t) { navigator.clipboard.writeText(t); alert("Copied sweetie! 😘"); }
        function adminTap() { tapCount++; if(tapCount >= 5) { alert("Admin Access Enabled"); tapCount=0; } }
    </script>
</body>
</html>
