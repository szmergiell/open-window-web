<script lang="ts">
  export let title: string = "";
  export let detail: string = "";
  export let errors: object = {};
</script>

{#if title}
  <article>
    <h4 class="error">{title}</h4>
    <p class="error">{detail}</p>
    <ul>
      {#each Object.entries(errors) as [key, value]}
        <li>{key} - {value}</li>
      {/each}
    </ul>
  </article>
{/if}

<style>
  .error {
    color: var(--red, red);
  }
</style>
