<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <title>MintCrest Infinity | Global Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #00040a; color: white; margin: 0; overflow: hidden; }
        .page-content { height: 100vh; overflow-y: scroll; padding-bottom: 200px; scroll-behavior: smooth; }
        ::-webkit-scrollbar { display: none; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-main { background: linear-gradient(135deg, #0052d4 0%, #4364f7 50%, #6fb1fc 100%); }
        .logout-btn { background: linear-gradient(45deg, #ff0055, #ffdd00, #00ffcc, #3333ff); background-size: 400%; animation: grad-move 5s infinite linear; border-radius: 1.5rem; }
        @keyframes grad-move { 0%{background-position:0%} 100%{background-position:100%} }
        .page { display: none; animation: slideUp 0.4s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { transform: translateY(20px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
        .nav-active { color: #00d4ff; filter: drop-shadow(0 0 8px #00d4ff); }
        .spin-wheel { border: 8px solid #333; transition: 4s cubic-bezier(0.1, 0, 0, 1); }
    </style>
</head>
<body>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#00040a] flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 grad-main rounded-[2.5rem] mb-6 flex items-center justify-center text-3xl shadow-2xl animate-bounce">💎</div>
        <h1 onclick="adminTap()" class="text-4xl font-black italic tracking-tighter mb-2">MINTCREST</h1>
        <p class="text-[8px] uppercase tracking-[0.5em] text-cyan-400 mb-10 font-bold">Trusted Infinity Node</p>
        <div class="glass p-8 rounded-[3.5rem] w-full max-w-xs border-t border-white/20">
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl mb-4 text-center font-bold outline-none border border-white/10 text-white">
            <input type="text" id="ref-by" placeholder="Referral ID (Optional)" class="w-full bg-white/5 p-3 rounded-xl mb-6 text-[10px] text-center outline-none border border-white/5 text-white">
            <button onclick="login()" class="w-full grad-main py-5 rounded-2xl font-black uppercase text-xs shadow-xl active:scale-95 transition-all">Establish Link</button>
        </div>
    </section>

    <main id="app-ui" class="hidden">
        
        <div id="p-home" class="page active-page page-content p-6">
            <div class="flex justify-between items-center mt-4 mb-8">
                <div><h2 class="text-2xl font-black" id="v-user">User</h2><p class="text-[9px] text-green-400 font-bold uppercase tracking-widest">● System Verified</p></div>
                <button onclick="logout()" class="logout-btn w-12 h-12 flex items-center justify-center text-xl shadow-lg">🚀</button>
            </div>

            <div class="grad-main p-8 rounded-[3.5rem] mb-8 shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black opacity-80 uppercase italic">Capital Node</p>
                <h2 class="text-5xl font-black my-2" id="v-bal">₨ 0</h2>
                <div class="mt-8 flex justify-between items-center border-t border-white/10 pt-4">
                    <div><p class="text-[8px] opacity-60 uppercase">Daily Profit</p><p class="text-lg font-black" id="v-profit">₨ 0</p></div>
                    <button onclick="copyRef()" class="bg-black/30 px-4 py-2 rounded-xl text-[8px] font-black uppercase border border-white/10">🔗 Copy ID</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="claimDaily()" class="glass p-5 rounded-3xl text-center"><span class="text-2xl block mb-1">🎁</span><span class="text-[10px] font-black uppercase">Daily Bonus</span></button>
                <button onclick="changePage('spin')" class="glass p-5 rounded-3xl text-center"><span class="text-2xl block mb-1">🎡</span><span class="text-[10px] font-black uppercase">Lucky Spin</span></button>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass py-8 rounded-[2.5rem] font-black text-xs uppercase text-cyan-400 border border-cyan-400/20">Capital In</button>
                <button onclick="changePage('withdraw')" class="glass py-8 rounded-[2.5rem] font-black text-xs uppercase text-rose-500 border border-rose-500/20">Capital Out</button>
            </div>
        </div>

        <div id="p-invest" class="page page-content p-6">
            <h3 class="text-center font-black text-xl italic mb-8 uppercase text-blue-400">Node Markets</h3>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-spin" class="page page-content p-6 text-center">
            <h3 class="font-black text-xl mb-10 uppercase italic">Infinity Spin</h3>
            <div class="relative w-64 h-64 mx-auto mb-10">
                <div id="wheel" class="w-full h-full rounded-full spin-wheel grad-main flex items-center justify-center text-2xl font-black border-4 border-white/20">WHEEL</div>
                <div class="absolute -top-4 left-1/2 -translate-x-1/2 text-3xl">▼</div>
            </div>
            <button onclick="spinWheel()" id="spin-btn" class="grad-main px-12 py-4 rounded-2xl font-black uppercase text-xs">Spin Now (₨ 50)</button>
        </div>

        <div id="p-wallet" class="page page-content p-6">
            <h3 class="text-center font-black text-xl mb-8 uppercase italic">Deposit Capital</h3>
            <div class="space-y-3 mb-8">
                <div onclick="selM('JazzCash','03705519562')" class="glass p-5 rounded-2xl flex justify-between items-center border-l-4 border-yellow-500"><span>JazzCash</span><b>03705519562</b></div>
                <div onclick="selM('Easypaisa','03379827882')" class="glass p-5 rounded-2xl flex justify-between items-center border-l-4 border-green-500"><span>Easypaisa</span><b>03379827882</b></div>
                <div onclick="selM('Binance','BINANCE-ID-HERE')" class="glass p-5 rounded-2xl flex justify-between items-center border-l-4 border-orange-500"><span>Binance</span><b>USDT-TRC20</b></div>
            </div>
            <div id="dep-box" class="hidden glass p-6 rounded-[2.5rem] border border-white/10">
                <p id="m-info" class="text-center text-[10px] mb-6 text-cyan-400 font-bold uppercase"></p>
                <input type="number" id="d-amt" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold outline-none border border-white/10">
                <input type="text" id="d-tid" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold outline-none border border-white/10">
                <input type="file" id="d-file" class="w-full text-[10px] mb-6">
                <button id="dep-btn" onclick="subDep()" class="w-full grad-main py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Confirm Request</button>
            </div>
        </div>

        <div id="p-withdraw" class="page page-content p-6">
            <div class="glass p-8 rounded-[3.5rem] border-t-8 border-rose-600">
                <h3 class="text-2xl font-black text-rose-500 mb-6 uppercase text-center italic">Withdraw</h3>
                <select id="w-method" class="w-full bg-white/10 p-4 rounded-xl mb-4 text-xs font-black outline-none border border-white/10 appearance-none text-center">
                    <option value="Easypaisa">Easypaisa</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="Binance">Binance (USDT)</option>
                    <option value="Bank">Local Bank</option>
                </select>
                <input type="number" id="w-amt" placeholder="Amount (Min 100)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold border border-white/10 outline-none">
                <input type="text" id="w-acc" placeholder="Account Number" class="w-full bg-white/5 p-4 rounded-xl mb-8 text-center border border-white/10 outline-none">
                <button onclick="subWd()" class="w-full bg-rose-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Send Payout Request</button>
            </div>
        </div>

        <div id="p-support" class="page page-content p-6">
            <div class="glass p-8 rounded-[3rem] mb-6 border-l-4 border-blue-400">
                <h3 class="font-black text-xl mb-4 text-cyan-400 italic">About MintCrest</h3>
                <p class="text-[10px] opacity-70 leading-relaxed font-medium">MintCrest is a legally registered global node that secures your capital through AI-driven liquidity pools. We ensure 100% safety and guaranteed daily yields on all verified active plans.</p>
                <p class="text-[10px] mt-4 opacity-70 font-bold">Privacy Policy: All transactions are 256-bit encrypted.</p>
            </div>
            <button onclick="window.open('https://chat.whatsapp.com/YOUR_LINK')" class="w-full bg-green-600 py-6 rounded-3xl font-black text-xs uppercase mb-4 flex justify-center items-center gap-2"><span>💬 Join WhatsApp Group</span></button>
            <button onclick="changePage('chat')" class="w-full grad-main py-6 rounded-3xl font-black text-xs uppercase">✉ Help Desk / Admin</button>
        </div>

        <div id="p-history" class="page page-content p-6"><h3 class="font-black text-xl mb-8 uppercase italic">Node Logs</h3><div id="user-history" class="space-y-3"></div></div>

        <div id="p-chat" class="page page-content flex flex-col h-screen p-6">
            <h3 class="font-black text-xl mb-4 uppercase">Admin Chat</h3>
            <div id="chat-box" class="flex-1 overflow-y-auto mb-4 p-4 glass rounded-3xl space-y-3"></div>
            <div class="flex gap-2"><input type="text" id="chat-msg" class="flex-1 bg-white/10 p-4 rounded-2xl outline-none" placeholder="Type here..."><button onclick="sendChat()" class="grad-main px-6 rounded-2xl font-black">▶</button></div>
        </div>

    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#00040a] z-[50000] hidden overflow-y-auto p-8">
        <div class="flex justify-between items-center mb-10 border-b border-white/10 pb-6">
            <h2 class="text-xl font-black text-cyan-400 uppercase italic">Supreme Core</h2>
            <button onclick="closeAdm()" class="bg-rose-600 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>

        <div class="grid grid-cols-2 gap-4 mb-8 text-center">
            <div class="glass p-6 rounded-3xl"><p class="text-[8px] opacity-40 uppercase">Total Users</p><h4 id="adm-users-count" class="text-xl font-black">0</h4></div>
            <div class="glass p-6 rounded-3xl"><p class="text-[8px] opacity-40 uppercase">Pending</p><h4 id="adm-req-count" class="text-xl font-black text-yellow-400">0</h4></div>
        </div>

        <div class="glass p-6 rounded-3xl mb-8 border border-white/10">
            <p class="text-[10px] font-black text-blue-400 mb-4 uppercase">Broadcast Message</p>
            <input type="text" id="adm-msg" placeholder="Global Announcement..." class="w-full bg-white/5 p-4 rounded-xl text-xs mb-3 border border-white/10 outline-none">
            <button onclick="sendBC()" class="w-full grad-main py-4 rounded-xl text-[9px] font-black uppercase">Push Broadcast</button>
        </div>

        <div class="glass p-6 rounded-3xl mb-10 border-l-4 border-yellow-500">
            <p class="text-[10px] font-black text-yellow-500 mb-4 uppercase italic">Manual Control</p>
            <input type="text" id="adm-u" placeholder="Target User ID" class="w-full bg-white/5 p-4 rounded-xl text-xs mb-3 border border-white/10 outline-none">
            <div class="grid grid-cols-2 gap-2"><button onclick="editU('balance')" class="bg-blue-600 py-3 rounded-xl text-[8px] font-black">SET BALANCE</button><button onclick="editU('profit')" class="bg-cyan-600 py-3 rounded-xl text-[8px] font-black">SET PROFIT</button></div>
        </div>

        <div id="adm-reqs-list" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-7 flex justify-around items-center fixed bottom-0 w-full z-[2000] rounded-t-[3.5rem] border-t border-white/10">
        <button onclick="changePage('home')" id="n-home" class="nav-btn text-2xl nav-active">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-2xl">📈</button>
        <button onclick="changePage('history')" id="n-history" class="nav-btn text-2xl">📜</button>
        <button onclick="changePage('support')" id="n-support" class="nav-btn text-2xl">ℹ️</button>
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
        let user = null; let tapCount = 0; let curM = '';

        // LOGIN WITH NO-REFRESH LOGOUT FIX
        window.onload = () => {
            const saved = localStorage.getItem('mint_session_v29');
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            renderPlans();
        };

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const r = document.getElementById('ref-by').value.trim().toLowerCase();
            if(!n) return alert("Sweetie, enter your name! 😘");
            
            const ref = db.collection("users").doc(n);
            const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, profit: 0, refBy: r || 'Direct', time: Date.now(), plans: [] });
            
            user = (await ref.get()).data();
            localStorage.setItem('mint_session_v29', n);
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            startSync(n);
        }

        // REAL-TIME SYNC
        function startSync(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                if(d.exists) {
                    user = d.data();
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
                    document.getElementById('v-user').innerText = n.toUpperCase();
                }
            });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const l = document.getElementById('user-history'); l.innerHTML = '';
                s.forEach(d => {
                    const x = d.data();
                    l.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between border-l-4 ${x.status==='pending'?'border-yellow-400':'border-green-400'}"><span class="text-[10px] font-black uppercase">${x.type} - ₨ ${x.amount}</span><span class="text-[8px] uppercase">${x.status}</span></div>`;
                });
            });
            syncChat(n);
        }

        // PLANS WITH TIMER & REFRRAL BONUS
        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            for(let i=1; i<=25; i++) {
                const isVIP = i > 20;
                let price = i === 1 ? 200 : (i * 500) - 300; 
                if(isVIP) price = (i-20) * 10000;
                let days = i <= 5 ? 15 + (i * 2) : 30 + i;
                const daily = (price * (isVIP ? 0.15 : 0.08)).toFixed(0);
                
                list.innerHTML += `<div class="glass p-6 rounded-[2.5rem] border-l-4 ${isVIP ? 'border-yellow-500 shadow-xl shadow-yellow-500/10' : 'border-blue-500'} relative mb-4">
                    ${isVIP ? '<span class="absolute top-0 right-0 bg-yellow-500 text-black text-[7px] font-black px-4 py-1 rounded-bl-2xl">SPECIAL OFFER</span>' : ''}
                    <div class="flex justify-between mb-4">
                        <div><h4 class="font-black text-[10px] uppercase">${isVIP ? 'Royal P'+(i-20) : 'Fleet Node V'+i}</h4>
                        <p class="text-[8px] opacity-60">${isVIP ? 'LIFETIME ACCESS' : 'Duration: '+days+' Days'}</p></div>
                        <div class="text-right"><p class="text-[7px] opacity-40 uppercase font-black">Price</p><b class="text-sm">₨ ${price}</b></div>
                    </div>
                    <div class="flex justify-between items-center"><span class="text-green-400 font-black text-xs">₨ ${daily}/Day</span>
                    <button onclick="buyPlan(${price}, ${days}, ${isVIP})" class="bg-white/10 px-6 py-3 rounded-xl text-[8px] font-black uppercase transition-all active:scale-90">Purchase</button></div>
                    <div id="timer-${i}" class="text-[7px] mt-2 opacity-50 uppercase font-bold text-center"></div>
                </div>`;
            }
        }

        async function buyPlan(p, d, isVIP) {
            if(user.balance < p) return alert("Sweetie, low balance! 😘");
            await db.collection("users").doc(user.name).update({ 
                balance: firebase.firestore.FieldValue.increment(-p),
                plans: firebase.firestore.FieldValue.arrayUnion({ price: p, daily: (p * (isVIP ? 0.15 : 0.08)), start: Date.now(), days: d })
            });
            alert("Node Activated Successfully! 🚀");
        }

        // SPIN WHEEL
        async function spinWheel() {
            if(user.balance < 50) return alert("50 balance required, sweetie! 😘");
            const btn = document.getElementById('spin-btn'); btn.disabled = true;
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-50) });
            const deg = Math.floor(3000 + Math.random() * 3000);
            document.getElementById('wheel').style.transform = `rotate(${deg}deg)`;
            setTimeout(async () => {
                const win = [0, 10, 50, 100, 200, 500][Math.floor(Math.random()*6)];
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(win) });
                alert(`Sweetie, you won ₨ ${win}! 😘`);
                btn.disabled = false;
            }, 4000);
        }

        // DEPOSIT & WITHDRAW
        function selM(m, a) { curM = m; document.getElementById('dep-box').classList.remove('hidden'); document.getElementById('m-info').innerText = `TRANSFER TO ${m}: ${a}`; }
        async function subDep() {
            const btn = document.getElementById('dep-btn');
            const a = document.getElementById('d-amt').value; const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(!a || !t || !f) return alert("Fill all details, Sweetie! 😘");
            btn.disabled = true; btn.innerText = "Processing...";
            try {
                const sRef = storage.ref(`proofs/${Date.now()}_${user.name}`);
                await sRef.put(f);
                const url = await sRef.getDownloadURL();
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "Deposit", status: "pending", time: Date.now(), refBy: user.refBy });
                alert("Deposit Logged! Admin will verify soon. 😘"); changePage('home');
            } catch(e) { alert("Error: Check Rules! " + e.message); }
            btn.disabled = false; btn.innerText = "Confirm Request";
        }

        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value; const m = document.getElementById('w-method').value;
            if(!a || a < 100 || a > user.balance) return alert("Invalid Amount, Sweetie! 😘");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, method: m, type: "Withdrawal", status: "pending", time: Date.now() });
            alert("Withdrawal Requested! 😘"); changePage('home');
        }

        // ADMIN TOOLS
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Pass:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdm() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function syncAdm() {
            db.collection("users").onSnapshot(s => document.getElementById('adm-users-count').innerText = s.size);
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                document.getElementById('adm-req-count').innerText = s.size;
                const l = document.getElementById('adm-reqs-list'); l.innerHTML = '';
                s.forEach(d => {
                    const x = d.data();
                    l.innerHTML += `<div class="glass p-5 rounded-3xl border-l-4 border-cyan-400 text-[10px]">
                        <p class="font-black">${x.user.toUpperCase()} | ${x.type} | ₨ ${x.amount}</p>
                        <p class="opacity-60 mb-1">Referral By: ${x.refBy || 'None'}</p>
                        <p class="opacity-60 mb-3">ID/Acc: ${x.tid || x.acc} (${x.method||'DEP'})</p>
                        ${x.proof ? `<a href="${x.proof}" target="_blank" class="bg-blue-600 block text-center py-2 rounded-xl mb-3 font-bold uppercase">View Proof</a>` : ''}
                        <div class="flex gap-2"><button onclick="hnd('${d.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 bg-green-600 py-3 rounded-xl font-bold">APPROVE</button>
                        <button onclick="hnd('${d.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-rose-600 py-3 rounded-xl font-bold">REJECT</button></div>
                    </div>`;
                });
            });
        }
        async function hnd(id, u, amt, act, type) {
            if(act==='approved' && type === "Deposit") await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(amt) });
            if(act==='rejected' && type === "Withdrawal") await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(amt) });
            await db.collection("requests").doc(id).update({ status: act });
        }
        async function editU(f) { const u = document.getElementById('adm-u').value.toLowerCase(); const v = parseInt(prompt("New Value:")); await db.collection("users").doc(u).update({ [f]: v }); alert("Success!"); }

        // UTILITIES
        function logout() { localStorage.removeItem('mint_session_v29'); location.reload(); }
        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('nav-active'));
            document.getElementById('p-'+p).classList.add('active-page');
            if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('nav-active');
        }
        function copyRef() { navigator.clipboard.writeText(user.name); alert("Referral ID Copied, sweetie! 😘"); }
        async function claimDaily() {
            const today = new Date().toDateString();
            if(localStorage.getItem('daily_bonus') === today) return alert("Already claimed today, sweetie! 😘");
            const bonus = Math.floor(Math.random() * 50) + 10;
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(bonus) });
            localStorage.setItem('daily_bonus', today);
            alert(`Sweetie, you got ₨ ${bonus} bonus! 😘`);
        }

        // CHAT SYNC
        function sendChat() { const m = document.getElementById('chat-msg').value; if(!m) return; db.collection("chats").add({ user: user.name, msg: m, time: Date.now(), role: 'user' }); document.getElementById('chat-msg').value = ''; }
        function syncChat(n) { db.collection("chats").where("user", "==", n).orderBy("time", "asc").onSnapshot(s => { const box = document.getElementById('chat-box'); box.innerHTML = ''; s.forEach(d => { const x = d.data(); box.innerHTML += `<div class="p-3 rounded-2xl text-[10px] ${x.role==='user'?'bg-blue-600 self-end ml-auto':'bg-white/10 self-start mr-auto'} max-w-[80%]">${x.msg}</div>`; }); box.scrollTop = box.scrollHeight; }); }
    </script>
</body>
</html>
