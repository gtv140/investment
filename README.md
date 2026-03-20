<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | v100.0 Sovereign</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --blue: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 120px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(20px); }
        .page { display: none; animation: fadeIn 0.4s ease-in-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        .btn-prime { background: linear-gradient(135deg, #1d4ed8, #3b82f6); color: white; border-radius: 25px; font-weight: 800; transition: 0.3s; box-shadow: 0 10px 25px -10px rgba(59,130,246,0.5); }
        input, select { background: #161b22; border: 1px solid var(--border); color: var(--text); padding: 18px; border-radius: 20px; width: 100%; outline: none; margin-bottom: 12px; font-size: 14px; }
        .float-wa { position: fixed; bottom: 110px; right: 20px; width: 55px; height: 55px; background: #25d366; border-radius: 50%; display: flex; items: center; justify-content: center; color: white; font-size: 28px; box-shadow: 0 10px 20px rgba(0,0,0,0.3); z-index: 9999; }
        .admin-card { background: rgba(37,99,235,0.05); border: 1px dashed var(--blue); border-radius: 35px; padding: 25px; margin-bottom: 25px; }
    </style>
</head>
<body>

    <a href="https://wa.me/923705519562" class="float-wa hidden" id="wa-btn"><i class="fa-brands fa-whatsapp"></i></a>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/90 backdrop-blur-xl border-b border-[var(--border)] p-6 flex justify-between items-center">
        <div class="flex items-center gap-3">
            <div onclick="adminTap()" class="w-11 h-11 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
            <div>
                <h1 class="text-[14px] font-black uppercase italic tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></h1>
                <p id="display-user" class="text-[9px] font-bold text-blue-400 uppercase tracking-widest italic">@USER</p>
            </div>
        </div>
        <div id="top-bal" class="text-[12px] font-black bg-blue-600/10 text-blue-500 px-5 py-2 rounded-full border border-blue-500/20 italic">₨ 0</div>
    </header>

    <main id="app-ui" class="hidden p-5 space-y-7">
        
        <div id="p-home" class="page active-page space-y-7">
            <div class="p-10 rounded-[4rem] bg-gradient-to-br from-blue-700 via-blue-900 to-black text-white shadow-2xl text-center border border-white/5">
                <p class="text-[10px] font-black uppercase opacity-50 tracking-[0.3em] mb-2">Available Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0.00</h2>
                <div class="mt-10 flex gap-3">
                   <button onclick="changePage('wallet')" class="flex-1 bg-white p-4 rounded-2xl text-[10px] font-black uppercase text-blue-950 shadow-xl">Deposit</button>
                   <button onclick="changePage('withdraw')" class="flex-1 bg-blue-500/20 p-4 rounded-2xl text-[10px] font-black uppercase border border-white/10">Withdraw</button>
                </div>
            </div>

            <div class="glass p-8 rounded-[3rem] border-l-8 border-blue-500 space-y-4">
                <div class="flex justify-between items-center">
                    <h4 class="text-sm font-black italic uppercase">Partner Rewards</h4>
                    <span id="ref-code-tag" class="text-[9px] bg-blue-600/20 text-blue-500 px-3 py-1 rounded-full font-bold italic">CODE: ...</span>
                </div>
                <button onclick="copyRef()" class="w-full py-4 btn-prime text-[10px] uppercase">Copy Invite Link</button>
                <div class="flex justify-around pt-2">
                    <div class="text-center"><p id="ref-count" class="text-xl font-black">0</p><p class="text-[8px] opacity-40 uppercase">Referrals</p></div>
                    <div class="text-center"><p id="ref-earn" class="text-xl font-black text-green-500">₨ 0</p><p class="text-[8px] opacity-40 uppercase">Commissions</p></div>
                </div>
            </div>

            <div class="flex gap-4">
                <button onclick="claimDaily()" id="claim-btn" class="flex-1 glass p-6 rounded-[2.5rem] text-center border-b-4 border-yellow-500">
                    <p class="text-[9px] font-black uppercase mb-1">Daily Gift</p>
                    <span class="text-[12px] font-bold text-yellow-500">CLAIM NOW</span>
                </button>
                <button onclick="changePage('promo')" class="flex-1 glass p-6 rounded-[2.5rem] text-center border-b-4 border-purple-500">
                    <p class="text-[9px] font-black uppercase mb-1">Promo Code</p>
                    <span class="text-[12px] font-bold text-purple-500">USE VOUCHER</span>
                </button>
            </div>
        </div>

        <div id="p-wallet" class="page space-y-5">
            <div class="glass p-10 rounded-[4rem] text-center space-y-6">
                <h3 class="text-xl font-black italic text-blue-500">CAPITAL INFLOW</h3>
                <div class="space-y-3">
                    <div class="p-4 bg-white/5 rounded-2xl border border-white/5">
                        <p class="text-[8px] opacity-40">JAZZCASH</p>
                        <h4 id="j-num-disp" class="text-lg font-black tracking-widest">...</h4>
                    </div>
                    <div class="p-4 bg-white/5 rounded-2xl border border-white/5">
                        <p class="text-[8px] opacity-40">EASYPAISA</p>
                        <h4 id="e-num-disp" class="text-lg font-black tracking-widest">...</h4>
                    </div>
                </div>
                <input id="d-amt" type="number" placeholder="Enter PKR Amount">
                <input id="d-tid" placeholder="Transaction ID (TID)">
                <input id="d-proof-file" type="file" accept="image/*" class="text-[10px] py-4">
                <button onclick="sendDeposit()" class="w-full p-6 btn-prime uppercase text-[12px] font-black">Submit Deposit</button>
            </div>
        </div>

        <div id="p-promo" class="page">
            <div class="glass p-10 rounded-[4rem] text-center space-y-6">
                <h3 class="text-xl font-black italic text-purple-500">VOUCHER CENTER</h3>
                <input id="promo-input" placeholder="ENTER CODE HERE" class="text-center font-bold">
                <button onclick="applyPromo()" class="w-full p-6 btn-prime bg-purple-600 shadow-purple-500/20">Apply Code</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-5">
            <div class="glass p-10 rounded-[4rem] text-center space-y-6">
                <h3 class="text-xl font-black italic text-red-500">CASHOUT ASSETS</h3>
                <input id="w-amt" type="number" placeholder="Withdrawal Amount">
                <input id="w-acc" placeholder="Account Name & No">
                <button onclick="sendWithdraw()" class="w-full p-6 btn-prime bg-red-600">Request Payout</button>
            </div>
        </div>

        <div id="p-nodes" class="page space-y-5 pb-24"><div id="plans-grid" class="grid grid-cols-1 gap-5"></div></div>
        <div id="p-history" class="page space-y-4 pb-24"><div id="history-list" class="space-y-4"></div></div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-7 pb-40">
        <div class="flex justify-between items-center mb-10">
            <div><h2 class="text-3xl font-black text-blue-500 italic">SOVEREIGN</h2><p class="text-[9px] opacity-30 tracking-[0.4em]">ADMIN v100.0</p></div>
            <button onclick="closeAdmin()" class="w-14 h-14 rounded-2xl bg-red-600/10 text-red-500 font-black border border-red-500/20">X</button>
        </div>

        <div class="admin-card">
            <h4 class="text-[10px] font-black uppercase text-blue-500 mb-4 tracking-widest">Global Config</h4>
            <input id="adm-jnum" placeholder="JazzCash Number">
            <input id="adm-enum" placeholder="EasyPaisa Number">
            <input id="adm-daily-amt" type="number" placeholder="Daily Bonus (PKR)">
            <input id="adm-ref-pct" type="number" placeholder="Referral Commission (%)">
            <button onclick="saveAdminSettings()" class="w-full p-4 bg-blue-600 rounded-xl text-[10px] font-black uppercase">Update Gateways & Rates</button>
        </div>

        <div class="admin-card">
            <h4 class="text-[10px] font-black uppercase text-purple-500 mb-4 tracking-widest">Promo Code Factory</h4>
            <input id="adm-promo-code" placeholder="Code Name (e.g. MEGA100)">
            <input id="adm-promo-amt" type="number" placeholder="Reward Amount">
            <button onclick="createPromo()" class="w-full p-4 bg-purple-600 rounded-xl text-[10px] font-black uppercase">Generate Code</button>
        </div>

        <div class="admin-card">
            <h4 class="text-[10px] font-black uppercase text-yellow-500 mb-4 tracking-widest">Manual User Bonus</h4>
            <input id="adm-bonus-user" placeholder="Exact User Name">
            <input id="adm-bonus-amt" type="number" placeholder="Amount to Add">
            <button onclick="sendManualBonus()" class="w-full p-4 bg-yellow-600 text-black rounded-xl text-[10px] font-black uppercase">Send Gift</button>
        </div>

        <h4 class="text-xl font-black italic text-green-500 mb-6">Pending Operations</h4>
        <div id="adm-req-list" class="space-y-8"></div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-7 flex justify-around items-center rounded-t-[4rem] z-[4000] shadow-2xl">
        <button onclick="changePage('home')" class="p-3"><i class="fa-solid fa-house-chimney text-lg"></i></button>
        <button onclick="changePage('nodes')" class="p-3"><i class="fa-solid fa-gem text-blue-500 text-3xl"></i></button>
        <button onclick="changePage('history')" class="p-3"><i class="fa-solid fa-list-ul text-lg"></i></button>
        <button onclick="logout()" class="p-3 text-red-500/20"><i class="fa-solid fa-power-off text-lg"></i></button>
    </nav>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-12 text-center">
        <div class="w-20 h-20 bg-blue-600 rounded-[2rem] mb-10 flex items-center justify-center text-4xl font-black italic text-white shadow-2xl">M</div>
        <h2 class="text-2xl font-black uppercase tracking-[0.3em] mb-10">MintCrest <span class="text-blue-500 italic">Gold</span></h2>
        <input id="l-name" placeholder="YOUR NAME" class="max-w-[320px] text-center font-bold">
        <input id="l-ref" placeholder="REFER CODE (OPTIONAL)" class="max-w-[320px] text-center text-[10px] opacity-50 font-bold">
        <button onclick="login()" class="w-full max-w-[320px] p-7 btn-prime uppercase text-[11px] font-black tracking-widest">Authorize Access</button>
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
            const r = document.getElementById('l-ref').value.trim().toLowerCase();
            if(n.length < 3) return alert("Please enter your name, sweetie! 😘");
            
            db.collection("users").doc(n).get().then(doc => {
                if(!doc.exists) {
                    db.collection("users").doc(n).set({ 
                        name: n, balance: 0, lastClaim: 0, 
                        referredBy: r || null, totalRefEarn: 0, refCount: 0 
                    }).then(() => { if(r) db.collection("users").doc(r).update({ refCount: firebase.firestore.FieldValue.increment(1) }); });
                }
                localStorage.setItem('mc_session', n); enterApp(n);
            });
        }

        async function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('wa-btn').classList.remove('hidden');
            document.getElementById('display-user').innerText = "@" + n.toUpperCase();
            document.getElementById('ref-code-tag').innerText = "CODE: " + n.toUpperCase();

            db.collection("settings").doc("global").onSnapshot(d => {
                settings = d.data() || { jazzNum: '...', easyNum: '...', refPct: 10, dailyReward: 50 };
                document.getElementById('j-num-disp').innerText = settings.jazzNum;
                document.getElementById('e-num-disp').innerText = settings.easyNum;
            });

            db.collection("users").doc(n).onSnapshot(doc => {
                user = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('ref-count').innerText = user.refCount || 0;
                document.getElementById('ref-earn').innerText = "₨ " + (user.totalRefEarn || 0).toLocaleString();
                
                const today = new Date().toDateString();
                if(user.lastClaim === today) {
                    document.getElementById('claim-btn').style.opacity = "0.4";
                    document.getElementById('claim-btn').disabled = true;
                }
            });
            syncHistory(n); syncPlans();
        }

        // --- USER ACTIONS ---
        async function claimDaily() {
            const today = new Date().toDateString();
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(settings.dailyReward || 50), lastClaim: today });
            alert("Daily Bonus Added! 😘");
        }

        async function applyPromo() {
            const code = document.getElementById('promo-input').value.trim().toUpperCase();
            const doc = await db.collection("promos").doc(code).get();
            if(!doc.exists) return alert("Invalid Voucher! 😘");
            const amt = doc.data().amount;
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(amt) });
            await db.collection("promos").doc(code).delete();
            await db.collection("requests").add({ user: user.name, amount: amt, type: 'Promo: '+code, status: 'approved', time: Date.now() });
            alert(`₨ ${amt} added successfully! 😘`);
            changePage('home');
        }

        async function sendDeposit() {
            const a = parseInt(document.getElementById('d-amt').value); 
            const t = document.getElementById('d-tid').value;
            const file = document.getElementById('d-proof-file').files[0];
            if(!a || !t || !file) return alert("Complete all fields! 😘");
            const reader = new FileReader(); reader.readAsDataURL(file);
            reader.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: reader.result, status: 'pending', type: 'Deposit', time: Date.now() });
                alert("Deposit submitted for review! 😘"); changePage('home');
            };
        }

        async function sendWithdraw() {
            const a = parseInt(document.getElementById('w-amt').value);
            const acc = document.getElementById('w-acc').value;
            if(user.balance < a) return alert("Insufficient Capital! 😘");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").add({ user: user.name, amount: a, tid: acc, status: 'pending', type: 'Withdraw', time: Date.now() });
            alert("Withdrawal Requested! 😘"); changePage('home');
        }

        // --- ADMIN HUB LOGIC ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Access Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        async function saveAdminSettings() {
            const j = document.getElementById('adm-jnum').value;
            const e = document.getElementById('adm-enum').value;
            const d = parseInt(document.getElementById('adm-daily-amt').value);
            const r = parseInt(document.getElementById('adm-ref-pct').value);
            await db.collection("settings").doc("global").set({ jazzNum: j, easyNum: e, dailyReward: d, refPct: r }, { merge: true });
            alert("System Config Updated! 😘");
        }

        async function createPromo() {
            const c = document.getElementById('adm-promo-code').value.toUpperCase();
            const a = parseInt(document.getElementById('adm-promo-amt').value);
            await db.collection("promos").doc(c).set({ amount: a, active: true });
            alert("New Voucher Live! 😘");
        }

        async function sendManualBonus() {
            const u = document.getElementById('adm-bonus-user').value.trim().toLowerCase();
            const a = parseFloat(document.getElementById('adm-bonus-amt').value);
            await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").add({ user: u, amount: a, type: 'System Gift', status: 'approved', time: Date.now() });
            alert("Gift Dispatched! 😘");
        }

        async function processRequest(id, u, a, action, type) {
            if(action === 'approve') {
                if(type === 'Deposit') {
                    await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
                    const uDoc = await db.collection("users").doc(u).get();
                    const refBy = uDoc.data().referredBy;
                    if(refBy) {
                        const commission = (a * (settings.refPct || 10)) / 100;
                        await db.collection("users").doc(refBy).update({ balance: firebase.firestore.FieldValue.increment(commission), totalRefEarn: firebase.firestore.FieldValue.increment(commission) });
                        await db.collection("requests").add({ user: refBy, amount: commission, type: 'Referral Bonus', status: 'approved', time: Date.now() });
                    }
                }
                await db.collection("requests").doc(id).update({ status: 'approved' });
            } else {
                if(type === 'Withdraw') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
                await db.collection("requests").doc(id).update({ status: 'rejected' });
            }
            alert("Action Executed! 😘");
        }

        // --- UTILS & SYNC ---
        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-req-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    list.innerHTML += `<div class="glass p-7 rounded-[3rem] border-l-8 ${r.type==='Deposit'?'border-blue-500':'border-red-500'} mb-8">
                        <p class="text-[9px] font-black uppercase text-blue-400 mb-2">${r.user} | ${r.type}</p>
                        <h4 class="text-3xl font-black italic mb-4">₨ ${r.amount}</h4>
                        ${r.proof ? `<img src="${r.proof}" class="w-full rounded-3xl mb-4 border border-white/5 shadow-xl">` : `<p class="text-[10px] opacity-40 italic mb-4">TID: ${r.tid}</p>`}
                        <div class="flex gap-3">
                            <button onclick="processRequest('${doc.id}', '${r.user}', ${r.amount}, 'approve', '${r.type}')" class="flex-1 bg-green-600 p-4 rounded-xl text-[10px] font-black uppercase">Approve</button>
                            <button onclick="processRequest('${doc.id}', '${r.user}', ${r.amount}, 'reject', '${r.type}')" class="flex-1 bg-red-600 p-4 rounded-xl text-[10px] font-black uppercase">Reject</button>
                        </div>
                    </div>`;
                });
            });
        }

        function syncHistory(n) {
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const h = document.getElementById('history-list'); h.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    const clr = r.status==='pending'?'bg-yellow-500':(r.status==='approved'?'bg-green-500':'bg-red-600');
                    h.innerHTML += `<div class="glass p-6 rounded-[2.5rem] flex justify-between items-center shadow-lg">
                        <div><p class="text-[8px] opacity-30 font-black uppercase">${r.type}</p><h4 class="text-xl font-black italic">₨ ${r.amount}</h4></div>
                        <div class="px-4 py-2 rounded-full text-[8px] font-black uppercase ${clr} text-black shadow-inner">${r.status}</div>
                    </div>`;
                });
            });
        }

        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => { const p = doc.data(); g.innerHTML += `<div class="glass p-9 rounded-[4rem] border-l-[15px] border-blue-600 flex justify-between items-center shadow-2xl">
                    <div><p class="text-[10px] font-black opacity-30 uppercase">${p.name}</p><h4 class="text-2xl font-black italic">₨ ${p.price}</h4><p class="text-[9px] font-bold text-blue-500 uppercase mt-2 italic">${p.profit}% Profit Daily</p></div>
                    <button onclick="buyPlan('${doc.id}', ${p.price}, '${p.name}')" class="btn-prime px-9 py-4 text-[10px] font-black uppercase">Invest</button>
                </div>`; });
            });
        }

        async function buyPlan(id, price, name) {
            if(user.balance < price) return alert("Deposit first, sweetie! 😘");
            if(confirm(`Invest in ${name}?`)) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-price) });
                await db.collection("requests").add({ user: user.name, amount: price, type: 'Plan: '+name, status: 'approved', time: Date.now() });
                alert(`${name} Activated! 🚀`);
            }
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); window.scrollTo(0,0); }
        function logout() { localStorage.removeItem('mc_session'); location.reload(); }
        function copyRef() { navigator.clipboard.writeText(user.name.toUpperCase()); alert("Your Invite Code Copied! 😘"); }
        window.onload = () => { if(localStorage.getItem('mc_session')) enterApp(localStorage.getItem('mc_session')); }
    </script>
</body>
</html>
