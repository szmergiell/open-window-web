<script lang="ts">
    let indoorTemperature: number = 0;
    let indoorRelativeHumidity: number = 0;

    let outdoorTemperature: number = 0;
    let outdoorRelativeHumidity: number = 0;
</script>

<form>
    <label>
        Indoor temperature [°C]:
        <input
            type="number"
            name="indoor-temperature"
            value={indoorTemperature}
            min="-100"
            max="100"
        />
    </label>
    <label>
        Indoor relative humidity [%]:
        <input
            type="number"
            name="indoor-relative-humidity"
            value={indoorRelativeHumidity}
            min="0"
            max="100"
        />
    </label>

    <label>
        Outdoor temperature [°C]:
        <input
            type="number"
            name="outdoor-temperature"
            value={outdoorTemperature}
            min="-100"
            max="100"
        />
    </label>
    <label>
        Outdoor relative humidity [%]:
        <input
            type="number"
            name="outdoor-relative-humidity"
            value={outdoorRelativeHumidity}
            min="0"
            max="100"
        />
    </label>
    <input type="submit" value="Submit" />
</form>

<style>
    form {
        display: flex;
        flex-flow: column nowrap;
        gap: 1rem;
    }

    label {
        display: flex;
        flex-flow: column nowrap;
        gap: 0.5rem;
        color: var(--green2);
    }

    input[type="submit"] {
        margin-top: 1rem;
        background-color: var(--green2);
        color: var(--bg-dark);
    }
</style>
