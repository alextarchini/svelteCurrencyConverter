<script>
  import Lifecycle from "./components/Lifecycle.svelte";
  const title = "Currency converter";
</script>

<style>
  .exchange-apps {
    display: grid;
    place-items: center;
  }
  .money-img {
    display: block;
    max-width: 100%;
    width: 75px;
  }
</style>

<h1>{title}</h1>
<div class="exchange-apps">
  <img
    src="https://s3.amazonaws.com/xeblog-prd/2018/12/logo.svg"
    alt="Money image for exchange apps."
    class="money-img" />
  <p>Choose the currency and the amounts to get the exchange rate</p>
  <Lifecycle />
</div>
