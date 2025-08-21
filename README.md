# index.html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Color Trading Game</title>
<style>
body { font-family: Arial; text-align:center; padding:10px; background:#f0f0f0; }
h1 { color:#333; }
.color-card { display:inline-block; margin:10px; border-radius:10px; overflow:hidden; width:120px; box-shadow:0 3px 6px rgba(0,0,0,0.2);}
.color-box { width:120px; height:120px; }
.price { font-weight:bold; padding:5px; color:#fff; text-shadow:1px 1px 2px #000; }
.buttons { display:flex; justify-content:space-around; padding:5px; background:#fff; }
button { flex:1; margin:0 2px; padding:6px 0; border:none; border-radius:6px; cursor:pointer; font-weight:bold; color:#fff; }
button.buy { background:#28a745; }
button.sell { background:#dc3545; }
#wallet,#portfolio { margin-top:10px; font-weight:bold; }
#ownedColors { margin-top:5px; font-weight:bold; }
#transactions { margin-top:10px; max-width:350px; margin:auto; text-align:left; }
#transactions p { background:#fff; padding:5px 10px; border-radius:5px; margin:5px 0; box-shadow:1px 1px 5px rgba(0,0,0,0.1); }
</style>
</head>
<body>

<h1>Color Trading Game</h1>
<div id="colors"></div>
<div id="wallet">Wallet: $100</div>
<div id="portfolio">Portfolio Value: $100</div>
<div id="ownedColors">Owned Colors: None</div>
<div id="transactions"></div>

<script>
let wallet = 100;
let owned = {};
const colors = ["red","green","blue","yellow","purple"];
const colorPrices = {};
const transactionsDiv = document.getElementById("transactions");

// Initialize random prices
colors.forEach(color => colorPrices[color] = Math.floor(Math.random()*20)+1);

function displayColors(){
  const colorsDiv = document.getElementById("colors");
  colorsDiv.innerHTML="";
  colors.forEach(color=>{
    const card=document.createElement("div");
    card.className="color-card";

    const box=document.createElement("div");
    box.className="color-box";
    box.style.backgroundColor=color;
    card.appendChild(box);

    const priceTag=document.createElement("div");
    priceTag.className="price";
    priceTag.style.backgroundColor=color;
    priceTag.innerText="$"+colorPrices[color];
    card.appendChild(priceTag);

    const btnDiv=document.createElement("div");
    btnDiv.className="buttons";

    const buyBtn=document.createElement("button");
    buyBtn.className="buy"; buyBtn.innerText="Buy";
    buyBtn.onclick=()=>buyColor(color);

    const sellBtn=document.createElement("button");
    sellBtn.className="sell"; sellBtn.innerText="Sell";
    sellBtn.onclick=()=>sellColor(color);

    btnDiv.appendChild(buyBtn); btnDiv.appendChild(sellBtn);
    card.appendChild(btnDiv);

    colorsDiv.appendChild(card);
  });
}

function buyColor(color){
  const price=colorPrices[color];
  if(wallet>=price){
    wallet-=price;
    owned[color]=(owned[color]||0)+1;
    addTransaction(`Bought 1 ${color} for $${price}`);
  } else alert("Not enough money!");
  updateUI();
}

function sellColor(color){
  if(owned[color]>0){
    const price=colorPrices[color];
    wallet+=price;
    owned[color]-=1;
    const bought = price - Math.floor(Math.random()*5);
    const profit = price - bought;
    addTransaction(`Sold 1 ${color} for $${price} (Profit: $${profit})`);
  } else alert("You don't own this color!");
  updateUI();
}

function updateWallet(){ document.getElementById("wallet").innerText="Wallet: $"+wallet; }

function updatePortfolio(){
  let portfolio=0;
  Object.keys(owned).forEach(c=>{ portfolio+=owned[c]*colorPrices[c]; });
  document.getElementById("portfolio").innerText="Portfolio Value: $"+(wallet+portfolio);
}

function updateOwned(){
  const ownedDiv=document.getElementById("ownedColors");
  let ownedText=Object.keys(owned).filter(c=>owned[c]>0).map(c=>`${c} (${owned[c]})`).join(", ");
  ownedDiv.innerText="Owned Colors: "+(ownedText||"None");
}

function addTransaction(text){
  const p=document.createElement("p");
  p.innerText=text;
  transactionsDiv.prepend(p);
}

function updateUI(){ updateWallet(); updateOwned(); updatePortfolio(); }

// Price fluctuation every 5 seconds
setInterval(()=>{
  colors.forEach(c=>{
    const change=Math.floor(Math.random()*5)-2;
    colorPrices[c]=Math.max(1,colorPrices[c]+change);
  });
  displayColors();
  updatePortfolio();
},5000);

displayColors();
updateUI();
</script>

</body>
</html>
