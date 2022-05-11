<template>
  <main>
    <div style="text-align: center">
      <b>Gestão pedidos</b>
      <br />
      Apagar <input type="number" v-model="amount" />
      <br />
      <br />
      <button @click="clearChunk(amount)" class="button">
        Apagar {{ amount }}
      </button>
    </div>
  </main>
</template>

<script>
export default {
  data() {
    return {
      orders: [],
      amount: 100,
    };
  },
  mounted() {
    let items = JSON.parse(window.localStorage.getItem("orders"));
    this.orders = items || [];
  },
  methods: {
    clearChunk(chunkSize) {
      if (!confirm("Confirma exclusão de todos os registro?")) {
        return false;
      }
      this.orders = this.orders.slice(chunkSize);

      this.updateStorage();
    },
    updateStorage() {
      window.localStorage.setItem("orders", JSON.stringify(this.orders));
      window.localStorage.setItem("lastCount", 1);
    },
  },
};
</script>

<style scoped>
main {
  margin: 0 auto;
  max-width: 320px;
  text-align: center;
}
</style>