<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Medela Supermercado - Admin System</title>
    <style>
        :root { --primary: #e53935; --admin-color: #2c3e50; --bg: #f4f4f4; --green: #2ecc71; }
        body { margin:0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: var(--bg); }
        .tela { display:none; min-height: 100vh; }
        .ativa { display:block; }

        /* Estilos Comuns */
        .container { padding: 15px; max-width: 800px; margin: auto; }
        input, select { width:100%; padding:12px; margin:8px 0; border-radius:8px; border:1px solid #ddd; box-sizing: border-box; }
        button { background: var(--primary); color:white; border:none; padding:12px; border-radius:8px; width:100%; cursor:pointer; font-weight:bold; }
        .card { background: white; padding: 15px; border-radius: 10px; box-shadow: 0 2px 5px rgba(0,0,0,0.1); margin-bottom: 10px; }
        
        /* Header */
        .header { background: var(--primary); color:white; padding:15px; display:flex; justify-content: space-between; align-items: center; }
        .admin-header { background: var(--admin-color); }

        /* Grid de Produtos */
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
        
        /* Admin Table */
        table { width: 100%; border-collapse: collapse; background: white; margin-top: 10px; }
        th, td { border: 1px solid #ddd; padding: 8px; text-align: left; font-size: 14px; }
        th { background: #eee; }

        /* Floating Cart */
        .cart-float { position:fixed; bottom:20px; right:20px; width:60px; height:60px; border-radius:50%; background:var(--green); color:white; border:none; font-size:24px; box-shadow: 0 4px 10px rgba(0,0,0,0.3); cursor:pointer; }
    </style>
</head>
<body>

<div id="login" class="tela ativa">
    <div class="container" style="text-align:center; padding-top:100px;">
        <h2>🛒 Medela Supermercado</h2>
        <input id="cpfLogin" placeholder="CPF (use 'admin' para painel)">
        <input id="senhaLogin" type="password" placeholder="Senha">
        <button onclick="entrar()">Entrar</button>
        <p onclick="ir('cadastro')" style="color:var(--primary); cursor:pointer; margin-top:20px;">Criar nova conta</p>
    </div>
</div>

<div id="cadastro" class="tela">
    <div class="container">
        <h2>Criar Conta</h2>
        <input id="nomeCad" placeholder="Nome Completo">
        <input id="cpfCad" placeholder="CPF (Único)">
        <input id="senhaCad" type="password" placeholder="Senha">
        <button onclick="cadastrar()">Finalizar Cadastro</button>
        <button onclick="ir('login')" style="background:#888; margin-top:10px;">Voltar</button>
    </div>
</div>

<div id="home" class="tela">
    <div class="header">
        <span id="txtBoasVindas">Olá!</span>
        <button style="width:auto; background:rgba(0,0,0,0.2)" onclick="sair()">Sair</button>
    </div>
    <div class="container">
        <select id="filtroCat" onchange="renderizarLoja()">
            <option value="Todos">Todas as Categorias</option>
            <option value="Alimentos">Alimentos</option>
            <option value="Bebidas">Bebidas</option>
            <option value="Limpeza">Limpeza</option>
        </select>
        <div id="listaProdutosLoja" class="grid"></div>
    </div>
    <button class="cart-float" onclick="alert('Funcionalidade de finalizar pedido...')">🛒</button>
</div>

<div id="admin" class="tela">
    <div class="header admin-header">
        <span>Painel de Controle</span>
        <button style="width:auto; background:rgba(255,255,255,0.2)" onclick="sair()">Sair Admin</button>
    </div>
    <div class="container">
        <div class="card">
            <h3>Novo Produto</h3>
            <input id="pNome" placeholder="Nome do Produto">
            <input id="pPreco" type="number" placeholder="Preço (Ex: 10.50)">
            <select id="pCat">
                <option value="Alimentos">Alimentos</option>
                <option value="Bebidas">Bebidas</option>
                <option value="Limpeza">Limpeza</option>
            </select>
            <button onclick="salvarProduto()" style="background:var(--admin-color)">Adicionar na Loja</button>
        </div>

        <div class="card">
            <h3>Gerenciar Produtos</h3>
            <div id="listaAdminProdutos"></div>
        </div>

        <div class="card">
            <h3>Clientes Cadastrados</h3>
            <table id="tabelaClientes">
                <thead><tr><th>Nome</th><th>CPF</th></tr></thead>
                <tbody></tbody>
            </table>
        </div>
    </div>
</div>

<script>
/* BANCO DE DADOS */
let db_produtos = JSON.parse(localStorage.getItem("medela_produtos")) || [
    {id: Date.now(), nome: "Café 500g", preco: 18.90, cat: "Alimentos"}
];

/* NAVEGAÇÃO */
function ir(tela) {
    document.querySelectorAll('.tela').forEach(t => t.classList.remove('ativa'));
    document.getElementById(tela).classList.add('ativa');
    if(tela === 'home') renderizarLoja();
    if(tela === 'admin') renderizarAdmin();
}

/* CADASTRO E LOGIN */
function cadastrar() {
    const nome = document.getElementById("nomeCad").value;
    const cpf = document.getElementById("cpfCad").value;
    const senha = document.getElementById("senhaCad").value;
    
    if(!nome || !cpf || !senha) return alert("Preencha tudo!");

    let usuarios = JSON.parse(localStorage.getItem("medela_users")) || {};
    if(usuarios[cpf]) return alert("Este CPF já existe!");

    usuarios[cpf] = { nome, senha };
    localStorage.setItem("medela_users", JSON.stringify(usuarios));
    alert("Conta criada!");
    ir('login');
}

function entrar() {
    const cpf = document.getElementById("cpfLogin").value;
    const senha = document.getElementById("senhaLogin").value;

    if(cpf === 'admin' && senha === 'admin123') return ir('admin');

    let usuarios = JSON.parse(localStorage.getItem("medela_users")) || {};
    if(usuarios[cpf] && usuarios[cpf].senha === senha) {
        localStorage.setItem("user_atual", usuarios[cpf].nome);
        ir('home');
    } else {
        alert("Dados incorretos!");
    }
}

/* ADMINISTRAÇÃO */
function salvarProduto() {
    const nome = document.getElementById("pNome").value;
    const preco = parseFloat(document.getElementById("pPreco").value);
    const cat = document.getElementById("pCat").value;

    if(!nome || !preco) return alert("Dados inválidos");

    db_produtos.push({ id: Date.now(), nome, preco, cat });
    localStorage.setItem("medela_produtos", JSON.stringify(db_produtos));
    
    document.getElementById("pNome").value = "";
    document.getElementById("pPreco").value = "";
    renderizarAdmin();
    alert("Produto adicionado!");
}

function excluirProduto(id) {
    db_produtos = db_produtos.filter(p => p.id !== id);
    localStorage.setItem("medela_produtos", JSON.stringify(db_produtos));
    renderizarAdmin();
}

function renderizarAdmin() {
    // Produtos
    const lista = document.getElementById("listaAdminProdutos");
    lista.innerHTML = db_produtos.map(p => `
        <div style="display:flex; justify-content:space-between; padding:5px; border-bottom:1px solid #eee">
            <span>${p.nome} (${p.cat})</span>
            <button onclick="excluirProduto(${p.id})" style="width:auto; padding:2px 10px; background:red">X</button>
        </div>
    `).join('');

    // Clientes
    const usuarios = JSON.parse(localStorage.getItem("medela_users")) || {};
    const tbody = document.querySelector("#tabelaClientes tbody");
    tbody.innerHTML = Object.keys(usuarios).map(cpf => `
        <tr><td>${usuarios[cpf].nome}</td><td>${cpf}</td></tr>
    `).join('');
}

/* LOJA */
function renderizarLoja() {
    const filtro = document.getElementById("filtroCat").value;
    const grid = document.getElementById("listaProdutosLoja");
    const nomeUser = localStorage.getItem("user_atual");
    document.getElementById("txtBoasVindas").innerText = "Olá, " + (nomeUser || "Cliente");

    let filtrados = filtro === "Todos" ? db_produtos : db_produtos.filter(p => p.cat === filtro);

    grid.innerHTML = filtrados.map(p => `
        <div class="card">
            <strong>${p.nome}</strong><br>
            <span style="color:green">R$ ${p.preco.toFixed(2)}</span><br>
            <small>${p.cat}</small>
            <button style="margin-top:10px; background:var(--green)">Adicionar</button>
        </div>
    `).join('');
}

function sair() {
    localStorage.removeItem("user_atual");
    location.reload();
}

window.onload = () => {
    if(localStorage.getItem("user_atual")) ir('home');
};
</script>
</body>
</html>
