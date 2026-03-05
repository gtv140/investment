<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script type="text/javascript" src="https://s3.tradingview.com/tv.js"></script>
    <title>MintCrest Gold | Ultimate Global Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;900&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #000308; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.01); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.06); border-radius: 2.5rem; }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        .active-tab { color: #3b82f6; border-top: 2px solid #3b82f6; transform: translateY(-3px); }
        .btn-glow { box-shadow: 0 0 20px rgba(59, 130, 246, 0.2); }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[9999] bg-[#000308] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-2 uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-[8px] tracking-[0.5em] text-blue-400 mb-16 font-bold uppercase">Authorized Access Only</p>
            <div class="glass p-10 border-t-2 border-blue-600/50">
                <input type="text" id="user-name" placeholder="Enter Username" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center font-bold outline-none border border-white/5 focus:border-blue-500">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 shadow-2xl btn-glow">Enter Vault</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div class="p-8 flex justify-between items-center">
            <div><p class="text-[9px] text-gray-500 font-bold uppercase">Account Node</p><h2 id="u-display" class="text-xl font-black italic">--</h2></div>
            <button onclick="changePage('history')" class="glass p-4 text-[9px] font-black uppercase">📜 LOGS</button>
        </div>

        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 mb-6 bg-gradient-to-br from-blue-600/10 to-transparent border-l-4 border-blue-600">
                <p class="text-[9px] text-blue-400 font-black uppercase tracking-widest mb-1">Total Assets</p>
                <h2 class="text-5xl font-black tracking-tighter mb-6" id="v-bal">₨ 0</h2>
                <div class="flex justify-between items-center bg-black/40 p-4 rounded-2xl">
                    <div><p class="text-[8px] text-gray-500 font-bold uppercase">Daily Yield</p><p class="text-xs font-black text-green-500" id="v-profit">₨ 0</p></div>
                    <div class="text-right"><p class="text-[8px] text-gray-500 font-bold uppercase">Fleet Status</p><p class="text-xs font-black" id="v-tier">Inactive</p></div>
                </div>
            </div>

            <div id="timer-box" class="hidden glass p-6 mb-6 border border-yellow-500/20 text-center">
                <p class="text-[8px] font-black text-yellow-500 uppercase mb-2">Cycle Reset Countdown</p>
                <h3 id="cycle-clock" class="text-2xl font-black tracking-widest">00:00:00</h3>
                <div class="w-full h-1 bg-white/5 rounded-full mt-3 overflow-hidden"><div id="cycle-bar" class="h-full bg-yellow-500 w-0 transition-all"></div></div>
            </div>

            <div class="glass p-4 h-32 mb-6 opacity-40">
                <div id="tv_chart" class="h-full w-full"></div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('deposit')" class="glass p-8 text-center bg-blue-600/5">📥<br><span class="text-[10px] font-black uppercase mt-2 block">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 text-center bg-red-600/5">📤<br><span class="text-[10px] font-black uppercase mt-2 block">Extract</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black uppercase text-sm mb-8 tracking-widest italic text-blue-500">Available Fleets</h2>
            <div id="plans-grid" class="space-y-3"></div>
        </div>

        <div id="p-history" class="page p-6">
            <h2 class="text-center font-black uppercase text-sm mb-6 tracking-widest">Transaction History</h2>
            <div id="history-list" class="space-y-3"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-4">
            <div class="glass p-8 text-center border-t-4 border-yellow-500">
                <h3 class="font-black uppercase text-[10px] mb-4 text-yellow-500">Redeem Promo Code</h3>
                <input type="text" id="promo-code" placeholder="Enter Code" class="w-full bg-white/5 p-4 rounded-xl text-center font-bold mb-4 uppercase">
                <button onclick="applyPromo()" class="w-full bg-yellow-600 py-4 rounded-xl font-black text-[10px] uppercase">Apply</button>
            </div>
            
            <div class="glass p-6 text-center">
                <h3 class="font-black uppercase text-[10px] mb-4">Referral Network</h3>
                <p id="ref-link" class="text-[8px] bg-black/50 p-3 rounded-lg mb-4 break-all opacity-50">--</p>
                <button onclick="copyRef()" class="bg-blue-600 px-8 py-3 rounded-xl font-black text-[9px] uppercase">Copy Link</button>
            </div>

            <a href="https://chat.whatsapp.com/yourlink" class="block glass p-6 text-center text-green-500 font-black text-[10px] uppercase">Join WhatsApp Community</a>
            <button onclick="changePage('about')" class="w-full glass p-6 text-center font-black text-[10px] uppercase">Company Credentials</button>
            <button onclick="logout()" class="w-full glass p-6 text-red-500 font-black text-[10px] uppercase">Sign Out</button>
        </div>

        <div id="p-deposit" class="page p-6">
            <div class="glass p-8 border-t-8 border-blue-600">
                <h3 class="text-center font-black uppercase text-sm mb-6">Capital Funding</h3>
                <select id="dep-m" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold outline-none">
                    <option value="EasyPaisa">EasyPaisa (03379827882)</option>
                    <option value="JazzCash">JazzCash (03705519562)</option>
                </select>
                <input type="number" id="dep-a" placeholder="Amount (Min 200)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold outline-none">
                <label class="block w-full bg-white/5 p-5 rounded-2xl border-2 border-dashed border-white/10 text-center cursor-pointer mb-6">
                    <span id="p-txt" class="text-[9px] font-black uppercase opacity-40">📸 Upload Screenshot Proof</span>
                    <input type="file" id="dep-f" class="hidden" onchange="document.getElementById('p-txt').innerText='Proof Linked ✅'">
                </label>
                <button onclick="submitDep()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase">Submit Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 border-t-8 border-red-600">
                <h3 class="text-center font-black uppercase text-sm mb-8">Asset Extraction</h3>
                <input type="number" id="wd-a" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold">
                <input type="text" id="wd-acc" placeholder="Account Number & Name" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center font-bold">
                <button onclick="submitWd()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase">Authorize Payout</button>
            </div>
        </div>

        <div id="p-about" class="page p-6">
            <div class="glass p-10 space-y-4 text-[10px] text-center">
                <h2 class="font-black uppercase text-blue-500">MintCrest Gold Ltd</h2>
                <p class="opacity-60 leading-relaxed uppercase">Global asset vault provider since 2022. Registered under UK corporate law #MC-7786. We provide institutional grade liquidity mining nodes.</p>
                <button onclick="changePage('more')" class="bg-white/5 px-6 py-2 rounded-lg">Back</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass fixed bottom-0 left-0 w-full p-8 flex justify-around items-center z-[1000] border-t border-white/5 bg-[#000308]/90">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-2xl">🏛️</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">💎</button>
        <button onclick="changePage('more')" id="n-more" class="text-2xl">⚙️</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-black z-[10000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-500 uppercase">Master Console</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-4 py-1 rounded text-[10px]">EXIT</button>
        </div>
        <div id="adm-list" class="space-y-4"></div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            {n:"Basic Node", p:200, r:3}, {n:"Bronze Node", p:500, r:3.5}, {n:"Silver Node", p:1000, r:4},
            {n:"Gold Node", p:3000, r:4.5}, {n:"Platinum Node", p:5000, r:5}, {n:"Diamond Node", p:10000, r:6}
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(doc.exists && doc.data().banned) return alert("BANNED!");
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, lastReq: 0, tierName: "Inactive", banned: false });
            localStorage.setItem('mc_user', name);
            document.getElementById('u-display').innerText = name;
            document.getElementById('ref-link').innerText = `https://mintcrest.gold/?ref=${name}`;
            startSync(name);
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
            new TradingView.widget({"container_id": "tv_chart", "symbols": [["XAUUSD"]], "interval": "D", "theme": "dark", "style": "3", "hide_top_toolbar": true});
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                if(doc.exists) { 
                    user = doc.data();
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit||0).toLocaleString();
                    document.getElementById('v-tier').innerText = user.tierName;
                    updateTimer();
                }
            });
            db.collection("requests").where("user","==",name).orderBy("time","desc").onSnapshot(s => {
                const l = document.getElementById('history-list'); l.innerHTML = '';
                s.forEach(d => { const r = d.data(); l.innerHTML += `<div class="glass p-4 text-[9px] flex justify-between uppercase"><div>${r.type}<br><span class="opacity-40">${new Date(r.time).toLocaleDateString()}</span></div><div class="text-right font-black ${r.status==='pending'?'text-yellow-500':'text-green-500'}">₨ ${r.amount}<br>${r.status}</div></div>`; });
            });
        }

        function updateTimer() {
            if(user.lastReq > 0) {
                document.getElementById('timer-box').classList.remove('hidden');
                const run = () => {
                    const diff = (user.lastReq + 86400000) - Date.now();
                    if(diff > 0) {
                        document.getElementById('cycle-clock').innerText = new Date(diff).toISOString().substr(11, 8);
                        document.getElementById('cycle-bar').style.width = (100 - (diff/86400000*100)) + "%";
                        requestAnimationFrame(run);
                    } else { document.getElementById('cycle-clock').innerText = "READY FOR PROFIT"; }
                }; run();
            }
        }

        function renderPlans() {
            const g = document.getElementById('plans-grid'); g.innerHTML = '';
            plans.forEach(p => {
                g.innerHTML += `<div onclick="buyP(${p.p},'${p.n}')" class="glass p-6 flex justify-between items-center active:scale-95 transition-all">
                    <div><h4 class="font-black text-[10px] uppercase">${p.n}</h4><p class="text-[8px] text-green-500 font-bold uppercase">${p.r}% Daily ROI</p></div>
                    <div class="font-black text-sm">₨ ${p.p}</div>
                </div>`;
            });
        }

        async function buyP(p, n) {
            if(user.balance < p) return alert("Low Funds!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - p, tierName: n, lastReq: Date.now() });
            alert("Fleet Active! 😘"); changePage('home');
        }

        async function submitDep() {
            const a = document.getElementById('dep-a').value; const f = document.getElementById('dep-f').files[0];
            if(!a || !f) return alert("Missing Data!");
            const r = new FileReader(); r.readAsDataURL(f);
            r.onload = async () => {
                await db.collection("requests").add({ user: user.name, type: "deposit", amount: parseInt(a), proof: r.result, status: "pending", time: Date.now() });
                alert("Request Sent! 😘"); location.reload();
            };
        }

        async function submitWd() {
            const a = document.getElementById('wd-a').value; if(a > user.balance) return alert("Low Balance!");
            await db.collection("requests").add({ user: user.name, type: "withdraw", amount: parseInt(a), status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) });
            alert("Extraction Requested! ❤️"); location.reload();
        }

        async function applyPromo() {
            const c = document.getElementById('promo-code').value; if(!c) return;
            await db.collection("requests").add({ user: user.name, type: "Promo Request", code: c, status: "pending", time: Date.now(), amount: 0 });
            alert("Submitted! 😘");
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('active-tab'); }
        function copyRef() { navigator.clipboard.writeText(document.getElementById('ref-link').innerText); alert("Copied!"); }
        function adminTap() { tapCount++; if(tapCount>=4) { if(prompt("Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function syncAdm() {
            db.collection("requests").where("status","==","pending").onSnapshot(s => {
                const l = document.getElementById('adm-list'); l.innerHTML = '';
                s.forEach(doc => { const d = doc.data(); 
                    l.innerHTML += `<div class="glass p-5 text-[9px] font-black uppercase mb-4">
                        ${d.user} | ${d.type} | Rs ${d.amount}
                        <div class="flex gap-2 mt-4">
                            ${d.proof ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-gray-700 px-3 py-1 rounded">PROOFS</button>` : ''}
                            <button onclick="handleAdm('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-1 rounded">APPROVE</button>
                        </div>
                    </div>`;
                });
            });
        }
        async function handleAdm(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const d = await ref.get();
            let final = amt;
            if(type.includes('Promo')) { final = parseInt(prompt("Promo Amount:")); if(!final) return; }
            if(act==='approved' && (type==='deposit' || type.includes('Promo'))) await ref.update({ balance: (d.data().balance||0)+final });
            await db.collection("requests").doc(id).update({ status: act, amount: final }); alert("Saved!");
        }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        window.onload = () => { if(localStorage.getItem('mc_user')) { document.getElementById('user-name').value = localStorage.getItem('mc_user'); login(); } };
    </script>
</body>
</html>
