<template>
  <div class="common-layout">
    <el-container>
      <el-header>
        <div class="div-header">
          <div class="div-logo">
            <img src="/fire.png" alt="logo" draggable="false">
          </div>
          <div class="div-date">
            <span class="ydate">
              {{ y_date }}<br>
              <span class="ndate">{{ n_date }}</span>
            </span>
          </div>
          <div class="div-theme">
              <el-button circle  @click="toggleDark()" v-if="isDark"><el-icon :size="4*4"><Cloudy /></el-icon></el-button>
              <el-button circle  @click="toggleDark()" v-if="isDark==false"><el-icon :size="4*4"><Sunny /></el-icon></el-button>
          </div>
        </div>
      </el-header>
      <el-main>
        <div class="div-f">
          <el-card :key="idx" style="max-width: 480px" v-for="(v, idx) in card_data">
            <div class="div-head">
              <h3>{{ v.name }}</h3>
            </div>
            <div class="inner">
              <ul class="hot-list-article">
                <!-- <img src="/hot.png" v-if="subItem.hot !=''"> <span>{{ subItem.hot }}</span> -->
                <li v-for="subItem in v.data" :key="subItem.index"> <a :href="subItem.url" target="_blank">{{ subItem.title }}</a></li>
              </ul>
            </div>
          </el-card>
        </div>
      </el-main>
      <el-footer><span><b>Copyright © gwt805 2024.04</b></span></el-footer>
    </el-container>
  </div>

</template>

<script setup lang="ts">
import axios from "axios"
import { ref, onUnmounted } from 'vue'
import { useDark } from '@vueuse/core'
import calendar from "solarday2lunarday"
import { useToggle } from '@vueuse/shared'
import { Sunny, Cloudy } from '@element-plus/icons-vue'

const isDark = useDark({storageKey: 'vitepress-theme-appearance'})
const toggleDark = useToggle(isDark)

const http = axios.create()

const card_data: any = ref([])

const cron_get_date = ref()
const y_date = ref("")
const n_date = ref("")

const getDate = () => {
    const date = new Date()
    const year = date.getFullYear()
    const month = date.getMonth() + 1 >= 10 ? date.getMonth() + 1 : `0${date.getMonth() + 1}`
    const day = date.getDate() >= 10 ? date.getDate() : `0${date.getDate()}`
    const hour = date.getHours() >= 10 ? date.getHours() : `0${date.getHours()}`
    const minute = date.getMinutes() >= 10 ? date.getMinutes() : `0${date.getMinutes()}`
    const second = date.getSeconds() >= 10 ? date.getSeconds() : `0${date.getSeconds()}`

    const a:any = calendar.solar2lunar(year, month, day)
    const gzyear = a["gzYear"]
    const i_month = a["IMonthCn"]
    const i_day = a["IDayCn"]
    const week = a["ncWeek"]

    y_date.value = `${year}-${month}-${day} ${hour}:${minute}:${second}`
    n_date.value = `${gzyear}年 ${i_month}${i_day} ${week}`
}

cron_get_date.value = setInterval(getDate, 1000)

onUnmounted(()=> {
    clearInterval(cron_get_date.value)
})
const get_data = async () => {
  return await http.get("https://api.vvhan.com/api/hotlist/all")
}

get_data().then((res: any) => {
  card_data.value = res.data.data;
})

</script>

<style scoped lang="less">
.common-layout {
  .el-container {
    .el-header {
      position: fixed;
      top: 0;
      width: 100%;
      height: 45px;

      .div-header {
        height: inherit;
        display: flex;
        justify-content: space-around;
        
        .div-logo {
          img {
            width: 40px;
          }
        }

        .div-date {
          .ndate {
            font-size: 12px;
            margin-left: 15px;
            color: gray;
          }
        }

        .div-theme {
          .el-button {
            margin-top: 5px;
          }
        }
      }
    }
    .el-main {
      position: fixed;
      top: 45px;
      width: 100%;
      height: calc(100% - 45px - 20px);

      .div-f {
        display: flex;
        flex-wrap: wrap;
        flex-basis: calc(100%/6);
        justify-content: space-around;
        overflow: auto;
        .div-head {
          margin-top: -20px;
          text-align: center;
        }

        .el-card {
          min-width: 350px;
          max-height: 500px;
          margin-bottom: 20px;
          border-radius: 20px;

          .inner {
            height: 420px;
            overflow: hidden;
            padding-left: 5px;

            .hot-list-article li {
              display: block;
              line-height: 32px;
              position: relative;
              margin: 3px 0 3px -45px;
              counter-increment: nums;
              padding-left: 30px;
              max-width: 260px;

              span {
                color: #787977;
              }
            }

            .hot-list-article li:first-child {
              margin-top: -20px;
            }

            .hot-list-article li a {
              color: #787977;
              text-decoration: none;
            }

            .hot-list-article li:before {
              color: #000;
              width: 22px;
              height: 22px;
              line-height: 22px;
              text-align: center;
              content: counter(nums, decimal);
              position: absolute;
              left: 0;
              top: 5px;
              border-radius: 100%;
              background-color: #edefee;
              text-shadow: 0 1px 0 rgba(255, 255, 255, .5);
              font-family: SourceCodeProRegular, Menlo, Monaco, Consolas, "Courier New", monospace, 'Helvetica Neue', Arial, sans-serif
            }

            .hot-list-article li:first-child:before,
            .hot-list-article li:nth-child(2):before,
            .hot-list-article li:nth-child(3):before {
              color: #fff;
              text-shadow: none
            }

            .hot-list-article li:first-child:before {
              background-color: #e24d46
            }

            /*第1行的行号样式*/
            .hot-list-article li:nth-child(2):before {
              background-color: #2ea7e0
            }

            /*第2行的行号样式*/
            .hot-list-article li:nth-child(3):before {
              background-color: #6bc30d
            }

            /*第3行的行号样式*/
            .hot-list-article li:hover {
              a {
                text-decoration: underline;
                font-size: large;
                display: inline-block;
                transition: transform 0.3s ease-in-out;
                transform: translateX(5px);
              }
            }
          }

          .inner:hover {
            overflow: auto;
          }
        }
      }
    }
    .el-footer {
      position: fixed;
      bottom: 0;
      width: 100%;
      height: 20px;
      text-align: center;
      line-height: 20px;
    }
  }
}
</style>