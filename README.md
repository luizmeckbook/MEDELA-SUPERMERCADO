<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mercado Medela</title>
    <link rel="stylesheet" href="css/style.css">
</head>
<body>
    <!-- Header / Menu -->
    <header>
        <div class="container">
            <img src="img/logo.png" alt="Mercado Medela Logo" class="logo">
            <nav>
                <ul>
                    <li><a href="#home">Home</a></li>
                    <li><a href="#produtos">Produtos</a></li>
                    <li><a href="#sobre">Sobre</a></li>
                    <li><a href="#contato">Contato</a></li>
                </ul>
            </nav>
        </div>
    </header>

    <!-- Home Section -->
    <section id="home" class="hero">
        <div class="container">
            <h1>Bem-vindo ao Mercado Medela</h1>
            <p>O melhor lugar para encontrar produtos de qualidade.</p>
            <a href="#produtos" class="btn">Ver Produtos</a>
        </div>
    </section>

    <!-- Produtos Section -->
    <section id="produtos">
        <div class="container">
            <h2>Nossos Produtos</h2>
            <div class="produtos-grid">
                <div class="produto-card">
                    <img src="img/produto1.jpg" alt="Produto 1">
                    <h3>Produto 1</h3>
                    <p>Descrição do produto 1.</p>
                    <button>Comprar</button>
                </div>
                <div class="produto-card">
                    <img src="img/produto2.jpg" alt="Produto 2">
                    <h3>Produto 2</h3>
                    <p>Descrição do produto 2.</p>
                    <button>Comprar</button>
                </div>
                <div class="produto-card">
                    <img src="img/produto3.jpg" alt="Produto 3">
                    <h3>Produto 3</h3>
                    <p>Descrição do produto 3.</p>
                    <button>Comprar</button>
                </div>
            </div>
        </div>
    </section>

    <!-- Sobre Section -->
    <section id="sobre">
        <div class="container">
            <h2>Sobre Nós</h2>
            <p>Somos o Mercado Medela, com anos de experiência em oferecer produtos de qualidade e atendimento impecável aos nossos clientes.</p>
        </div>
    </section>

    <!-- Contato Section -->
    <section id="contato">
        <div class="container">
            <h2>Contato</h2>
            <form id="form-contato">
                <input type="text" placeholder="Nome" required>
                <input type="email" placeholder="Email" required>
                <textarea placeholder="Mensagem" required></textarea>
                <button type="submit">Enviar</button>
            </form>
        </div>
    </section>

    <!-- Footer -->
    <footer>
        <div class="container">
            <p>&copy; 2026 Mercado Medela. Todos os direitos reservados.</p>
        </div>
    </footer>

    <script src="js/script.js"></script>
</body>
</html>
