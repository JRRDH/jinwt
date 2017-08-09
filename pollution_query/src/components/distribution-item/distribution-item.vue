<template>
  <div class="distribution-item">
    <div class="entName">
      {{item.EntName}}
    </div>
    <div class="desc">
      <div class="exceed" v-show="isExceed">超标</div>
      <div class="notExceed" v-show="!isExceed">未超标</div>
      <div class="primary-pollutant" v-show="isExceed">首要污染物：{{primaryPollutant}}</div>
    </div>
    <div class="extend-icon" @click="showExtend()">{{extendIcon}}</div>
    <div class="extend" v-show="isExtend">
      <div class="extend-item" v-for="(value, key, index) in pollutant">
        <div class="extend-item-content">
          <div class="value">{{transValue(value)}}</div>
          <div class="engName"><span v-html="transKey(key)"></span></div>
          <div class="cnName">{{transObj[key]}}</div>
        </div>
        <div class="extend-item-split" v-show="((index + 1) % 3 !== 0)"></div>
      </div>
    </div>
  </div>
</template>

<script>
    export default {
      props: {
        item: {}
      },
      created() {
        // 根据污染源请求排口
        this.$http.get('http://apps.homed.me/payTest/juhe/server.php', {params: {'api': 'sites', 'parm': '&entCode=' + this.item.EntCode}}).then((res) => {
          let jsondata = JSON.parse(res.bodyText);
          let result = jsondata.result;
          for (let i = 0; i < result.length; i++) {
            // 根据排口请求污染物
            this.$http.get('http://apps.homed.me/payTest/juhe/server.php', {params: {'api': 'loads', 'parm': '&siteCode=' + result[i].Stcode}}).then((res) => {
              let jsondata = JSON.parse(res.bodyText);
              let result = jsondata.result;
              if (jsondata.result.length > 0) {
                let temp = {
                  'Fume': '-1',
                  'Nox': '-1',
                  'So2': '-1',
                  'PH': '-1',
                  'COD': '-1',
                  'NH3/N': '-1'
                };
                for (let i = 0; i < result.length; i++) {
                  for (let key in result[i]) {
                    if (key in temp) {
                      temp[key] = result[i][key];
                    }
                  }
                }
                this.pollutant = temp;
              }
            });
          }
        });
      },
      data() {
          return {
            pollutant: {}, // 污染物
            primaryPollutant: '',  // 首要污染物
            isExtend: false, // 判断是否显示扩展信息
            extendIcon: '伤', // 图标
            transObj: {
               'Fume': '粉尘',
               'Nox': '氮氧化物',
               'So2': '二氧化硫',
               'PH': '酸碱综合度',
               'COD': '化学耗氧量',
               'NH3/N': '无机氨氮'
            }
          };
      },
      computed: {
        // 计算是否超标
        isExceed() {
          let maxValue = 0;
          let maxValueIndex = 0;
          let standardValue = {
            'Fume': '50',
            'Nox': '300',
            'So2': '200',
            'COD': '80',
            'NH3/N': '10',
            'PH': 1.5
          };
          for (let key in this.pollutant) {
            let value = this.pollutant[key];
            if (key === 'PH') {
              if (value > 9) {
                if ((value - 9) / 9 > maxValue) {
                  maxValue = (value - 9) / 9;
                  maxValueIndex = key;
                }
              } else if (value < 6) {
                if ((6 - value) / 6 > maxValue) {
                  maxValue = (6 - value) / 6;
                  maxValueIndex = key;
                }
              }
            } else if (value - standardValue[key] > 0) {
              if ((value - standardValue[key]) / standardValue[key] > maxValue) {
                maxValue = (value - standardValue[key]) / standardValue[key];
                maxValueIndex = key;
              }
            }
          }
          if (maxValue > 0) {
            this.primaryPollutant = maxValueIndex;
            return true;
          }
        }
      },
      // 没有获取到的值显示为 -
      methods: {
        transValue(value) {
          if (value === '-1') {
            return '-';
          }
          return parseFloat(value);
        },
        transKey(key) {
          return key === 'So2' ? 'SO2' : key;
        },
        // 扩展点击事件
        showExtend() {
          this.isExtend = !this.isExtend;
          this.extendIcon = this.isExtend ? '伣' : '伤';
        }
      }
    };
</script>

<style lang="scss">
  .distribution-item {
    position: relative;
    background-color: #ffffff;
    border-radius: 5px;
    .entName {
      padding-left: 0.18rem;
      font-size: 0.29rem;
      font-family: PingFang-SC-Medium;
      padding-top: 0.25rem;
    }
    .desc {
      position: relative;
      padding: 0.18rem 0 0.24rem 0.18rem;
      font-size: 0.22rem;
      display: flex;
      line-height: 0.35rem;
      .exceed, .notExceed {
        flex-grow: 0;
        padding: 0 0.15rem;
        border-radius: 0.17rem;
        color: #FFFFff;
      }
      .exceed {
        background-color: #eb5131;
      }
      .notExceed {
        background-color: #1dd081;
      }
      .primary-pollutant {
        flex-grow: 1;
        padding-left: 0.2rem;
        color: #999999;
      }
    }
    .extend-icon {
      position: absolute;
      top: 0.55rem;
      right: 0.25rem;
      font-size: 0.3rem;
      font-family: iconfont;
      color: #999999;
    }
    .extend {
      position: relative;
      padding: 0.18rem;
      border-top: 2px solid #f0f0f0;
      overflow: hidden;
      .extend-item {
        float: left;
        display: flex;
        width: 33.3%;
        text-align: center;
        margin: 0.18rem 0;
        .extend-item-content {
          flex: 1;
          .value {
            font-size: 0.49rem;
            font-family: SimHei;
          }
          .engName, .cnName {
            font-size: 0.22rem;
            font-family: PingFang-SC-Medium;
          }
          .engName {
            padding: 0.15rem 0 0.08rem 0;
          }
          .cnName {
            color: #999999;
          }
        }
        .extend-item-split {
          flex-basis: 0.012rem;
          margin-top: 0.46rem;
          height: 0.45rem;
          background-color: #f28300;
        }
      }
    }
  }
</style>
