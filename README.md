<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global Asset Network</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;900&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #000205; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.01); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.05); border-radius: 2.5rem; }
        .page { display: none; animation: slideUp 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .nav-btn.active-tab { color: #3b82f6; transform: translateY(-5px); }
        .btn-glow { box-shadow: 0 0 20px rgba(59, 130, 246, 0.3); }
        .timer-glow { text-shadow: 0 0 10px rgba(234, 179, 8, 0.5); }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[9999] bg-[#000205] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-2 uppercase">MINT<span class="text-blue-600">CREST</span></h1>
            <p class="text-[8px] tracking-[0.6em] text-blue-400 mb-16 font-bold">INSTITUTIONAL GRADE VAULT</p>
            <div class="glass p-10 border-t-2 border-blue-600/50">
                <input type="text" id="user-name" placeholder="Enter Full Name" class="w-full bg-white/5 p-5 rounded-2xl mb-5 text-center font-bold outline-none border border-white/5 focus:border-blue-500">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[11px] uppercase tracking-widest active:scale-95 shadow-2xl">Initialize Node</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-8 px-2">
                <div><p class="text-[10px] text-gray-500 font-bold uppercase">Welcome Back,</p><h2 id="u-display" class="text-xl font-black italic">--</h2></div>
                <button onclick="changePage('history')" class="glass p-4 text-[10px] font-black uppercase">📜 History</button>
            </div>

            <div class="glass p-10 mb-6 bg-gradient-to-br from-blue-600/10 to-transparent border-l-4 border-blue-600">
                <p class="text-[9px] text-blue-400 font-black uppercase tracking-widest mb-1">Total Liquidity</p>
                <h2 class="text-5xl font-black tracking-tighter mb-6" id="v-bal">₨ 0</h2>
                <div class="flex justify-between items-center bg-black/40 p-4 rounded-2xl border border-white/5">
                    <div><p class="text-[8px] text-gray-500 font-bold uppercase">Active Yield</p><p class="text-xs font-black text-green-500" id="v-profit">₨ 0</p></div>
                    <div class="text-right"><p class="text-[8px] text-gray-500 font-bold uppercase">Fleet Level</p><p class="text-xs font-black" id="v-tier">Standard</p></div>
                </div>
            </div>

            <div id="timer-box" class="hidden glass p-6 mb-6 border border-yellow-500/20">
                <div class="flex justify-between items-center mb-2">
                    <span class="text-[9px] font-black text-yellow-500 uppercase">Cycle Countdown</span>
                    <span id="cycle-clock" class="text-xs font-black timer-glow">00:00:00</span>
                </div>
                <div class="w-full h-1 bg-white/5 rounded-full overflow-hidden">
                    <div id="cycle-bar" class="h-full bg-yellow-500 w-0 transition-all duration-1000"></div>
                </div>
                <p class="text-[7px] text-gray-500 mt-2 uppercase text-center italic">Profit will be injected after countdown expires.</p>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('deposit')" class="glass p-8 text-center bg-blue-600/5">📥<br><span class="text-[10px] font-black uppercase mt-2 block">Inject</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 text-center bg-red-600/5">📤<br><span class="text-[10px] font-black uppercase mt-2 block">Extract</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black uppercase text-sm mb-10 tracking-widest text-blue-500 italic">Investment Fleets</h2>
            <div id="plans-grid" class="space-y-4"></div>
        </div>

        <div id="p-history" class="page p-6">
            <h2 class="text-center font-black uppercase text-sm mb-8 tracking-widest">Transaction Logs</h2>
            <div id="history-list" class="space-y-3"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-4">
            <div class="glass p-8 border-t-4 border-yellow-500">
                <h3 class="text-center text-[11px] font-black uppercase mb-4 text-yellow-500 tracking-widest">Promotional Code</h3>
                <input type="text" id="promo-code" placeholder="Enter Valid Code" class="w-full bg-white/5 p-4 rounded-xl text-center font-bold mb-4 border border-white/5 outline-none uppercase">
                <button onclick="applyPromo()" class="w-full bg-yellow-600 py-4 rounded-xl font-black text-[10px] uppercase">Apply Reward</button>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <a href="https://chat.whatsapp.com/your-link" class="glass p-6 text-center text-green-500 font-black text-[10px] uppercase">WhatsApp Group</a>
                <button onclick="changePage('about')" class="glass p-6 text-center font-black text-[10px] uppercase">Company Info</button>
            </div>

            <div class="glass p-8 space-y-4">
                <h3 class="text-center text-[11px] font-black uppercase text-blue-500">Node Support</h3>
                <div id="chat-box" class="h-40 overflow-y-auto text-[10px] space-y-2 opacity-60"></div>
                <div class="flex gap-2">
                    <input type="text" id="msg-input" class="flex-1 bg-white/10 p-4 rounded-xl outline-none text-[10px]" placeholder="Ask Support...">
                    <button onclick="sendMsg()" class="bg-blue-600 px-6 rounded-xl font-black text-[10px]">SEND</button>
                </div>
            </div>
            <button onclick="logout()" class="w-full glass p-6 text-red-500 font-black uppercase text-[10px]">Terminate Session</button>
        </div>

        <div id="p-deposit" class="page p-6">
            <div class="glass p-8 border-t-8 border-blue-600">
                <h3 class="text-center font-black uppercase text-sm mb-8">Funding Request</h3>
                <select id="dep-method" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold outline-none border border-white/5">
                    <option value="EasyPaisa">EasyPaisa (03379827882)</option>
                    <option value="JazzCash">JazzCash (03705519562)</option>
                </select>
                <input type="number" id="dep-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold outline-none border border-white/5">
                <label class="block w-full bg-white/5 p-5 rounded-2xl border-2 border-dashed border-white/10 text-center cursor-pointer mb-6">
                    <span id="p-lab" class="text-[10px] font-black uppercase opacity-40">📸 Upload Proof Screenshot</span>
                    <input type="file" id="dep-file" class="hidden" accept="image/*" onchange="document.getElementById('p-lab').innerText='Proof Linked ✅'">
                </label>
                <button onclick="submitDep()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase btn-glow">Submit Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 border-t-8 border-red-600">
                <h3 class="text-center font-black uppercase text-sm mb-8">Payout Request</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold outline-none border border-white/5">
                <input type="text" id="wd-acc" placeholder="Account Number & Name" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center font-bold outline-none border border-white/5">
                <button onclick="submitWd()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase">Secure Payout</button>
            </div>
        </div>
        
        <div id="p-about" class="page p-6">
            <div class="glass p-10 space-y-6">
                <h2 class="text-center font-black uppercase text-blue-500">Corporate Identity</h2>
                <p class="text-[10px] opacity-60 leading-relaxed uppercase font-bold italic">MintCrest Gold is an ISO-9001 certified asset manager specializing in gold futures and AI-driven liquidity mining. We operate 400+ nodes globally to ensure 100% capital protection and guaranteed daily ROI for our retail partners.</p>
                <div class="border-t border-white/5 pt-6 text-[8px] space-y-2 opacity-40">
                    <p>REGISTERED: LONDON, UK (2022)</p>
                    <p>CERTIFICATE: #MCG-7786-99</p>
                    <p>AUDIT: BINANCE LIQUIDITY VERIFIED</p>
                </div>
                <button onclick="changePage('more')" class="w-full bg-white/5 py-4 rounded-xl text-[10px] font-black">BACK</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass fixed bottom-0 left-0 w-full p-8 flex justify-around items-center z-[500] rounded-t-[4rem] border-t border-white/5 bg-[#000205]/90">
        <button onclick="changePage('home')" id="n-home" class="nav-btn active-tab text-2xl transition-all">🏛️</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-2xl transition-all">💎</button>
        <button onclick="changePage('more')" id="n-more" class="nav-btn text-2xl transition-all">⚙️</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-black z-[10000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Master Console</h2>
            <button onclick="closeAdmin()" class="bg-red-500 px-4 py-1 rounded text-[10px] font-bold">EXIT</button>
        </div>
        <div id="adm-requests" class="space-y-4"></div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            {n:"Trial Node", p:200, r:3}, {n:"Bronze Node", p:500, r:3.5}, {n:"Silver Node", p:1000, r:4},
            {n:"Gold Node", p:3000, r:4.5}, {n:"Platinum Node", p:5000, r:5}, {n:"Diamond Node", p:10000, r:6},
            {n:"Elite VIP", p:20000, r:8}, {n:"Master Node", p:50000, r:10}, {n:"Global Partner", p:100000, r:15}
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(doc.exists && doc.data().banned) return alert("Account Suspended!");
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, lastReqTime: 0, activeP: 0, tierName: "Unlinked", banned: false });
            localStorage.setItem('mc_user', name); document.getElementById('u-display').innerText = name;
            startSync(name);
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
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
                s.forEach(d => { const r = d.data(); l.innerHTML += `<div class="glass p-4 text-[9px] flex justify-between uppercase"><div>${r.type} <br> <span class="opacity-40">${new Date(r.time).toLocaleDateString()}</span></div><div class="text-right font-black ${r.status==='pending'?'text-yellow-500':'text-green-500'}">₨ ${r.amount}<br>${r.status}</div></div>`; });
            });
            db.collection("messages").where("user","==",name).orderBy("time").onSnapshot(s => {
                const b = document.getElementById('chat-box'); b.innerHTML = '';
                s.forEach(d => { const m = d.data(); b.innerHTML += `<div class="${m.side==='admin'?'text-blue-500':'text-gray-500'}">● ${m.text}</div>`; });
                b.scrollTop = b.scrollHeight;
            });
        }

        function updateTimer() {
            if(user.lastReqTime > 0) {
                document.getElementById('timer-box').classList.remove('hidden');
                const run = () => {
                    const diff = (user.lastReqTime + 86400000) - Date.now();
                    if(diff > 0) {
                        document.getElementById('cycle-clock').innerText = new Date(diff).toISOString().substr(11, 8);
                        document.getElementById('cycle-bar').style.width = (100 - (diff/86400000*100)) + "%";
                        requestAnimationFrame(run);
                    } else {
                        document.getElementById('cycle-clock').innerText = "READY FOR PROFIT";
                        document.getElementById('cycle-bar').style.width = "100%";
                    }
                };
                run();
            }
        }

        function renderPlans() {
            const g = document.getElementById('plans-grid'); g.innerHTML = '';
            plans.forEach(p => {
                g.innerHTML += `<div onclick="buyFleet(${p.p},${p.r},'${p.n}')" class="glass p-6 flex justify-between items-center active:scale-95 transition-all">
                    <div><h4 class="font-black text-[10px] uppercase">${p.n}</h4><p class="text-[8px] text-green-500 font-black uppercase">${p.r}% Daily Payout</p></div>
                    <div class="font-black text-sm">₨ ${p.p}</div>
                </div>`;
            });
        }

        async function buyFleet(p, r, n) {
            if(user.balance < p) return alert("Insufficient Liquidity!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - p, tierName: n, activeP: p, lastReqTime: Date.now() });
            alert("Fleet Linked Successfully! 😘"); changePage('home');
        }

        async function submitDep() {
            const m = document.getElementById('dep-method').value; const a = document.getElementById('dep-amt').value;
            const f = document.getElementById('dep-file').files[0]; if(!a || !f) return alert("Fill all fields!");
            const reader = new FileReader(); reader.readAsDataURL(f);
            reader.onload = async () => {
                await db.collection("requests").add({ user: user.name, type: "deposit", amount: parseInt(a), method: m, proof: reader.result, status: "pending", time: Date.now() });
                alert("Request Sent! Admin is verifying. 😘"); location.reload();
            };
        }

        async function submitWd() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value;
            if(a > user.balance) return alert("Low Balance!");
            await db.collection("requests").add({ user: user.name, type: "withdraw", amount: parseInt(a), acc: acc, status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) });
            alert("Extraction Requested! ❤️"); location.reload();
        }

        async function applyPromo() {
            const c = document.getElementById('promo-code').value; if(!c) return;
            await db.collection("requests").add({ user: user.name, type: "Promo Request", code: c, status: "pending", time: Date.now(), amount: 0 });
            alert("Promo Code Submitted for Approval! 😘");
        }

        async function sendMsg() {
            const m = document.getElementById('msg-input').value; if(!m) return;
            await db.collection("messages").add({ user: user.name, text: m, side: "user", time: Date.now() });
            document.getElementById('msg-input').value = '';
        }

        function adminTap() { tapCount++; if(tapCount>=4) { if(prompt("Access:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function syncAdm() {
            db.collection("requests").where("status","==","pending").onSnapshot(s => {
                const l = document.getElementById('adm-requests'); l.innerHTML = '';
                s.forEach(doc => { const d = doc.data(); 
                    l.innerHTML += `<div class="glass p-5 text-[9px] font-black uppercase mb-4 border-l-4 ${d.type==='deposit'?'border-blue-500':'border-red-500'}">
                        ${d.user} | ${d.type} <br> Rs ${d.amount} ${d.code?'| Code: '+d.code:''}
                        <div class="flex gap-2 mt-4">
                            ${d.proof ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-gray-700 px-3 py-1 rounded">IMAGE</button>` : ''}
                            <button onclick="handleAdm('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-1 rounded">OK</button>
                        </div>
                    </div>`;
                });
            });
        }
        async function handleAdm(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const d = await ref.get();
            let final = amt;
            if(type.includes('Promo')) { final = parseInt(prompt("Promo Reward:")); if(!final) return; }
            if(act==='approved' && (type==='deposit' || type.includes('Promo'))) await ref.update({ balance: (d.data().balance||0)+final });
            await db.collection("requests").doc(id).update({ status: act, amount: final }); alert("Success!");
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        window.onload = () => { if(localStorage.getItem('mc_user')) { document.getElementById('user-name').value = localStorage.getItem('mc_user'); login(); } };
    </script>
</body>
</html>
