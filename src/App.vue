<template>
  <h1>CRYPTO</h1>
  <Input
    :changeAmount="changeAmount"
    :convert="convert"
    :favourite="favourite"
  />
  <p v-if="error != ''">{{ error }}</p>
  <p v-if="result != 0" class="result-text">{{ result }}</p>
  <Favourite :favs="favs" v-if="favs.length > 0" :getFromFavs="getFromFavs" />
  <div class="selectors">
    <Selector :setCrypto="setCryptoFirst" :cryptoNow="cryptoFirst" />
    <Selector :setCrypto="setCryptoSecond" :cryptoNow="cryptoSecend" />
  </div>
</template>

<script>
import Input from "../src/components/input.vue";
import Selector from "../src/components/selector.vue";
import Favourite from "../src/components/favourite.vue";
import CryptoConvert from "crypto-convert";

const convert = new CryptoConvert();

export default {
  components: { Input, Selector, Favourite },
  data() {
    return {
      amount: 0,
      cryptoFirst: "",
      cryptoSecend: "",
      error: "",
      result: 0,
      favs: [],
    };
  },
  methods: {
    favourite() {
      this.favs.push({ from: this.cryptoFirst, to: this.cryptoSecend });
    },
    getFromFavs(index) {
      this.cryptoFirst = this.favs[index].from;
      this.cryptoSecend = this.favs[index].to;
    },
    changeAmount(val) {
      this.amount = val;
    },
    setCryptoFirst(val) {
      this.cryptoFirst = val;
    },
    setCryptoSecond(val) {
      this.cryptoSecend = val;
    },
    async convert() {
      if (this.amount <= 0) {
        this.error = "Введите число больше нуля";
        return;
      } else if (this.cryptoFirst == "" || this.cryptoSecend == "") {
        this.error = "Выберите валюту";
        return;
      } else if (this.cryptoFirst == this.cryptoSecend) {
        this.error = "Выберите другую валюту";
        return;
      }
      this.error = "";

      await convert.ready();
      if (this.cryptoFirst == "BTC" && this.cryptoSecend == "ETH") {
        this.result = convert.BTC.ETH(this.amount);
      } else if (this.cryptoFirst == "BTC" && this.cryptoSecend == "USDT") {
        this.result = convert.BTC.USDT(this.amount);
      } else if (this.cryptoFirst == "ETH" && this.cryptoSecend == "BTC") {
        this.result = convert.ETH.BTC(this.amount);
      } else if (this.cryptoFirst == "ETH" && this.cryptoSecend == "USDT") {
        this.result = convert.ETH.USDT(this.amount);
      } else if (this.cryptoFirst == "USDT" && this.cryptoSecend == "BTC") {
        this.result = convert.USDT.BTC(this.amount);
      } else if (this.cryptoFirst == "USDT" && this.cryptoSecend == "ETH") {
        this.result = convert.USDT.ETH(this.amount);
      }
    },
  },
};
</script>

<style scoped>
.selectors {
  display: flex;
  justify-content: space-around;
  width: 700px;
  margin: 0 auto;
}
.result-text {
  font-family: "Nabla", cursive;
  font-size: 2em;
}
</style>
