<template>
  <div class="app" id="app">
    <h1 class="app-title">KANPSACK</h1>
    
    <div class="app-matrix">
      <table class="app-matrix__table">
        <tr class="table-header">
          <th class="table__cell table__cell--hidden"></th>
          <th
            class="table__cell table__cell--highlighted"
            v-for="w in knapsack.matrix[0].length"
            :key="w"
          >{{w}}</th>
        </tr>
        <tr
          class="table-content"
          v-for="(row, index) in knapsack.matrix"
          :key="row"
        >
          <td class="table__cell table__cell--highlighted"><b>{{index}}</b></td>
          <td
            class="table__cell"
            v-for="i in row"
            :key="i"
          >{{i}}</td>
        </tr>
      </table>
    </div>

    <div class="app-weight">
      <label for="max-weight" class="app-weight__label">PESO MÁXIMO: </label>
      <input
        class="app-weight__input"
        type="number"
        id="max-weight"
        v-model="maxWeight"
      >
    </div>

    <div class="app-data">
        <form class="app-data__form" @submit.prevent="addItem">
        <label for="name" class="form__label">ITEM:</label>
        <input
          class="form__input"
          type="text"
          id="name"
          v-model="item.name"
        >
        <label for="weight" class="form__label">PESO:</label>
        <input
          class="form__input"
          type="number"
          id="weight"
          v-model="item.weight"
        >
        <label for="value" class="form__label">VALOR:</label>
        <input
          class="form__input"
          type="number"
          id="value"
          v-model="item.value"
        >
        <input
          class="form__submit"
          type="submit"
          value="ADICIONAR"
        >
      </form>
      <table class="app-data__table">
        <tr>
          <th class="data-table__cell">ITEM</th>
          <th class="data-table__cell">PESO</th>
          <th class="data-table__cell">VALOR</th>
          <th class="data-table__cell"></th>
        </tr>
        <tr 
          v-for="i in items"
          :key="i.id"
        >
          <td class="data-table__cell">{{i.name}}</td>
          <td class="data-table__cell">{{i.weight}}</td>
          <td class="data-table__cell">{{i.value}}</td>
          <td class="data-table__cell"><button class="data-table__button" @click="removeItem(i)">X</button></td>
        </tr>
      </table>
      <div class="data-result">
        <h2 class="data-result__title">LEVAR</h2>
        <ul class="data-result__list">
          <li
            class="data-result__item"
            v-for="item in knapsack.items"
            :key="item"
          >
            item: {{item.name}}, peso: {{item.weight}}, valor: {{item.value}}
          </li>
        </ul>
        <p class="data-result__total">VALOR TOTAL: {{knapsack.total}}</p>
      </div>
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

<style lang="css">
  @import 'app.css';
</style>
