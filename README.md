<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="theme-color" content="#050505">
    <title>MND Live Logistics | Cloud Tracking</title>

    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@600;800;900&family=Outfit:wght@300;500;700;900&family=Orbitron:wght@500;700;900&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">

    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-database-compat.js"></script>

    <style>
        /* === GLOBAL RESET & VARIABLES === */
        :root {
            --gold: #D4AF37;
            --gold-glow: rgba(212, 175, 55, 0.4);
            --neon-green: #00FA9A;
            --neon-cyan: #00E5FF;
            --bg-dark: #050508;
            --panel-bg: rgba(15, 15, 20, 0.85);
            --danger: #ff3333;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; }
        body, html { width: 100vw; height: 100vh; background: var(--bg-dark); color: #fff; overflow-x: hidden; display: flex; justify-content: center; align-items: flex-start; }

        /* Animated Background */
        .bg-map {
            position: fixed; inset: 0; z-index: -1; opacity: 0.15;
            background-image: radial-gradient(circle at 50% 50%, var(--gold-glow) 0%, transparent 70%);
            animation: pulseBg 4s infinite alternate;
        }
        @keyframes pulseBg { 0% { opacity: 0.1; } 100% { opacity: 0.3; } }

        /* === TOAST NOTIFICATIONS === */
        #toast-container { position: fixed; top: 20px; left: 50%; transform: translateX(-50%); z-index: 9999999; display: flex; flex-direction: column; gap: 10px; pointer-events: none; }
        .toast { background: rgba(10,10,15,0.95); border-left: 4px solid var(--gold); color: #fff; padding: 15px 20px; border-radius: 8px; font-weight: 600; font-size: 14px; box-shadow: 0 10px 30px rgba(0,0,0,0.8); display: flex; align-items: center; gap: 10px; animation: toastSlideIn 0.4s ease forwards, toastFadeOut 0.4s 3.5s forwards; }
        .toast.error { border-color: var(--danger); }
        .toast.success { border-color: var(--neon-green); }
        @keyframes toastSlideIn { from { opacity: 0; transform: translateY(-30px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes toastFadeOut { from { opacity: 1; transform: translateY(0); } to { opacity: 0; transform: translateY(-30px); } }

        /* === VIEW MANAGER === */
        .view-container { display: none; flex-direction: column; align-items: center; width: 100%; height: 100vh; padding: 40px 20px; animation: fadeIn 0.4s ease forwards; overflow-y: auto; }
        .active-view { display: flex !important; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        
        /* Scrollbar */
        ::-webkit-scrollbar { width: 6px; } ::-webkit-scrollbar-thumb { background: var(--gold); border-radius: 10px; }

        /* === GATEKEEPER UI === */
        .gatekeeper-box {
            background: var(--panel-bg); border: 2px solid var(--gold); border-radius: 20px;
            padding: 40px 25px; width: 100%; max-width: 450px; margin: auto;
            box-shadow: 0 20px 60px rgba(0,0,0,0.9), inset 0 0 30px rgba(212, 175, 55, 0.1);
            backdrop-filter: blur(15px); text-align: center;
        }
        .app-title { font-family: 'Cinzel', serif; color: var(--gold); font-size: 26px; font-weight: 900; letter-spacing: 2px; margin-bottom: 5px; text-shadow: 0 0 15px var(--gold-glow); }
        .app-subtitle { color: #aaa; font-size: 13px; margin-bottom: 25px; line-height: 1.5; }

        .input-group { width: 100%; position: relative; margin-bottom: 15px; }
        .input-group i { position: absolute; left: 15px; top: 16px; color: var(--gold); font-size: 16px; }
        .mn-input {
            width: 100%; padding: 15px 15px 15px 45px; background: rgba(255,255,255,0.03);
            border: 1px solid rgba(212,175,55,0.3); color: #fff; border-radius: 10px;
            font-size: 15px; outline: none; transition: 0.3s ease; box-sizing: border-box;
        }
        .mn-input:focus { border-color: var(--gold); background: rgba(212,175,55,0.08); box-shadow: 0 0 15px var(--gold-glow); }
        .pin-style { letter-spacing: 8px; font-size: 22px; font-weight: 900; text-align: center; padding-left: 15px; }
        .pin-style + i { display: none; }

        .mn-btn {
            width: 100%; padding: 16px; border-radius: 10px; font-weight: 900; font-size: 15px;
            cursor: pointer; transition: 0.3s; display: flex; justify-content: center; align-items: center; gap: 10px;
            border: none; letter-spacing: 1px; text-transform: uppercase;
        }
        .mn-btn:active { transform: scale(0.97); }
        .btn-gold { background: var(--gold); color: #000; box-shadow: 0 5px 20px var(--gold-glow); }
        .btn-green { background: #25D366; color: #fff; margin-top: 10px; }
        .btn-dark { background: rgba(0,0,0,0.5); border: 1px solid var(--gold); color: var(--gold); margin-top: 15px; }
        .btn-danger { background: rgba(255,51,51,0.1); border: 1px solid var(--danger); color: var(--danger); margin-top: 20px; }

        .error-msg { color: var(--danger); font-size: 13px; font-weight: bold; margin-top: 15px; display: none; text-align: center; }

        /* === DASHBOARD LAYOUTS === */
        .dash-header { width: 100%; max-width: 600px; display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; border-bottom: 1px solid rgba(212,175,55,0.3); padding-bottom: 15px; }
        .status-badge { background: rgba(0, 250, 154, 0.1); border: 1px solid var(--neon-green); color: var(--neon-green); padding: 5px 12px; border-radius: 30px; font-size: 11px; font-weight: bold; display: flex; align-items: center; gap: 5px;}
        
        .card { width: 100%; max-width: 600px; background: rgba(10,10,15,0.8); border: 1px solid rgba(212, 175, 55, 0.3); padding: 20px; border-radius: 15px; margin-bottom: 20px; backdrop-filter: blur(10px); }
        .card h3 { color: var(--gold); font-family: 'Cinzel'; margin-bottom: 15px; font-size: 18px; border-bottom: 1px dashed rgba(212, 175, 55, 0.3); padding-bottom: 5px; }

        /* Telemetrics Grid */
        .telemetrics { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin-bottom: 15px; }
        .metric { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.1); border-radius: 8px; padding: 10px; text-align: center; }
        .metric-lbl { font-size: 10px; color: #888; text-transform: uppercase; margin-bottom: 5px; }
        .metric-val { font-family: 'Orbitron'; font-size: 14px; font-weight: 700; color: var(--neon-cyan); }

        /* Admin Network List */
        .client-list-item { background: rgba(0,0,0,0.6); padding: 12px; border-radius: 8px; border-left: 3px solid var(--neon-green); margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; }
        .client-list-item h4 { margin:0 0 3px 0; font-size: 14px; }
        .client-list-item p { margin:0; font-size: 11px; color: #888; }
        .client-list-pin { font-family: 'Orbitron'; font-size: 16px; color: var(--gold); background: rgba(212,175,55,0.1); padding: 4px 8px; border-radius: 4px; }
        .revoke-btn { background: transparent; border: none; color: #666; font-size: 16px; cursor: pointer; transition: 0.3s; }
        .revoke-btn:hover { color: var(--danger); }

        /* Map Iframe */
        .map-wrapper { width: 100%; height: 250px; border-radius: 12px; overflow: hidden; border: 2px solid #333; position: relative; background: #111; margin-bottom: 10px; }
        .map-iframe { width: 100%; height: 100%; border: none; filter: invert(90%) hue-rotate(180deg) brightness(80%) contrast(120%) sepia(50%); pointer-events: none; }
        .map-loader { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); color: #555; font-size: 12px; text-align: center; font-weight: bold; }

        /* Updates Feed */
        .update-box { background: rgba(212,175,55,0.05); border-left: 3px solid var(--gold); padding: 15px; border-radius: 0 8px 8px 0; margin-top: 10px; animation: slideIn 0.3s ease; }
        @keyframes slideIn { from { opacity: 0; transform: translateX(-10px); } to { opacity: 1; transform: translateX(0); } }
        .update-time { font-size: 10px; color: var(--gold); margin-bottom: 5px; font-family: 'Orbitron'; }
        .update-text { font-size: 14px; color: #fff; line-height: 1.5; }

    </style>
</head>
<body>

    <div class="bg-map"></div>
    <div id="toast-container"></div>

    <div id="view-gatekeeper" class="view-container active-view" style="justify-content: center;">
        <div class="gatekeeper-box">
            <h1 class="app-title"><i class="fas fa-satellite-dish"></i> MND HUB</h1>
            <p class="app-subtitle">Secure Firebase Logistics Portal.<br>Enter your Number and PIN below.</p>
            
            <div class="input-group">
                <i class="fas fa-phone"></i>
                <input type="tel" id="login-phone" class="mn-input" placeholder="Mobile Number *">
            </div>
            
            <div class="input-group">
                <input type="password" id="login-pin" class="mn-input pin-style" placeholder="******" maxlength="6">
            </div>

            <button class="mn-btn btn-gold" id="login-btn" onclick="processLogin()"><i class="fas fa-fingerprint"></i> AUTHENTICATE CLOUD</button>
            <p id="login-error" class="error-msg"><i class="fas fa-times-circle"></i> Access Denied. Check Number or PIN.</p>
        </div>
    </div>

    <div id="view-admin" class="view-container">
        <div class="dash-header">
            <div>
                <h2 style="color:var(--gold); font-family:'Cinzel'; margin:0; font-size:20px;">Management Hub</h2>
                <span style="font-size:12px; color:#888;">Cloud Database Connected</span>
            </div>
            <div class="status-badge"><i class="fas fa-link"></i> DB SYNCED</div>
        </div>

        <div class="card">
            <h3><i class="fas fa-user-plus"></i> 1. Provision Client Access</h3>
            <input type="text" id="admin-c-name" class="mn-input" style="padding:10px; margin-bottom:10px;" placeholder="Client Name *">
            <input type="tel" id="admin-c-phone" class="mn-input" style="padding:10px; margin-bottom:10px;" placeholder="Client Phone (Must match login) *">
            <input type="text" id="admin-c-event" class="mn-input" style="padding:10px; margin-bottom:15px;" placeholder="Event Name *">
            
            <button class="mn-btn btn-gold" id="btn-generate" onclick="adminGeneratePin()"><i class="fas fa-cogs"></i> GENERATE PIN & SAVE</button>

            <div id="admin-pin-result" style="display:none; margin-top:15px; text-align:center;">
                <p style="color:#aaa; font-size:12px;">Share this data via WhatsApp:</p>
                <div style="font-family:'Orbitron'; font-size:32px; color:var(--neon-green); font-weight:900; letter-spacing:8px; margin:10px 0;" id="display-new-pin">000000</div>
                <button class="mn-btn btn-green" onclick="adminShareWhatsApp()"><i class="fab fa-whatsapp"></i> SEND TO CLIENT</button>
            </div>
        </div>

        <div class="card">
            <h3><i class="fas fa-network-wired"></i> 2. Active Cloud Network</h3>
            <p style="font-size:11px; color:#aaa; margin-bottom:10px;">Clients currently stored in your Firebase Database.</p>
            <div id="admin-active-list" style="max-height: 200px; overflow-y: auto;">
                <div style="text-align:center; color:#555; font-size:12px; padding:10px;">Waiting for network data...</div>
            </div>
        </div>

        <div class="card">
            <h3><i class="fas fa-broadcast-tower"></i> 3. Broadcast Live GPS</h3>
            <p style="font-size:12px; color:#aaa; margin-bottom:15px;">Send your real-time coordinates to Firebase for clients to view globally.</p>
            
            <div class="telemetrics" id="admin-telemetrics" style="display:none;">
                <div class="metric"><div class="metric-lbl">Speed</div><div class="metric-val" id="a-speed">0 km/h</div></div>
                <div class="metric"><div class="metric-lbl">Accuracy</div><div class="metric-val" id="a-acc">0 m</div></div>
                <div class="metric"><div class="metric-lbl">Pings</div><div class="metric-val" id="a-ping">0</div></div>
            </div>

            <button class="mn-btn btn-dark" id="btn-broadcast" onclick="toggleLocationBroadcast()"><i class="fas fa-location-arrow"></i> START CLOUD BROADCASTING</button>
        </div>

        <div class="card">
            <h3><i class="fas fa-comment-dots"></i> 4. Push Cloud Update</h3>
            <textarea id="admin-update-text" class="mn-input" rows="2" style="resize:none; padding:10px;" placeholder="e.g., 'Truck has reached Katoria bypass...'"></textarea>
            <button class="mn-btn btn-gold" id="btn-push-update" onclick="adminPushUpdate()"><i class="fas fa-paper-plane"></i> SEND ONLINE UPDATE</button>
        </div>

        <button class="mn-btn btn-danger" onclick="systemLogout()"><i class="fas fa-sign-out-alt"></i> SECURE LOGOUT</button>
    </div>

    <div id="view-client" class="view-container">
        <div class="dash-header">
            <div style="width: 70%;">
                <h2 id="client-dash-event" style="color:var(--gold); font-family:'Cinzel'; margin:0; font-size:18px; white-space:nowrap; overflow:hidden; text-overflow:ellipsis;">Loading Event...</h2>
                <span style="font-size:12px; color:#888;">Client: <span id="client-dash-name">Loading...</span></span>
            </div>
            <div class="status-badge" id="client-status"><i class="fas fa-circle fa-beat"></i> ONLINE</div>
        </div>

        <div class="card" style="padding: 15px;">
            <div style="display:flex; justify-content:space-between; align-items:center; padding-bottom: 10px; border-bottom: 1px dashed rgba(255,255,255,0.1); margin-bottom: 10px;">
                <h3 style="margin:0; border:none; padding:0; font-size:15px;"><i class="fas fa-map-marker-alt"></i> Logistics Tracker</h3>
                <span id="client-gps-status" style="font-size:10px; color:#888;">Awaiting Admin GPS...</span>
            </div>
            
            <div class="telemetrics">
                <div class="metric"><div class="metric-lbl">Speed</div><div class="metric-val" id="c-speed">--</div></div>
                <div class="metric"><div class="metric-lbl">Heading</div><div class="metric-val" id="c-heading">--</div></div>
                <div class="metric"><div class="metric-lbl">Signal Str</div><div class="metric-val" id="c-acc" style="color:var(--gold);">--</div></div>
            </div>

            <div class="map-wrapper">
                <div class="map-loader" id="map-loader-text"><i class="fas fa-satellite fa-spin fa-2x"></i><br><br>SYNCING SIGNAL...</div>
                <iframe id="client-map-iframe" class="map-iframe" src=""></iframe>
            </div>
            
            <a id="ext-map-link" href="#" target="_blank" class="mn-btn btn-dark" style="font-size:12px; padding:12px; text-decoration:none; display:none;"><i class="fas fa-external-link-alt"></i> OPEN IN MAPS APP</a>
        </div>

        <div class="card">
            <h3><i class="fas fa-bolt"></i> Live HQ Updates Feed</h3>
            <div id="client-updates-area">
                <div style="text-align:center; color:#555; font-size:13px; padding:20px;"><i class="fas fa-circle-notch fa-spin"></i> Awaiting transmissions from HQ...</div>
            </div>
        </div>

        <button class="mn-btn btn-danger" onclick="systemLogout()"><i class="fas fa-power-off"></i> DISCONNECT SESSION</button>
    </div>


    <script>
        // --- 1. TOAST NOTIFICATIONS ---
        function showToast(message, type = 'success') {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div');
            toast.className = `toast ${type}`;
            const icon = type === 'success' ? 'fa-check-circle' : 'fa-exclamation-triangle';
            toast.innerHTML = `<i class="fas ${icon}"></i> <span>${message}</span>`;
            container.appendChild(toast);
            setTimeout(() => { toast.remove(); }, 4000);
        }

        // --- 2. FIREBASE CONFIGURATION (NO AUTH REQUIRED) ---
        const firebaseConfig = {
            apiKey: "AIzaSyCZP-zuJNDW9S4sD_d4R_-nrTMjf0HD4MM",
            authDomain: "mnd-tracking.firebaseapp.com",
            databaseURL: "https://mnd-tracking-default-rtdb.asia-southeast1.firebasedatabase.app",
            projectId: "mnd-tracking",
            storageBucket: "mnd-tracking.firebasestorage.app",
            messagingSenderId: "794366177184",
            appId: "1:794366177184:web:3f394f0207215ccca0fec5"
        };
        
        // Initialize Firebase safely
        if (!firebase.apps.length) {
            firebase.initializeApp(firebaseConfig);
        }
        const db = firebase.database();
        // NOTE: firebase.auth() is INTENTIONALLY removed to prevent configuration errors.

        // --- 3. SYSTEM STATE ---
        const ADMIN_NUMBERS = ["9771617808", "9153635378", "7294969938", "+918544341240", "8544341240"];
        const MASTER_PIN = "121120";
        
        let geoWatchId = null;
        let pingCount = 0;
        let activeClientData = null; 

        // --- 4. VIEW ROUTER ---
        function switchView(viewId) {
            document.querySelectorAll('.view-container').forEach(el => el.classList.remove('active-view'));
            document.getElementById(viewId).classList.add('active-view');
            document.querySelectorAll('.error-msg').forEach(el => el.style.display = 'none');
            window.scrollTo(0,0);
        }

        // --- 5. SMART ONLINE GATEKEEPER ---
        function processLogin() {
            const phone = document.getElementById('login-phone').value.trim();
            const pin = document.getElementById('login-pin').value.trim();
            const err = document.getElementById('login-error');
            const btn = document.getElementById('login-btn');

            if(!phone || !pin) { err.innerText = "Please enter both fields."; err.style.display = 'block'; return; }

            // A. Check Admin Bypass
            if(ADMIN_NUMBERS.includes(phone) && pin === MASTER_PIN) {
                document.getElementById('login-phone').value = ''; document.getElementById('login-pin').value = '';
                switchView('view-admin');
                startAdminListListener();
                showToast("Admin Protocol Authorized");
                return;
            }

            // B. Client Online Firebase Check
            if (btn) { btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> QUERYING CLOUD...'; btn.disabled = true; }
            err.style.display = 'none';

            db.ref('trackings/' + phone).once('value')
            .then((snapshot) => {
                if(btn) { btn.innerHTML = '<i class="fas fa-fingerprint"></i> AUTHENTICATE CLOUD'; btn.disabled = false; }

                if (snapshot.exists() && snapshot.val().pin === pin) {
                    const data = snapshot.val();
                    document.getElementById('login-phone').value = ''; document.getElementById('login-pin').value = '';
                    document.getElementById('client-dash-name').innerText = data.name;
                    document.getElementById('client-dash-event').innerText = data.event;
                    
                    startClientWatch();
                    switchView('view-client');
                    showToast(`Welcome, ${data.name}`);
                } else {
                    err.innerHTML = "<i class='fas fa-times-circle'></i> Invalid Number or PIN.";
                    err.style.display = 'block';
                }
            })
            .catch((error) => {
                if(btn) { btn.innerHTML = '<i class="fas fa-fingerprint"></i> AUTHENTICATE CLOUD'; btn.disabled = false; }
                err.innerHTML = "<i class='fas fa-exclamation-triangle'></i> Cloud Error. Check Network or Firebase Rules.";
                err.style.display = 'block';
                console.error("Firebase Read Error:", error);
            });
        }

        // --- 6. ADMIN: PROVISION & SAVE TO CLOUD ---
        function adminGeneratePin() {
            const name = document.getElementById('admin-c-name').value.trim();
            const phone = document.getElementById('admin-c-phone').value.trim();
            const event = document.getElementById('admin-c-event').value.trim();
            const btn = document.getElementById('btn-generate');

            if(!name || !phone || !event) { showToast("All fields required.", "error"); return; }

            if(btn) { btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> SAVING TO CLOUD...'; btn.disabled = true; }

            const pin = Math.floor(100000 + Math.random() * 900000).toString();
            activeClientData = { name, phone, event, pin, timestamp: Date.now() };

            db.ref('trackings/' + phone).set(activeClientData)
            .then(() => {
                document.getElementById('display-new-pin').innerText = pin;
                document.getElementById('admin-pin-result').style.display = 'block';
                document.getElementById('admin-c-name').value = ''; document.getElementById('admin-c-phone').value = ''; document.getElementById('admin-c-event').value = '';
                
                if(btn) { btn.innerHTML = '<i class="fas fa-cogs"></i> GENERATE PIN & SAVE'; btn.disabled = false; }
                showToast("Client Provisioned Successfully");
            })
            .catch((error) => {
                showToast("Firebase Error. Check Database Rules.", "error");
                console.error(error);
                if(btn) { btn.innerHTML = '<i class="fas fa-cogs"></i> GENERATE PIN & SAVE'; btn.disabled = false; }
            });
        }

        function adminShareWhatsApp() {
            if(!activeClientData) return;
            const msg = `👑 *MAA NIRMALA DJ - LIVE TRACKING* 👑\n\nHello ${activeClientData.name},\nYour logistics for *${activeClientData.event}* are active.\n\nAccess the Live Tracker here:\nhttps://maa-nirmala-dj.github.io/-tent-house./\n\n📱 *Login Number:* ${activeClientData.phone}\n🔐 *Tracking PIN:* ${activeClientData.pin}`;
            const cleanPhone = activeClientData.phone.replace(/\D/g,'');
            window.open(`https://wa.me/91${cleanPhone}?text=${encodeURIComponent(msg)}`, '_blank');
        }

        // --- 7. ADMIN: ACTIVE CLOUD NETWORK ---
        function startAdminListListener() {
            const listArea = document.getElementById('admin-active-list');
            db.ref('trackings').on('value', (snapshot) => {
                if(snapshot.exists()) {
                    let html = '';
                    snapshot.forEach(child => {
                        const data = child.val();
                        html += `
                            <div class="client-list-item">
                                <div>
                                    <h4>${data.name}</h4>
                                    <p>${data.phone} • ${data.event}</p>
                                </div>
                                <div style="display:flex; align-items:center; gap:10px;">
                                    <div class="client-list-pin">${data.pin}</div>
                                    <button class="revoke-btn" onclick="revokeAccess('${data.phone}')" title="Revoke"><i class="fas fa-trash"></i></button>
                                </div>
                            </div>
                        `;
                    });
                    listArea.innerHTML = html;
                } else {
                    listArea.innerHTML = '<div style="text-align:center; color:#555; font-size:12px; padding:10px;">No active network links.</div>';
                }
            });
        }

        function revokeAccess(phone) {
            if(confirm("Permanently revoke this client's tracking access?")) {
                db.ref('trackings/' + phone).remove().then(() => showToast("Access Revoked")).catch(e => showToast("Error", "error"));
            }
        }

        // --- 8. ADMIN: BROADCAST GPS TELEMETRY ---
        function toggleLocationBroadcast() {
            const btn = document.getElementById('btn-broadcast');
            const metrics = document.getElementById('admin-telemetrics');

            if(geoWatchId !== null) {
                // STOP
                navigator.geolocation.clearWatch(geoWatchId);
                geoWatchId = null;
                btn.innerHTML = '<i class="fas fa-location-arrow"></i> START CLOUD BROADCASTING';
                btn.style.borderColor = 'var(--gold)'; btn.style.color = 'var(--gold)'; btn.style.background = 'transparent';
                metrics.style.display = 'none';
                db.ref('admin_gps/status').set('offline');
                showToast("Transmission Terminated", "error");
            } else {
                // START
                if(!navigator.geolocation) { showToast("GPS Not Supported.", "error"); return; }
                
                btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> ACQUIRING SIGNAL...';
                
                geoWatchId = navigator.geolocation.watchPosition((pos) => {
                    btn.innerHTML = '<i class="fas fa-times-circle"></i> STOP CLOUD BROADCASTING';
                    btn.style.borderColor = 'var(--danger)'; btn.style.color = 'var(--danger)'; btn.style.background = 'rgba(255,51,51,0.1)';
                    metrics.style.display = 'grid';

                    pingCount++;
                    const speed = pos.coords.speed ? (pos.coords.speed * 3.6).toFixed(1) : 0;
                    const acc = Math.round(pos.coords.accuracy);
                    const head = pos.coords.heading ? Math.round(pos.coords.heading) + "°" : "N/A";

                    document.getElementById('a-speed').innerText = speed + " km/h";
                    document.getElementById('a-acc').innerText = "±" + acc + "m";
                    document.getElementById('a-ping').innerText = pingCount;

                    db.ref('admin_gps').set({
                        status: 'online', lat: pos.coords.latitude, lng: pos.coords.longitude,
                        speed: speed, accuracy: acc, heading: head, time: new Date().toLocaleTimeString()
                    });

                    if(pingCount === 1) showToast("Satellite Uplink Established");
                }, (err) => {
                    showToast("GPS Error. Ensure Location is ON.", "error");
                    if(geoWatchId) navigator.geolocation.clearWatch(geoWatchId);
                    geoWatchId = null;
                    btn.innerHTML = '<i class="fas fa-location-arrow"></i> START CLOUD BROADCASTING';
                }, { enableHighAccuracy: true, maximumAge: 0, timeout: 10000 });
            }
        }

        // --- 9. ADMIN: PUSH CLOUD UPDATE ---
        function adminPushUpdate() {
            const text = document.getElementById('admin-update-text').value.trim();
            const btn = document.getElementById('btn-push-update');
            if(!text) return;

            if(btn) { btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> PUSHING...'; btn.disabled = true; }

            db.ref('live_updates').push({
                text: text, time: new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}), timestamp: Date.now()
            }).then(() => {
                document.getElementById('admin-update-text').value = '';
                if(btn) { btn.innerHTML = '<i class="fas fa-paper-plane"></i> SEND ONLINE UPDATE'; btn.disabled = false; }
                showToast("Update Transmitted");
            });
        }

        // --- 10. CLIENT: WATCH CLOUD TELEMETRY ---
        function startClientWatch() {
            // Watch GPS
            db.ref('admin_gps').on('value', (snapshot) => {
                const badge = document.getElementById('client-status');
                if(snapshot.exists()) {
                    const data = snapshot.val();
                    if(data.status === 'online') {
                        badge.innerHTML = '<i class="fas fa-circle fa-beat"></i> ONLINE'; badge.style.color = 'var(--neon-green)'; badge.style.borderColor = 'var(--neon-green)';
                        document.getElementById('client-gps-status').innerHTML = `<span style="color:var(--neon-green);">Signal Locked (${data.time})</span>`;
                        document.getElementById('map-loader-text').style.display = 'none';
                        
                        document.getElementById('c-speed').innerText = data.speed + " km/h";
                        document.getElementById('c-heading').innerText = data.heading;
                        document.getElementById('c-acc').innerText = "±" + data.accuracy + "m";

                        document.getElementById('ext-map-link').style.display = 'block';
                        document.getElementById('ext-map-link').href = `https://www.google.com/maps/search/?api=1&query=${data.lat},${data.lng}`;

                        const mapUrl = `https://maps.google.com/maps?q=${data.lat},${data.lng}&z=16&output=embed`;
                        const iframe = document.getElementById('client-map-iframe');
                        if(iframe.src !== mapUrl) { iframe.src = mapUrl; }
                    } else {
                        badge.innerHTML = '<i class="fas fa-exclamation-triangle"></i> OFFLINE'; badge.style.color = 'var(--danger)'; badge.style.borderColor = 'var(--danger)';
                        document.getElementById('client-gps-status').innerHTML = `<span style="color:var(--danger);">Target Offline</span>`;
                    }
                }
            });

            // Watch Updates
            db.ref('live_updates').orderByChild('timestamp').limitToLast(10).on('value', (snapshot) => {
                const updateArea = document.getElementById('client-updates-area');
                if(snapshot.exists()) {
                    let html = '';
                    const updates = [];
                    snapshot.forEach(child => updates.push(child.val()));
                    updates.reverse().forEach(update => {
                        html += `
                            <div class="update-box">
                                <div class="update-time">${update.time}</div>
                                <div class="update-text">${update.text}</div>
                            </div>
                        `;
                    });
                    updateArea.innerHTML = html;
                }
            });
        }

        // --- 11. GLOBAL LOGOUT ---
        function systemLogout() {
            if(geoWatchId !== null) toggleLocationBroadcast();
            
            db.ref('admin_gps').off(); db.ref('live_updates').off(); db.ref('trackings').off();
            
            document.getElementById('admin-c-name').value = ''; document.getElementById('admin-c-phone').value = ''; document.getElementById('admin-c-event').value = '';
            document.getElementById('admin-pin-result').style.display = 'none';
            document.getElementById('admin-update-text').value = '';
            
            document.getElementById('client-map-iframe').src = "";
            document.getElementById('map-loader-text').style.display = 'block';
            document.getElementById('ext-map-link').style.display = 'none';
            document.getElementById('client-gps-status').innerText = "Awaiting Admin Cloud GPS...";
            
            switchView('view-gatekeeper');
            showToast("Session Terminated", "error");
        }
    </script>
</body>
</html>
