<script lang="ts">
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  export let myPeerId = 'Getting ID ...';
  
  let otherPeerId = '';
  
  function connectHandler() {
    dispatch('connect', otherPeerId);
  }
</script>

<div class="backdrop">
  <div class="modal">
    <h1>Start a new chat</h1>
    <p>To start a new chat simply paste the peer ID of your partner and click on connect. If your partner is online the connection will be established.</p>
    <br>
    <h1>Your peer ID is:</h1>
    <p>{myPeerId}</p>
    <hr>
    <input type="text" class="no-style" placeholder="Your partners peer ID" bind:value={otherPeerId}>
		<button on:click={connectHandler}>Connect</button>
  </div>
</div>

<style lang="scss">
hr {
  margin: var(--xl) 0;
}

.backdrop {
  position: fixed;
  width: 100%;
  height: 100%;
  background-color: rgba(17, 8, 8, .8);
  backdrop-filter: blur(2px);
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal {
  background-color: var(--bg-color);
  border-radius: 5px;
  width: 90%;
  height: auto;
  padding: var(--xl);
  display: flex;
  flex-direction: column;
  gap: var(--m);

  h1 {
    font-size: 1rem;
  }

  input {
    margin-bottom: var(--xl);
  }
}
</style>