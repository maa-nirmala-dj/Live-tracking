<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="theme-color" content="#050508">
    <title>MND Live Logistics | Premium Cloud Tracking</title>

    <!-- Premium Fonts & Icons -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@600;800;900&family=Outfit:wght@300;400;500;700;900&family=Orbitron:wght@500;700;900&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    
    <!-- Premium Interactive Map (Leaflet) replacing standard iframe -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin="" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo=" crossorigin=""></script>

    <!-- Firebase SDKs (Database & Storage for Advanced Media) -->
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-database-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-storage-compat.js"></script>

    <style>
        /* =========================================================================
           GLOBAL RESET & VARIABLES (ENHANCED PREMIUM)
           ========================================================================= */
        :root {
            --gold: #D4AF37;
            --gold-glow: rgba(212, 175, 55, 0.4);
            --neon-green: #00FA9A;
            --neon-cyan: #00E5FF;
            --bg-dark: #050508;
            --panel-bg: rgba(15, 15, 20, 0.85);
            --glass-border: rgba(255, 255, 255, 0.08);
            --danger: #ff3333;
            --sos-red: #ff0044;
            
            /* WhatsApp Dark Theme Colors - Perfected */
            --wa-bg: #0b141a;
            --wa-header: #202c33;
            --wa-sent: #005c4b;
            --wa-received: #202c33;
            --wa-text: #e9edef;
            --wa-time: rgba(255,255,255,0.55);
            --wa-blue-tick: #53bdeb;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; outline: none; }
        body, html { width: 100vw; height: 100dvh; background: var(--bg-dark); color: #fff; overflow: hidden; display: flex; justify-content: center; align-items: flex-start; scroll-behavior: smooth; }

        /* Cinematic Background */
        .bg-map {
            position: fixed; inset: 0; z-index: -1; opacity: 0.15;
            background-image: 
                radial-gradient(circle at 20% 30%, var(--gold-glow) 0%, transparent 50%),
                radial-gradient(circle at 80% 80%, rgba(0, 250, 154, 0.15) 0%, transparent 50%);
            animation: pulseBg 8s infinite alternate ease-in-out;
        }
        .grid-overlay {
            position: fixed; inset: 0; z-index: -1; opacity: 0.04;
            background-image: linear-gradient(var(--gold) 1px, transparent 1px), linear-gradient(90deg, var(--gold) 1px, transparent 1px);
            background-size: 40px 40px;
        }
        @keyframes pulseBg { 0% { opacity: 0.1; transform: scale(1); } 100% { opacity: 0.25; transform: scale(1.08); } }

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

        .mn-btn { width: 100%; padding: 16px; border-radius: 12px; font-weight: 800; font-size: 14px; cursor: pointer; transition: all 0.3s ease; display: flex; justify-content: center; align-items: center; gap: 10px; border: none; letter-spacing: 1px; text-transform: uppercase; flex-shrink: 0; position: relative; overflow: hidden; }
        .mn-btn::after { content: ''; position: absolute; top: 50%; left: 50%; width: 5px; height: 5px; background: rgba(255,255,255,0.5); opacity: 0; border-radius: 100%; transform: scale(1, 1) translate(-50%); transform-origin: 50% 50%; }
        .mn-btn:active::after { animation: ripple 0.6s ease-out; }
        @keyframes ripple { 0% { transform: scale(0, 0); opacity: 0.5; } 100% { transform: scale(40, 40); opacity: 0; } }
        
        .mn-btn:active { transform: scale(0.96); }
        .btn-gold { background: linear-gradient(135deg, var(--gold) 0%, #FFD700 100%); color: #000; box-shadow: 0 5px 25px var(--gold-glow); }
        .btn-green { background: linear-gradient(135deg, #25D366 0%, #128C7E 100%); color: #fff; box-shadow: 0 5px 20px rgba(37, 211, 102, 0.3); }
        .btn-dark { background: rgba(0,0,0,0.5); border: 1px solid var(--gold); color: var(--gold); backdrop-filter: blur(5px); }
        .btn-danger { background: rgba(255,51,51,0.1); border: 1px solid var(--danger); color: var(--danger); margin-top: 15px; }
        
        /* SOS Button Premium */
        .btn-sos { background: linear-gradient(135deg, #ff0044 0%, #cc0000 100%); color: #fff; box-shadow: 0 5px 30px rgba(255, 0, 68, 0.5); border: 2px solid #ff4d4d; animation: sosPulse 2s infinite; }
        @keyframes sosPulse { 0% { box-shadow: 0 0 0 0 rgba(255, 0, 68, 0.7); } 70% { box-shadow: 0 0 0 20px rgba(255, 0, 68, 0); } 100% { box-shadow: 0 0 0 0 rgba(255, 0, 68, 0); } }

        .dash-header { width: 100%; max-width: 600px; display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; border-bottom: 1px dashed rgba(212,175,55,0.3); padding-bottom: 15px; flex-shrink: 0; }
        .status-badge { background: rgba(0, 250, 154, 0.1); border: 1px solid var(--neon-green); color: var(--neon-green); padding: 6px 14px; border-radius: 30px; font-size: 11px; font-weight: 800; display: flex; align-items: center; gap: 6px; letter-spacing: 1px; box-shadow: 0 0 15px rgba(0, 250, 154, 0.2); }
        .status-badge.offline { background: rgba(255, 51, 51, 0.1); border-color: var(--danger); color: var(--danger); box-shadow: 0 0 15px rgba(255, 51, 51, 0.2); }
        .status-badge.sos { background: rgba(255, 0, 68, 0.2); border-color: var(--sos-red); color: var(--sos-red); box-shadow: 0 0 20px rgba(255, 0, 68, 0.5); animation: blink 1s infinite; }
        @keyframes blink { 0%, 100% { opacity: 1; } 50% { opacity: 0.5; } }

        .card { width: 100%; max-width: 600px; background: rgba(10,10,15,0.85); border: 1px solid var(--glass-border); padding: 20px; border-radius: 16px; margin-bottom: 20px; backdrop-filter: blur(15px); box-shadow: 0 10px 30px rgba(0,0,0,0.5); flex-shrink: 0; position: relative; overflow: hidden; }
        .card::before { content:''; position:absolute; top:0; left:0; width:100%; height:4px; background: linear-gradient(90deg, transparent, var(--gold), transparent); opacity:0.5;}
        .card.flex-grow { flex-grow: 1; max-height: none; }
        .card h3 { color: var(--gold); font-family: 'Cinzel'; margin-bottom: 15px; font-size: 16px; border-bottom: 1px dashed rgba(212,175,55,0.3); padding-bottom: 8px; display: flex; align-items: center; gap: 10px; }

        /* =========================================================================
           TIMELINE, HISTORY & MAP (ADVANCED LEAFLET)
           ========================================================================= */
        .client-list-item { background: rgba(0,0,0,0.6); padding: 15px; border-radius: 10px; border-left: 4px solid var(--neon-cyan); margin-bottom: 12px; display: flex; justify-content: space-between; align-items: center; transition: 0.3s; cursor: pointer; box-shadow: 0 5px 15px rgba(0,0,0,0.3); position: relative;}
        .client-list-item:hover { background: rgba(212, 175, 55, 0.1); border-left-color: var(--gold); transform: translateY(-2px); }
        .client-list-item h4 { margin:0 0 4px 0; font-size: 15px; color: #fff; font-family: 'Cinzel', serif; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .client-list-item p { margin:0; font-size: 12px; color: #888; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .client-list-actions { display: flex; align-items: center; gap: 12px; }
        .client-list-pin { font-family: 'Orbitron', monospace; font-size: 16px; color: var(--gold); font-weight: bold; background: rgba(212,175,55,0.1); padding: 6px 10px; border-radius: 6px; letter-spacing: 2px; }
        .sos-indicator { position: absolute; top: -5px; right: -5px; background: red; color: white; border-radius: 50%; width: 20px; height: 20px; display: flex; align-items: center; justify-content: center; font-size: 10px; animation: blink 1s infinite; display: none; }

        .action-icon-btn { background: transparent; border: none; color: #888; font-size: 18px; cursor: pointer; transition: 0.3s; padding: 5px; }
        .action-icon-btn:hover { color: var(--neon-cyan); transform: scale(1.1); }
        .action-icon-btn.trash { color: rgba(255,51,51,0.7); }
        .action-icon-btn.trash:hover { color: var(--danger); transform: scale(1.2); }

        .timeline-trash-btn { background: #e53935; color: #050508; border: none; width: 22px; height: 22px; border-radius: 50%; display: flex; align-items: center; justify-content: center; cursor: pointer; transition: 0.2s; font-size: 11px; flex-shrink: 0; box-shadow: 0 2px 5px rgba(0,0,0,0.5); margin-top: 2px; }
        .timeline-trash-btn:hover { background: #ff4c4c; transform: scale(1.1); }

        /* Advanced Map Wrapper */
        .map-wrapper { width: 100%; height: 300px; border-radius: 12px; overflow: hidden; border: 2px solid #222; position: relative; background: #050505; margin-bottom: 15px; box-shadow: inset 0 0 20px rgba(0,0,0,0.8); z-index: 1;}
        #client-map, #admin-map { width: 100%; height: 100%; z-index: 1;}
        /* Dark Theme Leaflet Maps */
        .leaflet-layer,
        .leaflet-control-zoom-in,
        .leaflet-control-zoom-out,
        .leaflet-control-attribution {
            filter: invert(100%) hue-rotate(180deg) brightness(95%) contrast(90%);
        }

        /* Advanced Location Display (Village Name & Weather) */
        .location-display { background: rgba(0,229,255,0.05); border: 1px solid rgba(0,229,255,0.2); border-radius: 8px; padding: 12px; margin-bottom: 15px; display: flex; align-items: center; gap: 12px; }
        .location-icon { width: 35px; height: 35px; border-radius: 50%; background: rgba(0,229,255,0.15); display: flex; justify-content: center; align-items: center; color: var(--neon-cyan); font-size: 16px; flex-shrink: 0;}
        .location-text { flex-grow: 1; overflow: hidden;}
        .location-text h4 { margin: 0 0 3px 0; font-size: 10px; color: #888; text-transform: uppercase; letter-spacing: 1px; }
        .location-text p { margin: 0; font-size: 15px; color: #fff; font-weight: 700; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .weather-info { text-align: right; font-size: 12px; color: var(--gold); font-weight: bold; display: flex; flex-direction: column; align-items: flex-end;}
        .weather-info i { font-size: 16px; margin-bottom: 3px; color: var(--neon-cyan);}

        .telemetrics-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 8px; margin-bottom: 12px; }
        .metric-box { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.1); border-radius: 8px; padding: 10px; text-align: center; }
        .metric-label { font-size: 9px; color: #888; text-transform: uppercase; letter-spacing: 1px; margin-bottom: 5px; }
        .metric-value { font-family: 'Orbitron', sans-serif; font-size: 13px; font-weight: 700; color: var(--neon-cyan); white-space: nowrap;}

        /* Public Client Timeline Feed - FULL SCROLLING */
        .timeline-feed { padding-left: 20px; position: relative; flex-grow: 1; overflow-y: auto; padding-right: 5px; min-height: 200px; margin-bottom: 15px; }
        .timeline-item { position: relative; margin-bottom: 20px; padding-bottom: 20px; border-bottom: 1px dashed rgba(255,255,255,0.05); animation: slideInLeft 0.4s ease forwards; display: flex; justify-content: space-between; align-items: flex-start; }
        .timeline-item:last-child { border-bottom: none; margin-bottom: 0; padding-bottom: 0; }
        .timeline-item::before { content: ''; position: absolute; left: -26px; top: 3px; width: 12px; height: 12px; border-radius: 50%; background: var(--neon-green); box-shadow: 0 0 10px var(--neon-green); border: 2px solid #111; z-index: 2; }
        .timeline-item::after { content: ''; position: absolute; left: -21px; top: 15px; width: 2px; height: 100%; background: rgba(0, 250, 154, 0.3); z-index: 1; }
        .timeline-item:last-child::after { display: none; }
        
        @keyframes slideInLeft { from { opacity: 0; transform: translateX(-20px); } to { opacity: 1; transform: translateX(0); } }

        .update-content { width: 85%; }
        .update-time { font-size: 10px; color: var(--gold); margin-bottom: 5px; font-family: 'Orbitron', sans-serif; font-weight: bold; text-transform: uppercase; letter-spacing: 1px; }
        .update-text { font-size: 14px; color: #eee; line-height: 1.5; font-weight: 500; word-wrap: break-word; }

        .quick-actions { display: flex; gap: 8px; margin-bottom: 15px; flex-wrap: wrap; }
        .quick-btn { background: rgba(255,255,255,0.05); border: 1px solid rgba(212,175,55,0.4); color: #fff; padding: 8px 12px; border-radius: 8px; font-size: 11px; cursor: pointer; transition: 0.3s; font-family: 'Orbitron', sans-serif; flex-grow: 1; text-align: center; }
        .quick-btn:hover { background: rgba(212,175,55,0.2); border-color: var(--gold); color: var(--gold); }
        
        /* Admin Target Lock Banner */
        .target-lock-banner { background: rgba(0, 229, 255, 0.1); border: 1px dashed var(--neon-cyan); padding: 12px 15px; border-radius: 8px; margin-bottom: 20px; display: flex; align-items: center; gap: 15px; flex-shrink: 0; width: 100%; max-width: 600px; }
        .target-lock-banner i { color: var(--neon-cyan); font-size: 24px; animation: targetPulse 2s infinite; }
        .target-lock-details h4 { margin: 0; color: var(--neon-cyan); font-size: 14px; font-family: 'Orbitron'; letter-spacing: 1px; text-transform: uppercase; }
        .target-lock-details p { margin: 3px 0 0 0; color: #fff; font-size: 12px; }
        @keyframes targetPulse { 0%, 100% { transform: scale(1); opacity: 1; } 50% { transform: scale(1.1); opacity: 0.5; } }

        /* =========================================================================
           ADVANCED WHATSAPP-STYLE CHAT ENGINE (MEDIA, SWIPE, VOICE)
           ========================================================================= */
        .chat-card { padding: 0 !important; overflow: hidden; display: flex; flex-direction: column; height: 75vh; max-height: 800px; border: 1px solid rgba(255,255,255,0.1); border-radius: 16px; background: var(--wa-bg) !important; margin-bottom: 20px; width: 100%; max-width: 600px; flex-shrink:0; box-shadow: 0 20px 50px rgba(0,0,0,0.8); }
        
        .chat-header { background: var(--wa-header); padding: 12px 15px; display: flex; justify-content: space-between; align-items: center; z-index: 2; box-shadow: 0 2px 10px rgba(0,0,0,0.5); }
        .chat-typing-indicator { font-size: 11px; color: var(--neon-green); font-style: italic; display: none; }
        
        .chat-area { flex-grow: 1; overflow-y: auto; padding: 20px 15px; display: flex; flex-direction: column; gap: 12px; background-color: var(--wa-bg); background-image: radial-gradient(rgba(255,255,255,0.04) 1px, transparent 1px); background-size: 20px 20px; scroll-behavior: smooth; overflow-x: hidden; }
        
        .chat-input-wrapper { display: flex; align-items: flex-end; gap: 8px; padding: 10px 15px; background: var(--wa-header); z-index: 2; flex-wrap: wrap;}
        
        .chat-tools { display: flex; gap: 8px; align-items: center; }
        .chat-tool-btn { background: transparent; border: none; color: #8696a0; font-size: 22px; cursor: pointer; transition: 0.2s; padding: 5px;}
        .chat-tool-btn:hover { color: var(--gold); }
        .file-upload-label { cursor: pointer; color: #8696a0; font-size: 22px; padding: 5px; transition: 0.2s;}
        .file-upload-label:hover { color: var(--neon-cyan); }
        input[type="file"] { display: none; }

        .chat-input-pill { flex-grow: 1; background: #2a3942; border: none; padding: 14px 20px; border-radius: 25px; color: var(--wa-text); font-size: 15px; outline: none; font-family: 'Outfit', sans-serif; box-shadow: inset 0 2px 5px rgba(0,0,0,0.2); min-width: 150px; }
        .chat-input-pill::placeholder { color: #8696a0; }
        
        .chat-send-btn, .chat-mic-btn { width: 48px; height: 48px; border-radius: 50%; border: none; background: #00a884; color: #fff; font-size: 18px; display: flex; justify-content: center; align-items: center; cursor: pointer; transition: 0.2s; flex-shrink: 0; box-shadow: 0 4px 10px rgba(0,168,132,0.3); }
        .chat-send-btn:active, .chat-mic-btn:active { transform: scale(0.9); }
        .chat-mic-btn.recording { background: var(--danger); animation: pulseRecord 1s infinite; }
        @keyframes pulseRecord { 0% { box-shadow: 0 0 0 0 rgba(255,0,0,0.5); } 70% { box-shadow: 0 0 0 15px rgba(255,0,0,0); } 100% { box-shadow: 0 0 0 0 rgba(255,0,0,0); } }
        
        /* Interactive Chat Bubble for Swipe */
        .chat-bubble-container { display: flex; width: 100%; position: relative; transition: transform 0.2s ease-out; }
        .chat-bubble-container.sent { justify-content: flex-end; }
        .chat-bubble-container.received { justify-content: flex-start; }
        .swipe-reply-icon { position: absolute; top: 50%; transform: translateY(-50%); width: 30px; height: 30px; border-radius: 50%; background: rgba(255,255,255,0.1); display: flex; justify-content: center; align-items: center; color: #fff; opacity: 0; transition: opacity 0.2s; z-index: -1; }
        .chat-bubble-container.sent .swipe-reply-icon { left: -40px; }
        .chat-bubble-container.received .swipe-reply-icon { right: -40px; }

        .chat-bubble { max-width: 82%; padding: 8px 10px 8px 14px; border-radius: 12px; position: relative; font-size: 15px; line-height: 1.4; color: var(--wa-text); display: inline-block; word-wrap: break-word; box-shadow: 0 1px 2px rgba(0,0,0,0.3); cursor: pointer; transition: 0.2s; z-index: 2; background: var(--wa-received); }
        .chat-bubble:active { filter: brightness(1.2); }
        
        .chat-bubble-container.sent .chat-bubble { background: var(--wa-sent); border-top-right-radius: 0; }
        .chat-bubble-container.received .chat-bubble { background: var(--wa-received); border-top-left-radius: 0; }
        
        /* Media Rendering inside chat */
        .chat-media-img { max-width: 100%; border-radius: 8px; margin-bottom: 5px; cursor: pointer; max-height: 250px; object-fit: cover; border: 1px solid rgba(255,255,255,0.1);}
        .chat-media-video { max-width: 100%; border-radius: 8px; margin-bottom: 5px; max-height: 250px; background:#000;}
        .chat-media-audio { width: 220px; height: 40px; margin-bottom: 5px; outline: none; }
        .chat-media-audio::-webkit-media-controls-panel { background-color: rgba(255,255,255,0.1); }
        .chat-media-file { display: flex; align-items: center; gap: 10px; background: rgba(0,0,0,0.2); padding: 10px; border-radius: 8px; margin-bottom: 5px; text-decoration: none; color: #fff;}
        .chat-media-file i { font-size: 24px; color: var(--gold); }
        .chat-media-file span { font-size: 13px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; max-width: 150px; }

        .chat-info-row { display: flex; justify-content: flex-end; align-items: center; gap: 5px; margin-top: 2px; }
        .chat-time { font-size: 10px; color: var(--wa-time); font-family: 'Outfit'; margin-top: 3px;}
        .chat-ticks { font-size: 12px; color: var(--wa-time); }
        .chat-ticks.read { color: var(--wa-blue-tick); }
        
        .chat-reply-context { background: rgba(0,0,0,0.25); border-left: 4px solid var(--neon-green); padding: 6px 10px; border-radius: 6px; font-size: 13px; color: #ccc; margin-bottom: 6px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
        .chat-reply-context .sender { color: var(--neon-green); font-weight: 700; margin-bottom: 2px; font-size: 12px; }
        .chat-bubble-container.received .chat-reply-context { border-left-color: var(--neon-cyan); }
        .chat-bubble-container.received .chat-reply-context .sender { color: var(--neon-cyan); }
        
        .reply-banner { display: none; background: #202c33; padding: 10px 15px; align-items: center; border-bottom: 1px solid rgba(255,255,255,0.05); }
        .reply-banner-content { flex-grow: 1; border-left: 4px solid var(--neon-green); padding-left: 12px; background: rgba(0,0,0,0.2); border-radius: 4px 8px 8px 4px; padding-top: 6px; padding-bottom: 6px; }
        .reply-banner-content .rep-title { font-size: 12px; color: var(--neon-green); font-weight: bold; margin-bottom: 3px; }
        .replying-to-text { color: #ccc; font-size: 13px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; max-width: 90%; }
        .cancel-reply-btn { background: transparent; border: none; color: #8696a0; font-size: 20px; cursor: pointer; padding: 5px 10px; }

        /* Media Preview Strip before sending */
        .media-preview-strip { display: none; background: #202c33; padding: 10px 15px; gap: 10px; overflow-x: auto; border-bottom: 1px solid rgba(255,255,255,0.05); }
        .preview-item { position: relative; width: 60px; height: 60px; border-radius: 8px; border: 1px solid var(--gold); overflow: hidden; flex-shrink: 0;}
        .preview-item img { width: 100%; height: 100%; object-fit: cover; }
        .preview-remove { position: absolute; top: 2px; right: 2px; background: rgba(0,0,0,0.7); color: #fff; border: none; border-radius: 50%; width: 20px; height: 20px; font-size: 10px; cursor: pointer; }

        .chat-actions-menu { display: none; background: rgba(32,44,51,0.98); border: 1px solid rgba(255,255,255,0.1); border-radius: 12px; position: absolute; z-index: 10; padding: 5px; flex-direction: row; gap: 5px; box-shadow: 0 10px 25px rgba(0,0,0,0.9); top: 100%; right: 0; margin-top: 5px; backdrop-filter: blur(10px); }
        .chat-bubble-container.show-actions .chat-actions-menu { display: flex; animation: dropDown 0.2s ease forwards; }
        .chat-bubble-container.received .chat-actions-menu { right: auto; left: 0; } 
        
        .chat-action-btn { background: transparent; border: none; color: #e9edef; font-size: 16px; padding: 10px 14px; cursor: pointer; border-radius: 8px; transition: 0.2s; display: flex; align-items: center; justify-content: center; }
        .chat-action-btn:hover { background: rgba(255,255,255,0.1); color: #fff; }
        .chat-action-btn.delete { color: rgba(255,51,51,0.8); }
        .chat-action-btn.delete:hover { color: var(--danger); background: rgba(255,51,51,0.15); }

        /* Full Screen Image Modal */
        #image-modal { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.95); z-index: 10000000; justify-content: center; align-items: center; flex-direction: column; }
        #image-modal img { max-width: 95%; max-height: 85vh; border-radius: 8px; box-shadow: 0 0 30px rgba(255,255,255,0.1); }
        .close-modal-btn { position: absolute; top: 20px; right: 20px; background: rgba(255,255,255,0.1); border: none; color: #fff; font-size: 24px; width: 40px; height: 40px; border-radius: 50%; cursor: pointer; transition: 0.2s; }
        .close-modal-btn:hover { background: var(--danger); }
        .download-modal-btn { margin-top: 20px; }

    </style>
</head>
<body onclick="closeAllMenus()"> 

    <div class="bg-map"></div>
    <div class="grid-overlay"></div>
    <div id="toast-container"></div>

    <!-- Full Screen Image Modal -->
    <div id="image-modal">
        <button class="close-modal-btn" onclick="closeImageModal()"><i class="fas fa-times"></i></button>
        <img id="modal-img-src" src="" alt="Preview">
        <a id="modal-download-btn" href="#" download="MND_Logistics_Image.jpg" class="mn-btn btn-gold download-modal-btn" style="width: auto; padding: 10px 30px;"><i class="fas fa-download"></i> SAVE TO DEVICE</a>
    </div>

    <!-- ========================================================================= -->
    <!-- VIEW 1: GATEKEEPER -->
    <!-- ========================================================================= -->
    <div id="view-gatekeeper" class="view-container active-view" style="justify-content: center;">
        <div class="gatekeeper-box" style="background: rgba(15,15,20,0.85); position:relative; overflow:hidden; border-radius:20px; padding:40px 25px; box-shadow: 0 20px 60px rgba(0,0,0,0.9); border:2px solid var(--gold); max-width:450px; backdrop-filter: blur(10px);">
            <h1 class="app-title"><i class="fas fa-satellite-dish"></i> MND TRACKING</h1>
            <p class="app-subtitle">Premium Encrypted Cloud Logistics.<br>Enter Mobile Number & Secure PIN to commence sync.</p>
            
            <div class="input-group">
                <i class="fas fa-phone-alt"></i>
                <input type="tel" id="login-phone" class="mn-input" placeholder="Registered Mobile Number *" autocomplete="off">
            </div>
            
            <div class="input-group">
                <input type="password" id="login-pin" class="mn-input pin-style" placeholder="6 DIGIT PIN" maxlength="6" autocomplete="off">
            </div>

            <button class="mn-btn btn-gold" id="login-btn" onclick="processLogin(event)"><i class="fas fa-fingerprint"></i> INITIATE HANDSHAKE</button>
            <p id="login-error" style="display:none; color:var(--danger); font-size:13px; font-weight:bold; margin-top:15px; text-align: center;"></p>
        </div>
    </div>

    <!-- ========================================================================= -->
    <!-- VIEW 2: ADMIN DISPATCH CENTER (MAIN) -->
    <!-- ========================================================================= -->
    <div id="view-admin" class="view-container">
        <div class="dash-header">
            <div>
                <h2 style="color:var(--gold); font-family:'Cinzel'; margin:0; font-size:22px;">Command Center Pro</h2>
                <span style="font-size:12px; color:#888;">Level 9 Authorization Active</span>
            </div>
            <div class="status-badge"><i class="fas fa-link"></i> DB SYNCED</div>
        </div>

        <div class="card">
            <h3><i class="fas fa-key"></i> Provision / Update Client</h3>
            <p style="font-size:11px; color:#aaa; margin-bottom:15px;">Assigning to an existing number retains history & chat logs automatically.</p>
            
            <div style="display:flex; gap:10px;">
                <input type="text" id="admin-c-name" class="mn-input" style="padding:12px; margin-bottom:12px;" placeholder="Client Name *">
                <input type="tel" id="admin-c-phone" class="mn-input" style="padding:12px; margin-bottom:12px;" placeholder="Mobile No. *">
            </div>
            
            <div style="display:flex; gap:10px;">
                <input type="text" id="admin-c-event" class="mn-input" style="flex:2; padding:12px; margin-bottom:15px;" placeholder="Event Ref / Location *">
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
                <button class="quick-btn" style="width: auto; padding: 4px 10px;" onclick="exportAllLogs()"><i class="fas fa-download"></i> EXPORT LOGS</button>
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

        <div class="target-lock-banner" id="admin-lock-banner">
            <i class="fas fa-crosshairs" id="admin-crosshair-icon"></i>
            <div class="target-lock-details">
                <h4 id="admin-lock-title">Active Target Locked</h4>
                <p><span id="session-c-name" style="font-weight:bold;">Name</span> | <span id="session-c-event">Event</span> | <span id="session-c-phone" style="color:var(--gold);">Phone</span></p>
            </div>
            <div id="admin-sos-alert" style="display:none; margin-left:auto; color:var(--danger); font-size: 24px; animation: blink 0.5s infinite;"><i class="fas fa-exclamation-triangle"></i></div>
        </div>

        <!-- 📍 Client Pinned Location -->
        <div class="card" id="admin-client-loc-card" style="display:none; border-color:var(--neon-green); background:rgba(0,250,154,0.05);">
            <h3 style="color:var(--neon-green); border-bottom-color:rgba(0,250,154,0.3);"><i class="fas fa-street-view"></i> Target Event Location</h3>
            <p style="font-size:12px; color:#aaa; margin-bottom:15px;">The client has shared their exact event coordinates.</p>
            <div style="display:flex; gap: 10px;">
                <a id="admin-view-client-map" href="#" target="_blank" class="mn-btn btn-green" style="font-size:12px; padding:12px; text-decoration:none;"><i class="fas fa-map-marker-alt"></i> OPEN IN MAPS</a>
                <button class="mn-btn" style="background:rgba(255,255,255,0.1); color:#fff; font-size: 12px; padding:12px; border:1px solid var(--gold);" onclick="calculateETA()"><i class="fas fa-route"></i> GET ETA</button>
            </div>
            <p id="eta-display" style="font-size:12px; color:var(--gold); margin-top: 10px; display:none; font-family: 'Orbitron';"></p>
        </div>

        <!-- Session: GPS Telemetry & Live Map -->
        <div class="card">
            <h3 style="color:var(--neon-cyan); border-bottom-color:rgba(0, 229, 255, 0.3);"><i class="fas fa-satellite"></i> Direct GPS Uplink & Telemetry</h3>
            
            <div class="telemetrics-grid" id="admin-telemetrics" style="display:none;">
                <div class="metric-box"><div class="metric-label">Speed</div><div class="metric-value" id="a-speed">0 km/h</div></div>
                <div class="metric-box"><div class="metric-label">Accuracy</div><div class="metric-value" id="a-acc">0 m</div></div>
                <div class="metric-box"><div class="metric-label">Battery</div><div class="metric-value" id="a-batt" style="color:#00FA9A;">--%</div></div>
                <div class="metric-box"><div class="metric-label">Pings</div><div class="metric-value" id="a-ping">0</div></div>
            </div>
            
            <div id="admin-ai-location" class="location-display" style="display:none; border-color:var(--neon-cyan); background:rgba(0,229,255,0.05);">
                <div class="location-icon" style="color:var(--neon-cyan); background:rgba(0,229,255,0.1);"><i class="fas fa-map-marked-alt"></i></div>
                <div class="location-text">
                    <h4>Current Area Detected</h4>
                    <p id="a-loc-name">Resolving coordinates...</p>
                </div>
                <div class="weather-info" id="a-weather" style="display:none;">
                    <i class="fas fa-cloud-sun"></i>
                    <span id="a-weather-temp">--°C</span>
                </div>
            </div>

            <!-- Premium Leaflet Map -->
            <div class="map-wrapper" id="admin-map-wrapper" style="display:none;">
                <div id="admin-map"></div>
            </div>

            <button class="mn-btn btn-dark" style="border-color:var(--neon-cyan); color:var(--neon-cyan);" id="btn-broadcast" onclick="toggleLocationBroadcast(event)"><i class="fas fa-location-arrow"></i> INITIATE BROADCAST TO TARGET</button>
        </div>

        <!-- Session: Push Update (Public Timeline) -->
        <div class="card">
            <h3 style="color:var(--neon-cyan); border-bottom-color:rgba(0, 229, 255, 0.3);"><i class="fas fa-bullhorn"></i> Public Logistics Feed</h3>
            
            <div class="quick-actions">
                <button class="quick-btn" onclick="setQuickUpdate('🚚 Premium Fleet Dispatched. GPS Active.', event)"><i class="fas fa-truck-moving"></i> Dispatched</button>
                <button class="quick-btn" onclick="setQuickUpdate('📍 Fleet Arrived at coordinates. Unloading.', event)"><i class="fas fa-map-marker-alt"></i> Arrived</button>
                <button class="quick-btn" onclick="setQuickUpdate('⚙️ Stage, Audio & Lighting setup initiated.', event)"><i class="fas fa-tools"></i> Setup</button>
                <button class="quick-btn" style="border-color:rgba(255,51,51,0.5); color:#ff6b6b;" onclick="setQuickUpdate('🛑 Minor transit delay. Route recalculating.', event)"><i class="fas fa-traffic-light"></i> Delay</button>
            </div>

            <textarea id="admin-update-text" class="mn-input" rows="2" style="resize:none; padding:15px; flex-shrink:0;" placeholder="Push an official update to the client's public timeline..."></textarea>
            <button class="mn-btn btn-gold" style="margin-top:12px; flex-shrink:0; background: linear-gradient(135deg, var(--neon-cyan) 0%, #0088cc 100%); box-shadow: 0 5px 25px rgba(0,229,255,0.3);" id="btn-push-update" onclick="adminPushUpdate(event)"><i class="fas fa-paper-plane"></i> TRANSMIT TO TIMELINE</button>
            
            <div style="border-top: 1px dashed rgba(255,255,255,0.1); padding-top:15px; margin-top: 15px;">
                <h4 style="font-size:12px; color:#aaa; margin-bottom:10px; text-transform:uppercase; flex-shrink:0;">Manage Sent Updates:</h4>
                <div id="admin-manage-updates-area" class="timeline-feed" style="max-height: 250px;">
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
                        <div style="font-size:11px; color:var(--neon-green);" id="admin-typing-status">Online</div>
                    </div>
                </div>
                <div style="display: flex; flex-direction: column; align-items: flex-end;">
                    <span style="font-size:10px; color:var(--neon-cyan); border:1px solid var(--neon-cyan); padding:3px 8px; border-radius:12px; margin-bottom:4px;"><i class="fas fa-lock"></i> E2E Encrypted</span>
                </div>
            </div>
            
            <div id="admin-chat-area" class="chat-area">
                <div style="font-size:12px; color:rgba(255,255,255,0.5); text-align:center; margin: auto; background:rgba(0,0,0,0.4); padding:6px 12px; border-radius:12px;">End-to-end encrypted chat started. Swipe bubbles right to reply.</div>
            </div>

            <!-- Media Preview Strip -->
            <div id="admin-media-preview" class="media-preview-strip"></div>

            <div id="admin-reply-banner" class="reply-banner">
                <div class="reply-banner-content">
                    <div class="rep-title" id="admin-reply-sender">Client Name</div>
                    <div class="replying-to-text" id="admin-reply-text">Replying to...</div>
                </div>
                <button class="cancel-reply-btn" onclick="cancelReply('admin', event)"><i class="fas fa-times"></i></button>
            </div>

            <div class="chat-input-wrapper">
                <div class="chat-tools">
                    <label class="file-upload-label" title="Attach Media">
                        <i class="fas fa-paperclip"></i>
                        <input type="file" id="admin-file-input" accept="image/*,video/*,audio/*,.pdf,.doc,.docx" onchange="handleFileSelect(event, 'admin')">
                    </label>
                </div>
                <input type="text" id="admin-chat-input" class="chat-input-pill" placeholder="Message Client..." oninput="handleTyping('admin')" onkeypress="handleEnter(event, 'admin')">
                <button class="chat-mic-btn" id="admin-mic-btn" onmousedown="startRecording('admin')" onmouseup="stopRecording('admin')" onmouseleave="stopRecording('admin')" ontouchstart="startRecording('admin')" ontouchend="stopRecording('admin')"><i class="fas fa-microphone"></i></button>
                <button class="chat-send-btn" style="display:none;" id="admin-send-btn" onclick="sendChatMessage('admin', event)"><i class="fas fa-paper-plane"></i></button>
            </div>
        </div>
    </div>

    <!-- ========================================================================= -->
    <!-- VIEW 4: CLIENT LIVE DASHBOARD -->
    <!-- ========================================================================= -->
    <div id="view-client" class="view-container" style="padding: 20px 10px;">
        <div class="dash-header" style="max-width:100%; width:100%;">
            <div style="width:65%;">
                <h2 id="client-dash-event" style="color:var(--gold); font-family:'Cinzel'; margin:0; font-size:20px; white-space:nowrap; overflow:hidden; text-overflow:ellipsis;">Loading Event...</h2>
                <span style="font-size:12px; color:#888;">Authorized: <span id="client-dash-name" style="color:#fff;">Loading...</span></span>
            </div>
            <div style="display:flex; flex-direction:column; align-items:flex-end; gap:5px;">
                <div class="status-badge" id="client-conn-status"><i class="fas fa-circle fa-beat"></i> ONLINE</div>
                <span id="client-battery-display" style="font-size:10px; color:#aaa; font-family:'Orbitron';"><i class="fas fa-battery-half"></i> --%</span>
            </div>
        </div>

        <!-- SOS PANIC BUTTON -->
        <button class="mn-btn btn-sos" style="max-width:100%; width:100%; margin-bottom:15px; font-size: 16px; padding: 18px;" onclick="triggerSOS(event)" id="btn-client-sos"><i class="fas fa-exclamation-triangle"></i> EMERGENCY SOS</button>

        <!-- 📍 Client Send Location to HQ -->
        <div class="card" style="max-width:100%; width:100%; padding: 15px; background:rgba(0, 250, 154, 0.05); border-color:var(--neon-green); margin-bottom:15px; flex-shrink:0;">
            <div style="display:flex; justify-content:space-between; align-items:center;">
                <div>
                    <h4 style="color:var(--neon-green); margin:0; font-size:14px;"><i class="fas fa-street-view"></i> Event Location</h4>
                    <p style="font-size:11px; color:#aaa; margin:0;">Share exact event coordinates with HQ.</p>
                </div>
                <button class="mn-btn btn-green" style="width:auto; padding: 10px 15px; font-size:12px;" onclick="clientShareLocation(event)"><i class="fas fa-share-location"></i> PIN DROP</button>
            </div>
        </div>

        <!-- Premium Map & Telemetrics Area -->
        <div class="card" style="max-width:100%; width:100%; padding: 15px; flex-shrink:0; margin-bottom:15px;">
            <div style="display:flex; justify-content:space-between; align-items:center; padding-bottom: 12px; border-bottom: 1px dashed rgba(255,255,255,0.1); margin-bottom: 10px;">
                <h3 style="margin:0; border:none; padding:0; font-size:16px;"><i class="fas fa-crosshairs"></i> Live Target Tracking</h3>
                <span id="client-gps-time" style="font-size:11px; color:#888; font-family:'Orbitron';">Awaiting Signal...</span>
            </div>
            
            <div class="telemetrics-grid">
                <div class="metric-box"><div class="metric-label">Speed</div><div class="metric-value" id="c-speed">--</div></div>
                <div class="metric-box"><div class="metric-label">Heading</div><div class="metric-value" id="c-heading">--</div></div>
                <div class="metric-box"><div class="metric-label">Signal</div><div class="metric-value" id="c-accuracy" style="color:var(--gold);">--</div></div>
                <div class="metric-box"><div class="metric-label">Pings</div><div class="metric-value" id="c-pings" style="color:var(--neon-green);">0</div></div>
            </div>

            <!-- Premium Leaflet Map -->
            <div class="map-wrapper" id="client-map-wrapper">
                <div class="map-loader" id="map-loader-text"><i class="fas fa-satellite-dish fa-spin fa-2x" style="margin-bottom:10px;"></i><br>SEARCHING SATELLITE...</div>
                <div id="client-map" style="opacity: 0; transition: opacity 1s;"></div>
            </div>

            <!-- AI Location Appears Below Map -->
            <div class="location-display" id="client-location-display" style="display:none;">
                <div class="location-icon"><i class="fas fa-map-marker-alt"></i></div>
                <div class="location-text">
                    <h4>Nearest Detected Area</h4>
                    <p id="c-location-name">Syncing AI Location...</p>
                </div>
                <div class="weather-info" id="c-weather" style="display:none;">
                    <i class="fas fa-cloud-sun"></i>
                    <span id="c-weather-temp">--°C</span>
                </div>
            </div>
            
            <a id="ext-map-link" href="#" target="_blank" class="mn-btn btn-green" style="font-size:13px; padding:14px; text-decoration:none; display:none;"><i class="fas fa-external-link-alt"></i> OPEN IN GOOGLE MAPS</a>
        </div>

        <!-- Premium Timeline Updates Area -->
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
                        <div style="font-size:11px; color:var(--neon-green);" id="client-typing-status">Online</div>
                    </div>
                </div>
                <span style="font-size:10px; color:var(--gold); border:1px solid var(--gold); padding:3px 8px; border-radius:12px;"><i class="fas fa-lock"></i> E2E Encrypted</span>
            </div>
            
            <div id="client-chat-area" class="chat-area">
                <div style="font-size:12px; color:rgba(255,255,255,0.5); text-align:center; margin: auto; background:rgba(0,0,0,0.4); padding:6px 12px; border-radius:12px;">End-to-end encrypted chat started. Swipe bubbles right to reply.</div>
            </div>

            <!-- Media Preview Strip -->
            <div id="client-media-preview" class="media-preview-strip"></div>

            <div id="client-reply-banner" class="reply-banner">
                <div class="reply-banner-content">
                    <div class="rep-title" id="client-reply-sender">HQ Dispatch</div>
                    <div class="replying-to-text" id="client-reply-text">Replying to...</div>
                </div>
                <button class="cancel-reply-btn" onclick="cancelReply('client', event)"><i class="fas fa-times"></i></button>
            </div>

            <div class="chat-input-wrapper">
                <div class="chat-tools">
                    <label class="file-upload-label" title="Attach Media">
                        <i class="fas fa-paperclip"></i>
                        <input type="file" id="client-file-input" accept="image/*,video/*,audio/*,.pdf,.doc,.docx" onchange="handleFileSelect(event, 'client')">
                    </label>
                </div>
                <input type="text" id="client-chat-input" class="chat-input-pill" placeholder="Message HQ..." oninput="handleTyping('client')" onkeypress="handleEnter(event, 'client')">
                <button class="chat-mic-btn" id="client-mic-btn" onmousedown="startRecording('client')" onmouseup="stopRecording('client')" onmouseleave="stopRecording('client')" ontouchstart="startRecording('client')" ontouchend="stopRecording('client')"><i class="fas fa-microphone"></i></button>
                <button class="chat-send-btn" style="display:none;" id="client-send-btn" onclick="sendChatMessage('client', event)"><i class="fas fa-paper-plane"></i></button>
            </div>
        </div>

        <button class="mn-btn btn-danger" style="max-width:100%; width:100%; flex-shrink:0;" onclick="systemLogout(event)"><i class="fas fa-power-off"></i> DISCONNECT TERMINAL</button>
    </div>

    <!-- ========================================================================= -->
    <!-- JAVASCRIPT & FIREBASE ENGINE (PREMIUM ADVANCED) -->
    <!-- ========================================================================= -->
    <script>
        // --- Global Helpers & Click Listeners ---
        function closeAllMenus() {
            document.querySelectorAll('.chat-bubble-container').forEach(b => b.classList.remove('show-actions'));
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

        function playPremiumChime(type = 'default') {
            try {
                const ctx = new (window.AudioContext || window.webkitAudioContext)();
                const osc = ctx.createOscillator(); const gain = ctx.createGain();
                
                if(type === 'sos') {
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(1000, ctx.currentTime); 
                    osc.frequency.linearRampToValueAtTime(500, ctx.currentTime + 0.5);
                    osc.frequency.linearRampToValueAtTime(1000, ctx.currentTime + 1);
                    gain.gain.setValueAtTime(1, ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 2);
                } else {
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(880, ctx.currentTime); 
                    osc.frequency.exponentialRampToValueAtTime(440, ctx.currentTime + 0.5); 
                    gain.gain.setValueAtTime(0.4, ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 1.5);
                }
                
                osc.connect(gain); gain.connect(ctx.destination);
                osc.start(); osc.stop(ctx.currentTime + (type==='sos'? 2 : 1.5));
            } catch(e) {}
        }

        function showPushNotification(title, body, iconType = 'default') {
            if(iconType === 'sos') playPremiumChime('sos'); else playPremiumChime();
            
            if ("Notification" in window && Notification.permission === "granted" && 'serviceWorker' in navigator) {
                navigator.serviceWorker.ready.then(function(registration) {
                    registration.showNotification(title, {
                        body: body, icon: "https://cdn-icons-png.flaticon.com/512/814/814513.png", vibrate: iconType === 'sos' ? [500,200,500] : [200, 100, 200], tag: "mnd-logistics", renotify: true
                    });
                });
            } else if ("Notification" in window && Notification.permission === "granted") {
                new Notification(title, { body: body, icon: "https://cdn-icons-png.flaticon.com/512/814/814513.png" });
            }

            const container = document.getElementById('toast-container');
            const push = document.createElement('div');
            push.className = 'native-push';
            if(iconType === 'sos') push.style.boxShadow = '0 0 30px rgba(255,0,68,0.6)';
            
            push.innerHTML = `
                <div class="native-push-icon" style="${iconType==='sos' ? 'background:var(--danger); color:#fff;' : ''}"><i class="fas ${iconType==='sos' ? 'fa-exclamation-triangle' : 'fa-bell'}"></i></div>
                <div class="native-push-content">
                    <div class="native-push-title" style="${iconType==='sos' ? 'color:var(--danger);' : ''}">${title} <span>Just Now</span></div>
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

        // --- 2. FIREBASE CONFIGURATION (Database & Storage) ---
        const firebaseConfig = {
            apiKey: "AIzaSyCZP-zuJNDW9S4sD_d4R_-nrTMjf0HD4MM",
            authDomain: "mnd-tracking.firebaseapp.com",
            databaseURL: "https://mnd-tracking-default-rtdb.asia-southeast1.firebasedatabase.app",
            projectId: "mnd-tracking",
            storageBucket: "mnd-tracking.firebasestorage.app", // Added for Storage
            messagingSenderId: "794366177184",
            appId: "1:794366177184:web:3f394f0207215ccca0fec5"
        };
        
        if (!firebase.apps.length) { firebase.initializeApp(firebaseConfig); }
        const db = firebase.database();
        const storage = firebase.storage ? firebase.storage() : null;

        // --- 3. SYSTEM STATE & CONFIG ---
        const ADMIN_NUMBERS = ["9771617808", "9153635378", "7294969938", "+918544341240", "8544341240"];
        const MASTER_PIN = "121120";
        
        let geoWatchId = null; 
        let activeClientData = null; 
        let pingCount = 0;
        let cPings = 0; // Client side pings
        
        let currentClientPhone = ""; 
        let currentClientName = "Client";
        let currentAdminTargetPhone = ""; 

        // Memory for Push Notifications & Logic
        let processedUpdateKeys = new Set();
        let processedChatKeys = new Set();
        let isInitialLoad = true;
        let isChatInitialLoad = true;
        let typingTimeout = null;

        // Chat Reply & Media Context Memory
        let replyContext = { admin: null, client: null };
        let pendingMedia = { admin: null, client: null }; // Stores Base64 string of file to send
        
        // Voice Recording Vars
        let mediaRecorder;
        let audioChunks = [];
        let isRecording = false;

        // Map Vars
        let adminMap, clientMap;
        let adminMarker, clientMarker;
        let adminPolyline, clientPolyline;
        let adminPathLatlngs = [], clientPathLatlngs = [];
        let lastKnownTargetLoc = null; // for ETA calculation

        // --- 4. VIEW ROUTER ---
        function switchView(viewId) {
            document.querySelectorAll('.view-container').forEach(el => el.classList.remove('active-view'));
            document.getElementById(viewId).classList.add('active-view');
            document.querySelectorAll('.error-msg').forEach(el => el.style.display = 'none');
            window.scrollTo(0,0);
            
            // Fix Leaflet sizing issue when switching tabs
            setTimeout(() => {
                if(viewId === 'view-admin-session' && adminMap) adminMap.invalidateSize();
                if(viewId === 'view-client' && clientMap) clientMap.invalidateSize();
            }, 300);
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
                        
                        // Device Telemetry Sync
                        updateBatteryStatus('client');

                        initClientMap(); // Init Leaflet Map
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

        // --- Telemetry Additions (Battery & Weather) ---
        function updateBatteryStatus(role) {
            if(navigator.getBattery) {
                navigator.getBattery().then(battery => {
                    const level = Math.round(battery.level * 100);
                    const isCharging = battery.charging;
                    const bIcon = isCharging ? 'fa-battery-charging' : (level > 50 ? 'fa-battery-full' : (level > 20 ? 'fa-battery-half' : 'fa-battery-quarter'));
                    const bColor = level > 20 ? 'var(--neon-green)' : 'var(--danger)';
                    
                    if(role === 'client') {
                        document.getElementById('client-battery-display').innerHTML = `<i class="fas ${bIcon}" style="color:${bColor}"></i> ${level}% ${isCharging? '⚡':''}`;
                        if(currentClientPhone) db.ref(`trackings/${currentClientPhone}/telemetry/battery`).set({ level, isCharging });
                    }
                });
            }
        }
        
        async function fetchWeather(lat, lng, elementId) {
            try {
                // Using free Open-Meteo API
                const res = await fetch(`https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lng}&current_weather=true`);
                const data = await res.json();
                if(data && data.current_weather) {
                    const temp = data.current_weather.temperature;
                    const code = data.current_weather.weathercode;
                    let icon = 'fa-cloud';
                    if(code === 0) icon = 'fa-sun';
                    else if(code <= 3) icon = 'fa-cloud-sun';
                    else if(code <= 67) icon = 'fa-cloud-rain';
                    else if(code <= 77) icon = 'fa-snowflake';
                    else if(code <= 99) icon = 'fa-bolt';
                    
                    document.getElementById(elementId).innerText = `${temp}°C`;
                    document.getElementById(elementId).previousElementSibling.className = `fas ${icon}`;
                    document.getElementById(elementId).parentElement.style.display = 'flex';
                }
            } catch(e) { console.warn("Weather sync failed"); }
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

        // --- Export Logs Tool ---
        function exportAllLogs() {
            db.ref('trackings').once('value').then(snap => {
                let text = "MND LOGISTICS REPORT - " + new Date().toLocaleString() + "\n\n";
                snap.forEach(child => {
                    let c = child.val();
                    text += `CLIENT: ${c.name} | PHONE: ${c.phone} | EVENT: ${c.event} | DATE: ${c.dateStr}\n`;
                    if(c.updates) { text += `  UPDATES:\n`; Object.values(c.updates).forEach(u => text += `   - [${u.time}] ${u.text}\n`); }
                    text += "----------------------------------------\n";
                });
                
                const blob = new Blob([text], { type: "text/plain" });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url; a.download = `MND_Export_${Date.now()}.txt`;
                a.click();
                URL.revokeObjectURL(url);
                showToast("Logs Exported Successfully");
            });
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
                        let sosHtml = data.sos ? `<div class="sos-indicator"><i class="fas fa-exclamation"></i></div>` : '';
                        let sosGlow = data.sos ? `box-shadow: 0 0 15px rgba(255,0,0,0.5); border-left-color: var(--danger);` : '';

                        html += `
                            <div class="client-list-item" style="${sosGlow}" onclick="openAdminSession('${data.phone}', '${data.name.replace(/'/g, "\\'")}', '${(data.event || 'Logistics Event').replace(/'/g, "\\'")}')">
                                ${sosHtml}
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

        // --- MAP INITIALIZATION (LEAFLET) ---
        function initAdminMap() {
            if(adminMap) { adminMap.remove(); adminPathLatlngs=[]; }
            adminMap = L.map('admin-map', {zoomControl: false}).setView([20.5937, 78.9629], 5); // India center default
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', { maxZoom: 19 }).addTo(adminMap);
            
            var goldIcon = new L.Icon({
                iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-gold.png',
                shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/0.7.7/images/marker-shadow.png',
                iconSize: [25, 41], iconAnchor: [12, 41], popupAnchor: [1, -34], shadowSize: [41, 41]
            });
            adminMarker = L.marker([20.5937, 78.9629], {icon: goldIcon}).addTo(adminMap);
            adminPolyline = L.polyline([], {color: '#00E5FF', weight: 4, opacity: 0.7, dashArray: '5, 10'}).addTo(adminMap);
        }

        function initClientMap() {
            if(clientMap) { clientMap.remove(); clientPathLatlngs=[]; }
            clientMap = L.map('client-map', {zoomControl: false}).setView([20.5937, 78.9629], 5);
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', { maxZoom: 19 }).addTo(clientMap);
            
            var greenIcon = new L.Icon({
                iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-green.png',
                shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/0.7.7/images/marker-shadow.png',
                iconSize: [25, 41], iconAnchor: [12, 41], popupAnchor: [1, -34], shadowSize: [41, 41]
            });
            clientMarker = L.marker([20.5937, 78.9629], {icon: greenIcon}).addTo(clientMap);
            clientPolyline = L.polyline([], {color: '#00FA9A', weight: 4, opacity: 0.7}).addTo(clientMap);
        }

        function calculateETA() {
            if(!lastKnownTargetLoc || adminPathLatlngs.length === 0) { showToast("Need more data points for ETA", "error"); return; }
            const currentLoc = adminPathLatlngs[adminPathLatlngs.length-1];
            const dist = adminMap.distance(currentLoc, lastKnownTargetLoc) / 1000; // km
            
            // Assume avg speed of 40km/h if static, else use latest speed (if available, defaulting to 40)
            const speedText = document.getElementById('a-speed').innerText;
            let speed = parseFloat(speedText);
            if(isNaN(speed) || speed < 5) speed = 40; 
            
            const hours = dist / speed;
            const mins = Math.round(hours * 60);
            
            const disp = document.getElementById('eta-display');
            disp.innerText = `DISTANCE: ${dist.toFixed(1)} KM | EST. TIME: ${mins} MINS`;
            disp.style.display = 'block';
        }

        function openAdminSession(phone, name, eventName) {
            currentAdminTargetPhone = phone;
            document.getElementById('session-c-name').innerText = name;
            document.getElementById('session-c-event').innerText = eventName || "Event Logistics";
            document.getElementById('session-c-phone').innerText = phone;
            document.getElementById('admin-chat-header-name').innerText = name; 
            
            switchView('view-admin-session');
            showToast(`Target Locked: ${name}`, 'success');
            initAdminMap();

            processedChatKeys.clear();
            isChatInitialLoad = true;
            cancelReply('admin');

            // Listen for SOS
            db.ref(`trackings/${currentAdminTargetPhone}/sos`).on('value', snap => {
                const isSOS = snap.val();
                const banner = document.getElementById('admin-lock-banner');
                const icon = document.getElementById('admin-crosshair-icon');
                const alert = document.getElementById('admin-sos-alert');
                const title = document.getElementById('admin-lock-title');
                
                if(isSOS) {
                    banner.style.borderColor = 'var(--danger)'; banner.style.background = 'rgba(255,0,0,0.1)';
                    icon.style.color = 'var(--danger)'; alert.style.display = 'block';
                    title.innerText = "EMERGENCY SOS ACTIVE"; title.style.color = 'var(--danger)';
                    showPushNotification("SOS EMERGENCY", `${name} triggered the panic button!`, 'sos');
                } else {
                    banner.style.borderColor = 'var(--neon-cyan)'; banner.style.background = 'rgba(0, 229, 255, 0.1)';
                    icon.style.color = 'var(--neon-cyan)'; alert.style.display = 'none';
                    title.innerText = "Active Target Locked"; title.style.color = 'var(--neon-cyan)';
                }
            });

            // Listen for Client's Pinned Location 
            db.ref(`trackings/${currentAdminTargetPhone}/client_location`).on('value', (snap) => {
                const card = document.getElementById('admin-client-loc-card');
                if(snap.exists()) {
                    card.style.display = 'block';
                    const loc = snap.val();
                    lastKnownTargetLoc = [loc.lat, loc.lng];
                    document.getElementById('admin-view-client-map').href = `https://www.google.com/maps/search/?api=1&query=${loc.lat},${loc.lng}`;
                    
                    // Add pin to map
                    var redIcon = new L.Icon({iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-red.png', shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/0.7.7/images/marker-shadow.png', iconSize: [25, 41], iconAnchor: [12, 41]});
                    L.marker([loc.lat, loc.lng], {icon: redIcon}).addTo(adminMap).bindPopup('Event Location');
                } else {
                    card.style.display = 'none'; lastKnownTargetLoc = null;
                }
            });

            // Listen for Client Telemetry (Battery)
            db.ref(`trackings/${currentAdminTargetPhone}/telemetry`).on('value', snap => {
                if(snap.exists() && snap.val().battery) {
                    const b = snap.val().battery;
                    document.getElementById('a-batt').innerText = `${b.level}% ${b.isCharging?'⚡':''}`;
                    document.getElementById('a-batt').style.color = b.level > 20 ? 'var(--neon-green)' : 'var(--danger)';
                }
            });

            // Listen to Official Updates Timeline
            db.ref(`trackings/${currentAdminTargetPhone}/updates`).orderByChild('timestamp').on('value', (snap) => {
                const area = document.getElementById('admin-manage-updates-area');
                if(snap.exists()) {
                    let html = '';
                    const updates = [];
                    snap.forEach(child => updates.push({ key: child.key, ...child.val() }));
                    
                    updates.reverse().forEach(u => {
                        html += `
                            <div class="timeline-item" style="padding-bottom:15px; margin-bottom:15px; border-bottom: 1px dashed rgba(255,255,255,0.05); display: flex; justify-content: space-between; align-items: flex-start;">
                                <div class="update-content" style="width: 85%;">
                                    <div class="update-time">${u.time}</div>
                                    <div class="update-text">${u.text}</div>
                                </div>
                                <button class="timeline-trash-btn" onclick="adminDeleteUpdate('${u.key}', event)"><i class="fas fa-times"></i></button>
                            </div>
                        `;
                    });
                    area.innerHTML = html;
                } else {
                    area.innerHTML = '<div style="font-size:12px; color:#555; text-align:center; margin-top:10px;">No updates sent yet.</div>';
                }
            });

            // Listen to Typing status
            db.ref(`trackings/${currentAdminTargetPhone}/typing/client`).on('value', snap => {
                document.getElementById('admin-typing-status').innerText = snap.val() ? "typing..." : "Online";
                document.getElementById('admin-typing-status').style.fontStyle = snap.val() ? "italic" : "normal";
            });

            // 3. 💬 ADVANCED TWO-WAY CHAT LISTENER (ADMIN VIEW)
            db.ref(`trackings/${currentAdminTargetPhone}/client_messages`).orderByChild('timestamp').limitToLast(100).on('value', (snap) => {
                const area = document.getElementById('admin-chat-area');
                if(snap.exists()) {
                    let html = '';
                    
                    snap.forEach(child => {
                        const key = child.key;
                        const m = child.val();
                        
                        // Push notify new messages
                        if (!isChatInitialLoad && !processedChatKeys.has(key) && m.sender === 'client') {
                            showPushNotification(`Message from ${name}`, m.text || (m.type ? `Sent an ${m.type}` : 'New attachment'));
                            // Send read receipt back
                            db.ref(`trackings/${currentAdminTargetPhone}/client_messages/${key}/status`).set('read');
                        }
                        processedChatKeys.add(key);

                        const isMe = m.sender === 'admin';
                        const bubbleClass = isMe ? 'sent' : 'received'; 
                        const safeText = m.text ? m.text.replace(/'/g, "\\'").replace(/"/g, "&quot;") : '';
                        
                        let replyHtml = '';
                        if(m.replyToText || m.replyToMedia) {
                            const senderName = m.replyToSender === 'admin' ? 'HQ Dispatch' : name;
                            replyHtml = `<div class="chat-reply-context"><div class="sender">${senderName}</div>${m.replyToMedia ? '<i class="fas fa-photo-video"></i> Media' : m.replyToText}</div>`;
                        }

                        // Advanced Media Rendering
                        let mediaHtml = '';
                        if(m.type === 'image') mediaHtml = `<img src="${m.mediaUrl}" class="chat-media-img" onclick="openImageModal('${m.mediaUrl}')">`;
                        else if(m.type === 'video') mediaHtml = `<video src="${m.mediaUrl}" class="chat-media-video" controls></video>`;
                        else if(m.type === 'audio') mediaHtml = `<audio src="${m.mediaUrl}" class="chat-media-audio" controls></audio>`;
                        else if(m.type === 'file') mediaHtml = `<a href="${m.mediaUrl}" target="_blank" download class="chat-media-file"><i class="fas fa-file-alt"></i> <span>Download File</span></a>`;

                        // Read Receipts logic
                        let tickHtml = '';
                        if(isMe) {
                            if(m.status === 'read') tickHtml = `<i class="fas fa-check-double chat-ticks read"></i>`;
                            else tickHtml = `<i class="fas fa-check chat-ticks"></i>`;
                        }

                        html += `
                            <div class="chat-bubble-container ${bubbleClass}" id="msg-cont-${key}">
                                <div class="swipe-reply-icon"><i class="fas fa-reply"></i></div>
                                <div class="chat-bubble" id="msg-${key}" onclick="toggleChatActions('msg-cont-${key}', event)">
                                    ${replyHtml}
                                    ${mediaHtml}
                                    ${m.text ? `<div>${m.text}</div>` : ''}
                                    <div class="chat-info-row"><div class="chat-time">${m.time}</div> ${tickHtml}</div>
                                    <div class="chat-actions-menu">
                                        ${m.text ? `<button class="chat-action-btn" onclick="copyChatText('${safeText}', event)"><i class="fas fa-copy"></i></button>` : ''}
                                        <button class="chat-action-btn delete" onclick="deleteChatMessage('${currentAdminTargetPhone}', '${key}', event)"><i class="fas fa-trash"></i></button>
                                    </div>
                                </div>
                            </div>
                        `;
                    });
                    
                    isChatInitialLoad = false;
                    area.innerHTML = html;
                    area.scrollTop = area.scrollHeight; 
                    
                    // Attach swipe listeners after rendering
                    setTimeout(() => addSwipeListeners('admin'), 100);
                } else {
                    area.innerHTML = '<div style="font-size:12px; color:rgba(255,255,255,0.5); text-align:center; margin: auto; background:rgba(0,0,0,0.4); padding:6px 12px; border-radius:12px;">End-to-end encrypted chat started. Swipe to reply.</div>';
                    isChatInitialLoad = false;
                }
            });
        }

        function closeAdminSession(e) {
            if(e) e.stopPropagation();
            if(geoWatchId !== null) toggleLocationBroadcast(); 
            if(currentAdminTargetPhone) {
                db.ref(`trackings/${currentAdminTargetPhone}/sos`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/client_location`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/updates`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/client_messages`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/telemetry`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/typing`).off();
                // turn off typing if set
                db.ref(`trackings/${currentAdminTargetPhone}/typing/admin`).set(false);
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
            const mapW = document.getElementById('admin-map-wrapper');

            if(!currentAdminTargetPhone) return;

            if(geoWatchId !== null) {
                navigator.geolocation.clearWatch(geoWatchId);
                geoWatchId = null; pingCount = 0; adminPathLatlngs=[];
                btn.innerHTML = '<i class="fas fa-location-arrow"></i> INITIATE BROADCAST TO TARGET';
                btn.style.background = "transparent"; btn.style.color = "var(--neon-cyan)"; btn.style.borderColor = "var(--neon-cyan)";
                metrics.style.display = 'none'; locDisp.style.display = 'none'; mapW.style.display = 'none';
                
                db.ref(`trackings/${currentAdminTargetPhone}/location/status`).set('offline').catch(err => console.warn(err)); 
                showToast("Transmission Terminated", "error");
            } else {
                if(!navigator.geolocation) { showToast("Hardware GPS not supported.", "error"); return; }
                btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> ACQUIRING SATELLITES...';
                
                geoWatchId = navigator.geolocation.watchPosition(
                    async (pos) => {
                        btn.innerHTML = '<i class="fas fa-times-circle"></i> STOP BROADCASTING';
                        btn.style.background = "rgba(255,51,51,0.1)"; btn.style.color = "var(--danger)"; btn.style.borderColor = "var(--danger)";
                        metrics.style.display = 'grid'; locDisp.style.display = 'flex'; mapW.style.display = 'block';
                        
                        setTimeout(() => adminMap.invalidateSize(), 100);

                        pingCount++;
                        const lat = pos.coords.latitude; const lng = pos.coords.longitude;
                        const speedKmh = pos.coords.speed ? (pos.coords.speed * 3.6).toFixed(1) : 0;
                        
                        document.getElementById('a-speed').innerText = speedKmh + " km/h";
                        document.getElementById('a-acc').innerText = "±" + Math.round(pos.coords.accuracy) + "m";
                        document.getElementById('a-ping').innerText = pingCount;

                        // Update Leaflet Map
                        const newLoc = [lat, lng];
                        adminMarker.setLatLng(newLoc);
                        adminPathLatlngs.push(newLoc);
                        adminPolyline.setLatLngs(adminPathLatlngs);
                        if(pingCount === 1) adminMap.setView(newLoc, 15);

                        let locName = "Resolving...";
                        if(pingCount % 10 === 1) { 
                            locName = await fetchLocationName(lat, lng);
                            document.getElementById('a-loc-name').innerText = locName;
                            fetchWeather(lat, lng, 'a-weather-temp');
                        } else {
                            locName = document.getElementById('a-loc-name').innerText;
                        }

                        db.ref(`trackings/${currentAdminTargetPhone}/location`).set({
                            status: 'online', lat: lat, lng: lng,
                            speed: speedKmh, heading: pos.coords.heading ? Math.round(pos.coords.heading) + "°" : "N/A",
                            accuracy: Math.round(pos.coords.accuracy), time: new Date().toLocaleTimeString(),
                            locationName: locName, pings: pingCount
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

        // --- 11. 💬 WHATSAPP-STYLE CHAT FUNCTIONS (ADVANCED MEDIA, SWIPE, REC) ---
        
        // Typing Indicators
        function handleTyping(role) {
            const inputId = role === 'admin' ? 'admin-chat-input' : 'client-chat-input';
            const val = document.getElementById(inputId).value;
            const targetPhone = role === 'admin' ? currentAdminTargetPhone : currentClientPhone;
            
            // Toggle send/mic buttons based on input
            const sendBtn = document.getElementById(`${role}-send-btn`);
            const micBtn = document.getElementById(`${role}-mic-btn`);
            
            if(val.length > 0 || pendingMedia[role] !== null) {
                sendBtn.style.display = 'flex'; micBtn.style.display = 'none';
            } else {
                sendBtn.style.display = 'none'; micBtn.style.display = 'flex';
            }

            if(targetPhone) {
                db.ref(`trackings/${targetPhone}/typing/${role}`).set(val.length > 0);
                clearTimeout(typingTimeout);
                typingTimeout = setTimeout(() => db.ref(`trackings/${targetPhone}/typing/${role}`).set(false), 2000);
            }
        }
        
        function handleEnter(e, role) { if(e.key === 'Enter') sendChatMessage(role, e); }

        function toggleChatActions(containerId, e) {
            if(e) e.stopPropagation();
            closeAllMenus();
            document.getElementById(containerId).classList.toggle('show-actions');
        }

        function copyChatText(text, e) {
            if(e) e.stopPropagation();
            navigator.clipboard.writeText(text).then(() => {
                showToast("Copied to clipboard!");
                closeAllMenus();
            }).catch(err => showToast("Failed to copy", "error"));
        }

        // Swipe to Reply Gestures
        function addSwipeListeners(role) {
            const area = document.getElementById(role === 'admin' ? 'admin-chat-area' : 'client-chat-area');
            const containers = area.querySelectorAll('.chat-bubble-container');
            
            containers.forEach(container => {
                if(container.dataset.swipeBound) return;
                container.dataset.swipeBound = true;
                
                let startX = 0; let currentX = 0;
                container.addEventListener('touchstart', e => { startX = e.touches[0].clientX; }, {passive: true});
                container.addEventListener('touchmove', e => {
                    currentX = e.touches[0].clientX;
                    let diff = currentX - startX;
                    // Only swipe right, limit to 60px
                    if(diff > 0 && diff < 60) {
                        container.style.transform = `translateX(${diff}px)`;
                        const icon = container.querySelector('.swipe-reply-icon');
                        if(icon) icon.style.opacity = diff / 60;
                    }
                }, {passive: true});
                
                container.addEventListener('touchend', e => {
                    container.style.transform = 'translateX(0)';
                    const icon = container.querySelector('.swipe-reply-icon');
                    if(icon) icon.style.opacity = 0;
                    
                    if(currentX - startX > 45) { // Threshold for reply trigger
                        // Find bubble data
                        const bubble = container.querySelector('.chat-bubble');
                        const key = bubble.id.replace('msg-', '');
                        
                        // Check if it has text or media
                        const textDiv = Array.from(bubble.children).find(el => el.tagName === 'DIV' && !el.className.includes('chat-'));
                        const mediaEl = bubble.querySelector('img, video, audio, .chat-media-file');
                        
                        let text = textDiv ? textDiv.innerText : '';
                        let hasMedia = mediaEl ? true : false;
                        
                        // Sender determine (if container has 'sent', it's me, else them)
                        const originalSenderRole = container.classList.contains('sent') ? role : (role==='admin'?'client':'admin');
                        
                        initiateReply(key, text, originalSenderRole, role, null, hasMedia);
                    }
                });
            });
        }

        function initiateReply(key, text, originalSenderRole, role, e, hasMedia=false) {
            if(e) e.stopPropagation();
            closeAllMenus();
            
            let shortText = text.length > 40 ? text.substring(0, 40) + '...' : text;
            if(hasMedia && !shortText) shortText = "Media Attachment";
            
            const senderName = originalSenderRole === 'admin' ? 'HQ Dispatch' : (role==='admin' ? document.getElementById('session-c-name').innerText : 'You');
            
            replyContext[role] = { key, text: shortText, sender: originalSenderRole, isMedia: hasMedia };
            
            document.getElementById(`${role}-reply-sender`).innerText = senderName;
            document.getElementById(`${role}-reply-text`).innerHTML = hasMedia ? '<i class="fas fa-photo-video"></i> Media' : shortText;
            document.getElementById(`${role}-reply-banner`).style.display = 'flex';
            document.getElementById(`${role}-chat-input`).focus();
        }

        function cancelReply(role, e) {
            if(e) e.stopPropagation();
            replyContext[role] = null;
            document.getElementById(`${role}-reply-banner`).style.display = 'none';
        }

        // Advanced Media Handling (Compress & Base64 via Canvas)
        function handleFileSelect(e, role) {
            const file = e.target.files[0];
            if(!file) return;
            
            // Limit to ~4MB for RTDB base64 safely if Storage isn't heavily used.
            if(file.size > 4 * 1024 * 1024) {
                showToast("File too large. Max 4MB allowed.", "error"); return;
            }

            const reader = new FileReader();
            reader.onload = (event) => {
                const result = event.target.result;
                let type = 'file';
                if(file.type.startsWith('image/')) type = 'image';
                else if(file.type.startsWith('video/')) type = 'video';
                else if(file.type.startsWith('audio/')) type = 'audio';
                
                // Compress Images
                if(type === 'image') {
                    const img = new Image();
                    img.onload = () => {
                        const canvas = document.createElement('canvas');
                        const MAX_WIDTH = 800; const MAX_HEIGHT = 800;
                        let width = img.width; let height = img.height;
                        if(width > height && width > MAX_WIDTH) { height *= MAX_WIDTH / width; width = MAX_WIDTH; }
                        else if(height > MAX_HEIGHT) { width *= MAX_HEIGHT / height; height = MAX_HEIGHT; }
                        
                        canvas.width = width; canvas.height = height;
                        const ctx = canvas.getContext('2d');
                        ctx.drawImage(img, 0, 0, width, height);
                        const dataUrl = canvas.toDataURL('image/jpeg', 0.7);
                        setMediaPreview(dataUrl, 'image', role);
                    }
                    img.src = result;
                } else {
                    setMediaPreview(result, type, role);
                }
            };
            reader.readAsDataURL(file);
        }
        
        function setMediaPreview(dataUrl, type, role) {
            pendingMedia[role] = { url: dataUrl, type: type };
            const strip = document.getElementById(`${role}-media-preview`);
            
            let previewHtml = '';
            if(type === 'image') previewHtml = `<img src="${dataUrl}">`;
            else if(type === 'video') previewHtml = `<i class="fas fa-video" style="font-size:30px; color:#fff; position:absolute; top:50%; left:50%; transform:translate(-50%,-50%);"></i>`;
            else previewHtml = `<i class="fas fa-file" style="font-size:30px; color:#fff; position:absolute; top:50%; left:50%; transform:translate(-50%,-50%);"></i>`;
            
            strip.innerHTML = `
                <div class="preview-item">
                    ${previewHtml}
                    <button class="preview-remove" onclick="removeMediaPreview('${role}')"><i class="fas fa-times"></i></button>
                </div>
            `;
            strip.style.display = 'flex';
            handleTyping(role); // switch mic to send button
        }
        
        function removeMediaPreview(role) {
            pendingMedia[role] = null;
            document.getElementById(`${role}-media-preview`).style.display = 'none';
            document.getElementById(`${role}-file-input`).value = "";
            handleTyping(role);
        }

        // Voice Recording Note
        function startRecording(role) {
            if(!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) { showToast("Audio not supported", "error"); return; }
            navigator.mediaDevices.getUserMedia({ audio: true }).then(stream => {
                mediaRecorder = new MediaRecorder(stream);
                mediaRecorder.start();
                audioChunks = [];
                isRecording = true;
                document.getElementById(`${role}-mic-btn`).classList.add('recording');
                
                mediaRecorder.addEventListener("dataavailable", event => { audioChunks.push(event.data); });
                mediaRecorder.addEventListener("stop", () => {
                    const audioBlob = new Blob(audioChunks, { type: 'audio/webm' });
                    const reader = new FileReader();
                    reader.onload = (e) => {
                        pendingMedia[role] = { url: e.target.result, type: 'audio' };
                        sendChatMessage(role, null, true); // auto send voice note
                    };
                    reader.readAsDataURL(audioBlob);
                    stream.getTracks().forEach(track => track.stop()); // close mic
                });
            }).catch(err => showToast("Mic permission denied", "error"));
        }
        function stopRecording(role) {
            if(isRecording && mediaRecorder) {
                mediaRecorder.stop();
                isRecording = false;
                document.getElementById(`${role}-mic-btn`).classList.remove('recording');
            }
        }

        function sendChatMessage(role, e, isVoiceAutoSend = false) {
            if(e) e.stopPropagation();
            const targetPhone = role === 'admin' ? currentAdminTargetPhone : currentClientPhone;
            if(!targetPhone) return;
            
            const input = document.getElementById(`${role}-chat-input`);
            const text = input.value.trim();
            const media = pendingMedia[role];
            
            if(!text && !media && !isVoiceAutoSend) return;

            const btn = document.getElementById(`${role}-send-btn`);
            if(btn) { btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i>'; btn.disabled = true; }

            const msgData = {
                text: text, sender: role, status: 'sent',
                time: new Date().toLocaleTimeString('en-US', {hour: '2-digit', minute:'2-digit'}),
                timestamp: Date.now()
            };

            if(media) {
                msgData.type = media.type;
                msgData.mediaUrl = media.url;
            }

            if(replyContext[role]) {
                msgData.replyToKey = replyContext[role].key;
                msgData.replyToText = replyContext[role].text;
                msgData.replyToSender = replyContext[role].sender;
                msgData.replyToMedia = replyContext[role].isMedia;
            }

            db.ref(`trackings/${targetPhone}/client_messages`).push(msgData).then(() => {
                input.value = '';
                removeMediaPreview(role);
                cancelReply(role);
                db.ref(`trackings/${targetPhone}/typing/${role}`).set(false);
                if(btn) { btn.innerHTML = '<i class="fas fa-paper-plane"></i>'; btn.disabled = false; }
            }).catch(e => {
                showToast("Send failed", "error");
                if(btn) { btn.innerHTML = '<i class="fas fa-paper-plane"></i>'; btn.disabled = false; }
            });
        }

        function deleteChatMessage(targetPhone, key, e) {
            if(e) e.stopPropagation();
            if(!targetPhone || !key) return;
            
            db.ref(`trackings/${targetPhone}/client_messages/${key}`).remove()
            .then(() => closeAllMenus())
            .catch(err => showToast("Failed to delete", "error"));
        }

        // Image Modal Control
        function openImageModal(src) {
            document.getElementById('modal-img-src').src = src;
            document.getElementById('modal-download-btn').href = src;
            document.getElementById('image-modal').style.display = 'flex';
        }
        function closeImageModal() {
            document.getElementById('image-modal').style.display = 'none';
        }

        // --- 12. CLIENT: SHARE LOCATION & SOS ---
        function clientShareLocation(e) {
            if(e) e.stopPropagation();
            if(!currentClientPhone) return;
            if(!navigator.geolocation) { showToast("GPS not supported on this device.", "error"); return; }
            
            showToast("Acquiring your location...");
            navigator.geolocation.getCurrentPosition((pos) => {
                db.ref(`trackings/${currentClientPhone}/client_location`).set({
                    lat: pos.coords.latitude, lng: pos.coords.longitude, time: Date.now()
                }).then(() => {
                    showToast("Pin Dropped at HQ!");
                });
            }, (err) => {
                showToast("Failed to get location. Ensure GPS is ON.", "error");
            }, { enableHighAccuracy: true });
        }
        
        function triggerSOS(e) {
            if(e) e.stopPropagation();
            if(!currentClientPhone) return;
            
            const btn = document.getElementById('btn-client-sos');
            btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> TRANSMITTING SOS...';
            
            // Set SOS flag
            db.ref(`trackings/${currentClientPhone}/sos`).set(true).then(() => {
                btn.innerHTML = '<i class="fas fa-check-circle"></i> SOS TRANSMITTED';
                btn.style.background = "var(--danger)";
                showToast("Emergency signal sent to HQ", "error");
                setTimeout(() => {
                    btn.innerHTML = '<i class="fas fa-exclamation-triangle"></i> EMERGENCY SOS';
                    btn.style.background = "";
                }, 5000);
            });
            
            // Auto share location on SOS
            if(navigator.geolocation) {
                navigator.geolocation.getCurrentPosition((pos) => {
                    db.ref(`trackings/${currentClientPhone}/client_location`).set({
                        lat: pos.coords.latitude, lng: pos.coords.longitude, time: Date.now()
                    });
                });
            }
        }

        // --- 13. CLIENT: TARGETED WATCH LISTENERS ---
        function startClientWatchListeners() {
            if(!currentClientPhone) return;

            // GPS Listener (Leaflet Map Update)
            db.ref(`trackings/${currentClientPhone}/location`).on('value', (snapshot) => {
                const badge = document.getElementById('client-conn-status');
                if(snapshot.exists() && snapshot.val().status === 'online') {
                    const data = snapshot.val();
                    badge.innerHTML = '<i class="fas fa-circle fa-beat"></i> ONLINE'; badge.className = 'status-badge';
                    document.getElementById('client-gps-time').innerHTML = `<span style="color:var(--neon-green);">Signal Locked (${data.time})</span>`;
                    document.getElementById('c-speed').innerText = data.speed + " km/h";
                    document.getElementById('c-heading').innerText = data.heading;
                    document.getElementById('c-accuracy').innerText = "±" + data.accuracy + "m";
                    document.getElementById('c-pings').innerText = data.pings || 0;
                    
                    document.getElementById('map-loader-text').style.display = 'none';
                    document.getElementById('ext-map-link').style.display = 'block';
                    document.getElementById('ext-map-link').href = `https://www.google.com/maps/search/?api=1&query=${data.lat},${data.lng}`;
                    document.getElementById('client-map').style.opacity = '1';

                    const locDisp = document.getElementById('client-location-display');
                    if(data.locationName && data.locationName !== "Resolving...") {
                        locDisp.style.display = 'flex';
                        document.getElementById('c-location-name').innerText = data.locationName;
                        fetchWeather(data.lat, data.lng, 'c-weather-temp');
                    }

                    // Update Leaflet Map Pathing
                    const newLoc = [data.lat, data.lng];
                    clientMarker.setLatLng(newLoc);
                    clientPathLatlngs.push(newLoc);
                    clientPolyline.setLatLngs(clientPathLatlngs);
                    clientMap.setView(newLoc, 15);

                } else {
                    badge.innerHTML = '<i class="fas fa-exclamation-circle"></i> SIGNAL LOST'; badge.className = 'status-badge offline';
                    document.getElementById('client-gps-time').innerText = "Target Offline";
                    document.getElementById('client-map').style.opacity = '0.3';
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

            // Listen to Typing status from Admin
            db.ref(`trackings/${currentClientPhone}/typing/admin`).on('value', snap => {
                document.getElementById('client-typing-status').innerText = snap.val() ? "typing..." : "Online";
                document.getElementById('client-typing-status').style.fontStyle = snap.val() ? "italic" : "normal";
            });

            // 2. 💬 ADVANCED TWO-WAY CHAT LISTENER (CLIENT VIEW)
            db.ref(`trackings/${currentClientPhone}/client_messages`).orderByChild('timestamp').limitToLast(100).on('value', (snap) => {
                const area = document.getElementById('client-chat-area');
                if(snap.exists()) {
                    let html = '';
                    
                    snap.forEach(child => {
                        const key = child.key;
                        const m = child.val();
                        
                        if (!isChatInitialLoad && !processedChatKeys.has(key) && m.sender === 'admin') {
                            showPushNotification("New Message from HQ", m.text || "Media Attachment");
                            db.ref(`trackings/${currentClientPhone}/client_messages/${key}/status`).set('read');
                        }
                        processedChatKeys.add(key);

                        const isMe = m.sender === 'client';
                        const bubbleClass = isMe ? 'sent' : 'received';
                        const safeText = m.text ? m.text.replace(/'/g, "\\'").replace(/"/g, "&quot;") : '';
                        
                        let replyHtml = '';
                        if(m.replyToText || m.replyToMedia) {
                            const senderName = m.replyToSender === 'client' ? 'You' : 'HQ Dispatch';
                            replyHtml = `<div class="chat-reply-context"><div class="sender">${senderName}</div>${m.replyToMedia ? '<i class="fas fa-photo-video"></i> Media' : m.replyToText}</div>`;
                        }

                        // Media Rendering
                        let mediaHtml = '';
                        if(m.type === 'image') mediaHtml = `<img src="${m.mediaUrl}" class="chat-media-img" onclick="openImageModal('${m.mediaUrl}')">`;
                        else if(m.type === 'video') mediaHtml = `<video src="${m.mediaUrl}" class="chat-media-video" controls></video>`;
                        else if(m.type === 'audio') mediaHtml = `<audio src="${m.mediaUrl}" class="chat-media-audio" controls></audio>`;
                        else if(m.type === 'file') mediaHtml = `<a href="${m.mediaUrl}" target="_blank" download class="chat-media-file"><i class="fas fa-file-alt"></i> <span>Download File</span></a>`;

                        // Read Receipts
                        let tickHtml = '';
                        if(isMe) {
                            if(m.status === 'read') tickHtml = `<i class="fas fa-check-double chat-ticks read"></i>`;
                            else tickHtml = `<i class="fas fa-check chat-ticks"></i>`;
                        }

                        const deleteBtnHtml = isMe ? `<button class="chat-action-btn delete" onclick="deleteChatMessage('${currentClientPhone}', '${key}', event)"><i class="fas fa-trash"></i></button>` : '';

                        html += `
                            <div class="chat-bubble-container ${bubbleClass}" id="cmsg-cont-${key}">
                                <div class="swipe-reply-icon"><i class="fas fa-reply"></i></div>
                                <div class="chat-bubble" id="msg-${key}" onclick="toggleChatActions('cmsg-cont-${key}', event)">
                                    ${replyHtml}
                                    ${mediaHtml}
                                    ${m.text ? `<div>${m.text}</div>` : ''}
                                    <div class="chat-info-row"><div class="chat-time">${m.time}</div> ${tickHtml}</div>
                                    <div class="chat-actions-menu">
                                        ${m.text ? `<button class="chat-action-btn" onclick="copyChatText('${safeText}', event)"><i class="fas fa-copy"></i></button>` : ''}
                                        ${deleteBtnHtml}
                                    </div>
                                </div>
                            </div>
                        `;
                    });
                    
                    isChatInitialLoad = false;
                    area.innerHTML = html;
                    area.scrollTop = area.scrollHeight; 
                    setTimeout(() => addSwipeListeners('client'), 100);
                } else {
                    area.innerHTML = '<div style="font-size:12px; color:rgba(255,255,255,0.5); text-align:center; margin: auto; background:rgba(0,0,0,0.4); padding:6px 12px; border-radius:12px;">End-to-end encrypted chat started. Swipe bubbles right to reply.</div>';
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
                db.ref(`trackings/${currentClientPhone}/typing/admin`).off();
                db.ref(`trackings/${currentClientPhone}/typing/client`).set(false);
            }
            if(currentAdminTargetPhone) {
                db.ref(`trackings/${currentAdminTargetPhone}/sos`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/client_location`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/updates`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/client_messages`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/telemetry`).off();
            }
            
            currentAdminTargetPhone = ""; currentClientPhone = "";
            cancelReply('admin'); cancelReply('client');
            
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
