<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MINTCRESTGOLD | Live Earning</title>
<script src="https://cdn.tailwindcss.com"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/js/all.min.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
<style>
body{background:#0f172a;color:white;font-family:sans-serif;overflow-x:hidden;}
.card{background:#1e293b;padding:20px;border-radius:16px;transition:all 0.3s;margin-bottom:12px;}
.card:hover{transform:scale(1.02);box-shadow:0 5px 15px rgba(0,0,0,0.3);}
input, select{background:#0f172a;border:1px solid #334155;padding:10px;border-radius:8px;width:100%;color:white;margin-bottom:10px;}
button{padding:10px;border-radius:8px;font-weight:bold;margin-top:5px;transition:all 0.2s;}
button:hover{opacity:0.9;}
.nav-btn{flex:1;text-center;padding:12px;font-size:20px;}
.active-nav{color:#3b82f6;transform:scale(1.1);}
.progress-bar{height:6px;background:#3b82f6;border-radius:3px;margin-top:6px;transition:width 0.5s;}
img{max-width:100%;border-radius:8px;margin-top:5px;}
</style>
</head>
<body class="pb-24">

<!-- LOGIN -->
<div id="loginBox" class="max-w-sm mx-auto mt-20">
<div class="card text-center">
<h1 class="text-3xl font-bold mb-6">MINTCRESTGOLD</h1>
<input id="username" placeholder="Enter Username" class="mb-4">
<button onclick="login()" class="bg-blue-600 w-full">Login</button>
</div>
</div>

<!-- APP -->
<div id="appBox" class="hidden max-w-md mx-auto space-y-6">

<!-- Home / Wallet -->
<div id="page-home" class="page">
<div class="card text-center">
<h2 class="text-sm text-gray-400">Wallet Balance</h2>
<h1 id="balance" class="text-3xl font-bold mt-2">₨ 0</h1>
<p id="activePlan" class="text-xs text-yellow-400 mt-2"></p>
<p id="profitCountdown" class="text-xs text-green-400 mt-1"></p>
<div class="w-full bg-gray-600 rounded-full h-2 mt-2"><div id="profitBar" class="progress-bar w-0"></div></div>
<button onclick="claimGift()" class="bg-green-600 mt-2 w-full">🎁 Claim Gift</button>
</div>

<!-- Deposit -->
<div class="card">
<h3 class="font-bold mb-4">💳 Deposit</h3>
<input id="depAmount" type="number" placeholder="Amount">
<select id="depMethod">
<option value="">Select Payment Method</option>
<option value="JazzCash 03705519562">JazzCash 03705519562</option>
<option value="Easypaisa 03379827882">Easypaisa 03379827882</option>
<option value="SadaPay 03705519562">SadaPay 03705519562</option>
</select>
<input id="depTid" type="text" placeholder="Transaction ID">
<input id="depProof" type="file" accept="image/*">
<button onclick="deposit()" class="bg-green-600 w-full">Send Deposit Request</button>
<div id="depPreview"></div>
</div>

<!-- Withdraw -->
<div class="card">
<h3 class="font-bold mb-4">🏦 Withdraw</h3>
<input id="wdAmount" type="number" placeholder="Amount">
<input id="wdDetails" type="text" placeholder="Account Details">
<button onclick="withdraw()" class="bg-red-600 w-full">Send Withdraw Request</button>
</div>
</div>

<!-- Plans -->
<div id="page-plans" class="page hidden">
<div class="card">
<h3 class="font-bold mb-4">⚡ Plans / Nodes</h3>
<div id="plans"></div>
</div>
</div>

<!-- Activity -->
<div id="page-activity" class="page hidden">
<div class="card">
<h3 class="font-bold mb-4">📜 Activity / History</h3>
<div id="history"></div>
</div>
</div>

<!-- Help/Admin -->
<div id="page-help" class="page hidden">
<div class="card text-center">
<h3 class="font-bold mb-4">⚙️ Admin Panel / Help</h3>
<button onclick="openAdmin()" class="bg-yellow-600 w-full mb-2">Open Admin Panel</button>
<p class="text-xs text-gray-400">For queries or support contact admin</p>
</div>
</div>

</div>

<!-- Bottom Nav -->
<div class="fixed bottom-0 left-0 right-0 flex bg-black/90 text-gray-400">
<button class="nav-btn active-nav" onclick="showPage('home',this)">🏠</button>
<button class="nav-btn" onclick="showPage('plans',this)">⚡</button>
<button class="nav-btn" onclick="showPage('activity',this)">📜</button>
<button class="nav-btn" onclick="showPage('help',this)">⚙️</button>
</div>

<!-- ADMIN PANEL -->
<div id="adminBox" class="hidden fixed inset-0 bg-black p-6 overflow-auto">
<h2 class="text-xl font-bold mb-6">MINTCRESTGOLD Admin Panel</h2>
<div id="requests"></div>
<button onclick="closeAdmin()" class="mt-6 bg-red-600 px-4 py-2">Close</button>
</div>

<script>
// Firebase config
const firebaseConfig = { apiKey:"YOUR_API_KEY", authDomain:"YOUR_AUTH_DOMAIN", projectId:"YOUR_PROJECT_ID", storageBucket:"YOUR_STORAGE_BUCKET" };
firebase.initializeApp(firebaseConfig);
const db = firebase.firestore();
const storage = firebase.storage();

let currentUser=null;
let profitInterval=null;

// Plans with progress
const plans=[];
for(let i=1;i<=20;i++) plans.push({name:"Standard "+i,price:i*200,dailyProfit:5+(i*0.4)});
plans.push({name:"Premium 1",price:5000,dailyProfit:12});
plans.push({name:"Premium 2",price:8000,dailyProfit:15});
plans.push({name:"Premium 3",price:12000,dailyProfit:18});
plans.push({name:"Premium 4",price:20000,dailyProfit:22});
plans.push({name:"Premium 5",price:50000,dailyProfit:30});

// Render plans
function renderPlans(){
  let html="";
  plans.forEach(p=>{
    html+=`<div class="mb-4">
      <div class="flex justify-between"><span>${p.name} (${p.dailyProfit}% daily)</span><button onclick="buyPlan('${p.name}',${p.price},${p.dailyProfit})" class="bg-blue-600 text-xs px-3">₨ ${p.price}</button></div>
      <div class="w-full bg-gray-600 rounded-full h-2 mt-1"><div class="progress-bar" id="planBar${p.name.replace(/\s/g,'')}" style="width:0%"></div></div>
    </div>`;
  });
  document.getElementById("plans").innerHTML=html;
}

// Show page
function showPage(page,btn){
  document.querySelectorAll(".page").forEach(pg=>pg.classList.add("hidden"));
  document.getElementById("page-"+page).classList.remove("hidden");
  document.querySelectorAll(".nav-btn").forEach(b=>b.classList.remove("active-nav"));
  btn.classList.add("active-nav");
}

// LOGIN
function login(){
  const name=document.getElementById("username").value.trim().toUpperCase();
  if(!name)return alert("Enter username");
  currentUser=name;
  const ref=db.collection("users").doc(name);
  ref.get().then(doc=>{if(!doc.exists) ref.set({balance:0,activePlan:null,planExpiry:null,lastProfit:0,referrals:0});});
  ref.onSnapshot(snap=>{
    const d=snap.data();
    document.getElementById("balance").innerText="₨ "+d.balance;
    document.getElementById("activePlan").innerText=d.activePlan?`Active: ${d.activePlan} | Expiry: ${new Date(d.planExpiry).toLocaleDateString()}`:"No Active Plan";
    startProfitCountdown(d.planExpiry,d.lastProfit);
  });
  document.getElementById("loginBox").classList.add("hidden");
  document.getElementById("appBox").classList.remove("hidden");
  renderPlans();
}

// PROFIT COUNTDOWN + BAR
function startProfitCountdown(expiry,lastProfit){
  clearInterval(profitInterval);
  if(!expiry) return;
  const bar=document.getElementById("profitBar");
  profitInterval=setInterval(()=>{
    const diff=expiry-Date.now();
    if(diff<=0){document.getElementById("profitCountdown").innerText="Next Profit Ready!";bar.style.width="100%";clearInterval(profitInterval);return;}
    let h=Math.floor(diff/3600000), m=Math.floor((diff%3600000)/60000), s=Math.floor((diff%60000)/1000);
    document.getElementById("profitCountdown").innerText=`Next Profit in ${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
    bar.style.width=`${100 - (diff/86400000)*100}%`;
  },1000);
}

// CLAIM GIFT
function claimGift(){db.collection("requests").add({user:currentUser,type:"GIFT",amount:50,status:"pending",time:Date.now()});alert("Gift request sent to admin!");}

// PLAN PURCHASE
function buyPlan(name,price,dailyProfit){db.collection("requests").add({user:currentUser,type:"PLAN",planName:name,amount:price,dailyProfit:dailyProfit,status:"pending",time:Date.now()});alert("Plan Request Sent!");}

// DEPOSIT
document.getElementById("depProof")?.addEventListener("change",(e)=>{
  const file=e.target.files[0];
  if(file){
    const reader=new FileReader();
    reader.onload=function(){document.getElementById("depPreview").innerHTML=`<img src="${reader.result}">`;};
    reader.readAsDataURL(file);
  }
});

function deposit(){
  const amt=parseInt(document.getElementById("depAmount").value);
  const method=document.getElementById("depMethod").value;
  const tid=document.getElementById("depTid").value;
  const file=document.getElementById("depProof").files[0];
  if(!amt||!method||!tid||!file)return alert("Fill all fields");
  const storageRef=storage.ref(`depositProofs/${currentUser}_${Date.now()}_${file.name}`);
  storageRef.put(file).then(()=>storageRef.getDownloadURL().then(url=>{
    db.collection("requests").add({user:currentUser,type:"DEPOSIT",amount:amt,method:method,tid:tid,proof:url,status:"pending",time:Date.now()});
    alert("Deposit Request Sent to Admin");
  }));
}

// WITHDRAW
function withdraw(){const amt=parseInt(document.getElementById("wdAmount").value);const det=document.getElementById("wdDetails").value;if(!amt||!det)return alert("Fill withdraw fields");db.collection("requests").add({user:currentUser,type:"WITHDRAW",amount:amt,details:det,status:"pending",time:Date.now()});alert("Withdraw Request Sent");}

// ADMIN PANEL
function openAdmin(){
  const key=prompt("Admin Key");if(key!=="mint786")return alert("Wrong key");
  document.getElementById("adminBox").classList.remove("hidden");
  db.collection("requests").where("status","==","pending").onSnapshot(snap=>{
    let html="";
    snap.forEach(doc=>{
      const r=doc.data();
      html+=`<div class="card mb-4"><div>${r.user}</div><div>${r.type} - ₨ ${r.amount} ${r.planName? "("+r.planName+")":""}</div>${r.proof?`<img src="${r.proof}">`:''}<button onclick="approve('${doc.id}','${r.user}','${r.type}',${r.amount},'${r.planName||""}',${r.dailyProfit||0})" class="bg-green-600 mt-2">Approve</button><button onclick="reject('${doc.id}')" class="bg-red-600 mt-2 ml-2">Reject</button></div>`;
    });
    document.getElementById("requests").innerHTML=html;
  });
}
function approve(id,user,type,amount,planName,dailyProfit){
  const ref=db.collection("users").doc(user);
  ref.get().then(doc=>{const d=doc.data();
    if(type==="DEPOSIT"){ref.update({balance:d.balance+amount});}
    if(type==="WITHDRAW"){if(d.balance<amount)return alert("Low balance"); ref.update({balance:d.balance-amount});}
    if(type==="PLAN"){if(d.balance<amount)return alert("Low balance"); ref.update({balance:d.balance-amount,activePlan:planName,planExpiry:Date.now()+24*60*60*1000,lastProfit:dailyProfit});}
    if(type==="GIFT"){ref.update({balance:d.balance+amount});}
    db.collection("requests").doc(id).update({status:"approved"});
  });
}
function reject(id){db.collection("requests").doc(id).update({status:"rejected"});}
function closeAdmin(){document.getElementById("adminBox").classList.add("hidden");}
</script>

</body>
</html>
