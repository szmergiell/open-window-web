<script lang="ts">
  import Input from "./Input.svelte";

  export let label = "";
  export let name = "";
  export let value: number;
  export let isValid: (display: boolean) => boolean;
  export let valid: boolean;

  const min = 1;
  const max = 100;

  function validate(value: string | number): boolean {
    if (typeof value === "string") {
      return false;
    }

    const humidity = Number(value);

    if (isNaN(humidity)) {
      return false;
    }

    return min <= humidity && humidity <= max;
  }

  const errorMessage = `Relative humidity must be a percentage value between ${min} and ${max}`;
</script>

<Input
  {label}
  type="number"
  {name}
  bind:value
  min={min.toString()}
  max={max.toString()}
  step="1"
  {validate}
  {errorMessage}
  bind:isValid
  bind:valid
/>
