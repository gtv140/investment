<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Elite Asset Hub</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #01040a; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(40px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.2), transparent); }
        .page { display: none; animation: slideUp 0.5s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        .premium-card { background: linear-gradient(135deg, #0f172a 0%, #1e3a8a 100%); border: 1px solid rgba(59, 130, 246, 0.4); box-shadow: 0 20px 50px rgba(0,0,0,0.5); }
        .special-card { background: linear-gradient(135deg, #1e1b4b 0%, #4338ca 100%); border: 1px solid #6366f1; position: relative; overflow: hidden; }
        .special-card::after { content: 'HOT'; position: absolute; top: 10px; right: -20px; background: #f59e0b; color: black; font-size: 8px; font-weight: 900; padding: 2px 20px; transform: rotate(45deg); }
        .trust-badge { background: rgba(34, 197, 94, 0.1); color: #4ade80; font-size: 7px; font-weight: 800; padding: 3px 8px; border-radius: 6px; text-transform: uppercase; border: 1px solid rgba(34, 197, 94, 0.2); }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#01040a] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-1 uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[9px] uppercase tracking-[0.4em] mb-12 font-bold opacity-60">Professional Asset Vault</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Choose Username" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-4 focus:border-blue-500 transition-all text-sm">
                <input type="text" id="ref-by" placeholder="Invite Code (Optional)" class="w-full bg-white/5 p-4 rounded-xl border border-white/5 outline-none text-center text-[10px] mb-8 font-bold opacity-50">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[11px] uppercase tracking-widest shadow-2xl active:scale-95 transition-all">Establish Connection</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div class="p-8 pb-2 flex justify-between items-end">
            <div>
                <p class="text-[9px] font-black uppercase text-blue-500 tracking-widest mb-1">Authenticated Account</p>
                <h1 class="text-2xl font-black tracking-tighter" id="welcome-txt">Welcome, sweetie!</h1>
            </div>
            <div class="trust-badge">Verified 🛡️</div>
        </div>

        <div id="p-home" class="page active-page p-6">
            <div class="premium-card p-10 rounded-[3rem] mb-8 relative overflow-hidden">
                <div class="absolute right-0 top-0 p-4 opacity-10 text-5xl font-black italic uppercase">MCG</div>
                <p class="text-[10px] text-blue-300 font-extrabold uppercase tracking-widest opacity-80">Portfolio Balance</p>
                <h2 class="text-5xl font-black tracking-tighter mt-1" id="v-bal">₨ 0</h2>
                
                <div class="mt-8 grid grid-cols-2 gap-4">
                    <div class="bg-white/5 border border-white/10 p-4 rounded-2xl">
                        <p class="text-[7px] text-gray-400 uppercase font-black mb-1">Current Profit</p>
                        <span id="v-profit" class="text-xs font-bold text-green-400">₨ 0</span>
                    </div>
                    <div class="bg-white/5 border border-white/10 p-4 rounded-2xl">
                        <p class="text-[7px] text-gray-400 uppercase font-black mb-1">Active Fleet</p>
                        <span id="tier-tag" class="text-[9px] font-bold text-blue-400 uppercase italic">Inactive</span>
                    </div>
                </div>
            </div>
            
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] flex flex-col items-center border-b-4 border-blue-600 shadow-xl active:scale-95 transition-all">
                    <span class="text-3xl mb-2">📥</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Fund</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] flex flex-col items-center border-b-4 border-red-600 shadow-xl active:scale-95 transition-all">
                    <span class="text-3xl mb-2">📤</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Payout</span>
                </button>
            </div>

            <div class="mt-8 glass p-6 rounded-[2.5rem] flex justify-between items-center px-8">
                <div class="text-center"><p class="text-lg font-black" id="ref-count">0</p><p class="text-[7px] uppercase font-bold opacity-40">Network</p></div>
                <div class="w-[1px] h-8 bg-white/10"></div>
                <div class="text-center"><p class="text-lg font-black text-green-400">Active</p><p class="text-[7px] uppercase font-bold opacity-40">Node Status</p></div>
                <div class="w-[1px] h-8 bg-white/10"></div>
                <div class="text-center"><p class="text-lg font-black text-blue-400">99%</p><p class="text-[7px] uppercase font-bold opacity-40">Trust Score</p></div>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-500 text-xl tracking-tighter">Trading Fleet</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-4 pb-12"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 tracking-widest">Security Ledger</h2>
            <div id="user-history" class="space-y-3 pb-12"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6">
            <div class="glass p-8 rounded-[3rem] border-t-4 border-blue-500 text-center">
                <h3 class="text-blue-500 font-black text-[11px] uppercase mb-4 italic">Invite Protocol</h3>
                <div id="my-ref-id" class="text-2xl font-black py-2 tracking-tighter text-white">---</div>
                <button onclick="copyRef()" class="mt-3 bg-blue-600 px-10 py-3 rounded-2xl font-black text-[10px] uppercase active:scale-95">Copy Link</button>
            </div>

            <div class="glass p-8 rounded-[3rem] border-l-4 border-blue-600">
                <h3 class="text-blue-500 font-black text-[11px] uppercase mb-4 italic">🛡️ System Policy</h3>
                <ul class="text-[9px] text-gray-400 font-bold uppercase space-y-3">
                    <li>• Manual verification for high-value payouts.</li>
                    <li>• Multi-layered cold storage for assets.</li>
                    <li>• One account per unique hardware ID.</li>
                </ul>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-[10px] font-black text-red-500 uppercase">Terminate Link</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm text-center">Capital Funding</h3>
                <div class="space-y-3 mb-10 text-[10px] font-black">
                    <div class="glass p-4 rounded-xl flex justify-between bg-blue-500/5"><span>SADAPAY / JAZZCASH</span><span class="text-blue-400">03705519562</span></div>
                    <div class="glass p-4 rounded-xl flex justify-between bg-green-500/5"><span>EASYPAISA</span><span class="text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/10">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold uppercase outline-none border border-white/10">
                <label class="block w-full bg-white/5 p-5 rounded-2xl mb-8 border border-dashed border-white/20 text-center cursor-pointer">
                    <span id="file-label" class="text-[9px] font-black opacity-40 uppercase">📸 Upload ScreenShot</span>
                    <input type="file" id="dep-proof" accept="image/*" class="hidden" onchange="document.getElementById('file-label').innerText = 'Evidence Logged ✅'">
                </label>
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[11px] uppercase tracking-widest shadow-xl">Secure Link</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 shadow-2xl">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm text-center">Authorize Payout</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/10">
                <input type="text" id="wd-acc" placeholder="Account Title & No" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center text-[10px] font-bold outline-none border border-white/10">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[11px] uppercase tracking-widest shadow-xl">Confirm Request</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#01040a] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 italic uppercase">Central Command 5.0</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-4 py-2 rounded-xl text-[9px] font-black uppercase">Exit</button>
        </div>

        <div class="glass p-8 rounded-[3rem] mb-6">
            <h3 class="text-[10px] font-black uppercase mb-4 opacity-40 text-center">Asset Injection</h3>
            <input type="text" id="adm-user" placeholder="Username" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-[10px] text-center font-black border border-white/5 outline-none">
            <input type="number" id="adm-val" placeholder="Amount (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[10px] text-center font-black border border-white/5 outline-none">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="manualEdit('balance')" class="bg-blue-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Balance</button>
                <button onclick="manualEdit('profit')" class="bg-green-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Profit</button>
            </div>
        </div>

        <div id="adm-sec-requests" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab"><span class="text-2xl">🏠</span><span class="text-[8px] font-black uppercase">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2"><span class="text-2xl">📈</span><span class="text-[8px] font-black uppercase">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2"><span class="text-2xl">📜</span><span class="text-[8px] font-black uppercase">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2"><span class="text-2xl">🏢</span><span class="text-[8px] font-black uppercase">Firm</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3, d: 30 }, { n: "Micro-Elite II", p: 500, r: 3.2, d: 30 },
            { n: "Bronze-S", p: 1000, r: 3.5, d: 30 }, { n: "Silver-X", p: 5000, r: 4.5, d: 30 },
            { n: "Gold-Master", p: 15000, r: 6, d: 30 }, { n: "Royal-Grand", p: 100000, r: 10, d: 30 },
            { n: "⚡ FLASH 24H", p: 500, r: 12, s: true },
            { n: "🚀 ROCKET PRO", p: 12000, r: 18, s: true },
            { n: "👑 CROWN KING", p: 25000, r: 22, s: true }
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive" });
            document.getElementById('welcome-txt').innerText = `Welcome, sweetie! ${name} 👋`;
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); document.getElementById('my-ref-id').innerText = name;
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { 
                if(doc.exists) { 
                    user = doc.data(); 
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); 
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); 
                    document.getElementById('tier-tag').innerText = user.tierName;
                } 
            });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 border-blue-500 mb-2 text-[10px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="text-blue-400 font-black">${d.status}</span></div></div>`; });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const isS = p.s === true;
                const style = isS ? 'special-card' : 'glass';
                const yieldText = isS ? `Total Yield: ${p.r}% Profit` : `${p.r}% Daily Profit`;
                const contract = isS ? '' : `<p class="text-[7px] text-gray-500 font-bold mt-1">⏳ ${p.d} Days Contract</p>`;
                
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="${style} p-7 rounded-[2.5rem] flex justify-between items-center active:scale-95 cursor-pointer mb-2">
                    <div>
                        <h4 class="font-black text-[12px] uppercase ${isS ? 'text-white' : 'text-gray-400'}">${p.n}</h4>
                        <p class="text-[9px] text-green-400 font-bold uppercase">${yieldText}</p>
                        ${contract}
                    </div>
                    <div class="text-right"><p class="font-black text-[15px]">₨ ${p.p.toLocaleString()}</p></div>
                </div>`;
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const f = document.getElementById('dep-proof');
            if(!a || !t || !f.files[0]) return alert("All fields are mandatory!");
            const r = new FileReader(); r.readAsDataURL(f.files[0]); r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: r.result, type: "deposit", status: "pending", time: Date.now() });
                alert("Request Logged, sweetie! 😘"); changePage('activity');
            };
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a || !acc || parseInt(a) > user.balance) return alert("Insufficient Capital!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); alert("Request Authorized!"); changePage('activity');
        }

        async function buy(p, roi, tName) {
            if(user.balance < p) return alert("Insufficient Balance!");
            if(confirm("Confirm Investment in "+tName+"?")) { 
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, lastReqTime: Date.now() }); alert("Success!"); changePage('activity');
            }
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Auth Code:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        async function manualEdit(f) { const u = document.getElementById('adm-user').value; const v = parseInt(document.getElementById('adm-val').value); const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f]||0)+v }); alert("Done!"); }

        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    const pBtn = d.proof ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-blue-600 px-3 py-1 rounded text-[8px] font-black uppercase">Proof</button>` : '';
                    list.innerHTML += `<div class="glass p-5 rounded-2xl flex justify-between items-center text-[10px] font-black uppercase mb-3"><div>${d.user}<br>₨ ${d.amount} (${d.type})</div><div class="flex gap-2">${pBtn}<button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-1 rounded">Approve</button></div></div>`;
                });
            });
        }

        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get(); 
            if(act==='approved' && (type==='deposit' || type.includes('Profit'))) await ref.update({ balance: (doc.data().balance||0) + amt });
            await db.collection("requests").doc(id).update({ status: act }); 
        }

        window.onload = () => { const saved = localStorage.getItem('mc_user'); if(saved) { document.getElementById('user-name').value = saved; login(); } };
    </script>
</body>
</html>
