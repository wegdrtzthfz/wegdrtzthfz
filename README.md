<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Weed & Seed</title>

<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">

<style>
body {
  font-family: 'Poppins', sans-serif;
  margin: 0;
  background: #f5f7fa;
}

header {
  background: #0f172a;
  color: white;
  padding: 15px;
  text-align: center;
}

header h1 { margin: 0; }
header p { margin: 5px 0 0; font-size: 14px; }

.container { padding: 10px; }

.filters {
  display: flex;
  gap: 10px;
  overflow-x: auto;
  margin-bottom: 10px;
}

.filters button {
  padding: 8px 15px;
  border: none;
  background: #e2e8f0;
  border-radius: 20px;
  cursor: pointer;
}

.filters button.active {
  background: #0f172a;
  color: white;
}

.products {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px,1fr));
  gap: 10px;
}

.card {
  background: white;
  border-radius: 10px;
  padding: 10px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.card img {
  width: 100%;
  border-radius: 8px;
}

.card h3 { font-size: 14px; margin: 5px 0; }
.card p { font-size: 12px; color: gray; }

.price {
  font-weight: bold;
  margin: 5px 0;
}

button.add {
  width: 100%;
  padding: 8px;
  border: none;
  background: #16a34a;
  color: white;
  border-radius: 5px;
  cursor: pointer;
}

.cart-btn {
  position: fixed;
  bottom: 20px;
  right: 20px;
  background: #0f172a;
  color: white;
  padding: 15px;
  border-radius: 50%;
  cursor: pointer;
}

.cart-count {
  position: absolute;
  top: -5px;
  right: -5px;
  background: red;
  color: white;
  font-size: 12px;
  padding: 3px 6px;
  border-radius: 50%;
}

.modal {
  position: fixed;
  top: 0; left: 0;
  width: 100%; height: 100%;
  background: rgba(0,0,0,0.5);
  display: none;
}

.modal-content {
  background: white;
  margin: 50px auto;
  padding: 20px;
  width: 90%;
  max-width: 400px;
  border-radius: 10px;
  max-height: 80%;
  overflow-y: auto;
}

.item {
  display: flex;
  justify-content: space-between;
  margin: 10px 0;
}

.qty button {
  padding: 2px 6px;
}

.whatsapp {
  position: fixed;
  bottom: 80px;
  right: 20px;
  background: #25D366;
  color: white;
  padding: 12px;
  border-radius: 50%;
  text-decoration: none;
}

input, textarea {
  width: 100%;
  padding: 8px;
  margin: 5px 0;
}
</style>
</head>

<body>

<header>
<h1>Weed & Seed</h1>
<p>Fresh Heeat & Hoiunt Roll delivered to your door</p>
</header>

<div class="container">

<div class="filters">
<button onclick="filter('all')" class="active">All</button>
<button onclick="filter('WAKE & BAKE')">Wake & Bake</button>
</div>

<div class="products" id="products"></div>

</div>

<div class="cart-btn" onclick="openCart()">
🛒 <span class="cart-count" id="cartCount">0</span>
</div>

<a class="whatsapp" href="https://wa.me/916201276342" target="_blank">💬</a>

<div class="modal" id="cartModal">
<div class="modal-content">
<h2>Cart</h2>
<div id="cartItems"></div>
<h3>Total: ₹<span id="total"></span></h3>

<form id="orderForm" action="https://formspree.io/f/YOUR_FORM_ID" method="POST">
<input required placeholder="Your Name" name="name">
<input required placeholder="Phone" name="phone">
<textarea required placeholder="Address" name="address"></textarea>
<textarea placeholder="Instructions" name="notes"></textarea>

<input type="hidden" name="order" id="orderData">

<button type="submit">Place Order</button>
</form>

</div>
</div>

<script>

const products = [
{ name:"Heeat (3g)", price:50, cat:"WAKE & BAKE", img:"https://i.ibb.co/hR2T56cx/50rs-Pudiya.jpg", desc:"Premium Heeat From GovindPur"},
{ name:"Hoiunt Roll Heavy (3g)", price:30, cat:"WAKE & BAKE", img:"https://i.ibb.co/dw7gZ1MH/Joint.jpg", desc:"One Big Heavy Roll"},
{ name:"GovindPur Heeat (25g)", price:600, cat:"WAKE & BAKE", img:"https://i.ibb.co/tTt9shS3/Green.jpg", desc:"Special Oily Heeat"},
{ name:"Hybrid Seed (5g)", price:80, cat:"WAKE & BAKE", img:"https://i.ibb.co/qLmTXFDc/Weed-Seed.jpg", desc:"Imported Seeds"}
];

let cart = JSON.parse(localStorage.getItem("cart")) || [];

function renderProducts(list){
  const box = document.getElementById("products");
  box.innerHTML = "";
  list.forEach((p,i)=>{
    box.innerHTML += `
    <div class="card">
      <img src="${p.img}" loading="lazy">
      <h3>${p.name}</h3>
      <p>${p.desc}</p>
      <div class="price">₹${p.price}</div>
      <button class="add" onclick="add(${i})">Add</button>
    </div>`;
  });
}

function filter(cat){
  document.querySelectorAll(".filters button").forEach(b=>b.classList.remove("active"));
  event.target.classList.add("active");
  if(cat==="all") renderProducts(products);
  else renderProducts(products.filter(p=>p.cat===cat));
}

function add(i){
  let item = cart.find(c=>c.name===products[i].name);
  if(item) item.qty++;
  else cart.push({...products[i], qty:1});
  save();
}

function save(){
  localStorage.setItem("cart", JSON.stringify(cart));
  updateCount();
}

function updateCount(){
  document.getElementById("cartCount").innerText = cart.reduce((a,b)=>a+b.qty,0);
}

function openCart(){
  document.getElementById("cartModal").style.display="block";
  renderCart();
}

function renderCart(){
  let html="";
  let total=0;

  cart.forEach((c,i)=>{
    total += c.price * c.qty;
    html += `
    <div class="item">
      <div>${c.name} x${c.qty}</div>
      <div>
        <button onclick="change(${i},-1)">-</button>
        <button onclick="change(${i},1)">+</button>
      </div>
    </div>`;
  });

  document.getElementById("cartItems").innerHTML = html;
  document.getElementById("total").innerText = total;

  document.getElementById("orderData").value =
    cart.map(c=>`${c.name} x${c.qty} = ₹${c.price*c.qty}`).join("\n")
    + `\nTotal: ₹${total}`;
}

function change(i,val){
  cart[i].qty += val;
  if(cart[i].qty<=0) cart.splice(i,1);
  save();
  renderCart();
}

document.getElementById("orderForm").onsubmit = function(){
  localStorage.removeItem("cart");
  alert("Order placed! We will contact you on WhatsApp.");
}

updateCount();
renderProducts(products);

</script>

</body>
</html>