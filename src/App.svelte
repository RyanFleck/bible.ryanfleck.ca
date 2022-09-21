<script>
  import bibleSvg from "./assets/bible.svg";
  import Counter from "./lib/Counter.svelte";
  import kjv from "./assets/kjv.json";
  import books from "./assets/books.json";

  // Build a list of all Bible book names.
  let allBooks = [];
  allBooks = allBooks.concat(books["New Testament"]);
  allBooks = allBooks.concat(books["Old Testament"]);

  // Lowercase, trim numbers and whitespace.
  let allBooksNoNums = allBooks.map((x) => x.replace(/[0-9]/g, "").trim());
  let allBooksNNLower = allBooksNoNums.map((x) => x.toLowerCase());

  // let list = "";
  // allBooks.forEach((x) => (list = list.concat(`${x}\n`)));
  // console.log(list);

  let scripture = "";

  let book_number = 0;
  let book_name = "";
  let verse = "";

  const handleInput = (e) => {
    let text = e.target.value;
    let all_components = [].concat(text.match(/[a-zA-Z]+|[0-9\:\-]+/g));
    let text_components = [];
    let numeric_components = [];

    if (all_components.length > 0) {
      // Split into numeric and textual components.

      for (let x = 0; x < all_components.length; x++) {
        let content = all_components[x];
        if (content && !isNaN(content[0])) {
          numeric_components.push(content);
        } else {
          text_components.push(content);
        }
      }

      // Figure out book name
      let start_of_name = text_components.join(" ").toLowerCase();
      console.log(`Start of name: ${start_of_name}`);
      for (let x = 0; x < allBooksNNLower.length; x++) {
        if (allBooksNNLower[x].startsWith(start_of_name)) {
          book_name = allBooksNoNums[x];
          console.log(book_name);
          break;
        }
      }

      console.log(text_components);
      console.log(numeric_components);
    } else {
      book_name = "";
      book_number = 0;
      verse = "";
    }
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

  <div class="scripture-search">
    <div>
      <input type="text" name="scripture" on:input={handleInput} />
    </div>

    <small
      >Searching for
      {book_number == 0 ? "" : book_number}
      {book_name == "" ? "..." : book_name}
      {verse}</small
    >
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
  .scripture-search input {
    padding: 0.5em;
    margin: 1em;
  }
</style>
