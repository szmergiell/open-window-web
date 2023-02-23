<script lang="ts" context="module">
  export interface IProblemJson {
    title: string;
    detail: string;
    errors?: object | undefined;
  }
</script>

<script lang="ts">
  export let problemJson: IProblemJson;
</script>

{#if problemJson}
  <article>
    <h4 class="error">{problemJson.title}</h4>
    <p class="error">{problemJson.detail}</p>
    {#if problemJson.errors}
      <ul>
        {#each Object.entries(problemJson.errors) as [key, value]}
          <li>{key} - {value}</li>
        {/each}
      </ul>
    {/if}
  </article>
{/if}

<style>
  .error {
    color: var(--red, red);
  }
</style>
