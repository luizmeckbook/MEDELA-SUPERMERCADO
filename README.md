<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title id="siteTitle">Medela Supermercado</title>
    <style id="dynamicStyles">
        :root { --primary: #e53935; --admin-bg: #2c3e50; --bg: #f4f4f4; --whatsapp: #25d366; }
    </style>
    <style>
        body { margin:0; font-family: 'Segoe UI', Arial, sans-serif; background: var(--bg); }
        .tela { display:none; min-height: 100vh; position: relative; }
        .ativa { display:block; }
        .container { padding: 15px; max-width: 900px; margin: auto; }
        .card { background: white; padding: 15px; border-radius: 12px; box-shadow: 0 2px 5px rgba(0,0,0,0.1); margin-bottom: 15px; }
        input, select { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        button { border: none; padding: 12px; border-radius: 8px; font-weight: bold; cursor: pointer; color: white; width: 100%; }
        .btn-main { background: var(--primary); }
        .header { background: var(--primary); color: white; padding: 15px; display: flex; justify-content: space-between; align-items: center; position: sticky; top:0; z-index: 10; }
        
        /* Chat Estilo WhatsApp */
        .chat-screen { display: flex; flex-direction: column; height: 100vh; background: #e5ddd5; }
        .chat-messages { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 10px; }
        .msg { max-width: 80%; padding: 8px 12px; border-radius: 12px; font-size: 14px; position: relative; line-height: 1.4; }
        .msg.sent { align-self: flex-end; background: #dcf8c6; border-bottom-right-radius: 2px; }
        .msg.received { align-self: flex-start; background: white; border-bottom-left-radius: 2px; }
        .chat-input-area { background: #f0f0f0; padding: 10px; display: flex; gap: 10px; align-items: center; }
        .chat-input-area input { margin: 0; flex: 1; border-radius: 25px; padding: 10px 20px; }
        .btn-send { width: 50px; height: 50px; border-radius: 50%; background: var(--whatsapp); font-size: 20px; }

        /* Floating Button */
        .float-chat { position: fixed; bottom: 20px; right: 20px; width: 60px; height: 60px; border-radius: 50%; background: var(--whatsapp); font-size: 30px; box-shadow: 0 4px 10px rgba(0,0,0,0.3); z-index: 100; }
        
        /* Grid */
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
        .cat-chip { padding: 8px 15px; background: white; border-radius: 20px; margin-right: 5px; cursor: pointer; border: 1px solid #ddd; white-space: nowrap; }
        .cat-chip.active { background: var(--primary); color: white; }
    </style>
</head>
<body>

<div id="login" class="tela ativa">
    <div class="container" style="text-align:center; padding-top:80px;">
        <h1 id="brandName">🛒 Medela</h1>
        <div class="card">
            <input id="lCpf" placeholder="CPF ou 'admin'">
            <input id="lSenha" type="password" placeholder="Senha">
            <button class="btn-main" onclick="entrar()">Entrar</button>
            <p onclick="ir('cadastro')" style="color:blue; cursor:pointer; margin-top:15px;">Criar Conta</p>
        </div>
    </div>
</div>

<div id="cadastro" class="tela">
    <div class="container">
        <h2>Criar Conta</h2>
        <input id="cNome" placeholder="Nome Completo">
        <input id="cCpf" placeholder="CPF">
        <input id="cSenha" type="password" placeholder="Senha">
        <button class="btn-main" onclick="registrar()">Cadastrar</button>
        <button onclick="ir('login')" style="background:none; color:gray; margin-top:10px;">Voltar</button>
    </div>
</div>

<div id="home" class="tela">
    <div class="header">
        <span id="welcome">Olá</span>
        <button style="width:auto; background:rgba(0,0,0,0.2)" onclick="sair()">Sair</button>
    </div>
    <div class="container">
        <div id="catBar" style="display:flex; overflow-x:auto; padding-bottom:10px;"></div>
        <div class="grid" id="listaLoja"></div>
    </div>
    <button class="float-chat" onclick="abrirChatCliente()">💬</button>
</div>

<div id="chat_cliente" class="tela">
    <div class="chat-screen">
        <div class="header" style="background:#075e54">
            <button onclick="ir('home')" style="width:auto; background:none; font-size:20px">←</button>
            <span>Suporte Medela</span>
            <div style="width:30px"></div>
        </div>
        <div class="chat-messages" id="msgs_cliente"></div>
        <div class="chat-input-area">
            <input id="input_msg_cliente" placeholder="Mensagem">
            <button class="btn-send" onclick="enviarMensagem('cliente')">➤</button>
        </div>
    </div>
</div>

<div id="admin" class="tela">
    <div class="header" style="background:var(--admin-bg)">
        <span>⚙️ Gestão Medela</span>
        <button style="width:auto; background:rgba(255,255,255,0.2)" onclick="sair()">Sair</button>
    </div>
    <div class="container">
        <div class="card">
            <h3>🎨 Personalizar Site</h3>
            <input id="cfgNome" placeholder="Nome do Mercado">
            <input type="color" id="cfgCor">
            <button onclick="salvarConfig()" style="background:var(--admin-bg)">Aplicar</button>
        </div>

        <div class="card">
            <h3>💬 Conversas dos Clientes</h3>
            <div id="lista_conversas_admin"></div>
        </div>

        <div class="card">
            <h3>📦 Novo Produto</h3>
            <input id="pNome" placeholder="Produto">
            <input id="pPreco" type="number" placeholder="Preço">
            <select id="pCat">
                <option>Açougue</option><option>Bebida</option><option>Limpeza</option><option>Padaria</option><option>Mercearia</option>
            </select>
            <button onclick="addProduto()" style="background:var(--admin-bg)">Adicionar</button>
        </div>

        <div class="card">
            <h3>👥 Usuários e Senhas</h3>
            <div id="lista_usuarios_admin"></div>
        </div>
    </div>
</div>

<div id="chat_admin" class="tela">
    <div class="chat-screen">
        <div class="header" style="background:#075e54">
            <button onclick="ir('admin')" style="width:auto; background:none; font-size:20px">←</button>
            <span id="nome_cliente_chat">Cliente</span>
            <div style="width:30px"></div>
        </div>
        <div class="chat-messages" id="msgs_admin"></div>
        <div class="chat-input-area">
            <input id="input_msg_admin" placeholder="Responder cliente...">
            <button class="btn-send" onclick="enviarMensagem('admin')">➤</button>
        </div>
    </div>
</div>

<script>
let produtos = JSON.parse(localStorage.getItem("med_prod")) || [];
let usuarios = JSON.parse(localStorage.getItem("med_users")) || {};
let mensagens = JSON.parse(localStorage.getItem("med_chat")) || {}; 
let config = JSON.parse(localStorage.getItem("med_config")) || { nome: "Medela Supermercado", cor: "#e53935" };
let usuarioAtivo = ""; // CPF do usuário logado
let chatSelecionado = ""; // CPF do cliente que o admin está respondendo

function aplicarConfig() {
    document.getElementById("brandName").innerText = "🛒 " + config.nome;
    document.getElementById("dynamicStyles").innerHTML = `:root { --primary: ${config.cor}; --admin-bg: #2c3e50; --bg: #f4f4f4; --whatsapp: #25d366; }`;
}

function ir(tela) {
    document.querySelectorAll('.tela').forEach(t => t.classList.remove('ativa'));
    document.getElementById(tela).classList.add('ativa');
    if(tela === 'home') renderizarLoja();
    if(tela === 'admin') renderizarAdmin();
}

function entrar() {
    let cpf = document.getElementById("lCpf").value;
    let senha = document.getElementById("lSenha").value;
    if(cpf === 'admin' && senha === 'admin123') return ir('admin');
    if(usuarios[cpf] && usuarios[cpf].senha === senha) {
        usuarioAtivo = cpf;
        localStorage.setItem("sessao", cpf);
        ir('home');
    } else { alert("Erro!"); }
}

function registrar() {
    let nome = document.getElementById("cNome").value;
    let cpf = document.getElementById("cCpf").value;
    let senha = document.getElementById("cSenha").value;
    usuarios[cpf] = { nome, senha };
    localStorage.setItem("med_users", JSON.stringify(usuarios));
    ir('login');
}

/* CHAT LOGIC */
function abrirChatCliente() {
    ir('chat_cliente');
    renderizarMensagens('msgs_cliente', usuarioAtivo);
}

function abrirChatAdmin(cpfCliente) {
    chatSelecionado = cpfCliente;
    document.getElementById("nome_cliente_chat").innerText = usuarios[cpfCliente].nome;
    ir('chat_admin');
    renderizarMensagens('msgs_admin', cpfCliente);
}

function enviarMensagem(quem) {
    let input = document.getElementById(quem === 'cliente' ? "input_msg_cliente" : "input_msg_admin");
    let cpf = quem === 'cliente' ? usuarioAtivo : chatSelecionado;
    
    if(!input.value) return;
    if(!mensagens[cpf]) mensagens[cpf] = [];

    mensagens[cpf].push({
        texto: input.value,
        autor: quem, // 'cliente' ou 'admin'
        data: new Date().toLocaleTimeString()
    });

    localStorage.setItem("med_chat", JSON.stringify(mensagens));
    input.value = "";
    renderizarMensagens(quem === 'cliente' ? 'msgs_cliente' : 'msgs_admin', cpf);
}

function renderizarMensagens(containerId, cpfCliente) {
    let div = document.getElementById(containerId);
    let msgs = mensagens[cpfCliente] || [];
    div.innerHTML = msgs.map(m => `
        <div class="msg ${m.autor === (containerId === 'msgs_cliente' ? 'cliente' : 'admin') ? 'sent' : 'received'}">
            ${m.texto} <br><small style="font-size:10px; opacity:0.5">${m.data}</small>
        </div>
    `).join('');
    div.scrollTop = div.scrollHeight;
}

/* ADMIN LOGIC */
function renderizarAdmin() {
    // Lista de Clientes com Senha
    document.getElementById("lista_usuarios_admin").innerHTML = Object.keys(usuarios).map(cpf => `
        <div class="card" style="margin-bottom:5px"><b>${usuarios[cpf].nome}</b> | CPF: ${cpf} | Senha: <b style="color:red">${usuarios[cpf].senha}</b></div>
    `).join('');

    // Lista de Conversas
    document.getElementById("lista_conversas_admin").innerHTML = Object.keys(mensagens).map(cpf => `
        <div class="card" onclick="abrirChatAdmin('${cpf}')" style="cursor:pointer; border-left: 5px solid var(--whatsapp)">
            <strong>${usuarios[cpf] ? usuarios[cpf].nome : cpf}</strong><br>
            <small>Clique para responder</small>
        </div>
    `).join('') || "Nenhuma conversa iniciada.";
}

function salvarConfig() {
    config.nome = document.getElementById("cfgNome").value || config.nome;
    config.cor = document.getElementById("cfgCor").value;
    localStorage.setItem("med_config", JSON.stringify(config));
    location.reload();
}

function addProduto() {
    let nome = document.getElementById("pNome").value;
    let preco = parseFloat(document.getElementById("pPreco").value);
    let cat = document.getElementById("pCat").value;
    produtos.push({id: Date.now(), nome, preco, cat});
    localStorage.setItem("med_prod", JSON.stringify(produtos));
    alert("Adicionado!");
}

function renderizarLoja() {
    document.getElementById("welcome").innerText = "Olá, " + (usuarios[usuarioAtivo] ? usuarios[usuarioAtivo].nome : "Cliente");
    document.getElementById("listaLoja").innerHTML = produtos.map(p => `
        <div class="card" style="text-align:center">
            <strong>${p.nome}</strong><br>
            <span style="color:green">R$ ${p.preco.toFixed(2)}</span><br>
            <small>${p.cat}</small>
            <button class="btn-main" style="margin-top:10px">Adicionar</button>
        </div>
    `).join('');
}

function sair() { localStorage.clear(); location.reload(); }

window.onload = () => {
    aplicarConfig();
    let s = localStorage.getItem("sessao");
    if(s) { usuarioAtivo = s; ir('home'); }
};
</script>
</body>
</html>
