<template>
  <!-- 顶部导航栏 -->
  <div class="bg">
    <div class="left">
      <div class="name1">成都民族医药研究院</div>
      <div class="name2">四川华西中医药研究所</div>
    </div>
    <div class="middle">
      <div><router-link to="/">首页</router-link></div>
      <div><router-link to="/introduce">蜀人堂介绍</router-link></div>
      <div><router-link to="/expertintro">专家介绍</router-link></div>
      <div><router-link to="/news">新闻资讯</router-link></div>
      <div><router-link to="/onlineappointment">在线预约</router-link></div>
      <div><router-link to="/contactus">联系我们</router-link></div>
    </div>
    <div class="right">
      <img src="@/assets/images/magnifier.png" alt="">
    </div>
  </div>
</template>

<script>
export default {
  name: 'AppHeader'
}
</script>

<style scoped>
@import url('@/assets/css/top.css');
</style>