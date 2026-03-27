<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Medela Supermercado - App</title>
    <style>
        :root {
            --primary-green: #00a859;
            --orange-main: #f37021;
            --bg-gray: #f2f2f2;
            --white: #ffffff;
        }

        body { font-family: 'Segoe UI', sans-serif; background-color: var(--bg-gray); margin: 0; padding: 0; }

        /* ESTRUTURA DE TELAS */
        .app-screen { display: none; min-height: 100vh; width: 100%; box-sizing: border-box; background: white; }
        .active { display: flex; flex-direction: column; }

        /* FORMULÁRIOS ESTILO APP */
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        .logo-area { text-align: center; margin: 30px 0; }
        .logo-area h1 { color: var(--primary-green); margin: 0; font-size: 28px; }
        .logo-area span { color: var(--orange-main); }

        .input-group { margin-bottom: 12px; text-align: left; }
        .input-group label { display: block; font-size: 11px; color: #666; margin-bottom: 4px; font-weight: bold; text-transform: uppercase; }
        .input-group input { 
            width: 100%; padding: 14px; border: 1px solid #ddd; border-radius: 8px; 
            background: #f8f8f8; box-sizing: border-box; font-size: 14px; outline: none;
        }

        .btn-green { 
            background: var(--primary-green); color: white; border: none; width: 100%; 
            padding: 16px; border-radius: 8px; font-weight: bold; cursor: pointer; font-size: 15px; margin-top: 10px;
        }

        /* HEADER E HOME */
        .header-app { background: linear-gradient(135deg, var(--orange-main), #ff8c42); padding: 45px 20px; color: white; border-radius: 0 0 25px 25px; position: relative; }
        .economy-card { background: white; margin: -30px 20px 0 20px; padding: 20px; border-radius: 15px; box-shadow: 0 8px 20px rgba(0,0,0,0.1); z-index: 10; }

        /* PRODUTOS */
        .product-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 20px; margin-bottom: 80px; }
        .product-card { background: white; padding: 15px; border-radius: 12px; text-align: center; border: 1px solid #eee; }
        .product-card img { width: 70px; height: 70px; object-fit: contain; }
        .price-old { text-decoration: line-through; color: #999; font-size: 11px; }
        .price-new { display: block; font-size: 18px; font-weight: bold; color: #000; }

        /* BARRA DE NAVEGAÇÃO */
        .bottom-nav { position: fixed; bottom: 0; width: 100%; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; z-index: 100; }
        .nav-item { border: none; background: none; color: #bbb; font-size: 10px; text-align: center; cursor: pointer; }
        .nav-item.active-nav { color: var(--primary-green); font-weight: bold; }
    </style>
</head>
<body>

    <section id="screen-login" class="app-screen active">
        <div class="container">
            <div class="logo-area">
                <h1>Medela<span> Supermercado</span></h1>
                <p style="color:#888; font-size: 14px;">Use seu CPF ou E-mail para entrar</p>
            </div>
            <div class="input-group">
                <label>* CPF ou E-mail</label>
                <input type="text" id="login-id" placeholder="Digite seu CPF ou Gmail">
            </div>
            <div class="input-group">
                <label>* Senha</label>
                <input type="password" id="login-pass" placeholder="••••••••">
            </div>
            <button class="btn-green" onclick="executarLogin()">Entrar</button>
            <p style="text-align:center; color: var(--primary-green); font-weight:bold; cursor:pointer; margin-top:25px;" onclick="irPara('screen-register')">Criar conta gratuita</p>
        </div>
    </section>

    <section id="screen-register" class="app-screen">
        <div class="container">
            <h2 style="color:var(--primary-green); margin-bottom: 5px;">Cadastrar</h2>
            <p style="color:#888; font-size:13px; margin-bottom: 20px;">Preencha os campos abaixo</p>
            
            <div class="input-group">
                <label>* CPF</label>
                <input type="text" id="reg-cpf" placeholder="000.000.000-00" oninput="mascaraCPF(this)">
            </div>
            <div class="input-group">
                <label>* Nome Completo</label>
                <input type="text" id="reg-nome" placeholder="Nome Sobrenome">
            </div>
            <div class="input-group">
                <label>* Telefone</label>
                <input type="text" id="reg-tel" placeholder="(00) 00000-0000">
            </div>
            <div class="input-group">
                <label>* E-mail (Gmail)</label>
                <input type="email" id="reg-email" placeholder="seuemail@gmail.com">
            </div>
            <div class="input-group">
                <label>* Crie sua Senha</label>
                <input type="password" id="reg-senha" placeholder="Mínimo 6 dígitos">
            </div>
            
            <button class="btn-green" onclick="finalizarCadastro()">Finalizar Cadastro</button>
            <p style="text-align:center; cursor:pointer; font-size:14px; color:#666;" onclick="irPara('screen-login')">Já tenho conta. Voltar</p>
        </div>
    </section>

    <section id="screen-home" class="app-screen">
        <header class="header-app">
            <h2 id="user-display" style="margin:0">Olá!</h2>
            <p style="font-size:14px; opacity:0.9">Você está no Medela Online</p>
        </header>
        
        <div class="economy-card">
            <span style="font-size:11px; color:#999; text-transform: uppercase;">Economia acumulada:</span>
            <h2 style="color:var(--primary-green); margin:5px 0; font-size: 28px;">R$ 0,00</h2>
        </div>

        <div class="product-grid">
            <div class="product-card">
                <img src="https://cdn-icons-png.flaticon.com/512/1134/1134444.png">
                <p style="font-size:12px; margin:10px 0;">Acém Bovino kg</p>
                <span class="price-old">R$ 35,90</span>
                <span class="price-new">R$ 29,98</span>
                <button class="btn-green" style="padding:8px; font-size:11px;">Ativar</button>
            </div>
            <div class="product-card">
                <img src="https://cdn-icons-png.flaticon.com/512/1041/1041653.png">
                <p style="font-size:12px; margin:10px 0;">Peito de Frango kg</p>
                <span class="price-old">R$ 13,99</span>
                <span class="price-new">R$ 11,98</span>
                <button class="btn-green" style="padding:8px; font-size:11px;">Ativar</button>
            </div>
        </div>

        <nav class="bottom-nav">
            <div class="nav-item active-nav">Ofertas</div>
            <div class="nav-item">Jornal</div>
            <div class="nav-item">Extrato</div>
            <div class="nav-item" onclick="location.reload()">Sair</div>
        </nav>
    </section>

    <script>
        // Troca de Telas
        function irPara(id) {
            document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
            document.getElementById(id).classList.add('active');
            window.scrollTo(0,0);
        }

        // Máscara CPF
        function mascaraCPF(i) {
            let v = i.value.replace(/\D/g, "");
            v = v.replace(/(\d{3})(\d)/, "$1.$2");
            v = v.replace(/(\d{3})(\d)/, "$1.$2");
            v = v.replace(/(\d{3})(\d{1,2})$/, "$1-$2");
            i.value = v;
        }

        // CADASTRO
        function finalizarCadastro() {
            const cpf = document.getElementById('reg-cpf').value;
            const nome = document.getElementById('reg-nome').value;
            const email = document.getElementById('reg-email').value.toLowerCase();
            const senha = document.getElementById('reg-senha').value;

            if (cpf.length < 14 || !email.includes('@') || senha.length < 4) {
                alert("Por favor, preencha os dados corretamente!");
                return;
            }

            const dadosUsuario = { nome, cpf, email, senha };
            
            // Salva duas vezes para permitir busca por CPF ou por Email
            localStorage.setItem(cpf, JSON.stringify(dadosUsuario));
            localStorage.setItem(email, JSON.stringify(dadosUsuario));

            alert("Cadastro realizado! Agora faça o login.");
            irPara('screen-login');
        }

        // LOGIN HÍBRIDO (CPF ou EMAIL)
        function executarLogin() {
            const loginId = document.getElementById('login-id').value.toLowerCase();
            const senha = document.getElementById('login-pass').value;
            
            const dadosRecuperados = localStorage.getItem(loginId);

            if (!dadosRecuperados) {
                alert("Usuário não encontrado!");
                return;
            }

            const user = JSON.parse(dadosRecuperados);

            if (user.senha === senha) {
                document.getElementById('user-display').innerText = "Olá, " + user.nome.split(' ')[0] + "!";
                irPara('screen-home');
            } else {
                alert("Senha incorreta!");
            }
        }
    </script>
</body>
</html>
