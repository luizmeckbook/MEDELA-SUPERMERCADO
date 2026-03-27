<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO - Oficial</title>
    <style>
        :root { --primary-green: #00a859; --orange-header: #f37021; --bg-gray: #f4f4f4; --text-dark: #333; }
        body { font-family: 'Segoe UI', Roboto, sans-serif; background: var(--bg-gray); margin: 0; color: var(--text-dark); overflow-x: hidden; }
        
        /* Telas */
        .app-screen { display: none; min-height: 100vh; flex-direction: column; width: 100%; }
        .active { display: flex !important; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        
        /* UI Elements */
        .header-orange { background: var(--orange-header); padding: 20px; color: white; border-radius: 0 0 20px 20px; position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        .bottom-nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; z-index: 1000; left: 50%; transform: translateX(-50%); }
        .nav-item { font-size: 11px; color: #aaa; text-align: center; border: none; background: none; font-weight: bold; cursor: pointer; transition: 0.3s; }
        .nav-active { color: var(--primary-green); }
        
        .input-group { margin-bottom: 12px; }
        .input-group label { display: block; font-size: 11px; font-weight: bold; margin-bottom: 4px; color: #666; }
        .input-group input, .input-group select { width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; font-size: 14px; outline: none; }
        .input-group input:focus { border-color: var(--primary-green); }
        
        .btn-green { background: var(--primary-green); color: white; border: none; width: 100%; padding: 15px; border-radius: 10px; font-weight: bold; cursor: pointer; font-size: 16px; margin-top: 10px; }
        .btn-green:active { transform: scale(0.98); }

        /* Grid de Produtos */
        .market-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; padding: 15px; padding-bottom: 150px; }
        .item-card { background: white; border-radius: 12px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.05); display: flex; flex-direction: column; justify-content: space-between; }
        .qty-selector { display: flex; align-items: center; justify-content: center; gap: 5px; margin: 8px 0; background: #f9f9f9; padding: 5px; border-radius: 5px; }
        .qty-input { width: 60px !important; padding: 5px !important; text-align: center; border: 1px solid #ddd; border-radius: 4px; }

        /* Itens Admin e Carrinho */
        .list-item { background: white; padding: 12px; margin-bottom: 8px; border-radius: 8px; display: flex; justify-content: space-between; align-items: center; border-left: 5px solid var(--orange-header); box-shadow: 0 1px 3px rgba(0,0,0,0.1); }
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
        <div class="input-group">
            <label>CPF</label>
            <input type="text" id="login-cpf" placeholder="000.000.000-00" oninput="mascaraCPF(this)" maxlength="14">
        </div>
        <button class="btn-green" onclick="fazerLogin()">ENTRAR</button>
        <p style="text-align:center; margin-top:20px; font-size:14px;">
            Ainda não é cliente? <br>
            <span style="color:var(--orange-header); font-weight:bold; cursor:pointer;" onclick="irPara('screen-register')">Cadastre-se aqui</span>
        </p>
        <p style="text-align:center; margin-top:50px; font-size:11px; color:#ccc; cursor:pointer;" onclick="acessoAdmin()">⚙️ ACESSO ADMINISTRATIVO</p>
    </div>
</section>

<section id="screen-register" class="app-screen">
    <div class="header-orange"><h2>Cadastro de Cliente</h2></div>
    <div class="container">
        <div class="input-group">
            <label>Nome Completo</label>
            <input type="text" id="reg-nome" placeholder="Seu nome">
        </div>
        <div class="input-group">
            <label>CPF (Para o login)</label>
            <input type="text" id="reg-cpf" placeholder="000.000.000-00" oninput="mascaraCPF(this)" maxlength="14">
        </div>
        <div class="input-group">
            <label>Endereço de Entrega</label>
            <input type="text" id="reg-end" placeholder="Rua, Número, Bairro">
        </div>
        <button class="btn-green" onclick="salvarCadastro()">FINALIZAR CADASTRO</button>
        <button class="btn-green" style="background:#888" onclick="irPara('screen-login')">VOLTAR</button>
    </div>
</section>

<section id="screen-home" class="app-screen">
    <div class="header-orange">
        <h3 id="user-display" style="margin:0">Olá!</h3>
        <input type="text" placeholder="🔍 O que você procura?" style="width:100%; margin-top:10px; padding:12px; border-radius:8px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div class="market-grid" id="product-grid"></div>
</section>

<section id="screen-cart" class="app-screen">
    <div class="header-orange"><h2>Meu Carrinho</h2></div>
    <div class="container" style="padding-bottom: 200px;" id="cart-items-list"></div>
    <div id="cart-footer" class="container" style="position:fixed; bottom:60px; background:white; width:100%; border-top:2px solid #eee; display:none; left:50%; transform:translateX(-50%);">
        <div style="display:flex; justify-content: space-between; font-size: 14px; margin-bottom: 5px;">
            <span>Subtotal:</span><span id="sub-val">0,00</span>
        </div>
        <div style="display:flex; justify-content: space-between; font-size: 14px; color: #666;">
            <span>Taxa de Entrega:</span><span>R$ 7,00</span>
        </div>
        <h3 style="color:var(--primary-green); margin:10px 0; border-top: 1px solid #eee; padding-top:10px;">TOTAL: <span id="total-val" style="float:right">0,00</span></h3>
        <button class="btn-green" onclick="enviarWhatsApp()">PEDIR PELO WHATSAPP</button>
    </div>
</section>

<section id="screen-admin" class="app-screen">
    <div class="header-orange"><h2>Painel de Controle</h2></div>
    <div class="container" style="padding-bottom: 100px;">
        <div style="background: #fff; padding: 15px; border-radius: 10px; margin-bottom: 20px; border: 1px solid #ddd;">
            <h4 style="margin:0 0 10px 0">Cadastrar Novo Produto</h4>
            <div class="input-group"><input type="text" id="adm-nome" placeholder="Ex: Alcatra ou Feijão"></div>
            <div class="input-group">
                <select id="adm-tipo">
                    <option value="un">Unidade (UN)</option>
                    <option value="kg">Quilo (KG)</option>
                </select>
            </div>
            <div class="input-group"><input type="number" id="adm-preco" step="0.01" placeholder="Preço (Ex: 10.90)"></div>
            <button class="btn-green" onclick="admAdicionar()">CADASTRAR PRODUTO</button>
        </div>

        <div class="input-group">
            <input type="text" id="adm-search-input" placeholder="🔍 Buscar produto no estoque..." oninput="renderAdmin(this.value)" style="border: 2px solid var(--orange-header);">
        </div>
        <div id="adm-lista-geral"></div>
        <button class="btn-green" style="background:#333; margin-top:20px" onclick="location.reload()">SAIR DO MODO ADMIN</button>
    </div>
</section>

<nav class="bottom-nav" id="main-nav" style="display:none;">
    <button class="nav-item" id="nav-home" onclick="irPara('screen-home')">LOJA</button>
    <button class="nav-item" id="nav-cart" onclick="irPara('screen-cart')">CARRINHO (<span id="count">0</span>)</button>
    <button class="nav-item" onclick="logout()">SAIR</button>
</nav>

<script>
    const SENHA_MESTRA = "123"; 
    let estoque = JSON.parse(localStorage.getItem('medela_estoque_v2')) || [];
    let carrinho = [];
    let userAtivo = JSON.parse(localStorage.getItem('medela_user_logado'));

    window.onload = () => {
        if(userAtivo) {
            logarInterface();
            irPara('screen-home');
        } else {
            irPara('screen-login');
        }
    };

    function irPara(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        
        // Controle da Nav
        const isApp = (id === 'screen-home' || id === 'screen-cart');
        document.getElementById('main-nav').style.display = isApp ? 'flex' : 'none';
        
        // Atualiza estilo da nav
        document.getElementById('nav-home').classList.toggle('nav-active', id === 'screen-home');
        document.getElementById('nav-cart').classList.toggle('nav-active', id === 'screen-cart');

        if(id === 'screen-home') renderHome();
        if(id === 'screen-cart') renderCart();
        if(id === 'screen-admin') renderAdmin();
    }

    // --- SISTEMA DE CADASTRO E LOGIN ---
    function salvarCadastro() {
        const nome = document.getElementById('reg-nome').value.trim();
        const cpf = document.getElementById('reg-cpf').value.trim();
        const end = document.getElementById('reg-end').value.trim();

        if(!nome || cpf.length < 14 || !end) {
            return alert("Por favor, preencha todos os campos corretamente.");
        }

        const cliente = { nome, cpf, end };
        localStorage.setItem(`cliente_${cpf}`, JSON.stringify(cliente));
        alert("Cadastro realizado! Agora você pode entrar.");
        irPara('screen-login');
    }

    function fazerLogin() {
        const cpf = document.getElementById('login-cpf').value.trim();
        const dadosSalvos = localStorage.getItem(`cliente_${cpf}`);

        if(dadosSalvos) {
            userAtivo = JSON.parse(dadosSalvos);
            localStorage.setItem('medela_user_logado', JSON.stringify(userAtivo));
            logarInterface();
            irPara('screen-home');
        } else {
            alert("CPF não cadastrado. Por favor, faça seu cadastro primeiro.");
        }
    }

    function logarInterface() {
        document.getElementById('user-display').innerText = `Olá, ${userAtivo.nome.split(' ')[0]}!`;
    }

    // --- LOJA ---
    function renderHome(busca = "") {
        const grid = document.getElementById('product-grid');
        grid.innerHTML = "";
        
        const filtrados = estoque.filter(p => p.n.toLowerCase().includes(busca.toLowerCase()));

        if(filtrados.length === 0) {
            grid.innerHTML = "<p style='grid-column:1/-1; text-align:center; padding:20px;'>Nenhum produto disponível.</p>";
            return;
        }

        grid.innerHTML = filtrados.map(p => `
            <div class="item-card">
                <b style="font-size:15px; min-height:36px; display:block;">${p.n}</b>
                <span style="color:var(--primary-green); font-weight:bold;">R$ ${p.p.toFixed(2)} / ${p.tipo.toUpperCase()}</span>
                <div class="qty-selector">
                    <input type="number" id="qty-${p.id}" class="qty-input" value="1" step="${p.tipo === 'kg' ? '0.1' : '1'}" min="0.1">
                    <span>${p.tipo}</span>
                </div>
                <button onclick="addAoCarrinho(${p.id})" class="btn-green" style="padding:8px; font-size:12px;">ADICIONAR</button>
            </div>
        `).join('');
    }

    function addAoCarrinho(id) {
        const p = estoque.find(x => x.id === id);
        const qtd = parseFloat(document.getElementById(`qty-${id}`).value);
        
        if(!qtd || qtd <= 0) return alert("Quantidade inválida");

        carrinho.push({ ...p, qtdPedida: qtd, subtotal: p.p * qtd });
        document.getElementById('count').innerText = carrinho.length;
        alert("Adicionado ao carrinho!");
    }

    function renderCart() {
        const container = document.getElementById('cart-items-list');
        container.innerHTML = "";
        
        if(carrinho.length === 0) {
            container.innerHTML = "<center style='margin-top:50px;'>O carrinho está vazio.</center>";
            document.getElementById('cart-footer').style.display = "none";
            return;
        }
        
        let sub = 0;
        container.innerHTML = carrinho.map((it, i) => {
            sub += it.subtotal;
            return `
                <div class="list-item">
                    <div>
                        <b>${it.n}</b><br>
                        <small>${it.qtdPedida}${it.tipo} x R$ ${it.p.toFixed(2)}</small>
                    </div>
                    <div style="text-align:right">
                        <b>R$ ${it.subtotal.toFixed(2)}</b><br>
                        <span onclick="remCart(${i})" style="color:red; font-size:11px; cursor:pointer;">Remover</span>
                    </div>
                </div>
            `;
        }).join('');
        
        document.getElementById('sub-val').innerText = "R$ " + sub.toFixed(2);
        document.getElementById('total-val').innerText = "R$ " + (sub + 7).toFixed(2);
        document.getElementById('cart-footer').style.display = "block";
    }

    function remCart(i) {
        carrinho.splice(i, 1);
        document.getElementById('count').innerText = carrinho.length;
        renderCart();
    }

    // --- ADMIN ---
    function acessoAdmin() {
        if(prompt("Senha de acesso:") === SENHA_MESTRA) irPara('screen-admin');
        else alert("Senha incorreta.");
    }

    function renderAdmin(busca = "") {
        const lista = document.getElementById('adm-lista-geral');
        const filtrados = estoque.filter(p => p.n.toLowerCase().includes(busca.toLowerCase()));
        
        lista.innerHTML = filtrados.map(p => `
            <div class="list-item">
                <span><b>${p.n}</b> <span class="badge-type">${p.tipo}</span><br>R$ ${p.p.toFixed(2)}</span>
                <button onclick="admRemover(${p.id})" style="background:#ff4d4d; color:white; border:none; padding:8px; border-radius:5px; cursor:pointer;">APAGAR</button>
            </div>
        `).join('');
    }

    function admAdicionar() {
        const n = document.getElementById('adm-nome').value.trim();
        const t = document.getElementById('adm-tipo').value;
        const p = parseFloat(document.getElementById('adm-preco').value);

        if(!n || isNaN(p)) return alert("Preencha corretamente.");

        estoque.push({ id: Date.now(), n, tipo: t, p });
        localStorage.setItem('medela_estoque_v2', JSON.stringify(estoque));
        document.getElementById('adm-nome').value = "";
        document.getElementById('adm-preco').value = "";
        renderAdmin();
        alert("Produto salvo!");
    }

    function admRemover(id) {
        if(confirm("Deseja apagar este produto?")) {
            estoque = estoque.filter(p => p.id !== id);
            localStorage.setItem('medela_estoque_v2', JSON.stringify(estoque));
            renderAdmin(document.getElementById('adm-search-input').value);
        }
    }

    // --- WHATSAPP ---
    function enviarWhatsApp() {
        let msg = `*NOVO PEDIDO - MEDELA SUPERMERCADO*\n\n`;
        msg += `*Cliente:* ${userAtivo.nome}\n`;
        msg += `*CPF:* ${userAtivo.cpf}\n`;
        msg += `*Endereço:* ${userAtivo.end}\n`;
        msg += `----------------------------\n`;
        
        carrinho.forEach(i => {
            msg += `• ${i.n} (${i.qtdPedida}${i.tipo}) = R$ ${i.subtotal.toFixed(2)}\n`;
        });
        
        msg += `----------------------------\n`;
        msg += `*Subtotal:* ${document.getElementById('sub-val').innerText}\n`;
        msg += `*Entrega:* R$ 7,00\n`;
        msg += `*TOTAL:* ${document.getElementById('total-val').innerText}`;
        
        window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`);
    }

    function mascaraCPF(i) {
        let v = i.value.replace(/\D/g, "");
        v = v.replace(/(\d{3})(\d)/, "$1.$2");
        v = v.replace(/(\d{3})(\d)/, "$1.$2");
        v = v.replace(/(\d{3})(\d{1,2})$/, "$1-$2");
        i.value = v;
    }

    function logout() {
        localStorage.removeItem('medela_user_logado');
        location.reload();
    }
</script>
</body>
</html>
