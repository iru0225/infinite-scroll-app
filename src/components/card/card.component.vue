<template>
  <div class="card-item">
      <img class="card-image" :src="data.download_url"/>
      <span class="card-author">Author: {{data.author}}</span>
  </div>
</template>

<script>
export default {
    props: [
        'item'
    ],

    data() {
        return {
            data: this.item
        }
    },

    watch: {
        data: {
            immediate: true,
            deep: true,
            handler(newData, oldData){
                let th = this;
                th.data = newData ? newData : null;
            }
        }
    }
}
</script>

<style lang="scss" scoped>
    .card-item{
        width: 282px;
        height: 222px;
        margin: 0 auto;
        position: relative;
        background: black;

        .card-image{
            width: 100%;
            height: 100%;
            object-fit: contain;
        }

        .card-author{
            position: absolute;
            right: 0;
            bottom: 0;
            color: #fff;
            font-size: 12px;
            margin-right: 10px;
        }
    }
</style>