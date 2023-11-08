<template>
  <div class="buttons">
    <RouterLink class="button" style="margin-bottom: 10px;" to="/login">
      Login
    </RouterLink>
    <RouterLink class="button" to="/register">
      Register
    </RouterLink>
  </div>
</template>

<style>
.buttons {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 80vh;
}
.button {
  display: flex;
  background-color: #4CAF50;
  border: none;
  color: white;
  cursor: pointer;  
  width: 200px;
  height: 80px;
  font-size: 20px;
  text-decoration: none;
  justify-content: center;
  align-items: center;
}
</style>
