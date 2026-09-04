<script setup>
  import { pedidos } from '@/data/pedidos';
  import { ref } from 'vue';
  import { computed } from 'vue';

  const novoCodigo = ref("");
  const novoCliente = ref("");
  const novoProduto = ref("");
  const precoUnitario = ref("");
  const quantidade = ref("");

  const itens = ref([]);
  const mensagem = ref("");

  let id = 6;

  const totalCompra = computed(() =>
    itens.value.reduce(
      (total, item) => total + item.precoUnitario * item.quantidade,0,),);

  // formatar moeda pegeui de uma atv antiga do sor fabio
  const formatarMoeda = (valor) =>
    valor.toLocaleString('pt-BR', {
      style: 'currency',
      currency: 'BRL',
    });


  const adicionarProduto = () => {
    const preco = precoUnitario.value;
    
    const quantidadeQueTem = quantidade.value;

    if (!novoProduto.value || preco <= 0 || quantidadeQueTem < 1) {
      mensagem.value = 'Dados invalidos';
      return;
    }

    itens.value.push({
      id: id++,
      produto: novoProduto.value,
      precoUnitario: preco ,
      quantidade: quantidadeQueTem,
    });

    novoProduto.value = "";
    precoUnitario.value = "";
    quantidade.value = "" ;
    mensagem.value = "";
  };

  const removerProduto = (id) => {
    itens.value = itens.value.filter((item) => item.id !== id);
    // tbm peguei da atv antiga do sor fabio a todolist
  };

  // limpar fiz igual no hacka 
  const limpar = () => {
    novoCodigo.value = "";
    novoCliente.value = "";
    novoProduto.value = "";
    precoUnitario.value = "";
    quantidade.value = "";
    itens.value = [];
    mensagem.value = "";
  };

  const finalizarPedido = () => {
    if (!novoCodigo.value.trim() ||  !novoCliente.value.trim() || !itens.value.length){
      mensagem.value = 'Informe o código, o cliente e pelo menos um produto.';
      return;
    }

    pedidos.value.push({
      codigo: novoCodigo.value.trim(),
      cliente: novoCliente.value.trim(),
      itens: itens.value.map((item) => ({ ...item })),
    });
    limpar();
    mensagem.value = 'Pedido finalizado';
  };
</script>

<template>
  <main class="container page">
    <header class="page-header">
      <h1>Fazer compra</h1>
      <p>
        Cadastre o cliente e adicione os produtos do pedido.
      </p>
    </header>

    <section class="card" aria-labelledby="dados-pedido">
      <h2 id="dados-pedido">Dados do pedido</h2>

      <div class="form-grid form-grid-two-columns">
        <div class="form-group">
          <label for="codigoPedido">
            Código do pedido
          </label>

          <input
            id="codigoPedido"
            name="codigoPedido"
            type="text"
            placeholder="Ex.: PED-001"
            v-model="novoCodigo"
          />
        </div>

        <div class="form-group">
          <label for="nomeCliente">
            Nome do cliente
          </label>

          <input
            id="nomeCliente"
            name="nomeCliente"
            type="text"
            placeholder="Digite o nome do cliente"
            v-model="novoCliente"
          />
        </div>
      </div>
      <!-- O aluno deverá implementar as mensagens de validação. -->
      <p v-if="mensagem.length < 1">{{ mensagem }}</p>
      <!-- Exiba aqui uma mensagem quando os dados forem inválidos. -->
      
    </section>

    <section class="card" aria-labelledby="adicionar-produto">
      <h2 id="adicionar-produto">Adicionar produto</h2>

      <div class="form-grid form-grid-product">
        <div class="form-group">
          <label for="nomeProduto">
            Produto
          </label>

          <input
            id="nomeProduto"
            name="nomeProduto"
            type="text"
            placeholder="Ex.: Tomate"
            v-model="novoProduto"
          />
        </div>

        <div class="form-group">
          <label for="precoUnitario">
            Preço unitário
          </label>

          <input
            id="precoUnitario"
            name="precoUnitario"
            type="number"
            min="0"
            step="0.01"
            placeholder="0,00"
            v-model="precoUnitario"
          />
        </div>

        <div class="form-group">
          <label for="quantidade">
            Quantidade
          </label>

          <input
            id="quantidade"
            name="quantidade"
            type="number"
            min="1"
            step="1"
            placeholder="0"
            v-model="quantidade"
          />
        </div>
      </div>

      <div class="form-actions">
        <button class="button button-primary" type="button" @click="adicionarProduto">
          Adicionar produto
        </button>
      </div>
    </section>

    <section class="card" aria-labelledby="itens-pedido">
      <h2 id="itens-pedido">Itens do pedido</h2>

      <!--
        O aluno deverá utilizar uma diretiva condicional para
        mostrar uma mensagem na tela quando não houver produtos.
      -->

      <p v-if="itens.length === 0">Nenhum produto foi adicionado</p>

      <!--
        O aluno deverá utilizar v-for para apresentar os produtos.
      -->
      <div class="table-responsive">
        <table>
          <thead>
            <tr>
              <th scope="col">Produto</th>
              <th scope="col">Preço unitário</th>
              <th scope="col">Quantidade</th>
              <th scope="col">Total</th>
              <th scope="col">Ação</th>
            </tr>
          </thead>

          <tbody>
            <tr v-for="item in itens" :key="item.id">
              <td> {{ item.produto }} </td>

              <td> {{ formatarMoeda(item.precoUnitario) }} </td>

              <td> {{ item.quantidade }} </td>

              <td>{{ formatarMoeda(item.precoUnitario * item.quantidade) }} </td>

              <td>
                <button type="button" @click="removerProduto(item.id)">
                  Excluir
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <div class="order-total">
        <span>Total da compra</span>

        <strong>{{ formatarMoeda(totalCompra) }}</strong>
      </div>

      <div class="form-actions">
        <button class="button button-secondary" type="button" @click="limpar()">Limpar</button>

        <button class="button button-primary" type="button" @click="finalizarPedido()">Finalizar pedido</button>
      </div>
    </section>
  </main>
</template>

