<div class="box">
<h3>Cadastroconst check = await db.collection("usuarios")
  .where("cpf", "==", cpf.value)
  .get();

if(!check.empty){
  return alert("CPF já cadastrado");
} / Login</h3>

<input id="cpf" placeholder="CPF">
<input id="email" placeholder="Email (apenas no cadastro)">
<input id="senha" type="password" placeholder="Senha"><br>

<button onclick="registrar()">Cadastrar</button>
<button onclick="loginCPF()">Entrar</button>
</div>
// CONFIG
const firebaseConfig = {
  apiKey: "COLE_AQUI",
  authDomain: "COLE_AQUI",
  projectId: "COLE_AQUI"
};

firebase.initializeApp(firebaseConfig);
const auth = firebase.auth();
const db = firebase.firestore();

let carrinho = [];
let usuarioAtual = null;

// 🔐 CADASTRO
async function registrar(){
  if(!cpf.value || !email.value || !senha.value){
    return alert("Preencha tudo");
  }

  const user = await auth.createUserWithEmailAndPassword(email.value, senha.value);

  await db.collection("usuarios").doc(user.user.uid).set({
    cpf: cpf.value,
    email: email.value,
    tipo: "cliente"
  });

  alert("Cadastro realizado!");
}

// 🔐 LOGIN COM CPF
async function loginCPF(){
  if(!cpf.value || !senha.value){
    return alert("Preencha CPF e senha");
  }

  // busca usuário pelo CPF
  const snap = await db.collection("usuarios")
    .where("cpf", "==", cpf.value)
    .get();

  if(snap.empty){
    return alert("CPF não encontrado");
  }

  let userData = snap.docs[0].data();

  // login usando email salvo
  await auth.signInWithEmailAndPassword(userData.email, senha.value);
}

// LOGOUT
function logout(){
  auth.signOut();
  location.reload();
}

// USUÁRIO LOGADO
auth.onAuthStateChanged(user=>{
  if(user){
    usuarioAtual = user;
    document.getElementById("app").style.display="block";

    carregarProdutos();

    document.body.innerHTML += `<button onclick="logout()">Sair</button>`;
  }
});

// PRODUTOS
function carregarProdutos(){
  db.collection("produtos").onSnapshot(snap=>{
    let div = document.getElementById("produtos");
    div.innerHTML="";

    snap.forEach(doc=>{
      let p = doc.data();

      div.innerHTML += `
        <p>${p.nome} - R$${p.preco}
        <button onclick="addCarrinho('${p.nome}',${p.preco})">+</button>
        </p>
      `;
    });
  });
}

// CARRINHO
function addCarrinho(nome,preco){
  carrinho.push({nome,preco});
  atualizarCarrinho();
}

function atualizarCarrinho(){
  let div = document.getElementById("carrinho");
  div.innerHTML="";
  let total=0;

  carrinho.forEach((i,index)=>{
    total+=i.preco;

    div.innerHTML += `
      ${i.nome} - R$${i.preco}
      <button onclick="remover(${index})">X</button><br>
    `;
  });

  document.getElementById("total").innerText=total;
}

function remover(i){
  carrinho.splice(i,1);
  atualizarCarrinho();
}

// PEDIDO
function finalizar(){
  if(carrinho.length === 0){
    return alert("Carrinho vazio");
  }

  db.collection("pedidos").add({
    userId: usuarioAtual.uid,
    itens: carrinho,
    total: carrinho.reduce((t,i)=>t+i.preco,0),
    status: "pendente",
    criadoEm: new Date()
  });

  alert("Pedido enviado!");
  carrinho=[];
  atualizarCarrinho();
}
