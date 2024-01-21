<script>
  import NavBar from "$lib/components/NavBar.svelte";
  import { onMount } from 'svelte';
  import  {createClient} from "@supabase/supabase-js";
  
  // import  session  from '$app/stores';
  const supabase = createClient("https://dgmvlwyaxmqsoixhuaoz.supabase.co", "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImRnbXZsd3lheG1xc29peGh1YW96Iiwicm9sZSI6ImFub24iLCJpYXQiOjE3MDUwNjIxOTYsImV4cCI6MjAyMDYzODE5Nn0.bsQfkT84K2_Qjcqe8C4J6RKkvHy1BY1Rpd1jZSNgy4Q");
  let userId = '1'; // User ID for the current user
  let otherUserId = '2'; // ID of the other user

  let messageInput = '';
  let chats = [];

  onMount(async () => {
    // Fetch initial chats when the component is mounted
    await getChats();
  });

  async function getChats() {
    try {
      const response = await fetch(`http://localhost:3020/getChats/${userId}`);
      const data = await response.json();
      chats = data;
    } catch (error) {
      console.error('Error fetching chats:', error);
    }
  }

  async function sendMessage() {
  try {
    const response = await fetch('http://localhost:3020/sendMessage', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        user1Id: userId,
        user2Id: otherUserId,
        message: messageInput,
      }),
    });

    // Check if the response is successful (status code 200)
    if (response.ok) {
      const newMessage = await response.json();

      // Update the UI with the new message
      chats = [newMessage, ...chats];

      // Clear the message input
      messageInput = '';
    } else {
      console.error('Error sending message:', response.statusText);
    }
  } catch (error) {
    console.error('Error sending message:', error);
  }
}
</script>

<svelte:head>
  <title>Match Page</title>
  <meta name="description" content="User Profile Page" />
</svelte:head>

<header>
  <NavBar />
</header>

<style>
  /* Add your styles here */
   .def {
    --tw-bg-opacity: 1;
    border-radius: 0.5rem /* 8px */;
    background-color: rgb(255 255 255 / var(--tw-bg-opacity));
    border-width: 2px;
    --tw-drop-shadow: drop-shadow(0 1px 1px rgb(0 0 0 / 0.05));
    filter: var(--tw-blur) var(--tw-brightness) var(--tw-contrast)
      var(--tw-grayscale) var(--tw-hue-rotate) var(--tw-invert)
      var(--tw-saturate) var(--tw-sepia) var(--tw-drop-shadow);
    --tw-shadow-color: rgb(59 130 246 / 0.2);
    --tw-shadow: var(--tw-shadow-colored);
}


  #chat-container {
    display: flex;
    height: 100vh;
  }

  #chat-content {
    flex: 3;
    padding: 20px;
    overflow-y: auto;
  }

  #message-input-container {
    display: flex;
    align-items: center;
    padding: 10px;
    background-color: #007bff;
  }

   #message-input {
    flex: 1;
    padding: 10px;
    margin-right: 10px;
    border: none;
    border-radius: 5px;
    color: #080808; 
  } 

  #send-button {
    padding: 10px;
    background-color: #007bff; 
    color: #fff;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }

  .message-bubble {
    background-color: #e1f5fe; 
    border-radius: 10px;
    padding: 10px;
    margin-bottom: 10px;
    color: #000; 
  }
  .incoming-message {
    background-color: #fff;
    text-align: right;
  }
</style>

<div id="chat-container">
  <div id="chat-content">
    {#each chats as chat (chat.id)}
      {#if chat.user_id_1 !== userId}
        <div class="incoming-message message-bubble" key={`incoming_${userId}_${chat.id}`}>
          <span>{chat.message}</span>
        </div>
      {:else}
        <div class="message-bubble" key={`outgoing_${userId}_${chat.id}`}>
          <span>{chat.message}</span>
        </div>
      {/if}
    {/each}
  </div>
</div>





<div id="message-input-container">
  <input bind:value={messageInput} id="message-input" placeholder="Type your message" />
  <button on:click={sendMessage} id="send-button">Send</button>
</div>