<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>XIT | Creative Studio</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
    <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script> <style>
        :root {
            --gold: #d4af37; --gold-hover: #f3b965; --bg: #0a0a0a; --surface: #141414;
            --surface-hover: #1e1e1e; --border: rgba(255, 255, 255, 0.08); --text: #f5f5f5;
            --dim: #999; --radius: 12px; --transition: 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
        }
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { font-family: 'Inter', system-ui, sans-serif; background: var(--bg); color: var(--text); min-height: 100vh; overflow-x: hidden; }
        
        /* Navbar & Mobile Menu */
        .navbar { position: sticky; top: 0; z-index: 500; background: rgba(10,10,10,0.85); backdrop-filter: blur(20px); padding: 0.8rem 1.5rem; display: flex; align-items: center; border-bottom: 1px solid var(--border); transition: 0.3s; }
        .nav-logo { font-size: 1.5rem; font-weight: 900; color: var(--gold); cursor: pointer; letter-spacing: 1px; }
        .nav-links { display: flex; gap: 0.5rem; list-style: none; align-items: center; margin-left: 20px; }
        .nav-links a { color: var(--dim); text-decoration: none; padding: 0.5rem 1rem; border-radius: 20px; font-size: 0.9rem; transition: var(--transition); cursor: pointer; }
        .nav-links a:hover, .nav-links a.active { color: #fff; background: rgba(255,255,255,0.08); }
        .nav-right { display: flex; align-items: center; gap: 8px; margin-left: auto; }
        
        .nav-btn { padding: 0.4rem 0.8rem; border-radius: 20px; border: 1px solid var(--border); background: transparent; color: #fff; cursor: pointer; font-size: 0.75rem; font-weight: 600; transition: var(--transition); }
        .nav-btn.primary { background: var(--gold); color: #000; border: none; }
        
        .hamburger { display: none; background: none; border: none; flex-direction: column; gap: 5px; cursor: pointer; padding: 5px; }
        .hamburger span { width: 22px; height: 2px; background: #fff; border-radius: 2px; }
        
        .mobile-menu { position: fixed; top: 0; right: -320px; width: 280px; height: 100vh; background: rgba(10,10,10,0.98); backdrop-filter: blur(20px); z-index: 600; padding: 5rem 1.5rem 2rem; transition: right 0.3s ease; border-left: 1px solid var(--border); display: flex; flex-direction: column; }
        .mobile-menu.open { right: 0; }
        .mobile-menu a { display: block; color: var(--dim); padding: 1rem; text-decoration: none; border-radius: 12px; font-weight: 500; cursor: pointer;}
        .mobile-profile-box { margin: 15px 0; padding: 15px; background: rgba(212, 175, 55, 0.1); border: 1px solid var(--gold); border-radius: 12px; text-align: center; color: var(--gold); font-weight: bold; }
        .overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.8); z-index: 550; display: none; }
        .overlay.show { display: block; }
        
        /* Animations & Structure */
        .home-container { display: flex; flex-direction: column; align-items: center; justify-content: center; min-height: 75vh; }
        .xit-logo { font-size: 8rem; font-weight: 900; letter-spacing: 12px; transition: transform 0.1s; background: linear-gradient(90deg, #fff, var(--gold), #fff); background-size: 200% auto; color: transparent; -webkit-background-clip: text; background-clip: text; animation: shine 3s linear infinite; cursor: pointer; }
        @keyframes shine { to { background-position: 200% center; } }

        .page { display: none; padding: 2rem; max-width: 1200px; margin: 0 auto; animation: pageFade 0.4s ease forwards; }
        .page.show { display: block; }
        @keyframes pageFade { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        
        .search-container { display: flex; align-items: center; background: #111; border: 1px solid var(--border); border-radius: 20px; padding: 0.6rem 1.2rem; margin-bottom: 1.5rem; }
        .search-container input { background: transparent; border: none; color: #fff; width: 100%; outline: none; margin-left: 10px; }

        /* Media Grids */
        .video-feed { display: grid; grid-template-columns: repeat(auto-fill, minmax(320px, 1fr)); gap: 1.5rem; }
        .video-card-yt { background: var(--surface); border-radius: 12px; border: 1px solid var(--border); overflow: hidden; }
        .video-thumb-yt { width: 100%; aspect-ratio: 16/9; background: #000; position: relative; }
        .video-thumb-yt video, .video-thumb-yt iframe { position: absolute; top: 0; left: 0; width: 100%; height: 100%; object-fit: contain; }
        
        .gallery-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); gap: 1rem; }
        .gallery-item { position: relative; border-radius: 12px; overflow: hidden; cursor: pointer; background: #000; aspect-ratio: 4/5; border: 1px solid var(--border); }
        .gallery-item img { width: 100%; height: 100%; object-fit: cover; }
        
        /* Lightbox */
        .lightbox { position: fixed; inset: 0; background: rgba(0,0,0,0.98); z-index: 9000; display: none; align-items: center; justify-content: center; }
        .lightbox.show { display: flex; }
        .lb-image-wrap { position: relative; width: 100%; height: 80vh; display: flex; align-items: center; justify-content: center; }
        .lightbox img { max-width: 95vw; max-height: 80vh; border-radius: 8px; object-fit: contain; }

        /* Custom Form System Classes */
        .form-card { background: var(--surface); padding: 1.5rem; border-radius: 12px; border: 1px solid var(--border); margin-bottom: 1rem; display: flex; justify-content: space-between; align-items: center; }
        .form-builder-field { background: #000; padding: 1rem; border-radius: 8px; border: 1px solid var(--border); margin-bottom: 10px; }
        .form-view-container { max-width: 600px; margin: 2rem auto; background: var(--surface); padding: 2rem; border-radius: 16px; border: 1px solid var(--border); box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        .form-view-container input, .form-view-container select { width: 100%; padding: 1rem; margin-bottom: 15px; border-radius: 8px; border: 1px solid var(--border); background: #111; color: #fff; font-size: 1rem; }
        .response-card { background: #000; padding: 1rem; border-radius: 8px; border: 1px solid var(--border); margin-bottom: 10px; cursor: pointer; transition: 0.2s; }
        .response-card:hover { border-color: var(--gold); }
        .chart-container { width: 100%; max-width: 300px; margin: 0 auto 2rem; }

        /* Modals */
        .modal-backdrop { position: fixed; inset: 0; background: rgba(0,0,0,0.85); z-index: 6000; display: none; align-items: center; justify-content: center; backdrop-filter: blur(8px); }
        .modal-backdrop.show { display: flex; }
        .modal-dialog { background: #111; border-radius: 16px; padding: 2.5rem; width: 90%; max-width: 500px; border: 1px solid var(--border); position: relative; max-height: 90vh; overflow-y: auto; }
        .modal-dialog input, .modal-dialog textarea, .modal-dialog select { width: 100%; padding: 0.9rem; margin: 0.5rem 0; border-radius: 8px; border: 1px solid var(--border); background: #000; color: #fff; outline: none; }
        .modal-submit { width: 100%; padding: 1rem; margin-top: 1rem; border-radius: 8px; border: none; background: var(--gold); color: #000; font-weight: 700; cursor: pointer; }
        .modal-close { position: absolute; top: 15px; right: 20px; background: none; border: none; color: var(--dim); font-size: 1.2rem; cursor: pointer; }

        /* Toast */
        .toast-container { position: fixed; top: 20px; right: 20px; z-index: 10000; display: flex; flex-direction: column; gap: 10px; pointer-events: none; }
        .toast { padding: 14px 20px; border-radius: 8px; color: #fff; background: rgba(15,15,15,0.95); animation: toastIn 0.3s forwards; border-left: 4px solid var(--gold); }
        @keyframes toastIn { from { transform: translateX(100%); } to { transform: translateX(0); } }
        
        @media (max-width: 768px) {
            .nav-links { display: none; }
            .hamburger { display: flex; }
            .xit-logo { font-size: 5rem; }
        }
    </style>
</head>
<body>
    <div class="toast-container" id="toastContainer"></div>
    <div class="overlay" id="mobileOverlay" onclick="closeMenu()"></div>

    <nav class="navbar" id="mainNav">
        <div class="nav-logo" onclick="navigateTo('home')">XIT</div>
        <ul class="nav-links">
            <li><a data-page="home" class="active" onclick="navigateTo('home')">Home</a></li>
            <li><a data-page="live" onclick="navigateTo('live')">Live</a></li>
            <li><a data-page="gallery" onclick="navigateTo('gallery')">Photos</a></li>
            <li><a data-page="videos" onclick="navigateTo('videos')">Videos</a></li>
            <li><a data-page="documents" onclick="navigateTo('documents')">Docs</a></li>
            <li><a data-page="forms" onclick="navigateTo('forms')">Forms</a></li>
        </ul>
        <div class="nav-right">
            <span id="authBtns"><button class="nav-btn" onclick="showAuth('login')">Log In</button><button class="nav-btn primary" onclick="showAuth('signup')">Sign Up</button></span>
            <div id="userProfile" style="display:none;" class="profile-chip" onclick="document.getElementById('profileDropdown').classList.toggle('show')">
                <span id="userNameText"></span><i class="fas fa-chevron-down"></i>
                <div class="profile-dropdown" id="profileDropdown" style="right:0;"><a onclick="doLogout()">Logout</a></div>
            </div>
            <button class="hamburger" onclick="openMenu()"><span></span><span></span><span></span></button>
        </div>
    </nav>

    <div class="mobile-menu" id="mobileMenu">
        <div id="mobileNameBox" class="mobile-profile-box" style="display:none;"></div>
        <a onclick="navigateTo('home')">Home</a>
        <a onclick="navigateTo('live')">Live Stream</a>
        <a onclick="navigateTo('gallery')">Photos</a>
        <a onclick="navigateTo('videos')">Videos</a>
        <a onclick="navigateTo('documents')">Documents</a>
        <a onclick="navigateTo('forms')">Forms (Custom)</a>
        <hr style="border:none; border-top:1px solid var(--border); margin:15px 0;">
        <div id="mobileAuth"><a onclick="showAuth('login')">Log In</a><a onclick="showAuth('signup')" style="color:var(--gold);">Sign Up</a></div>
        <a id="mobileLogout" onclick="doLogout()" style="display:none; color:#e74c3c;">Logout</a>
    </div>

    <div class="main-content" id="appContent">
        
        <div class="page show" id="page-home">
            <div class="home-container"><div class="xit-logo" id="xitLogo">XIT</div><p class="hero-subtitle">Creative Studio</p></div>
        </div>

        <div class="page" id="page-live">
            <h2 style="margin-bottom:15px;"><i class="fas fa-satellite-dish" style="color:var(--gold)"></i> Live Broadcast</h2>
            <div id="adminLiveControls" style="display:none; margin-bottom:20px; background:#111; padding:15px; border-radius:12px; border:1px dashed var(--gold);">
                <button class="nav-btn primary" onclick="document.getElementById('liveModal').classList.add('show')">Setup Stream</button>
            </div>
            <div style="background:var(--surface); padding:1.5rem; border-radius:16px;">
                <div style="display:flex; justify-content:space-between; margin-bottom:10px;">
                    <span id="liveBadge" style="background:#e74c3c; padding:5px 15px; border-radius:20px; font-size:0.8rem; font-weight:bold;">OFFLINE</span>
                    <span style="background:#222; padding:5px 15px; border-radius:20px; font-size:0.8rem;"><i class="fas fa-eye"></i> <span id="vCount">0</span></span>
                </div>
                <div style="width:100%; aspect-ratio:16/9; background:#000; border-radius:12px; position:relative; overflow:hidden;" id="livePlayer">
                    <div style="position:absolute; inset:0; display:flex; align-items:center; justify-content:center; color:#555;">STREAM OFFLINE</div>
                </div>
            </div>
        </div>

        <div class="page" id="page-gallery">
            <div class="search-container"><i class="fas fa-search"></i><input type="text" id="sPhoto" placeholder="Search photos..." onkeyup="fetchMedia()"></div>
            <div id="pAdd" style="display:none; margin-bottom:15px;"><button class="nav-btn primary" onclick="openAdd('photo')">Add Photo</button></div>
            <div class="gallery-grid" id="photoGrid"></div>
        </div>

        <div class="page" id="page-videos">
            <div class="search-container"><i class="fas fa-search"></i><input type="text" id="sVideo" placeholder="Search videos..." onkeyup="fetchMedia()"></div>
            <div id="vAdd" style="display:none; margin-bottom:15px;"><button class="nav-btn primary" onclick="openAdd('video')">Add Video</button></div>
            <div class="video-feed" id="videoFeed"></div>
        </div>
        
        <div class="page" id="page-documents">
            <div class="search-container"><i class="fas fa-search"></i><input type="text" id="sDoc" placeholder="Search docs..." onkeyup="fetchMedia()"></div>
            <div id="dAdd" style="display:none; margin-bottom:15px;"><button class="nav-btn primary" onclick="openAdd('doc')">Add Document</button></div>
            <div class="video-feed" id="docList" style="display:flex; flex-direction:column; gap:10px;"></div>
        </div>

        <div class="page" id="page-forms">
            <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:20px;">
                <h2><i class="fas fa-clipboard-list" style="color:var(--gold)"></i> Custom Forms</h2>
                <button class="nav-btn primary" id="createFormBtn" style="display:none;" onclick="openFormBuilder()">+ Create New Form</button>
            </div>
            <div id="formsList"></div>
        </div>
    </div>

    <div id="publicFormView" style="display:none; min-height:100vh; padding:2rem; background:var(--bg);"></div>

    <div class="modal-backdrop" id="authModal">
        <div class="modal-dialog">
            <button class="modal-close" onclick="closeModal('authModal')">✕</button>
            <h2 id="authTitle">Welcome</h2>
            <input type="text" id="aName" placeholder="Full Name" style="display:none;">
            <input type="email" id="aEmail" placeholder="Email">
            <input type="password" id="aPass" placeholder="Password">
            <input type="password" id="aCPass" placeholder="Confirm Password" style="display:none;">
            <button class="modal-submit" onclick="handleAuth()">Submit</button>
        </div>
    </div>

    <div class="modal-backdrop" id="mediaModal">
        <div class="modal-dialog">
            <button class="modal-close" onclick="closeModal('mediaModal')">✕</button>
            <input type="text" id="mTitle" placeholder="Title">
            <input type="text" id="mUrl" placeholder="Paste Cloudinary/YouTube URL here">
            <small style="color:var(--dim); display:block; margin-bottom:15px;">(Use Cloudinary dashboard to upload files, paste link here)</small>
            <button class="modal-submit" onclick="saveMedia()">Save</button>
            <input type="hidden" id="mType">
        </div>
    </div>

    <div class="modal-backdrop" id="liveModal">
        <div class="modal-dialog">
            <button class="modal-close" onclick="closeModal('liveModal')">✕</button>
            <h2>Setup Live</h2>
            <input type="text" id="lTitle" placeholder="Stream Title">
            <input type="text" id="lUrl" placeholder="Stream URL (m3u8 / YouTube)">
            <button class="modal-submit" onclick="setLive()">Go Live</button>
            <button class="nav-btn" style="width:100%; margin-top:10px; color:#e74c3c; border:none;" onclick="stopLive()">Stop Stream</button>
        </div>
    </div>

    <div class="modal-backdrop" id="formBuilderModal">
        <div class="modal-dialog" style="max-width:600px;">
            <button class="modal-close" onclick="closeModal('formBuilderModal')">✕</button>
            <h2>Create Custom Form</h2>
            <input type="text" id="fbTitle" placeholder="Form Title (e.g. Event Registration)">
            <label style="color:var(--dim); font-size:0.8rem;">Page Background Color</label>
            <input type="color" id="fbBgColor" value="#111111" style="height:40px; padding:0;">
            
            <div id="fbFields" style="margin:20px 0;"></div>
            
            <button class="nav-btn" onclick="addFormField()" style="width:100%; margin-bottom:20px; border-style:dashed;">+ Add Field</button>
            <button class="modal-submit" onclick="saveCustomForm()">Publish Form</button>
        </div>
    </div>

    <div class="modal-backdrop" id="formResponsesModal">
        <div class="modal-dialog" style="max-width:700px;">
            <button class="modal-close" onclick="closeModal('formResponsesModal')">✕</button>
            <h2>Form Analytics & Data</h2>
            <div class="chart-container"><canvas id="formChart"></canvas></div>
            <h3 style="margin-bottom:10px;">Responses (<span id="respCount">0</span>)</h3>
            <div id="responsesList" style="max-height:300px; overflow-y:auto;"></div>
        </div>
    </div>

    <div class="modal-backdrop" id="singleResponseModal">
        <div class="modal-dialog">
            <button class="modal-close" onclick="closeModal('singleResponseModal')">✕</button>
            <h2>Submission Details</h2>
            <div id="singleResponseData" style="color:#fff; font-size:0.9rem; line-height:1.8;"></div>
        </div>
    </div>

    <div class="lightbox" id="lightbox">
        <div style="position:absolute; top:20px; right:20px; z-index:10;"><button style="background:none;border:none;color:#fff;font-size:2rem;cursor:pointer;" onclick="closeLB()">✕</button></div>
        <img id="lbImg" src="">
    </div>

    <script>
        // INIT SUPABASE & GLOBALS
        const SB = window.supabase.createClient('https://wvlvamuehbyfdsozjczk.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Ind2bHZhbXVlaGJ5ZmRzb3pqY3prIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzU1NTM4MDAsImV4cCI6MjA5MTEyOTgwMH0.oaF4wymSu0ePwQSp3moV0sKZVgz5f50ILun5iaU816A');
        const ADMIN = "princekvishwakarma@yahoo.com";
        let user = null, mode = 'login';
        let ALL_MEDIA = [], ALL_FORMS = [], ALL_SUBMISSIONS = [];
        let formBuilderFields = [];
        let currentChart = null;

        function TOAST(m, t='info') {
            const b = document.getElementById('toastContainer'), d = document.createElement('div');
            d.className = 'toast ' + t; d.innerHTML = m; b.appendChild(d);
            setTimeout(() => { d.style.animation = 'toastIn 0.3s ease reverse forwards'; setTimeout(() => d.remove(), 300); }, 3000);
        }

        window.onload = async () => {
            // Check for Custom Form Routing
            const hash = window.location.hash.replace('#', '');
            if(hash.startsWith('formview_')) {
                document.getElementById('mainNav').style.display = 'none';
                document.getElementById('appContent').style.display = 'none';
                renderPublicForm(hash.split('_')[1]);
                return;
            }

            const { data } = await SB.auth.getSession();
            updateUI(data.session ? data.session.user : null);
            fetchMedia();
            
            if(hash) navigateTo(hash);

            // Viewer Sync
            const channel = SB.channel('live').on('presence', { event: 'sync' }, () => {
                document.getElementById('vCount').innerText = Object.keys(channel.presenceState()).length;
            }).subscribe(async (s) => { if(s==='SUBSCRIBED') await channel.track({ online: true }); });
        };

        function updateUI(u) {
            user = u; const isA = user && user.email === ADMIN;
            document.getElementById('authBtns').style.display = u ? 'none' : 'block';
            document.getElementById('userProfile').style.display = u ? 'flex' : 'none';
            document.getElementById('mobileAuth').style.display = u ? 'none' : 'block';
            document.getElementById('mobileLogout').style.display = u ? 'block' : 'none';
            if(u) {
                const n = u.user_metadata?.full_name || u.email.split('@')[0];
                document.getElementById('userNameText').innerText = n;
                document.getElementById('mobileNameBox').innerText = n;
                document.getElementById('mobileNameBox').style.display = 'block';
            }
            
            document.getElementById('pAdd').style.display = isA ? 'block' : 'none';
            document.getElementById('vAdd').style.display = isA ? 'block' : 'none';
            document.getElementById('dAdd').style.display = isA ? 'block' : 'none';
            document.getElementById('adminLiveControls').style.display = isA ? 'flex' : 'none';
            document.getElementById('createFormBtn').style.display = isA ? 'block' : 'none';
        }

        function navigateTo(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('show'));
            document.getElementById('page-'+p).classList.add('show');
            document.querySelectorAll('.nav-links a').forEach(a => a.classList.remove('active'));
            const l = document.querySelector(`[data-page="${p}"]`); if(l) l.classList.add('active');
            window.location.hash = p; closeMenu();
        }

        // AUTH
        function showAuth(m) { mode = m; document.getElementById('authTitle').innerText = m==='login'?'Login':'Signup'; document.getElementById('aName').style.display = m==='signup'?'block':'none'; document.getElementById('aCPass').style.display = m==='signup'?'block':'none'; document.getElementById('authModal').classList.add('show'); }
        async function handleAuth() {
            const e = document.getElementById('aEmail').value, p = document.getElementById('aPass').value;
            if(mode === 'signup') {
                if(p !== document.getElementById('aCPass').value) return TOAST('Passwords do not match', 'error');
                const { data, error } = await SB.auth.signUp({ email: e, password: p, options:{data:{full_name:document.getElementById('aName').value}}});
                if(error) TOAST(error.message, 'error'); else { TOAST('Signup Success'); closeModal('authModal'); }
            } else {
                const { data, error } = await SB.auth.signInWithPassword({ email: e, password: p });
                if(error) TOAST(error.message, 'error'); else { updateUI(data.user); closeModal('authModal'); }
            }
        }
        async function doLogout() { await SB.auth.signOut(); updateUI(null); }

        // MEDIA ENGINE
        async function fetchMedia() {
            const { data } = await SB.from('media').select('*').order('created_at', { ascending: false });
            if(!data) return;
            ALL_MEDIA = data;
            
            const qP = document.getElementById('sPhoto')?.value.toLowerCase()||'', qV = document.getElementById('sVideo')?.value.toLowerCase()||'', qD = document.getElementById('sDoc')?.value.toLowerCase()||'';
            const isA = user?.email === ADMIN;
            
            const photos = data.filter(m => m.type==='photo' && m.title.toLowerCase().includes(qP));
            const videos = data.filter(m => m.type==='video' && m.title.toLowerCase().includes(qV));
            const docs = data.filter(m => m.type==='doc' && m.title.toLowerCase().includes(qD));
            const live = data.filter(m => m.type==='live');
            
            ALL_FORMS = data.filter(m => m.type==='form');
            ALL_SUBMISSIONS = data.filter(m => m.type==='submission');

            // Photos
            document.getElementById('photoGrid').innerHTML = photos.map(p => `
                <div class="gallery-item"><img src="${p.url}" onclick="openLB('${p.url}')">
                <div class="media-title">${p.title}</div>
                ${isA?`<button style="position:absolute;top:5px;right:5px;background:rgba(255,0,0,0.8);border:none;color:#fff;padding:5px;border-radius:5px;" onclick="delMedia('${p.id}')"><i class="fas fa-trash"></i></button>`:''}
                </div>`).join('');

            // Videos
            document.getElementById('videoFeed').innerHTML = videos.map(v => `
                <div class="video-card-yt">
                    <div class="video-thumb-yt">
                        ${v.url.includes('youtube')?`<iframe src="${v.url.replace('watch?v=','embed/')}"></iframe>`:`<video src="${v.url}" controls playsinline preload="metadata"></video>`}
                    </div>
                    <div style="padding:15px; display:flex; justify-content:space-between;"><h3>${v.title}</h3>
                    ${isA?`<button class="nav-btn" style="color:#e74c3c;" onclick="delMedia('${v.id}')"><i class="fas fa-trash"></i></button>`:''}
                    </div>
                </div>`).join('');

            // Docs (Using Target Blank)
            document.getElementById('docList').innerHTML = docs.map(d => `
                <div style="background:var(--surface); padding:15px; border-radius:8px; border:1px solid var(--border); display:flex; justify-content:space-between; align-items:center;">
                    <span><i class="fas fa-file-pdf" style="color:#e74c3c;"></i> ${d.title}</span>
                    <div>
                        <a href="${d.url}" target="_blank" class="nav-btn primary" style="text-decoration:none;">Open</a>
                        ${isA?`<button class="nav-btn" style="color:#e74c3c;" onclick="delMedia('${d.id}')"><i class="fas fa-trash"></i></button>`:''}
                    </div>
                </div>`).join('');

            // Live
            const lp = document.getElementById('livePlayer'), lb = document.getElementById('liveBadge');
            if(live.length) {
                lb.innerText = "LIVE NOW"; lb.style.background = "#2ecc71";
                const u = live[0].url;
                if(u.includes('.m3u8')) {
                    lp.innerHTML = `<video id="hlsV" controls autoplay playsinline style="width:100%;height:100%;object-fit:contain;"></video>`;
                    const v = document.getElementById('hlsV');
                    if(Hls.isSupported()){ const h = new Hls(); h.loadSource(u); h.attachMedia(v); }
                } else lp.innerHTML = `<iframe src="${u.includes('youtube')?u.replace('watch?v=','embed/'):u}" allowfullscreen style="width:100%;height:100%;"></iframe>`;
            } else { lb.innerText = "OFFLINE"; lb.style.background = "#e74c3c"; lp.innerHTML = '<div class="coming-soon-overlay"><h3>STREAM OFFLINE</h3></div>'; }

            // Forms
            renderAdminForms();
        }

        function openAdd(t) { document.getElementById('mType').value = t; document.getElementById('mediaModal').classList.add('show'); }
        async function saveMedia() {
            const t = document.getElementById('mType').value, title = document.getElementById('mTitle').value, url = document.getElementById('mUrl').value;
            if(!title || !url) return TOAST('Please enter Title and URL', 'error');
            await SB.from('media').insert([{ type: t, title, url }]);
            closeModal('mediaModal'); fetchMedia(); TOAST('Added Successfully', 'success');
        }
        async function delMedia(id) { if(confirm('Delete?')) { await SB.from('media').delete().eq('id', id); fetchMedia(); } }

        // LIVE SETTINGS
        async function setLive() {
            const title = document.getElementById('lTitle').value, url = document.getElementById('lUrl').value;
            await SB.from('media').delete().eq('type', 'live');
            await SB.from('media').insert([{ type: 'live', title, url }]);
            closeModal('liveModal'); fetchMedia(); TOAST('Stream Live!');
        }
        async function stopLive() { await SB.from('media').delete().eq('type', 'live'); fetchMedia(); closeModal('liveModal'); }

        // LIGHTBOX (No Download)
        function openLB(url) { document.getElementById('lbImg').src = url; document.getElementById('lightbox').classList.add('show'); }
        function closeLB() { document.getElementById('lightbox').classList.remove('show'); }

        // ==========================================
        // 🚀 CUSTOM FORM BUILDER & ANALYTICS (NEW)
        // ==========================================

        function openFormBuilder() {
            formBuilderFields = []; renderBuilderFields();
            document.getElementById('fbTitle').value = '';
            document.getElementById('formBuilderModal').classList.add('show');
        }

        function addFormField() {
            formBuilderFields.push({ id: Date.now(), label: '', type: 'text', required: false, options: '' });
            renderBuilderFields();
        }

        function renderBuilderFields() {
            const container = document.getElementById('fbFields');
            container.innerHTML = formBuilderFields.map((f, i) => `
                <div class="form-builder-field">
                    <div style="display:flex; justify-content:space-between; margin-bottom:10px;">
                        <strong>Field ${i+1}</strong>
                        <button onclick="removeFormField(${i})" style="background:none;border:none;color:#e74c3c;cursor:pointer;"><i class="fas fa-trash"></i></button>
                    </div>
                    <input type="text" placeholder="Question / Label (e.g. First Name)" value="${f.label}" oninput="updateField(${i}, 'label', this.value)" style="background:#1a1a1a;">
                    <div style="display:flex; gap:10px;">
                        <select onchange="updateField(${i}, 'type', this.value)" style="background:#1a1a1a;">
                            <option value="text" ${f.type==='text'?'selected':''}>Short Text</option>
                            <option value="textarea" ${f.type==='textarea'?'selected':''}>Long Paragraph</option>
                            <option value="number" ${f.type==='number'?'selected':''}>Number / Phone</option>
                            <option value="select" ${f.type==='select'?'selected':''}>Dropdown Options</option>
                        </select>
                        <label style="display:flex; align-items:center; gap:5px; color:#fff;"><input type="checkbox" ${f.required?'checked':''} onchange="updateField(${i}, 'required', this.checked)"> Compulsory</label>
                    </div>
                    ${f.type==='select' ? `<input type="text" placeholder="Options separated by comma (e.g. Male, Female)" value="${f.options}" oninput="updateField(${i}, 'options', this.value)" style="background:#1a1a1a; margin-top:10px;">` : ''}
                </div>
            `).join('');
        }

        function updateField(i, key, val) { formBuilderFields[i][key] = val; if(key==='type') renderBuilderFields(); }
        function removeFormField(i) { formBuilderFields.splice(i, 1); renderBuilderFields(); }

        async function saveCustomForm() {
            const title = document.getElementById('fbTitle').value;
            const bgColor = document.getElementById('fbBgColor').value;
            if(!title || formBuilderFields.length === 0) return TOAST('Title and at least 1 field required', 'error');
            
            const schema = { bgColor, fields: formBuilderFields };
            await SB.from('media').insert([{ type: 'form', title: title, url: JSON.stringify(schema) }]);
            TOAST('Form Published!', 'success'); closeModal('formBuilderModal'); fetchMedia();
        }

        function renderAdminForms() {
            const list = document.getElementById('formsList');
            if(ALL_FORMS.length === 0) { list.innerHTML = '<p style="color:var(--dim)">No forms created yet.</p>'; return; }
            
            list.innerHTML = ALL_FORMS.map(f => {
                const link = `${window.location.origin}${window.location.pathname}#formview_${f.id}`;
                const subsCount = ALL_SUBMISSIONS.filter(s => s.title === f.id.toString()).length;
                return `
                <div class="form-card">
                    <div>
                        <h3 style="color:var(--gold); margin-bottom:5px;">${f.title}</h3>
                        <p style="font-size:0.8rem; color:var(--dim);">Responses: ${subsCount}</p>
                    </div>
                    <div style="display:flex; gap:10px;">
                        <button class="nav-btn" onclick="navigator.clipboard.writeText('${link}'); TOAST('Link Copied!', 'success')"><i class="fas fa-link"></i> Copy Link</button>
                        <button class="nav-btn primary" onclick="viewFormAnalytics('${f.id}', '${f.title}')"><i class="fas fa-chart-pie"></i> View Data</button>
                        <button class="nav-btn" style="color:#e74c3c; border-color:#e74c3c;" onclick="delMedia('${f.id}')"><i class="fas fa-trash"></i></button>
                    </div>
                </div>`;
            }).join('');
        }

        // PUBLIC VIEW RENDERER (For users filling the form)
        async function renderPublicForm(formId) {
            const { data } = await SB.from('media').select('*').eq('id', formId);
            if(!data || data.length === 0) return document.write('<h2>Form not found or deleted.</h2>');
            
            const form = data[0];
            const schema = JSON.parse(form.url);
            
            const container = document.getElementById('publicFormView');
            container.style.display = 'block';
            container.style.background = schema.bgColor || '#111';
            
            let html = `<div class="form-view-container"><h1 style="color:var(--gold); margin-bottom:20px; text-align:center;">${form.title}</h1><form id="customFormEl">`;
            
            schema.fields.forEach(f => {
                html += `<div style="margin-bottom:15px;"><label style="display:block; margin-bottom:8px; font-weight:500;">${f.label} ${f.required?'<span style="color:#e74c3c">*</span>':''}</label>`;
                
                if(f.type === 'text') html += `<input type="text" name="${f.id}" ${f.required?'required':''}>`;
                else if(f.type === 'number') html += `<input type="number" name="${f.id}" ${f.required?'required':''}>`;
                else if(f.type === 'textarea') html += `<textarea name="${f.id}" rows="3" ${f.required?'required':''} style="width:100%; padding:1rem; border-radius:8px; border:1px solid var(--border); background:#111; color:#fff;"></textarea>`;
                else if(f.type === 'select') {
                    const opts = f.options.split(',').map(o => `<option value="${o.trim()}">${o.trim()}</option>`).join('');
                    html += `<select name="${f.id}" ${f.required?'required':''}><option value="">Select an option</option>${opts}</select>`;
                }
                html += `</div>`;
            });
            
            html += `<button type="submit" class="modal-submit" style="margin-top:20px;">Submit Form</button></form></div>`;
            container.innerHTML = html;
            
            document.getElementById('customFormEl').onsubmit = async (e) => {
                e.preventDefault();
                const formData = new FormData(e.target);
                const answers = {};
                
                // Map answers back to readable labels
                schema.fields.forEach(f => { answers[f.label] = formData.get(f.id.toString()); });
                
                document.querySelector('.modal-submit').innerText = "Submitting...";
                await SB.from('media').insert([{ type: 'submission', title: formId.toString(), url: JSON.stringify(answers) }]);
                container.innerHTML = `<div class="form-view-container" style="text-align:center;"><h2 style="color:#2ecc71;"><i class="fas fa-check-circle"></i> Submitted Successfully!</h2><p style="color:var(--dim); margin-top:10px;">Your response has been recorded.</p></div>`;
            };
        }

        // VIEW ANALYTICS & RESPONSES
        function viewFormAnalytics(formId, formTitle) {
            const subs = ALL_SUBMISSIONS.filter(s => s.title === formId);
            document.getElementById('respCount').innerText = subs.length;
            
            const list = document.getElementById('responsesList');
            list.innerHTML = '';
            
            let chartData = { Male: 0, Female: 0, Other: 0 }; // Example logic for Gender tracking
            let hasChartData = false;

            subs.forEach((s, idx) => {
                const ans = JSON.parse(s.url);
                
                // Try to find Name to display
                let dispName = `Response #${idx+1}`;
                for(let key in ans) {
                    if(key.toLowerCase().includes('name')) { dispName = ans[key]; break; }
                }
                
                // Try to find Gender for Chart
                for(let key in ans) {
                    if(key.toLowerCase().includes('gender') || key.toLowerCase().includes('male')) {
                        const val = ans[key].toLowerCase();
                        if(val === 'male') chartData.Male++;
                        else if(val === 'female') chartData.Female++;
                        else chartData.Other++;
                        hasChartData = true;
                    }
                }

                // Make safe JSON string for onclick
                const safeJson = JSON.stringify(ans).replace(/'/g, "&#39;").replace(/"/g, "&quot;");

                list.innerHTML += `
                <div class="response-card" onclick="openSingleResponse('${safeJson}')">
                    <div style="font-weight:bold; color:var(--gold);"><i class="fas fa-user"></i> ${dispName}</div>
                    <div style="font-size:0.8rem; color:var(--dim); margin-top:5px;">${new Date(s.created_at).toLocaleString()}</div>
                </div>`;
            });

            if(subs.length === 0) list.innerHTML = '<p style="color:var(--dim)">No responses yet.</p>';

            // Handle Chart Generation
            const ctx = document.getElementById('formChart').getContext('2d');
            if(currentChart) currentChart.destroy();
            
            if(hasChartData) {
                document.getElementById('formChart').style.display = 'block';
                currentChart = new Chart(ctx, {
                    type: 'doughnut',
                    data: {
                        labels: ['Male', 'Female', 'Other'],
                        datasets: [{ data: [chartData.Male, chartData.Female, chartData.Other], backgroundColor: ['#3498db', '#e74c3c', '#f1c40f'], borderWidth: 0 }]
                    },
                    options: { plugins: { legend: { labels: { color: '#fff' } } } }
                });
            } else {
                document.getElementById('formChart').style.display = 'none';
            }

            document.getElementById('formResponsesModal').classList.add('show');
        }

        function openSingleResponse(jsonStr) {
            const ans = JSON.parse(jsonStr.replace(/&quot;/g, '"').replace(/&#39;/g, "'"));
            let html = '';
            for(let key in ans) {
                html += `<div style="margin-bottom:10px;"><strong style="color:var(--gold); display:block; font-size:0.8rem;">${key}</strong> <span style="background:#000; padding:8px; border-radius:6px; display:block; margin-top:4px;">${ans[key] || '-'}</span></div>`;
            }
            document.getElementById('singleResponseData').innerHTML = html;
            document.getElementById('singleResponseModal').classList.add('show');
        }

        function closeModal(id) { document.getElementById(id).classList.remove('show'); }
        function openMenu() { document.getElementById('mobileMenu').classList.add('open'); document.getElementById('mobileOverlay').classList.add('show'); }
        function closeMenu() { document.getElementById('mobileMenu').classList.remove('open'); document.getElementById('mobileOverlay').classList.remove('show'); }
    </script>
</body>
</html>