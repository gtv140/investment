<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | v70.0 Empire Edition</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --blue: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 130px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(25px); }
        .page { display: none; animation: slideUp 0.5s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1d4ed8, #3b82f6); color: white; border-radius: 30px; font-weight: 800; border: none; cursor: pointer; transition: 0.3s; box-shadow: 0 15px 35px -10px rgba(59, 130, 246, 0.6); }
        input { background: #161b22; border: 1px solid var(--border); color: var(--text); padding: 20px; border-radius: 25px; width: 100%; outline: none; font-size: 14px; margin-bottom: 15px; }
        .admin-card { background: linear-gradient(145deg, rgba(59, 130, 246, 0.12), rgba(0,0,0,0)); border: 1px solid rgba(255,255,255,0.05); border-radius: 50px; padding: 40px; margin-bottom: 40px; }
        .refer-badge { background: linear-gradient(90deg, #f59e0b, #fbbf24); color: black; font-weight: 900; font-size: 10px; padding: 5px 15px; border-radius: 50px; text-transform: uppercase; }
    </style>
</head>
<body>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/95 backdrop-blur-3xl border-b border-[var(--border)] p-6 flex justify-between items-center">
        <div class="flex items-center gap-4">
            <div onclick="adminTap()" class="w-12 h-12 bg-blue-600 rounded-[1.5rem] flex items-center justify-center font-black text-white italic shadow-2xl active:scale-90 transition cursor-pointer">M</div>
            <div>
                <h1 class="text-[14px] font-black uppercase italic tracking-tighter">MintCrest <span class="text-blue-500 text-xl">.</span></h1>
                <p id="display-user" class="text-[9px] font-bold text-blue-400 uppercase tracking-widest flex items-center gap-1">@USER <i class="fa-solid fa-crown text-[8px]"></i></p>
            </div>
        </div>
        <div id="top-bal" class="text-[14px] font-black bg-blue-600/10 text-blue-500 px-7 py-3 rounded-full border border-blue-500/30 italic shadow-inner">₨ 0</div>
    </header>

    <main id="app-ui" class="hidden p-5 space-y-8">
        
        <div id="p-home" class="page active-page space-y-8">
            <div class="p-14 rounded-[5.5rem] bg-gradient-to-br from-blue-700 via-blue-900 to-black text-white shadow-2xl relative overflow-hidden text-center border border-white/10">
                <div class="absolute -left-10 -bottom-10 w-40 h-40 bg-blue-500/20 blur-3xl"></div>
                <p class="text-[11px] font-black uppercase opacity-60 tracking-[0.5em] mb-4">Capital Assets</p>
                <h2 class="text-6xl font-black tracking-tighter" id="v-bal">₨ 0.00</h2>
                <div class="mt-12 flex gap-4">
                   <button onclick="changePage('wallet')" class="flex-1 bg-white p-6 rounded-[2rem] text-[11px] font-black uppercase text-blue-950 shadow-2xl active:scale-95 transition">Deposit</button>
                   <button onclick="changePage('withdraw')" class="flex-1 bg-blue-500/20 p-6 rounded-[2rem] text-[11px] font-black uppercase border border-white/20 active:scale-95 transition backdrop-blur-xl">Withdraw</button>
                </div>
            </div>

            <div class="glass p-10 rounded-[4.5rem] border-t-4 border-blue-500 shadow-2xl text-center">
                <div class="refer-badge mb-5 inline-block">Partner Program</div>
                <h4 class="text-xl font-black italic mb-2">Invite & Earn</h4>
                <p class="text-[10px] opacity-40 uppercase font-bold mb-6">Earn commission on friend's deposits</p>
                <div class="bg-black/40 p-5 rounded-[2rem] border border-white/5 flex justify-between items-center mb-4">
                    <span id="ref-link" class="text-[11px] font-bold text-blue-400 truncate pr-4">Loading Link...</span>
                    <button onclick="copyRef()" class="text-white bg-blue-600 px-6 py-3 rounded-2xl text-[10px] font-black">COPY</button>
                </div>
                <div class="flex justify-around mt-4">
                    <div class="text-center"><p id="ref-count" class="text-2xl font-black text-white">0</p><p class="text-[8px] uppercase opacity-40">Total Invited</p></div>
                    <div class="text-center"><p id="ref-earn" class="text-2xl font-black text-green-500">₨ 0</p><p class="text-[8px] uppercase opacity-40">Total Commissions</p></div>
                </div>
            </div>

            <div class="grid grid-cols-1 gap-5">
                <div class="glass p-8 rounded-[3.5rem] border-l-8 border-yellow-500 flex justify-between items-center shadow-2xl">
                    <div><h4 class="text-lg font-black italic">Daily Gift</h4><p class="text-[9px] opacity-50 font-bold uppercase">Claim Reward</p></div>
                    <button id="claim-btn" onclick="claimDaily()" class="bg-yellow-500 text-black px-8 py-4 rounded-2xl text-[10px] font-black uppercase shadow-lg active:scale-90 transition">Claim</button>
                </div>
                <div class="glass p-8 rounded-[3.5rem] border-l-8 border-purple-500 shadow-2xl">
                    <div class="flex gap-3">
                        <input id="promo-input" placeholder="Voucher Code" class="mb-0 py-4 px-6 text-[12px] bg-purple-500/5 border-purple-500/20">
                        <button onclick="applyPromo()" class="bg-purple-600 px-8 rounded-2xl text-[10px] font-black uppercase">Apply</button>
                    </div>
                </div>
            </div>
        </div>

        <div id="p-history" class="page space-y-4"><div id="history-list" class="space-y-4"></div></div>
        <div id="p-nodes" class="page space-y-6 pb-24"><div id="plans-grid" class="grid grid-cols-1 gap-6"></div></div>
        <div id="p-wallet" class="page"><div class="glass p-10 rounded-[5rem] space-y-6 text-center shadow-2xl">
            <h3 class="font-black uppercase text-blue-500 italic text-xl">Deposit</h3>
            <input id="d-amt" type="number" placeholder="Amount (PKR)">
            <input id="d-tid" placeholder="TID (Transaction ID)">
            <input id="d-proof-file" type="file" accept="image/*" class="text-[11px] file:bg-blue-600 file:border-none file:text-white file:rounded-xl">
            <button onclick="sendDeposit()" class="w-full p-6 btn-prime uppercase text-[12px] font-black tracking-widest">Transmit Funds</button>
        </div></div>

        <div id="p-withdraw" class="page"><div class="glass p-10 rounded-[5rem] space-y-6 text-center shadow-2xl">
            <h3 class="font-black uppercase text-blue-500 italic text-xl">Cashout</h3>
            <input id="w-amt" type="number" placeholder="Amount (PKR)">
            <input id="w-acc" placeholder="Account Name & No">
            <button onclick="sendWithdraw()" class="w-full p-6 btn-prime uppercase text-[12px] font-black italic">Request Payout</button>
        </div></div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-7 pb-44">
        <div class="flex justify-between items-center mb-12">
            <div><h2 class="text-3xl font-black text-blue-500 italic uppercase">Empire Control</h2><p class="text-[10px] opacity-30 uppercase tracking-[0.5em]">v70.0 Royal Master</p></div>
            <button onclick="closeAdmin()" class="w-16 h-16 rounded-[2rem] bg-red-600/20 text-red-500 border border-red-500/20 flex items-center justify-center font-black">X</button>
        </div>

        <div class="admin-card border-l-4 border-orange-500">
            <h4 class="text-[11px] font-black uppercase text-orange-500 mb-6 tracking-widest">Referral Commission (%)</h4>
            <p class="text-[9px] opacity-40 mb-4 uppercase">How much % to give inviter on friend's deposit?</p>
            <input id="adm-ref-pct" type="number" placeholder="Commission % (e.g. 10)">
            <button onclick="updateRefSettings()" class="w-full p-5 bg-orange-600 text-white rounded-2xl text-[11px] font-black uppercase shadow-xl">Set Commission %</button>
        </div>

        <div class="admin-card">
            <h4 class="text-[11px] font-black uppercase text-blue-500 mb-6 tracking-widest">Manual User Bonus</h4>
            <input id="adm-bonus-user" placeholder="Target User Name">
            <input id="adm-bonus-amt" type="number" placeholder="Amount (PKR)">
            <button onclick="sendManualBonus()" class="w-full p-5 bg-blue-600 rounded-2xl text-[11px] font-black uppercase">Give Bonus Now</button>
        </div>

        <h4 class="text-[18px] font-black uppercase text-green-500 italic mb-8">Verification Desk</h4>
        <div id="adm-req-list" class="space-y-10"></div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-8 flex justify-around items-center rounded-t-[5rem] z-[4000] shadow-2xl">
        <button onclick="changePage('home')" class="p-3 active:scale-90 transition"><i class="fa-solid fa-house-user text-xl"></i></button>
        <button onclick="changePage('nodes')" class="p-3 active:scale-90 transition"><i class="fa-solid fa-gem text-blue-500 text-4xl"></i></button>
        <button onclick="changePage('history')" class="p-3 active:scale-90 transition"><i class="fa-solid fa-receipt text-xl"></i></button>
        <button onclick="logout()" class="p-3 active:scale-90 transition text-red-500/30"><i class="fa-solid fa-power-off text-xl"></i></button>
    </nav>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-12 text-center">
        <div class="w-28 h-28 bg-blue-600 rounded-[3.5rem] mb-14 flex items-center justify-center text-6xl font-black italic text-white shadow-2xl">M</div>
        <input id="l-name" placeholder="YOUR NAME" class="max-w-[340px] text-center font-black uppercase italic py-6">
        <input id="l-ref" placeholder="REFERRAL CODE (OPTIONAL)" class="max-w-[340px] text-center font-black uppercase text-[11px] opacity-60">
        <button onclick="login()" class="w-full max-w-[340px] p-8 btn-prime uppercase text-[13px] font-black tracking-[0.4em]">ENTER PLATFORM</button>
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
            if(n.length < 3) return alert("Enter valid name, sweetie! 😘");
            
            db.collection("users").doc(n).get().then(doc => {
                if(!doc.exists) {
                    db.collection("users").doc(n).set({ 
                        name: n, balance: 0, lastClaim: 0, 
                        referredBy: r || null, totalRefEarn: 0, refCount: 0 
                    }).then(() => {
                        if(r) db.collection("users").doc(r).update({ refCount: firebase.firestore.FieldValue.increment(1) });
                    });
                }
                localStorage.setItem('mc_session', n); enterApp(n);
            });
        }

        async function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-user').innerText = "@" + n.toUpperCase();
            document.getElementById('ref-link').innerText = "CODE: " + n.toUpperCase();

            db.collection("settings").doc("global").onSnapshot(d => { settings = d.data() || { refPct: 10, dailyReward: 50 }; });

            db.collection("users").doc(n).onSnapshot(doc => {
                user = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('ref-count').innerText = user.refCount || 0;
                document.getElementById('ref-earn').innerText = "₨ " + (user.totalRefEarn || 0).toLocaleString();
                
                const today = new Date().toDateString();
                if(user.lastClaim === today) {
                    document.getElementById('claim-btn').innerText = "Claimed";
                    document.getElementById('claim-btn').disabled = true;
                }
            });
            syncHistory(n); syncPlans();
        }

        // --- ADMIN ELITE TOOLS ---
        async function updateRefSettings() {
            const pct = parseInt(document.getElementById('adm-ref-pct').value);
            await db.collection("settings").doc("global").update({ refPct: pct });
            alert(`Referral Commission set to ${pct}%! 😘`);
        }

        async function processRequest(id, u, a, action, type) {
            if(action === 'approve') {
                if(type === 'Deposit') {
                    await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
                    // Handle Referral Commission
                    const uDoc = await db.collection("users").doc(u).get();
                    const refBy = uDoc.data().referredBy;
                    if(refBy) {
                        const commission = (a * (settings.refPct || 10)) / 100;
                        await db.collection("users").doc(refBy).update({ 
                            balance: firebase.firestore.FieldValue.increment(commission),
                            totalRefEarn: firebase.firestore.FieldValue.increment(commission)
                        });
                        await db.collection("requests").add({ user: refBy, amount: commission, type: 'Referral Bonus from '+u, status: 'approved', time: Date.now() });
                    }
                }
                await db.collection("requests").doc(id).update({ status: 'approved' });
            } else {
                if(type === 'Withdraw') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
                await db.collection("requests").doc(id).update({ status: 'rejected' });
            }
            alert("Processed Successfully! 😘");
        }

        // --- CORE LOGIC ---
        function copyRef() { navigator.clipboard.writeText(user.name.toUpperCase()); alert("Your Invite Code Copied! 😘"); }
        async function claimDaily() {
            const today = new Date().toDateString();
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(settings.dailyReward || 50), lastClaim: today });
            alert(`Daily Login Gift Received! 😘`);
        }

        async function sendDeposit() {
            const a = parseInt(document.getElementById('d-amt').value); 
            const t = document.getElementById('d-tid').value;
            const file = document.getElementById('d-proof-file').files[0];
            const reader = new FileReader();
            reader.readAsDataURL(file);
            reader.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: reader.result, status: 'pending', type: 'Deposit', time: Date.now() });
                alert("Deposit Logged! Admin will verify soon. 😘");
                changePage('home');
            };
        }

        async function sendWithdraw() {
            const a = parseInt(document.getElementById('w-amt').value);
            const acc = document.getElementById('w-acc').value;
            if(user.balance < a) return alert("Insufficient Balance! 😘");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").add({ user: user.name, amount: a, tid: acc, status: 'pending', type: 'Withdraw', time: Date.now() });
            alert("Withdrawal Requested! 😘");
            changePage('home');
        }

        // --- UI TOOLS ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-req-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    list.innerHTML += `<div class="glass p-10 rounded-[4rem] border-l-8 ${r.type==='Deposit'?'border-blue-600':'border-red-600'} mb-12 text-center shadow-2xl">
                        <p class="text-[10px] font-black uppercase text-blue-500 mb-2">${r.user} | ${r.type}</p>
                        <h4 class="text-4xl font-black italic">₨ ${r.amount}</h4>
                        ${r.proof ? `<img src="${r.proof}" class="w-full rounded-[3rem] my-8 border border-white/5 shadow-2xl">` : ''}
                        <div class="flex gap-4">
                            <button onclick="processRequest('${doc.id}', '${r.user}', ${r.amount}, 'approve', '${r.type}')" class="flex-1 bg-green-600 p-5 rounded-3xl text-[11px] font-black uppercase shadow-lg">Approve</button>
                            <button onclick="processRequest('${doc.id}', '${r.user}', ${r.amount}, 'reject', '${r.type}')" class="flex-1 bg-red-600 p-5 rounded-3xl text-[11px] font-black uppercase shadow-lg">Reject</button>
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
                    const color = r.status==='pending'?'bg-yellow-500':(r.status==='approved'?'bg-green-500':'bg-red-600');
                    h.innerHTML += `<div class="glass p-7 rounded-[3rem] flex justify-between items-center shadow-lg border border-white/5">
                        <div><p class="text-[9px] font-black uppercase opacity-30">${r.type}</p><h4 class="text-xl font-black italic">₨ ${r.amount}</h4></div>
                        <div class="px-5 py-2.5 rounded-full text-[9px] font-black uppercase ${color} text-black shadow-inner">${r.status}</div>
                    </div>`;
                });
            });
        }
        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => { const p = doc.data(); g.innerHTML += `<div class="glass p-10 rounded-[4.5rem] border-l-[15px] border-blue-600 flex justify-between items-center shadow-2xl relative overflow-hidden">
                    <div class="absolute right-[-20px] top-[-20px] w-32 h-32 bg-blue-600/5 rounded-full blur-2xl"></div>
                    <div><p class="text-[11px] font-black opacity-30 uppercase">${p.name}</p><h4 class="text-3xl font-black italic tracking-tighter">₨ ${p.price}</h4><p class="text-[10px] font-bold text-blue-500 uppercase mt-3 italic"><i class="fa-solid fa-fire"></i> ${p.profit}% Daily</p></div>
                    <button onclick="buyPlan('${doc.id}', ${p.price}, '${p.name}')" class="btn-prime px-12 py-5 text-[12px] font-black uppercase active:scale-90 transition">Invest</button>
                </div>`; });
            });
        }
        async function buyPlan(id, price, name) {
            if(user.balance < price) return alert("Deposit Needed! 😘");
            if(confirm(`Invest in ${name}?`)) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-price) });
                await db.collection("requests").add({ user: user.name, amount: price, type: 'Activated: '+name, status: 'approved', time: Date.now() });
                alert(`${name} Node Online! 🚀`);
            }
        }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); window.scrollTo(0,0); }
        function logout() { localStorage.removeItem('mc_session'); location.reload(); }
        window.onload = () => { if(localStorage.getItem('mc_session')) enterApp(localStorage.getItem('mc_session')); }
    </script>
</body>
</html>
