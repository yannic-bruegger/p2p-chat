<script lang="ts">
  export let text: string;
  export let time: Date;
  export let received: boolean;
  export let sent: boolean;
</script>

<div class="message-container" class:received class:sent>
  <div class="message" class:received class:sent>
    <div>{text}</div>
    <small>{(new Date(time)).toLocaleString('de-DE')}</small>
  </div>
</div>

<style lang="scss">
.message-container {
  display: flex;
  justify-content: center;
  
  &.received {
    justify-content: flex-start;
  }
  
  &.sent {
    justify-content: flex-end;
  }
}
.message {
  display: inline-block;
  padding: .5rem 1rem;
  border-radius: 20px;
  width: auto;
  text-align: center;

  &.received {
    border-end-start-radius: 0;
    background-color: var(--bg-secondary-color);
    text-align: left;
  }
  
  &.sent {
    border-end-end-radius: 0;
    background-color: var(--bg-secondary-color);
    text-align: left;
  }

  small {
    color: var(--color-transparent);
  }
}
</style>

