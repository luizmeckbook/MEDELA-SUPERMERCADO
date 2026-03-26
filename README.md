// CONFIG
const firebaseConfig = {
  apiKey: "COLE_AQUI",
  authDomain: "COLE_AQUI",
  projectId: "COLE_AQUI"
};

frietorerules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    match /produtos/{id} {
      allow read: if true;
      allow write: if request.auth != null;
    }

    match /pedidos/{id} {
      allow read, write: if request.auth != null;
    }

    match /usuarios/{id} {
      allow read, write: if request.auth != null;
    }
  }
}firebase.initializeApp(firebaseConfig);
const auth = firebase.auth();
const db = firebase.firestore();

let carrinho = [];
let usuarioAtual = null;
let isAdmin = false;

// LOGIN
function registrar(){
  auth.createUserWithEmailAndPassword(CPF,value, senha.value)
  .then(res=>{
    // cria usuário no banco
    db.collection("usuarios").doc(res.user.uid).set({
      email: email.value,
      tipo: "cliente"
    });
  });
}

function login(){
  auth.signInWithEmailAndPassword(email.value, senha.value);
}

// LOGOUT
function logout(){
  auth.signOut();
  location.reload();
}

// USUÁRIO
auth.onAuthStateChanged(async user=>{
  if(user){
    usuarioAtual = user;

    const doc = await db.collection("usuarios").doc(user.uid).get();

    if(doc.exists){
      isAdmin = doc.data().tipo === "admin";
    }

    document.getElementById("app").style.display="block";
    carregarProdutos();

    // mostrar botão logout
    document.body.innerHTML += `<button onclick="logout()">Sair</button>`;
  }
});

// PRODUTOS
function addProduto(){
  if(!isAdmin) return alert("Apenas admin");

  db.collection("produtos").add({
    nome: nomeProd.value,
    preco: Number(precoProd.value),
    criadoPor: usuarioAtual.uid
  });
}

function deletarProduto(id){
  if(!isAdmin) return alert("Apenas admin");

  db.collection("produtos").doc(id).delete();
}

function carregarProdutos(){
  db.collection("produtos").onSnapshot(snap=>{
    let div = document.getElementById("produtos");
    div.innerHTML="";

    snap.forEach(doc=>{
      let p = doc.data();

      div.innerHTML += `
        <p>${p.nome} - R$${p.preco}
        <button onclick="addCarrinho('${p.nome}',${p.preco})">+</button>
        ${isAdmin ? `<button onclick="deletarProduto('${doc.id}')">X</button>` : ""}
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

// PEDIDO PROFISSIONAL
function finalizar(){
  if(carrinho.length === 0) return alert("Carrinho vazio");

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
