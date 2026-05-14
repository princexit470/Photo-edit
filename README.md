<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>XIT | Creative Studio</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
    <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        :root {
            --gold: #d4af37; --gold-hover: #f3b965; --bg: #060606; --surface: #111; --surface-hover: #1a1a1a;
            --border: rgba(255, 255, 255, 0.08); --text: #f0f0f0; --dim: #999; --radius: 14px; --transition: 0.25s ease;
        }
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { font-family: 'Inter', system-ui, sans-serif; background: var(--bg); color: var(--text); min-height: 100vh; overflow-x: hidden; }

        /* Scrollbar */
        ::-webkit-scrollbar { width: 6px; } ::-webkit-scrollbar-track { background: #000; } ::-webkit-scrollbar-thumb { background: #333; border-radius: 6px; }

        .navbar { position: sticky; top: 0; z-index: 500; background: rgba(6,6,6,0.8); backdrop-filter: blur(24px); padding: 0.8rem 1.8rem; display: flex; align-items: center; border-bottom: 1px solid var(--border); transition: 0.3s; }
        .nav-logo { font-size: 1.7rem; font-weight: 900; color: var(--gold); cursor: pointer; letter-spacing: 2px; }
        .nav-links { display: flex; gap: 0.5rem; list-style: none; align-items: center; margin-left: 30px; }
        .nav-links a { color: var(--dim); text-decoration: none; padding: 0.6rem 1.2rem; border-radius: 24px; font-size: 0.9rem; transition: var(--transition); font-weight: 500; }
        .nav-links a:hover, .nav-links a.active { color: #fff; background: rgba(255,255,255,0.06); }
        .nav-right { display: flex; align-items: center; gap: 10px; margin-left: auto; }
        .nav-btn { padding: 0.5rem 1rem; border-radius: 24px; border: 1px solid var(--border); background: transparent; color: #fff; cursor: pointer; font-size: 0.8rem; font-weight: 600; transition: var(--transition); }
        .nav-btn.primary { background: var(--gold); color: #000; border: none; }
        .nav-btn.primary:hover { background: var(--gold-hover); }
        .hamburger { display: none; background: none; border: none; flex-direction: column; gap: 5px; cursor: pointer; padding: 5px; }
        .hamburger span { width: 22px; height: 2px; background: #fff; border-radius: 2px; }
        .mobile-menu { position: fixed; top: 0; right: -340px; width: 300px; height: 100vh; background: rgba(10,10,10,0.97); backdrop-filter: blur(24px); z-index: 600; padding: 5rem 1.8rem 2rem; transition: right 0.3s ease; border-left: 1px solid var(--border); }
        .mobile-menu.open { right: 0; }
        .mobile-menu a { display: block; color: var(--dim); padding: 1rem; text-decoration: none; border-radius: 14px; font-weight: 500; margin-bottom: 4px; }
        .mobile-profile-box { margin: 15px 0; padding: 15px; background: rgba(212,175,55,0.1); border: 1px solid var(--gold); border-radius: 14px; text-align: center; color: var(--gold); font-weight: bold; }
        .overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.75); z-index: 550; display: none; }
        .overlay.show { display: block; }

        .home-container { display: flex; flex-direction: column; align-items: center; justify-content: center; min-height: 80vh; text-align: center; }
        .xit-logo { font-size: 7rem; font-weight: 900; letter-spacing: 10px; background: linear-gradient(90deg, #fff, var(--gold), #fff); background-size: 200% auto; color: transparent; -webkit-background-clip: text; background-clip: text; animation: shine 3.5s linear infinite; cursor: pointer; }
        @keyframes shine { to { background-position: 200% center; } }

        .page { display: none; padding: 2rem; max-width: 1300px; margin: 0 auto; animation: pageFade 0.35s ease; }
        .page.show { display: block; }
        @keyframes pageFade { from { opacity: 0; transform: translateY(12px); } to { opacity: 1; transform: translateY(0); } }

        .search-container { display: flex; align-items: center; background: #0d0d0d; border: 1px solid var(--border); border-radius: 24px; padding: 0.6rem 1.2rem; margin-bottom: 1.8rem; }
        .search-container input { background: transparent; border: none; color: #fff; width: 100%; outline: none; margin-left: 10px; font-size: 0.95rem; }

        .video-feed { display: grid; grid-template-columns: repeat(auto-fill, minmax(320px, 1fr)); gap: 1.5rem; }
        .video-card-yt { background: var(--surface); border-radius: 14px; border: 1px solid var(--border); overflow: hidden; transition: transform 0.2s; }
        .video-card-yt:hover { transform: translateY(-4px); }
        .video-thumb-yt { width: 100%; aspect-ratio: 16/9; background: #000; position: relative; }
        .video-thumb-yt video, .video-thumb-yt iframe { position: absolute; top: 0; left: 0; width: 100%; height: 100%; object-fit: contain; }

        .gallery-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); gap: 1rem; }
        .gallery-item { position: relative; border-radius: 14px; overflow: hidden; cursor: pointer; background: #000; aspect-ratio: 4/5; border: 1px solid var(--border); transition: transform 0.2s; }
        .gallery-item:hover { transform: scale(1.02); }
        .gallery-item img { width: 100%; height: 100%; object-fit: cover; }

        /* Lightbox with swipe */
        .lightbox { position: fixed; inset: 0; background: rgba(0,0,0,0.98); z-index: 9000; display: none; align-items: center; justify-content: center; }
        .lightbox.show { display: flex; }
        .lb-image-wrap { position: relative; width: 100%; height: 80vh; display: flex; align-items: center; justify-content: center; }
        .lightbox img { max-width: 95vw; max-height: 85vh; border-radius: 8px; object-fit: contain; user-select: none; }
        .lb-nav { position: absolute; top: 50%; transform: translateY(-50%); background: rgba(255,255,255,0.1); border: none; color: #fff; font-size: 2.5rem; padding: 0.5rem; border-radius: 50%; cursor: pointer; z-index: 10; backdrop-filter: blur(10px); }
        .lb-prev { left: 20px; } .lb-next { right: 20px; }

        .form-card { background: var(--surface); padding: 1.2rem 1.5rem; border-radius: 14px; border: 1px solid var(--border); margin-bottom: 1rem; display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 1rem; }
        .form-builder-field { background: #080808; padding: 1rem; border-radius: 10px; border: 1px solid var(--border); margin-bottom: 12px; }
        .form-view-container { max-width: 700px; margin: 2rem auto; background: var(--surface); padding: 2.5rem; border-radius: 20px; border: 1px solid var(--border); box-shadow: 0 20px 40px rgba(0,0,0,0.6); }
        .form-view-container input, .form-view-container select, .form-view-container textarea { width: 100%; padding: 0.9rem; margin-bottom: 18px; border-radius: 10px; border: 1px solid var(--border); background: #0a0a0a; color: #fff; font-size: 1rem; }
        .response-card { background: #0a0a0a; padding: 1rem; border-radius: 10px; border: 1px solid var(--border); margin-bottom: 10px; cursor: pointer; transition: 0.2s; }
        .response-card:hover { border-color: var(--gold); }
        .chart-container { width: 100%; max-width: 400px; margin: 0 auto 2rem; }

        .modal-backdrop { position: fixed; inset: 0; background: rgba(0,0,0,0.85); z-index: 6000; display: none; align-items: center; justify-content: center; backdrop-filter: blur(8px); }
        .modal-backdrop.show { display: flex; }
        .modal-dialog { background: #0f0f0f; border-radius: 20px; padding: 2.5rem; width: 90%; max-width: 600px; border: 1px solid var(--border); position: relative; max-height: 90vh; overflow-y: auto; box-shadow: 0 25px 50px rgba(0,0,0,0.8); }
        .modal-dialog input, .modal-dialog textarea, .modal-dialog select { width: 100%; padding: 0.8rem; margin: 0.6rem 0; border-radius: 10px; border: 1px solid var(--border); background: #000; color: #fff; outline: none; }
        .modal-submit { width: 100%; padding: 0.9rem; margin-top: 1rem; border-radius: 10px; border: none; background: var(--gold); color: #000; font-weight: 700; cursor: pointer; font-size: 1rem; }
        .modal-close { position: absolute; top: 18px; right: 22px; background: none; border: none; color: var(--dim); font-size: 1.4rem; cursor: pointer; }

        .toast-container { position: fixed; top: 20px; right: 20px; z-index: 10000; display: flex; flex-direction: column; gap: 10px; pointer-events: none; }
        .toast { padding: 14px 22px; border-radius: 10px; color: #fff; background: rgba(20,20,20,0.95); animation: toastIn 0.3s forwards; border-left: 4px solid var(--gold); backdrop-filter: blur(8px); }
        @keyframes toastIn { from { transform: translateX(100%); } to { transform: translateX(0); } }

        .step-indicator { display: flex; gap: 8px; margin-bottom: 25px; }
        .step-dot { width: 12px; height: 12px; border-radius: 50%; background: #333; }
        .step-dot.active { background: var(--gold); }

        @media (max-width: 768px) {
            .nav-links { display: none; } .hamburger { display: flex; } .xit-logo { font-size: 5rem; }
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
        <a onclick="navigateTo('home')">Home</a><a onclick="navigateTo('live')">Live Stream</a><a onclick="navigateTo('gallery')">Photos</a><a onclick="navigateTo('videos')">Videos</a><a onclick="navigateTo('documents')">Documents</a><a onclick="navigateTo('forms')">Forms</a>
        <hr style="border:none; border-top:1px solid var(--border); margin:15px 0;">
        <div id="mobileAuth"><a onclick="showAuth('login')">Log In</a><a onclick="showAuth('signup')" style="color:var(--gold);">Sign Up</a></div>
        <a id="mobileLogout" onclick="doLogout()" style="display:none; color:#e74c3c;">Logout</a>
    </div>

    <div class="main-content" id="appContent">
        <div class="page show" id="page-home"><div class="home-container"><div class="xit-logo">XIT</div><p style="color:var(--dim); font-size:1.2rem;">Creative Studio</p></div></div>

        <div class="page" id="page-live">
            <h2 style="margin-bottom:15px;"><i class="fas fa-satellite-dish" style="color:var(--gold)"></i> Live Broadcast</h2>
            <div id="adminLiveControls" style="display:none; margin-bottom:20px;"><button class="nav-btn primary" onclick="document.getElementById('liveModal').classList.add('show')">Setup Stream</button></div>
            <div style="background:var(--surface); padding:1.5rem; border-radius:18px;">
                <div style="display:flex; justify-content:space-between; margin-bottom:10px;">
                    <span id="liveBadge" style="background:#e74c3c; padding:5px 15px; border-radius:20px; font-size:0.8rem; font-weight:bold;">OFFLINE</span>
                    <span style="background:#1a1a1a; padding:5px 15px; border-radius:20px; font-size:0.8rem;"><i class="fas fa-eye"></i> <span id="vCount">0</span></span>
                </div>
                <div style="width:100%; aspect-ratio:16/9; background:#000; border-radius:14px; position:relative; overflow:hidden;" id="livePlayer"><div style="position:absolute; inset:0; display:flex; align-items:center; justify-content:center; color:#555;">STREAM OFFLINE</div></div>
            </div>
        </div>

        <div class="page" id="page-gallery">
            <div class="search-container"><i class="fas fa-search"></i><input type="text" id="sPhoto" placeholder="Search photos..." onkeyup="fetchMedia()"></div>
            <div id="pAdd" style="display:none; margin-bottom:15px;"><button class="nav-btn primary" onclick="openAdd('photo')">Add Photo</button></div>
            <div class="gallery-grid" id="photoGrid"></div>
        </div>

        <div class="page" id="page-videos">
            <div class="search-container"><i class="fas fa-search"></i><input type="text" id="sVideo" placeholder="Search videos..." onkeyup="fetchMedia()"></div>
            <div id="vAdd" style="display:none;"><button class="nav-btn primary" onclick="openAdd('video')">Add Video</button></div>
            <div class="video-feed" id="videoFeed"></div>
        </div>

        <div class="page" id="page-documents">
            <div class="search-container"><i class="fas fa-search"></i><input type="text" id="sDoc" placeholder="Search docs..." onkeyup="fetchMedia()"></div>
            <div id="dAdd" style="display:none;"><button class="nav-btn primary" onclick="openAdd('doc')">Add Document</button></div>
            <div id="docList" style="display:flex; flex-direction:column; gap:10px;"></div>
        </div>

        <div class="page" id="page-forms">
            <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:25px;">
                <h2><i class="fas fa-clipboard-list" style="color:var(--gold)"></i> Custom Forms</h2>
                <button class="nav-btn primary" id="createFormBtn" style="display:none;" onclick="openFormBuilder()">+ Create New Form</button>
            </div>
            <div id="formsList"></div>
        </div>
    </div>

    <div id="publicFormView" style="display:none; min-height:100vh; padding:2rem; background:var(--bg);"></div>

    <!-- Auth Modal -->
    <div class="modal-backdrop" id="authModal"><div class="modal-dialog"><button class="modal-close" onclick="closeModal('authModal')">✕</button><h2 id="authTitle">Welcome</h2><input type="text" id="aName" placeholder="Full Name" style="display:none;"><input type="email" id="aEmail" placeholder="Email"><input type="password" id="aPass" placeholder="Password"><input type="password" id="aCPass" placeholder="Confirm Password" style="display:none;"><button class="modal-submit" onclick="handleAuth()">Submit</button></div></div>
    <!-- Media Add Modal -->
    <div class="modal-backdrop" id="mediaModal"><div class="modal-dialog"><button class="modal-close" onclick="closeModal('mediaModal')">✕</button><input type="text" id="mTitle" placeholder="Title"><input type="text" id="mUrl" placeholder="Paste Cloudinary/YouTube URL"><button class="modal-submit" onclick="saveMedia()">Save</button><input type="hidden" id="mType"></div></div>
    <!-- Live Modal -->
    <div class="modal-backdrop" id="liveModal"><div class="modal-dialog"><button class="modal-close" onclick="closeModal('liveModal')">✕</button><h2>Setup Live</h2><input type="text" id="lTitle" placeholder="Stream Title"><input type="text" id="lUrl" placeholder="Stream URL (m3u8 / YouTube)"><button class="modal-submit" onclick="setLive()">Go Live</button><button class="nav-btn" style="width:100%; margin-top:10px; color:#e74c3c; border:none;" onclick="stopLive()">Stop Stream</button></div></div>

    <!-- Form Builder (Multi-page) -->
    <div class="modal-backdrop" id="formBuilderModal"><div class="modal-dialog" style="max-width:700px;"><button class="modal-close" onclick="closeModal('formBuilderModal')">✕</button><h2>Build Form</h2>
        <input type="text" id="fbTitle" placeholder="Form Title">
        <label style="color:var(--dim);">Page Background Color</label><input type="color" id="fbBgColor" value="#111111" style="height:40px; padding:0;">
        <div style="margin:15px 0;"><button class="nav-btn" onclick="addFormPage()"><i class="fas fa-plus"></i> Add Page</button></div>
        <div id="fbPagesContainer"></div>
        <button class="modal-submit" onclick="saveCustomForm()">Publish Form</button>
    </div></div>

    <!-- Responses & Analytics -->
    <div class="modal-backdrop" id="formResponsesModal"><div class="modal-dialog" style="max-width:800px;"><button class="modal-close" onclick="closeModal('formResponsesModal')">✕</button>
        <div style="display:flex; gap:15px; align-items:center; margin-bottom:20px;"><input type="text" id="respSearch" placeholder="Search by name or code..." onkeyup="filterSubmissions()" style="flex:1; background:#0a0a0a; padding:0.7rem; border-radius:8px; border:1px solid var(--border); color:#fff;"><span style="color:var(--dim);">Total: <span id="respCount">0</span></span></div>
        <div class="chart-container"><canvas id="formChart"></canvas></div>
        <div id="chartExtra" style="display:flex; flex-wrap:wrap; gap:15px;"></div>
        <h3 style="margin:20px 0 10px;">Responses</h3>
        <div id="responsesList" style="max-height:350px; overflow-y:auto;"></div>
    </div></div>

    <div class="modal-backdrop" id="singleResponseModal"><div class="modal-dialog"><button class="modal-close" onclick="closeModal('singleResponseModal')">✕</button><h2>Submission Details</h2><div id="singleResponseData"></div></div></div>

    <!-- Lightbox (with swipe) -->
    <div class="lightbox" id="lightbox">
        <button class="lb-nav lb-prev" onclick="lbSwipe(-1)"><i class="fas fa-chevron-left"></i></button>
        <img id="lbImg" src="">
        <button class="lb-nav lb-next" onclick="lbSwipe(1)"><i class="fas fa-chevron-right"></i></button>
    </div>

    <script>
        const SB = window.supabase.createClient('https://wvlvamuehbyfdsozjczk.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Ind2bHZhbXVlaGJ5ZmRzb3pqY3prIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzU1NTM4MDAsImV4cCI6MjA5MTEyOTgwMH0.oaF4wymSu0ePwQSp3moV0sKZVgz5f50ILun5iaU816A');
        const ADMIN = "princekvishwakarma@yahoo.com";
        let user = null, mode = 'login';
        let ALL_MEDIA = [], ALL_FORMS = [], ALL_SUBMISSIONS = [];
        let formBuilderPages = []; // array of {title, description, fields}
        let currentChart = null, currentLbIndex = 0, currentLbImages = [];

        function TOAST(m, t='info') {
            const b = document.getElementById('toastContainer'), d = document.createElement('div');
            d.className = 'toast ' + t; d.innerHTML = m; b.appendChild(d);
            setTimeout(() => { d.style.animation = 'toastIn 0.3s ease reverse forwards'; setTimeout(() => d.remove(), 300); }, 3000);
        }

        window.onload = async () => {
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

        // Auth
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

        // Media Fetch & Render
        async function fetchMedia() {
            const { data } = await SB.from('media').select('*').order('created_at', { ascending: false });
            if(!data) return;
            ALL_MEDIA = data;
            const qP = document.getElementById('sPhoto')?.value.toLowerCase()||'', qV = document.getElementById('sVideo')?.value.toLowerCase()||'', qD = document.getElementById('sDoc')?.value.toLowerCase()||'';
            const isA = user?.email === ADMIN;
            const photos = data.filter(m => m.type==='photo' && m.title.toLowerCase().includes(qP));
            const videos = data.filter(m => m.type==='video' && m.title.toLowerCase().includes(qV));
            const docs = data.filter(m => m.type==='doc' && m.title.toLowerCase().includes(qD));
            ALL_FORMS = data.filter(m => m.type==='form');
            ALL_SUBMISSIONS = data.filter(m => m.type==='submission');

            document.getElementById('photoGrid').innerHTML = photos.map((p,i) => `
                <div class="gallery-item"><img src="${p.url}" onclick="openLB('${p.url}', ${i}, photos)">
                ${isA?`<button style="position:absolute;top:5px;right:5px;background:rgba(255,0,0,0.8);border:none;color:#fff;padding:5px;border-radius:5px;" onclick="event.stopPropagation(); delMedia('${p.id}')"><i class="fas fa-trash"></i></button>`:''}
                </div>`).join('');

            document.getElementById('videoFeed').innerHTML = videos.map(v => `
                <div class="video-card-yt">
                    <div class="video-thumb-yt">${v.url.includes('youtube')?`<iframe src="${v.url.replace('watch?v=','embed/')}"></iframe>`:`<video src="${v.url}" controls playsinline></video>`}</div>
                    <div style="padding:15px; display:flex; justify-content:space-between;"><h3>${v.title}</h3>${isA?`<button class="nav-btn" style="color:#e74c3c;" onclick="delMedia('${v.id}')"><i class="fas fa-trash"></i></button>`:''}</div>
                </div>`).join('');

            document.getElementById('docList').innerHTML = docs.map(d => `
                <div style="background:var(--surface); padding:15px 20px; border-radius:14px; border:1px solid var(--border); display:flex; justify-content:space-between; align-items:center;">
                    <span><i class="fas fa-file-pdf" style="color:#e74c3c;"></i> ${d.title}</span>
                    <div style="display:flex; gap:10px;">
                        <button class="nav-btn primary" onclick="downloadFile('${d.url}', '${d.title}')"><i class="fas fa-download"></i> Download</button>
                        ${isA?`<button class="nav-btn" style="color:#e74c3c;" onclick="delMedia('${d.id}')"><i class="fas fa-trash"></i></button>`:''}
                    </div>
                </div>`).join('');

            const live = data.filter(m => m.type==='live');
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

        async function setLive() {
            const title = document.getElementById('lTitle').value, url = document.getElementById('lUrl').value;
            await SB.from('media').delete().eq('type', 'live');
            await SB.from('media').insert([{ type: 'live', title, url }]);
            closeModal('liveModal'); fetchMedia(); TOAST('Stream Live!');
        }
        async function stopLive() { await SB.from('media').delete().eq('type', 'live'); fetchMedia(); closeModal('liveModal'); }

        // Lightbox with swipe
        function openLB(url, index, arr) {
            currentLbIndex = index; currentLbImages = arr.map(p => p.url);
            document.getElementById('lbImg').src = url;
            document.getElementById('lightbox').classList.add('show');
        }
        function closeLB() { document.getElementById('lightbox').classList.remove('show'); }
        function lbSwipe(dir) {
            if(currentLbImages.length < 2) return;
            currentLbIndex = (currentLbIndex + dir + currentLbImages.length) % currentLbImages.length;
            document.getElementById('lbImg').src = currentLbImages[currentLbIndex];
        }
        // Touch swipe
        document.addEventListener('DOMContentLoaded', () => {
            const lb = document.getElementById('lightbox');
            let touchStartX = 0;
            lb.addEventListener('touchstart', (e) => { touchStartX = e.touches[0].clientX; });
            lb.addEventListener('touchend', (e) => {
                if(!touchStartX) return;
                const diff = e.changedTouches[0].clientX - touchStartX;
                if(Math.abs(diff) > 50) lbSwipe(diff > 0 ? -1 : 1);
                touchStartX = 0;
            });
        });

        // Download helper
        function downloadFile(url, filename) {
            const a = document.createElement('a'); a.href = url; a.download = filename || 'download'; document.body.appendChild(a); a.click(); a.remove();
        }

        // ===================== FORM SYSTEM (Multi-page) =====================
        const FORM_TEMPLATES = {
            "Event Registration": [
                { title: "Personal Info", fields: [{label:"Full Name", type:"text", required:true}, {label:"Email", type:"email", required:true}, {label:"Phone", type:"number", required:true}] },
                { title: "Event Details", fields: [{label:"Ticket Type", type:"select-one", options:"VIP,General,Student", required:true}, {label:"Dietary Restrictions", type:"textarea"}] }
            ],
            "Job Application": [
                { title: "Contact", fields: [{label:"Name", type:"text", required:true}, {label:"Email", type:"email", required:true}, {label:"Portfolio URL", type:"text"}] },
                { title: "Experience", fields: [{label:"Years of Experience", type:"number"}, {label:"Skills", type:"textarea"}] },
                { title: "Upload", fields: [{label:"Resume (PDF)", type:"file", required:true, maxSize:5}] }
            ]
        };

        function openFormBuilder() {
            formBuilderPages = [{ title: "Page 1", fields: [] }];
            document.getElementById('fbTitle').value = '';
            renderBuilderPages();
            document.getElementById('formBuilderModal').classList.add('show');
        }

        function addFormPage() {
            formBuilderPages.push({ title: `Page ${formBuilderPages.length+1}`, fields: [] });
            renderBuilderPages();
        }

        function addFormField(pageIndex) {
            formBuilderPages[pageIndex].fields.push({ id: Date.now(), label: '', type: 'text', required: false, options: '', maxSize: 5 });
            renderBuilderPages();
        }

        function removeFormField(pageIndex, fieldIndex) {
            formBuilderPages[pageIndex].fields.splice(fieldIndex, 1);
            renderBuilderPages();
        }

        function removeFormPage(pageIndex) {
            if(formBuilderPages.length <= 1) return;
            formBuilderPages.splice(pageIndex, 1);
            renderBuilderPages();
        }

        function updateField(pageIndex, fieldIndex, key, value) {
            formBuilderPages[pageIndex].fields[fieldIndex][key] = value;
            if(key === 'type') renderBuilderPages();
        }

        function loadTemplate(name) {
            if(!FORM_TEMPLATES[name]) return;
            formBuilderPages = JSON.parse(JSON.stringify(FORM_TEMPLATES[name]));
            document.getElementById('fbTitle').value = name;
            renderBuilderPages();
        }

        function renderBuilderPages() {
            const container = document.getElementById('fbPagesContainer');
            container.innerHTML = formBuilderPages.map((page, pIdx) => `
                <div style="border:1px solid var(--border); border-radius:14px; padding:1.2rem; margin-bottom:1rem; background:#0a0a0a;">
                    <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:15px;">
                        <input type="text" value="${page.title}" oninput="formBuilderPages[${pIdx}].title=this.value" style="background:transparent; border:none; color:var(--gold); font-weight:bold; width:auto;">
                        <button class="nav-btn" style="color:#e74c3c;" onclick="removeFormPage(${pIdx})"><i class="fas fa-trash"></i></button>
                    </div>
                    ${page.fields.map((f, fIdx) => `
                        <div class="form-builder-field">
                            <div style="display:flex; justify-content:space-between; margin-bottom:10px;"><strong>Field ${fIdx+1}</strong><button onclick="removeFormField(${pIdx}, ${fIdx})" style="background:none;border:none;color:#e74c3c;cursor:pointer;"><i class="fas fa-trash"></i></button></div>
                            <input type="text" placeholder="Question / Label" value="${f.label}" oninput="updateField(${pIdx},${fIdx},'label',this.value)" style="background:#1a1a1a;">
                            <div style="display:flex; gap:10px; margin-top:8px;">
                                <select onchange="updateField(${pIdx},${fIdx},'type',this.value)" style="background:#1a1a1a;">
                                    <option value="text" ${f.type==='text'?'selected':''}>Short Text</option>
                                    <option value="textarea" ${f.type==='textarea'?'selected':''}>Paragraph</option>
                                    <option value="number" ${f.type==='number'?'selected':''}>Number</option>
                                    <option value="email" ${f.type==='email'?'selected':''}>Email</option>
                                    <option value="date" ${f.type==='date'?'selected':''}>Date</option>
                                    <option value="select-one" ${f.type==='select-one'?'selected':''}>Pick One</option>
                                    <option value="select-many" ${f.type==='select-many'?'selected':''}>Pick Many</option>
                                    <option value="file" ${f.type==='file'?'selected':''}>File Upload</option>
                                </select>
                                <label style="display:flex; align-items:center; gap:5px; color:#fff;"><input type="checkbox" ${f.required?'checked':''} onchange="updateField(${pIdx},${fIdx},'required',this.checked)"> Required</label>
                            </div>
                            ${f.type==='select-one'||f.type==='select-many' ? `<input type="text" placeholder="Options (comma separated)" value="${f.options||''}" oninput="updateField(${pIdx},${fIdx},'options',this.value)" style="background:#1a1a1a; margin-top:8px;">` : ''}
                            ${f.type==='file' ? `<input type="number" placeholder="Max file size (MB)" value="${f.maxSize||5}" oninput="updateField(${pIdx},${fIdx},'maxSize',this.value)" style="background:#1a1a1a; margin-top:8px;">` : ''}
                        </div>
                    `).join('')}
                    <button class="nav-btn" style="width:100%; margin-top:15px; border-style:dashed;" onclick="addFormField(${pIdx})">+ Add Field</button>
                </div>
            `).join('');

            // Template selector
            const templateSel = document.createElement('div');
            templateSel.innerHTML = `<label style="color:var(--dim); margin-bottom:5px; display:block;">Load Template:</label>
                <select onchange="loadTemplate(this.value)" style="width:100%; padding:0.6rem; border-radius:10px; background:#0a0a0a; color:#fff; border:1px solid var(--border);">
                    <option value="">-- Custom --</option>
                    ${Object.keys(FORM_TEMPLATES).map(t => `<option>${t}</option>`).join('')}
                </select>`;
            container.prepend(templateSel);
        }

        async function saveCustomForm() {
            const title = document.getElementById('fbTitle').value;
            const bgColor = document.getElementById('fbBgColor').value;
            if(!title || formBuilderPages.length===0) return TOAST('Title and at least one page required', 'error');
            const schema = { bgColor, pages: formBuilderPages };
            await SB.from('media').insert([{ type: 'form', title, url: JSON.stringify(schema) }]);
            TOAST('Form Published!', 'success'); closeModal('formBuilderModal'); fetchMedia();
        }

        function renderAdminForms() {
            const list = document.getElementById('formsList');
            if(ALL_FORMS.length === 0) { list.innerHTML = '<p style="color:var(--dim)">No forms created yet.</p>'; return; }
            list.innerHTML = ALL_FORMS.map(f => {
                const subsCount = ALL_SUBMISSIONS.filter(s => s.title === f.id.toString()).length;
                const link = `${window.location.origin}${window.location.pathname}#formview_${f.id}`;
                return `<div class="form-card">
                    <div><h3 style="color:var(--gold);">${f.title}</h3><p style="font-size:0.8rem; color:var(--dim);">${subsCount} responses</p></div>
                    <div style="display:flex; gap:10px; flex-wrap:wrap;">
                        <button class="nav-btn" onclick="navigator.clipboard.writeText('${link}'); TOAST('Link Copied!')"><i class="fas fa-link"></i> Copy</button>
                        <button class="nav-btn primary" onclick="viewFormAnalytics('${f.id}', '${f.title}')"><i class="fas fa-chart-pie"></i> Data</button>
                        <button class="nav-btn" style="color:#e74c3c;" onclick="delMedia('${f.id}')"><i class="fas fa-trash"></i></button>
                    </div>
                </div>`;
            }).join('');
        }

        // Public Multi-step Form Renderer
        async function renderPublicForm(formId) {
            const { data } = await SB.from('media').select('*').eq('id', formId);
            if(!data || data.length===0) return document.write('<h2>Form not found.</h2>');
            const form = data[0];
            const schema = JSON.parse(form.url);
            const container = document.getElementById('publicFormView');
            container.style.display = 'block';
            container.style.background = schema.bgColor || '#111';

            let currentPage = 0;
            const pages = schema.pages;
            function renderPage(idx) {
                const page = pages[idx];
                let html = `<div class="form-view-container"><h1 style="color:var(--gold); text-align:center; margin-bottom:5px;">${form.title}</h1><h3 style="color:#fff; text-align:center; margin-bottom:25px;">${page.title}</h3>
                <div class="step-indicator">${pages.map((_,i)=>`<div class="step-dot${i===idx?' active':''}"></div>`).join('')}</div>
                <form id="customFormStep">`;
                page.fields.forEach(f => {
                    html += `<div style="margin-bottom:18px;"><label style="display:block; margin-bottom:8px; font-weight:500;">${f.label} ${f.required?'<span style="color:#e74c3c">*</span>':''}</label>`;
                    if(f.type==='text'||f.type==='email'||f.type==='number'||f.type==='date') html += `<input type="${f.type}" name="${f.label}" ${f.required?'required':''}>`;
                    else if(f.type==='textarea') html += `<textarea name="${f.label}" rows="3" ${f.required?'required':''}></textarea>`;
                    else if(f.type==='select-one') html += `<select name="${f.label}" ${f.required?'required':''}><option value="">Select</option>${(f.options||'').split(',').map(o=>`<option>${o.trim()}</option>`).join('')}</select>`;
                    else if(f.type==='select-many') html += `<div style="display:flex; flex-wrap:wrap; gap:10px;">${(f.options||'').split(',').map(o=>`<label style="display:flex; align-items:center; gap:5px;"><input type="checkbox" name="${f.label}" value="${o.trim()}"> ${o.trim()}</label>`).join('')}</div>`;
                    else if(f.type==='file') html += `<input type="file" name="${f.label}" ${f.required?'required':''} accept="image/*,.pdf" data-maxsize="${f.maxSize||5}">`;
                    html += `</div>`;
                });
                html += `<div style="display:flex; gap:15px; margin-top:25px;">${idx>0?`<button type="button" class="nav-btn" onclick="prevPage()">Previous</button>`:''}
                ${idx<pages.length-1?`<button type="button" class="nav-btn primary" onclick="nextPage()">Next</button>`:`<button type="submit" class="modal-submit">Submit</button>`}</div></form></div>`;
                container.innerHTML = html;
                document.getElementById('customFormStep').onsubmit = (e) => handleSubmit(e);
                window.nextPage = () => { if(validatePage()) { currentPage++; renderPage(currentPage); } };
                window.prevPage = () => { currentPage--; renderPage(currentPage); };
            }

            function validatePage() {
                const page = pages[currentPage];
                let valid = true;
                page.fields.forEach(f => {
                    const el = document.querySelector(`[name="${f.label}"]`);
                    if(!el) return;
                    if(f.required && (el.type==='file'?el.files.length===0:el.value.trim()==='')) valid = false;
                    if(f.type==='email' && el.value && !/^\S+@\S+\.\S+$/.test(el.value)) valid = false;
                    if(f.type==='file' && el.files[0]) {
                        const maxSize = (f.maxSize||5)*1024*1024;
                        if(el.files[0].size > maxSize) { TOAST(`File too large (max ${f.maxSize}MB)`,'error'); valid=false; }
                    }
                });
                if(!valid) TOAST('Please fill all required fields correctly','error');
                return valid;
            }

            async function handleSubmit(e) {
                e.preventDefault();
                if(!validatePage()) return;
                const allAnswers = {};
                for(let p=0; p<pages.length; p++) {
                    const page = pages[p];
                    page.fields.forEach(f => {
                        const els = document.querySelectorAll(`[name="${f.label}"]`);
                        if(f.type==='select-many') {
                            allAnswers[f.label] = Array.from(els).filter(el=>el.checked).map(el=>el.value).join(', ');
                        } else if(f.type==='file') {
                            // Simplified: store filename, real upload would need storage
                            allAnswers[f.label] = els[0].files[0] ? els[0].files[0].name : '';
                        } else {
                            allAnswers[f.label] = els[0]?.value || '';
                        }
                    });
                }
                const code = 'XIT-'+Date.now().toString(36).toUpperCase();
                await SB.from('media').insert([{ type: 'submission', title: formId.toString(), url: JSON.stringify({code, answers: allAnswers}) }]);
                container.innerHTML = `<div class="form-view-container" style="text-align:center;"><h2 style="color:#2ecc71;"><i class="fas fa-check-circle"></i> Submitted!</h2><p>Your reference: <strong>${code}</strong></p></div>`;
            }
            renderPage(0);
        }

        // Analytics & Submissions
        let currentFormIdAnalytics = null;
        function viewFormAnalytics(formId, formTitle) {
            currentFormIdAnalytics = formId;
            const subs = ALL_SUBMISSIONS.filter(s => s.title === formId);
            document.getElementById('respCount').innerText = subs.length;
            document.getElementById('respSearch').value = '';
            window.filterSubmissions = () => {
                const q = document.getElementById('respSearch').value.toLowerCase();
                const filtered = subs.filter(s => {
                    const ans = JSON.parse(s.url).answers || {};
                    return Object.values(ans).some(v => String(v).toLowerCase().includes(q));
                });
                renderResponsesList(filtered);
            };
            renderResponsesList(subs);
            renderCharts(subs);
            document.getElementById('formResponsesModal').classList.add('show');
        }

        function renderResponsesList(subs) {
            const list = document.getElementById('responsesList');
            list.innerHTML = subs.length ? subs.map(s => {
                const data = JSON.parse(s.url);
                const ans = data.answers || {};
                const code = data.code || 'N/A';
                const name = ans['Full Name'] || ans['Name'] || Object.values(ans)[0] || 'No Name';
                return `<div class="response-card" onclick="openSingleResponse('${encodeURIComponent(JSON.stringify(ans))}')">
                    <div><strong>${code}</strong> - ${name}</div><div style="font-size:0.8rem; color:var(--dim);">${new Date(s.created_at).toLocaleString()}</div>
                </div>`;
            }).join('') : '<p style="color:var(--dim);">No responses yet.</p>';
        }

        function renderCharts(subs) {
            if(currentChart) currentChart.destroy();
            const extra = document.getElementById('chartExtra');
            extra.innerHTML = '';
            if(subs.length===0) return;
            const form = ALL_FORMS.find(f => f.id.toString() === currentFormIdAnalytics);
            if(!form) return;
            const schema = JSON.parse(form.url);
            const allFields = schema.pages.flatMap(p => p.fields);
            const counts = {};
            subs.forEach(s => {
                const ans = JSON.parse(s.url).answers;
                allFields.forEach(f => {
                    if(f.type==='select-one'||f.type==='select-many') {
                        const val = ans[f.label] || '';
                        counts[f.label] = counts[f.label] || {};
                        val.split(',').forEach(v => { v=v.trim(); counts[f.label][v] = (counts[f.label][v]||0)+1; });
                    }
                });
            });
            const firstSelect = Object.keys(counts)[0];
            if(firstSelect) {
                const ctx = document.getElementById('formChart').getContext('2d');
                const labels = Object.keys(counts[firstSelect]);
                const data = Object.values(counts[firstSelect]);
                currentChart = new Chart(ctx, { type: 'bar', data: { labels, datasets: [{ label: firstSelect, data, backgroundColor: '#d4af37' }] }, options: { plugins: { legend: { labels: { color: '#fff' } } } } });
            }
        }

        function openSingleResponse(encoded) {
            const ans = JSON.parse(decodeURIComponent(encoded));
            document.getElementById('singleResponseData').innerHTML = Object.entries(ans).map(([k,v]) => `<div style="margin-bottom:12px;"><strong style="color:var(--gold);">${k}</strong><p style="background:#0a0a0a; padding:10px; border-radius:8px; margin-top:4px;">${v}</p></div>`).join('');
            document.getElementById('singleResponseModal').classList.add('show');
        }

        function closeModal(id) { document.getElementById(id).classList.remove('show'); }
        function openMenu() { document.getElementById('mobileMenu').classList.add('open'); document.getElementById('mobileOverlay').classList.add('show'); }
        function closeMenu() { document.getElementById('mobileMenu').classList.remove('open'); document.getElementById('mobileOverlay').classList.remove('show'); }
    </script>
</body>
</html>