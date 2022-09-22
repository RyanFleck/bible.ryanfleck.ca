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

  let scripture = [];

  let book_number = 0;
  let book_name = "";
  let verse = "";

  let query = "";

  const parseVerse = (v) => {
    let v_parts = parseVerseInt(v);
    return formatVerseInts(v_parts);
  };

  const formatVerseInts = (i) => {
    if (i.length == 3) return `${i[0]}:${i[1]}-${i[2]}`;
    if (i.length == 2) return `${i[0]}:${i[1]}`;
    if (i.length == 1) return `${i[0]}:1`;
    return "";
  };

  const parseVerseInt = (v) => {
    let v_parts = [].concat(v.match(/[0-9]+/g)).map((x) => parseInt(x));
    if (v_parts.length == 0) return [];
    if (v_parts.length >= 3) {
      if (v_parts[2] <= v_parts[1]) return [v_parts[0], v_parts[1]];
      return [v_parts[0], v_parts[1], v_parts[2]];
    }
    if (v_parts.length == 2) return v_parts;
    if (v_parts.length == 1) return v_parts.concat(1);
  };

  const buildVerseQuery = (book_number, book_name, verse) =>
    `${book_number > 0 ? book_number : ""} ${book_name} ${verse}`.trim();

  const handleInput = (e) => {
    let text = e.target.value;
    let all_components = [].concat(text.match(/[a-zA-Z]+|[0-9\:\-]+/g));
    let text_components = [];
    let numeric_components = [];

    if (text && all_components.length != 0) {
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
        verse = parseVerse(numeric_components[1]);
      } else if (numeric_components.length == 1) {
        verse = parseVerse(numeric_components[0]);
      }

      // Assemble query string.
      query = buildVerseQuery(book_number, book_name, verse);

      // Check for the verse if all the components are in place.
      if (query) {
        let verse_found = false;
        let verse_ints = parseVerseInt(verse);
        let first_query = buildVerseQuery(
          book_number,
          book_name,
          formatVerseInts(verse_ints.slice(0, 2))
        );
        console.log(`First Query: ${first_query} ${verse_ints}`);
        for (let x = 0; x < kjv.length; x++) {
          if (kjv[x].name == first_query) {
            scripture = [
              {
                number: verse_ints[1],
                verse: kjv[x].verse,
              },
            ];
            verse_found = true;

            // Grab more scripture.
            if (verse_ints.length == 3) {
              console.log(
                `Collecting from verse ${verse_ints[1]} to ${verse_ints[2]}`
              );
              let z = x + 1;
              for (let y = verse_ints[1] + 1; y <= verse_ints[2]; y++) {
                let next_query = buildVerseQuery(
                  book_number,
                  book_name,
                  formatVerseInts([verse_ints[0], y])
                );
                console.log(`Checking for ${next_query}`);
                if (kjv[z] && kjv[z].name == next_query) {
                  scripture.push({
                    number: y,
                    verse: kjv[z].verse,
                  });
                  z++;
                } else {
                  break;
                }
              }
            }
          }
        }
        if (!verse_found) scripture = [];
      } else {
        scripture = [];
      }
    } else {
      book_name = "";
      book_number = 0;
      verse = "";
      query = "";
      scripture = [];
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
    <div>
      <p>
        {#each scripture as s}
          <span><sup>{s.number}</sup>{s.verse} </span>
        {/each}
      </p>
    </div>
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
    padding: 0.5em 2em;
    margin: 1em;
    margin-bottom: 0;
    text-align: center;
    font-weight: bold;
    font-size: 1rem;
  }
</style>
