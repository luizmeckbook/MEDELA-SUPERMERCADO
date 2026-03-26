<!MEDELA SUPERMERCADOl>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Medela Supermercado</title>
    <style>
        /* CONFIGURAÇÕES GERAIS */
        :root {
            --primary-green: #00a859;
            --orange-main: #f37021;
            --bg-gray: #f2f2f2;
            --text-dark: #333;
            --white: #ffffff;
        }

        body {
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-gray);
            margin: 0;
            padding: 0;
            color: var(--text-dark);
        }

        /* HEADER LARANJA (HOME) */
        .header-home {
            background: linear-gradient(135deg, var(--orange-main), #ff8c42);
            padding: 25px 20px 50px 20px;
            color: white;
            border-radius: 0 0 25px 25px;
            text-align: left;
        }

        .logo-text {
            font-size: 24px;
            font-weight: bold;
            margin-bottom: 5px;
        }

        /* CARDS DE STATUS */
        .status-container {
            display: flex;
            gap: 12px;
            padding: 0 20px;
            margin-top: -30px;
        }

        .status-card {
            background: var(--white);
            flex: 1;
            padding: 15px;
            border-radius: 12px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.08);
        }

        .status-card span { font-size: 11px; color: #666; display: block; }
        .status-card strong { font-size: 18px; display: block; margin-top: 4px; }
        .status-green { color: var(--primary-green); }

        /* SEÇÃO DE OFERTAS */
        .section-title { padding: 20px; font-size: 18px; font-weight: bold; }

        .product-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            padding: 0 20px 100px 20px;
        }

        .product-card {
            background: var(--white);
            border-radius: 15px;
            padding: 12px;
            text-align: center;
            position: relative;
        }

        .economy-tag {
            background: #fff0e5;
            color: var(--orange-main);
            font-size: 10px;
            padding: 4px 8px;
            border-radius: 4px;
            position: absolute;
            top: 8px; right: 8px;
        }

        .product-card img { width: 100%; max-width: 100px; margin: 10px 0; }
        .product-card h3 { font-size: 13px; margin: 5px 0; height: 32px; overflow: hidden; }
        .old-price { text-decoration: line-through; color: #999; font-size: 12px; }
        .new-price { font-size: 18px; font-weight: bold; color: #000; }

        .btn-activate {
            background: var(--primary-green);
            color: white;
            border: none;
            width: 100%;
            padding: 10px;
            border-radius: 8px;
            margin-top: 10px;
            font-weight: bold;
            font-size: 12px;
        }

        /* FORMULÁRIO DE CADASTRO (ESTILO INPUTS DO PRINT) */
        .form-container {
            background: white;
            padding: 20px;
            margin: 20px;
            border-radius: 15px;
        }

        .input-group { margin-bottom: 15px; }
        .input-group label { display: block; font-size: 13px; margin-bottom: 5px; color: #555; }
        .input-group input, .input-group select {
            width: 100%;
            padding: 12px;
            border: 1px solid #e0e0e0;
            border-radius: 8px;
            background: #f9f9f9;
            box-sizing: border-box;
        }

        /* BARRA INFERIOR */
        .bottom-nav {
            position: fixed;
            bottom: 0;
            width: 100%;
            background: white;
            display: flex;
            justify-content: space-around;
            padding: 12px 0;
            border-top: 1px solid #eee;
        }

        .nav-item { font-size: 10px; color: #888; text-align: center; }
        .nav-active { color: var(--primary-green); font-weight: bold; }
    </style>
</head>
<body>

    <header class="header-home">
        <div class="logo-text">Medela Supermercado</div>
        <p style="font-size: 14px; opacity: 0.9;">Olá, Michele Miranda Cancio</p>
    </header>

    <section class="status-container">
        <div class="status-card">
            <span>Você já economizou:</span>
            <strong class="status-green">R$ 6,21</strong>
        </div>
        <div class="status-card">
            <span>Total de pontos:</span>
            <strong>150</strong>
        </div>
    </section>

    <div class="section-title">Cadastrar no Medela</div>
    <section class="form-container">
        <div class="input-group">
            <label>* Nome Completo</label>
            <input type="text" placeholder="Digite seu nome">
        </div>
        <div class="input-group">
            <label>* CPF/CNPJ</label>
            <input type="text" placeholder="000.000.000-00">
        </div>
        <div class="input-group">
            <label>* Gênero</label>
            <select>
                <option>Selecione uma opção</option>
                <option>Masculino</option>
                <option>Feminino</option>
            </select>
        </div>
    </section>

    <div class="section-title">Ofertas do Dia</div>
    <section class="product-grid">
        <div class="product-card">
            <div class="economy-tag">Economize R$ 5,92</div>
            <img src="https://cdn-icons-png.flaticon.com/512/1134/1134444.png" alt="Carne">
            <h3>ACÉM BOVINO RESFRIADO KG</h3>
            <p class="old-price">R$ 35,90</p>
            <p class="new-price">R$ 29,98</p>
            <button class="btn-activate">Ativar Desconto</button>
        </div>

        <div class="product-card">
            <div class="economy-tag">Economize R$ 2,01</div>
            <img src="https://cdn-icons-png.flaticon.com/512/1041/1041653.png" alt="Frango">
            <h3>PEITO DE FRANGO KG</h3>
            <p class="old-price">R$ 13,99</p>
            <p class="new-price">R$ 11,98</p>
            <button class="btn-activate">Ativar Desconto</button>
        </div>
    </section>

    <nav class="bottom-nav">
        <div class="nav-item nav-active">DESCONTO</div>
        <div class="nav-item">JORNAL</div>
        <div class="nav-item">PONTOS</div>
        <div class="nav-item">CONTA</div>
    </nav>

</body>
</html>
