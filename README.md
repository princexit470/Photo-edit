<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>XIT | Creative Studio</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
    <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
    
    <style>
        :root {
            --gold: #d4af37; --gold-hover: #f3b965; --bg: #0a0a0a; --surface: #141414;
            --surface-hover: #1e1e1e; --border: rgba(255, 255, 255, 0.08); --text: #f5f5f5;
            --dim: #999; --radius: 12px; --transition: 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
        }
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { font-family: 'Inter', system-ui, sans-serif; background: var(--bg); color: var(--text); min-height: 100vh; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        body.no-scroll { overflow: hidden; }

        /* TOAST */
        .toast-container { position: fixed; top: 20px; right: 20px; z-index: 10000; display: flex; flex-direction: column; gap: 10px; pointer-events: none; }
        .toast { padding: 12px 20px; border-radius: 8px; color: #fff; font-weight: 500; font-size: 0.9rem; background: rgba(15,15,15,0.95); backdrop-filter: blur(10px); animation: toastIn 0.3s ease forwards; box-shadow: 0 10px 30px rgba(0,0,0,0.6); border-left: 4px solid var(--gold); }
        .toast.success { border-left-color: #2ecc71; } .toast.error { border-left-color: #e74c3c; }
        @keyframes toastIn { from { transform: translateX(120%); opacity: 0; } to { transform: translateX(0); opacity: 1; } }

        /* NAVBAR */
        .navbar { position: sticky; top: 0; z-index: 500; background: rgba(10,10,10,0.85); backdrop-filter: saturate(180%) blur(20px); padding: 0.8rem 1.5rem; display: flex; align-items: center; border-bottom: 1px solid var(--border); }
        .nav-logo { font-size: 1.5rem; font-weight: 900; color: var(--gold); cursor: pointer; letter-spacing: 1px; }
        .nav-links { display: flex; gap: 0.5rem; list-style: none; align-items: center; margin-left: 20px; }
        .nav-links a { color: var(--dim); text-decoration: none; padding: 0.5rem 1rem; border-radius: 20px; font-size: 0.9rem; transition: var(--transition); cursor: pointer; }
        .nav-links a:hover, .nav-links a.active { color: #fff; background: rgba(255,255,255,0.08); }
        .nav-right { display: flex; align-items: center; gap: 8px; margin-left: auto; }
        
        .nav-btn { padding: 0.4rem 0.8rem; border-radius: 20px; border: 1px solid var(--border); background: transparent; color: #fff; cursor: pointer; font-size: 0.75rem; font-weight: 600; transition: var(--transition); }
        .nav-btn.primary { background: var(--gold); color: #000; border: none; }
        .nav-btn:hover { background: rgba(255,255,255,0.05); } .nav-btn.primary:hover { background: var(--gold-hover); transform: translateY(-1px); }

        /* PROFILE DROPDOWN */
        .profile-wrap { position: relative; }
        .profile-chip { display: flex; align-items: center; gap: 8px; background: rgba(255,255,255,0.05); padding: 0.4rem 1rem; border-radius: 20px; cursor: pointer; border: 1px solid var(--border); }
        .profile-chip span { color: var(--gold); font-size: 0.85rem; font-weight: 600; max-width: 100px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
        .profile-dropdown { position: absolute; top: 110%; right: 0; background: #111; border-radius: 12px; padding: 0.5rem; border: 1px solid var(--border); z-index: 60; display: none; min-width: 150px; box-shadow: 0 10px 40px rgba(0,0,0,0.8); }
        .profile-dropdown.show { display: block; }
        .profile-dropdown a { display: block; padding: 0.7rem 1rem; color: var(--dim); cursor: pointer; border-radius: 8px; font-size: 0.85rem; }
        .profile-dropdown a:hover { background: rgba(255,255,255,0.05); color: #fff; }

        /* MOBILE MENU */
        .hamburger { display: none; background: none; border: none; flex-direction: column; gap: 5px; cursor: pointer; padding: 5px; }
        .hamburger span { width: 22px; height: 2px; background: #fff; border-radius: 2px; }
        .mobile-menu { position: fixed; top: 0; right: -320px; width: 280px; height: 100vh; background: rgba(10,10,10,0.98); backdrop-filter: blur(20px); z-index: 600; padding: 5rem 1.5rem 2rem; transition: right 0.3s ease; border-left: 1px solid var(--border); display: flex; flex-direction: column; }
        .mobile-menu.open { right: 0; }
        .mobile-menu a { display: block; color: var(--dim); padding: 1rem; text-decoration: none; border-radius: 12px; font-weight: 500; cursor: pointer; }
        .mobile-menu a:hover { background: rgba(255,255,255,0.05); color: #fff; }
        .mobile-profile-box { margin: 15px 0; padding: 15px; background: rgba(212, 175, 55, 0.1); border: 1px solid var(--gold); border-radius: 12px; text-align: center; color: var(--gold); font-weight: bold; }
        .overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.8); z-index: 550; display: none; }
        .overlay.show { display: block; }

        /* PAGES */
        .main-content { padding: 2rem; max-width: 1200px; margin: 0 auto; min-height: 80vh; }
        .page { display: none; animation: pageFade 0.4s ease forwards; opacity: 0; }
        .page.show { display: block; opacity: 1; }
        @keyframes pageFade { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        
        /* HOME (XIT ANIMATION) */
        .home-container { display: flex; flex-direction: column; align-items: center; justify-content: center; min-height: 70vh; }
        .xit-logo { font-size: 8rem; font-weight: 900; letter-spacing: 12px; user-select: none; transition: transform 0.1s ease-out; background: linear-gradient(90deg, #fff, var(--gold), #fff); background-size: 200% auto; color: transparent; -webkit-background-clip: text; background-clip: text; animation: shine 3s linear infinite; cursor: crosshair; }
        @keyframes shine { to { background-position: 200% center; } }
        .hero-subtitle { color: var(--dim); font-size: 1rem; letter-spacing: 3px; text-transform: uppercase; margin-top: 10px; font-weight: 500; }

        /* SEARCH BAR */
        .search-container { display: flex; align-items: center; background: #111; border: 1px solid var(--border); border-radius: 20px; padding: 0.6rem 1.2rem; margin-bottom: 1.5rem; }
        .search-container i { color: var(--dim); margin-right: 10px; }
        .search-container input { background: transparent; border: none; color: #fff; width: 100%; outline: none; font-size: 0.9rem; }

        /* MEDIA GRIDS */
        .section-title { font-size: 1.8rem; margin-bottom: 1.5rem; font-weight: 700; color: #fff; }
        .add-media-bar { display: flex; gap: 10px; margin-bottom: 1.5rem; }
        
        .gallery-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); gap: 1rem; }
        .gallery-item { position: relative; border-radius: 12px; overflow: hidden; cursor: pointer; background: #000; aspect-ratio: 4/5; border: 1px solid var(--border); transition: transform 0.3s; }
        .gallery-item:hover { transform: scale(1.02); border-color: var(--gold); }
        .gallery-item img { width: 100%; height: 100%; object-fit: cover; }
        .media-title { position: absolute; bottom: 0; left: 0; right: 0; background: linear-gradient(transparent, rgba(0,0,0,0.9)); color: #fff; padding: 30px 15px 15px; font-size: 0.85rem; }

        /* 🔥 STRICT 16:9 VIDEO FEED */
        .video-feed { display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr)); gap: 1.5rem; }
        .video-card-yt { display: flex; flex-direction: column; background: var(--surface); border-radius: 12px; border: 1px solid var(--border); overflow: hidden; }
        .video-thumb-yt { width: 100%; aspect-ratio: 16/9; background: #000; position: relative; overflow: hidden; }
        .video-thumb-yt video, .video-thumb-yt iframe { position: absolute; top: 0; left: 0; width: 100%; height: 100%; border: none; object-fit: contain; }
        .vid-duration { position: absolute; bottom: 8px; right: 8px; background: rgba(0,0,0,0.8); color: #fff; padding: 3px 6px; border-radius: 4px; font-size: 0.75rem; font-weight: 600; pointer-events: none; }
        .video-info-yt { padding: 1rem; display: flex; flex-direction: column; gap: 10px; }
        .video-info-yt h3 { font-size: 1rem; font-weight: 600; color: #fff; margin:0; }
        
        .action-group { display: flex; gap: 5px; }
        .action-group button { padding: 5px 10px; border-radius: 6px; border: none; cursor: pointer; font-size: 0.8rem; font-weight: 600; display: flex; align-items: center; gap: 5px; }
        .btn-edit { background: rgba(52, 152, 219, 0.1); color: #3498db; }
        .btn-del { background: rgba(231, 76, 60, 0.1); color: #e74c3c; }

        /* LIVE SECTION */
        .live-admin-top { display: flex; gap: 10px; margin-bottom: 20px; flex-wrap: wrap; background: #111; padding: 15px; border-radius: 12px; border: 1px dashed var(--gold); align-items: center; }
        .live-section { margin: 0 auto; max-width: 900px; padding: 1.5rem; background: var(--surface); border-radius: var(--radius); border: 1px solid var(--border); position: relative; box-shadow: 0 10px 40px rgba(0,0,0,0.4); }
        .live-header-bar { display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px; }
        .live-badge { background: #e74c3c; color: #fff; padding: 0.4rem 1rem; border-radius: 20px; font-size: 0.8rem; font-weight: 700; animation: pulse 2s infinite; display: inline-flex; align-items: center; gap: 6px; }
        .live-badge.offline { background: #333; color: #888; animation: none; }
        .viewer-count { background: rgba(255,255,255,0.1); color: #fff; padding: 0.4rem 1rem; border-radius: 20px; font-size: 0.85rem; display: flex; align-items: center; gap: 6px; border: 1px solid var(--border); }
        .live-video-container { width: 100%; aspect-ratio: 16/9; background: #000; border-radius: 12px; overflow: hidden; position: relative; border: 1px solid var(--border); }
        .live-video-container iframe, .live-video-container video { position: absolute; top: 0; left: 0; width: 100%; height: 100%; border: none; object-fit: contain; }
        .coming-soon-overlay { position: absolute; inset: 0; display: flex; align-items: center; justify-content: center; background: #050505; flex-direction: column; gap: 15px; z-index: 2; }

        /* FULL SCREEN CAMERA UI */
        #fullscreenCameraUI { position: fixed; inset: 0; background: #000; z-index: 99999; display: none; flex-direction: column; justify-content: center; align-items: center; }
        #fullscreenCameraUI.show { display: flex; }
        #liveVideoElement { width: 100vw; height: 100vh; object-fit: cover; position: absolute; top: 0; left: 0; }
        .cam-controls { position: absolute; bottom: 40px; display: flex; gap: 20px; background: rgba(0,0,0,0.6); padding: 15px 30px; border-radius: 40px; backdrop-filter: blur(10px); border: 1px solid rgba(255,255,255,0.2); z-index: 10; }
        .cam-btn { width: 60px; height: 60px; border-radius: 50%; border: none; font-size: 1.5rem; cursor: pointer; color: #fff; display: flex; align-items: center; justify-content: center; }
        .cam-btn-play { background: #2ecc71; } .cam-btn-pause { background: #f39c12; } .cam-btn-stop { background: #e74c3c; }

        /* LIGHTBOX */
        .lightbox { position: fixed; inset: 0; background: rgba(0,0,0,0.98); z-index: 9999; display: flex; align-items: center; justify-content: center; flex-direction: column; opacity: 0; pointer-events: none; transition: opacity 0.3s ease; }
        .lightbox.show { opacity: 1; pointer-events: auto; }
        .lb-image-wrap { position: relative; width: 100%; height: 80vh; display: flex; align-items: center; justify-content: center; }
        .lightbox img { max-width: 95vw; max-height: 80vh; border-radius: 8px; object-fit: contain; box-shadow: 0 10px 40px rgba(0,0,0,0.5); user-select: none; transition: transform 0.3s ease; }
        .lb-top-bar { position: absolute; top: 0; left: 0; right: 0; padding: 20px; display: flex; justify-content: space-between; z-index: 10; background: linear-gradient(rgba(0,0,0,0.8), transparent); }
        .lb-counter { color: #fff; font-size: 1rem; font-weight: 500; }
        .lb-actions { display: flex; gap: 15px; }
        .lb-btn { background: rgba(255,255,255,0.1); border: none; color: #fff; width: 40px; height: 40px; border-radius: 50%; cursor: pointer; font-size: 1.1rem; }
        .lb-nav-btn { position: absolute; top: 50%; transform: translateY(-50%); background: transparent; border: none; color: rgba(255,255,255,0.5); font-size: 3rem; cursor: pointer; z-index: 10; padding: 20px; }
        .lb-prev { left: 10px; } .lb-next { right: 10px; }

        /* MODALS (CRITICAL CSS FIX) */
        .modal-backdrop { position: fixed; inset: 0; background: rgba(0,0,0,0.85); z-index: 6000; display: none; align-items: center; justify-content: center; backdrop-filter: blur(8px); }
        .modal-backdrop.show { display: flex; }
        .modal-dialog { background: #111; border-radius: 16px; padding: 2.5rem; width: 90%; max-width: 450px; border: 1px solid var(--border); position: relative; box-shadow: 0 20px 50px rgba(0,0,0,0.8); max-height: 90vh; overflow-y: auto; }
        .modal-dialog h2 { color: #fff; text-align: center; margin-bottom: 1.5rem; font-size: 1.4rem; }
        .input-group { margin-bottom: 1rem; }
        .input-group label { display: block; font-size: 0.85rem; color: var(--dim); margin-bottom: 6px; }
        .modal-dialog input, .modal-dialog textarea, .modal-dialog select { width: 100%; padding: 0.9rem; border-radius: 10px; border: 1px solid var(--border); background: #000; color: #fff; outline: none; font-size: 0.95rem; }
        .modal-dialog input:focus { border-color: var(--gold); }
        .modal-submit { width: 100%; padding: 1rem; margin-top: 1rem; border-radius: 10px; border: none; background: var(--gold); color: #000; font-weight: 700; font-size: 1rem; cursor: pointer; }
        .modal-close { position: absolute; top: 15px; right: 20px; background: none; border: none; color: var(--dim); font-size: 1.2rem; cursor: pointer; }
        
        .tabs { display: flex; gap: 8px; margin-bottom: 1.5rem; background: #000; padding: 5px; border-radius: 10px; border: 1px solid var(--border); }
        .tab-btn { flex: 1; padding: 0.6rem; border: none; background: transparent; color: var(--dim); border-radius: 6px; cursor: pointer; font-weight: 500; }
        .tab-btn.active { background: var(--surface-hover); color: #fff; }

        /* UPLOAD MANAGER */
        .upload-manager { position: fixed; bottom: 20px; right: 20px; width: 340px; background: #111; border: 1px solid var(--border); border-radius: 12px; z-index: 5500; display: none; flex-direction: column; overflow: hidden; box-shadow: 0 10px 40px rgba(0,0,0,0.8); }
        .upload-manager.active { display: flex; }
        .um-header { padding: 12px 15px; background: #1a1a1a; display: flex; justify-content: space-between; cursor: pointer; border-bottom: 1px solid var(--border); color: #fff; font-weight: bold;}
        .um-list { max-height: 250px; overflow-y: auto; padding: 10px; display: flex; flex-direction: column; gap: 8px; }
        .um-item { background: #000; border: 1px solid var(--border); padding: 10px; border-radius: 8px; }
        .um-progress-bar { width: 100%; height: 5px; background: #333; margin-top: 8px; border-radius: 3px; overflow: hidden;}
        .um-progress-fill { height: 100%; background: var(--gold); width: 0%; transition: 0.3s; }

        @media (max-width: 768px) {
            .nav-links { display: none; }
            .hamburger { display: flex; }
            .lb-nav-btn { display: none; }
            .xit-logo { font-size: 5rem; }
            .upload-manager { width: calc(100% - 40px); }
        }
    </style>
</head>
<body>
    <div class="toast-container" id="toastContainer"></div>
    <div class="overlay" id="mobileOverlay"></div>

    <nav class="navbar">
        <div class="nav-logo" onclick="navigateTo('home')">XIT</div>
        <ul class="nav-links" id="desktopNavLinks">
            <li><a data-page="home" class="active" onclick="navigateTo('home')">Home</a></li>
            <li><a data-page="live" onclick="navigateTo('live')">Live Stream</a></li>
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
            <div class="profile-wrap" id="profileChipWrapper" style="display:none;">
                <div class="profile-chip" onclick="document.getElementById('profileDropdown').classList.toggle('show')">
                    <span id="profileEmailText"></span><i class="fas fa-chevron-down" style="font-size:0.7rem; color:var(--dim);"></i>
                </div>
                <div class="profile-dropdown" id="profileDropdown">
                    <a onclick="doLogout()"><i class="fas fa-sign-out-alt"></i> Logout</a>
                </div>
            </div>
            <button class="hamburger" id="hamburgerBtn"><span></span><span></span><span></span></button>
        </div>
    </nav>

    <div class="mobile-menu" id="mobileMenu">
        <div style="flex:1;">
            <a onclick="navigateTo('home')"><i class="fas fa-home fa-fw"></i> Home</a>
            <a onclick="navigateTo('live')"><i class="fas fa-satellite-dish fa-fw"></i> Live Stream</a>
            <a onclick="navigateTo('gallery')"><i class="fas fa-image fa-fw"></i> Photos</a>
            <a onclick="navigateTo('videos')"><i class="fas fa-video fa-fw"></i> Videos</a>
            <a onclick="navigateTo('documents')"><i class="fas fa-file-alt fa-fw"></i> Documents</a>
            <a href="https://princexit470.github.io/Chatus/" target="_blank"><i class="fas fa-comment fa-fw"></i> ChatUs</a>
            <a href="https://princexit470.github.io/QR-studio/" target="_blank"><i class="fas fa-qrcode fa-fw"></i> QR Studio</a>
            <a onclick="navigateTo('about')"><i class="fas fa-info-circle fa-fw"></i> Contact</a>
            
            <hr class="mobile-divider" style="border:none; border-top:1px solid var(--border); margin:15px 0;">
            
            <div id="mobileProfileSection" class="mobile-profile-box" style="display:none;">
                <i class="fas fa-user-circle"></i> <span id="mobileProfileName"></span>
            </div>
            
            <div id="mobileLoggedOut">
                <a onclick="showAuth('login')"><i class="fas fa-sign-in-alt fa-fw"></i> Log In</a>
                <a onclick="showAuth('signup')" style="color:var(--gold);"><i class="fas fa-user-plus fa-fw"></i> Sign Up</a>
            </div>
            <div id="mobileLoggedIn" class="hidden">
                <a onclick="doLogout()" style="color:#e74c3c;"><i class="fas fa-sign-out-alt fa-fw"></i> Logout</a>
            </div>
        </div>
    </div>

    <div class="main-content">
        
        <div class="page show" id="page-home">
            <div class="home-container">
                <div class="xit-logo" id="xitLogo">XIT</div>
                <p class="hero-subtitle">Photography • Cinematography</p>
            </div>
        </div>

        <div class="page" id="page-live">
            <h2 class="section-title"><i class="fas fa-satellite-dish" style="color:var(--gold);"></i> Live Broadcast</h2>
            
            <div class="live-admin-top" id="liveAdminButtons" style="display:none;">
                <span style="color:var(--gold); font-weight:bold; margin-right:auto;"><i class="fas fa-user-shield"></i> Admin Settings</span>
                <button class="nav-btn primary" onclick="document.getElementById('liveStudioModal').classList.add('show')"><i class="fas fa-cog"></i> Setup Stream</button>
                <button class="nav-btn" onclick="openFullscreenCamera()"><i class="fas fa-camera"></i> Web Camera Preview</button>
            </div>

            <div class="live-section" id="liveSection">
                <div class="live-header-bar">
                    <span class="live-badge offline" id="liveBadge"><i class="fas fa-circle"></i> OFFLINE</span>
                    <div class="viewer-count"><i class="fas fa-eye"></i> <span id="homeViewerCount">0</span></div>
                </div>
                
                <div class="live-video-container" id="liveVideoContainer">
                    <div class="coming-soon-overlay" id="liveOverlay">
                        <i class="fas fa-broadcast-tower" style="font-size:3rem; color:#333;"></i>
                        <h3 style="color:#555; letter-spacing:2px; font-weight:500;">STREAM OFFLINE</h3>
                    </div>
                </div>
            </div>
        </div>

        <div class="page" id="page-gallery">
            <h2 class="section-title"><i class="fas fa-images" style="color:var(--gold);"></i> Photos</h2>
            <div class="search-container"><i class="fas fa-search"></i><input type="text" id="searchPhotos" placeholder="Search photos..." onkeyup="fetchMedia()"></div>
            <div class="add-media-bar" id="photoAddBar" style="display:none;"><button class="nav-btn primary" onclick="openAddMediaModal('photo')"><i class="fas fa-upload"></i> Upload Photos / URL</button></div>
            <div class="gallery-grid" id="photoGrid"></div>
        </div>

        <div class="page" id="page-videos">
            <h2 class="section-title"><i class="fas fa-play-circle" style="color:var(--gold);"></i> Videos</h2>
            <div class="search-container"><i class="fas fa-search"></i><input type="text" id="searchVideos" placeholder="Search videos..." onkeyup="fetchMedia()"></div>
            <div class="add-media-bar" id="videoAddBar" style="display:none;"><button class="nav-btn primary" onclick="openAddMediaModal('video')"><i class="fas fa-upload"></i> Upload Video / URL</button></div>
            <div class="video-feed" id="videoFeed"></div>
        </div>

        <div class="page" id="page-documents">
            <h2 class="section-title"><i class="fas fa-file-pdf" style="color:var(--gold);"></i> Documents</h2>
            <div class="search-container"><i class="fas fa-search"></i><input type="text" id="searchDocs" placeholder="Search documents..." onkeyup="fetchMedia()"></div>
            <div class="add-media-bar" id="docAddBar" style="display:none;"><button class="nav-btn primary" onclick="openAddMediaModal('doc')"><i class="fas fa-upload"></i> Upload Document / URL</button></div>
            <div class="video-feed" style="display:flex; flex-direction:column; gap:1rem;" id="docList"></div>
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
        <div class="lb-title" id="lbTitleText" style="position: absolute; bottom: 30px; color: #fff; font-size: 1.2rem; text-align: center; width: 100%;"></div>
    </div>

    <div id="fullscreenCameraUI">
        <div style="position:absolute; top:20px; left:20px; right:20px; display:flex; justify-content:space-between; z-index:10;">
            <div style="background:#e74c3c; color:#fff; padding:5px 15px; border-radius:20px; font-weight:bold; font-size:0.9rem; animation:pulse 2s infinite;"><i class="fas fa-circle"></i> LIVE REC</div>
            <div style="background:rgba(0,0,0,0.7); color:#fff; padding:5px 15px; border-radius:20px; font-size:0.9rem; border:1px solid rgba(255,255,255,0.2);"><i class="fas fa-eye"></i> <span id="camViewerCount">0</span></div>
        </div>
        <video id="liveVideoElement" autoplay muted playsinline></video>
        <div class="cam-controls">
            <button id="camPlayBtn" onclick="toggleCameraPlay()" class="cam-btn cam-btn-play" style="display:none;"><i class="fas fa-play"></i></button>
            <button id="camPauseBtn" onclick="toggleCameraPause()" class="cam-btn cam-btn-pause"><i class="fas fa-pause"></i></button>
            <button onclick="stopFullscreenCamera()" class="cam-btn cam-btn-stop"><i class="fas fa-stop"></i></button>
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
            <h2 id="addMediaTitle">Add Media</h2>
            
            <div class="tabs" id="mediaTabs">
                <button class="tab-btn active" data-tab="upload">📁 Upload Files</button>
                <button class="tab-btn" data-tab="link">🔗 Paste URL</button>
            </div>
            
            <div id="mediaTabUpload">
                <div class="input-group">
                    <label>Title (Optional)</label>
                    <input type="text" id="addMediaName" placeholder="e.g. Cinematic B-Roll">
                </div>
                <div class="input-group">
                    <label>Select Files</label>
                    <input type="file" id="addMediaFile" multiple style="padding:0.5rem; background:transparent;">
                </div>
                <div class="input-group" id="thumbnailGroup" style="display:none;">
                    <label>Custom Thumbnail/Poster (Optional)</label>
                    <input type="file" id="addMediaThumb" accept="image/*" style="padding:0.5rem; background:transparent;">
                    <small style="color:var(--gold); display:block; margin-top:5px;">Leave blank for Auto-Thumbnail.</small>
                </div>
            </div>

            <div id="mediaTabLink" style="display:none;">
                <div class="input-group">
                    <label>Title *</label>
                    <input type="text" id="addMediaLinkName" placeholder="Title">
                </div>
                <div class="input-group">
                    <label>Direct Link / YouTube URL *</label>
                    <textarea id="addMediaUrl" placeholder="Paste URL here..." rows="3"></textarea>
                </div>
                <div class="input-group" id="linkThumbGroup" style="display:none;">
                    <label>Thumbnail Image URL (Optional)</label>
                    <input type="text" id="addMediaLinkThumb" placeholder="https://...image.jpg">
                </div>
            </div>

            <button class="modal-submit" onclick="submitMedia()">Save Media</button>
            <input type="hidden" id="addMediaType" value="photo">
        </div>
    </div>

    <div class="modal-backdrop" id="editMediaModal">
        <div class="modal-dialog">
            <button class="modal-close" onclick="closeModal('editMediaModal')">✕</button>
            <h2>Edit Media Details</h2>
            <div class="input-group">
                <label>New Title</label>
                <input type="text" id="editMediaTitleInput">
            </div>
            <div class="input-group">
                <label>Update Thumbnail URL (Optional)</label>
                <input type="text" id="editMediaThumbInput" placeholder="https://...image.jpg">
            </div>
            <input type="hidden" id="editMediaId">
            <input type="hidden" id="editMediaUrlOrig">
            <button class="modal-submit" onclick="saveMediaEdit()">Update Media</button>
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
                    <option value="youtube">YouTube Link</option>
                    <option value="custom">Custom WebRTC / HLS (.m3u8) URL</option>
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
                <input type="text" id="liveStreamUrlInput" placeholder="e.g. https://...m3u8 or YouTube URL">
            </div>

            <button class="modal-submit" onclick="saveLiveSettings()"><i class="fas fa-broadcast-tower"></i> Publish Stream</button>
            <button class="nav-btn" style="width:100%;margin-top:10px; background:rgba(231,76,60,0.1); color:#e74c3c; border:none;" onclick="clearLive()">Stop Current Stream</button>
        </div>
    </div>

    <div class="upload-manager" id="uploadManager">
        <div class="um-header" onclick="document.getElementById('umList').style.display = document.getElementById('umList').style.display==='none'?'flex':'none'">
            <span id="umTitle">Uploads</span>
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
        let ALL_PHOTOS = [];

        // XIT ANIMATION
        const xitLogo = document.getElementById('xitLogo');
        document.addEventListener('mousemove', (e) => {
            if(!xitLogo || document.getElementById('page-home').classList.contains('show') === false) return;
            const x = (window.innerWidth / 2 - e.pageX) / 30;
            const y = (window.innerHeight / 2 - e.pageY) / 30;
            xitLogo.style.transform = `perspective(600px) rotateY(${x}deg) rotateX(${y}deg) scale(1.05)`;
        });
        document.addEventListener('mouseleave', () => { if(xitLogo) xitLogo.style.transform = 'perspective(600px) rotateY(0deg) rotateX(0deg) scale(1)'; });

        // TOAST
        function TOAST(m, t='info') {
            const b = document.getElementById('toastContainer'), d = document.createElement('div');
            d.className = 'toast ' + t; d.innerHTML = m; b.appendChild(d);
            setTimeout(() => { d.style.animation = 'toastIn 0.3s ease reverse forwards'; setTimeout(() => d.remove(), 300); }, 3000);
        }

        // INIT
        window.onload = async () => {
            const { data } = await SB.auth.getSession();
            updateUI(data.session ? data.session.user : null);
            fetchMedia();
            setupRealtimePresence();
            
            const hash = window.location.hash.replace('#', '');
            if(hash) navigateTo(hash);

            document.getElementById('hamburgerBtn').onclick = () => { document.getElementById('mobileMenu').classList.add('open'); document.getElementById('mobileOverlay').classList.add('show'); };
            document.getElementById('mobileOverlay').onclick = () => { document.getElementById('mobileMenu').classList.remove('open'); document.getElementById('mobileOverlay').classList.remove('show'); };
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

        // AUTHENTICATION
        function switchAuthMode(mode) {
            AUTH_MODE = mode;
            const isSignup = mode === 'signup';
            document.getElementById('authNameGroup').style.display = isSignup ? 'block' : 'none';
            document.getElementById('authConfirmPassGroup').style.display = isSignup ? 'block' : 'none';
            document.getElementById('authTitle').innerText = isSignup ? 'Create Account' : 'Welcome Back';
            document.getElementById('authSubmit').innerText = isSignup ? 'Sign Up' : 'Log In';
            document.getElementById('authSwitch').innerHTML = isSignup ? 'Have account? <span style="color:var(--gold);cursor:pointer;" onclick="switchAuthMode(\'login\')">Log In</span>' : 'New here? <span style="color:var(--gold);cursor:pointer;" onclick="switchAuthMode(\'signup\')">Create account</span>';
        }

        function showAuth(mode) { switchAuthMode(mode); document.getElementById('authModal').classList.add('show'); }
        function closeModal(id) { document.getElementById(id).classList.remove('show'); }

        async function doAuth() {
            const email = document.getElementById('authEmail').value, pass = document.getElementById('authPass').value;
            if(!email || !pass) return TOAST('Fill required fields', 'error');
            document.getElementById('authSubmit').disabled = true; document.getElementById('authSubmit').innerText = "Processing...";
            try {
                let res;
                if(AUTH_MODE === 'signup') {
                    const name = document.getElementById('authName').value, cpass = document.getElementById('authConfirmPass').value;
                    if(!name) throw new Error("Full name required");
                    if(pass !== cpass) throw new Error("Passwords do not match");
                    res = await SB.auth.signUp({ email, password: pass, options: { data: { full_name: name } }});
                } else res = await SB.auth.signInWithPassword({ email, password: pass });
                
                if(res.error) throw res.error;
                TOAST(AUTH_MODE === 'signup' ? 'Account Created!' : 'Logged In', 'success');
                closeModal('authModal'); updateUI(res.data.user);
            } catch(e) { TOAST(e.message, 'error'); } 
            finally { document.getElementById('authSubmit').disabled = false; document.getElementById('authSubmit').innerText = AUTH_MODE === 'signup' ? 'Sign Up' : 'Log In'; }
        }

        async function doLogout() { await SB.auth.signOut(); updateUI(null); TOAST('Logged Out'); document.getElementById('profileDropdown').classList.remove('show'); }

        function updateUI(u) {
            USER = u; const isAdm = USER && USER.email === ADMIN_EMAIL;
            
            document.getElementById('desktopAuthBtns').style.display = USER ? 'none' : 'block';
            document.getElementById('profileChipWrapper').style.display = USER ? 'block' : 'none';
            if(USER) document.getElementById('profileEmailText').innerText = USER.user_metadata?.full_name || USER.email.split('@')[0];
            
            document.getElementById('mobileLoggedOut').style.display = USER ? 'none' : 'block';
            document.getElementById('mobileLoggedIn').style.display = USER ? 'block' : 'none';
            document.getElementById('mobileProfileSection').style.display = USER ? 'block' : 'none';
            if(USER) document.getElementById('mobileProfileName').innerText = USER.user_metadata?.full_name || USER.email.split('@')[0];
            
            document.getElementById('photoAddBar').style.display = isAdm ? 'flex' : 'none';
            document.getElementById('videoAddBar').style.display = isAdm ? 'flex' : 'none';
            document.getElementById('docAddBar').style.display = isAdm ? 'flex' : 'none';
            document.getElementById('liveAdminButtons').style.display = isAdm ? 'flex' : 'none';
        }

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

        function formatTime(seconds) {
            if(isNaN(seconds) || seconds === 0) return '';
            const m = Math.floor(seconds / 60); const s = Math.floor(seconds % 60).toString().padStart(2, '0');
            return `${m}:${s}`;
        }
        function extractYtId(url) {
            const match = url.match(/(?:youtu\.be\/|youtube\.com\/(?:embed\/|v\/|watch\?v=|watch\?.+&v=))([^&]{11})/);
            return match ? match[1] : null;
        }

        // FETCH MEDIA (Search integration)
        async function fetchMedia() {
            const { data } = await SB.from('media').select('*').order('created_at', { ascending: false });
            if(!data) return;
            
            const qPhoto = (document.getElementById('searchPhotos') ? document.getElementById('searchPhotos').value.toLowerCase() : '');
            const qVideo = (document.getElementById('searchVideos') ? document.getElementById('searchVideos').value.toLowerCase() : '');
            const qDoc = (document.getElementById('searchDocs') ? document.getElementById('searchDocs').value.toLowerCase() : '');
            
            const photos = data.filter(m => m.type === 'photo' && m.title.toLowerCase().includes(qPhoto));
            const videos = data.filter(m => m.type === 'video' && m.title.toLowerCase().includes(qVideo));
            const docs = data.filter(m => m.type === 'doc' && m.title.toLowerCase().includes(qDoc));
            const live = data.filter(m => m.type === 'live');
            const admin = USER && USER.email === ADMIN_EMAIL;
            
            ALL_PHOTOS = photos;
            
            document.getElementById('photoGrid').innerHTML = photos.map((p, index) => {
                let pUrl = p.url, tUrl = '';
                if(p.url.includes('|')) { const pts = p.url.split('|'); pUrl = pts[0]; tUrl = pts[1]; }
                return `
                <div class="gallery-item">
                    <img src="${pUrl}" loading="lazy" onclick="openLB(${index})">
                    <div class="media-title">${p.title}</div>
                    ${admin?`<div class="admin-absolute-actions">
                        <button class="btn-edit" onclick="openEditModal('${p.id}', '${p.title.replace(/'/g,"\\'")}', '${p.url}')"><i class="fas fa-edit"></i></button>
                        <button class="btn-del" onclick="delMedia('${p.id}')"><i class="fas fa-trash"></i></button>
                    </div>`:''}
                </div>`;
            }).join('');
            
            // VIDEOS (Strict 16:9 + Metadata)
            document.getElementById('videoFeed').innerHTML = videos.map(v => {
                let vidSrc = v.url, poster = '';
                if(v.url.includes('|')) {
                    const parts = v.url.split('|'); vidSrc = parts[0]; poster = parts[1];
                } else if (!vidSrc.includes('youtube') && !vidSrc.includes('youtu.be')) {
                    poster = vidSrc.replace(/\.(mp4|webm|mov|mkv)$/i, '.jpg'); 
                }
                
                let playerHtml = '';
                if(vidSrc.includes('youtube') || vidSrc.includes('youtu.be')) {
                    const ytId = extractYtId(vidSrc);
                    playerHtml = ytId ? `<iframe src="https://www.youtube.com/embed/${ytId}" loading="lazy"></iframe>` : `<a href="${vidSrc}" target="_blank">View Video</a>`;
                } else {
                    playerHtml = `<video src="${vidSrc}" poster="${poster}" controls playsinline preload="metadata" onloadedmetadata="if(this.nextElementSibling) this.nextElementSibling.innerText = formatTime(this.duration)"></video><span class="vid-duration">...</span>`;
                }
                
                return `
                <div class="video-card-yt">
                    <div class="video-thumb-yt">${playerHtml}</div>
                    <div class="video-info-yt">
                        <div>
                            <h3>${v.title}</h3>
                            <p style="font-size:0.8rem; color:var(--dim); margin-top:5px;">${new Date(v.created_at).toLocaleDateString()}</p>
                        </div>
                        ${admin?`
                        <div class="action-group">
                            <button class="btn-edit" onclick="openEditModal('${v.id}', '${v.title.replace(/'/g,"\\'")}', '${v.url}')"><i class="fas fa-edit"></i> Edit</button>
                            <button class="btn-del" onclick="delMedia('${v.id}')"><i class="fas fa-trash"></i> Delete</button>
                        </div>`:''}
                    </div>
                </div>`;
            }).join('');
            
            document.getElementById('docList').innerHTML = docs.map(d => `
                <div class="video-card-yt" style="padding:15px; flex-direction:row; justify-content:space-between; align-items:center;">
                    <span style="font-weight:600;"><i class="fas fa-file-pdf" style="color:#e74c3c; font-size:1.2rem; margin-right:10px;"></i> ${d.title}</span>
                    <div style="display:flex; gap:10px;">
                        <a href="${d.url}" target="_blank" class="nav-btn primary" style="text-decoration:none;"><i class="fas fa-download"></i> Open</a>
                        ${admin?`<button class="btn-del" style="position:static; width:auto; border-radius:6px; padding:0 10px;" onclick="delMedia('${d.id}')"><i class="fas fa-trash"></i></button>`:''}
                    </div>
                </div>
            `).join('');

            // LIVE SECTION
            const lContainer = document.getElementById('liveVideoContainer');
            const lBadge = document.getElementById('liveBadge');
            const lOverlay = document.getElementById('liveOverlay');
            lContainer.innerHTML = '';
            
            if(live.length > 0) {
                const url = live[0].url;
                lBadge.classList.remove('offline'); lBadge.innerHTML = `<i class="fas fa-circle"></i> ${live[0].title || 'LIVE NOW'}`;
                
                if (url.includes('.m3u8')) {
                    lContainer.innerHTML = `<video id="hlsLivePlayer" controls autoplay playsinline></video>`;
                    const video = document.getElementById('hlsLivePlayer');
                    if (Hls.isSupported()) { const hls = new Hls(); hls.loadSource(url); hls.attachMedia(video); }
                    else if (video.canPlayType('application/vnd.apple.mpegurl')) { video.src = url; }
                } else if(url.includes('youtube') || url.includes('youtu.be')) {
                    const ytId = extractYtId(url);
                    if(ytId) lContainer.innerHTML = `<iframe src="https://www.youtube.com/embed/${ytId}?autoplay=1" allowfullscreen></iframe>`;
                } else if(url.includes('cloudinary')) {
                    lContainer.innerHTML = `<iframe src="${url}" allowfullscreen></iframe>`;
                } else {
                    lContainer.innerHTML = `<video src="${url}" controls autoplay playsinline></video>`;
                }
            } else {
                lBadge.classList.add('offline'); lBadge.innerHTML = '<i class="fas fa-circle"></i> OFFLINE';
                lContainer.appendChild(lOverlay);
            }
        }

        async function delMedia(id) { if(!confirm('Delete permanently?')) return; await SB.from('media').delete().eq('id', id); fetchMedia(); TOAST('Deleted', 'success'); }

        // EDIT MEDIA
        function openEditModal(id, title, urlRaw) {
            document.getElementById('editMediaId').value = id; document.getElementById('editMediaTitleInput').value = title; document.getElementById('editMediaUrlOrig').value = urlRaw;
            let thumb = ''; if(urlRaw.includes('|')) thumb = urlRaw.split('|')[1];
            document.getElementById('editMediaThumbInput').value = thumb;
            document.getElementById('editMediaModal').classList.add('show');
        }

        async function saveMediaEdit() {
            const id = document.getElementById('editMediaId').value, newTitle = document.getElementById('editMediaTitleInput').value, newThumb = document.getElementById('editMediaThumbInput').value;
            let origUrl = document.getElementById('editMediaUrlOrig').value;
            let baseVid = origUrl.includes('|') ? origUrl.split('|')[0] : origUrl;
            let finalUrl = newThumb ? `${baseVid}|${newThumb}` : baseVid;

            await SB.from('media').update({ title: newTitle, url: finalUrl }).eq('id', id);
            TOAST('Updated successfully!', 'success'); closeModal('editMediaModal'); fetchMedia();
        }

        // LIGHTBOX
        let lbIndex = 0; const lbElement = document.getElementById('lightbox'), lbImage = document.getElementById('lbImage');
        function openLB(index) { lbIndex = index; updateLB(); lbElement.classList.add('show'); document.body.classList.add('no-scroll'); }
        function closeLB() { lbElement.classList.remove('show'); document.body.classList.remove('no-scroll'); }
        function updateLB() {
            const photo = ALL_PHOTOS[lbIndex];
            let pUrl = photo.url.includes('|') ? photo.url.split('|')[0] : photo.url;
            lbImage.style.transform = 'scale(0.8)'; lbImage.style.opacity = '0';
            setTimeout(() => {
                lbImage.src = pUrl; document.getElementById('lbCounter').innerText = `${lbIndex + 1} / ${ALL_PHOTOS.length}`;
                document.getElementById('lbTitleText').innerText = photo.title; document.getElementById('lbDownloadBtn').onclick = () => window.open(pUrl, '_blank');
                lbImage.style.transform = 'scale(1)'; lbImage.style.opacity = '1';
            }, 150);
        }
        function lbNav(dir) { lbIndex += dir; if(lbIndex<0) lbIndex=ALL_PHOTOS.length-1; if(lbIndex>=ALL_PHOTOS.length) lbIndex=0; updateLB(); }
        
        let touchStartY=0, touchStartX=0;
        lbElement.addEventListener('touchstart', e => { touchStartX = e.changedTouches[0].screenX; touchStartY = e.changedTouches[0].screenY; });
        lbElement.addEventListener('touchend', e => {
            let dx = e.changedTouches[0].screenX - touchStartX, dy = e.changedTouches[0].screenY - touchStartY;
            if (dy > 100 && Math.abs(dx) < 60) closeLB(); else if (dx > 70) lbNav(-1); else if (dx < -70) lbNav(1);
        });

        // TABS FOR ADD MEDIA
        document.getElementById('mediaTabs').addEventListener('click', (e) => {
            if (!e.target.classList.contains('tab-btn')) return;
            document.querySelectorAll('#mediaTabs .tab-btn').forEach(b => b.classList.remove('active'));
            e.target.classList.add('active');
            const isLink = e.target.dataset.tab === 'link';
            document.getElementById('mediaTabLink').style.display = isLink ? 'block' : 'none';
            document.getElementById('mediaTabUpload').style.display = isLink ? 'none' : 'block';
        });

        function openAddMediaModal(type) {
            document.getElementById('addMediaType').value = type;
            document.getElementById('addMediaName').value = ''; document.getElementById('addMediaFile').value = '';
            document.getElementById('addMediaLinkName').value = ''; document.getElementById('addMediaUrl').value = '';
            document.getElementById('thumbnailGroup').style.display = type === 'video' ? 'block' : 'none';
            document.getElementById('linkThumbGroup').style.display = type === 'video' ? 'block' : 'none';
            
            document.querySelectorAll('#mediaTabs .tab-btn').forEach(b => b.classList.remove('active'));
            document.querySelector('[data-tab="upload"]').classList.add('active');
            document.getElementById('mediaTabLink').style.display = 'none'; document.getElementById('mediaTabUpload').style.display = 'block';
            document.getElementById('addMediaModal').classList.add('show');
        }

        // UPLOAD MANAGER
        let uploads = [];
        function renderUM() {
            const um = document.getElementById('uploadManager');
            if(uploads.length === 0) { um.classList.remove('active'); return; }
            um.classList.add('active'); document.getElementById('umTitle').innerText = `Uploads (${uploads.filter(u=>u.status==='uploading').length})`;
            document.getElementById('umList').innerHTML = uploads.map(u => `
                <div class="um-item">
                    <div style="display:flex;justify-content:space-between;color:${u.status==='error'?'#e74c3c':(u.status==='done'?'#2ecc71':'#fff')};font-size:0.85rem;">
                        <span style="white-space:nowrap;overflow:hidden;text-overflow:ellipsis;max-width:70%;">${u.name}</span>
                        <span style="font-weight:bold;">${u.status==='uploading'?u.progress+'%':(u.status==='done'?'Success':'Failed')}</span>
                    </div>
                    <div class="um-progress-bar"><div class="um-progress-fill" style="width:${u.status==='error'?100:u.progress}%;background:${u.status==='error'?'#e74c3c':(u.status==='done'?'#2ecc71':'var(--gold)')}"></div></div>
                </div>
            `).reverse().join('');
        }
        function clearUploads() { uploads = uploads.filter(u=>u.status==='uploading'); renderUM(); }

        // SUBMIT MEDIA
        async function submitMedia() {
            const type = document.getElementById('addMediaType').value;
            const tab = document.querySelector('#mediaTabs .tab-btn.active').dataset.tab;

            if(tab === 'link') {
                const title = document.getElementById('addMediaLinkName').value.trim();
                const url = document.getElementById('addMediaUrl').value.trim();
                const thumb = document.getElementById('addMediaLinkThumb') ? document.getElementById('addMediaLinkThumb').value.trim() : '';
                if(!title || !url) return TOAST('Title & URL required', 'error');
                let finalUrl = (type === 'video' && thumb) ? `${url}|${thumb}` : url;
                await SB.from('media').insert([{ type, title, url: finalUrl }]);
                TOAST('Saved!', 'success'); closeModal('addMediaModal'); fetchMedia(); return;
            }

            const files = document.getElementById('addMediaFile').files;
            const customTitle = document.getElementById('addMediaName').value.trim();
            const thumbFile = document.getElementById('addMediaThumb') ? document.getElementById('addMediaThumb').files[0] : null;

            if(files.length === 0) return TOAST('Select files', 'error');
            closeModal('addMediaModal');
            
            const uploadToCloudinary = (file, onProgress) => {
                return new Promise((resolve, reject) => {
                    const fd = new FormData(); fd.append('file', file); fd.append('upload_preset', CLOUDINARY_UPLOAD_PRESET); fd.append('cloud_name', CLOUDINARY_CLOUD_NAME);
                    const xhr = new XMLHttpRequest(); xhr.open('POST', `https://api.cloudinary.com/v1_1/${CLOUDINARY_CLOUD_NAME}/auto/upload`);
                    if(onProgress) xhr.upload.onprogress = onProgress;
                    xhr.onload = () => { if(xhr.status>=200 && xhr.status<300) resolve(JSON.parse(xhr.responseText).secure_url); else reject(); };
                    xhr.onerror = () => reject(); xhr.send(fd);
                });
            };

            for(let i=0; i<files.length; i++) {
                const file = files[i], id = Date.now()+i, finalTitle = (files.length === 1 && customTitle) ? customTitle : file.name;
                uploads.push({ id, name: finalTitle, progress: 0, status: 'uploading' }); renderUM(); document.getElementById('umList').style.display = 'flex';
                
                try {
                    let thumbUrl = null; if(type === 'video' && thumbFile && i === 0) thumbUrl = await uploadToCloudinary(thumbFile, null);
                    const fileUrl = await uploadToCloudinary(file, (e) => { if(e.lengthComputable) { const idx = uploads.findIndex(u=>u.id===id); if(idx>-1) { uploads[idx].progress = Math.round((e.loaded/e.total)*100); renderUM(); } } });
                    let finalUrl = thumbUrl ? `${fileUrl}|${thumbUrl}` : fileUrl;
                    await SB.from('media').insert([{ type, title: finalTitle, url: finalUrl }]);
                    const idx = uploads.findIndex(u=>u.id===id); if(idx>-1) { uploads[idx].status = 'done'; uploads[idx].progress = 100; renderUM(); fetchMedia(); }
                } catch(error) { const idx = uploads.findIndex(u=>u.id===id); if(idx>-1) { uploads[idx].status = 'error'; renderUM(); TOAST('Error', 'error'); } }
            }
        }

        // LIVE STUDIO
        function toggleLiveInputs() {
            const type = document.getElementById('liveSourceType').value;
            document.getElementById('liveObsDetails').style.display = type === 'obs' ? 'block' : 'none';
            document.getElementById('liveUrlGroup').style.display = type === 'obs' ? 'none' : 'block';
        }

        async function saveLiveSettings() {
            const title = document.getElementById('liveTitle').value, type = document.getElementById('liveSourceType').value;
            let url = '';
            if(type === 'obs') url = 'https://player.cloudinary.com/embed/?cloud_name=dl4pbclvi&public_id=live_stream_f1e3551f3179487a9bc883505c9c2518_hls&profile=cld-live-streaming';
            else { url = document.getElementById('liveStreamUrlInput').value; if(!url) return TOAST('Please enter URL', 'error'); }
            
            await SB.from('media').delete().eq('type', 'live');
            await SB.from('media').insert([{ type:'live', title, url }]);
            TOAST('Stream Configured!', 'success'); closeModal('liveStudioModal'); fetchMedia();
        }
        async function clearLive() { await SB.from('media').delete().eq('type', 'live'); TOAST('Stream Stopped'); closeModal('liveStudioModal'); fetchMedia(); }

        // CAMERA (FULL SCREEN)
        let camStream = null;
        async function openFullscreenCamera() {
            try {
                camStream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'environment' }, audio: true });
                document.getElementById('liveVideoElement').srcObject = camStream;
                document.getElementById('fullscreenCameraUI').classList.add('show');
                document.getElementById('camPlayBtn').style.display = 'none';
                document.getElementById('camPauseBtn').style.display = 'block';
                TOAST('Web Camera Started', 'success');
            } catch(e) { TOAST('Camera permission denied', 'error'); }
        }

        function toggleCameraPause() {
            if(camStream) { camStream.getVideoTracks()[0].enabled = false; document.getElementById('camPauseBtn').style.display = 'none'; document.getElementById('camPlayBtn').style.display = 'block'; TOAST('Camera Paused', 'info'); }
        }

        function toggleCameraPlay() {
            if(camStream) { camStream.getVideoTracks()[0].enabled = true; document.getElementById('camPlayBtn').style.display = 'none'; document.getElementById('camPauseBtn').style.display = 'block'; TOAST('Camera Resumed', 'success'); }
        }

        function stopFullscreenCamera() {
            if(camStream) { camStream.getTracks().forEach(t => t.stop()); camStream = null; }
            document.getElementById('fullscreenCameraUI').classList.remove('show'); TOAST('Camera Stopped', 'info');
        }
    </script>
</body>
</html>