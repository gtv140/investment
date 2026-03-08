<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <title>MintCrest Infinity | Official</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #00040a; color: white; margin: 0; }
        /* Smooth Mobile Scroll Fix */
        .app-container { height: 100vh; overflow-y: auto; -webkit-overflow-scrolling: touch; padding-bottom: 120px; }
        ::-webkit-scrollbar { display: none; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-main { background: linear-gradient(135deg, #0052d4 0%, #4364f7 50%, #6fb1fc 100%); }
        .page { display: none; }
        .active-page { display: block; animation: fadeIn 0.3s ease-out; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        .logout-btn { background: linear-gradient(45deg, #ff0055, #9333ea); border-radius: 1rem; }
    </style>
</head>
<body class="bg-[#00040a]">

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#00040a] flex flex-col items-center justify-center p-8 text-center">
        <h1 class="text-4xl font-black italic tracking-tighter mb-8">MINTCREST</h1>
        <div class="glass p-8 rounded-[3rem] w-full max-w-xs">
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl mb-4 text-center font-bold outline-none border border-white/10 text-white">
            <button onclick="login()" class="w-full grad-main py-4 rounded-2xl font-black uppercase text-xs shadow-xl">Login</button>
        </div>
    </section>

    <main id="app-ui" class="hidden app-container">
        
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mt-4 mb-8">
                <div><h2 class="text-2xl font-black" id="v-user">User</h2><p class="text-[9px] text-green-400 font-bold uppercase tracking-widest">● Verified Node</p></div>
                <button onclick="logout()" class="logout-btn w-10 h-10 flex items-center justify-center text-lg shadow-lg">🚀</button>
            </div>

            <div class="grad-main p-8 rounded-[3rem] mb-6 shadow-2xl">
                <p class="text-[10px] font-black opacity-80 uppercase">Available Balance</p>
                <h2 class="text-4xl font-black my-2" id="v-bal">₨ 0</h2>
                <div class="mt-4 pt-4 border-t border-white/10 flex justify-between">
                    <span class="text-[9px] font-bold">Total Profit: <b id="v-profit">₨ 0</b></span>
                    <button onclick="copyRef()" class="text-[9px] bg-black/20 px-3 py-1 rounded-lg">Copy ID</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-3 mb-6">
                <button onclick="sendBonusReq()" class="glass p-4 rounded-2xl text-center"><span class="text-xl block">🎁</span><p class="text-[9px] font-black uppercase mt-1">Daily Bonus</p></button>
                <button onclick="changePage('spin')" class="glass p-4 rounded-2xl text-center"><span class="text-xl block">🎡</span><p class="text-[9px] font-black uppercase mt-1">Lucky Spin</p></button>
            </div>

            <div class="grid grid-cols-2 gap-3">
                <button onclick="changePage('wallet')" class="glass py-6 rounded-3xl font-black text-[10px] uppercase text-cyan-400">Deposit</button>
                <button onclick="changePage('withdraw')" class="glass py-6 rounded-3xl font-black text-[10px] uppercase text-rose-500">Withdraw</button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h3 class="text-center font-black text-xl mb-6 uppercase text-blue-400">Investment Fleets</h3>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-wallet" class="page p-6">
            <h3 class="text-center font-black mb-6 uppercase tracking-widest">Capital In</h3>
            <div class="space-y-3 mb-6">
                <div class="glass p-4 rounded-xl flex justify-between items-center text-xs"><span>JazzCash</span><b>03705519562</b></div>
                <div class="glass p-4 rounded-xl flex justify-between items-center text-xs"><span>Easypaisa</span><b>03379827882</b></div>
            </div>
            <div class="glass p-6 rounded-[2.5rem]">
                <input type="number" id="d-amt" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center border border-white/10 outline-none">
                <input type="text" id="d-tid" placeholder="TID Number" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center border border-white/10 outline-none">
                <input type="file" id="d-file" accept="image/*" class="w-full text-[10px] mb-4">
                <button id="dep-btn" onclick="subDep()" class="w-full grad-main py-4 rounded-xl font-black uppercase text-[10px]">Submit Proof</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-6 rounded-[2.5rem]">
                <h3 class="text-center font-black mb-6 uppercase">Withdraw</h3>
                <input type="number" id="w-amt" placeholder="Min 100" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center outline-none">
                <input type="text" id="w-acc" placeholder="Account Number" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center outline-none">
                <select id="w-method" class="w-full bg-black/40 p-4 rounded-xl mb-6 text-center text-xs">
                    <option value="Easypaisa">Easypaisa</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="Bank">Bank Transfer</option>
                </select>
                <button onclick="subWd()" class="w-full bg-rose-600 py-4 rounded-xl font-black uppercase text-[10px]">Request Payout</button>
            </div>
        </div>

        <div id="p-history" class="page p-6"><h3 class="font-black mb-6 uppercase">Activities</h3><div id="user-history" class="space-y-3"></div></div>

    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#00040a] z-[50000] hidden overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-6">
            <h2 class="font-black text-cyan-400">ADMIN CONTROL</h2>
            <button onclick="closeAdm()" class="bg-rose-600 px-4 py-1 rounded text-[10px]">EXIT</button>
        </div>
        <div id="adm-reqs-list" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-6 flex justify-around fixed bottom-0 w-full rounded-t-[2.5rem] border-t border-white/10">
        <button onclick="changePage('home')" class="text-2xl">🏠</button>
        <button onclick="changePage('invest')" class="text-2xl">📈</button>
        <button onclick="changePage('history')" class="text-2xl">📜</button>
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

        window.onload = () => {
            const saved = localStorage.getItem('mint_user_v1'); // Purane users ka data yahan se connect hoga
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            renderPlans();
        };

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            if(!n) return;
            const ref = db.collection("users").doc(n);
            const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, profit: 0, time: Date.now() });
            localStorage.setItem('mint_user_v1', n);
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n);
        }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0);
                document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0);
                document.getElementById('v-user').innerText = n.toUpperCase();
            });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const l = document.getElementById('user-history'); l.innerHTML = '';
                s.forEach(d => {
                    const x = d.data();
                    l.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-4 ${x.status==='pending'?'border-yellow-500':'border-green-500'}">
                        <span class="text-[9px] font-bold uppercase">${x.type} - ₨ ${x.amount}</span>
                        <span class="text-[8px] opacity-60">${x.status}</span>
                    </div>`;
                });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            for(let i=1; i<=25; i++) {
                const vip = i > 20;
                let p = i === 1 ? 200 : (i * 500) - 300; if(vip) p = (i-20) * 10000;
                let d = i <= 5 ? 15 + (i * 2) : 30 + i;
                const daily = (p * (vip ? 0.15 : 0.08)).toFixed(0);
                list.innerHTML += `<div class="glass p-5 rounded-[2rem] border-l-4 ${vip?'border-yellow-500':'border-blue-500'} mb-4">
                    <div class="flex justify-between mb-2"><b>V${i} - ₨ ${p}</b><span class="text-[8px] opacity-60">${vip?'Lifetime':d+' Days'}</span></div>
                    <div class="flex justify-between items-center"><span class="text-green-400 font-bold text-xs">₨ ${daily}/Day</span><button onclick="alert('Invest Request Sent!')" class="bg-white/10 px-4 py-2 rounded-lg text-[8px] uppercase">Activate</button></div>
                </div>`;
            }
        }

        async function subDep() {
            const btn = document.getElementById('dep-btn');
            const a = document.getElementById('d-amt').value; const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(!a || !t || !f) return alert("Sweetie, complete details! 😘");
            btn.innerText = "UPLOADING..."; btn.disabled = true;
            try {
                const sRef = storage.ref(`proofs/${Date.now()}_${user.name}`);
                const snap = await sRef.put(f);
                const url = await snap.ref.getDownloadURL();
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "Deposit", status: "pending", time: Date.now() });
                alert("Request Sent! 😘"); changePage('home');
            } catch(e) { alert("Error: Rules lock hain! Check Firebase."); }
            btn.innerText = "SUBMIT PROOF"; btn.disabled = false;
        }

        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value;
            if(!a || a > user.balance) return alert("Kam balance hai! 😘");
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, type: "Withdrawal", status: "pending", time: Date.now() });
            alert("Withdrawal Requested! 😘"); changePage('home');
        }

        async function sendBonusReq() { alert("Daily Bonus Request Sent to Admin! 😘"); await db.collection("requests").add({ user: user.name, amount: 50, type: "Daily Bonus", status: "pending", time: Date.now() }); }

        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Pass:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdm() { document.getElementById('admin-panel').classList.add('hidden'); }
        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const l = document.getElementById('adm-reqs-list'); l.innerHTML = '';
                s.forEach(d => {
                    const x = d.data();
                    l.innerHTML += `<div class="glass p-4 rounded-xl text-[10px]">
                        <p>${x.user} | ${x.type} | ₨ ${x.amount}</p>
                        ${x.proof ? `<a href="${x.proof}" target="_blank" class="text-blue-400 block my-2 underline">VIEW PHOTO</a>`:''}
                        <div class="flex gap-2 mt-2"><button onclick="hnd('${d.id}','${x.user}',${x.amount},'approved','${x.type}')" class="bg-green-600 px-4 py-1 rounded">APPROVE</button>
                        <button onclick="hnd('${d.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="bg-rose-600 px-4 py-1 rounded">REJECT</button></div>
                    </div>`;
                });
            });
        }
        async function hnd(id, u, amt, act, type) {
            if(act==='approved') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(amt) });
            await db.collection("requests").doc(id).update({ status: act });
        }

        function logout() { localStorage.removeItem('mint_user_v1'); location.reload(); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function copyRef() { navigator.clipboard.writeText(user.name); alert("ID Copied! 😘"); }
    </script>
</body>
</html>
