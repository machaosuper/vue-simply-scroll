<template>
    <div class="test">
        <simple-scroll v-if="sceneData.length > 0" :on-refresh="refresh" :on-infinite="infinite" :distance="0" >
            <ul>
                <li v-for="item in sceneData">
                    <img :src="item.coverImg" alt="">
                </li>
            </ul>
        </simple-scroll>
    </div>
</template>

<script>
    // import TestTem from '../components/testTem.vue';
    export default {
        data () {
            return {
                list: 50,
                pageNo: 0,
                sceneData: {}
            };
        },
        created () {
            this.getData();
        },
        components: { TestTem },
        methods: {
            refresh (done) {
                setTimeout(() => {
                    done();
                }, 1000);
            },
            infinite (done) {
                console.log('infinite');
                this.getData(() => {
                    done();
                }, true);
            },
            getData (callback, isInfinite, isRefresh) {
                let params = {};
                params.pageSize = 4;
                params.pageNo = isInfinite ? ++this.pageNo : 0;
                window.api.post('index/scene.do', params, (res) => {
                    if (res.code === '000000') {
                        if (isInfinite) {
                            this.sceneData = this.sceneData.concat(res.data.allSceneInfo);
                        } else {
                            this.sceneData = res.data.allSceneInfo;
                        }
                        callback && callback();
                    } else {
                        window.toast(res.msg);
                    }
                });
            }
        }
    };
</script>

<style lang="less">
    .test{
        height: 100vh;
    }
</style>
