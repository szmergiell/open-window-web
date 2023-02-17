<script lang="ts">
    import OpenWindow from "./lib/OpenWindow.svelte";
</script>

<main>
    <article>
        <h1>Should you open window?</h1>
        <p>
            This little tool answers a question whether one should open windows
            in order to decrease indoor humidity.
        </p>
        <p>
            Question is answered by comparing indoor and outdoor dew points
            calculated based on indoor/outdoor temperature and relative humidity
            measurements.
        </p>
    </article>
    <div class="card">
        <OpenWindow />
    </div>
</main>

<style>
</style>
