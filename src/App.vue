<template>
  <div id="app">
    <h1>knapsack</h1>
    <form @submit.prevent="addItem">
      <input
        type="text"
        v-model="item.name"
      >
      <input
        type="number"
        v-model="item.weight"
      >
      <input
        type="number"
        v-model="item.value"
      >
      <input
        type="submit"
        value="Adicionar"
      >
    </form>
    <input
      type="number"
      v-model="maxWeight"
    >
    <ul>
      <li
        v-for="i in items"
        :key="i.id"
      >
        item:{{i.name}} | w:{{i.weight}} | v:{{i.value}} | <button @click="removeItem(i)">X</button>
      </li>
    </ul>
    
    <div>
      <h1>Levar</h1>
        <h3
          v-for="item in knapsack.items"
          :key="item"
        >{{item.name}} w:{{item.weight}} v:{{item.value}}</h3>
        <h2>Total {{knapsack.total}}</h2>
    </div>
    <div>
      <table>
        <tr>
          <th></th>
          <th
            v-for="w in knapsack.matrix[0].length"
            :key="w"
          >{{w}}</th>
        </tr>
        <tr
          v-for="(row, index) in knapsack.matrix"
          :key="row"
        >
          <td><b>{{index}}</b></td>
          <td
            v-for="i in row"
            :key="i"
          >{{i}}</td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      answer: "",
      item: {
        id: 4,
        name: "",
        weight: 0,
        value: 0
      },
      maxWeight: 5,
      items: [
        {
          id: 1,
          name: "a",
          weight: 1,
          value: 6
        },
        {
          id: 2,
          name: "b",
          weight: 2,
          value: 10
        },
        {
          id: 3,
          name: "c",
          weight: 3,
          value: 12
        }
      ]
    };
  },
  methods: {
    addItem() {
      if (this.item.name == "" || this.item.weight == "") {
        alert("Preencha todos os campos");
        return;
      }
      this.items.push(this.item);
      this.item = {
        id: (this.item.id += 1),
        name: "",
        weight: 0,
        value: 0
      };
    },
    removeItem(item) {
      let i = 0;
      for (i = 0; i < this.items.length; i++) {
        if (item.id == this.items[i].id) break;
      }
      this.items.splice(i, 1);
    }
  },
  computed: {
    knapsack() {
      let W = Number(this.maxWeight);
      let N = this.items.length;
      let K = Array(this.items.length + 1)
        .fill({})
        .map(() => Array(W + 1).fill({}));
      for (let col = 0; col <= W; col++) {
        K[0][col] = { items: [], total: 0 };
      }
      for (let row = 0; row <= N; row++) {
        K[row][0] = { items: [], total: 0 };
      }

      for (let i = 1; i <= N; i++) {
        for (let weight = 1; weight <= W; weight++) {
          if (Number(this.items[i - 1].weight) <= weight) {
            let take =
              Number(this.items[i - 1].value) +
              K[i - 1][weight - Number(this.items[i - 1].weight)].total;
            let notTake = K[i - 1][weight].total;

            if (take == Math.max(take, notTake)) {
              K[i][weight] = JSON.parse(
                JSON.stringify(
                  K[i - 1][weight - Number(this.items[i - 1].weight)]
                )
              );
              K[i][weight].items.push(this.items[i - 1]);
              K[i][weight].total = take;
            } else {
              K[i][weight] = JSON.parse(JSON.stringify(K[i - 1][weight]));
              K[i][weight].total = notTake;
            }
          } else {
            K[i][weight] = JSON.parse(JSON.stringify(K[i - 1][weight]));
          }
        }
      }
      let M = K;
      M = M.map(i => i.map(j => j.total));
      // eslint-disable-next-line no-console
      console.log(M);
      return {
        items: K[N][W].items,
        total: K[N][W].total,
        matrix: M
      };
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
