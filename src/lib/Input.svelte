<script lang="ts">
  export let label: string = "";
  export let type: "text" | "number" = "text";
  export let name: string = "";
  export let value: string | number = "";
  export let min: string = "";
  export let max: string = "";
  export let step: string = "";
  export let validate: (value: string | number) => boolean = () => true;
  export let errorMessage: string = "";

  let invalid: boolean = false;

  function handleOnChange() {
    invalid = !validate(value);
  }

  function handleOnInput(event: Event) {
    const target = event.currentTarget as HTMLInputElement;
    const newValue = target.value;
    if (type === "number") {
      value = Number(newValue);
    } else {
      value = newValue;
    }
  }
</script>

<label class:invalid>
  {label}
  <input
    {type}
    {name}
    {value}
    min={type === "number" && min ? min : undefined}
    max={type === "number" && max ? max : undefined}
    step={type === "number" && step ? step : undefined}
    on:change={handleOnChange}
    on:input={handleOnInput}
  />
  {invalid ? errorMessage : ""}
</label>

<style>
  .invalid {
    color: var(--red, red);
  }

  .invalid input {
    border-color: var(--red, red);
  }
</style>
