<script lang="ts">
  import Input from "./Input.svelte";

  export let label = "";
  export let name = "";
  export let value: number;

  const min = -100;
  const max = 100;

  function validate(temperature: number): boolean {
    return min <= temperature && temperature <= max;
  }

  const errorMessage = `Temperature must be a number between ${min} and ${max}`;
</script>

<Input
  {label}
  type="number"
  {name}
  bind:value
  step="0.1"
  min={min.toString()}
  max={max.toString()}
  {validate}
  {errorMessage}
/>
