<template>
  <div class="flex items-center justify-between  text-white font-bold mb-4 p-2 mx-10 rounded-lg">
    <div class="text-center">Group Chat</div>

    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-gray-400 hover:text-gray-200 cursor-pointer" viewBox="0 0 24 24" fill="currentColor">
  <path d="M22 8h-4.18l-2-3h-5.64l-2 3H6a2 2 0 0 0-2 2v10a2 2 0 0 0 2 2h16a2 2 0 0 0 2-2V10a2 2 0 0 0-2-2zM12 16c-2.21 0-4-1.79-4-4s1.79-4 4-4 4 1.79 4 4-1.79 4-4 4z"/>
  <circle cx="12" cy="12" r="3"/>
</svg>


    </div>

  <div class="fixed bottom-0 justify-center p-4 ml-6">
    <div class="flex items-center">
      <input v-model="messageInput" type="text" placeholder="Enter a message" class="rounded-full text-center border border-black mb-4 h-8 w-full text-slate-950 mr-2">
      <button @click="sendMessage" class="bg-green-600 text-white text-md font-semibold rounded-full h-8 text-center mb-4">Send <span class="text-zinc-800">Message</span></button>
    </div>
  </div>

  <!-- Display messages -->
  <ul id="messages" class="mt-8 mx-2">
    <li v-for="msg in messages" :key="msg.id" :class="{ 'text-right': msg.sender === username, 'text-left': msg.sender !== username, 'text-blue-600': msg.sender === username, 'text-gray-600': msg.sender !== username, 'font-bold': true }">
      <!-- Use conditional rendering to style sent messages -->
      <div v-if="msg.sender === username" class="bg-green-300 rounded-lg py-2 px-4 text-center mb-2 inline-block">{{ msg.text }}</div>
      <!-- Use conditional rendering to style received messages -->
      <div v-else class="bg-gray-300 rounded-lg py-2 px-4 text-center mb-2 inline-block">{{ msg.sender && msg.sender !== username ? msg.sender + ': ' : '' }}{{ msg.text }}</div>
    </li>
  </ul>
</template>


<script>
import { onMounted, ref } from 'vue'; // Import ref and onMounted from Vue 3
import io from 'socket.io-client';

export default {
  setup() {
    const messageInput = ref(''); // Reactive variable for message input
    const messages = ref([]); // Reactive variable to store messages
    const socket = io('http://localhost:3000'); // Connect to your server
    const username = ref(''); // Reactive variable to store the username

    // Add authentication token logic
    const token = localStorage.getItem('token');
    if (token) {
      socket.emit('authenticate', token);
    }

    onMounted(() => {
      // Extract username from JWT token payload
      try {
        const token = localStorage.getItem('token');
        const decodedToken = JSON.parse(atob(token.split('.')[1])); // Decodes the payload part of the JWT token
        if (decodedToken) {
          username.value = decodedToken.username;
        }
      } catch (error) {
        console.error('Error fetching username from token:', error);
      }

      socket.on('connect', () => {
        console.log('Connected to server');
      });

      socket.on('chat message', (msg) => {
        messages.value.push({ id: Date.now(), text: msg.text, sender: msg.sender, isTemplate: msg.text.includes('<template>') });
        window.scrollTo(0, document.body.scrollHeight);
      });
    });

    const sendMessage = () => {
      if (messageInput.value.trim() !== '') {
        socket.emit('chat message', { text: messageInput.value.trim(), sender: username.value }); // Use the extracted username here
        messageInput.value = '';
      }
    };

    return { messageInput, sendMessage, messages, username };
  },
};
</script>




 
 







<!-- <template>
   <div class="text-center text-lg font-bold mb-4 w-full mx-2">Chat Box</div>
 
   <div class="fixed bottom-0 justify-center p-4 ml-6">
     <input v-model="messageInput" type="text" placeholder="Enter a message" class="rounded-full text-center border border-white mb-4 h-8 w-full">
     <button @click="sendMessage" class="bg-green-600 text-white text-md font-semibold rounded-full h-8 w-full text-center">Send Message</button>
   </div>
 </template>
 
 <script>
 import { onMounted, ref } from 'vue'; // Import ref and onMounted from Vue 3
 import io from 'socket.io-client';
 
 export default {
   setup() {
     const messageInput = ref(''); // Reactive variable for message input
     const socket = io('http://localhost:3000'); // Connect to your server
 
     onMounted(() => {
       socket.on('connect', () => {
         console.log('Connected to server');
       });
 
       socket.on('chat message', (msg) => {
         const item = document.createElement('li');
         item.textContent = msg;
         document.getElementById('messages').appendChild(item);
         window.scrollTo(0, document.body.scrollHeight);
       });
     });
 
     const sendMessage = () => {
       if (messageInput.value.trim() !== '') {
         socket.emit('chat message', messageInput.value.trim());
         messageInput.value = '';
       }
     };
 
     return { messageInput, sendMessage };
   },
 };
 </script>
  -->
 




 <!-- <template>
   <div class="text-center text-lg font-bold mb-4 w-full mx-2">Chat Box</div>

   <div class="fixed bottom-0 justify-center p-4 ml-6">
   <input type="text" placeholder="Enter a message" class="rounded-full text-center border border-white mb-4 h-8 w-full">
   <button class="bg-green-600 text-white text-md font-semibold rounded-full h-8 w-full text-center">Send Message</button>
</div>
</template> -->