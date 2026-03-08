<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        .light { --bg: #f8fafc; --card: #ffffff; --text: #0f172a; --border: #e2e8f0; --accent: #2563eb; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); transition: 0.3s; padding-bottom: 100px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 16px; font-weight: 800; transition: 0.2s; }
        .btn-prime:active { transform: scale(0.95); }
        input, textarea { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 14px; width: 100%; outline: none; font-size: 13px; }
        .nav-active { color: var(--accent); transform: scale(1.1); opacity: 1 !important; }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[var(--bg)] hidden flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 bg-red-500/20 rounded-full flex items-center justify-center mb-6 animate-pulse text-4xl">⚠️</div>
        <h1 id="lock-title" class="text-2xl font-black uppercase italic text-red-500 mb-2">Access Restricted</h1>
        <p id="lock-msg" class="text-[10px] font-bold opacity-60 uppercase tracking-widest leading-relaxed">System is currently undergoing optimization.</p>
    </div>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] z-[5000]">
        <div class="flex items-center gap-2">
            <div onclick="adminTap()" class="w-9 h-9 bg-blue-600 rounded-xl flex items-center justify-center font-black text-white italic shadow-lg shadow-blue-500/20">M</div>
            <span class="font-black text-xs uppercase tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></span>
        </div>
        <div class="flex items-center gap-3">
            <button onclick="toggleTheme()" class="w-8 h-8 glass rounded-full flex items-center justify-center text-sm">🌓</button>
            <div id="top-bal" class="text-[10px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
        </div>
    </header>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8 text-center">
        <div class="w-16 h-16 bg-blue-600 rounded-[1.5rem] mb-6 flex items-center justify-center text-4xl font-black text-white italic shadow-2xl">M</div>
        <h1 class="text-2xl font-black italic tracking-tighter mb-8 uppercase">Initialize Node</h1>
        <div class="w-full max-w-[300px] space-y-4">
            <input type="text" id="user-name" placeholder="Username" class="text-center font-bold">
            <input type="password" id="user-pass" placeholder="Password" class="text-center font-bold">
            <button onclick="login()" class="w-full p-4 btn-prime uppercase text-[10px] tracking-widest shadow-xl">Authorize Access</button>
            <p onclick="alert('Contact Admin at @MintCrestSupport for reset.')" class="text-[9px] text-blue-400 font-bold uppercase cursor-pointer italic opacity-60">Forgot Identity Key?</p>
        </div>
    </section>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="bg-blue-600/5 border border-blue-500/10 p-2 rounded-xl overflow-hidden whitespace-nowrap">
                <p id="v-news" class="animate-marquee inline-block text-[8px] font-black uppercase text-blue-400 italic">
                    Welcome to MintCrest Gold! Minimum Deposit ₨ 200 | Trusted Node Active...
                </p>
            </div>

            <div class="p-8 rounded-[2.5rem] bg-gradient-to-br from-blue-700 to-blue-900 text-white shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60 tracking-widest">Active Liquidity</p>
                <h2 class="text-4xl font-black tracking-tighter mt-1" id="v-bal">₨ 0.00</h2>
                <div class="mt-6 flex justify-between items-center bg-black/20 p-4 rounded-2xl backdrop-blur-md">
                    <span class="text-[8px] font-bold uppercase italic">Node Status: Online</span>
                    <button onclick="copyRef()" class="bg-white text-blue-800 px-4 py-2 rounded-xl text-[8px] font-black uppercase">Copy Invite Link</button>
                </div>
            </div>

            <div class="glass p-5 rounded-[2rem] flex gap-2 items-center">
                <input type="text" id="promo-input" placeholder="Promo Code" class="flex-1 !p-3 font-bold uppercase">
                <button onclick="applyPromo()" class="btn-prime px-6 py-3 text-[9px] uppercase">Apply</button>
            </div>

            <div id="timer-box" class="hidden glass p-6 rounded-[2.5rem] border-l-4 border-green-500 bg-green-500/5">
                <p class="text-[8px] font-black uppercase opacity-60">Yield countdown active</p>
                <div class="flex justify-between items-end mt-2">
                    <h3 id="countdown" class="text-3xl font-black italic text-green-500 tracking-tighter">00:00:00</h3>
                    <p class="text-[7px] font-black opacity-30 uppercase tracking-widest">Auto-Mining Profit</p>
                </div>
            </div>

            <div class="flex gap-2 p-1 bg-white/5 rounded-2xl">
                <button onclick="renderPlans('normal')" id="btn-normal" class="flex-1 p-3 rounded-xl bg-blue-600 text-white text-[9px] font-black uppercase">Standard Nodes</button>
                <button onclick="renderPlans('special')" id="btn-special" class="flex-1 p-3 rounded-xl text-[9px] font-black uppercase opacity-40">VIP Offers</button>
            </div>
            <div id="plans-grid" class="grid grid-cols-1 gap-4"></div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <h3 class="text-[10px] font-black uppercase opacity-40 text-center italic tracking-widest">Fund Your Node</h3>
            <div class="space-y-3">
                <div class="glass p-5 rounded-[2rem] border-l-4 border-red-600 flex justify-between items-center">
                    <div><p class="text-[8px] font-black text-red-500 uppercase">JazzCash</p><p id="j-num" class="text-lg font-black tracking-widest">03705519562</p></div>
                    <button onclick="copyText('03705519562')" class="text-[8px] bg-white/5 px-3 py-2 rounded-lg font-bold">COPY</button>
                </div>
                <div class="glass p-5 rounded-[2rem] border-l-4 border-green-500 flex justify-between items-center">
                    <div><p class="text-[8px] font-black text-green-500 uppercase">EasyPaisa</p><p id="e-num" class="text-lg font-black tracking-widest">03379827882</p></div>
                    <button onclick="copyText('03379827882')" class="text-[8px] bg-white/5 px-3 py-2 rounded-lg font-bold">COPY</button>
                </div>
                <div class="glass p-5 rounded-[2rem] border-l-4 border-black flex justify-between items-center">
                    <div><p class="text-[8px] font-black text-gray-400 uppercase">SadaPay</p><p class="text-lg font-black tracking-widest">03705519562</p></div>
                    <button onclick="copyText('03705519562')" class="text-[8px] bg-white/5 px-3 py-2 rounded-lg font-bold">COPY</button>
                </div>
            </div>
            <div class="glass p-6 rounded-[2.5rem] space-y-4">
                <input type="number" id="dep-amt" placeholder="Amount Sent (Min: ₨ 200)">
                <input type="text" id="dep-tid" placeholder="TID (Transaction ID)">
                <div class="relative">
                    <input type="file" id="dep-file" accept="image/*" class="hidden" onchange="previewImg(this)">
                    <label for="dep-file" class="w-full p-4 border-2 border-dashed border-[var(--border)] rounded-xl flex flex-col items-center justify-center gap-1 cursor-pointer">
                        <span id="upload-icon" class="text-xl">📸</span>
                        <span id="file-label" class="text-[8px] font-black uppercase opacity-50">Upload Proof Screenshot</span>
                    </label>
                    <img id="img-preview" class="hidden w-full h-32 object-cover rounded-xl mt-2 border border-blue-500/30">
                </div>
                <button onclick="submitDep()" class="w-full p-4 btn-prime uppercase text-[10px] tracking-widest">Confirm Deposit ⚡</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <h3 class="text-[10px] font-black uppercase opacity-40 text-center italic tracking-widest">Request Payout (Tax: 10%)</h3>
            <div class="grid grid-cols-2 gap-3">
                <div onclick="selBank('JazzCash')" id="b-JazzCash" class="bank-card glass p-4 rounded-2xl flex flex-col items-center gap-2 border-2 border-transparent transition-all">
                    <span class="text-2xl">🔴</span><span class="text-[8px] font-black uppercase">JazzCash</span>
                </div>
                <div onclick="selBank('EasyPaisa')" id="b-EasyPaisa" class="bank-card glass p-4 rounded-2xl flex flex-col items-center gap-2 border-2 border-transparent transition-all">
                    <span class="text-2xl">🟢</span><span class="text-[8px] font-black uppercase">EasyPaisa</span>
                </div>
            </div>
            <div class="glass p-6 rounded-[2.5rem] space-y-4">
                <input type="number" id="w-amt" placeholder="Amount (Min: ₨ 100)">
                <input type="text" id="w-acc" placeholder="Account Number">
                <input type="text" id="w-name" placeholder="Account Holder Name">
                <button onclick="submitWith()" class="w-full p-4 btn-prime uppercase text-[10px] tracking-widest">Withdraw Funds 📤</button>
            </div>
        </div>

        <div id="p-history" class="page space-y-4">
            <h3 class="text-[10px] font-black uppercase opacity-40 italic tracking-widest">Transaction Log</h3>
            <div id="history-list" class="space-y-3"></div>
        </div>

    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-4 md:p-8">
        <div class="flex justify-between items-center mb-8 sticky top-0 bg-[#010409]/90 backdrop-blur-md py-4 z-10">
            <h2 class="text-xl font-black text-blue-500 italic uppercase">Administration Node</h2>
            <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black uppercase">Exit</button>
        </div>
        <div id="adm-requests-list" class="space-y-4 pb-20"></div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-4 flex justify-around items-center rounded-t-[2.5rem] z-[4000]">
        <button onclick="changePage('home')" id="n-home" class="nav-active opacity-40 flex flex-col items-center gap-1 px-4 py-2">🏠<span class="text-[7px] font-black">NODE</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="opacity-40 flex flex-col items-center gap-1 px-4 py-2">📥<span class="text-[7px] font-black">FUND</span></button>
        <button onclick="changePage('withdraw')" id="n-withdraw" class="opacity-40 flex flex-col items-center gap-1 px-4 py-2">📤<span class="text-[7px] font-black">PAYOUT</span></button>
        <button onclick="changePage('history')" id="n-history" class="opacity-40 flex flex-col items-center gap-1 px-4 py-2">📜<span class="text-[7px] font-black">LOGS</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tapCount = 0; let selectedBank = "";

        // --- AUTH & CORE ---
        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const p = document.getElementById('user-pass').value.trim();
            if(!n || !p) return alert("Fill credentials.");

            const ref = db.collection("users").doc(n);
            const d = await ref.get();

            if(!d.exists) {
                await ref.set({ name: n, password: p, balance: 0, time: Date.now() });
                alert("Identity Created.");
            } else {
                if(d.data().password !== p) return alert("Wrong Password.");
                if(d.data().banned) return showLock("BANNED", "Your account is restricted.");
            }
            localStorage.setItem('mc_user', n);
            enterApp(n);
        }

        function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n);
        }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                const bal = (user.balance || 0).toLocaleString();
                document.getElementById('v-bal').innerText = "₨ " + bal;
                document.getElementById('top-bal').innerText = "₨ " + bal;
            });
        }

        // --- DEPOSIT WITH PROOF ---
        function previewImg(i) {
            if (i.files && i.files[0]) {
                const r = new FileReader();
                r.onload = e => {
                    document.getElementById('img-preview').src = e.target.result;
                    document.getElementById('img-preview').classList.remove('hidden');
                    document.getElementById('file-label').innerText = "Selected";
                };
                r.readAsDataURL(i.files[0]);
            }
        }

        async function submitDep() {
            const a = parseInt(document.getElementById('dep-amt').value);
            const t = document.getElementById('dep-tid').value;
            const f = document.getElementById('dep-file').files[0];
            if(!a || a < 200) return alert("Minimum deposit is ₨ 200.");
            if(!t || !f) return alert("Fill all details and upload proof.");
            
            const r = new FileReader();
            r.readAsDataURL(f);
            r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: r.result, type: "deposit", status: "pending", time: Date.now() });
                alert("Submitted."); location.reload();
            };
        }

        // --- WITHDRAW WITH TAX ---
        function selBank(b) { selectedBank = b; document.querySelectorAll('.bank-card').forEach(c=>c.classList.remove('border-blue-600','bg-blue-600/5')); document.getElementById('b-'+b).classList.add('border-blue-600','bg-blue-600/5'); }
        async function submitWith() {
            const a = parseInt(document.getElementById('w-amt').value);
            const ac = document.getElementById('w-acc').value;
            const nm = document.getElementById('w-name').value;
            if(!a || a < 100) return alert("Minimum withdrawal is ₨ 100.");
            if(!ac || !nm || !selectedBank) return alert("Fill details.");
            if(a > user.balance) return alert("Low Balance.");

            const tax = a * 0.10;
            const net = a - tax;

            if(confirm(`Withdraw ₨ ${a}?\nTax (10%): ₨ ${tax}\nPayable: ₨ ${net}`)) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
                await db.collection("requests").add({ user: user.name, amount: a, tax: tax, receiveAmount: net, method: selectedBank, account: ac, holder: nm, type: "withdrawal", status: "pending", time: Date.now() });
                alert("Requested."); location.reload();
            }
        }

        // --- ADMIN SYNC (TAX DISPLAYED) ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Security Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        async function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const rlist = document.getElementById('adm-requests-list'); rlist.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    let info = r.type === 'withdrawal' ? `Payable: ₨ ${r.receiveAmount} (Tax: ₨ ${r.tax})` : `Deposit: ₨ ${r.amount}`;
                    rlist.innerHTML += `<div class="glass p-6 rounded-[2rem] border-l-4 border-blue-500">
                        <div class="flex flex-col md:flex-row gap-4">
                            ${r.proof ? `<img src="${r.proof}" class="w-20 h-20 rounded-xl">` : ''}
                            <div class="flex-1 text-[10px] uppercase font-bold">
                                <p>${r.user} | ${r.type}</p><p class="text-green-500">${info}</p>
                                <p>Method: ${r.method || 'N/A'} | Acc: ${r.account || 'N/A'}</p>
                            </div>
                            <button onclick="appvReq('${doc.id}','${r.user}', ${r.amount})" class="bg-blue-600 p-2 rounded-xl text-[8px]">APPROVE</button>
                        </div>
                    </div>`;
                });
            });
        }
        async function appvReq(id, u, a) { await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) }); await db.collection("requests").doc(id).update({ status: 'approved' }); }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('nav-active','opacity-100')); document.getElementById('n-'+p).classList.add('nav-active'); }
        function toggleTheme() { document.getElementById('main-html').classList.toggle('dark'); document.getElementById('main-html').classList.toggle('light'); }
        function copyText(t) { navigator.clipboard.writeText(t); alert("Copied."); }

        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); }
    </script>
</body>
</html>
