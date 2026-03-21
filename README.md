<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MintCrest Ultimate</title>

<script src="https://cdn.tailwindcss.com"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>

<style>
body { background:#0d1117; color:white; font-family:sans-serif; }
input { padding:10px; margin:5px 0; width:100%; border-radius:8px; background:#161b22; border:1px solid #30363d; }
button { padding:10px; border-radius:10px; }
.card { background:#161b22; padding:12px; border-radius:12px; border:1px solid #30363d; margin-bottom:10px; }
</style>
</head>
<body>

<!-- ================= AUTH ================= -->
<div id="authPage">
  <h2>Login / Signup</h2>
  <input id="auth-name" placeholder="Username">
  <input id="auth-pass" type="password" placeholder="Password">
  <button onclick="login()" style="background:#22c55e;">Login</button>
  <button onclick="signup()" style="background:#2563eb;">Signup</button>
</div>

<!-- ================= APP ================= -->
<div id="app" style="display:none;padding:20px;">

<h2>Dashboard</h2>
<div id="balance">Balance: 0</div>

<input id="amount" placeholder="Amount">
<input id="account" placeholder="Account">
<input id="name" placeholder="Name">

<button onclick="deposit()" style="background:#22c55e;">Deposit</button>
<button onclick="withdraw()" style="background:#ef4444;">Withdraw</button>

<button onclick="showHistory()">History</button>
<button onclick="logout()">Logout</button>

<h3>Admin Panel</h3>
<div id="admin"></div>

</div>

<!-- ================= HISTORY ================= -->
<div id="historyPage" style="display:none;position:fixed;top:0;left:0;width:100%;height:100%;background:#0d1117;overflow:auto;padding:20px;">
<h2>History</h2>
<button onclick="closeHistory()">Close</button>
<div id="historyList"></div>
</div>

<script>

// 🔐 Firebase config
const firebaseConfig = {
  apiKey: "YOUR_KEY",
  authDomain: "YOUR_DOMAIN",
  projectId: "YOUR_PROJECT"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.firestore();

let user = JSON.parse(localStorage.getItem("user") || "null");

// ================= INIT =================
window.onload = async ()=>{
  if(user){
    authPage.style.display="none";
    app.style.display="block";
    loadBalance();
    loadAdmin();
  }
};

// ================= AUTH =================
async function signup(){
  const name=auth-name.value.trim();
  const pass=auth-pass.value.trim();

  const ref=db.collection("users").doc(name);
  if((await ref.get()).exists) return alert("User exists");

  await ref.set({password:pass,balance:0});
  alert("Account created");
}

async function login(){
  const name=auth-name.value.trim();
  const pass=auth-pass.value.trim();

  const doc=await db.collection("users").doc(name).get();
  if(!doc.exists) return alert("No user");

  if(doc.data().password!==pass) return alert("Wrong pass");

  localStorage.setItem("user",JSON.stringify({name}));
  location.reload();
}

// ================= LOGOUT =================
function logout(){
  localStorage.removeItem("user");
  location.reload();
}

// ================= BALANCE =================
async function loadBalance(){
  const doc=await db.collection("users").doc(user.name).get();
  balance.innerText="Balance: "+doc.data().balance;
}

// ================= DEPOSIT =================
async function deposit(){
  const amt=Number(amount.value);
  if(amt<=0) return alert("Invalid");

  await db.collection("requests").add({
    user:user.name,amount:amt,type:"deposit",status:"pending",time:Date.now()
  });

  alert("Deposit request sent");
}

// ================= WITHDRAW =================
async function withdraw(){
  const amt=Number(amount.value);
  if(amt<=0) return alert("Invalid");

  await db.collection("requests").add({
    user:user.name,amount:amt,type:"withdraw",status:"pending",time:Date.now()
  });

  alert("Withdraw request sent");
}

// ================= HISTORY =================
let allHistory=[];

async function showHistory(){
  historyPage.style.display="block";
  const snap=await db.collection("requests").where("user","==",user.name).get();

  allHistory=[];
  snap.forEach(d=>allHistory.push(d.data()));

  renderHistory(allHistory);
}

function renderHistory(data){
  historyList.innerHTML="";
  data.forEach(d=>{
    let color="#facc15";
    if(d.status==="approved") color="#22c55e";
    if(d.status==="rejected") color="#ef4444";

    historyList.innerHTML+=`
    <div class="card">
      <b>${d.type}</b> - <span style="color:${color}">${d.status}</span><br>
      ${d.amount} PKR<br>
      ${new Date(d.time).toLocaleString()}
    </div>`;
  });
}

function closeHistory(){ historyPage.style.display="none"; }

// ================= ADMIN =================
async function loadAdmin(){
  if(user.name!=="admin") return;

  const snap=await db.collection("requests").get();
  admin.innerHTML="<h3>Requests</h3>";

  snap.forEach(doc=>{
    const d=doc.data();

    admin.innerHTML+=`
    <div class="card">
      ${d.user} - ${d.type} - ${d.amount}
      <button onclick="approve('${doc.id}')">Approve</button>
      <button onclick="reject('${doc.id}')">Reject</button>
    </div>`;
  });
}

async function approve(id){
  const ref=db.collection("requests").doc(id);
  const d=(await ref.get()).data();

  if(d.type==="deposit"){
    await db.collection("users").doc(d.user)
      .update({balance:firebase.firestore.FieldValue.increment(d.amount)});
  }

  if(d.type==="withdraw"){
    await db.collection("users").doc(d.user)
      .update({balance:firebase.firestore.FieldValue.increment(-d.amount)});
  }

  await ref.update({status:"approved"});
  loadAdmin(); loadBalance();
}

async function reject(id){
  await db.collection("requests").doc(id).update({status:"rejected"});
  loadAdmin();
}

</script>

</body>
</html>
