<script lang="ts">
  import type { IProblemJson } from "./ProblemJson.svelte";
  import ProblemJson from "./ProblemJson.svelte";
  import TemperatureInput from "./TemperatureInput.svelte";
  import HumidityInput from "./HumidityInput.svelte";

  let indoorTemperature: number;
  let indoorRelativeHumidity: number;

  let outdoorTemperature: number;
  let outdoorRelativeHumidity: number;

  let indoorDewPoint: number;
  let outdoorDewPoint: number;
  let openWindow: boolean;

  const genericError: IProblemJson = {
    title: "Submit failed.",
    detail: "Could not submit data to the server. Please try again later.",
  };

  const unprocessableEntity: IProblemJson = {
    title: "Invalid request.",
    detail: "Request validation failed. Please check the data and try again.",
  };

  let problemJson: IProblemJson;

  function round(value: number): number {
    return Math.round(value * 100) / 100;
  }

  async function handleSubmit() {
    openWindow = undefined;

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

    const requestInit: RequestInit = {
      method: "POST",
      headers: {
        Accept: "application/json",
        "Content-Type": "application/json",
      },
      body: JSON.stringify(data),
    };

    let response: Response;

    try {
      response = await fetch("http://localhost:3000/open-window", requestInit);
    } catch (ex) {
      console.log(ex);
      problemJson = genericError;
      return;
    }

    if (
      response.status !== 200 &&
      response.status !== 400 &&
      response.status !== 422
    ) {
      problemJson = genericError;
      return;
    }

    if (response.status === 422) {
      problemJson = unprocessableEntity;
      return;
    }

    if (response.status === 400) {
      problemJson = await response.json();
      return;
    }

    if (response.status === 200) {
      problemJson = undefined;
      const json = await response.json();
      indoorDewPoint = round(json.indoor_dew_point);
      outdoorDewPoint = round(json.outdoor_dew_point);
      openWindow = json.open_window;
      return;
    }
  }
</script>

<form on:submit|preventDefault={handleSubmit}>
  <TemperatureInput
    label="Indoor temperature [°C]:"
    name="indoor-temperature"
    bind:value={indoorTemperature}
  />
  <HumidityInput
    label="Indoor relative humidity [%]:"
    name="indoor-relative-humidity"
    bind:value={indoorRelativeHumidity}
  />

  <TemperatureInput
    label="Outdoor temperature [°C]:"
    name="outdoor-temperature"
    bind:value={outdoorTemperature}
  />
  <HumidityInput
    label="Outdoor relative humidity [%]:"
    name="outdoor-relative-humidity"
    bind:value={outdoorRelativeHumidity}
  />

  <input type="submit" value="Submit" />

  <ProblemJson {problemJson} />
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
