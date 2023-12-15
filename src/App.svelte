<script>
  import { onMount } from 'svelte';
  import Banner from './components/Banner.svelte';
  import BestExchange from './components/BestExchange.svelte';

  
  let exchangeData = {};

  const EXCHANGE_INFO = {
    belo: {
      url: "https://belo.app?ref=usdc.ar",
      logo: "https://ik.imagekit.io/ferminrp/jS7hVHZM_400x400.jpg?updatedAt=1702030494776&tr=w-60,h-60,f=webp",
    },
    lemoncash: {
      url: "https://lemon.me?ref=usdc.ar",
      logo: "https://ik.imagekit.io/ferminrp/_gKE_CQT_400x400.jpg?updatedAt=1702030494417&tr=w-60,h-60,f=webp",
    },
    letsbit: {
      url: "https://letsbit.io/?ref=usdc.ar",
      logo: "https://ik.imagekit.io/ferminrp/imavxY1f_400x400.jpg?updatedAt=1702030494534&tr=w-60,h-60,f=webp",
    },
    buenbit: {
      url: "https://buenbit.com?ref=usdc.ar",
      logo: "https://ik.imagekit.io/ferminrp/nDMEc3-Y_400x400.png?updatedAt=1702030494734&tr=w-60,h-60,f=webp",
    },
    ripio: {
      url: "https://ripio.com?ref=usdc.ar",
      logo: "https://ik.imagekit.io/ferminrp/BzpLV7F7_400x400.jpg?updatedAt=1702030970688&tr=w-60,h-60,f=webp",
    },
    fiwind: {
      url: "https://fiwind.io?ref=usdc.ar",
      logo: "https://ik.imagekit.io/ferminrp/gKj3U76V_400x400.jpg?updatedAt=1702030494696&tr=w-60,h-60,f=webp",
    },
    tiendacrypto: {
      url: "https://tiendacrypto.com?ref=usdc.ar",
      logo: "https://ik.imagekit.io/ferminrp/mEGN7laX_400x400.png?updatedAt=1702030494717&tr=w-60,h-60,f=webp",
    },
    satoshitango: {
      url: "https://satoshitango.com?ref=usdc.ar",
      logo: "https://ik.imagekit.io/ferminrp/l0dXf1pF_400x400.jpg?updatedAt=1702030494516&tr=w-60,h-60,f=webp",
    },
};


onMount(async () => {
  try {
    const res = await fetch("https://criptoya.com/api/usdc/ars/100");
    exchangeData = await res.json();
  } catch (error) {
    console.error("Error fetching exchange data:", error);
    // Manejar el error adecuadamente
  }
});


  $: filteredExchangeData = filterExchanges(exchangeData);
  $: bestExchangeToBuy = findBestExchange(filteredExchangeData, "ask");
  $: bestExchangeToSell = findBestExchange(filteredExchangeData, "bid");
  $: bestSpreadExchange = findBestExchange(filteredExchangeData, "spread");
  $: bestSpreadValue = filteredExchangeData[bestSpreadExchange]?.totalAsk - filteredExchangeData[bestSpreadExchange]?.totalBid;

  function filterExchanges(exchangeData) {
  const exchangeInfoKeys = Object.keys(EXCHANGE_INFO).map(key => key.toLowerCase());
  return Object.fromEntries(
    Object.entries(exchangeData).filter(
      ([exchange]) => exchangeInfoKeys.includes(exchange.toLowerCase())
    )
  );
}
  
function findBestExchange(exchangeData, action = "ask") {
  let bestExchange = "";
  let bestRate;

  if (action === "ask") {
    bestRate = Infinity;
  } else if (action === "bid") {
    bestRate = 0;
  } else if (action === "spread") {
    bestRate = Infinity;
  }

  for (const [exchange, rates] of Object.entries(exchangeData)) {
    if (action === "ask" && rates.totalAsk < bestRate) {
      bestRate = rates.totalAsk;
      bestExchange = exchange;
    } else if (action === "bid" && rates.totalBid > bestRate) {
      bestRate = rates.totalBid;
      bestExchange = exchange;
    } else if (action === "spread") {
      const spread = rates.totalAsk - rates.totalBid;
      if (spread < bestRate) {
        bestRate = spread;
        bestExchange = exchange;
      }
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
  
  {#if Object.keys(exchangeData).length > 0}
  <div class="best">
    <BestExchange 
      price={filteredExchangeData[bestExchangeToBuy]?.ask}
      label="Mejor para comprar"
      value={bestExchangeToBuy}
      url={EXCHANGE_INFO[bestExchangeToBuy]?.url}
      logo={EXCHANGE_INFO[bestExchangeToBuy]?.logo}
    />
    <BestExchange 
      price={filteredExchangeData[bestExchangeToSell]?.bid}
      label="Mejor para vender"
      value={bestExchangeToSell}
      url={EXCHANGE_INFO[bestExchangeToSell]?.url}
      logo={EXCHANGE_INFO[bestExchangeToSell]?.logo}
    />
    <BestExchange 
      price={bestSpreadValue !== undefined ? bestSpreadValue.toFixed(2) : 'N/A'}
      label="Menor spread"
      value={bestSpreadExchange}
      url={EXCHANGE_INFO[bestSpreadExchange]?.url}
      logo={EXCHANGE_INFO[bestSpreadExchange]?.logo}
    />
  </div>
  
  
  <div class="container">
  {#each Object.keys(filteredExchangeData) as exchange}
  <div class="card" class:best-card={exchange === bestExchangeToBuy || exchange === bestExchangeToSell || exchange === bestSpreadExchange}>
    <a target="_blank" href={EXCHANGE_INFO[exchange].url} style="display: block;">
      <img class="logo" src={EXCHANGE_INFO[exchange].logo} alt="{exchange} Logo" style="border-radius: 50%; width: 40px; height: 40px;">
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
{/if}

<a id="cafecito" href='https://cafecito.app/ferminrp' rel='noopener' target='_blank'><img srcset='https://cdn.cafecito.app/imgs/buttons/button_3.png 1x, https://cdn.cafecito.app/imgs/buttons/button_3_2x.png 2x, https://cdn.cafecito.app/imgs/buttons/button_3_3.75x.png 3.75x' src='https://cdn.cafecito.app/imgs/buttons/button_3.png' alt='Invitame un café en cafecito.app' /></a>
<div style="margin-top: 20px;">
  <small>Última actualización: {new Date().toLocaleTimeString()}</small><br>
  <small>Data provista por el api de criptoya.com</small><br>
  <small>Sugerencias a <a href="https://x.com/ferminrp">X.com/ferminrp</a></small>
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
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
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
