<div class="h1-container">
    <h1>Cotizaciones de 
        <select id="currencyDropdown">
            <option value="https://usdc.ar">USDC</option>
            <option value="https://btchoy.ar">BTC</option>
            <option value="https://usdthoy.ar">USDT</option>
        </select>
    </h1>
  </div>


<style>
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
</style>