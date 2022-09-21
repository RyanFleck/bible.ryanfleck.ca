<script>
  import bibleSvg from "./assets/bible.svg";
  import Counter from "./lib/Counter.svelte";
  import kjv from "./assets/kjv.json";

  let scripture = "";

  const handleInput = (e) => {
    let text = e.target.value;
    console.log(text);
  };
</script>

<main>
  <div class="title-container">
    <img src={bibleSvg} class="logo svelte" alt="Svelte Logo" />
    <h1 class="title">Holy Bible</h1>
  </div>
  <small
    >If you are reading this the entire King James Bible has been loaded into
    your RAM.</small
  >

  <div class="card">
    <Counter />
  </div>

  <div class="scripture-search">
    <input type="text" name="scripture" on:input={handleInput} />
    <p><i>{scripture}</i></p>
  </div>
</main>

<style>
  .logo {
    height: 60px;
    will-change: filter;
    padding: 0;
  }
  .logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
  }
  .title {
    padding: 0.2em;
    font-size: 60px;
    margin: 0;
  }
  .title-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
  }
</style>
