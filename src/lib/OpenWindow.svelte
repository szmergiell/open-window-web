<script lang="ts">
  import ProblemJson from "./ProblemJson.svelte";
  import Input from "./Input.svelte";

  let indoorTemperature: number;
  let indoorRelativeHumidity: number;

  let outdoorTemperature: number;
  let outdoorRelativeHumidity: number;

  let indoorDewPoint: number;
  let outdoorDewPoint: number;
  let openWindow: boolean;

  let problemJson: {
    title: string;
    detail: string;
    errors: object;
  };

  function round(value: number): number {
    return Math.round(value * 100) / 100;
  }

  async function handleSubmit() {
    const data = {
      indoor_measurement: {
        temperature: indoorTemperature,
        relative_humidity: indoorRelativeHumidity,
      },
      outdoor_measurement: {
        temperature: outdoorTemperature,
        relative_humidity: outdoorRelativeHumidity,
      },
    };

    const response = await fetch("http://localhost:3000/open-window", {
      method: "POST",
      headers: {
        Accept: "application/json",
        "Content-Type": "application/json",
      },
      body: JSON.stringify(data),
    });

    const json = await response.json();

    if (response.status != 200) {
      problemJson = json;
      openWindow = undefined;
      return;
    }

    problemJson = undefined;

    indoorDewPoint = round(json.indoor_dew_point);
    outdoorDewPoint = round(json.outdoor_dew_point);
    openWindow = json.open_window;
  }

  function validateTemperature(value: number): boolean {
    const temperature = Number(value);
    return -100 <= temperature && temperature <= 100;
  }

  const temperatureErrorMessage: string =
    "Temperature must be a number between -100 and 100";

  function validateHumidity(value: number): boolean {
    const humidity = Number(value);
    return 1 <= humidity && humidity <= 100;
  }

  const humidityErrorMessage: string =
    "Relative humidity must be a percentage value between 1 and 100";
</script>

<form on:submit|preventDefault={handleSubmit}>
  <Input
    label="Outdoor temperature [°C]:"
    type="number"
    name="indoor-temperature"
    bind:value={indoorTemperature}
    min="-100"
    max="100"
    step="0.1"
    validate={validateTemperature}
    errorMessage={temperatureErrorMessage}
  />
  <Input
    label="Indoor relative humidity [%]:"
    type="number"
    name="indoor-relative-humidity"
    bind:value={indoorRelativeHumidity}
    min="1"
    max="100"
    validate={validateHumidity}
    errorMessage={humidityErrorMessage}
  />

  <Input
    label="Outdoor temperature [°C]:"
    type="number"
    name="outdoor-temperature"
    bind:value={outdoorTemperature}
    min="-100"
    max="100"
    step="0.1"
    validate={validateTemperature}
    errorMessage={temperatureErrorMessage}
  />
  <Input
    label="Outdoor relative humidity [%]:"
    type="number"
    name="outdoor-relative-humidity"
    bind:value={outdoorRelativeHumidity}
    min="1"
    max="100"
    validate={validateHumidity}
    errorMessage={humidityErrorMessage}
  />

  <input type="submit" value="Submit" />

  <ProblemJson {...problemJson} />
</form>

{#if openWindow !== undefined}
  <div class="result">
    <div class="open-window">
      <h2>Open window?</h2>
      <p>{openWindow ? "YES!" : "NO :("}</p>
    </div>
    <div class="details">
      <div>
        <h4>Indoor dew point [°C]:</h4>
        <p>{indoorDewPoint}</p>
      </div>
      <div>
        <h4>{openWindow ? ">" : "<"}</h4>
      </div>
      <div>
        <h4>Outdoor dew point [°C]:</h4>
        <p>{outdoorDewPoint}</p>
      </div>
    </div>
  </div>
{/if}

<style>
  form {
    display: flex;
    flex-flow: column nowrap;
    gap: 1rem;
  }

  form :global(label) {
    display: flex;
    flex-flow: column nowrap;
    gap: 0.5rem;
    color: var(--green2);
  }

  form input[type="submit"] {
    margin-top: 1rem;
    background-color: var(--green2);
    color: var(--bg-dark);
  }

  .result {
    text-align: center;
  }

  div.details {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
</style>
