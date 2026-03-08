<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>MintCrest Gold | Omni-Terminal</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 100px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: slideUp 0.3s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 18px; font-weight: 800; }
        input { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 16px; width: 100%; outline: none; }
        .method-card.active { border-color: #3b82f6; background: rgba(59, 130, 246, 0.1); }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[#010409]/90 backdrop-blur-md border-b border-[#30363d] z-[5000]">
        <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
        <div class="text-right">
            <p id="top-bal" class="text-xs font-black text-blue-500 italic">₨ 0.00</p>
            <p id="u-display-name" class="text-[8px] opacity-40 uppercase font-black tracking-widest"></p>
        </div>
    </header>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#010409] flex flex-col items-center justify-center p-8">
        <div class="w-20 h-20 bg-blue-600 rounded-[2.5rem] mb-10 flex items-center justify-center text-4xl font-black text-white italic shadow-2xl">M</div>
        <div class="w-full max-w-[300px] space-y-4">
            <input type="text" id="user-name" placeholder="Username" class="text-center uppercase font-bold tracking-widest">
            <input type="password" id="user-pass" placeholder="Password" class="text-center">
            <button onclick="login()" class="w-full p-5 btn-prime uppercase text-[10px] tracking-widest shadow-xl">Secure Network Entry</button>
        </div>
    </section>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-8 rounded-[3.5rem] bg-gradient-to-br from-blue-600 to-blue-800 text-white shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60 italic">Current Liquidity</p>
                <h2 class="text-4xl font-black tracking-tighter mt-1" id="v-bal">₨ 0</h2>
                <div class="absolute -right-4 -bottom-4 opacity-10 text-8xl font-black italic">GOLD</div>
            </div>

            <div class="glass p-5 rounded-[2.5rem] text-center border-t-2 border-blue-500/20">
                <p class="text-[9px] font-black uppercase opacity-50 mb-1 italic">Mining Status</p>
                <div id="timer-display" class="text-xl font-black italic tracking-tighter text-blue-400">Scanning Node...</div>
            </div>

            <div class="glass p-5 rounded-[2.5rem] border border-white/5">
                <p class="text-[10px] font-black uppercase text-blue-500 mb-4 px-2 italic">📜 Activity History</p>
                <div id="history-list" class="space-y-3 max-h-48 overflow-y-auto italic"></div>
            </div>

            <div id="plans-grid" class="grid grid-cols-1 gap-6 pb-10"></div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div class="glass p-6 rounded-[2.5rem] space-y-4">
                <p class="text-center font-black text-blue-500 text-[10px] uppercase italic">Choose Deposit Channel</p>
                <div class="grid grid-cols-2 gap-3">
                    <div onclick="setPay('EasyPaisa', '03705519562')" id="m-ep" class="method-card glass p-4 rounded-2xl text-center cursor-pointer border-2 border-transparent">
                        <div class="text-2xl mb-1">🟢</div><p class="text-[10px] font-black">EasyPaisa</p>
                    </div>
                    <div onclick="setPay('JazzCash', '0300XXXXXXX')" id="m-jc" class="method-card glass p-4 rounded-2xl text-center cursor-pointer border-2 border-transparent">
                        <div class="text-2xl mb-1">🟡</div><p class="text-[10px] font-black">JazzCash</p>
                    </div>
                </div>
                <div id="pay-box" class="hidden p-4 bg-white/5 rounded-2xl border border-white/5 text-center">
                    <p id="pay-name" class="text-blue-500 font-black text-[10px] uppercase"></p>
                    <p id="pay-num" class="text-xl font-black tracking-widest my-1"></p>
                </div>
                <input type="number" id="dep-amt" placeholder="Amount (₨)">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <label class="block w-full p-5 border-2 border-dashed border-blue-500/20 rounded-2xl bg-blue-500/5 text-center cursor-pointer">
                    <span id="proof-label" class="text-[10px] font-black uppercase text-blue-400 italic">📸 Select Proof Screenshot</span>
                    <input type="file" id="proof-input" accept="image/*" class="hidden" onchange="handleImg(this, 'proof-label')">
                </label>
                <button onclick="submitDep()" class="w-full p-5 btn-prime uppercase italic text-[10px] tracking-widest">Submit Request</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-5">
                <input type="number" id="w-amt" placeholder="Withdrawal Amount">
                <input type="text" id="w-acc" placeholder="Account No & Method">
                <button onclick="submitWith()" class="w-full p-5 btn-prime uppercase italic text-[10px]">Request Withdrawal</button>
            </div>
        </div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-black z-[100000] hidden overflow-y-auto p-4 italic">
        <div class="flex justify-between items-center mb-8 sticky top-0 bg-black/90 py-4 border-b border-white/5">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Master Console 👑</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-4 py-2 rounded-xl text-[9px] font-black uppercase">Exit</button>
        </div>

        <div class="grid grid-cols-2 gap-4 mb-8">
            <div class="glass p-5 rounded-[2rem] text-center"><p id="adm-u-count" class="text-xl font-black">0</p><p class="text-[8px] opacity-40 uppercase">Users</p></div>
            <div class="glass p-5 rounded-[2rem] text-center text-green-500"><p id="adm-total-cash" class="text-xl font-black">0</p><p class="text-[8px] opacity-40 uppercase">Total Vol</p></div>
        </div>

        <div class="glass p-6 rounded-[2.5rem] mb-8 border-l-4 border-blue-500">
            <p class="text-[9px] font-black uppercase mb-4 text-blue-400">Broadcast New Node Plan</p>
            <div class="space-y-3">
                <input id="adm-n-name" placeholder="Node Name">
                <input id="adm-n-price" type="number" placeholder="Price (₨)">
                <input id="adm-n-profit" type="number" placeholder="Profit %">
                <label class="block p-4 border border-white/10 rounded-xl text-center bg-white/5 cursor-pointer">
                    <span id="adm-img-label" class="text-[9px] font-black uppercase">Select Direct Photo</span>
                    <input type="file" id="adm-img-input" accept="image/*" class="hidden" onchange="handleImg(this, 'adm-img-label')">
                </label>
                <button onclick="addPlan()" class="w-full bg-blue-600 p-4 rounded-xl font-black text-[10px] uppercase tracking-widest">Launch Plan</button>
            </div>
            <div id="adm-plans-list" class="mt-6 space-y-2 border-t border-white/5 pt-4"></div>
        </div>

        <div class="mb-10"><h4 class="text-[9px] font-black uppercase text-yellow-500 mb-4 tracking-widest">⚡ Pending Requests</h4><div id="adm-requests-list" class="space-y-4"></div></div>

        <div class="glass rounded-[2.5rem] mb-20 overflow-hidden">
            <div class="p-5 bg-white/5 text-[9px] font-black uppercase text-blue-400">Identity Control & Bonus</div>
            <div class="overflow-x-auto"><table class="w-full text-left text-[11px]"><tbody id="adm-users-list"></tbody></table></div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-white/5 p-5 flex justify-around items-center rounded-t-[2.5rem] z-[4000]">
        <button onclick="changePage('home')" id="n-home" class="nav-active flex flex-col items-center gap-1 opacity-40">🏠<span class="text-[8px] font-black tracking-widest">HOME</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="flex flex-col items-center gap-1 opacity-40">📥<span class="text-[8px] font-black tracking-widest">DEPOSIT</span></button>
        <button onclick="changePage('withdraw')" id="n-withdraw" class="flex flex-col items-center gap-1 opacity-40">📤<span class="text-[8px] font-black tracking-widest">PAYOUT</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tempImg = ""; let tap = 0;

        // AUTH & SYNC
        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const p = document.getElementById('user-pass').value.trim();
            if(!n || !p) return alert("Fill data.");
            const ref = db.collection("users").doc(n);
            const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, password: p, balance: 0, time: Date.now() });
            else if(d.data().password !== p) return alert("Wrong access key.");
            localStorage.setItem('mc_user', n);
            enter();
        }

        function enter() {
            const n = localStorage.getItem('mc_user');
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('u-display-name').innerText = "@"+n;
            sync(n);
        }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
            });
            syncHistory(n);
            syncPlans();
            startTimer();
        }

        // TIMER & MINING
        function startTimer() {
            setInterval(() => {
                const el = document.getElementById('timer-display');
                if(!user || !user.activePlan || user.activePlan.status !== 'active') { el.innerText = "No Active Node"; return; }
                const diff = user.activePlan.expiresAt - Date.now();
                if(diff <= 0) { el.innerText = "Mining Complete! Auto-Requesting..."; submitProfit(); return; }
                const h = Math.floor(diff/3600000); const m = Math.floor((diff%3600000)/60000); const s = Math.floor((diff%60000)/1000);
                el.innerHTML = `<span class="text-blue-400 font-black">${h}h ${m}m ${s}s</span>`;
            }, 1000);
        }

        async function submitProfit() {
            const p = user.activePlan;
            const amt = p.price + (p.price * p.profit / 100);
            await db.collection("requests").add({ user: user.name, type: 'profit_claim', amount: amt, status: 'pending', time: Date.now() });
            await db.collection("users").doc(user.name).update({ "activePlan.status": 'expired' });
        }

        // DEPOSIT LOGIC
        function setPay(n, num) {
            document.querySelectorAll('.method-card').forEach(c => c.classList.remove('active'));
            document.getElementById(n==='EasyPaisa'?'m-ep':'m-jc').classList.add('active');
            document.getElementById('pay-box').classList.remove('hidden');
            document.getElementById('pay-name').innerText = n;
            document.getElementById('pay-num').innerText = num;
        }

        function handleImg(input, labelId) {
            const file = input.files[0];
            const reader = new FileReader();
            reader.onloadend = () => { tempImg = reader.result; document.getElementById(labelId).innerText = "✅ File Selected"; };
            if(file) reader.readAsDataURL(file);
        }

        async function submitDep() {
            const a = document.getElementById('dep-amt').value; const t = document.getElementById('dep-tid').value;
            if(!a || !t || !tempImg) return alert("Proof & Details missing.");
            await db.collection("requests").add({ user: user.name, type: 'deposit', amount: parseInt(a), tid: t, img: tempImg, status: 'pending', time: Date.now() });
            alert("Request sent, sweetie!"); location.reload();
        }

        // ADMIN CORE
        function adminTap() { tap++; if(tap>=5) { if(prompt("Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tap=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        function syncAdm() {
            db.collection("users").onSnapshot(s => {
                document.getElementById('adm-u-count').innerText = s.size;
                const l = document.getElementById('adm-users-list'); l.innerHTML = ''; let vol = 0;
                s.forEach(d => { const u = d.data(); vol += (u.balance||0);
                    l.innerHTML += `<tr class="border-b border-white/5"><td class="p-4 text-blue-400">@${u.name}</td><td class="p-4 font-black">₨ ${u.balance}</td><td class="p-4"><input id="b-${u.name}" placeholder="Bonus" class="!p-1 !w-16 !text-[9px]"><button onclick="giveBonus('${u.name}')" class="ml-2 text-green-500 font-bold uppercase text-[8px]">GIVE</button></td></tr>`;
                });
                document.getElementById('adm-total-cash').innerText = "₨ " + vol.toLocaleString();
            });

            db.collection("plans").onSnapshot(s => {
                const l = document.getElementById('adm-plans-list'); l.innerHTML = '';
                s.forEach(d => { l.innerHTML += `<div class="flex justify-between p-2 bg-white/5 rounded-lg text-[10px]"><p>${d.data().name}</p><button onclick="delPlan('${d.id}')" class="text-red-500 font-black">DEL</button></div>`; });
            });

            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const q = document.getElementById('adm-requests-list'); q.innerHTML = '';
                s.forEach(d => { const r = d.data();
                    q.innerHTML += `<div class="glass p-5 rounded-[2.5rem] border-l-4 border-yellow-500 flex justify-between items-center">
                    <div><p class="text-[9px] font-bold">@${r.user} | ${r.type}</p><p class="text-xl font-black italic">₨ ${r.amount}</p>${r.img?`<a href="${r.img}" target="_blank" class="text-blue-400 text-[8px] underline">VIEW PROOF</a>`:''}</div>
                    <button onclick="appv('${d.id}','${r.user}',${r.amount},'${r.type}')" class="bg-blue-600 px-5 py-2 rounded-xl text-[9px] font-black uppercase">Approve</button></div>`;
                });
            });
        }

        async function addPlan() {
            const n = document.getElementById('adm-n-name').value; const p = document.getElementById('adm-n-price').value; const pr = document.getElementById('adm-n-profit').value;
            if(!n || !p || !tempImg) return alert("Fill all fields.");
            await db.collection("plans").add({ name:n, price:parseInt(p), profit:parseInt(pr), img:tempImg, time:Date.now() });
            alert("Plan Broadcasted!");
        }

        async function delPlan(id) { if(confirm("Delete plan?")) await db.collection("plans").doc(id).delete(); }
        async function appv(id, u, a, t) { if(t !== 'withdrawal') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) }); await db.collection("requests").doc(id).update({ status: 'approved' }); alert("Done!"); }
        async function giveBonus(u) { const a = document.getElementById('b-'+u).value; if(!a) return; await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(parseInt(a)) }); alert("Bonus Sent!"); }

        // UTILS
        function syncHistory(n) {
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const l = document.getElementById('history-list'); l.innerHTML = '';
                s.forEach(d => { const r = d.data(); l.innerHTML += `<div class="flex justify-between p-3 bg-white/5 rounded-xl border-l-2 ${r.status==='approved'?'border-green-500':'border-yellow-500'}"><p class="text-[10px] font-black uppercase">${r.type}</p><div class="text-right"><p class="text-[10px] font-black italic">₨ ${r.amount}</p><p class="text-[8px] font-black uppercase opacity-40">${r.status}</p></div></div>`; });
            });
        }

        function syncPlans() {
            db.collection("plans").orderBy("time", "desc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => { const p = doc.data(); g.innerHTML += `<div class="glass rounded-[2.5rem] overflow-hidden border-b-4 border-blue-500"><img src="${p.img}" class="w-full h-44 object-cover"><div class="p-6"><div><p class="text-[9px] opacity-40 uppercase font-black">${p.name}</p><h3 class="text-2xl font-black text-blue-500">₨ ${p.price}</h3><p class="text-blue-500 text-[10px] font-bold mt-1">${p.profit}% Profit / 24H</p></div><button onclick="buy('${p.name}',${p.price},${p.profit})" class="w-full mt-4 btn-prime p-4 text-[10px] uppercase italic tracking-widest">Initialise Node</button></div></div>`; });
            });
        }

        async function buy(n, p, pr) {
            if(user.balance < p) return alert("Low balance.");
            if(user.activePlan && user.activePlan.status === 'active') return alert("1 node limit.");
            const exp = Date.now() + (24*60*60*1000);
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-p), activePlan: { name:n, price:p, profit:pr, expiresAt:exp, status:'active' } });
            alert("Node Activated!");
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.add('opacity-40')); document.getElementById('n-'+p).classList.remove('opacity-40'); window.scrollTo(0,0); }
        async function submitWith() { const a = document.getElementById('w-amt').value; if(user.balance < a) return alert("Low balance."); await db.collection("users").doc(user.name).update({balance:firebase.firestore.FieldValue.increment(-parseInt(a))}); await db.collection("requests").add({user:user.name, type:'withdrawal', amount:parseInt(a), status:'pending', time:Date.now()}); alert("Payout requested!"); }
        
        window.onload = () => { if(localStorage.getItem('mc_user')) enter(); }
    </script>
</body>
</html>
