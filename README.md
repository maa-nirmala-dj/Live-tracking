<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="theme-color" id="theme-color-meta" content="#050505">
    <meta name="application-name" content="MNDs Hub">
    
    <title>👑 MND HUB — Maa Nirmala DJ & Tent House | Full Screen Interface</title>
    
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@500;700;900&family=Outfit:wght@300;400;600;800&family=Rajdhani:wght@500;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">

    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-database-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-storage-compat.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs"></script>
    <script src="https://cdn.jsdelivr.net/npm/@tensorflow-models/blazeface"></script>

    <style>
        /* =========================================================================
           GLOBAL VARIABLES & RESET
           ========================================================================= */
        :root {
            --bg-base: #030303; --bg-panel: rgba(10, 10, 10, 0.85); --bg-card: rgba(20, 20, 20, 0.9);
            --border-color: rgba(212, 175, 55, 0.3); --gold-primary: #D4AF37; --gold-shine: #FFD700;
            --neon-pink: #FF00FF; --neon-cyan: #00E5FF; --neon-lime: #00FA9A;
            --text-main: #FFFFFF; --text-sub: #b3b3b3;
            --font-head: 'Cinzel', serif; --font-body: 'Outfit', sans-serif;
            --dynamic-shift: 15s; --header-height: 65px; --footer-height: 75px;
            --glass-blur: blur(25px); --nav-glass: blur(30px);
        }

        [data-theme="light"] {
            --bg-base: #f2f2f2; --bg-card: rgba(255, 255, 255, 0.95); --border-color: rgba(180, 140, 40, 0.3);
            --gold-primary: #b08d26; --gold-shine: #d4a017; --text-main: #111111; --text-sub: #555555;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; outline: none; user-select: none; -webkit-tap-highlight-color: transparent; }
        body, html { width: 100vw; min-height: 100vh; background-color: var(--bg-base); color: var(--text-main); font-family: var(--font-body); overflow-x: hidden; -webkit-overflow-scrolling: touch; transition: background-color 0.4s ease, color 0.4s ease; }
        ::-webkit-scrollbar { width: 4px; } ::-webkit-scrollbar-track { background: #000; } ::-webkit-scrollbar-thumb { background: var(--gold-primary); border-radius: 10px; }
        .hidden { display: none !important; }

        /* =========================================================================
           SMART SCROLL HIDING FOR FLOATING BUTTONS
           ========================================================================= */
        .ai-trigger, .live-track-trigger, .bottom-nav { transition: transform 0.5s cubic-bezier(0.25, 0.8, 0.25, 1), opacity 0.5s ease; z-index: 4000; }
        .hide-on-scroll { transform: translateY(150px) scale(0.8) !important; opacity: 0; pointer-events: none; }

        /* =========================================================================
           GATEKEEPER (ENTRY SCREEN)
           ========================================================================= */
        #gatekeeper { position: fixed; inset: 0; z-index: 99999; background: var(--bg-base); display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 20px; text-align: center; background-image: radial-gradient(circle at 50% 50%, rgba(212, 175, 55, 0.1) 0%, transparent 80%); transition: opacity 0.8s ease, visibility 0.8s; }
        .gate-card { width: 100%; max-width: 400px; max-height: 85vh; background: rgba(10, 10, 10, 0.95); border: 1px solid var(--gold-primary); border-radius: 20px; padding: 40px 25px; box-shadow: 0 0 80px rgba(212, 175, 55, 0.15); backdrop-filter: blur(20px); position: relative; overflow-y: auto; overflow-x: hidden; animation: pulseCard 4s infinite alternate; }
        @keyframes pulseCard { from { transform: scale(1); border-color: rgba(212,175,55,0.3); } to { transform: scale(1.005); border-color: rgba(212,175,55,0.6); } }
        .gate-img-frame { width: 110px; height: 110px; margin: 0 auto 20px; border-radius: 50%; padding: 3px; border: 2px solid var(--gold-primary); box-shadow: 0 0 30px rgba(212, 175, 55, 0.3); }
        .gate-img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }
        .gate-title { font-family: 'Cinzel'; color: var(--gold-primary); font-size: 26px; margin-bottom: 5px; text-shadow: 0 0 15px rgba(212, 175, 55, 0.5); font-weight: 700; }
        .gate-sub { color: #666; font-size: 11px; letter-spacing: 4px; margin-bottom: 30px; font-family: 'Rajdhani'; text-transform: uppercase; }
        .gate-input-group { position: relative; margin-bottom: 15px; text-align: left; }
        .gate-input { width: 100%; padding: 15px 15px 15px 45px; background: rgba(255, 255, 255, 0.05); border: 1px solid #333; color: #fff; font-size: 16px; font-family: 'Rajdhani'; transition: 0.3s; border-radius: 10px; box-sizing: border-box; }
        .gate-input:focus { border-color: var(--gold-primary); background: rgba(212, 175, 55, 0.08); box-shadow: 0 0 15px rgba(212,175,55,0.1); }
        .gate-icon { position: absolute; left: 15px; top: 17px; color: #555; transition: 0.3s; }
        .gate-input:focus + .gate-icon { color: var(--gold-primary); }
        .gate-btn { width: 100%; padding: 16px; background: linear-gradient(135deg, var(--gold-primary), #997d2d); color: #000; font-weight: 800; font-size: 16px; border: none; cursor: pointer; border-radius: 10px; text-transform: uppercase; letter-spacing: 2px; margin-top: 15px; box-shadow: 0 5px 25px rgba(212, 175, 55, 0.3); transition: 0.3s; }
        .gate-btn:active { transform: scale(0.98); }
        .loading-text { margin-top: 20px; font-size: 12px; color: var(--gold-primary); font-family: 'Rajdhani'; display: none; letter-spacing: 1px; font-weight: bold; }

        /* =========================================================================
           MAIN UI LAYOUT
           ========================================================================= */
        #main-interface { display: none; opacity: 0; transition: opacity 1.5s ease; padding-top: 65px; padding-bottom: 90px; }
        
        #dynamic-bg { position: fixed; top: 0; left: 0; right: 0; bottom: 0; z-index: -10; background-color: var(--bg-base); background-image: radial-gradient(circle at 15% 25%, var(--neon-pink-dim), transparent 45%), radial-gradient(circle at 85% 75%, var(--neon-cyan-dim), transparent 45%), radial-gradient(circle at 50% 50%, transparent 0%, #000 100%); animation: bodyHueShift var(--dynamic-shift) infinite linear; pointer-events: none; }
        @keyframes bodyHueShift { 0% { filter: hue-rotate(0deg); } 100% { filter: hue-rotate(360deg); } }

        /* Header */
        header { position: fixed; top: 0; left: 0; right: 0; height: var(--header-height); background: rgba(5, 5, 5, 0.90); padding: 0 20px; border-bottom: 1px solid rgba(255,255,255,0.1); display: flex; flex-direction: column; justify-content: center; backdrop-filter: blur(15px); z-index: 1000; box-shadow: 0 5px 20px rgba(0,0,0,0.5); }
        .header-top { display: flex; justify-content: space-between; align-items: center; }
        .logo-block { font-family: var(--font-head); font-weight: 900; font-size: 20px; color: var(--gold-primary); text-shadow: 0 0 12px rgba(212,175,55,0.5); }
        .control-icons { display: flex; gap: 15px; color: #fff; font-size: 20px; cursor: pointer; align-items: center; }
        .control-icons i:hover { color: var(--gold-primary); text-shadow: 0 0 10px var(--gold-primary); }

        /* Google Translate Square Button */
        #google_translate_element { width: 38px; height: 38px; overflow: hidden; border-radius: 8px; }
        .goog-te-gadget-simple { background-color: rgba(255, 255, 255, 0.05) !important; border: 1px solid #D4AF37 !important; border-radius: 8px !important; width: 38px !important; height: 38px !important; padding: 0 !important; display: flex !important; justify-content: center !important; align-items: center !important; box-sizing: border-box !important; position: relative; cursor: pointer; transition: 0.3s ease; }
        .goog-te-gadget-simple:hover { background-color: rgba(212,175,55,0.15) !important; box-shadow: 0 0 10px rgba(212,175,55,0.4) !important; }
        .goog-te-gadget-simple span, .goog-te-gadget-icon, .goog-te-menu-value span { display: none !important; }
        .goog-te-gadget-simple::after { content: '\f1ab'; font-family: 'Font Awesome 6 Free', 'Font Awesome 5 Free'; font-weight: 900; color: #D4AF37; font-size: 16px; }
        .goog-logo-link { display: none !important; } .goog-te-gadget { color: transparent !important; }

        /* Bottom Nav */
        .bottom-nav { position: fixed; bottom: 0; left: 0; width: 100%; height: var(--footer-height); background: rgba(5, 5, 5, 0.95); backdrop-filter: var(--nav-glass); border-top: 1px solid var(--border-color); display: flex; justify-content: space-around; align-items: center; box-shadow: 0 -5px 20px rgba(0,0,0,0.5); }
        .bottom-nav::before { content: ''; position: absolute; top: 0; left: 0; width: 100%; height: 3px; background: linear-gradient(to right, var(--neon-cyan), var(--gold-primary), var(--neon-pink)); box-shadow: 0 0 20px 2px var(--gold-primary); animation: bottomNavShine 3s infinite linear; }
        .nav-item { flex: 1; display: flex; flex-direction: column; align-items: center; justify-content: center; color: var(--text-sub); gap: 4px; cursor: pointer; transition: 0.3s; }
        .nav-item i { font-size: 22px; }
        .nav-item span { font-size: 11px; font-family: var(--font-body); text-transform: uppercase; font-weight: 800; }
        .nav-item:hover i { color: #fff; transform: translateY(-3px); }
        .nav-item.active { color: var(--gold-primary); text-shadow: 0 0 15px var(--gold-primary); }
        .nav-item.active i { transform: translateY(-3px); }

        /* Modals & Overlays */
        .mn-modal-overlay { display: none; position: fixed; inset: 0; background: rgba(5,5,8,0.95); z-index: 9999999; justify-content: center; align-items: center; backdrop-filter: blur(12px); animation: fadeInOverlay 0.3s ease; }
        .mn-modal-box { background: linear-gradient(135deg, #111 0%, #050505 100%); border: 2px solid var(--gold-primary); border-radius: 16px; width: 95%; max-width: 500px; max-height: 90vh; display: flex; flex-direction: column; position: relative; box-shadow: 0 20px 50px rgba(0,0,0,0.9), inset 0 0 30px rgba(212, 175, 55, 0.1); animation: slideDownFade 0.4s cubic-bezier(0.25, 0.8, 0.25, 1) forwards; }
        .mn-modal-header { padding: 20px; text-align: center; border-bottom: 1px solid rgba(212,175,55,0.3); position: relative; }
        .mn-modal-close { position: absolute; top: 15px; right: 20px; color: var(--gold-primary); font-size: 35px; line-height: 1; cursor: pointer; transition: 0.3s; z-index: 10; }
        .mn-modal-content { padding: 20px; overflow-y: auto; flex-grow: 1; }
        
        @keyframes fadeInOverlay { from { opacity: 0; } to { opacity: 1; } }
        @keyframes slideDownFade { from { opacity: 0; transform: translateY(-30px); } to { opacity: 1; transform: translateY(0); } }

        /* Floating Triggers */
        .ai-trigger { position: fixed; bottom: 90px; right: 25px; width: 60px; height: 60px; border-radius: 50%; background: linear-gradient(135deg, var(--gold-primary), #997d2d); display: flex; align-items: center; justify-content: center; box-shadow: 0 0 30px rgba(212,175,55,0.4); cursor: pointer; animation: pulse 3s infinite; }
        @keyframes pulse { 0% { transform: scale(1); } 50% { transform: scale(1.05); } 100% { transform: scale(1); } }
        
        .live-track-trigger { position: fixed; bottom: 165px; right: 25px; width: 60px; height: 60px; border-radius: 50%; background: rgba(212, 175, 55, 0.15); backdrop-filter: blur(8px); -webkit-backdrop-filter: blur(8px); border: 1px solid rgba(212, 175, 55, 0.6); display: flex; align-items: center; justify-content: center; color: #D4AF37; font-size: 24px; cursor: pointer; box-shadow: 0 0 20px rgba(212, 175, 55, 0.2); }
        .live-track-trigger:hover { background: rgba(212, 175, 55, 0.8); color: #000; box-shadow: 0 0 30px rgba(212, 175, 55, 0.6); transform: scale(1.05); }

        /* Generic Form Elements */
        .mnd-input { width: 100%; padding: 14px 15px; background: rgba(255, 255, 255, 0.05); border: 1px solid rgba(212, 175, 55, 0.3); color: #fff; border-radius: 8px; outline: none; font-family: 'Outfit', sans-serif; box-sizing: border-box; transition: all 0.3s ease; margin-bottom: 15px; font-size: 14px; }
        .mnd-input:focus { border-color: #D4AF37; background: rgba(212, 175, 55, 0.05); box-shadow: 0 0 12px rgba(212, 175, 55, 0.4); }
        .mnd-btn { width: 100%; padding: 15px; border-radius: 8px; font-family: 'Outfit', sans-serif; font-weight: bold; font-size: 15px; cursor: pointer; transition: 0.3s; border: none; letter-spacing: 1px; display: flex; justify-content: center; align-items: center; gap: 8px; }
        .btn-gold { background: var(--gold-primary); color: #000; box-shadow: 0 5px 20px rgba(212, 175, 55, 0.3); }
        .btn-outline { background: transparent; border: 1px solid var(--gold-primary); color: var(--gold-primary); }
    </style>
</head>
<body data-theme="dark">

    <div id="gatekeeper">
        <div class="gate-card">
            <div class="gate-img-frame"><img src="https://i.postimg.cc/76mz1v2j/file-0000000090a471fa84cbecd48a774885.png" class="gate-img" alt="Profile"></div>
            <h2 class="gate-title">MAA NIRMALA DJ</h2>
            <div class="gate-sub">SECURE PORTFOLIO GATEWAY</div>
            <div class="gate-input-group"><input type="text" id="g-name" class="gate-input" placeholder="YOUR FULL NAME"><i class="fas fa-user gate-icon"></i></div>
            <div class="gate-input-group"><input type="tel" id="g-phone" class="gate-input" placeholder="YOUR MOBILE NUMBER"><i class="fas fa-phone gate-icon"></i></div>
            <button class="gate-btn" id="btn-verify" onclick="initiateSecureEntry()"><i class="fas fa-fingerprint"></i> VERIFY & ENTER</button>
            <div class="loading-text" id="g-status"><i class="fas fa-circle-notch fa-spin"></i> OPENING WAIT FEW SEC...</div>
        </div>
    </div>

    <div id="main-interface">
        <div id="dynamic-bg"></div>

        <header>
            <div class="header-top">
                <div class="logo-block"><i class="fas fa-crown"></i> MND CREATORS</div>
                <div class="control-icons">
                    <div id="google_translate_element"></div>
                    <div class="nav-btn-square theme-btn" onclick="themeSwitch()"><i class="fas fa-sun" id="themeIcon"></i></div>
                    <div class="nav-btn-square set-btn" onclick="openModal('masterSettingsOverlay')"><i class="fas fa-cog"></i></div>
                </div>
            </div>
        </header>

        <div id="main-scroll-content" style="width: 100%; padding: 0 15px; display: flex; flex-direction: column; align-items: center; overflow-y: auto;">
            <div style="max-width: 800px; width: 100%;">
                
                <div style="position: relative; width: 100vw; margin-left: calc(-50vw + 50%); height: 45vh; min-height: 400px; border-radius: 0 0 35px 35px; overflow: hidden; border-bottom: 1px solid var(--border-color); box-shadow: 0 10px 40px rgba(0,0,0,0.9);">
                    <img src="https://i.postimg.cc/g20XqtDW/IMG_20260303_121446.png" style="width: 100%; height: 100%; object-fit: cover; object-position: center 20%;">
                    <div style="position: absolute; inset: 0; background: linear-gradient(to bottom, transparent 0%, rgba(5,5,5,0.8) 50%, #050505 100%);"></div>
                    <div style="position: absolute; bottom: 20px; left: 0; width: 100%; text-align: center; padding: 0 20px;">
                        <h1 style="font-family: 'Cinzel'; font-size: 38px; font-weight: 900; margin: 0; background: linear-gradient(to right, #FFD700, #FF1493, #8A2BE2, #00FA9A, #FFD700); background-size: 200% auto; -webkit-background-clip: text; -webkit-text-fill-color: transparent; animation: textDeepShine 3s linear infinite;">MAA NIRMALA DJ</h1>
                        <span style="font-family: 'Cinzel'; font-size: 18px; font-weight: 700; letter-spacing: 4px; color: var(--gold-primary);">STAY ROYAL, BE PREMIUM</span>
                    </div>
                </div>
                <style>@keyframes textDeepShine { 0% { background-position: 0% 50%; } 100% { background-position: 200% 50%; } }</style>

                <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-top: 30px;">
                    <a href="tel:+919771617808" class="mnd-btn btn-outline" style="text-decoration: none;"><i class="fas fa-phone-alt"></i> CALL NOW</a>
                    <button class="mnd-btn btn-gold" onclick="openModal('bookModal')"><i class="fas fa-calendar-check"></i> BOOK NOW</button>
                </div>

                <div style="background: var(--bg-card); border: 1px solid var(--border-color); border-radius: 16px; padding: 20px; margin-top: 25px; text-align: center; box-shadow: 0 5px 20px rgba(0,0,0,0.3);">
                    <h3 style="color: var(--gold-primary); font-family: 'Cinzel'; margin-bottom: 10px; font-size: 20px;"><i class="fas fa-bolt"></i> Event Highlights</h3>
                    <p style="font-size: 14px; line-height: 1.6; color: #ddd; margin-bottom: 15px;">Experience premium streaming, high-bass audio, and cinematic lighting setups for your grand occasions.</p>
                    <button class="mnd-btn btn-outline" onclick="openModal('galleryModalOverlay')">VIEW LIVE GALLERY</button>
                </div>

                <div style="height: 100px;"></div> </div>
        </div>

        <div class="ai-trigger" onclick="openModal('aiModal')"><i class="fas fa-brain" style="font-size:24px; color:#fff;"></i></div>
        <div class="live-track-trigger" onclick="openModal('liveTrackModalOverlay')"><div class="radar-ping"></div><i class="fas fa-satellite-dish"></i></div>

        <div class="bottom-nav">
            <div class="nav-item active" onclick="navAction('home')"><i class="fas fa-home"></i><span>Home</span></div>
            <div class="nav-item" onclick="openModal('linksModal')"><i class="fas fa-link"></i><span>Links</span></div>
            <div class="nav-item" onclick="openModal('socialModalOverlay')"><i class="fas fa-camera-retro"></i><span>Social</span></div>
            <div class="nav-item" onclick="openModal('authModal')"><i class="fas fa-user-shield"></i><span>Admin</span></div>
        </div>

    </div>

    <div id="liveTrackModalOverlay" class="mn-modal-overlay" onclick="closeOnOutside(event, 'liveTrackModalOverlay')">
        <div class="mn-modal-box">
            <div class="mn-modal-header">
                <span class="mn-modal-close" onclick="closeModal('liveTrackModalOverlay')">×</span>
                <h2 style="margin:0; color:var(--gold-primary); font-family:'Cinzel'; font-size:22px; font-weight:800;"><i class="fas fa-route"></i> Live Event Portal</h2>
            </div>
            <div class="mn-modal-content">
                <div id="clientTrackLoginArea">
                    <p style="color:#aaa; text-align:center; font-size:13px; margin-bottom:20px;">Enter your registered mobile number and the 6-digit tracking PIN provided by Management.</p>
                    <input type="tel" id="client-track-phone" class="mnd-input" placeholder="Your Registered Mobile Number *">
                    <input type="password" id="client-track-pin" class="mnd-input" placeholder="6-Digit Tracking PIN *" maxlength="6" style="letter-spacing: 5px; text-align: center; font-size: 20px; font-weight: bold;">
                    <button class="mnd-btn btn-gold" onclick="clientLoginToTracking()"><i class="fas fa-unlock-alt"></i> ACCESS LIVE TRACKING</button>
                </div>
                <div id="clientTrackDashboardArea" class="hidden" style="flex-direction:column; align-items:center;">
                    <div style="background: rgba(0, 250, 154, 0.1); border: 1px solid #00fa9a; padding: 10px 20px; border-radius: 30px; color: #00fa9a; font-size: 12px; font-weight: bold; margin-bottom: 20px;"><i class="fas fa-circle fa-beat"></i> CONNECTION SECURE</div>
                    <div style="width: 100%; background: rgba(255,255,255,0.05); border-radius: 12px; padding: 15px; border: 1px solid rgba(212,175,55,0.3); margin-bottom: 15px;">
                        <h4 style="color:#D4AF37; font-family:'Cinzel'; margin: 0 0 10px 0;">Event Logistics</h4>
                        <p style="color:#fff; font-size:14px; margin: 0 0 5px 0;"><strong>Event:</strong> <span id="dash-event-name">Loading...</span></p>
                        <p style="color:#aaa; font-size:12px; margin: 0;"><strong>Admin:</strong> Maa Nirmala HQ</p>
                    </div>
                    <div style="width: 100%; background: rgba(255,255,255,0.05); border-radius: 12px; padding: 15px; border: 1px solid rgba(212,175,55,0.3); margin-bottom: 20px; text-align: center;">
                        <h4 style="color:#D4AF37; font-family:'Cinzel'; margin: 0 0 10px 0;"><i class="fas fa-map-marker-alt"></i> Admin Live GPS</h4>
                        <p style="color:#fff; font-size:13px; margin-bottom: 15px;">Location broadcast is active.</p>
                        <a id="dash-map-link" href="#" target="_blank" class="mnd-btn btn-gold" style="text-decoration: none;"><i class="fas fa-location-arrow"></i> OPEN LIVE MAP</a>
                    </div>
                    <button class="mnd-btn btn-outline" style="border-color: #ff3333; color: #ff3333;" onclick="logoutTracking()"><i class="fas fa-sign-out-alt"></i> DISCONNECT</button>
                </div>
            </div>
        </div>
    </div>

    <div id="authModal" class="mn-modal-overlay" onclick="closeOnOutside(event, 'authModal')">
        <div class="mn-modal-box">
            <div class="mn-modal-header">
                <span class="mn-modal-close" onclick="closeModal('authModal')">×</span>
                <h2 style="margin:0; color:var(--gold-primary); font-family:'Cinzel'; font-size:22px; font-weight:800;"><i class="fas fa-user-shield"></i> Management Hub</h2>
            </div>
            <div class="mn-modal-content">
                <div id="adminLoginArea">
                    <p style="color:#aaa; font-size:13px; text-align:center; margin-bottom:20px;">Restricted access. Enter management credentials.</p>
                    <input type="tel" id="admin-phone" class="mnd-input" placeholder="Admin Mobile Number *">
                    <input type="password" id="admin-pin" class="mnd-input" placeholder="Secure Auth Code *">
                    <button class="mnd-btn btn-gold" onclick="verifyAdmin()"><i class="fas fa-shield-alt"></i> VERIFY LOGIN</button>
                </div>
                <div id="adminPanel" class="hidden">
                    <p style="color:#00FA9A; font-size:12px; font-weight:bold; text-align:center; margin-bottom:20px;"><i class="fas fa-check-circle"></i> ADMIN VERIFIED</p>
                    
                    <div style="background: rgba(255,255,255,0.05); border: 1px solid var(--gold-primary); border-radius: 12px; padding: 15px; margin-bottom: 20px;">
                        <h4 style="color:#D4AF37; font-family:'Cinzel'; margin: 0 0 10px 0;"><i class="fas fa-satellite"></i> Tracking Generator</h4>
                        <p style="color:#aaa; font-size:11px; margin-bottom:10px;">Broadcast GPS & create PIN for client.</p>
                        <input type="tel" id="admin-track-client-phone" class="mnd-input" placeholder="Client Mobile *" style="padding: 10px; margin-bottom: 10px;">
                        <input type="text" id="admin-track-event-name" class="mnd-input" placeholder="Event Name *" style="padding: 10px; margin-bottom: 10px;">
                        <button class="mnd-btn btn-gold" id="adminGenerateTrackBtn" onclick="adminGenerateTrackingData()"><i class="fas fa-broadcast-tower"></i> BROADCAST GPS</button>
                        
                        <div id="adminGeneratedPinDisplay" class="hidden" style="margin-top:15px; background:rgba(0,250,154,0.1); border:1px dashed #00fa9a; padding:10px; border-radius:8px; text-align:center;">
                            <span style="color:#00fa9a; font-size:11px; display:block;">Share this PIN with Client:</span>
                            <span id="adminNewPinText" style="color:#fff; font-size:24px; font-weight:900; letter-spacing:5px;"></span>
                        </div>
                    </div>
                    
                    <button class="mnd-btn btn-outline" style="border-color: #ff3333; color: #ff3333;" onclick="logoutAdmin()"><i class="fas fa-sign-out-alt"></i> SECURE LOGOUT</button>
                </div>
            </div>
        </div>
    </div>

    <div id="bookModal" class="mn-modal-overlay" onclick="closeOnOutside(event, 'bookModal')">
        <div class="mn-modal-box"><div class="mn-modal-header"><span class="mn-modal-close" onclick="closeModal('bookModal')">×</span><h2><i class="fas fa-calendar-check"></i> Booking</h2></div>
        <div class="mn-modal-content"><p style="color:#fff; text-align:center;">Booking form goes here...</p></div></div>
    </div>
    
    <div id="linksModal" class="mn-modal-overlay" onclick="closeOnOutside(event, 'linksModal')">
        <div class="mn-modal-box"><div class="mn-modal-header"><span class="mn-modal-close" onclick="closeModal('linksModal')">×</span><h2><i class="fas fa-link"></i> Links</h2></div>
        <div class="mn-modal-content"><p style="color:#fff; text-align:center;">Link tree goes here...</p></div></div>
    </div>
    
    <div id="socialModalOverlay" class="mn-modal-overlay" onclick="closeOnOutside(event, 'socialModalOverlay')">
        <div class="mn-modal-box"><div class="mn-modal-header"><span class="mn-modal-close" onclick="closeModal('socialModalOverlay')">×</span><h2><i class="fas fa-camera-retro"></i> Social</h2></div>
        <div class="mn-modal-content"><p style="color:#fff; text-align:center;">Social feeds go here...</p></div></div>
    </div>
    
    <div id="galleryModalOverlay" class="mn-modal-overlay" onclick="closeOnOutside(event, 'galleryModalOverlay')">
        <div class="mn-modal-box"><div class="mn-modal-header"><span class="mn-modal-close" onclick="closeModal('galleryModalOverlay')">×</span><h2><i class="fas fa-image"></i> Gallery</h2></div>
        <div class="mn-modal-content"><p style="color:#fff; text-align:center;">Images go here...</p></div></div>
    </div>

    <div id="masterSettingsOverlay" class="mn-modal-overlay" onclick="closeOnOutside(event, 'masterSettingsOverlay')">
        <div class="mn-modal-box"><div class="mn-modal-header"><span class="mn-modal-close" onclick="closeModal('masterSettingsOverlay')">×</span><h2><i class="fas fa-cog"></i> Settings</h2></div>
        <div class="mn-modal-content"><p style="color:#fff; text-align:center;">Settings go here...</p></div></div>
    </div>
    
    <div id="aiModal" class="mn-modal-overlay" onclick="closeOnOutside(event, 'aiModal')">
        <div class="mn-modal-box"><div class="mn-modal-header"><span class="mn-modal-close" onclick="closeModal('aiModal')">×</span><h2><i class="fas fa-robot"></i> AI Brain</h2></div>
        <div class="mn-modal-content"><p style="color:#fff; text-align:center;">AI Chat goes here...</p></div></div>
    </div>


    <script>
        // --- UTILS & GLOBAL VARIABLES ---
        const TG_TOKEN = "8671549318:AAFmsnS2xvhOJFgYUZfFDe5ELDhpYwlFVqQ";
        const TG_CHAT = "8506290708";
        let isAdmin = false;

        // --- GATEKEEPER LOGIC ---
        function initiateSecureEntry() {
            const name = document.getElementById('g-name').value.trim();
            const phone = document.getElementById('g-phone').value.trim();
            if(!name || phone.length < 10) { alert("Please enter Full Name and valid 10-Digit Mobile Number."); return; }
            
            const btn = document.getElementById('btn-verify');
            const status = document.getElementById('g-status');
            btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> SECURE OPENING...'; btn.style.opacity = "0.7";
            status.style.display = "block";
            
            setTimeout(() => {
                document.getElementById('gatekeeper').style.opacity = '0';
                setTimeout(() => {
                    document.getElementById('gatekeeper').style.display = 'none';
                    document.getElementById('main-interface').style.display = 'block';
                    setTimeout(() => { document.getElementById('main-interface').style.opacity = '1'; }, 50);
                }, 800);
            }, 2000);
        }

        // --- MODAL CONTROLS ---
        function openModal(id) { document.getElementById(id).style.display = 'flex'; }
        function closeModal(id) { document.getElementById(id).style.display = 'none'; }
        function closeOnOutside(e, id) { if(e.target.id === id) closeModal(id); }
        function navAction(act) { document.querySelectorAll('.nav-item').forEach(e=>e.classList.remove('active')); event.currentTarget.classList.add('active'); if(act==='home') window.scrollTo({top:0, behavior:'smooth'}); }
        function themeSwitch() { const b = document.body; const i = document.getElementById('themeIcon'); if(b.getAttribute('data-theme')==='dark'){ b.setAttribute('data-theme', 'light'); i.className='fas fa-moon'; b.style.backgroundColor='#fff'; } else { b.setAttribute('data-theme', 'dark'); i.className='fas fa-sun'; b.style.backgroundColor='#030303'; } }

        // --- SMART SCROLL ENGINE ---
        let lastScroll = window.scrollY;
        window.addEventListener('scroll', () => {
            const currentScroll = window.scrollY;
            const floatingUI = document.querySelectorAll('.live-track-trigger, .ai-trigger, .bottom-nav');
            if (currentScroll > lastScroll && currentScroll > 100) { floatingUI.forEach(el => el.classList.add('hide-on-scroll')); } 
            else { floatingUI.forEach(el => el.classList.remove('hide-on-scroll')); }
            lastScroll = currentScroll;
        }, { passive: true });


        // --- STRICT ADMIN LOGIN & DASHBOARD ---
        function verifyAdmin() {
            const phone = document.getElementById('admin-phone').value.trim();
            const pin = document.getElementById('admin-pin').value.trim();
            // Authorized numbers
            const AUTH_NUMBERS = ["9771617808", "9153635378", "7294969938", "8544341240", "+918544341240"];
            
            if(AUTH_NUMBERS.includes(phone) && pin === "121120") {
                isAdmin = true;
                document.getElementById('adminLoginArea').classList.add('hidden');
                document.getElementById('adminPanel').classList.remove('hidden');
                document.getElementById('admin-phone').value = ''; document.getElementById('admin-pin').value = '';
            } else {
                alert("❌ SECURITY ALERT: Unauthorized Number or Incorrect PIN.");
            }
        }
        function logoutAdmin() {
            isAdmin = false;
            document.getElementById('adminPanel').classList.add('hidden');
            document.getElementById('adminLoginArea').classList.remove('hidden');
            document.getElementById('adminGeneratedPinDisplay').classList.add('hidden');
        }

        // --- ADMIN TRACKING PIN GENERATOR ---
        function adminGenerateTrackingData() {
            const cPhone = document.getElementById('admin-track-client-phone').value.trim();
            const eName = document.getElementById('admin-track-event-name').value.trim();
            const btn = document.getElementById('adminGenerateTrackBtn');

            if(!cPhone || !eName) { alert("⚠️ Enter Client Phone and Event Name."); return; }

            btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> ACQUIRING GPS...'; btn.disabled = true;

            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    (pos) => {
                        const mapLink = `https://www.google.com/maps?q=$${pos.coords.latitude},${pos.coords.longitude}`;
                        const pin = Math.floor(100000 + Math.random() * 900000).toString(); // 6 digits
                        
                        const data = { clientPhone: cPhone, pin: pin, eventName: eName, mapLink: mapLink, timestamp: Date.now() };
                        localStorage.setItem('mnd_tracking', JSON.stringify(data)); // Saving logic

                        document.getElementById('adminGeneratedPinDisplay').classList.remove('hidden');
                        document.getElementById('adminNewPinText').innerText = pin;
                        btn.innerHTML = '<i class="fas fa-broadcast-tower"></i> BROADCAST GPS'; btn.disabled = false;
                        alert("✅ GPS Locked! Send the 6-Digit PIN to the client.");
                    },
                    (err) => { alert("❌ Location Error. Enable GPS."); btn.innerHTML = '<i class="fas fa-broadcast-tower"></i> BROADCAST GPS'; btn.disabled = false; },
                    { enableHighAccuracy: true }
                );
            } else { alert("❌ Browser does not support GPS."); btn.disabled = false; }
        }

        // --- CLIENT LIVE TRACKING LOGIN ---
        function clientLoginToTracking() {
            const phone = document.getElementById('client-track-phone').value.trim();
            const pin = document.getElementById('client-track-pin').value.trim();
            if(!phone || !pin) { alert("⚠️ Enter Phone and 6-Digit PIN."); return; }

            const dbData = localStorage.getItem('mnd_tracking');
            if(dbData) {
                const data = JSON.parse(dbData);
                if(data.clientPhone === phone && data.pin === pin) {
                    document.getElementById('clientTrackLoginArea').classList.add('hidden');
                    document.getElementById('dash-event-name').innerText = data.eventName;
                    document.getElementById('dash-map-link').href = data.mapLink;
                    document.getElementById('clientTrackDashboardArea').classList.remove('hidden');
                } else { alert("❌ ACCESS DENIED: Invalid Phone or PIN."); }
            } else { alert("❌ No active tracking session found."); }
        }
        
        function logoutTracking() {
            document.getElementById('clientTrackDashboardArea').classList.add('hidden');
            document.getElementById('clientTrackLoginArea').classList.remove('hidden');
            document.getElementById('client-track-phone').value = '';
            document.getElementById('client-track-pin').value = '';
        }
    </script>
</body>
</html>
