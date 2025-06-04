<template>
    <div class="news">
      <div class="banner"></div>
  
      <div class="newslist">
        <div class="list" v-for="(news, index) in newsList" :key="index">
          <div class="yearmonth">{{ formatYearMonth(news.date) }}</div>
          <hr>
          <div class="content">
            <div class="pic"></div>
            <div class="words">
              <div class="title">{{ news.title }}</div>
              <div class="date">{{ formatDate(news.date) }}</div>
              <p>{{ news.summary }}</p>
              <button @click="viewNewsDetail(news)">了解更多</button>
            </div>
          </div>
        </div>
  
        <div class="watchmore">
          浏览更多
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'News',
    data() {
      return {
        newsList: [
          {
            id: 1,
            title: '我院召开2021年第13次党委理论中心组（扩大）学习会',
            date: '2021-08-08',
            summary: '8月5日，我院以主会场+分会场+视频会议的形式召开2021年第13次党委理论中心组（扩大）学习会。党委书记常德贵传达学习省委十一届九次全会精神...他指出本次全会是在庆祝建党百年华诞...',
            content: '完整新闻内容...'
          },
          {
            id: 2,
            title: '马来西亚卫生部代表团来访我院',
            date: '2021-08-10',
            summary: '8月28日，马来西亚卫生部李文材副部长等一行10人来我院进行交流访问。党委书记常德贵，院长谢春光，党委副书记、纪委书记祝捷，副院长田理共同会见了来访代表团...',
            content: '完整新闻内容...'
          },
          {
            id: 3,
            title: '我院举行2021年新员工入职培训',
            date: '2021-08-15',
            summary: '8月15日至17日，我院举行2021年新员工入职培训。党委书记常德贵出席开班仪式并讲话，院长谢春光作专题报告...',
            content: '完整新闻内容...'
          }
        ]
      }
    },
    methods: {
      formatDate(dateString) {
        const date = new Date(dateString);
        return `${date.getFullYear()}年${date.getMonth() + 1}月${date.getDate()}日`;
      },
      formatYearMonth(dateString) {
        const date = new Date(dateString);
        return `${date.getFullYear()}年${date.getMonth() + 1}月`;
      },
      viewNewsDetail(news) {
        this.$router.push({ 
          name: 'NewsDetail', 
          params: { id: news.id },
          query: { title: news.title }
        });
      }
    }
  }
  </script>
  
  <style scoped>
  
  @import url('@/assets/css/news.css');
  
  </style>