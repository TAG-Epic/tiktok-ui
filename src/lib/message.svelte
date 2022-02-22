<div class="message">
    {#if moderator}
        <span class="badge"><strong>[MOD]</strong></span>
    {/if}
    {#if new_gifter}
        <span class="badge"><strong>[GIFTER]</strong></span>
    {/if}
    {#if system}
        <span class="badge system"><strong>[SYSTEM]</strong></span>
    {/if}
    <span><strong>{message.uniqueId}</strong> {message.comment}</span>
</div>

<script>
    export let message;
    let moderator = false;
    let new_gifter = false;
    let system = false;
    if (message.userBadges !== undefined) {
        message.userBadges.forEach((badge) => {
            if (badge.name === 'Moderator') {
                moderator = true;
            } else if (badge.name === "New gifter") {
                new_gifter = true;
            } else if (badge.name === "System") {
                moderator = false;
                new_gifter = false;
                system = true;
            } else {
                console.log(`Unknown badge: ${badge.name}`);
            }
        });
    }
</script>

<style>
    .badge {
        color: #060;
    }
    .system {
        color: #f00;
    }
</style>
