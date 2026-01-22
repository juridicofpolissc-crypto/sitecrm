<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<title>CRM Imobiliário</title>
<style>
body { font-family: Arial; background:#f4f4f4; margin:0; padding:20px }
h2 { margin-top:0 }
input, button { padding:8px; margin:5px 0; width:100% }
button { background:#007bff; color:#fff; border:none; cursor:pointer }
.card { background:#fff; padding:15px; margin-bottom:15px; border-radius:5px }
.hidden { display:none }
img { max-width:100%; margin-top:5px }
.site-box { border:1px solid #ddd; padding:10px; margin-top:10px }
</style>
</head>

<body>

<!-- LOGIN -->
<div id="login" class="card">
  <h2>Login CRM</h2>
  <input id="email" placeholder="Email">
  <input id="senha" type="password" placeholder="Senha">
  <button onclick="login()">Entrar</button>
</div>

<!-- PAINEL -->
<div id="painel" class="hidden">

  <h2>Painel Administrativo</h2>
  <button onclick="logout()">Sair</button>

  <!-- CRIAR SITE -->
  <div class="card">
    <h3>Criar Imobiliária / Site</h3>
    <input id="siteNome" placeholder="Nome do site">
    <input type="file" id="siteLogo">
    <button onclick="criarSite()">Criar Site</button>
  </div>

  <!-- CADASTRAR IMÓVEL -->
  <div class="card">
    <h3>Cadastrar Imóvel</h3>
    <input id="imovelTitulo" placeholder="Título">
    <input id="imovelValor" placeholder="Valor">
    <input id="siteId" placeholder="ID do Site">
    <input type="file" id="imovelFotos" multiple>
    <button onclick="criarImovel()">Cadastrar</button>
  </div>

  <!-- LISTAGEM -->
  <div class="card">
    <h3>Sites e Imóveis</h3>
    <div id="lista"></div>
  </div>

</div>

<script>
/* LOGIN PADRÃO */
const ADMIN = { email:"admin@crm.com", senha:"admin123" };

/* STORAGE */
const sites = JSON.parse(localStorage.getItem("sites") || "[]");
const imoveis = JSON.parse(localStorage.getItem("imoveis") || "[]");

function login(){
  if(email.value === ADMIN.email && senha.value === ADMIN.senha){
    localStorage.setItem("logado", "1");
    mostrarPainel();
  } else alert("Login inválido");
}

function logout(){
  localStorage.removeItem("logado");
  location.reload();
}

function mostrarPainel(){
  login.classList.add("hidden");
  painel.classList.remove("hidden");
  render();
}

/* BASE64 */
function fileToBase64(file){
  return new Promise(r=>{
    const fr = new FileReader();
    fr.onload = () => r(fr.result);
    fr.readAsDataURL(file);
  });
}

/* CRIAR SITE */
async function criarSite(){
  const logo = await fileToBase64(siteLogo.files[0]);
  sites.push({
    id: Date.now(),
    nome: siteNome.value,
    logo
  });
  localStorage.setItem("sites", JSON.stringify(sites));
  render();
}

/* CRIAR IMÓVEL */
async function criarImovel(){
  const fotos = [];
  for(const f of imovelFotos.files)
    fotos.push(await fileToBase64(f));

  imoveis.push({
    siteId: siteId.value,
    titulo: imovelTitulo.value,
    valor: imovelValor.value,
    fotos
  });
  localStorage.setItem("imoveis", JSON.stringify(imoveis));
  render();
}

/* RENDER */
function render(){
  lista.innerHTML = "";
  sites.forEach(s=>{
    const div = document.createElement("div");
    div.className = "site-box";
    div.innerHTML = `
      <strong>ID:</strong> ${s.id}<br>
      <strong>${s.nome}</strong><br>
      <img src="${s.logo}">
    `;
    imoveis.filter(i=>i.siteId==s.id)
      .forEach(i=>{
        div.innerHTML += `<p>${i.titulo} - R$ ${i.valor}</p>`;
        i.fotos.forEach(f=> div.innerHTML += `<img src="${f}">`);
      });
    lista.appendChild(div);
  });
}

/* AUTO LOGIN */
if(localStorage.getItem("logado")) mostrarPainel();
</script>

</body>
</html>
