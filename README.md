<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <title>SkillMint | Global Investment</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;900&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #020617; color: white; overflow: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .scroll-hide::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="h-screen flex flex-col lg:flex-row">

    <aside class="hidden lg:flex w-72 bg-gray-950 border-r border-gray-800 flex-col p-8">
        <h1 class="text-3xl font-black text-blue-500 italic mb-12 uppercase tracking-tighter">SkillMint</h1>
        <nav class="space-y-6 flex-1 text-lg">
            <button onclick="showPage('home')" class="w-full text-left flex items-center gap-3 hover:text-blue-500 transition"><span>🏠</span> Home</button>
            <button onclick="showPage('invest')" class="w-full text-left flex items-center gap-3 hover:text-blue-500 transition"><span>📈</span> Invest</button>
            <button onclick="showPage('wallet')" class="w-full text-left flex items-center gap-3 hover:text-blue-500 transition"><span>💰</span> Wallet</button>
            <button onclick="showPage('profile')" class="w-full text-left flex items-center gap-3 hover:text-blue-500 transition"><span>👤</span> Profile</button>
        </nav>
        <button onclick="logout()" class="text-red-500 font-bold border border-red-500/20 p-3 rounded-xl hover:bg-red-500/10">Sign Out</button>
    </aside>

    <div class="flex-1 overflow-y-auto pb-24 lg:pb-0 scroll-hide" id="main-scroll">
        
        <section id="page-auth" class="h-screen flex items-center justify-center p-6">
            <div class="w-full max-w-md glass p-10 rounded-[3rem] text-center shadow-2xl border-t-8 border-blue-600">
                <h2 class="text-4xl font-black mb-2 uppercase italic tracking-widest">SkillMint</h2>
                <p class="text-gray-500 text-xs mb-10 tracking-widest">FUTURE OF WEALTH</p>
                <input type="text" id="login-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none focus:border-blue-500 mb-4 text-center text-lg">
                <button onclick="login()" class="w-full bg-blue-600 py-4 rounded-2xl font-black shadow-xl shadow-blue-900/40 hover:scale-105 transition-all">ACCESS ACCOUNT</button>
            </div>
        </section>

        <div id="app-body" class="hidden">
            
            <section id="home-view" class="p-6 lg:p-12 animate-in fade-in">
                <div class="flex justify-between items-center mb-10">
                    <h2 class="text-2xl font-black uppercase">Dashboard</h2>
                    <div class="bg-blue-600/20 text-blue-400 px-4 py-1 rounded-full text-[10px] font-bold">LIVE MARKET ON</div>
                </div>

                <div class="glass p-12 rounded-[3.5rem] bg-gradient-to-br from-blue-600/10 to-transparent mb-10 text-center lg:text-left">
                    <p class="text-gray-400 text-xs font-bold uppercase tracking-widest mb-2">Portfolio Balance</p>
                    <h3 class="text-7xl font-black tracking-tighter" id="v-bal">₨ 0</h3>
                    <div class="mt-10 flex flex-wrap gap-4 justify-center lg:justify-start">
                        <button onclick="showPage('wallet')" class="bg-blue-600 px-10 py-3 rounded-2xl font-black text-sm">Add Funds</button>
                        <button onclick="showPage('invest')" class="bg-white/5 border border-white/10 px-10 py-3 rounded-2xl font-black text-sm">View Plans</button>
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                    <div class="glass p-8 rounded-[2rem] border-b-4 border-green-500">
                        <p class="text-[10px] font-bold text-gray-500">PROFIT REWARD</p>
                        <p class="text-3xl font-black text-green-400" id="v-profit">₨ 0</p>
                    </div>
                    <div class="glass p-8 rounded-[2rem] border-b-4 border-blue-500">
                        <p class="text-[10px] font-bold text-gray-500">ACTIVE TIERS</p>
                        <p class="text-3xl font-black" id="v-tiers">0</p>
                    </div>
                    <div class="glass p-8 rounded-[2rem] border-b-4 border-purple-500">
                        <p class="text-[10px] font-bold text-gray-500">REFERRAL KEY</p>
                        <p class="text-xl font-bold text-purple-400">SM-88271</p>
                    </div>
                </div>
            </section>

            <section id="invest-view" class="hidden p-6 lg:p-12">
                <h2 class="text-3xl font-black mb-8 italic uppercase">Choose Your Tier</h2>
                <div id="plans-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                    </div>
            </section>

            <section id="wallet-view" class="hidden p-6 lg:p-12">
                <h2 class="text-3xl font-black mb-8 italic uppercase tracking-tighter text-blue-500">Funding Portal</h2>
                <div class="glass p-10 rounded-[3rem] max-w-xl mx-auto border-t-8 border-green-600">
                    <p class="text-gray-400 text-center mb-8">Send payment and WhatsApp the screenshot for instant approval.</p>
                    <div class="space-y-4">
                        <div class="p-6 bg-white/5 rounded-[2rem] flex justify-between items-center border border-white/5">
                            <div><p class="text-[10px] text-red-500 font-bold uppercase">JazzCash</p><p class="text-xl font-black">03705519562</p></div>
                            <button onclick="copy('03705519562')" class="bg-red-600/20 text-red-500 px-4 py-1 rounded-lg text-xs font-bold">Copy</button>
                        </div>
                        <div class="p-6 bg-white/5 rounded-[2rem] flex justify-between items-center border border-white/5">
                            <div><p class="text-[10px] text-green-500 font-bold uppercase">EasyPaisa</p><p class="text-xl font-black">03379827882</p></div>
                            <button onclick="copy('03379827882')" class="bg-green-600/20 text-green-500 px-4 py-1 rounded-lg text-xs font-bold">Copy</button>
                        </div>
                    </div>
                    <a href="https://wa.me/923379827882" class="block w-full text-center bg-green-600 py-5 rounded-[2rem] font-black mt-10 text-lg shadow-xl shadow-green-900/20">SEND SCREENSHOT (WHATSAPP)</a>
                </div>
            </section>
        </div>
    </div>

    <nav class="lg:hidden fixed bottom-0 left-0 w-full glass flex justify-around p-5 z-[500] border-t border-white/10">
        <button onclick="showPage('home')" id="tab-home" class="flex flex-col items-center gap-1 active-tab pt-2">🏠<span class="text-[10px] font-bold uppercase">Home</span></button>
        <button onclick="showPage('invest')" id="tab-invest" class="flex flex-col items-center gap-1 pt-2">📈<span class="text-[10px] font-bold uppercase">Invest</span></button>
        <button onclick="showPage('wallet')" id="tab-wallet" class="flex flex-col items-center gap-1 pt-2">💰<span class="text-[10px] font-bold uppercase">Wallet</span></button>
        <button onclick="showPage('profile')" id="tab-profile" class="flex flex-col items-center gap-1 pt-2">👤<span class="text-[10px] font-bold uppercase">User</span></button>
    </nav>

    <script>
        const plans = [
            { n: "Starter", p: 500, r: "10%" }, { n: "Basic", p: 1500, r: "15%" }, 
            { n: "Silver", p: 3000, r: "20%" }, { n: "Pro", p: 5000, r: "25%" },
            { n: "Gold", p: 10000, r: "30%" }, { n: "Premium", p: 20000, r: "35%" },
            { n: "Elite", p: 35000, r: "40%" }, { n: "Diamond", p: 50000, r: "45%" },
            { n: "Master", p: 75000, r: "50%" }, { n: "Ultra", p: 100000, r: "60%" },
            { n: "Executive", p: 150000, r: "70%" }, { n: "VIP", p: 250000, r: "85%" },
            { n: "Legend", p: 400000, r: "100%" }, { n: "Empire", p: 600000, r: "130%" },
            { n: "Supreme", p: 1000000, r: "180%" }
        ];

        let user = JSON.parse(localStorage.getItem('sm_v3')) || null;

        function login() {
            const n = document.getElementById('login-name').value;
            if(!n) return alert("Enter Name!");
            user = { name: n, bal: 0, profit: 0, plans: 0 };
            save(); render();
        }

        function render() {
            if(!user) return;
            document.getElementById('page-auth').classList.add('hidden');
            document.getElementById('app-body').classList.remove('hidden');
            document.getElementById('v-bal').innerText = "₨ " + user.bal.toLocaleString();
            document.getElementById('v-profit').innerText = "₨ " + user.profit.toLocaleString();
            document.getElementById('v-tiers').innerText = user.plans;

            const grid = document.getElementById('plans-grid');
            grid.innerHTML = '';
            plans.forEach((p, i) => {
                grid.innerHTML += `
                    <div onclick="buyPlan(${p.p})" class="glass p-8 rounded-[2.5rem] border-b-4 border-blue-600 hover:scale-105 transition cursor-pointer">
                        <h4 class="font-black text-xl">${p.n}</h4>
                        <p class="text-green-400 font-bold mb-6">${p.r} ROI / 10 Days</p>
                        <div class="flex justify-between items-center bg-white/5 p-4 rounded-2xl">
                            <span class="font-black">₨ ${p.p.toLocaleString()}</span>
                            <span class="text-[10px] font-bold text-blue-500">BUY NOW</span>
                        </div>
                    </div>
                `;
            });
        }

        function showPage(pageId) {
            ['home', 'invest', 'wallet'].forEach(p => {
                document.getElementById(p + '-view').classList.add('hidden');
                document.getElementById('tab-' + p).classList.remove('active-tab');
            });
            document.getElementById(pageId + '-view').classList.remove('hidden');
            document.getElementById('tab-' + pageId).classList.add('active-tab');
        }

        function buyPlan(price) {
            if(user.bal < price) { alert("Insufficient funds! Go to Wallet."); showPage('wallet'); }
            else { user.bal -= price; user.plans++; save(); render(); alert("Plan Activated!"); }
        }

        function save() { localStorage.setItem('sm_v3', JSON.stringify(user)); }
        function logout() { localStorage.removeItem('sm_v3'); location.reload(); }
        function copy(t) { navigator.clipboard.writeText(t); alert("Copied!"); }

        // ADMIN: Ctrl+Shift+Enter = 1 Lakh PKR
        window.addEventListener('keydown', (e) => {
            if(e.ctrlKey && e.shiftKey && e.key === 'Enter') {
                user.bal += 100000; save(); render(); alert("Admin: Added 1 Lakh PKR");
            }
        });

        if(user) render();
    </script>
</body>
</html>
