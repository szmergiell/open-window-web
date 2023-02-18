<script lang="ts">
  let indoorTemperature: number = 0;
  let indoorRelativeHumidity: number = 0;

  let outdoorTemperature: number = 0;
  let outdoorRelativeHumidity: number = 0;

  let indoorDewPoint: number;
  let outdoorDewPoint: number;
  let openWindow: boolean;

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
    indoorDewPoint = round(json["indoor_dew_point"]);
    outdoorDewPoint = round(json["outdoor_dew_point"]);
    openWindow = json["open_window"];
  }
</script>

<form on:submit|preventDefault={handleSubmit}>
  <label>
    Indoor temperature [°C]:
    <input
      type="number"
      name="indoor-temperature"
      bind:value={indoorTemperature}
      min="-100"
      max="100"
    />
  </label>
  <label>
    Indoor relative humidity [%]:
    <input
      type="number"
      name="indoor-relative-humidity"
      bind:value={indoorRelativeHumidity}
      min="0"
      max="100"
    />
  </label>

  <label>
    Outdoor temperature [°C]:
    <input
      type="number"
      name="outdoor-temperature"
      bind:value={outdoorTemperature}
      min="-100"
      max="100"
    />
  </label>
  <label>
    Outdoor relative humidity [%]:
    <input
      type="number"
      name="outdoor-relative-humidity"
      bind:value={outdoorRelativeHumidity}
      min="0"
      max="100"
    />
  </label>

  <input type="submit" value="Submit" />
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

  form label {
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
