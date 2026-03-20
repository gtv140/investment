<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Professional Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 100px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 16px; font-weight: 800; transition: 0.2s; }
        input, textarea { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 14px; width: 100%; outline: none; }
    </style>
</head>
<body>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] z-[5000]">
        <div class="flex items-center gap-2">
            <div onclick="adminTap()" class="w-9 h-9 bg-blue-600 rounded-xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
            <span class="font-black text-xs uppercase tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></span>
        </div>
        <div class="flex items-center gap-2">
            <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" target="_blank" class="w-8 h-8 bg-green-600 rounded-full flex items-center justify-center text-xs shadow-lg">💬</a>
            <div id="top-bal" class="text-[10px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
        </div>
    </header>

    <main id="app-ui" class="p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-8 rounded-[2.5rem] bg-gradient-to-br from-blue-700 to-blue-900 text-white shadow-2xl relative">
                <p class="text-[10px] font-black uppercase opacity-60">Global Liquidity</p>
                <h2 class="text-4xl font-black tracking-tighter" id="v-bal">₨ 0.00</h2>
                <button onclick="claimDaily()" id="daily-btn" class="mt-4 w-full bg-white/20 p-3 rounded-xl text-[9px] font-black uppercase backdrop-blur-md border border-white/10">🎁 Claim Daily Bonus</button>
            </div>

            <div class="glass p-5 rounded-[2rem] space-y-3">
                <p class="text-[8px] font-black text-blue-500 uppercase">Redeem Promo</p>
                <div class="flex gap-2">
                    <input type="text" id="promo-input" placeholder="Enter Admin Code" class="flex-1 !p-3 font-bold uppercase">
                    <button onclick="applyPromo()" class="btn-prime px-6 py-3 text-[9px] uppercase tracking-widest">Apply</button>
                </div>
            </div>

            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-20"></div>
        </div>

        <div id="p-support" class="page space-y-6">
            <h3 class="text-[10px] font-black uppercase opacity-40 text-center italic tracking-widest">Direct Admin Support</h3>
            <div class="glass p-6 rounded-[2.5rem] space-y-4">
                <p class="text-[9px] font-bold opacity-60 text-center uppercase">Send a message directly to the node administrator.</p>
                <textarea id="support-msg" placeholder="Write your message here sweetie..." rows="4"></textarea>
                <button onclick="sendSupport()" class="w-full p-4 btn-prime text-[10px] uppercase tracking-widest">Send Message ✉️</button>
            </div>
            <div class="text-center">
                <p class="text-[10px] font-black text-blue-500">Official Email: webhub262@gmail.com</p>
            </div>
        </div>

        <div id="p-about" class="page space-y-6">
            <h3 class="text-[10px] font-black uppercase opacity-40 text-center italic tracking-widest">Trust & Security</h3>
            <div class="glass p-6 rounded-[2.5rem] space-y-4 text-[11px] leading-relaxed font-bold opacity-80">
                <p class="text-blue-500 uppercase italic">MintCrest Gold Ltd (UK)</p>
                <p>We are a certified liquidity mining provider. Registered Office: London, UK. Registration: #UK-MCG-2026.</p>
                <h4 class="text-blue-400 uppercase">Privacy Policy</h4>
                <p>1. We encrypt all user credentials and transaction IDs. <br> 2. Payouts are processed via secure JazzCash/EasyPaisa API gateways. <br> 3. Your personal data is never shared with third parties.</p>
                <p>Contact: <span class="text-blue-500">webhub262@gmail.com</span></p>
            </div>
        </div>

    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-4">
        <div class="flex justify-between items-center mb-6">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Control Center</h2>
            <button onclick="closeAdmin()" class="bg-red-500 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>
        <div class="glass p-6 rounded-3xl space-y-4 mb-6">
            <h4 class="text-[10px] font-black text-blue-400 uppercase">Bonus Settings</h4>
            <input type="number" id="set-daily-amt" placeholder="Daily Bonus Amount (₨)">
            <input type="text" id="set-promo-code" placeholder="Active Promo Code">
            <input type="number" id="set-promo-amt" placeholder="Promo Bonus Amount (₨)">
            <button onclick="updateSettings()" class="w-full btn-prime p-4 text-[10px] uppercase">Save Configuration</button>
        </div>
        <h4 class="text-[10px] font-black text-green-500 uppercase mb-4">Support Messages</h4>
        <div id="adm-msg-list" class="space-y-4 pb-20"></div>
    </div>

    <nav id="bottom-nav" class="fixed bottom-0 w-full glass border-t border-[var(--border)] p-4 flex justify-around items-center rounded-t-[2.5rem] z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1">🏠<span class="text-[7px] font-black">HOME</span></button>
        <button onclick="changePage('support')" class="flex flex-col items-center gap-1">✉️<span class="text-[7px] font-black">CHAT</span></button>
        <button onclick="changePage('about')" class="flex flex-col items-center gap-1">🛡️<span class="text-[7px] font-black">LEGAL</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let system = {};

        // --- AUTH & SYNC ---
        function enterApp(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            });
            db.collection("settings").doc("system").onSnapshot(d => { system = d.data() || {}; });
            renderPlans();
        }

        // --- DAILY BONUS (24H LOGIC) ---
        async function claimDaily() {
            const now = Date.now();
            if(user.lastDaily && (now - user.lastDaily < 86400000)) {
                return alert("Sweetie, aap 24 ghante mein sirf ek baar bonus le sakte hain! 😘");
            }
            const amt = system.dailyBonusAmt || 10;
            await db.collection("users").doc(user.name).update({
                balance: firebase.firestore.FieldValue.increment(amt),
                lastDaily: now
            });
            alert(`Mubarak ho! ₨ ${amt} Daily Bonus mil gaya. 😘`);
        }

        // --- DYNAMIC PROMO ---
        async function applyPromo() {
            const c = document.getElementById('promo-input').value.trim().toUpperCase();
            if(c === system.activePromoCode) {
                if(user.usedPromos && user.usedPromos.includes(c)) return alert("Already used! 😘");
                await db.collection("users").doc(user.name).update({
                    balance: firebase.firestore.FieldValue.increment(system.promoAmt || 50),
                    usedPromos: firebase.firestore.FieldValue.arrayUnion(c)
                });
                alert("Promo Applied! Bonus Added. 😘");
            } else { alert("Invalid Code! 😘"); }
        }

        // --- SUPPORT SYSTEM ---
        async function sendSupport() {
            const m = document.getElementById('support-msg').value.trim();
            if(!m) return alert("Kuch likho toh sahi! 😘");
            await db.collection("support").add({ user: user.name, message: m, time: Date.now(), status: "new" });
            alert("Message Admin ko bhej diya gaya hai! 😘");
            document.getElementById('support-msg').value = '';
        }

        // --- ADMIN SYNC ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncMessages(); } tapCount=0; } }
        async function updateSettings() {
            const d = parseInt(document.getElementById('set-daily-amt').value);
            const pc = document.getElementById('set-promo-code').value.toUpperCase();
            const pa = parseInt(document.getElementById('set-promo-amt').value);
            await db.collection("settings").doc("system").set({ dailyBonusAmt: d, activePromoCode: pc, promoAmt: pa }, { merge: true });
            alert("Settings Updated! 😘");
        }
        function syncMessages() {
            db.collection("support").orderBy("time", "desc").onSnapshot(s => {
                const list = document.getElementById('adm-msg-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const m = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-2xl border-l-4 border-blue-500">
                        <p class="text-[8px] font-black text-blue-400">@${m.user} says:</p>
                        <p class="text-[10px] font-bold mt-1">${m.message}</p>
                    </div>`;
                });
            });
        }

        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function renderPlans() {
            const grid = document.getElementById('plans-grid'); grid.innerHTML = '';
            for(let i=1; i<=10; i++) {
                grid.innerHTML += `<div class="glass p-5 rounded-[2rem] border-l-4 border-blue-500 flex justify-between items-center">
                    <div><h4 class="font-black text-[10px] uppercase text-blue-500 italic">Mining Node v${i}</h4><p class="text-[9px] font-bold tracking-tighter">Profit System Active</p></div>
                    <button class="btn-prime px-6 py-2 text-[8px] uppercase">BUY</button>
                </div>`;
            }
        }
        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); }
    </script>
</body>
</html>
