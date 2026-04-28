<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=yes, viewport-fit=cover">
  <title>StandSalmis</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    :root {
      --glass: rgba(20, 20, 35, 0.7);
      --glass-border: rgba(255, 215, 0, 0.25);
      --blur: blur(18px);
      --accent: #fbbf24;
      --accent2: #f59e0b;
      --bg-deep: #0b0b1a;
      --text-light: #f1f5f9;
      --danger: #ef4444;
      --success: #10b981;
      --shadow-gold: 0 10px 30px -10px rgba(251, 191, 36, 0.3);
      --radius-xl: 28px;
      --radius-lg: 20px;
      --radius-md: 14px;
      --font: 'Inter', system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
    }

    body {
      background: radial-gradient(circle at 10% 20%, #1e1b4b, #0b0b1a);
      color: var(--text-light);
      font-family: var(--font);
      min-height: 100vh;
      padding: 16px;
      padding-top: 0;
      letter-spacing: 0.2px;
    }

    @keyframes fadeSlide {
      0% { opacity: 0; transform: translateY(24px); }
      100% { opacity: 1; transform: translateY(0); }
    }
    @keyframes pulseGlow {
      0% { box-shadow: 0 0 8px var(--accent); }
      50% { box-shadow: 0 0 22px var(--accent2); }
      100% { box-shadow: 0 0 8px var(--accent); }
    }
    .fade-in {
      animation: fadeSlide 0.4s ease;
    }

    .glass {
      background: var(--glass);
      backdrop-filter: var(--blur);
      -webkit-backdrop-filter: var(--blur);
      border: 1px solid var(--glass-border);
      border-radius: var(--radius-lg);
      box-shadow: 0 20px 35px rgba(0,0,0,0.5);
      padding: 20px;
    }

    .gold-btn {
      background: linear-gradient(145deg, #2d2a4a, #1a1a30);
      border: 1px solid rgba(251, 191, 36, 0.4);
      color: #ffdd7a;
      font-weight: 600;
      padding: 12px 20px;
      border-radius: 40px;
      cursor: pointer;
      transition: all 0.25s;
      backdrop-filter: blur(8px);
      display: inline-flex;
      align-items: center;
      justify-content: center;
      gap: 6px;
    }
    .gold-btn:hover {
      background: #fbbf24;
      color: #0b0b1a;
      border-color: #fbbf24;
      box-shadow: 0 0 18px #fbbf24;
      transform: scale(1.02);
    }

    .icon-btn {
      background: rgba(255,255,255,0.06);
      border: 1px solid rgba(255,255,255,0.2);
      border-radius: 40px;
      padding: 10px 18px;
      color: white;
      font-weight: 500;
      cursor: pointer;
      transition: 0.2s;
    }
    .icon-btn:hover {
      background: rgba(255,215,0,0.15);
      border-color: var(--accent);
    }

    .danger-btn {
      background: rgba(239,68,68,0.15);
      border-color: rgba(239,68,68,0.5);
      color: #fecaca;
    }
    .danger-btn:hover {
      background: #ef4444;
      color: white;
    }

    input, textarea {
      background: rgba(0,0,0,0.5);
      border: 1px solid rgba(255,215,0,0.3);
      border-radius: 40px;
      padding: 14px 18px;
      color: white;
      width: 100%;
      margin: 8px 0;
      outline: none;
    }
    input:focus {
      border-color: #fbbf24;
      box-shadow: 0 0 10px #fbbf24;
    }

    .grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
      gap: 18px;
      margin: 20px 0;
    }

    .top-banner {
      background: #fbbf24;
      color: #0b0b1a;
      padding: 12px 20px;
      border-radius: 60px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      margin-bottom: 20px;
      font-weight: 700;
      cursor: pointer;
      box-shadow: 0 0 20px #fbbf24;
      flex-wrap: wrap;
    }

    .side-panel {
      position: fixed;
      right: 16px;
      top: 16px;
      width: 360px;
      max-width: 90vw;
      background: rgba(10,10,25,0.9);
      backdrop-filter: blur(24px);
      border: 1px solid var(--glass-border);
      border-radius: 32px;
      padding: 20px;
      z-index: 120;
      display: none;
      box-shadow: -10px 20px 40px black;
    }

    .modal {
      position: fixed;
      inset: 0;
      background: rgba(0,0,0,0.7);
      backdrop-filter: blur(10px);
      display: flex;
      align-items: center;
      justify-content: center;
      z-index: 200;
    }

    .admin-badge {
      background: #fbbf24;
      color: black;
      border-radius: 30px;
      padding: 4px 12px;
      font-weight: 700;
      margin-left: 8px;
    }

    @media (max-width: 500px) {
      .grid {
        grid-template-columns: repeat(2, 1fr);
      }
    }
  </style>
</head>
<body>
  <div id="activePaymentBar" style="display: none; margin-top: 8px;" class="top-banner" onclick="resumePayment()">
    <span>Внимание: У вас незавершённая оплата</span>
    <span style="text-decoration: underline;">Вернуться к заказу</span>
  </div>

  <div id="authScreen" class="fade-in" style="max-width: 400px; margin: 60px auto;">
    <div class="glass" style="text-align: center;">
      <h2 style="color: #fbbf24; margin-bottom: 15px;">GOLD SHOP</h2>
      <input type="text" id="loginInput" placeholder="Логин" autocomplete="off">
      <input type="password" id="passInput" placeholder="Пароль">
      <button class="gold-btn" style="width:100%; margin-top:10px;" onclick="handleAuth()">Войти / Регистрация</button>
      <p style="font-size:12px; margin-top:10px; opacity:0.7;">При новом логине создаётся аккаунт</p>
    </div>
    <div id="banMessage" style="display: none; background:#7f1d1d; border-radius:40px; padding:15px; margin-top:15px; text-align:center; font-weight:bold;">
      Ваш аккаунт заблокирован. Обратитесь в Telegram: <span style="color:#fbbf24;">@Salmiser</span>
    </div>
  </div>

  <div id="shopScreen" style="display: none;" class="fade-in">
    <div style="display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap;">
      <h2>Gold Shop</h2>
      <div style="display: flex; gap: 10px;">
        <button class="icon-btn" onclick="openHistory()">История</button>
        <button class="icon-btn danger-btn" onclick="logout()">Выйти</button>
      </div>
    </div>
    <div class="grid" id="goldGrid"></div>
    <p style="text-align:center; margin-top:12px;">Поддержка: Telegram <strong>@Salmiser</strong></p>
  </div>

  <div id="cartPanel" class="side-panel">
    <div style="display: flex; justify-content: space-between;">
      <h3>Корзина</h3>
      <button class="icon-btn" onclick="closeCart()">X</button>
    </div>
    <div id="cartItems" style="max-height: 260px; overflow-y: auto; margin: 15px 0;"></div>
    <p><strong>Итого: <span id="cartTotal">0</span> ₽</strong></p>
    <button class="gold-btn" style="width:100%;" onclick="proceedToPayment()">Оформить заказ</button>
    <button class="icon-btn" style="width:100%; margin-top:8px;" onclick="clearCart()">Очистить корзину</button>
  </div>

  <div id="paymentScreen" style="display: none;" class="fade-in">
    <div class="glass" style="max-width: 500px; margin: 20px auto; border-radius: 32px;">
      <div style="display: flex; gap: 10px; margin-bottom: 15px;">
        <button class="icon-btn" onclick="exitPaymentToMenu()">В главное меню</button>
        <button class="icon-btn danger-btn" onclick="cancelOrderAndLeave()">Сбросить покупку</button>
      </div>
      <h2>Оплата заказа</h2>
      <p>Сумма: <strong id="paymentTotal" style="color:#fbbf24;">0</strong> ₽</p>
      <input type="text" id="cardNumber" placeholder="Номер карты (16 цифр)" maxlength="16" oninput="detectBank()">
      <p id="bankHint" style="font-size:13px;">Банк определится автоматически</p>
      <button class="gold-btn" style="width:100%;" id="startChatBtn" onclick="activateChat()">Продолжить</button>

      <div id="chatBlock" style="display: none; margin-top: 20px;">
        <div class="glass" style="background:#0f0f20;">
          <p><strong>Реквизиты:</strong> +7 993 589 00 27 (Т-Банк)</p>
          <p>Переведите точную сумму. Чат с модератором:</p>
          <div id="chatMessages" style="height:150px; overflow-y:auto; background:black; border-radius:16px; padding:10px; margin:10px 0;">Ожидание подтверждения...</div>
          <input type="text" id="chatMessageInput" placeholder="Сообщение...">
          <button class="gold-btn" onclick="sendChatMessage()">Отправить</button>
        </div>
        <p style="color:#fbbf24; margin-top:10px;">Напишите в Telegram <strong>@Salmiser</strong></p>
      </div>
    </div>
  </div>

  <div id="historyScreen" style="display: none;" class="fade-in">
    <button class="icon-btn" onclick="backToShop()">Назад</button>
    <h2>История покупок</h2>
    <div id="historyList" class="glass" style="margin-top:15px;"></div>
  </div>

  <div id="adminScreen" style="display: none;" class="fade-in">
    <div style="display: flex; justify-content: space-between;">
      <h2>Админ панель</h2>
      <button class="danger-btn icon-btn" onclick="logout()">Выйти</button>
    </div>
    <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-top: 20px;">
      <div class="glass">
        <h3>Активные заказы</h3>
        <div id="adminOrders"></div>
      </div>
      <div class="glass">
        <h3>Пользователи</h3>
        <div id="adminUsersList"></div>
      </div>
    </div>
    <div class="glass" style="margin-top:20px;">
      <h3>Чат с пользователем</h3>
      <div id="adminChatView"></div>
    </div>
  </div>

  <script>
    (function() {
      const GOLD_PACKS = [
        { id: 1, amount: 10000, price: 20 },
        { id: 2, amount: 30000, price: 40 },
        { id: 3, amount: 70000, price: 70 },
        { id: 4, amount: 120000, price: 100 },
        { id: 5, amount: 200000, price: 150 },
        { id: 6, amount: 300000, price: 220 }
      ];

      const ADMIN_CRED = { login: "admin", pass: "k9#Lp2!vX8$mQ" };

      let currentUser = null;
      let cart = [];
      let activeOrder = null;

      function saveUsers(users) { localStorage.setItem('game_users', JSON.stringify(users)); }
      function getUsers() { return JSON.parse(localStorage.getItem('game_users')) || {}; }
      function saveActiveOrder(order) { localStorage.setItem('active_order', JSON.stringify(order)); }
      function getActiveOrder() { return JSON.parse(localStorage.getItem('active_order')) || null; }
      function clearActiveOrder() { localStorage.removeItem('active_order'); activeOrder = null; }

      function hideAll() {
        document.getElementById('authScreen').style.display = 'none';
        document.getElementById('shopScreen').style.display = 'none';
        document.getElementById('paymentScreen').style.display = 'none';
        document.getElementById('historyScreen').style.display = 'none';
        document.getElementById('adminScreen').style.display = 'none';
        document.getElementById('cartPanel').style.display = 'none';
      }

      function showAuth() { hideAll(); document.getElementById('authScreen').style.display = 'block'; }
      function showShop() {
        hideAll();
        document.getElementById('shopScreen').style.display = 'block';
        renderGoldGrid();
        updatePaymentBanner();
      }
      function showPayment() {
        hideAll();
        document.getElementById('paymentScreen').style.display = 'block';
        if (activeOrder) {
          document.getElementById('paymentTotal').innerText = activeOrder.total;
          document.getElementById('cardNumber').value = activeOrder.card || '';
          if (activeOrder.chatActive) {
            document.getElementById('chatBlock').style.display = 'block';
            document.getElementById('startChatBtn').style.display = 'none';
            renderChatMessages();
          } else {
            document.getElementById('chatBlock').style.display = 'none';
            document.getElementById('startChatBtn').style.display = 'block';
          }
        }
      }
      function showHistoryScreen() {
        hideAll();
        document.getElementById('historyScreen').style.display = 'block';
        renderHistory();
      }
      function showAdmin() {
        hideAll();
        document.getElementById('adminScreen').style.display = 'block';
        renderAdminPanel();
      }

      function updatePaymentBanner() {
        const bar = document.getElementById('activePaymentBar');
        if (currentUser && !currentUser.isAdmin && activeOrder && activeOrder.user === currentUser.login) {
          bar.style.display = 'flex';
        } else {
          bar.style.display = 'none';
        }
      }

      function resumePayment() {
        if (activeOrder && currentUser && activeOrder.user === currentUser.login) {
          showPayment();
        } else {
          alert('Нет активной оплаты.');
        }
      }

      window.handleAuth = function() {
        const login = document.getElementById('loginInput').value.trim();
        const pass = document.getElementById('passInput').value.trim();
        if (!login || !pass) return alert('Введите логин и пароль');

        if (login === ADMIN_CRED.login && pass === ADMIN_CRED.pass) {
          currentUser = { login, isAdmin: true };
          localStorage.setItem('current_user', JSON.stringify(currentUser));
          showAdmin();
          return;
        }

        let users = getUsers();
        if (users[login]) {
          if (users[login].banned) {
            document.getElementById('banMessage').style.display = 'block';
            return;
          }
          if (users[login].pass !== pass) return alert('Неверный пароль');
          currentUser = { login, isAdmin: false };
        } else {
          users[login] = { pass, banned: false, history: [] };
          saveUsers(users);
          currentUser = { login, isAdmin: false };
        }
        localStorage.setItem('current_user', JSON.stringify(currentUser));
        activeOrder = getActiveOrder();
        showShop();
      };

      window.logout = function() {
        localStorage.removeItem('current_user');
        currentUser = null;
        activeOrder = null;
        cart = [];
        hideAll();
        showAuth();
      };

      function renderGoldGrid() {
        const grid = document.getElementById('goldGrid');
        grid.innerHTML = GOLD_PACKS.map(p => `
          <div class="glass" style="text-align:center; cursor:pointer;" onclick="selectGold(${p.id})">
            <div style="font-size:2rem; margin-bottom: 5px;">GOLD</div>
            <div style="font-weight:bold;">${p.amount.toLocaleString()} ед.</div>
            <div style="color:#fbbf24;">${p.price} ₽</div>
          </div>
        `).join('');
      }

      window.selectGold = function(id) {
        const pack = GOLD_PACKS.find(p => p.id === id);
        if (!pack) return;
        document.getElementById('cartPanel').style.display = 'block';
        cart.push(pack);
        refreshCartDisplay();
      };

      window.removeCartItem = function(index) {
        cart.splice(index, 1);
        refreshCartDisplay();
      };

      window.clearCart = function() {
        cart = [];
        refreshCartDisplay();
      };

      function refreshCartDisplay() {
        const container = document.getElementById('cartItems');
        container.innerHTML = cart.map((item, idx) => `
          <div style="display:flex; justify-content:space-between; margin-bottom:8px;">
            <span>${item.amount} GOLD</span><span>${item.price}₽</span>
            <button onclick="removeCartItem(${idx})" style="background:none; border:none; color:red; cursor:pointer;">X</button>
          </div>`).join('');
        updateCartTotal();
      }

      function updateCartTotal() {
        const total = cart.reduce((sum, i) => sum + i.price, 0);
        document.getElementById('cartTotal').innerText = total;
      }

      window.closeCart = function() {
        document.getElementById('cartPanel').style.display = 'none';
      };

      window.proceedToPayment = function() {
        if (cart.length === 0) return alert('Добавьте товары');
        if (activeOrder) return alert('Уже есть незавершённая оплата. Завершите её.');
        const total = cart.reduce((s, i) => s + i.price, 0);
        activeOrder = {
          user: currentUser.login,
          items: [...cart],
          total,
          card: '',
          status: 'pending',
          chatActive: false,
          messages: []
        };
        saveActiveOrder(activeOrder);
        cart = [];
        closeCart();
        showPayment();
      };

      window.detectBank = function() {
        const num = document.getElementById('cardNumber').value;
        const hint = document.getElementById('bankHint');
        if (num.startsWith('4276') || num.startsWith('4279')) hint.innerText = 'Сбербанк';
        else if (num.startsWith('5106') || num.startsWith('5536')) hint.innerText = 'Т-Банк';
        else if (num.startsWith('4890')) hint.innerText = 'Альфа-Банк';
        else if (num.length >= 4) hint.innerText = 'Другой банк';
        else hint.innerText = 'Банк определится';
        if (activeOrder) {
          activeOrder.card = num;
          saveActiveOrder(activeOrder);
        }
      };

      window.activateChat = function() {
        const card = document.getElementById('cardNumber').value;
        if (card.length < 16) return alert('Введите 16 цифр карты');
        if (!activeOrder) return;
        activeOrder.chatActive = true;
        activeOrder.card = card;
        saveActiveOrder(activeOrder);
        document.getElementById('chatBlock').style.display = 'block';
        document.getElementById('startChatBtn').style.display = 'none';
        addSystemMessage('Чат активирован. Ожидайте подтверждения.');
      };

      function addSystemMessage(text) {
        if (!activeOrder) return;
        activeOrder.messages.push({ sender: 'system', text, time: Date.now() });
        saveActiveOrder(activeOrder);
        renderChatMessages();
      }

      window.sendChatMessage = function() {
        const input = document.getElementById('chatMessageInput');
        const msg = input.value.trim();
        if (!msg || !activeOrder) return;
        activeOrder.messages.push({ sender: 'user', text: msg, time: Date.now() });
        saveActiveOrder(activeOrder);
        input.value = '';
        renderChatMessages();
      };

      function renderChatMessages() {
        const box = document.getElementById('chatMessages');
        if (!activeOrder) return;
        box.innerHTML = activeOrder.messages.map(m => {
          let author = m.sender === 'user' ? 'Вы' : (m.sender === 'admin' ? 'Модератор' : 'Система');
          return `<div><strong>${author}:</strong> ${m.text}</div>`;
        }).join('') || 'Чат пуст';
      }

      window.exitPaymentToMenu = function() {
        if (confirm('Оставить оплату активной? (Вы сможете вернуться)')) {
          showShop();
        } else {
          cancelOrderAndLeave();
        }
      };

      window.cancelOrderAndLeave = function() {
        if (activeOrder && confirm('Сбросить покупку? Она отобразится как отменённая.')) {
          addToHistory({ ...activeOrder, status: 'cancelled' });
          clearActiveOrder();
          alert('Покупка отменена.');
          showShop();
        }
      };

      function addToHistory(order) {
        let users = getUsers();
        if (users[order.user]) {
          users[order.user].history.push(order);
          saveUsers(users);
        }
      }

      window.openHistory = function() { showHistoryScreen(); };
      window.backToShop = function() { showShop(); };

      function renderHistory() {
        const users = getUsers();
        const hist = users[currentUser.login]?.history || [];
        const container = document.getElementById('historyList');
        container.innerHTML = hist.length ? hist.map(h => `
          <div style="border-bottom:1px solid gray; padding:8px;">
            ${h.total}₽ – <span style="color:${h.status==='finished'?'#10b981':'#ef4444'}">${h.status==='finished'?'Завершена':'Отменена'}</span>
          </div>`).join('') : 'История пуста.';
      }

      function renderAdminPanel() {
        const users = getUsers();
        const order = getActiveOrder();
        const ordersDiv = document.getElementById('adminOrders');
        const usersDiv = document.getElementById('adminUsersList');
        const chatDiv = document.getElementById('adminChatView');

        ordersDiv.innerHTML = order ? `
          <div><b>${order.user}</b> – ${order.total}₽ (${order.status})</div>
          <button class="gold-btn" onclick="adminFinishOrder()">Подтвердить</button>
          <button class="danger-btn icon-btn" onclick="adminCancelOrder()">Отклонить</button>
        ` : '<p>Нет активных заказов</p>';

        usersDiv.innerHTML = Object.entries(users).map(([login, data]) => `
          <div style="display:flex; justify-content:space-between; margin:8px 0;">
            <span>${login} ${data.banned ? '[Забанен]' : '[Активен]'}</span>
            <button class="icon-btn" onclick="toggleUserBan('${login}')">${data.banned ? 'Разбанить' : 'Забанить'}</button>
          </div>`).join('');

        chatDiv.innerHTML = order?.messages ? order.messages.map(m => `<div><b>${m.sender}:</b> ${m.text}</div>`).join('') : 'Нет сообщений';
        if (order) {
          chatDiv.innerHTML += `<input id="adminChatInput" placeholder="Ответить..."><button onclick="adminSendMessage()">Отправить</button>`;
        }
      }

      window.adminFinishOrder = function() {
        let order = getActiveOrder();
        if (!order) return;
        order.status = 'finished';
        addToHistory(order);
        clearActiveOrder();
        alert('Заказ завершён.');
        renderAdminPanel();
      };

      window.adminCancelOrder = function() {
        let order = getActiveOrder();
        if (!order) return;
        order.status = 'cancelled';
        addToHistory(order);
        clearActiveOrder();
        alert('Заказ отменён.');
        renderAdminPanel();
      };

      window.toggleUserBan = function(login) {
        let users = getUsers();
        users[login].banned = !users[login].banned;
        saveUsers(users);
        renderAdminPanel();
      };

      window.adminSendMessage = function() {
        const input = document.getElementById('adminChatInput');
        if (!input) return;
        let order = getActiveOrder();
        if (!order) return;
        order.messages.push({ sender: 'admin', text: input.value, time: Date.now() });
        saveActiveOrder(order);
        renderAdminPanel();
      };

      window.onload = function() {
        const savedUser = JSON.parse(localStorage.getItem('current_user'));
        if (savedUser) {
          currentUser = savedUser;
          activeOrder = getActiveOrder();
          if (currentUser.isAdmin) showAdmin();
          else showShop();
        } else {
          showAuth();
        }
      };
    })();
  </script>
</body>
</html>
