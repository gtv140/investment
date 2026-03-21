<!DOCTYPE html>
<html>
<head>
  <title>Mint App</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style>
    body{font-family:Arial;background:#0f172a;color:white;padding:10px}
    .card{background:#1e293b;padding:15px;border-radius:10px;margin:10px 0}
    button{padding:8px;margin:5px;border:none;border-radius:5px;cursor:pointer}
    input,select{padding:8px;margin:5px;border:none;border-radius:5px;width:90%}
    img{border-radius:10px;margin-top:5px}
  </style>
</head>

<body>

<h2>💰 Mint App</h2>

<div id="userPanel">

<div class="card">
Balance: <b id="bal">0</b>
</div>

<div class="card">
<h3>💳 Deposit</h3>

<select id="method">
<option value="jazzcash">JazzCash</option>
<option value="easypaisa">EasyPaisa</option>
<option value="sadapay">SadaPay</option>
</select>

<div id="paymentInfo"></div>

<input id="amount" placeholder="Amount">
<input id="tid" placeholder="TID">
<input id="account" placeholder="Your Number">
<input type="file" id="proof">

<button onclick="deposit()">Submit Deposit</button>
</div>

<div class="card">
<h3>💸 Withdraw</h3>
<input id="wamount" placeholder="Amount">
<input id="waccount" placeholder="Account">
<input type="file" id="wproof">
<button onclick="withdraw()">Withdraw</button>
</div>

</div>

<div id="adminPanel" style="display:none">
<h3>🛠 Admin Panel</h3>
<div id="requests"></div>
</div>

<div id="secretBox" style="display:none;position:fixed;top:40%;left:50%;transform:translate(-50%,-50%);background:#000;padding:20px;border-radius:10px;">
<input id="secretKey" placeholder="Enter Key">
<button onclick="checkKey()">Enter</button>
</div>

<script type="module">

import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getFirestore, collection, addDoc, getDocs, doc, updateDoc, getDoc, query, where, setDoc } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";
import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-auth.js";
import { getStorage, ref, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-storage.js";

const firebaseConfig = {
  apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg",
  authDomain: "investment-84f4e.firebaseapp.com",
  projectId: "investment-84f4e",
  storageBucket: "investment-84f4e.appspot.com"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);
const auth = getAuth(app);
const storage = getStorage(app);

const ADMIN_UID = "PASTE_UID_HERE";
const SECRET = "Mint786";

let currentUser;
let attempts = 0;

signInAnonymously(auth);

onAuthStateChanged(auth, async (user)=>{
  currentUser = user;

  const refUser = doc(db,"users",user.uid);
  const snap = await getDoc(refUser);

  if(!snap.exists()){
    await setDoc(refUser,{balance:0});
  }

  loadBalance();
});

async function loadBalance(){
  const d = (await getDoc(doc(db,"users",currentUser.uid))).data();
  bal.innerText = d.balance || 0;
}

function updatePaymentInfo(){
  let m = method.value;
  let num = m==="easypaisa"?"03379827882":"03705519562";
  paymentInfo.innerHTML = "Send to: "+num;
}
method.onchange = updatePaymentInfo;
updatePaymentInfo();

window.deposit = async ()=>{
  let file = proof.files[0];
  let url="";

  if(file){
    let r = ref(storage,"proof/"+Date.now());
    await uploadBytes(r,file);
    url = await getDownloadURL(r);
  }

  await addDoc(collection(db,"requests"),{
    user:currentUser.uid,
    amount:Number(amount.value),
    tid:tid.value,
    account:account.value,
    method:method.value,
    proof:url,
    type:"deposit",
    status:"pending"
  });

  alert("Deposit sent");
};

window.withdraw = async ()=>{
  await addDoc(collection(db,"requests"),{
    user:currentUser.uid,
    amount:Number(wamount.value),
    account:waccount.value,
    type:"withdraw",
    status:"pending"
  });
};

async function loadAdmin(){
  userPanel.style.display="none";
  adminPanel.style.display="block";

  const q = query(collection(db,"requests"), where("status","==","pending"));
  const snap = await getDocs(q);

  requests.innerHTML="";

  snap.forEach(d=>{
    let data = d.data();

    requests.innerHTML+=`
    <div class="card">
    ${data.user}<br>
    ${data.type}<br>
    ${data.amount || ""}<br>

    <input id="amt_${d.id}" placeholder="Amount">

    <button onclick="approve('${d.id}')">Approve</button>
    <button onclick="reject('${d.id}')">Reject</button>
    </div>`;
  });

  // ⏳ Auto hide admin after 5 min
  setTimeout(()=>{
    adminPanel.style.display="none";
    userPanel.style.display="block";
  },300000);
}

window.approve = async (id)=>{
  let refReq = doc(db,"requests",id);
  let snap = await getDoc(refReq);
  let d = snap.data();

  let userRef = doc(db,"users",d.user);
  let u = (await getDoc(userRef)).data();

  let amt = Number(document.getElementById("amt_"+id).value) || 0;

  await updateDoc(userRef,{balance:(u.balance||0)+amt});
  await updateDoc(refReq,{status:"approved"});

  loadAdmin();
};

window.reject = async (id)=>{
  await updateDoc(doc(db,"requests",id),{status:"rejected"});
  loadAdmin();
};

// TAP SYSTEM
let taps=0;
document.body.onclick=()=>{
  taps++;
  if(taps>=5){
    secretBox.style.display="block";
    taps=0;
  }
};

// 🔐 KEY SYSTEM
window.checkKey = ()=>{
  if(!secretKey.value) return alert("Enter key");

  if(secretKey.value === SECRET){
    attempts = 0;
    loadAdmin();
    secretBox.style.display="none";
  } else {
    attempts++;
    alert("Wrong key");

    if(attempts >= 3){
      alert("Too many attempts!");
      secretBox.style.display="none";
      attempts = 0;
    }
  }
};

</script>

</body>
</html>
