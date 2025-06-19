<template>
  <div class="index bottom-container">

    <banner class="banner"></banner>

    <div class="aboutus">
      <div class="info">
        <h1>了解我们</h1>
        <h3>ABOUT US</h3>
        <p>
          成都中医药大学民族药学院是全国高等中医药大学中第一个民族医学院。目前，学院拥有藏医学和藏药学两个本科专业，民族医学博士点，国家中医药管理局
          民族药学重点学科。藏医学本科专业于2009年被确定为国家级特色专业建设点、藏药学本科专业于2010年确定为校级特色专业建设点。
        </p>
        <div class="go" @click="viewIntroduceDetail">
          查看详情
          <button>→</button>
        </div>
      </div>
      <div class="pic"></div>
    </div>

    <div class="expert">
      <div class="ex-title">
        <h1>专家一览</h1>
      </div>

      <div class="department">
        <div v-for="(dept, index) in departments" :key="index" :class="{ 'point': dept === currentDepartment }"
          @click="changeDepartment(dept)">
          {{ dept }}
        </div>
      </div>

      <div class="ex-pic">
        <div v-if="loading" class="loading">加载中...</div>
        <div v-else-if="error" class="error">{{ error }}</div>
        <template v-else>
          <div class="people" v-for="(expert, index) in pagedExperts" :key="index">
            <img :src="expert.photo || require('@/assets/default-doctor.png')" alt="医生照片" class="doctor-photo">
            <div class="ex-info">
              <h1>{{ expert.name }}</h1>
              <h5>{{ expert.title }}</h5>
              <button @click="viewExpertDetail(expert)">查看详情</button>
            </div>
          </div>
        </template>
      </div>

      <div class="control" v-if="currentExperts && currentExperts.length > pageSize">
        <div class="left" @click="changePage('prev')">◀</div>
        <div class="right" @click="changePage('next')">▶</div>
      </div>
    </div>

    <div class="news">
      <div class="newsmain">
        <div class="newsbanner">
          <h1>新闻资讯</h1>
          <h4>NEWS</h4>
          <div>+</div>
          <div>更多动态</div>
        </div>

        <div class="main">
          <template v-if="newsLoading">
            <div class="loading">加载新闻中...</div>
          </template>
          <template v-else-if="newsError">
            <div class="error">{{ newsError }}</div>
          </template>
          <template v-else>
            <div class="headlines" v-if="headlineNews">
              <div class="newspic" :style="{ backgroundImage: `url(${headlineNews.n_pic || ''})` }"></div>
              <div class="newstitle">{{ headlineNews.n_title }}</div>
              <div class="newsinfo">{{ headlineNews.n_content }}</div>
            </div>

            <div class="list">
              <div class="listcontent" v-for="(newsItem, index) in newsList" :key="index">
                <div class="date">
                  <div class="d">{{ newsItem.day }}</div>
                  <div class="ym">{{ newsItem.yearMonth }}</div>
                </div>
                <div class="word">
                  <div class="newstitle">{{ newsItem.title }}</div>
                  <div class="newsinfo">{{ newsItem.summary }}</div>
                  <button class="details" @click="viewNewsDetail(newsItem)">查看详情> ></button>
                </div>
              </div>
            </div>
          </template>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Index',
  data() {
    return {
      allExperts: [],
      departments: [],
      currentDepartment: '',
      currentExperts: [],
      pagedExperts: [],
      currentPage: 1,
      pageSize: 4,
      loading: false,
      error: null,
      newsLoading: false,
      newsError: null,
      newsList: [],
      headlineNews: null
    }
  },
  created() {
    this.fetchDoctors();
    this.fetchNews();
  },
  methods: {
    async fetchDoctors() {
      this.loading = true;
      this.error = null;
      console.groupCollapsed('[DEBUG] 医生数据请求');
      console.log('开始请求医生数据...');

      try {
        console.log('发送请求到: /api/doctor/list');
        const response = await axios.get('/api/doctor/list');
        console.log('收到响应:', {
          status: response.status,
          data: response.data,
          headers: response.headers
        });

        if (!response.data) {
          console.warn('API返回空数据');
          throw new Error('API返回空数据');
        }

        this.processDoctorData(response.data);
        console.log('成功处理数据，医生数量:', this.allExperts.length);
      } catch (error) {
        console.error('请求失败:', error);
        if (error.response) {
          console.error('错误详情:', {
            status: error.response.status,
            data: error.response.data
          });
          this.error = `请求失败: ${error.response.status}`;
        } else {
          this.error = '无法连接到服务器';
        }
      } finally {
        this.loading = false;
        console.log('请求处理完成');
        console.groupEnd();
      }
    },

    processDoctorData(data) {
      const doctorsData = Array.isArray(data) ? data : data.list || data.data || [];
      const basePath = 'http://localhost:8080';

      this.allExperts = doctorsData.map(doctor => ({
        id: doctor.docId,
        name: doctor.docName,
        title: doctor.docClass,
        department: doctor.dname,
        photo: basePath + doctor.docPhoto
      }));

      this.departments = [...new Set(this.allExperts.map(d => d.department))];
      this.currentDepartment = this.departments[0] || '';
      this.filterExperts();
    },

    filterExperts() {
      this.currentExperts = this.currentDepartment
        ? this.allExperts.filter(d => d.department === this.currentDepartment)
        : this.allExperts;
      this.currentPage = 1;
      this.updatePagedExperts();
    },

    updatePagedExperts() {
      const start = (this.currentPage - 1) * this.pageSize;
      const end = start + this.pageSize;
      this.pagedExperts = this.currentExperts.slice(start, end);
    },

    changeDepartment(dept) {
      this.currentDepartment = dept;
      this.filterExperts();
    },

    changePage(direction) {
      const totalPages = Math.ceil(this.currentExperts.length / this.pageSize);
      if (direction === 'prev' && this.currentPage > 1) {
        this.currentPage--;
      } else if (direction === 'next' && this.currentPage < totalPages) {
        this.currentPage++;
      }
      this.updatePagedExperts();
    },

    viewExpertDetail(expert) {
      this.$router.push({
        path: '/expertintro',
        query: { expertId: expert.id }
      });
    },
    viewNewsDetail() {
      this.$router.push('/news');
    },
    viewIntroduceDetail() {
      this.$router.push("/introduce");
    },

    async fetchNews() {
      this.newsLoading = true;
      this.newsError = null;
      try {
        console.groupCollapsed('[DEBUG] 新闻数据请求');
        console.log('开始请求新闻数据...');
        const response = await axios.get('/api/news/list');
        
        console.log('完整API响应:', {
          status: response.status,
          headers: response.headers,
          data: response.data
        });

        if (!response.data) {
          console.error('API返回空数据');
          throw new Error('API返回空数据');
        }

        // 检查数据是否嵌套在某个属性中
        const rawData = response.data;
        let newsData = [];
        
        if (Array.isArray(rawData)) {
          newsData = rawData;
        } else if (rawData && Array.isArray(rawData.list)) {
          newsData = rawData.list;
        } else if (rawData && Array.isArray(rawData.data)) {
          newsData = rawData.data;
        } else if (rawData && rawData.records) {
          newsData = rawData.records;
        } else {
          console.error('无法识别的数据结构:', rawData);
          throw new Error('无法识别的数据结构');
        }
        
        console.log('提取后的新闻数据:', newsData);
        
        if (newsData.length > 0) {
          // 处理头条新闻 - 添加更多可能的字段名和嵌套路径
          const firstNews = newsData[0];
          const firstContent = firstNews?.n_content || firstNews?.content || 
                             firstNews?.newsContent || firstNews?.description || '';
          this.headlineNews = {
            n_pic: firstNews?.n_pic || firstNews?.pic || 
                  firstNews?.imageUrl || firstNews?.coverImage ? 
                  (firstNews.n_pic?.startsWith('http') ? firstNews.n_pic : 
                   firstNews.pic?.startsWith('http') ? firstNews.pic :
                   firstNews.imageUrl?.startsWith('http') ? firstNews.imageUrl :
                   firstNews.coverImage?.startsWith('http') ? firstNews.coverImage :
                   'http://localhost:8080' + (firstNews?.n_pic || firstNews?.pic || 
                   firstNews?.imageUrl || firstNews?.coverImage)) : '',
            n_title: firstNews?.n_title || firstNews?.title || 
                    firstNews?.newsTitle || '无标题',
            n_content: firstContent.substring(0, 50) + (firstContent.length > 30 ? '...' : '')
          };

          // 处理新闻列表 - 只展示2条新闻
          this.newsList = newsData.slice(1, 3).map(news => {
            const content = news?.n_content || news?.content || 
                          news?.newsContent || news?.description || '';
            const date = news?.n_date || news?.date || 
                        news?.publish_date || news?.createTime || news?.updateTime;
            const imagePath = news?.n_pic || news?.pic || 
                            news?.imageUrl || news?.coverImage;
            const fullImagePath = imagePath ? 
                                (imagePath.startsWith('http') ? imagePath : 
                                 'http://localhost:8080' + imagePath) : '';
            return {
              day: date ? new Date(date).getDate().toString() : '--',
              yearMonth: date 
                ? `${new Date(date).getFullYear()}/${(new Date(date).getMonth() + 1).toString().padStart(2, '0')}`
                : '--/--',
              title: news?.n_title || news?.title || 
                    news?.newsTitle || '无标题',
              summary: content.substring(0, 50) + (content.length > 50 ? '...' : ''),
              image: fullImagePath
            };
          });
        }
        console.log('处理后的新闻数据:', {
          headlineNews: this.headlineNews,
          newsList: this.newsList
        });
        console.groupEnd();
      } catch (error) {
        this.newsError = '获取新闻数据失败';
        console.error('获取新闻数据失败:', error);
      } finally {
        this.newsLoading = false;
      }
    }
  }
}
</script>

<style scoped>
@import url('@/assets/css/index.css');
</style>