<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Admin Food Menu</title>
  <style>
    body { font-family: 'Segoe UI', Arial, sans-serif; margin:0; padding:20px; background: linear-gradient(135deg,#f6f9fc,#e9eef5);}
    h1 { text-align:center; margin-bottom:30px; font-size:2.5rem;}
    .products { max-width:500px; margin:auto;}
    .products h2 { background:white; padding:16px; border-radius:15px; box-shadow:0 10px 25px rgba(0,0,0,0.08); margin-bottom:16px; text-align:center;}
    .product { background:#fff; display:flex; justify-content:space-between; align-items:center; padding:14px 12px; margin-bottom:12px; border-radius:12px; box-shadow:0 6px 14px rgba(0,0,0,0.08);}
    button { background:#4f46e5; color:white; border:none; padding:8px 14px; border-radius:8px; cursor:pointer; font-size:0.9rem; margin-left:5px;}
    button:hover { background:#4338ca;}
    .add-item { display:flex; justify-content:center; margin-bottom:20px;}
    input[type="text"], input[type="number"] { padding:4px 6px; margin-right:5px; border-radius:5px; border:1px solid #ccc; width:120px;}
    span[contenteditable] { outline:1px dashed #aaa; padding:2px 4px; border-radius:4px;}
    #orders { max-width:500px; margin:20px auto; background:white; padding:16px; border-radius:12px; box-shadow:0 6px 14px rgba(0,0,0,0.08); }
    #orders h2 { margin-top:0; text-align:center; }
    #orders ul { list-style:none; padding-left:0; }
    #orders li { padding:6px 0; border-bottom:1px solid #eee; }
    #total { font-weight:bold; margin-top:10px; text-align:right; }
  </style>
</head>
<body>
  <h1>Admin Food Menu</h1>

  <div class="add-item">
    <input type="text" id="newName" placeholder="Food Name" />
    <input type="number" id="newPrice" placeholder="Price" step="0.01" />
    <button onclick="addNewItem()">Add Food</button>
  </div>

  <div class="products" id="menu">
    <h2>Menu</h2>
  </div>

  <div id="orders">
    <h2>Orders</h2>
    <p>No orders yet.</p>
    <div id="total">Total: $0.00</div>
  </div>

  <script>
    const menu = document.getElementById('menu');
    const ordersDiv = document.getElementById('orders');
    const totalDiv = document.getElementById('total');

    let items = [
      { name: '💍 Ring Pop', price: 0.50 },
      { name: '🔥 Takis', price: 1.00 },
      { name: '🍬 Gum (single)', price: 0.25 },
      { name: '📦 Gum (full pack)', price: 3.00 },
      { name: '🌈 Skittles', price: 2.00 },
      { name: '💥 Airheads Xtremes', price: 2.00 }
    ];

    const WEB_APP_URL = "https://script.google.com/macros/s/AKfycbx1cnqDi-SjXpA4FnNSb5-VerMQcHS-j2XV9DSpoh9sQWzfLmIhxm9T2OXAy-_Th3B6/exec";

    function renderMenu() {
      menu.innerHTML = '<h2>Menu</h2>';
      items.forEach((item, index) => {
        const div = document.createElement('div');
        div.className = 'product';
        div.innerHTML = `<span contenteditable='true' oninput='updateItemName(${index}, this.textContent)'>${item.name}</span> - $<span contenteditable='true' oninput='updateItemPrice(${index}, this.textContent)'>${item.price.toFixed(2)}</span>` +
                        `<div>` +
                        `<label style="font-size:0.8rem; margin-right:8px;"><input type='checkbox' onchange='toggleSoldOut(this)'> Sold Out</label>` +
                        `<button onclick='addToCart(items[${index}].name, items[${index}].price)'>Add</button>` +
                        `<button onclick='removeItem(${index})'>Remove</button>` +
                        `</div>`;
        menu.appendChild(div);
      });
    }

    function updateItemName(index, newName) { items[index].name = newName; }
    function updateItemPrice(index, newPrice) { const price = parseFloat(newPrice); if(!isNaN(price)) items[index].price = price; }
    function addNewItem() { 
      const name = document.getElementById('newName').value; 
      const price = parseFloat(document.getElementById('newPrice').value); 
      if(name && !isNaN(price)){ items.push({name, price}); document.getElementById('newName').value=''; document.getElementById('newPrice').value=''; renderMenu(); } 
      else alert('Enter valid name and price'); 
    }
    function removeItem(index){ items.splice(index,1); renderMenu(); }
    function toggleSoldOut(checkbox){ const button = checkbox.parentElement.nextElementSibling; button.disabled=checkbox.checked; button.style.opacity=checkbox.checked?'0.5':'1'; }
    function addToCart(name, price){ alert(name + ' added to cart!'); }

    function fetchOrders() {
      fetch(WEB_APP_URL)
        .then(res => res.json())
        .then(data => {
          let html = '<h2>Orders</h2>';
          if(data.length === 0){
            html += '<p>No orders yet.</p>';
            totalDiv.textContent = 'Total: $0.00';
          } else {
            let total = 0;
            html += '<ul>';
            data.forEach(order => {
              html += `<li>${order.item} - $${parseFloat(order.price).toFixed(2)} | ${order.name} | ${order.team} | ${order.class} | ${order.grade}</li>`;
              total += parseFloat(order.price);
            });
            html += '</ul>';
            totalDiv.textContent = `Total: $${total.toFixed(2)}`;
          }
          ordersDiv.innerHTML = html + totalDiv.outerHTML;
        })
        .catch(err => console.log(err));
    }

    setInterval(fetchOrders, 5000);
    renderMenu();
    fetchOrders();
  </script>
</body>
</html>
