<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Elite Asset Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #F8FAFC; color: #1E293B; overflow-x: hidden; }
        .vibrant-bg { background: linear-gradient(-45deg, #EEF2FF, #FAF5FF, #F0FDF4, #FFFBEB); background-size: 400% 400%; animation: gradientBG 15s ease infinite; }
        @keyframes gradientBG { 0% { background-position: 0% 50%; } 50% { background-position: 100% 50%; } 100% { background-position: 0% 50%; } }
        .glass-light { background: rgba(255, 255, 255, 0.7); backdrop-filter: blur(15px); border: 1px solid rgba(255, 255, 255, 0.5); box-shadow: 0 10px 30px -10px rgba(0,0,0,0.05); }
        .card-blue { background: linear-gradient(135deg, #3B82F6 0%, #2563EB 100%); color: white; box-shadow: 0 20px 40px -10px rgba(59, 130, 246, 0.3); }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; white-space: nowrap; }
        .active-tab { color: #3B82F6; font-weight: 800; transform: translateY(-3px); }
        .page { display: none; animation: slideUp 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); filter: blur(5px); } to { opacity: 1; transform: translateY(0); filter: blur(0); } }
        .badge-pending { background: #FEF3C7; color: #92400E; padding: 2px 8px; border-radius: 8px; font-size: 8px; font-weight: bold; }
        .badge-approved { background: #DCFCE7; color: #166534; padding: 2px 8px; border-radius: 8px; font-size: 8px; font-weight: bold; }
        .badge-rejected { background: #FEE2E2; color: #991B1B; padding: 2px 8px; border-radius: 8px; font-size: 8px; font-weight: bold; }
    </style>
</head>
<body class="h-screen flex flex-col vibrant-bg">

    <div id="promo-ticker" class="hidden bg-white/50 backdrop-blur-md border-b border-blue-100 py-2 overflow-hidden z-[500]">
        <div class="animate-marquee text-[10px] font-black uppercase tracking-widest text-blue-600 italic">
            📢 <span id="ticker-text">Syncing System Updates...</span>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-white flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-4xl font-black italic tracking-tighter mb-2 uppercase text-slate-800">MINT<span class="text-blue-600">CREST</span></h1>
            <p class="text-slate-400 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold italic">Official Digital Asset Vault</p>
            <div class="glass-light p-10 rounded-[3rem] border-t-4 border-blue-600">
                <input type="text" id="user-name" placeholder="Legal Full Name" class="w-full bg-slate-50 p-5 rounded-2xl border border-slate-100 outline-none text-center font-bold mb-5 focus:border-blue-500 text-slate-800">
                <button onclick="login()" class="w-full bg-slate-900 text-white py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 shadow-xl">Secure Access</button>
            </div>
            <p class="mt-8 text-[7px] text-slate-400 uppercase font-bold tracking-widest">© 2026 MintCrest Gold Enterprise</p>
        </div>
    </section>

    <header class="p-6 flex justify-between items-center sticky top-0 z-[500] bg-white/60 backdrop-blur-md border-b border-gray-200/50">
        <h1 class="text-xl font-black italic tracking-tighter uppercase text-slate-800">MINT<span class="text-blue-600 text-2xl">CREST</span></h1>
        <div onclick="adminTap()" class="bg-blue-100 p-2.5 rounded-xl border border-blue-200 active:scale-90 transition-all">
            <i class="fa-solid fa-fingerprint text-blue-600 text-lg"></i>
        </div>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="card-blue p-8 rounded-[2.5rem] mb-6 relative overflow-hidden">
                <p class="text-[10px] opacity-80 font-black mb-1 uppercase tracking-widest">Active Assets Pool</p>
                <h2 class="text-5xl font-black tracking-tighter mb-2" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="text-[9px] font-black uppercase text-blue-200 italic mb-6">System Ready</div>
                <div class="flex items-center justify-between border-t border-white/20 pt-6">
                    <div>
                        <p class="text-[8px] opacity-70 font-black uppercase">Net Profit</p>
                        <p id="v-profit" class="text-lg font-black text-white">₨ 0</p>
                    </div>
                    <div class="text-right">
                        <p class="text-[8px] opacity-70 font-black uppercase tracking-tighter">Current Fleet</p>
                        <p id="tier-tag" class="text-[10px] font-black uppercase italic text-blue-100">Scanning...</p>
                    </div>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass-light p-6 rounded-[2rem] flex flex-col items-center gap-2 border-b-4 border-blue-500 active:scale-95 transition-all">
                    <i class="fa-solid fa-plus text-blue-600"></i>
                    <span class="text-[9px] font-black uppercase text-slate-600">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass-light p-6 rounded-[2rem] flex flex-col items-center gap-2 border-b-4 border-rose-500 active:scale-95 transition-all">
                    <i class="fa-solid fa-paper-plane text-rose-600"></i>
                    <span class="text-[9px] font-black uppercase text-slate-600">Withdraw</span>
                </button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-600 text-lg tracking-widest">Active Trading Fleet</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-slate-800">Security Ledger</h2>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-4">
            <div class="glass-light p-8 rounded-[2.5rem] border-l-8 border-purple-600 shadow-lg mb-4">
                <h3 class="text-purple-600 font-black text-[10px] uppercase mb-4 text-center tracking-widest">🎁 Claim Bonus Code</h3>
                <div class="flex gap-2">
                    <input type="text" id="promo-input" placeholder="ENTER CODE" class="flex-1 bg-white p-4 rounded-xl text-[10px] border border-slate-100 outline-none text-center font-black uppercase text-slate-800">
                    <button onclick="applyPromo()" class="bg-purple-600 text-white px-6 rounded-xl font-black text-[10px] uppercase active:scale-95">Redeem</button>
                </div>
            </div>

            <div class="glass-light p-6 rounded-[2.5rem] border-l-8 border-blue-600">
                <h3 class="text-blue-600 font-black text-[10px] uppercase mb-2">🏢 Official LLC Details</h3>
                <p class="text-[8px] font-bold text-slate-600 leading-relaxed">
                    MintCrest Gold Enterprise (LLC) is a registered private limited firm.<br>
                    <b>Reg No:</b> 04296474 | <b>Status:</b> Active<br>
                    <b>Location:</b> Valiant House, 12 Knoll Rise, Orpington, BR6 0PG
                </p>
            </div>

            <div class="glass-light p-6 rounded-[2.5rem] border-l-8 border-slate-400">
                <h3 class="text-slate-800 font-black text-[10px] uppercase mb-2">📜 Privacy Policy</h3>
                <p class="text-[7.5px] text-slate-500 italic">User data is encrypted and used only for security verification and payout authorization.</p>
            </div>

            <div class="glass-light p-6 rounded-[2.5rem] border-l-8 border-yellow-500">
                <h3 class="text-yellow-600 font-black text-[10px] uppercase mb-2 text-center">❓ Common Questions</h3>
                <div class="space-y-2 text-[8px] font-bold text-slate-700">
                    <p>• <b>Withdrawal Time:</b> Usually processed within 1-24 hours.</p>
                    <p>• <b>Minimum Deposit:</b> Start your fleet from Rs 200.</p>
                </div>
            </div>

            <div class="glass-light p-8 rounded-[2.5rem] text-center border-l-8 border-green-500">
                <h3 class="text-green-600 font-black text-[10px] uppercase mb-4">Official Community</h3>
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="inline-block bg-green-50 text-green-600 px-8 py-3 rounded-xl font-black text-[9px] uppercase border border-green-100">📢 Join WhatsApp</a>
            </div>

            <div class="glass-light p-8 rounded-[2.5rem] text-center border-l-8 border-blue-500">
                <h3 class="text-blue-600 font-black text-[10px] uppercase mb-4">Help Desk</h3>
                <textarea id="support-msg" placeholder="Message for Admin..." class="w-full bg-white p-4 rounded-xl text-[10px] border border-slate-100 outline-none mb-4 h-24 font-bold text-slate-800"></textarea>
                <button onclick="sendSupport()" class="w-full bg-blue-600 text-white py-4 rounded-xl font-black text-[10px] uppercase active:scale-95 shadow-lg shadow-blue-100">Submit Ticket</button>
            </div>

            <div class="glass-light p-8 rounded-[2.5rem] text-center border-l-8 border-slate-800">
                <p class="text-[8px] font-bold text-slate-400 mb-4 uppercase italic">Your Fleet Invite Link</p>
                <input type="text" id="ref-link" readonly class="w-full bg-slate-50 p-4 rounded-xl text-[10px] text-center font-bold mb-4 border border-slate-100 outline-none text-slate-500">
                <button onclick="copyRef()" class="bg-slate-800 text-white px-10 py-3 rounded-xl font-black text-[9px] uppercase active:scale-95">Copy Invite</button>
            </div>

            <button onclick="logout()" class="w-full glass-light p-6 rounded-[2.5rem] text-center text-[10px] font-black text-rose-500 uppercase active:scale-95">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="space-y-3 mb-8">
                <div class="glass-light p-5 rounded-3xl flex justify-between items-center border-l-4 border-yellow-500">
                    <div><p class="text-[7px] font-black opacity-40 uppercase">JazzCash/SadaPay</p><p class="font-black text-sm text-slate-800">03705519562</p></div>
                    <button onclick="copyToClipboard('03705519562')" class="bg-yellow-100 p-3 rounded-xl active:bg-yellow-200"><i class="fa-solid fa-copy text-yellow-600"></i></button>
                </div>
                <div class="glass-light p-5 rounded-3xl flex justify-between items-center border-l-4 border-green-500">
                    <div><p class="text-[7px] font-black opacity-40 uppercase">EasyPaisa</p><p class="font-black text-sm text-slate-800">03379827882</p></div>
                    <button onclick="copyToClipboard('03379827882')" class="bg-green-100 p-3 rounded-xl active:bg-green-200"><i class="fa-solid fa-copy text-green-600"></i></button>
                </div>
            </div>
            <div class="glass-light p-8 rounded-[2.5rem] space-y-4 text-center">
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white p-5 rounded-2xl border border-slate-100 text-center font-bold text-slate-800">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white p-5 rounded-2xl border border-slate-100 text-center font-bold uppercase text-slate-800">
                <label class="block w-full bg-slate-50 p-5 rounded-2xl border-2 border-dashed border-slate-200 text-center cursor-pointer">
                    <span id="file-label" class="text-[9px] font-bold text-slate-400 uppercase italic">📸 Upload Receipt Proof</span>
                    <input type="file" id="dep-proof" accept="image/*" class="hidden" onchange="document.getElementById('file-label').innerText = 'Screenshot Secured ✅'; document.getElementById('file-label').style.color = '#10B981'">
                </label>
                <button id="dep-btn" onclick="submitDeposit()" class="w-full bg-blue-600 text-white py-6 rounded-2xl font-black text-[10px] uppercase shadow-xl shadow-blue-100 active:scale-95">Verify Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass-light p-10 rounded-[3rem] border-l-8 border-rose-500 text-center">
                <h3 class="font-black text-rose-500 mb-6 uppercase text-sm italic tracking-widest">Payout Terminal</h3>
                
                <select id="wd-method" class="w-full bg-white p-5 rounded-2xl mb-4 text-[10px] font-black border border-slate-100 text-slate-800 outline-none">
                    <option value="JazzCash">JazzCash</option>
                    <option value="EasyPaisa">EasyPaisa</option>
                    <option value="SadaPay">SadaPay</option>
                    <option value="Bank Transfer">Bank Transfer</option>
                </select>

                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white p-5 rounded-2xl mb-4 text-center font-bold border border-slate-100 text-slate-800">
                <input type="text" id="wd-acc-name" placeholder="Account Holder Name" class="w-full bg-white p-5 rounded-2xl mb-4 text-center text-[10px] font-bold border border-slate-100 text-slate-800">
                <input type="text" id="wd-acc-num" placeholder="Account Number/IBAN" class="w-full bg-white p-5 rounded-2xl mb-8 text-center text-[10px] font-bold border border-slate-100 text-slate-800">
                
                <button onclick="submitWithdraw()" class="w-full bg-rose-600 text-white py-6 rounded-2xl font-black text-[10px] uppercase shadow-xl shadow-rose-100 active:scale-95">Authorize Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-white z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-600 uppercase italic tracking-tighter">Master Console</h2>
            <button onclick="closeAdmin()" class="bg-rose-100 text-rose-600 px-6 py-2 rounded-xl text-[10px] font-black border border-rose-200 uppercase">Exit Terminal</button>
        </div>
        <div class="flex gap-2 mb-8 overflow-x-auto pb-2">
            <button onclick="showAdmTab('requests')" class="bg-blue-600 text-white px-6 py-3 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">Requests</button>
            <button onclick="showAdmTab('users')" class="bg-slate-100 text-slate-600 px-6 py-3 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">Database</button>
            <button onclick="showAdmTab('promos')" class="bg-purple-600 text-white px-6 py-3 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">Promos</button>
            <button onclick="showAdmTab('broadcast')" class="bg-yellow-500 text-white px-6 py-3 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">Broadcast</button>
        </div>
        
        <div id="adm-sec-requests" class="adm-tab space-y-4"></div>
        <div id="adm-sec-users" class="adm-tab hidden space-y-3"></div>
        
        <div id="adm-sec-promos" class="adm-tab hidden space-y-6">
            <div class="glass-light p-8 rounded-[2.5rem] border-l-8 border-purple-600">
                <h3 class="text-purple-600 font-black text-xs uppercase mb-4 text-center italic">Deploy New Promocode</h3>
                <input type="text" id="new-p-code" placeholder="CODE NAME (e.g. GOLD100)" class="w-full p-4 mb-2 bg-white rounded-xl text-[10px] font-black uppercase border">
                <input type="number" id="new-p-val" placeholder="Reward Value (PKR)" class="w-full p-4 mb-4 bg-white rounded-xl text-[10px] font-black border">
                <button onclick="createPromo()" class="w-full bg-purple-600 text-white py-4 rounded-xl font-black text-[10px] uppercase shadow-lg shadow-purple-100">Activate Code</button>
            </div>
            <div id="promo-list" class="space-y-2"></div>
        </div>

        <div id="adm-sec-broadcast" class="adm-tab hidden glass-light p-8 rounded-[2.5rem]">
            <h3 class="text-yellow-600 font-black text-xs uppercase mb-4 text-center italic">Live Ticker Update</h3>
            <textarea id="bc-msg" placeholder="Broadcast message..." class="w-full bg-white p-5 rounded-2xl text-[10px] border border-slate-100 outline-none mb-4 h-24 font-bold"></textarea>
            <button onclick="sendBroadcast()" class="w-full bg-yellow-500 text-white py-4 rounded-xl font-black text-[10px] uppercase shadow-lg shadow-yellow-100">🚀 Deploy Broadcast</button>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden bg-white/80 backdrop-blur-xl border-t border-slate-100 p-6 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[3.5rem] shadow-[0_-10px_40px_rgba(0,0,0,0.03)]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center transition-all active-tab"><i class="fa-solid fa-house-user text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center transition-all opacity-40"><i class="fa-solid fa-chart-line text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center transition-all opacity-40"><i class="fa-solid fa-receipt text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center transition-all opacity-40"><i class="fa-solid fa-building-shield text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Firm</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3, d: 30 }, { n: "Micro-Elite II", p: 500, r: 3.2, d: 30 },
            { n: "Bronze-S", p: 1000, r: 3.5, d: 30 }, { n: "Bronze-X", p: 2000, r: 3.8, d: 30 },
            { n: "Silver-S", p: 3000, r: 4.2, d: 30 }, { n: "Silver-X", p: 5000, r: 4.5, d: 30 },
            { n: "Gold-S", p: 7000, r: 5, d: 30 }, { n: "Gold-X", p: 10000, r: 5.5, d: 30 },
            { n: "Gold-Master", p: 15000, r: 6, d: 30 }, { n: "Platinum-S", p: 20000, r: 6.5, d: 30 },
            { n: "Platinum-X", p: 30000, r: 7, d: 30 }, { n: "Diamond-S", p: 40000, r: 7.5, d: 30 },
            { n: "Diamond-X", p: 50000, r: 8, d: 30 }, { n: "Royal-S", p: 75000, r: 9, d: 30 },
            { n: "Royal-Grand", p: 100000, r: 10, d: 30 },
            { n: "⚡ FLASH 24H", p: 500, r: 12, s: true },
            { n: "⭐ VIP ACCESS", p: 2500, r: 14, s: true },
            { n: "💎 ELITE PASS", p: 8000, r: 15, s: true },
            { n: "🚀 ROCKET PRO", p: 12000, r: 18, s: true },
            { n: "👑 CROWN KING", p: 25000, r: 22, s: true }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000);
            listenForPromos();
        };

        function listenForPromos() {
            db.collection("app_data").doc("announcement").onSnapshot(doc => {
                if(doc.exists) {
                    document.getElementById('promo-ticker').classList.remove('hidden');
                    document.getElementById('ticker-text').innerText = doc.data().message;
                }
            });
        }

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const d = await ref.get();
            if(!d.exists) await ref.set({ name, balance: 0, profit: 0, usedPromos: [], time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive" });
            startSync(name); document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); document.getElementById('ref-link').value = window.location.origin + window.location.pathname + "?ref=" + name;
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit||0).toLocaleString(); document.getElementById('tier-tag').innerText = user.tierName; checkProfitReq(user); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center opacity-20 py-20 text-[9px] font-black uppercase italic">Ledger Empty</p>' : '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { 
                    list.innerHTML += `<div class="glass-light p-4 rounded-2xl flex justify-between items-center mb-2 font-black text-[9px] uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px] font-bold">${new Date(d.time).toLocaleTimeString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; 
                });
            });
        }

        // New Promocode Logic
        async function applyPromo() {
            const code = document.getElementById('promo-input').value.trim().toUpperCase(); if(!code) return;
            if(user.usedPromos && user.usedPromos.includes(code)) return alert("Code already claimed! 😘");
            const ref = await db.collection("promocodes").doc(code).get();
            if(ref.exists) {
                const val = ref.data().value;
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(val), usedPromos: firebase.firestore.FieldValue.arrayUnion(code) });
                alert(`Verified! ₨ ${val} added to your vault. 😘`);
                document.getElementById('promo-input').value = '';
            } else alert("Invalid or Expired Code. ❌");
        }

        async function createPromo() {
            const c = document.getElementById('new-p-code').value.trim().toUpperCase(); const v = parseInt(document.getElementById('new-p-val').value);
            if(c && v) { await db.collection("promocodes").doc(c).set({ value: v }); alert("Promo Deployed! ✅"); document.getElementById('new-p-code').value=''; document.getElementById('new-p-val').value=''; }
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass-light p-5 rounded-[2rem] flex justify-between items-center active:scale-95 cursor-pointer mb-2 border-l-4 ${p.s ? 'border-blue-500' : 'border-slate-300'}"><div><h4 class="font-black text-[10px] uppercase text-slate-800">${p.n}</h4><p class="text-[8px] text-green-600 font-black uppercase">${p.r}% Daily Yield</p></div><div class="text-right font-black text-[11px] text-slate-900">₨ ${p.p.toLocaleString()}</div></div>`;
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const f = document.getElementById('dep-proof'); const b = document.getElementById('dep-btn');
            if(!a || !t || !f.files[0]) return alert("Missing Proof!"); b.disabled = true; b.innerText = "UPLOADING...";
            const r = new FileReader(); r.readAsDataURL(f.files[0]); r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: r.result, type: "deposit", status: "pending", time: Date.now() });
                alert("Request Sent! 😘"); b.disabled = false; b.innerText = "VERIFY FUNDING"; changePage('activity');
            };
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const method = document.getElementById('wd-method').value;
            const accName = document.getElementById('wd-acc-name').value; const accNum = document.getElementById('wd-acc-num').value;
            if(!a || !accName || !accNum) return alert("Fill all fields!");
            if(parseInt(a) > user.balance) return alert("Insufficient Balance!");
            const detailStr = `🏦 ${method} | 👤 ${accName} | 💳 ${accNum}`;
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: detailStr, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) });
            alert("Payout Queued! 😘"); changePage('activity');
        }

        async function buy(p, roi, tName) {
            if(user.balance < p) { alert("Low Funds! Redirecting to Wallet..."); changePage('wallet'); }
            else { if(confirm("Activate "+tName+"?")) { await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, lastReqTime: Date.now() }); alert("Fleet Ready! 🚀"); changePage('activity'); } }
        }

        async function checkProfitReq(u) { if (u.activeTier > 0 && (Date.now() - u.lastReqTime) >= 86400000) { const amt = (u.activeTier * u.tierROI) / 100; await db.collection("requests").add({ user: u.name, amount: amt, type: "Daily Profit Yield", status: "pending", time: Date.now() }); await db.collection("users").doc(u.name).update({ lastReqTime: Date.now() }); } }
        function updateCountdown() { if (user && user.activeTier > 0) { const d = (user.lastReqTime + 86400000) - Date.now(); if(d>0) { const h = Math.floor(d/3600000); const m = Math.floor((d%3600000)/60000); const s = Math.floor((d%60000)/1000); document.getElementById('countdown-display').innerText = `YIELD IN: ${h}H ${m}M ${s}S`; } else { document.getElementById('countdown-display').innerText = "YIELD READY"; } } else { document.getElementById('countdown-display').innerText = "SYSTEM IDLE"; } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.add('opacity-40')); document.getElementById('p-'+p).classList.add('active-page'); document.getElementById('n-'+p).classList.remove('opacity-40'); document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { if(confirm("Terminate?")) { localStorage.removeItem('mc_user'); location.reload(); } }
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Terminal Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } setTimeout(()=>tapCount=0,3000); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }
        function copyRef() { navigator.clipboard.writeText(document.getElementById('ref-link').value); alert("Link Copied! 😘"); }
        function copyToClipboard(t) { navigator.clipboard.writeText(t); alert("Number Copied! 😘"); }
        async function sendSupport() { const m = document.getElementById('support-msg').value.trim(); if(!m) return; await db.collection("requests").add({ user: user.name, message: m, type: "Support Ticket", status: "pending", time: Date.now(), amount: 0 }); alert("Ticket Sent!"); }
        async function sendBroadcast() { const m = document.getElementById('bc-msg').value.trim(); if(!m) return; await db.collection("app_data").doc("announcement").set({ message: m, time: Date.now() }); alert("Broadcast Deployed!"); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { 
                    const d = doc.data(); const pBtn = d.proof ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-blue-100 text-blue-600 p-2 rounded text-[8px] font-black">📸 PROOF</button>` : '';
                    const withdrawDetails = d.type === 'withdraw' ? `<div class="mt-2 p-2 bg-rose-50 border border-rose-100 rounded-lg text-rose-700 font-bold">${d.acc}</div>` : '';
                    list.innerHTML += `<div class="glass-light p-5 rounded-2xl mb-4 border border-slate-100"><div class="flex justify-between items-start text-[9px] font-black uppercase"><div><span class="text-blue-600">${d.user}</span><br><span class="text-slate-800 text-sm">Rs ${d.amount.toLocaleString()}</span><br><span class="opacity-50">${d.type}</span></div><div class="flex gap-2">${pBtn}<button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 text-white px-3 py-2 rounded-lg">YES</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-rose-600 text-white px-3 py-2 rounded-lg">NO</button></div></div>${withdrawDetails}</div>`;
                });
            });
            db.collection("users").onSnapshot(snap => { const list = document.getElementById('adm-sec-users'); list.innerHTML = ''; snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass-light p-4 rounded-xl flex justify-between items-center text-[8px] font-black uppercase mb-2"><div>${u.name}</div><div>Bal: ${u.balance}</div><button onclick="db.collection('users').doc('${u.name}').delete()" class="text-rose-600"><i class="fa-solid fa-trash"></i></button></div>`; }); });
            db.collection("promocodes").onSnapshot(snap => {
                const list = document.getElementById('promo-list'); list.innerHTML = '';
                snap.forEach(doc => { list.innerHTML += `<div class="glass-light p-3 rounded-xl flex justify-between items-center text-[10px] font-black uppercase mb-2"><div>${doc.id} (₨ ${doc.data().value})</div><button onclick="db.collection('promocodes').doc('${doc.id}').delete()" class="text-rose-600 underline">DELETE</button></div>`; });
            });
        }

        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(act==='approved' && (type==='deposit' || type.includes('Profit'))) { await ref.update({ balance: (doc.data().balance||0) + amt }); }
            else if(act==='rejected' && type==='withdraw') { await ref.update({ balance: (doc.data().balance||0) + amt }); }
            await db.collection("requests").doc(id).update({ status: act });
        }
    </script>
</body>
</html>
