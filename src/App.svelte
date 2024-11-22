<script>
  import copy from "copy-text-to-clipboard";
  import pbkdf2Hmac from "pbkdf2-hmac";
  import Footer from "./Footer.svelte";
  import Header from "./Header.svelte";
  import InfoModal from "./InfoModal.svelte";

  let site = $state("");
  let cipher = $state("");
  let derivedPassword = $state("");
  let copyButtonText = $state("Copy");
  let copyWithoutSymbolsText = $state("Copy without symbols");
  let showCipher = $state(false);
  let showDerivedPassword = $state(false);
  let showInfoModal = $state(false);

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
      16,
    );

    const indices = new Uint8Array(derivedBuffer).map(
      (byte) => byte % allowedCharacters.length,
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
    copy(derivedPassword.replaceAll(/[^0-9a-z]/gi, ""));
    copyWithoutSymbolsText = "Copied!";
    setTimeout(() => {
      copyWithoutSymbolsText = "Copy without symbols";
    }, 1000);
  }
</script>

<main>
  <Header
    on:info={() => {
      showInfoModal = true;
    }}
  />

  <article>
    <label for="site">Site:</label>

    <input
      type="text"
      id="site"
      placeholder="GitHub"
      bind:value={site}
      oninput={handleDerive}
    />

    <button aria-label="Info" class="info" tabindex="-1">
      <i
        class="ri-information-line"
        title="Site is not case-sensitive. “GitHub” equals “github”."
      ></i>
    </button>

    <label for="cipher">Cipher key:</label>

    <input
      type={showCipher ? "text" : "password"}
      id="cipher"
      placeholder="correct horse battery staple"
      value={cipher}
      oninput={(e) => {
        cipher = e.target.value;
        handleDerive();
      }}
    />

    <button
      aria-label="Show/hide cipher key"
      class="toggle"
      disabled={!cipher}
      onclick={() => (showCipher = !showCipher)}
    >
      <i
        class={showCipher ? "ri-eye-line" : "ri-eye-off-line"}
        title="Show/hide cipher key"
      ></i>
    </button>

    <label for="derived-password">Password:</label>

    <input
      disabled
      type={showDerivedPassword ? "text" : "password"}
      id="derived-password"
      value={derivedPassword}
      class="derived"
    />

    <button
      aria-label="Show/hide password"
      class="toggle"
      disabled={!derivedPassword}
      onclick={() => (showDerivedPassword = !showDerivedPassword)}
    >
      <i
        class={showDerivedPassword ? "ri-eye-line" : "ri-eye-off-line"}
        title="Show/hide password"
      ></i>
    </button>

    <button disabled={!derivedPassword} onclick={handleCopy}
      >{copyButtonText}</button
    >

    <button disabled={!derivedPassword} onclick={handleCopyWithoutSymbols}
      >{copyWithoutSymbolsText}</button
    >
  </article>

  <Footer />
</main>

<InfoModal
  isOpen={showInfoModal}
  on:close={() => {
    showInfoModal = false;
  }}
/>

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
