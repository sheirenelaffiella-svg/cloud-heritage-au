#   
<!DOCTYPE html>  
<html lang="id">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0">  
    <title>The Cloud Heritage - AU Simulator</title>  
    <style>  
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; }  
        body { background: #121212; color: #fff; display: flex; justify-content: center; align-items: center; min-height: 100vh; padding: 10px; }  
        .phone-container { width: 375px; height: 750px; background: #000; border-radius: 40px; border: 12px solid #333; position: relative; overflow: hidden; display: flex; flex-direction: column; box-shadow: 0 20px 50px rgba(0,0,0,0.8); }  
        .status-bar { height: 30px; background: rgba(0,0,0,0.5); display: flex; justify-content: space-between; align-items: center; padding: 0 20px; font-size: 12px; font-weight: bold; z-index: 10; }  
        .screen { flex: 1; position: relative; background-size: cover; background-position: center; overflow: hidden; display: flex; flex-direction: column; }  
          
        /* Wallpapers */  
        .wp-cortis { background-image: url('https://raw.githubusercontent.com/sheirenlaffiella-svg/thecloudheritage/main/cortis.png'), url('https://via.placeholder.com/375x750/222/fff?text=CORTIS'); }  
  
        /* Lockscreen */  
        #lockscreen { position: absolute; inset: 0; z-index: 20; display: flex; flex-direction: column; justify-content: space-between; padding: 60px 20px 40px; text-align: center; background: rgba(0,0,0,0.4); backdrop-filter: blur(5px); }  
        .time { font-size: 64px; font-weight: 200; }  
        .date { font-size: 16px; opacity: 0.8; margin-top: -10px; }  
        .unlock-btn { background: rgba(255,255,255,0.2); padding: 12px 24px; border-radius: 20px; border: 1px solid rgba(255,255,255,0.4); color: #fff; font-size: 14px; cursor: pointer; align-self: center; }  
  
        /* Story Notification Banner */  
        #story-notif { position: absolute; top: 40px; left: 10px; right: 10px; background: rgba(20,20,20,0.95); border: 1px solid #444; border-radius: 12px; padding: 12px; z-index: 30; display: none; box-shadow: 0 4px 15px rgba(0,0,0,0.5); }  
        #story-notif h4 { font-size: 12px; color: #3498db; margin-bottom: 4px; text-transform: uppercase; }  
        #story-notif p { font-size: 13px; line-height: 1.4; color: #ddd; }  
        #story-notif .close-notif { font-size: 11px; color: #aaa; text-align: right; margin-top: 6px; cursor: pointer; }  
  
        /* App Launcher & Navigation */  
        .app-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 20px; padding: 40px 20px; }  
        .app-icon { display: flex; flex-direction: column; align-items: center; gap: 6px; cursor: pointer; }  
        .app-icon div { width: 55px; height: 55px; border-radius: 14px; display: flex; justify-content: center; align-items: center; font-size: 24px; background: #333; }  
        .app-icon span { font-size: 11px; text-align: center; }  
  
        /* Apps UI */  
        .app-window { position: absolute; inset: 0; background: #1c1c1e; z-index: 15; display: none; flex-direction: column; }  
        .app-header { height: 50px; background: #2c2c2e; display: flex; align-items: center; justify-content: space-between; padding: 0 15px; border-bottom: 1px solid #38383a; }  
        .app-header h3 { font-size: 16px; }  
        .back-btn { background: none; border: none; color: #0a84ff; font-size: 16px; cursor: pointer; }  
  
        /* Chat App */  
        .contact-list { flex: 1; overflow-y: auto; }  
        .contact-item { display: flex; align-items: center; padding: 12px 15px; border-bottom: 1px solid #2c2c2e; cursor: pointer; gap: 12px; }  
        .avatar { width: 48px; height: 48px; border-radius: 50%; object-fit: cover; background: #444; }  
        .chat-content { flex: 1; display: flex; flex-direction: column; overflow: hidden; }  
        .chat-messages { flex: 1; padding: 15px; overflow-y: auto; display: flex; flex-direction: column; gap: 10px; }  
        .msg { max-width: 80%; padding: 10px 14px; border-radius: 16px; font-size: 14px; line-height: 1.4; }  
        .msg.received { background: #2c2c2e; align-self: flex-start; }  
        .msg.sent { background: #0b93f6; align-self: flex-end; }  
          
        /* Interactive Choice Buttons */  
        .choice-container { padding: 10px 15px; background: #2c2c2e; display: flex; flex-direction: column; gap: 8px; }  
        .choice-btn { background: #3a3a3c; border: 1px solid #555; color: #fff; padding: 10px; border-radius: 8px; font-size: 13px; cursor: pointer; text-align: left; }  
        .choice-btn:hover { background: #0b93f6; }  
  
        /* Camera / Locker App */  
        .cam-view { flex: 1; background: #000; display: flex; flex-direction: column; justify-content: center; align-items: center; position: relative; }  
        .locker-overlay { border: 2px dashed #00ff00; padding: 30px; text-align: center; background: rgba(0,0,0,0.6); border-radius: 10px; }  
        .pass-input { background: #222; border: 1px solid #555; color: #fff; padding: 8px; width: 120px; text-align: center; border-radius: 6px; font-size: 16px; margin-top: 10px; }  
    </style>  
</head>  
<body>  
  
<div class="phone-container">  
    <div class="status-bar">  
        <span>18.20</span>  
        <span>5G 🔋42%</span>  
    </div>  
  
    <div id="main-screen" class="screen wp-cortis">  
        <!-- Story Notification Banner -->  
        <div id="story-notif">  
            <h4>NARRATIVE UPDATE</h4>  
            <p id="notif-text">Hujan deras membilas kaca jendela kamar kos Jayden saat ia menatap layar ponsel bekas yang baru dibelinya...</p>  
            <div class="close-notif" onclick="closeNotif()">[Tutup Notifikasi]</div>  
        </div>  
  
        <!-- Lockscreen -->  
        <div id="lockscreen">  
            <div>  
                <div class="time">18:20</div>  
                <div class="date">Kamis, 18 Agustus</div>  
            </div>  
            <button class="unlock-btn" onclick="unlockPhone()">Geser / Klik Untuk Buka</button>  
        </div>  
  
        <!-- App Launcher Grid -->  
        <div class="app-grid">  
            <div class="app-icon" onclick="openApp('chat-app')">  
                <div style="background:#34c759;">💬</div>  
                <span>Messages</span>  
            </div>  
            <div class="app-icon" onclick="openApp('camera-app')">  
                <div style="background:#8e8e93;">📷</div>  
                <span>Loker 404</span>  
            </div>  
            <div class="app-icon" onclick="openApp('notes-app')">  
                <div style="background:#ffcc00;">📝</div>  
                <span>Notes Log</span>  
            </div>  
            <div class="app-icon" onclick="openApp('vault-app')">  
                <div style="background:#ff9500;">🔒</div>  
                <span>Vault</span>  
            </div>  
        </div>  
    </div>  
  
    <!-- CHAT APP -->  
    <div id="chat-app" class="app-window">  
        <div class="app-header">  
            <button class="back-btn" onclick="closeApp('chat-app')">‹ Back</button>  
            <h3 id="chat-header-title">Messages</h3>  
            <div></div>  
        </div>  
          
        <!-- Contact List -->  
        <div id="contact-list" class="contact-list">  
            <div class="contact-item" onclick="openChat('marvin', 'Marvin (Pemilik Lama)', 'https://via.placeholder.com/150/ff7f7f/333?text=Marvin')">  
                <img class="avatar" src="https://via.placeholder.com/150/ff7f7f/333?text=Marvin" alt="Marvin">  
                <div>  
                    <strong>Marvin</strong>  
                    <p style="font-size:12px; color:#aaa;">Audio memo terenkripsi...</p>  
                </div>  
            </div>  
            <div class="contact-item" onclick="openChat('kevin', 'Kevin', 'https://via.placeholder.com/150/7f7fff/333?text=Kevin')">  
                <img class="avatar" src="https://via.placeholder.com/150/7f7fff/333?text=Kevin" alt="Kevin">  
                <div>  
                    <strong>Kevin</strong>  
                    <p style="font-size:12px; color:#aaa;">Kev, aneh banget. Gua baru beli HP...</p>  
                </div>  
            </div>  
            <div class="contact-item" onclick="openChat('julian', 'Julian', 'https://via.placeholder.com/150/7fff7f/333?text=Julian')">  
                <img class="avatar" src="https://via.placeholder.com/150/7fff7f/333?text=Julian" alt="Julian">  
                <div>  
                    <strong>Julian</strong>  
                    <p style="font-size:12px; color:#aaa;">Mas Jayden, serahin HP itu baik-baik.</p>  
                </div>  
            </div>  
            <div class="contact-item" onclick="openChat('sean', 'Sean (Pemilik Konter)', 'https://via.placeholder.com/150/ffff7f/333?text=Sean')">  
                <img class="avatar" src="https://via.placeholder.com/150/ffff7f/333?text=Sean" alt="Sean">  
                <div>  
                    <strong>Sean</strong>  
                    <p style="font-size:12px; color:#aaa;">Jangan coba-coba lapor polisi!</p>  
                </div>  
            </div>  
        </div>  
  
        <!-- Chat Room Area -->  
        <div id="chat-room" class="chat-content" style="display:none;">  
            <div id="chat-messages" class="chat-messages"></div>  
            <div id="choice-container" class="choice-container"></div>  
        </div>  
    </div>  
  
    <!-- CAMERA / LOCKER APP -->  
    <div id="camera-app" class="app-window">  
        <div class="app-header">  
            <button class="back-btn" onclick="closeApp('camera-app')">‹ Exit</button>  
            <h3>Live Camera - Locker 404</h3>  
            <div></div>  
        </div>  
        <div class="cam-view">  
            <div class="locker-overlay">  
                <p style="color:#00ff00; font-size:12px;">SCANNING LOCKER DEPOSIT #42</p>  
                <p style="font-size:11px; margin-top:5px; color:#aaa;">Masukkan Passcode 6-Digit:<br><i>(Clue: Tanggal Debut CORTIS - DDMMYY)</i></p>  
                <input type="password" id="locker-pass" class="pass-input" maxlength="6" placeholder="******">  
                <button onclick="checkPasscode()" style="margin-top:10px; padding:6px 15px; background:#00ff00; border:none; border-radius:4px; font-weight:bold; cursor:pointer;">BUKA LOKER</button>  
            </div>  
        </div>  
    </div>  
  
    <!-- NOTES APP -->  
    <div id="notes-app" class="app-window">  
        <div class="app-header">  
            <button class="back-btn" onclick="closeApp('notes-app')">‹ Back</button>  
            <h3>Notes Log</h3>  
            <div></div>  
        </div>  
        <div style="padding:15px; font-size:13px; line-height:1.6; color:#ccc;">  
            <h4 style="color:#fff; margin-bottom:8px;">📌 Catatan Investigasi Jayden:</h4>  
            <p>- HP bekas dibeli dari Konter Sean.</p>  
            <p>- Cloud Backup akun Marvin ditarik otomatis.</p>  
            <p>- Clue Vault & Loker Deposit: <b>Tanggal Debut CORTIS</b> (Format: DDMMYY / Liputan Terakhir Marvin: 18 Agustus 2025 -> <b>180825</b>).</p>  
        </div>  
    </div>  
  
    <!-- VAULT APP -->  
    <div id="vault-app" class="app-window">  
        <div class="app-header">  
            <button class="back-btn" onclick="closeApp('vault-app')">‹ Back</button>  
            <h3>Encrypted Vault</h3>  
            <div></div>  
        </div>  
        <div style="padding:20px; text-align:center;">  
            <p style="font-size:14px; color:#aaa;">Buka Loker 404 via Kamera terlebih dahulu untuk mendekripsi isi Vault CORTIS.</p>  
        </div>  
    </div>  
</div>  
  
<script>  
    function unlockPhone() {  
        document.getElementById('lockscreen').style.display = 'none';  
        showNotif("Hujan deras membilas kaca jendela kos Jayden. Data backup cloud akun Marvin ditarik otomatis ke HP ini...");  
    }  
  
    function showNotif(text) {  
        document.getElementById('notif-text').innerText = text;  
        document.getElementById('story-notif').style.display = 'block';  
    }  
  
    function closeNotif() {  
        document.getElementById('story-notif').style.display = 'none';  
    }  
  
    function openApp(appId) {  
        document.getElementById(appId).style.display = 'flex';  
    }  
  
    function closeApp(appId) {  
        document.getElementById(appId).style.display = 'none';  
        if(appId === 'chat-app') {  
            document.getElementById('chat-room').style.display = 'none';  
            document.getElementById('contact-list').style.display = 'block';  
        }  
    }  
  
    function openChat(person, name, avatar) {  
        document.getElementById('contact-list').style.display = 'none';  
        document.getElementById('chat-room').style.display = 'flex';  
        document.getElementById('chat-header-title').innerText = name;  
          
        let msgBox = document.getElementById('chat-messages');  
        let choiceBox = document.getElementById('choice-container');  
        msgBox.innerHTML = '';  
        choiceBox.innerHTML = '';  
  
        if(person === 'kevin') {  
            msgBox.innerHTML = `  
                <div class="msg sent">Kev, aneh banget. Gua baru beli HP bekas di toko Sean, pas nyalain WA malah narik backup orang lain. Namanya Marvin.</div>  
                <div class="msg received">Serius lu? Marvin jurnalis BEM yang hilang tahun lalu? Jangan asal buka bro, bahaya data orang!</div>  
            `;  
            choiceBox.innerHTML = `  
                <button class="choice-btn" onclick="sendMsg('Gua mau coba cek folder tersembunyinya dulu.')">Pilihan A: Cek Folder Tersembunyi</button>  
                <button class="choice-btn" onclick="sendMsg('Mending gua kembalikan HP ini ke Sean.')">Pilihan B: Kembalikan HP</button>  
            `;  
        } else if(person === 'julian') {  
            msgBox.innerHTML = `  
                <div class="msg received">Mas Jayden, kamu lagi duduk di dekat jendela lantai dua kosan kan? Lampu kamarmu baru aja keliatan mati-hidup. Saya ada di bawah nih, serahin HP itu baik-baik.</div>  
            `;  
            showNotif("PERINGATAN GPS: Julian berada di depan gang kosanmu! Dia melacak titik lokasi presisi tinggi!");  
        } else if(person === 'sean') {  
            msgBox.innerHTML = `  
                <div class="msg sent">Gua udah tau semuanya! Sean yang jual HP ini ke gua kan? Kalian sengaja jadiin gua kambing hitam!</div>  
                <div class="msg received">Bagus kalau lu sadar posisi, bocah! Datang malam ini jam 8 kalau lu masih mau selamat!</div>  
            `;  
        } else if(person === 'marvin') {  
            msgBox.innerHTML = `  
                <div class="msg received"><i>[SYSTEM ENCRYPTED FILE]: Audio_Memo_20250818.wav (Terdekripsi)</i><br>"Uang 1.5 Miliar sudah gua simpan di Loker Deposit Stasiun Utama. Kodenya adalah Tanggal Debut CORTIS!"</div>  
            `;  
        }  
    }  
  
    function sendMsg(text) {  
        let msgBox = document.getElementById('chat-messages');  
        msgBox.innerHTML += `<div class="msg sent">${text}</div>`;  
        showNotif("Pilihan dibuat. Alur cerita berlanjut ke tahap berikutnya...");  
    }  
  
    function checkPasscode() {  
        let val = document.getElementById('locker-pass').value;  
        if(val === '180825') {  
            alert('AKSES DITERIMA! Loker #42 Terbuka. Berkas Bukti Pemerasan 1.5M & Emas berhasil diakses!');  
        } else {  
            alert('PASSCODE SALAH! Petunjuk: Tanggal Debut CORTIS (DDMMYY -> 180825)');  
        }  
    }  
</script>  
</body>  
</html>  
