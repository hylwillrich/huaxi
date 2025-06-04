<template>
  <div class="expertintro bottom-container">
    

    <div class="expertintro">
      <div class="topbanner"></div>

      <div class="expert-intro">
        <div class="title">
          <h1>专家介绍</h1>
        </div>
        <div class="department">
          <span 
            v-for="(dept, index) in departments" 
            :key="index"
            :class="{ select: currentDepartment === dept }"
            @click="changeDepartment(dept)"
          >
            {{ dept }}
          </span>
        </div>

        <div class="experts">
          <div class="dp" :class="{ select: currentDepartment === department }" v-for="(department, dIndex) in departments" :key="dIndex">
            <div class="infos" v-for="(expert, eIndex) in experts" :key="eIndex">
              <div class="pic"></div>
              <div class="description">
                <p>职称职务 {{ expert.title }}</p>
                <p>所在科室 {{ department }}</p>
                <div class="line"></div>
                <h4>{{ expert.name }}</h4>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
export default {
  name: 'ExpertIntro',
  data() {
    return {
      currentDepartment: '消化科',
      departments: [
        '消化科',
        '肝病科',
        '肝胆疾病科',
        '肿瘤内科',
        '心血管内科',
        '内分泌科',
        '儿科'
      ],
      experts: [
        { name: '刘毅', title: '教授、主任医师' },
        { name: '张华', title: '副主任医师' },
        { name: '李明', title: '主治医师' },
        { name: '王芳', title: '主任医师' },
        { name: '赵强', title: '副主任医师' }
      ]
    }
  },
  methods: {
    changeDepartment(dept) {
      this.currentDepartment = dept;
    }
  }
}
</script>

<style scoped>

@import url('@/assets/css/expertintro.css');

</style>