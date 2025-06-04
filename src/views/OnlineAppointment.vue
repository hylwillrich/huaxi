<template>
  <div class="page">
    

    <div class="onlineappointment">
      <div class="banner"></div>

      <div class="online-ap">
        <div class="title">在线预约<div></div>
        </div>

        <div class="departments">
          <div class="list">
            <div class="icon" v-for="(dept, index) in departments" :key="index">
              <p>{{ dept }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>

    
  </div>
</template>

<script>
export default {
  name: 'OnlineAppointment',
  data() {
    return {
      departments: [
        '消化科',
        '肝病科',
        '肝胆疾病科',
        '肿瘤内科',
        '心血管内科',
        '内分泌科',
        '消化科',
        '肝病科',
        '肝胆疾病科',
        '肿瘤内科',
        '心血管内科',
        '内分泌科'
      ]
    }
  }
}
</script>

<style scoped>
@import url('@/assets/css/onlineappointment.css');
</style>