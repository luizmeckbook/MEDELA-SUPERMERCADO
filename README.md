<script>
    // BANCO DE DADOS DE PRODUTOS
    const produtosMaster = [
        { cat: "Mercearia Seca", itens: ["Arroz Agulhinha", "Arroz Integral", "Arroz Parbolizado", "Feijão Carioca", "Feijão Preto", "Feijão Fradinho", "Lentilha", "Grão-de-bico", "Farinha de Trigo", "Farinha de Milho", "Farinha de Mandioca", "Fubá", "Aveia", "Espaguete", "Macarrão Parafuso", "Macarrão Penne", "Lasanha Massa", "Nhoque", "Macarrão Instantâneo", "Talharim", "Óleo de Soja", "Óleo de Canola", "Óleo de Girassol", "Azeite Extra-virgem", "Vinagre Branco", "Milho Enlatado", "Ervilha", "Palmito", "Extrato de Tomate", "Atum em Lata", "Sardinha em Lata", "Azeitona", "Ketchup", "Mostarda", "Maionese", "Sal Refinado", "Sal Grosso", "Açúcar Cristal", "Açúcar Mascavo", "Café em Pó", "Café Solúvel", "Cápsulas Café", "Chá Preto", "Mate", "Biscoito Cream-cracker", "Rosquinhas", "Granola", "Leite Condensado", "Creme de Leite", "Achocolatado", "Geleia"] },
        { cat: "Hortifrúti", itens: ["Banana", "Laranja", "Mamão", "Melão", "Maçã", "Uva", "Abacaxi", "Kiwi", "Morango", "Abacate", "Tomate", "Cebola", "Alho", "Batata", "Cenoura", "Mandioca", "Abobrinha", "Berinjela", "Pimentão", "Pepino", "Alface Crespa", "Couve", "Rúcula", "Brócolis", "Repolho", "Salsinha", "Cebolinha", "Coentro", "Manjericão"] },
        { cat: "Açougue", itens: ["Picanha KG", "Alcatra KG", "Contrafilé KG", "Coxão Mole KG", "Patinho KG", "Carne Moída", "Lombo Suíno", "Pernil Suíno", "Costela Suína", "Linguiça Toscana", "Frango Inteiro", "Peito de Frango", "Coxa de Frango", "Asas de Frango", "Ovos Brancos 30un", "Tilápia", "Camarão", "Bacalhau"] },
        { cat: "Laticínios", itens: ["Leite Integral", "Leite Desnatado", "Leite sem Lactose", "Iogurte Natural", "Iogurte Frutas", "Queijo Muçarela", "Queijo Prato", "Queijo Minas", "Parmesão Ralado", "Requeijão", "Manteiga", "Margarina"] },
        { cat: "Bebidas", itens: ["Água s/ Gás", "Água c/ Gás", "Coca-Cola 2L", "Guaraná 2L", "Suco Concentrado", "Néctar Uva", "Água de Coco", "Cerveja Lata", "Vinho Tinto", "Vinho Branco", "Espumante", "Cachaça", "Vodca", "Energético"] },
        { cat: "Limpeza", itens: ["Sabão em Pó", "Sabão Líquido", "Amaciante", "Alvejante", "Detergente", "Esponja", "Desinfetante", "Água Sanitária", "Multiuso", "Saco de Lixo", "Papel Higiênico", "Papel Toalha"] },
        { cat: "Higiene", itens: ["Sabonete Barra", "Sabonete Líquido", "Xampu", "Condicionador", "Pasta de Dente", "Escova Dental", "Desodorante", "Absorvente", "Lâmina Barbear"] },
        { cat: "Congelados", itens: ["Batata Palito", "Pizza Congelada", "Lasanha Congelada", "Hambúrguer", "Nuggets", "Pão de Queijo", "Sorvete Pote", "Açaí"] },
        { cat: "Padaria", itens: ["Pão Francês", "Pão de Forma", "Torrada", "Bolo Pronto", "Mel", "Geleia Morango"] },
        { cat: "Outros", itens: ["Ração Cão", "Ração Gato", "Areia Higiênica", "Pilhas AA", "Vela", "Fósforo"] }
    ];

    let carrinho = [];
    let usuarioLogado = null;
    let categoriaAtual = "Mercearia Seca";

    // --- FUNÇÃO DE INICIALIZAÇÃO (IMPEDE VOLTAR PRO LOGIN NO REFRESH) ---
    window.onload = function() {
        const sessaoSalva = localStorage.getItem('sessaoAtiva');
        if (sessaoSalva) {
            usuarioLogado = JSON.parse(sessaoSalva);
            document.getElementById('user-display').innerText = "Olá, " + usuarioLogado.nome.split(' ')[0] + "!";
            irPara('screen-home'); // Pula direto para os produtos
        }
    };

    function botaoPrincipalMenu() {
        if (usuarioLogado) irPara('screen-home');
        else irPara('screen-login');
    }

    function irPara(id) {
        // Trava de segurança: se logado, não deixa ver login/register
        if (usuarioLogado && (id === 'screen-login' || id === 'screen-register')) id = 'screen-home';
        
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('nav-active'));
        
        const target = document.getElementById(id);
        if(target) target.classList.add('active');

        if(id === 'screen-home') {
            document.getElementById('nav-main-btn').classList.add('nav-active');
            renderizarCategorias();
            renderizarProdutos();
        } else if(id === 'screen-cart') {
            document.getElementById('nav-cart-btn').classList.add('nav-active');
            renderizarCarrinho();
        }
        window.scrollTo(0,0);
    }

    function renderizarCategorias() {
        const list = document.getElementById('cat-list');
        list.innerHTML = produtosMaster.map(c => `
            <div class="cat-pill ${categoriaAtual === c.cat ? 'cat-active' : ''}" 
                 onclick="setCat('${c.cat}')">${c.cat}</div>
        `).join('');
    }

    function setCat(cat) {
        categoriaAtual = cat;
        renderizarCategorias();
        renderizarProdutos();
    }

    function renderizarProdutos(filtro = "") {
        const grid = document.getElementById('product-grid');
        grid.innerHTML = "";
        
        produtosMaster.forEach(c => {
            if (filtro === "" && c.cat !== categoriaAtual) return;
            c.itens.forEach(item => {
                if (filtro !== "" && !item.toLowerCase().includes(filtro.toLowerCase())) return;
                const precoFake = (Math.random() * (40 - 5) + 5).toFixed(2);
                grid.innerHTML += `
                    <div class="item-card">
                        <span class="name">${item}</span>
                        <span class="price">R$ ${precoFake}</span>
                        <button class="btn-add-item" onclick="addAoCarrinho('${item}', ${precoFake})">ADICIONAR</button>
                    </div>`;
            });
        });
    }

    function filtrarProdutos() {
        const termo = document.getElementById('search-input').value;
        renderizarProdutos(termo);
    }

    function executarLogin() {
        const idInput = document.getElementById('login-id').value;
        if(idInput.length > 3) {
            // Verifica se o usuário existe no banco local
            const dadosSalvos = localStorage.getItem(idInput);
            usuarioLogado = dadosSalvos ? JSON.parse(dadosSalvos) : { nome: "Cliente", endereco: "Não informado", cpf: idInput };
            
            // SALVA A SESSÃO ATIVA (Isso resolve o problema do refresh)
            localStorage.setItem('sessaoAtiva', JSON.stringify(usuarioLogado));
            
            document.getElementById('user-display').innerText = "Olá, " + usuarioLogado.nome.split(' ')[0] + "!";
            irPara('screen-home');
        } else {
            alert("Digite um CPF válido.");
        }
    }

    // Função para Sair e limpar a sessão
    function fazerLogout() {
        localStorage.removeItem('sessaoAtiva');
        location.reload();
    }

    function finalizarCadastro() {
        const cpf = document.getElementById('reg-cpf').value;
        const nome = document.getElementById('reg-nome').value;
        const endereco = document.getElementById('reg-endereco').value;
        if(cpf && nome) {
            const novoUsuario = {nome, cpf, endereco};
            localStorage.setItem(cpf, JSON.stringify(novoUsuario));
            alert("Cadastro realizado! Faça login.");
            irPara('screen-login');
        }
    }

    function addAoCarrinho(nome, preco) {
        if(!usuarioLogado) return irPara('screen-login');
        carrinho.push({ nome, preco });
        document.getElementById('cart-count').innerText = carrinho.length;
    }

    function renderizarCarrinho() {
        const list = document.getElementById('cart-list');
        const footer = document.getElementById('cart-footer');
        if(carrinho.length === 0) {
            list.innerHTML = '<p style="text-align:center; color:#999; margin-top:50px;">Carrinho vazio.</p>';
            footer.style.display = "none";
            return;
        }
        let total = 0;
        list.innerHTML = carrinho.map((item, index) => {
            total += item.preco;
            return `
                <div class="cart-item">
                    <div><strong>${item.nome}</strong><br><small>R$ ${item.preco.toFixed(2)}</small></div>
                    <button onclick="removerItem(${index})" style="color:red; border:none; background:none; font-weight:bold;">X</button>
                </div>`;
        }).join('');
        document.getElementById('cart-total').innerText = total.toFixed(2);
        footer.style.display = "block";
    }

    function removerItem(index) {
        carrinho.splice(index, 1);
        document.getElementById('cart-count').innerText = carrinho.length;
        renderizarCarrinho();
    }

    function enviarPedido() {
        let msg = `*NOVO PEDIDO - MEU TERÊ*\n\n*Cliente:* ${usuarioLogado.nome}\n*Endereço:* ${usuarioLogado.endereco}\n\n`;
        carrinho.forEach(i => msg += `- ${i.nome}: R$ ${i.preco.toFixed(2)}\n`);
        msg += `\n*TOTAL: R$ ${document.getElementById('cart-total').innerText}*`;
        window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`);
    }

    function mascaraCPF(i) {
        let v = i.value.replace(/\D/g, "");
        v = v.replace(/(\d{3})(\d)/, "$1.$2");
        v = v.replace(/(\d{3})(\d)/, "$1.$2");
        v = v.replace(/(\d{3})(\d{1,2})$/, "$1-$2");
        i.value = v;
    }
</script>
