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
        <div class="point">消化科</div>
        <div>肝病科</div>
        <div>肝胆疾病科</div>
        <div>肿瘤内科</div>
        <div>心血管内科</div>
        <div>内分泌科</div>
        <div>消化科</div>
        <div>儿科</div>
      </div>

      <div class="ex-pic">
        <div class="people" v-for="(expert, index) in experts" :key="index">
          <div class="img">
            <div class="dc"></div>
            <div class="hover"></div>
          </div>
          <div class="ex-info">
            <h1>{{ expert.name }}</h1>
            <h5>{{ expert.title }}</h5>
            <button @click="viewExpertDetail(expert)">查看详情</button>
          </div>
        </div>
      </div>

      <div class="control">
        <div class="left">◀</div>
        <div class="right">▶</div>
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
          <div class="headlines">
            <div class="newspic"></div>
            <div class="newstitle">我院召开2021年第13次党委理论中心组(扩大)学习会</div>
            <div class="newsinfo">8月5日，我院以主会场+分会场+视频会议的形式召开2021年第13次党委理论中心组(扩大)学习会。党委书记常德贵传达学习省委十一届九次全会精神...
            </div>
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
        </div>
      </div>
    </div>

  </div>
</template>

<script>
export default {
  name: 'Index',
  data() {
    return {
      experts: [
        { name: '蔡萍', title: '主任中医师' },
        { name: '蔡萍', title: '主任中医师' },
        { name: '蔡萍', title: '主任中医师' },
        { name: '蔡萍', title: '主任中医师' }
      ],
      newsList: [
        {
          day: '10',
          yearMonth: '2021/08',
          title: '马来西亚卫生部8月1日代表团来访院',
          summary: '8月28日，马来西亚卫生部李文材副部长等一行10人来我院进行交流访问。党委书记常德贵，院长谢春光，党委副书记、纪委书记祝捷，副院长田理共同会见了来访代表团...'
        },
        {
          day: '10',
          yearMonth: '2021/08',
          title: '马来西亚卫生部代表团来访我院',
          summary: '8月28日，马来西亚卫生部李文材副部长等一行10人来我院进行交流访问。党委书记常德贵，院长谢春光，党委副书记、纪委书记祝捷，副院长田理共同会见了来访代表团...'
        }
      ]
    }
  },
  methods: {
    viewExpertDetail(expert) {
      // 查看专家详情逻辑
      this.$router.push({ path: '/expertintro', query: { expertId: expert.id } })
    },
    viewNewsDetail() {
      // 查看新闻详情逻辑
      this.$router.push('/news')
    },
    viewIntroduceDetail(){
      // 查看医院介绍逻辑
      this.$router.push("/introduce")
    }
  }
}
</script>

<style scoped>

@import url('@/assets/css/index.css');

</style>