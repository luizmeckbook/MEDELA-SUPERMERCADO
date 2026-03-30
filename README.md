<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<title>Açougue Medela</title>

<script src="https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.12.0/firebase-auth.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js"></script>

<style>
body { font-family: Arial; margin:0; background:#f5f5f5; }
.header { background:#f58634; color:white; padding:15px; font-size:20px; }
.card { background:white; margin:15px; padding:15px; border-radius:10px; }
input, button { width:100%; padding:10px; margin:5px 0; }
.menu { padding:10px; border-bottom:1px solid #eee; }
.admin { background:#222; color:#fff; }
</style>
</head>

<body>

<div id="app"></div>

<script>
/* 🔥 CONFIG FIREBASE */
const firebaseConfig = {
  apiKey: "COLE_AQUI",
  authDomain: "COLE_AQUI",
  projectId: "COLE_AQUI"
};

firebase.initializeApp(firebaseConfig);
const auth = firebase.auth();
const db = firebase.firestore();

/* 🔁 ESTADO */
let user = null;

/* 🔐 LOGIN */
function telaLogin() {
  document.getElementById("app").innerHTML = `
    <div class="card">
      <h2>Açougue Medela</h2>
      <input id="email" placeholder="Email">
      <input id="senha" type="password" placeholder="Senha">
      <button onclick="login()">Entrar</button>
      <button onclick="telaCadastro()">Criar conta</button>
    </div>
  `;
}

function login() {
  const email = document.getElementById("email").value;
  const senha = document.getElementById("senha").value;

  auth.signInWithEmailAndPassword(email, senha)
    .then(() => notificar("Login realizado"))
    .catch(() => alert("Erro no login"));
}

/* 📝 CADASTRO */
function telaCadastro() {
  document.getElementById("app").innerHTML = `
    <div class="card">
      <h2>Cadastro</h2>
      <input id="nome" placeholder="Nome">
      <input id="cpf" placeholder="CPF">
      <input id="email" placeholder="Email">
      <input id="senha" type="password" placeholder="Senha">
      <button onclick="cadastrar()">Cadastrar</button>
    </div>
  `;
}

function cadastrar() {
  const nome = document.getElementById("nome").value;
  const cpf = document.getElementById("cpf").value;
  const email = document.getElementById("email").value;
  const senha = document.getElementById("senha").value;

  auth.createUserWithEmailAndPassword(email, senha)
    .then(userCred => {
      db.collection("usuarios").doc(userCred.user.uid).set({
        nome, cpf, saldo:0, admin:false
      });
      notificar("Conta criada!");
    });
}

/* 🏠 CONTA */
function telaConta(dados) {
  document.getElementById("app").innerHTML = `
    <div class="header">Açougue Medela - ${dados.nome}</div>

    <div class="card">
      <h2>Saldo: R$ ${dados.saldo}</h2>
    </div>

    <div class="card">
      <div class="menu">Cupons</div>
      <div class="menu">Sorteios</div>
      <div class="menu">Histórico</div>
      <div class="menu">Meus dados</div>
      ${dados.admin ? '<div class="menu" onclick="telaAdmin()">PAINEL ADMIN</div>' : ''}
      <div class="menu" onclick="logout()">Sair</div>
    </div>
  `;
}

/* 🛠️ ADMIN */
function telaAdmin() {
  document.getElementById("app").innerHTML = `
    <div class="header admin">PAINEL ADMIN</div>

    <div class="card">
      <input id="uid" placeholder="UID do usuário">
      <input id="valor" placeholder="Adicionar saldo">
      <button onclick="addSaldo()">Adicionar</button>
      <button onclick="voltar()">Voltar</button>
    </div>
  `;
}

function addSaldo() {
  const uid = document.getElementById("uid").value;
  const valor = parseFloat(document.getElementById("valor").value);

  db.collection("usuarios").doc(uid).update({
    saldo: firebase.firestore.FieldValue.increment(valor)
  });

  notificar("Saldo adicionado");
}

/* 🔔 NOTIFICAÇÃO */
function notificar(msg) {
  alert(msg);
}

/* 🔓 LOGOUT */
function logout() {
  auth.signOut();
}

/* 🔁 VOLTAR */
function voltar() {
  carregarUsuario();
}

/* 🔄 CARREGAR USUÁRIO */
function carregarUsuario() {
  const uid = auth.currentUser.uid;

  db.collection("usuarios").doc(uid).get()
    .then(doc => {
      telaConta(doc.data());
    });
}

/* 🔁 MONITOR LOGIN */
auth.onAuthStateChanged(u => {
  if (u) {
    user = u;
    carregarUsuario();
  } else {
    telaLogin();
  }
});
</script>

</body>
</html>
