<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <title>WealthWise Pro | Local Storage Edition</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@400;700;900&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #05070a; color: white; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .hidden { display: none; }
    </style>
</head>
<body class="min-h-screen">

    <section id="auth-section" class="min-h-screen flex items-center justify-center p-6">
        <div class="glass w-full max-w-md p-10 rounded-[2.5rem] shadow-2xl text-center">
            <h1 class="text-3xl font-black text-blue-500 mb-6 italic tracking-tighter uppercase">WealthWise</h1>
            <div id="auth-box" class="space-y-4">
                <input type="text" id="username" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none focus:border-blue-500">
                <input type="password" id="password" placeholder="Password" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none focus:border-blue-500">
                <button onclick="handleLogin()" class="w-full bg-blue-600 py-4 rounded-2xl font-black shadow-lg hover:bg-blue-500 transition-all">ENTER DASHBOARD</button>
                <p class="text-[10px] text-gray-500 uppercase tracking-widest">Data is saved locally on your device</p>
            </div>
        </div>
    </section>

    <section id="main-dashboard" class="hidden min-h-screen">
        <nav class="p-5 flex justify-between items-center glass sticky top-0 z-50">
            <h1 class="text-xl font-black text-blue-500 italic">WEALTHWISE</h1>
            <button onclick="logout()" class="text-[10px] bg-red-600/20 text-red-500 px-4 py-1 rounded-full border border-red-500/20 font-bold">LOGOUT</button>
        </nav>

        <main class="max-w-4xl mx-auto p-6">
            <div class="glass p-10 rounded-[3rem] border-t-8 border-blue-600 shadow-2xl mb-10">
                <p class="text-gray-500 text-xs font-bold uppercase tracking-widest">Account Balance</p>
                <h2 class="text-6xl font-black mt-2 tracking-tighter text-white" id="balDisplay">₨ 0</h2>
                <div class="mt-8 flex gap-4">
                    <button onclick="showPop('dep')" class="bg-blue-600 px-10 py-4 rounded-2xl font-black text-sm shadow-xl shadow-blue-900/30">DEPOSIT</button>
                    <button onclick="alert('Withdrawals process on Sundays!')" class="bg-white/5 border border-white/10 px-10 py-4 rounded-2xl font-black text-sm">WITHDRAW</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-10">
                <div class="glass p-6 rounded-3xl border-l-4 border-green-500">
                    <p class="text-gray-500 text-[10px] font-bold">ACTIVE PLAN</p>
                    <p class="text-xl font-black text-green-400" id="planDisplay">None</p>
                </div>
                <div class="glass p-6 rounded-3xl border-l-4 border-yellow-500">
                    <p class="text-gray-500 text-[10px] font-bold">USER STATUS</p>
                    <p class="text-xl font-black" id="userDisplay">Investor</p>
                </div>
            </div>

            <h3 class="text-xl font-black mb-6 italic opacity-70 uppercase tracking-tighter">Choose Your Plan</h3>
            <div class="space-y-4 mb-20">
                <div onclick="buyPlan('Starter', 1500)" class="glass p-6 rounded-[2rem] flex justify-between items-center cursor-pointer hover:border-blue-500 transition">
                    <div><h4 class="font-black">Starter Growth</h4><p class="text-xs text-gray-500">15% Profit / 7 Days</p></div>
                    <div class="text-right"><p class="font-black text-blue-500 italic">₨ 1,500</p></div>
                </div>
                <div onclick="buyPlan('Elite VIP', 10000)" class="glass p-6 rounded-[2rem] flex justify-between items-center cursor-pointer hover:border-yellow-500 transition border-l-4 border-yellow-500">
                    <div><h4 class="font-black">Elite VIP</h4><p class="text-xs text-gray-500">45% Profit / 20 Days</p></div>
                    <div class="text-right"><p class="font-black text-yellow-500 italic">₨ 10,000</p></div>
                </div>
            </div>
        </main>
    </section>

    <div id="dep-modal" class="hidden fixed inset-0 bg-black/95 z-[100] flex items-center justify-center p-6">
        <div class="glass w-full max-w-sm p-8 rounded-[2.5rem] border border-blue-500/30 text-center">
            <h2 class="text-xl font-black mb-6">FUND YOUR WALLET</h2>
            <div class="space-y-4 mb-6">
                <div class="p-4 bg-white/5 rounded-2xl">
                    <p class="text-[10px] text-red-500 font-bold">JAZZCASH</p>
                    <p class="text-lg font-black tracking-widest">03705519562</p>
                </div>
                <div class="p-4 bg-white/5 rounded-2xl">
                    <p class="text-[10px] text-green-500 font-bold">EASYPAISA</p>
                    <p class="text-lg font-black tracking-widest">03379827882</p>
                </div>
            </div>
            <a href="https://wa.me/923379827882" class="block w-full bg-green-600 py-4 rounded-2xl font-black text-sm mb-4">SEND SCREENSHOT</a>
            <button onclick="showPop('hide')" class="text-gray-500 text-xs">GO BACK</button>
        </div>
    </div>

    <script>
        // CORE SYSTEM
        let user = JSON.parse(localStorage.getItem('ww_user')) || null;

        function handleLogin() {
            const name = document.getElementById('username').value;
            const pass = document.getElementById('password').value;
            if(!name || !pass) return alert("Please fill all fields!");

            user = { name: name, balance: 0, plan: 'None', status: 'Active' };
            save();
            render();
        }

        function render() {
            if(user) {
                document.getElementById('auth-section').classList.add('hidden');
                document.getElementById('main-dashboard').classList.remove('hidden');
                document.getElementById('balDisplay').innerText = "₨ " + user.balance.toLocaleString();
                document.getElementById('planDisplay').innerText = user.plan;
                document.getElementById('userDisplay').innerText = user.name.split(' ')[0];
            }
        }

        function save() {
            localStorage.setItem('ww_user', JSON.stringify(user));
        }

        function logout() {
            localStorage.removeItem('ww_user');
            location.reload();
        }

        function showPop(type) {
            if(type === 'dep') document.getElementById('dep-modal').classList.remove('hidden');
            else document.getElementById('dep-modal').classList.add('hidden');
        }

        function buyPlan(pName, price) {
            if(user.balance < price) {
                alert("Insufficient Balance! Deposit funds first.");
                showPop('dep');
            } else {
                user.balance -= price;
                user.plan = pName;
                save();
                render();
                alert(pName + " Plan Activated!");
            }
        }

        // SECRET ADMIN CONTROLS (Ctrl + Shift + Enter)
        window.addEventListener('keydown', (e) => {
            if(e.ctrlKey && e.shiftKey && e.key === 'Enter') {
                let amount = prompt("Admin: Enter Amount to Add (PKR):");
                if(amount) {
                    user.balance += parseInt(amount);
                    save();
                    render();
                }
            }
        });

        // Initialize
        if(user) render();
    </script>
</body>
</html>
