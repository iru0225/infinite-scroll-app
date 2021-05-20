<template>
    <div>
        <div class="card-container">
            <div v-for="(item, index) in itemList" :key="index">
                <v-card :item="item"/>
            </div>
        </div>

        <div ref="infiniteScrollTrigger"></div>
        <div v-show="showLoader" class="loader">
            <div class="spiner"></div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

import CardComponent from '../card/card.component'
export default {
    data() {
        return {
            itemList: [],
            page: 1,
            url: 'https://picsum.photos/v2/list?',
            showLoader: false
        }
    },

    components: {
        'v-card' : CardComponent
    },

    methods:{
        scrollTrigger(){
            const observer = new IntersectionObserver(entries => {
                if (entries[0].isIntersecting && this.itemList.length != 0) {
                    this.page +=1;
                }
            });

            observer.observe(this.$refs.infiniteScrollTrigger);
        }
    },

    watch:{
        page: {
            immediate: true,
            deep: true,
            handler(newData){
               let th = this;

               th.showLoader = true;

               axios({
                   method: 'get',
                   url: `${th.url}page=${newData}&limit=100`
               }).then(response => {
                   let ids = new Set(th.itemList.map(e => e.id));

                   if (ids.size == 0) {
                       th.itemList = response.data;
                       th.showLoader = false;
                       return;
                   }

                   th.itemList = [...th.itemList, ...response.data.filter(d => !ids.has(d.id))];
                   th.showLoader = false
               }).catch(err => {
                   console.log(err);
               })
            }
        }
    },

    mounted(){
        this.scrollTrigger();
    }
}
</script>

<style lang="scss" scoped>
    .card-container{
        display: grid;
        margin: 0 auto;
        grid-template-columns: 1fr 1fr 1fr 1fr;
        grid-gap: 15px;
        width: 95%;
    }

    .loader{
            display: flex;
            justify-content: center;

            .spiner{
                width: 2rem;
                height: 2rem;
                border-radius: 50%;
                border: .3rem solid rgba($color: #979fd0, $alpha: .3);
                border-top-color: #979fd0;
                animation: 1.5s spin infinite linear;
            }
        }

        @keyframes spin {
            to{
                transform: rotate(360deg);
            }
        }
</style>