<script>
  import bibleSvg from "./assets/bible.svg";
  import Counter from "./lib/Counter.svelte";
</script>

<main>
  <div class="title-container">
    <img src={bibleSvg} class="logo svelte" alt="Svelte Logo" />
    <h1 class="title">Holy Bible</h1>
  </div>

  <div class="card">
    <Counter />
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
    padding: .2em;
    font-size: 60px;
  }
  .title-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
  }
  .title-container > img {
    flex: 1;
  }
  .title-container > h1 {
    flex-grow: 4;
  }
</style>
