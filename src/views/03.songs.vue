<template>
  <div class="songs-container">
    <div class="tab-bar">
      <span class="item" @click="tag = 0" :class="{ active: tag == 0 }"
        >全部</span
      >
      <span class="item" @click="tag = 7" :class="{ active: tag == 7 }"
        >华语</span
      >
      <span class="item" @click="tag = 96" :class="{ active: tag == 96 }"
        >欧美</span
      >
      <span class="item" @click="tag = 8" :class="{ active: tag == 8 }"
        >日本</span
      >
      <span class="item" @click="tag = 16" :class="{ active: tag == 16 }"
        >韩国</span
      >
    </div>
    <!-- 底部的table -->
    <table class="el-table playlit-table">
      <thead>
        <th></th>
        <th></th>
        <th>音乐标题</th>
        <th>歌手</th>
        <th>专辑</th>
        <th>时长</th>
      </thead>
      <tbody>
        <tr class="el-table__row" v-for="(item, index) in lists" :key="index">
          <td>{{ index + 1 }}</td>
          <td>
            <div class="img-wrap">
              <img v-lazy="item.album.picUrl" alt="" />
              <span
                @click="playMusic(item.id)"
                class="iconfont icon-play"
              ></span>
            </div>
          </td>
          <td>
            <div class="song-wrap">
              <div class="name-wrap">
                <span>{{ item.name }}</span>
                <span class="iconfont icon-mv"></span>
              </div>
              <span>电视剧加油练习生插曲</span>
            </div>
          </td>
          <td>{{ item.album.artists["0"].name }}</td>
          <td>{{ item.album.name }}</td>
          <td>{{ item.duration }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "songs",
  data() {
    return {
      lists: [],
      tag: "0",
    };
  },
  watch: {
    tag() {
      this.getList();
    },
  },

  created() {
    this.getList();
    console.log("重新加载");
  },
  methods: {
    getList() {
      axios({
        url: "https://autumnfish.cn/top/song",
        method: "get",
        params: {
          type: this.tag,
        },
      }).then((res) => {
        // console.log(res);
        this.lists = res.data.data;

        for (let i = 0; i < this.lists.length; i++) {
          let duration = this.lists[i].duration;
          let min = parseInt(duration / 1000 / 60);
          if (min < 10) {
            min = "0" + min;
          }
          let sec = parseInt((duration / 1000) % 60);
          if (sec < 10) {
            sec = "0" + sec;
          }
          this.lists[i].duration = `${min}:${sec}`;
        }
      });
    },
    playMusic(id) {
      axios({
        url: "https://autumnfish.cn/song/url",
        method: "get",
        params: {
          id,
        },
      }).then((res) => {
        console.log(res);
        let url = res.data.data[0].url;
        this.$parent.musicUrl = url;
      });
    },
  },
};
</script>

<style >
.el-table tr {
  height: 100px;
}
.el-table td {
  white-space: nowrap;
}
</style>
