<!-- +page.svelte -->
<script>
    import { onMount } from 'svelte';
    import { useZero } from '@stolinski/zero-svelte'; 
    import { formatDate } from './date';

    // Initialize Zero
    const { zero, state } = useZero();
    const reactiveState = runes(state);

    let filterUser = '';
    let filterText = '';
    let action;
    let users = []; // Assuming you will fetch users from the state
    let allMessages = []; // Assuming you will fetch messages from the state
    let filteredMessages = [];

    onMount(() => {
        // Fetch users and messages from the state
        users = reactiveState.users || [];
        allMessages = reactiveState.messages || [];
        filteredMessages = allMessages; // Initialize with all messages
    });

    // Function to handle adding a message
    const handleAddAction = () => {
        // Implement your add message logic here
    };

    // Function to handle removing a message
    const handleRemoveAction = () => {
        // Implement your remove message logic here
    };

    // Function to filter messages
    const filterMessages = () => {
        filteredMessages = allMessages.filter(message => {
            const matchesUser = filterUser ? message.senderID === filterUser : true;
            const matchesText = filterText ? message.body.includes(filterText) : true;
            return matchesUser && matchesText;
        });
    };

    // Watch for changes in filters
    $: filterMessages();

    // Function to toggle login
    const toggleLogin = () => {
        // Implement your login/logout logic here
    };
</script>

<main>
    <h1>Welcome to the Svelte App</h1>
    <div class="controls">
        <div>
            <button on:mousedown={handleAddAction} on:mouseup={stopAction} on:mouseleave={stopAction}>
                Add Messages
            </button>
            <button on:mousedown={handleRemoveAction} on:mouseup={stopAction} on:mouseleave={stopAction}>
                Remove Messages
            </button>
            <em>(hold down buttons to repeat)</em>
        </div>
        <div style="justify-content: end;">
            {#if reactiveState.user === "anon"}
                <button on:mousedown={toggleLogin}>Login</button>
            {:else}
                <span>Logged in as {reactiveState.user}</span>
                <button on:mousedown={toggleLogin}>Logout</button>
            {/if}
        </div>
    </div>
    <div class="controls">
        <div>
            From:
            <select bind:value={filterUser}>
                <option value="">Sender</option>
                {#each users as user}
                    <option value={user.id}>{user.name}</option>
                {/each}
            </select>
        </div>
        <div>
            Contains:
            <input type="text" placeholder="message" bind:value={filterText} />
        </div>
    </div>
    <div class="controls">
        <em>
            {#if filteredMessages.length === 0}
                No posts found 😢
            {:else}
                Showing {filteredMessages.length} of {allMessages.length} messages.
            {/if}
        </em>
    </div>
    {#if filteredMessages.length > 0}
        <table border="1" cellspacing="0" cellpadding="6" width="100%">
            <thead>
                <tr>
                    <th>Sender</th>
                    <th>Medium</th>
                    <th>Message</th>
                    <th>Sent</th>
                    <th>Edit</th>
                </tr>
            </thead>
            <tbody>
                {#each filteredMessages as message}
                    <tr key={message.id}>
                        <td align="left">{message.sender?.name}</td>
                        <td align="left">{message.medium?.name}</td>
                        <td align="left">{message.body}</td>
                        <td align="right">{formatDate(message.timestamp)}</td>
                        <td on:mousedown={(e) => editMessage(e, message.id, message.senderID, message.body)}>
                            ✏️
                        </td>
                    </tr>
                {/each}
            </tbody>
        </table>
    {/if}
</main>