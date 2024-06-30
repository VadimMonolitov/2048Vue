<template>
    <button class="restartButton"><slot></slot></button>
</template>

<script>
export default {

}
</script>

<style scoped>
.restartButton {
    font-family: "Clear Sans", Arial, sans-serif;
    font-size: 18px;
    font-weight: bold;
    background: #8f7a66;
    border: none;
    border-radius: 3px;
    padding: 0 20px;
    margin: 15px;
    text-decoration: none;
    color: #f9f6f2;
    height: 40px;
    line-height: 42px;
    cursor: pointer;
}
</style>