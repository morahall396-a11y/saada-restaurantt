<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>مطعم السعادة - المنيو الاحترافي الكامل</title>
<style>
:root { --primary: #d63031; --accent: #25D366; --dark: #2d3436; }
* { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, sans-serif; }
body { background-color: #f4f6f8; color: #333; padding-bottom: 80px; }

header { background: var(--primary); color: white; padding: 1rem 1.5rem; display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 100; box-shadow: 0 4px 12px rgba(0,0,0,0.1); }
.cart-btn { background: white; color: var(--primary); border: none; padding: 0.6rem 1.2rem; border-radius: 25px; font-weight: bold; cursor: pointer; position: relative; }
.cart-badge { background: #00b894; color: white; border-radius: 50%; padding: 2px 7px; font-size: 0.8rem; margin-right: 5px; }

.container { max-width: 1200px; margin: 1.5rem auto; padding: 0 1rem; }
.controls { display: flex; flex-direction: column; gap: 1rem; margin-bottom: 1.5rem; }
.search-bar { width: 100%; padding: 0.8rem 1rem; border: 1px solid #ccc; border-radius: 8px; font-size: 1rem; outline: none; }
.categories { display: flex; gap: 10px; overflow-x: auto; padding-bottom: 5px; }
.cat-btn { background: white; border: 1px solid #ddd; padding: 0.5rem 1.2rem; border-radius: 20px; cursor: pointer; white-space: nowrap; font-weight: 500; }
.cat-btn.active { background: var(--primary); color: white; border-color: var(--primary); }

.meals-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(220px, 1fr)); gap: 1.2rem; }
.card { background: white; border-radius: 12px; overflow: hidden; box-shadow: 0 4px 12px rgba(0,0,0,0.05); display: flex; flex-direction: column; justify-content: space-between; cursor: pointer; transition: 0.2s; }
.card:hover { transform: translateY(-3px); }
.card img { width: 100%; height: 160px; object-fit: cover; }
.card-body { padding: 0.8rem; text-align: center; flex-grow: 1; display: flex; flex-direction: column; justify-content: space-between; }
.card h3 { font-size: 1rem; margin-bottom: 0.4rem; }
.card .price { color: #00b894; font-weight: bold; font-size: 1.1rem; margin-bottom: 0.8rem; }
.btn-add { background: var(--primary); color: white; border: none; padding: 0.6rem; border-radius: 6px; cursor: pointer; font-weight: bold; width: 100%; }

.modal { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.6); display: none; justify-content: center; align-items: center; z-index: 1000; }
.modal-content { background: white; padding: 1.5rem; border-radius: 12px; width: 95%; max-width: 480px; max-height: 90vh; overflow-y: auto; text-align: center; }
.cart-item { display: flex; justify-content: space-between; align-items: center; margin-bottom: 0.8rem; border-bottom: 1px solid #eee; padding-bottom: 0.5rem; }
.qty-btn { background: #eee; border: none; width: 26px; height: 26px; border-radius: 4px; cursor: pointer; font-weight: bold; }
.form-group { margin-top: 0.8rem; text-align: right; }
.form-group label { display: block; font-size: 0.85rem; font-weight: bold; margin-bottom: 0.3rem; }
.form-group input, .form-group textarea { width: 100%; padding: 0.6rem; border: 1px solid #ccc; border-radius: 6px; }
.btn-send { background: var(--accent); color: white; border: none; padding: 0.8rem; border-radius: 6px; font-weight: bold; cursor: pointer; width: 100%; margin-top: 1rem; font-size: 1rem; }

.admin-btn { position: fixed; bottom: 20px; left: 20px; background: var(--dark); color: white; padding: 12px; border-radius: 50%; cursor: pointer; border: none; font-size: 18px; z-index: 99; }
</style>
</head>
<body>

<header>
<h1>مطعم السعادة 🍔</h1>
<button class="cart-btn" onclick="openCart()">
السلة 🛒 <span class="cart-badge" id="cart-count">0</span>
</button>
</header>

<div class="container">
<div class="controls">
<input type="text" id="search-input" class="search-bar" placeholder="ابحث عن وجبتك المفضلة..." oninput="filterMeals()">
<div class="categories" id="categories-container"></div>
</div>

<div class="meals-grid" id="meals-container"></div>
</div>

<!-- زر لوحة التحكم -->
<button onclick="adminLogin()" class="admin-btn" title="لوحة الإدارة">⚙️</button>

<!-- مودال التفاصيل -->
<div id="details-modal" class="modal">
<div class="modal-content" id="details-body"></div>
</div>

<!-- مودال السلة -->
<div class="modal" id="cart-modal">
<div class="modal-content">
<h2>سلة المشتريات 🛒</h2>
<div id="cart-items" style="margin: 1rem 0;"></div>
<h3 style="text-align: left; margin-bottom: 1rem;">الإجمالي: <span id="cart-total" style="color:#00b894;">0</span> ج.م</h3>
<hr>
<div class="form-group">
<label>الاسم بالكامل *</label>
<input type="text" id="cust-name" placeholder="أدخل اسمك">
</div>
<div class="form-group">
<label>العنوان التفصيلي *</label>
<input type="text" id="cust-address" placeholder="الشارع / رقم العمارة / الشقة">
</div>
<div class="form-group">
<label>ملاحظات إضافية (اختياري)</label>
<textarea id="cust-notes" rows="2" placeholder="بدون بصل / صوص زيادة..."></textarea>
</div>
<button class="btn-send" onclick="sendOrder()">إرسال الطلب عبر الواتساب 💬</button>
<button onclick="closeModal('cart-modal')" style="background:#888; color:white; border:none; padding:0.6rem; border-radius:6px; cursor:pointer; width:100%; margin-top:0.5rem;">إغلاق</button>
</div>
</div>

<!-- مودال الإدارة -->
<div id="admin-modal" class="modal">
<div class="modal-content">
<h2>لوحة الإدارة والإحصائيات 📊</h2>
<div id="admin-actions" style="margin-top:15px;"></div>
<button onclick="closeModal('admin-modal')" style="background:#888; color:white; border:none; padding:0.6rem; border-radius:6px; cursor:pointer; width:100%; margin-top:1rem;">إغلاق</button>
</div>
</div>

<script>
const myPhoneNumber = "201095075717";

if(!localStorage.getItem('pass_v2')) {
localStorage.removeItem('my_menu');
localStorage.setItem('pass_v2', 'true');
}

const defaultMeals = [
{ id: 1, name: "برجر كلاسيك", category: "برجر", price: 120, img: "https://images.unsplash.com/photo-1568901346375-23c9450c58cd?w=400", desc: "قطعة لحم بقري صافي، خس طازج، طماطم، خيار مخلل، صوص كلاسيك.", salesCount: 0 },
{ id: 2, name: "تشيز برجر دبل", category: "برجر", price: 150, img: "https://images.unsplash.com/photo-1550547660-d9450f859349?w=400", desc: "قطعتين لحم بقري، شريحتين جبنة شيدر أصلية، صوص الشيدر الذائب.", salesCount: 0 },
{ id: 3, name: "برجر مشروم سوبريم", category: "برجر", price: 140, img: "https://images.unsplash.com/photo-1586190848861-99aa4a171e90?w=400", desc: "لحم بقري، مشروم طازج مشوي، جبنة سويسرية، صوص المشروم الغني.", salesCount: 0 },
{ id: 4, name: "برجر بيض وجبنة", category: "برجر", price: 110, img: "https://images.unsplash.com/photo-1568901346375-23c9450c58cd?w=400", desc: "شريحة لحم مشوية، بيض مقلي، جبنة شيدر، خس، صوص المايونيز.", salesCount: 0 },
{ id: 5, name: "برجر بالباربيكيو", category: "برجر", price: 125, img: "https://images.unsplash.com/photo-1568901346375-23c9450c58cd?w=400", desc: "لحم بقري، حلقات بصل مقرمشة، جبنة شيدر، صوص الباربيكيو المدخن.", salesCount: 0 },
{ id: 6, name: "برجر سموكد بيكون", category: "برجر", price: 160, img: "https://images.unsplash.com/photo-1550547660-d9450f859349?w=400", desc: "لحم بقري، شريحة بيكون مدخن، جبنة شيدر، صوص مميز.", salesCount: 0 },

{ id: 7, name: "بيتزا ببروني", category: "بيتزا", price: 160, img: "https://images.unsplash.com/photo-1628840042765-356cda07504e?w=400", desc: "شرائح ببروني بقر، جبنة موتزاريلّا، صوص طماطم إيطالي، أوريجانو.", salesCount: 0 },
{ id: 8, name: "بيتزا خضار", category: "بيتزا", price: 130, img: "https://images.unsplash.com/photo-1513104890138-7c749659a591?w=400", desc: "فلفل ألوان، زيتون أسود، مشروم، بصل، طماطم، جبنة موتزاريلّا.", salesCount: 0 },
{ id: 9, name: "بيتزا مارجريتا", category: "بيتزا", price: 120, img: "https://images.unsplash.com/photo-1604382354936-07c5d9983bd3?w=400", desc: "صلصة طماطم أصلية، جبنة موتزاريلّا غنية، أوراق ريحان طازجة.", salesCount: 0 },
{ id: 10, name: "بيتزا سي فود", category: "بيتزا", price: 180, img: "https://images.unsplash.com/photo-1628840042765-356cda07504e?w=400", desc: "جمبري، كاليماري، فلفل ألوان، ثوم، جبنة موتزاريلّا وصوص خاص.", salesCount: 0 },
{ id: 11, name: "بيتزا مشكل جبن", category: "بيتزا", price: 150, img: "https://images.unsplash.com/photo-1513104890138-7c749659a591?w=400", desc: "مزيج من الموتزاريلّا، الشيدر، الجبنة الرومي، والجبنة الاستراكيولا.", salesCount: 0 },
{ id: 12, name: "بيتزا BBQ دجاج", category: "بيتزا", price: 165, img: "https://images.unsplash.com/photo-1604382354936-07c5d9983bd3?w=400", desc: "قطع دجاج مشوي، بصل أحمر، صوص باربيكيو، جبنة موتزاريلّا.", salesCount: 0 },
{ id: 13, name: "بيتزا تونة", category: "بيتزا", price: 140, img: "https://images.unsplash.com/photo-1513104890138-7c749659a591?w=400", desc: "قطعة تونة فاخرة، بصل، زيتون، فلفل ألوان، جبنة موتزاريلّا.", salesCount: 0 },

{ id: 14, name: "وجبة دجاج مقرمش", category: "وجبات دجاج", price: 140, img: "https://images.unsplash.com/photo-1626645738196-c2a7c87a8f58?w=400", desc: "3 قطع دجاج بروستد مقرمش، بطاطس، ثومية، خبز، كلوسلو.", salesCount: 0 },
{ id: 15, name: "فتة شاورما دجاج", category: "وجبات دجاج", price: 110, img: "https://images.unsplash.com/photo-1561651823-34feb02250e4?w=400", desc: "أرز بسمتي، شاورما دجاج، خبز محمص، صوص الثومية الشهير.", salesCount: 0 },
{ id: 16, name: "شاورما لحم عربي", category: "وجبات دجاج", price: 120, img: "https://images.unsplash.com/photo-1529006557810-274b9b2fc783?w=400", desc: "قطع شاورما لحم بخبز الصاج، مقطعة مع بطاطس وطحينة ومخلل.", salesCount: 0 },
{ id: 17, name: "ساندوتش زنجر سبايسي", category: "وجبات دجاج", price: 85, img: "https://images.unsplash.com/photo-1528735602780-2552fd46c7af?w=400", desc: "صدر دجاج حار مقرمش، خس، مايونيز سبايسي، شيدر.", salesCount: 0 },
{ id: 18, name: "وجبة استريبس 5 قطع", category: "وجبات دجاج", price: 130, img: "https://images.unsplash.com/photo-1562967914-608f82629710?w=400", desc: "5 قطع استريبس مقرمشة، بطاطس، صوص شيدر، خبز.", salesCount: 0 },
{ id: 19, name: "فتة شاورما لحم", category: "وجبات دجاج", price: 130, img: "https://images.unsplash.com/photo-1561651823-34feb02250e4?w=400", desc: "أرز بسمتي، شاورما بلدي، خبز محمص، صوص طحينة وزبادي.", salesCount: 0 },
{ id: 20, name: "وجبة شيش طاووق", category: "وجبات دجاج", price: 135, img: "https://images.unsplash.com/photo-1569058242253-92a9c755a0ec?w=400", desc: "أسيخ دجاج متبل مشوي على الفحم، أرز، خضار مشوي، ثومية.", salesCount: 0 },
{ id: 21, name: "وجبة دجاج مسحب", category: "وجبات دجاج", price: 145, img: "https://images.unsplash.com/photo-1626645738196-c2a7c87a8f58?w=400", desc: "صدور دجاج مخلية متبلة ومشوية، بطاطس، ثومية، خبز.", salesCount: 0 },
{ id: 22, name: "وجبة ريزو دجاج", category: "وجبات دجاج", price: 85, img: "https://images.unsplash.com/photo-1569058242253-92a9c755a0ec?w=400", desc: "أرز ريزو خاص، قطع دجاج مقرمشة، صوص باربيكيو أو حار.", salesCount: 0 },

{ id: 23, name: "كريب دجاج زنجر", category: "كريب", price: 95, img: "https://images.unsplash.com/photo-1565299585323-38d6b0865b47?w=400", desc: "قطع زنجر حار، موتزاريلّا، رومي، فلفل، كاتشب ومايونيز.", salesCount: 0 },
{ id: 24, name: "كريب سوسيس", category: "كريب", price: 80, img: "https://images.unsplash.com/photo-1519671482749-fd09be7ccebf?w=400", desc: "شرائح سوسيس، جبنة موتزاريلّا، زيتون، فلفل ألوان وصوصات.", salesCount: 0 },
{ id: 25, name: "كريب شاورما دجاج", category: "كريب", price: 90, img: "https://images.unsplash.com/photo-1565299585323-38d6b0865b47?w=400", desc: "شاورما دجاج، موتزاريلّا، ثومية، خيار مخلل.", salesCount: 0 },
{ id: 26, name: "كريب مكس أجبان", category: "كريب", price: 85, img: "https://images.unsplash.com/photo-1519671482749-fd09be7ccebf?w=400", desc: "موتزاريلّا، شيدر، رومي، كيري، فلفل وزيتون.", salesCount: 0 },
{ id: 27, name: "كريب زنجر دبل", category: "كريب", price: 110, img: "https://images.unsplash.com/photo-1565299585323-38d6b0865b47?w=400", desc: "ضعف كمية الزنجر، جبن مشكل، صوص شيدر.", salesCount: 0 },
{ id: 28, name: "كريب مشكل لحوم", category: "كريب", price: 105, img: "https://images.unsplash.com/photo-1519671482749-fd09be7ccebf?w=400", desc: "سجق، سوسيس، لحم مفروم، موتزاريلّا وصوصات.", salesCount: 0 },

{ id: 29, name: "حواوشي مخصوص", category: "شرقي وباستا", price: 85, img: "https://images.unsplash.com/photo-1544025162-d76694265947?w=400", desc: "لحم بلدي متبل بالبهارات الشرقية في خبز بلدي محمص.", salesCount: 0 },
{ id: 30, name: "حواوشي جبنة", category: "شرقي وباستا", price: 90, img: "https://images.unsplash.com/photo-1544025162-d76694265947?w=400", desc: "حواوشي لحم بلدي مع طبقة غنية من الجبنة الموتزاريلّا.", salesCount: 0 },
{ id: 31, name: "حواوشي سجق", category: "شرقي وباستا", price: 95, img: "https://images.unsplash.com/photo-1544025162-d76694265947?w=400", desc: "سجق شرقي متبل، فلفل حار، خلطة حواوشي خاصة.", salesCount: 0 },
{ id: 32, name: "باستا وايت صوص", category: "شرقي وباستا", price: 100, img: "https://images.unsplash.com/photo-1551183053-bf91a1d81141?w=400", desc: "مكرونة بيني، صوص كريمة أبيض، مشروم، جبنة بارميزان.", salesCount: 0 },
{ id: 33, name: "باستا ريد صوص دجاج", category: "شرقي وباستا", price: 110, img: "https://images.unsplash.com/photo-1621996346565-e3d5d6281322?w=400", desc: "مكرونة بصلصة الطماطم الإيطالية، قطع دجاج مشوية.", salesCount: 0 },
{ id: 34, name: "ساندوتش كبده اسكندراني", category: "شرقي وباستا", price: 60, img: "https://images.unsplash.com/photo-1553909489-cd47e0907980?w=400", desc: "كبدة بلدي بالفلفل الحار والتوم والليمون بخبز فينوا.", salesCount: 0 },
{ id: 35, name: "ساندوتش سجق شرقي", category: "شرقي وباستا", price: 65, img: "https://images.unsplash.com/photo-1528735602780-2552fd46c7af?w=400", desc: "سجق شرقي بصلصة الطماطم والفلفل الألوان.", salesCount: 0 },
{ id: 36, name: "حواوشي مخصوص دبل", category: "شرقي وباستا", price: 115, img: "https://images.unsplash.com/photo-1544025162-d76694265947?w=400", desc: "رغيف مضاعف الحجم واللحم البلدي مع البهارات.", salesCount: 0 },

{ id: 37, name: "بطاطس مقلية", category: "مقبلات وحلويات", price: 40, img: "https://images.unsplash.com/photo-1573080496219-bb080dd4f877?w=400", desc: "أصابع بطاطس ذهبية مقرمشة مع بهارات التوابل.", salesCount: 0 },
{ id: 38, name: "كريب شيكولاتة", category: "مقبلات وحلويات", price: 70, img: "https://images.unsplash.com/photo-1519671482749-fd09be7ccebf?w=400", desc: "كريب محشو بشيكولاتة نوتيلا وموز أو مكسرات.", salesCount: 0 },
{ id: 39, name: "وافل نوتيلا", category: "مقبلات وحلويات", price: 65, img: "https://images.unsplash.com/photo-1562376552-0d160a2f238d?w=400", desc: "وافل ذهبي هش مغطى بشيكولاتة نوتيلا وسكر ناعم.", salesCount: 0 },
{ id: 40, name: "بطاطس فارم فريتس", category: "مقبلات وحلويات", price: 50, img: "https://images.unsplash.com/photo-1630384060421-cb20d0e0649d?w=400", desc: "بطاطس مغطاة بصوص الجبنة الشيدر الدافئ.", salesCount: 0 }
];

let meals = JSON.parse(localStorage.getItem('my_menu')) || defaultMeals;
let cart = [];
let currentCategory = "الكل";

function initCategories() {
const cats = ["الكل", ...new Set(meals.map(m => m.category))];
document.getElementById('categories-container').innerHTML = cats.map(c => 
'<button class="cat-btn ' + (c === 'الكل' ? 'active' : '') + '" onclick="setCategory(\'' + c + '\', this)">' + c + '</button>'
).join('');
}

function setCategory(cat, btn) {
currentCategory = cat;
document.querySelectorAll('.cat-btn').forEach(b => b.classList.remove('active'));
btn.classList.add('active');
filterMeals();
}

function filterMeals() {
const query = document.getElementById('search-input').value.toLowerCase();
const filtered = meals.filter(m => {
const matchesCat = currentCategory === "الكل" || m.category === currentCategory;
const matchesQuery = m.name.toLowerCase().includes(query);
return matchesCat && matchesQuery;
});
renderMeals(filtered);
}

function renderMeals(list) {
document.getElementById('meals-container').innerHTML = list.map(m => 
'<div class="card" onclick="showDetails(' + m.id + ')">' +
'<img src="' + m.img + '" alt="' + m.name + '">' +
'<div class="card-body">' +
'<h3>' + m.name + '</h3>' +
'<div class="price">' + m.price + ' ج.م</div>' +
'<button class="btn-add" onclick="event.stopPropagation(); addToCart(' + m.id + ')">إضافة للسلة 🛒</button>' +
'</div></div>'
).join('');
}

function showDetails(id) {
const meal = meals.find(m => m.id === id);
document.getElementById('details-body').innerHTML = 
'<h2>' + meal.name + '</h2>' +
'<img src="' + meal.img + '" style="width:100%; height:200px; object-fit:cover; border-radius:8px; margin:10px 0;">' +
'<p style="margin-bottom:10px;">' + (meal.desc || 'لا يوجد وصف متاح') + '</p>' +
'<p style="text-align:right; color:#00b894; font-weight:bold; font-size:1.2rem;">السعر: ' + meal.price + ' ج.م</p>' +
'<button onclick="addToCart(' + meal.id + '); closeModal(\'details-modal\');" class="btn-add" style="margin-top:15px;">إضافة للسلة 🛒</button>' +
'<button onclick="closeModal(\'details-modal\')" style="background:#888; color:white; border:none; padding:0.6rem; border-radius:6px; cursor:pointer; width:100%; margin-top:0.5rem;">إغلاق</button>';
document.getElementById('details-modal').style.display = 'flex';
}

function addToCart(id) {
const item = cart.find(i => i.id === id);
if(item) item.qty++;
else {
const meal = meals.find(m => m.id === id);
cart.push({ ...meal, qty: 1 });
}
updateCartUI();
}

function updateCartQty(id, delta) {
const item = cart.find(i => i.id === id);
if(item) {
item.qty += delta;
if(item.qty <= 0) cart = cart.filter(i => i.id !== id);
}
updateCartUI();
}

function updateCartUI() {
const totalCount = cart.reduce((sum, item) => sum + item.qty, 0);
const totalPrice = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);

document.getElementById('cart-count').innerText = totalCount;
document.getElementById('cart-total').innerText = totalPrice;

document.getElementById('cart-items').innerHTML = cart.length === 0 ? '<p>السلة فارغة</p>' : cart.map(i => 
'<div class="cart-item">' +
'<div><strong>' + i.name + '</strong><br><small>' + i.price + ' x ' + i.qty + ' = ' + (i.price * i.qty) + ' ج.م</small></div>' +
'<div><button class="qty-btn" onclick="updateCartQty(' + i.id + ', -1)">-</button><span style="margin:0 5px;">' + i.qty + '</span><button class="qty-btn" onclick="updateCartQty(' + i.id + ', 1)">+</button></div>' +
'</div>'
).join('');
}

function openCart() { document.getElementById('cart-modal').style.display = 'flex'; }
function closeModal(id) { document.getElementById(id).style.display = 'none'; }

function sendOrder() {
const name = document.getElementById('cust-name').value;
const address = document.getElementById('cust-address').value;
const notes = document.getElementById('cust-notes').value;

if(cart.length === 0) return alert("السلة فارغة!");
if(!name || !address) return alert("يرجى كتابة الاسم والعنوان الكامل");

cart.forEach(item => {
const meal = meals.find(m => m.id === item.id);
if (meal) {
meal.salesCount = (meal.salesCount || 0) + item.qty;
}
});
localStorage.setItem('my_menu', JSON.stringify(meals));

let itemsText = cart.map(i => '• ' + i.name + ' (عدد ' + i.qty + ') = ' + (i.price * i.qty) + ' ج.م').join('\n');
let totalPrice = cart.reduce((sum, i) => sum + (i.price * i.qty), 0);

let msg = 'طلب جديد من الموقع 🍔\n\nالعميل: ' + name + '\nالعنوان: ' + address + '\nالملاحظات: ' + (notes || 'لا يوجد') + '\n\nالطلبات:\n' + itemsText + '\n\nالإجمالي الكلي: ' + totalPrice + ' ج.م';

window.open('https://wa.me/' + myPhoneNumber + '?text=' + encodeURIComponent(msg), '_blank');
cart = [];
updateCartUI();
closeModal('cart-modal');
}

function adminLogin() {
const pass = prompt("أدخل كلمة المرور:");
if (!pass) return;

if (pass.trim() === "54321") {
showAdminPanel();
} else {
alert("كلمة مرور خاطئة!");
}
}

function showAdminPanel() {
document.getElementById('admin-modal').style.display = 'flex';
let html = '<div style="text-align:right; max-height:350px; overflow-y:auto; padding-left:5px;">';

meals.forEach(m => {
html += '<div style="border-bottom:1px solid #eee; padding:8px 0; display:flex; justify-content:space-between; align-items:center;">' +
'<div><strong>' + m.name + '</strong><br><small style="color:#00b894;">تم بيعها: ' + (m.salesCount || 0) + ' مرة | السعر: ' + m.price + ' ج.م</small></div>' +
'<button onclick="deleteMeal(' + m.id + ')" style="background:#e74c3c; color:white; border:none; padding:5px 10px; border-radius:4px; cursor:pointer;">حذف</button></div>';
});

html += '</div><hr style="margin:10px 0;"><button onclick="addMeal()" style="width:100%; padding:10px; background:var(--primary); color:white; border:none; border-radius:6px; font-weight:bold; cursor:pointer;">إضافة وجبة جديدة ➕</button>';
document.getElementById('admin-actions').innerHTML = html;
}

function deleteMeal(id) {
if(confirm("هل أنت تأكد من حذف هذه الوجبة؟")) {
meals = meals.filter(m => m.id !== id);
localStorage.setItem('my_menu', JSON.stringify(meals));
renderMeals(meals);
showAdminPanel();
}
}

function addMeal() {
const name = prompt("اسم الوجبة:");
const price = prompt("السعر (بالجنيه):");
const desc = prompt("مكونات/وصف الوجبة:");
const img = prompt("رابط الصورة (URL):") || "https://images.unsplash.com/photo-1568901346375-23c9450c58cd?w=400";

if(name && price) {
meals.push({
id: Date.now(),
name: name,
category: "عام",
price: parseFloat(price),
img: img,
desc: desc || "",
salesCount: 0
});
localStorage.setItem('my_menu', JSON.stringify(meals));
renderMeals(meals);
showAdminPanel();
}
}

initCategories();
renderMeals(meals);
</script>
</body>
</html>
