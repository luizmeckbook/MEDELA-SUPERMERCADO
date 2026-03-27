<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO - ANTI-BUG V7</title>
    <style>
        :root { --primary-green: #00a859; --orange-header: #f37021; --bg-gray: #f4f4f4; }
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: var(--bg-gray); margin: 0; overflow-x: hidden; }
        
        /* Previne que telas invisíveis ocupem espaço */
        .app-screen { display: none; min-height: 100vh; width: 100%; flex-direction: column; }
        .active { display: flex !important; }
        
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        .header-orange { background: var(--orange-header); padding: 15px; color: white; text-align: center; border-radius: 0 0 20px 20px; sticky; top: 0; z-index: 100; }
        
        .btn-green { background: var(--primary-green); color: white; border: none; padding: 15px; border-radius: 10px; font-weight: bold; width: 100%; cursor: pointer; margin-top: 10px; }
        .input-group { margin-bottom: 15px; }
        .input-group input, .input-group select { width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }

        .market-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; padding: 15px; padding-bottom: 120px; }
        .item-card { background: white; border-radius: 12px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        .product-img { width: 100%; height: 80px; object-fit: contain; margin-bottom: 8px; border-radius: 5px; }

        .bottom-nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; left: 50%; transform: translateX(-50%); z-index: 1000; }
        .nav-item { font-size: 11px; color: #aaa; border: none; background: none; font-weight: bold; cursor: pointer; }
        .nav-active { color: var(--primary-green); }

        .payment-methods { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; margin: 10px 0; }
        .pay-btn { padding: 10px; border: 1px solid #ddd; border-radius: 8px; background: #fff; cursor: pointer; font-size: 11px; }
        .pay-btn.selected { border-color: var(--primary-green); background: #e8f5e9; color: var(--primary-green); }
        .info-box { background: #fff; padding: 10px; border-radius: 8px; border: 1px solid #ddd; margin-top: 10px; display: none; }
    </style>
</head>
<body>

<section id="screen-login" class="app-screen">
    <div class="container">
        <div style="text-align: center; margin: 40px 0;">
            <h1 style="color: var(--primary-green); margin:0;">MEDELA</h1>
            <p style="color:var(--orange-header); font-weight:bold;">SUPERMERCADO</p>
        </div>
        <div class="input-group"><input type="text" id="login-cpf" placeholder="CPF (000.000.000-00)" oninput="mascaraCPF(this)" maxlength="14"></div>
        <button class="btn-green" onclick="fazerLogin()">ENTRAR</button>
        <p style="text-align:center;">Não tem conta? <span style="color:var(--orange-header); cursor:pointer" onclick="irPara('screen-register')">Cadastre-se</span></p>
        <p style="text-align:center; font-size:10px; color:#ccc; margin-top:40px;" onclick="acessoAdmin()">ADMINISTRAÇÃO</p>
    </div>
</section>

<section id="screen-register" class="app-screen">
    <div class="header-orange"><h2>Cadastro</h2></div>
    <div class="container">
        <div class="input-group"><input type="text" id="reg-nome" placeholder="Nome Completo"></div>
        <div class="input-group"><input type="text" id="reg-cpf" placeholder="CPF" oninput="mascaraCPF(this)" maxlength="14"></div>
        <div class="input-group"><input type="text" id="reg-end" placeholder="Endereço"></div>
        <button class="btn-green" onclick="salvarCadastro()">CADASTRAR</button>
        <button class="btn-green" style="background:#888" onclick="irPara('screen-login')">VOLTAR</button>
    </div>
</section>

<section id="screen-home" class="app-screen">
    <div class="header-orange">
        <h3 id="user-display" style="margin:0">Olá!</h3>
        <input type="text" placeholder="Buscar..." style="width:100%; margin-top:10px; padding:10px; border-radius:8px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div id="product-grid" class="market-grid"></div>
</section>

<section id="screen-cart" class="app-screen">
    <div class="header-orange"><h2>Carrinho</h2></div>
    <div class="container" id="cart-list" style="padding-bottom: 250px;"></div>
    <div id="cart-footer" class="container" style="position:fixed; bottom:60px; background:white; width:100%; border-top:1px solid #eee; display:none; left:50%; transform:translateX(-50%);">
        <h3 style="color:var(--primary-green)">Total: R$ <span id="total-val">0,00</span></h3>
        <div class="payment-methods">
            <button class="pay-btn" id="btn-pix" onclick="setMetodo('pix')">PIX</button>
            <button class="pay-btn" id="btn-credito" onclick="setMetodo('credito')">CRÉDITO</button>
            <button class="pay-btn" id="btn-debito" onclick="setMetodo('debito')">DÉBITO</button>
            <button class="pay-btn" id="btn-dinheiro" onclick="setMetodo('dinheiro')">DINHEIRO</button>
        </div>
        <div id="info-credito" class="info-box">Parcelas: <select id="sel-parcelas" style="width:100%"></select></div>
        <div id="info-dinheiro" class="info-box"><input type="number" id="troco-input" placeholder="Troco para quanto?"></div>
        <button class="btn-green" style="background:#25d366" onclick="finalizarWhatsApp()">FINALIZAR PEDIDO</button>
    </div>
</section>

<section id="screen-admin" class="app-screen">
    <div class="header-orange"><h2>Painel Admin</h2></div>
    <div class="container">
        <div class="input-group"><input type="text" id="adm-nome" placeholder="Produto"></div>
        <div class="input-group"><input type="text" id="adm-foto" placeholder="URL da Foto"></div>
        <div class="input-group"><input type="number" id="adm-preco" placeholder="Preço"></div>
        <button class="btn-green" onclick="addProduto()">SALVAR PRODUTO</button>
        <input type="text" placeholder="Pesquisar para excluir..." oninput="renderAdmin(this.value)" style="width:100%; margin:15px 0; padding:10px;">
        <div id="adm-lista"></div>
        <button class="btn-green" style="background:#333" onclick="sairAdmin()">SAIR</button>
    </div>
</section>

<nav class="bottom-nav" id="main-nav" style="display:none;">
    <button class="nav-item" id="nav-h" onclick="irPara('screen-home')">LOJA</button>
    <button class="nav-item" id="nav-c" onclick="irPara('screen-cart')">CESTA (<span id="count">0</span>)</button>
    <button class="nav-item" onclick="logout()">SAIR</button>
</nav>

<script>
    // 1. CONFIGURAÇÕES E ESTADO
    const SENHA_ADMIN = "123";
    let estoque = JSON.parse(localStorage.getItem('m_estoque')) || [];
    let carrinho = [];
    let user = JSON.parse(localStorage.getItem('m_user'));
    let isAdmin = localStorage.getItem('m_admin') === 'true';
    let metodo = "";

    // 2. CORREÇÃO DA TELA BRANCA (BOOT SYSTEM)
    window.onload = () => {
        try {
            if (isAdmin) {
                irPara('screen-admin');
            } else if (user && user.cpf) {
                irPara('screen-home');
                document.getElementById('user-display').innerText = `Olá, ${user.nome.split(' ')[0]}`;
            } else {
                irPara('screen-login');
            }
        } catch (e) {
            console.error("Erro fatal detectado. Limpando sessão.");
            localStorage.clear();
            location.reload();
        }
    };

    function irPara(id) {
        // Esconde todas
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        // Mostra a escolhida
        const target = document.getElementById(id);
        if (target) {
            target.classList.add('active');
        } else {
            document.getElementById('screen-login').classList.add('active');
        }
        
        // Controle da barra de navegação
        const nav = document.getElementById('main-nav');
        nav.style.display = (id === 'screen-home' || id === 'screen-cart') ? 'flex' : 'none';
        
        if(id === 'screen-home') renderHome();
        if(id === 'screen-cart') renderCart();
        if(id === 'screen-admin') renderAdmin();
    }

    // 3. LOGICA DO CLIENTE
    function fazerLogin() {
        const cpf = document.getElementById('login-cpf').value;
        const dados = localStorage.getItem(`u_${cpf}`);
        if(dados) {
            user = JSON.parse(dados);
            localStorage.setItem('m_user', JSON.stringify(user));
            location.reload();
        } else alert("CPF não encontrado.");
    }

    function salvarCadastro() {
        const n = document.getElementById('reg-nome').value, c = document.getElementById('reg-cpf').value, e = document.getElementById('reg-end').value;
        if(!n || c.length < 14) return alert("Preencha tudo.");
        const d = {nome: n, cpf: c, end: e};
        localStorage.setItem(`u_${c}`, JSON.stringify(d));
        localStorage.setItem('m_user', JSON.stringify(d));
        location.reload();
    }

    function renderHome(busca = "") {
        const grid = document.getElementById('product-grid');
        grid.innerHTML = estoque.filter(p => p.n.toLowerCase().includes(busca.toLowerCase())).map(p => `
            <div class="item-card">
                <img src="${p.f || 'https://via.placeholder.com/100'}" class="product-img">
                <b style="font-size:12px">${p.n}</b><br>
                <span style="color:var(--primary-green)">R$ ${p.p.toFixed(2)}</span><br>
                <button onclick="addCart(${p.id})" style="background:var(--orange-header); color:white; border:none; padding:5px; border-radius:5px; width:100%; margin-top:5px; cursor:pointer">ADD</button>
            </div>
        `).join('');
    }

    function addCart(id) {
        const p = estoque.find(x => x.id === id);
        carrinho.push({...p});
        document.getElementById('count').innerText = carrinho.length;
    }

    function renderCart() {
        const list = document.getElementById('cart-list');
        if(carrinho.length === 0) {
            list.innerHTML = "<p style='text-align:center'>Vazio</p>";
            document.getElementById('cart-footer').style.display = "none";
            return;
        }
        let total = carrinho.reduce((a, b) => a + b.p, 0) + 7;
        list.innerHTML = carrinho.map((it, i) => `
            <div style="background:white; padding:10px; margin-bottom:5px; border-radius:8px; display:flex; justify-content:space-between">
                <span>${it.n}</span>
                <b>R$ ${it.p.toFixed(2)} <span onclick="remCart(${i})" style="color:red; margin-left:10px">X</span></b>
            </div>
        `).join('');
        document.getElementById('total-val').innerText = total.toFixed(2);
        document.getElementById('cart-footer').style.display = "block";
    }

    function remCart(i) { carrinho.splice(i,1); renderCart(); document.getElementById('count').innerText = carrinho.length; }

    function setMetodo(m) {
        metodo = m;
        document.querySelectorAll('.pay-btn').forEach(b => b.classList.remove('selected'));
        document.getElementById(`btn-${m}`).classList.add('selected');
        document.getElementById('info-credito').style.display = m === 'credito' ? 'block' : 'none';
        document.getElementById('info-dinheiro').style.display = m === 'dinheiro' ? 'block' : 'none';
        if(m === 'credito') {
            const sel = document.getElementById('sel-parcelas');
            sel.innerHTML = "";
            let t = parseFloat(document.getElementById('total-val').innerText);
            for(let i=1; i<=12; i++) {
                let j = i === 1 ? 1 : (1 + (0.0398 * (i-1)));
                let vf = t * j;
                sel.innerHTML += `<option>${i}x de R$ ${(vf/i).toFixed(2)} (Total R$ ${vf.toFixed(2)})</option>`;
            }
        }
    }

    function finalizarWhatsApp() {
        if(!metodo) return alert("Escolha o pagamento!");
        let t = document.getElementById('total-val').innerText;
        let msg = `*PEDIDO MEDELA*\nCliente: ${user.nome}\nEndereço: ${user.end}\nTotal: R$ ${t}\nPagamento: ${metodo.toUpperCase()}`;
        window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`);
    }

    // 4. ADMIN
    function acessoAdmin() { if(prompt("Senha:") === SENHA_ADMIN) { localStorage.setItem('m_admin','true'); location.reload(); } }
    function sairAdmin() { localStorage.removeItem('m_admin'); location.reload(); }
    function addProduto() {
        const n = document.getElementById('adm-nome').value, f = document.getElementById('adm-foto').value, p = parseFloat(document.getElementById('adm-preco').value);
        if(!n || !p) return alert("Dados inválidos");
        estoque.push({id: Date.now(), n, f, p});
        localStorage.setItem('m_estoque', JSON.stringify(estoque));
        renderAdmin();
    }
    function renderAdmin(busca = "") {
        document.getElementById('adm-lista').innerHTML = estoque.filter(p => p.n.toLowerCase().includes(busca.toLowerCase())).map(p => `
            <div style="background:#fff; padding:10px; margin-bottom:5px; display:flex; justify-content:space-between">
                <span>${p.n}</span>
                <button onclick="remProd(${p.id})" style="background:red; color:white; border:none">X</button>
            </div>
        `).join('');
    }
    function remProd(id) { estoque = estoque.filter(p => p.id !== id); localStorage.setItem('m_estoque', JSON.stringify(estoque)); renderAdmin(); }

    // 5. AUXILIARES
    function mascaraCPF(i) {
        let v = i.value.replace(/\D/g, "");
        v = v.replace(/(\d{3})(\d)/, "$1.$2").replace(/(\d{3})(\d)/, "$1.$2").replace(/(\d{3})(\d{1,2})$/, "$1-$2");
        i.value = v;
    }
    function logout() { localStorage.removeItem('m_user'); location.reload(); }
</script>
</body>
</html>
