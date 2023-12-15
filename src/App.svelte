<script>
  import { onMount } from 'svelte';
  import Banner from './components/Banner.svelte';
  import BestExchange from './components/BestExchange.svelte';
  import Heading from './components/Heading.svelte';

  
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
  <Heading />
  
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
  .container {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); /* increased from 200px to 250px */
    gap: 16px;
  }
  .card {
    padding: 20px;
    background: var(--color-gray-three);
    border: 1px solid var(--color-gray-two);
    border-radius: 8px;
    position: relative;
  }
  .best {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 16px;
    margin-bottom: 20px;
  }
  .best-card {
    border-color: var(--accent-color);
  }
  a .exchange-name {
    font-weight: bold;
    margin-bottom: 12px;
    margin-top: 12px;
    text-decoration: none;
    font-size: 1.2em;
  }
  .best-exchange {
    font-weight: bold;
    background-color: var(--color-gray-two);
    padding: 12px;
    border-radius: 8px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .best-exchange p {
    margin: 0px;
  }
  .logo {
    //margin-bottom: 12px;
  }
  svg {
    background-color: var(--accent-color);
    border-radius: 4px;
    padding: 1px;
    margin-bottom: 6px;
    margin-right: 18px;
  }
  .svg-path {
    color: var(--background-color);
  }
  .best-price {
    color: var(--accent-color);
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
  .fixed-banner {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    background-color: black;
    color: white;
    text-align: center;
    z-index: 999;
    font-size: 1rem;
    padding: 0.5rem;
    color: white;
    text-decoration: none;
  }
  .price {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .label {
    color: var(--primary-color);
    font-weight: 500!important;
  }
  .numeric-value {
    color: var(--color-gray-one);
    font-variant-numeric: tabular-nums;
  }
  .alert {
    color: var(--color-alert);
    font-weight: bold;
  }
  .warning {
    color: var(--color-warning);
    font-weight: bold;
  }
.h1-container {
    display: flex;
    align-items: center;
    //justify-content: center;
    font-family: 'Arial', sans-serif; /* Replace with your desired font */
}

/* Style the select dropdown */
.h1-container select {
    background-color: var(--accent-with-less-opacity);
    color: var(--accent-color);
    border: none;
    border-radius: 5px;
    padding: 10px 20px;
    cursor: pointer;
    font-weight: 800;
    font-size: 0.8em;
    

    appearance: none; /* This hides the default dropdown arrow in some browsers */
    -webkit-appearance: none; /* This hides the default dropdown arrow in Webkit browsers */
    -moz-appearance: none; /* This hides the default dropdown arrow in Firefox */
    padding: 4px 10px;
    margin: 0 4px;
    cursor: pointer;
    border-radius: 4px;

    /* Add custom arrow */
    background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="%237C4DFF" viewBox="0 0 256 256"><path d="M216.49,104.49l-80,80a12,12,0,0,1-17,0l-80-80a12,12,0,0,1,17-17L128,159l71.51-71.52a12,12,0,0,1,17,17Z"></path></svg>');
    background-repeat: no-repeat;
    background-position: right 6px center;
    padding-right:45px; /* Space for the arrow */
}

/* Just in case, a hover effect */
.h1-container select:hover {
    background-color: #ae96f2; /* A slightly darker shade for hover */
}

option {
  padding: 4px 10px;
  background-color: #f5f5f5;
  cursor: pointer;
}
a,
a:visited,
a:hover,
a:active {
  color: inherit; /* Inherit the color from the parent element */
  text-decoration: none; /* Removes underline */
}

.badge {
  color: var(--accent-color);
  position: absolute;
  top: 20px;
  right: 20px;
  max-width: 50%;
  text-align: right;
}

hr {
  border: none;
  height: 1px; 
  background-color: var(--color-gray-two)!important;
  margin-top: 12px;
  margin-bottom: 12px;
}
.spread-percentage {
  color: var(--color-gray-one);
  font-size: 13px;
}
.spread-and-percentage {
  display: flex;
  flex-direction: column;
}

</style>
