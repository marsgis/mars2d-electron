<template>
  <div :id="withKeyId" class="mars2d-container"></div>
</template>
<script setup lang="ts">
/**
 * 地图渲染组件
 * @copyright 火星科技 mars2d.cn
 * @author 火星吴彦祖 2022-02-19
 */
import { computed, onUnmounted, onMounted } from "vue"
// import * as mars2d from "mars2d" 
// import { $alert, $message } from "@mars/components/mars-ui/index"
const mars2d = window.mars2d

const props = withDefaults(
  defineProps<{
    url: string
    mapKey?: string
    options?: any
  }>(),
  {
    url: "",
    mapKey: "default",
    options: () => ({})
  }
)

// 用于存放地球组件实例
let map // 地图对象

// 使用用户传入的 mapKey 拼接生成 withKeyId 作为当前显示容器的id
const withKeyId = computed(() => `mars2d-container-${props.mapKey}`)

onMounted(() => {
  // 获取配置
  mars2d.Util.fetchJson({ url: props.url }).then((data: any) => {
    initMars2d({
      // 合并配置项
      ...data.mars2d,
      ...props.options
    })
  })
})

// onload事件将在地图渲染后触发
const emit = defineEmits(["onload"])
const initMars2d = (option: any) => {
  map = new mars2d.Map(withKeyId.value, option)

  // //如果有xyz传参，进行定位
  // const lat = getQueryString("lat")
  // const lng = getQueryString("lng")
  // if (lat && lng) {
  //   map.flyToPoint(new mars2d.LngLatPoint(lng, lat), { duration: 0 })
  // }

  // 开场动画
  // map.openFlyAnimation();

  // 针对不同终端的优化配置
  if (mars2d.Util.isPCBroswer()) {
    map.zoomFactor = 2.0 // 鼠标滚轮放大的步长参数

    // IE浏览器优化
    if (window.navigator.userAgent.toLowerCase().indexOf("msie") >= 0) {
      map.viewer.targetFrameRate = 20 // 限制帧率
      map.scene.requestRenderMode = false // 取消实时渲染
    }
  } else {
    map.zoomFactor = 5.0 // 鼠标滚轮放大的步长参数

    // 移动设备上禁掉以下几个选项，可以相对更加流畅
    map.scene.requestRenderMode = false // 取消实时渲染
    map.scene.fog.enabled = false
    map.scene.skyAtmosphere.show = false
    map.scene.globe.showGroundAtmosphere = false
  }

  // //二三维切换不用动画
  if (map.viewer.sceneModePicker) {
    map.viewer.sceneModePicker.viewModel.duration = 0.0
  }

  // webgl渲染失败后，刷新页面
  // map.on(mars2d.EventType.renderError, async () => {
  //   await $alert("程序内存消耗过大，请重启浏览器")
  //   window.location.reload()
  // })

  // map构造完成后的一些处理
  onMapLoad()

  emit("onload", map)
}

// map构造完成后的一些处理
function onMapLoad() {
  // // Mars2D地图内部使用，如右键菜单弹窗
  // // @ts-ignore
  // window.globalAlert = $alert
  // // @ts-ignore
  // window.globalMsg = $message

  // // 用于 config.json 中 西藏垭口 图层的详情按钮 演示
  // // @ts-ignore
  // window.showPopupDetails = (item: any) => {
  //   $alert(item.NAME)
  // }

 
}

// 组件卸载之前销毁mars2d实例
onUnmounted(() => {
  if (map) {
    map.destroy()
    map = null
  }
  console.log("map销毁完成", map)
})
</script>

<style> 
</style>
