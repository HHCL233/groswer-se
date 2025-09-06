<template>
  <wintab :url="[]"
    :menu="[{ name: '主页', icon: 'M904.699382 574.392002l58.97416-58.97416-118.529557-118.520347 0.005117-213.882252-81.07041 0 0.00307 132.827191L513.284761 65.066925l-1.11438 1.113357-1.113357-1.113357L60.696896 515.416819l58.993602 58.95574 70.992891-70.991868 0.008186 456.137715 81.050967 0L271.742543 959.485661l492.354568 0.027629 0 0.005117 81.032548 0 0 0 0.019443 0L845.149101 878.429577l-0.017396 0 0.00921-363.590925L904.699382 574.392002zM764.095064 878.434693l-168.609139 0.010233L595.485925 607.621824l-166.594249 0 0 270.831288-157.149133 0.00921 0-456.138738 239.313457-239.308341 1.113357 1.113357 1.11438-1.113357 250.80007 250.77551L764.095064 878.434693z' },]"
    titlebar="false" titlebartext="" style="height: calc( 100% - 0.9px );">
    <div class="window">
      <wincard class="top_card">
        <winbutton @click="back_url()" id="go"><svg t="1757146586210" class="icon" viewBox="0 0 1024 1024" version="1.1"
            xmlns="http://www.w3.org/2000/svg" p-id="2500" width="22" height="22">
            <path
              d="M928.090221 429.52448L512.563821 55.71968 88.073581 437.20448c-5.52576 5.52448-10.81344 21.05472-1.28 28.92032 5.49248 3.4752 5.49248 3.4752 12.34048 4.2496h114.87744v337.2544H446.628461V597.53856h137.80864V807.6288h233.8944v-342.784c0-11.05536-14.89536-22.11456-25.95456-22.11456-11.05664 0-24.67456 11.06048-24.67456 22.11456v293.84192H629.127021V548.59776H397.275501v210.09024H257.421421V443.54944c0-11.06048-5.5296-16.58496-16.58496-16.58496h-74.432L512.563821 117.40672 894.043501 464.8448c5.5296 5.5296 11.06048 6.8096 11.06048 6.8096 4.62208 0.8256 14.1696 1.94304 24.26624-6.8096 7.39584-5.8048 9.47584-19.4048 5.12-27.64032l-6.4-7.68z m-34.04672 35.32032c5.92256 5.2352 11.06048 6.8096 11.06048 6.8096"
              fill="" p-id="2501"></path>
          </svg></winbutton>
        <wininputbox class="url_input" placeholder="URL..." @change="url = $event.target.value" :value="url" />
        <winbutton @click="go_url(url)" id="go">Go</winbutton>
      </wincard>
      <div class="webview" ref="container" @click="handleWebviewClick">
        <div v-html="gtml" />
      </div>
    </div>
  </wintab>
</template>

<script setup lang="ts">
import 'web-win-vue/web-win-vue.css'
import { wincard, wininputbox, winbutton, wintab } from 'web-win-vue'
import { ref } from 'vue'
import { fetch } from '@tauri-apps/plugin-http'

let gtml_html_dict = {
  '<gtml>': '<html>',
  '</gtml>': '</html>',
  '<配置>': '<head>',
  '</配置>': '</head>',
  '<内容>': '<body>',
  '</内容>': '</body>',
  '<字': '<p',
  '</字': '</p',
  '<标题1': '<h1',
  '</标题1': '/<h1',
  '<标题2': '<h2',
  '</标题2': '</h2',
  '<标题3': '<h3',
  '</标题3': '</h3',
  '<标题4': '<h4',
  '</标题4': '</h4',
  '<标题5': '<h5',
  '</标题5': '</h5',
  '<标题6': '<h6',
  '</标题6': '</h6',
  '<链接': '<a',
  '</链接': '</a',
  '<表格>': '<table>',
  '</表格': '</table>',
  '<行': '<tr',
  '</行>': '</tr',
  '<列标题>': '<th',
  '</列标题>': '</th',
  '<格>': '<td',
  '</格>': '</td',
  '<图像': '<img',
  '路径': 'src',
  '宽度': 'width',
  '高度': 'height',
  '链接': 'href',
  '<加粗': '<b',
  '</加粗': '</b',
  '<下标': '<sub',
  '</下标': '</sub',
  '<上标': '<sup',
  '</上标': '</sup',
  '<网页名': '<title',
  '</网页名': '</title',
  '<节': '<div',
  '</节': '</div',
  '<缩写': '<abbr',
  '</缩写': '</abbr',
  '<联系人': '<address',
  '</联系人': '</address',
  '<文章内容': '<article',
  '</文章内容': '</article',
  '<侧栏': '<aside',
  '</侧栏': '</aside',
  '<音频': '<audio',
  '</音频': '</audio',
  '音频面板': 'controls',
  '<块级引用': '<blockquote',
  '</块级引用': '</blockquote',
};
let gtml_html_dict_1 = new RegExp(Object.keys(gtml_html_dict).join("|"), "g");

let url = ref("about:blank")
let backurl = ref("gorswer://home")

let gtml = ref("")

function update_html(elements: any) {
  console.log(elements)
  console.log(url.value)
  gtml.value = elements
}
function back_url() {
  go_url(backurl.value)
}
let groswer_ver = "0.1.0 (Beta版) (64位)"

let groswer_url = ["gorswer://home", "gorswer://version", "gorswer://error", "about:blank", "gorswer://about"]
let groswer_url_elements = [
  "Gorswer-SE的主页  <br> <a href='#' data-target='gorswer://about' class='internal-link'>gorswer://about</a>",
  `<table cellspacing="10" style="font-size: 0.9em;">
    <tr>
      <td style="font-weight: bold;">Gorswer-SE:</td>
      <td>${groswer_ver}</td>
    </tr>
    <tr>
      <td style="font-weight: bold;">操作系统:</td>
      <td>${window.navigator.platform}</td>
    </tr>
    <tr>
      <td style="font-weight: bold;" colspan="2">此浏览器基于 Tauri 及其他开源软件。</td>
    </tr>
  </table>`,
  '<div style="color: red;">无法加载页面: ${targetUrl}</div>',
  '',
  `Gorswer-SE URLs
  <br> 
  <a href='#' data-target='gorswer://about' class='internal-link'>gorswer://about</a>
  <br>
  <a href='#' data-target='gorswer://version' class='internal-link'>gorswer://version</a> 
  <br> 
  <a href='#' data-target='gorswer://error' class='internal-link'>gorswer://error</a>
  <br> 
  <a href='#' data-target='gorswer://home' class='internal-link'>gorswer://home</a>
  <br> 
  `
]

function handleWebviewClick(e: MouseEvent) {
  const target = e.target as HTMLElement
  if (target.classList.contains('internal-link')) {
    e.preventDefault()
    const targetUrl = target.dataset.target
    if (targetUrl) {
      go_url(targetUrl)
    }
  }
}

async function go_url(targetUrl: string) {
  url.value = targetUrl
  if (groswer_url.includes(targetUrl)) {
    const index = groswer_url.indexOf(targetUrl)
    update_html(groswer_url_elements[index])
  } else {
    try {
      const response = await fetch(targetUrl, {
        method: 'GET',
      })
      const html_code = await response.text()
      console.log('请求状态:', response.status)
      // 这里记得写gtml处理部分
      let gtml_code = html_code.replace(gtml_html_dict_1, (matched) => gtml_html_dict[matched as keyof typeof gtml_html_dict]);
      update_html(gtml_code)
    } catch (error) {
      console.error('请求错误:', error)
      update_html(`<div style="color: red;">无法加载页面: ${targetUrl}</div>`)
    }
  }
}
</script>

<style scoped>
.top_card {
  height: 38px;
  width: 100%;
  display: flex;
  flex-wrap: nowrap;
  justify-content: space-between;
  flex: 0 0 auto;
  gap: 8px;
}

.url_input {
  width: 100%;
}

.webview {
  flex: auto;
  padding: 16px;
  min-height: 200px;
}

.window {
  display: flex;
  flex-direction: column;
  height: 100%;
  box-sizing: border-box;
}

.webview :deep(.internal-link) {
  color: #0066cc;
  text-decoration: underline;
  cursor: pointer;
}

.webview :deep(.internal-link):hover {
  color: #004080;
}
</style>
