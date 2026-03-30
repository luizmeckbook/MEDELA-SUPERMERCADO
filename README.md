<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<title>Açougue Medela</title>

<style>
body { font-family: Arial; margin:0; background:#f5f5f5; }

.box {
  max-width: 320px;
  margin: 80px auto;
  background: white;
  padding: 20px;
  border-radius: 10px;
  text-align: center;
}

input, button {
  width:100%;
  padding:10px;
  margin-top:10px;
}

button {
  background:#b30000;
  color:white;
  border:none;
}

header {
  background:#b30000;
  color:white;
  padding:15px;
  text-align:center;
}

.msg {
  background:#e0e0e0;
  margin:5px;
  padding:8px;
  border-radius:8px;
}

.pedido {
  background:white;
  margin:10px;
  padding:10px;
  border-radius:10px;
}
</style>
</head>
<body>

<!-- LOGIN -->
<div id="login" class="box">
<h2>Login</h2>
<input id="cpfLogin" placeholder="CPF">
<input id="senhaLogin" type="password" placeholder="Senha">
<button onclick="login()">Entrar</button>
<span onclick="mostrarCadastro()">Cadastrar</span>
</div>

<!-- CADASTRO -->
<div id="cadastro" class="box" style="display:none;">
<h2>Cadastro</h2>
<input id="nome" placeholder="Nome">
<input id="cpfCadastro" placeholder="CPF">
<input id="senhaCadastro" type="password" placeholder="Senha">
<button onclick="cadastrar()">Criar</button>
</div>

<!-- APP -->
<div id="app" style="display:none;">
<header>Olá <span id="nomeUser"></span></header>

<h3>Chat</h3>
<div id="chat"></div>
<input id="msg">
<button onclick="enviar()">Enviar</button>

<h3>Pedido</h3>
<button onclick="criarPedido()">Fazer Pedido</button>
<div id="pedidos"></div>
</div>

<!-- ADMIN -->
<div id="admin" style="display:none;">
<header>👑 ADMIN</header>

<h3>Pedidos</h3>
<div id="adminPedidos"></div>

<h3>Chat</h3>
<div id="adminChat"></div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import {
  getFirestore,
  collection,
  addDoc,
  getDocs,
  updateDoc,
  doc,
  onSnapshot
} from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "SUA_KEY",
  authDomain: "SEU_DOMINIO",
  projectId: "SEU_ID"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

// ADMIN MASTER
const MASTER_CPF = "00000000000";
const MASTER_SENHA = "123456";

// TELAS
window.mostrarCadastro = () => {
  login.style.display="none";
  cadastro.style.display="block";
};

// CADASTRO
window.cadastrar = async () => {
  await addDoc(collection(db,"usuarios"),{
    nome:nome.value,
    cpf:cpfCadastro.value,
    senha:senhaCadastro.value,
    admin:false
  });
  alert("Criado!");
};

// LOGIN
let usuarioAtual = "";

window.login = async () => {

  // MASTER
  if(cpfLogin.value==MASTER_CPF && senhaLogin.value==MASTER_SENHA){
    login.style.display="none";
    admin.style.display="block";
    carregarAdmin();
    ouvirChatAdmin();
    ouvirPedidosAdmin();
    return;
  }

  const snap = await getDocs(collection(db,"usuarios"));
  snap.forEach(docu=>{
    const u = docu.data();

    if(u.cpf==cpfLogin.value && u.senha==senhaLogin.value){
      usuarioAtual = u.nome;
      nomeUser.innerText = u.nome;
      login.style.display="none";
      app.style.display="block";

      ouvirChat();
      ouvirPedidos();
    }
  });
};

// CHAT TEMPO REAL
window.enviar = async () => {
  await addDoc(collection(db,"chat"),{
    nome:usuarioAtual,
    msg:msg.value
  });
};

function ouvirChat(){
  onSnapshot(collection(db,"chat"), snap=>{
    chat.innerHTML="";
    snap.forEach(d=>{
      const m = d.data();
      chat.innerHTML += `<div class="msg">${m.nome}: ${m.msg}</div>`;
    });
  });
}

function ouvirChatAdmin(){
  onSnapshot(collection(db,"chat"), snap=>{
    adminChat.innerHTML="";
    snap.forEach(d=>{
      const m = d.data();
      adminChat.innerHTML += `<div class="msg">${m.nome}: ${m.msg}</div>`;
    });
  });
}

// PEDIDOS
window.criarPedido = async () => {
  await addDoc(collection(db,"pedidos"),{
    cliente:usuarioAtual,
    status:"Preparando"
  });
};

function ouvirPedidos(){
  onSnapshot(collection(db,"pedidos"), snap=>{
    pedidos.innerHTML="";
    snap.forEach(d=>{
      const p = d.data();
      if(p.cliente==usuarioAtual){
        pedidos.innerHTML += `
          <div class="pedido">
            Status: ${p.status}
          </div>`;
      }
    });
  });
}

// ADMIN PEDIDOS
function ouvirPedidosAdmin(){
  onSnapshot(collection(db,"pedidos"), snap=>{
    adminPedidos.innerHTML="";
    snap.forEach(d=>{
      const p = d.data();
      adminPedidos.innerHTML += `
        <div>
          ${p.cliente} - ${p.status}
          <button onclick="mudar('${d.id}','Saiu')">Saiu</button>
          <button onclick="mudar('${d.id}','Entregue')">Entregue</button>
        </div>`;
    });
  });
}

window.mudar = async (id,status) => {
  await updateDoc(doc(db,"pedidos",id),{status});
};

function carregarAdmin(){}
</script>

</body>
</html>
