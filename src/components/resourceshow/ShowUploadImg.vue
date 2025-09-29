<template>
    <div>
        <!-- <input type="file" @change="onUploadPngFile"/> -->
        <!-- <button></button> -->
    </div>
</template>
<script>
export default {
    name:"ShowUploadImg",
    data() {
        return {
            "pngBaseUrl":"",
        };
    },
    methods: {
        onUploadPngFile(e) {
            console.log(e);
        },
    }
}
</script>
<style lang="css">
</style>