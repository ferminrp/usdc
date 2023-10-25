<script>
  import { onMount } from 'svelte';
  
  const BLACKLISTED_EXCHANGES = ["latamex", "bitsoalpha", "cryptomkt", "ripioexchange","vibrant","kriptonmarket"];
  let exchangeData = {};

  onMount(async () => {
    const res = await fetch("https://criptoya.com/api/usdc/ars/100");
    exchangeData = await res.json();
  });

  $: filteredExchangeData = filterBlacklistedExchanges(exchangeData);
  $: bestExchangeToBuy = findBestExchange(filteredExchangeData, "ask");
  $: bestExchangeToSell = findBestExchange(filteredExchangeData, "bid");
  $: bestSpreadExchange = findLowestSpreadExchange(filteredExchangeData);
  $: bestSpreadValue = filteredExchangeData[bestSpreadExchange]?.ask - filteredExchangeData[bestSpreadExchange]?.bid;

  function filterBlacklistedExchanges(exchangeData) {
    return Object.fromEntries(
      Object.entries(exchangeData).filter(
        ([exchange]) => !BLACKLISTED_EXCHANGES.includes(exchange.toLowerCase())
      )
    );
  }
  
  function findBestExchange(exchangeData, action = "ask") {
    let bestExchange = "";
    let bestRate = action === "ask" ? Infinity : 0;

    for (const [exchange, rates] of Object.entries(exchangeData)) {
      if (action === "ask" && rates.ask < bestRate) {
        bestRate = rates.ask;
        bestExchange = exchange;
      } else if (action === "bid" && rates.bid > bestRate) {
        bestRate = rates.bid;
        bestExchange = exchange;
      }
    }

    return bestExchange;
  }

  function findLowestSpreadExchange(exchangeData) {
    let bestSpreadExchange = "";
    let bestSpread = Infinity;

    for (const [exchange, rates] of Object.entries(exchangeData)) {
      const spread = rates.ask - rates.bid;
      if (spread < bestSpread) {
        bestSpread = spread;
        bestSpreadExchange = exchange;
      }
    }

    return bestSpreadExchange;
  }
</script>

<section>
  <h1>Cotizaciones de USDC en Exchanges</h1>
  <div class="best">
    <div class="best-exchange">
      <div class="inline-container">
        <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="#FFFFFF" viewBox="0 0 256 256"><path d="M200,88V192a8,8,0,0,1-8,8H88a8,8,0,0,1,0-16h84.69L58.34,69.66A8,8,0,0,1,69.66,58.34L184,172.69V88a8,8,0,0,1,16,0Z"></path></svg>
        <p class="best-price">$ {filteredExchangeData[bestExchangeToBuy]?.ask || 'N/A'}</p>
      </div>
      <p>Mejor para comprar: {bestExchangeToBuy}</p>
    </div>
    <div class="best-exchange">
      <div class="inline-container">
        <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="#FFFFFF" viewBox="0 0 256 256"><path d="M200,64V168a8,8,0,0,1-16,0V83.31L69.66,197.66a8,8,0,0,1-11.32-11.32L172.69,72H88a8,8,0,0,1,0-16H192A8,8,0,0,1,200,64Z"></path></svg>        <p class="best-price">$ {filteredExchangeData[bestExchangeToSell]?.bid || 'N/A'}</p>
      </div>
      <p>Mejor para vender: {bestExchangeToSell}</p>
    </div>
    <div class="best-exchange">
      <div class="inline-container">
        <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="#FFFFFF" viewBox="0 0 256 256"><path d="M128,24A104,104,0,1,0,232,128,104.11,104.11,0,0,0,128,24Zm0,192a88,88,0,1,1,88-88A88.1,88.1,0,0,1,128,216ZM80,108a12,12,0,1,1,12,12A12,12,0,0,1,80,108Zm104,0a8,8,0,0,1-8,8H152a8,8,0,0,1,0-16h24A8,8,0,0,1,184,108Zm-9.08,48c-10.29,17.79-27.39,28-46.92,28s-36.63-10.2-46.93-28a8,8,0,1,1,13.86-8c7.46,12.91,19.2,20,33.07,20s25.61-7.1,33.08-20a8,8,0,1,1,13.84,8Z"></path></svg>
        <p class="best-price">$ {bestSpreadValue !== undefined ? bestSpreadValue.toFixed(2) : 'N/A'}</p>
      </div>
      <p>Menor spread: {bestSpreadExchange}</p>
    </div>
  </div>
</section>


<style>
  section {
    font-family: 'Manrope', sans-serif;
    margin: 20px;
    background: #F5F6F8;
    color: #4C505B;
  }
  h1 {
    font-weight: 800;
  }
  .container {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); /* increased from 200px to 250px */
    gap: 16px;
  }
  .card {
    padding: 20px;
    background: #fff;
    border: 1px solid #E7E8EA;
    border-radius: 8px;
  }
  .best {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 16px;
    margin-bottom: 20px;
  }
  .best-card {
    border-color: #683FFD;
  }
  a {
    text-decoration: none;
  }
  a .exchange-name {
    font-weight: bold;
    margin-bottom: 12px;
    color: #4C505B;
    text-decoration: none;
    font-size: 1.2em;
  }
  a .price {
    color: #4C505B;
    text-decoration: none;
  }
  .best-exchange {
    font-weight: bold;
    background-color: #E7E8EA;
    padding: 12px;
    border-radius: 8px;
  }
  .logo {
    margin-bottom: 12px;
  }
  svg {
    background-color: #683FFD;
    border-radius: 4px;
    padding: 1px;
    margin-bottom: 6px;
    margin-right: 18px;
  }
  .best-price {
    color: #683FFD;
    font-size: 1.2em;
    font-weight: bold;
  }
  .inline-container {
    display: flex;
    align-items: center;
  }
  #cafecito {
    display:block;
    margin-top: 20px;
  }
</style>
