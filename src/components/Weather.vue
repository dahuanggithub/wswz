<template>
  <div class="weather">
    <h2 class="city-name">{{ weatherData.basic.city }}</h2>
    <div class="weather-icon">
      <i class="iconfont" v-bind:class="['icon-'+weatherData.now.cond.code]"></i>
      <span>{{ weatherData.now.cond.txt }}</span>
    </div>
    <div class="temp">
      <span class="temp-now">{{ weatherData.now.tmp }}°</span>
      <span class="temp-range">{{ weatherData.daily_forecast[0].tmp.min }}°/{{ weatherData.daily_forecast[0].tmp.max }}°</span>
      </div>
    
    <br class="clear">
    <div class="weather-next">
      <div v-for="item in weather_5d">
        <i class="iconfont" v-bind:class="['icon-'+item.cond.code_d]"></i>
        <p>{{ item.cond.txt_d }}</p>
        <p>{{ item.tmp.min }}°/{{ item.tmp.max }}</p>
      </div>
    </div>
    <p class="update-time">update time: {{ weatherData.basic.update.loc }}</p>
  </div>
</template>

<script>
export default {
  ready: function() {
      if(localStorage.weatherdata){
        var weather_json = JSON.parse(localStorage.weatherdata)
        this.$set('weatherData',  weather_json)
        var  weather_5d = new Array()
        weather_5d = weather_json.daily_forecast.slice(1,6)
        this.$set('weather_5d',  weather_5d)
        return
      }else{
      }
      // GET request
      this.$http({url: 'https://api.heweather.com/x3/weather?cityid=CN101210303&key=4462f5e166c5416593a64ba001e8e15f', method: 'GET'}).then(function (response) {
        // alert(response.data["HeWeather data service 3.0"][0].basic.city)
        this.$set('weatherData', response.data["HeWeather data service 3.0"][0]) // success callback
        localStorage.weatherdata = JSON.stringify(response.data["HeWeather data service 3.0"][0])
      }, function (response) {
        // alert(response.status) // error callback
      });
    },
    
    data (){  // 在 Vue.extend() 中必须是函数
    return{
      
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@font-face {
  font-family: 'iconfont';
  src: url('../assets/weather_iconfont/iconfont.eot'); /* IE9*/
  src: url('../assets/weather_iconfont/iconfont.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
  url('../assets/weather_iconfont/iconfont.woff') format('woff'), /* chrome、firefox */
  url('../assets/weather_iconfont/iconfont.ttf') format('truetype'), /* chrome、firefox、opera、Safari, Android, iOS 4.2+*/
  url('../assets/weather_iconfont/iconfont.svg#iconfont') format('svg'); /* iOS 4.1- */
}
.iconfont{
  font-family: "iconfont" !important;
  font-size: 16px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -webkit-text-stroke-width: 0.2px;
  -moz-osx-font-smoothing: grayscale;
  color: #fff;
}
.icon-100:before { content: "\e600"; } /*晴*/
.icon-101:before { content: "\e601"; } /*多云*/
.icon-102:before { content: "\e600"; } /*少云*/
.icon-103:before { content: "\e601"; } /*晴间多云*/
.icon-104:before { content: "\e602"; } /*阴*/

.icon-200:before { content: "\e611"; } /*有风*/

.icon-300:before { content: "\e60a"; } /*阵雨*/
.icon-301:before { content: "\e609"; } /*强阵雨*/
.icon-302:before { content: "\e60d"; } /*雷阵雨*/
.icon-303:before { content: "\e60d"; } /*强雷阵雨*/
.icon-304:before { content: "\e604"; } /*强雷阵雨冰雹*/
.icon-305:before { content: "\e606"; } /*小雨*/
.icon-306:before { content: "\e607"; } /*中雨*/
.icon-307:before { content: "\e60b"; } /*大雨*/
.icon-308:before { content: "\e608"; } /*大雨*/
.icon-309:before { content: "\e606"; } /*毛毛雨*/
.icon-310:before { content: "\e608"; } /*暴雨*/
.icon-311:before { content: "\e608"; } /*大暴雨*/
.icon-312:before { content: "\e608"; } /*特大暴雨*/
.icon-313:before { content: "\e605"; } /*冻雨*/

.icon-400:before { content: "\e60c"; } /*小雪*/
.icon-401:before { content: "\e60c"; } /*中雪*/
.icon-402:before { content: "\e60c"; } /*大雪*/
.icon-403:before { content: "\e60c"; } /*暴雪*/
.icon-404:before { content: "\e604"; } /*雨夹雪*/
.icon-405:before { content: "\e60c"; } /*雨雪天气*/
.icon-406:before { content: "\e60c"; } /*阵雨夹雪*/
.icon-407:before { content: "\e60c"; } /*阵雪*/

.icon-500:before { content: "\e603"; } /*薄雾*/
.icon-501:before { content: "\e603"; } /*雾*/
.icon-502:before { content: "\e60e"; } /*霾*/



.weather{
  position: absolute;
  height: 250px;
  width: 100%;
  background-color: #A361FC;
  color: #fff;
}
.city-name{
  margin-top: 10px;
  color: #fff;
  margin-bottom: 10px;
}
.update-time{
  position: absolute;
  bottom: 90px;
  width: 100%;
  text-align: center;
  font-size: 10px;
  color: rgba(255,255,255,.25);
}
.weather-icon{
  float: left;
  width: 80px;
  height: 80px;
  margin-left: 20px;

}
.weather-icon i{
  font-size: 64px;
  line-height: 64px;
}
.temp{
  float: left;
  width: 50px;
  height: 80px;
  margin-left: 6px;
}
.temp{
  margin-top: 12px;
  font-size: 52px;
  line-height: 52px;
  font-weight: 100;
  vertical-align: bottom;
}
.temp-range{
  margin-left: 6px;
  vertical-align: top;
  font-size: 16px;
  line-height: 16px;
}

.clear{
  clear: both;
}
.weather-next{
  width: 100%;
  height:100px;
}
.weather-next div{
  float: left;
  margin-top: 20px;
  padding-top: 6px;
  width: 20%;
  height:90px;
}
.weather-next div:nth-child(1){
  background-color: rgba(255,255,255,.3);
}
.weather-next div:nth-child(2){
  background-color: rgba(255,255,255,.25);
}
.weather-next div:nth-child(3){
  background-color: rgba(255,255,255,.2);
}
.weather-next div:nth-child(4){
  background-color: rgba(255,255,255,.15);
}
.weather-next div:nth-child(5){
  background-color: rgba(255,255,255,.1);
}
.weather-next i{
  font-size: 30px;
}
.weather-next p{
  font-size: 13px;
}
</style>
