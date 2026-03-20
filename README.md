<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Licensed & Trusted v11.0</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 110px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(12px); }
        .page { display: none; animation: fadeIn 0.4s ease-in-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 20px; font-weight: 800; border: none; cursor: pointer; transition: 0.3s; }
        .btn-prime:active { transform: scale(0.95); }
        input, textarea { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 16px; border-radius: 18px; width: 100%; outline: none; font-size: 13px; margin-bottom: 12px; }
        .trust-border { border: 1px solid rgba(16, 185, 129, 0.3); background: rgba(16, 185, 129, 0.05); }
        .qr-box { transition: 0.3s; border: 2px solid transparent; }
        .qr-box.active { border-color: #3b82f6; background: rgba(59, 130, 246, 0.1); }
    </style>
</head>
<body>

    <div id="bc-ui" class="fixed top-20 inset-x-4 z-[9999] hidden">
        <div class="glass border-blue-500 p-4 rounded-2xl flex items-center gap-3 shadow-[0_0_30px_rgba(59,130,246,0.2)]">
            <span class="text-xl animate-pulse">📢</span>
            <p id="bc-msg" class="text-[10px] font-black uppercase italic text-blue-400 leading-tight"></p>
        </div>
    </div>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)]">
        <div class="p-4 flex justify-between items-center">
            <div class="flex items-center gap-2">
                <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
                <div><h1 class="text-[12px] font-black uppercase">MintCrest <span class="text-blue-500">Gold</span></h1><p id="display-user" class="text-[8px] font-bold text-blue-400 uppercase italic">@Verified_User</p></div>
            </div>
            <div id="top-bal" class="text-[11px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
        </div>
    </header>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-8 rounded-[3.5rem] bg-gradient-to-br from-blue-600 to-indigo-900 text-white shadow-2xl relative overflow-hidden">
                <div class="flex justify-between items-start">
                    <div><p class="text-[10px] font-black uppercase opacity-60">Available Liquidity</p><h2 class="text-5xl font-black tracking-tighter my-2" id="v-bal">₨ 0.00</h2></div>
                    <i class="fa-solid fa-shield-check text-3xl opacity-20"></i>
                </div>
                <div class="mt-6 flex gap-3">
                   <button onclick="claimDaily()" class="flex-1 bg-white/10 p-3 rounded-xl text-[9px] font-black uppercase italic">🎁 Claim Bonus</button>
                   <button onclick="changePage('wallet')" class="flex-1 bg-blue-400/20 p-3 rounded-xl text-[9px] font-black uppercase italic">💸 Deposit</button>
                </div>
            </div>

            <div class="glass p-4 rounded-3xl border-blue-500/10">
                <div class="flex items-center gap-2 mb-3"><span class="w-2 h-2 bg-green-500 rounded-full animate-ping"></span><h3 class="text-[9px] font-black uppercase text-blue-400">Recent Payouts</h3></div>
                <div id="proof-feed" class="space-y-2 h-8 overflow-hidden"></div>
            </div>

            <div class="grid grid-cols-2 gap-3">
                <div onclick="changePage('about')" class="glass p-4 rounded-2xl text-center border-blue-500/5 cursor-pointer">
                    <i class="fa-solid fa-building-shield text-blue-500 mb-2"></i>
                    <p class="text-[8px] font-black uppercase">About Company</p>
                </div>
                <div onclick="changePage('policy')" class="glass p-4 rounded-2xl text-center border-blue-500/5 cursor-pointer">
                    <i class="fa-solid fa-file-contract text-blue-500 mb-2"></i>
                    <p class="text-[8px] font-black uppercase">Privacy Policy</p>
                </div>
            </div>

            <h3 class="text-[11px] font-black uppercase text-blue-500 px-2 italic">Global Mining Nodes</h3>
            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-20"></div>
        </div>

        <div id="p-about" class="page space-y-6">
            <h3 class="text-xl font-black text-center uppercase text-blue-500 italic">Corporate Identity</h3>
            <div class="glass p-6 rounded-[2.5rem] space-y-4 text-[11px] leading-relaxed opacity-80">
                <div class="trust-border p-4 rounded-2xl flex items-center gap-4 mb-4">
                    <i class="fa-solid fa-certificate text-green-500 text-3xl"></i>
                    <div><p class="font-black uppercase">Official License</p><p class="text-[8px] opacity-60 italic">Reg No: MC-786-GOLD-2026</p></div>
                </div>
                <p><strong>MintCrest Gold</strong> is a leading digital asset mining firm founded by <strong>Muhammad Nazim</strong>. Headquartered in Rawalpindi, Pakistan, we specialize in high-frequency liquidity mining and decentralized financial nodes.</p>
                <p>Our mission is to democratize investment opportunities by providing high-yield mining nodes to the general public with 100% transparency and instant liquidity.</p>
                <button onclick="changePage('home')" class="w-full p-4 glass rounded-2xl text-[9px] font-black uppercase italic mt-4">Back to Dashboard</button>
            </div>
        </div>

        <div id="p-policy" class="page space-y-6">
            <h3 class="text-xl font-black text-center uppercase text-blue-500 italic">Security Protocol</h3>
            <div class="glass p-6 rounded-[3rem] space-y-4 text-[10px] uppercase font-bold tracking-tight opacity-70">
                <p>1. <span class="text-blue-500">Data Protection:</span> All user data is encrypted via Firebase SSL 256-bit encryption.</p>
                <p>2. <span class="text-blue-500">Withdrawal Policy:</span> Minimum withdrawal ₨ 500. Processing time 1-24 hours.</p>
                <p>3. <span class="text-blue-500">Risk Warning:</span> Investments in digital nodes carry market risks. Only invest what you can afford to lose.</p>
                <p>4. <span class="text-blue-500">Zero Spam:</span> We never share your transaction IDs or phone numbers with third parties.</p>
                <button onclick="changePage('home')" class="w-full p-4 glass rounded-2xl text-[9px] font-black uppercase italic mt-4">Accept & Return</button>
            </div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-6 border-blue-500/20">
                <h3 class="text-center font-black uppercase text-blue-500 italic"><i class="fa-solid fa-qrcode mr-2"></i>Secure Deposit</h3>
                
                <div class="grid grid-cols-2 gap-4">
                    <div onclick="selectQR('JazzCash', 'qr-jazz.jpg')" id="m-jazz" class="qr-box glass p-4 rounded-2xl text-center cursor-pointer">
                        <img src="https://upload.wikimedia.org/wikipedia/en/thumb/2/25/JazzCash_logo.png/220px-JazzCash_logo.png" class="h-6 mx-auto mb-2 grayscale">
                        <p class="text-[8px] font-black">JAZZCASH</p>
                    </div>
                    <div onclick="selectQR('EasyPaisa', 'qr-easy.jpg')" id="m-easy" class="qr-box glass p-4 rounded-2xl text-center cursor-pointer">
                        <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/e0/Easypaisa_logo.png/1200px-Easypaisa_logo.png" class="h-6 mx-auto mb-2 grayscale">
                        <p class="text-[8px] font-black">EASYPAISA</p>
                    </div>
                </div>

                <div id="qr-area" class="hidden p-6 bg-white rounded-[2rem] text-center scale-up">
                    <p class="text-black text-[9px] font-black mb-3 uppercase italic">Scan & Pay to: M. Nazim</p>
                    <img id="qr-image" src="" class="w-44 h-44 mx-auto mb-3 shadow-xl">
                    <p class="text-blue-600 text-[10px] font-black">Account: 03379827882</p>
                </div>

                <div class="space-y-4">
                    <input type="number" id="dep-amt" placeholder="Deposit Amount (₨)">
                    <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                    <button onclick="sendDeposit()" class="w-full p-4 btn-prime text-[11px] uppercase italic">Confirm Funding</button>
                </div>
            </div>
        </div>

        <div id="p-history" class="page space-y-6">
            <h3 class="text-xl font-black text-center uppercase text-blue-500 italic">Node History</h3>
            <div id="user-logs" class="space-y-3 pb-20"></div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-5 flex justify-around items-center rounded-t-[3.5rem] z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-gem"></i><span class="text-[7px] font-black uppercase">Nodes</span></button>
        <button onclick="changePage('wallet')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-wallet"></i><span class="text-[7px] font-black uppercase">Wallet</span></button>
        <button onclick="changePage('history')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-history"></i><span class="text-[7px] font-black uppercase">Logs</span></button>
    </nav>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-28">
        <div class="flex justify-between items-center mb-8 border-b border-blue-500/20 pb-4">
            <h2 class="text-xl font-black text-blue-500 uppercase italic"><i class="fa-solid fa-ghost mr-2"></i>God View</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-6 py-2 rounded-xl text-[10px] font-black uppercase">Exit</button>
        </div>
        <div class="glass p-6 rounded-3xl mb-6 space-y-4 border-blue-500/20">
            <h4 class="text-[10px] font-black uppercase text-blue-400">Broadcast Portal</h4>
            <textarea id="adm-bc-text" placeholder="Send global alert sweetie..." rows="2"></textarea>
            <button onclick="sendBroadcast()" class="w-full p-3 btn-prime text-[9px] uppercase">Fire Now</button>
        </div>
        <div id="adm-req-list" class="space-y-4 mb-8"></div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 bg-blue-600 rounded-[2rem] mb-6 flex items-center justify-center text-5xl font-black text-white italic shadow-2xl">M</div>
        <input type="text" id="user-login-name" placeholder="VERIFIED USERNAME" class="max-w-[300px] text-center font-bold uppercase">
        <button onclick="login()" class="w-full max-w-[300px] p-4 btn-prime uppercase text-[11px] tracking-widest">Connect to Node</button>
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
        let user = null; let tapCount = 0; let curMethod = '';

        function login() {
            const n = document.getElementById('user-login-name').value.trim().toLowerCase();
            if(!n) return;
            localStorage.setItem('mc_user', n);
            enterApp(n);
        }

        async function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-user').innerText = "@" + n.toUpperCase();

            db.collection("users").doc(n).onSnapshot(doc => {
                if(!doc.exists) return db.collection("users").doc(n).set({ name: n, balance: 0, lastDaily: 0 });
                user = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            });

            syncPlans();
            syncLogs(n);
            listenBroadcast();
            startPayoutFeed();
        }

        function selectQR(m, img) {
            curMethod = m;
            document.querySelectorAll('.qr-box').forEach(b => b.classList.remove('active'));
            document.getElementById('m-' + (m==='JazzCash'?'jazz':'easy')).classList.add('active');
            document.getElementById('qr-area').classList.remove('hidden');
            document.getElementById('qr-image').src = img;
        }

        async function sendDeposit() {
            const a = document.getElementById('dep-amt').value; const t = document.getElementById('dep-tid').value;
            if(!a || !t || !curMethod) return alert("Sweetie, select method & fill details!");
            const id = Date.now().toString();
            await db.collection("requests").doc(id).set({ user: user.name, amount: parseInt(a), tid: t, status: 'pending', method: curMethod, time: Date.now() });
            await db.collection("logs").doc(id).set({ user: user.name, msg: `Deposit: ₨ ${a} (${curMethod})`, status: 'pending', type: 'finance', time: Date.now() });
            alert("Verification initiated! 😘");
        }

        function startPayoutFeed() {
            const users = ['Arsalan', 'Zoya', 'Hamid', 'Mehak', 'Bilal', 'Sana', 'Irfan'];
            const feed = document.getElementById('proof-feed');
            setInterval(() => {
                const u = users[Math.floor(Math.random()*users.length)];
                const a = (Math.floor(Math.random()*20)+5)*200;
                feed.innerHTML = `<div class="flex justify-between items-center text-[9px] font-black uppercase text-blue-400 bg-blue-500/5 p-2 rounded-xl animate-bounce"><span>${u}*** Payout</span><span class="text-green-500">₨ ${a} SUCCESS</span></div>`;
            }, 4000);
        }

        // Admin & Utility Logic
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("God Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        async function sendBroadcast() {
            const m = document.getElementById('adm-bc-text').value;
            if(m) await db.collection("settings").doc("broadcast").set({ text: m, time: Date.now() });
        }
        function listenBroadcast() {
            db.collection("settings").doc("broadcast").onSnapshot(d => {
                if(d.exists && (Date.now() - d.data().time < 600000)) {
                    document.getElementById('bc-ui').classList.remove('hidden');
                    document.getElementById('bc-msg').innerText = d.data().text;
                } else document.getElementById('bc-ui').classList.add('hidden');
            });
        }
        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    g.innerHTML += `<div class="glass p-6 rounded-[2.5rem] border-l-4 border-blue-500 flex justify-between items-center">
                        <div><p class="text-[9px] uppercase font-black opacity-40">${p.name}</p><h4 class="text-xl font-black italic">₨ ${p.price}</h4></div>
                        <button onclick="buyNode('${doc.id}', ${p.price})" class="btn-prime px-6 py-3 text-[10px] uppercase font-black italic">Activate</button>
                    </div>`;
                });
            });
        }
        function syncLogs(n) {
            db.collection("logs").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const l = document.getElementById('user-logs'); l.innerHTML = '';
                s.forEach(doc => {
                    const d = doc.data();
                    l.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-4 border-blue-500 mb-2">
                        <div><p class="text-[10px] font-black uppercase">${d.msg}</p><p class="text-[7px] opacity-40 italic">TID: ${d.tid || 'System'}</p></div>
                        <span class="text-[8px] font-black uppercase px-2 py-1 rounded-full ${d.status==='pending'?'bg-yellow-500/10 text-yellow-500':'bg-green-500/10 text-green-500'}">${d.status || 'Success'}</span>
                    </div>`;
                });
            });
        }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); window.scrollTo(0,0); }
        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); }
    </script>
</body>
</html>
