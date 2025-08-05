<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Controle de Estoque</title>
  <style>
    body {
      font-family: Arial;
      margin: 20px;
      background-color: #f4f4f4;
    }
    h2 {
      color: #333;
    }
    input, button {
      margin: 5px;
      padding: 8px;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 20px;
      background: white;
    }
    th, td {
      border: 1px solid #ccc;
      padding: 10px;
      text-align: center;
    }
    th {
      background-color: #eee;
    }
    .container {
      background: white;
      padding: 20px;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <div class="container">
    <h2>Controle de Estoque</h2>

    <h3>Cadastrar Produto</h3>
    <input type="text" id="codigo" placeholder="Código">
    <input type="text" id="nome" placeholder="Nome">
    <input type="number" id="quantidade" placeholder="Quantidade">
    <input type="number" step="0.01" id="preco" placeholder="Preço">
    <button onclick="cadastrarProduto()">Cadastrar</button>

    <h3>Entrada / Saída de Produto</h3>
    <input type="text" id="codigoMovimento" placeholder="Código">
    <input type="number" id="qtdMovimento" placeholder="Quantidade">
    <button onclick="entradaProduto()">Entrada</button>
    <button onclick="saidaProduto()">Saída</button>

    <h3>Estoque Atual</h3>
    <table id="tabelaEstoque">
      <thead>
        <tr>
          <th>Código</th>
          <th>Nome</th>
          <th>Quantidade</th>
          <th>Preço</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>
  </div>

  <script>
    let estoque = JSON.parse(localStorage.getItem("estoque")) || {};

    function salvarEstoque() {
      localStorage.setItem("estoque", JSON.stringify(estoque));
    }

    function atualizarTabela() {
      const tbody = document.querySelector("#tabelaEstoque tbody");
      tbody.innerHTML = "";
      for (const codigo in estoque) {
        const item = estoque[codigo];
        const tr = `
          <tr>
            <td>${codigo}</td>
            <td>${item.nome}</td>
            <td>${item.quantidade}</td>
            <td>R$ ${item.preco.toFixed(2)}</td>
          </tr>
        `;
        tbody.innerHTML += tr;
      }
    }

    function cadastrarProduto() {
      const codigo = document.getElementById("codigo").value;
      const nome = document.getElementById("nome").value;
      const quantidade = parseInt(document.getElementById("quantidade").value);
      const preco = parseFloat(document.getElementById("preco").value);

      if (!codigo || !nome || isNaN(quantidade) || isNaN(preco)) {
        alert("Preencha todos os campos corretamente!");
        return;
      }

      if (estoque[codigo]) {
        alert("Produto com esse código já existe!");
        return;
      }

      estoque[codigo] = { nome, quantidade, preco };
      salvarEstoque();
      atualizarTabela();
      document.getElementById("codigo").value = "";
      document.getElementById("nome").value = "";
      document.getElementById("quantidade").value = "";
      document.getElementById("preco").value = "";
    }

    function entradaProduto() {
      const codigo = document.getElementById("codigoMovimento").value;
      const qtd = parseInt(document.getElementById("qtdMovimento").value);

      if (!estoque[codigo]) {
        alert("Produto não encontrado!");
        return;
      }

      estoque[codigo].quantidade += qtd;
      salvarEstoque();
      atualizarTabela();
    }

    function saidaProduto() {
      const codigo = document.getElementById("codigoMovimento").value;
      const qtd = parseInt(document.getElementById("qtdMovimento").value);

      if (!estoque[codigo]) {
        alert("Produto não encontrado!");
        return;
      }

      if (estoque[codigo].quantidade < qtd) {
        alert("Estoque insuficiente!");
        return;
      }

      estoque[codigo].quantidade -= qtd;
      salvarEstoque();
      atualizarTabela();
    }

    // Atualiza a tabela ao abrir a página
    atualizarTabela();
  </script>
</body>
</html>
