<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MintCrest Pro</title>

<script src="https://cdn.tailwindcss.com"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>

<style>
body { background:#0d1117; color:white; font-family:sans-serif; }
input { padding:10px; margin:5px 0; width:100%; border-radius:8px; background:#161b22; border:1px solid #30363d; }
button { cursor:pointer; }
.fbtn{ padding:8px 12px; border-radius:8px; background:#21262d; color:white; }
</style>

</head>
<body>

<h2 style="padding:20px;">Dashboard</h2>

<input id="w-amt" placeholder="Amount">
<input id="w-acc" placeholder="Account">
<input id="w-name" placeholder="Account Name">

<button onclick="submitWith()" style="padding:10px;background:#2563eb;border-radius:10px;">
Withdraw
</button>

<div id="countdown" style="padding:20px;"></div>

<!-- ================= HISTORY PAGE ================= -->
<div id="historyPage" style="display:none;position:fixed;top:0;left:0;width:100%;height:100%;background:#0d1117;z-index:9999;overflow:auto;padding:20px;">

  <div style="display:flex;justify-content:space-between;align-items:center;">
    <h2 style="font-size:22px;">📜 History</h2>
    <button onclick="closeHistory()">✖</button>
  </div>

  <div style="display:flex;gap:10px;margin:15px 0;">
    <button onclick="filterHistory('all')" class="fbtn">All</button>
    <button onclick="filterHistory('deposit')" class="fbtn">Deposit</button>
    <button onclick="filterHistory('withdraw')" class="fbtn">Withdraw</button>
  </div>

  <div id="historyList"></div>
</div>

<!-- ================= FLOATING BUTTONS ================= -->
<div style="position:fixed;bottom:110px;right:20px;display:flex;flex-direction:column;gap:12px;">

  <button onclick="showHistory()" style="width:55px;height:55px;border-radius:50%;background:#2563eb;font-size:22px;">
    📜
  </button>

  <button onclick="logout()" style="width:55px;height:55px;border-radius:50%;background:#ef4444;font-size:22px;">
    ⎋
  </button>

</div>

<script>

// 🔐 Firebase config (ADD YOURS)
const firebaseConfig = {
  apiKey: "YOUR_KEY",
  authDomain: "YOUR_DOMAIN",
  projectId: "YOUR_PROJECT"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.firestore();

// 👤 USER
let user = JSON.parse(localStorage.getItem("user") || "null");

// ================= WITHDRAW =================
async function submitWith(){
    const a = Number(document.getElementById('w-amt').value);
    const ac = document.getElementById('w-acc').value.trim();
    const nm = document.getElementById('w-name').value.trim();

    if(isNaN(a) || a<=0) return alert("Invalid amount");
    if(!ac || !nm) return alert("Fill details!");
    if(!user) return alert("Login first");

    await db.collection("requests").add({
        user: user.name,
        amount: a,
        account: ac,
        holder: nm,
        type: "withdraw",
        status: "pending",
        time: Date.now()
    });

    alert("Request Submitted");
}

// ================= TIMER =================
let timerInterval;
function startTimer(end){
    if(timerInterval) clearInterval(timerInterval);

    timerInterval = setInterval(()=>{
        const diff = end - Date.now();

        if(diff<=0){
            clearInterval(timerInterval);
            document.getElementById('countdown').innerText = "DONE";
            return;
        }

        document.getElementById('countdown').innerText =
            Math.floor(diff/1000) + "s";
    },1000);
}

// ================= HISTORY SYSTEM =================
let allHistory = [];

async function showHistory(){
    if(!user || !user.name){
        alert("Login required");
        return;
    }

    document.getElementById("historyPage").style.display = "block";
    const list = document.getElementById("historyList");
    list.innerHTML = "Loading...";

    try{
        const snap = await db.collection("requests")
            .where("user","==",user.name)
            .get();

        allHistory = [];
        snap.forEach(doc=> allHistory.push(doc.data()));

        renderHistory(allHistory);

    }catch(e){
        console.error(e);
        list.innerHTML = "Error loading";
    }
}

function renderHistory(data){
    const list = document.getElementById("historyList");

    if(data.length === 0){
        list.innerHTML = "No data found";
        return;
    }

    list.innerHTML = "";

    data.reverse().forEach(d=>{
        let color = "#facc15";

        if(d.status === "approved") color = "#22c55e";
        if(d.status === "rejected") color = "#ef4444";

        const div = document.createElement("div");

        div.style.padding = "12px";
        div.style.marginBottom = "10px";
        div.style.borderRadius = "12px";
        div.style.background = "#161b22";
        div.style.border = "1px solid #30363d";

        div.innerHTML = `
            <div style="display:flex;justify-content:space-between;">
                <b>${d.type}</b>
                <span style="color:${color};">${d.status}</span>
            </div>
            <div>Amount: ${d.amount} PKR</div>
            <div style="font-size:12px;opacity:0.7;">
                ${new Date(d.time).toLocaleString()}
            </div>
        `;

        list.appendChild(div);
    });
}

function filterHistory(type){
    if(type === "all"){
        renderHistory(allHistory);
    }else{
        renderHistory(allHistory.filter(d=>d.type === type));
    }
}

function closeHistory(){
    document.getElementById("historyPage").style.display = "none";
}

// ================= LOGOUT =================
function logout(){
    if(confirm("Logout?")){
        localStorage.removeItem("user");
        location.reload();
    }
}

</script>

</body>
</html>
