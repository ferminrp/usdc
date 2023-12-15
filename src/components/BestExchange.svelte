<script>
    export let price;
    export let label;
    export let value;
    export let url;
    export let logo;

function formatWithThousandSeparator(num) {
  return num.toLocaleString("de-DE", { minimumFractionDigits: 2, maximumFractionDigits: 2 });
}

function toPercentage(value) {
  return (value * 100).toFixed(1).replace('.', ',') + '%';
}
</script>
  
<div class="best">
    <a class="best-exchange" href="{url}">
      <div class="left">
        <div class="inline-container">
            {#if label === 'Mejor para comprar'}
            <svg fill="var(--background-color)"  xmlns="http://www.w3.org/2000/svg" width="32" height="32"  viewBox="0 0 256 256"><path d="M200,88V192a8,8,0,0,1-8,8H88a8,8,0,0,1,0-16h84.69L58.34,69.66A8,8,0,0,1,69.66,58.34L184,172.69V88a8,8,0,0,1,16,0Z"></path></svg>
            {:else if label === 'Mejor para vender'}
            <svg fill="var(--background-color)"  xmlns="http://www.w3.org/2000/svg" width="32" height="32"  viewBox="0 0 256 256"><path d="M200,64V168a8,8,0,0,1-16,0V83.31L69.66,197.66a8,8,0,0,1-11.32-11.32L172.69,72H88a8,8,0,0,1,0-16H192A8,8,0,0,1,200,64Z"></path></svg>
            {:else if label === 'Menor spread'}
            <svg fill="var(--background-color)"  xmlns="http://www.w3.org/2000/svg" width="32" height="32"  viewBox="0 0 256 256"><path d="M128,24A104,104,0,1,0,232,128,104.11,104.11,0,0,0,128,24Zm0,192a88,88,0,1,1,88-88A88.1,88.1,0,0,1,128,216ZM80,108a12,12,0,1,1,12,12A12,12,0,0,1,80,108Zm104,0a8,8,0,0,1-8,8H152a8,8,0,0,1,0-16h24A8,8,0,0,1,184,108Zm-9.08,48c-10.29,17.79-27.39,28-46.92,28s-36.63-10.2-46.93-28a8,8,0,1,1,13.86-8c7.46,12.91,19.2,20,33.07,20s25.61-7.1,33.08-20a8,8,0,1,1,13.84,8Z"></path></svg>
            {/if}
          <p class="best-price">$ {
            formatWithThousandSeparator(price)
          }</p>
        </div>
        <p>{label}: {value}</p>
      </div>
      <img loading="lazy" class="logo" src="{logo}" alt="${value} Logo" style="border-radius: 50%; width: 40px; height: 40px;">
    </a>
  </div>
  
  <style>
    /* ".inherited-styles-for-exported-element" is a generated class for the inherited styles of the exported element, feel free to rename it. */

    .inherited-styles-for-exported-element {
    color: #4c505b;
    font-family: Manrope, sans-serif;
    }

    svg {
    background-color: #683ffd;
    border-radius: 4px;
    margin-bottom: 6px;
    margin-right: 18px;
    padding: 1px;
    }

    a, a:active, a:hover, a:visited {
    text-decoration: none;
    }

    .best-exchange {
    align-items: center;
    background-color: #e7e8ea;
    border-radius: 8px;
    display: flex;
    font-weight: 700;
    justify-content: space-between;
    padding: 12px;
    }

    .best-price {
    color: #683ffd;
    font-size: 1.2em;
    font-weight: 700;
    }

    .inline-container {
    align-items: center;
    display: flex;
    }

    .best-exchange p {
    margin: 0;
    }

  
  </style>
  