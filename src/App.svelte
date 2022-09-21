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

  let scripture = "";

  let book_number = 0;
  let book_name = "";
  let verse = "";

  const parseVerse = (v) => {
    let v_parts = [].concat(v.match(/[0-9]+/g));
    if (v_parts.length == 0) {
      verse = "";
      return;
    }
    if (v_parts.length == 3) {
      // Support verse stretches soon! :(
      verse = `${v_parts[0]}:${v_parts[1]}`;
      return;
    }
    if (v_parts.length == 2) {
      verse = `${v_parts[0]}:${v_parts[1]}`;
      return;
    }
    if (v_parts.length == 1) {
      verse = `${v_parts[0]}:1`;
      return;
    }
  };

  const handleInput = (e) => {
    let text = e.target.value;
    let all_components = [].concat(text.match(/[a-zA-Z]+|[0-9\:\-]+/g));
    let text_components = [];
    let numeric_components = [];

    if (all_components.length != 0) {
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
      for (let x = 0; x < allBooksNNLower.length; x++) {
        if (allBooksNNLower[x].startsWith(start_of_name)) {
          book_name = allBooksNoNums[x];
          break;
        } else if (x == allBooksNNLower.length - 1) {
          book_name = "";
        }
      }

      // Detect book number
      if (numeric_components.length == 2) {
        book_number = parseInt(numeric_components[0]);
      } else if (numeric_components.length == 1) {
        book_number = 0;
      }

      // Detect verses
      if (numeric_components.length == 2) {
        parseVerse(numeric_components[1]);
      } else if (numeric_components.length == 1) {
        parseVerse(numeric_components[0]);
      }

      // Assemble query string.
      let query = "";
      if (book_number) query = query.concat(`${book_number} `);
      if (book_name) query = query.concat(`${book_name} `);
      if (verse) query = query.concat(verse);

      // Check for the verse if all the components are in place.
      if (query && book_name && verse) {
        let verse_found = false;
        for (let x = 0; x < kjv.length; x++) {
          if (kjv[x].name == query) {
            scripture = kjv[x].verse;
            verse_found = true;
            break;
          }
        }
        if (!verse_found) scripture = "";
        // Trigger build.
      }
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
