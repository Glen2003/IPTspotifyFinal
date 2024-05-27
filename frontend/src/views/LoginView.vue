<template>
  <div>
    <header class="bg-neutral-900 py-8">
      <div class="container mx-auto px-4 flex items-center justify-between">
        <!-- Logo -->
        <div class="flex items-center">
          <img src="/logo.png" alt="Logo" class="h-8 w-auto mr-1" />
          <h1 class="text-white text-2xl font-extrabold">Spotify</h1>
        </div>
        <!-- Navigation Links -->
        <nav class="space-x-4">
        </nav>
      </div>
    </header>
    <div class="min-h-screen flex items-center justify-center bg-zinc-800 py-12 px-4 sm:px-6 lg:px-8">
      <div class="max-w-md w-full">
        <div class="items-center bg-neutral-900 p-8 py-12 rounded-md shadow-md space-y-8 w-111">
          <div class="flex items-center justify-center">
            <img src="/logo.png" alt="Logo" class="h-8 w-auto mr-2" />
            <h1 class="text-white text-2xl font-semibold">Spotify</h1>
          </div>
          <h2 class="text-center text-2xl font-extrabold text-white">
            Login to Spotify
          </h2>
          <button type="submit" class="bg-zinc-800 text-white py-3 px-4 rounded-full w-full shadow-lg flex items-center justify-center space-x-2">
             <img src="/google.png" alt="Logo" class="h-8 w-auto " />
               <span>Continue with Apple</span>
                    </button>
              <button type="submit" class="bg-zinc-800 text-white py-3 px-4 rounded-full w-full shadow-lg flex items-center justify-center space-x-2">
             <img src="/fb.png" alt="Logo" class="h-8 w-auto " />
               <span>Continue with facebook</span>
                    </button>
              <button type="submit" class="bg-zinc-800 text-white py-3 px-4 rounded-full w-full shadow-lg flex items-center justify-center space-x-2">
             <img src="/pol.png" alt="Logo" class="h-8 w-auto " />
               <span>Continue with Apple</span>
                    </button>

              <p class="text-white">__________________________________________________________</p>
          <form @submit.prevent="login" class="space-y-6">
            <!-- Username Input -->
            <div class="rounded-md shadow-sm -space-y-px">
              <div>
                <label for="username" class="sr-only">Username</label>
                <input v-model="username" id="username" name="username" type="text" required
                  class="mb-8 appearance-none rounded-full relative block w-full px-3 py-3 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-full focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm"
                  placeholder="Username" />
              </div>
              <!-- Password Input -->
              <div>
                <label for="password" class="sr-only">Password</label>
                <input v-model="password" id="password" name="password" type="password" required
                  class="rounded-full appearance-none rounded-ful relative block w-full px-3 py-3 border border-gray-300 placeholder-gray-500 text-gray-900 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm"
                  placeholder="Password" />
              </div>
            </div>
            <!-- Sign In Button -->
            <div>
              <button type="submit"
                class=" rounded-full w-full flex justify-center py-3 px-4 border border-transparent text-sm font-medium text-white bg-green-500 hover:bg-green-600 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                Sign in
              </button>
            </div>
            <h1 class="font-bold text-white text-center">Dont have and account? <span><RouterLink class="text-green-400 hover:text-green-600" to="/register">Sign Up</RouterLink></span></h1>
            <!-- Error Message -->
            <div v-if="errorMessage" class="text-red-500 text-sm">
              {{ errorMessage }}
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Swal from "sweetalert2";

export default {
  data() {
    return {
      username: "",
      password: "",
      errorMessage: "",
    };
  },
  methods: {
    async login() {
      try {
        const response = await axios.post("http://localhost:3000/login", {
          username: this.username,
          password: this.password,
        });
        const token = response.data.token;
        localStorage.setItem("token", token); // Save token to localStorage
        this.$router.push("/dashboard"); // Redirect to dashboard
        Swal.fire({
          icon: "success",
          title: "Logged in successfully",
          showConfirmButton: false,
          timer: 1500,
          position: "top", // Move the SweetAlert to the top
          customClass: {
            container: "my-swal", // Apply custom class for styling
            popup: "my-swal-popup", // Apply custom class for styling
            content: "my-swal-content", // Apply custom class for styling
            title: "my-swal-title", // Apply custom class for styling
          },
        }); // Use SweetAlert for success message
      } catch (error) {
        console.error("Login error:", error.response);
        if (
          error.response &&
          error.response.data &&
          error.response.data.error
        ) {
          this.errorMessage = error.response.data.error; // Display error message from backend
        } else {
          this.errorMessage = "An error occurred. Please try again later."; // Default error message
        }
      }
    },
  },
};
</script>

<style scoped>
.my-swal {
  width: 200px; /* Adjust width as needed */
}

.my-swal-popup {
  width: 200px; /* Adjust width as needed */
}

.my-swal-content {
  padding: 10px; /* Adjust padding as needed */
}

.my-swal-title {
  font-size: 16px; /* Adjust font size as needed */
}
</style>
