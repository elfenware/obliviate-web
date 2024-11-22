<script>
  import { createBubbler, stopPropagation } from "svelte/legacy";

  const bubble = createBubbler();
  import { createEventDispatcher } from "svelte";

  let { isOpen } = $props();

  const dispatch = createEventDispatcher();

  const handleClose = () => {
    dispatch("close");
  };
</script>

{#if isOpen}
  <div class="modal-wrapper" onclick={handleClose}>
    <div class="modal" onclick={stopPropagation(bubble("click"))}>
      <h1>What is Obliviate?</h1>
      <p>
        Obliviate does not store your passwords, but gives them to you when you
        need them. How?
      </p>
      <p>It asks you for two things:</p>
      <ul>
        <li>the site you want to log in to</li>
        <li>a cipher key, which is any passphrase you can remember</li>
      </ul>
      <p>
        Using these, it will derive a password, which you can set as your new
        password for that site.
      </p>
      <p>
        The next time you need it, enter the same site and same cipher key.
        Obliviate will derive the same password as before.
      </p>
      <p>It’s not magic, but it’s quite close.</p>
      <button class="close-button" onclick={handleClose}>&times;</button>
    </div>
  </div>
{/if}

<style>
  .modal-wrapper {
    background-color: rgba(48 48 48 / 0.8);
    position: absolute;
    top: 0;
    width: 100%;
    height: 100%;
  }

  .modal {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    margin: auto;
    width: calc(100% - 32px);
    max-width: 640px;
    height: calc(100% - 32px);
    max-height: 480px;
    padding: 24px;
    background-color: var(--bg-color);
    border-radius: 6px;
  }

  .close-button {
    cursor: pointer;
    position: absolute;
    top: -0.6rem;
    left: -0.6rem;
    border: 2px solid;
    border-radius: 50%;
    background: var(--button-bg);
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 1.25rem;
    width: 1.5rem;
    height: 1.5rem;
    margin: auto;
    padding-bottom: 10px;
  }
</style>
