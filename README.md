# Frugiterfood
Trananhminh
<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>FOODIE AERO</title>
<style>
*{box-sizing:border-box}
body{
 margin:0;font-family:Arial,sans-serif;color:#155c70;
 background:linear-gradient(140deg,#a9f4f4,#eaffff,#b8f3dc);
}
header{text-align:center;padding:25px;background:linear-gradient(135deg,#32cddd,#c4ffff);border-bottom:3px solid white}
h1{font-size:32px;text-shadow:2px 3px white}
main{max-width:850px;margin:auto;padding:16px}
.panel{background:#ffffffb9;border:2px solid white;border-radius:22px;padding:16px;margin:15px 0;box-shadow:0 5px 20px #299ab533}
button{border:2px solid white;border-radius:18px;padding:10px 14px;background:linear-gradient(135deg,#bfffff,#54d5df);color:#155c70;font-weight:bold;cursor:pointer}
button:disabled{opacity:.4}
input,textarea{width:100%;padding:13px;margin:6px 0;border:2px solid white;border-radius:13px;background:#f1ffff;font:inherit;color:#155c70}
.grid{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:12px}
.card{background:linear-gradient(145deg,#fff,#c8f8f5);padding:12px;border:2px solid white;border-radius:20px}
.art{height:100px;display:grid;place-items:center;font-size:55px;background:#d7ffff;border-radius:15px}
.price{color:#078ba4;font-weight:bold}
.row{display:flex;justify-content:space-between;align-items:center;gap:8px}
.small{font-size:13px;color:#397d89}
#success{display:none;position:fixed;inset:0;background:#075b7099;z-index:10;align-items:center;justify-content:center;padding:20px}
#successBox{background:linear-gradient(145deg,#e4ffff,#a8f4ed);border:4px solid white;border-radius:28px;padding:25px;text-align:center;max-width:380px;width:100%;box-shadow:0 10px 40px #003b4b66;animation:pop .55s ease}
.cat{font-size:95px;animation:bounce 1s infinite alternate}
@keyframes pop{from{transform:scale(.3)}to{transform:scale(1)}}
@keyframes bounce{to{transform:translateY(-12px)}}
footer{text-align:center;padding:25px}
</style>
</head>
<body>
<header>
<h1>🫧 FOODIE AERO</h1>
<p>Welcome to our Aqua Food World 🌊</p>
</header>
<main>
<section class="panel">
<h2>📍 Giao hàng đến</h2>
<input id="address" placeholder="Nhập địa chỉ giao hàng...">
</section>

<section class="panel">
<h2>🏪 Chọn quán ăn</h2>
<div class="grid" id="restaurants"></div>
</section>

<section class="panel" id="menuPanel" style="display:none">
<div class="row">
<h2 id="restaurantName">Menu</h2>
<button onclick="closeMenu()">← Quay lại</button>
</div>
<div class="grid" id="menu"></div>
</section>

<section class="panel">
<h2>🛒 Giỏ hàng</h2>
<div id="cart">Chưa có món nào!</div>
<h2 id="total" class="price">Tổng: 0đ</h2>
</section>

<section class="panel">
<h2>📦 Thông tin đặt hàng</h2>
<input id="customer" placeholder="Tên người nhận">
<input id="phone" type="tel" placeholder="Số điện thoại">
<textarea id="note" placeholder="Ghi chú cho quán (nếu có)"></textarea>
<p>💳 Thanh toán giả lập</p>
<p class="small">Không thu tiền thật. Đây chỉ là đơn hàng thử nghiệm.</p>
<button style="width:100%" onclick="checkout()">XÁC NHẬN ĐẶT HÀNG ♡</button>
</section>
</main>

<footer>FOODIE AERO © 2026 🫧</footer>

<div id="success">
<div id="successBox">
<div class="cat">🐱</div>
<h2>MEOW MEOW! ♡</h2>
<h2>CẢM ƠN BẠN!</h2>
<p>Đơn hàng demo của bạn đã hoàn tất!</p>
<p id="orderInfo"></p>
<button onclick="finish()">Quay lại cửa hàng 🩵</button>
</div>
</div>

<script>
const shops=[
{name:"Burger Planet",emoji:"🍔",foods:[
["Burger bò",45000,"🍔"],["Burger gà",39000,"🍗"],
["Khoai tây chiên",25000,"🍟"],["Gà rán",35000,"🍗"],
["Hotdog",28000,"🌭"]]},
{name:"Aqua Tea",emoji:"🧋",foods:[
["Trà sữa",29000,"🧋"],["Trà đào",25000,"🍑"],
["Matcha latte",35000,"🍵"],["Soda dâu",22000,"🥤"],
["Trà chanh",18000,"🍋"]]},
{name:"Pizza Galaxy",emoji:"🍕",foods:[
["Pizza phô mai",59000,"🍕"],["Pizza xúc xích",65000,"🍕"],
["Mì Ý bò",49000,"🍝"],["Mì Ý kem",52000,"🍝"]]},
{name:"Sweet Cloud",emoji:"🍰",foods:[
["Bánh kem dâu",35000,"🍰"],["Tiramisu",39000,"🍮"],
["Waffle",32000,"🧇"],["Cupcake",27000,"🧁"]]},
{name:"Snack Ocean",emoji:"🍿",foods:[
["Cá viên chiên",25000,"🍢"],["Phô mai que",30000,"🧀"],
["Tokbokki",39000,"🍲"],["Bánh tráng trộn",25000,"🥗"]]},
{name:"Rice Garden",emoji:"🍚",foods:[
["Cơm gà",35000,"🍗"],["Cơm bò",45000,"🥩"],
["Cơm trứng",29000,"🍳"],["Cơm sườn",42000,"🍖"]]}
];

let selected=null;
let cart={};

const money=n=>n.toLocaleString("vi-VN")+"đ";

function showShops(){
 document.getElementById("restaurants").innerHTML=
 shops.map((s,i)=>`
 <div class="card">
 <div class="art">${s.emoji}</div>
 <h3>${s.name}</h3>
 <p class="small">Đồ ăn ngon • Aqua style</p>
 <button onclick="openMenu(${i})">Xem thực đơn →</button>
 </div>`).join("");
}

function openMenu(i){
 selected=i;
 const shop=shops[i];
 document.getElementById("menuPanel").style.display="block";
 document.getElementById("restaurantName").textContent=
 shop.emoji+" "+shop.name;
 document.getElementById("menu").innerHTML=
 shop.foods.map((f,j)=>`
 <div class="card">
 <div class="art">${f[2]}</div>
 <h3>${f[0]}</h3>
 <p class="price">${money(f[1])}</p>
 <button onclick="add(${i},${j})">＋ Thêm vào giỏ</button>
 </div>`).join("");
 document.getElementById("menuPanel").scrollIntoView({behavior:"smooth"});
}

function closeMenu(){
 document.getElementById("menuPanel").style.display="none";
}

function add(shop,food){
 const f=shops[shop].foods[food];
 const id=shop+"-"+food;
 if(cart[id])cart[id].qty++;
 else cart[id]={shop,food,name:f[0],price:f[1],qty:1};
 render();
}

function change(id,n){
 if(!cart[id])return;
 cart[id].qty+=n;
 if(cart[id].qty<=0)delete cart[id];
 render();
}

function render(){
 const items=Object.entries(cart);
 document.getElementById("cart").innerHTML=
 items.length?items.map(([id,x])=>`
 <div class="row">
 <div><b>${x.name}</b><br><span class="small">${shops[x.shop].name}</span><br>${money(x.price*x.qty)}</div>
 <div>
 <button onclick="change('${id}',-1)">−</button>
 ${x.qty}
 <button onclick="change('${id}',1)">+</button>
 </div>
 </div><hr style="border-color:#b9e9e9">
 `).join(""):"Chưa có món nào!";
 const total=items.reduce((s,[id,x])=>s+x.price*x.qty,0);
 document.getElementById("total").textContent="Tổng: "+money(total);
}

function checkout(){
 const items=Object.values(cart);
 if(!items.length)return alert("Bạn chưa chọn món nào!");
 const name=document.getElementBy