<script>
    import { onMount } from 'svelte';

    let pins = [
        { id: 'livingroom', lastRefresh: 'N/A', temp: 'N/A', hum: 'N/A' },
        { id: 'cellar', lastRefresh: 'N/A', temp: 'N/A', hum: 'N/A' },
        { id: 'garage', lastRefresh: 'N/A', temp: 'N/A', hum: 'N/A' },
        { id: 'kitchen', lastRefresh: 'N/A', temp: 'N/A', hum: 'N/A' },
        { id: 'bedroom1', lastRefresh: 'N/A', temp: 'N/A', hum: 'N/A' },
        { id: 'bedroom2', lastRefresh: 'N/A', temp: 'N/A', hum: 'N/A' },
        { id: 'office', lastRefresh: 'N/A', temp: 'N/A', hum: 'N/A' }
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
        <div id="livingroom" class="pin" style="position: absolute; left: 24%; top: 16%; transform: translate(-50%, -50%)">
            <p style="color: {isOlderThan5Minutes(pins.find(p => p.id === 'livingroom')?.lastRefresh) ? 'red' : 'blue'}">
                {pins.find(p => p.id === 'livingroom')?.temp}°C
            </p>
            <p style="color: {isOlderThan5Minutes(pins.find(p => p.id === 'livingroom')?.lastRefresh) ? 'red' : 'blue'}">
                {pins.find(p => p.id === 'livingroom')?.hum}%
            </p>
        </div>
        <div id="cellar" class="pin" style="position: absolute; left: 13%; top: 52%; transform: translate(-50%, -50%)">
            <p style="color: {isOlderThan5Minutes(pins.find(p => p.id === 'cellar')?.lastRefresh) ? 'red' : 'blue'}">
                {pins.find(p => p.id === 'cellar')?.temp}°C
            </p>
            <p style="color: {isOlderThan5Minutes(pins.find(p => p.id === 'cellar')?.lastRefresh) ? 'red' : 'blue'}">
                {pins.find(p => p.id === 'cellar')?.hum}%
            </p>
        </div>
        <div id="kitchen" class="pin" style="position: absolute; left: 41%; top: 51%; transform: translate(-50%, -50%);">
            <p style="color: {isOlderThan5Minutes(pins.find(p => p.id === 'kitchen')?.lastRefresh) ? 'red' : 'blue'}">
                {pins.find(p => p.id === 'kitchen')?.temp}°C
            </p>
            <p style="color: {isOlderThan5Minutes(pins.find(p => p.id === 'kitchen')?.lastRefresh) ? 'red' : 'blue'}">
                {pins.find(p => p.id === 'kitchen')?.hum}%
            </p>
        </div>

        <div id="bedroom1" class="pin" style="position: absolute; left: 63%; top: 14%; transform: translate(-50%, -50%);">
            <p style="color: {isOlderThan5Minutes(pins.find(p => p.id === 'bedroom1')?.lastRefresh) ? 'red' : 'blue'}">
                {pins.find(p => p.id === 'bedroom1')?.temp}°C
            </p>
            <p style="color: {isOlderThan5Minutes(pins.find(p => p.id === 'bedroom1')?.lastRefresh) ? 'red' : 'blue'}">
                {pins.find(p => p.id === 'bedroom1')?.hum}%
            </p>
        </div>
        <div id="bedroom2" class="pin" style="position: absolute; left: 87%; top: 14%; transform: translate(-50%, -50%);">
            <p style="color: {isOlderThan5Minutes(pins.find(p => p.id === 'bedroom2')?.lastRefresh) ? 'red' : 'blue'}">
                {pins.find(p => p.id === 'bedroom2')?.temp}°C
            </p>
            <p style="color: {isOlderThan5Minutes(pins.find(p => p.id === 'bedroom2')?.lastRefresh) ? 'red' : 'blue'}">
                {pins.find(p => p.id === 'bedroom2')?.hum}%
            </p>
        </div>
        <div id="office" class="pin" style="position: absolute; left: 69%; top: 48%; transform: translate(-50%, -50%);">
            <p style="color: {isOlderThan5Minutes(pins.find(p => p.id === 'office')?.lastRefresh) ? 'red' : 'blue'}">
                {pins.find(p => p.id === 'office')?.temp}°C
            </p>
            <p style="color: {isOlderThan5Minutes(pins.find(p => p.id === 'office')?.lastRefresh) ? 'red' : 'blue'}">
                {pins.find(p => p.id === 'office')?.hum}%
            </p>
        </div>
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