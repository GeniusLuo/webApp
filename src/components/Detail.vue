<template>
  <div>
    <my-header :title="page.title || '页面详情'" :hasBack="true"></my-header>
    <div class="wrapper">
      <!-- 帖子详情 begin -->
      <h2 class="detail-head">{{ page.title }}</h2>
      <div class="detail-info">
        <div class="detail-info-head">
          <div class="avatar">
            <img :src="page.uid && page.uid.pic" alt />
          </div>
          <div class="cont">
            <p class="name">{{ page.uid && page.uid.name }}</p>
            <p class="time">{{ page.created | moment }}</p>
          </div>
        </div>
        <div class="detail-info-body" v-html="content"></div>
        <div class="detail-info-foot">{{ page.reads }}阅读</div>
      </div>

      <!-- 帖子详情 end -->
      <div class="comment-box">
        <div class="box-title">评论</div>
        <ul class="list" ref="scrollObj">
          <li
            class="item"
            v-for="(item, index) in comments"
            :key="item._id + index"
          >
            <div class="detail-head">
              <img class="avatar" :src="item.cuid.pic" alt />
              <div class="cont">
                <div class="userName">{{ item.cuid.name }}</div>
                <div class="opt">
                  <span class="time">{{ item.created | moment }}</span>
                  <span class="reply">
                    <span class="dot"></span>
                    评论了帖子
                  </span>
                  <span class="goReply">回复</span>
                  <!-- <template v-if="isAdmin">
                    <span type="edit">编辑</span>
                    <span type="del">删除</span>
                    <span class="jieda-accept" type="accept">采纳</span>
                  </template>-->
                  <svg-icon
                    icon="zan"
                    v-if="item.isBest == 1"
                    title="最佳答案"
                  ></svg-icon>
                </div>
              </div>
              <div class="f-right">
                <span class="zan" :class="{ zanok: true }">
                  <svg-icon icon="zan"></svg-icon>
                  <span class="hands" v-if="item.hands > 0">{{
                    item.hands
                  }}</span>
                </span>
              </div>
            </div>
            <div class="detail-info">
              <div class="txt" v-html="item.content"></div>
            </div>
          </li>
        </ul>
      </div>
      <!-- 详情底部 -->
      <div class="detail-bottom">
        <span class="bottom-input-wrap">
          <svg-icon icon="pen"></svg-icon>写评论...
        </span>
        <ul class="bottom-right">
          <li :class="{ row: !showText }">
            <svg-icon icon="bianji"></svg-icon>
            <p>
              <span v-show="showText">评论</span>
              {{ page.answer }}
            </p>
          </li>
          <li :class="{ selected: page.isFav, row: !showText }">
            <svg-icon icon="shoucang" class="svg-fav"></svg-icon>
            <p>
              <span v-show="showText">{{
                page.isFav ? "取消收藏" : "收藏"
              }}</span>
            </p>
          </li>
          <li :class="{ row: !showText }">
            <svg-icon icon="zan"></svg-icon>
            <p>
              <span v-show="showText">赞</span>
              {{ page.fav }}
            </p>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import { escapeHtml } from '@/utils/escapeHtml'
import { getDetail } from '@/api/content'
import { getComents } from '@/api/comments'
export default {
  name: 'detail',
  props: ['tid'],
  data () {
    return {
      total: 0,
      size: 10,
      current: 0,
      page: {},
      comments: [],
      editInfo: {
        content: '',
        code: '',
        sid: ''
      },
      showText: false
    }
  },
  mounted () {
    this.getPostDetail()
    this.getCommentsList()
  },
  methods: {
    getPostDetail () {
      getDetail(this.tid).then(res => {
        if (res.code === 200) {
          this.page = res.data
        }
      })
    },
    getCommentsList () {
      getComents({
        tid: this.tid,
        page: this.current,
        limit: this.size
      }).then(res => {
        if (res.code === 200) {
          this.comments = res.data
          this.total = res.total
        }
      })
    }
  },
  computed: {
    content () {
      return escapeHtml(this.page.content || '')
    }
  }
}
</script>

<style lang="scss" scoped></style>
