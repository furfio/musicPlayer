<template>
    <div class="background">
        <div class="main">
            <div class="search">
                <input v-model="query" type="text" @keyup.enter="searchMusic"
                       placeholder="输入歌名、歌手,回车搜索"
                />
            </div>
            <div class="list">
                <ul>
                    <li v-for="item in musicList">
                        <button  type="image" v-on:click="playMusic(item.id)" ></button>&nbsp;&nbsp;&nbsp;{{item.name}}
                    </li>
                </ul>
            </div>

            <div class="player" :class="{playing:isPlaying}">
                <!-- 黑胶碟片 -->
                <img src="../../public/img/disc.png" class="disc autoRotate" />
                <img :src="musicCover" class="cover autoRotate" alt="">
            </div>

            <div class="audio">
                <audio @play="play" @pause="pause" ref="audio" :src="musicUrl" controls autoplay loop class="audioBar"></audio>
            </div>
        </div>
    </div>
</template>

<script>
    import axios from 'axios'
    export default {
        name: "Main",
        data(){
            return{
                query:"",
                musicList:[],
                musicUrl:'',
                musicCover:'../../public/img/cover.png',
                isPlaying:false
            }
        },
        methods:{
            searchMusic:function () {
                axios.get("https://autumnfish.cn/search?keywords="+this.query)
                    .then(
                        (response)=>{
                            this.musicList=response.data.result.songs
                            console.log(response)
                        },
                        (err)=>{console.log(err)}
                    )
            },
            playMusic:function (musicID) {
                console.log(musicID)
                axios.get("https://autumnfish.cn/song/url?id="+musicID)
                .then(
                    (res)=>{
                        this.musicUrl=res.data.data[0].url//貌似这里要是用this必须写到回调函数第一行，
                        // 否则就应该在playMusic函数一开始定义一个that=this，然后这里用that
                        console.log(res.data.data[0].url)

                    },
                    (err)=>{console.log(err)}
                )

                axios.get("https://autumnfish.cn/song/detail?ids="+musicID)
                .then(
                    (res)=>{
                        this.musicCover=res.data.songs[0].al.picUrl
                        // console.log("封面"+JSON.stringify(res))

                    },
                    (err)=>{console.log(err)}
                )
            },

            //利用v-on绑定audio的属性，即监听音乐播放器现在是否在播放
            play:function(){
                // console.log("开始播放")
                this.isPlaying=true
            },
            pause:function(){
                // console.log("暂停播放")
                this.isPlaying=false
            }


        }
    }
</script>

<style scoped>
    body{
        margin:0;
        padding:0;
    }
    .background{
        position: absolute;
        top:0px;
        left: 0px;
        width: 100%;
        height: 750px;
        background-color: bisque;
        z-index: 1;
    }
    .background .main{
        margin-top: 100px;
        width: 80%;
        height:570px;
        margin-left: 10%;
        z-index: 2;
        background-size: 100% auto;
        background-repeat: no-repeat;
        background-image: url("../../public/img/bg.png");
    }
    .search{
        width: 100%;
        z-index: 3;
    }
    .search input{
        border-radius: 10px;
        margin-left: 12%;
        margin-top: 20px;
        padding: 3px;
        height: 30px;
        font-size: large;
        width: 75%;
        background-color:rgba(255,255,255,0.5);
    }
    li{
        list-style:none;
        margin: 10px;
        padding: 10px;
        border-bottom: 1px solid;
    }
    ul{
        padding: 0;
    }
    .list button:hover{
        cursor:pointer;
    }
    .list button{
        border: 0;
        background-color:rgba(255,255,255,0);
        width: 20px;
        height: 20px;
        background-size: 100% 100%;
        background-repeat: no-repeat;
        background-image: url("../../public/img/play.png");
    }
    .list{
        background-color:rgba(255,255,255,0.5);
        margin-left: 100px;
        margin-top: 60px;
        overflow:scroll;
        overflow-x:hidden;
        width:40%;
        height: 300px;
        float: left;
        z-index: 3;
    }
    .player{
        margin-top: 80px;
        width: 37%;
        float: right;
        z-index: 3;
    }
    .audio{
        margin-top: 480px;
        width: 100%;
        z-index: 3;
    }
    .audio .audioBar{
        padding-left: 5%;
        height: 30px;
        width: 90%;
    }
    .disc {
        padding-left: 0px;
        padding-top: 0px;
        z-index: 9;
    }
    .cover {
        position: absolute;
        top:295px;
        left:970px;
        width: 150px;
        height: 150px;
        border-radius: 75px;
        z-index: 8;
    }
    /* 旋转的动画 */
    @keyframes Rotate {
        from {
            transform: rotateZ(0);
        }
        to {
            transform: rotateZ(360deg);
        }
    }
    /* 旋转的类名 */
    .autoRotate {
        animation-name: Rotate;
        animation-iteration-count: infinite;
        animation-play-state: paused;
        animation-timing-function: linear;
        animation-duration: 5s;
    }
    /* 是否正在播放 */
    .player.playing .disc,
    .player.playing .cover {
        animation-play-state: running;
    }
</style>