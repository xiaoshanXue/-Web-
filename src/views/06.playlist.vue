<template>
  <div class="playlist-container">
    <div class="top-wrap">
      <div class="img-wrap">
        <img :src="playlist.coverImgUrl" alt="" />
      </div>
      <div class="info-wrap">
        <p class="title">{{ playlist.name }}</p>
        <div class="author-wrap">
          <img class="avatar" :src="playlist.creator.avatarUrl" alt="" />
          <span class="name">{{ playlist.creator.nickname }}</span>
          <span class="time">{{ createTime }} 创建</span>
        </div>
        <!-- <div class="play-wrap">
          <span class="iconfont icon-circle-play"></span>
          <span class="text">播放全部</span>
        </div> -->
        <div class="tag-wrap">
          <span class="title">标签:</span>
          <ul>
            <li v-for="(item, index) in playlist.tags" :key="index">
              {{ item }}
            </li>
          </ul>
        </div>
        <div class="desc-wrap">
          <span class="title">简介:</span>
          <span class="desc">{{ playlist.description }}</span>
        </div>
      </div>
    </div>
    <el-tabs v-model="activeIndex">
      <el-tab-pane label="歌曲列表" name="1">
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
            <tr
              class="el-table__row"
              v-for="(item, index) in tracks"
              :key="index"
            >
              <td>{{ index + 1 }}</td>
              <td>
                <div class="img-wrap">
                  <img :src="item.al.picUrl" alt="" />
                  <span
                    class="iconfont icon-play"
                    @click="playMusic(item.id)"
                  ></span>
                </div>
              </td>
              <td>
                <div class="song-wrap">
                  <div class="name-wrap">
                    <span v-text="item.name"></span>
                    <span class="iconfont icon-mv"></span>
                  </div>
                  <span>{{ item.alia[0] }} </span>
                </div>
              </td>
              <td>{{ item.ar[0].name }}</td>
              <td>{{ item.al.name }}</td>
              <td>{{ item.dt | format }}</td>
            </tr>
          </tbody>
        </table>
      </el-tab-pane>
      <el-tab-pane :label="'评论' + hotCount" name="2">
        <!-- 精彩评论 -->
        <div class="comment-wrap">
          <p class="title">
            精彩评论<span class="number">({{ hotCount }})</span>
          </p>
          <div class="comments-wrap">
            <div class="item" v-for="(item, index) in hotComment" :key="index">
              <div class="icon-wrap">
                <img :src="item.user.avatarUrl" alt="" />
              </div>
              <div class="content-wrap">
                <div class="content">
                  <span class="name">{{ item.user.nickname }}：</span>
                  <span class="comment">{{ item.content }}</span>
                </div>
                <div class="re-content" v-if="item.beReplied.length != 0">
                  <span class="name"
                    >{{ item.beReplied[0].user.nickname }}：</span
                  >
                  <span class="comment"> {{ item.beReplied[0].content }} </span>
                </div>
                <div class="date" v-text="commentTime(index)"></div>
              </div>
            </div>
          </div>
        </div>
        <!-- 最新评论 -->
        <div class="comment-wrap">
          <p class="title">
            最新评论<span class="number">( {{ total }} )</span>
          </p>
          <div class="comments-wrap">
            <div class="item" v-for="(item, index) in comments" :key="index">
              <div class="icon-wrap">
                <img :src="item.user.avatarUrl" alt="" />
              </div>
              <div class="content-wrap">
                <div class="content">
                  <span class="name"> {{ item.user.nickname }} ：</span>
                  <span class="comment">{{ item.content }}</span>
                </div>
                <div class="re-content" v-if="item.beReplied.length != 0">
                  <span class="name"
                    >{{ item.beReplied[0].user.nickname }}：</span
                  >
                  <span class="comment">{{ item.beReplied[0].content }}</span>
                </div>
                <div class="date" v-text="commentTime1(index)"></div>
              </div>
            </div>
          </div>
        </div>
        <!-- 分页器 -->
        <el-pagination
          @current-change="handleCurrentChange"
          background
          layout="prev, pager, next"
          :total="total"
          :current-page="page"
          :page-size="5"
        >
        </el-pagination>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "playlist",
  data() {
    return {
      activeIndex: "1",
      // 总条数
      total: 0,
      // 页码
      page: 1,
      playlist: {},
      tracks: [],
      hotComment: [],
      hotCount: 0,
      comments: [],
    };
  },
  methods: {
    handleCurrentChange(val) {
      this.page = val;
      axios({
        url: "https://autumnfish.cn/comment/playlist",
        method: "get",
        params: {
          id: this.$route.query.q,
          limit: 10,
          offset: (this.page - 1) * 10,
        },
      }).then((res) => {
        this.total = res.data.total;
        this.comments = res.data.comments;
      });
    },
    getData(n) {
      let now = new Date(n),
        y = now.getFullYear(),
        m = now.getMonth() + 1,
        d = now.getDate();
      return (
        y +
        "-" +
        (m < 10 ? "0" + m : m) +
        "-" +
        (d < 10 ? "0" + d : d) +
        " " +
        now.toTimeString().substr(0, 8)
      );
    },
    commentTime(n) {
      return this.getData(this.hotComment[n].time);
    },
    commentTime1(n) {
      return this.getData(this.comments[n].time);
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
  filters: {
    format(val) {
      let min = parseInt(val / 1000 / 60);
      let sec = parseInt((val / 1000) % 60);
      if (min < 10) {
        min = "0" + min;
      }
      if (sec < 10) {
        sec = "0" + sec;
      }
      return min + ":" + sec;
    },
  },
  computed: {
    createTime() {
      return this.getData(this.playlist.createTime);
    },
  },
  created() {
    axios({
      url: "https://autumnfish.cn/playlist/detail",
      method: "get",
      params: {
        id: this.$route.query.q,
        type: 2,
      },
    }).then((res) => {
      this.playlist = res.data.playlist;
      this.tracks = res.data.playlist.tracks;
      console.log(this.tracks);
    });

    axios({
      url: "https://autumnfish.cn/comment/playlist",
      method: "get",
      params: {
        id: this.$route.query.q,
        limit: 10,
        offset: 0,
      },
    }).then((res) => {
      this.hotComment = res.data.hotComments;
      this.hotCount = res.data.total;
    });

    axios({
      url: "https://autumnfish.cn/comment/playlist",
      method: "get",
      params: {
        id: this.$route.query.q,
        limit: 10,
        offset: 0,
      },
    }).then((res) => {
      this.total = res.data.total;
      this.comments = res.data.comments;
    });
  },
};
</script>

<style >
td {
  text-align: center;
}
.playlit-table {
  margin-bottom: 60px;
}
</style>
