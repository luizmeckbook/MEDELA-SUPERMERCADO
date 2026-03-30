🥩 Açougue Medela — Sistema Profissional (VERSÃO 2)

📁 Estrutura (GitHub)

acougue-medela/
│
├── index.html
├── login.html
├── admin.html
│
├── /css/style.css
├── /js/app.js
├── /js/auth.js
├── /js/admin.js
│
├── /firebase/config.js


---

🔐 firebase/config.js

import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import { getFirestore } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "SUA_KEY",
  authDomain: "SEU_DOMINIO",
  projectId: "SEU_ID",
};

const app = initializeApp(firebaseConfig);
export const db = getFirestore(app);


---

🎨 css/style.css

body { font-family: Arial; margin: 0; background: #f5f5f5; }
header { background: #b30000; color: white; padding: 15px; }
.container { padding: 15px; }
.produto { background: white; margin: 10px 0; padding: 10px; border-radius: 10px; }
button { background: #b30000; color: white; border: none; padding: 8px; }


---

🔐 login.html

<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" href="css/style.css">
</head>
<body>
<div class="container">
<h2>Login</h2>
<input id="cpf" placeholder="CPF"><br>
<input id="senha" placeholder="Senha" type="password"><br>
<button onclick="login()">Entrar</button>
</div>
<script type="module" src="js/auth.js"></script>
</body>
</html>


---

🔐 js/auth.js

import { db } from "../firebase/config.js";
import { collection, query, where, getDocs } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

window.login = async () => {
  const cpf = document.getElementById("cpf").value;
  const senha = document.getElementById("senha").value;

  const q = query(collection(db, "usuarios"), where("cpf", "==", cpf), where("senha", "==", senha));
  const res = await getDocs(q);

  if (res.empty) {
    alert("Login inválido");
  } else {
    localStorage.setItem("cpf", cpf);
    window.location.href = "index.html";
  }
};


---

🛒 index.html

<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" href="css/style.css">
</head>
<body>
<header>🥩 Açougue Medela</header>
<div class="container">
<h2>Produtos</h2>
<div id="produtos"></div>

<h2>Carrinho</h2>
<div id="carrinho"></div>
<button onclick="finalizar()">Finalizar</button>

<h2>Status</h2>
<p id="status"></p>

<a href="admin.html">Admin</a>
</div>
<script type="module" src="js/app.js"></script>
</body>
</html>


---

⚙️ js/app.js

import { db } from "../firebase/config.js";
import { collection, getDocs, addDoc, onSnapshot } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

let carrinho = [];
const cpf = localStorage.getItem("cpf");

async function carregar() {
  const snap = await getDocs(collection(db, "produtos"));
  let html = "";

  snap.forEach(doc => {
    let p = doc.data();
    html += `<div class='produto'>${p.nome} - ${p.preco} <button onclick='add("${p.nome}",${p.preco})'>+</button></div>`;
  });

  document.getElementById("produtos").innerHTML = html;
}

window.add = (n, p) => {
  carrinho.push({ n, p });
  render();
};

function render() {
  let html = "";
  carrinho.forEach(i => html += `<p>${i.n}</p>`);
  document.getElementById("carrinho").innerHTML = html;
}

window.finalizar = async () => {
  await addDoc(collection(db, "pedidos"), {
    cpf,
    carrinho,
    status: "Em preparo"
  });
  alert("Pedido feito");
};

onSnapshot(collection(db, "pedidos"), snap => {
  snap.forEach(doc => {
    let d = doc.data();
    if (d.cpf === cpf) document.getElementById("status").innerText = d.status;
  });
});

carregar()

<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" href="css/style.css">
</head>
<body>
<div class="container">
<h2>Admin</h2>
<input id="nome" placeholder="Produto">
<input id="preco" placeholder="Preço">
<button onclick="addProduto()">Adicionar</button>

<h2>Pedidos</h2>
<div id="pedidos"></div>
</div>
<script type="module" src="js/admin.js"></script>
</body>
</html>


---

⚙️ js/admin.js

import { db } from "../firebase/config.js";
import { collection, addDoc, onSnapshot, doc, updateDoc } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

window.addProduto = async () => {
  const nome = document.getElementById("nome").value;
  const preco = parseFloat(document.getElementById("preco").value);

  await addDoc(collection(db, "produtos"), { nome, preco });
  alert("Produto adicionado");
};

onSnapshot(collection(db, "pedidos"), snap => {
  let html = "";

  snap.forEach(docu => {
    let d = docu.data();
    html += `<div>${d.cpf} - ${d.status}
    <button onclick="update('${docu.id}','Saiu')">Saiu</button>
    </div>`;
  });

  document.getElementById("pedidos").innerHTML = html;
});

window.update = async (id, status) => {
  await updateDoc(doc(db, "pedidos", id), { status });
};
