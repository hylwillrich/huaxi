<template>
  <div class="page">
    <ChatBot />
    <AppointmentForm 
      :show="showAppointmentForm" 
      :department="selectedDepartment"
      :department-id="selectedDepartmentId"
      @close="showAppointmentForm = false"
    />
    
    <div class="onlineappointment">
      <div class="banner"></div>

      <div class="online-ap">
        <div class="title">在线预约<div></div>
        </div>

        <div class="departments">
          <div class="list">
            <div 
              class="icon" 
              v-for="(dept, index) in departmentConfig" 
              :key="index"
              @click="handleDepartmentClick(dept.name)"
              :style="dept.isPediatrics ? '' : { backgroundPosition: dept.bgPos }"
              :class="{ 'pediatrics-icon': dept.isPediatrics }"
            >
              <p>{{ dept.name }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>


  </div>
</template>

<script>
import ChatBot from '@/components/ChatBot.vue'
import AppointmentForm from '@/components/AppointmentForm.vue'

export default {
  name: 'OnlineAppointment',
  components: {
    ChatBot,
    AppointmentForm
  },
  data() {
    return {
      departments: [],
      showAppointmentForm: false,
      selectedDepartment: '',
      selectedDepartmentId: null
    }
  },
  data() {
    return {
      departments: [],
      showAppointmentForm: false,
      selectedDepartment: '',
      selectedDepartmentId: null,
      departmentConfig: [
        { name: '消化科', id: 1, bgPos: '-6px 0' },
        { name: '肝病科', id: 2, bgPos: '-210px 0' },
        { name: '肝胆疾病科', id: 3, bgPos: '-411px 0' },
        { name: '肿瘤内科', id: 4, bgPos: '-615px 0' },
        { name: '心血管内科', id: 5, bgPos: '-820px 0' },
        { name: '内分泌科', id: 6, bgPos: '-1024px 0' },
        { name: '儿科', id: 7, isPediatrics: true }
      ]
    }
  },
  computed: {
    departmentMap() {
      return this.departmentConfig.reduce((map, dept) => {
        map[dept.name] = dept.id;
        return map;
      }, {});
    },
    departments() {
      return this.departmentConfig.map(dept => dept.name);
    }
  },
  methods: {
    handleDepartmentClick(departmentName) {
      this.selectedDepartment = departmentName
      this.selectedDepartmentId = this.departmentMap[departmentName]
      this.showAppointmentForm = true
    }
  }
}
</script>
@import url('@/assets/css/onlineappointment.css');
<style scoped>
@import url('@/assets/css/onlineappointment.css');
</style>