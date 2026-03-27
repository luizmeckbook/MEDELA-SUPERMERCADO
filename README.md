<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO - V2.1</title>
    <style>
        :root { --primary-green: #00a859; --orange-header: #f37021; --bg-gray: #f4f4f4; --text-dark: #333; }
        body { font-family: 'Segoe UI', Roboto, sans-serif; background: var(--bg-gray); margin: 0; color: var(--text-dark); }
        .app-screen { display: none; min-height: 100vh; flex-direction: column; }
        .active { display: flex !important; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        
        .header-orange { background: var(--orange-header); padding: 20px; color: white; border-radius: 0 0 20px 20px; position: sticky; top: 0; z-index: 100; }
        .bottom-nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; z-index: 1000; left: 50%; transform: translateX(-50%); }
        .nav-item { font-size: 11px; color: #aaa; text-align: center; border: none; background: none; font-weight: bold; cursor: pointer; }
        
        .input-group { margin-bottom: 12px; }
        .input-group label { display: block; font-size: 11px; font-weight: bold; margin-bottom: 4px; }
        .input-group input, .input-group select { width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; font-size: 14px; }
        .btn-green { background: var(--primary-green); color: white; border: none; width: 100%; padding: 15px; border-radius: 10px; font-weight: bold; cursor: pointer; }
        
        .market-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; padding: 15px; padding-bottom: 150px; }
        .item-card { background: white; border-radius: 12px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
        .qty-selector { display: flex; align-items: center; justify-content: center; gap: 5px; margin-top: 8px; }
        .qty-input { width: 65px !important; padding: 5px !important; text-align: center; border: 1px solid #ccc; border-radius: 5px; }

        .admin-item { background: white; padding: 12px; margin-bottom: 8px; border-radius: 8px; display: flex; justify-content: space-between; align-items: center; border-left: 5px solid var(--orange-header); box-shadow: 0 1px 3px rgba(0,0,0,0.1); }
        .badge-type { font-size: 10px; padding: 2px 5px; background: #e0f2f1; color: var(--primary-green); border-radius: 4px; font-weight: bold; }
    </style>
</head>
<body>

<section id="screen-login" class="app-screen">
    <div class="container">
        <div style="text-align: center; margin: 40px 0;">
            <h1 style="color: var(--primary-green); margin:0; font-size: 40px;">MEDELA</h1>
            <p style="color:var(--orange-header); font-weight:bold; letter-spacing: 2px;">SUPERMERCADO</p>
        </div>
        <div class="input-group"><label>CPF</label><input type="text" id="login-cpf" placeholder="000.000.000-00" oninput="mascaraCPF(this)"></div>
        <div class="input-group"><label>Senha (Opcional p/ Clientes)</label><input type="password" id="login-pass"></div>
        <button class="btn-green" onclick="fazerLogin()">ENTRAR NA LOJA</button>
        <p style="text-align:center; margin-top:40px; font-size:12px; color:#999; cursor:pointer; border-top: 1px solid #ddd; padding-top: 20px;" onclick="acessoAdmin()">⚙️ PAINEL DO ADMINISTRADOR</p>
    </div>
</section>

<section id="screen-home" class="app-screen">
    <div class="header-orange">
        <h3 id="user-display" style="margin:0">Olá!</h3>
        <input type="text" placeholder="Buscar produto na loja..." style="width:100%; margin-top:10px; padding:12px; border-radius:8px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div class="market-grid" id="product-grid"></div>
</section>

<section id="screen-cart" class="app-screen">
    <div class="header-orange"><h2>Meu Carrinho</h2></div>
    <div class="container" style="padding-bottom: 200px;" id="cart-items-list"></div>
    <div id="cart-footer" class="container" style="position:fixed; bottom:60px; background:white; width:100%; border-top:2px solid #eee; display:none; left:50%; transform:translateX(-50%);">
        <div style="display:flex; justify-content: space-between;"><span>Taxa Entrega:</span><span>R$ 7,00</span></div>
        <h3 style="color:var(--primary-green); margin:10px 0;">TOTAL: <span id="total-val" style="float:right">0,00</span></h3>
        <button class="btn-green" onclick="enviarWhatsApp()">FINALIZAR PEDIDO</button>
    </div>
</section>

<section id="screen-admin" class="app-screen">
    <div class="header-orange"><h2>Gerenciar Estoque</h2></div>
    <div class="container" style="padding-bottom: 100px;">
        <div style="background: #fff; padding: 15px; border-radius: 10px; margin-bottom: 20px; border: 1px solid #ddd;">
            <h4 style="margin:0 0 10px 0">Novo Produto</h4>
            <div class="input-group"><input type="text" id="adm-nome" placeholder="Nome (Ex: Alcatra)"></div>
            <div class="input-group">
                <select id="adm-tipo">
                    <option value="un">Venda por Unidade (UN)</option>
                    <option value="kg">Venda por Quilo (KG)</option>
                </select>
            </div>
            <div class="input-group"><input type="number" id="adm-preco" step="0.01" placeholder="Preço (0.00)"></div>
            <button class="btn-green" onclick="admAdicionar()">SALVAR NO ESTOQUE</button>
        </div>

        <div class="input-group">
            <input type="text" id="adm-search-input" placeholder="🔍 Buscar produto p/ apagar..." oninput="renderAdmin(this.value)" style="border: 2px solid var(--orange-header);">
        </div>
        
        <div id="adm-lista-geral">
            </div>
        
        <button class="btn-green" style="background:#333; margin-top:20px" onclick="location.reload()">SAIR DO ADM</button>
    </div>
</section>

<nav class="bottom-nav" id="main-nav" style="display:none;">
    <button class="nav-item" onclick="irPara('screen-home')">PRODUTOS</button>
    <button class="nav-item" onclick="irPara('screen-cart')">CARRINHO (<span id="count">0</span>)</button>
    <button class="nav-item" onclick="logout()">SAIR</button>
</nav>

<script>
    const SENHA_MESTRA = "123"; 
    // Garante que o estoque nunca seja nulo
    let estoque = JSON.parse(localStorage.getItem('medela_estoque_v2')) || [];
    let carrinho = [];
    let user = JSON.parse(localStorage.getItem('medela_user_ativo'));

    window.onload = () => {
        if(user) { logarInterface(); irPara('screen-home'); } 
        else { irPara('screen-login'); }
    };

    function irPara(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        const target = document.getElementById(id);
        if(target) target.classList.add('active');
        
        document.getElementById('main-nav').style.display = (id === 'screen-home' || id === 'screen-cart') ? 'flex' : 'none';
        
        if(id === 'screen-home') renderHome();
        if(id === 'screen-cart') renderCart();
        if(id === 'screen-admin') renderAdmin();
    }

    // --- FUNCIONALIDADES LOJA ---
    function renderHome(busca = "") {
        const grid = document.getElementById('product-grid');
        grid.innerHTML = ""; // Limpa antes de renderizar
        
        const filtrados = estoque.filter(p => p.n.toLowerCase().includes(busca.toLowerCase()));

        if(filtrados.length === 0) {
            grid.innerHTML = "<p style='grid-column: 1/-1; text-align:center;'>Nenhum produto encontrado.</p>";
            return;
        }

        grid.innerHTML = filtrados.map(p => `
            <div class="item-card">
                <b style="font-size:14px">${p.n}</b><br>
                <small style="color:#666">R$ ${p.p.toFixed(2)} / ${p.tipo.toUpperCase()}</small>
                <div class="qty-selector">
                    <input type="number" id="qty-${p.id}" class="qty-input" value="1" step="${p.tipo === 'kg' ? '0.1' : '1'}" min="0.1">
                    <span style="font-size:12px">${p.tipo}</span>
                </div>
                <button onclick="addAoCarrinho(${p.id})" style="background:var(--orange-header); color:white; border:none; width:100%; padding:8px; border-radius:5px; margin-top:8px; cursor:pointer; font-weight:bold;">ADICIONAR</button>
            </div>
        `).join('');
    }

    function addAoCarrinho(id) {
        const p = estoque.find(x => x.id === id);
        const inputQtd = document.getElementById(`qty-${id}`);
        const qtd = parseFloat(inputQtd.value);
        
        if(!qtd || qtd <= 0) return alert("Informe uma quantidade válida");

        carrinho.push({ ...p, qtdSelecionada: qtd, precoFinal: p.p * qtd });
        document.getElementById('count').innerText = carrinho.length;
        alert(`${p.n} adicionado!`);
    }

    function renderCart() {
        const container = document.getElementById('cart-items-list');
        container.innerHTML = "";
        
        if(carrinho.length === 0) {
            container.innerHTML = "<div style='text-align:center; margin-top:50px;'>🛒 Seu carrinho está vazio</div>";
            document.getElementById('cart-footer').style.display = "none";
            return;
        }
        
        let total = 0;
        container.innerHTML = carrinho.map((it, i) => {
            total += it.precoFinal;
            return `
                <div class="admin-item">
                    <div>
                        <b>${it.n}</b><br>
                        <small>${it.qtdSelecionada} ${it.tipo} x R$ ${it.p.toFixed(2)}</small>
                    </div>
                    <div style="text-align:right">
                        <b>R$ ${it.precoFinal.toFixed(2)}</b><br>
                        <button onclick="remCart(${i})" style="color:red; border:none; background:none; font-size:11px; cursor:pointer;">Remover</button>
                    </div>
                </div>
            `;
        }).join('');
        document.getElementById('total-val').innerText = "R$ " + (total + 7).toFixed(2);
        document.getElementById('cart-footer').style.display = "block";
    }

    function remCart(i) {
        carrinho.splice(i, 1);
        document.getElementById('count').innerText = carrinho.length;
        renderCart();
    }

    // --- PAINEL ADMINISTRATIVO (CORREÇÕES DE TELA BRANCA AQUI) ---
    function acessoAdmin() {
        const pass = prompt("Digite a senha do administrador:");
        if(pass === SENHA_MESTRA) {
            irPara('screen-admin');
        } else if(pass !== null) {
            alert("Senha incorreta!");
        }
    }

    function renderAdmin(busca = "") {
        const lista = document.getElementById('adm-lista-geral');
        if(!lista) return; // Evita erro se o elemento não existir
        
        lista.innerHTML = ""; // Limpa a lista
        
        // Filtra o estoque com base na busca
        const filtrados = estoque.filter(p => p.n.toLowerCase().includes(busca.toLowerCase()));
        
        if(filtrados.length === 0) {
            lista.innerHTML = "<p style='text-align:center; color:#999;'>Nenhum produto cadastrado com este nome.</p>";
            return;
        }

        lista.innerHTML = filtrados.map(p => `
            <div class="admin-item">
                <span>
                    <b>${p.n}</b> <span class="badge-type">${p.tipo.toUpperCase()}</span><br>
                    <span style="color:var(--primary-green)">R$ ${p.p.toFixed(2)}</span>
                </span>
                <button onclick="admRemover(${p.id})" style="background:#ff4d4d; color:white; border:none; padding:8px 12px; border-radius:5px; cursor:pointer; font-weight:bold;">EXCLUIR</button>
            </div>
        `).join('');
    }

    function admAdicionar() {
        const nomeIn = document.getElementById('adm-nome');
        const tipoIn = document.getElementById('adm-tipo');
        const precoIn = document.getElementById('adm-preco');

        const n = nomeIn.value.trim();
        const t = tipoIn.value;
        const p = parseFloat(precoIn.value);

        if(!n || isNaN(p)) {
            alert("Por favor, preencha o nome e o preço corretamente.");
            return;
        }

        const novoProd = { id: Date.now(), n: n, tipo: t, p: p };
        estoque.push(novoProd);
        localStorage.setItem('medela_estoque_v2', JSON.stringify(estoque));
        
        // Limpa campos
        nomeIn.value = "";
        precoIn.value = "";
        
        renderAdmin();
        alert("Produto cadastrado com sucesso!");
    }

    function admRemover(id) {
        if(confirm("Tem certeza que deseja apagar este produto?")) {
            estoque = estoque.filter(p => p.id !== id);
            localStorage.setItem('medela_estoque_v2', JSON.stringify(estoque));
            renderAdmin(document.getElementById('adm-search-input').value);
        }
    }

    // --- SISTEMA AUXILIAR ---
    function fazerLogin() {
        const cpf = document.getElementById('login-cpf').value;
        if(cpf.length < 11) return alert("Informe seu CPF corretamente.");
        user = { nome: "Cliente", cpf: cpf, end: "" };
        localStorage.setItem('medela_user_ativo', JSON.stringify(user));
        logarInterface();
        irPara('screen-home');
    }

    function logarInterface() {
        document.getElementById('user-display').innerText = "Bem-vindo!";
    }

    function logout() {
        localStorage.removeItem('medela_user_ativo');
        location.reload();
    }

    function mascaraCPF(i) {
        let v = i.value.replace(/\D/g, "");
        v = v.replace(/(\d{3})(\d)/, "$1.$2");
        v = v.replace(/(\d{3})(\d)/, "$1.$2");
        v = v.replace(/(\d{3})(\d{1,2})$/, "$1-$2");
        i.value = v;
    }

    function enviarWhatsApp() {
        let msg = `*NOVO PEDIDO - MEDELA SUPERMERCADO*\n\n`;
        carrinho.forEach(i => {
            msg += `• ${i.n} (${i.qtdSelecionada} ${i.tipo}) = R$ ${i.precoFinal.toFixed(2)}\n`;
        });
        msg += `\n*Taxa de Entrega:* R$ 7,00`;
        msg += `\n*TOTAL A PAGAR: ${document.getElementById('total-val').innerText}*`;
        
        const fone = "5521977126638";
        window.open(`https://api.whatsapp.com/send?phone=${fone}&text=${encodeURIComponent(msg)}`);
    }
</script>
</body>
</html>
