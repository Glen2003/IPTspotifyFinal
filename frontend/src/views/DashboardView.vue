<template>
  <section class="app-main">
    <div class="min-h-screen flex flex-col bg-black text-white">
      <div class="container mx-auto flex flex-grow justify-center bg-neutral-900 border-8 border-black">
        <nav class="lg:w-1/4 p-9 bg-neutral-900 shadow-lg border-8 border-black rounded-lg overflow-y-auto fixed top-0 left-0 z-50 h-41">
          <ul>
            <li class="mb-4">
              <a href="#" class="flex items-center text-white">
                <svg xmlns="http://www.w3.org/2000/svg" width="10" height="10" viewBox="0 0 24 24" class="w-6 h-6 mr-4" fill="currentColor">
                  <path fill-rule="evenodd" d="M12 2L1 12h3v10h16v-10h3L12 2zm0 2.83L18.17 11H5.83L12 4.83zm-1 4.68V13h2v-1.5h2V13h2V9.51L12 7.51z" fill="#ffffff"/>
                </svg>
                Home
              </a>
            </li>
            <li class="mb-4">
              <a href="#" @click="toggleSearch" class="flex items-center text-white">
                <svg xmlns="http://www.w3.org/2000/svg" width="10" height="10" viewBox="0 0 50 50" class="w-6 h-6 mr-4" fill="currentColor">
                  <path fill-rule="evenodd" d="M21 3C11.6 3 4 10.6 4 20C4 29.4 11.6 37 21 37C24.354553 37 27.47104 36.01984 30.103516 34.347656L42.378906 46.621094L46.621094 42.378906L34.523438 30.279297C36.695733 27.423994 38 23.870646 38 20C38 10.6 30.4 3 21 3zM21 7C28.2 7 34 12.8 34 20C34 27.2 28.2 33 21 33C13.8 33 8 27.2 8 20C8 12.8 13.8 7 21 7z" fill="#ffffff"/>
                </svg>
                Search
              </a>
            </li>
          </ul>
          <nav class="lg:w-1/4 p-9 bg-neutral-900 shadow-lg border-8 border-black rounded-lg overflow-y-auto fixed left-0 top-36 z-50 h-full max-h-90">
            <ul>
              <li class="mb-4">
                <a href="#" class="flex items-center text-white">
                  <svg xmlns="http://www.w3.org/2000/svg" width="10" height="10" viewBox="0 0 24 24" class="w-6 h-6 mr-4" fill="currentColor">
                    <path d="M12 4.5h1v15h-1z"/>
                    <path d="M17 4.5h1v15h-1z"/>
                    <path d="M7 4.5h1v15H7z"/>
                  </svg>
                  Your Library
                </a>
              </li>
              <li>
                <button class="fixed bg-green-500 bg-zinc-800 text-white py-2 px-4 rounded-full shadow-lg">
                  Playlist
                </button>
                <span><button class="fixed left-32 bg-green-500 bg-zinc-800 text-white py-2 px-4 rounded-full shadow-lg">
                  Artists
                </button></span>
              </li>
              <li>
                <button @click="toggleChat" class="fixed bottom-12 bg-zinc-800 hover:bg-black text-white py-2 px-4 rounded-full shadow-lg">
                  Group Message
                </button>
              </li>
            </ul>
          </nav>
        </nav>
        <div class="w-4/6 p-20">
          <input v-model="searchQuery" v-show="showSearch" type="text" placeholder="What do you want to play?" class="sticky top-0 z-50 text-center w-1/2 p-3 m-3 rounded-full bg-zinc-800 text-white placeholder-gray-400 focus:outline-none focus:bg-gray-700 mb-4 border border-black">
          <p class="sticky top-0 z-50 cursor-pointer inline-block"><span class="h-8 w-8 p-5 flex items-center justify-center rounded-full bg-gray-700 text-white sticky top-0 z-50 cursor-pointer inline-block">
            {{ username.charAt(0) }}</span></p><button @click="logout" class="sticky top-0 z-50 bg-green-700 hover:bg-green-600 rounded-full px-4 py-2 ml-4 inline-block">Logout</button>
          <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4 mt-4">
            <div v-for="track in tracks" :key="track.id" class="bg-gray-800 rounded-lg overflow-hidden shadow-lg">
              <iframe :src="generateEmbedUrl(track.id)" width="100%" height="250" frameborder="0" allowfullscreen="true" allow="clipboard-write; encrypted-media; fullscreen; picture-in-picture" loading="lazy"></iframe>
              <div class="p-3">
                <div class="font-bold text-lg">{{ track.name }}</div>
                <p class="text-gray-400">{{ track.artists[0].name }}</p>
                <button @click="shareMusic(track)" class="bg-zinc-800 text-white py-1 px-3 rounded-full mt-2">Share Music</button>
              </div>
            </div>
          </div>
        </div>
        <div v-if="showChat" class="fixed right-0 z-10 bg-zinc-800 text-black w-full md:w-96 h-full p-4 border-4 border-black rounded-lg" style="bottom: -17rem;">
          <button @click="toggleChat" class="absolute top-2 right-2 text-black hover:text-gray-300 focus:outline-none">
            <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
            </svg>
          </button>
          <ChatComponent/>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import ChatComponent from '@/components/ChatComponent.vue';
import { ref, onMounted, watch } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';
import Swal from 'sweetalert2';
import { io } from 'socket.io-client'; // Import socket.io-client

const searchQuery = ref('');
const tracks = ref([]);
const isLoggedIn = ref(false);
const message = ref('');
const username = ref('');
const router = useRouter();
const showChat = ref(false);
const showSearch = ref(false);

const toggleChat = () => {
  showChat.value = !showChat.value;
};

const toggleSearch = () => {
  showSearch.value = !showSearch.value;
};

const search = async (query) => {
  try {
    const response = await axios.get('http://localhost:3000/search', {
      params: { q: query }
    });
    tracks.value = response.data.tracks.items;
  } catch (error) {
    console.error('Error searching tracks:', error);
  }
};

const generateEmbedUrl = (trackId) => {
  return `https://open.spotify.com/embed/track/${trackId}`;
};

const logout = () => {
  console.log("Logged out!");
  localStorage.removeItem('token');
  router.push('/');
  Swal.fire({
    icon: 'success',
    title: 'Logged out successfully',
    position: 'top',
    showConfirmButton: false,
    timer: 1500,
    customClass: { popup: 'my-swal-popup', title: 'my-swal-title' }
  });
};

const shareMusic = (track) => {
  const musicTemplate = `
    <div class="bg-gray-800 rounded-lg overflow-hidden shadow-lg p-3">
      <iframe src="${generateEmbedUrl(track.id)}" width="100%" height="250" frameborder="0" allowfullscreen="true" allow="clipboard-write; encrypted-media; fullscreen; picture-in-picture"></iframe>
      <div class="font-bold text-lg">${track.name}</div>
      <p class="text-gray-400">${track.artists[0].name}</p>
    </div>`;

  const token = localStorage.getItem('token');
  if (token) {
    const socket = io('http://localhost:3000', {
      auth: {
        token: token
      }
    });

    // Logging the connection status
    socket.on('connect', () => {
      console.log('Socket connected:', socket.id);
      socket.emit('chat message', { text: musicTemplate, sender: username.value });
    });

    socket.on('disconnect', (reason) => {
      console.log('Socket disconnected:', reason);
      if (reason === 'io server disconnect') {
        console.log('Disconnected by server due to failed authentication');
      }
    });

    socket.on('connect_error', (error) => {
      console.error('Connection Error:', error);
    });
  } else {
    console.error('User is not authenticated');
  }
};

onMounted(async () => {
  try {
    const response = await axios.get('http://localhost:3000/dashboard', {
      headers: { Authorization: `Bearer ${localStorage.getItem('token')}` }
    });
    message.value = response.data.message;
    isLoggedIn.value = true;

    const token = localStorage.getItem('token');
    const decodedToken = JSON.parse(atob(token.split('.')[1]));
    if (decodedToken) username.value = decodedToken.username;
  } catch (error) {
    console.error('Error fetching dashboard message:', error);
  }
});

watch(searchQuery, (newValue) => {
  if (newValue.trim() !== '') {
    search(newValue);
  } else {
    tracks.value = [];
  }
});
</script>
