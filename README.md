<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>MintCrest Gold | Unlimited Terminal</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: rgba(13, 17, 23, 0.8); --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f0f6fc; padding-bottom: 100px; -webkit-tap-highlight-color: transparent; }
        .glass { background: var(--card); border: 1px solid rgba(255,255,255,0.05); backdrop-filter: blur(15px); }
        .page { display: none; animation: slideUp 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1d4ed8, #3b82f6); color: white; border-radius: 22px; font-weight: 800; transition: 0.3s; }
        .btn-prime:active { transform: scale(0.95); }
        input, select { background: rgba(22, 27, 34, 0.8); border: 1px solid rgba(255,255,255,0.1); color: white; padding: 15px; border-radius: 20px; width: 100%; outline: none; }
        .admin-card { background: #0d1117; border: 1px solid #30363d; border-radius: 28px; padding: 20px; margin-bottom: 15px; }
        .whatsapp-float { position: fixed; bottom: 120px; right: 20px; background: #25d366; width: 55px; height: 55px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 26px; z-index: 3000; box-shadow: 0 8px 25px rgba(37, 211, 102, 0.3); }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body>

    <a href="https://chat.whatsapp.com/YOUR_GROUP_LINK" class="whatsapp-float">💬</a>

    <header class="p-6 flex justify-between items-center sticky top-0 bg-[#010409]/90 backdrop-blur-md z-[5000] border-b border-white/5">
        <div onclick="adminTap()" class="flex items-center gap-3 cursor-pointer">
            <div class="w-11 h-11 bg-blue-600 rounded-[1.2rem] flex items-center justify-center font-black italic shadow-lg shadow-blue-500/20">M</div>
            <div><p class="text-[11px] font-black uppercase tracking-tight text-blue-500">MintCrest Gold</p><p class="text-[8px] opacity-40 uppercase font-bold tracking-widest">Unlimited v3</p></div>
        </div>
        <div class="text-right">
            <p id="top-bal" class="text-sm font-black text-blue-400 italic">₨ 0.00</p>
            <p id="u-display" class="text-[8px] opacity-40 uppercase font-black"></p>
        </div>
    </header>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#010409] flex flex-col items-center justify-center p-10">
        <div class="w-24 h-24 bg-blue-600 rounded-[2.8rem] mb-12 flex items-center justify-center text-5xl font-black italic shadow-2xl shadow-blue-600/40">M</div>
        <div class="w-full max-w-[320px] space-y-4">
            <input type="text" id="user-name" placeholder="ENTER USERNAME" class="text-center font-black tracking-widest uppercase">
            <input type="password" id="user-pass" placeholder="ENTER PASSWORD" class="text-center">
            <button onclick="login()" class="w-full p-5 btn-prime uppercase text-[11px] tracking-widest shadow-xl">Start Session</button>
        </div>
    </section>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-10 rounded-[3.8rem] bg-gradient-to-br from-blue-600 to-blue-900 text-white shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60 italic">Available Balance</p>
                <h2 class="text-5xl font-black tracking-tighter mt-1" id="v-bal">₨ 0</h2>
                <div class="absolute -right-10 -bottom-10 opacity-10 text-[10rem] font-black italic">G</div>
            </div>

            <div class="glass p-6 rounded-[2.8rem] space-y-4">
                <p class="text-[10px] font-black uppercase text-blue-500 italic flex items-center gap-2"><span>🎧</span> Live Support Terminal</p>
                <div id="chat-display" class="h-40 overflow-y-auto space-y-3 p-2 text-[11px]"></div>
                <div class="flex gap-2">
                    <input id="chat-msg" placeholder="Write a message..." class="!rounded-full !py-3 !px-5 !text-xs">
                    <button onclick="sendChat()" class="btn-prime px-6 rounded-full italic text-[10px]">SEND</button>
                </div>
            </div>

            <div id="plans-grid" class="grid grid-cols-1 gap-6 pb-20"></div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div class="glass p-7 rounded-[3rem] space-y-4">
                <p class="text-center font-black text-blue-500 text-[10px] uppercase mb-2">Select Channel</p>
                <div class="grid grid-cols-2 gap-3">
                    <div onclick="setPay('EasyPaisa', '03379827882')" class="glass p-4 rounded-3xl text-center border-2 border-transparent hover:border-blue-500 cursor-pointer transition-all">
                        <p class="text-[11px] font-black italic">EasyPaisa</p>
                    </div>
                    <div onclick="setPay('JazzCash', '03705519562')" class="glass p-4 rounded-3xl text-center border-2 border-transparent hover:border-blue-500 cursor-pointer transition-all">
                        <p class="text-[11px] font-black italic">JazzCash</p>
                    </div>
                </div>
                <div id="pay-box" class="hidden p-6 bg-blue-600/10 rounded-[2rem] border border-blue-500/20 text-center animate-pulse">
                    <p id="pay-name" class="text-blue-500 font-black text-[10px] uppercase"></p>
                    <p id="pay-num" class="text-2xl font-black tracking-widest my-1"></p>
                </div>
                <input type="number" id="dep-amt" placeholder="Amount (₨)">
                <input type="text" id="dep-tid" placeholder="TID (Transaction ID)">
                <label class="block w-full p-7 border-2 border-dashed border-blue-500/20 rounded-[2rem] bg-blue-500/5 text-center cursor-pointer">
                    <span id="proof-label" class="text-[10px] font-black uppercase text-blue-400">📸 Attach Payment Proof</span>
                    <input type="file" id="proof-input" accept="image/*" class="hidden" onchange="handleImg(this, 'proof-label')">
                </label>
                <button onclick="submitDep()" class="w-full p-5 btn-prime uppercase italic text-[11px] tracking-widest">Transmit Assets</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-4">
                <p class="text-[10px] font-black text-blue-500 uppercase italic">Payout Terminal</p>
                <input type="number" id="w-amt" placeholder="Amount (₨)">
                <input type="text" id="w-acc" placeholder="Account Name & Number">
                <select id="w-method">
                    <option value="EasyPaisa">EasyPaisa</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="SadaPay">SadaPay</option>
                </select>
                <button onclick="submitWith()" class="w-full p-5 btn-prime uppercase italic text-[11px]">Request Withdrawal</button>
            </div>
        </div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[200000] hidden overflow-y-auto p-4 italic">
        <div class="flex justify-between items-center mb-8 sticky top-0 bg-[#010409]/95 py-4 border-b border-white/10 z-[200001]">
            <h2 class="text-xl font-black text-blue-500 uppercase tracking-tighter">God Console 👑</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-6 py-2 rounded-2xl text-[10px] font-black uppercase">Close</button>
        </div>

        <div class="grid grid-cols-3 gap-3 mb-8">
            <div onclick="admSection('req')" class="glass p-5 rounded-[2rem] text-center flex flex-col items-center gap-1">
                <span class="text-2xl">⚡</span><p class="text-[8px] font-black uppercase">Requests</p>
            </div>
            <div onclick="admSection('user')" class="glass p-5 rounded-[2rem] text-center flex flex-col items-center gap-1">
                <span class="text-2xl">👤</span><p class="text-[8px] font-black uppercase">Users</p>
            </div>
            <div onclick="admSection('plan')" class="glass p-5 rounded-[2rem] text-center flex flex-col items-center gap-1">
                <span class="text-2xl">💎</span><p class="text-[8px] font-black uppercase">Plan</p>
            </div>
        </div>

        <div id="adm-req" class="adm-tab space-y-4">
            <h3 class="text-[10px] font-black text-yellow-500 uppercase mb-4 tracking-widest">Pending Approvals</h3>
            <div id="adm-req-list" class="space-y-4"></div>
        </div>

        <div id="adm-user" class="adm-tab hidden space-y-4">
            <h3 class="text-[10px] font-black text-blue-400 uppercase mb-4 tracking-widest">Network Database</h3>
            <div id="adm-user-list" class="space-y-3"></div>
        </div>

        <div id="adm-plan" class="adm-tab hidden space-y-4">
            <div class="admin-card">
                <h3 class="text-[10px] font-black text-green-500 uppercase mb-4">Add New Node</h3>
                <div class="space-y-3 italic">
                    <input id="adm-n-name" placeholder="Plan Name">
                    <input id="adm-n-price" type="number" placeholder="Price (₨)">
                    <input id="adm-n-profit" type="number" placeholder="Daily Profit %">
                    <label class="block p-5 border border-white/10 rounded-2xl text-center bg-white/5 cursor-pointer">
                        <span id="adm-img-lbl" class="text-[10px] font-black uppercase">Upload Plan Photo</span>
                        <input type="file" accept="image/*" class="hidden" onchange="handleImg(this, 'adm-img-lbl')">
                    </label>
                    <button onclick="addPlan()" class="w-full bg-blue-600 p-4 rounded-2xl font-black uppercase text-[10px]">Launch Plan</button>
                </div>
            </div>
        </div>

        <div class="mt-10 pb-24">
            <h3 class="text-[10px] font-black text-purple-500 uppercase mb-4 tracking-widest">Message Center</h3>
            <div id="adm-chat-list" class="space-y-3"></div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-white/5 p-6 flex justify-around items-center rounded-t-[3.5rem] z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1">🏠<span class="text-[8px] font-black">HOME</span></button>
        <button onclick="changePage('wallet')" class="flex flex-col items-center gap-1">📥<span class="text-[8px] font-black">DEPOSIT</span></button>
        <button onclick="changePage('withdraw')" class="flex flex-col items-center gap-1">📤<span class="text-[8px] font-black">PAYOUT</span></button>
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
        const db = firebase.firestore();
        let user = null; let tempImg = ""; let tap = 0;

        // AUTH & CORE
        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const p = document.getElementById('user-pass').value.trim();
            if(!n || !p) return alert("Credentials required, sweetie!");
            const ref = db.collection("users").doc(n);
            const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, password: p, balance: 0, time: Date.now() });
            else if(d.data().password !== p) return alert("Security key mismatch!");
            localStorage.setItem('mc_user', n); enter();
        }

        function enter() {
            const n = localStorage.getItem('mc_user');
            document.getElementById('auth-ui').style.display = 'none';
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('u-display').innerText = "@" + n;
            sync(n);
        }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
            });
            syncPlans(); syncChat(n);
        }

        // IMAGE HANDLER
        function handleImg(input, lbl) {
            const reader = new FileReader();
            reader.onloadend = () => { tempImg = reader.result; document.getElementById(lbl).innerText = "✅ IMAGE READY"; };
            if(input.files[0]) reader.readAsDataURL(input.files[0]);
        }

        // USER ACTIONS
        function setPay(n, num) {
            document.getElementById('pay-box').classList.remove('hidden');
            document.getElementById('pay-name').innerText = n;
            document.getElementById('pay-num').innerText = num;
        }

        async function submitDep() {
            const a = document.getElementById('dep-amt').value; const t = document.getElementById('dep-tid').value;
            if(!a || !t || !tempImg) return alert("Please fill all details, sweetie!");
            await db.collection("requests").add({ user: user.name, type: 'deposit', amount: parseInt(a), tid: t, img: tempImg, status: 'pending', time: Date.now() });
            alert("Deposit broadcasted to Admin!"); location.reload();
        }

        async function submitWith() {
            const a = document.getElementById('w-amt').value; const d = document.getElementById('w-acc').value;
            if(!a || a > user.balance) return alert("Insufficient Balance!");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").add({ user: user.name, type: 'withdrawal', amount: parseInt(a), details: d, status: 'pending', time: Date.now() });
            alert("Withdrawal request sent!");
        }

        // CHAT LOGIC
        function syncChat(n) {
            db.collection("chat").where("user", "==", n).orderBy("time", "asc").onSnapshot(s => {
                const disp = document.getElementById('chat-display'); disp.innerHTML = '';
                s.forEach(doc => { const m = doc.data(); disp.innerHTML += `<div class="p-3 rounded-2xl ${m.sender==='admin'?'bg-blue-600/20 text-blue-400 self-start':'bg-white/5 self-end'}">${m.msg}</div>`; });
                disp.scrollTop = disp.scrollHeight;
            });
        }
        async function sendChat() {
            const m = document.getElementById('chat-msg').value; if(!m) return;
            await db.collection("chat").add({ user: user.name, msg: m, sender: user.name, time: Date.now(), status: 'unread' });
            document.getElementById('chat-msg').value = '';
        }

        // ADMIN GOD PANEL
        function adminTap() { tap++; if(tap>=5) { if(prompt("GOD KEY:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tap=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function admSection(id) { document.querySelectorAll('.adm-tab').forEach(t => t.classList.add('hidden')); document.getElementById('adm-'+id).classList.remove('hidden'); }

        function syncAdm() {
            // USERS CARDS
            db.collection("users").onSnapshot(s => {
                const l = document.getElementById('adm-user-list'); l.innerHTML = '';
                s.forEach(d => { const u = d.data();
                    l.innerHTML += `<div class="admin-card flex justify-between items-center">
                        <div><p class="font-black text-xs">@${u.name}</p><p class="text-blue-500 font-bold text-[10px]">₨ ${u.balance}</p></div>
                        <div class="flex gap-2"><input id="b-${u.name}" placeholder="Bonus" class="!w-16 !p-2 !text-[10px] glass"><button onclick="giveBonus('${u.name}')" class="bg-green-600 px-3 py-2 rounded-xl text-[9px] font-black uppercase">Add</button></div>
                    </div>`;
                });
            });

            // REQUESTS CARDS
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const q = document.getElementById('adm-req-list'); q.innerHTML = '';
                s.forEach(d => { const r = d.data();
                    q.innerHTML += `<div class="admin-card border-l-4 border-yellow-500">
                        <div class="flex justify-between mb-2 text-[10px] font-black uppercase"><span>@${r.user}</span><span class="text-yellow-500">${r.type}</span></div>
                        <h4 class="text-xl font-black italic">₨ ${r.amount}</h4>
                        <div class="mt-2 space-y-1">${r.tid ? `<p class="text-[9px] opacity-40">TID: ${r.tid}</p>` : `<p class="text-[9px] opacity-40">${r.details}</p>`}</div>
                        <div class="flex gap-2 mt-4">
                            ${r.img ? `<button onclick="window.open('${r.img}')" class="flex-1 bg-white/5 p-3 rounded-xl text-[9px] font-black uppercase">View Photo</button>` : ''}
                            <button onclick="appv('${d.id}','${r.user}',${r.amount},'${r.type}')" class="flex-1 bg-blue-600 p-3 rounded-xl text-[9px] font-black uppercase">Approve</button>
                        </div>
                    </div>`;
                });
            });

            // CHAT INBOX
            db.collection("chat").where("status", "==", "unread").onSnapshot(s => {
                const l = document.getElementById('adm-chat-list'); l.innerHTML = '';
                s.forEach(d => { const m = d.data(); if(m.sender === 'admin') return;
                    l.innerHTML += `<div class="admin-card border-l-4 border-purple-500"><p class="text-[11px] font-bold">@${m.user}: ${m.msg}</p><button onclick="replyChat('${m.user}','${d.id}')" class="mt-3 text-purple-400 text-[10px] font-black uppercase">REPLY USER</button></div>`;
                });
            });
        }

        async function appv(id, u, a, t) { 
            if(t === 'deposit' || t === 'profit') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'approved' }); alert("Success!"); 
        }
        async function giveBonus(u) { const a = document.getElementById('b-'+u).value; if(!a) return; await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(parseInt(a)) }); alert("Bonus Added!"); }
        async function replyChat(u, id) { const r = prompt("Your Reply:"); if(!r) return; await db.collection("chat").add({ user: u, msg: r, sender: 'admin', time: Date.now() }); await db.collection("chat").doc(id).update({ status: 'read' }); }

        // PLANS LOGIC
        function syncPlans() {
            db.collection("plans").orderBy("time", "desc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => { const p = doc.data(); g.innerHTML += `<div class="glass rounded-[3.5rem] overflow-hidden border-b-4 border-blue-600 shadow-xl"><img src="${p.img}" class="w-full h-48 object-cover"><div class="p-8"><p class="text-[10px] opacity-40 uppercase font-black tracking-widest">${p.name}</p><h3 class="text-3xl font-black text-blue-500">₨ ${p.price}</h3><p class="text-blue-400 text-[10px] font-bold mt-1 uppercase italic">Profit: ${p.profit}% Every 24H</p><button onclick="buyNode('${p.name}',${p.price},${p.profit})" class="w-full mt-6 btn-prime p-5 text-[11px] uppercase italic tracking-widest">Initialise Node</button></div></div>`; });
            });
        }
        async function buyNode(n, p, pr) {
            if(user.balance < p) return alert("Sweetie, insufficient balance!");
            const daily = (p * pr / 100);
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-p) });
            await db.collection("requests").add({ user: user.name, type: 'profit', amount: daily, details: 'Daily Profit Node: '+n, status: 'pending', time: Date.now() });
            alert("Node Activated! Daily profit request transmitted.");
        }
        async function addPlan() {
            const n = document.getElementById('adm-n-name').value; const p = document.getElementById('adm-n-price').value; const pr = document.getElementById('adm-n-profit').value;
            if(!n || !p || !tempImg) return alert("Missing plan data!");
            await db.collection("plans").add({ name:n, price:parseInt(p), profit:parseInt(pr), img:tempImg, time:Date.now() });
            alert("Plan Broadcasted successfully!");
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); window.scrollTo(0,0); }
        window.onload = () => { if(localStorage.getItem('mc_user')) enter(); }
    </script>
</body>
</html>
