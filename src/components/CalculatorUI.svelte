<script>
  export let calculatedField;
  export let buttons;
</script>

<div class="calculator">
  <div id="Workspace">
    <div class="calculatedField">{calculatedField.length ? calculatedField.join(" ") : 0}</div>
  </div>
  {#each buttons as button}
    <button id={button.id} on:click={button.func}>{button.value}</button>
  {/each}
</div>

<style>
  .calculator {
    display: grid;
    grid-template-columns: 20% 20% 20% 20%;
    grid-gap: 20px;
    color: #444;
    width: 300px;
    height: 600px;
    padding: 20px 20px 40px 20px;
    background-color: #F1F1F3;
    cursor: pointer;
    user-select: none;
    border-radius: 40px;
  
  }

  #Workspace {
    grid-column: 1 / span 4;
    height: 200px;
    cursor: initial;
    padding: 0 5px;
    border-radius: 40px 40px 0px 0px;
    overflow: hidden;

    display: flex;
    flex-direction: column;
    justify-content: end;
    text-align: right;
  }

  .calculatedField {
    font-size: 50px;
    line-height: 100%;
    color: #FEA010;
  }
  .result {
    font-size: 30px;
    line-height: 100%;
    color: #FEA010;
  }
  button {
    height: 60px;
    border-radius: 100%;
    border: none;
    font-weight: 600;
    font-size: 25px;
    cursor: pointer;
    color: #7d7d7d;
    box-shadow: 1px 0.1em rgb(173, 173, 173), -1px 0 .4em white;
  }
  button:hover {
    color: black;
  }
  button:active {
    padding-top: 2px;
    padding-right: 1px;
    box-shadow: none;
  }
  #clear, #divide, #multiply, #minus, #plus {
    background-color: #FEA010;
    color: #F1F1F3;
    box-shadow: none;
  }
  #clear:active, #divide:active,  #multiply:active, #minus:active, #plus:active {
    padding: 0;
    color: black;
  }
  
</style>
