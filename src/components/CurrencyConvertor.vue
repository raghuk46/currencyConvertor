<template>
  <div class="calculator">
    <form @submit="sub" class="form-inline">
      <div class="input">
        <label style="color: white; margin-right: 10px">Amount</label>
        <input
          name="amount"
          v-model="amount"
          type="number"
          class="form-input"
          placeholder="1"
        />
      </div>
      <div class="input">
        <label style="color: white; margin-right: 5px">From</label>
        <select name="from" v-model="from" class="form-input">
          <option
            v-for="option in options"
            :key="option.key"
            :value="option.value"
            :disabled="to === option.value"
            >{{ option.text }}</option
          >
        </select>
      </div>
      <div class="input">
        <span
          style="color: white; text-align: ceneter; background-color: transparent; font-size: 22px"
          v-on:click="toggleSwitch"
        >
          <i class="fa fa-exchange-alt"></i>
        </span>
      </div>
      <div class="input">
        <label style="color: white; margin-right: 5px">To</label>
        <select name="to" v-model="to" class="form-input">
          <option
            v-for="option in options"
            :key="option.key"
            :value="option.value"
            :disabled="from === option.value"
            >{{ option.text }}</option
          >
        </select>
      </div>
      <div class="input">
        <button class="form-input-button" type="submit">
          <span style="text-align: center; font-size: 20px;"
            ><i class="fa fa-greater-than"></i
          ></span>
        </button>
      </div>
    </form>
    <div class="result" v-if="result > 0">
      <div style="color: white;">{{ amount }} {{ from }} =</div>
      <div style="color: white; font-size: 48px; font-weight: '800'">
        {{ this.result }} {{ to }}
      </div>
      <div style="color: white;">
        <span>1 {{ from }} = {{ this.fromConversion }} {{ to }}</span>
        <br />
        <span>1 {{ to }} = {{ this.toConversion }} {{ from }}</span>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "CurrencyConvertor",
  data: function() {
    return {
      amount: 1,
      from: "USD",
      to: "MYR",
      fromConversion: 0,
      toConversion: 0,
      result: 0,
      tmp: null,
      options: [
        { key: "1", text: "USD", value: "USD" },
        { key: "2", text: "MYR", value: "MYR" },
        { key: "3", text: "INR", value: "INR" },
        { key: "4", text: "EUR", value: "EUR" },
        { key: "5", text: "EUR", value: "EUR" },
        { key: "6", text: "AUD", value: "AUD" },
        { key: "7", text: "CAD", value: "CAD" },
        { key: "8", text: "SGD", value: "SGD" },
        { key: "9", text: "GBP", value: "GBP" },
      ],
    };
  },
  props: {
    msg: String,
  },
  methods: {
    toggleSwitch: function() {
      this.tmp = this.from;
      this.from = this.to;
      this.to = this.tmp;
      this.result = 0;
    },
    sub: function(event) {
      event.preventDefault();
      const query = `${this.from}_${this.to}`;
      const queryRev = `${this.to}_${this.from}`;
      axios
        .get(
          `https://free.currconv.com/api/v7/convert?q=${query},${queryRev}&compact=ultra&apiKey=29af044c74833b2e2b9f`
        )
        .then((response) => {
          console.log(response);
          if (response.data) {
            this.result = parseFloat(
              response.data[query] * this.amount
            ).toFixed(4);
            this.fromConversion = response.data[query];
            this.toConversion = response.data[queryRev];
          }
        })
        .catch((err) => console.log(err));
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.calculator {
  background-color: darkolivegreen;
  margin: 25px;
  border-radius: 15px;
}
.form-inline {
  display: flex;
  flex-flow: row wrap;
  align-items: center;
}
.input {
  padding: 15px;
  align-self: start;
}
.form-input {
  width: 150px;
  height: 35px;
  border-radius: 15px;
}
.form-input-button {
  width: 50px;
  height: 35px;
  border-radius: 15px;
}
</style>
