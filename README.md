<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <title>MintCrest Gold | Global Master Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; user-select: none; -webkit-user-select: none; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .page { display: none; }
        .active-page { display: block !important; animation: slideUp 0.4s ease-out; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .ticker { white-space: nowrap; animation: marquee 25s linear infinite; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
    </style>
</head>
<body class="h-screen flex flex-col overflow-hidden">

    <div class="bg-blue-600/10 border-b border-blue-500/20 py-2 overflow-hidden z-[500]">
        <div class="ticker text-[8px] font-black uppercase tracking-[0.2em] text-blue-400">
            NY GOLD: $2,184.20 (+0.85%) • SECURE USA NODE: CONNECTED • SYSTEM AUDIT: 100% SECURE • NO TAX ON FIRST DEPOSIT • 
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 class="text-4xl font-black italic mb-2 tracking-tighter">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-[7px] text-gray-500 uppercase tracking-[0.5em] mb-12 italic">USA Strategic Asset Management</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="ENTER FULL NAME" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 text-center font-bold mb-5 uppercase outline-none focus:border-blue-500 transition-all">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-lg active:scale-95 transition-all">Establish Session</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-end mb-6">
                <div>
                    <h3 id="display-username" class="text-xl font-black text-blue-400 uppercase italic leading-none">User</h3>
                    <span id="user-rank" class="text-[7px] font-black text-yellow-500 uppercase tracking-widest border border-yellow-500/30 px-2 py-0.5 rounded-full">BRONZE NODE</span>
                </div>
            </div>

            <div class="glass p-10 rounded-[3.5rem] mb-6 border-l-8 border-blue-600 shadow-2xl relative overflow-hidden" onclick="secretAdminTap()">
                <div class="absolute top-0 right-0 p-4 opacity-10 text-4xl italic font-black">USA</div>
                <p class="text-[9px] text-blue-400 font-black uppercase mb-1">Total Trading Capital</p>
                <h2 class="text-5xl font-black tracking-tighter flex items-baseline">
                    <span class="text-xl mr-2">₨</span><span id="v-bal">0</span>
                </h2>
                <div class="mt-4 flex justify-between items-center">
                    <div class="text-[8px] font-bold text-green-500 uppercase italic">Live Yield Flowing...</div>
                    <button onclick="openSpin()" class="bg-yellow-500 text-black px-4 py-1.5 rounded-full text-[8px] font-black uppercase shadow-lg animate-pulse">Daily Gift 🎁</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] font-black text-[9px] uppercase active:scale-95 transition-all">Capital Top-up</button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] font-black text-[9px] uppercase text-red-500 active:scale-95 transition-all">Profit Payout</button>
            </div>

            <div class="glass p-6 rounded-[2.5rem] border-t-2 border-green-500 mb-6 flex items-center justify-between">
                <div class="text-left">
                    <p class="text-[9px] font-black uppercase text-white">Official Support</p>
                    <p class="text-[7px] text-gray-400 font-bold uppercase italic">Join for daily signals</p>
                </div>
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="bg-green-600 p-3 rounded-2xl text-[10px] font-black uppercase tracking-widest shadow-xl">Join Group</a>
            </div>
        </div>

        <div id="p-invest" class="page p-6"><div id="plans-list" class="space-y-4 pb-10"></div></div>
        <div id="p-activity" class="page p-6"><div id="user-history" class="space-y-2 pb-10"></div></div>

        <div id="p-more" class="page p-6 space-y-4">
            <div class="glass p-6 rounded-[2.5rem] border-b-4 border-blue-500">
                <p class="text-[8px] font-black text-blue-400 uppercase mb-2">My Agency Code</p>
                <div id="my-ref-display" class="text-2xl font-black tracking-widest text-white mb-2 italic">MC-GOLD</div>
                <div class="flex justify-between text-[7px] font-bold text-gray-500 uppercase"><span>Team L1: 5%</span><span>L2: 2%</span></div>
            </div>

            <div class="glass p-6 rounded-[2.5rem]">
                <h3 class="text-white font-black text-[10px] uppercase mb-4 tracking-widest">Submit Support Ticket</h3>
                <textarea id="ticket-msg" placeholder="REASON FOR SUPPORT..." class="w-full bg-white/5 p-4 rounded-xl text-[10px] outline-none mb-3 h-20 uppercase font-bold border border-white/10"></textarea>
                <button onclick="sendTicket()" class="w-full bg-white/10 py-3 rounded-xl text-[9px] font-black uppercase">Establish Ticket</button>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-[10px] font-black text-red-500 uppercase italic">Log Out Session</button>
        </div>

        <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
            <div class="flex justify-between items-center mb-10"><h2 class="text-xl font-black text-blue-500 italic">MASTER CONSOLE</h2><button onclick="closeAdmin()" class="bg-red-500 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button></div>
            <div id="adm-requests" class="space-y-4 pb-20"></div>
        </div>
    </main>

    <div id="spin-modal" class="fixed inset-0 bg-black/95 z-[6000] hidden flex items-center justify-center p-8">
        <div class="glass p-10 rounded-[4rem] text-center w-full max-w-sm border-2 border-yellow-500/20">
            <h2 class="text-2xl font-black mb-10 italic">LUCKY REWARD</h2>
            <div id="wheel" class="w-48 h-48 rounded-full border-8 border-yellow-500 mx-auto mb-10 flex items-center justify-center text-5xl">🎰</div>
            <button id="spin-btn" onclick="startSpin()" class="bg-yellow-500 text-black w-full py-5 rounded-2xl font-black uppercase italic tracking-widest">Spin Node</button>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[3.5rem]">
        <button onclick="changePage('home')" id="n-home" class="active-tab px-4 text-2xl transition-all">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="px-4 text-2xl transition-all">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="px-4 text-2xl transition-all">📜</button>
        <button onclick="changePage('more')" id="n-more" class="px-4 text-2xl transition-all">🏢</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null, tapCount = 0;
        const plans = []; for(let i=1; i<=20; i++){ plans.push({ n: `Fleet Node ${i}`, p: 200 + (i-1)*500, r: (2.5 + i*0.2).toFixed(1) }); }

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, activeTier: 0, refCode: "MC-"+Math.floor(1000+Math.random()*9000), rank: 'BRONZE NODE', lastReqTime: Date.now() });
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-username').innerText = name;
            startSync(name); renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = (user.balance || 0).toLocaleString(); document.getElementById('user-rank').innerText = user.rank; document.getElementById('my-ref-display').innerText = user.refCode; } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); if(!list) return; list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-3xl mb-2 flex justify-between text-[9px] font-black border-l-4 border-blue-500 uppercase"><div>${d.type}</div><div class="text-right">₨ ${d.amount}<br><span class="text-blue-400">${d.status}</span></div></div>`; });
            });
        }

        function renderPlans() {
            const container = document.getElementById('plans-list'); container.innerHTML = '';
            plans.forEach((p, i) => { container.innerHTML += `<div class="glass p-6 rounded-[2.5rem] flex justify-between items-center"><div class="text-[10px] font-black uppercase">FLEET ${i+1}<br><span class="text-blue-400">₨ ${p.p}</span></div><button onclick="buyPlan(${i})" class="bg-blue-600 px-6 py-3 rounded-2xl text-[9px] font-black uppercase shadow-lg">Deploy</button></div>`; });
        }

        async function buyPlan(i) {
            const p = plans[i]; if(user.balance < p.p) return alert("Insufficient Capital!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - p.p, activeTier: p.p, tierROI: p.r, lastReqTime: Date.now() }); alert("Node Active!");
        }

        function secretAdminTap() {
            tapCount++;
            if(tapCount >= 5) {
                tapCount = 0;
                if(prompt("Enter Master Access Key:") === "mint786") {
                    document.getElementById('admin-panel').classList.remove('hidden');
                    syncAdmin();
                }
            }
            setTimeout(() => tapCount = 0, 3000);
        }

        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('n-'+p)?.classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }

        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-3xl border-l-4 border-blue-500 mb-2 font-black text-[9px] uppercase">${d.user} | ${d.type}<br><span class="opacity-40">${d.message || d.tid || d.account || ''}</span><br><input type="number" id="amt-${doc.id}" placeholder="Final Amt" class="bg-white/10 p-2 mt-2 outline-none rounded w-full border border-white/5"><div class="flex gap-2 mt-2"><button onclick="approve('${doc.id}', '${d.user}', '${d.type}')" class="bg-blue-600 flex-1 p-2 rounded">APPROVE</button><button onclick="db.collection('requests').doc('${doc.id}').update({status:'rejected'})" class="bg-red-600 flex-1 p-2 rounded">REJECT</button></div></div>`; });
            });
        }

        async function approve(id, userName, type) {
            const val = parseFloat(document.getElementById('amt-'+id).value || 0);
            const uRef = db.collection("users").doc(userName); const uDoc = await uRef.get();
            if(type !== 'Withdraw' && type !== 'Support Ticket') await uRef.update({ balance: (uDoc.data().balance || 0) + val });
            await db.collection("requests").doc(id).update({ status: 'approved', amount: val }); alert("Done!");
        }

        window.onload = () => { if(localStorage.getItem('mc_user')) login(); };
    </script>
</body>
</html>
