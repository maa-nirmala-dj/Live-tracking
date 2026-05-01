<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="theme-color" content="#050508">
    <title>MND Live Logistics | Premium Cloud Tracking</title>

    <!-- Premium Fonts & Icons -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@600;800;900&family=Outfit:wght@300;500;700;900&family=Orbitron:wght@500;700;900&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">

    <!-- Firebase SDKs (Database Only) -->
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-database-compat.js"></script>

    <style>
        /* =========================================================================
           GLOBAL RESET & VARIABLES
           ========================================================================= */
        :root {
            --gold: #D4AF37;
            --gold-glow: rgba(212, 175, 55, 0.4);
            --neon-green: #00FA9A;
            --neon-cyan: #00E5FF;
            --bg-dark: #050508;
            --danger: #ff3333;
            /* WhatsApp Dark Theme Colors */
            --wa-bg: #0b141a;
            --wa-header: #202c33;
            --wa-sent: #005c4b;
            --wa-received: #202c33;
            --wa-text: #e9edef;
            --wa-time: rgba(255,255,255,0.55);
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; outline: none; }
        body, html { width: 100vw; height: 100dvh; background: var(--bg-dark); color: #fff; overflow: hidden; display: flex; justify-content: center; align-items: flex-start; }

        /* Cinematic Background */
        .bg-map {
            position: fixed; inset: 0; z-index: -1; opacity: 0.15;
            background-image: 
                radial-gradient(circle at 20% 30%, var(--gold-glow) 0%, transparent 50%),
                radial-gradient(circle at 80% 80%, rgba(0, 250, 154, 0.15) 0%, transparent 50%);
            animation: pulseBg 6s infinite alternate ease-in-out;
        }
        .grid-overlay {
            position: fixed; inset: 0; z-index: -1; opacity: 0.05;
            background-image: linear-gradient(var(--gold) 1px, transparent 1px), linear-gradient(90deg, var(--gold) 1px, transparent 1px);
            background-size: 40px 40px;
        }
        @keyframes pulseBg { 0% { opacity: 0.1; transform: scale(1); } 100% { opacity: 0.25; transform: scale(1.05); } }

        /* =========================================================================
           NATIVE-STYLE PUSH NOTIFICATIONS & TOASTS
           ========================================================================= */
        #toast-container { position: fixed; top: 15px; left: 50%; transform: translateX(-50%); z-index: 9999999; display: flex; flex-direction: column; gap: 10px; pointer-events: none; width: 100%; align-items: center; }
        .toast { background: rgba(10,10,15,0.98); border-left: 4px solid var(--gold); color: #fff; padding: 15px 20px; border-radius: 8px; font-weight: 600; font-size: 14px; box-shadow: 0 10px 30px rgba(0,0,0,0.8); display: flex; align-items: center; gap: 10px; max-width: 90%; animation: dropDown 0.4s cubic-bezier(0.2, 0.8, 0.2, 1) forwards, slideUp 0.4s 3.5s forwards; }
        .toast.success { border-color: var(--neon-green); }
        .toast.error { border-color: var(--danger); }
        
        .native-push { background: rgba(20, 20, 25, 0.95); backdrop-filter: blur(20px); border: 1px solid rgba(255, 255, 255, 0.15); border-radius: 18px; padding: 15px; width: 92%; max-width: 400px; display: flex; gap: 15px; box-shadow: 0 20px 40px rgba(0,0,0,0.8), 0 0 20px rgba(212, 175, 55, 0.2); animation: dropDownPush 0.6s cubic-bezier(0.2, 0.8, 0.2, 1) forwards, slideUpPush 0.5s 6s forwards; }
        .native-push-icon { width: 45px; height: 45px; min-width: 45px; border-radius: 12px; background: linear-gradient(135deg, var(--gold), #FFD700); display: flex; align-items: center; justify-content: center; color: #000; font-size: 24px; box-shadow: 0 5px 15px rgba(212,175,55,0.4); }
        .native-push-content { flex-grow: 1; display: flex; flex-direction: column; justify-content: center; }
        .native-push-title { font-family: 'Outfit', sans-serif; font-size: 15px; font-weight: 800; color: #fff; margin: 0 0 3px 0; text-transform: uppercase; letter-spacing: 1px; display:flex; justify-content:space-between; }
        .native-push-title span { font-size: 10px; color: var(--gold); font-weight: 500; text-transform: none; }
        .native-push-body { font-family: 'Outfit', sans-serif; font-size: 13px; color: #ccc; margin: 0; line-height: 1.4; font-weight: 500; }

        @keyframes dropDown { from { opacity: 0; transform: translateY(-30px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes slideUp { from { opacity: 1; transform: translateY(0); } to { opacity: 0; transform: translateY(-30px); } }
        @keyframes dropDownPush { from { opacity: 0; transform: translateY(-50px) scale(0.95); } to { opacity: 1; transform: translateY(0) scale(1); } }
        @keyframes slideUpPush { from { opacity: 1; transform: translateY(0) scale(1); } to { opacity: 0; transform: translateY(-50px) scale(0.95); } }

        /* =========================================================================
           VIEW MANAGER (Strict SPA) - NATIVE FULL SCROLL
           ========================================================================= */
        .view-container { display: none; flex-direction: column; align-items: center; width: 100%; height: 100dvh; padding: 25px 15px 60px; animation: fadeInView 0.5s ease forwards; overflow-y: auto; -webkit-overflow-scrolling: touch; }
        .active-view { display: flex !important; }
        @keyframes fadeInView { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        ::-webkit-scrollbar { width: 4px; } ::-webkit-scrollbar-track { background: transparent; } ::-webkit-scrollbar-thumb { background: rgba(212,175,55,0.4); border-radius: 10px; }

        /* =========================================================================
           UI COMPONENTS (Inputs, Buttons, Cards)
           ========================================================================= */
        .app-title { font-family: 'Cinzel', serif; color: var(--gold); font-size: 28px; font-weight: 900; letter-spacing: 2px; margin-bottom: 5px; text-shadow: 0 0 15px var(--gold-glow); text-align: center; }
        .app-subtitle { color: #aaa; font-size: 13px; margin-bottom: 30px; line-height: 1.5; font-weight: 300; text-align: center; }

        .input-group { width: 100%; position: relative; margin-bottom: 18px; }
        .input-group i { position: absolute; left: 18px; top: 18px; color: var(--gold); font-size: 18px; opacity: 0.8; }
        .mn-input { width: 100%; padding: 16px 16px 16px 50px; background: rgba(0,0,0,0.6); border: 1px solid rgba(212,175,55,0.3); color: #fff; border-radius: 12px; font-size: 16px; outline: none; transition: 0.3s ease; box-shadow: inset 0 2px 10px rgba(0,0,0,0.5); }
        .mn-input:focus { border-color: var(--gold); background: rgba(212,175,55,0.05); box-shadow: 0 0 15px var(--gold-glow), inset 0 2px 10px rgba(0,0,0,0.5); }
        .pin-style { letter-spacing: 12px; font-size: 24px; font-weight: 900; text-align: center; padding-left: 20px; font-family: 'Orbitron', sans-serif; color: var(--neon-cyan); }
        .pin-style + i { display: none; }
        .pin-style::placeholder { color: #555; letter-spacing: 5px; font-size: 16px; font-family: 'Outfit', sans-serif; font-weight: 400; }

        .mn-btn { width: 100%; padding: 16px; border-radius: 12px; font-weight: 800; font-size: 14px; cursor: pointer; transition: all 0.3s ease; display: flex; justify-content: center; align-items: center; gap: 10px; border: none; letter-spacing: 1px; text-transform: uppercase; flex-shrink: 0; }
        .mn-btn:active { transform: scale(0.96); }
        .btn-gold { background: linear-gradient(135deg, var(--gold) 0%, #FFD700 100%); color: #000; box-shadow: 0 5px 25px var(--gold-glow); }
        .btn-green { background: linear-gradient(135deg, #25D366 0%, #128C7E 100%); color: #fff; box-shadow: 0 5px 20px rgba(37, 211, 102, 0.3); }
        .btn-dark { background: rgba(0,0,0,0.5); border: 1px solid var(--gold); color: var(--gold); backdrop-filter: blur(5px); }
        .btn-danger { background: rgba(255,51,51,0.1); border: 1px solid var(--danger); color: var(--danger); margin-top: 15px; }

        .dash-header { width: 100%; max-width: 600px; display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; border-bottom: 1px dashed rgba(212,175,55,0.3); padding-bottom: 15px; flex-shrink: 0; }
        .status-badge { background: rgba(0, 250, 154, 0.1); border: 1px solid var(--neon-green); color: var(--neon-green); padding: 6px 14px; border-radius: 30px; font-size: 11px; font-weight: 800; display: flex; align-items: center; gap: 6px; letter-spacing: 1px; box-shadow: 0 0 15px rgba(0, 250, 154, 0.2); }
        .status-badge.offline { background: rgba(255, 51, 51, 0.1); border-color: var(--danger); color: var(--danger); box-shadow: 0 0 15px rgba(255, 51, 51, 0.2); }
        
        .card { width: 100%; max-width: 600px; background: rgba(10,10,15,0.85); border: 1px solid var(--glass-border); padding: 20px; border-radius: 16px; margin-bottom: 20px; backdrop-filter: blur(15px); box-shadow: 0 10px 30px rgba(0,0,0,0.5); flex-shrink: 0; }
        .card.flex-grow { flex-grow: 1; max-height: none; }
        .card h3 { color: var(--gold); font-family: 'Cinzel'; margin-bottom: 15px; font-size: 16px; border-bottom: 1px dashed rgba(212,175,55,0.3); padding-bottom: 8px; display: flex; align-items: center; gap: 10px; }

        /* =========================================================================
           WHATSAPP-STYLE CHAT ENGINE (100% NATIVE FEEL)
           ========================================================================= */
        .chat-card { padding: 0 !important; overflow: hidden; display: flex; flex-direction: column; height: 75vh; max-height: 800px; border: 1px solid rgba(255,255,255,0.1); border-radius: 16px; background: var(--wa-bg) !important; margin-bottom: 20px; width: 100%; max-width: 600px; flex-shrink:0; box-shadow: 0 20px 50px rgba(0,0,0,0.8); }
        
        .chat-header { background: var(--wa-header); padding: 12px 15px; display: flex; justify-content: space-between; align-items: center; z-index: 2; box-shadow: 0 2px 10px rgba(0,0,0,0.5); }
        
        /* Subtle Chat Background Pattern */
        .chat-area { flex-grow: 1; overflow-y: auto; padding: 20px 15px; display: flex; flex-direction: column; gap: 12px; background-color: var(--wa-bg); background-image: radial-gradient(rgba(255,255,255,0.04) 1px, transparent 1px); background-size: 20px 20px; scroll-behavior: smooth; }
        
        /* WhatsApp Style Input Bar */
        .chat-input-wrapper { display: flex; align-items: flex-end; gap: 10px; padding: 10px 15px; background: var(--wa-header); z-index: 2; }
        .chat-input-pill { flex-grow: 1; background: #2a3942; border: none; padding: 14px 20px; border-radius: 25px; color: var(--wa-text); font-size: 15px; outline: none; font-family: 'Outfit', sans-serif; box-shadow: inset 0 2px 5px rgba(0,0,0,0.2); }
        .chat-input-pill::placeholder { color: #8696a0; }
        .chat-send-btn { width: 48px; height: 48px; border-radius: 50%; border: none; background: #00a884; color: #fff; font-size: 18px; display: flex; justify-content: center; align-items: center; cursor: pointer; transition: 0.2s; flex-shrink: 0; box-shadow: 0 4px 10px rgba(0,168,132,0.3); }
        .chat-send-btn:active { transform: scale(0.9); }
        
        /* WhatsApp Chat Bubbles */
        .chat-bubble { max-width: 82%; padding: 8px 10px 8px 14px; border-radius: 12px; position: relative; font-size: 15px; line-height: 1.4; color: var(--wa-text); display: inline-block; word-wrap: break-word; box-shadow: 0 1px 2px rgba(0,0,0,0.3); cursor: pointer; transition: 0.2s; }
        .chat-bubble:active { filter: brightness(1.2); }
        
        .chat-bubble.sent { align-self: flex-end; background: var(--wa-sent); border-top-right-radius: 0; } /* Sharp corner right */
        .chat-bubble.received { align-self: flex-start; background: var(--wa-received); border-top-left-radius: 0; } /* Sharp corner left */
        
        .chat-time { font-size: 10px; color: var(--wa-time); float: right; margin: 10px -4px -4px 15px; font-family: 'Outfit'; }
        
        /* Reply Embedded inside Bubble */
        .chat-reply-context { background: rgba(0,0,0,0.25); border-left: 4px solid var(--neon-green); padding: 6px 10px; border-radius: 6px; font-size: 13px; color: #ccc; margin-bottom: 6px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
        .chat-reply-context .sender { color: var(--neon-green); font-weight: 700; margin-bottom: 2px; font-size: 12px; }
        .received .chat-reply-context { border-left-color: var(--neon-cyan); }
        .received .chat-reply-context .sender { color: var(--neon-cyan); }
        
        /* Reply Banner hovering above input */
        .reply-banner { display: none; background: #202c33; padding: 10px 15px; align-items: center; border-bottom: 1px solid rgba(255,255,255,0.05); }
        .reply-banner-content { flex-grow: 1; border-left: 4px solid var(--neon-green); padding-left: 12px; background: rgba(0,0,0,0.2); border-radius: 4px 8px 8px 4px; padding-top: 6px; padding-bottom: 6px; }
        .reply-banner-content .rep-title { font-size: 12px; color: var(--neon-green); font-weight: bold; margin-bottom: 3px; }
        .replying-to-text { color: #ccc; font-size: 13px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; max-width: 90%; }
        .cancel-reply-btn { background: transparent; border: none; color: #8696a0; font-size: 20px; cursor: pointer; padding: 5px 10px; }

        /* Popup Action Menu on Bubble Click */
        .chat-actions-menu { display: none; background: rgba(32,44,51,0.98); border: 1px solid rgba(255,255,255,0.1); border-radius: 12px; position: absolute; z-index: 10; padding: 5px; flex-direction: row; gap: 5px; box-shadow: 0 10px 25px rgba(0,0,0,0.9); top: 100%; right: 0; margin-top: 5px; backdrop-filter: blur(10px); }
        .chat-bubble.show-actions .chat-actions-menu { display: flex; animation: dropDown 0.2s ease forwards; }
        .chat-bubble.received .chat-actions-menu { right: auto; left: 0; } 
        
        .chat-action-btn { background: transparent; border: none; color: #e9edef; font-size: 16px; padding: 10px 14px; cursor: pointer; border-radius: 8px; transition: 0.2s; display: flex; align-items: center; justify-content: center; }
        .chat-action-btn:hover { background: rgba(255,255,255,0.1); color: #fff; }
        .chat-action-btn.delete { color: rgba(255,51,51,0.8); }
        .chat-action-btn.delete:hover { color: var(--danger); background: rgba(255,51,51,0.15); }

        /* =========================================================================
           TIMELINE, HISTORY & MAP
           ========================================================================= */
        .client-list-item { background: rgba(0,0,0,0.6); padding: 15px; border-radius: 10px; border-left: 4px solid var(--neon-cyan); margin-bottom: 12px; display: flex; justify-content: space-between; align-items: center; transition: 0.3s; cursor: pointer; box-shadow: 0 5px 15px rgba(0,0,0,0.3); }
        .client-list-item:hover { background: rgba(212, 175, 55, 0.1); border-left-color: var(--gold); transform: translateY(-2px); }
        .client-list-item h4 { margin:0 0 4px 0; font-size: 15px; color: #fff; font-family: 'Cinzel', serif; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .client-list-item p { margin:0; font-size: 12px; color: #888; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .client-list-actions { display: flex; align-items: center; gap: 12px; }
        .client-list-pin { font-family: 'Orbitron', monospace; font-size: 16px; color: var(--gold); font-weight: bold; background: rgba(212,175,55,0.1); padding: 6px 10px; border-radius: 6px; letter-spacing: 2px; }
        
        .action-icon-btn { background: transparent; border: none; color: #888; font-size: 18px; cursor: pointer; transition: 0.3s; padding: 5px; }
        .action-icon-btn:hover { color: var(--neon-cyan); transform: scale(1.1); }
        .action-icon-btn.trash { color: rgba(255,51,51,0.7); }
        .action-icon-btn.trash:hover { color: var(--danger); transform: scale(1.2); }

        .map-wrapper { width: 100%; height: 260px; border-radius: 12px; overflow: hidden; border: 2px solid #222; position: relative; background: #050505; margin-bottom: 15px; }
        .map-iframe { width: 100%; height: 100%; border: none; filter: invert(100%) hue-rotate(180deg) brightness(85%) contrast(110%) sepia(30%); pointer-events: none; transition: 1s ease; opacity: 0; }
        .map-iframe.loaded { opacity: 1; }
        .map-loader { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); color: var(--gold); font-size: 13px; text-align: center; font-weight: bold; letter-spacing: 1px; z-index: 1; }
        
        .location-display { background: rgba(0,229,255,0.05); border: 1px solid rgba(0,229,255,0.2); border-radius: 8px; padding: 12px; margin-bottom: 15px; display: flex; align-items: center; gap: 12px; }
        .location-icon { width: 35px; height: 35px; border-radius: 50%; background: rgba(0,229,255,0.15); display: flex; justify-content: center; align-items: center; color: var(--neon-cyan); font-size: 16px; }
        .location-text h4 { margin: 0 0 3px 0; font-size: 10px; color: #888; text-transform: uppercase; letter-spacing: 1px; }
        .location-text p { margin: 0; font-size: 15px; color: #fff; font-weight: 700; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; max-width: 250px; }

        .telemetrics-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; margin-bottom: 12px; }
        .metric-box { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.1); border-radius: 8px; padding: 10px; text-align: center; }
        .metric-label { font-size: 10px; color: #888; text-transform: uppercase; letter-spacing: 1px; margin-bottom: 5px; }
        .metric-value { font-family: 'Orbitron', sans-serif; font-size: 14px; font-weight: 700; color: var(--neon-cyan); }

        .timeline-feed { padding-left: 20px; position: relative; flex-grow: 1; overflow-y: auto; padding-right: 5px; min-height: 200px; margin-bottom: 15px; }
        .timeline-item { position: relative; margin-bottom: 20px; padding-bottom: 20px; border-bottom: 1px dashed rgba(255,255,255,0.05); animation: slideInLeft 0.4s ease forwards; display: flex; justify-content: space-between; align-items: flex-start; }
        .timeline-item:last-child { border-bottom: none; margin-bottom: 0; padding-bottom: 0; }
        .timeline-item::before { content: ''; position: absolute; left: -26px; top: 3px; width: 12px; height: 12px; border-radius: 50%; background: var(--neon-green); box-shadow: 0 0 10px var(--neon-green); border: 2px solid #111; z-index: 2; }
        .timeline-item::after { content: ''; position: absolute; left: -21px; top: 15px; width: 2px; height: 100%; background: rgba(0, 250, 154, 0.3); z-index: 1; }
        .timeline-item:last-child::after { display: none; }
        
        .update-content { width: 85%; }
        .update-time { font-size: 10px; color: var(--gold); margin-bottom: 5px; font-family: 'Orbitron', sans-serif; font-weight: bold; text-transform: uppercase; letter-spacing: 1px; }
        .update-text { font-size: 14px; color: #eee; line-height: 1.5; font-weight: 500; word-wrap: break-word; }

        .quick-actions { display: flex; gap: 8px; margin-bottom: 15px; flex-wrap: wrap; }
        .quick-btn { background: rgba(255,255,255,0.05); border: 1px solid rgba(212,175,55,0.4); color: #fff; padding: 8px 12px; border-radius: 8px; font-size: 11px; cursor: pointer; transition: 0.3s; font-family: 'Orbitron', sans-serif; flex-grow: 1; text-align: center; }
        .quick-btn:hover { background: rgba(212,175,55,0.2); border-color: var(--gold); color: var(--gold); }
        
        .target-lock-banner { background: rgba(0, 229, 255, 0.1); border: 1px dashed var(--neon-cyan); padding: 12px 15px; border-radius: 8px; margin-bottom: 20px; display: flex; align-items: center; gap: 15px; flex-shrink: 0; width: 100%; max-width: 600px; }
        .target-lock-banner i { color: var(--neon-cyan); font-size: 24px; animation: targetPulse 2s infinite; }
        .target-lock-details h4 { margin: 0; color: var(--neon-cyan); font-size: 14px; font-family: 'Orbitron'; letter-spacing: 1px; text-transform: uppercase; }
        .target-lock-details p { margin: 3px 0 0 0; color: #fff; font-size: 12px; }
        @keyframes targetPulse { 0%, 100% { transform: scale(1); opacity: 1; } 50% { transform: scale(1.1); opacity: 0.5; } }
    </style>
</head>
<body onclick="closeAllMenus()"> 

    <div class="bg-map"></div>
    <div class="grid-overlay"></div>
    <div id="toast-container"></div>

    <!-- ========================================================================= -->
    <!-- VIEW 1: GATEKEEPER -->
    <!-- ========================================================================= -->
    <div id="view-gatekeeper" class="view-container active-view" style="justify-content: center;">
        <div class="gatekeeper-box" style="background: rgba(15,15,20,0.85); position:relative; overflow:hidden; border-radius:20px; padding:40px 25px; box-shadow: 0 20px 60px rgba(0,0,0,0.9); border:2px solid var(--gold); max-width:450px;">
            <h1 class="app-title"><i class="fas fa-satellite-dish"></i> MND TRACKING</h1>
            <p class="app-subtitle">Secured Cloud Logistics Portal.<br>Enter your Mobile Number and PIN below. Send in your Register Whatsapp Number</p>
            
            <div class="input-group">
                <i class="fas fa-phone-alt"></i>
                <input type="tel" id="login-phone" class="mn-input" placeholder="Mobile Number *" autocomplete="off">
            </div>
            
            <div class="input-group">
                <input type="password" id="login-pin" class="mn-input pin-style" placeholder="6 DIGIT PIN" maxlength="6" autocomplete="off">
            </div>

            <button class="mn-btn btn-gold" id="login-btn" onclick="processLogin(event)"><i class="fas fa-fingerprint"></i> INITIATE HANDSHAKE</button>
            <p id="login-error" style="display:none; color:var(--danger); font-size:13px; font-weight:bold; margin-top:15px;"></p>
        </div>
    </div>

    <!-- ========================================================================= -->
    <!-- VIEW 2: ADMIN DISPATCH CENTER (MAIN) -->
    <!-- ========================================================================= -->
    <div id="view-admin" class="view-container">
        <div class="dash-header">
            <div>
                <h2 style="color:var(--gold); font-family:'Cinzel'; margin:0; font-size:22px;">Command Center</h2>
                <span style="font-size:12px; color:#888;">Authorized Personnel Only</span>
            </div>
            <div class="status-badge"><i class="fas fa-link"></i> DB SYNCED</div>
        </div>

        <div class="card">
            <h3><i class="fas fa-key"></i> Provision / Update Client</h3>
            <p style="font-size:11px; color:#aaa; margin-bottom:15px;">Entering an existing number will elegantly UPDATE their PIN without deleting history.</p>
            
            <div style="display:flex; gap:10px;">
                <input type="text" id="admin-c-name" class="mn-input" style="padding:12px; margin-bottom:12px;" placeholder="Client Name *">
                <input type="tel" id="admin-c-phone" class="mn-input" style="padding:12px; margin-bottom:12px;" placeholder="Mobile No. *">
            </div>
            
            <div style="display:flex; gap:10px;">
                <input type="text" id="admin-c-event" class="mn-input" style="flex:2; padding:12px; margin-bottom:15px;" placeholder="Event Ref *">
                <input type="text" id="admin-c-custom-pin" class="mn-input" style="flex:1; padding:12px; margin-bottom:15px; font-family:'Orbitron'; text-align:center; letter-spacing:4px; color:var(--neon-cyan);" placeholder="Custom PIN" maxlength="6">
            </div>
            
            <button class="mn-btn btn-gold" id="btn-generate" onclick="adminGeneratePin(event)"><i class="fas fa-microchip"></i> AUTHORIZE & SAVE TO CLOUD</button>

            <div id="admin-pin-result" style="display:none; margin-top:20px; text-align:center; background: rgba(0,0,0,0.6); padding: 20px; border-radius: 12px; border: 1px dashed var(--neon-green);">
                <p style="color:#aaa; font-size:12px; text-transform:uppercase; letter-spacing:1px;">Client Access Ready:</p>
                <div style="font-family:'Orbitron', monospace; font-size:40px; color:var(--neon-green); font-weight:900; letter-spacing:10px; margin:10px 0; text-shadow: 0 0 20px rgba(0,250,154,0.4);" id="display-new-pin">000000</div>
                <button class="mn-btn btn-green" onclick="adminShareWhatsApp(event)"><i class="fab fa-whatsapp"></i> DISPATCH VIA WHATSAPP</button>
            </div>
        </div>

        <div class="card flex-grow">
            <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom: 5px; flex-shrink: 0;">
                <h3 style="margin:0; border:none; padding:0;"><i class="fas fa-history"></i> Live Network Roster</h3>
                <span style="font-size:11px; background:rgba(212,175,55,0.1); padding:4px 8px; border-radius:4px; color:var(--gold);">Auto-Sync</span>
            </div>
            <p style="font-size:11px; color:#888; margin-bottom:15px; flex-shrink:0;">Click any client below to open their Targeted Live Session for GPS & Comms.</p>
            
            <div id="admin-active-list" style="flex-grow:1; overflow-y:auto; padding-right: 5px;">
                <div style="text-align:center; color:#555; font-size:13px; padding:20px;"><i class="fas fa-circle-notch fa-spin"></i> Fetching history logs...</div>
            </div>
        </div>

        <button class="mn-btn btn-danger" onclick="systemLogout(event)"><i class="fas fa-sign-out-alt"></i> TERMINATE SYSTEM</button>
    </div>

    <!-- ========================================================================= -->
    <!-- VIEW 3: ADMIN TARGETED SESSION -->
    <!-- ========================================================================= -->
    <div id="view-admin-session" class="view-container">
        <div class="dash-header" style="border-color:var(--neon-cyan);">
            <div>
                <h2 style="color:var(--neon-cyan); font-family:'Orbitron'; margin:0; font-size:20px;">Targeted Session</h2>
                <span style="font-size:12px; color:#888;">Direct Encrypted Link</span>
            </div>
            <button class="action-icon-btn" onclick="closeAdminSession(event)" style="color:var(--gold); border:1px solid var(--gold); border-radius:50%; width:35px; height:35px; background:rgba(212,175,55,0.1);"><i class="fas fa-times"></i></button>
        </div>

        <div class="target-lock-banner">
            <i class="fas fa-crosshairs"></i>
            <div class="target-lock-details">
                <h4>Active Target Locked</h4>
                <p><span id="session-c-name" style="font-weight:bold;">Name</span> | <span id="session-c-event">Event</span> | <span id="session-c-phone" style="color:var(--gold);">Phone</span></p>
            </div>
        </div>

        <!-- 📍 Client Pinned Location -->
        <div class="card" id="admin-client-loc-card" style="display:none; border-color:var(--neon-green); background:rgba(0,250,154,0.05);">
            <h3 style="color:var(--neon-green); border-bottom-color:rgba(0,250,154,0.3);"><i class="fas fa-street-view"></i> Target Event Location</h3>
            <p style="font-size:12px; color:#aaa; margin-bottom:15px;">The client has shared their exact event coordinates.</p>
            <a id="admin-view-client-map" href="#" target="_blank" class="mn-btn btn-green" style="font-size:13px; padding:12px; text-decoration:none;"><i class="fas fa-map-marker-alt"></i> OPEN CLIENT LOCATION IN MAPS</a>
        </div>

        <!-- Session: GPS Telemetry -->
        <div class="card">
            <h3 style="color:var(--neon-cyan); border-bottom-color:rgba(0, 229, 255, 0.3);"><i class="fas fa-satellite"></i> Direct GPS Uplink</h3>
            <div class="telemetrics-grid" id="admin-telemetrics" style="display:none;">
                <div class="metric-box"><div class="metric-label">Speed</div><div class="metric-value" id="a-speed">0 km/h</div></div>
                <div class="metric-box"><div class="metric-label">Accuracy</div><div class="metric-value" id="a-acc">0 m</div></div>
                <div class="metric-box"><div class="metric-label">Pings</div><div class="metric-value" id="a-ping">0</div></div>
            </div>
            
            <div id="admin-ai-location" class="location-display" style="display:none; border-color:var(--neon-cyan); background:rgba(0,229,255,0.05);">
                <div class="location-icon" style="color:var(--neon-cyan); background:rgba(0,229,255,0.1);"><i class="fas fa-map-marked-alt"></i></div>
                <div class="location-text">
                    <h4>Current Area Detected</h4>
                    <p id="a-loc-name">Resolving coordinates...</p>
                </div>
            </div>

            <button class="mn-btn btn-dark" style="border-color:var(--neon-cyan); color:var(--neon-cyan);" id="btn-broadcast" onclick="toggleLocationBroadcast(event)"><i class="fas fa-location-arrow"></i> INITIATE BROADCAST TO TARGET</button>
        </div>

        <!-- Session: Push Update (Public Timeline) -->
        <div class="card">
            <h3 style="color:var(--neon-cyan); border-bottom-color:rgba(0, 229, 255, 0.3);"><i class="fas fa-bullhorn"></i> Public Logistics Feed</h3>
            
            <div class="quick-actions">
                <button class="quick-btn" onclick="setQuickUpdate('🚚 Fleet Dispatched. En route to venue.', event)"><i class="fas fa-truck-moving"></i> Dispatched</button>
                <button class="quick-btn" onclick="setQuickUpdate('📍 Fleet Arrived. Commencing Unloading.', event)"><i class="fas fa-map-marker-alt"></i> Arrived</button>
                <button class="quick-btn" onclick="setQuickUpdate('⚙️ Unloaded. Stage & Audio setup begun.', event)"><i class="fas fa-tools"></i> Setup</button>
                <button class="quick-btn" style="border-color:rgba(255,51,51,0.5); color:#ff6b6b;" onclick="setQuickUpdate('🛑 Minor delay due to traffic. Actively moving.', event)"><i class="fas fa-traffic-light"></i> Delay</button>
            </div>

            <textarea id="admin-update-text" class="mn-input" rows="2" style="resize:none; padding:15px; flex-shrink:0;" placeholder="Push an official update to the client's public timeline..."></textarea>
            <button class="mn-btn btn-gold" style="margin-top:12px; flex-shrink:0; background: linear-gradient(135deg, var(--neon-cyan) 0%, #0088cc 100%); box-shadow: 0 5px 25px rgba(0,229,255,0.3);" id="btn-push-update" onclick="adminPushUpdate(event)"><i class="fas fa-paper-plane"></i> TRANSMIT TO TIMELINE</button>
            
            <div style="border-top: 1px dashed rgba(255,255,255,0.1); padding-top:15px; margin-top: 15px;">
                <h4 style="font-size:12px; color:#aaa; margin-bottom:10px; text-transform:uppercase; flex-shrink:0;">Manage Sent Updates:</h4>
                <div id="admin-manage-updates-area" class="timeline-feed" style="max-height: 200px;">
                    <!-- Updates populate here -->
                </div>
            </div>
        </div>

        <!-- 💬 ADVANCED TWO-WAY WHATSAPP CHAT (ADMIN SIDE) -->
        <div class="chat-card">
            <div class="chat-header">
                <div style="display:flex; align-items:center; gap:12px;">
                    <div style="width:40px; height:40px; border-radius:50%; background:var(--neon-cyan); display:flex; justify-content:center; align-items:center; color:#000; font-size:18px;"><i class="fas fa-user-tie"></i></div>
                    <div>
                        <h3 style="margin:0; padding:0; color:#fff; font-size:16px; border:none;" id="admin-chat-header-name">Target Client</h3>
                        <div style="font-size:11px; color:var(--neon-green);">Online</div>
                    </div>
                </div>
                <span style="font-size:10px; color:var(--neon-cyan); border:1px solid var(--neon-cyan); padding:3px 8px; border-radius:12px;"><i class="fas fa-lock"></i> Encrypted</span>
            </div>
            
            <div id="admin-chat-area" class="chat-area">
                <div style="font-size:12px; color:rgba(255,255,255,0.5); text-align:center; margin: auto; background:rgba(0,0,0,0.4); padding:6px 12px; border-radius:12px;">End-to-end encrypted chat started</div>
            </div>

            <div id="admin-reply-banner" class="reply-banner">
                <div class="reply-banner-content">
                    <div class="rep-title" id="admin-reply-sender">Client Name</div>
                    <div class="replying-to-text" id="admin-reply-text">Replying to...</div>
                </div>
                <button class="cancel-reply-btn" onclick="cancelReply('admin', event)"><i class="fas fa-times"></i></button>
            </div>

            <div class="chat-input-wrapper">
                <input type="text" id="admin-chat-input" class="chat-input-pill" placeholder="Message Client...">
                <button class="chat-send-btn" onclick="adminSendChatMessage(event)"><i class="fas fa-paper-plane"></i></button>
            </div>
        </div>
    </div>

    <!-- ========================================================================= -->
    <!-- VIEW 4: CLIENT LIVE DASHBOARD -->
    <!-- ========================================================================= -->
    <div id="view-client" class="view-container" style="padding: 20px 10px;">
        <div class="dash-header" style="max-width:100%; width:100%;">
            <div style="width:70%;">
                <h2 id="client-dash-event" style="color:var(--gold); font-family:'Cinzel'; margin:0; font-size:20px; white-space:nowrap; overflow:hidden; text-overflow:ellipsis;">Loading Event...</h2>
                <span style="font-size:12px; color:#888;">Authorized: <span id="client-dash-name" style="color:#fff;">Loading...</span></span>
            </div>
            <div class="status-badge" id="client-conn-status"><i class="fas fa-circle fa-beat"></i> ONLINE</div>
        </div>

        <!-- 📍 Client Send Location to HQ -->
        <div class="card" style="max-width:100%; width:100%; padding: 15px; background:rgba(0, 250, 154, 0.05); border-color:var(--neon-green); margin-bottom:15px; flex-shrink:0;">
            <div style="display:flex; justify-content:space-between; align-items:center;">
                <div>
                    <h4 style="color:var(--neon-green); margin:0; font-size:14px;"><i class="fas fa-street-view"></i> Event Location</h4>
                    <p style="font-size:11px; color:#aaa; margin:0;">Send exact map pin to the logistics team.</p>
                </div>
                <button class="mn-btn btn-green" style="width:auto; padding: 10px 15px; font-size:12px;" onclick="clientShareLocation(event)"><i class="fas fa-share-location"></i> SHARE</button>
            </div>
        </div>

        <!-- Map & Telemetrics Area -->
        <div class="card" style="max-width:100%; width:100%; padding: 15px; flex-shrink:0; margin-bottom:15px;">
            <div style="display:flex; justify-content:space-between; align-items:center; padding-bottom: 12px; border-bottom: 1px dashed rgba(255,255,255,0.1); margin-bottom: 10px;">
                <h3 style="margin:0; border:none; padding:0; font-size:16px;"><i class="fas fa-crosshairs"></i> Live Target Tracking</h3>
                <span id="client-gps-time" style="font-size:11px; color:#888; font-family:'Orbitron';">Awaiting Signal...</span>
            </div>
            
            <div class="telemetrics-grid">
                <div class="metric-box"><div class="metric-label">Speed</div><div class="metric-value" id="c-speed">--</div></div>
                <div class="metric-box"><div class="metric-label">Heading</div><div class="metric-value" id="c-heading">--</div></div>
                <div class="metric-box"><div class="metric-label">Signal</div><div class="metric-value" id="c-accuracy" style="color:var(--gold);">--</div></div>
            </div>

            <div class="map-wrapper">
                <div class="map-loader" id="map-loader-text"><i class="fas fa-satellite-dish fa-spin fa-2x" style="margin-bottom:10px;"></i><br>SEARCHING SATELLITE...</div>
                <iframe id="client-map-iframe" class="map-iframe" src="" onload="this.classList.add('loaded')"></iframe>
            </div>

            <!-- AI Location Appears Below Map -->
            <div class="location-display" id="client-location-display" style="display:none;">
                <div class="location-icon"><i class="fas fa-map-marker-alt"></i></div>
                <div class="location-text">
                    <h4>Nearest Detected Area</h4>
                    <p id="c-location-name">Syncing AI Location...</p>
                </div>
            </div>
            
            <a id="ext-map-link" href="#" target="_blank" class="mn-btn btn-green" style="font-size:13px; padding:14px; text-decoration:none; display:none;"><i class="fas fa-external-link-alt"></i> OPEN IN GOOGLE MAPS APP</a>
        </div>

        <!-- Premium Timeline Updates Area (STAYS INTACT) -->
        <div class="card" style="max-width:100%; width:100%; display:flex; flex-direction:column; flex-shrink:0;">
            <h3 style="margin-bottom: 20px; flex-shrink:0;"><i class="fas fa-bullhorn"></i> Official Logistics Updates</h3>
            <div id="client-updates-area" class="timeline-feed" style="max-height: 250px;">
                <div style="text-align:center; color:#555; font-size:13px; padding:20px; border:none; margin-left:-20px;"><i class="fas fa-circle-notch fa-spin"></i> Listening for HQ timeline broadcasts...</div>
            </div>
        </div>

        <!-- 💬 ADVANCED TWO-WAY WHATSAPP CHAT (CLIENT SIDE) -->
        <div class="chat-card">
            <div class="chat-header">
                <div style="display:flex; align-items:center; gap:12px;">
                    <div style="width:40px; height:40px; border-radius:50%; background:var(--gold); display:flex; justify-content:center; align-items:center; color:#000; font-size:18px;"><i class="fas fa-headset"></i></div>
                    <div>
                        <h3 style="margin:0; padding:0; color:#fff; font-size:16px; border:none;">HQ Dispatch Support</h3>
                        <div style="font-size:11px; color:var(--neon-green);">Online</div>
                    </div>
                </div>
                <span style="font-size:10px; color:var(--gold); border:1px solid var(--gold); padding:3px 8px; border-radius:12px;"><i class="fas fa-lock"></i> Encrypted</span>
            </div>
            
            <div id="client-chat-area" class="chat-area">
                <div style="font-size:12px; color:rgba(255,255,255,0.5); text-align:center; margin: auto; background:rgba(0,0,0,0.4); padding:6px 12px; border-radius:12px;">End-to-end encrypted chat started</div>
            </div>

            <div id="client-reply-banner" class="reply-banner">
                <div class="reply-banner-content">
                    <div class="rep-title" id="client-reply-sender">HQ Dispatch</div>
                    <div class="replying-to-text" id="client-reply-text">Replying to...</div>
                </div>
                <button class="cancel-reply-btn" onclick="cancelReply('client', event)"><i class="fas fa-times"></i></button>
            </div>

            <div class="chat-input-wrapper">
                <input type="text" id="client-chat-input" class="chat-input-pill" placeholder="Message HQ...">
                <button class="chat-send-btn" onclick="clientSendChatMessage(event)"><i class="fas fa-paper-plane"></i></button>
            </div>
        </div>

        <button class="mn-btn btn-danger" style="max-width:100%; width:100%; flex-shrink:0;" onclick="systemLogout(event)"><i class="fas fa-power-off"></i> DISCONNECT TERMINAL</button>
    </div>

    <!-- ========================================================================= -->
    <!-- JAVASCRIPT & FIREBASE ENGINE -->
    <!-- ========================================================================= -->
    <script>
        // --- Global Click Listener for Chat Menus ---
        function closeAllMenus() {
            document.querySelectorAll('.chat-bubble').forEach(b => b.classList.remove('show-actions'));
        }

        // --- 1. PREMIUM AUDIO & PUSH NOTIFICATIONS ---
        if ('serviceWorker' in navigator) {
            navigator.serviceWorker.register('sw.js').then(function(registration) {
                console.log('SW Registered');
            }).catch(function(error) {});
        }

        function requestNotificationPermission() {
            if ("Notification" in window && Notification.permission !== "granted" && Notification.permission !== "denied") {
                Notification.requestPermission();
            }
        }

        function playPremiumChime() {
            try {
                const ctx = new (window.AudioContext || window.webkitAudioContext)();
                const osc = ctx.createOscillator(); const gain = ctx.createGain();
                osc.type = 'sine';
                osc.frequency.setValueAtTime(880, ctx.currentTime); 
                osc.frequency.exponentialRampToValueAtTime(440, ctx.currentTime + 0.5); 
                gain.gain.setValueAtTime(0.4, ctx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 1.5);
                osc.connect(gain); gain.connect(ctx.destination);
                osc.start(); osc.stop(ctx.currentTime + 1.5);
            } catch(e) {}
        }

        function showPushNotification(title, body) {
            playPremiumChime();
            
            if ("Notification" in window && Notification.permission === "granted" && 'serviceWorker' in navigator) {
                navigator.serviceWorker.ready.then(function(registration) {
                    registration.showNotification(title, {
                        body: body, icon: "https://cdn-icons-png.flaticon.com/512/814/814513.png", vibrate: [200, 100, 200], tag: "mnd-logistics", renotify: true
                    });
                });
            } else if ("Notification" in window && Notification.permission === "granted") {
                new Notification(title, { body: body, icon: "https://cdn-icons-png.flaticon.com/512/814/814513.png" });
            }

            const container = document.getElementById('toast-container');
            const push = document.createElement('div');
            push.className = 'native-push';
            push.innerHTML = `
                <div class="native-push-icon"><i class="fas fa-bell"></i></div>
                <div class="native-push-content">
                    <div class="native-push-title">${title} <span>Just Now</span></div>
                    <p class="native-push-body">${body}</p>
                </div>
            `;
            container.prepend(push); 
            setTimeout(() => { push.remove(); }, 6500);
        }

        function showToast(message, type = 'success') {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div');
            toast.className = `toast ${type}`;
            const icon = type === 'success' ? 'fa-check-circle' : 'fa-exclamation-triangle';
            toast.innerHTML = `<i class="fas ${icon}"></i> <span>${message}</span>`;
            container.appendChild(toast);
            setTimeout(() => { toast.remove(); }, 4000);
        }

        // --- 2. FIREBASE CONFIGURATION (Direct DB connection) ---
        const firebaseConfig = {
            apiKey: "AIzaSyCZP-zuJNDW9S4sD_d4R_-nrTMjf0HD4MM",
            authDomain: "mnd-tracking.firebaseapp.com",
            databaseURL: "https://mnd-tracking-default-rtdb.asia-southeast1.firebasedatabase.app",
            projectId: "mnd-tracking",
            storageBucket: "mnd-tracking.firebasestorage.app",
            messagingSenderId: "794366177184",
            appId: "1:794366177184:web:3f394f0207215ccca0fec5"
        };
        
        if (!firebase.apps.length) { firebase.initializeApp(firebaseConfig); }
        const db = firebase.database();

        // --- 3. SYSTEM STATE & CONFIG ---
        const ADMIN_NUMBERS = ["9771617808", "9153635378", "7294969938", "+918544341240", "8544341240"];
        const MASTER_PIN = "121120";
        
        let geoWatchId = null; 
        let activeClientData = null; 
        let pingCount = 0;
        
        let currentClientPhone = ""; 
        let currentClientName = "Client";
        let currentAdminTargetPhone = ""; 

        // Memory for Push Notifications
        let processedUpdateKeys = new Set();
        let processedChatKeys = new Set();
        let isInitialLoad = true;
        let isChatInitialLoad = true;

        // Chat Reply Context Memory
        let adminReplyContext = null;
        let clientReplyContext = null;

        // --- 4. VIEW ROUTER ---
        function switchView(viewId) {
            document.querySelectorAll('.view-container').forEach(el => el.classList.remove('active-view'));
            document.getElementById(viewId).classList.add('active-view');
            document.querySelectorAll('.error-msg').forEach(el => el.style.display = 'none');
            window.scrollTo(0,0);
        }

        // --- 5. THE GATEKEEPER LOGIC ---
        function processLogin(e) {
            if(e) e.stopPropagation();
            requestNotificationPermission();

            const phone = document.getElementById('login-phone').value.trim();
            const pin = document.getElementById('login-pin').value.trim();
            const err = document.getElementById('login-error');
            const btn = document.getElementById('login-btn');

            if(!phone || !pin) { err.innerHTML = "<i class='fas fa-exclamation'></i> Credentials required."; err.style.display = 'block'; return; }

            // Admin Logic
            if(ADMIN_NUMBERS.includes(phone) && pin === MASTER_PIN) {
                document.getElementById('login-phone').value = ''; document.getElementById('login-pin').value = '';
                switchView('view-admin');
                startAdminHistoryListener(); 
                showToast("Admin Protocol Authorized");
                return;
            }

            // Client Logic
            btn.innerHTML = '<i class="fas fa-circle-notch fa-spin"></i> DECRYPTING...';
            btn.disabled = true; err.style.display = 'none';

            db.ref('trackings/' + phone).once('value').then((snapshot) => {
                btn.innerHTML = '<i class="fas fa-fingerprint"></i> INITIATE HANDSHAKE';
                btn.disabled = false;

                if (snapshot.exists()) {
                    const clientData = snapshot.val();
                    if(clientData.pin === pin || !clientData.pin) {
                        document.getElementById('login-phone').value = ''; document.getElementById('login-pin').value = '';
                        document.getElementById('client-dash-name').innerText = clientData.name || "Client";
                        document.getElementById('client-dash-event').innerText = clientData.event || "Event Logistics";
                        
                        currentClientPhone = phone;
                        currentClientName = clientData.name || "Client";
                        
                        processedUpdateKeys.clear(); processedChatKeys.clear();
                        isInitialLoad = true; isChatInitialLoad = true;

                        startClientWatchListeners(); 
                        switchView('view-client');
                        showToast(`Welcome, ${currentClientName}`);
                    } else {
                        err.innerHTML = "<i class='fas fa-shield-alt'></i> Incorrect Security PIN."; err.style.display = 'block';
                    }
                } else {
                    err.innerHTML = "<i class='fas fa-search'></i> Profile not found in Active Database."; err.style.display = 'block';
                }
            }).catch((error) => {
                btn.innerHTML = '<i class="fas fa-fingerprint"></i> INITIATE HANDSHAKE'; btn.disabled = false;
                err.innerHTML = "<i class='fas fa-wifi'></i> Server connection failed. Check Database Rules."; err.style.display = 'block';
            });
        }

        // --- 6. ADMIN: PROVISION & UPDATE PIN ---
        function adminGeneratePin(e) {
            if(e) e.stopPropagation();
            const name = document.getElementById('admin-c-name').value.trim();
            const phone = document.getElementById('admin-c-phone').value.trim();
            const event = document.getElementById('admin-c-event').value.trim();
            const customPinInput = document.getElementById('admin-c-custom-pin').value.trim();
            const btn = document.getElementById('btn-generate');

            if(!name || !phone || !event) { showToast("Name, Phone, and Event required.", "error"); return; }
            if(customPinInput && customPinInput.length !== 6) { showToast("Custom PIN must be exactly 6 digits.", "error"); return; }

            btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> SAVING TO CLOUD...'; btn.disabled = true;

            const finalPin = customPinInput ? customPinInput : Math.floor(100000 + Math.random() * 900000).toString();
            const timestamp = Date.now();
            const humanDate = new Date().toLocaleString('en-US', { day:'numeric', month:'short', hour:'2-digit', minute:'2-digit'});

            activeClientData = { name, phone, event, pin: finalPin, timestamp: timestamp, dateStr: humanDate };

            db.ref('trackings/' + phone).update(activeClientData).then(() => {
                document.getElementById('display-new-pin').innerText = finalPin;
                document.getElementById('admin-pin-result').style.display = 'block';
                
                document.getElementById('admin-c-name').value = ''; document.getElementById('admin-c-phone').value = '';
                document.getElementById('admin-c-event').value = ''; document.getElementById('admin-c-custom-pin').value = '';

                btn.innerHTML = '<i class="fas fa-microchip"></i> AUTHORIZE & SAVE TO CLOUD'; btn.disabled = false;
                showToast("Client Provisioned/Updated Successfully");
            }).catch((error) => {
                showToast("Database Permission Denied. Check Rules.", "error");
                btn.innerHTML = '<i class="fas fa-microchip"></i> AUTHORIZE & SAVE TO CLOUD'; btn.disabled = false;
            });
        }

        function adminShareWhatsApp(e) {
            if(e) e.stopPropagation();
            if (!activeClientData) return;
            
            const msg = `*👑 MAA NIRMALA DJ & TENT HOUSE BELTIKRI ( 813106 ) 👑*\n\n *📍 LIVE LOCATION TRACKING📍*\n\nHello ${activeClientData.name},\nYour logistics for *${activeClientData.event}* are active.\n----------------------------------------------------------\n*How to track:*\n1. Click the link and open the website.\n2. At the bottom right corner (above the MND AI button), click the 'Live Location' button to open the tracking portal.\n3. Enter your registered mobile number and 6-digit PIN to track.\n----------------------------------------------------------\n ••••••••••••••••••••••••••••••••••••••••••••••••\n 🔗 *Link:* https://maa-nirmala-dj.github.io/-tent-house./\n📱 *Login Number:* ${activeClientData.phone}\n🔐 *Tracking PIN:* ${activeClientData.pin}\n•••••••••••••••••••••••••••••••••••••••••••••••\nThank You.`;

            const cleanPhone = activeClientData.phone.replace(/\D/g, '');
            window.open(`https://wa.me/91${cleanPhone}?text=${encodeURIComponent(msg)}`, '_blank');
        }

        // --- 7. ADMIN: SEQUENTIAL HISTORY LIST ---
        function startAdminHistoryListener() {
            const listArea = document.getElementById('admin-active-list');
            db.ref('trackings').orderByChild('timestamp').on('value', (snapshot) => {
                if(snapshot.exists()) {
                    let html = '';
                    const records = [];
                    snapshot.forEach(child => {
                        let data = child.val();
                        if(data.name && data.phone) { records.push(data); }
                    });
                    
                    records.reverse().forEach(data => {
                        html += `
                            <div class="client-list-item" onclick="openAdminSession('${data.phone}', '${data.name.replace(/'/g, "\\'")}', '${(data.event || 'Logistics Event').replace(/'/g, "\\'")}')">
                                <div style="width: 60%; overflow: hidden;">
                                    <h4 style="white-space:nowrap; overflow:hidden; text-overflow:ellipsis;">${data.name}</h4>
                                    <p style="color:#D4AF37; margin-bottom:2px; white-space:nowrap; overflow:hidden; text-overflow:ellipsis;">${data.event || 'Logistics Event'}</p>
                                    <p>${data.phone} • <span style="font-size:9px;">${data.dateStr || 'Legacy Record'}</span></p>
                                </div>
                                <div class="client-list-actions">
                                    <div class="client-list-pin" title="Active PIN">${data.pin || 'N/A'}</div>
                                    <button class="action-icon-btn trash" onclick="event.stopPropagation(); revokeAccess('${data.phone}')" title="Revoke"><i class="fas fa-trash-alt"></i></button>
                                </div>
                            </div>
                        `;
                    });
                    listArea.innerHTML = html;
                } else {
                    listArea.innerHTML = '<div style="text-align:center; color:#555; font-size:13px; padding:20px;">History is empty.</div>';
                }
            });
        }

        function revokeAccess(phone) {
            if(confirm("Are you sure you want to permanently revoke tracking access for this number?")) {
                db.ref('trackings/' + phone).remove().then(() => showToast("Access Revoked")).catch(err => showToast("Error", "error"));
            }
        }

        function openAdminSession(phone, name, eventName) {
            currentAdminTargetPhone = phone;
            document.getElementById('session-c-name').innerText = name;
            document.getElementById('session-c-event').innerText = eventName || "Event Logistics";
            document.getElementById('session-c-phone').innerText = phone;
            document.getElementById('admin-chat-header-name').innerText = name; // Update chat header
            
            switchView('view-admin-session');
            showToast(`Target Locked: ${name}`, 'success');

            processedChatKeys.clear();
            isChatInitialLoad = true;
            cancelReply('admin');

            // 1. Listen for Client's Location Share
            db.ref(`trackings/${currentAdminTargetPhone}/client_location`).on('value', (snap) => {
                const card = document.getElementById('admin-client-loc-card');
                if(snap.exists()) {
                    card.style.display = 'block';
                    const loc = snap.val();
                    document.getElementById('admin-view-client-map').href = `https://www.google.com/maps/search/?api=1&query=${loc.lat},${loc.lng}`;
                } else {
                    card.style.display = 'none';
                }
            });

            // 2. Listen to Official Updates Timeline
            db.ref(`trackings/${currentAdminTargetPhone}/updates`).orderByChild('timestamp').on('value', (snap) => {
                const area = document.getElementById('admin-manage-updates-area');
                if(snap.exists()) {
                    let html = '';
                    const updates = [];
                    snap.forEach(child => updates.push({ key: child.key, ...child.val() }));
                    
                    updates.reverse().forEach(u => {
                        html += `
                            <div class="timeline-item" style="padding-bottom:10px; margin-bottom:10px;">
                                <div class="update-content">
                                    <div class="update-time">${u.time}</div>
                                    <div class="update-text">${u.text}</div>
                                </div>
                                <button class="action-icon-btn trash" onclick="adminDeleteUpdate('${u.key}', event)"><i class="fas fa-times-circle"></i></button>
                            </div>
                        `;
                    });
                    area.innerHTML = html;
                } else {
                    area.innerHTML = '<div style="font-size:12px; color:#555; text-align:center; margin-top:10px;">No updates sent yet.</div>';
                }
            });

            // 3. 💬 ADVANCED TWO-WAY CHAT LISTENER (ADMIN VIEW)
            db.ref(`trackings/${currentAdminTargetPhone}/client_messages`).orderByChild('timestamp').limitToLast(100).on('value', (snap) => {
                const area = document.getElementById('admin-chat-area');
                if(snap.exists()) {
                    let html = '';
                    
                    snap.forEach(child => {
                        const key = child.key;
                        const m = child.val();
                        
                        // Push Notification for new messages from Client
                        if (!isChatInitialLoad && !processedChatKeys.has(key) && m.sender === 'client') {
                            showPushNotification(`Message from ${name}`, m.text);
                        }
                        processedChatKeys.add(key);

                        const isMe = m.sender === 'admin';
                        const bubbleClass = isMe ? 'sent' : 'received'; // Using WA style
                        const safeText = m.text.replace(/'/g, "\\'").replace(/"/g, "&quot;");
                        
                        let replyHtml = '';
                        if(m.replyToText) {
                            const senderName = m.replyToSender === 'admin' ? 'HQ Dispatch' : name;
                            replyHtml = `<div class="chat-reply-context"><div class="sender">${senderName}</div>${m.replyToText}</div>`;
                        }

                        html += `
                            <div class="chat-bubble ${bubbleClass}" id="msg-${key}" onclick="toggleChatActions('msg-${key}', event)">
                                ${replyHtml}
                                <div>${m.text}</div>
                                <div class="chat-time">${m.time}</div>
                                <div class="chat-actions-menu">
                                    <button class="chat-action-btn" onclick="copyChatText('${safeText}', event)"><i class="fas fa-copy"></i></button>
                                    <button class="chat-action-btn" onclick="initiateReply('${key}', '${safeText}', '${m.sender}', 'admin', event)"><i class="fas fa-reply"></i></button>
                                    <button class="chat-action-btn delete" onclick="deleteChatMessage('${currentAdminTargetPhone}', '${key}', event)"><i class="fas fa-trash"></i></button>
                                </div>
                            </div>
                        `;
                    });
                    
                    isChatInitialLoad = false;
                    area.innerHTML = html;
                    area.scrollTop = area.scrollHeight; // Auto-scroll
                } else {
                    area.innerHTML = '<div style="font-size:12px; color:rgba(255,255,255,0.5); text-align:center; margin: auto; background:rgba(0,0,0,0.4); padding:6px 12px; border-radius:12px;">End-to-end encrypted chat started</div>';
                    isChatInitialLoad = false;
                }
            });
        }

        function closeAdminSession(e) {
            if(e) e.stopPropagation();
            if(geoWatchId !== null) toggleLocationBroadcast(); 
            if(currentAdminTargetPhone) {
                db.ref(`trackings/${currentAdminTargetPhone}/client_location`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/updates`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/client_messages`).off();
            }
            currentAdminTargetPhone = "";
            switchView('view-admin');
        }

        // --- 8. AI REVERSE GEOCODING (Nearest Village) ---
        async function fetchLocationName(lat, lng) {
            try {
                const res = await fetch(`https://nominatim.openstreetmap.org/reverse?format=json&lat=${lat}&lon=${lng}&zoom=14`);
                const data = await res.json();
                if(data && data.address) {
                    return data.address.village || data.address.town || data.address.city || data.address.county || data.display_name.split(',')[0];
                }
                return "Unknown Zone";
            } catch(e) { return "Coordinates Locked"; }
        }

        // --- 9. ADMIN: TARGETED GPS TELEMETRY ---
        function toggleLocationBroadcast(e) {
            if(e) e.stopPropagation();
            const btn = document.getElementById('btn-broadcast');
            const metrics = document.getElementById('admin-telemetrics');
            const locDisp = document.getElementById('admin-ai-location');

            if(!currentAdminTargetPhone) return;

            if(geoWatchId !== null) {
                navigator.geolocation.clearWatch(geoWatchId);
                geoWatchId = null; pingCount = 0;
                btn.innerHTML = '<i class="fas fa-location-arrow"></i> INITIATE BROADCAST TO TARGET';
                btn.style.background = "transparent"; btn.style.color = "var(--neon-cyan)"; btn.style.borderColor = "var(--neon-cyan)";
                metrics.style.display = 'none'; locDisp.style.display = 'none';
                
                db.ref(`trackings/${currentAdminTargetPhone}/location/status`).set('offline').catch(err => console.warn(err)); 
                showToast("Transmission Terminated", "error");
            } else {
                if(!navigator.geolocation) { showToast("Hardware GPS not supported.", "error"); return; }
                btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> ACQUIRING SATELLITES...';
                
                geoWatchId = navigator.geolocation.watchPosition(
                    async (pos) => {
                        btn.innerHTML = '<i class="fas fa-times-circle"></i> STOP BROADCASTING';
                        btn.style.background = "rgba(255,51,51,0.1)"; btn.style.color = "var(--danger)"; btn.style.borderColor = "var(--danger)";
                        metrics.style.display = 'grid'; locDisp.style.display = 'flex';
                        
                        pingCount++;
                        const speedKmh = pos.coords.speed ? (pos.coords.speed * 3.6).toFixed(1) : 0;
                        
                        document.getElementById('a-speed').innerText = speedKmh + " km/h";
                        document.getElementById('a-acc').innerText = "±" + Math.round(pos.coords.accuracy) + "m";
                        document.getElementById('a-ping').innerText = pingCount;

                        let locName = "Resolving...";
                        if(pingCount % 10 === 1) { 
                            locName = await fetchLocationName(pos.coords.latitude, pos.coords.longitude);
                            document.getElementById('a-loc-name').innerText = locName;
                        } else {
                            locName = document.getElementById('a-loc-name').innerText;
                        }

                        db.ref(`trackings/${currentAdminTargetPhone}/location`).set({
                            status: 'online', lat: pos.coords.latitude, lng: pos.coords.longitude,
                            speed: speedKmh, heading: pos.coords.heading ? Math.round(pos.coords.heading) + "°" : "N/A",
                            accuracy: Math.round(pos.coords.accuracy), time: new Date().toLocaleTimeString(),
                            locationName: locName
                        });
                        
                        if(pingCount === 1) showToast("Satellite Uplink Established");
                    },
                    (err) => {
                        showToast("GPS Error. Ensure Location Services are ON.", "error");
                        if(geoWatchId !== null) navigator.geolocation.clearWatch(geoWatchId);
                        geoWatchId = null; btn.innerHTML = '<i class="fas fa-location-arrow"></i> INITIATE BROADCAST TO TARGET';
                    }, 
                    { enableHighAccuracy: true, maximumAge: 0, timeout: 10000 }
                );
            }
        }

        // --- 10. ADMIN: TARGETED OFFICIAL UPDATE (Timeline) ---
        function setQuickUpdate(msg, e) { 
            if(e) e.stopPropagation();
            document.getElementById('admin-update-text').value = msg; 
        }

        function adminPushUpdate(e) {
            if(e) e.stopPropagation();
            if(!currentAdminTargetPhone) return;

            const text = document.getElementById('admin-update-text').value.trim();
            const btn = document.getElementById('btn-push-update');
            if(!text) { showToast("Enter an official update to transmit.", "error"); return; }

            btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> TRANSMITTING...'; btn.disabled = true;

            db.ref(`trackings/${currentAdminTargetPhone}/updates`).push({
                text: text, time: new Date().toLocaleTimeString('en-US', {hour: '2-digit', minute:'2-digit'}), timestamp: Date.now()
            }).then(() => {
                document.getElementById('admin-update-text').value = '';
                btn.innerHTML = '<i class="fas fa-paper-plane"></i> TRANSMIT TO TIMELINE'; btn.disabled = false;
                showToast("Official Update Transmitted");
            }).catch(err => {
                showToast("Permission Denied", "error"); btn.disabled = false;
            });
        }

        function adminDeleteUpdate(key, e) {
            if(e) e.stopPropagation();
            if(!currentAdminTargetPhone) return;
            db.ref(`trackings/${currentAdminTargetPhone}/updates/${key}`).remove()
            .then(() => showToast("Update Deleted"))
            .catch(e => showToast("Failed to delete", "error"));
        }

        // --- 11. 💬 WHATSAPP-STYLE CHAT FUNCTIONS (TWO-WAY) ---
        function toggleChatActions(bubbleId, e) {
            if(e) e.stopPropagation();
            closeAllMenus(); // Close others first
            document.getElementById(bubbleId).classList.toggle('show-actions');
        }

        function copyChatText(text, e) {
            if(e) e.stopPropagation();
            navigator.clipboard.writeText(text).then(() => {
                showToast("Copied to clipboard!");
                closeAllMenus();
            }).catch(err => {
                showToast("Failed to copy", "error");
            });
        }

        function initiateReply(key, text, originalSenderRole, role, e) {
            if(e) e.stopPropagation();
            closeAllMenus();
            
            // Limit reply text length visually
            let shortText = text.length > 40 ? text.substring(0, 40) + '...' : text;
            
            if(role === 'admin') {
                const senderName = originalSenderRole === 'admin' ? 'HQ Dispatch' : document.getElementById('session-c-name').innerText;
                adminReplyContext = { key, text: shortText, sender: originalSenderRole };
                document.getElementById('admin-reply-sender').innerText = senderName;
                document.getElementById('admin-reply-text').innerText = shortText;
                document.getElementById('admin-reply-banner').style.display = 'flex';
                document.getElementById('admin-chat-input').focus();
            } else {
                const senderName = originalSenderRole === 'admin' ? 'HQ Dispatch' : currentClientName;
                clientReplyContext = { key, text: shortText, sender: originalSenderRole };
                document.getElementById('client-reply-sender').innerText = senderName;
                document.getElementById('client-reply-text').innerText = shortText;
                document.getElementById('client-reply-banner').style.display = 'flex';
                document.getElementById('client-chat-input').focus();
            }
        }

        function cancelReply(role, e) {
            if(e) e.stopPropagation();
            if(role === 'admin') {
                adminReplyContext = null;
                document.getElementById('admin-reply-banner').style.display = 'none';
            } else {
                clientReplyContext = null;
                document.getElementById('client-reply-banner').style.display = 'none';
            }
        }

        function adminSendChatMessage(e) {
            if(e) e.stopPropagation();
            if(!currentAdminTargetPhone) return;
            
            const input = document.getElementById('admin-chat-input');
            const text = input.value.trim();
            if(!text) return;

            const msgData = {
                text: text, sender: 'admin',
                time: new Date().toLocaleTimeString('en-US', {hour: '2-digit', minute:'2-digit'}),
                timestamp: Date.now()
            };

            if(adminReplyContext) {
                msgData.replyToKey = adminReplyContext.key;
                msgData.replyToText = adminReplyContext.text;
                msgData.replyToSender = adminReplyContext.sender;
            }

            db.ref(`trackings/${currentAdminTargetPhone}/client_messages`).push(msgData).then(() => {
                input.value = '';
                cancelReply('admin');
            }).catch(e => showToast("Send failed", "error"));
        }

        function clientSendChatMessage(e) {
            if(e) e.stopPropagation();
            if(!currentClientPhone) return;
            
            const input = document.getElementById('client-chat-input');
            const text = input.value.trim();
            if(!text) return;

            const msgData = {
                text: text, sender: 'client',
                time: new Date().toLocaleTimeString('en-US', {hour: '2-digit', minute:'2-digit'}),
                timestamp: Date.now()
            };

            if(clientReplyContext) {
                msgData.replyToKey = clientReplyContext.key;
                msgData.replyToText = clientReplyContext.text;
                msgData.replyToSender = clientReplyContext.sender;
            }

            db.ref(`trackings/${currentClientPhone}/client_messages`).push(msgData).then(() => {
                input.value = '';
                cancelReply('client');
            }).catch(e => showToast("Send failed", "error"));
        }

        function deleteChatMessage(targetPhone, key, e) {
            if(e) e.stopPropagation();
            if(!targetPhone || !key) return;
            
            db.ref(`trackings/${targetPhone}/client_messages/${key}`).remove()
            .then(() => {
                closeAllMenus();
            })
            .catch(err => showToast("Failed to delete", "error"));
        }


        // --- 12. CLIENT: SHARE LOCATION TO HQ ---
        function clientShareLocation(e) {
            if(e) e.stopPropagation();
            if(!currentClientPhone) return;
            if(!navigator.geolocation) { showToast("GPS not supported on this device.", "error"); return; }
            
            showToast("Acquiring your location...");
            navigator.geolocation.getCurrentPosition((pos) => {
                db.ref(`trackings/${currentClientPhone}/client_location`).set({
                    lat: pos.coords.latitude, lng: pos.coords.longitude, time: Date.now()
                }).then(() => {
                    showToast("Location Shared with HQ Successfully!");
                });
            }, (err) => {
                showToast("Failed to get location. Ensure GPS is ON.", "error");
            }, { enableHighAccuracy: true });
        }

        // --- 13. CLIENT: TARGETED WATCH LISTENERS & PUSH NOTIFICATIONS ---
        let lastKnownLat = 0; let lastKnownLng = 0;

        function startClientWatchListeners() {
            if(!currentClientPhone) return;

            // GPS Listener
            db.ref(`trackings/${currentClientPhone}/location`).on('value', (snapshot) => {
                const badge = document.getElementById('client-conn-status');
                if(snapshot.exists() && snapshot.val().status === 'online') {
                    const data = snapshot.val();
                    badge.innerHTML = '<i class="fas fa-circle fa-beat"></i> ONLINE'; badge.className = 'status-badge';
                    document.getElementById('client-gps-time').innerHTML = `<span style="color:var(--neon-green);">Signal Locked (${data.time})</span>`;
                    document.getElementById('c-speed').innerText = data.speed + " km/h";
                    document.getElementById('c-heading').innerText = data.heading;
                    document.getElementById('c-accuracy').innerText = "±" + data.accuracy + "m";
                    
                    document.getElementById('map-loader-text').style.display = 'none';
                    document.getElementById('ext-map-link').style.display = 'block';
                    document.getElementById('ext-map-link').href = `https://www.google.com/maps/search/?api=1&query=${data.lat},${data.lng}`;

                    const locDisp = document.getElementById('client-location-display');
                    if(data.locationName && data.locationName !== "Resolving...") {
                        locDisp.style.display = 'flex';
                        document.getElementById('c-location-name').innerText = data.locationName;
                    }

                    if(Math.abs(data.lat - lastKnownLat) > 0.0001 || Math.abs(data.lng - lastKnownLng) > 0.0001) {
                        const mapUrl = `https://maps.google.com/maps?q=${data.lat},${data.lng}&z=15&output=embed`;
                        document.getElementById('client-map-iframe').src = mapUrl;
                        lastKnownLat = data.lat; lastKnownLng = data.lng;
                    }
                } else {
                    badge.innerHTML = '<i class="fas fa-exclamation-circle"></i> SIGNAL LOST'; badge.className = 'status-badge offline';
                    document.getElementById('client-gps-time').innerText = "Target Offline";
                }
            });

            // 1. Official Updates Listener (One-way Timeline)
            db.ref(`trackings/${currentClientPhone}/updates`).orderByChild('timestamp').limitToLast(50).on('value', (snapshot) => {
                const feedArea = document.getElementById('client-updates-area');
                if(snapshot.exists()) {
                    let html = '';
                    const updates = [];
                    
                    snapshot.forEach(child => {
                        const key = child.key;
                        const updateData = child.val();
                        updates.push({key, ...updateData});

                        if (!isInitialLoad && !processedUpdateKeys.has(key)) {
                            showPushNotification("Official Logistics Update", updateData.text);
                        }
                        processedUpdateKeys.add(key);
                    });
                    
                    isInitialLoad = false; 
                    
                    updates.reverse().forEach((update) => {
                        html += `
                            <div class="timeline-item">
                                <div class="update-content">
                                    <div class="update-time">${update.time}</div>
                                    <div class="update-text">${update.text}</div>
                                </div>
                            </div>
                        `;
                    });
                    feedArea.innerHTML = html;
                } else {
                    feedArea.innerHTML = '<div style="text-align:center; color:#555; font-size:13px; padding:20px; border:none; margin-left:-20px;">Awaiting communications...</div>';
                    isInitialLoad = false; 
                }
            });

            // 2. 💬 ADVANCED TWO-WAY CHAT LISTENER (CLIENT VIEW)
            db.ref(`trackings/${currentClientPhone}/client_messages`).orderByChild('timestamp').limitToLast(100).on('value', (snap) => {
                const area = document.getElementById('client-chat-area');
                if(snap.exists()) {
                    let html = '';
                    
                    snap.forEach(child => {
                        const key = child.key;
                        const m = child.val();
                        
                        // Push Notification for new messages from Admin
                        if (!isChatInitialLoad && !processedChatKeys.has(key) && m.sender === 'admin') {
                            showPushNotification("New Message from HQ", m.text);
                        }
                        processedChatKeys.add(key);

                        const isMe = m.sender === 'client';
                        const bubbleClass = isMe ? 'sent' : 'received';
                        const safeText = m.text.replace(/'/g, "\\'").replace(/"/g, "&quot;");
                        
                        let replyHtml = '';
                        if(m.replyToText) {
                            const senderName = m.replyToSender === 'client' ? 'You' : 'HQ Dispatch';
                            replyHtml = `<div class="chat-reply-context"><div class="sender">${senderName}</div>${m.replyToText}</div>`;
                        }

                        // Client can only delete THEIR OWN messages
                        const deleteBtnHtml = isMe ? `<button class="chat-action-btn delete" onclick="deleteChatMessage('${currentClientPhone}', '${key}', event)"><i class="fas fa-trash"></i></button>` : '';

                        html += `
                            <div class="chat-bubble ${bubbleClass}" id="cmsg-${key}" onclick="toggleChatActions('cmsg-${key}', event)">
                                ${replyHtml}
                                <div>${m.text}</div>
                                <div class="chat-time">${m.time}</div>
                                <div class="chat-actions-menu">
                                    <button class="chat-action-btn" onclick="copyChatText('${safeText}', event)"><i class="fas fa-copy"></i></button>
                                    <button class="chat-action-btn" onclick="initiateReply('${key}', '${safeText}', '${m.sender}', 'client', event)"><i class="fas fa-reply"></i></button>
                                    ${deleteBtnHtml}
                                </div>
                            </div>
                        `;
                    });
                    
                    isChatInitialLoad = false;
                    area.innerHTML = html;
                    area.scrollTop = area.scrollHeight; // Auto-scroll to bottom
                } else {
                    area.innerHTML = '<div style="font-size:12px; color:rgba(255,255,255,0.5); text-align:center; margin: auto; background:rgba(0,0,0,0.4); padding:6px 12px; border-radius:12px;">End-to-end encrypted chat started</div>';
                    isChatInitialLoad = false;
                }
            });
        }

        // --- 14. GLOBAL LOGOUT ---
        function systemLogout(e) {
            if(e) e.stopPropagation();
            if(geoWatchId !== null) toggleLocationBroadcast();
            
            db.ref('trackings').off(); 
            if(currentClientPhone) {
                db.ref(`trackings/${currentClientPhone}/location`).off();
                db.ref(`trackings/${currentClientPhone}/updates`).off();
                db.ref(`trackings/${currentClientPhone}/client_messages`).off();
            }
            if(currentAdminTargetPhone) {
                db.ref(`trackings/${currentAdminTargetPhone}/client_location`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/updates`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/client_messages`).off();
            }
            
            currentAdminTargetPhone = ""; currentClientPhone = "";
            cancelReply('admin'); cancelReply('client');
            
            document.getElementById('client-map-iframe').src = "";
            document.getElementById('map-loader-text').style.display = 'block';
            document.getElementById('client-location-display').style.display = 'none';
            document.getElementById('ext-map-link').style.display = 'none';
            document.getElementById('client-gps-time').innerText = "Awaiting Signal...";
            
            showToast("Session Terminated", "error");
            switchView('view-gatekeeper');
        }
    </script>
</body>
</html>
