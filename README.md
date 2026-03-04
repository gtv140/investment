<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold | Ultimate Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(35px); border: 1px solid rgba(255,255,255,0.08); transition: 0.3s; }
        .glass:active { transform: scale(0.98); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        .spin-wheel { transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); }
        .badge-pending { background: rgba(234, 179, 8, 0.1); color: #eab308; padding: 2px 6px; border-radius: 4px; font-size: 8px; font-weight: 800; text-transform: uppercase; }
        .badge-approved { background: rgba(34, 197, 94, 0.1); color: #22c55e; padding: 2px 6px; border-radius: 4px; font-size: 8px; font-weight: 800; text-transform: uppercase; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-5xl font-black italic mb-2 cursor-pointer uppercase tracking-tighter">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.5em] mb-12 font-bold italic">Global Asset Protection</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 text-white uppercase focus:border-blue-500 transition-all">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 shadow-lg shadow-blue-600/20">Initialize Vault</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-6 px-2">
                <div>
                    <p class="text-[8px] text-gray-500 uppercase font-black tracking-widest">Welcome Back</p>
                    <h3 class="text-lg font-black text-white" id="dash-user">GUEST</h3>
                </div>
                <div class="bg-blue-600/10 border border-blue-500/20 px-3 py-1 rounded-full text-[8px] font-black text-blue-400">VERIFIED ✅</div>
            </div>

            <div class="glass p-10 rounded-[3rem] mb-8 border-l-[12px] border-blue-600 shadow-2xl relative overflow-hidden">
                <div class="absolute -right-4 -top-4 opacity-5 text-9xl">💰</div>
                <p class="text-[10px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Total Capital</p>
                <h2 class="text-5xl font-black tracking-tighter mb-6" id="v-bal">₨ 0</h2>
                <div class="grid grid-cols-2 gap-3">
                    <div class="bg-white/5 p-4 rounded-2xl border border-white/5">
                        <p class="text-[7px] text-gray-500 uppercase font-bold">Total Profit</p>
                        <p class="text-sm font-black text-green-400" id="v-profit">₨ 0</p>
                    </div>
                    <div class="bg-white/5 p-4 rounded-2xl border border-white/5">
                        <p class="text-[7px] text-gray-500 uppercase font-bold">Active Fleet</p>
                        <p class="text-[8px] font-black text-blue-400 uppercase truncate" id="tier-tag">None</p>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center border-t-4 border-blue-500/20">📥 <span class="text-[10px] font-black block mt-2 uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center border-t-4 border-red-500/20">📤 <span class="text-[10px] font-black block mt-2 uppercase">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-500 text-xl tracking-tighter">Investment Fleets</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 tracking-widest">Ledger History</h2>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass p-10 rounded-[3rem] text-center border-t-4 border-yellow-500">
                <h3 class="text-yellow-500 font-black text-[12px] uppercase mb-4 italic">Lucky Spin Node</h3>
                <div id="wheel" class="text-6xl mb-6 spin-wheel">🎡</div>
                <button id="spin-btn" onclick="spinNow()" class="bg-yellow-600 px-12 py-4 rounded-2xl font-black text-[10px] uppercase active:scale-95 transition-all shadow-lg shadow-yellow-600/20">Spin Wheel</button>
                <p class="text-[7px] text-gray-500 mt-4 uppercase font-bold tracking-widest">Reward verified by Master Admin</p>
            </div>

            <div class="glass p-8 rounded-[3rem]">
                <h3 class="text-blue-500 font-black text-[10px] uppercase mb-4 italic text-center underline">Help Desk</h3>
                <textarea id="support-msg" placeholder="Describe your issue..." class="w-full bg-white/5 p-4 rounded-xl text-[10px] text-white h-24 mb-4 outline-none border border-white/5 focus:border-blue-500"></textarea>
                <button onclick="sendSupport()" class="w-full bg-blue-600 py-3 rounded-xl font-black text-[9px] uppercase">Submit Ticket</button>
            </div>

            <div class="glass p-6 rounded-[2rem] text-[8px] text-gray-500 uppercase font-bold leading-relaxed border-b-4 border-white/10">
                <p class="text-blue-400 mb-2 font-black italic">Privacy & Security Policy</p>
                1. Withdrawals are processed within 1-24 hours.<br>
                2. One legal identity per account only.<br>
                3. Market risks apply to all trading fleets.<br>
                4. Group: <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="text-green-500 underline">WhatsApp Link</a>
            </div>

            <button onclick="logout()" class="w-full glass p-5 rounded-[2rem] text-red-500 font-black text-[10px] uppercase tracking-widest">Logout Vault</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3.5rem] border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-6 uppercase text-center italic">Asset Funding</h3>
                <div class="space-y-2 mb-8 text-[10px] font-black uppercase">
                    <div class="flex justify-between p-3 bg-white/5 rounded-xl border border-white/5"><span>JazzCash</span><span class="text-blue-400">03705519562</span></div>
                    <div class="flex justify-between p-3 bg-white/5 rounded-xl border border-white/5"><span>Easypaisa</span><span class="text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold text-white outline-none border border-white/5 focus:border-blue-500 transition-all">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold text-white outline-none uppercase border border-white/5 focus:border-blue-500">
                <input type="text" id="dep-promo" placeholder="Promo Code (Optional)" class="w-full bg-blue-600/5 p-4 rounded-xl mb-4 text-center font-black text-[10px] text-blue-400 outline-none uppercase border border-blue-500/20">
                <p class="text-[8px] text-gray-500 mb-2 uppercase font-bold text-center italic">Upload Payment Screenshot</p>
                <input type="file" id="dep-proof" accept="image/*" class="w-full text-[9px] mb-8 text-center text-gray-400">
                <button onclick="submitDeposit()" id="dep-btn" class="w-full bg-blue-600 py-5 rounded-xl font-black text-[11px] uppercase tracking-widest shadow-xl shadow-blue-600/20">Verify Transaction</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 shadow-2xl">
                <h3 class="font-black text-red-500 mb-8 uppercase text-center italic">Asset Withdrawal</h3>
                <input type="number" id="wd-amt" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold text-white outline-none border border-white/5 focus:border-red-500">
                <input type="text" id="wd-acc" placeholder="Account Name & Number" class="w-full bg-white/5 p-4 rounded-xl mb-8 text-center font-bold text-[10px] text-white outline-none border border-white/5">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-xl font-black text-[11px] uppercase tracking-widest shadow-xl shadow-red-600/20">Authorize Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 italic uppercase">Master Control</h2>
            <button onclick="closeAdmin()" class="bg-red-500/20 text-red-500 px-4 py-2 rounded-xl text-[10px] font-black uppercase">Exit</button>
        </div>
        
        <div class="space-y-6">
            <div class="glass p-5 rounded-2xl">
                <p class="text-[8px] font-black uppercase text-gray-500 mb-3">Modify User Balance</p>
                <input type="text" id="adm-u-name" placeholder="User Name" class="w-full bg-white/5 p-3 rounded-xl mb-2 text-[10px] uppercase font-bold outline-none border border-white/5">
                <input type="number" id="adm-u-val" placeholder="Amount (+/-)" class="w-full bg-white/5 p-3 rounded-xl mb-3 text-[10px] font-bold outline-none border border-white/5">
                <button onclick="adminEdit()" class="w-full bg-blue-600 py-3 rounded-xl text-[9px] font-black uppercase">Update Account</button>
            </div>
            
            <div class="grid grid-cols-2 gap-3"><div class="glass p-4 rounded-2xl text-center"><p class="text-[7px] font-black uppercase text-gray-500">Total Users</p><h4 id="adm-count-users" class="text-xl font-black">0</h4></div><div class="glass p-4 rounded-2xl text-center"><p class="text-[7px] font-black uppercase text-yellow-500">Pending</p><h4 id="adm-count-pend" class="text-xl font-black text-yellow-500">0</h4></div></div>
            
            <div id="adm-requests-list" class="space-y-3"></div>
            <div id="adm-users-list" class="space-y-2 border-t border-white/5 pt-6"></div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] border-t border-white/5">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl">📜</button>
        <button onclick="changePage('more')" id="n-more" class="text-2xl">⚙️</button>
    </nav>

    <script>
        // FIREBASE INITIALIZATION WITH YOUR CONFIG, SWEETIE
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
        const storage = firebase.storage();
        
        let user = null; let tapCount = 0;

        const plans = [
            {n:"Basic-X 1", p:200, r:3, d:30}, {n:"Basic-X 2", p:500, r:3.2, d:30}, {n:"Bronze-X 1", p:1000, r:3.6, d:30},
            {n:"Silver-X 1", p:5000, r:4.2, d:45}, {n:"Gold-X 1", p:15000, r:5.5, d:60}, {n:"Elite-X 3", p:100000, r:10, d:120},
            {n:"👑 CROWN KING", p:25000, r:22, d:15}, {n:"⚡ LIGHTNING", p:5000, r:10, d:5}
        ]; // Shortened for display, but keep your 25 plans in mind.

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); 
            if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); 
            const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, lastSpin: 0, activeTier: 0, tierName: "Inactive" });
            
            db.collection("users").doc(name).onSnapshot(d => { user = d.data(); updateUI(); });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(d => { const r = d.data(); list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center mb-2 text-[8px] font-black uppercase"><div>${r.type}<br><span class="opacity-30">${r.promo||''}</span></div><div>₨ ${r.amount} <span class="badge-${r.status}">${r.status}</span></div></div>`; });
            });
            
            document.getElementById('dash-user').innerText = name;
            document.getElementById('auth-ui').classList.add('hidden'); 
            document.getElementById('app-ui').classList.remove('hidden'); 
            document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function updateUI() {
            document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
            document.getElementById('tier-tag').innerText = user.tierName;
            if(Date.now() - (user.lastSpin || 0) < 86400000) { 
                document.getElementById('spin-btn').disabled = true; 
                document.getElementById('spin-btn').innerText = "LOCKED (24h)";
                document.getElementById('spin-btn').classList.add('opacity-40');
            }
        }

        function spinNow() {
            if(Date.now() - (user.lastSpin || 0) < 86400000) return alert("Come back tomorrow, Sweetie!");
            const deg = Math.floor(3000 + Math.random() * 3000);
            document.getElementById('wheel').style.transform = `rotate(${deg}deg)`;
            setTimeout(async () => {
                const bonus = Math.floor(Math.random() * 300) + 50;
                alert(`Sweetie! You won ₨ ${bonus}! Verification sent.`);
                await db.collection("requests").add({ user: user.name, amount: bonus, type: "Spin Reward", status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastSpin: Date.now() });
            }, 4000);
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value, p = document.getElementById('dep-promo').value, file = document.getElementById('dep-proof').files[0];
            if(!a || !t || !file) return alert("Sweetie, please provide all info and proof!");
            document.getElementById('dep-btn').innerText = "Uploading...";
            const sRef = storage.ref(`proofs/${Date.now()}_${user.name}`);
            await sRef.put(file); const url = await sRef.getDownloadURL();
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, promo: p||"No Code", proof: url, type: "Deposit", status: "pending", time: Date.now() });
            alert("Verification Sent to Master Admin!"); location.reload();
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buy(${p.p},${p.r},${p.d},'${p.n}')" class="glass p-5 rounded-[2.5rem] flex justify-between items-center mb-2 border-l-4 border-blue-500/20 active:scale-95"><div><h4 class="font-black text-[10px] text-blue-400 uppercase">${p.n}</h4><p class="text-[7px] text-gray-500 uppercase">${p.r}% Daily • ${p.d} Days</p></div><p class="font-black text-[13px]">₨ ${p.p.toLocaleString()}</p></div>`;
            });
        }

        async function buy(p,r,d,n) {
            if(user.balance < p) return alert("Insufficient Funds, Sweetie!");
            if(confirm(`Activate ${n} Fleet?`)) {
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: r, tierName: n, tierExpiry: Date.now()+(d*86400000), lastReqTime: Date.now() });
                alert("Fleet Active!");
            }
        }

        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("Master Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        
        async function adminEdit() {
            const name = document.getElementById('adm-u-name').value.toUpperCase();
            const val = parseInt(document.getElementById('adm-u-val').value);
            const ref = db.collection("users").doc(name); const d = await ref.get();
            if(d.exists) { await ref.update({ balance: (d.data().balance||0) + val }); alert("Updated!"); }
        }

        function syncAdmin() {
            db.collection("users").onSnapshot(snap => {
                document.getElementById('adm-count-users').innerText = snap.size;
                const list = document.getElementById('adm-users-list'); list.innerHTML = '';
                snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-3 flex justify-between items-center text-[8px] font-black"><span>${u.name} (₨ ${u.balance})</span><button onclick="deleteU('${u.name}')" class="text-red-500">REMOVE</button></div>`; });
            });
            db.collection("requests").where("status","==","pending").onSnapshot(snap => {
                document.getElementById('adm-count-pend').innerText = snap.size;
                const list = document.getElementById('adm-requests-list'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 text-[8px] font-black uppercase mb-2">User: ${d.user}<br>Type: ${d.type}<br>Amt: ${d.amount}<br>Code: ${d.promo||'None'}<br>${d.proof?`<a href="${d.proof}" target="_blank" class="text-blue-500 underline">VIEW PROOF</a>`:''}<div class="mt-2"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved')" class="bg-green-600 px-4 py-1 rounded">APPROVE</button></div></div>`; });
            });
        }

        async function handle(id, u, amt, act) {
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(act==='approved') await ref.update({ balance: (d.data().balance||0)+amt });
            await db.collection("requests").doc(id).update({ status: act });
        }

        async function deleteU(n) { if(confirm("Permanently Delete User?")) await db.collection("users").doc(n).delete(); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.clear(); location.reload(); }
        async function sendSupport() { const m = document.getElementById('support-msg').value; await db.collection("requests").add({ user: user.name, type: "Support", msg: m, status: "pending", time: Date.now() }); alert("Sent!"); }
        async function submitWithdraw() { const a = parseInt(document.getElementById('wd-amt').value); if(a > user.balance) return alert("Low Capital!"); await db.collection("requests").add({ user: user.name, amount: a, type: "Withdraw", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ balance: user.balance - a }); alert("Authorized!"); }
    </script>
</body>
</html>
