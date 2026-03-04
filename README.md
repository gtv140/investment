<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Professional Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; -webkit-user-select: text !important; user-select: text !important; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; }
        .page { display: none; }
        .active-page { display: block; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-4xl font-black italic mb-12 uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600">
                <input type="text" id="user-name" placeholder="Enter Old Name Exactly" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black uppercase text-[10px]">Restore Connection</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6 text-center">
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600">
                <p class="text-[10px] text-blue-400 font-bold uppercase">Your Balance</p>
                <h2 class="text-5xl font-black" id="v-bal">₨ 0</h2>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-3xl uppercase font-black text-[9px]">Deposit</button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-3xl uppercase font-black text-[9px]">Withdraw</button>
            </div>
        </div>
        </main>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();

        async function login() {
            // Sweetie, yahan fix kiya hai: No .toUpperCase()
            const name = document.getElementById('user-name').value.trim(); 
            if(!name) return;

            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name);
            const doc = await ref.get();

            if(!doc.exists) {
                // Naya user tabhi banega agar purana naam galat likha ho
                await ref.set({ name: name, balance: 0, profit: 0, activeTier: 0, lastReqTime: Date.now() });
            }
            
            startSync(name);
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden');
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                if(doc.exists) {
                    document.getElementById('v-bal').innerText = "₨ " + doc.data().balance;
                }
            });
        }

        function changePage(p) { /* Logic to switch pages */ }
        function adminTap() { /* Admin access key mint786 */ }
    </script>
</body>
</html>
