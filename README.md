<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>testsalmis - Pro Edition</title>
    <script src="https://accounts.google.com/gsi/client" async defer></script>
    <style>
        :root {
            --bg-color: #0f172a;
            --card-bg: rgba(30, 41, 59, 0.6);
            --text-main: #f8fafc;
            --text-muted: #94a3b8;
            --accent: #3b82f6;
            --accent-hover: #2563eb;
            --danger: #ef4444;
            --success: #10b981;
            --warning: #f59e0b;
            --glass-border: rgba(255, 255, 255, 0.1);
            --neon-glow: 0 0 15px rgba(59, 130, 246, 0.5);
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

        ::-webkit-scrollbar { width: 8px; height: 8px; }
        ::-webkit-scrollbar-track { background: rgba(0, 0, 0, 0.2); }
        ::-webkit-scrollbar-thumb { background: var(--accent); border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: var(--accent-hover); }

        header {
            background: rgba(15, 23, 42, 0.7);
            backdrop-filter: blur(15px);
            -webkit-backdrop-filter: blur(15px);
            padding: 15px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            position: sticky;
            top: 0;
            z-index: 100;
            border-bottom: 1px solid var(--glass-border);
            box-shadow: 0 4px 30px rgba(0,0,0,0.5);
        }

        .logo { font-size: 1.8rem; font-weight: 800; background: linear-gradient(90deg, #3b82f6, #8b5cf6, #ec4899); -webkit-background-clip: text; -webkit-text-fill-color: transparent; cursor: pointer; text-shadow: var(--neon-glow); }

        .search-bar { display: flex; flex: 1; max-width: 500px; margin: 0 20px; }
        .search-bar input, .custom-input, select {
            width: 100%; padding: 12px 15px; border-radius: 12px; border: 1px solid var(--glass-border);
            background: rgba(0,0,0,0.4); color: white; outline: none; transition: 0.3s;
            backdrop-filter: blur(5px);
        }
        .search-bar input:focus, .custom-input:focus, select:focus { border-color: var(--accent); background: rgba(0,0,0,0.6); box-shadow: var(--neon-glow); }

        .burger { display: flex; flex-direction: column; gap: 6px; cursor: pointer; z-index: 1001; padding: 5px; }
        .burger span { width: 30px; height: 3px; background: white; transition: 0.3s; border-radius: 3px; box-shadow: 0 0 5px rgba(255,255,255,0.5); }
        .burger.active span:nth-child(1) { transform: translateY(9px) rotate(45deg); }
        .burger.active span:nth-child(2) { opacity: 0; }
        .burger.active span:nth-child(3) { transform: translateY(-9px) rotate(-45deg); }

        .sidebar {
            position: fixed; top: 0; right: -320px; width: 320px; height: 100vh;
            background: rgba(15, 23, 42, 0.85); backdrop-filter: blur(20px); -webkit-backdrop-filter: blur(20px);
            border-left: 1px solid var(--glass-border);
            box-shadow: -10px 0 30px rgba(0,0,0,0.7); transition: 0.4s cubic-bezier(0.4, 0, 0.2, 1);
            z-index: 1000; padding: 80px 20px 20px; display: flex; flex-direction: column; gap: 10px; overflow-y: auto;
        }
        .sidebar.open { right: 0; }
        .sidebar button {
            background: transparent; border: 1px solid transparent; color: var(--text-main);
            padding: 14px 15px; border-radius: 12px; cursor: pointer; transition: 0.3s; text-align: left; font-size: 1.05rem;
            display: flex; align-items: center; gap: 12px; font-weight: 500;
        }
        .sidebar button:hover { background: rgba(255,255,255,0.1); border-color: var(--glass-border); transform: translateX(5px); text-shadow: 0 0 8px rgba(255,255,255,0.4); }

        main { padding: 30px 20px; max-width: 1200px; margin: 0 auto; width: 100%; flex: 1; }
        .view { display: none; }
        .view.active { display: block; animation: fadeIn 0.4s ease forwards; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        .user-name-container { display: inline-flex; align-items: center; justify-content: flex-start; gap: 6px; cursor: pointer; transition: 0.2s; vertical-align: middle; }
        .user-name-container:hover { opacity: 0.8; }
        .badge { display: inline-flex; align-items: center; justify-content: center; width: 16px; height: 16px; border-radius: 50%; font-size: 10px; color: white; flex-shrink: 0; }
        .badge-blue { background: #3b82f6; box-shadow: 0 0 8px rgba(59,130,246,0.8); }

        .nick-rainbow { background: linear-gradient(90deg, #ff0000, #ff7f00, #ffff00, #00ff00, #0000ff, #4b0082, #8b00ff); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: bold; }
        .nick-yellow { color: #facc15; font-weight: bold; text-shadow: 0 0 5px rgba(250, 204, 21, 0.5); }
        .nick-pink { color: #ec4899; font-weight: bold; text-shadow: 0 0 5px rgba(236, 72, 153, 0.5); }
        .nick-green { color: #10b981; font-weight: bold; text-shadow: 0 0 5px rgba(16, 185, 129, 0.5); }
        .nick-blue { color: #3b82f6; font-weight: bold; text-shadow: 0 0 5px rgba(59, 130, 246, 0.5); }
        .nick-white { color: #ffffff; font-weight: normal; }

        .holidays-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(320px, 1fr)); gap: 25px; margin-top: 25px; }

        .card {
            background: var(--card-bg); backdrop-filter: blur(12px); -webkit-backdrop-filter: blur(12px); border-radius: 20px; padding: 25px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3); border: 1px solid var(--glass-border);
            transition: 0.3s; position: relative; overflow: hidden; display: flex; flex-direction: column; cursor: pointer;
        }
        .card::before { content: ''; position: absolute; top: 0; left: -100%; width: 50%; height: 100%; background: linear-gradient(90deg, transparent, rgba(255,255,255,0.05), transparent); transform: skewX(-20deg); transition: 0.5s; }
        .card:hover::before { left: 150%; }
        .card:hover { transform: translateY(-8px); border-color: rgba(59, 130, 246, 0.6); box-shadow: 0 15px 40px rgba(59, 130, 246, 0.3); }
        .card h3 { margin-bottom: 12px; font-size: 1.4rem; transition: color 0.2s; user-select: none; }
        .card:hover h3 { color: var(--accent); }

        .btn-primary { background: linear-gradient(45deg, var(--accent), #6366f1); color: white; padding: 14px; border: none; border-radius: 12px; cursor: pointer; font-weight: bold; width: 100%; transition: 0.3s; box-shadow: 0 4px 15px rgba(59, 130, 246, 0.4); text-transform: uppercase; letter-spacing: 1px; }
        .btn-primary:hover { transform: scale(1.02); box-shadow: 0 6px 20px rgba(59, 130, 246, 0.6); }
        .btn-danger { background: linear-gradient(45deg, var(--danger), #be123c); color: white; padding: 10px 15px; border: none; border-radius: 8px; cursor: pointer; transition: 0.2s; box-shadow: 0 4px 10px rgba(239, 68, 68, 0.3); text-transform: uppercase; font-weight: bold;}
        .btn-danger:hover { opacity: 0.9; transform: scale(1.05); }
        .btn-success { background: linear-gradient(45deg, var(--success), #059669); color: white; padding: 10px 15px; border: none; border-radius: 8px; cursor: pointer; transition: 0.2s; box-shadow: 0 4px 10px rgba(16, 185, 129, 0.3); }
        .btn-success:hover { opacity: 0.9; transform: scale(1.05); }

        .single-event-timer { display: flex; flex-direction: column; align-items: center; gap: 30px; margin: 40px 0; background: rgba(255,255,255,0.02); padding: 40px; border-radius: 30px; border: 1px solid var(--glass-border); }
        .timer-row { display: flex; justify-content: center; gap: 20px; flex-wrap: wrap; }
        
        .timer-circle-photo {
            background: #ffffff; color: #000000; border-radius: 50%;
            display: flex; flex-direction: column; align-items: center; justify-content: center;
            position: relative; border: 5px solid transparent; border-top-color: #f59e0b; border-right-color: #f59e0b; border-left-color: #f59e0b;
            transform: rotate(-45deg); box-shadow: 0 10px 25px rgba(0,0,0,0.2);
        }
        .timer-inner { transform: rotate(45deg); display: flex; flex-direction: column; align-items: center; justify-content: center; width: 100%; height: 100%; }
        .timer-circle-photo.large { width: 180px; height: 180px; border-width: 8px; }
        .timer-circle-photo.large .val { font-size: 4rem; font-weight: 900; line-height: 1; letter-spacing: -2px; }
        .timer-circle-photo.large .lbl { font-size: 1rem; color: #555; text-transform: uppercase; font-weight: 700; margin-top: 5px; }
        .timer-circle-photo.small { width: 120px; height: 120px; border-width: 5px; }
        .timer-circle-photo.small .val { font-size: 2.5rem; font-weight: 900; line-height: 1; letter-spacing: -1px; }
        .timer-circle-photo.small .lbl { font-size: 0.65rem; color: #555; text-transform: uppercase; font-weight: 700; margin-top: 5px; }

        .rating-box { display: flex; gap: 15px; margin-top: 20px; }
        .rate-btn { background: rgba(0,0,0,0.4); border: 1px solid var(--glass-border); color: white; padding: 10px 20px; border-radius: 20px; cursor: pointer; display: flex; align-items: center; gap: 8px; font-size: 1.1rem; transition: 0.2s; }
        .rate-btn:hover { background: rgba(255,255,255,0.1); transform: translateY(-2px); }
        .rate-btn.active-like { background: rgba(16, 185, 129, 0.2); border-color: var(--success); color: var(--success); }
        .rate-btn.active-dislike { background: rgba(239, 68, 68, 0.2); border-color: var(--danger); color: var(--danger); }

        .admin-table { width: 100%; border-collapse: collapse; margin-top: 15px; background: rgba(0,0,0,0.4); border-radius: 15px; overflow: hidden; backdrop-filter: blur(10px); border: 1px solid var(--glass-border); }
        .admin-table th, .admin-table td { padding: 15px; text-align: left; border-bottom: 1px solid var(--glass-border); vertical-align: middle;}
        .admin-table th { background: rgba(255,255,255,0.05); font-weight: 600; color: var(--accent); text-transform: uppercase; font-size: 0.9rem; }
        .admin-table tr:hover td { background: rgba(255,255,255,0.02); }

        #toastContainer { position: fixed; bottom: 30px; right: 30px; display: flex; flex-direction: column; gap: 10px; z-index: 9999; }
        .toast { background: rgba(15, 23, 42, 0.95); backdrop-filter: blur(10px); color: white; padding: 15px 25px; border-radius: 12px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); border-left: 4px solid var(--accent); animation: slideIn 0.3s ease forwards; display: flex; align-items: center; gap: 15px; min-width: 300px; font-weight: 500; }
        @keyframes slideIn { from { transform: translateX(100%) scale(0.9); opacity: 0; } to { transform: translateX(0) scale(1); opacity: 1; } }

        .achievement { display: flex; align-items: center; gap: 15px; background: rgba(0,0,0,0.4); padding: 15px; border-radius: 15px; margin-bottom: 10px; border: 1px solid var(--glass-border); transition: 0.3s; }
        .achievement:hover { background: rgba(255,255,255,0.05); transform: translateX(5px); }
        .achievement.locked { opacity: 0.4; filter: grayscale(1); }
        .ach-icon { font-size: 2.2rem; filter: drop-shadow(0 0 5px rgba(255,255,255,0.3)); }

        .form-group { margin-bottom: 20px; }
        .form-group label { display: block; margin-bottom: 8px; color: var(--text-muted); font-size: 0.95rem; font-weight: 500; }
        .form-row { display: flex; gap: 10px; }
        .form-row > div { flex: 1; }

        /* Контейнер для настоящей кнопки Google */
        .google-btn-container { margin-top: 15px; display: flex; justify-content: center; }

        @media (max-width: 600px) {
            .search-bar { display: none; }
            .holidays-grid { grid-template-columns: 1fr; }
            .timer-circle-photo.large { width: 140px; height: 140px; }
            .timer-circle-photo.large .val { font-size: 3rem; }
            .timer-circle-photo.small { width: 80px; height: 80px; border-width: 3px; }
            .timer-circle-photo.small .val { font-size: 1.5rem; }
            .timer-circle-photo.small .lbl { font-size: 0.5rem; }
            .single-event-timer { padding: 20px; gap: 15px; }
            .timer-row { gap: 10px; }
        }
    </style>
</head>
<body>

    <div id="g_id_onload"
         data-client_id="ТВОЙ_GOOGLE_CLIENT_ID" 
         data-context="signin"
         data-ux_mode="popup"
         data-callback="handleGoogleLogin"
         data-auto_prompt="false">
    </div>

    <div id="toastContainer"></div>

    <header>
        <div class="logo" onclick="navigate('home')">testsalmis</div>
        <div class="search-bar">
            <input type="text" id="searchInput" placeholder="Поиск событий..." oninput="renderHolidays(this.value)">
        </div>
        <div class="burger" id="burgerMenu"><span></span><span></span><span></span></div>
    </header>

    <div class="sidebar" id="sidebar">
        <div id="sidebarUserInfo" style="margin-bottom: 15px; padding: 20px; border-bottom: 1px solid var(--glass-border); background: rgba(0,0,0,0.3); border-radius: 15px; box-shadow: inset 0 0 10px rgba(0,0,0,0.5);"></div>
        
        <input type="text" id="searchInputMobile" class="custom-input" style="display:none; margin-bottom: 15px;" placeholder="Поиск..." oninput="renderHolidays(this.value)">

        <button onclick="navigate('home')">🏠 Главная</button>
        <button onclick="navigate('leaderboard')">🏆 Таблица лидеров (Очки)</button>
        <button id="navAddBtn" style="display:none;" onclick="navigate('add')">➕ Создать событие</button>
        <button id="navLoginBtn" onclick="navigate('auth')">🔑 Вход / Регистрация</button>
        <button id="navProfileBtn" style="display:none;" onclick="openPublicProfile(currentUser.username)">👤 Мой профиль</button>
        <button id="navSettingsBtn" style="display:none;" onclick="navigate('settings')">⚙️ Настройки</button>
        <button id="navDialogsBtn" style="display:none;" onclick="navigate('dialogs')">💬 Диалоги</button>
        <button id="navModsBtn" style="display:none;" onclick="navigate('mods')">🛡️ Модераторы</button>
        <button id="navVerifyBtn" style="display:none;" onclick="navigate('verify')">✅ Верификация</button>
        <button id="navAdminBtn" style="display:none; color: var(--warning);" onclick="navigate('admin')">👑 Админ панель</button>
    </div>

    <main>
        <div id="view-home" class="view active">
            <h2>🎉 Актуальные события</h2>
            <div class="holidays-grid" id="holidaysList"></div>
        </div>

        <div id="view-event" class="view">
            <button onclick="navigate('home')" class="btn-primary" style="width:auto; padding: 10px 20px; margin-bottom: 20px; background: rgba(255,255,255,0.1); border: 1px solid var(--glass-border); box-shadow: none;">← Назад</button>
            <div style="background: var(--card-bg); padding: 40px; border-radius: 30px; border: 1px solid var(--glass-border); box-shadow: 0 20px 50px rgba(0,0,0,0.5);">
                <h1 id="singleEventTitle" style="font-size: 2.5rem; text-align: center; margin-bottom: 10px; text-shadow: var(--neon-glow);"></h1>
                <div style="text-align: center; margin-bottom: 20px;">
                    <span style="color: var(--text-muted);">Автор:</span> <span id="singleEventAuthor"></span>
                </div>
                
                <div style="text-align: center; font-size: 1.2rem; color: var(--text-muted); margin-bottom: 10px;" id="singleEventDateStr"></div>

                <div class="single-event-timer">
                    <div class="timer-row">
                        <div class="timer-circle-photo large">
                            <div class="timer-inner"><span class="val" id="se-d">0</span><span class="lbl">Дня</span></div>
                        </div>
                    </div>
                    <div class="timer-row">
                        <div class="timer-circle-photo small">
                            <div class="timer-inner"><span class="val" id="se-h">0</span><span class="lbl">Часов</span></div>
                        </div>
                        <div class="timer-circle-photo small">
                            <div class="timer-inner"><span class="val" id="se-m">0</span><span class="lbl">Минут</span></div>
                        </div>
                        <div class="timer-circle-photo small">
                            <div class="timer-inner"><span class="val" id="se-s">0</span><span class="lbl">Секунды</span></div>
                        </div>
                        <div class="timer-circle-photo small" style="border-color: #f59e0b transparent #f59e0b #f59e0b;">
                            <div class="timer-inner"><span class="val" id="se-ms">000</span><span class="lbl">Миллисекунды</span></div>
                        </div>
                    </div>
                </div>

                <div class="rating-box" style="justify-content: center;">
                    <button class="rate-btn" id="btnLike" onclick="rateEvent(true)">👍 <span id="valLikes">0</span></button>
                    <button class="rate-btn" id="btnDislike" onclick="rateEvent(false)">👎 <span id="valDislikes">0</span></button>
                </div>
                <p style="text-align: center; margin-top: 15px; color: var(--text-muted); font-size: 0.9rem;">1 лайк = 5 очков автору в Лидерборде!</p>
            </div>
        </div>

        <div id="view-leaderboard" class="view">
            <h2>🏆 Таблица лидеров (Топ-20)</h2>
            <p style="color: var(--text-muted); margin-bottom: 20px;">Зарабатывайте очки получая лайки на свои события!</p>
            <div style="background: var(--card-bg); border-radius: 20px; padding: 20px; border: 1px solid var(--glass-border);">
                <table class="admin-table" style="margin-top: 0;">
                    <thead><tr><th style="width: 50px;">Место</th><th>Пользователь</th><th>Очки</th></tr></thead>
                    <tbody id="leaderboardList"></tbody>
                </table>
            </div>
        </div>

        <div id="view-public-profile" class="view">
            <button onclick="navigate('home')" class="btn-primary" style="width:auto; padding: 10px 20px; margin-bottom: 20px; background: rgba(255,255,255,0.1); border: 1px solid var(--glass-border); box-shadow: none;">← Назад</button>
            
            <div style="display: flex; flex-wrap: wrap; gap: 20px; margin-bottom: 30px;">
                <div style="flex: 1; min-width: 300px; background: var(--card-bg); padding: 30px; border-radius: 20px; border: 1px solid var(--glass-border); text-align: center;">
                    <div style="font-size: 4rem; margin-bottom: 10px;">👤</div>
                    <h2 id="pubProfName" style="margin-bottom: 5px; justify-content: center; font-size: 2rem;">Имя</h2>
                    <p id="pubProfRole" style="color: var(--accent); margin-bottom: 15px; font-weight: bold;"></p>
                    <div style="display: flex; justify-content: center; gap: 20px; margin-top: 20px; background: rgba(0,0,0,0.3); padding: 15px; border-radius: 15px;">
                        <div><div style="font-size: 1.5rem; font-weight: bold; color: var(--warning);" id="pubProfPoints">0</div><div style="font-size: 0.8rem; color: var(--text-muted);">Очков</div></div>
                        <div><div style="font-size: 1.5rem; font-weight: bold;" id="pubProfPosts">0</div><div style="font-size: 0.8rem; color: var(--text-muted);">Событий</div></div>
                    </div>
                </div>
                <div style="flex: 1; min-width: 300px; background: var(--card-bg); padding: 30px; border-radius: 20px; border: 1px solid var(--glass-border);">
                    <h3>🏆 Достижения</h3>
                    <div id="pubProfAchieve" style="margin-top: 15px; max-height: 250px; overflow-y: auto;"></div>
                </div>
            </div>
            <h3>Публикации пользователя:</h3>
            <div class="holidays-grid" id="pubProfHolidays"></div>
        </div>

        <div id="view-auth" class="view">
            <div style="max-width: 450px; margin: 40px auto; background: var(--card-bg); padding: 40px; border-radius: 25px; border: 1px solid var(--glass-border); box-shadow: 0 20px 50px rgba(0,0,0,0.5); backdrop-filter: blur(20px);">
                <h2 style="margin-bottom: 25px; text-align: center; font-size: 2rem; text-shadow: var(--neon-glow);">Авторизация</h2>
                <div class="form-group">
                    <label>Логин (без мата)</label>
                    <input type="text" id="authUsername" class="custom-input" placeholder="Введите логин">
                </div>
                <div class="form-group">
                    <label>Пароль</label>
                    <input type="password" id="authPassword" class="custom-input" placeholder="Введите пароль">
                </div>
                <div class="form-group">
                    <label>День Рождения (для регистрации)</label>
                    <div class="form-row">
                        <div>
                            <select id="authMonth" class="custom-input">
                                <option value="">Месяц</option>
                                <option value="01">Январь</option><option value="02">Февраль</option><option value="03">Март</option>
                                <option value="04">Апрель</option><option value="05">Май</option><option value="06">Июнь</option>
                                <option value="07">Июль</option><option value="08">Август</option><option value="09">Сентябрь</option>
                                <option value="10">Октябрь</option><option value="11">Ноябрь</option><option value="12">Декабрь</option>
                            </select>
                        </div>
                        <div><select id="authDay" class="custom-input"><option value="">День</option></select></div>
                    </div>
                </div>
                <button onclick="handleAuth()" class="btn-primary">Войти / Создать аккаунт</button>
                
                <div style="text-align: center; margin: 20px 0; color: var(--text-muted); position: relative;">
                    <hr style="border: none; border-top: 1px solid var(--glass-border); position: absolute; width: 100%; top: 50%;">
                    <span style="background: var(--card-bg); padding: 0 10px; position: relative; font-size: 0.9rem;">ИЛИ</span>
                </div>

                <div class="google-btn-container">
                    <div class="g_id_signin" data-type="standard" data-shape="rectangular" data-theme="outline" data-text="signin_with" data-size="large" data-logo_alignment="left"></div>
                </div>
                <p style="text-align:center; font-size: 0.75rem; color: var(--text-muted); margin-top: 5px;">(Требуется настроенный Client ID)</p>
            </div>
        </div>

        <div id="view-add" class="view">
            <div style="max-width: 500px; margin: 40px auto; background: var(--card-bg); padding: 40px; border-radius: 20px; border: 1px solid var(--glass-border);">
                <h2 style="margin-bottom: 25px;">Создать событие</h2>
                <div class="form-group">
                    <label>Название события</label>
                    <input type="text" id="newHolidayTitle" class="custom-input" placeholder="Без мата">
                </div>
                <div class="form-group">
                    <label>Дата (Без года, авто-отсчет)</label>
                    <div class="form-row">
                        <div>
                            <select id="newHolMonth" class="custom-input">
                                <option value="01">Январь</option><option value="02">Февраль</option><option value="03">Март</option>
                                <option value="04">Апрель</option><option value="05">Май</option><option value="06">Июнь</option>
                                <option value="07">Июль</option><option value="08">Август</option><option value="09">Сентябрь</option>
                                <option value="10">Октябрь</option><option value="11">Ноябрь</option><option value="12">Декабрь</option>
                            </select>
                        </div>
                        <div><select id="newHolDay" class="custom-input"></select></div>
                    </div>
                </div>
                <div class="form-group">
                    <label>Время</label>
                    <input type="time" id="newHolTime" class="custom-input" value="00:00">
                </div>
                <button onclick="addHoliday()" class="btn-success" style="width: 100%; padding: 14px; font-size: 1.1rem;">🚀 Опубликовать</button>
            </div>
        </div>

        <div id="view-settings" class="view">
            <div style="max-width: 600px; background: var(--card-bg); padding: 40px; border-radius: 20px; border: 1px solid var(--glass-border);">
                <h2 style="margin-bottom: 25px;">⚙️ Настройки аккаунта</h2>

                <div class="form-group">
                    <label>Смена фона</label>
                    <div style="display: flex; gap: 10px; margin-bottom: 10px;">
                        <button onclick="changeBackground('#0f172a')" class="btn-primary" style="flex: 1; background: #0f172a; border: 1px solid #333;">Темный (Дефолт)</button>
                        <button onclick="changeBackground('#000000')" class="btn-primary" style="flex: 1; background: #000000; border: 1px solid #333;">Черный</button>
                    </div>
                    
                    <label style="margin-top: 15px;">Загрузить фото (Файл):</label>
                    <input type="file" id="bgFileInput" accept="image/*" class="custom-input" style="padding: 8px; margin-bottom: 10px;" onchange="handleBgUpload(event)">
                    
                    <label>Или укажите ссылку (URL):</label>
                    <input type="text" id="setBg" class="custom-input" placeholder="https://...">
                    <button onclick="changeBackground(document.getElementById('setBg').value)" class="btn-primary" style="margin-top: 10px; width: auto;">Применить URL</button>
                </div>

                <div class="form-group" style="margin-top: 30px; border-top: 1px solid var(--glass-border); padding-top: 20px;">
                    <label>Активация промокода (Для доступа к админке и др.)</label>
                    <input type="text" id="promoCodeInput" class="custom-input" placeholder="Введите промокод">
                    <button onclick="applyPromoCode()" class="btn-success" style="margin-top: 10px; width: auto;">Активировать</button>
                </div>

                <div class="form-group" style="margin-top: 30px; border-top: 1px solid var(--glass-border); padding-top: 20px;">
                    <label>Смена пароля</label>
                    <input type="password" id="setPass" class="custom-input">
                    <button onclick="changePassword()" class="btn-primary" style="margin-top: 10px; width: auto;">Изменить пароль</button>
                </div>

                <div class="form-group" style="margin-top: 40px; border-top: 1px solid var(--glass-border); padding-top: 20px;">
                    <button onclick="logout()" class="btn-danger" style="width: 100%; padding: 15px; font-size: 1.1rem; border-radius: 12px; display: flex; align-items: center; justify-content: center; gap: 10px;">
                        <span>🚪</span> Выйти из аккаунта
                    </button>
                </div>
            </div>
        </div>

        <div id="view-admin" class="view">
            <h2 style="color: var(--warning); text-shadow: 0 0 10px rgba(245, 158, 11, 0.5);">👑 Админ-панель</h2>
            
            <div style="background: var(--card-bg); padding: 25px; border-radius: 20px; border: 1px solid var(--glass-border); margin: 20px 0;">
                <h3>🤖 Управление Ботами</h3>
                <p style="color: var(--text-muted); font-size: 0.9rem; margin-bottom: 15px;">Изменяйте ники ботов для лидерборда.</p>
                <div style="overflow-x: auto;">
                    <table class="admin-table">
                        <thead><tr><th>Текущий Ник</th><th>Новый Ник</th><th>Действие</th></tr></thead>
                        <tbody id="adminBotsList"></tbody>
                    </table>
                </div>
            </div>

            <div style="background: var(--card-bg); padding: 25px; border-radius: 20px; border: 1px solid var(--glass-border); margin: 20px 0;">
                <h3>🛡️ Пользователи (Цвета, Галочки, Роли)</h3>
                <div style="overflow-x: auto;">
                    <table class="admin-table">
                        <thead><tr><th>Никнейм</th><th>Цвет Ника</th><th>Галочка</th><th>Роль</th><th>Бан</th></tr></thead>
                        <tbody id="adminUsersList"></tbody>
                    </table>
                </div>
            </div>

            <div style="background: var(--card-bg); padding: 25px; border-radius: 20px; border: 1px solid var(--glass-border);">
                <h3>📢 Управление постами</h3>
                <div style="overflow-x: auto;">
                    <table class="admin-table">
                        <thead><tr><th>Заголовок</th><th>Автор</th><th>Статус</th><th>Действия</th></tr></thead>
                        <tbody id="adminPostsList"></tbody>
                    </table>
                </div>
            </div>
        </div>

        <div id="view-verify" class="view">
            <h2>✅ Верификация</h2>
            <p>Пройдите верификацию для получения синей галочки.</p>
            <button class="btn-primary" onclick="showToast('Заявка отправлена (Демо)', 'success')" style="max-width: 200px; margin-top: 20px;">Отправить заявку</button>
        </div>
        <div id="view-mods" class="view"><h2>🛡️ Модераторы</h2><div id="modsList"></div></div>
        <div id="view-dialogs" class="view"><h2>💬 Диалоги (В разработке)</h2></div>

    </main>

    <script>
        // --- БАЗА ДАННЫХ ---
        let db = { users: [], posts: [] };
        let currentUser = null;
        let currentEventId = null;
        let timerAnimFrame = null;
        const BAD_WORDS = ['мат', 'сука', 'блять', 'хуй', 'пизда', 'еблан'];

        const COLORS = [
            {val: 'white', label: 'Стандарт (Белый)'},
            {val: 'rainbow', label: 'Радужный (Градиент)'},
            {val: 'yellow', label: 'Желтый (Неон)'},
            {val: 'pink', label: 'Розовый (Неон)'},
            {val: 'green', label: 'Зеленый (Неон)'},
            {val: 'blue', label: 'Синий (Неон)'}
        ];

        function initDB() {
            const stored = localStorage.getItem('testsalmisProDB');
            if(stored) {
                db = JSON.parse(stored);
                // Если база загрузилась, но нужно обнулить старых админов как просили в задании:
                db.users.forEach(u => {
                    if(!u.isBot && (u.role === 'admin' || u.role === 'moderator')) {
                        // Сбрасываем роли у всех, чтобы проверить систему промокодов
                        u.role = 'user'; 
                    }
                });
            }
            
            if(!db.users) db.users = [];
            if(!db.posts) db.posts = [];

            // Создаем ботов с НУЛЕВЫМИ очками
            if(!db.users.find(u => u.isBot)) {
                db.users.push({ username: 'Kwent Бот', isBot: true, points: 0, role: 'user', nameColor: 'green', verified: false });
                db.users.push({ username: 'Glebchek Бот', isBot: true, points: 0, role: 'user', nameColor: 'pink', verified: false });
                db.users.push({ username: 'Тестер Бот', isBot: true, points: 0, role: 'user', nameColor: 'white', verified: false });
            }

            // Миграция данных
            db.users.forEach(u => { 
                if(u.points === undefined) u.points = 0; 
                // Принудительно ставим всем 0 очков для старта (по заданию "сделай у всех сначала 0 очков")
                if(!stored) u.points = 0; 
                if(!u.nameColor) u.nameColor = 'white'; 
                if(u.verified === undefined) u.verified = false;
            });
            db.posts.forEach(p => { if(!p.likes) p.likes = 0; if(!p.dislikes) p.dislikes = 0; if(!p.likedBy) p.likedBy = []; if(!p.dislikedBy) p.dislikedBy = []; });

            saveDB();
            
            populateDaysSelect('authMonth', 'authDay');
            populateDaysSelect('newHolMonth', 'newHolDay');
            document.getElementById('authMonth').addEventListener('change', () => populateDaysSelect('authMonth', 'authDay'));
            document.getElementById('newHolMonth').addEventListener('change', () => populateDaysSelect('newHolMonth', 'newHolDay'));
            
            checkAutoLogin();
        }

        function saveDB() { localStorage.setItem('testsalmisProDB', JSON.stringify(db)); }

        // --- УТИЛИТЫ ---
        function populateDaysSelect(monthId, dayId) {
            const m = document.getElementById(monthId).value;
            const dSel = document.getElementById(dayId);
            dSel.innerHTML = '<option value="">День</option>';
            if(!m) return;
            const daysInMonth = new Date(2024, parseInt(m), 0).getDate();
            for(let i=1; i<=daysInMonth; i++) {
                const val = String(i).padStart(2, '0');
                dSel.innerHTML += `<option value="${val}">${val}</option>`;
            }
        }

        function showToast(msg, type = 'info') {
            const container = document.getElementById('toastContainer');
            const toast = document.createElement('div');
            toast.className = 'toast';
            toast.style.borderLeftColor = type === 'error' ? 'var(--danger)' : type === 'success' ? 'var(--success)' : 'var(--accent)';
            toast.innerHTML = `<span style="font-size:1.5rem">${type === 'error' ? '❌' : type === 'success' ? '✅' : 'ℹ️'}</span> ${msg}`;
            container.appendChild(toast);
            setTimeout(() => {
                toast.style.animation = 'slideIn 0.3s ease reverse forwards';
                setTimeout(() => toast.remove(), 300);
            }, 3000);
        }

        function getUserHTML(username) {
            const u = db.users.find(x => x.username === username);
            if(!u) return `<span class="nick-white">${username}</span>`;
            
            let badge = '';
            if(u.verified || u.role === 'admin' || u.role === 'moderator') {
                badge = `<span class="badge badge-blue" title="Верифицирован">✔</span>`;
            }
            if(u.isBot) badge += `<span class="badge" style="background:#64748b; margin-left:4px;" title="Бот">🤖</span>`;
            
            return `
                <div class="user-name-container" onclick="event.stopPropagation(); openPublicProfile('${u.username}')">
                    <span class="nick-${u.nameColor}">${u.username}</span>
                    ${badge}
                </div>
            `;
        }

        function getNextOccurrence(month, day, time = "00:00") {
            const now = new Date();
            let year = now.getFullYear();
            let target = new Date(`${year}-${month}-${day}T${time}:00`);
            if(target < now) target.setFullYear(year + 1);
            return target.toISOString();
        }

        function navigate(viewId) {
            document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
            const target = document.getElementById(`view-${viewId}`);
            if(target) target.classList.add('active');
            
            if(viewId === 'home') renderHolidays();
            if(viewId === 'leaderboard') renderLeaderboard();
            if(viewId === 'admin') renderAdmin();
            if(viewId === 'settings' && currentUser) document.getElementById('setBg').value = currentUser.bg && !currentUser.bg.startsWith('data:') ? currentUser.bg : '';
            
            updateUI();
            document.getElementById('sidebar').classList.remove('open');
            document.getElementById('burgerMenu').classList.remove('active');
            
            if(timerAnimFrame) cancelAnimationFrame(timerAnimFrame); 
        }

        function updateUI() {
            const sbInfo = document.getElementById('sidebarUserInfo');
            if(currentUser) {
                if(currentUser.bg) {
                    document.body.style.backgroundImage = currentUser.bg.startsWith('#') ? 'none' : `url('${currentUser.bg}')`;
                    document.body.style.backgroundColor = currentUser.bg.startsWith('#') ? currentUser.bg : 'var(--bg-color)';
                } else {
                    document.body.style.backgroundImage = 'none';
                    document.body.style.backgroundColor = 'var(--bg-color)';
                }
                
                sbInfo.innerHTML = `Привет, ${getUserHTML(currentUser.username)}<br><small style="color:var(--accent)">Очки: ${currentUser.points} | ${currentUser.role || 'User'}</small>`;
                ['navLoginBtn'].forEach(id => document.getElementById(id).style.display = 'none');
                ['navLogoutBtn', 'navProfileBtn', 'navAddBtn', 'navSettingsBtn', 'navVerifyBtn'].forEach(id => document.getElementById(id).style.display = 'flex');
                
                document.getElementById('navAdminBtn').style.display = (currentUser.role === 'admin') ? 'flex' : 'none';
            } else {
                document.body.style.backgroundImage = 'none';
                document.body.style.backgroundColor = 'var(--bg-color)';
                sbInfo.innerHTML = `Вы не вошли в систему`;
                ['navLoginBtn'].forEach(id => document.getElementById(id).style.display = 'flex');
                ['navLogoutBtn', 'navProfileBtn', 'navAddBtn', 'navSettingsBtn', 'navVerifyBtn', 'navAdminBtn'].forEach(id => document.getElementById(id).style.display = 'none');
            }
        }

        // --- АВТОРИЗАЦИЯ (Обычная и Google) ---
        function checkAutoLogin() {
            const storedUser = localStorage.getItem('testsalmisProCurrentUser');
            if(storedUser) {
                const u = db.users.find(x => x.username === storedUser);
                if(u) currentUser = u;
            }
        }

        function handleAuth() {
            const u = document.getElementById('authUsername').value.trim();
            const p = document.getElementById('authPassword').value.trim();
            if(!u || !p) return showToast("Введите логин и пароль", "error");
            if(BAD_WORDS.some(w => u.toLowerCase().includes(w))) return showToast("Недопустимый никнейм", "error");

            let user = db.users.find(x => x.username.toLowerCase() === u.toLowerCase());
            if(user) {
                if(user.password !== p) return showToast("Неверный пароль", "error");
                authSuccess(user);
            } else {
                const m = document.getElementById('authMonth').value;
                const d = document.getElementById('authDay').value;
                if(!m || !d) return showToast("Для регистрации укажите ДР!", "error");
                user = { username: u, password: p, role: 'user', points: 0, nameColor: 'white', isBot: false, verified: false, createdAt: Date.now() };
                db.users.push(user); saveDB(); authSuccess(user);
            }
        }

        // Обработчик ответа от Google (ПАРСИНГ JWT ТОКЕНА)
        window.handleGoogleLogin = (response) => {
            const responsePayload = parseJwt(response.credential);
            
            // Берем имя пользователя из Google (первая часть почты или полное имя)
            let gName = responsePayload.name || responsePayload.email.split('@')[0];
            
            // Проверяем, есть ли такой пользователь
            let user = db.users.find(x => x.username.toLowerCase() === gName.toLowerCase());
            
            if(!user) {
                // Регистрируем нового пользователя через Google
                user = { 
                    username: gName, 
                    password: "google_oauth_protected", // Заглушка, пароль не нужен для входа по гуглу
                    role: 'user', points: 0, nameColor: 'white', isBot: false, verified: false, 
                    createdAt: Date.now(), googleId: responsePayload.sub 
                };
                db.users.push(user);
                saveDB();
            }
            authSuccess(user);
            showToast("Успешный вход через Google!", "success");
        };

        // Вспомогательная функция декодирования JWT (стандартный подход для фронтенда)
        function parseJwt (token) {
            var base64Url = token.split('.')[1];
            var base64 = base64Url.replace(/-/g, '+').replace(/_/g, '/');
            var jsonPayload = decodeURIComponent(window.atob(base64).split('').map(function(c) {
                return '%' + ('00' + c.charCodeAt(0).toString(16)).slice(-2);
            }).join(''));
            return JSON.parse(jsonPayload);
        }

        function authSuccess(user) {
            currentUser = user;
            localStorage.setItem('testsalmisProCurrentUser', user.username);
            navigate('home'); showToast(`Добро пожаловать, ${user.username}!`, 'success');
        }

        function logout() { 
            currentUser = null; localStorage.removeItem('testsalmisProCurrentUser');
            navigate('home'); showToast("Вы вышли из аккаунта", "success");
        }

        // --- ПРОМОКОДЫ ---
        function applyPromoCode() {
            if(!currentUser) return;
            const code = document.getElementById('promoCodeInput').value.trim();
            if(!code) return;

            if(code === 'adminuser' || code.toLowerCase() === 'кэря') {
                currentUser.role = 'admin';
                saveDB();
                updateUI();
                showToast("Kwent, промокод активирован! Вы теперь Администратор.", "success");
                document.getElementById('promoCodeInput').value = '';
            } else {
                showToast("Неверный промокод", "error");
            }
        }

        // --- ФОН ---
        window.handleBgUpload = (e) => {
            const file = e.target.files[0];
            if(file) {
                const reader = new FileReader();
                reader.onload = (ev) => { changeBackground(ev.target.result); };
                reader.readAsDataURL(file);
            }
        };
        function changeBackground(val) {
            if(currentUser) { currentUser.bg = val; saveDB(); updateUI(); showToast("Фон обновлен", "success"); }
        }

        // --- СОБЫТИЯ И ЛАЙКИ ---
        function addHoliday() {
            if(!currentUser) return showToast("Войдите в аккаунт", "error");
            const title = document.getElementById('newHolidayTitle').value.trim();
            const m = document.getElementById('newHolMonth').value;
            const d = document.getElementById('newHolDay').value;
            const t = document.getElementById('newHolTime').value;

            if(!title || !m || !d) return showToast("Заполните все поля", "error");
            const targetDate = getNextOccurrence(m, d, t);

            db.posts.push({ id: Date.now(), title, targetDate, author: currentUser.username, isBanned: false, likes: 0, dislikes: 0, likedBy: [], dislikedBy: [] });
            saveDB(); showToast("Событие опубликовано!", "success"); navigate('home');
        }

        function renderHolidays(filter = "") {
            const list = document.getElementById('holidaysList'); list.innerHTML = '';
            let sorted = db.posts.filter(p => !p.isBanned && p.title.toLowerCase().includes(filter.toLowerCase()));
            sorted.sort((a,b) => new Date(a.targetDate) - new Date(b.targetDate));

            sorted.forEach(p => {
                const d = new Date(p.targetDate);
                const dateStr = `${String(d.getDate()).padStart(2,'0')}.${String(d.getMonth()+1).padStart(2,'0')}.${d.getFullYear()}`;
                list.innerHTML += `
                    <div class="card" onclick="openSingleEvent(${p.id})">
                        <h3>⏱️ ${p.title}</h3>
                        <div style="margin-bottom: 10px;">${getUserHTML(p.author)}</div>
                        <div style="font-size: 0.9rem; color: var(--text-muted); margin-bottom: 10px;">📅 До: ${dateStr}</div>
                        <div style="display:flex; gap:15px; font-size:0.9rem; color:var(--text-muted);">
                            <span>👍 ${p.likes}</span> <span>👎 ${p.dislikes}</span>
                        </div>
                    </div>
                `;
            });
        }

        // --- ПРОСМОТР ОДНОГО СОБЫТИЯ (С ТАЙМЕРОМ) ---
        window.openSingleEvent = (id) => {
            const p = db.posts.find(x => x.id === id);
            if(!p) return;
            currentEventId = id;
            navigate('event');

            document.getElementById('singleEventTitle').innerText = p.title;
            document.getElementById('singleEventAuthor').innerHTML = getUserHTML(p.author);
            
            const d = new Date(p.targetDate);
            document.getElementById('singleEventDateStr').innerText = `Окончание отсчёта: ${d.toLocaleString('ru-RU', {weekday: 'long', year: 'numeric', month: 'long', day: 'numeric'})} г. ${String(d.getHours()).padStart(2,'0')}:${String(d.getMinutes()).padStart(2,'0')}`;

            updateRatingUI();
            startHighResTimer(p.targetDate);
        };

        function startHighResTimer(targetDateStr) {
            const target = new Date(targetDateStr).getTime();
            
            function update() {
                if(document.getElementById('view-event').classList.contains('active')) {
                    const now = Date.now();
                    const diff = target - now;

                    if(diff > 0) {
                        document.getElementById('se-d').innerText = Math.floor(diff / 86400000);
                        document.getElementById('se-h').innerText = Math.floor((diff % 86400000) / 3600000);
                        document.getElementById('se-m').innerText = Math.floor((diff % 3600000) / 60000);
                        document.getElementById('se-s').innerText = Math.floor((diff % 60000) / 1000);
                        document.getElementById('se-ms').innerText = String(Math.floor(diff % 1000)).padStart(3, '0');
                    } else {
                        document.getElementById('se-d').innerText = "0";
                        document.getElementById('se-h').innerText = "0";
                        document.getElementById('se-m').innerText = "0";
                        document.getElementById('se-s').innerText = "0";
                        document.getElementById('se-ms').innerText = "000";
                    }
                    timerAnimFrame = requestAnimationFrame(update);
                }
            }
            if(timerAnimFrame) cancelAnimationFrame(timerAnimFrame);
            timerAnimFrame = requestAnimationFrame(update);
        }

        // --- СИСТЕМА ОЧКОВ И ЛАЙКОВ ---
        window.rateEvent = (isLike) => {
            if(!currentUser) return showToast("Войдите чтобы голосовать", "error");
            const p = db.posts.find(x => x.id === currentEventId);
            if(!p) return;

            const hasLiked = p.likedBy.includes(currentUser.username);
            const hasDisliked = p.dislikedBy.includes(currentUser.username);

            if(isLike) {
                if(hasLiked) return showToast("Вы уже поставили лайк", "info");
                p.likedBy.push(currentUser.username); p.likes++;
                if(hasDisliked) { p.dislikedBy = p.dislikedBy.filter(u => u !== currentUser.username); p.dislikes--; }
                
                const author = db.users.find(u => u.username === p.author);
                if(author && author.username !== currentUser.username) author.points += 5; 
            } else {
                if(hasDisliked) return showToast("Вы уже поставили дизлайк", "info");
                p.dislikedBy.push(currentUser.username); p.dislikes++;
                if(hasLiked) { 
                    p.likedBy = p.likedBy.filter(u => u !== currentUser.username); p.likes--; 
                    const author = db.users.find(u => u.username === p.author);
                    if(author && author.username !== currentUser.username) author.points -= 5; 
                }
            }
            saveDB(); updateRatingUI();
        };

        function updateRatingUI() {
            const p = db.posts.find(x => x.id === currentEventId);
            if(!p) return;
            document.getElementById('valLikes').innerText = p.likes;
            document.getElementById('valDislikes').innerText = p.dislikes;
            
            const btnL = document.getElementById('btnLike');
            const btnD = document.getElementById('btnDislike');
            btnL.classList.remove('active-like'); btnD.classList.remove('active-dislike');
            
            if(currentUser) {
                if(p.likedBy.includes(currentUser.username)) btnL.classList.add('active-like');
                if(p.dislikedBy.includes(currentUser.username)) btnD.classList.add('active-dislike');
            }
        }

        // --- ЛИДЕРБОРД (ТОП 20) ---
        function renderLeaderboard() {
            const list = document.getElementById('leaderboardList'); list.innerHTML = '';
            // Сортировка по очкам по убыванию и обрезка до 20 мест (для оптимизации)
            const sortedUsers = [...db.users].sort((a,b) => b.points - a.points).slice(0, 20);
            
            sortedUsers.forEach((u, index) => {
                let placeStr = `${index + 1}`;
                if(index === 0) placeStr = '🥇 1';
                if(index === 1) placeStr = '🥈 2';
                if(index === 2) placeStr = '🥉 3';

                list.innerHTML += `
                    <tr>
                        <td style="font-weight:bold; font-size:1.2rem;">${placeStr}</td>
                        <td>${getUserHTML(u.username)}</td>
                        <td style="font-weight:bold; color:var(--warning);">${u.points}</td>
                    </tr>
                `;
            });
        }

        // --- ПУБЛИЧНЫЙ ПРОФИЛЬ ---
        window.openPublicProfile = (username) => {
            const u = db.users.find(x => x.username === username);
            if(!u) return;
            navigate('public-profile');

            document.getElementById('pubProfName').innerHTML = getUserHTML(u.username);
            document.getElementById('pubProfRole').innerText = (u.isBot ? '🤖 Бот Системы' : (u.role === 'admin' ? 'Администратор' : (u.role === 'moderator' ? 'Модератор' : 'Пользователь')));
            document.getElementById('pubProfPoints').innerText = u.points;

            const userPosts = db.posts.filter(p => p.author === u.username);
            document.getElementById('pubProfPosts').innerText = userPosts.length;

            const achList = document.getElementById('pubProfAchieve'); achList.innerHTML = '';
            
            const achs = [
                { title: "Новичок", desc: "Создать 1 событие", done: userPosts.length >= 1 || u.isBot, icon: "👶" },
                { title: "Популярный", desc: "Набрать 50 очков", done: u.points >= 50, icon: "🔥" },
                { title: "Звезда", desc: "Набрать 500 очков", done: u.points >= 500, icon: "🌟" }
            ];
            achs.forEach(a => {
                achList.innerHTML += `
                    <div class="achievement ${a.done ? '' : 'locked'}">
                        <div class="ach-icon">${a.icon}</div>
                        <div><div style="font-weight:bold;">${a.title}</div><div style="font-size:0.8rem; color:var(--text-muted);">${a.desc}</div></div>
                    </div>
                `;
            });

            const holList = document.getElementById('pubProfHolidays'); holList.innerHTML = '';
            userPosts.forEach(p => {
                holList.innerHTML += `
                    <div class="card" onclick="openSingleEvent(${p.id})">
                        <h3>⏱️ ${p.title}</h3>
                        <div style="display:flex; gap:15px; font-size:0.9rem; color:var(--text-muted);"><span>👍 ${p.likes}</span></div>
                    </div>
                `;
            });
        };

        // --- АДМИН ПАНЕЛЬ ---
        function renderAdmin() {
            if(!currentUser || currentUser.role !== 'admin') return navigate('home');
            
            const bList = document.getElementById('adminBotsList'); bList.innerHTML = '';
            db.users.filter(u => u.isBot).forEach(b => {
                bList.innerHTML += `
                    <tr>
                        <td>${getUserHTML(b.username)}</td>
                        <td><input type="text" id="botname_${b.username}" class="custom-input" style="padding:5px;" placeholder="Новый ник"></td>
                        <td><button class="btn-primary" style="padding:5px 10px;" onclick="admRenameBot('${b.username}')">Сменить</button></td>
                    </tr>
                `;
            });

            const uList = document.getElementById('adminUsersList'); uList.innerHTML = '';
            db.users.filter(u => !u.isBot).forEach(u => {
                let colorOpts = COLORS.map(c => `<option value="${c.val}" ${u.nameColor===c.val?'selected':''}>${c.label}</option>`).join('');
                
                uList.innerHTML += `
                    <tr>
                        <td>${getUserHTML(u.username)}</td>
                        <td><select onchange="admSetColor('${u.username}', this.value)" class="custom-input" style="padding:5px; width: 120px;">${colorOpts}</select></td>
                        <td>
                            <label style="cursor:pointer; display:flex; align-items:center; gap:5px;">
                                <input type="checkbox" onchange="admSetVerify('${u.username}', this.checked)" ${u.verified ? 'checked' : ''} style="width:20px;height:20px;">
                                Дать галочку
                            </label>
                        </td>
                        <td>
                            <select onchange="admSetRole('${u.username}', this.value)" class="custom-input" style="padding:5px;">
                                <option value="user" ${u.role==='user'?'selected':''}>User</option>
                                <option value="moderator" ${u.role==='moderator'?'selected':''}>Moderator</option>
                                <option value="admin" ${u.role==='admin'?'selected':''}>Admin</option>
                            </select>
                        </td>
                        <td>${u.isBanned ? `<button class="btn-success" style="padding:5px;" onclick="admToggleBan('${u.username}')">Разбан</button>` : `<button class="btn-danger" style="padding:5px;" onclick="admToggleBan('${u.username}')">Бан</button>`}</td>
                    </tr>
                `;
            });

            const pList = document.getElementById('adminPostsList'); pList.innerHTML = '';
            db.posts.forEach(p => {
                pList.innerHTML += `
                    <tr>
                        <td>${p.title}</td><td>${p.author}</td>
                        <td style="color:${p.isBanned?'var(--danger)':'var(--success)'}">${p.isBanned?'Скрыт':'ОК'}</td>
                        <td><button class="btn-danger" style="padding:5px;" onclick="admTogglePost(${p.id})">${p.isBanned?'Вернуть':'Скрыть'}</button></td>
                    </tr>
                `;
            });
        }

        window.admRenameBot = (oldName) => {
            const newName = document.getElementById(`botname_${oldName}`).value.trim();
            if(!newName) return;
            const b = db.users.find(u => u.username === oldName);
            if(b) { b.username = newName; saveDB(); renderAdmin(); showToast("Ник бота изменен", "success"); }
        };
        window.admSetColor = (nick, color) => {
            const u = db.users.find(u => u.username === nick);
            if(u) { u.nameColor = color; saveDB(); renderAdmin(); }
        };
        window.admSetVerify = (nick, isVerified) => {
            const u = db.users.find(u => u.username === nick);
            if(u) { u.verified = isVerified; saveDB(); renderAdmin(); showToast(`Галочка у ${nick} обновлена`, "success"); }
        };
        window.admSetRole = (nick, role) => {
            const u = db.users.find(u => u.username === nick);
            if(u) { u.role = role; saveDB(); renderAdmin(); }
        };
        window.admToggleBan = (nick) => {
            const u = db.users.find(u => u.username === nick);
            if(u) { u.isBanned = !u.isBanned; saveDB(); renderAdmin(); }
        };
        window.admTogglePost = (id) => {
            const p = db.posts.find(p => p.id === id);
            if(p) { p.isBanned = !p.isBanned; saveDB(); renderAdmin(); }
        };

        document.getElementById('burgerMenu').onclick = function() {
            this.classList.toggle('active'); document.getElementById('sidebar').classList.toggle('open');
        };

        initDB();
        navigate('home');
    </script>
</body>
</html>
