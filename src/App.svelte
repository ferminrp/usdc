<script>
  import { onMount } from 'svelte';
  
  const BLACKLISTED_EXCHANGES = ["latamex", "bitsoalpha", "cryptomkt", "ripioexchange","vibrant","kriptonmarket"];
  let exchangeData = {};
  const EXCHANGE_INFO = {
  "belo": {
    "url": "https://belo.app?ref=usdc.ar",
    "logo": "https://pbs.twimg.com/profile_images/1608876734249582593/jS7hVHZM_400x400.jpg"
  },
  "lemoncash": {
    "url": "https://lemon.me?ref=usdc.ar",
    "logo": "https://pbs.twimg.com/profile_images/1529811619408117760/_gKE_CQT_400x400.jpg"
  },
  "letsbit": {
    "url": "https://letsbit.io/?ref=usdc.ar",
    "logo": "https://pbs.twimg.com/profile_images/1589990872611000321/imavxY1f_400x400.jpg"
  },
  "buenbit":{
    "url":"https://buenbit.com?ref=usdc.ar",
    "logo":"https://pbs.twimg.com/profile_images/1681298472782426113/nDMEc3-Y_400x400.png"
  },
  "ripio":{
    "url":"https://ripio.com?ref=usdc.ar",
    "logo":"https://pbs.twimg.com/profile_images/1668306347895472135/BzpLV7F7_400x400.jpg"
  },
  "fiwind":{
    "url":"https://fiwind.io?ref=usdc.ar",
    "logo":"https://pbs.twimg.com/profile_images/1638613392653951029/gKj3U76V_400x400.jpg"
  },
  "bybit":{
    "url":"https://bybit.com?ref=usdc.ar",
    "logo":"https://pbs.twimg.com/profile_images/1611306382845964289/yphzW-GB_400x400.jpg"
  },
  "tiendacrypto":{
    "url":"https://tiendacrypto.com?ref=usdc.ar",
    "logo":"https://pbs.twimg.com/profile_images/1503863542214270984/mEGN7laX_400x400.png"
  },
  "satoshitango":{
    "url":"https://satoshitango.com?ref=usdc.ar",
    "logo":"https://pbs.twimg.com/profile_images/1616138860009865217/l0dXf1pF_400x400.jpg"
  },
};

  let defaultUrl = '#'; // Default URL if not found in EXCHANGE_INFO
  let defaultLogo = ''; // Default logo URL if not found in EXCHANGE_INFO

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
      Mejor para comprar: {bestExchangeToBuy}
    </div>
    <div class="best-exchange">
      <div class="inline-container">
        <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="#FFFFFF" viewBox="0 0 256 256"><path d="M200,64V168a8,8,0,0,1-16,0V83.31L69.66,197.66a8,8,0,0,1-11.32-11.32L172.69,72H88a8,8,0,0,1,0-16H192A8,8,0,0,1,200,64Z"></path></svg>        <p class="best-price">$ {filteredExchangeData[bestExchangeToSell]?.bid || 'N/A'}</p>
      </div>
      Mejor para vender: {bestExchangeToSell}
    </div>
    <div class="best-exchange">
      <div class="inline-container">
        <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="#FFFFFF" viewBox="0 0 256 256"><path d="M128,24A104,104,0,1,0,232,128,104.11,104.11,0,0,0,128,24Zm0,192a88,88,0,1,1,88-88A88.1,88.1,0,0,1,128,216ZM80,108a12,12,0,1,1,12,12A12,12,0,0,1,80,108Zm104,0a8,8,0,0,1-8,8H152a8,8,0,0,1,0-16h24A8,8,0,0,1,184,108Zm-9.08,48c-10.29,17.79-27.39,28-46.92,28s-36.63-10.2-46.93-28a8,8,0,1,1,13.86-8c7.46,12.91,19.2,20,33.07,20s25.61-7.1,33.08-20a8,8,0,1,1,13.84,8Z"></path></svg>
        <p class="best-price">$ {bestSpreadValue !== undefined ? bestSpreadValue.toFixed(2) : 'N/A'}</p>
      </div>
      Menor spread: {bestSpreadExchange}
    </div>
  </div>
  <div class="container">
  {#each Object.keys(filteredExchangeData) as exchange}
  <div class="card" class:best-card={exchange === bestExchangeToBuy || exchange === bestExchangeToSell || exchange === bestSpreadExchange}>
    <a target="_blank" href={EXCHANGE_INFO[exchange].url}>
      <img class="logo" src={EXCHANGE_INFO[exchange].logo} alt="{exchange} Logo">
      <div class="exchange-name">{exchange}</div>
      
      <div class="price">Precio para comprar: ${filteredExchangeData[exchange].ask}</div>
      <div class="price">Precio para vender: ${filteredExchangeData[exchange].bid}</div>
      <div class="price">
        Spread: ${ (filteredExchangeData[exchange].ask - filteredExchangeData[exchange].bid).toFixed(2) }
      </div>
    </a>
  </div>
{/each}
</div>

<a id="cafecito" href='https://cafecito.app/ferminrp' rel='noopener' target='_blank'><img srcset='https://cdn.cafecito.app/imgs/buttons/button_3.png 1x, https://cdn.cafecito.app/imgs/buttons/button_3_2x.png 2x, https://cdn.cafecito.app/imgs/buttons/button_3_3.75x.png 3.75x' src='https://cdn.cafecito.app/imgs/buttons/button_3.png' alt='Invitame un café en cafecito.app' /></a>
<div style="margin-top: 20px;">
  <small>Última actualización: {new Date().toLocaleTimeString()}</small><br>
  <small>Data provista por el api de criptoya.com</small><br>
  <small>Sugerencias a <a href="https://x.com/ferminrp">X.com/ferminrp</a></small>
</div>
</section>

