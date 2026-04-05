<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>testsalmis - EventTimer</title>
    <style>
        :root {
            --bg-color: #0f172a;
            --card-bg: rgba(30, 41, 59, 0.75);
            --text-main: #f8fafc;
            --text-muted: #94a3b8;
            --accent: #3b82f6;
            --accent-hover: #2563eb;
            --danger: #ef4444;
            --success: #10b981;
            --warning: #f59e0b;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Tahoma, sans-serif; }

        body {
            background: var(--bg-color);
            background-size: cover;
            background-position: center;
            background-attachment: fixed;
            color: var(--text-main);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            transition: background 0.5s ease;
        }

        /* Общие компоненты */
        header {
            background: rgba(15, 23, 42, 0.85);
            backdrop-filter: blur(15px);
            padding: 15px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            position: sticky;
            top: 0;
            z-index: 100;
            border-bottom: 1px solid rgba(255,255,255,0.08);
            box-shadow: 0 4px 30px rgba(0,0,0,0.4);
        }

        .logo { font-size: 1.6rem; font-weight: 800; background: linear-gradient(135deg, #3b82f6, #8b5cf6, #ec4899); -webkit-background-clip: text; -webkit-text-fill-color: transparent; cursor: pointer; }

        .search-bar { display: flex; flex: 1; max-width: 500px; margin: 0 20px; }
        .search-bar input, .custom-input, select {
            width: 100%; padding: 14px 18px; border-radius: 12px; border: 1px solid rgba(255,255,255,0.1);
            background: rgba(0,0,0,0.4); color: white; outline: none; transition: 0.3s; font-size: 1rem;
        }
        .search-bar input:focus, .custom-input:focus, select:focus { border-color: var(--accent); background: rgba(0,0,0,0.6); box-shadow: 0 0 15px rgba(59, 130, 246, 0.3); }

        .burger { display: flex; flex-direction: column; gap: 6px; cursor: pointer; z-index: 1001; padding: 5px; }
        .burger span { width: 28px; height: 3px; background: white; transition: 0.4s; border-radius: 3px; }
        .burger.active span:nth-child(1) { transform: translateY(9px) rotate(45deg); }
        .burger.active span:nth-child(2) { opacity: 0; }
        .burger.active span:nth-child(3) { transform: translateY(-9px) rotate(-45deg); }

        .sidebar {
            position: fixed; top: 0; right: -320px; width: 320px; height: 100vh;
            background: rgba(15, 23, 42, 0.95); backdrop-filter: blur(20px);
            box-shadow: -5px 0 30px rgba(0,0,0,0.6); transition: 0.4s cubic-bezier(0.4, 0, 0.2, 1);
            z-index: 1000; padding: 80px 20px 20px; display: flex; flex-direction: column; gap: 10px; overflow-y: auto; border-left: 1px solid rgba(255,255,255,0.05);
        }
        .sidebar.open { right: 0; }
        .sidebar button {
            background: transparent; border: 1px solid transparent; color: var(--text-main);
            padding: 14px 18px; border-radius: 12px; cursor: pointer; transition: 0.2s; text-align: left; font-size: 1.05rem;
            display: flex; align-items: center; gap: 12px;
        }
        .sidebar button:hover { background: rgba(255,255,255,0.08); transform: translateX(8px); }

        main { padding: 30px 20px; max-width: 1200px; margin: 0 auto; width: 100%; flex: 1; }
        .view { display: none; }
        .view.active { display: block; animation: fadeIn 0.4s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }

        /* Имена и галочки */
        .user-name-wrapper { display: inline-flex; align-items: center; gap: 4px; cursor: pointer; transition: 0.2s; }
        .user-name-wrapper:hover { opacity: 0.8; }
        .user-nick { font-weight: bold; text-shadow: 0 0 5px rgba(0,0,0,0.5); }
        .badge { display: inline-flex; align-items: center; justify-content: center; width: 16px; height: 16px; border-radius: 50%; font-size: 10px; color: white; background: var(--accent); box-shadow: 0 0 8px var(--accent); }

        /* Карточки постов */
        .holidays-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(320px, 1fr)); gap: 25px; margin-top: 25px; }
        .card {
            background: var(--card-bg); backdrop-filter: blur(15px); border-radius: 20px; padding: 25px;
            box-shadow: 0 10px 30px -10px rgba(0,0,0,0.5); border: 1px solid rgba(255,255,255,0.08);
            transition: 0.3s; position: relative; overflow: hidden; display: flex; flex-direction: column;
        }
        .card:hover { transform: translateY(-8px); border-color: rgba(59, 130, 246, 0.6); box-shadow: 0 15px 40px -10px rgba(59, 130, 246, 0.3); }
        .card-header { font-size: 1.4rem; font-weight: bold; margin-bottom: 10px; cursor: pointer; color: white; text-decoration: none; display: flex; justify-content: space-between;}
        .card-header:hover { color: var(--accent); }

        /* Таймер внутри отдельного окна */
        .event-view-container { text-align: center; background: var(--card-bg); padding: 50px; border-radius: 30px; border: 1px solid rgba(255,255,255,0.1); backdrop-filter: blur(20px); box-shadow: 0 20px 60px rgba(0,0,0,0.5); }
        .event-title { font-size: 3rem; margin-bottom: 10px; background: linear-gradient(45deg, #fff, #a5b4fc); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .timer-display { display: flex; justify-content: center; flex-wrap: wrap; gap: 20px; margin: 40px 0; }
        .timer-circle {
            width: 100px; height: 100px; border-radius: 25px; background: rgba(0,0,0,0.4); color: white; display: flex; flex-direction: column; align-items: center; justify-content: center; border: 2px solid var(--accent); box-shadow: 0 0 20px rgba(59, 130, 246, 0.3);
        }
        .timer-circle .val { font-size: 2rem; font-weight: 900; }
        .timer-circle .lbl { font-size: 0.7rem; color: var(--text-muted); text-transform: uppercase; font-weight: bold; letter-spacing: 1px; }

        /* Кнопки */
        .btn-primary { background: linear-gradient(45deg, var(--accent), #60a5fa); color: white; padding: 14px 20px; border: none; border-radius: 12px; cursor: pointer; font-weight: bold; transition: 0.3s; box-shadow: 0 5px 15px rgba(59,130,246,0.3); }
        .btn-primary:hover { transform: translateY(-2px); box-shadow: 0 8px 25px rgba(59,130,246,0.5); }
        .btn-danger { background: var(--danger); color: white; padding: 10px 15px; border: none; border-radius: 8px; cursor: pointer; transition: 0.2s;}
        .btn-success { background: var(--success); color: white; padding: 10px 15px; border: none; border-radius: 8px; cursor: pointer; transition: 0.2s;}
        .btn-google { background: white; color: #333; padding: 14px; border: none; border-radius: 12px; cursor: pointer; font-weight: bold; width: 100%; display: flex; align-items: center; justify-content: center; gap: 10px; transition: 0.3s; margin-top: 10px;}
        .btn-google:hover { background: #f1f5f9; }

        /* Уведомления */
        #toastContainer { position: fixed; bottom: 30px; right: 30px; display: flex; flex-direction: column; gap: 15px; z-index: 9999; }
        .toast { background: rgba(15, 23, 42, 0.95); color: white; padding: 15px 25px; border-radius: 15px; box-shadow: 0 10px 40px rgba(0,0,0,0.6); border-left: 5px solid var(--accent); animation: slideIn 0.4s cubic-bezier(0.2, 0.8, 0.2, 1); display: flex; align-items: center; gap: 15px; min-width: 300px; backdrop-filter: blur(10px); }
        @keyframes slideIn { from { transform: translateX(120%); opacity: 0; } to { transform: translateX(0); opacity: 1; } }

        /* Таблицы */
        .admin-table { width: 100%; border-collapse: collapse; margin-top: 15px; background: rgba(0,0,0,0.3); border-radius: 15px; overflow: hidden; }
        .admin-table th, .admin-table td { padding: 15px; text-align: left; border-bottom: 1px solid rgba(255,255,255,0.05); }
        .admin-table th { background: rgba(0,0,0,0.5); color: var(--accent); font-weight: bold; }

        .leaderboard-row { display: flex; align-items: center; justify-content: space-between; padding: 15px 20px; background: rgba(0,0,0,0.3); border-radius: 15px; margin-bottom: 10px; border: 1px solid rgba(255,255,255,0.05); transition: 0.2s;}
        .leaderboard-row:hover { background: rgba(0,0,0,0.5); border-color: var(--accent); }
        
        .file-upload-wrapper { position: relative; overflow: hidden; display: inline-block; width: 100%; }
        .file-upload-wrapper input[type=file] { font-size: 100px; position: absolute; left: 0; top: 0; opacity: 0; cursor: pointer; height: 100%; }
        
        @media (max-width: 768px) {
            .search-bar { display: none; }
            .timer-circle { width: 75px; height: 75px; border-radius: 15px; }
            .timer-circle .val { font-size: 1.4rem; }
            .event-title { font-size: 2rem; }
        }
    </style>
</head>
<body>

    <div id="toastContainer"></div>

    <header>
        <div class="logo" onclick="navigate('home')">testsalmis</div>
        <div class="search-bar">
            <input type="text" id="searchInput" placeholder="Поиск событий..." oninput="renderHolidays(this.value)">
        </div>
        <div style="display: flex; align-items: center; gap: 15px;">
            <div style="font-weight: bold; color: var(--warning); display: none;" id="headerPoints">🪙 <span id="userPointsVal">0</span></div>
            <div class="burger" id="burgerMenu"><span></span><span></span><span></span></div>
        </div>
    </header>

    <div class="sidebar" id="sidebar">
        <div id="sidebarUserInfo" style="margin-bottom: 15px; padding: 15px; background: rgba(0,0,0,0.3); border-radius: 15px;"></div>
        
        <input type="text" id="searchInputMobile" class="custom-input" style="display:none; margin-bottom: 15px;" placeholder="Поиск..." oninput="renderHolidays(this.value)">

        <button onclick="navigate('home')">🏠 Главная</button>
        <button id="navLeaderboardBtn" onclick="navigate('leaderboard')">🏆 Лидерборд</button>
        <button id="navAddBtn" style="display:none;" onclick="navigate('add')">➕ Создать событие</button>
        <button id="navLoginBtn" onclick="navigate('auth')">🔑 Авторизация</button>
        <button id="navProfileBtn" style="display:none;" onclick="openProfile(currentUser?.username)">👤 Мой профиль</button>
        <button id="navSettingsBtn" style="display:none;" onclick="navigate('settings')">⚙️ Настройки</button>
        <button id="navAdminBtn" style="display:none;" onclick="navigate('admin')">👑 Админ панель</button>
        <button id="navLogoutBtn" style="display:none; color: var(--danger); margin-top: auto;" onclick="logout()">🚪 Выйти</button>
    </div>

    <main>
        <div id="view-home" class="view active">
            <h2>🎉 Актуальные события</h2>
            <div class="holidays-grid" id="holidaysList"></div>
        </div>

        <div id="view-event" class="view">
            <button onclick="navigate('home')" style="background:transparent; border:none; color:var(--text-muted); cursor:pointer; font-size: 1.1rem; margin-bottom: 20px;">← Назад к ленте</button>
            <div id="eventContainer" class="event-view-container"></div>
        </div>

        <div id="view-leaderboard" class="view">
            <h2 style="margin-bottom: 20px; text-align: center;">🏆 Таблица лидеров</h2>
            <p style="text-align: center; color: var(--text-muted); margin-bottom: 30px;">Получайте очки за лайки на ваших событиях (1 👍 = 5 очков).</p>
            <div id="leaderboardList" style="max-width: 800px; margin: 0 auto;"></div>
        </div>

        <div id="view-userprofile" class="view">
            <button onclick="navigate('home')" style="background:transparent; border:none; color:var(--text-muted); cursor:pointer; margin-bottom: 15px;">← Назад</button>
            <div style="background: var(--card-bg); padding: 40px; border-radius: 20px; border: 1px solid rgba(255,255,255,0.05); text-align: center;">
                <h2 id="upTitle" style="font-size: 2.5rem; margin-bottom: 10px;">Профиль</h2>
                <div style="display: flex; justify-content: center; gap: 20px; margin-top: 20px;">
                    <div style="background: rgba(0,0,0,0.3); padding: 20px; border-radius: 15px; min-width: 150px;">
                        <h3 style="color: var(--text-muted); font-size: 1rem;">Очки</h3>
                        <p id="upPoints" style="font-size: 2rem; font-weight: bold; color: var(--warning);">0</p>
                    </div>
                    <div style="background: rgba(0,0,0,0.3); padding: 20px; border-radius: 15px; min-width: 150px;">
                        <h3 style="color: var(--text-muted); font-size: 1rem;">Создано</h3>
                        <p id="upPosts" style="font-size: 2rem; font-weight: bold; color: var(--accent);">0</p>
                    </div>
                </div>
            </div>
            <h3 style="margin-top: 40px; margin-bottom: 20px;">Публикации пользователя</h3>
            <div class="holidays-grid" id="upList"></div>
        </div>

        <div id="view-auth" class="view">
            <div style="max-width: 450px; margin: 40px auto; background: var(--card-bg); padding: 40px; border-radius: 20px; box-shadow: 0 10px 40px rgba(0,0,0,0.5);">
                <h2 style="margin-bottom: 25px; text-align: center;">Вход / Регистрация</h2>
                <div class="form-group" style="margin-bottom: 15px;">
                    <input type="text" id="authUsername" class="custom-input" placeholder="Логин">
                </div>
                <div class="form-group" style="margin-bottom: 25px;">
                    <input type="password" id="authPassword" class="custom-input" placeholder="Пароль">
                </div>
                <button onclick="handleAuth()" class="btn-primary" style="width: 100%; margin-bottom: 15px;">Продолжить</button>
                
                <div style="text-align: center; color: var(--text-muted); margin: 20px 0;">или</div>
                
                <button onclick="mockGoogleLogin()" class="btn-google">
                    <svg width="20" height="20" viewBox="0 0 24 24"><path fill="#4285F4" d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z"/><path fill="#34A853" d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z"/><path fill="#FBBC05" d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z"/><path fill="#EA4335" d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z"/></svg>
                    Войти через Google
                </button>
            </div>
        </div>

        <div id="view-add" class="view">
            <div style="max-width: 500px; margin: 40px auto; background: var(--card-bg); padding: 40px; border-radius: 20px;">
                <h2 style="margin-bottom: 25px;">Новое событие</h2>
                <input type="text" id="newHolidayTitle" class="custom-input" placeholder="Название" style="margin-bottom: 15px;">
                <input type="datetime-local" id="newHolDate" class="custom-input" style="margin-bottom: 25px;">
                <button onclick="addHoliday()" class="btn-primary" style="width: 100%;">Опубликовать</button>
            </div>
        </div>

        <div id="view-settings" class="view">
            <div style="max-width: 600px; margin: 0 auto; background: var(--card-bg); padding: 40px; border-radius: 20px;">
                <h2 style="margin-bottom: 25px;">⚙️ Настройки</h2>
                
                <div style="margin-bottom: 25px;">
                    <label style="display:block; margin-bottom:10px; color:var(--text-muted);">Изменить фон (URL)</label>
                    <input type="text" id="setBgUrl" class="custom-input" placeholder="https://..." style="margin-bottom: 10px;">
                    <button onclick="changeBackground(document.getElementById('setBgUrl').value)" class="btn-primary">Применить URL</button>
                </div>

                <div style="margin-bottom: 30px; padding: 20px; background: rgba(0,0,0,0.3); border-radius: 15px;">
                    <label style="display:block; margin-bottom:10px; color:var(--text-muted);">Или загрузить фото с устройства</label>
                    <div class="file-upload-wrapper">
                        <button class="btn-primary" style="width:100%; pointer-events: none;">📂 Выбрать файл</button>
                        <input type="file" id="bgFileInput" accept="image/*" onchange="uploadBg(event)">
                    </div>
                    <small style="color:var(--warning); display:block; margin-top:10px;">Внимание: Большие фото могут занять всю память (localStorage).</small>
                </div>
            </div>
        </div>

        <div id="view-admin" class="view">
            <h2>👑 Админ-панель</h2>
            
            <div style="background: rgba(0,0,0,0.3); padding: 20px; border-radius: 15px; margin: 20px 0;">
                <h3>🛡️ Пользователи и цвета ников</h3>
                <div style="overflow-x: auto;">
                    <table class="admin-table">
                        <thead><tr><th>Никнейм</th><th>Цвет ника</th><th>Роль</th><th>Бан</th></tr></thead>
                        <tbody id="adminUsersList"></tbody>
                    </table>
                </div>
            </div>

            <div style="background: rgba(0,0,0,0.3); padding: 20px; border-radius: 15px; margin: 20px 0;">
                <h3>🤖 Управление Ботами</h3>
                <div style="display:flex; gap:10px; margin-bottom:15px;">
                    <input type="text" id="newBotName" class="custom-input" placeholder="Имя бота">
                    <button onclick="addBot()" class="btn-primary">Создать бота</button>
                </div>
                <div style="overflow-x: auto;">
                    <table class="admin-table">
                        <thead><tr><th>Бот</th><th>Очки</th><th>Удалить</th></tr></thead>
                        <tbody id="adminBotsList"></tbody>
                    </table>
                </div>
            </div>
        </div>

    </main>

    <script>
        // --- БАЗА ДАННЫХ И ИНИЦИАЛИЗАЦИЯ ---
        let db = { users: [], posts: [], bots: [] };
        let currentUser = null;
        let activeTimerInterval = null;

        function initDB() {
            try {
                const stored = localStorage.getItem('eventTimerProDB_v2');
                if(stored) db = JSON.parse(stored);
            } catch(e) { console.error("Ошибка чтения БД", e); }
            
            if(!db.users) db.users = [];
            if(!db.posts) db.posts = [];
            if(!db.bots) db.bots = [
                { id: 'bot1', username: 'SpeedsterBot', points: 450, isBot: true },
                { id: 'bot2', username: 'TimeKeeper', points: 120, isBot: true }
            ];

            // Создаем главного админа если нет
            if(!db.users.find(u => u.username === 'salmis')) {
                db.users.push({ username: 'salmis', password: '123', role: 'admin', nameColor: '#3b82f6' });
            }

            // Перенос старых постов под новую систему лайков
            db.posts.forEach(p => {
                if(!p.likes) p.likes = [];
                if(!p.dislikes) p.dislikes = [];
            });

            saveDB();
            checkSession();
        }

        function saveDB() { 
            try {
                localStorage.setItem('eventTimerProDB_v2', JSON.stringify(db)); 
            } catch (e) {
                showToast("Ошибка сохранения! Возможно, загружено слишком большое фото.", "error");
            }
        }

        // --- ВСПОМОГАТЕЛЬНЫЕ ---
        function showToast(msg, type = 'info') {
            const container = document.getElementById('toastContainer');
            const toast = document.createElement('div');
            toast.className = 'toast';
            toast.style.borderLeftColor = type === 'error' ? 'var(--danger)' : type === 'success' ? 'var(--success)' : 'var(--accent)';
            toast.innerHTML = `<span style="font-size:1.3rem">${type === 'error' ? '❌' : type === 'success' ? '✅' : '🔔'}</span> ${msg}`;
            container.appendChild(toast);
            setTimeout(() => { toast.style.animation = 'slideIn 0.4s reverse forwards'; setTimeout(() => toast.remove(), 400); }, 3000);
        }

        function getBadgeHTML(user) {
            if(!user) return '';
            if(user.role === 'admin' || user.role === 'moderator' || user.verified) return `<span class="badge">✔</span>`;
            if(user.isBot) return `<span class="badge" style="background:var(--warning)" title="Бот">🤖</span>`;
            return '';
        }

        function renderUserName(username) {
            const u = db.users.find(x => x.username === username) || db.bots.find(x => x.username === username);
            if(!u) return `<span class="user-name-wrapper"><span class="user-nick">${username}</span></span>`;
            const color = u.nameColor || 'white';
            return `<span class="user-name-wrapper" onclick="openProfile('${u.username}'); event.stopPropagation();">
                        <span class="user-nick" style="color: ${color}">${u.username}</span>${getBadgeHTML(u)}
                    </span>`;
        }

        function calculatePoints(username) {
            const bot = db.bots.find(b => b.username === username);
            if(bot) return bot.points;

            const userPosts = db.posts.filter(p => p.author === username);
            let likesCount = 0;
            userPosts.forEach(p => { likesCount += (p.likes ? p.likes.length : 0); });
            return likesCount * 5;
        }

        // --- НАВИГАЦИЯ ---
        function navigate(viewId) {
            document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
            document.getElementById(`view-${viewId}`).classList.add('active');
            
            if(viewId === 'home') renderHolidays();
            if(viewId === 'leaderboard') renderLeaderboard();
            if(viewId === 'admin') renderAdmin();
            if(viewId === 'settings' && currentUser) document.getElementById('setBgUrl').value = currentUser.bg || '';
            
            updateUI();
            document.getElementById('sidebar').classList.remove('open');
            document.getElementById('burgerMenu').classList.remove('active');
            clearInterval(activeTimerInterval); // Останавливаем тяжелый таймер при уходе
        }

        function updateUI() {
            const sbInfo = document.getElementById('sidebarUserInfo');
            const hp = document.getElementById('headerPoints');
            if(currentUser) {
                document.body.style.backgroundImage = currentUser.bg ? `url('${currentUser.bg}')` : 'none';
                sbInfo.innerHTML = `Привет, ${renderUserName(currentUser.username)}<br><small style="color:var(--text-muted)">${currentUser.role || 'Пользователь'}</small>`;
                ['navLoginBtn'].forEach(id => document.getElementById(id).style.display = 'none');
                ['navLogoutBtn', 'navProfileBtn', 'navAddBtn', 'navSettingsBtn'].forEach(id => document.getElementById(id).style.display = 'flex');
                document.getElementById('navAdminBtn').style.display = (currentUser.role === 'admin') ? 'flex' : 'none';
                
                hp.style.display = 'block';
                document.getElementById('userPointsVal').innerText = calculatePoints(currentUser.username);
            } else {
                document.body.style.backgroundImage = 'none';
                sbInfo.innerHTML = `Вы не авторизованы`;
                hp.style.display = 'none';
                ['navLoginBtn'].forEach(id => document.getElementById(id).style.display = 'flex');
                ['navLogoutBtn', 'navProfileBtn', 'navAddBtn', 'navSettingsBtn', 'navAdminBtn'].forEach(id => document.getElementById(id).style.display = 'none');
            }
        }

        // --- АВТОРИЗАЦИЯ ---
        function checkSession() {
            const nick = localStorage.getItem('eventTimerProUser_v2');
            if(nick) {
                const u = db.users.find(x => x.username === nick);
                if(u) { currentUser = u; }
            }
        }

        function handleAuth() {
            const u = document.getElementById('authUsername').value.trim();
            const p = document.getElementById('authPassword').value.trim();
            if(!u || !p) return showToast("Заполните поля", "error");

            let user = db.users.find(x => x.username === u);
            if(user) {
                if(user.password !== p) return showToast("Неверный пароль", "error");
            } else {
                user = { username: u, password: p, role: 'user', createdAt: Date.now() };
                db.users.push(user); saveDB();
            }
            currentUser = user; localStorage.setItem('eventTimerProUser_v2', user.username);
            navigate('home'); showToast(`Добро пожаловать, ${u}!`, 'success');
        }

        function mockGoogleLogin() {
            // Имитация Google OAuth
            const email = prompt("Окно Google: Введите ваш Google email для входа (имитация)");
            if(email && email.includes('@')) {
                const nick = email.split('@')[0];
                let user = db.users.find(x => x.username === nick);
                if(!user) {
                    user = { username: nick, password: 'google_auth', role: 'user', createdAt: Date.now() };
                    db.users.push(user); saveDB();
                }
                currentUser = user; localStorage.setItem('eventTimerProUser_v2', user.username);
                navigate('home'); showToast(`Успешный вход через Google!`, 'success');
            } else if(email) {
                showToast("Неверный формат email", "error");
            }
        }

        function logout() { currentUser = null; localStorage.removeItem('eventTimerProUser_v2'); navigate('home'); }

        // --- ПРОФИЛИ И ЛИДЕРБОРД ---
        window.openProfile = (username) => {
            if(!username) return;
            const target = db.users.find(u => u.username === username) || db.bots.find(b => b.username === username);
            if(!target) return;

            document.getElementById('upTitle').innerHTML = renderUserName(target.username);
            document.getElementById('upPoints').innerText = calculatePoints(target.username);
            
            const userPosts = db.posts.filter(p => p.author === target.username);
            document.getElementById('upPosts').innerText = userPosts.length;
            
            const list = document.getElementById('upList'); list.innerHTML = '';
            userPosts.forEach(p => {
                const l = p.likes ? p.likes.length : 0;
                list.innerHTML += `
                    <div class="card" onclick="openEvent(${p.id})">
                        <div class="card-header">${p.title}</div>
                        <div style="color:var(--text-muted); font-size: 0.9rem;">👍 ${l} лайков</div>
                    </div>`;
            });
            if(userPosts.length === 0) list.innerHTML = '<p style="color:var(--text-muted)">Нет публикаций</p>';
            navigate('userprofile');
        }

        function renderLeaderboard() {
            const list = document.getElementById('leaderboardList'); list.innerHTML = '';
            
            let all = [];
            db.users.forEach(u => all.push({ name: u.username, pts: calculatePoints(u.username) }));
            db.bots.forEach(b => all.push({ name: b.username, pts: b.points }));

            all.sort((a, b) => b.pts - a.pts);

            all.forEach((item, idx) => {
                let medal = idx === 0 ? '🥇' : idx === 1 ? '🥈' : idx === 2 ? '🥉' : `${idx+1}.`;
                list.innerHTML += `
                    <div class="leaderboard-row" onclick="openProfile('${item.name}')" style="cursor:pointer;">
                        <div style="font-size: 1.2rem; display:flex; align-items:center; gap:15px;">
                            <span style="width: 30px; text-align:center; font-weight:bold;">${medal}</span>
                            ${renderUserName(item.name)}
                        </div>
                        <div style="font-size: 1.2rem; font-weight: bold; color: var(--warning);">
                            ${item.pts} <span style="font-size:0.9rem; color:var(--text-muted)">очков</span>
                        </div>
                    </div>
                `;
            });
        }

        // --- СОБЫТИЯ ---
        function addHoliday() {
            if(!currentUser) return showToast("Войдите в аккаунт", "error");
            const title = document.getElementById('newHolidayTitle').value.trim();
            const date = document.getElementById('newHolDate').value;
            if(!title || !date) return showToast("Заполните поля", "error");
            
            db.posts.push({ id: Date.now(), title, targetDate: new Date(date).getTime(), author: currentUser.username, likes: [], dislikes: [] });
            saveDB(); showToast("Опубликовано", "success"); navigate('home');
        }

        function renderHolidays(filter = "") {
            const list = document.getElementById('holidaysList'); list.innerHTML = '';
            let sorted = db.posts.filter(p => p.title.toLowerCase().includes(filter.toLowerCase()));
            sorted.sort((a,b) => a.targetDate - b.targetDate);

            sorted.forEach(p => {
                const l = p.likes ? p.likes.length : 0;
                list.innerHTML += `
                    <div class="card" onclick="openEvent(${p.id})" style="cursor:pointer;">
                        <div class="card-header">${p.title}</div>
                        <p style="font-size:0.9rem; margin-bottom:10px; color:var(--text-muted);">
                            Автор: ${renderUserName(p.author)}
                        </p>
                        <div style="display:flex; justify-content:space-between; align-items:center; margin-top:auto;">
                            <span style="background:rgba(255,255,255,0.1); padding:5px 10px; border-radius:8px; font-size:0.8rem;">
                                📅 ${new Date(p.targetDate).toLocaleDateString()}
                            </span>
                            <span style="color:var(--success); font-weight:bold;">👍 ${l}</span>
                        </div>
                    </div>
                `;
            });
        }

        // --- ОТДЕЛЬНОЕ ОКНО СОБЫТИЯ (ФОТО-СТИЛЬ) ---
        window.openEvent = (id) => {
            const p = db.posts.find(x => x.id === id);
            if(!p) return;
            
            clearInterval(activeTimerInterval);
            navigate('event');
            const cont = document.getElementById('eventContainer');
            
            const l = p.likes ? p.likes.length : 0;
            const d = p.dislikes ? p.dislikes.length : 0;
            const myVote = !currentUser ? null : (p.likes.includes(currentUser.username) ? 'like' : (p.dislikes.includes(currentUser.username) ? 'dislike' : null));

            cont.innerHTML = `
                <div class="event-title">${p.title}</div>
                <div style="font-size:1.2rem; margin-bottom: 20px;">Создатель: ${renderUserName(p.author)}</div>
                
                <div class="timer-display" id="bigTimerBox">
                    <div class="timer-circle"><span class="val" id="t-d">0</span><span class="lbl">Дней</span></div>
                    <div class="timer-circle"><span class="val" id="t-h">0</span><span class="lbl">Часов</span></div>
                    <div class="timer-circle"><span class="val" id="t-m">0</span><span class="lbl">Минут</span></div>
                    <div class="timer-circle"><span class="val" id="t-s">0</span><span class="lbl">Секунд</span></div>
                </div>

                <div style="display:flex; justify-content:center; gap:20px; margin-top:40px;">
                    <button class="btn-primary" style="background:${myVote==='like'?'var(--success)':'rgba(255,255,255,0.1)'}" onclick="vote(${p.id}, 'like')">
                        👍 Лайк (${l})
                    </button>
                    <button class="btn-danger" style="background:${myVote==='dislike'?'var(--danger)':'rgba(255,255,255,0.1)'}" onclick="vote(${p.id}, 'dislike')">
                        👎 Дизлайк (${d})
                    </button>
                </div>
            `;

            activeTimerInterval = setInterval(() => {
                let diff = p.targetDate - Date.now();
                if(diff <= 0) {
                    document.getElementById('bigTimerBox').innerHTML = "<h2 style='color:var(--success); font-size:3rem;'>Событие наступило! 🎉</h2>";
                    clearInterval(activeTimerInterval); return;
                }
                document.getElementById('t-d').innerText = Math.floor(diff / 86400000);
                document.getElementById('t-h').innerText = String(Math.floor((diff % 86400000) / 3600000)).padStart(2,'0');
                document.getElementById('t-m').innerText = String(Math.floor((diff % 3600000) / 60000)).padStart(2,'0');
                document.getElementById('t-s').innerText = String(Math.floor((diff % 60000) / 1000)).padStart(2,'0');
            }, 1000);
        }

        window.vote = (id, type) => {
            if(!currentUser) return showToast("Войдите, чтобы голосовать!", "error");
            const p = db.posts.find(x => x.id === id);
            if(!p) return;

            // Удаляем старый голос
            p.likes = p.likes.filter(u => u !== currentUser.username);
            p.dislikes = p.dislikes.filter(u => u !== currentUser.username);

            // Добавляем новый если нужно
            if(type === 'like') p.likes.push(currentUser.username);
            if(type === 'dislike') p.dislikes.push(currentUser.username);
            
            saveDB(); updateUI(); // Обновляем стату (очки)
            openEvent(id); // Перерисовываем UI события
        }

        // --- НАСТРОЙКИ (ФОН) ---
        function changeBackground(val) {
            if(currentUser) { 
                currentUser.bg = val; saveDB(); updateUI(); showToast("Фон обновлен", "success"); 
            }
        }

        window.uploadBg = (event) => {
            const file = event.target.files[0];
            if(!file) return;
            if(file.size > 2 * 1024 * 1024) return showToast("Файл слишком большой! Максимум 2MB", "error");
            
            const reader = new FileReader();
            reader.onload = (e) => { changeBackground(e.target.result); };
            reader.readAsDataURL(file);
        }

        // --- АДМИНКА ---
        function renderAdmin() {
            if(!currentUser || currentUser.role !== 'admin') return;
            
            const uList = document.getElementById('adminUsersList'); uList.innerHTML = '';
            db.users.forEach(u => {
                uList.innerHTML += `
                    <tr>
                        <td>${u.username}</td>
                        <td><input type="text" value="${u.nameColor||''}" placeholder="red, #fff..." class="custom-input" style="padding:5px;" onchange="admSetColor('${u.username}', this.value)"></td>
                        <td>${u.role}</td>
                        <td><button class="btn-danger" onclick="alert('Упрощено для примера')">Бан</button></td>
                    </tr>
                `;
            });

            const bList = document.getElementById('adminBotsList'); bList.innerHTML = '';
            db.bots.forEach(b => {
                bList.innerHTML += `
                    <tr>
                        <td><input type="text" value="${b.username}" class="custom-input" style="padding:5px;" onchange="admRenameBot('${b.id}', this.value)"></td>
                        <td><input type="number" value="${b.points}" class="custom-input" style="padding:5px; width:80px;" onchange="admBotPts('${b.id}', this.value)"></td>
                        <td><button class="btn-danger" onclick="admDelBot('${b.id}')">Удалить</button></td>
                    </tr>
                `;
            });
        }

        window.admSetColor = (nick, color) => {
            const u = db.users.find(u => u.username === nick);
            if(u) { u.nameColor = color; saveDB(); showToast("Цвет изменен", "success"); renderAdmin(); }
        }
        window.addBot = () => {
            const name = document.getElementById('newBotName').value.trim();
            if(!name) return;
            db.bots.push({ id: 'bot_'+Date.now(), username: name, points: 0, isBot: true });
            saveDB(); renderAdmin(); document.getElementById('newBotName').value = '';
        }
        window.admRenameBot = (id, newName) => {
            const b = db.bots.find(x => x.id === id);
            if(b) { b.username = newName; saveDB(); showToast("Бот переименован", "success"); }
        }
        window.admBotPts = (id, pts) => {
            const b = db.bots.find(x => x.id === id);
            if(b) { b.points = parseInt(pts)||0; saveDB(); showToast("Очки обновлены", "success"); }
        }
        window.admDelBot = (id) => {
            db.bots = db.bots.filter(x => x.id !== id); saveDB(); renderAdmin();
        }

        // Бургер меню
        document.getElementById('burgerMenu').onclick = function() {
            this.classList.toggle('active'); document.getElementById('sidebar').classList.toggle('open');
        };

        // Старт
        initDB(); navigate('home');
    </script>
</body>
</html>
