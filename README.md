# your-diary
you can create diary.
[U<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Multi-User Secure Diary</title>
    <style>
        :root { --primary: #6366f1; --bg: #f8fafc; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--bg); margin: 0; display: flex; justify-content: center; padding: 20px; }
        
        /* Auth Screen */
        #auth-screen {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: #1e293b; display: flex; justify-content: center; align-items: center; z-index: 1000;
        }
        .auth-box { background: white; padding: 30px; border-radius: 12px; width: 320px; text-align: center; }
        
        /* App UI */
        .container { width: 100%; max-width: 600px; background: white; padding: 30px; border-radius: 15px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); display: none; }
        .header { display: flex; justify-content: space-between; align-items: center; border-bottom: 2px solid #f1f5f9; padding-bottom: 10px; margin-bottom: 20px; }
        
        input, textarea { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #e2e8f0; border-radius: 8px; box-sizing: border-box; }
        button { cursor: pointer; border: none; border-radius: 8px; font-weight: 600; transition: 0.2s; }
        
        .btn-main { background: var(--primary); color: white; padding: 12px; width: 100%; }
        .btn-logout { background: #ef4444; color: white; padding: 5px 12px; font-size: 0.8rem; }
        
        .diary-item { background: #fdfdfd; border: 1px solid #e2e8f0; padding: 15px; margin-top: 15px; border-radius: 10px; }
        .diary-item small { color: #94a3b8; }
        .delete-link { color: #ef4444; cursor: pointer; font-size: 0.8rem; margin-top: 10px; display: inline-block; }
    </style>
</head>
<body>

    <div id="auth-screen">
        <div class="auth-box">
            <h2 id="auth-title">Welcome</h2>
            <p id="auth-msg">Login or Register a new ID</p>
            <input type="text" id="username" placeholder="User ID (Username)">
            <input type="password" id="password" placeholder="Password">
            <button class="btn-main" onclick="handleAuth()">Login / Register</button>
        </div>
    </div>

    <div class="container" id="app-container">
        <div class="header">
            <div>
                <h2 id="welcome-text">My Diary</h2>
                <small id="date-text"></small>
            </div>
            <button class="btn-logout" onclick="logout()">Logout</button>
        </div>

        <section>
            <input type="text" id="title" placeholder="Entry Title">
            <textarea id="content" placeholder="Write your thoughts..." rows="4"></textarea>
            <button class="btn-main" onclick="addEntry()">Save Secret Entry</button>
        </section>

        <div id="entries-list">
            </div>
    </div>

    <script>
        let currentUser = null;

        // AUTHENTICATION LOGIC
        function handleAuth() {
            const userIn = document.getElementById('username').value.trim().toLowerCase();
            const passIn = document.getElementById('password').value;

            if (!userIn || !passIn) return alert("Fill all fields");

            let users = JSON.parse(localStorage.getItem('diaryUsers')) || {};

            if (users[userIn]) {
                // User exists, check password
                if (users[userIn].password === passIn) {
                    login(userIn);
                } else {
                    alert("Wrong password!");
                }
            } else {
                // Register new user
                if (confirm("User ID not found. Create new account?")) {
                    users[userIn] = { password: passIn, entries: [] };
                    localStorage.setItem('diaryUsers', JSON.stringify(users));
                    login(userIn);
                }
            }
        }

        function login(username) {
            currentUser = username;
            document.getElementById('auth-screen').style.display = 'none';
            document.getElementById('app-container').style.display = 'block';
            document.getElementById('welcome-text').innerText = `Hello, ${username}`;
            document.getElementById('date-text').innerText = new Date().toDateString();
            loadEntries();
        }

        function logout() {
            location.reload();
        }

        // DIARY LOGIC
        function addEntry() {
            const title = document.getElementById('title').value;
            const content = document.getElementById('content').value;
            if (!title || !content) return;

            const entry = {
                title,
                content,
                date: new Date().toLocaleString(),
                id: Date.now()
            };

            let users = JSON.parse(localStorage.getItem('diaryUsers'));
            users[currentUser].entries.unshift(entry);
            localStorage.setItem('diaryUsers', JSON.stringify(users));

            document.getElementById('title').value = '';
            document.getElementById('content').value = '';
            loadEntries();
        }

        function loadEntries() {
            const list = document.getElementById('entries-list');
            const users = JSON.parse(localStorage.getItem('diaryUsers'));
            const entries = users[currentUser].entries;

            list.innerHTML = entries.length ? '' : '<p style="text-align:center; color:#94a3b8">Your diary is empty.</p>';

            entries.forEach(item => {
                const div = document.createElement('div');
                div.className = 'diary-item';
                div.innerHTML = `
                    <small>${item.date}</small>
                    <h3>${item.title}</h3>
                    <p>${item.content}</p>
                    <span class="delete-link" onclick="deleteEntry(${item.id})">Delete Memory</span>
                `;
                list.appendChild(div);
            });
        }

        function deleteEntry(entryId) {
            if (!confirm("Delete this entry?")) return;
            let users = JSON.parse(localStorage.getItem('diaryUsers'));
            users[currentUser].entries = users[currentUser].entries.filter(e => e.id !== entryId);
            localStorage.setItem('diaryUsers', JSON.stringify(users));
            loadEntries();
        }
    </script>
</body>
</html>ploading diary.html…]()
