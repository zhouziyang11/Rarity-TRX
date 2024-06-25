
<template>
  <div class="content">
    <div class="logo">
      <span>Rarity TRX</span>
      <p>波场靓号 生成器</p>
    </div>
    <p class="kaishi" style="text-align:center;color:#fff">
      <span>
        <a href="#createBox">立即开始</a>
      </span>
    </p>
    <p style="text-align:center;color:#fff">郑重说明：Rarity TRX绝对不会存储你的任何信息，代码中没有任何网络请求,完全本地生成，可以断网生成</p>
    <div class="tip">
      <div class="tip_item">
        <p class="tip_item_title">什么是靓号？</p>
        <p>波场靓号是指由你自己选择的TRC20地址，使其看起来不那么普通。</p>
        <p>比如:</p>
        <p>
          TBkc75QyLnykcfEH6vZRYbUU47wboo
          <span class="red">UUUU</span>
        </p>
        <p>
          TNp4oZPYep1Nc1xypg5zgoWG3QKWiC
          <span class="red">1111</span>
        </p>
        <p>
          TNTHoa
          <span class="red">8888</span>hHCSNt2eWPY9M2gAgeqLxvhd5N
        </p>
      </div>
      <div class="tip_item">
        <p class="tip_item_title">工作原理</p>
        <p>输入你选择的前缀/后缀，然后点击 “生成” 按钮开始。你的浏览器将生成大量随机波场靓号地址，并筛选出与你输入相匹配的。</p>
        <p>找到喜欢的TRX靓号地址后，你可以复制私钥导入到钱包</p>
      </div>
      <div class="tip_item">
        <p class="tip_item_title">安全性</p>
        <p>如上所述，所有内容仅在你电脑的浏览器中计算并生成。</p>
        <p>任何东西都不会离开你的机器，甚至你的浏览器。没有数据库，没有服务端代码。当你关闭浏览器时，一切都消失了。</p>
      </div>
      <div class="tip_item">
        <p class="tip_item_title">郑重承诺</p>
        <p>Rarity TRX绝对不会，也没有办法去保存你生成的内容。</p>
        <p>Rarity TRX支持离线断网生成</p>
        <p>注意：全部数据只保存在你的浏览器，浏览器关闭将全部消失</p>
      </div>
      <div class="tip_item">
        <p class="tip_item_title">性能表现</p>
        <p>因技术规范和内核原因，Rarity TRX波场靓号生成器在不同浏览器的性能表现可能会有较大差异，目前Chrome提供了最好的效果。</p>
        <p>在你的手机或平板电脑上也可以使用Rarity TRX波场靓号生成器， 但效果没有电脑好。</p>
      </div>
    </div>
    <div class="createBox" id="createBox">
      <div class="from">
        <input class="input" placeholder="请输入你想要包含的后缀，如：888" type="text" v-model="weihao" />
        <div>
          <button
            :style="{background:loading?'#ccc':''}"
            @click="submit"
          >{{tiaojian == 0 ? '生成' : '重新生成'}}</button>
          <br />
        </div>
        <span class="total">
          <p v-if="total != 0">已生成：{{total}} 个{{list.length ? ` 已筛选出${list.length}个` : ''}}</p>
          <p class="red">
            {{
            tiaojian == 0 ? '' :
            tiaojian == 1 ? '已经生成了足够多的地址，如果还是没有你想要的，点击从新开始' :
            '已生成符合你要求的地址'
            }}
          </p>
        </span>
      </div>
      <div class="resList">
        <template v-if="list.length">
          <div v-for="(item,index) in list" :key="index">
            <p class="address">
              地址：
              <span v-html="item.showAddress"></span>
            </p>
            <p class="key">
              私钥：
              <span @click="copy(item.privateKey)">复制</span>
            </p>
          </div>
        </template>
        <div v-else-if="loading">暂未匹配到靓号地址，生成中...</div>
      </div>
    </div>
    <div class="footer">
      <p>
        如果您期望我们能不断开发Rarity TRX波场靓号生成器，以获得更好的体验和更有用的功能，我们期待着您的捐赠
        <a href="https://t.me/zd_9528h">联系我</a>
      </p>
      <p>
        捐赠地址：{{address}}
        <span class="copy" @click="copy(address)">复制</span>
      </p>
    </div>
  </div>
</template>

<script setup>
import TronWeb from "tronweb/dist/TronWeb.js";
import { ref, computed } from "vue";

const address = ref("TFdRPf4wuqaRJgBEeWoX3mwH7jXPDDDDDD");
const list = ref([]);
const total = ref(0);
const num2 = ref(0);
const weihao = ref("");
const reg = computed(() => new RegExp(weihao.value + "$"));

const tronWeb = new TronWeb({
  fullHost: "https://api.trongrid.io"
});

async function sleep() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve();
    }, 0);
  });
}

const loading = ref(false);
let tiaojian = ref(0);
async function submit() {
  if (loading.value) return;
  if (weihao.value.length < 1) {
    return alert("请输入后缀，最少一位");
  }
  if (tiaojian.value === 0) {
    loading.value = true;
    while (tiaojian.value === 0) {
      await sleep();
      const res = await tronWeb.createAccount();
      const showAddress = checkString(res.address.base58);
      if (showAddress) {
        res.showAddress = showAddress;
        list.value.push(res);
        await sleep();
        document.querySelector("body").scrollIntoView(false);
      }
      total.value += 12;
      if (list.value.length > 20) {
        tiaojian.value = 1;
        loading.value = false;
        alert("已经生成了足够多的地址，如果没有你想要的地址，请点击重新生成");
      }
    }
  } else {
    tiaojian.value = 0;
    submit();
    total.value = 0;
    list.value = [];
  }
}
// submit();

function checkString(fullString) {
  if (reg.value.test(fullString)) {
    fullString = insertCharacter(
      fullString,
      fullString.length - weihao.value.length,
      '<span class="red">'
    );
    fullString = insertCharacter(fullString, fullString.length, "</span>");
    tiaojian.value = 2;
    loading.value = false;
    return fullString;
  } else {
    // // 判断字符串中是否存在连续的三个递增字符或连续的三个相同的数字;
    for (let i = 0; i < fullString.length - 2; i++) {
      const char1 = fullString[i];
      const char2 = fullString[i + 1];
      const char3 = fullString[i + 2];
      const char4 = fullString[i + 3];
      const r2 = !isNaN(char1) && char1 === char2 && char1 === char3;
      const r1 = char1 == char2 + 1 && char1 == char3 + 2 && char1 == char4 + 3;
      if (r1 || r2) {
        fullString = insertCharacter(fullString, i, '<span class="red">');
        fullString = insertCharacter(
          fullString,
          i + 3 + '<span class="red">'.length,
          "</span>"
        );
        return fullString;
      }
    }
    return false;
  }
}
function insertCharacter(originalString, position, character) {
  const leftPart = originalString.slice(0, position);
  const rightPart = originalString.slice(position);
  return leftPart + character + rightPart;
}

function copy(e) {
  // 创建一个临时的文本输入框
  const tempInput = document.createElement("input");
  tempInput.value = e;
  document.body.appendChild(tempInput);
  // 选择文本内容
  tempInput.select();
  tempInput.setSelectionRange(0, 99999); // 兼容移动端
  // 复制文本到剪贴板
  document.execCommand("copy");
  // 移除临时文本输入框
  document.body.removeChild(tempInput);
  alert("已复制");
}
</script>

<style lang='scss'>
* {
  box-sizing: border-box;
}
.red {
  color: red;
  font-weight: bold;
}
.kaishi {
  span {
    background-color: #ffffff;
    color: #b94a78;
    border-radius: 2px;
    padding: 6px 16px;
    cursor: pointer;
    font-weight: bold;
    a {
      color: #b94a78;
      text-decoration: none;
    }
  }
  margin-bottom: 25px;
}
body {
  background-color: #a81f58cd;
  padding: 0 20px;
}
.content {
  margin: 0;
  padding: 0;
  font-size: 14px;
  .logo {
    padding: 16px 16px 0 16px;
    text-align: center;
    span {
      font-size: 50px;
      font-weight: bold;
      color: #fff;
      border: 3px solid #fff;
      padding: 6px;
      border-radius: 6px;
    }
    p {
      margin-top: 16px;
      font-weight: bold;
      color: #fff;
    }
  }
  .tip {
    margin: 20px;
    background-color: #fff;
    padding: 16px 25px;
    .tip_item {
      margin-bottom: 30px;
      p {
        margin-bottom: 6px;
      }
      .tip_item_title {
        margin-bottom: 16px;
        font-size: 25px;
      }
    }
  }
  .createBox {
    margin: 20px;
    background-color: #fff;
    padding: 16px 25px;
    .from {
      text-align: center;
      .input {
        width: 100%;
        display: block;
        padding: 16px;
        font-weight: bold;
        font-size: 20px;
      }
      button {
        background-color: #b44976;
        border: none;
        padding: 10px 50px;
        font-size: 22px;
        font-weight: bold;
        color: #fff;
        margin-top: 25px;
        cursor: pointer;
      }
      .total {
        margin-left: 20px;
        font-weight: bold;
      }
    }
    .resList {
      margin-top: 30px;
      > div {
        margin-top: 20px;
        .key {
          display: flex;
          margin-top: 10px;
          span {
            color: #b44976;
            padding: 3px 16px;
            background: #b44976;
            color: #fff;
            display: flex;
            position: relative;
            cursor: pointer;
            &::after,
            &::before {
              content: "";
              display: block;
              width: 100%;
              flex-shrink: 0;
              height: 2px;
              background: #fff;
              position: absolute;
              transition: all 0.15s;
            }
            &::after {
              top: 1px;
              left: -100%;
            }
            &::before {
              bottom: 1px;
              right: -100%;
            }
            &:hover {
              &::after {
                left: 0;
              }
              &::before {
                right: 0;
              }
            }
          }
        }
      }
    }
  }
  .footer {
    background-color: #fff;
    padding: 16px;
    margin: 16px;
    a {
      text-decoration: none;
      margin-left: 20px;
    }
  }
  .copy {
    margin-left: 25px;
    font-size: 14px;
    color: blue;
  }
}
</style>
