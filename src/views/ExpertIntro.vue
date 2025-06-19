<template>
  <div class="expertintro bottom-container">


    <div class="expertintro">
      <div class="topbanner"></div>

      <div class="expert-intro">
        <div class="title">
          <h1>专家介绍</h1>
        </div>
        <div class="department">
          <span v-for="(dept, index) in departments" :key="index" :class="{ select: currentDepartment === dept }"
            @click="changeDepartment(dept)">
            {{ dept }}
          </span>
        </div>

        <div class="experts">
          <div v-if="loading" class="loading">加载中...</div>
          <div v-else-if="error" class="error">{{ error }}</div>
          <template v-else>
            <div class="dp">
              <template v-for="(group, index) in groupedExperts" :key="index">
                <div class="infos-group">
                  <div class="infos" v-for="expert in group" :key="expert.id">
                    <div class="pic" :style="{ backgroundImage: `url(${expert.photo || require('@/assets/default-doctor.png')})` }"></div>
                    <div class="description">
                      <p>职称职务 {{ expert.title }}</p>
                      <p>所在科室 {{ expert.department }}</p>
                      <div class="line"></div>
                      <h4>{{ expert.name }}</h4>
                    </div>
                  </div>
                </div>
              </template>
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
  name: 'ExpertIntro',
  data() {
    return {
      currentDepartment: '',
      departments: [],
      allExperts: [],
      filteredExperts: [],
      loading: false,
      error: null
    }
  },
  created() {
    this.fetchDoctors();
  },
  computed: {
    groupedExperts() {
      const groups = [];
      for (let i = 0; i < this.filteredExperts.length; i += 2) {
        groups.push(this.filteredExperts.slice(i, i + 2));
      }
      return groups;
    }
  },
  methods: {
    async fetchDoctors() {
      this.loading = true;
      this.error = null;
      try {
        const response = await axios.get('/api/doctor/list');
        const doctorsData = Array.isArray(response.data) ? response.data : 
                          response.data.list || response.data.data || [];
        
        this.allExperts = doctorsData.map(doctor => ({
          id: doctor.docId,
          name: doctor.docName,
          title: doctor.docClass,
          department: doctor.dname,
          photo: doctor.docPhoto?.startsWith('http') ? doctor.docPhoto : 
                'http://localhost:8080' + doctor.docPhoto
        }));

        this.departments = ['总览', ...new Set(this.allExperts.map(d => d.department))];
        this.currentDepartment = '总览';
        this.filterExperts();
      } catch (error) {
        this.error = error.response ? `请求失败: ${error.response.status}` : '无法连接到服务器';
        console.error('获取医生数据失败:', error);
      } finally {
        this.loading = false;
      }
    },
    filterExperts() {
      this.filteredExperts = this.currentDepartment === '总览'
        ? this.allExperts
        : this.allExperts.filter(d => d.department === this.currentDepartment);
    },
    changeDepartment(dept) {
      this.currentDepartment = dept;
      this.filterExperts();
    }
  }
}
</script>


<style scoped>
@import url('@/assets/css/expertintro.css');
</style>