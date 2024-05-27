<template>
  <header class="bg-gray-800 py-4">
    <div class="container mx-auto px-4 flex items-center justify-between">
      <div class="text-white"/>
      <nav class="space-x-4">
        <RouterLink class="text-white hover:text-gray-300" to="/about">About</RouterLink>
        <RouterLink class="text-white hover:text-gray-300" to="/">Login</RouterLink>
        <RouterLink class="text-white hover:text-gray-300" to="/register">Register</RouterLink>
      </nav>
    </div>
  </header>
  <div class="about">
    <h1>This is an about page</h1>
  </div>
</template>

<style>
</style>
