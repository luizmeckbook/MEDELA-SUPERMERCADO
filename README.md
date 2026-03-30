acougue-medela/
│
├── index.html
├── login.html
├── admin.html
│
├── css/
│   └── style.css
│
├── js/
│   ├── app.js
│   ├── auth.js
│   ├── admin.js
│   ├── chat.js
│
├── firebase/
│   └── config.js
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import { getFirestore } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "SUA_KEY",
  authDomain: "SEU_DOMINIO",
  projectId: "SEU_ID",
};

const app = initializeApp(firebaseConfig);
export const db = getFirestore(app);
body { font-family: Arial; margin: 0; background: #f5f5f5; }

header {
  background: #b30000;
  color: white;
  padding: 15px;
  font-size: 20px;
}

.container { padding: 15px; }

.produto {
  background: white;
  margin: 10px 0;
  padding: 15px;
  border-radius: 10px;
}

button {
  background: #b30000;
  color: white;
  border: none;
  padding: 10px;
  border-radius: 5px;
}

input {
  padding: 10px;
  margin: 5px 0;
  width: 100%;
}

/* CHAT */
#chatBox {
  background: #e5ddd5;
  height: 300px;
  overflow-y: auto;
  padding: 10px;
  border-radius: 10px;
}

.msg {
  padding: 10px;
  margin: 5px;
  border-radius: 10px;
  max-width: 70%;
}

.cliente {
  background: #dcf8c6;
  margin-left: auto;
  text-align: right;
}

.admin {
  background: white;
}
<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" href="css/style.css">
</head>
<body>

<div class="container">
<h2>Login</h2>
<input id="cpf" placeholder="CPF">
<input id="senha" type="password" placeholder="Senha">
<button onclick="login()">Entrar</button>
</div>

<script type="module" src="js/auth.js"></script>
</body>
</html>
import { db } from "../firebase/config.js";
import { collection, query, where, getDocs } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

window.login = async () => {
  const cpf = document.getElementById("cpf").value;
  const senha = document.getElementById("senha").value;

  if (!cpf || !senha) {
    alert("Preencha tudo");
    return;
  }

  const q = query(
    collection(db, "usuarios"),
    where("cpf", "==", cpf),
    where("senha", "==", senha)
  );

  const res = await getDocs(q);

  if (res.empty) {
    alert("Login inválido");
  } else {
    localStorage.setItem("cpf", cpf);
    window.location.href = "index.html";
  }
};
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
<p id="total"></p>
<button onclick="finalizar()">Finalizar Pedido</button>

<h2>Status do Pedido</h2>
<p id="status"></p>

<h2>Chat</h2>
<div id="chatBox"></div>
<input id="msg" placeholder="Digite mensagem...">
<button onclick="enviarMensagem()">Enviar</button>

<br><br>
<a href="admin.html">Painel Admin</a>

</div>

<script type="module" src="js/app.js"></script>
<script type="module" src="js/chat.js"></script>

</body>
</html>
import { db } from "../firebase/config.js";
import {
  collection,
  getDocs,
  addDoc,
  onSnapshot
} from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

let carrinho = [];
const cpf = localStorage.getItem("cpf");

if (!cpf) {
  window.location.href = "login.html";
}

async function carregarProdutos() {
  const snap = await getDocs(collection(db, "produtos"));
  let html = "";

  snap.forEach(doc => {
    let p = doc.data();
    html += `
      <div class="produto">
        ${p.nome} - R$${p.preco}
        <button onclick="add('${p.nome}', ${p.preco})">+</button>
      </div>
    `;
  });

  document.getElementById("produtos").innerHTML = html;
}

window.add = (nome, preco) => {
  carrinho.push({ nome, preco });
  render();
};

function render() {
  let html = "";
  let total = 0;

  carrinho.forEach(p => {
    total += p.preco;
    html += `<p>${p.nome} - R$${p.preco}</p>`;
  });

  document.getElementById("carrinho").innerHTML = html;
  document.getElementById("total").innerText = "Total: R$" + total;
}

window.finalizar = async () => {
  await addDoc(collection(db, "pedidos"), {
    cpf,
    carrinho,
    status: "Em preparo"
  });

  alert("Pedido realizado!");
};

onSnapshot(collection(db, "pedidos"), snap => {
  snap.forEach(doc => {
    let d = doc.data();
    if (d.cpf === cpf) {
      document.getElementById("status").innerText = d.status;
    }
  });
});

carregarProdutos();
import { db } from "../firebase/config.js";
import {
  collection,
  addDoc,
  query,
  where,
  orderBy,
  onSnapshot
} from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

const cpf = localStorage.getItem("cpf");

window.enviarMensagem = async () => {
  const input = document.getElementById("msg");
  const texto = input.value;

  if (!texto) return;

  await addDoc(collection(db, "chats"), {
    cpf,
    mensagem: texto,
    tipo: "cliente",
    data: new Date()
  });

  input.value = "";
};

const q = query(
  collection(db, "chats"),
  where("cpf", "==", cpf),
  orderBy("data")
);

onSnapshot(q, (snapshot) => {
  let html = "";

  snapshot.forEach(doc => {
    let msg = doc.data();

    if (msg.tipo === "cliente") {
      html += `<div class="msg cliente">${msg.mensagem}</div>`;
    } else {
      html += `<div class="msg admin">${msg.mensagem}</div>`;
    }
  });

  const chatBox = document.getElementById("chatBox");
  chatBox.innerHTML = html;
  chatBox.scrollTop = chatBox.scrollHeight;
});
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
import { db } from "../firebase/config.js";
import {
  collection,
  addDoc,
  onSnapshot,
  doc,
  updateDoc
} from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

window.addProduto = async () => {
  const nome = document.getElementById("nome").value;
  const preco = parseFloat(document.getElementById("preco").value);

  if (!nome || !preco) {
    alert("Preencha tudo");
    return;
  }

  await addDoc(collection(db, "produtos"), { nome, preco });
  alert("Produto adicionado");
};

window.responder = async (cpf) => {
  const msg = prompt("Resposta:");

  if (!msg) return;

  await addDoc(collection(db, "chats"), {
    cpf,
    mensagem: msg,
    tipo: "admin",
    data: new Date()
  });
};

onSnapshot(collection(db, "pedidos"), snap => {
  let html = "";

  snap.forEach(docu => {
    let d = docu.data();

    html += `
      <div class="produto">
        ${d.cpf} - ${d.status}
        <button onclick="update('${docu.id}','Saiu')">Saiu</button>
        <button onclick="responder('${d.cpf}')">Chat</button>
      </div>
    `;
  });

  document.getElementById("pedidos").innerHTML = html;
});

window.update = async (id, status) => {
  await updateDoc(doc(db, "pedidos", id), { status });
};
