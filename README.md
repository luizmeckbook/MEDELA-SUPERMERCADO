<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO - App</title>
    <style>
        :root { --primary-green: #00a859; --orange-header: #f37021; --bg-gray: #f4f4f4; --text-dark: #333; }
        body { font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; background-color: var(--bg-gray); margin: 0; padding: 0; color: var(--text-dark); }
        .app-screen { display: none; min-height: 100vh; width: 100%; box-sizing: border-box; background-color: var(--bg-gray); }
        .active { display: flex !important; flex-direction: column; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        
        /* CABEÇALHO E LOGIN */
        .logo-main { text-align: center; margin: 30px 0; }
        .logo-main h1 { color: var(--primary-green); margin: 0; font-size: 28px; letter-spacing: -1px; }
        .welcome-text { text-align: center; color: #666; font-size: 14px; margin-bottom: 25px; }
        .input-group { margin-bottom: 15px; }
        .input-group label { display: block; font-size: 12px; color: #444; margin-bottom: 5px; font-weight: bold; }
        .input-group input { width: 100%; padding: 14px; border: 1px solid #ddd; border-radius: 10px; background: #fff; box-sizing: border-box; outline: none; }
        .btn-green { background: var(--primary-green); color: white; border: none; width: 100%; padding: 15px; border-radius: 10px; font-weight: bold; cursor: pointer; font-size: 16px; }

        /* INTERFACE PRINCIPAL */
        .header-orange { background: var(--orange-header); padding: 30px 20px; color: white; border-radius: 0 0 25px 25px; position: sticky; top: 0; z-index: 100; }
        .search-box { margin-top: 15px; width: 100%; padding: 12px; border-radius: 8px; border: none; outline: none; font-size: 14px; }
        .category-scroll { display: flex; overflow-x: auto; padding: 10px 20px; gap: 10px; background: #fff; white-space: nowrap; border-bottom: 1px solid #eee; }
        .cat-pill { padding: 8px 15px; background: #eee; border-radius: 20px; font-size: 13px; cursor: pointer; border: 1px solid #ddd; }
        .cat-active { background: var(--primary-green); color: white; border-color: var(--primary-green); font-weight: bold; }

        /* GRID PRODUTOS */
        .market-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 15px; padding-bottom: 150px; }
        .item-card { background: white; border-radius: 12px; padding: 12px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.05); border: 1px solid #f0f0f0; }
        .item-card .name { font-size: 13px; font-weight: 600; display: block; height: 35px; margin: 8px 0; overflow: hidden; }
        .item-card .price { font-size: 15px; color: var(--primary-green); font-weight: bold; display: block; margin-bottom: 8px; }
        .btn-add-item { background: var(--orange-header); color: white; border: none; width: 100%; padding: 8px; border-radius: 6px; cursor: pointer; font-weight: bold; font-size: 11px; }

        /* CARRINHO ESTILIZADO */
        .cart-item { display: flex; justify-content: space-between; align-items: center; padding: 12px; background: #fff; border-radius: 10px; margin-bottom: 8px; border: 1px solid #eee; }
        .info-entrega { background: #fff; padding: 15px; border-radius: 12px; margin-bottom: 15px; border: 1px solid #ddd; font-size: 13px; }
        .resumo-linha { display: flex; justify-content: space-between; margin-bottom: 5px; font-size: 14px; }
        .footer-cart { position: fixed; bottom: 65px; background: white; width: 100%; max-width: 450px; border-top: 1px solid #eee; padding: 15px; box-sizing: border-box; box-shadow: 0 -4px 10px rgba(0,0,0,0.1); z-index: 999; left: 50%; transform: translateX(-50%); }

        /* MENU INFERIOR */
        .bottom-nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 10px 0; border-top: 1px solid #eee; z-index: 1000; left: 50%; transform: translateX(-50%); }
        .nav-item { font-size: 10px; color: #aaa; text-align: center; border: none; background: none; font-weight: bold; width: 33%; cursor: pointer; }
        .nav-active { color: var(--primary-green); }
        .cart-badge { background: #e31b1b; color: white; border-radius: 50%; padding: 2px 5px; font-size: 9px; margin-left: 3px; }
    </style>
</head>
<body>

<section id="screen-login" class="app-screen active">
    <div class="container">
        <div class="logo-main"><h1>MEDELA</h1><p style="color:var(--orange-header); font-weight:bold;">SUPERMERCADO</p></div>
        <div class="input-group"><label>CPF/CNPJ</label><input type="text" id="login-id" placeholder="000.000.000-00"></div>
        <div class="input-group"><label>Senha</label><input type="password" id="login-pass" placeholder="••••••••"></div>
        <button class="btn-green" onclick="executarLogin()">Entrar</button>
        <p style="text-align:center; margin-top:20px; font-size:14px; color:var(--primary-green); cursor:pointer;" onclick="irPara('screen-register')">Criar nova conta</p>
    </div>
</section>

<section id="screen-register" class="app-screen">
    <div class="container">
        <h2 style="color:var(--primary-green);">Novo Cadastro</h2>
        <div class="input-group"><label>Nome Completo</label><input type="text" id="reg-nome" placeholder="Seu nome"></div>
        <div class="input-group"><label>CPF/CNPJ</label><input type="text" id="reg-cpf" oninput="mascaraCPF(this)"></div>
        <div class="input-group"><label>Endereço de Entrega</label><input type="text" id="reg-endereco" placeholder="Rua, número, bairro"></div>
        <div class="input-group"><label>Senha</label><input type="password" id="reg-senha"></div>
        <button class="btn-green" onclick="finalizarCadastro()">Salvar Dados</button>
        <p style="text-align:center; margin-top:20px; color:#666; cursor:pointer;" onclick="irPara('screen-login')">Já tenho conta</p>
    </div>
</section>

<section id="screen-home" class="app-screen">
    <div class="header-orange">
        <h2 id="user-display">Olá!</h2>
        <input type="text" class="search-box" id="search-input" placeholder="O que você procura hoje?" oninput="filtrarProdutos()">
    </div>
    <div class="category-scroll" id="cat-list"></div>
    <div class="market-grid" id="product-grid"></div>
</section>

<section id="screen-cart" class="app-screen">
    <div class="header-orange"><h2>Finalizar Pedido</h2></div>
    <div class="container">
        <div class="info-entrega" id="cliente-info-box">
            <strong>Entregar para:</strong> <span id="info-nome">...</span><br>
            <strong>Endereço:</strong> <span id="info-end">...</span>
        </div>
        
        <div id="cart-list"></div>
    </div>

    <div class="footer-cart" id="cart-footer" style="display:none;">
        <div class="resumo-linha"><span>Subtotal:</span><span>R$ <span id="cart-subtotal">0,00</span></span></div>
        <div class="resumo-linha"><span>Taxa de Entrega:</span><span style="color:var(--orange-header);">R$ 7,00</span></div>
        <hr>
        <div class="resumo-linha" style="font-weight:bold; font-size:18px;">
            <span>Total:</span><span style="color:var(--primary-green);">R$ <span id="cart-total">0,00</span></span>
        </div>
        <button class="btn-green" style="margin-top:10px;" onclick="enviarPedido()">Finalizar no WhatsApp</button>
    </div>
</section>

<nav class="bottom-nav">
    <button class="nav-item nav-active" id="nav-main-btn" onclick="botaoPrincipalMenu()">PRODUTOS</button>
    <button class="nav-item" id="nav-cart-btn" onclick="irPara('screen-cart')">CARRINHO<span id="cart-count" class="cart-badge">0</span></button>
    <button class="nav-item" onclick="fazerLogout()">SAIR</button>
</nav>

<script>
    const TAXA_ENTREGA = 7.00;
    const produtosMaster = [
        { cat: "Mercearia Seca", itens: ["Arroz 5kg", "Feijão Preto 1kg", "Açúcar Cristal", "Café Pilão", "Óleo de Soja", "Macarrão Espaguete", "Farinha de Trigo"] },
        { cat: "Hortifrúti", itens: ["Banana KG", "Laranja KG", "Batata KG", "Cebola KG", "Tomate KG", "Ovos 30un"] },
        { cat: "Açougue", itens: ["Picanha KG", "Alcatra", "Contrafilé", "Carne Moída", "Frango Inteiro", "Linguiça"] },
        { cat: "Bebidas", itens: ["Coca-Cola 2L", "Cerveja Lata", "Água 500ml", "Suco de Uva"] }
    ];

    let carrinho = JSON.parse(localStorage.getItem('carrinhoSalvo')) || [];
    let usuarioLogado = null;
    let categoriaAtual = "Mercearia Seca";

    window.onload = function() {
        const sessao = localStorage.getItem('sessaoAtiva');
        if (sessao) {
            usuarioLogado = JSON.parse(sessao);
            atualizarDadosInterface();
            irPara('screen-home');
        } else {
            irPara('screen-login');
        }
    };

    function atualizarDadosInterface() {
        if(usuarioLogado) {
            document.getElementById('user-display').innerText = "Olá, " + usuarioLogado.nome.split(' ')[0] + "!";
            document.getElementById('info-nome').innerText = usuarioLogado.nome;
            document.getElementById('info-end').innerText = usuarioLogado.endereco;
            document.getElementById('cart-count').innerText = carrinho.length;
        }
    }

    function irPara(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('nav-active'));
        const target = document.getElementById(id);
        if (target) target.classList.add('active');

        if(id === 'screen-home') {
            document.getElementById('nav-main-btn').classList.add('nav-active');
            renderizarCategorias();
            renderizarProdutos();
        } else if(id === 'screen-cart') {
            document.getElementById('nav-cart-btn').classList.add('nav-active');
            renderizarCarrinho();
        }
    }

    function executarLogin() {
        const id = document.getElementById('login-id').value;
        const dados = localStorage.getItem(id);
        if(dados) {
            usuarioLogado = JSON.parse(dados);
            localStorage.setItem('sessaoAtiva', JSON.stringify(usuarioLogado));
            atualizarDadosInterface();
            irPara('screen-home');
        } else {
            alert("Cadastro não encontrado! Crie uma nova conta.");
        }
    }

    function finalizarCadastro() {
        const nome = document.getElementById('reg-nome').value;
        const cpf = document.getElementById('reg-cpf').value;
        const endereco = document.getElementById('reg-endereco').value;
        const senha = document.getElementById('reg-senha').value;

        if(!nome || !cpf || !endereco) return alert("Preencha todos os campos!");

        const dados = { nome, cpf, endereco, senha };
        localStorage.setItem(cpf, JSON.stringify(dados));
        alert("Cadastrado com sucesso!");
        irPara('screen-login');
    }

    function fazerLogout() {
        localStorage.removeItem('sessaoAtiva');
        window.location.reload();
    }

    function renderizarCategorias() {
        const list = document.getElementById('cat-list');
        list.innerHTML = produtosMaster.map(c => `<div class="cat-pill ${categoriaAtual === c.cat ? 'cat-active' : ''}" onclick="setCat('${c.cat}')">${c.cat}</div>`).join('');
    }

    function renderizarProdutos() {
        const grid = document.getElementById('product-grid');
        grid.innerHTML = "";
        const cat = produtosMaster.find(c => c.cat === categoriaAtual);
        cat.itens.forEach(item => {
            const preco = (Math.random() * 15 + 5).toFixed(2);
            grid.innerHTML += `
                <div class="item-card">
                    <span class="name">${item}</span>
                    <span class="price">R$ ${preco}</span>
                    <button class="btn-add-item" onclick="addAoCarrinho('${item}', ${preco})">Adicionar</button>
                </div>`;
        });
    }

    function addAoCarrinho(nome, preco) {
        carrinho.push({ nome, preco: parseFloat(preco) });
        localStorage.setItem('carrinhoSalvo', JSON.stringify(carrinho));
        document.getElementById('cart-count').innerText = carrinho.length;
    }

    function renderizarCarrinho() {
        const list = document.getElementById('cart-list');
        const footer = document.getElementById('cart-footer');
        if(carrinho.length === 0) {
            list.innerHTML = '<p style="text-align:center; padding:40px;">Seu carrinho está vazio.</p>';
            footer.style.display = "none";
            return;
        }
        let subtotal = 0;
        list.innerHTML = carrinho.map((item, index) => {
            subtotal += item.preco;
            return `<div class="cart-item">
                <div><strong>${item.nome}</strong><br><small>R$ ${item.preco.toFixed(2)}</small></div>
                <button onclick="removerItem(${index})" style="color:red; border:none; background:none; font-weight:bold;">X</button>
            </div>`;
        }).join('');
        
        document.getElementById('cart-subtotal').innerText = subtotal.toFixed(2);
        document.getElementById('cart-total').innerText = (subtotal + TAXA_ENTREGA).toFixed(2);
        footer.style.display = "block";
    }

    function removerItem(index) {
        carrinho.splice(index, 1);
        localStorage.setItem('carrinhoSalvo', JSON.stringify(carrinho));
        document.getElementById('cart-count').innerText = carrinho.length;
        renderizarCarrinho();
    }

    function enviarPedido() {
        let msg = `*MEDELA SUPERMERCADO*\n\n`;
        msg += `*DADOS DO CLIENTE*\n`;
        msg += `Nome: ${usuarioLogado.nome}\n`;
        msg += `Endereço: ${usuarioLogado.endereco}\n\n`;
        msg += `*ITENS DO PEDIDO*\n`;
        carrinho.forEach(i => msg += `- ${i.nome}: R$ ${i.preco.toFixed(2)}\n`);
        msg += `\nSubtotal: R$ ${document.getElementById('cart-subtotal').innerText}`;
        msg += `\nTaxa de Entrega: R$ 7,00`;
        msg += `\n*TOTAL: R$ ${document.getElementById('cart-total').innerText}*`;
        
        window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`);
    }

    function setCat(cat) { categoriaAtual = cat; renderizarCategorias(); renderizarProdutos(); }
    function mascaraCPF(i) {
        let v = i.value.replace(/\D/g, "");
        v = v.replace(/(\d{3})(\d)/, "$1.$2");
        v = v.replace(/(\d{3})(\d)/, "$1.$2");
        v = v.replace(/(\d{3})(\d{1,2})$/, "$1-$2");
        i.value = v;
    }
    function botaoPrincipalMenu() { irPara(usuarioLogado ? 'screen-home' : 'screen-login'); }
</script>
</body>
</html>
