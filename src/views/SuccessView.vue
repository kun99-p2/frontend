<template>
    <div style="display: flex; flex-direction:column; height: 80vh; justify-content: center; align-items: center;">
        <h1>Success</h1>
        <p >You will be redirected in a few seconds...</p>
    </div>
</template>
    
<script>
export default {
  mounted() {
    setTimeout(() => {
      this.$router.push('/');
    }, 5000);
  },
};
</script>
    