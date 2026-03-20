<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Ultimate Master v30.0</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 100px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(15px); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #2563eb, #3b82f6); color: white; border-radius: 20px; font-weight: 800; border: none; cursor: pointer; transition: 0.3s; }
        input, select { background: #161b22; border: 1px solid var(--border); color: var(--text); padding: 15px; border-radius: 15px; width: 100%; outline: none; font-size: 13px; margin-bottom: 12px; }
        .admin-card { background: rgba(59, 130, 246, 0.05); border: 1px solid rgba(255,255,255,0.05); border-radius: 28px; padding: 20px; margin-bottom: 15px; }
        .full-overlay { position: fixed; inset: 0; background: #000; z-index: 999999; display: none; flex-direction: column; items: center; justify-content: center; text-align: center; padding: 20px; }
    </style>
</head>
<body>

    <div id="maint-screen" class="full-overlay bg-slate-950">
        <i class="fa-solid fa-bolt-lightning text-yellow-400 text-6xl mb-4 animate-pulse"></i>
        <h1 class="text-2xl font-black uppercase tracking-tighter text-white">System Optimization</h1>
        <p class="opacity-50 text-[10px] mt-2 italic text-blue-300">Wait for us, sweetie! 😘</p>
    </div>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/90 backdrop-blur-xl border-b border-[var(--border)] p-4 flex justify-between items-center">
        <div class="flex items-center gap-3">
            <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg active:scale-90 transition-transform">M</div>
            <div>
                <h1 class="text-[12px] font-black uppercase italic">MintCrest <span class="text-blue-500">Gold</span></h1>
                <p id="display-user" class="text-[8px] font-bold text-blue-400 uppercase tracking-widest">@USER</p>
            </div>
        </div>
        <div id="top-bal" class="text-[10px] font-black bg-blue-600/10 text-blue-500 px-4 py-2 rounded-full border border-blue-500/20 italic shadow-sm">₨ 0</div>
    </header>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-10 rounded-[3.5rem] bg-gradient-to-br from-blue-700 to-indigo-950 text-white shadow-2xl relative overflow-hidden text-center">
                <div class="absolute -top-10 -right-10 w-32 h-32 bg-white/5 rounded-full blur-3xl"></div>
                <p class="text-[10px] font-black uppercase opacity-60 tracking-widest">Available Balance</p>
                <h2 class="text-5xl font-black tracking-tighter my-2" id="v-bal">₨ 0.00</h2>
                <div class="mt-8 flex gap-3">
                   <button onclick="changePage('wallet')" class="flex-1 bg-white/10 p-4 rounded-2xl text-[10px] font-black uppercase border border-white/10 active:scale-95 transition">Deposit</button>
                   <button onclick="changePage('withdraw')" class="flex-1 bg-blue-400/20 p-4 rounded-2xl text-[10px] font-black uppercase border border-blue-500/10 active:scale-95 transition">Withdraw</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <a href="https://chat.whatsapp.com/YOUR_GROUP_LINK" class="glass p-6 rounded-[2.5rem] flex flex-col items-center gap-2 text-green-500 shadow-xl">
                    <i class="fa-brands fa-whatsapp text-3xl"></i>
                    <span class="text-[9px] font-black uppercase">Official Group</span>
                </a>
                <button onclick="changePage('about')" class="glass p-6 rounded-[2.5rem] flex flex-col items-center gap-2 text-blue-400 shadow-xl">
                    <i class="fa-solid fa-building-shield text-3xl"></i>
                    <span class="text-[9px] font-black uppercase">Company Info</span>
                </button>
            </div>
        </div>

        <div id="p-nodes" class="page space-y-4 pb-24">
            <div class="text-center py-4">
                <h3 class="text-2xl font-black text-white uppercase italic tracking-tighter">Investment Nodes</h3>
                <p class="text-[9px] text-blue-500 font-black uppercase tracking-[0.3em]">Select Your Tier</p>
            </div>
            <div id="plans-grid" class="grid grid-cols-1 gap-4"></div>
        </div>

        <div id="p-wallet" class="page"><div class="glass p-8 rounded-[3.5rem] space-y-6 text-center shadow-2xl">
            <h3 class="font-black uppercase text-blue-500 italic text-xl">Deposit Center</h3>
            <div class="grid grid-cols-1 gap-3">
                <div class="p-4 bg-white/5 border border-white/5 rounded-2xl">
                    <p class="text-[8px] uppercase opacity-40 mb-1">JazzCash Account</p>
                    <h4 id="j-num-disp" class="text-xl font-black text-white italic">03XX-XXXXXXX</h4>
                </div>
                <div class="p-4 bg-white/5 border border-white/5 rounded-2xl">
                    <p class="text-[8px] uppercase opacity-40 mb-1">EasyPaisa Account</p>
                    <h4 id="e-num-disp" class="text-xl font-black text-white italic">03XX-XXXXXXX</h4>
                </div>
            </div>
            <hr class="border-white/5">
            <input id="d-amt" type="number" placeholder="Deposit Amount (Min 200)">
            <input id="d-tid" placeholder="Transaction ID (TID)">
            <div class="text-left bg-blue-500/5 p-4 rounded-2xl border border-blue-500/10">
                <label class="text-[9px] font-black uppercase opacity-50 block mb-2">Upload Screenshot Proof:</label>
                <input id="d-proof-file" type="file" accept="image/*" class="text-[10px] file:bg-blue-600 file:border-none file:text-white file:rounded-lg file:px-3 file:py-1 cursor-pointer">
            </div>
            <button id="dep-btn" onclick="sendDeposit()" class="w-full p-5 btn-prime text-[11px] font-black uppercase italic shadow-blue-600/20 shadow-lg">Submit Payment</button>
        </div></div>

        <div id="p-withdraw" class="page"><div class="glass p-8 rounded-[3.5rem] space-y-6 text-center shadow-2xl">
            <h3 class="font-black uppercase text-blue-500 italic text-xl">Request Payout</h3>
            <input id="w-amt" type="number" placeholder="Amount (Min 100)">
            <input id="w-acc" placeholder="Account Number & Name">
            <button onclick="sendWithdraw()" class="w-full p-5 btn-prime text-[11px] font-black uppercase italic">Withdraw Now</button>
        </div></div>

        <div id="p-about" class="page space-y-4"><div class="glass p-8 rounded-[3.5rem] italic text-[13px] opacity-80 leading-relaxed shadow-xl">
            <h3 class="font-black text-blue-500 uppercase not-italic text-lg mb-4 underline decoration-blue-500/20">Platform Details</h3>
            <p><b class="text-blue-500">Founder:</b> Muhammad Nazim</p>
            <p><b class="text-blue-500">Company:</b> Prime Solutions Digital Assets</p>
            <p><b class="text-blue-500">Email:</b> webhub262@gmail.com</p>
            <p class="mt-6 font-bold not-italic text-[10px] uppercase opacity-40">Privacy Policy: All financial transactions are encrypted. Screenshots are required for all manual deposits to ensure account safety.</p>
        </div></div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-40">
        <div class="flex justify-between items-center mb-10">
            <div>
                <h2 class="text-3xl font-black text-blue-500 italic uppercase tracking-tighter">Admin Hub</h2>
                <p class="text-[9px] font-black text-white/30 uppercase tracking-[0.4em]">v30.0 Full Access</p>
            </div>
            <button onclick="closeAdmin()" class="w-14 h-14 rounded-3xl bg-red-600/20 text-red-500 border border-red-500/20 flex items-center justify-center font-black text-xl shadow-lg shadow-red-500/10">X</button>
        </div>

        <div class="admin-card">
            <h4 class="text-[10px] font-black uppercase text-blue-400 mb-5 border-b border-white/5 pb-2">Gateway Management</h4>
            <input id="adm-jnum" placeholder="Update JazzCash Number">
            <input id="adm-enum" placeholder="Update EasyPaisa Number">
            <button onclick="savePaymentSettings()" class="w-full p-4 bg-blue-600 text-white rounded-2xl text-[10px] font-black uppercase shadow-lg">Save New Numbers</button>
        </div>

        <div class="admin-card">
            <h4 class="text-[10px] font-black uppercase text-red-500 mb-5 border-b border-white/5 pb-2">System Maintenance</h4>
            <div class="flex gap-3">
                <button onclick="toggleMaint(true)" class="flex-1 bg-red-600 text-white p-4 rounded-2xl text-[10px] font-black uppercase">Enable Block</button>
                <button onclick="toggleMaint(false)" class="flex-1 bg-green-600 text-white p-4 rounded-2xl text-[10px] font-black uppercase">Go Online</button>
            </div>
        </div>

        <div class="admin-card">
            <h4 class="text-[10px] font-black uppercase text-purple-400 mb-5 border-b border-white/5 pb-2">Nodes Manager</h4>
            <div id="adm-plan-manager" class="space-y-3 mb-6"></div>
            <input id="pl-name" placeholder="Plan Name (e.g. Starter)">
            <div class="flex gap-3"><input id="pl-price" type="number" placeholder="Price"><input id="pl-profit" type="number" placeholder="Daily %"></div>
            <button onclick="createPlan()" class="w-full p-4 bg-purple-600 text-white rounded-2xl text-[10px] font-black uppercase">Add New Node</button>
        </div>

        <h4 class="text-[14px] font-black uppercase text-green-500 italic mb-5 tracking-widest">Incoming Proofs</h4>
        <div id="adm-req-list" class="space-y-6"></div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-6 flex justify-around items-center rounded-t-[4rem] z-[4000] shadow-2xl">
        <button onclick="changePage('home')" class="p-2 transition-all active:scale-90"><i class="fa-solid fa-house text-lg"></i></button>
        <button onclick="changePage('nodes')" class="p-2 transition-all active:scale-90"><i class="fa-solid fa-cube text-blue-500 text-3xl"></i></button>
        <button onclick="changePage('wallet')" class="p-2 transition-all active:scale-90"><i class="fa-solid fa-wallet text-lg"></i></button>
        <button onclick="logout()" class="p-2 transition-all active:scale-90 text-red-500/50"><i class="fa-solid fa-power-off text-lg"></i></button>
    </nav>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-10 text-center">
        <div class="w-24 h-24 bg-blue-600 rounded-[2.5rem] mb-12 flex items-center justify-center text-5xl font-black italic text-white shadow-2xl shadow-blue-600/30">M</div>
        <h2 class="text-xl font-black uppercase tracking-widest mb-10">Access <span class="text-blue-500 italic">MintCrest</span></h2>
        <input id="l-name" placeholder="YOUR NAME" class="max-w-[320px] text-center font-black uppercase italic border-blue-500/20">
        <button onclick="login()" class="w-full max-w-[320px] p-6 btn-prime uppercase text-[12px] font-black tracking-widest shadow-2xl">Start Earning</button>
    </section>

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
        let user = null; let tapCount = 0; let settings = {};

        function login() {
            const n = document.getElementById('l-name').value.trim().toLowerCase();
            if(n.length < 3) return alert("Sweetie, enter your name! 😘");
            localStorage.setItem('mc_session', n); enterApp(n);
        }

        async function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-user').innerText = "@" + n.toUpperCase();

            db.collection("settings").doc("global").onSnapshot(d => {
                if(!d.exists) db.collection("settings").doc("global").set({ maintenance: false, jazzNum: 'Set', easyNum: 'Set' });
                settings = d.data() || {};
                document.getElementById('maint-screen').style.display = settings.maintenance ? 'flex' : 'none';
                document.getElementById('j-num-disp').innerText = settings.jazzNum || '03xx-xxxxxxx';
                document.getElementById('e-num-disp').innerText = settings.easyNum || '03xx-xxxxxxx';
            });

            db.collection("users").doc(n).onSnapshot(doc => {
                if(!doc.exists) return db.collection("users").doc(n).set({ name: n, balance: 0, status: 'active', joined: Date.now() });
                user = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            });
            syncPlans();
        }

        // --- ADMIN POWERS ---
        async function savePaymentSettings() {
            const j = document.getElementById('adm-jnum').value; 
            const e = document.getElementById('adm-enum').value;
            const data = {};
            if(j) data.jazzNum = j; if(e) data.easyNum = e;
            await db.collection("settings").doc("global").set(data, { merge: true });
            alert("Payment Numbers Updated! 😘");
        }

        async function createPlan() {
            const n = document.getElementById('pl-name').value; 
            const p = parseInt(document.getElementById('pl-price').value); 
            const pr = parseInt(document.getElementById('pl-profit').value);
            if(!n || !p) return alert("Details missing!");
            await db.collection("plans").add({ name: n, price: p, profit: pr });
            alert("Investment Node Created! 😘");
        }

        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Master Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        async function syncAdm() {
            db.collection("plans").onSnapshot(s => {
                const l = document.getElementById('adm-plan-manager'); l.innerHTML = '';
                s.forEach(doc => { l.innerHTML += `<div class="flex justify-between items-center p-3 glass rounded-2xl mb-1 text-[10px] font-black uppercase"><span>${doc.data().name} (₨ ${doc.data().price})</span><button onclick="db.collection('plans').doc('${doc.id}').delete()" class="text-red-500 font-bold">REMOVE</button></div>`; });
            });
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-req-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    list.innerHTML += `<div class="glass p-6 rounded-[3rem] border-l-8 border-yellow-500 mb-4 text-center shadow-xl">
                        <p class="text-[11px] font-black text-blue-400 uppercase tracking-tighter">${r.user} - Deposit Request</p>
                        <h4 class="text-3xl font-black my-2">₨ ${r.amount}</h4>
                        <p class="text-[9px] opacity-40 mb-4">TID: ${r.tid}</p>
                        <img src="${r.proof}" class="w-full rounded-[2rem] my-4 border border-white/10 shadow-lg">
                        <button onclick="approveReq('${doc.id}', '${r.user}', ${r.amount})" class="w-full bg-green-600 p-4 rounded-2xl text-[11px] font-black uppercase shadow-lg shadow-green-600/20 active:scale-95 transition">Verify & Approve</button>
                    </div>`;
                });
            });
        }

        async function approveReq(id, u, a) {
            await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'approved' });
            alert("User balance updated successfully! 😘");
        }

        async function toggleMaint(v) { await db.collection("settings").doc("global").update({ maintenance: v }); }

        // --- USER ACTIONS ---
        async function sendDeposit() {
            const a = parseInt(document.getElementById('d-amt').value); 
            const t = document.getElementById('d-tid').value;
            const file = document.getElementById('d-proof-file').files[0];
            if(!a || a < 200 || !t || !file) return alert("Please fill all details and upload the proof image! 😘");
            
            document.getElementById('dep-btn').innerText = "Processing Proof...";
            document.getElementById('dep-btn').disabled = true;

            const reader = new FileReader();
            reader.readAsDataURL(file);
            reader.onload = async () => {
                const proofUrl = reader.result;
                const id = Date.now().toString();
                await db.collection("requests").doc(id).set({ user: user.name, amount: a, tid: t, proof: proofUrl, status: 'pending', type: 'Deposit', time: Date.now() });
                document.getElementById('dep-btn').innerText = "Submit Payment";
                document.getElementById('dep-btn').disabled = false;
                alert("Deposit request sent! Admin will verify your screenshot soon. 😘");
                changePage('home');
            };
        }

        async function buyPlan(id, price, name) {
            if(user.balance < price) return alert("your balance is low! Deposit first. 😘");
            if(confirm(`Do you want to activate the ${name} node for ₨ ${price}?`)) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-price) });
                alert(`${name} activated successfully! 🚀`);
            }
        }

        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => { const p = doc.data(); g.innerHTML += `<div class="glass p-8 rounded-[3rem] flex justify-between items-center border-l-4 border-blue-500 shadow-lg"><div><p class="text-[9px] font-black opacity-30 uppercase tracking-[0.2em]">${p.name}</p><h4 class="text-2xl font-black italic">₨ ${p.price}</h4><p class="text-[9px] font-bold text-blue-400 uppercase mt-1">${p.profit}% Daily Yield</p></div><button onclick="buyPlan('${doc.id}', ${p.price}, '${p.name}')" class="btn-prime px-9 py-4 text-[11px] font-black uppercase tracking-widest active:scale-90 transition">Activate</button></div>`; });
            });
        }

        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); 
            document.getElementById('p-'+p).classList.add('active-page'); 
            window.scrollTo(0,0); 
        }
        function logout() { localStorage.removeItem('mc_session'); location.reload(); }
        window.onload = () => { const s = localStorage.getItem('mc_session'); if(s) enterApp(s); }
    </script>
</body>
</html>
