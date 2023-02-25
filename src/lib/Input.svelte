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
  export let valid: boolean = false;

  export const isValid: (display: boolean) => boolean = (display) => {
    if (!display) {
      return validate(value);
    }

    touched = true;
    valid = validate(value);
    return valid;
  };

  let touched: boolean = false;
  $: displayError = !valid && touched;

  function handleOnChange() {
    touched = true;
    valid = validate(value);
  }

  let inputElement: HTMLInputElement;

  function onNextBlurTriggerChange() {
    inputElement.addEventListener(
      "blur",
      function () {
        handleOnChange();
      },
      { once: true }
    );
  }

  function handleOnInput(event: Event) {
    const target = event.currentTarget as HTMLInputElement;
    const newValue = target.value;
    if (type === "text") {
      value = newValue;
      return;
    }

    if (type === "number") {
      if (newValue.length === 0) {
        // Number input type returns "" when it contains non valid characters.
        // Which sometimes leads to situation where input value changed on UI,
        // but underlying node / JS value hasn't changed,
        // so the "change" event is not being triggered.
        // e.g., type "a" into blank numeric input field
        onNextBlurTriggerChange();
        value = newValue;
        return;
      }
      const newNumber = Number(newValue);
      if (isNaN(newNumber)) {
        console.error(`this should not happen - ${newValue}`);
        value = newValue;
        return;
      }
      value = newNumber;
    }
  }
</script>

<label class={displayError ? "invalid" : ""}>
  {label}
  <input
    bind:this={inputElement}
    {type}
    {name}
    {value}
    min={type === "number" && min ? min : undefined}
    max={type === "number" && max ? max : undefined}
    step={type === "number" && step ? step : undefined}
    on:change={handleOnChange}
    on:input={handleOnInput}
  />
  {displayError ? errorMessage : ""}
</label>

<style>
  label {
    color: var(--green2);
  }

  .invalid {
    color: var(--red, red);
  }

  .invalid input {
    border-color: var(--red, red);
  }
</style>
