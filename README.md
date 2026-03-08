<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global Financial Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); transition: 0.3s; padding-bottom: 100px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 16px; font-weight: 800; transition: 0.2s; }
        .btn-prime:active { transform: scale(0.95); }
        input, select { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 14px; width: 100%; outline: none; font-size: 13px; }
        .nav-active { color: var(--accent); transform: scale(1.1); opacity: 1 !important; }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[var(--bg)] hidden flex flex-col items-center justify-center p-8 text-center">
        <h1 id="lock-title" class="text-2xl font-black text-red-500 mb-2 uppercase italic">Access Restricted</h1>
        <p id="lock-msg" class="text-[10px] font-bold opacity-60 uppercase tracking-widest leading-relaxed">System Optimization in Progress.</p>
    </div>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] z-[5000]">
        <div class="flex items-center gap-2">
            <div onclick="adminTap()" class="w-9 h-9 bg-blue-600 rounded-xl flex items-center justify-center font-black text-white italic shadow-lg">M</div>
            <span class="font-black text-xs uppercase tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></span>
        </div>
        <div class="flex items-center gap-3">
            <div id="top-bal" class="text-[10px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
            <button onclick="logout()" class="w-8 h-8 glass rounded-full flex items-center justify-center text-xs shadow-md">🚪</button>
        </div>
    </header>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8 text-center">
        <h1 class="text-2xl font-black italic tracking-tighter mb-8 uppercase text-blue-500">Initialize Node</h1>
        <div class="w-full max-w-[300px] space-y-4">
            <input type="text" id="user-name" placeholder="Username" class="text-center font-bold">
            <input type="password" id="user-pass" placeholder="Password" class="text-center font-bold">
            <button onclick="login()" class="w-full p-4 btn-prime uppercase text-[10px] tracking-widest shadow-xl">Authorize Access</button>
        </div>
    </section>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="bg-blue-600/5 border border-blue-500/10 p-2 rounded-xl overflow-hidden whitespace-nowrap">
                <p id="v-news" class="animate-marquee inline-block text-[8px] font-black uppercase text-blue-400 italic">Welcome to MintCrest Gold Global Infrastructure...</p>
            </div>

            <div class="p-8 rounded-[2.5rem] bg-gradient-to-br from-blue-700 to-blue-900 text-white shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60 tracking-widest">Global Liquidity</p>
                <h2 class="text-4xl font-black tracking-tighter mt-1" id="v-bal">₨ 0.00</h2>
                <button onclick="copyRef()" class="mt-4 bg-white/20 px-4 py-2 rounded-xl text-[8px] font-black uppercase backdrop-blur-md">Copy Referral Link</button>
            </div>

            <div class="flex gap-2 p-1 bg-white/5 rounded-2xl">
                <button onclick="renderPlans('normal')" id="btn-normal" class="flex-1 p-3 rounded-xl bg-blue-600 text-white text-[9px] font-black uppercase">Standard (20)</button>
                <button onclick="renderPlans('special')" id="btn-special" class="flex-1 p-3 rounded-xl text-[9px] font-black uppercase opacity-40">VIP Nodes (5)</button>
            </div>
            <div id="plans-grid" class="grid grid-cols-1 gap-4"></div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div class="space-y-3">
                <div class="glass p-5 rounded-[2rem] border-l-4 border-red-600 flex justify-between items-center">
                    <div><p class="text-[8px] font-black text-red-500 uppercase">JazzCash</p><p class="text-lg font-black tracking-widest">03705519562</p></div>
                    <button onclick="copyText('03705519562')" class="text-[8px] bg-white/5 px-3 py-2 rounded-lg font-bold">COPY</button>
                </div>
                <div class="glass p-5 rounded-[2rem] border-l-4 border-green-500 flex justify-between items-center">
                    <div><p class="text-[8px] font-black text-green-500 uppercase">EasyPaisa</p><p class="text-lg font-black tracking-widest">03379827882</p></div>
                    <button onclick="copyText('03379827882')" class="text-[8px] bg-white/5 px-3 py-2 rounded-lg font-bold">COPY</button>
                </div>
            </div>
            <div class="glass p-6 rounded-[2.5rem] space-y-4">
                <input type="number" id="dep-amt" placeholder="Amount (Min: ₨ 200)">
                <input type="text" id="dep-tid" placeholder="Transaction ID">
                <input type="file" id="dep-file" accept="image/*" class="hidden" onchange="previewImg(this)">
                <label for="dep-file" class="w-full p-4 border-2 border-dashed border-[var(--border)] rounded-xl flex flex-col items-center justify-center cursor-pointer">
                    <span id="file-label" class="text-[8px] font-black uppercase opacity-50">Upload Proof Screenshot</span>
                    <img id="img-preview" class="hidden w-full h-32 object-cover rounded-xl mt-2">
                </label>
                <button onclick="submitDep()" class="w-full p-4 btn-prime uppercase text-[10px]">Submit Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <h3 class="text-[10px] font-black uppercase opacity-40 text-center italic tracking-widest">Withdrawal (10% Service Tax)</h3>
            <div class="glass p-6 rounded-[2.5rem] space-y-4">
                <select id="w-method"><option value="">Gateway</option><option value="JazzCash">JazzCash</option><option value="EasyPaisa">EasyPaisa</option></select>
                <input type="number" id="w-amt" placeholder="Amount (Min: ₨ 100)">
                <input type="text" id="w-acc" placeholder="Account Number">
                <input type="text" id="w-name" placeholder="Holder Name">
                <button onclick="submitWith()" class="w-full p-4 btn-prime uppercase text-[10px]">Request Payout</button>
            </div>
        </div>

        <div id="p-history" class="page space-y-6">
            <div class="glass p-6 rounded-[2.5rem]">
                <h3 class="text-[10px] font-black uppercase mb-4 italic">Company Profile & Privacy</h3>
                <p class="text-[9px] opacity-60 leading-relaxed">MintCrest Gold is a global financial infrastructure provider. We ensure secure data encryption and 24/7 node monitoring. All investments are subject to market activation rules. 10% tax applies on all withdrawals for system maintenance.</p>
            </div>
            <h3 class="text-[10px] font-black uppercase opacity-40 italic tracking-widest">Recent Node Activities</h3>
            <div id="history-list" class="space-y-3"></div>
        </div>

    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-4 md:p-8">
        <div class="flex justify-between items-center mb-8 sticky top-0 bg-[#010409]/90 backdrop-blur-md py-4 z-10 border-b border-blue-500/20">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Admin God Mode</h2>
            <button onclick="closeAdmin()" class="bg-red-500 text-white px-6 py-2 rounded-xl text-[10px] font-black uppercase">Exit</button>
        </div>
        
        <div class="grid grid-cols-2 gap-2 mb-6">
            <button onclick="sysToggle('maintenance')" id="m-btn" class="bg-blue-600/20 p-4 rounded-xl text-[8px] font-black border border-blue-500/30 uppercase">Maintenance: OFF</button>
            <button onclick="let t=prompt('News:'); if(t) db.collection('settings').doc('news').set({text:t})" class="bg-blue-600/20 p-4 rounded-xl text-[8px] font-black border border-blue-500/30 uppercase">Update News</button>
        </div>

        <div id="adm-requests-list" class="space-y-4 pb-20"></div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-4 flex justify-around items-center rounded-t-[2.5rem] z-[4000]">
        <button onclick="changePage('home')" id="n-home" class="nav-active opacity-40 flex flex-col items-center gap-1">🏠<span class="text-[7px] font-black">HOME</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="opacity-40 flex flex-col items-center gap-1">📥<span class="text-[7px] font-black">FUND</span></button>
        <button onclick="changePage('withdraw')" id="n-withdraw" class="opacity-40 flex flex-col items-center gap-1">📤<span class="text-[7px] font-black">PAYOUT</span></button>
        <button onclick="changePage('history')" id="n-history" class="opacity-40 flex flex-col items-center gap-1">📜<span class="text-[7px] font-black">LOGS</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tapCount = 0;

        // 20 Normal Plans + 5 Special Plans
        const NORMAL_PLANS = Array.from({length: 20}, (_, i) => ({ name: `Standard Node v${i+1}`, price: 200 + (i*200), daily: 20 + (i*15) }));
        const SPECIAL_PLANS = Array.from({length: 5}, (_, i) => ({ name: `VIP Elite x${i+1}`, price: 5000 + (i*2000), daily: 800 + (i*300) }));

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const p = document.getElementById('user-pass').value.trim();
            if(!n || !p) return alert("Credentials required.");
            const ref = db.collection("users").doc(n);
            const d = await ref.get();
            if(!d.exists) {
                await ref.set({ name: n, password: p, balance: 0, time: Date.now() });
            } else if(d.data().password !== p) {
                return alert("Incorrect Password.");
            }
            localStorage.setItem('mc_user', n);
            enterApp(n);
        }

        function logout() { localStorage.removeItem('mc_user'); localStorage.removeItem('is_admin'); location.reload(); }

        function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                if(user.banned) showLock("ACCOUNT SUSPENDED", "Security violation detected.");
            });
            syncLogs(n); renderPlans('normal');
        }

        function syncLogs(n) {
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const list = document.getElementById('history-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[10px] font-bold border-l-4 ${x.status==='approved'?'border-green-500':'border-yellow-500'}">
                        <div><p class="uppercase">${x.type}</p><p class="text-[7px] opacity-40">${new Date(x.time).toLocaleDateString()}</p></div>
                        <div class="text-right"><p>₨ ${x.amount||0}</p><p class="text-[7px] uppercase italic">${x.status}</p></div>
                    </div>`;
                });
            });
        }

        function renderPlans(cat) {
            const grid = document.getElementById('plans-grid');
            const data = cat === 'normal' ? NORMAL_PLANS : SPECIAL_PLANS;
            grid.innerHTML = '';
            data.forEach(p => {
                grid.innerHTML += `<div class="glass p-6 rounded-[2.5rem] border-l-4 border-blue-500 flex justify-between items-center">
                    <div><h4 class="font-black text-xs uppercase">${p.name}</h4><p class="text-[10px] text-blue-500 font-bold">Cost: ₨ ${p.price}</p></div>
                    <button onclick="buyPlan('${p.name}', ${p.price}, ${p.daily})" class="btn-prime px-6 py-3 text-[8px]">BUY</button>
                </div>`;
            });
            document.getElementById('btn-normal').className = cat==='normal'?'flex-1 p-3 rounded-xl bg-blue-600 text-white text-[9px] font-black uppercase':'flex-1 p-3 rounded-xl text-[9px] font-black uppercase opacity-40';
            document.getElementById('btn-special').className = cat==='special'?'flex-1 p-3 rounded-xl bg-blue-600 text-white text-[9px] font-black uppercase':'flex-1 p-3 rounded-xl text-[9px] font-black uppercase opacity-40';
        }

        async function buyPlan(pName, price, daily) {
            if(user.balance < price) return alert("Insufficient balance.");
            if(confirm(`Request ${pName} activation for ₨ ${price}?`)) {
                await db.collection("requests").add({ user: user.name, type: "plan_activation", plan: pName, amount: price, daily: daily, status: "pending", time: Date.now() });
                alert("Request sent for admin approval.");
            }
        }

        function previewImg(i) { if (i.files && i.files[0]) { const r = new FileReader(); r.onload = e => { document.getElementById('img-preview').src = e.target.result; document.getElementById('img-preview').classList.remove('hidden'); }; r.readAsDataURL(i.files[0]); } }
        async function submitDep() {
            const a = parseInt(document.getElementById('dep-amt').value); const tid = document.getElementById('dep-tid').value; const f = document.getElementById('dep-file').files[0];
            if(!a || a < 200 || !tid || !f) return alert("Fill all details (Min 200).");
            const r = new FileReader(); r.readAsDataURL(f);
            r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: a, tid: tid, proof: r.result, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit request submitted."); location.reload();
            };
        }

        async function submitWith() {
            const a = parseInt(document.getElementById('w-amt').value); const m = document.getElementById('w-method').value; const ac = document.getElementById('w-acc').value; const nm = document.getElementById('w-name').value;
            if(!a || a < 100 || !m || !ac || !nm) return alert("Minimum ₨ 100 payout.");
            if(a > user.balance) return alert("Low balance.");
            const tax = a * 0.10; const net = a - tax;
            if(confirm(`Total: ₨ ${a} | Tax: ₨ ${tax} | Receive: ₨ ${net}`)) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
                await db.collection("requests").add({ user: user.name, amount: a, tax: tax, receive: net, method: m, account: ac, holder: nm, type: "withdrawal", status: "pending", time: Date.now() });
                alert("Payout requested."); location.reload();
            }
        }

        // --- GOD MODE ADMIN ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("God Mode Key:")==="mint786") { localStorage.setItem('is_admin','true'); document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function sysToggle(key) { db.collection("settings").doc("system").set({ [key]: !maintenanceActive }, {merge:true}); }

        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-requests-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    let info = r.type === 'plan_activation' ? `Plan: ${r.plan} (₨ ${r.amount})` : r.type === 'withdrawal' ? `Pay: ₨ ${r.receive}` : `Dep: ₨ ${r.amount}`;
                    list.innerHTML += `<div class="glass p-6 rounded-3xl border-l-4 border-blue-500">
                        <div class="flex gap-4 items-center uppercase">
                            ${r.proof ? `<img src="${r.proof}" class="w-16 h-16 rounded-xl object-cover">` : ''}
                            <div class="flex-1 text-[10px] font-black"><p>${r.user} | ${r.type.replace('_',' ')}</p><p class="text-blue-400 mt-1">${info}</p></div>
                            <div class="flex flex-col gap-2">
                                <button onclick="approve('${doc.id}', '${r.user}', '${r.type}', ${r.amount}, ${r.daily || 0})" class="bg-blue-600 px-4 py-2 rounded-lg text-[8px] font-black">APPROVE</button>
                                <button onclick="db.collection('requests').doc('${doc.id}').update({status:'rejected'})" class="bg-red-600 px-4 py-2 rounded-lg text-[8px] font-black">REJECT</button>
                            </div>
                        </div>
                    </div>`;
                });
            });
        }

        async function approve(id, u, type, amt, daily) {
            const ref = db.collection("users").doc(u);
            const userSnapshot = await ref.get();
            const userData = userSnapshot.data();
            if(type === 'deposit') await ref.update({ balance: firebase.firestore.FieldValue.increment(amt) });
            else if(type === 'plan_activation') {
                if(userData.balance < amt) return alert("User balance now insufficient.");
                await ref.update({ balance: firebase.firestore.FieldValue.increment(-amt), activePlan: true, dailyYield: daily });
            }
            await db.collection("requests").doc(id).update({ status: 'approved' });
            alert("Action Processed.");
        }

        // Global Sync
        let maintenanceActive = false;
        db.collection("settings").doc("system").onSnapshot(d => {
            if(d.exists) {
                maintenanceActive = d.data().maintenance;
                if(maintenanceActive && localStorage.getItem('is_admin') !== 'true') document.getElementById('lock-screen').classList.remove('hidden');
                else document.getElementById('lock-screen').classList.add('hidden');
                document.getElementById('m-btn').innerText = "Maintenance: " + (maintenanceActive?"ON":"OFF");
            }
        });
        db.collection("settings").doc("news").onSnapshot(d => { if(d.exists) document.getElementById('v-news').innerText = d.data().text; });

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('nav-active')); document.getElementById('n-'+p).classList.add('nav-active'); }
        function copyText(t) { navigator.clipboard.writeText(t); alert("Copied."); }
        function copyRef() { copyText(window.location.origin + window.location.pathname + "?ref=" + user.name); }
        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); }
    </script>
</body>
</html>
