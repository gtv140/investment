<html>
<head>
<title>Mint Pro</title>
<meta name="viewport" content="width=device-width, initial-scale=1">

<style>
body{
  margin:0;
  font-family:sans-serif;
  background:linear-gradient(135deg,#0f172a,#1e293b);
  color:white;
}

.header{
  padding:15px;
  text-align:center;
  font-size:20px;
  font-weight:bold;
}

.card{
  background:rgba(255,255,255,0.05);
  backdrop-filter:blur(10px);
  border-radius:15px;
  padding:15px;
  margin:10px;
  box-shadow:0 0 10px rgba(0,0,0,0.3);
}

button{
  background:#22c55e;
  color:white;
  border:none;
  padding:10px;
  border-radius:8px;
  cursor:pointer;
}

input,select{
  width:100%;
  padding:10px;
  margin:5px 0;
  border-radius:8px;
  border:none;
}

.balance{
  font-size:24px;
  font-weight:bold;
}

.status{
  padding:5px;
  border-radius:5px;
  font-size:12px;
}

.pending{background:orange}
.approved{background:green}
.rejected{background:red}

.toast{
  position:fixed;
  bottom:20px;
  left:50%;
  transform:translateX(-50%);
  background:#000;
  padding:10px;
  border-radius:10px;
  display:none;
}
</style>
</head>

<body>

<div class="header">💰 Mint Pro</div>

<button onclick="logout()">Logout</button>

<div id="userPanel">

<div class="card">
Balance: <div class="balance" id="bal">0</div>
</div>

<div class="card">
<h3>💳 Deposit</h3>

<select id="method">
<option value="jazzcash">JazzCash</option>
<option value="easypaisa">EasyPaisa</option>
<option value="sadapay">SadaPay</option>
</select>

<div id="paymentInfo"></div>

<button onclick="copyNumber()">Copy Number</button>

<input id="amount" placeholder="Amount">
<input id="tid" placeholder="TID">
<input id="account" placeholder="Your Number">
<input type="file" id="proof">

<button onclick="deposit()">Submit</button>
</div>

<div class="card">
<h3>💸 Withdraw</h3>
<input id="wamount" placeholder="Amount">
<input id="waccount" placeholder="Account">
<button onclick="withdraw()">Withdraw</button>
</div>

<div class="card">
<h3>👥 Referral</h3>
Your Code: <b id="ref"></b>
</div>

<div class="card">
<h3>📜 History</h3>
<div id="history"></div>
</div>

</div>

<div id="adminPanel" style="display:none">
<div class="card">
<h3>Admin Panel</h3>
<div id="requests"></div>
</div>
</div>

<div id="secretBox" style="display:none;position:fixed;top:40%;left:50%;transform:translate(-50%,-50%);background:#000;padding:20px;border-radius:10px;">
<input id="secretKey" placeholder="Enter Key">
<button onclick="checkKey()">Enter</button>
</div>

<div class="toast" id="toast"></div>

<script type="module">

import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getFirestore, collection, addDoc, getDocs, doc, updateDoc, getDoc, query, where, setDoc } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";
import { getAuth, signInAnonymously, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-auth.js";
import { getStorage, ref, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-storage.js";

const app = initializeApp({
  apiKey:"AIzaSy...",
  authDomain:"investment-84f4e.firebaseapp.com",
  projectId:"investment-84f4e",
  storageBucket:"investment-84f4e.appspot.com"
});

const db = getFirestore(app);
const auth = getAuth(app);
const storage = getStorage(app);

const SECRET="Mint786";
let user,attempts=0;

signInAnonymously(auth);

onAuthStateChanged(auth, async(u)=>{
  user=u;

  let refUser=doc(db,"users",u.uid);
  let snap=await getDoc(refUser);

  if(!snap.exists()){
    await setDoc(refUser,{balance:0});
  }

  bal.innerText=(await getDoc(refUser)).data().balance;
  ref.innerText=u.uid.slice(0,6);

  loadHistory();
});

function toastMsg(t){
  toast.innerText=t;
  toast.style.display="block";
  setTimeout(()=>toast.style.display="none",2000);
}

function updatePaymentInfo(){
  let m=method.value;
  let num=m==="easypaisa"?"03379827882":"03705519562";
  paymentInfo.innerHTML="Send to: "+num;
}
method.onchange=updatePaymentInfo;
updatePaymentInfo();

window.copyNumber=()=>{
  navigator.clipboard.writeText(paymentInfo.innerText);
  toastMsg("Copied!");
};

window.deposit=async()=>{
  let file=proof.files[0];
  let url="";

  if(file){
    let r=ref(storage,"proof/"+Date.now());
    await uploadBytes(r,file);
    url=await getDownloadURL(r);
  }

  await addDoc(collection(db,"requests"),{
    user:user.uid,
    amount:Number(amount.value),
    type:"deposit",
    proof:url,
    status:"pending"
  });

  toastMsg("Deposit Sent");
};

window.withdraw=async()=>{
  await addDoc(collection(db,"requests"),{
    user:user.uid,
    amount:Number(wamount.value),
    type:"withdraw",
    status:"pending"
  });

  toastMsg("Withdraw Sent");
};

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

window.logout=()=>{
  signOut(auth);
  location.reload();
};

// ADMIN
async function loadAdmin(){
  userPanel.style.display="none";
  adminPanel.style.display="block";

  let q=query(collection(db,"requests"),where("status","==","pending"));
  let snap=await getDocs(q);

  requests.innerHTML="";
  snap.forEach(d=>{
    let data=d.data();

    requests.innerHTML+=`
    <div class="card">
    ${data.user}<br>${data.type}<br>${data.amount}
    <input id="amt_${d.id}" placeholder="Amount">
    <button onclick="approve('${d.id}')">Approve</button>
    <button onclick="reject('${d.id}')">Reject</button>
    </div>`;
  });
}

// SECRET
let taps=0;
document.body.onclick=()=>{
  taps++;
  if(taps>=5){
    secretBox.style.display="block";
    taps=0;
  }
};

window.checkKey=()=>{
  if(secretKey.value===SECRET){
    loadAdmin();
    secretBox.style.display="none";
  }else{
    attempts++;
    toastMsg("Wrong Key");
    if(attempts>=3){
      secretBox.style.display="none";
      attempts=0;
    }
  }
};

window.approve=async(id)=>{
  let r=doc(db,"requests",id);
  let d=(await getDoc(r)).data();

  let uRef=doc(db,"users",d.user);
  let u=(await getDoc(uRef)).data();

  let amt=Number(document.getElementById("amt_"+id).value)||0;

  await updateDoc(uRef,{balance:(u.balance||0)+amt});
  await updateDoc(r,{status:"approved"});
  loadAdmin();
};

window.reject=async(id)=>{
  await updateDoc(doc(db,"requests",id),{status:"rejected"});
  loadAdmin();
};

</script>

</body>
</html>
