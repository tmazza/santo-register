<template>
  <main>
    <div style="text-align: center">
      <b>Novo pedido</b>
      <br />

      <textarea
        name="order"
        id="order"
        cols="30"
        rows="10"
        v-model="order"
      ></textarea>
      <br />
      <button @click="add" class="button">Imprimir</button>
    </div>
    <br />
    <hr />
    <br />
    <div>
      <b>Últimos pedidos</b>

      <div v-for="o of ordersSorted" :key="o.id" class="card">
        <!-- <button @click="remove(o.id)">&times;</button> -->
        <button @click="print(o.id)">Imprimir</button>

        {{ o.description }}
      </div>
    </div>
  </main>
  <div id="print">
    <pre>{{ toPrint }}</pre>
  </div>
</template>

<script>
export default {
  data() {
    return {
      order: "",
      orders: [],
      toPrint: "",
    };
  },
  mounted() {
    let items = JSON.parse(window.localStorage.getItem("orders"));
    console.log("LS", items);
    this.orders = items || [];
  },
  methods: {
    add() {
      if (!this.order) {
        return;
      }

      let id = this.currentDate();
      let description = id + "\n" + this.order;

      this.orders.push({
        id: id,
        description: description,
      });

      this.toPrint = description;
      this.$nextTick(() => {
        window.print();
      });

      this.order = "";

      this.updateStorage();
    },
    remove(id) {
      console.log("remove", id);
      this.updateStorage();
    },
    updateStorage() {
      window.localStorage.setItem("orders", JSON.stringify(this.orders));
    },
    currentDate() {
      let data = new Date();
      let dia = data.getDate().toString();
      let diaF = dia.length == 1 ? "0" + dia : dia;
      let mes = (data.getMonth() + 1).toString();
      let mesF = mes.length == 1 ? "0" + mes : mes;
      let anoF = data.getFullYear();
      let h = data.getHours();
      let m = data.getMinutes();
      let s = data.getSeconds();

      return diaF + "/" + mesF + "/" + anoF + " " + h + ":" + m + ":" + s;
    },
    clearAll() {
      this.orders = [];
      this.updateStorage();
    },
    print(id) {
      let order = this.orders.find((o) => o.id == id);
      this.toPrint = order.description;
      window.print();
    },
  },
  computed: {
    ordersSorted() {
      let items = this.orders;
      items.sort();
      items.reverse();

      return items;
    },
  },
};
</script>

<style scoped>
main {
  margin: 0 auto;
  border: 1px solid red;
  max-width: 320px;
  text-align: center;
}

textarea {
  text-align: left;
}
.card {
  text-align: left;
  border: 1px solid blue;
}

#print {
  background: white;
  color: #000000;
  font-size: 14px;
  display: none;
}

@media print {
  #print {
    display: block;
  }
  main {
    display: none;
  }
}
</style>