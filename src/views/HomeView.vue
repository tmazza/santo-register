<template>
  <main>
    <div style="text-align: center; max-width: 320px; margin: 0 auto">
      <b>Novo pedido</b>
      <br />

      <div class="form">
        <textarea
          class="form-control"
          name="order"
          id="order"
          cols="30"
          rows="10"
          v-model="order.description"
        ></textarea>
        <br />
        <input
          class="form-control"
          type="number"
          min="0"
          max="100000"
          step="0.05"
          v-model="order.amount"
        />
      </div>
      <br />
      <button @click="add" class="btn btn-info">Imprimir</button>
    </div>

    <hr />
    <br />
    <div style="margin: 0 auto; max-width: 640px">
      <b>
        Últimos pedidos <small>({{ lastCount }})</small>
      </b>
      <table>
        <tr v-for="o of ordersSorted" :key="o.id">
          <td>
            <button class="btn btn-info btn-sm" @click="print(o.id)">
              Imprimir
            </button>
          </td>
          <td>{{ o.idSeq }}</td>
          <td style="padding-left: 6px">{{ o.description }}</td>
          <td>{{ o.amount }}</td>
        </tr>
      </table>
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
      order: {
        id: "",
        idSeq: 0,
        description: "",
        amount: 0,
      },
      orders: [],
      toPrint: "",
      lastCount: 0,
    };
  },
  mounted() {
    let items = JSON.parse(window.localStorage.getItem("orders"));
    this.orders = items || [];

    let lastCount = parseInt(
      JSON.parse(window.localStorage.getItem("lastCount"))
    );
    this.lastCount = lastCount || 0;
  },
  methods: {
    add() {
      if (!this.order.description) {
        return;
      }

      let id = this.currentDate();
      let description =
        id + "\n" + this.order.description + "\n\nTotal: " + this.order.amount;

      this.order.description = description;
      this.order.id = id;
      this.order.idSeq = this.lastCount;

      this.orders.push(this.order);

      this.toPrint = description;
      this.$nextTick(() => {
        window.print();
      });
      this.resetOrder();

      this.updateStorage();
    },
    remove(id) {
      console.log("remove", id);
      this.updateStorage();
    },
    updateStorage() {
      window.localStorage.setItem("orders", JSON.stringify(this.orders));
      window.localStorage.setItem("lastCount", ++this.lastCount);
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
      this.$nextTick(() => {
        window.print();
      });
    },
    resetOrder() {
      this.order = {
        id: "",
		idSeq: "",
        description: "",
        amount: 0,
      };
    },
  },
  computed: {
    ordersSorted() {
      return this.orders.sort((a, b) => {
        return b.idSeq - a.idSeq;
      });
    },
  },
};
</script>

<style scoped>
main {
  margin: 0 auto;
  text-align: center;
}

textarea {
  text-align: left;
}

#print {
  background: white;
  color: #000000;
  font-size: 14px;
  display: none;
}
.form {
  display: flex;
  flex-direction: column;
}
.form input {
  margin-top: 8px;
}
table {
  width: 100%;
}
table tr td {
  text-align: left;
  padding: 12px;
}

@media print {
  #print {
    display: block;
    color: #000000 !important;
  }
  main {
    display: none;
  }
}
</style>