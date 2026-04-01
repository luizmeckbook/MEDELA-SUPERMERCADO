<html lang="pt-br">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0">  
    <title id="siteTitle">Medela Supermercado</title>  
    <style id="dynamicStyles">  
        :root { --primary: #e53935; --admin-bg: #2c3e50; --bg: #f4f4f4; --whatsapp: #25d366; }  
    </style>  
    <style>  
        body { margin:0; font-family: 'Segoe UI', Arial, sans-serif; background: var(--bg); overflow-x: hidden; }  
        .tela { display:none; min-height: 100vh; width: 100%; }  
        .ativa { display:block; }  
        .container { padding: 15px; max-width: 600px; margin: auto; }  
        .card { background: white; padding: 15px; border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); margin-bottom: 15px; }  
        input, select { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }  
        button { border: none; padding: 12px; border-radius: 8px; font-weight: bold; cursor: pointer; color: white; width: 100%; transition: 0.2s; }  
        .btn-main { background: var(--primary); }  
        .btn-support { background: var(--whatsapp); margin-top: 10px; display: flex; align-items: center; justify-content: center; gap: 8px; }  
        .header { background: var(--primary); color: white; padding: 15px; display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 100; }  
        
        .chat-screen { display: flex; flex-direction: column; height: 100vh; background: #e5ddd5; }  
        .chat-messages { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 8px; }  
        .msg { max-width: 80%; padding: 10px; border-radius: 10px; font-size: 14px; box-shadow: 0 1px 1px rgba(0,0,0,0.1); }  
        .msg.sent { align-self: flex-end; background: #dcf8c6; }  
        .msg.received { align-self: flex-start; background: white; }  
        .chat-footer { padding: 10px; background: #f0f0f0; display: flex; gap: 5px; }  
        
        .cat-nav { display: flex; gap: 10px; overflow-x: auto; padding: 10px 0; }  
        .cat-btn { background: white; border: 1px solid #ddd; padding: 8px 15px; border-radius: 20px; white-space: nowrap; width: auto; color: #333; }  
        .cat-btn.active { background: var(--primary); color: white; }  
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }  

        .cart-bar { position: fixed; bottom: 0; left: 0; right: 0; background: #333; color: white; padding: 20px; display: none; justify-content: space-between; align-items: center; z-index: 200; border-radius: 20px 20px 0 0; cursor: pointer; }
        .remove-btn { background: #ff5252; padding: 5px 10px; width: auto; font-size: 11px; }
        .profit-badge { background: #e8f5e9; color: #2e7d32; padding: 2px 6px; border-radius: 4px; font-size: 10px; font-weight: bold; }
        
        .report-table { width: 100%; border-collapse: collapse; font-size: 11px; margin-top: 10px; }
        .report-table th, .report-table td { border: 1px solid #eee; padding: 6px; text-align: left; }
    </style>
</head>  
<body>  

<div id="login" class="tela ativa">  
    <div class="container" style="text-align:center; padding-top:60px;">  
        <h1 id="brandName">🛒 Medela</h1>  
        <div class="card">  
            <input id="lCpf" placeholder="CPF ou 'admin'">  
            <input id="lSenha" type="password" placeholder="Senha">  
            <button class="btn-main" onclick="entrar()">Entrar</button>  
            <button class="btn-support" onclick="abrirSuporteVisitante()">💬 Suporte</button>  
            <p onclick="ir('cadastro')" style="color:var(--primary); cursor:pointer; margin-top:20px;">Criar Conta</p>  
        </div>  
    </div>  
</div>  

<div id="cadastro" class="tela">  
    <div class="container">  
        <h2>Criar Conta</h2>  
        <input id="cNome" placeholder="Nome Completo">  
        <input id="cCpf" placeholder="CPF">  
        <input id="cSenha" type="password" placeholder="Senha">  
        <button class="btn-main" onclick="registrar()">Finalizar Cadastro</button>  
        <button onclick="ir('login')" style="background:#888; margin-top:10px;">Voltar</button>  
    </div>  
</div>  

<div id="home" class="tela">  
    <div class="header" id="headerCliente">  
        <span id="welcome">Olá</span>  
        <button style="width:auto; background:rgba(0,0,0,0.2)" onclick="voltarParaLogin()">Sair</button>  
    </div>  
    <div class="container" style="padding-bottom: 100px;">  
        <input type="text" id="pesquisaLoja" placeholder="Pesquisar produto..." oninput="renderizarLoja()">
        <div class="cat-nav" id="catNav"></div>  
        <div class="grid" id="listaLoja"></div>  
    </div>  
    <div class="cart-bar" id="cartBar" onclick="ir('carrinho')">
        <span>🛒 <b id="cartCount">0</b> itens</span>
        <b id="cartTotal">R$ 0,00</b>
        <span style="background:var(--whatsapp); padding:5px 10px; border-radius:8px">ABRIR</span>
    </div>
    <button onclick="ir('chat')" style="position:fixed; bottom:90px; right:20px; width:60px; height:60px; border-radius:50%; background:var(--whatsapp); border:none; font-size:25px; color:white; box-shadow: 0 4px 10px rgba(0,0,0,0.3); z-index: 150;">💬</button>  
</div>  

<div id="carrinho" class="tela">
    <div class="header">
        <button onclick="ir('home')" style="width:auto; background:none; font-size:20px">←</button>
        <span>Meu Carrinho</span>
        <div style="width:30px"></div>
    </div>
    <div class="container">
        <div id="listaItensCarrinho"></div>
        <div class="card" id="cardPagamento" style="display:none;">
            <h3>Pagamento</h3>
            <select id="metodoPagamento" onchange="toggleTroco()">
                <option value="">Selecione a forma...</option>
                <option value="Pix">Pix</option>
                <option value="Cartão de Crédito">Cartão de Crédito</option>
                <option value="Cartão de Débito">Cartão de Débito</option>
                <option value="Dinheiro">Dinheiro</option>
            </select>
            <div id="divTroco" style="display:none;">
                <input id="valorTroco" type="number" placeholder="Troco para quanto?">
            </div>
            <button class="btn-main" onclick="finalizarPedido()" style="margin-top:15px; background:var(--whatsapp)">Finalizar via WhatsApp</button>
        </div>
    </div>
</div>

<div id="admin" class="tela">  
    <div class="header" id="headerAdmin" style="background:var(--admin-bg)">  
        <span>⚙️ Painel Gestor</span>  
        <button style="width:auto; background:rgba(255,255,255,0.2)" onclick="voltarParaLogin()">Sair</button>  
    </div>  
    <div class="container">  
        <div class="card">  
            <h3>📊 Relatório de Vendas (Por Data)</h3>
            <div id="relatorioVendas" style="overflow-x:auto"></div>
            <button onclick="limparRelatorio()" style="background:#888; font-size:10px; margin-top:10px; width:auto; padding:5px">Limpar Histórico</button>
        </div>

        <div class="card">  
            <h3>🎨 Modelagem do Site</h3>  
            <input id="cfgNome" placeholder="Nome do Mercado">  
            <input type="color" id="cfgCor">  
            <button onclick="salvarConfigSemRefresh()" class="btn-main">Aplicar Agora</button>  
        </div>  
        <div class="card">  
            <h3>📦 Novo Produto</h3>  
            <input id="pNome" placeholder="Nome do Produto">  
            <div style="display:flex; gap:10px">
                <input id="pBruto" type="number" step="0.01" placeholder="Custo (Bruto)">  
                <input id="pLiquido" type="number" step="0.01" placeholder="Venda (Líquido)">
            </div>
            <select id="pCat">  
                <option>Açougue</option><option>Bebida</option><option>Limpeza</option><option>Padaria</option><option>Mercearia</option>  
            </select>  
            <button onclick="addProduto()" style="background:var(--admin-bg)">Cadastrar e Salvar</button>  
            <hr>
            <h3>🔍 Gerenciar Produtos</h3>
            <input type="text" id="pesquisaAdmin" placeholder="Procurar produto..." oninput="renderizarAdmin()">
            <div id="admin_lista_excluir" style="margin-top:10px; max-height: 250px; overflow-y: auto;"></div>
        </div>  
        <div class="card">  
            <h3>💬 Conversas Ativas</h3>  
            <div id="lista_chats_admin"></div>  
        </div>  
        <div class="card">  
            <h3>👥 Clientes e Senhas</h3>  
            <div id="lista_usuarios_admin"></div>  
        </div>  
    </div>
</div>  

<div id="chat" class="tela">  
    <div class="chat-screen">  
        <div class="header" id="chatHeaderCustom">  
            <button onclick="voltarDoChat()" style="width:auto; background:none; font-size:20px">←</button>  
            <span id="chatTitulo">Chat Suporte</span>  
            <div style="width:30px"></div>  
        </div>  
        <div class="chat-messages" id="box_msgs"></div>  
        <div class="chat-footer">  
            <input id="msg_input" placeholder="Digite uma mensagem...">  
            <button onclick="enviarMensagem()" style="width:50px; border-radius:50%; background:var(--whatsapp)">➤</button>  
        </div>  
    </div>  
</div>  

<script>  
let usuarios = JSON.parse(localStorage.getItem("m_users")) || {};  
let produtos = JSON.parse(localStorage.getItem("m_prod")) || [];  
let config = JSON.parse(localStorage.getItem("m_cfg")) || { nome: "Medela Supermercado", cor: "#e53935" };  
let mensagens = JSON.parse(localStorage.getItem("m_chat")) || {};  
let vendas = JSON.parse(localStorage.getItem("m_vendas")) || []; 
let carrinho = [];

let sessaoAtiva = "";   
let clienteNoChat = "";   
let modoAdminChat = false;  
let categoriaAtual = "Todos";

function aplicarEstilos() {  
    document.getElementById("brandName").innerText = "🛒 " + config.nome;  
    document.getElementById("siteTitle").innerText = config.nome;  
    document.getElementById("dynamicStyles").innerHTML = `:root { --primary: ${config.cor}; --admin-bg: #2c3e50; --bg: #f4f4f4; --whatsapp: #25d366; }`;  
    document.getElementById("headerCliente").style.backgroundColor = config.cor;  
    document.getElementById("chatHeaderCustom").style.backgroundColor = config.cor;
}  
  
function ir(tela) {  
    document.querySelectorAll('.tela').forEach(t => t.classList.remove('ativa'));  
    document.getElementById(tela).classList.add('ativa');  
    if(tela === 'home') renderizarLoja();
    if(tela === 'admin') renderizarAdmin();  
    if(tela === 'carrinho') renderizarCarrinho();
    if(tela === 'chat') renderizarMensagens();
}  
  
function entrar() {  
    let cpf = document.getElementById("lCpf").value;  
    let senha = document.getElementById("lSenha").value;  
    if(cpf === 'admin' || cpf === 'SUPER-MEDELA#') {
        if(senha === 'MEDELA1053@') { sessaoAtiva = "admin"; ir('admin'); return; }
    }  
    if(usuarios[cpf] && usuarios[cpf].senha === senha) { sessaoAtiva = cpf; ir('home'); }  
    else { alert("Acesso Negado!"); }  
}  
  
function registrar() {  
    let n = document.getElementById("cNome").value;  
    let c = document.getElementById("cCpf").value;  
    let s = document.getElementById("cSenha").value;  
    if(!n || !c || !s) return alert("Preencha todos os campos");  
    usuarios[c] = { nome: n, senha: s };  
    localStorage.setItem("m_users", JSON.stringify(usuarios));  
    alert("Cadastro realizado!"); ir('login');  
}  

function addCarrinho(idUnico) {
    let p = produtos.find(item => item.id === idUnico);
    if(p) {
        carrinho.push(p);
        atualizarBarraCarrinho();
    }
}

function removerCarrinho(index) {
    carrinho.splice(index, 1);
    renderizarCarrinho();
    atualizarBarraCarrinho();
}

function atualizarBarraCarrinho() {
    let total = carrinho.reduce((a, b) => a + b.liquido, 0);
    let bar = document.getElementById("cartBar");
    if(carrinho.length > 0) {
        bar.style.display = "flex";
        document.getElementById("cartCount").innerText = carrinho.length;
        document.getElementById("cartTotal").innerText = "R$ " + total.toFixed(2);
    } else {
        bar.style.display = "none";
    }
}

function renderizarCarrinho() {
    let html = "";
    carrinho.forEach((p, i) => {
        html += `<div class="card" style="display:flex; justify-content:space-between; align-items:center;">
            <div><b>${p.nome}</b><br>R$ ${p.liquido.toFixed(2)}</div>
            <button class="remove-btn" onclick="removerCarrinho(${i})">Remover</button>
        </div>`;
    });
    document.getElementById("listaItensCarrinho").innerHTML = html || "<p style='text-align:center; padding:20px;'>O carrinho está vazio.</p>";
    document.getElementById("cardPagamento").style.display = carrinho.length > 0 ? "block" : "none";
}

function toggleTroco() {
    let m = document.getElementById("metodoPagamento").value;
    document.getElementById("divTroco").style.display = m === "Dinheiro" ? "block" : "none";
}

function finalizarPedido() {
    let metodo = document.getElementById("metodoPagamento").value;
    if(!metodo) return alert("Escolha o pagamento");
    
    let totalL = carrinho.reduce((a, b) => a + b.liquido, 0);
    let totalB = carrinho.reduce((a, b) => a + b.bruto, 0);
    let hoje = new Date().toLocaleDateString();
    
    vendas.push({ data: hoje, bruto: totalB, liquido: totalL });
    localStorage.setItem("m_vendas", JSON.stringify(vendas));

    let msg = `*Pedido - ${config.nome}*\n\n`;
    carrinho.forEach(p => msg += `• ${p.nome}: R$ ${p.liquido.toFixed(2)}\n`);
    msg += `\n*Total:* R$ ${totalL.toFixed(2)}\n*Pagamento:* ${metodo}`;
    
    window.open(`https://wa.me/55?text=${encodeURIComponent(msg)}`);
    carrinho = []; // Limpa após pedir
    ir('home');
}

function renderizarLoja() {  
    document.getElementById("welcome").innerText = "Olá, " + (usuarios[sessaoAtiva]?.nome || "Cliente");  
    const cats = ["Todos", "Açougue", "Bebida", "Limpeza", "Padaria", "Mercearia"];  
    document.getElementById("catNav").innerHTML = cats.map(c => `<button class="cat-btn ${categoriaAtual === c ? 'active' : ''}" onclick="filtrar('${c}')">${c}</button>`).join('');  
    
    let busca = document.getElementById("pesquisaLoja").value.toLowerCase();
    let filtrados = categoriaAtual === "Todos" ? produtos : produtos.filter(p => p.cat === categoriaAtual);
    if(busca) filtrados = filtrados.filter(p => p.nome.toLowerCase().includes(busca));

    document.getElementById("listaLoja").innerHTML = filtrados.map((p) => `
        <div class="card" style="text-align:center">  
            <strong>${p.nome}</strong><br><span style="color:green">R$ ${p.liquido.toFixed(2)}</span>
            <button class="btn-main" onclick="addCarrinho(${p.id})" style="margin-top:10px; padding:5px; font-size:12px;">+ Adicionar</button>
        </div>`).join('') || "<p style='grid-column:1/3'>Sem produtos.</p>";  
}  

function filtrar(c) { categoriaAtual = c; renderizarLoja(); }

function renderizarAdmin() {  
    // Relatório
    const r = {};
    vendas.forEach(v => {
        if(!r[v.data]) r[v.data] = { b: 0, l: 0 };
        r[v.data].b += v.bruto; r[v.data].l += v.liquido;
    });
    let hRel = `<table class="report-table"><tr><th>Data</th><th>Custo</th><th>Venda</th><th>Lucro</th></tr>`;
    for(let d in r) hRel += `<tr><td>${d}</td><td>R$ ${r[d].b.toFixed(2)}</td><td>R$ ${r[d].l.toFixed(2)}</td><td style="color:green">R$ ${(r[d].l-r[d].b).toFixed(2)}</td></tr>`;
    document.getElementById("relatorioVendas").innerHTML = Object.keys(r).length ? hRel + "</table>" : "Sem vendas.";

    // Chats
    document.getElementById("lista_chats_admin").innerHTML = Object.keys(mensagens).map(id => `
        <button onclick="abrirChatAdmin('${id}')" style="background:#eee; color:#333; margin-bottom:5px; text-align:left">
            💬 ${usuarios[id]?.nome || id} (${mensagens[id].length} msgs)
        </button>
    `).join('') || "Sem conversas.";

    // Gerenciar Produtos (Busca Corrigida)
    let bAdmin = document.getElementById("pesquisaAdmin").value.toLowerCase();
    let pFiltro = bAdmin ? produtos.filter(p => p.nome.toLowerCase().includes(bAdmin)) : produtos;
    document.getElementById("admin_lista_excluir").innerHTML = pFiltro.map((p) => `
        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:8px; font-size:12px; border-bottom:1px solid #eee; padding-bottom:5px">
            <div><b>${p.nome}</b><br>L: R$ ${p.liquido.toFixed(2)} <span class="profit-badge">Lucro: R$ ${(p.liquido-p.bruto).toFixed(2)}</span></div>
            <button onclick="excluirProd(${p.id})" style="background:red; padding:4px 8px; width:auto">Excluir</button>
        </div>`).join('');

    document.getElementById("lista_usuarios_admin").innerHTML = Object.keys(usuarios).map(c => `<div style="font-size:12px"><b>${usuarios[c].nome}</b> - ${c}</div>`).join('');
}  

function addProduto() {  
    let n = document.getElementById("pNome").value;  
    let b = parseFloat(document.getElementById("pBruto").value);  
    let l = parseFloat(document.getElementById("pLiquido").value);  
    if(!n || isNaN(b) || isNaN(l)) return alert("Dados inválidos");
    produtos.push({ id: Date.now(), nome: n, bruto: b, liquido: l, cat: document.getElementById("pCat").value });  
    localStorage.setItem("m_prod", JSON.stringify(produtos));  
    document.getElementById("pNome").value = "";
    renderizarAdmin();  
}  

function excluirProd(id) {
    if(confirm("Excluir produto?")) {
        produtos = produtos.filter(p => p.id !== id);
        localStorage.setItem("m_prod", JSON.stringify(produtos));
        renderizarAdmin();
    }
}

function limparRelatorio() { if(confirm("Limpar vendas?")) { vendas = []; localStorage.setItem("m_vendas", JSON.stringify(vendas)); renderizarAdmin(); } }

function salvarConfigSemRefresh() {  
    config.nome = document.getElementById("cfgNome").value || config.nome;  
    config.cor = document.getElementById("cfgCor").value;  
    localStorage.setItem("m_cfg", JSON.stringify(config));  
    aplicarEstilos(); alert("Salvo!");  
}  
  
function voltarParaLogin() { sessaoAtiva = ""; carrinho = []; ir('login'); }  

function abrirSuporteVisitante() { sessaoAtiva = "visitante_" + Math.floor(Math.random()*999); clienteNoChat = sessaoAtiva; modoAdminChat = false; ir('chat'); }  
function abrirChatAdmin(id) { clienteNoChat = id; modoAdminChat = true; ir('chat'); }  
function enviarMensagem() { 
    let inp = document.getElementById("msg_input"); 
    if(!inp.value) return; 
    let id = modoAdminChat ? clienteNoChat : sessaoAtiva; 
    if(!mensagens[id]) mensagens[id] = []; 
    mensagens[id].push({ texto: inp.value, autor: modoAdminChat ? 'admin' : 'cliente' }); 
    localStorage.setItem("m_chat", JSON.stringify(mensagens)); 
    inp.value = ""; renderizarMensagens(); 
}  
function renderizarMensagens() { 
    let id = modoAdminChat ? clienteNoChat : sessaoAtiva; 
    let msgs = mensagens[id] || []; 
    document.getElementById("box_msgs").innerHTML = msgs.map(m => `<div class="msg ${m.autor===(modoAdminChat?'admin':'cliente')?'sent':'received'}">${m.texto}</div>`).join(''); 
    document.getElementById("box_msgs").scrollTop = 9999; 
}  
function voltarDoChat() { modoAdminChat ? ir('admin') : (sessaoAtiva.includes("visitante") ? ir('login') : ir('home')); }  

window.onload = aplicarEstilos;  
</script>  
</body>  
</html>
