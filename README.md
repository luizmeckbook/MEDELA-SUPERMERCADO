<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO - V15 GOLD</title>
    <style>
        :root { --green: #00a859; --orange: #f37021; --gray: #f4f4f4; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--gray); margin: 0; padding: 0; }
        .app-screen { display: none; min-height: 100vh; flex-direction: column; width: 100%; }
        .active { display: flex !important; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; box-sizing: border-box; }
        .header { background: var(--orange); padding: 15px; color: white; text-align: center; border-radius: 0 0 20px 20px; position: sticky; top: 0; z-index: 100; }
        
        /* Câmera e Chat */
        .camera-box { width: 100%; background: #000; border-radius: 12px; height: 200px; overflow: hidden; margin-bottom: 10px; border: 2px solid #ddd; }
        #video { width: 100%; height: 100%; object-fit: cover; transform: scaleX(-1); }
        .chat-area { flex: 1; overflow-y: auto; padding: 15px; background: #e5ddd5; display: flex; flex-direction: column; gap: 8px; margin-bottom: 80px; }
        .msg { padding: 10px; border-radius: 10px; max-width: 80%; font-size: 14px; box-shadow: 0 1px 2px rgba(0,0,0,0.1); }
        .msg-u { background: #dcf8c6; align-self: flex-end; }
        .msg-a { background: #fff; align-self: flex-start; }
        .msg img { max-width: 100%; border-radius: 5px; margin-top: 5px; display: block; }

        /* Loja e Itens */
        .cat-bar { display: flex; overflow-x: auto; padding: 10px; gap: 8px; background: white; white-space: nowrap; scrollbar-width: none; }
        .cat-item { padding: 8px 15px; background: #eee; border-radius: 20px; font-size: 11px; font-weight: bold; cursor: pointer; }
        .cat-item.active-cat { background: var(--green); color: white; }
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; padding: 15px; padding-bottom: 120px; }
        .card { background: white; border-radius: 12px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.1); display: flex; flex-direction: column; justify-content: space-between; }
        .card img { width: 100%; height: 80px; object-fit: contain; }

        .btn { background: var(--green); color: white; border: none; padding: 12px; border-radius: 10px; font-weight: bold; width: 100%; cursor: pointer; }
        .input-group { margin-bottom: 10px; width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        
        .nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; left: 50%; transform: translateX(-50%); z-index: 1000; }
        .nav-btn { font-size: 11px; color: #aaa; border: none; background: none; font-weight: bold; cursor: pointer; }
        .admin-item { background: white; padding: 10px; margin-bottom: 8px; border-radius: 10px; display: flex; align-items: center; border-left: 5px solid var(--orange); }
        .user-img-adm { width: 55px; height: 55px; border-radius: 50%; object-fit: cover; margin-right: 12px; border: 2px solid #ddd; }
    </style>
</head>
<body>

<section id="scr-login" class="app-screen">
    <div class="container">
        <div style="text-align: center; margin: 35px 0;">
            <h1 style="color: var(--green); margin:0;">MEDELA</h1>
            <p style="color:var(--orange); font-weight:bold;">SUPERMERCADO</p>
        </div>
        <input type="text" id="l-cpf" placeholder="CPF" class="input-group" oninput="maskCPF(this)" maxlength="14">
        <input type="password" id="l-pass" placeholder="Senha" class="input-group">
        <button class="btn" onclick="login()">ENTRAR</button>
        <p style="text-align:center; font-size:12px;">Não tem conta? <span style="color:var(--orange); cursor:pointer; font-weight:bold;" onclick="go('scr-reg')">Cadastre-se</span></p>
        <button class="btn" style="background:#444; margin-top:10px;" onclick="abrirSuporte('visitante')">SUPORTE / AJUDA</button>
        <p style="text-align:center; font-size:10px; color:#ccc; margin-top:40px;" onclick="askAdmin()">PAINEL ADMINISTRATIVO</p>
    </div>
</section>

<section id="scr-reg" class="app-screen">
    <div class="header"><h2>Criar Conta</h2></div>
    <div class="container">
        <div class="camera-box" id="cam-area"><video id="video" autoplay playsinline></video></div>
        <img id="photo-preview" style="width:100%; border-radius:12px; display:none; margin-bottom:10px; border: 3px solid var(--green);">
        <button id="btn-snap" class="btn" style="background:#444; margin-bottom:10px;" onclick="takeSnap()">TIRAR SELFIE DE SEGURANÇA</button>
        <button id="btn-retake" class="btn" style="background:#888; margin-bottom:10px; display:none;" onclick="startCam()">TIRAR OUTRA FOTO</button>

        <input type="text" id="r-nome" placeholder="Nome Completo" class="input-group">
        <input type="text" id="r-cpf" placeholder="CPF (Seu Login)" class="input-group" oninput="maskCPF(this)" maxlength="14">
        <input type="password" id="r-pass" placeholder="Crie uma Senha" class="input-group">
        <input type="text" id="r-tel" placeholder="Celular" class="input-group" oninput="maskTel(this)" maxlength="15">
        <label style="font-size:11px; color:#666;">Data de Nascimento:</label>
        <input type="date" id="r-nasc" class="input-group">
        <input type="text" id="r-end" placeholder="Endereço Completo" class="input-group">
        <button class="btn" onclick="register()">FINALIZAR CADASTRO</button>
        <button class="btn" style="background:#888; margin-top:5px;" onclick="go('scr-login')">VOLTAR</button>
    </div>
</section>

<section id="scr-home" class="app-screen">
    <div class="header">
        <h3 id="u-name" style="margin:0">Olá!</h3>
        <input type="text" placeholder="🔍 Buscar produtos..." style="width:100%; margin-top:10px; padding:10px; border-radius:8px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div id="cat-loja" class="cat-bar"></div>
    <div id="p-grid" class="grid"></div>
</section>

<section id="scr-cart" class="app-screen">
    <div class="header"><h2>Minha Cesta</h2></div>
    <div class="container" id="c-list" style="padding-bottom: 220px;"></div>
    <div id="c-foot" class="container" style="position:fixed; bottom:60px; background:white; width:100%; max-width:450px; left:50%; transform:translateX(-50%); border-top:1px solid #eee; display:none;">
        <h3 style="color:var(--green)">Total: R$ <span id="t-val">0,00</span></h3>
        <button class="btn" style="background:#25d366" onclick="sendZap()">PEDIR NO WHATSAPP</button>
    </div>
</section>

<section id="scr-suporte" class="app-screen">
    <div class="header"><h2 id="sup-title">Suporte</h2></div>
    <div id="chat-box" class="chat-area"></div>
    <div class="container" style="position:fixed; bottom:60px; background:#f0f0f0; width:100%; max-width:450px; left:50%; transform:translateX(-50%); display:flex; padding:10px; gap:5px;">
        <label style="background:var(--orange); color:white; padding:10px; border-radius:50%; cursor:pointer;">
            📷 <input type="file" accept="image/*" style="display:none" onchange="enviarMsg(true, this)">
        </label>
        <input type="text" id="msg-input" placeholder="Mensagem..." style="flex:1; border-radius:20px; border:1px solid #ccc; padding:10px;">
        <button onclick="enviarMsg(false)" style="background:var(--green); color:white; border:none; border-radius:50%; width:40px;">➔</button>
    </div>
</section>

<section id="scr-admin" class="app-screen">
    <div class="header"><h2>Gestão Medela</h2></div>
    <div class="cat-bar">
        <div class="cat-item active-cat" id="tab-p" onclick="abaAdmin('produtos')">Estoque</div>
        <div class="cat-item" id="tab-u" onclick="abaAdmin('usuarios')">Nuvem Usuários</div>
        <div class="cat-item" id="tab-s" onclick="abaAdmin('suporte')">Chats</div>
    </div>
    <div class="container" id="adm-content"></div>
</section>

<nav class="nav" id="bot-nav" style="display:none;">
    <button class="nav-btn" onclick="go('scr-home')">LOJA</button>
    <button class="nav-btn" onclick="go('scr-cart')">CESTA (<span id="count">0</span>)</button>
    <button class="nav-btn" onclick="abrirSuporte()">SUPORTE</button>
    <button class="nav-btn" onclick="logout()">SAIR</button>
</nav>

<canvas id="canvas" style="display:none;"></canvas>

<script>
    const CATS = ["Mercearia", "Açougue", "Hortifrúti", "Bebidas", "Limpeza", "Padaria", "Fiambreria"];
    let db = JSON.parse(localStorage.getItem('m_db')) || [];
    let chats = JSON.parse(localStorage.getItem('m_chats')) || {};
    let user = JSON.parse(localStorage.getItem('m_user'));
    let isAdmin = localStorage.getItem('m_is_admin') === 'true';
    let cart = [];
    let curCat = "Mercearia";
    let curChatID = "";
    let stream = null;
    let fotoRosto = "";

    window.onload = () => {
        if (isAdmin) { go('scr-admin'); abaAdmin('produtos'); }
        else if (user) { go('scr-home'); }
        else { go('scr-login'); }
    };

    function go(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('bot-nav').style.display = (id === 'scr-home' || id === 'scr-cart' || id === 'scr-suporte') ? 'flex' : 'none';
        
        if(id === 'scr-reg') startCam();
        else if(stream) { stream.getTracks().forEach(t => t.stop()); stream = null; }

        if(id === 'scr-home') renderHome();
        if(id === 'scr-cart') renderCart();
        if(id === 'scr-suporte') renderChat();
    }

    // --- CÂMERA ---
    async function startCam() {
        document.getElementById('cam-area').style.display = 'block';
        document.getElementById('photo-preview').style.display = 'none';
        document.getElementById('btn-snap').style.display = 'block';
        document.getElementById('btn-retake').style.display = 'none';
        try { stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } }); document.getElementById('video').srcObject = stream; } 
        catch (e) { alert("Câmera desligada."); }
    }
    function takeSnap() {
        const v = document.getElementById('video'); const c = document.getElementById('canvas');
        c.width = v.videoWidth; c.height = v.videoHeight;
        c.getContext('2d').drawImage(v, 0, 0);
        fotoRosto = c.toDataURL('image/jpeg');
        document.getElementById('photo-preview').src = fotoRosto;
        document.getElementById('photo-preview').style.display = 'block';
        document.getElementById('cam-area').style.display = 'none';
        document.getElementById('btn-snap').style.display = 'none';
        document.getElementById('btn-retake').style.display = 'block';
        stream.getTracks().forEach(t => t.stop());
    }

    // --- LOJA ---
    function setCatLoja(c) { curCat = c; document.getElementById('cat-loja').innerHTML = CATS.map(cat => `<div class="cat-item ${cat===curCat?'active-cat':''}" onclick="setCatLoja('${cat}')">${cat}</div>`).join(''); renderHome(); }
    function renderHome(s = "") {
        if(!document.getElementById('cat-loja').innerHTML) setCatLoja(curCat);
        const filtered = db.filter(p => p.cat === curCat && p.nome.toLowerCase().includes(s.toLowerCase()));
        document.getElementById('p-grid').innerHTML = filtered.map(p => `
            <div class="card">
                <img src="${p.foto || 'https://via.placeholder.com/80'}">
                <b style="font-size:11px;">${p.nome}</b>
                <span style="color:var(--green); font-size:13px;">R$ ${p.preco.toFixed(2)} / ${p.tipo}</span>
                <div style="display:flex; gap:3px;">
                    <input type="${p.tipo==='UN'?'number':'text'}" id="qty-${p.id}" value="1" style="width:40px; border:1px solid #ddd; border-radius:5px; text-align:center;">
                    <button class="btn" style="margin:0; padding:5px; font-size:10px;" onclick="addToCart(${p.id})">ADD</button>
                </div>
            </div>`).join('');
        document.getElementById('u-name').innerText = "Olá, " + user.nome.split(' ')[0];
    }
    function addToCart(id) {
        const p = db.find(x => x.id === id);
        let val = document.getElementById(`qty-${id}`).value.replace(',', '.');
        let q = parseFloat(val);
        if (p.tipo === 'UN' && !Number.isInteger(q)) return alert("Use números inteiros para Unidades.");
        cart.push({...p, qty: q, total: p.preco * q});
        document.getElementById('count').innerText = cart.length;
    }
    function renderCart() {
        const cL = document.getElementById('c-list');
        if(cart.length===0) { cL.innerHTML="Vazio"; document.getElementById('c-foot').style.display="none"; return; }
        cL.innerHTML = cart.map((p,i) => `<div class="admin-item"><span>${p.nome}<br><small>${p.qty}${p.tipo}</small></span><b>R$ ${p.total.toFixed(2)} <span onclick="remCart(${i})" style="color:red; cursor:pointer">X</span></b></div>`).join('');
        document.getElementById('t-val').innerText = (cart.reduce((a,b)=>a+b.total,0)+7).toFixed(2);
        document.getElementById('c-foot').style.display="block";
    }
    function remCart(i) { cart.splice(i,1); renderCart(); document.getElementById('count').innerText = cart.length; }

    // --- SUPORTE ---
    function abrirSuporte(tipo) {
        curChatID = tipo === 'visitante' ? 'Vist_'+Date.now() : user.cpf;
        go('scr-suporte');
    }
    function enviarMsg(isFoto, input) {
        if(!chats[curChatID]) chats[curChatID] = [];
        if(isFoto) {
            const r = new FileReader(); r.onload = (e) => { chats[curChatID].push({ from:'user', img: e.target.result }); salvarChat(); };
            r.readAsDataURL(input.files[0]);
        } else {
            let t = document.getElementById('msg-input'); if(!t.value) return;
            chats[curChatID].push({ from:'user', text: t.value }); t.value = ""; salvarChat();
        }
    }
    function salvarChat() { localStorage.setItem('m_chats', JSON.stringify(chats)); renderChat(); }
    function renderChat() {
        const box = document.getElementById('chat-box');
        box.innerHTML = (chats[curChatID] || []).map(m => `<div class="msg ${m.from==='user'?'msg-u':'msg-a'}">${m.text || ''} ${m.img ? `<img src="${m.img}">`:''}</div>`).join('');
        box.scrollTop = box.scrollHeight;
    }

    // --- ADMIN ---
    function abaAdmin(tipo) {
        const cont = document.getElementById('adm-content');
        document.querySelectorAll('.cat-item').forEach(c => c.classList.remove('active-cat'));
        if(tipo === 'usuarios') {
            document.getElementById('tab-u').classList.add('active-cat');
            cont.innerHTML = `<input type="text" placeholder="🔍 Buscar Nome ou CPF..." class="input-group" oninput="renderUsersAdm(this.value)"><div id="list-u"></div>`;
            renderUsersAdm();
        } else if(tipo === 'suporte') {
            document.getElementById('tab-s').classList.add('active-cat');
            cont.innerHTML = `<div id="list-s"></div>`;
            document.getElementById('list-s').innerHTML = Object.keys(chats).map(id => `<div class="admin-item" onclick="respChat('${id}')">Chat: ${id} ➔</div>`).join('') || "Sem mensagens.";
        } else {
            document.getElementById('tab-p').classList.add('active-cat');
            cont.innerHTML = `<div style="background:white; padding:10px; border-radius:10px;"><input type="text" id="a-nome" placeholder="Produto" class="input-group"><input type="number" id="a-preco" placeholder="Preço" class="input-group"><select id="a-tipo" class="input-group"><option value="UN">UN</option><option value="KG">KG</option></select><select id="a-cat" class="input-group">${CATS.map(c=>`<option>${c}</option>`).join('')}</select><button class="btn" onclick="addP()">SALVAR</button></div><div id="list-p"></div>`;
            renderP();
        }
    }
    function renderUsersAdm(f = "") {
        let html = "";
        for (let i = 0; i < localStorage.length; i++) {
            let k = localStorage.key(i);
            if(k.startsWith("u_")) {
                let u = JSON.parse(localStorage.getItem(k));
                if(u.nome.toLowerCase().includes(f.toLowerCase()) || u.cpf.includes(f)) {
                    html += `<div class="admin-item"><img src="${u.rosto}" class="user-img-adm"><div><b>${u.nome}</b><br><small>CPF: ${u.cpf} | Senha: <b>${u.senha}</b></small></div></div>`;
                }
            }
        }
        document.getElementById('list-u').innerHTML = html || "Nada encontrado.";
    }
    function respChat(id) { let r = prompt("Resposta:"); if(r) { chats[id].push({from:'admin', text:r}); localStorage.setItem('m_chats', JSON.stringify(chats)); abaAdmin('suporte'); } }
    function addP() { let n = document.getElementById('a-nome').value, p = parseFloat(document.getElementById('a-preco').value), t = document.getElementById('a-tipo').value, c = document.getElementById('a-cat').value; db.push({id:Date.now(), nome:n, preco:p, tipo:t, cat:c}); localStorage.setItem('m_db', JSON.stringify(db)); renderP(); }
    function renderP() { document.getElementById('list-p').innerHTML = db.map(p=>`<div class="admin-item">${p.nome} <button onclick="remP(${p.id})">X</button></div>`).join(''); }
    function remP(id) { db = db.filter(x=>x.id!==id); localStorage.setItem('m_db', JSON.stringify(db)); renderP(); }

    // --- SISTEMA ---
    function login() {
        const c = document.getElementById('l-cpf').value, p = document.getElementById('l-pass').value;
        const s = localStorage.getItem(`u_${c}`);
        if(s && JSON.parse(s).senha === p) { user = JSON.parse(s); localStorage.setItem('m_user', s); location.reload(); } else alert("Erro!");
    }
    function register() {
        const c = document.getElementById('r-cpf').value, p = document.getElementById('r-pass').value;
        if(!fotoRosto || !c || !p) return alert("Falta foto ou dados!");
        const d = {nome:document.getElementById('r-nome').value, cpf:c, senha:p, rosto:fotoRosto, tel:document.getElementById('r-tel').value, end:document.getElementById('r-end').value, nascimento:document.getElementById('r-nasc').value};
        localStorage.setItem(`u_${c}`, JSON.stringify(d)); user = d; localStorage.setItem('m_user', JSON.stringify(d)); location.reload();
    }
    function maskCPF(i) { let v = i.value.replace(/\D/g,""); v = v.replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d{1,2})$/,"$1-$2"); i.value = v; }
    function maskTel(i) { let v = i.value.replace(/\D/g,""); v = v.replace(/^(\d{2})(\d)/g,"($1) $2").replace(/(\d)(\d{4})$/,"$1-$2"); i.value = v; }
    function askAdmin() { if(prompt("Senha:")==="123") { localStorage.setItem('m_is_admin','true'); location.reload(); } }
    function logout() { localStorage.removeItem('m_user'); location.reload(); }
    function sairAdmin() { localStorage.removeItem('m_is_admin'); location.reload(); }
    function sendZap() {
        let t = document.getElementById('t-val').innerText;
        let msg = `*PEDIDO MEDELA*\n*Cliente:* ${user.nome}\n*CPF:* ${user.cpf}\n*Produtos:* ${cart.length}\n*TOTAL: R$ ${t}*`;
        window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`);
    }
</script>
</body>
</html>
