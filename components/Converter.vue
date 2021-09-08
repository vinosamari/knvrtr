<template>
  <section class="mainWrapper">
    <h1 class="header">KNVRT</h1>
    <h2 class="subheader">Up-to-date Currency Converter</h2>
    <div class="fromCurrency">
      <select v-model="convertFromCurrency" @change="convertCurrency">
        <option
          v-for="currSymbol in this.currSymbols"
          :value="currSymbol.code"
          :key="currSymbol.code"
        >
          {{ currSymbol.description }}
        </option>
      </select>
      <input
        type="number"
        v-model="convertFromAmount"
        placeholder="0"
        @keyup="convertCurrency"
      />
    </div>
    <div class="toCurrency">
      <select v-model="convertToCurrency" @change="convertCurrency">
        <option
          v-for="currSymbol in this.currSymbols"
          :value="currSymbol.code"
          :key="currSymbol.code"
        >
          {{ currSymbol.description }}
        </option>
      </select>
      <div class="loadingSpinner" v-if="loading">
        <div class="innerSpinner"></div>
      </div>

      <input
        v-else
        type="number"
        v-model="convertToAmount"
        placeholder="0"
        disabled
      />
    </div>
  </section>
</template>

<script>
export default {
  data() {
    return {
      loading: false,
      conversionResult: "",
      convertFromCurrency: "",
      convertToCurrency: "",
      convertFromAmount: "",
      convertToAmount: "",
      currSymbols: [
        {
          code: 1,
          description: "USD",
        },
        {
          code: 2,
          description: "NGN",
        },
        {
          code: 3,
          description: "EUR",
        },
      ],
    };
  },
  methods: {
    // CONVERSION LOGIC
    async convertCurrency(fromCurr, toCurr, fromAmt, toAmt) {
      this.loading = true;
      fromCurr = this.convertFromCurrency;
      toCurr = this.convertToCurrency;
      fromAmt = this.convertFromAmount;
      let convertURL = `https://api.exchangerate.host/convert?from=${fromCurr}&to=${toCurr}`;
      // MAKE API CALL
      await this.$axios
        .$get(convertURL, { params: { amount: fromAmt, places: 2 } })
        .then((res) => {
          this.loading = false;
          this.toAmt = res.result;
          this.convertToAmount = res.result;
          this.conversionResult = res.result;
        });
    },
  },
  computed: {},
  mounted() {
    let symbolsURL = "https://api.exchangerate.host/symbols";
    let symbols;
    this.convertFromCurrency = "USD";
    this.convertToCurrency = "NGN";
    this.convertFromAmount = "";
    this.convertToAmount = "";
    symbols = this.$axios.$get(symbolsURL).then((res) => {
      this.currSymbols = res.symbols;
    });
  },
};
</script>

<style scoped>
.mainWrapper {
  @apply flex flex-col items-center justify-center text-center  bg-gradient-to-bl from-purple-600 via-blue-800 to-green-800 min-h-screen;
}
.header {
  @apply text-5xl font-black tracking-widest font-serif mb-4 first-letter:text-6xl text-white;
}
.subheader {
  @apply text-lg px-10 mb-14 uppercase font-bold font-mono tracking-widest text-yellow-50;
}
label {
  @apply font-black mb-4 tracking-widest uppercase mr-4;
}
input {
  @apply mb-10 text-center border-0 rounded-xl bg-white text-black font-bold font-sans  py-2 text-2xl disabled:bg-gray-300 placeholder-black;
}
select {
  @apply mb-8 text-black border-none rounded-xl font-bold text-sm uppercase px-4 py-2 w-auto;
}

.convertedResult {
  @apply text-white font-sans tracking-widest;
}
.amountSpan {
  @apply font-bold text-2xl border-2 px-3 py-1 mr-1;
}
.loadingSpinner {
  @apply rounded-full my-4 relative mx-auto;
}
.innerSpinner {
  @apply p-4 border-4 bg-gradient-to-bl from-blue-900 via-purple-700 to-blue-500 border-white rounded-full absolute inset-0 w-16 h-16   mx-auto animate-spin;
}
</style>