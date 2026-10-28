<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Luni Chat</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: Arial, sans-serif; }
        body { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); min-height: 100vh; color: #fff; }
        .container { max-width: 1200px; margin: 0 auto; padding: 0; min-height: 100vh; display: flex; flex-direction: column; }
        
        header { display: flex; justify-content: space-between; align-items: center; padding: 12px 20px; background: rgba(0,0,0,0.2); }
        h1 { font-size: 20px; }
        
        .account-area { display: flex; align-items: center; gap: 10px; position: relative; }
        .current-account { display: flex; align-items: center; gap: 8px; cursor: pointer; padding: 5px 12px; border-radius: 20px; background: rgba(255,255,255,0.15); }
        .avatar { width: 32px; height: 32px; border-radius: 50%; background: #a855f7; display: flex; align-items: center; justify-content: center; font-weight: bold; }
        .account-name { font-size: 14px; }
        
        .menu-btn { font-size: 22px; cursor: pointer; padding: 5px 10px; user-select: none; }
        .menu-dropdown { position: absolute; right: 20px; top: 50px; background: #1f2937; border-radius: 10px; min-width: 180px; display: none; z-index: 100; }
        .menu-dropdown.show { display: block; }
        .menu-item { padding: 12px 15px; cursor: pointer; }
        .menu-item:hover { background: rgba(255,255,255,0.1); }
        
        .account-dropdown { position: absolute; left: 0; top: 45px; background: #1f2937; border-radius: 10px; min-width: 200px; display: none; z-index: 100; }
        .account-dropdown.show { display: block; }
        .account-item { padding: 10px 15px; cursor: pointer; }
        .account-item:hover { background: rgba(255,255,255,0.1); }
        .account-item.add-new { color: #4ade80; border-top: 1px solid rgba(255,255,255,0.1); }
        
        .main-wrapper { display: flex; flex: 1; overflow: hidden; }
        .sidebar { width: 280px; background: rgba(0,0,0,0.15); padding: 15px; overflow-y: auto; border-right: 1px solid rgba(255,255,255,0.1); }
        .sidebar h3 { font-size: 16px; margin-bottom: 12px; display: flex; justify-content: space-between; align-items: center; }
        .add-group-btn { background: #4ade80; color: #111; border: none; border-radius: 6px; padding: 6px 10px; cursor: pointer; font-size: 18px; font-weight: bold; }
        .group-item { padding: 12px; border-radius: 8px; margin-bottom: 6px; cursor: pointer; background: rgba(255,255,255,0.05); }
        .group-item:hover { background: rgba(255,255,255,0.12); }
        .empty-state { text-align: center; padding: 30px 15px; opacity: 0.6; font-size: 14px; }
        
        .content-area { flex: 1; display: flex; flex-direction: column; overflow: hidden; }
        .welcome-screen { display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100%; opacity: 0.5; }
        
        .modal-bg { position: fixed; inset: 0; background: rgba(0,0,0,0.6); display: none; align-items: center; justify-content: center; z-index: 200; }
        .modal-bg.show { display: flex; }
        .modal { background: #fff; color: #333; border-radius: 12px; padding: 25px; max-width: 420px; width: 90%; }
        .modal h3 { margin-bottom: 18px; color: #4ade80; font-size: 18px; }
        input, textarea { width: 100%; padding: 10px; margin: 6px 0 12px 0; border: 1px solid #ddd; border-radius: 6px; font-size: 14px; }
        button { padding: 10px 16px; border-radius: 6px; border: none; cursor: pointer; font-size: 14px; }
        button.primary { background: #4ade80; color: #111; font-weight: bold; }
        button.secondary { background: #e5e7eb; color: #333; }
        .btn-group { display: flex; gap: 8px; justify-content: flex-end; margin-top: 10px; }
        
        .emoticon-grid { display: grid; grid-template-columns: repeat(5, 1fr); gap: 8px; margin-top: 15px; }
        .emoticon-item { font-size: 28px; text-align: center; padding: 15px 10px; border: 1px solid #ddd; border-radius: 8px; cursor: pointer; background: #fafafa; color: #333; }
        .emoticon-item:hover { background: #f0fdf4; border-color: #4ade80; }
        .question-mark { font-size: 24px; font-weight: bold; color: #a855f7; }
        .result-text { text-align: center; margin-top: 15px; font-weight: bold; min-height: 24px; color: #333; }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <div class="account-area">
                <div class="current-account" id="accountBtn">
                    <div class="avatar">?</div>
                    <span class="account-name" id="curName">Belum Ada Akun</span>
                    <span>▼</span>
                </div>
                <div class="account-dropdown" id="accountDropdown">
                    <div id="accountList"></div>
                    <div class="account-item add-new" onclick="openAccountModal()">➕ Buat Akun Baru</div>
                </div>
            </div>
            
            <h1>🌙 Luni Chat</h1>
            
            <div style="display:flex; align-items:center; gap:15px;">
                <div class="menu-btn" id="menuBtn">☰</div>
                <div class="menu-dropdown" id="mainMenu">
                    <div class="menu-item" onclick="openDiamondModal()">💎 Toko Emoticon</div>
                    <div class="menu-item" onclick="alert('Pengaturan akan segera hadir! ⚙️')">⚙️ Pengaturan</div>
                    <div class="menu-item" onclick="closeMainMenu()">❌ Tutup</div>
                </div>
            </div>
        </header>

        <div class="main-wrapper">
            <div class="sidebar">
                <h3>Grup Saya <button class="add-group-btn" onclick="openCreateGroupModal()">+</button></h3>
                <div id="groupList">
                    <div class="empty-state">Belum ada grup.<br>Tekan tombol + untuk buat grup baru.</div>
                </div>
            </div>
            
            <div class="content-area">
                <div class="welcome-screen">
                    <h2>Selamat Datang di Luni Chat 🌙</h2>
                    <p style="margin-top:10px;">Buat akun → buat grup → mulai berbagi cerita</p>
                </div>
            </div>
        </div>
    </div>

    <!-- MODAL BUAT AKUN -->
    <div class="modal-bg" id="accountModal">
        <div class="modal">
            <h3>➕ Buat Akun Baru</h3>
            <input type="text" id="newAccountName" placeholder="Masukkan nama akun">
            <div class="btn-group">
                <button class="secondary" onclick="closeAccountModal()">Batal</button>
                <button class="primary" onclick="createAccount()">Simpan</button>
            </div>
        </div>
    </div>

    <!-- MODAL BUAT GRUP -->
    <div class="modal-bg" id="createGroupModal">
        <div class="modal">
            <h3>Buat Grup Baru</h3>
            <input type="text" id="newGroupName" placeholder="Nama Grup">
            <textarea id="newGroupBio" placeholder="Bio grup (opsional)"></textarea>
            <div class="btn-group">
                <button class="secondary" onclick="closeCreateGroupModal()">Batal</button>
                <button class="primary" onclick="createGroup()">Buat Grup</button>
            </div>
        </div>
    </div>

    <!-- MODAL TOKO EMOTICON -->
    <div class="modal-bg" id="diamondModal">
        <div class="modal">
            <h3>💎 Toko Emoticon — Keberuntungan</h3>
            <p style="font-size:13px; color:#666; margin-bottom:15px;">Pilih tanda tanya di bawah ini:</p>
            
            <div class="emoticon-grid">
                <div class="emoticon-item" onclick="pickItem(0)"><div class="question-mark">?</div></div>
                <div class="emoticon-item" onclick="pickItem(1)"><div class="question-mark">?</div></div>
                <div class="emoticon-item" onclick="pickItem(2)"><div class="question-mark">?</div></div>
                <div class="emoticon-item" onclick="pickItem(3)"><div class="question-mark">?</div></div>
                <div class="emoticon-item" onclick="pickItem(4)"><div class="question-mark">?</div></div>
            </div>
            
            <div class="result-text" id="luckResult"></div>
            
            <div class="btn-group" style="margin-top:20px;">
                <button class="secondary" onclick="closeDiamondModal()">Tutup</button>
            </div>
        </div>
    </div>

    <script>
        // Data
        let accounts = [];
        let groups = [];

        // === FUNGSI MENU ===
        document.getElementById('accountBtn').addEventListener('click', function(e) {
            e.stopPropagation();
            document.getElementById('accountDropdown').classList.toggle('show');
            document.getElementById('mainMenu').classList.remove('show');
        });

        document.getElementById('menuBtn').addEventListener('click', function(e) {
            e.stopPropagation();
            document.getElementById('mainMenu').classList.toggle('show');
            document.getElementById('accountDropdown').classList.remove('show');
        });

        document.addEventListener('click', function() {
            document.getElementById('accountDropdown').classList.remove('show');
            document.getElementById('mainMenu').classList.remove('show');
        });

        function closeMainMenu() {
            document.getElementById('mainMenu').classList.remove('show');
        }

        // === AKUN ===
        function openAccountModal() {
            document.getElementById('accountModal').classList.add('show');
            document.getElementById('newAccountName').value = '';
        }
        function closeAccountModal() {
            document.getElementById('accountModal').classList.remove('show');
        }
        function createAccount() {
            let name = document.getElementById('newAccountName').value.trim();
            if (!name) { alert('Silakan isi nama akun!'); return; }
            accounts.push(name);
            document.getElementById('curName').textContent = name;
            closeAccountModal();
            alert('Akun "' + name + '" berhasil dibuat! ✅');
        }

        // === GRUP ===
        function openCreateGroupModal() {
            document.getElementById('createGroupModal').classList.add('show');
        }
        function closeCreateGroupModal() {
            document.getElementById('createGroupModal').classList.remove('show');
        }
        function createGroup() {
            let name = document.getElementById('newGroupName').value.trim();
            let bio = document.getElementById('newGroupBio').value.trim();
            if (!name) { alert('Silakan isi nama grup!'); return; }
            groups.push({ name: name, bio: bio });
            updateGroupList();
            closeCreateGroupModal();
            alert('Grup "' + name + '" berhasil dibuat! ✅');
        }
        function updateGroupList() {
            let list = document.getElementById('groupList');
            if (groups.length === 0) {
                list.innerHTML = '<div class="empty-state">Belum ada grup.<br>Tekan tombol + untuk buat grup baru.</div>';
                return;
            }
            list.innerHTML = '';
            groups.forEach(function(g, i) {
                list.innerHTML += '<div class="group-item">' + g.name + '</div>';
            });
        }

        // === TOKO EMOTICON ===
        function openDiamondModal() {
            document.getElementById('diamondModal').classList.add('show');
            document.getElementById('luckResult').textContent = '';
        }
        function closeDiamondModal() {
            document.getElementById('diamondModal').classList.remove('show');
        }
        function pickItem(index) {
            let rewards = ['😊 Senyum Bahagia', '💎 5 Diamond', '✨ Bintang Keberuntungan', '🌟 Emoticon Spesial', '❤️ Hati Penuh Kasih'];
            let rnd = Math.floor(Math.random() * rewards.length);
            document.getElementById('luckResult').textContent = 'Kamu mendapat: ' + rewards[rnd] + ' 🎉';
        }
    </script>
</body>
</html>
