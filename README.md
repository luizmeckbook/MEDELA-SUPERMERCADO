<!MEDELA SUPERMERCADO>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Medela Supermercado - Login</title>
    <style>
        :root {
            --primary-green: #00a859;
            --orange-main: #f37021;
            --bg-gray: #f2f2f2;
            --white: #ffffff;
        }

        body {
            font-family: 'Segoe UI', sans-serif;
            background-color: var(--bg-gray);
            margin: 0;
            overflow-x: hidden;
        }

        /* TELA DE LOGIN */
        #login-screen {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            padding: 20px;
            background: white;
            text-align: center;
        }

        .logo-box {
            margin-bottom: 30px;
        }

        .logo-box h1 {
            color: var(--primary-green);
            font-size: 32px;
            margin: 0;
        }

        .logo-box span {
            color: var(--orange-main);
        }

        .login-card {
            width: 100%;
            max-width: 400px;
        }

        .input-group {
            text-align: left;
            margin-bottom: 20px;
        }

        .input-group label {
            display: block;
            font-size: 13px;
            color: #666;
            margin-bottom: 5px;
        }

        .input-group input {
            width: 100%;
            padding: 15px;
            border: 1px solid #ddd;
            border-radius: 10px;
            background: #f9f9f9;
            box-sizing: border-box;
        }

        .btn-main {
            background-color: var(--primary-green);
            color: white;
            border: none;
            width: 100%;
            padding: 15px;
            border-radius: 10px;
            font-weight: bold;
            font-size: 16px;
            cursor: pointer;
            box-shadow: 0 4px 10px rgba(0,168,89,0.3);
        }

        /* ÁREA DE PRODUTOS (ESCONDIDA INICIALMENTE) */
        #main-content {
            display: none;
        }

        .header-app {
            background: linear-gradient(135deg, var(--orange-main), #ff8c42);
            padding: 30px 20px 60px 20px;
            color: white;
            border-radius: 0 0 30px 30px;
        }

        .economy-bar {
            padding: 0 20px;
            margin-top: -35px;
        }

        .card-economy {
            background: white;
            padding: 20px;
            border-radius: 15px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
            text-align: left;
        }

        .card-economy span { color: #888; font-size: 12px; }
        .card-economy h2 { color: var(--primary-green); margin: 5px 0 0 0; font-size: 24px; }

        .product-list {
            padding: 20px;
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-bottom: 80px;
        }

        .product-item {
            background: white;
            border-radius: 15px;
            padding: 15px;
            text-align: center;
        }

        .product-item img { width: 80px; height: 80px; object-fit: contain; }

        .price-new { font-weight: bold; font-size: 18px; display: block; }
        .price-old { text-decoration: line-through; color: #999; font-size: 12px; }

        /* NAV INFERIOR */
        .bottom-nav {
            position: fixed;
            bottom: 0;
            width: 100%;
            background: white;
            display: flex;
            justify-content: space-around;
            padding: 15px 0;
            border-top: 1px solid #eee;
        }
    </style>
</head>
<body>

    <section id="login-screen">
        <div class="logo-box">
            <h1>Medela<span> Supermercado</span></h1>
            <p>Preencha com seus dados para entrar.</p>
        </div>

        <div class="login-card">
            <div class="input-group">
                <label>* CPF/CNPJ</label>
                <input type="text" placeholder="000.000.000-00" id="user">
            </div>
            <div class="input-group">
                <label>* Senha</label>
                <input type="password" placeholder="••••••••" id="pass">
            </div>
            <button class="btn-main" onclick="fazerLogin()">Entrar</button>
            <p style="color: var(--primary-green); font-size: 14px; margin-top: 20px; cursor: pointer;">Esqueceu sua senha?</p>
            <p style="font-size: 14px; color: #666;">Ainda não tem conta? <strong>Cadastre-se</strong></p>
        </div>
    </section>

    <main id="main-content">
        <header class="header-app">
            <div style="display: flex; justify-content: space-between; align-items: center;">
                <div>
                    <h2 style="margin: 0;">Medela</h2>
                    <p style="margin: 5px 0 0 0; opacity: 0.9; font-size: 14px;">Olá, Cliente Medela</p>
                </div>
                <div style="background: rgba(255,255,255,0.2); padding: 8px; border-radius: 50%;">🔔</div>
            </div>
        </header>

        <section class="economy-bar">
            <div class="card-economy">
                <span>Você já economizou neste mês:</span>
                <h2 id="valor-economia">R$ 0,00</h2>
            </div>
        </section>

        <h3 style="padding: 20px 20px 0 20px; margin-bottom: 0;">Ofertas Especiais</h3>
        
        <section class="product-list">
            <div class="product-item">
                <img src="https://cdn-icons-png.flaticon.com/512/1134/1134444.png">
                <p style="font-size: 12px;">Acém Bovino kg</p>
                <span class="price-old">R$ 35,90</span>
                <span class="price-new">R$ 29,98</span>
                <button class="btn-main" style="padding: 8px; font-size: 12px; margin-top: 10px;">Ativar</button>
            </div>
            <div class="product-item">
                <img src="https://cdn-icons-png.flaticon.com/512/1041/1041653.png">
                <p style="font-size: 12px;">Peito de Frango kg</p>
                <span class="price-old">R$ 13,99</span>
                <span class="price-new">R$ 11,98</span>
                <button class="btn-main" style="padding: 8px; font-size: 12px; margin-top: 10px;">Ativar</button>
            </div>
        </section>

        <nav class="bottom-nav">
            <div style="color: var(--primary-green); font-weight: bold;">Ofertas</div>
            <div>Extrato</div>
            <div>Perfil</div>
        </nav>
    </main>

    <script>
        function fazerLogin() {
            // Simulação simples de transição
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('main-content').style.display = 'block';
            
            // Aqui você poderá futuramente buscar o valor real do seu banco de dados
            console.log("Usuário logado. Sincronizando dados...");
        }
    </script>

</body>
</html>
