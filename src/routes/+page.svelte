<script lang="js">
    import { onMount } from 'svelte';

    let pins = [
        { id: 'livingroom', lastRefresh: 'N/A', temp: 'N/A', hum: 'N/A', x: '24%', y: '16%' },
        { id: 'cellar', lastRefresh: 'N/A', temp: 'N/A', hum: 'N/A', x: '13%', y: '52%' },
        { id: 'kitchen', lastRefresh: 'N/A', temp: 'N/A', hum: 'N/A', x: '41%', y: '51%' },
        { id: 'bedroom1', lastRefresh: 'N/A', temp: 'N/A', hum: 'N/A', x: '63%', y: '14%' },
        { id: 'bedroom2', lastRefresh: 'N/A', temp: 'N/A', hum: 'N/A', x: '87%', y: '14%' },
        { id: 'office', lastRefresh: 'N/A', temp: 'N/A', hum: 'N/A', x: '69%', y: '48%' }
    ];

    onMount(() => {
        refreshPins();
        const interval = setInterval(refreshPins, 60000);
        return () => {clearInterval(interval); console.log(pins);};
    });

    async function refreshPins() {
        const response = await fetch('http://localhost:4000/', {
            method: 'GET',
            headers: {
                'Content-Type': 'application/json'
            }
        });
        const data = await response.json();

        console.log('Fetched data:', data);
        pins.forEach(pin => {
            pin.temp = data[pin.id]?.last_temp ?? 'N/A';
            pin.hum = data[pin.id]?.last_hydro ?? 'N/A';
            pin.lastRefresh = data[pin.id]?.last_log_time ?? 'N/A';
        });
        console.log('Updated pins:', pins);
        pins = pins;
    };

    function isOlderThan5Minutes(timestamp) {
        if (timestamp === 'N/A') return true;
        const today = new Date().toISOString().split('T')[0];
        const logTime = new Date(`${today}T${timestamp}`).getTime();
        const currentTime = new Date().getTime();
        const diffInMinutes = (currentTime - logTime) / (1000 * 60);
        return diffInMinutes > 5;
    }

</script>

<main>
    <div class="image-container" style="position: relative; display: inline-block;">
        <img src="/Plan.png" alt="Plan" />
        {#each pins as pin}
            <div id="{pin.id}" class="pin" style="position: absolute; left: {pin.x}; top: {pin.y}; transform: translate(-50%, -50%);">
                <p style="color: {isOlderThan5Minutes(pin.lastRefresh) ? 'red' : 'blue'}">
                    {pin.temp}°C
                </p>
                <p style="color: {isOlderThan5Minutes(pin.lastRefresh) ? 'red' : 'blue'}">
                    {pin.hum}%
                </p>
            </div>
        {/each}
    </div>
</main>

<style>
    main {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        background-color: #f0f0f0;
    }

    img {
        max-width: 100%;
        height: auto;
        border: 2px solid #ccc;
        border-radius: 8px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    }
</style>