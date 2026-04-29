<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="theme-color" content="#050505">
    <title>MND Live Logistics | Cloud Tracking Portal</title>

    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@600;800;900&family=Outfit:wght@300;500;700;900&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">

    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-database-compat.js"></script>

    <style>
        /* === GLOBAL RESET & VARIABLES === */
        :root {
            --gold: #D4AF37;
            --gold-glow: rgba(212, 175, 55, 0.4);
            --neon-green: #00FA9A;
            --bg-dark: #050508;
            --panel-bg: rgba(15, 15, 20, 0.95);
            --danger: #ff3333;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; }
        body, html { width: 100vw; height: 100vh; background: var(--bg-dark); color: #fff; overflow-x: hidden; display: flex; justify-content: center; align-items: center; }

        /* Animated Background */
        .bg-map {
            position: fixed; inset: 0; z-index: -1; opacity: 0.15;
            background-image: radial-gradient(circle at 50% 50%, var(--gold-glow) 0%, transparent 60%);
            animation: pulseBg 4s infinite alternate;
        }
        @keyframes pulseBg { 0% { opacity: 0.1; } 100% { opacity: 0.25; } }

        /* === VIEW MANAGER (Strict SPA) === */
        .view-container {
            display: none; flex-direction: column; align-items: center;
            width: 100%; min-height: 100vh; padding: 40px 20px;
            animation: fadeIn 0.4s ease forwards; overflow-y: auto;
        }
        .active-view { display: flex !important; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

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
        .btn-dark { background: transparent; border: 1px solid var(--gold); color: var(--gold); margin-top: 15px; }
        .btn-danger { background: transparent; border: 1px solid var(--danger); color: var(--danger); margin-top: 20px; }

        .error-msg { color: var(--danger); font-size: 13px; font-weight: bold; margin-top: 15px; display: none; text-align: center; }

        /* === DASHBOARD LAYOUTS === */
        .dash-header { width: 100%; max-width: 600px; display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; border-bottom: 1px solid rgba(212,175,55,0.3); padding-bottom: 15px; }
        .status-badge { background: rgba(0, 250, 154, 0.1); border: 1px solid var(--neon-green); color: var(--neon-green); padding: 5px 12px; border-radius: 30px; font-size: 11px; font-weight: bold; display: flex; align-items: center; gap: 5px;}
        
        .card { width: 100%; max-width: 600px; background: rgba(0,0,0,0.6); border: 1px solid var(--glass-border); padding: 20px; border-radius: 15px; margin-bottom: 20px; backdrop-filter: blur(10px); }
        .card h3 { color: var(--gold); font-family: 'Cinzel'; margin-bottom: 15px; font-size: 18px; border-bottom: 1px dashed var(--glass-border); padding-bottom: 5px; }

        /* Admin Live List */
        .client-list-item { background: rgba(255,255,255,0.05); padding: 12px; border-radius: 8px; border-left: 3px solid var(--neon-green); margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; }
        .client-list-item h4 { margin:0 0 3px 0; font-size: 14px; }
        .client-list-item p { margin:0; font-size: 11px; color: #888; }
        .client-list-pin { font-family: monospace; font-size: 16px; color: var(--gold); font-weight: bold; background: rgba(0,0,0,0.5); padding: 4px 8px; border-radius: 4px; }

        /* Map Iframe */
        .map-wrapper { width: 100%; height: 250px; border-radius: 12px; overflow: hidden; border: 2px solid #333; position: relative; background: #111; }
        .map-iframe { width: 100%; height: 100%; border: none; filter: invert(90%) hue-rotate(180deg) brightness(80%) contrast(120%) sepia(50%); pointer-events: none; }
        .map-loader { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); color: #555; font-size: 12px; text-align: center; font-weight: bold; }

        /* Updates Feed */
        .update-box { background: rgba(212,175,55,0.05); border-left: 3px solid var(--gold); padding: 15px; border-radius: 0 8px 8px 0; margin-top: 10px; }
        .update-time { font-size: 10px; color: #888; margin-bottom: 5px; font-family: monospace; }
        .update-text { font-size: 14px; color: #fff; line-height: 1.5; }

    </style>
</head>
<body>

    <div class="bg-map"></div>

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
            <div class="status-badge"><i class="fas fa-cloud"></i> ONLINE</div>
        </div>

        <div class="card">
            <h3><i class="fas fa-user-plus"></i> 1. Create Client Access</h3>
            <input type="text" id="admin-c-name" class="mn-input" style="padding:10px; margin-bottom:10px;" placeholder="Client Name *">
            <input type="tel" id="admin-c-phone" class="mn-input" style="padding:10px; margin-bottom:10px;" placeholder="Client Phone (Used for Login) *">
            <input type="text" id="admin-c-event" class="mn-input" style="padding:10px; margin-bottom:15px;" placeholder="Event Name *">
            
            <button class="mn-btn btn-gold" id="btn-generate" onclick="adminGeneratePin()"><i class="fas fa-cogs"></i> GENERATE PIN & SAVE</button>

            <div id="admin-pin-result" style="display:none; margin-top:15px; text-align:center;">
                <p style="color:#aaa; font-size:12px;">Share this data via WhatsApp:</p>
                <div style="font-family:'Courier New'; font-size:32px; color:var(--neon-green); font-weight:900; letter-spacing:8px; margin:10px 0;" id="display-new-pin">000000</div>
                <button class="mn-btn btn-green" onclick="adminShareWhatsApp()"><i class="fab fa-whatsapp"></i> SEND TO CLIENT</button>
            </div>
        </div>

        <div class="card">
            <h3><i class="fas fa-network-wired"></i> 2. Active Network Links</h3>
            <p style="font-size:11px; color:#888; margin-bottom:10px;">Clients currently stored in the cloud database.</p>
            <div id="admin-active-list" style="max-height: 150px; overflow-y: auto;">
                <div style="text-align:center; color:#555; font-size:12px; padding:10px;">Loading database...</div>
            </div>
        </div>

        <div class="card">
            <h3><i class="fas fa-broadcast-tower"></i> 3. Broadcast Live GPS</h3>
            <p style="font-size:12px; color:#aaa; margin-bottom:15px;">Push your real-time coordinates to Firebase for clients to see.</p>
            
            <button class="mn-btn btn-dark" id="btn-broadcast" onclick="toggleLocationBroadcast()"><i class="fas fa-location-arrow"></i> START BROADCASTING</button>
            <p id="broadcast-status" style="color:var(--neon-green); font-size:12px; text-align:center; margin-top:10px; display:none; font-weight:bold;"><i class="fas fa-circle fa-beat"></i> GPS Cloud Sync Active...</p>
        </div>

        <div class="card">
            <h3><i class="fas fa-comment-dots"></i> 4. Push Cloud Update</h3>
            <textarea id="admin-update-text" class="mn-input" rows="2" style="resize:none; padding:10px;" placeholder="e.g., 'Truck has reached Katoria bypass...'"></textarea>
            <button class="mn-btn btn-gold" id="btn-push-update" onclick="adminPushUpdate()"><i class="fas fa-paper-plane"></i> SEND UPDATE</button>
        </div>

        <button class="mn-btn btn-danger" onclick="systemLogout()"><i class="fas fa-sign-out-alt"></i> SECURE LOGOUT</button>
    </div>

    <div id="view-client" class="view-container">
        <div class="dash-header">
            <div style="width:70%;">
                <h2 id="client-dash-event" style="color:var(--gold); font-family:'Cinzel'; margin:0; font-size:18px; white-space:nowrap; overflow:hidden; text-overflow:ellipsis;">Loading Event...</h2>
                <span style="font-size:12px; color:#888;">Client: <span id="client-dash-name">Loading...</span></span>
            </div>
            <div class="status-badge"><i class="fas fa-circle fa-beat"></i> LIVE CLOUD</div>
        </div>

        <div class="card" style="padding: 10px;">
            <div style="display:flex; justify-content:space-between; align-items:center; padding: 0 10px 10px; border-bottom: 1px dashed #333; margin-bottom: 10px;">
                <h3 style="margin:0; border:none; padding:0; font-size:15px;"><i class="fas fa-map-marker-alt"></i> Logistics Tracker</h3>
                <span id="client-gps-status" style="font-size:10px; color:#888;">Awaiting Cloud GPS...</span>
            </div>
            
            <div class="map-wrapper">
                <div class="map-loader" id="map-loader-text"><i class="fas fa-satellite fa-spin fa-2x"></i><br><br>SYNCING SIGNAL...</div>
                <iframe id="client-map-iframe" class="map-iframe" src=""></iframe>
            </div>
        </div>

        <div class="card">
            <h3><i class="fas fa-bolt"></i> Live HQ Updates</h3>
            <div id="client-updates-area">
                <div style="text-align:center; color:#555; font-size:12px; padding:10px;">Awaiting updates from Firebase...</div>
            </div>
        </div>

        <button class="mn-btn btn-danger" onclick="systemLogout()"><i class="fas fa-power-off"></i> DISCONNECT SESSION</button>
    </div>


    <script>
        // --- 1. FIREBASE CLOUD CONFIGURATION ---
        const firebaseConfig = {
            apiKey: "AIzaSyAyydGIkA9fDUxrBtKWHiY3q7adpnpiWe0",
            authDomain: "mnd-40060.firebaseapp.com",
            databaseURL: "https://mnd-40060-default-rtdb.asia-southeast1.firebasedatabase.app",
            projectId: "mnd-40060"
        };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.database();

        // Admin Auth Configuration
        const ADMIN_NUMBERS = ["9771617808", "9153635378", "7294969938", "+918544341240", "8544341240"];
        const MASTER_PIN = "121120";
        
        let broadcastInterval = null;
        let activeClientData = null; // Used for WhatsApp sharing
        let clientPhoneListener = ""; // Used to track which client node to listen to

        // --- 2. VIEW ROUTER ---
        function switchView(viewId) {
            document.querySelectorAll('.view-container').forEach(el => el.classList.remove('active-view'));
            document.getElementById(viewId).classList.add('active-view');
            document.querySelectorAll('.error-msg').forEach(el => el.style.display = 'none');
            window.scrollTo(0,0);
        }

        // --- 3. THE SMART CLOUD GATEKEEPER ---
        function processLogin() {
            const phone = document.getElementById('login-phone').value.trim();
            const pin = document.getElementById('login-pin').value.trim();
            const err = document.getElementById('login-error');
            const btn = document.getElementById('login-btn');

            if(!phone || !pin) { err.innerText = "Please enter both fields."; err.style.display = 'block'; return; }

            // A. Check if Admin
            if(ADMIN_NUMBERS.includes(phone) && pin === MASTER_PIN) {
                document.getElementById('login-phone').value = ''; document.getElementById('login-pin').value = '';
                switchView('view-admin');
                startAdminListListener(); // Fetch active clients from Firebase
                return;
            }

            // B. Check Client against Firebase Database
            btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> QUERYING CLOUD...';
            btn.disabled = true;

            // Look up the specific phone number in the tracking database
            db.ref('active_trackings/' + phone).once('value').then((snapshot) => {
                btn.innerHTML = '<i class="fas fa-fingerprint"></i> AUTHENTICATE CLOUD';
                btn.disabled = false;

                if (snapshot.exists()) {
                    const clientData = snapshot.val();
                    
                    // Verify the PIN matches what is stored online
                    if(clientData.pin === pin) {
                        // Success!
                        document.getElementById('login-phone').value = ''; document.getElementById('login-pin').value = '';
                        document.getElementById('client-dash-name').innerText = clientData.name;
                        document.getElementById('client-dash-event').innerText = clientData.event;
                        
                        clientPhoneListener = phone; // Save this to track specific client data if needed
                        startClientWatchListeners(); // Turn on Cloud listeners
                        switchView('view-client');
                    } else {
                        err.innerHTML = "<i class='fas fa-times-circle'></i> Access Denied. Incorrect PIN.";
                        err.style.display = 'block';
                    }
                } else {
                    err.innerHTML = "<i class='fas fa-times-circle'></i> Access Denied. Number not found in database.";
                    err.style.display = 'block';
                }
            }).catch((error) => {
                btn.innerHTML = '<i class="fas fa-fingerprint"></i> AUTHENTICATE CLOUD';
                btn.disabled = false;
                err.innerHTML = "<i class='fas fa-wifi'></i> Network Error. Check connection.";
                err.style.display = 'block';
            });
        }

        // --- 4. ADMIN: GENERATE SECURE PIN & SAVE TO FIREBASE ---
        function adminGeneratePin() {
            const name = document.getElementById('admin-c-name').value.trim();
            const phone = document.getElementById('admin-c-phone').value.trim();
            const event = document.getElementById('admin-c-event').value.trim();
            const btn = document.getElementById('btn-generate');

            if(!name || !phone || !event) { alert("⚠️ All fields required to generate PIN."); return; }

            btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> SAVING TO CLOUD...';
            btn.disabled = true;

            // Generate 6-Digit PIN
            const pin = Math.floor(100000 + Math.random() * 900000).toString();
            activeClientData = { name, phone, event, pin };

            // Save to Firebase under the phone number
            db.ref('active_trackings/' + phone).set({
                name: name,
                phone: phone,
                event: event,
                pin: pin,
                timestamp: Date.now()
            }).then(() => {
                // Update UI
                document.getElementById('display-new-pin').innerText = pin;
                document.getElementById('admin-pin-result').style.display = 'block';
                
                // Clear inputs
                document.getElementById('admin-c-name').value = '';
                document.getElementById('admin-c-phone').value = '';
                document.getElementById('admin-c-event').value = '';

                btn.innerHTML = '<i class="fas fa-cogs"></i> GENERATE PIN & SAVE';
                btn.disabled = false;
            }).catch((error) => {
                alert("Database Error: " + error.message);
                btn.innerHTML = '<i class="fas fa-cogs"></i> GENERATE PIN & SAVE';
                btn.disabled = false;
            });
        }

        function adminShareWhatsApp() {
            if(!activeClientData) return;
            const msg = `👑 *MAA NIRMALA DJ - CLOUD TRACKING* 👑\n\nHello ${activeClientData.name},\nYour logistics for *${activeClientData.event}* are now online.\n\nAccess the Live Cloud Tracker here:\nhttps://maa-nirmala-dj.github.io/Live-tracking/\n\n📱 *Login Number:* ${activeClientData.phone}\n🔐 *Tracking PIN:* ${activeClientData.pin}`;
            const cleanPhone = activeClientData.phone.replace(/\D/g,'');
            window.open(`https://wa.me/91${cleanPhone}?text=${encodeURIComponent(msg)}`, '_blank');
        }

        // --- 5. ADMIN: WATCH ACTIVE NETWORK (FIREBASE) ---
        function startAdminListListener() {
            const listArea = document.getElementById('admin-active-list');
            
            // Listen for changes in the active_trackings node
            db.ref('active_trackings').on('value', (snapshot) => {
                if(snapshot.exists()) {
                    let html = '';
                    snapshot.forEach((childSnapshot) => {
                        const data = childSnapshot.val();
                        html += `
                            <div class="client-list-item">
                                <div>
                                    <h4>${data.name}</h4>
                                    <p>${data.phone} • ${data.event}</p>
                                </div>
                                <div class="client-list-pin">${data.pin}</div>
                            </div>
                        `;
                    });
                    listArea.innerHTML = html;
                } else {
                    listArea.innerHTML = '<div style="text-align:center; color:#555; font-size:12px; padding:10px;">No active tracking sessions.</div>';
                }
            });
        }

        // --- 6. ADMIN: BROADCAST GPS TO FIREBASE ---
        function toggleLocationBroadcast() {
            const btn = document.getElementById('btn-broadcast');
            const status = document.getElementById('broadcast-status');

            if(broadcastInterval) {
                // Stop Broadcasting
                clearInterval(broadcastInterval);
                broadcastInterval = null;
                btn.innerHTML = '<i class="fas fa-location-arrow"></i> START BROADCASTING';
                btn.style.borderColor = 'var(--gold)'; btn.style.color = 'var(--gold)';
                status.style.display = 'none';
            } else {
                // Start Broadcasting
                if(!navigator.geolocation) { alert("Browser does not support GPS."); return; }
                
                btn.innerHTML = '<i class="fas fa-times-circle"></i> STOP BROADCASTING';
                btn.style.borderColor = 'var(--danger)'; btn.style.color = 'var(--danger)';
                status.style.display = 'block';

                // Update location immediately, then every 10 seconds
                pushLocationToCloud(); 
                broadcastInterval = setInterval(pushLocationToCloud, 10000);
            }
        }

        function pushLocationToCloud() {
            navigator.geolocation.getCurrentPosition((pos) => {
                const lat = pos.coords.latitude;
                const lng = pos.coords.longitude;
                // Push globally to Firebase so any logged-in client can read it
                db.ref('admin_location').set({
                    lat: lat,
                    lng: lng,
                    time: new Date().toLocaleTimeString()
                });
            }, (err) => {
                console.error("GPS Error", err);
            }, { enableHighAccuracy: true });
        }

        // --- 7. ADMIN: PUSH CLOUD UPDATE ---
        function adminPushUpdate() {
            const text = document.getElementById('admin-update-text').value.trim();
            const btn = document.getElementById('btn-push-update');
            if(!text) return;

            btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> PUSHING...';
            btn.disabled = true;

            // Push a new record into a list of updates
            db.ref('live_updates').push({
                text: text,
                time: new Date().toLocaleTimeString(),
                timestamp: Date.now()
            }).then(() => {
                document.getElementById('admin-update-text').value = '';
                btn.innerHTML = '<i class="fas fa-paper-plane"></i> SEND UPDATE';
                btn.disabled = false;
            });
        }

        // --- 8. CLIENT: WATCH CLOUD FOR UPDATES ---
        function startClientWatchListeners() {
            // 1. Listen for Admin GPS Changes
            db.ref('admin_location').on('value', (snapshot) => {
                if(snapshot.exists()) {
                    const data = snapshot.val();
                    document.getElementById('client-gps-status').innerHTML = `<span style="color:var(--neon-green);">GPS Link Active (${data.time})</span>`;
                    document.getElementById('map-loader-text').style.display = 'none';
                    
                    // Update Iframe Map cleanly
                    const mapUrl = `https://maps.google.com/maps?q=${data.lat},${data.lng}&z=16&output=embed`;
                    const iframe = document.getElementById('client-map-iframe');
                    if(iframe.src !== mapUrl) { iframe.src = mapUrl; }
                }
            });

            // 2. Listen for Text Updates (OrderBy child 'timestamp' to get chronological order)
            db.ref('live_updates').orderByChild('timestamp').limitToLast(10).on('value', (snapshot) => {
                const updateArea = document.getElementById('client-updates-area');
                if(snapshot.exists()) {
                    let html = '';
                    const updates = [];
                    // Convert to array to reverse order (newest top)
                    snapshot.forEach((child) => { updates.push(child.val()); });
                    updates.reverse().forEach((update) => {
                        html += `
                            <div class="update-box">
                                <div class="update-time">${update.time}</div>
                                <div class="update-text">${update.text}</div>
                            </div>
                        `;
                    });
                    updateArea.innerHTML = html;
                } else {
                    updateArea.innerHTML = '<div style="text-align:center; color:#555; font-size:12px; padding:10px;">Awaiting updates from Firebase...</div>';
                }
            });
        }

        // --- 9. SYSTEM LOGOUT ---
        function systemLogout() {
            // Stop Admin Broadcasting if running
            if(broadcastInterval) toggleLocationBroadcast();
            
            // Turn off Firebase Listeners to save bandwidth
            db.ref('active_trackings').off();
            db.ref('admin_location').off();
            db.ref('live_updates').off();
            clientPhoneListener = "";
            
            // Clear Client UI
            document.getElementById('client-map-iframe').src = "";
            document.getElementById('map-loader-text').style.display = 'block';
            document.getElementById('client-gps-status').innerText = "Awaiting Cloud GPS...";
            document.getElementById('client-updates-area').innerHTML = '<div style="text-align:center; color:#555; font-size:12px; padding:10px;">Awaiting updates from Firebase...</div>';
            
            // Clear Admin UI
            document.getElementById('admin-pin-result').style.display = 'none';
            document.getElementById('admin-update-text').value = '';
            
            switchView('view-gatekeeper');
        }
    </script>
</body>
</html>
