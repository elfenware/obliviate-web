<style>
  header {
    grid-column: 2;
    display: grid;
    grid-template-columns: 5rem auto 2rem;
    column-gap: 0.5rem;
    margin: 1rem 0 2.5rem;
  }

  h1 {
    margin: 0;
    font-size: 20px;
    grid-column: 2;
  }

  .subtitle {
    margin-top: 0.25rem;
    margin-bottom: 0;
    grid-column: 2;
  }

  img {
    grid-row: 1 / span 2;
    grid-column: 1;
    align-self: center;
    justify-self: end;
  }
</style>

<header>
  <h1>Obliviate</h1>
  <p class="subtitle">
    A password manager that forgets your passwords (<a
      href="https://github.com/elfenware/obliviate-web#readme"
      target="_blank"
      rel="noopener noreferrer">Wut?</a>)
  </p>
  <img src="logo.svg" alt="Obliviate logo" width="48" height="48" />
</header>
