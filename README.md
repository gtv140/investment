<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <title>MintCrest Gold | USA Secure Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; -webkit-user-select: text; user-select: text; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .active-page { display: block !important; }
        .page { display: none; animation: fadeIn 0.5s ease; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 class="text-4xl font-black italic tracking-tighter mb-2">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-[7px] text-gray-500 uppercase tracking-[0.4em] mb-12 font-bold italic">USA Private Equity Node</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600">
                <input type="text" id="user-name" placeholder="Enter Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 uppercase">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest">Connect Node</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-6">
                <div onclick="adminTap()">
                    <h3 id="display-username" class="text-lg font-black text-blue-400 uppercase italic leading-none">User</h3>
                    <span class="text-[7px] font-black text-gray-500 uppercase tracking-widest italic">Identity Verified 🇺🇸</span>
                </div>
                <div class="text-[8px] font-bold text-white/40 border border-white/10 px-3 py-1 rounded-full uppercase">Secure HQ</div>
            </div>

            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-2xl">
                <p class="text-[9px] text-blue-400 font-black uppercase mb-1">Total Assets</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="yield-status" class="mt-4 text-[10px] font-black text-yellow-500 italic uppercase">System Online...</div>
            </div>

            <div class="glass p-6 rounded-[2.5rem] border-t-2 border-green-500 mb-6">
                <p class="text-[9px] text-gray-400 font-bold uppercase mb-4">Join Community for Profits & Promos.</p>
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3?mode=hqctcla" class="block w-full bg-green-600/10 text-green-500 border border-green-500/20 py-4 rounded-2xl text-center text-[10px] font-black uppercase tracking-widest">WhatsApp Group</a>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] font-black text-[9px] uppercase border border-white/5">Deposit</button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] font-black text-[9px] uppercase text-red-500 border border-white/5">Withdraw</button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-lg">Asset Fleets</h2>
            <div id="plans-list" class="space-y-4 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400">Ledger History</h2>
            <div id="user-history" class="space-y-2"></div>
        </div>

        <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
            <div class="flex justify-between items-center mb-10">
                <h2 class="text-xl font-black text-blue-500 uppercase">Master Console</h2>
                <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
            </div>
            <div id="adm-requests" class="space-y-4"></div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[3.5rem]">
        <button onclick="changePage('home')" id="n-home" class="active-tab px-4 text-xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="px-4 text-xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="px-4 text-xl">📜</button>
        <button onclick="changePage('more')" id="n-more" class="px-4 text-xl">🏢</button>
    </nav>

    <script>
        // Sweetie, yahan apna Firebase Config lazmi paste karna hai!
        const firebaseConfig = { 
            apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", 
            authDomain: "investment-84f4e.firebaseapp.com", 
            projectId: "investment-84f4e", 
            storageBucket: "investment-84f4e.firebasestorage.app", 
            messagingSenderId: "975293889308", 
            appId: "1:975293889308:web:6d034a99cc966c75ff58d9" 
        };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();

        let user = null;
        let tapCount = 0;

        // Plans starting from 200 PKR
        const plans = [];
        for(let i=1; i<=20; i++){ 
            plans.push({ n: `Node ${i}`, p: 200 + (i-1)*500, r: (2.5 + i*0.2).toFixed(1), d: i > 10 ? 45 : 30 }); 
        }

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase();
            if(!name) return alert("Enter Name");
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name);
            const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, activeTier: 0, lastReqTime: Date.now() });
            
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-username').innerText = name;
            
            startSync(name);
            renderPlans();
        }

        function startSync(name) {
            // Real-time Balance Sync (Data kabhi nahi jayega)
            db.collection("users").doc(name).onSnapshot(doc => {
                if(doc.exists) { 
                    user = doc.data(); 
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                }
            });
            // Real-time History Sync
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history');
                list.innerHTML = '';
                snap.forEach(doc => {
                    const d = doc.data();
                    list.innerHTML += `<div class="glass p-5 rounded-3xl mb-2 flex justify-between uppercase text-[9px] font-black border-l-4 border-blue-500">
                        <div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleTimeString()}</span></div>
                        <div class="text-right">₨ ${d.amount}<br><span class="${d.status=='approved'?'text-green-500':'text-yellow-500'}">${d.status}</span></div>
                    </div>`;
                });
            });
        }

        function renderPlans() {
            const container = document.getElementById('plans-list');
            container.innerHTML = '';
            plans.forEach((p, i) => {
                container.innerHTML += `<div class="glass p-6 rounded-[2.5rem]">
                    <div class="flex justify-between mb-2"><h4 class="font-black text-[10px] text-blue-400 uppercase">${p.n}</h4><span class="text-[10px] font-black italic">₨ ${p.p.toLocaleString()}</span></div>
                    <div class="flex justify-between text-[8px] font-bold opacity-50 uppercase mb-4"><span>Daily: ${p.r}%</span><span>Days: ${p.d}</span></div>
                    <button onclick="buyPlan(${i})" class="w-full py-4 rounded-2xl bg-white/5 font-black text-[9px] uppercase border border-white/10">Deploy Node</button>
                </div>`;
            });
        }

        async function buyPlan(i) {
            const p = plans[i];
            if(user.balance < p.p) return alert("Paisa kam hai sweetie!");
            if(confirm(`Buy ${p.n}?`)) {
                await db.collection("users").doc(user.name).update({ 
                    balance: user.balance - p.p, 
                    activeTier: p.p, 
                    tierROI: p.r,
                    lastReqTime: Date.now() 
                });
                alert("Node Active!");
            }
        }

        function adminTap() {
            tapCount++;
            if(tapCount >= 4) {
                if(prompt("Key:") === "mint786") {
                    document.getElementById('admin-panel').classList.remove('hidden');
                    syncAdmin();
                }
                tapCount = 0;
            }
        }

        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
        }

        // Automatic page recovery
        window.onload = () => {
            const saved = localStorage.getItem('mc_user');
            if(saved) { document.getElementById('user-name').value = saved; login(); }
        }
    </script>
</body>
</html>
