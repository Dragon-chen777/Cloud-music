<template>
  <div class="play-mv">
    <scroll
      class="play-mv-scroll"
      ref="scroll"
      :pull-up-load="true"
      @pullingUp="pullingUp()"
    >
      <div class="layout-left">
        <!-- 标题 -->
        <div class="title" v-if="detail != null">
          <div class="left-mv">MV</div>
          <div class="name">{{ detail.name }}</div>
          <div class="artist">{{ detail.artistName }}</div>
        </div>
        <!-- 视频 -->
        <div class="video" v-if="url != null">
          <video
            :src="url"
            controls
            autoplay
            width="100%"
            class="video-play"
          ></video>
        </div>
        <!-- 评论 -->
        <div class="recommend">
          <p class="p">评论</p>
          <recommends class="rec" :recommends="recommends" />
        </div>
      </div>

      <div class="right">
        <div class="desc">
          <p class="p">MV介绍</p>
          <div class="base" v-if="detail!=null">
            <div class="date">发布时间：{{ detail.publishTime }}</div>
            <div class="playCount">播放次数：{{ detail.playCount }}次</div>
            <div class="clear"></div>
          </div>
          <div
            class="mv-desc"
            ref="mvDesc"
            v-if="detail!= null"
            @mouseenter="enter()"
            @mouseleave="leave()"
          >
            <scroll class="desc-scroll" ref="descScroll">
              <span>简介：</span>
              {{ detail.desc }}
            </scroll>
          </div>
        </div>
        <!-- 相关推荐 -->
        <div class="alia">
          <p class="p">相关推荐</p>
          <simi-mv-item :mv-list="simiMv" />
        </div>
      </div>
      <div class="clear"></div>
    </scroll>
  </div>
</template>

<script>
import Scroll from "components/common/scroll/Scroll";
import Recommends from "views/musicListDetail/childComps/Recommends";
import SimiMvItem from "views/mv/childComps/SimiMvItem";

import {
  _getMvDetail,
  _getMVUrl,
  _getMVComment,
  _getSimiMv,
  MV,
} from "network/mv";
export default {
  name: "PlayMV",
  data() {
    return {
      id: null,
      // mv详细信息
      detail: null,
      // mv播放路径
      url: null,
      recommends: null,
      offset: 1,
      limit: 20,
      // 相关推荐MV
      simiMv: [],
    };
  },
  components: {
    Scroll,
    Recommends,
    SimiMvItem,
  },
  watch: {
    $route() {
      this.id = this.$route.params.id;
      if (this.id != null) {
        this.getBaseInfo();
      }
    },
  },
  created() {
    this.id = this.$route.params.id;
    if (this.id != null) {
      this.getBaseInfo();
    }
  },
  methods: {
    pullingUp() {
      this.getComment();
    },
    getBaseInfo() {
      _getMvDetail(this.id).then((res) => {
        this.detail = res.data.data;
        return;
      });
      _getMVUrl(this.id).then((res) => {
        this.url = res.data.data.url;
        return;
      });
      _getMVComment(this.id, this.limit).then((res) => {
        this.recommends = res.data.comments;
        return;
      });
      _getSimiMv(this.id).then((res) => {
        console.log(res);
        for (let item of res.data.mvs) {
          let mv = new MV(
            item.id,
            item.cover,
            item.name,
            item.artistName,
            item.playCount
          );
          this.simiMv.push(mv);
          // if (item == res.data.mvs.length - 1) {
          //   return;
          // }
        }
      });
    },
    getComment() {
      _getMVComment(this.id, this.offset * this.limit).then((res) => {
        this.recommends = res.data.comments;
        this.offset++;
        this.$refs.scroll.finishPullUp();
      });
    },
    enter() {
      this.$refs.scroll.disable();
    },
    leave() {
      this.$refs.scroll.enable();
    },
  },
};
</script>

<style scoped>
.play-mv {
  width: 100%;
  height: 100%;
  padding: 0px 10%;
  overflow: hidden;
}
.play-mv-scroll {
  height: 100%;
}
.layout-left {
  display: inline-block;
  width: 70%;
}
.video-play {
  outline-style: none;
}
.right {
  float: right;
  width: 30%;
  padding-left: 20px;
  padding-top: 10px;
}
.clear {
  clear: both;
}
.layout-left .title {
  display: flex;
  align-items: flex-end;
  margin-bottom: 10px;
}
.title .left-mv {
  padding: 2px;
  display: inline-block;
  border: 1px solid red;
  color: red;
  border-radius: 3px;
}
.title .name {
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
  font-size: 18px;
  margin-left: 5px;
}
.title .artist {
  color: var(--color-text-an);
  margin-left: 5px;
}
.p {
  font-size: 18px;
  color: var(--color-text-tint);
  margin-bottom: 10px;
}
.base {
  font-size: 12px;
  color: var(--color-text-an);
  margin-bottom: 10px;
}
.base .date {
  float: left;
}
.base .playCount {
  float: right;
}
.mv-desc {
  max-height: 288px;
  width: 100%;
  overflow: hidden;
  line-height: 1.2em;
  font-size: 13px;
  color: var(--color-text-an);
  margin-bottom: 15px;
  text-overflow: ellipsis;
}
.desc-scroll {
  max-height: 288px;
}
.mv-desc span {
  color: var(--color-text-blue);
  font-size: 14px;
}
.recommend {
  margin-top: 10px;
}
.rec{
    margin-top: 5px;
    padding: 5px 0 10px 0;
}
</style>