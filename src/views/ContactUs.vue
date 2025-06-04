<template>
  <div class="contactusbody bottom-container">
    

    <div class="banner"></div>
    <div class="contactus">
      <div class="title">
        联系我们 <div></div>
      </div>

      <div class="contactinfos">
        <div class="phone list">
          <div class="icon"></div>
          <div class="info">
            <p>电话</p>
            <p>028-12356900</p>
          </div>
        </div>
        <div class="email list">
          <div class="icon"></div>
          <div class="info">
            <p>邮箱</p>
            <p>lisgdhfh@163.com</p>
          </div>
        </div>
        <div class="address list">
          <div class="icon"></div>
          <div class="info">
            <p>地址</p>
            <p>四川省成都市青羊区清江东路169号</p>
          </div>
        </div>
      </div>

      <div class="busway">
        <p>乘车路线：</p>
        <p>123路、1路、75路、89路、4号地铁线下车A出口清江东路159号</p>
      </div>

      <div class="map" id="map"></div>
    </div>

  </div>
</template>

<script>
export default {
  name: 'ContactUs',
  mounted() {
    this.loadBaiduMap();
  },
  methods: {
    loadBaiduMap() {
      // 动态加载百度地图API
      const script = document.createElement('script');
      script.src = 'https://api.map.baidu.com/api?v=2.0&ak=2TMfE9yIOQFeyGluzaP5HfqbzvWHuOB0&callback=initMap';
      document.body.appendChild(script);
      
      // 定义全局回调函数
      window.initMap = () => {
        const map = new BMap.Map("map");
        const point = new BMap.Point(104.038038, 30.675068);
        map.centerAndZoom(point, 20);

        const marker = new BMap.Marker(point);
        map.addOverlay(marker);
        marker.setAnimation(BMAP_ANIMATION_BOUNCE);
      };
    }
  }
}
</script>

<style scoped>

@import url('@/assets/css/contactus.css');

</style>