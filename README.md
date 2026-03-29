<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA V18 - SUPORTE PRO</title>
    <style>
        :root { --green: #00a859; --orange: #f37021; --gray: #f4f4f4; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--gray); margin: 0; padding: 0; }
        .app-screen { display: none; min-height: 100vh; flex-direction: column; width: 100%; }
        .active { display: flex !important; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; box-sizing: border-box; }
        .header { background: var(--orange); padding: 15px; color: white; text-align: center; border-radius: 0 0 20px 20px; position: sticky; top: 0; z-index: 100; }
        
        /* CHAT ESTILO WHATSAPP */
        .chat-container { flex: 1; display: flex; flex-direction: column; background: #e5ddd5; position: relative; height: 70vh; overflow: hidden; }
        .chat-msgs { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 8px; }
        .bubble { padding: 8px 12px; border-radius: 10px; max-width: 80%; font-size: 14px; position: relative; box-shadow: 0 1px 1px rgba(0,0,0,0.1); }
        .bubble-user { background: #dcf8c6; align-self: flex-end; border-top-right-radius: 0; }
        .bubble-adm { background: #fff; align-self: flex-start; border-top-left-radius: 0; }
        .bubble img { max-width: 100%; border-radius: 5px; margin-top: 5px; display: block; }
        
        .chat-input-area { background: #f0f0f0; padding: 10px; display: flex; gap: 8px; align-items: center; border-top: 1px solid #ddd; }
        .chat-input { flex: 1; padding: 10px; border-radius: 20px; border: 1px solid #ccc; outline: none; }
        
        /* ADM LISTA DE ABAS */
        .chat-tab { background: white; padding: 15px; margin-bottom: 5px; border-radius: 10px; cursor: pointer; display: flex; justify-content: space-between; align-items: center; border-left: 5px solid var(--orange); }
        .badge { background: var(--orange); color: white; padding: 2px 8px; border-radius: 10px; font-size: 10px; }

        .btn { background: var(--green); color: white; border: none; padding: 12px; border-radius: 10px; font-weight: bold; width: 100%; cursor: pointer; }
        .nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; left: 50%; transform: translateX(-50%); z-index: 1000; }
        .input-group { margin-bottom: 10px; width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
    </style>
</head>
<body>

<section id="scr-login" class="app-screen">
    <div class="container">
        <h1 style="text-align:center; color:var(--green)">MEDELA</h1>
        <input type="text" id="l-cpf" placeholder="CPF" class="input-group" oninput="maskCPF(this)">
        <input type="password" id="l-pass" placeholder="Senha" class="input-group">
        <button class="btn" onclick="login()">ENTRAR</button>
        <button class="btn" style="background:#444; margin-top:10px;" onclick="abrirSuporteCliente()">SUPORTE / ESQUECI SENHA</button>
        <p onclick="askAdmin()" style="text-align:center; font-size:10px; color:#ccc; margin-top:50px;">ADM</p>
    </div>
</section>

<section id="scr-home" class="app-screen">
    <div class="header"><h3 id="u-name">Medela Mercado</h3></div>
    <div class="container">
        <p>Bem-vindo à nossa loja!</p>
        <div id="p-grid" style="display:grid; grid-template-columns:1fr 1fr; gap:10px;"></div>
    </div>
</section>

<section id="scr-chat" class="app-screen">
    <div class="header">
        <div style="display:flex; justify-content:space-between; align-items:center;">
            <button onclick="voltarChat()" style="background:none; border:none; color:white; font-size:20px;">❮</button>
            <span id="chat-with-name">Suporte Medela</span>
            <span></span>
        </div>
    </div>
    <div class="chat-container">
        <div id="chat-msgs" class="chat-msgs"></div>
        <div class="chat-input-area">
            <label style="cursor:pointer; font-size:20px;">📷<input type="file" hidden accept="image/*" onchange="enviarMidia(this)"></label>
            <input type="text" id="chat-input" class="chat-input" placeholder="Digite uma mensagem...">
            <button onclick="enviarTexto()" style="background:var(--green); color:white; border:none; border-radius:50%; width:35px; height:35px;">➔</button>
        </div>
    </div>
</section>

<section id="scr-admin-chats" class="app-screen">
    <div class="header"><h2>Conversas Ativas</h2></div>
    <div class="container" id="lista-abas-chat"></div>
    <button class="btn" style="background:#333; width:90%; margin:20px auto;" onclick="location.reload()">SAIR ADM</button>
</section>

<nav class="nav" id="bot-nav" style="display:none;">
    <button onclick="go('scr-home')">LOJA</button>
    <button onclick="abrirSuporteCliente()">SUPORTE</button>
    <button onclick="logout()">SAIR</button>
</nav>

<script>
    let db_chats = JSON.parse(localStorage.getItem('m_chats_v18')) || {};
    let user = JSON.parse(localStorage.getItem('m_user'));
    let isAdmin = false;
    let chatAtivoCPF = "";

    function go(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('bot-nav').style.display = (id === 'scr-home') ? 'flex' : 'none';
    }

    // --- LÓGICA DO SUPORTE ---
    function abrirSuporteCliente() {
        if(!user) {
            let tempCPF = prompt("Para suporte, digite seu CPF:");
            if(!tempCPF) return;
            chatAtivoCPF = "VISITANTE_" + tempCPF.replace(/\D/g, "");
        } else {
            chatAtivoCPF = user.cpf;
        }
        document.getElementById('chat-with-name').innerText = "Suporte Medela";
        renderizarMensagens();
        go('scr-chat');
    }

    function abrirChatAdm(cpf) {
        chatAtivoCPF = cpf;
        document.getElementById('chat-with-name').innerText = "Cliente: " + cpf;
        renderizarMensagens();
        go('scr-chat');
    }

    function enviarTexto() {
        let input = document.getElementById('chat-input');
        if(!input.value) return;
        pushMsg({ tipo: 'texto', conteudo: input.value, remetente: isAdmin ? 'adm' : 'user' });
        input.value = "";
    }

    function enviarMidia(input) {
        if(input.files && input.files[0]) {
            let reader = new FileReader();
            reader.onload = function(e) {
                pushMsg({ tipo: 'img', conteudo: e.target.result, remetente: isAdmin ? 'adm' : 'user' });
            };
            reader.readAsDataURL(input.files[0]);
        }
    }

    function pushMsg(msgObj) {
        if(!db_chats[chatAtivoCPF]) db_chats[chatAtivoCPF] = [];
        db_chats[chatAtivoCPF].push(msgObj);
        localStorage.setItem('m_chats_v18', JSON.stringify(db_chats));
        renderizarMensagens();
    }

    function renderizarMensagens() {
        let area = document.getElementById('chat-msgs');
        let msgs = db_chats[chatAtivoCPF] || [];
        area.innerHTML = msgs.map(m => `
            <div class="bubble ${m.remetente === (isAdmin ? 'adm' : 'user') ? 'bubble-user' : 'bubble-adm'}">
                ${m.tipo === 'texto' ? m.conteudo : `<img src="${m.conteudo}">`}
            </div>
        `).join('');
        area.scrollTop = area.scrollHeight;
    }

    function voltarChat() {
        if(isAdmin) { renderAbasAdm(); go('scr-admin-chats'); }
        else { go(user ? 'scr-home' : 'scr-login'); }
    }

    // --- ADMIN ABAS ---
    function renderAbasAdm() {
        isAdmin = true;
        let lista = document.getElementById('lista-abas-chat');
        let cpfs = Object.keys(db_chats);
        lista.innerHTML = cpfs.map(cpf => `
            <div class="chat-tab" onclick="abrirChatAdm('${cpf}')">
                <span><b>${cpf}</b></span>
                <span class="badge">${db_chats[cpf].length} msgs</span>
            </div>
        `).join('') || "<p style='text-align:center'>Nenhuma conversa ainda.</p>";
    }

    // --- SISTEMA BASE ---
    function login() {
        let c = document.getElementById('l-cpf').value;
        let p = document.getElementById('l-pass').value;
        let s = localStorage.getItem('u_' + c);
        if(s && JSON.parse(s).senha === p) {
            user = JSON.parse(s);
            localStorage.setItem('m_user', s);
            go('scr-home');
        } else alert("CPF ou Senha inválidos.");
    }

    function askAdmin() {
        if(prompt("Senha Mestra:") === "123") {
            isAdmin = true;
            renderAbasAdm();
            go('scr-admin-chats');
        }
    }

    function logout() { localStorage.removeItem('m_user'); location.reload(); }
    function maskCPF(i) { let v = i.value.replace(/\D/g,""); v = v.replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d{1,2})$/,"$1-$2"); i.value = v; }
</script>
</body>
</html>
