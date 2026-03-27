<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA V17 - GESTÃO TOTAL</title>
    <style>
        :root { --green: #00a859; --orange: #f37021; --gray: #f4f4f4; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--gray); margin: 0; padding: 0; }
        .app-screen { display: none; min-height: 100vh; flex-direction: column; width: 100%; }
        .active { display: flex !important; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; box-sizing: border-box; }
        .header { background: var(--orange); padding: 15px; color: white; text-align: center; border-radius: 0 0 20px 20px; position: sticky; top: 0; z-index: 100; }
        
        /* Estilo Cards e Inputs */
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; padding: 15px; padding-bottom: 120px; }
        .card { background: white; border-radius: 12px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        .card img { width: 100%; height: 80px; object-fit: contain; }
        .btn { background: var(--green); color: white; border: none; padding: 12px; border-radius: 10px; font-weight: bold; width: 100%; cursor: pointer; margin-top: 5px; }
        .btn-edit { background: #2196F3; padding: 5px; font-size: 10px; margin-right: 5px; }
        .btn-del { background: #ff4444; padding: 5px; font-size: 10px; }
        .input-group { margin-bottom: 10px; width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        
        /* ADM e Navegação */
        .cat-bar { display: flex; overflow-x: auto; padding: 10px; gap: 8px; background: white; white-space: nowrap; scrollbar-width: none; }
        .cat-item { padding: 8px 15px; background: #eee; border-radius: 20px; font-size: 11px; font-weight: bold; cursor: pointer; }
        .cat-item.active-cat { background: var(--green); color: white; }
        .nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; left: 50%; transform: translateX(-50%); z-index: 1000; }
        .admin-item { background: white; padding: 12px; margin-bottom: 8px; border-radius: 10px; display: flex; align-items: center; justify-content: space-between; border-left: 5px solid var(--orange); }
    </style>
</head>
<body>

<section id="scr-login" class="app-screen">
    <div class="container">
        <div style="text-align: center; margin: 40px 0;">
            <h1 style="color: var(--green); margin:0;">MEDELA</h1>
            <p style="color:var(--orange); font-weight:bold;">SUPERMERCADO</p>
        </div>
        <input type="text" id="l-cpf" placeholder="Seu CPF" class="input-group" oninput="maskCPF(this)" maxlength="14">
        <input type="password" id="l-pass" placeholder="Sua Senha" class="input-group">
        <button class="btn" onclick="login()">ENTRAR NO MERCADO</button>
        <p style="text-align:center; font-size:13px; margin-top:20px;">Novo cliente? <span style="color:var(--orange); cursor:pointer; font-weight:bold;" onclick="go('scr-reg')">Criar Conta</span></p>
        <button class="btn" style="background:#444; margin-top:20px;" onclick="abrirSuporte('visitante')">SUPORTE</button>
        <p style="text-align:center; font-size:10px; color:#ccc; margin-top:50px;" onclick="askAdmin()">ADM</p>
    </div>
</section>

<section id="scr-reg" class="app-screen">
    <div class="header"><h2>Cadastro</h2></div>
    <div class="container">
        <input type="text" id="r-nome" placeholder="Nome Completo" class="input-group">
        <input type="text" id="r-cpf" placeholder="CPF" class="input-group" oninput="maskCPF(this)" maxlength="14">
        <input type="password" id="r-pass" placeholder="Senha de Acesso" class="input-group">
        <button class="btn" onclick="register()">FINALIZAR E ENTRAR</button>
        <button class="btn" style="background:#888" onclick="go('scr-login')">VOLTAR</button>
    </div>
</section>

<section id="scr-home" class="app-screen">
    <div class="header">
        <h3 id="u-name" style="margin:0">Olá!</h3>
        <input type="text" placeholder="🔍 Buscar no mercado..." style="width:100%; margin-top:10px; padding:10px; border-radius:8px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div id="cat-loja" class="cat-bar"></div>
    <div id="p-grid" class="grid"></div>
</section>

<section id="scr-admin" class="app-screen">
    <div class="header"><h2>Painel Administrativo</h2></div>
    <div class="cat-bar">
        <div class="cat-item active-cat" id="tab-p" onclick="abaAdmin('estoque')">📦 Estoque</div>
        <div class="cat-item" id="tab-u" onclick="abaAdmin('usuarios')">👥 Usuários</div>
    </div>
    
    <div class="container" id="adm-content">
        <div style="background:white; padding:15px; border-radius:10px; margin-bottom:20px;">
            <h4 style="margin-top:0">Novo/Editar Produto</h4>
            <input type="hidden" id="edit-id">
            <input type="text" id="a-nome" placeholder="Nome do Produto" class="input-group">
            <input type="number" id="a-preco" placeholder="Preço (ex: 10.50)" class="input-group" step="0.01">
            <select id="a-tipo" class="input-group">
                <option value="UN">Unidade (UN)</option>
                <option value="KG">Quilo (KG)</option>
            </select>
            <select id="a-cat" class="input-group">
                <option>Mercearia</option><option>Açougue</option><option>Hortifrúti</option>
                <option>Bebidas</option><option>Limpeza</option><option>Padaria</option>
            </select>
            <button class="btn" onclick="salvarProduto()">SALVAR NO ESTOQUE</button>
            <button id="btn-cancel-edit" class="btn" style="background:#888; display:none;" onclick="limparFormProd()">CANCELAR EDIÇÃO</button>
        </div>
        <div id="adm-list-estoque"></div>
    </div>
    <button class="btn" style="background:#333; width:90%; margin: 10px auto;" onclick="sairAdmin()">SAIR DO MODO ADM</button>
</section>

<nav class="nav" id="bot-nav" style="display:none;">
    <button onclick="go('scr-home')">🏠 LOJA</button>
    <button onclick="logout()">🚪 SAIR</button>
</nav>

<script>
    const CATS = ["Mercearia", "Açougue", "Hortifrúti", "Bebidas", "Limpeza", "Padaria"];
    let db = JSON.parse(localStorage.getItem('m_db')) || [];
    let user = JSON.parse(localStorage.getItem('m_user'));
    let isAdmin = localStorage.getItem('m_is_admin') === 'true';
    let curCat = "Mercearia";

    window.onload = () => {
        if (isAdmin) { go('scr-admin'); abaAdmin('estoque'); }
        else if (user) { go('scr-home'); }
        else { go('scr-login'); }
    };

    function go(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('bot-nav').style.display = (id === 'scr-home') ? 'flex' : 'none';
        if(id === 'scr-home') renderHome();
    }

    // --- LOGICA DE PRODUTOS (ADM) ---
    function salvarProduto() {
        const id = document.getElementById('edit-id').value;
        const nome = document.getElementById('a-nome').value;
        const preco = parseFloat(document.getElementById('a-preco').value);
        const tipo = document.getElementById('a-tipo').value;
        const cat = document.getElementById('a-cat').value;

        if(!nome || isNaN(preco)) return alert("Preencha nome e preço!");

        if(id) { // EDITAR
            const index = db.findIndex(p => p.id == id);
            db[index] = { id: Number(id), nome, preco, tipo, cat };
        } else { // NOVO
            db.push({ id: Date.now(), nome, preco, tipo, cat });
        }

        localStorage.setItem('m_db', JSON.stringify(db));
        limparFormProd();
        renderEstoqueAdm();
    }

    function editarProd(id) {
        const p = db.find(x => x.id == id);
        document.getElementById('edit-id').value = p.id;
        document.getElementById('a-nome').value = p.nome;
        document.getElementById('a-preco').value = p.preco;
        document.getElementById('a-tipo').value = p.tipo;
        document.getElementById('a-cat').value = p.cat;
        document.getElementById('btn-cancel-edit').style.display = 'block';
    }

    function deletarProd(id) {
        if(confirm("Excluir este produto?")) {
            db = db.filter(p => p.id != id);
            localStorage.setItem('m_db', JSON.stringify(db));
            renderEstoqueAdm();
        }
    }

    function limparFormProd() {
        document.getElementById('edit-id').value = "";
        document.getElementById('a-nome').value = "";
        document.getElementById('a-preco').value = "";
        document.getElementById('btn-cancel-edit').style.display = 'none';
    }

    function renderEstoqueAdm() {
        const list = document.getElementById('adm-list-estoque');
        list.innerHTML = db.map(p => `
            <div class="admin-item">
                <div><b>${p.nome}</b><br><small>${p.cat} - R$ ${p.preco.toFixed(2)}/${p.tipo}</small></div>
                <div>
                    <button class="btn btn-edit" onclick="editarProd(${p.id})">EDITAR</button>
                    <button class="btn btn-del" onclick="deletarProd(${p.id})">X</button>
                </div>
            </div>
        `).join('');
    }

    // --- LOJA CLIENTE ---
    function setCatLoja(c) { curCat = c; renderHome(); }

    function renderHome(s = "") {
        document.getElementById('u-name').innerText = "Olá, " + user.nome.split(' ')[0];
        document.getElementById('cat-loja').innerHTML = CATS.map(c => `<div class="cat-item ${c===curCat?'active-cat':''}" onclick="setCatLoja('${c}')">${c}</div>`).join('');
        
        const filtered = db.filter(p => p.cat === curCat && p.nome.toLowerCase().includes(s.toLowerCase()));
        document.getElementById('p-grid').innerHTML = filtered.map(p => `
            <div class="card">
                <img src="https://via.placeholder.com/80?text=${p.nome[0]}">
                <b style="font-size:12px;">${p.nome}</b>
                <span style="color:var(--green); font-size:14px;">R$ ${p.preco.toFixed(2)}</span>
                <button class="btn" style="font-size:10px; padding:5px;">ADICIONAR</button>
            </div>
        `).join('');
    }

    // --- AUTH ---
    function login() {
        const c = document.getElementById('l-cpf').value, p = document.getElementById('l-pass').value;
        const s = localStorage.getItem(`u_${c}`);
        if(s && JSON.parse(s).senha === p) {
            user = JSON.parse(s);
            localStorage.setItem('m_user', s);
            go('scr-home');
        } else alert("CPF ou Senha incorretos!");
    }

    function register() {
        const n = document.getElementById('r-nome').value, c = document.getElementById('r-cpf').value, p = document.getElementById('r-pass').value;
        if(!n || !c || !p) return alert("Preencha tudo!");
        const d = { nome: n, cpf: c, senha: p };
        localStorage.setItem(`u_${c}`, JSON.stringify(d));
        user = d; localStorage.setItem('m_user', JSON.stringify(d));
        go('scr-home');
    }

    function abaAdmin(t) {
        document.getElementById('adm-list-estoque').style.display = t === 'estoque' ? 'block' : 'none';
        if(t === 'estoque') renderEstoqueAdm();
        // Lógica de usuários (conforme v16) pode ser chamada aqui
    }

    function maskCPF(i) { let v = i.value.replace(/\D/g,""); v = v.replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d{1,2})$/,"$1-$2"); i.value = v; }
    function askAdmin() { if(prompt("Senha:")==="123") { localStorage.setItem('m_is_admin','true'); location.reload(); } }
    function logout() { localStorage.removeItem('m_user'); location.reload(); }
    function sairAdmin() { localStorage.removeItem('m_is_admin'); location.reload(); }
</script>
</body>
</html>
