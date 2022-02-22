<div class="feed">
    {#each feed as feed_item}
        {#if feed_item.type === "message" && (!follower_only || feed_item.d.followRole === 2)}
            <Message message={feed_item.d} />
            <hr />
        {/if}
        {#if feed_item.type === "share"}
            <Message message={feed_item.d} />
            <hr />
        {/if}
        {#if feed_item.type === "gift"}
            <Message message={feed_item.d} />
            <hr />
        {/if}
        {#if feed_item.type === "follow"}
            <Message message={feed_item.d} />
            <hr />
        {/if}
    {/each}
</div>
<svelte:head>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/4.4.1/socket.io.min.js" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
</svelte:head>

<script>
    import { onMount } from "svelte";

    import Message from "../lib/message.svelte";

    let feed = [];
    let api_url = "wss://tiktok-chat.herokuapp.com/";
    let follower_only = false;

    function connect() {
        const urlParams = new URLSearchParams(window.location.search);
        const streamer = urlParams.get("streamer");

        let connection = new io("ws://localhost:5000");
        connection.emit("setUniqueId", streamer, {enableExtendedGiftInfo: true});

        connection.on("chat", (data) => {
            feed = [...feed, {"type": "message", "d": data}];
        });
        connection.on("social", (data) => {
            if (data.displayType === "pm_mt_guidance_share") {
                data.comment = `Shared`;
                data.userBadges.push({name: "System"});
                feed = [...feed, {"type": "share", "d": data}];
            } else {
                data.comment = `Followed`;
                data.userBadges.push({name: "System"});
                feed = [...feed, {"type": "follow", "d": data}];
            }
            console.log(data);
        });
        connection.on("gift", (data) => {
            data.comment = `Gifted ${data.gift.repeat_count}x${data.extendedGiftInfo.describe} (${data.extendedGiftInfo.diamond_count * data.gift.repeat_count} diamonds)`;
            data.userBadges.push({name: "System"});
            feed = [...feed, {"type": "gift", "d": data}];

            console.log(data);
        });

    }
    onMount(() => {
        connect();
    });
</script>

