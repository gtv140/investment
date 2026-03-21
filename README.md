<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>MINTCREST GOLD | FINAL SYSTEM</title>

<style>
body{margin:0;font-family:Arial;background:#0b0f1a;color:#fff}
header{padding:15px;text-align:center;background:linear-gradient(90deg,#d4af37,#b8860b);font-weight:bold}
.container{max-width:1100px;margin:auto;padding:15px}
.card{background:#121a2a;padding:15px;border-radius:12px;margin:10px 0}
input{width:100%;padding:10px;margin:6px 0;border-radius:8px;border:none}
button{padding:10px;border:none;border-radius:8px;font-weight:bold;cursor:pointer}
.btn{background:#d4af37}
.danger{background:#ff4d4d;color:#fff}
.success{background:#00c853;color:#000}
.row{display:flex;gap:10px;flex-wrap:wrap}
.col{flex:1;min-width:250px}
.hidden{display:none}
.tx{background:#0f1626;padding:10px;margin:6px 0;border-radius:10px;font-size:13px}
.badge{padding:3px 8px;border-radius:6px;font-size:11px}
.pending{background:orange;color:#000}
.approved{background:#00c853;color:#000}
.rejected{background:#ff4d4d;color:#fff}
</style>

</head>

<body>

<header>MINTCREST GOLD | FINAL LIVE SYSTEM</header>

<div class="container">

<!-- LOGIN -->
<div class="card" id="loginBox">
<h3>Login</h3>
<input id="username" placeholder="Enter Username"/>
<button class="btn" onclick="login()">Enter</button>
</div>

<!-- APP -->
<div id="app" class="hidden">

<div class="card">
<b id="userLabel"></b><br>
Balance: <span id="balance">0</span>
<br><br>
<button class="danger" onclick="logout()">Logout</button>
</div>

<div class="row">

<!-- DEPOSIT -->
<div class="card col">
<h3>Deposit</h3>
<input id="amount" type="number" placeholder="Amount"/>
<input id="proof" placeholder="Proof URL"/>
<button class="btn" onclick="deposit()">Send Deposit</button>
</div>

<!-- WITHDRAW -->
<div class="card col">
<h3>Withdraw</h3>
<input id="wamount" type="number" placeholder="Amount"/>
<button class="btn" onclick="withdraw()">Send Withdraw</button>
</div>

</div>

<div class="row">

<div class="card col">
<h3>History</h3>
<div id="history"></div>
</div>

<div class="card col" id="adminBox">
<h3>Admin Panel</h3>
<div id="admin"></div>
</div>

</div>

</div>

</div>

<script type="module">

import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js";
import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-auth.js";
import {
getFirestore,
collection,
addDoc,
query,
getDocs,
updateDoc,
doc,
runTransaction,
increment,
setDoc,
getDoc
} from "https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js";

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

let user;

// ⚠️ CHANGE THIS TO YOUR REAL ADMIN UID
const ADMIN_UID = "CHANGE_THIS_ADMIN_UID";

window.login = async () => {
if(!username.value) return alert("Enter username");
localStorage.setItem("u", username.value);
await signInAnonymously(auth);
};

onAuthStateChanged(auth, async (u)=>{
if(u){
user = u;
loginBox.classList.add("hidden");
app.classList.remove("hidden");
userLabel.innerText = localStorage.getItem("u");
await ensureUser();
load();
}
});

window.logout = ()=> location.reload();

// CREATE USER WALLET
async function ensureUser(){
const ref = doc(db,"users",user.uid);
const snap = await getDoc(ref);
if(!snap.exists()){
await setDoc(ref,{balance:0});
}
}

// DEPOSIT
window.deposit = async ()=>{
await addDoc(collection(db,"requests"),{
uid:user.uid,
username:localStorage.getItem("u"),
type:"deposit",
amount:Number(amount.value),
proof:proof.value,
status:"pending",
time:Date.now()
});
alert("Deposit sent");
load();
};

// WITHDRAW
window.withdraw = async ()=>{
await addDoc(collection(db,"requests"),{
uid:user.uid,
username:localStorage.getItem("u"),
type:"withdraw",
amount:Number(wamount.value),
status:"pending",
time:Date.now()
});
alert("Withdraw sent");
load();
};

// LOAD DATA
async function load(){

const snap = await getDocs(collection(db,"requests"));

let hist="",adm="";

snap.forEach(d=>{
let x = d.data();

// HISTORY
if(x.uid === user.uid){
hist += `<div class='tx'>
${x.type} | ${x.amount}
<span class='badge ${x.status}'>${x.status}</span>
</div>`;
}

// ADMIN PANEL
adm += `<div class='tx'>
<b>${x.username}</b><br>
${x.type} | ${x.amount}<br>
Status: ${x.status}<br>
${x.proof ? `<a href="${x.proof}" target="_blank">View Proof</a><br>`:""}
<button class="success" onclick="approve('${d.id}','${x.uid}',${x.amount},'${x.type}')">Approve</button>
<button class="danger" onclick="reject('${d.id}')">Reject</button>
</div>`;
});

history.innerHTML = hist || "No data";
admin.innerHTML = adm;

// BALANCE UPDATE
const userRef = doc(db,"users",user.uid);
const userSnap = await getDoc(userRef);
balance.innerText = userSnap.data()?.balance || 0;

// ADMIN ACCESS
if(localStorage.getItem("u") === "admin"){
adminBox.classList.remove("hidden");
}else{
adminBox.classList.add("hidden");
}
}

// APPROVE (LEDGER SAFE)
window.approve = async (id, uid, amount, type)=>{

const reqRef = doc(db,"requests",id);
const userRef = doc(db,"users",uid);

await runTransaction(db, async (t)=>{

const req = await t.get(reqRef);
if(req.data().status !== "pending") return;

t.update(reqRef,{status:"approved"});

if(type === "deposit"){
t.update(userRef,{balance: increment(amount)});
}

if(type === "withdraw"){
t.update(userRef,{balance: increment(-amount)});
}

});

alert("Approved");
load();
};

// REJECT
window.reject = async (id)=>{
await updateDoc(doc(db,"requests",id),{status:"rejected"});
alert("Rejected");
load();
};

</script>

</body>
</html>
