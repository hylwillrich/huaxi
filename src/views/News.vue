<template>
  <div class="news">
    <div class="banner"></div>

    <div class="newslist">
      <!-- 新闻详情弹窗 -->
      <div class="news-detail-modal" v-if="showDetailModal">
        <div class="news-detail-content">
          <button class="close-button" @click="closeDetailModal">×</button>
          <h2 class="news-detail-title">{{ currentNews.title }}</h2>
          <div class="news-detail-date">{{ formatDate(currentNews.date) }}</div>
          <img
            class="news-detail-image"
            :src="currentNews.imageUrl || defaultNewsImage"
            alt="新闻图片"
            v-if="currentNews.imageUrl || defaultNewsImage"
          />
          <div class="news-detail-body" v-html="currentNews.content"></div>
        </div>
      </div>
      <div v-if="loading" class="loading">加载中...</div>
      <div v-else-if="error" class="error">{{ error }}</div>
      <template v-else>
        <div class="list" v-for="(news, index) in displayedNews" :key="index">
          <div class="yearmonth">{{ formatYearMonth(news.date) }}</div>
          <hr />
          <div class="content">
            <div
              class="pic"
              :style="{
                backgroundImage: `url(${news.imageUrl || defaultNewsImage})`,
              }"
            ></div>
            <div class="words">
              <div class="title" @click="viewNewsDetail(news)">
                {{ news.title }}
              </div>
              <div class="date">{{ formatDate(news.date) }}</div>
              <p>{{ truncateSummary(news.summary) }}</p>
              <button @click="viewNewsDetail(news)">了解更多</button>
            </div>
          </div>
        </div>

        <div class="watchmore" @click="loadMoreNews" v-if="hasMore">
          浏览更多
        </div>
        <div class="no-more" v-else>已全部展示</div>
      </template>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import defaultNewsImage from "@/assets/images/news/newslistpic1.png";

export default {
  name: "News",
  data() {
    return {
      allNews: [], // 存储所有新闻数据
      displayedNews: [], // 当前显示的新闻
      loading: false,
      error: null,
      defaultNewsImage,
      currentPage: 1,
      pageSize: 3,
      hasMore: true,
      showDetailModal: false,
      currentNews: {},
    };
  },
  created() {
    this.fetchNews();
  },
  methods: {
    async fetchNews() {
      this.loading = true;
      this.error = null;
      try {
        // 从API获取新闻列表数据
        const response = await axios.get("/api/news/list", {
          params: {
            page: 1,
            pageSize: 10,
          },
        });

        // 尝试从不同字段获取新闻数据
        const jsonData = response.data;
        let newsItems = [];

        if (Array.isArray(jsonData)) {
          newsItems = jsonData;
        } else if (jsonData && Array.isArray(jsonData.news)) {
          newsItems = jsonData.news;
        } else if (jsonData && Array.isArray(jsonData.list)) {
          newsItems = jsonData.list;
        } else if (jsonData && Array.isArray(jsonData.data)) {
          newsItems = jsonData.data;
        } else {
          console.error("API返回数据格式不符合预期:", jsonData);
          throw new Error(
            `API返回的JSON格式不正确，期望包含news/list/data数组字段，实际结构: ${JSON.stringify(
              jsonData
            )}`
          );
        }

        // 按n_date降序排列
        newsItems.sort((a, b) => {
          const dateA = a.n_date ? new Date(a.n_date) : new Date(0);
          const dateB = b.n_date ? new Date(b.n_date) : new Date(0);
          return dateB - dateA;
        });

        this.allNews = newsItems.map((news, i) => {
          // 映射API字段到模板字段
          const item = {
            // 保留原始数据用于调试
            _raw: news,
            // 映射字段
            id: news.id || Date.now().toString(),
            title: news.n_title || "无标题",
            date: news.n_date || new Date().toISOString(),
            summary: news.n_content || "暂无内容摘要",
            content: news.n_content || "",
            imageUrl: news.n_pic
              ? `http://localhost:8080${news.n_pic}`
              : this.defaultNewsImage,
          };

          return item;
        });
        // 初始加载前3条
        this.loadMoreNews();
      } catch (error) {
        this.error = error.response
          ? `请求失败: ${error.response.status}`
          : error.message || "无法连接到服务器";
        console.error("获取新闻数据失败:", error);
      } finally {
        this.loading = false;
      }
    },
    formatDate(dateString) {
      const date = new Date(dateString);
      return `${date.getFullYear()}年${
        date.getMonth() + 1
      }月${date.getDate()}日`;
    },
    formatYearMonth(dateString) {
      const date = new Date(dateString);
      return `${date.getFullYear()}年${date.getMonth() + 1}月`;
    },
    viewNewsDetail(news) {
      this.currentNews = news;
      this.showDetailModal = true;
    },
    closeDetailModal() {
      this.showDetailModal = false;
    },
    truncateSummary(text) {
      if (!text) return "";
      if (text.length <= 50) return text;
      return text.substring(0, 50) + "...";
    },
    loadMoreNews() {
      const startIndex = (this.currentPage - 1) * this.pageSize;
      const endIndex = startIndex + this.pageSize;
      const newNews = this.allNews.slice(startIndex, endIndex);

      this.displayedNews = [...this.displayedNews, ...newNews];
      this.currentPage++;

      // 检查是否还有更多新闻
      this.hasMore = endIndex < this.allNews.length;
    },
  },
};
</script>

<style scoped>
@import url("@/assets/css/news.css");
</style>
