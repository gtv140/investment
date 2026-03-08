<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>MintCrest Gold | Professional Node</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        .light { --bg: #f8fafc; --card: #ffffff; --text: #0f172a; --border: #e2e8f0; --accent: #2563eb; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 110px; overflow-x: hidden; transition: 0.3s; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 18px; font-weight: 800; transition: 0.2s; }
        input, textarea { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 16px; width: 100%; outline: none; font-size: 13px; font-weight: 600; }
        .nav-active { color: var(--accent); opacity: 1 !important; transform: scale(1.1); }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] z-[5000]">
        <div class="flex items-center gap-2">
            <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
            <div class="leading-none">
                <p class="font-black text-[10px] uppercase tracking-tighter">MintCrest</p>
                <p class="text-blue-500 text-xs font-black italic">GOLD NODE</p>
            </div>
        </div>
        <div class="flex items-center gap-3">
            <button onclick="toggleTheme()" class="w-9 h-9 glass rounded-xl flex items-center justify-center text-sm">🌓</button>
            <div id="top-bal" class="text-[11px] font-black bg-blue-600/10 text-blue-500 px-4 py-2 rounded-xl border border-blue-500/20 italic">₨ 0</div>
        </div>
    </header>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8">
        <div class="w-24 h-24 bg-blue-600 rounded-[2.5rem] mb-10 flex items-center justify-center text-5xl font-black text-white italic shadow-2xl">M</div>
        <div class="w-full max-w-[320px] space-y-4">
            <input type="text" id="user-name" placeholder="Username" class="text-center uppercase tracking-widest">
            <input type="password" id="user-pass" placeholder="Password" class="text-center">
            <button onclick="login()" class="w-full p-5 btn-prime uppercase text-[11px] tracking-[0.2em] shadow-xl text-white">Login to System</button>
        </div>
    </section>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="bg-blue-600/5 border border-blue-500/10 p-2 rounded-xl overflow-hidden whitespace-nowrap">
                <p id="v-news" class="animate-marquee inline-block text-[9px] font-black uppercase text-blue-400 italic">Global Market is Live. Start Mining to Earn Daily Yield.</p>
            </div>

            <div class="p-8 rounded-[3.5rem] bg-gradient-to-br from-blue-600 to-blue-800 text-white shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60 tracking-widest italic">Total Balance</p>
                <h2 class="text-4xl font-black tracking-tighter mt-1" id="v-bal">₨ 0.00</h2>
            </div>

            <div class="flex gap-2">
                <a href="https://chat.whatsapp.com/YOUR_LINK" class="flex-1 glass p-4 rounded-2xl flex items-center justify-center gap-2 border-l-4 border-green-500">
                    <span class="text-xl">💬</span>
                    <span class="text-[9px] font-black uppercase">Official Group</span>
                </a>
                <button onclick="changePage('support')" class="flex-1 glass p-4 rounded-2xl flex items-center justify-center gap-2 border-l-4 border-blue-500">
                    <span class="text-xl">🎧</span>
                    <span class="text-[9px] font-black uppercase">Live Support</span>
                </button>
            </div>

            <div id="plans-grid" class="grid grid-cols-1 gap-5 pb-10"></div>
        </div>

        <div id="p-support" class="page space-y-6">
            <div class="text-center py-4 text-blue-500 text-2xl font-black uppercase italic">Help Desk</div>
            <div id="chat-box" class="glass p-4 rounded-[2rem] h-[300px] overflow-y-auto space-y-3 mb-4">
                </div>
            <div class="glass p-5 rounded-[2.5rem] flex gap-2">
                <input id="support-msg" placeholder="Type your message..." class="flex-1 !border-none">
                <button onclick="sendSupportMsg()" class="btn-prime px-6 py-3 text-[10px] uppercase">Send</button>
            </div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-5">
                <p class="text-[10px] font-black uppercase text-blue-500 text-center tracking-widest">Deposit Panel</p>
                <input type="number" id="dep-amt" placeholder="Amount (₨)">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <div class="relative">
                    <input type="file" id="dep-img" accept="image/*" class="hidden" onchange="updateFileName(this)">
                    <label for="dep-img" class="w-full flex items-center justify-center gap-3 p-5 border-2 border-dashed border-blue-500/30 rounded-2xl bg-blue-500/5 cursor-pointer">
                        <span id="file-name-label" class="text-[10px] font-black uppercase text-blue-400">📸 Upload Screenshot</span>
                    </label>
                </div>
                <button id="dep-btn" onclick="submitDepWithImage()" class="w-full p-5 btn-prime uppercase italic">Submit Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-5">
                <input type="number" id="w-amt" placeholder="Withdrawal Amount">
                <input type="text" id="w-acc" placeholder="Account Details">
                <button onclick="submitWith()" class="w-full p-5 btn-prime uppercase italic">Withdraw Now</button>
            </div>
        </div>

        <div id="p-about" class="page space-y-6">
            <div class="glass p-10 rounded-[4rem] text-center">
                <button onclick="logout()" class="w-full p-6 bg-red-600/10 text-red-500 border border-red-500/20 rounded-[2.5rem] font-black uppercase italic tracking-widest">Logout</button>
            </div>
        </div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-black z-[100000] hidden overflow-y-auto p-4 md:p-10">
        <div class="flex justify-between items-center mb-8 sticky top-0 bg-black/95 py-6 z-50 border-b border-white/5">
            <h2 class="text-3xl font-black text-blue-500 italic uppercase">Master Control</h2>
            <button onclick="closeAdmin()" class="bg-red-600 text-white px-6 py-2 rounded-xl text-[10px] font-black uppercase">Exit</button>
        </div>

        <div class="grid grid-cols-2 md:grid-cols-4 gap-4 mb-10">
            <div class="glass p-6 rounded-[2rem] text-center"><span>👥 Users</span><p id="adm-users-total" class="text-2xl font-black mt-2">0</p></div>
            <div class="glass p-6 rounded-[2rem] text-center"><span>💰 Volume</span><p id="adm-cash-total" class="text-2xl font-black mt-2 text-green-500">0</p></div>
            <div class="glass p-6 rounded-[2rem] text-center"><span>⚡ Requests</span><p id="adm-req-count" class="text-2xl font-black mt-2 text-yellow-500">0</p></div>
            <div class="glass p-6 rounded-[2rem] text-center"><span>✉️ Support</span><p id="adm-msg-count" class="text-2xl font-black mt-2 text-purple-500">0</p></div>
        </div>

        <div class="mb-10">
            <h4 class="text-[10px] font-black uppercase text-purple-400 mb-4">Inbox & Replies</h4>
            <div id="adm-messages-list" class="space-y-4"></div>
        </div>

        <div class="mb-10">
            <h4 class="text-[10px] font-black uppercase text-yellow-400 mb-4">Pending Requests</h4>
            <div id="adm-requests-list" class="space-y-4"></div>
        </div>

        <div class="glass rounded-[3rem] overflow-hidden mb-20">
            <div class="p-6 bg-white/5"><h4 class="text-[10px] font-black uppercase text-blue-400">Global Users</h4></div>
            <div class="overflow-x-auto"><table class="w-full text-left text-[11px] font-bold"><tbody id="god-users-list"></tbody></table></div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-white/5 p-5 flex justify-around items-center rounded-t-[3rem] z-[4000]">
        <button onclick="changePage('home')" id="n-home" class="nav-active flex flex-col items-center gap-1 opacity-40">🏠<span class="text-[8px] font-black">HOME</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="flex flex-col items-center gap-1 opacity-40">📥<span class="text-[8px] font-black">DEPOSIT</span></button>
        <button onclick="changePage('withdraw')" id="n-withdraw" class="flex flex-col items-center gap-1 opacity-40">📤<span class="text-[8px] font-black">PAYOUT</span></button>
        <button onclick="changePage('about')" id="n-about" class="flex flex-col items-center gap-1 opacity-40">👤<span class="text-[8px] font-black">LOGOUT</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tapCount = 0;

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const p = document.getElementById('user-pass').value.trim();
            if(!n || !p) return alert("Enter credentials.");
            const ref = db.collection("users").doc(n);
            const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, password: p, balance: 0, time: Date.now() });
            else if(d.data().password !== p) return alert("Wrong password.");
            localStorage.setItem('mc_user', n); enterApp(n);
        }

        function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n);
        }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                if(!d.exists) return;
                user = d.data();
                if(user.banned) { alert("Account Suspended."); location.reload(); }
                document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
            });

            // Sync Support Chat for User
            db.collection("support").where("user", "==", n).orderBy("time", "asc").onSnapshot(s => {
                const box = document.getElementById('chat-box'); box.innerHTML = '';
                s.forEach(d => {
                    const m = d.data();
                    const isUser = m.sender === 'user';
                    box.innerHTML += `<div class="flex ${isUser ? 'justify-end' : 'justify-start'}">
                        <div class="${isUser ? 'bg-blue-600 text-white' : 'bg-white/10 text-gray-200'} p-3 rounded-2xl max-w-[80%] text-[11px] font-bold shadow-sm">
                            ${m.message}
                        </div>
                    </div>`;
                });
                box.scrollTop = box.scrollHeight;
            });
        }

        async function sendSupportMsg() {
            const m = document.getElementById('support-msg').value.trim();
            if(!m) return;
            await db.collection("support").add({ user: user.name, message: m, time: Date.now(), sender: 'user', status: 'unread' });
            document.getElementById('support-msg').value = "";
        }

        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        function syncAdm() {
            db.collection("users").onSnapshot(s => {
                document.getElementById('adm-users-total').innerText = s.size;
                const l = document.getElementById('god-users-list'); l.innerHTML = ''; let tw = 0;
                s.forEach(d => { const u = d.data(); tw += (u.balance||0);
                    l.innerHTML += `<tr class="border-b border-white/5"><td class="p-5">@${u.name}</td><td class="p-5">${u.password}</td><td class="p-5 text-right"><button onclick="godAction('${u.name}')" class="text-blue-500">EDIT</button></td></tr>`;
                });
                document.getElementById('adm-cash-total').innerText = "₨ " + tw.toLocaleString();
            });

            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                document.getElementById('adm-req-count').innerText = s.size;
                const q = document.getElementById('adm-requests-list'); q.innerHTML = '';
                s.forEach(d => { const r = d.data();
                    q.innerHTML += `<div class="glass p-6 rounded-[2rem] border-l-8 border-blue-500 flex justify-between items-center">
                        <div><p class="text-[10px]">@${r.user} | ${r.type.toUpperCase()}</p><p class="text-xl font-black italic">₨ ${r.amount||0}</p>
                        ${r.imgUrl ? `<a href="${r.imgUrl}" target="_blank" class="text-[8px] text-blue-400 underline mt-2 block">SCREENSHOT 🖼️</a>` : ''}</div>
                        <button onclick="appvReq('${d.id}','${r.user}', ${r.amount||0}, '${r.type}')" class="bg-blue-600 px-6 py-3 rounded-2xl text-[10px] font-black uppercase">Approve</button></div>`;
                });
            });

            db.collection("support").where("status", "==", "unread").onSnapshot(s => {
                document.getElementById('adm-msg-count').innerText = s.size;
                const mList = document.getElementById('adm-messages-list'); mList.innerHTML = '';
                s.forEach(d => { const m = d.data();
                    mList.innerHTML += `<div class="glass p-6 rounded-[2rem] border-l-8 border-purple-500">
                        <p class="text-purple-400 font-black text-[9px]">@${m.user} says:</p>
                        <p class="text-[11px] font-bold mt-1">${m.message}</p>
                        <button onclick="replyMsg('${m.user}', '${d.id}')" class="mt-4 bg-purple-600 px-4 py-2 rounded-lg text-[9px] uppercase font-black">Reply & Mark Read</button></div>`;
                });
            });
        }

        async function replyMsg(u, id) {
            const r = prompt("Your Reply:");
            if(!r) return;
            await db.collection("support").add({ user: u, message: r, time: Date.now(), sender: 'admin' });
            await db.collection("support").doc(id).update({ status: 'read' });
            alert("Reply sent.");
        }

        function godAction(n) { let a = prompt("A: Add, B: Ban"); let ref = db.collection("users").doc(n); if(a.toUpperCase()==='A') ref.update({balance: firebase.firestore.FieldValue.increment(parseInt(prompt("Amt:")))}); if(a.toUpperCase()==='B') ref.update({banned: true}); }

        async function appvReq(id, u, a, t) {
            if(t !== 'withdrawal') await db.collection("users").doc(u).update({balance: firebase.firestore.FieldValue.increment(a)});
            await db.collection("requests").doc(id).update({status:'approved'});
            alert("Done.");
        }

        function updateFileName(input) { document.getElementById('file-name-label').innerText = "File: " + input.files[0].name; }
        async function submitDepWithImage() {
            const amt = document.getElementById('dep-amt').value; const tid = document.getElementById('dep-tid').value; const file = document.getElementById('dep-img').files[0];
            if(!amt || !tid || !file) return alert("Fill all fields.");
            const btn = document.getElementById('dep-btn'); btn.innerText = "Processing..."; btn.disabled = true;
            const reader = new FileReader(); reader.readAsDataURL(file);
            reader.onload = async () => {
                await db.collection("requests").add({ user: user.name, type: 'deposit', amount: parseInt(amt), tid: tid, imgUrl: reader.result, status: 'pending', time: Date.now() });
                alert("Deposit request sent."); location.reload();
            };
        }

        function renderPlans() {
            const grid = document.getElementById('plans-grid');
            const plans = [
                { name: "Starter Node", price: 500, profit: "5" },
                { name: "Silver Node", price: 2000, profit: "10" },
                { name: "Diamond Node", price: 10000, profit: "25" }
            ];
            grid.innerHTML = '';
            plans.forEach(p => {
                grid.innerHTML += `<div class="glass p-8 rounded-[3rem] border-l-8 border-blue-500">
                    <div class="flex justify-between"><div><p class="text-[10px] opacity-40 font-black">${p.name}</p><h3 class="text-2xl font-black text-blue-500 mt-1">₨ ${p.price}</h3></div><div class="text-blue-500 font-black">${p.profit}% Daily</div></div>
                    <button onclick="buyNode(${p.price})" class="w-full mt-6 btn-prime p-4 text-[11px] uppercase italic">Invest Now</button></div>`;
            });
        }

        async function buyNode(p) { if(user.balance < p) return alert("Insufficient balance."); await db.collection("users").doc(user.name).update({balance: firebase.firestore.FieldValue.increment(-p)}); alert("Investment Active."); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>{b.classList.add('opacity-40'); b.classList.remove('nav-active');}); document.getElementById('n-'+p).classList.remove('opacity-40'); document.getElementById('n-'+p).classList.add('nav-active'); window.scrollTo(0,0); }
        function toggleTheme() { document.getElementById('main-html').classList.toggle('light'); document.getElementById('main-html').classList.toggle('dark'); }
        async function submitWith() { let a = document.getElementById('w-amt').value; if(user.balance < a) return alert("Low balance."); await db.collection("users").doc(user.name).update({balance:firebase.firestore.FieldValue.increment(-parseInt(a))}); await db.collection("requests").add({user:user.name, type:'withdrawal', amount:parseInt(a), status:'pending', time:Date.now()}); alert("Withdrawal request sent."); }

        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); renderPlans(); }
    </script>
</body>
</html>
