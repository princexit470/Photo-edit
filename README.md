<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>princexit_ | Creative Studio</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
    <style>
        :root {
            --gold: #d4af37; --gold-hover: #f3b965; --bg: #0d0d0d; --surface: #171717;
            --surface-hover: #222; --border: rgba(255, 255, 255, 0.08); --text: #f0f0f0;
            --dim: #a0a0a0; --radius: 16px; --transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { font-family: 'Segoe UI', system-ui, sans-serif; background: var(--bg); color: var(--text); min-height: 100vh; line-height: 1.6; -webkit-tap-highlight-color: transparent; overflow-x: hidden; scroll-behavior: smooth; }
        body.no-scroll { overflow: hidden; touch-action: none; }
        
        /* Toast Notifications */
        .toast-container { position: fixed; top: 20px; right: 20px; z-index: 10000; display: flex; flex-direction: column; gap: 10px; pointer-events: none; }
        .toast { padding: 14px 24px; border-radius: 12px; color: #fff; font-weight: 500; font-size: 0.9rem; background: rgba(20,20,20,0.95); backdrop-filter: blur(10px); animation: toastIn 0.4s ease forwards; box-shadow: 0 10px 30px rgba(0,0,0,0.5); border-left: 4px solid var(--gold); }
        .toast.success { border-left-color: #2ecc71; } .toast.error { border-left-color: #e74c3c; }
        @keyframes toastIn { from { transform: translateX(120%); opacity: 0; } to { transform: translateX(0); opacity: 1; } }
        
        /* Navbar */
        .navbar { position: sticky; top: 0; z-index: 500; background: rgba(13,13,13,0.85); backdrop-filter: blur(20px); padding: 1rem 2rem; display: flex; align-items: center; justify-content: space-between; border-bottom: 1px solid var(--border); }
        .nav-logo { font-size: 1.5rem; font-weight: 800; color: var(--gold); cursor: pointer; letter-spacing: -0.5px; }
        .nav-links { display: flex; gap: 0.5rem; list-style: none; align-items: center; }
        .nav-links a { color: var(--dim); text-decoration: none; padding: 0.5rem 1rem; border-radius: 20px; cursor: pointer; font-size: 0.95rem; font-weight: 500; transition: var(--transition); }
        .nav-links a:hover, .nav-links a.active { color: #fff; background: rgba(255,255,255,0.05); }
        .nav-btn { padding: 0.55rem 1.3rem; border-radius: 20px; border: 1px solid var(--border); background: transparent; color: #fff; cursor: pointer; font-size: 0.85rem; font-weight: 500; transition: var(--transition); }
        .nav-btn:hover { background: rgba(255,255,255,0.05); }
        .nav-btn.primary { background: var(--gold); border: none; color: #000; font-weight: 600; }
        .nav-btn.primary:hover { background: var(--gold-hover); transform: translateY(-2px); }
        
        /* User Profile Dropdown */
        .profile-chip { display: flex; align-items: center; gap: 8px; background: rgba(255,255,255,0.05); padding: 0.4rem 1rem; border-radius: 20px; cursor: pointer; border: 1px solid var(--border); }
        .profile-dropdown { position: absolute; top: 60px; right: 20px; background: #111; border-radius: 12px; padding: 0.5rem; border: 1px solid var(--border); z-index: 60; display: none; min-width: 150px; box-shadow: 0 10px 30px rgba(0,0,0,0.8); }
        .profile-dropdown.show { display: block; animation: dropIn 0.2s ease; }
        @keyframes dropIn { from { opacity: 0; transform: translateY(-10px); } to { opacity: 1; transform: translateY(0); } }
        
        /* Mobile Menu */
        .hamburger { display: none; background: none; border: none; flex-direction: column; gap: 5px; cursor: pointer; padding: 5px; }
        .hamburger span { width: 24px; height: 2px; background: #fff; display: block; transition: var(--transition); }
        .mobile-menu { position: fixed; top: 0; right: -300px; width: 280px; height: 100vh; background: rgba(10,10,10,0.98); backdrop-filter: blur(20px); z-index: 600; padding: 5rem 1.5rem 2rem; transition: right 0.4s ease; border-left: 1px solid var(--border); }
        .mobile-menu.open { right: 0; }
        .mobile-menu a { display: block; color: var(--dim); padding: 1rem; text-decoration: none; border-radius: 12px; cursor: pointer; font-weight: 500; font-size: 1.1rem;}
        .mobile-menu a:hover { background: rgba(255,255,255,0.05); color: #fff;}
        .overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.8); z-index: 550; display: none; }
        .overlay.show { display: block; }
        
        /* Main Layout */
        .main-content { padding: 2rem; max-width: 1200px; margin: 0 auto; min-height: 80vh; }
        .page { display: none; animation: pageFade 0.4s ease forwards; opacity: 0; }
        .page.show { display: block; opacity: 1; }
        @keyframes pageFade { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        
        /* Hero & Live Section */
        .hero-section { text-align: center; padding: 2rem 0 3rem; }
        .hero-title { font-size: 3.5rem; color: var(--gold); font-weight: 900; letter-spacing: -1px; }
        
        .live-section { margin: 0 auto 3rem; max-width: 900px; padding: 1.5rem; background: var(--surface); border-radius: var(--radius); border: 1px solid var(--border); position: relative; box-shadow: 0 10px 40px rgba(0,0,0,0.4); }
        .live-badge { background: #e74c3c; color: #fff; padding: 0.4rem 1rem; border-radius: 20px; font-size: 0.8rem; font-weight: 700; animation: pulse 2s infinite; display: inline-flex; align-items: center; gap: 6px; }
        .live-badge.offline { background: #333; color: #888; animation: none; }
        .viewer-count { position: absolute; top: 1.5rem; right: 1.5rem; background: rgba(0,0,0,0.7); color: #fff; padding: 0.4rem 1rem; border-radius: 20px; font-size: 0.85rem; display: flex; align-items: center; gap: 6px; z-index: 10; border: 1px solid var(--border); backdrop-filter: blur(5px); }
        
        .live-video-container { width: 100%; aspect-ratio: 16/9; background: #000; border-radius: 12px; overflow: hidden; position: relative; border: 1px solid var(--border); margin-top: 1rem; }
        .live-video-container iframe, .live-video-container video { width: 100%; height: 100%; border: none; object-fit: contain; }
        .coming-soon-overlay { position: absolute; inset: 0; display: flex; align-items: center; justify-content: center; background: #050505; flex-direction: column; gap: 15px; z-index: 2; }
        .live-buttons { position: absolute; bottom: -20px; left: 50%; transform: translateX(-50%); display: flex; gap: 10px; z-index: 15; background: #111; padding: 10px 20px; border-radius: 30px; border: 1px solid var(--border); box-shadow: 0 10px 20px rgba(0,0,0,0.5); }
        
        /* Media Grids */
        .section-title { font-size: 1.8rem; margin-bottom: 1.5rem; font-weight: 700; color: #fff; }
        .add-media-bar { display: flex; gap: 10px; margin-bottom: 1.5rem; }
        
        .gallery-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); gap: 1.2rem; }
        .gallery-item { position: relative; border-radius: 14px; overflow: hidden; cursor: pointer; background: #000; aspect-ratio: 4/5; border: 1px solid var(--border); transition: transform 0.3s; }
        .gallery-item:hover { transform: scale(1.03); border-color: var(--gold); }
        .gallery-item img { width: 100%; height: 100%; object-fit: cover; }
        .media-title { position: absolute; bottom: 0; left: 0; right: 0; background: linear-gradient(transparent, rgba(0,0,0,0.9)); color: #fff; padding: 30px 15px 15px; font-size: 0.9rem; font-weight: 500; }
        
        .video-feed { display: flex; flex-direction: column; gap: 1.5rem; max-width: 900px; margin: 0 auto; }
        .video-card-yt { display: flex; gap: 1rem; background: var(--surface); border-radius: 16px; border: 1px solid var(--border); overflow: hidden; transition: var(--transition); }
        .video-card-yt:hover { border-color: rgba(255,255,255,0.2); }
        .video-thumb-yt { width: 300px; aspect-ratio: 16/9; background: #000; position: relative; flex-shrink: 0; }
        .video-thumb-yt video, .video-thumb-yt iframe { width: 100%; height: 100%; border: none; object-fit: cover; }
        .video-info-yt { padding: 1.5rem 1.5rem 1.5rem 0; flex: 1; display: flex; flex-direction: column; justify-content: center; }
        
        .del-btn { position: absolute; top: 10px; right: 10px; width: 32px; height: 32px; border-radius: 50%; background: rgba(231,76,60,0.8); color: #fff; border: none; cursor: pointer; display: flex; align-items: center; justify-content: center; backdrop-filter: blur(5px); z-index: 10; transition: var(--transition); }
        .del-btn:hover { background: #e74c3c; transform: scale(1.1); }

        /* ADVANCED LIGHTBOX (Smooth Animations & Swipes) */
        .lightbox { position: fixed; inset: 0; background: rgba(0,0,0,0.98); z-index: 9999; display: flex; align-items: center; justify-content: center; flex-direction: column; opacity: 0; pointer-events: none; transition: opacity 0.3s ease; }
        .lightbox.show { opacity: 1; pointer-events: auto; }
        .lb-image-wrap { position: relative; width: 100%; height: 80vh; display: flex; align-items: center; justify-content: center; overflow: hidden; transition: transform 0.3s cubic-bezier(0.2, 0.8, 0.2, 1); }
        .lightbox img { max-width: 95vw; max-height: 80vh; border-radius: 12px; object-fit: contain; box-shadow: 0 20px 50px rgba(0,0,0,0.5); user-select: none; pointer-events: none; }
        
        .lb-top-bar { position: absolute; top: 0; left: 0; right: 0; padding: 20px; display: flex; justify-content: space-between; z-index: 10; background: linear-gradient(rgba(0,0,0,0.8), transparent); }
        .lb-counter { color: #fff; font-size: 1rem; font-weight: 500; }
        .lb-actions { display: flex; gap: 15px; }
        .lb-btn { background: rgba(255,255,255,0.1); border: none; color: #fff; width: 44px; height: 44px; border-radius: 50%; cursor: pointer; font-size: 1.2rem; display: flex; align-items: center; justify-content: center; backdrop-filter: blur(5px); transition: 0.2s; }
        .lb-btn:hover { background: rgba(255,255,255,0.2); transform: scale(1.05); }
        
        .lb-nav-btn { position: absolute; top: 50%; transform: translateY(-50%); background: transparent; border: none; color: rgba(255,255,255,0.5); font-size: 3rem; cursor: pointer; z-index: 10; padding: 20px; transition: 0.2s; }
        .lb-nav-btn:hover { color: #fff; }
        .lb-prev { left: 10px; } .lb-next { right: 10px; }
        .lb-title { position: absolute; bottom: 30px; color: #fff; font-size: 1.2rem; text-shadow: 0 2px 4px rgba(0,0,0,0.8); text-align: center; width: 100%; }

        /* Modals & Upload Manager */
        .modal-backdrop { position: fixed; inset: 0; background: rgba(0,0,0,0.85); z-index: 6000; display: none; align-items: center; justify-content: center; backdrop-filter: blur(8px); }
        .modal-backdrop.show { display: flex; }
        .modal-dialog { background: #111; border-radius: 20px; padding: 2.5rem; width: 90%; max-width: 450px; border: 1px solid var(--border); position: relative; box-shadow: 0 20px 60px rgba(0,0,0,0.8); max-height: 90vh; overflow-y: auto; }
        .modal-dialog h2 { color: #fff; text-align: center; margin-bottom: 1.5rem; font-size: 1.5rem; }
        .input-group { margin-bottom: 1rem; }
        .input-group label { display: block; font-size: 0.85rem; color: var(--dim); margin-bottom: 5px; }
        .modal-dialog input, .modal-dialog textarea, .modal-dialog select { width: 100%; padding: 1rem; border-radius: 12px; border: 1px solid var(--border); background: #000; color: #fff; outline: none; transition: 0.2s; font-size: 0.95rem; }
        .modal-dialog input:focus { border-color: var(--gold); }
        .modal-submit { width: 100%; padding: 1rem; margin-top: 1rem; border-radius: 12px; border: none; background: var(--gold); color: #000; font-weight: 700; font-size: 1rem; cursor: pointer; transition: 0.2s; }
        .modal-submit:hover { background: var(--gold-hover); transform: translateY(-2px); }
        .modal-close { position: absolute; top: 15px; right: 20px; background: none; border: none; color: var(--dim); font-size: 1.2rem; cursor: pointer; }
        
        .upload-manager { position: fixed; bottom: 20px; right: 20px; width: 340px; background: #111; border: 1px solid var(--border); border-radius: 16px; z-index: 5500; box-shadow: 0 10px 40px rgba(0,0,0,0.8); display: none; flex-direction: column; overflow: hidden; }
        .upload-manager.active { display: flex; }
        .um-header { padding: 15px; background: #1a1a1a; display: flex; justify-content: space-between; align-items: center; cursor: pointer; border-bottom: 1px solid var(--border); }
        .um-list { max-height: 250px; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 10px; }
        .um-item { background: #000; border: 1px solid var(--border); padding: 12px; border-radius: 10px; }
        .um-progress-bar { width: 100%; height: 6px; background: #333; margin-top: 8px; border-radius: 3px; overflow: hidden;}
        .um-progress-fill { height: 100%; background: var(--gold); width: 0%; transition: width 0.3s ease; }

        @media (max-width: 768px) {
            .nav-links { display: none; }
            .hamburger { display: flex; }
            .video-card-yt { flex-direction: column; }
            .video-thumb-yt { width: 100%; border-right: none; }
            .lb-nav-btn { display: none; /* Hide arrows on mobile, rely on swipe */ }
        }
    </style>
</head>
<body>
    <div class="toast-container" id="toastContainer"></div>
    <div class="overlay" id="mobileOverlay"></div>

    <nav class="navbar">
        <div class="nav-logo" onclick="navigateTo('home')">princexit_</div>
        <ul class="nav-links" id="desktopNavLinks">
            <li><a data-page="home" class="active" onclick="navigateTo('home')">Home</a></li>
            <li><a data-page="gallery" onclick="navigateTo('gallery')">Photos</a></li>
            <li><a data-page="videos" onclick="navigateTo('videos')">Videos</a></li>
            <li><a data-page="documents" onclick="navigateTo('documents')">Documents</a></li>
            <li><a data-page="about" onclick="navigateTo('about')">Contact</a></li>
        </ul>
        <div class="nav-right">
            <span id="desktopAuthBtns">
                <button class="nav-btn" onclick="showAuth('login')">Log In</button>
                <button class="nav-btn primary" onclick="showAuth('signup')">Sign Up</button>
            </span>
            <span class="profile-chip hidden" id="profileChip" onclick="document.getElementById('profileDropdown').classList.toggle('show')">
                <span id="profileEmailText"></span><i class="fas fa-chevron-down" style="font-size:0.7rem;"></i>
            </span>
            <div class="profile-dropdown" id="profileDropdown">
                <a onclick="doLogout()"><i class="fas fa-sign-out-alt"></i> Logout</a>
            </div>
            <button class="hamburger" id="hamburgerBtn"><span></span><span></span><span></span></button>
        </div>
    </nav>

    <div class="mobile-menu" id="mobileMenu">
        <a onclick="navigateTo('home')"><i class="fas fa-home fa-fw"></i> Home</a>
        <a onclick="navigateTo('gallery')"><i class="fas fa-image fa-fw"></i> Photos</a>
        <a onclick="navigateTo('videos')"><i class="fas fa-video fa-fw"></i> Videos</a>
        <a onclick="navigateTo('documents')"><i class="fas fa-file-alt fa-fw"></i> Documents</a>
        <a href="https://princexit470.github.io/Chatus/" target="_blank"><i class="fas fa-comment fa-fw"></i> ChatUs</a>
        <a href="https://princexit470.github.io/QR-studio/" target="_blank"><i class="fas fa-qrcode fa-fw"></i> QR Studio</a>
        <a onclick="navigateTo('about')"><i class="fas fa-info-circle fa-fw"></i> Contact</a>
        <hr class="mobile-divider">
        <div id="mobileLoggedOut">
            <a onclick="showAuth('login')"><i class="fas fa-sign-in-alt fa-fw"></i> Log In</a>
            <a onclick="showAuth('signup')" style="color:var(--gold);"><i class="fas fa-user-plus fa-fw"></i> Sign Up</a>
        </div>
        <div id="mobileLoggedIn" class="hidden"><a onclick="doLogout()"><i class="fas fa-sign-out-alt fa-fw"></i> Logout</a></div>
    </div>

    <div class="main-content">
        <div class="page show" id="page-home">
            <div class="hero-section">
                <h1 class="hero-title">Creative Studio</h1>
                <p class="hero-subtitle">Photography • Cinematography • Live Broadcast</p>
            </div>
            
            <div class="live-section" id="liveSection">
                <div class="viewer-count"><i class="fas fa-eye"></i> <span id="homeViewerCount">0</span></div>
                <div class="live-header"><span class="live-badge offline" id="liveBadge"><i class="fas fa-circle"></i> OFFLINE</span></div>
                
                <div class="live-video-container" id="liveVideoContainer">
                    <div class="coming-soon-overlay" id="liveOverlay">
                        <i class="fas fa-satellite-dish" style="font-size:3rem; color:#333;"></i>
                        <h3 style="color:#555; letter-spacing:2px; font-weight:500;">STREAM OFFLINE</h3>
                    </div>
                </div>
                
                <div class="live-buttons" id="liveAdminButtons" style="display:none;">
                    <button class="nav-btn primary" onclick="document.getElementById('liveStudioModal').classList.add('show')"><i class="fas fa-cog"></i> Live Studio Setup</button>
                    <button class="nav-btn" onclick="openFullscreenCamera()"><i class="fas fa-mobile-alt"></i> Camera Live</button>
                </div>
            </div>
        </div>

        <div class="page" id="page-gallery">
            <h2 class="section-title"><i class="fas fa-images" style="color:var(--gold);"></i> Photos</h2>
            <div class="add-media-bar" id="photoAddBar" style="display:none;"><button class="nav-btn primary" onclick="openAddMediaModal('photo')"><i class="fas fa-upload"></i> Upload Photos</button></div>
            <div class="gallery-grid" id="photoGrid"></div>
        </div>

        <div class="page" id="page-videos">
            <h2 class="section-title"><i class="fas fa-play-circle" style="color:var(--gold);"></i> Videos</h2>
            <div class="add-media-bar" id="videoAddBar" style="display:none;"><button class="nav-btn primary" onclick="openAddMediaModal('video')"><i class="fas fa-upload"></i> Upload Video</button></div>
            <div class="video-feed" id="videoFeed"></div>
        </div>

        <div class="page" id="page-documents">
            <h2 class="section-title"><i class="fas fa-file-pdf" style="color:var(--gold);"></i> Documents</h2>
            <div class="add-media-bar" id="docAddBar" style="display:none;"><button class="nav-btn primary" onclick="openAddMediaModal('doc')"><i class="fas fa-upload"></i> Upload Document</button></div>
            <div class="video-feed" id="docList"></div>
        </div>
        
        <div class="page" id="page-about">
            <div style="text-align:center;padding:4rem;background:var(--surface);border-radius:var(--radius);max-width:600px;margin:auto;">
                <h2 style="color:var(--gold);">Get in Touch</h2>
                <p>princekvishwakarma@yahoo.com</p>
                <div style="margin-top:1rem;"><a href="https://instagram.com/princexit_" target="_blank" style="color:#fff;font-size:2rem;"><i class="fab fa-instagram"></i></a></div>
            </div>
        </div>
    </div>

    <div id="lightbox" class="lightbox">
        <div class="lb-top-bar">
            <div class="lb-counter" id="lbCounter">1 / 10</div>
            <div class="lb-actions">
                <button class="lb-btn" id="lbDownloadBtn"><i class="fas fa-download"></i></button>
                <button class="lb-btn" onclick="closeLB()"><i class="fas fa-times"></i></button>
            </div>
        </div>
        <button class="lb-nav-btn lb-prev" onclick="lbNav(-1)"><i class="fas fa-chevron-left"></i></button>
        <button class="lb-nav-btn lb-next" onclick="lbNav(1)"><i class="fas fa-chevron-right"></i></button>
        <div class="lb-image-wrap" id="lbImageWrap">
            <img id="lbImage" src="">
        </div>
        <div class="lb-title" id="lbTitleText"></div>
    </div>

    <div id="fullscreenCameraUI" style="position:fixed; inset:0; background:#000; z-index:9999; display:none; flex-direction:column;">
        <div style="position:absolute; top:20px; left:20px; right:20px; display:flex; justify-content:space-between; z-index:10;">
            <div style="background:#e74c3c; color:#fff; padding:5px 15px; border-radius:20px; font-weight:bold; font-size:0.9rem; animation:pulse 2s infinite;"><i class="fas fa-circle"></i> LIVE</div>
            <div style="background:rgba(0,0,0,0.7); color:#fff; padding:5px 15px; border-radius:20px; font-size:0.9rem; border:1px solid rgba(255,255,255,0.2);"><i class="fas fa-eye"></i> <span id="camViewerCount">0</span> Viewers</div>
        </div>
        <video id="liveVideoElement" autoplay muted playsinline style="width:100%; height:100%; object-fit:cover;"></video>
        <div style="position:absolute; bottom:30px; left:50%; transform:translateX(-50%); display:flex; gap:20px; background:rgba(0,0,0,0.6); padding:15px 30px; border-radius:40px; backdrop-filter:blur(10px); border:1px solid rgba(255,255,255,0.2);">
            <button id="camPlayBtn" onclick="toggleCameraPlay()" style="display:none; width:50px; height:50px; border-radius:50%; border:none; background:#2ecc71; color:#fff; font-size:1.2rem; cursor:pointer;"><i class="fas fa-play"></i></button>
            <button id="camPauseBtn" onclick="toggleCameraPause()" style="width:50px; height:50px; border-radius:50%; border:none; background:#f39c12; color:#fff; font-size:1.2rem; cursor:pointer;"><i class="fas fa-pause"></i></button>
            <button onclick="stopFullscreenCamera()" style="width:50px; height:50px; border-radius:50%; border:none; background:#e74c3c; color:#fff; font-size:1.2rem; cursor:pointer;"><i class="fas fa-stop"></i></button>
        </div>
    </div>

    <div class="modal-backdrop" id="authModal">
        <div class="modal-dialog">
            <button class="modal-close" onclick="closeModal('authModal')">✕</button>
            <h2 id="authTitle">Welcome Back</h2>
            <div class="input-group" id="authNameGroup" style="display:none;">
                <label>Full Name</label>
                <input type="text" id="authName" placeholder="Enter your name">
            </div>
            <div class="input-group">
                <label>Email Address</label>
                <input type="email" id="authEmail" placeholder="you@example.com">
            </div>
            <div class="input-group">
                <label>Password</label>
                <input type="password" id="authPass" placeholder="••••••••">
            </div>
            <div class="input-group" id="authConfirmPassGroup" style="display:none;">
                <label>Confirm Password</label>
                <input type="password" id="authConfirmPass" placeholder="••••••••">
            </div>
            <button class="modal-submit" id="authSubmit" onclick="doAuth()">Log In</button>
            <p style="text-align:center;margin-top:1.5rem;font-size:0.9rem;color:var(--dim);" id="authSwitch">New here? <span style="color:var(--gold);cursor:pointer;font-weight:600;" onclick="switchAuthMode('signup')">Create account</span></p>
        </div>
    </div>

    <div class="modal-backdrop" id="addMediaModal">
        <div class="modal-dialog">
            <button class="modal-close" onclick="closeModal('addMediaModal')">✕</button>
            <h2 id="addMediaTitle">Upload Media</h2>
            
            <div class="input-group">
                <label>Media Title (Optional for Multi-upload)</label>
                <input type="text" id="addMediaName" placeholder="e.g. Wedding Cinematic">
            </div>
            
            <div class="input-group">
                <label>Select Files</label>
                <input type="file" id="addMediaFile" multiple style="padding:0.5rem; background:transparent;">
            </div>

            <div class="input-group" id="thumbnailGroup" style="display:none;">
                <label>Custom Thumbnail/Poster (Optional)</label>
                <input type="file" id="addMediaThumb" accept="image/*" style="padding:0.5rem; background:transparent;">
                <small style="color:var(--gold); display:block; margin-top:5px;">If left blank, AI will auto-extract a frame from the middle of the video!</small>
            </div>

            <button class="modal-submit" onclick="submitMedia()">Start Upload</button>
            <input type="hidden" id="addMediaType" value="photo">
        </div>
    </div>

    <div class="modal-backdrop" id="liveStudioModal">
        <div class="modal-dialog">
            <button class="modal-close" onclick="closeModal('liveStudioModal')">✕</button>
            <h2>Live Studio Setup</h2>
            <div class="input-group">
                <label>Stream Title</label>
                <input type="text" id="liveTitle" placeholder="Live Broadcast Name" value="Special Live Event">
            </div>
            <div class="input-group">
                <label>Stream Source (Platform)</label>
                <select id="liveSourceType" onchange="toggleLiveInputs()">
                    <option value="obs">OBS / Cloudinary (RTMP)</option>
                    <option value="youtube">YouTube Live Link</option>
                    <option value="custom">Custom HLS (.m3u8)</option>
                </select>
            </div>
            
            <div id="liveObsDetails" style="background:#1a1a1a; padding:15px; border-radius:10px; margin-bottom:15px; border:1px dashed var(--dim);">
                <p style="font-size:0.8rem; color:var(--dim); margin-bottom:10px;">Copy these details into OBS Settings > Stream</p>
                <label style="font-size:0.8rem; color:#fff;">Server:</label>
                <input type="text" value="rtmp://live.cloudinary.com/streams" readonly style="margin-bottom:10px; padding:0.6rem; background:#000;">
                <label style="font-size:0.8rem; color:#fff;">Stream Key:</label>
                <input type="text" value="T4e1jE08Epr7CNzf4tgjEL5o6Ks4Q4" readonly style="padding:0.6rem; background:#000;">
            </div>
            
            <div class="input-group" id="liveUrlGroup" style="display:none;">
                <label>Direct Stream URL</label>
                <input type="text" id="liveStreamUrlInput" placeholder="https://youtube.com/... or https://...m3u8">
            </div>

            <button class="modal-submit" onclick="saveLiveSettings()"><i class="fas fa-broadcast-tower"></i> Go Live on Website</button>
            <button class="nav-btn" style="width:100%;margin-top:10px; background:rgba(231,76,60,0.1); color:#e74c3c; border:none;" onclick="clearLive()">Stop Current Stream</button>
        </div>
    </div>

    <div class="upload-manager" id="uploadManager">
        <div class="um-header" onclick="document.getElementById('umList').style.display = document.getElementById('umList').style.display==='none'?'flex':'none'">
            <strong style="color:#fff; font-size:0.95rem;" id="umTitle">Uploads</strong>
            <button onclick="event.stopPropagation(); clearUploads()" style="background:none;border:none;color:var(--dim);cursor:pointer;"><i class="fas fa-trash"></i></button>
        </div>
        <div class="um-list" id="umList" style="display:flex;"></div>
    </div>

    <script>
        // CONFIG
        const SB = window.supabase.createClient('https://wvlvamuehbyfdsozjczk.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Ind2bHZhbXVlaGJ5ZmRzb3pqY3prIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzU1NTM4MDAsImV4cCI6MjA5MTEyOTgwMH0.oaF4wymSu0ePwQSp3moV0sKZVgz5f50ILun5iaU816A');
        const ADMIN_EMAIL = "princekvishwakarma@yahoo.com";
        const CLOUDINARY_CLOUD_NAME = "dl4pbclvi";
        const CLOUDINARY_UPLOAD_PRESET = "princexit_upload";
        
        let USER = null, AUTH_MODE = 'login';
        let ALL_PHOTOS = []; // For Lightbox Array

        // TOAST
        function TOAST(m, t='info') {
            const b = document.getElementById('toastContainer'), d = document.createElement('div');
            d.className = 'toast ' + t; d.innerHTML = m; b.appendChild(d);
            setTimeout(() => { d.style.animation = 'toastIn 0.4s ease reverse forwards'; setTimeout(() => d.remove(), 400); }, 3500);
        }

        // INIT & REALTIME
        window.onload = async () => {
            const { data } = await SB.auth.getSession();
            updateUI(data.session ? data.session.user : null);
            fetchMedia();
            setupRealtimePresence();
            
            // Hash Navigation
            const hash = window.location.hash.replace('#', '');
            if(hash) navigateTo(hash);

            // Mobile Menu
            document.getElementById('hamburgerBtn').onclick = () => {
                document.getElementById('mobileMenu').classList.add('open');
                document.getElementById('mobileOverlay').classList.add('show');
            };
            document.getElementById('mobileOverlay').onclick = () => {
                document.getElementById('mobileMenu').classList.remove('open');
                document.getElementById('mobileOverlay').classList.remove('show');
            };
        };

        let liveChannel = null;
        function setupRealtimePresence() {
            liveChannel = SB.channel('live_viewers', { config: { presence: { key: 'viewer' } } });
            liveChannel.on('presence', { event: 'sync' }, () => {
                const count = Object.keys(liveChannel.presenceState()).length;
                document.getElementById('homeViewerCount').innerText = count;
                document.getElementById('camViewerCount').innerText = count;
            }).subscribe(async (status) => {
                if (status === 'SUBSCRIBED') await liveChannel.track({ online_at: new Date().toISOString() });
            });
        }

        // AUTH
        function switchAuthMode(mode) {
            AUTH_MODE = mode;
            const isSignup = mode === 'signup';
            document.getElementById('authNameGroup').style.display = isSignup ? 'block' : 'none';
            document.getElementById('authConfirmPassGroup').style.display = isSignup ? 'block' : 'none';
            document.getElementById('authTitle').innerText = isSignup ? 'Create Account' : 'Welcome Back';
            document.getElementById('authSubmit').innerText = isSignup ? 'Sign Up' : 'Log In';
            document.getElementById('authSwitch').innerHTML = isSignup ? 'Have account? <span style="color:var(--gold);cursor:pointer;font-weight:600;" onclick="switchAuthMode(\'login\')">Log In</span>' : 'New here? <span style="color:var(--gold);cursor:pointer;font-weight:600;" onclick="switchAuthMode(\'signup\')">Create account</span>';
        }

        function showAuth(mode) { switchAuthMode(mode); document.getElementById('authModal').classList.add('show'); }
        function closeModal(id) { document.getElementById(id).classList.remove('show'); }

        async function doAuth() {
            const email = document.getElementById('authEmail').value, pass = document.getElementById('authPass').value;
            if(!email || !pass) return TOAST('Fill required fields', 'error');
            
            document.getElementById('authSubmit').disabled = true;
            document.getElementById('authSubmit').innerText = "Processing...";

            try {
                let res;
                if(AUTH_MODE === 'signup') {
                    const name = document.getElementById('authName').value, cpass = document.getElementById('authConfirmPass').value;
                    if(!name) throw new Error("Full name required");
                    if(pass !== cpass) throw new Error("Passwords do not match");
                    res = await SB.auth.signUp({ email, password: pass, options: { data: { full_name: name } }});
                } else {
                    res = await SB.auth.signInWithPassword({ email, password: pass });
                }
                if(res.error) throw res.error;
                TOAST(AUTH_MODE === 'signup' ? 'Account Created!' : 'Logged In', 'success');
                closeModal('authModal');
                updateUI(res.data.user);
            } catch(e) {
                TOAST(e.message, 'error');
            } finally {
                document.getElementById('authSubmit').disabled = false;
                document.getElementById('authSubmit').innerText = AUTH_MODE === 'signup' ? 'Sign Up' : 'Log In';
            }
        }

        async function doLogout() { await SB.auth.signOut(); updateUI(null); TOAST('Logged Out'); document.getElementById('profileDropdown').classList.remove('show'); }

        function updateUI(u) {
            USER = u; const isAdm = USER && USER.email === ADMIN_EMAIL;
            document.getElementById('desktopAuthBtns').style.display = USER ? 'none' : 'block';
            document.getElementById('profileChip').style.display = USER ? 'flex' : 'none';
            document.getElementById('mobileLoggedOut').style.display = USER ? 'none' : 'block';
            document.getElementById('mobileLoggedIn').style.display = USER ? 'block' : 'none';
            
            if(USER) document.getElementById('profileEmailText').innerText = USER.user_metadata?.full_name || USER.email.split('@')[0];
            
            document.getElementById('photoAddBar').style.display = isAdm ? 'flex' : 'none';
            document.getElementById('videoAddBar').style.display = isAdm ? 'flex' : 'none';
            document.getElementById('docAddBar').style.display = isAdm ? 'flex' : 'none';
            document.getElementById('liveAdminButtons').style.display = isAdm ? 'flex' : 'none';
        }

        // NAVIGATION
        function navigateTo(page) {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('show'));
            document.getElementById('page-' + page).classList.add('show');
            document.querySelectorAll('.nav-links a').forEach(a => a.classList.remove('active'));
            const link = document.querySelector(`[data-page="${page}"]`); if(link) link.classList.add('active');
            window.location.hash = page;
            window.scrollTo({top:0, behavior:'smooth'});
            document.getElementById('mobileMenu').classList.remove('open');
            document.getElementById('mobileOverlay').classList.remove('show');
        }

        // FETCH MEDIA (Optimized with Lazy Load and Smart Thumbnails)
        async function fetchMedia() {
            const { data } = await SB.from('media').select('*').order('created_at', { ascending: false });
            if(!data) return;
            
            const photos = data.filter(m => m.type === 'photo');
            const videos = data.filter(m => m.type === 'video');
            const docs = data.filter(m => m.type === 'doc');
            const live = data.filter(m => m.type === 'live');
            const admin = USER && USER.email === ADMIN_EMAIL;
            
            ALL_PHOTOS = photos; // Save globally for lightbox
            
            // Render Photos
            document.getElementById('photoGrid').innerHTML = photos.map((p, index) => `
                <div class="gallery-item" onclick="openLB(${index})">
                    <img src="${p.url}" loading="lazy">
                    <div class="media-title">${p.title}</div>
                    ${admin?`<button class="del-btn" onclick="event.stopPropagation(); delMedia('${p.id}')"><i class="fas fa-trash"></i></button>`:''}
                </div>
            `).join('');
            
            // Render Videos (With Auto/Custom Thumbnails & Preload=none)
            document.getElementById('videoFeed').innerHTML = videos.map(v => {
                // Split trick: if url is "VIDEO_URL|THUMB_URL"
                let vidSrc = v.url, poster = '';
                if(v.url.includes('|')) {
                    const parts = v.url.split('|');
                    vidSrc = parts[0]; poster = parts[1];
                } else if (!vidSrc.includes('youtube')) {
                    // Auto generate Cloudinary Thumbnail (.jpg replaces .mp4)
                    poster = vidSrc.replace(/\.(mp4|webm|mov|mkv)$/i, '.jpg');
                }
                
                let playerHtml = vidSrc.includes('youtube') 
                    ? `<iframe src="${vidSrc.replace('watch?v=','embed/')}" loading="lazy"></iframe>`
                    : `<video src="${vidSrc}" poster="${poster}" controls preload="none"></video>`;
                
                return `
                <div class="video-card-yt">
                    <div class="video-thumb-yt">${playerHtml}</div>
                    <div class="video-info-yt">
                        <h3 style="font-size:1.1rem; margin-bottom:10px;">${v.title}</h3>
                        <p style="font-size:0.8rem; color:var(--dim);">Added: ${new Date(v.created_at).toLocaleDateString()}</p>
                        ${admin?`<button class="nav-btn" style="background:rgba(231,76,60,0.1); color:#e74c3c; border:none; margin-top:auto; align-self:flex-start;" onclick="delMedia('${v.id}')"><i class="fas fa-trash"></i> Delete Video</button>`:''}
                    </div>
                </div>`;
            }).join('');
            
            // Render Docs
            document.getElementById('docList').innerHTML = docs.map(d => `
                <div class="video-card-yt" style="padding:15px; flex-direction:row; justify-content:space-between; align-items:center;">
                    <span style="font-weight:600;"><i class="fas fa-file-pdf" style="color:#e74c3c; font-size:1.2rem; margin-right:10px;"></i> ${d.title}</span>
                    <div style="display:flex; gap:10px;">
                        <a href="${d.url}" target="_blank" class="nav-btn primary" style="text-decoration:none;"><i class="fas fa-download"></i> Download</a>
                        ${admin?`<button class="del-btn" style="position:static;" onclick="delMedia('${d.id}')"><i class="fas fa-trash"></i></button>`:''}
                    </div>
                </div>
            `).join('');

            // LIVE SECTION
            const lContainer = document.getElementById('liveVideoContainer');
            const lBadge = document.getElementById('liveBadge');
            const lOverlay = document.getElementById('liveOverlay');
            lContainer.innerHTML = '';
            
            if(live.length > 0) {
                lBadge.classList.remove('offline'); lBadge.innerHTML = `<i class="fas fa-circle"></i> ${live[0].title || 'LIVE NOW'}`;
                if(live[0].url.includes('youtube')) {
                    lContainer.innerHTML = `<iframe src="${live[0].url.replace('watch?v=','embed/')}?autoplay=1" allowfullscreen></iframe>`;
                } else if(live[0].url.includes('cloudinary') || live[0].url.includes('hls')) {
                    lContainer.innerHTML = `<iframe src="${live[0].url}" allowfullscreen></iframe>`;
                } else {
                    lContainer.innerHTML = `<video src="${live[0].url}" controls autoplay playsinline></video>`;
                }
            } else {
                lBadge.classList.add('offline'); lBadge.innerHTML = '<i class="fas fa-circle"></i> OFFLINE';
                lContainer.appendChild(lOverlay);
            }
        }

        async function delMedia(id) {
            if(!confirm('Delete permanently?')) return;
            await SB.from('media').delete().eq('id', id);
            fetchMedia(); TOAST('Deleted', 'success');
        }

        // =====================================
        // ADVANCED LIGHTBOX (Animations & Swipes)
        // =====================================
        let lbIndex = 0;
        const lbElement = document.getElementById('lightbox');
        const lbImageWrap = document.getElementById('lbImageWrap');
        
        function openLB(index) {
            lbIndex = index;
            updateLB();
            lbElement.classList.add('show');
            document.body.classList.add('no-scroll');
        }

        function closeLB() {
            lbElement.classList.remove('show');
            document.body.classList.remove('no-scroll');
        }

        function updateLB() {
            const photo = ALL_PHOTOS[lbIndex];
            document.getElementById('lbImage').src = photo.url;
            document.getElementById('lbCounter').innerText = `${lbIndex + 1} / ${ALL_PHOTOS.length}`;
            document.getElementById('lbTitleText').innerText = photo.title;
            document.getElementById('lbDownloadBtn').onclick = () => window.open(photo.url, '_blank');
            
            // Animation reset
            lbImageWrap.style.transform = 'scale(0.95)';
            setTimeout(() => lbImageWrap.style.transform = 'scale(1)', 50);
        }

        function lbNav(dir) {
            lbIndex += dir;
            if (lbIndex < 0) lbIndex = ALL_PHOTOS.length - 1;
            if (lbIndex >= ALL_PHOTOS.length) lbIndex = 0;
            updateLB();
        }

        // Swipe Gestures
        let touchStartY = 0; let touchStartX = 0;
        lbElement.addEventListener('touchstart', e => { 
            touchStartX = e.changedTouches[0].screenX; 
            touchStartY = e.changedTouches[0].screenY; 
        });
        lbElement.addEventListener('touchend', e => {
            let dx = e.changedTouches[0].screenX - touchStartX;
            let dy = e.changedTouches[0].screenY - touchStartY;
            
            // Swipe Down to Close
            if (dy > 120 && Math.abs(dx) < 80) closeLB();
            // Swipe Left/Right to Navigate
            else if (dx > 80) lbNav(-1);
            else if (dx < -80) lbNav(1);
        });

        // LIVE STUDIO SETTINGS
        function toggleLiveInputs() {
            const type = document.getElementById('liveSourceType').value;
            document.getElementById('liveObsDetails').style.display = type === 'obs' ? 'block' : 'none';
            document.getElementById('liveUrlGroup').style.display = type === 'obs' ? 'none' : 'block';
        }

        async function saveLiveSettings() {
            const title = document.getElementById('liveTitle').value;
            const type = document.getElementById('liveSourceType').value;
            let url = '';
            
            if(type === 'obs') {
                url = 'https://player.cloudinary.com/embed/?cloud_name=dl4pbclvi&public_id=live_stream_f1e3551f3179487a9bc883505c9c2518_hls&profile=cld-live-streaming';
            } else {
                url = document.getElementById('liveStreamUrlInput').value;
                if(!url) return TOAST('Please enter stream URL', 'error');
            }
            
            await SB.from('media').delete().eq('type', 'live');
            await SB.from('media').insert([{ type:'live', title: title, url: url }]);
            TOAST('Website is now LIVE!', 'success'); 
            closeModal('liveStudioModal'); fetchMedia();
        }
        async function clearLive() { await SB.from('media').delete().eq('type', 'live'); TOAST('Stream Stopped'); closeModal('liveStudioModal'); fetchMedia(); }

        // UPLOAD MANAGER & MEDIA UPLOAD
        function openAddMediaModal(type) {
            document.getElementById('addMediaType').value = type;
            document.getElementById('addMediaName').value = '';
            document.getElementById('addMediaFile').value = '';
            document.getElementById('thumbnailGroup').style.display = type === 'video' ? 'block' : 'none';
            document.getElementById('addMediaModal').classList.add('show');
        }

        let uploads = [];
        function renderUM() {
            const um = document.getElementById('uploadManager');
            if(uploads.length === 0) { um.classList.remove('active'); return; }
            um.classList.add('active');
            document.getElementById('umTitle').innerText = `Uploads (${uploads.filter(u=>u.status==='uploading').length})`;
            document.getElementById('umList').innerHTML = uploads.map(u => `
                <div class="um-item">
                    <div style="display:flex; justify-content:space-between; color:${u.status==='error'?'#e74c3c':(u.status==='done'?'#2ecc71':'#fff')}; font-size:0.85rem;">
                        <span style="white-space:nowrap; overflow:hidden; text-overflow:ellipsis; max-width:70%;">${u.name}</span>
                        <span style="font-weight:bold;">${u.status==='uploading' ? u.progress+'%' : (u.status==='done'?'Success':'Failed')}</span>
                    </div>
                    <div class="um-progress-bar"><div class="um-progress-fill" style="width:${u.status==='error'?100:u.progress}%; background:${u.status==='error'?'#e74c3c':(u.status==='done'?'#2ecc71':'var(--gold)')}"></div></div>
                </div>
            `).reverse().join('');
        }
        function clearUploads() { uploads = uploads.filter(u=>u.status==='uploading'); renderUM(); }

        async function submitMedia() {
            const type = document.getElementById('addMediaType').value;
            const files = document.getElementById('addMediaFile').files;
            const customTitle = document.getElementById('addMediaName').value;
            const thumbFile = document.getElementById('addMediaThumb') ? document.getElementById('addMediaThumb').files[0] : null;

            if(files.length === 0) return TOAST('Please select files', 'error');
            closeModal('addMediaModal');
            
            // Helper function to upload to Cloudinary directly via API
            const uploadToCloudinary = (file, onProgress) => {
                return new Promise((resolve, reject) => {
                    const fd = new FormData();
                    fd.append('file', file); fd.append('upload_preset', CLOUDINARY_UPLOAD_PRESET); fd.append('cloud_name', CLOUDINARY_CLOUD_NAME);
                    const xhr = new XMLHttpRequest();
                    xhr.open('POST', `https://api.cloudinary.com/v1_1/${CLOUDINARY_CLOUD_NAME}/auto/upload`);
                    if(onProgress) xhr.upload.onprogress = onProgress;
                    xhr.onload = () => { if(xhr.status>=200 && xhr.status<300) resolve(JSON.parse(xhr.responseText).secure_url); else reject(xhr.responseText); };
                    xhr.onerror = () => reject('Network Error');
                    xhr.send(fd);
                });
            };

            for(let i=0; i<files.length; i++) {
                const file = files[i];
                const id = Date.now()+i;
                // If single file selected, use custom title. Else use filename.
                const finalTitle = (files.length === 1 && customTitle) ? customTitle : file.name;
                
                uploads.push({ id, name: finalTitle, progress: 0, status: 'uploading' });
                renderUM();
                document.getElementById('umList').style.display = 'flex'; // Ensure UI open
                
                try {
                    let thumbUrl = null;
                    if(type === 'video' && thumbFile && i === 0) {
                        // Upload custom thumbnail first
                        thumbUrl = await uploadToCloudinary(thumbFile, null);
                    }

                    // Upload actual file with progress tracking
                    const fileUrl = await uploadToCloudinary(file, (e) => {
                        if(e.lengthComputable) {
                            const idx = uploads.findIndex(u=>u.id===id);
                            if(idx>-1) { uploads[idx].progress = Math.round((e.loaded/e.total)*100); renderUM(); }
                        }
                    });

                    // Save to Supabase
                    let finalUrl = fileUrl;
                    if(thumbUrl) finalUrl = `${fileUrl}|${thumbUrl}`; // Split trick for custom thumbnail
                    
                    await SB.from('media').insert([{ type, title: finalTitle, url: finalUrl }]);
                    
                    const idx = uploads.findIndex(u=>u.id===id);
                    if(idx>-1) { uploads[idx].status = 'done'; uploads[idx].progress = 100; renderUM(); fetchMedia(); }
                    
                } catch(error) {
                    const idx = uploads.findIndex(u=>u.id===id);
                    if(idx>-1) { uploads[idx].status = 'error'; renderUM(); TOAST('Error: ' + error, 'error'); }
                }
            }
        }

        // FULL SCREEN CAMERA LIVE UI
        let camStream = null;
        async function openFullscreenCamera() {
            try {
                camStream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'environment' }, audio: true });
                document.getElementById('liveVideoElement').srcObject = camStream;
                document.getElementById('fullscreenCameraUI').classList.add('show');
                document.getElementById('camPlayBtn').style.display = 'none';
                document.getElementById('camPauseBtn').style.display = 'block';
                TOAST('Camera Feed Started', 'success');
            } catch(e) { TOAST('Camera permission denied', 'error'); }
        }

        function toggleCameraPause() {
            if(camStream) {
                camStream.getVideoTracks()[0].enabled = false;
                document.getElementById('camPauseBtn').style.display = 'none';
                document.getElementById('camPlayBtn').style.display = 'block';
                TOAST('Stream Paused', 'info');
            }
        }

        function toggleCameraPlay() {
            if(camStream) {
                camStream.getVideoTracks()[0].enabled = true;
                document.getElementById('camPlayBtn').style.display = 'none';
                document.getElementById('camPauseBtn').style.display = 'block';
                TOAST('Stream Resumed', 'success');
            }
        }

        function stopFullscreenCamera() {
            if(camStream) camStream.getTracks().forEach(t => t.stop());
            document.getElementById('fullscreenCameraUI').classList.remove('show');
            TOAST('Camera Feed Stopped', 'info');
        }
    </script>
</body>
</html>
