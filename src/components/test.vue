<template>
    <div class="refresh-infinite">

        <simple-scroll v-if="imgList.length" :on-refresh="refresh" :on-infinite="infinite" :distance="0">
            <ul>
                <li v-for="item in imgList">
                    <img :src="item.img" alt="">
                </li>
            </ul>
        </simple-scroll>
    </div>
</template>

<script>
    import Qs from 'qs';
    const PAGESIZE = 5;
    export default {
        data () {
            return {
                list: 50,
                pageNo: 0,
                imgList: {}
            };
        },
        created () {
            this.getData();
        },
        components: {},
        methods: {
            refresh (done) {
                console.log('refresh');
                this.getData(() => {
                    this.pageNo = 0;
                    done();
                }, false, true);
                
            },
            infinite (done) {
                console.log('infinite');
                this.getData((isNoData) => {
                    done(isNoData);
                }, true);
            },
            getData (callback, isInfinite, isRefresh) {
                let params = {};
                params.pageSize = PAGESIZE;
                params.pageNo = isInfinite ? ++this.pageNo : 0;
                params = Qs.stringify(params);
                // console.log(this.$http.post);
                this.$http.post('/api/imgList', params).then((res) => {
                    if (res.status === 200) {
                        res = res.data;
                        console.log(res)
                        if (res.code === '000000') {
                            let isNoData = res.data.isLast;
                            if (isInfinite) {
                                this.imgList = this.imgList.concat(res.data.imgList);
                            } else {
                                this.imgList = res.data.imgList;
                            }
                            console.log(this.imgList);

                            callback && callback(isNoData);
                        }
                    }
                }).catch((err) => {
                    console.log(err);
                })
            }
        }
    };
</script>

<style>
    .refresh-infinite{
        height: 100vh;
        overflow: hidden;
    }
    .refresh-infinite ul li img{
        width: 100%;
        height: 100%;
    }
        
</style>
