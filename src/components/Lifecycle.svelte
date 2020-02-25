<script>
  import { onMount } from "svelte";
  import Loading from "./Loading.svelte";
  const API_URL = "https://api.exchangerate-api.com/v4/latest/";

  const fetchData = async currency => {
    const response = await fetch(API_URL + currency, { method: "GET" });
    if (!response.ok) throw new Error(response.status);
    return await response.json();
  };

  const calculate = async () => {
    const data = await fetchData(currencyOne);
    rate = data.rates[currencyTwo];
    amountTwo = (rate * amountOne).toFixed(2);
  };

  const swap = async () => {
    [currencyOne, currencyTwo] = [currencyTwo, currencyOne];
    await calculate();
  };

  let isLoading = false;
  let currencies = [];
  let error = null;
  let currencyOne = null;
  let currencyTwo = null;
  let amountOne = 1;
  let amountTwo = 1;
  let rate = 1;

  onMount(async () => {
    try {
      isLoading = true;
      const data = await fetchData("USD");
      currencies = Object.keys(data.rates);
      currencies.sort((a, b) => (a < b ? -1 : 1));
      currencyOne = currencies[0];
      currencyTwo = currencies[0];
      isLoading = false;
    } catch (err) {
      error = err.message;
      isLoading = false;
    }
  });
</script>

<style>
  .currency {
    display: grid;
    grid-template-columns: 1fr auto;
    grid-gap: 10px;
    text-align: right;
  }
  .swap-rate-container {
    display: grid;
    grid-template-columns: 1fr;
    align-items: center;
    grid-gap: 5px;
    padding-bottom: 5px;
  }
  .rate {
    color: blue;
    text-align: right;
    font-size: 0.85rem;
    font-weight: bold;
    font-family: "Consolas", cursive;
  }
  #amount-two {
    background-color: #f5f5f5;
    color: #303030;
  }
  button {
    cursor: pointer;
    user-select: none;
    font: inherit;
    background-color: #cf0056;
    color: #f5f5f5;
    border: 1px solid #cf0056;
    border-radius: 5px;
    box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.26);
    padding: 0.5rem 1rem;
  }
  button:focus {
    outline: none;
  }
  button:hover,
  button:active {
    background: #e40763;
    border-color: #e40763;
    box-shadow: 1px 1px 8px rgba(77, 51, 51, 0.26);
  }
  button:disabled,
  button:disabled:hover,
  button:disabled:active {
    background: #ccc;
    border-color: #ccc;
    color: #959595;
    box-shadow: none;
    cursor: not-allowed;
  }
  .fetch-error {
    color: #9b0f0f;
    text-align: center;
  }
</style>

<div class="container">
  {#if isLoading}
    <p>Loading&hellip;</p>
    <Loading />
  {:else if !error}
    <div class="currency">
      <select id="currency-one" bind:value={currencyOne} on:change={calculate}>
        {#each currencies as currency, index (index)}
          <option>{currency.toUpperCase()}</option>
        {/each}
      </select>
      <input
        type="number"
        id="amount-one"
        min="1"
        bind:value={amountOne}
        on:change={calculate} />
    </div>
    <div class="swap-rate-container">
      <button on:click={swap}>Swap</button>
      <div class="rate" id="rate">1 {currencyOne} = {rate} {currencyTwo}</div>
    </div>
    <div class="currency">
      <select id="currency-two" bind:value={currencyTwo} on:change={calculate}>
        {#each currencies as currency, index (index)}
          <option>{currency.toUpperCase()}</option>
        {/each}
      </select>
      <input
        type="number"
        id="amount-two"
        min="1"
        bind:value={amountTwo}
        disabled />
    </div>
  {:else if error}
    <p class="fetch-error">Request error ({error})</p>
  {/if}
</div>
