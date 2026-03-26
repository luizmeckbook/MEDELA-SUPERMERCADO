<!MEDELA SUPERMERCADO>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Medela Supermercado</title>

<style>
body {
  margin: 0;
  font-family: Arial;
  background: #0a0a0a;
  color: white;
}

/* TOPO */
header {
  background: #b30000;
  padding: 15px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

header h1 {
  margin: 0;
}

header input {
  padding: 8px;
  border-radius: 8px;
  border: none;
}

/* BANNER */
.banner {
  background: linear-gradient(45deg,#b30000,#000);
  height: 180px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 28px;
  font-weight: bold;
}

/* PRODUTOS */
.produtos {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px,1fr));
  gap: 15px;
  padding: 15px;
}

.produto {
  background: #1a1a1a;
  padding: 10px;
  border-radius: 10px;
  text-align: center;
  box-shadow: 0 0 10px #b30000;
}

.produto img {
  width: 100%;
  height: 120px;
  object-fit: cover;
  border-radius: 10px;
}

.produto button {
  background: #b30000;
  color: white;
  border: none;
  padding: 8px;
  border-radius: 8px;
  cursor: pointer;
}

/* CARRINHO */
.carrinho {
  position: fixed;
  bottom: 0;
  width: 100%;
  background: #b30000;
  padding: 10px;
}

.item-carrinho {
  display: flex;
  justify-content: space-between;
  margin: 5px 0;
}

.remover {
  background: black;
  color: red;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

</style>
</head>

<body>

<header>
  <h1>🛒 Medela Supermercado</h1>
  <input placeholder="Buscar produto...">
</header>

<div class="banner">
  🔥 Promoções Medela
</div>

<div class="produtos">

  <div class="produto">
    <img src="https://images.unsplash.com/photo-1586201375761-83865001e31c">
    <h3>Arroz 5kg</h3>
    <p>R$ 25</p>
    <button onclick="add('Arroz',25)">Adicionar</button>
  </div>

  <div class="produto">
    <img src="https://images.unsplash.com/photo-1585238342028-4c6a7c64c6d2">
    <h3>Feijão</h3>
    <p>R$ 8</p>
    <button onclick="add('Feijão',8)">Adicionar</button>
  </div>

  <div class="produto">
    <img src="https://images.unsplash.com/photo-1604908177522-432d3a1fa90b">
    <h3>Leite</h3>
    <p>R$ 5</p>
    <button onclick="add('Leite',5)">Adicionar</button>
  </div>

</div>

<div class="carrinho">
  <h3>🛒 Carrinho</h3>
  <div id="lista"></div>
  <p>Total: R$ <span id="total">0</span></p>
  <button onclick="comprar()">Finalizar no WhatsApp</button>
</div>

<script>
let carrinho = [];
let total = 0;

function add(nome, preco){
  carrinho.push({nome, preco});
  atualizar();
}

function remover(index){
  total -= carrinho[index].preco;
  carrinho.splice(index,1);
  atualizar();
}

function atualizar(){
  let lista = document.getElementById("lista");
  lista.innerHTML = "";
  total = 0;

  carrinho.forEach((item,index)=>{
    total += item.preco;

    let div = document.createElement("div");
    div.className = "item-carrinho";

    div.innerHTML = `
      ${item.nome} - R$${item.preco}
      <button class="remover" onclick="remover(${index})">X</button>
    `;

    lista.appendChild(div);
  });

  document.getElementById("total").innerText = total;
}

function comprar(){
  let msg = "Pedido Medela:%0A";

  carrinho.forEach(item=>{
    msg += `${item.nome} - R$${item.preco}%0A`;
  });

  msg += `Total: R$${total}`;

  window.open("https://wa.me/5521999999999?text=" + msg);
}
</script>

</body>
</html>
