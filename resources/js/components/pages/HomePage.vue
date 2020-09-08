<template>
    <div class="container">
        <h4>Welcome to Money Saver</h4>
    </div>
</template>

<script>
    export default {
        mounted() {
            this.$parent.checkLogin();
        }
    }
</script>
