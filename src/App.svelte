<script>
  import copy from "copy-text-to-clipboard";
  import pbkdf2Hmac from "pbkdf2-hmac";
  import Footer from "./Footer.svelte";
  import Header from "./Header.svelte";
  import InfoModal from "./InfoModal.svelte";

  let site = "";
  let cipher = "";
  let derivedPassword = "";
  let copyButtonText = "Copy";
  let copyWithoutSymbolsText = "Copy without symbols";
  let showCipher = false;
  let showDerivedPassword = false;
  let showInfoModal = false;

  const allowedCharacters = [
    "0",
    "1",
    "2",
    "3",
    "4",
    "5",
    "6",
    "7",
    "8",
    "9",
    "A",
    "B",
    "C",
    "D",
    "E",
    "F",
    "G",
    "H",
    "I",
    "J",
    "K",
    "L",
    "M",
    "N",
    "O",
    "P",
    "Q",
    "R",
    "S",
    "T",
    "U",
    "V",
    "W",
    "X",
    "Y",
    "Z",
    "a",
    "b",
    "c",
    "d",
    "e",
    "f",
    "g",
    "h",
    "i",
    "j",
    "k",
    "l",
    "m",
    "n",
    "o",
    "p",
    "q",
    "r",
    "s",
    "t",
    "u",
    "v",
    "w",
    "x",
    "y",
    "z",
    "!",
    "#",
    "$",
    "%",
    "&",
    "(",
    ")",
    "*",
    "+",
    "-",
    ";",
    "<",
    "=",
    ">",
    "?",
    "@",
    "^",
    "_",
    "`",
    "{",
    "|",
    "}",
    "~",
  ];

  async function handleDerive() {
    if (!site || !cipher) {
      derivedPassword = "";
      return;
    }

    const derivedBuffer = await pbkdf2Hmac(
      cipher,
      site.toLowerCase(),
      10000,
      16
    );

    const indices = new Uint8Array(derivedBuffer).map(
      (byte) => byte % allowedCharacters.length
    );

    derivedPassword = Array.from(indices)
      .map((index) => allowedCharacters[index % allowedCharacters.length])
      .join("");
  }

  function handleCopy() {
    copy(derivedPassword);
    copyButtonText = "Copied!";
    setTimeout(() => {
      copyButtonText = "Copy";
    }, 1000);
  }

  function handleCopyWithoutSymbols() {
    copy(derivedPassword.replaceAll(/[^0-9a-z]/ig, ""));
    copyWithoutSymbolsText = "Copied!";
    setTimeout(() => {
      copyWithoutSymbolsText = "Copy without symbols";
    }, 1000);
  }
</script>

<style>
  main {
    display: grid;
    grid-template-columns:
      minmax(1.2rem, 1fr)
      minmax(auto, 24rem)
      minmax(1.2rem, 1fr);
    grid-template-rows: auto 1fr auto;
  }

  article {
    grid-column: 2;
    display: grid;
    grid-template-columns: 5rem auto 2rem;
    column-gap: 0.5rem;
    align-items: center;
  }

  label {
    grid-column: 1;
    justify-self: end;
    text-align: right;
  }

  input,
  button:not(.toggle):not(.info) {
    grid-column: 2;
  }

  button.toggle,
  button.info {
    grid-column: 3;
  }

  .derived {
    background: transparent;
    border: none;
    color: var(--fg-color);
    margin: 0;
    padding-left: 0;
    font-weight: bold;
  }
</style>

<main>
  <Header
    on:info={() => {
      showInfoModal = true;
    }} />

  <article>
    <label for="site">Site:</label>

    <input
      type="text"
      id="site"
      placeholder="GitHub"
      bind:value={site}
      on:input={handleDerive} />

    <button class="info" tabindex="-1">
      <i
        class="ri-information-line"
        title="Site is not case-sensitive. “GitHub” equals “github”."
        aria-label="Site is not case-sensitive. “GitHub” equals “github”." />
    </button>

    <label for="cipher">Cipher key:</label>

    <!-- Svelte won't allow bind:value when type is dynamic. -->
    <input
      type={showCipher ? 'text' : 'password'}
      id="cipher"
      placeholder="correct horse battery staple"
      value={cipher}
      on:input={(e) => {
        cipher = e.target.value;
        handleDerive();
      }} />

    <button
      class="toggle"
      disabled={!cipher}
      on:click={() => (showCipher = !showCipher)}>
      <i
        class={showCipher ? 'ri-eye-line' : 'ri-eye-off-line'}
        title="Show/hide cipher key"
        aria-label="Show/hide cipher key" />
    </button>

    <label for="derived-password">Password:</label>

    <input
      disabled
      type={showDerivedPassword ? 'text' : 'password'}
      id="derived-password"
      value={derivedPassword}
      class="derived" />

    <button
      class="toggle"
      disabled={!derivedPassword}
      on:click={() => (showDerivedPassword = !showDerivedPassword)}>
      <i
        class={showDerivedPassword ? 'ri-eye-line' : 'ri-eye-off-line'}
        title="Show/hide password"
        aria-label="Show/hide password" />
    </button>

    <button
      disabled={!derivedPassword}
      on:click={handleCopy}>{copyButtonText}</button>

    <button
      disabled={!derivedPassword}
      on:click={handleCopyWithoutSymbols}>{copyWithoutSymbolsText}</button>
  </article>

  <Footer />
</main>

<InfoModal
  isOpen={showInfoModal}
  on:close={() => {
    showInfoModal = false;
  }} />
