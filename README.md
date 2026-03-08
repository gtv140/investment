<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold | Professional Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #00040a; color: white; margin: 0; overflow: hidden; }
        .page-content { height: 100vh; overflow-y: scroll; padding-bottom: 160px; scroll-behavior: smooth; }
        ::-webkit-scrollbar { display: none; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-main { background: linear-gradient(135deg, #0052d4 0%, #4364f7 50%, #6fb1fc 100%); }
        .page { display: none; animation: slideUp 0.3s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        .nav-btn { filter: grayscale(1); opacity: 0.4; transition: 0.4s; }
        .active-tab { filter: grayscale(0); opacity: 1; color: #00d4ff; }
        .logout-btn { background: linear-gradient(45deg, #f43f5e, #e11d48); box-shadow: 0 0 20px rgba(225, 29, 72, 0.4); }
    </style>
</head>
<body>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#00040a] flex flex-col items-center justify-center p-8">
        <h1 onclick="adminTap()" class="text-4xl font-black italic tracking-tighter mb-10 text-white">MINTCREST</h1>
        <div class="glass p-8 rounded-[3rem] w-full max-w-xs text-center border-t border-white/20">
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl mb-4 text-center font-bold outline-none border border-white/10">
            <input type="text" id="ref-by" placeholder="Referral Code (Optional)" class="w-full bg-white/5 p-3 rounded-xl mb-6 text-[10px] text-center outline-none border border-white/5">
            <button onclick="login()" class="w-full grad-main py-4 rounded-2xl font-black uppercase text-xs">Unlock Account</button>
        </div>
    </section>

    <main id="app-ui" class="hidden">
        
        <div id="p-home" class="page active-page page-content p-6">
            <div class="flex justify-between items-center mt-4 mb-8">
                <div>
                    <h2 class="text-2xl font-black" id="v-user">User</h2>
                    <p class="text-[9px] text-blue-400 font-bold uppercase tracking-widest">Global Node V21</p>
                </div>
                <button onclick="logout()" class="logout-btn p-3 rounded-2xl text-xl">🏃‍♂️</button>
            </div>

            <div class="grad-main p-8 rounded-[3rem] mb-8 shadow-2xl relative">
                <p class="text-[10px] font-black opacity-80 uppercase italic">Main Liquidity</p>
                <h2 class="text-5xl font-black my-2" id="v-bal">₨ 0</h2>
                <div class="mt-8 flex justify-between items-center border-t border-white/10 pt-4">
                    <p class="text-[9px] font-black uppercase">Active Profits: <span id="v-profit">₨ 0</span></p>
                    <button onclick="copyRef()" class="bg-black/20 px-4 py-2 rounded-xl text-[8px] font-black uppercase border border-white/10">🔗 Share Link</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="claimDaily()" class="glass p-5 rounded-3xl flex flex-col items-center gap-2">
                    <span class="text-2xl">🎁</span><span class="text-[10px] font-black uppercase">Daily Bonus</span>
                </button>
                <button onclick="changePage('spin')" class="glass p-5 rounded-3xl flex flex-col items-center gap-2">
                    <span class="text-2xl">🎡</span><span class="text-[10px] font-black uppercase">Lucky Spin</span>
                </button>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass py-8 rounded-[2.5rem] font-black text-xs uppercase text-cyan-400">Deposit</button>
                <button onclick="changePage('withdraw')" class="glass py-8 rounded-[2.5rem] font-black text-xs uppercase text-rose-500">Withdraw</button>
            </div>
        </div>

        <div id="p-invest" class="page page-content p-6">
            <h3 class="text-center font-black text-xl italic mb-6 uppercase text-blue-400">Node Fleets</h3>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-withdraw" class="page page-content p-6">
            <div class="glass p-8 rounded-[3.5rem] border-t-8 border-rose-600">
                <h3 class="text-2xl font-black text-rose-500 mb-6 uppercase text-center italic">Payout</h3>
                <input type="text" id="w-user-auto" readonly class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-black opacity-40">
                <select id="w-method" class="w-full bg-white/10 p-4 rounded-xl mb-4 text-xs font-black outline-none border border-white/10">
                    <option value="Easypaisa">Easypaisa</option><option value="JazzCash">JazzCash</option><option value="Binance">Binance (USDT)</option>
                </select>
                <input type="number" id="w-amt" placeholder="Amount (Min 100)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold outline-none border border-white/10">
                <input type="text" id="w-acc" placeholder="Account Number" class="w-full bg-white/5 p-4 rounded-xl mb-8 text-center text-xs font-bold border border-white/10">
                <button onclick="subWd()" class="w-full bg-rose-600 py-5 rounded-2xl font-black text-xs uppercase">Confirm Withdraw</button>
            </div>
        </div>

        <div id="p-activity" class="page page-content p-6">
            <h3 class="font-black text-xl mb-8 uppercase italic">Transaction Vault</h3>
            <div id="user-history" class="space-y-3"></div>
        </div>

        <div id="p-chat" class="page page-content flex flex-col h-screen">
            <div class="p-6 border-b border-white/10 flex justify-between items-center">
                <h3 class="font-black text-xl uppercase italic">Admin Help Desk</h3>
            </div>
            <div id="chat-box" class="flex-1 overflow-y-auto p-6 flex flex-col"></div>
            <div class="p-4 glass border-t border-white/10">
                <div class="flex gap-2">
                    <input type="text" id="chat-msg" placeholder="Type issue..." class="flex-1 bg-white/5 p-4 rounded-2xl text-xs outline-none">
                    <button onclick="sendChat()" class="grad-main px-6 rounded-2xl">▶</button>
                </div>
            </div>
        </div>

    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#00040a] z-[50000] hidden overflow-y-auto p-8">
        <div class="flex justify-between items-center mb-10 border-b border-white/10 pb-6">
            <h2 class="text-xl font-black text-cyan-400 uppercase">Master Dashboard</h2>
            <button onclick="closeAdm()" class="bg-rose-600 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>

        <div class="grid grid-cols-2 gap-4 mb-8">
            <div class="glass p-6 rounded-3xl text-center"><p class="text-[9px] opacity-50">TOTAL USERS</p><h4 id="adm-total-users" class="text-2xl font-black">0</h4></div>
            <div class="glass p-6 rounded-3xl text-center"><p class="text-[9px] opacity-50">LIVE REQUESTS</p><h4 id="adm-live-reqs" class="text-2xl font-black">0</h4></div>
        </div>

        <div class="glass p-6 rounded-3xl mb-8 border border-white/10">
            <p class="text-[10px] font-black text-blue-400 mb-4 uppercase">📢 GLOBAL BROADCAST</p>
            <input type="text" id="adm-msg" placeholder="Message to all..." class="w-full bg-white/5 p-4 rounded-xl text-xs mb-3 outline-none border border-white/10">
            <button onclick="sendBC()" class="w-full grad-main py-4 rounded-xl text-[9px] font-black uppercase">PUSH MESSAGE</button>
        </div>

        <div class="glass p-6 rounded-3xl mb-10 border-l-4 border-yellow-500">
            <p class="text-[10px] font-black text-yellow-500 mb-4 uppercase">Manual Control</p>
            <input type="text" id="adm-u" placeholder="User Name" class="w-full bg-white/5 p-4 rounded-xl text-xs mb-3 border border-white/10 outline-none">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="editU('balance')" class="bg-blue-600 py-3 rounded-xl text-[8px] font-black">ADD BAL</button>
                <button onclick="editU('profit')" class="bg-cyan-600 py-3 rounded-xl text-[8px] font-black">ADD PROF</button>
            </div>
        </div>

        <div id="adm-reqs" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-7 flex justify-around items-center fixed bottom-0 w-full z-[2000] rounded-t-[3.5rem] border-t border-white/10">
        <button onclick="changePage('home')" id="n-home" class="nav-btn text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-2xl">📊</button>
        <button onclick="changePage('chat')" id="n-chat" class="nav-btn text-2xl">💬</button>
        <button onclick="changePage('activity')" id="n-activity" class="nav-btn text-2xl">📜</button>
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
        const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        // Auto Login Fix
        window.onload = () => {
            const saved = localStorage.getItem('mint_user');
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            renderPlans();
        };

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const r = document.getElementById('ref-by').value.trim().toLowerCase();
            if(!n) return;
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) {
                await ref.set({ name: n, balance: 0, profit: 0, refBy: r || 'None', time: Date.now(), activePlans: [] });
            }
            user = (await ref.get()).data();
            localStorage.setItem('mint_user', n);
            document.getElementById('v-user').innerText = n.toUpperCase();
            document.getElementById('w-user-auto').value = "USER: " + n.toUpperCase();
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            startSync(n); syncChat(n);
        }

        function logout() { localStorage.removeItem('mint_user'); location.reload(); }

        // Plans Rendering (20 Normal + 5 Special)
        function renderPlans() {
            const list = document.getElementById('plans-list');
            list.innerHTML = '';
            for(let i=1; i<=25; i++) {
                const isSpecial = i > 20;
                const price = i * 500;
                const daily = (price * 0.1).toFixed(0);
                list.innerHTML += `
                <div class="glass p-6 rounded-[2.5rem] border-l-4 ${isSpecial ? 'border-yellow-500' : 'border-blue-500'}">
                    <div class="flex justify-between items-center mb-2">
                        <h4 class="font-black text-xs uppercase">${isSpecial ? '💎 Special Node' : '📊 Fleet V'+i}</h4>
                        <span class="text-[10px] font-black text-cyan-400">₨ ${price}</span>
                    </div>
                    <p class="text-[9px] opacity-60 mb-4">${isSpecial ? 'High Priority Daily Yield' : 'Cycle Duration: '+ (i+7) +' Days'}</p>
                    <div class="flex justify-between items-center">
                        <span class="text-[10px] font-black text-green-400">Profit: ₨ ${daily}/Day</span>
                        <button onclick="buyPlan(${price}, ${daily}, ${isSpecial ? 0 : i+7})" class="bg-white/10 px-6 py-2 rounded-xl text-[9px] font-black uppercase">Purchase</button>
                    </div>
                </div>`;
            }
        }

        async function buyPlan(p, d, days) {
            if(user.balance < p) return alert("Insufficient Balance, Sweetie! 😘");
            const endTime = days > 0 ? Date.now() + (days * 86400000) : 0;
            await db.collection("users").doc(user.name).update({ 
                balance: user.balance - p,
                activePlans: firebase.firestore.FieldValue.arrayUnion({ price: p, daily: d, end: endTime })
            });
            alert("Plan Activated! 🚀");
        }

        // Dashboard Sync
        function startSync(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
            });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const l = document.getElementById('user-history'); l.innerHTML = '';
                s.forEach(d => {
                    const x = d.data();
                    l.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center">
                        <span class="text-[10px] font-black uppercase">${x.type}</span>
                        <span class="text-[9px] font-black ${x.status === 'pending' ? 'text-yellow-500' : 'text-green-500'}">${x.status.toUpperCase()}</span>
                    </div>`;
                });
            });
        }

        // DEPOSIT / WITHDRAWAL
        async function subDep() {
            const a = parseInt(document.getElementById('d-amt').value);
            const t = document.getElementById('d-tid').value;
            const f = document.getElementById('d-file').files[0];
            if(!a || !t || !f) return alert("Sweetie, incomplete data! 😘");
            const sRef = storage.ref(`proofs/${Date.now()}`); await sRef.put(f);
            const url = await sRef.getDownloadURL();
            await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: url, type: "Deposit", status: "pending", time: Date.now(), refBy: user.refBy });
            alert("Sent! Waiting for Admin Approval. ✨");
        }

        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value);
            const acc = document.getElementById('w-acc').value;
            if(a < 100 || a > user.balance) return alert("Limit Error!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, type: "Withdrawal", status: "pending", time: Date.now() });
            alert("Withdrawal Logged! 😘");
        }

        // ADMIN FUNCTIONS
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Pass:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdm() { document.getElementById('admin-panel').classList.add('hidden'); }

        function syncAdm() {
            db.collection("users").onSnapshot(s => { document.getElementById('adm-total-users').innerText = s.size; });
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                document.getElementById('adm-live-reqs').innerText = s.size;
                const l = document.getElementById('adm-reqs'); l.innerHTML = '';
                s.forEach(d => {
                    const x = d.data();
                    l.innerHTML += `<div class="glass p-6 rounded-3xl border-l-4 border-cyan-400">
                        <p class="text-[10px] font-black mb-1">${x.user.toUpperCase()} | ${x.type}</p>
                        <p class="text-[8px] opacity-60">Amt: ${x.amount} | ID: ${x.tid || x.acc} | RefBy: ${x.refBy || 'N/A'}</p>
                        <div class="flex gap-2 mt-4">
                            <button onclick="hnd('${d.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 bg-green-600 py-3 rounded-xl text-[9px] font-black">APPROVE</button>
                            <button onclick="hnd('${d.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-rose-600 py-3 rounded-xl text-[9px] font-black">REJECT</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function hnd(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u);
            if(act==='approved' && type === "Deposit") await ref.update({ balance: firebase.firestore.FieldValue.increment(amt) });
            if(act==='rejected' && type === "Withdrawal") await ref.update({ balance: firebase.firestore.FieldValue.increment(amt) });
            await db.collection("requests").doc(id).update({ status: act });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('active-tab'); }
        function copyRef() { navigator.clipboard.writeText(window.location.origin + "?ref=" + user.name); alert("Referral Link Copied! 🔗"); }
    </script>
</body>
</html>
