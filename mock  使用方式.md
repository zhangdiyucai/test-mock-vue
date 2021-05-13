```
mock  使用方式 这种方式不能实现网站  只能是特殊的网址   除了easy mock 有时网址链接不了
```

```
// 引入mockjs   mock使用 一个单独的文件
mock/api.js
const Mock = require('mockjs');
// 获取 mock.Random 对象
const Random = Mock.Random;
// mock一组数据
const produceNewsData = function() {
	let articles = [];
	for (let i = 0; i < 100; i++) {
		let newArticleObject = {
			title: Random.csentence(5, 30), //  Random.csentence( min, max )
			thumbnail_pic_s: Random.dataImage('300x250', 'mock的图片'), // Random.dataImage( size, text ) 生成一段随机的 Base64 图片编码
			author_name: Random.cname(), // Random.cname() 随机生成一个常见的中文姓名
			date: Random.date() + ' ' + Random.time() // Random.date()指示生成的日期字符串的格式,默认为yyyy-MM-dd；Random.time() 返回一个随机的时间字符串
		}
		articles.push(newArticleObject)
	}

	return {
		articles: articles
	}
}

// Mock.mock( url, post/get , 返回的数据)；
Mock.mock('/news/index', 'post', produceNewsData);
```

```
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.

mian.js 
import Vue from 'vue'
import App from './App'
import router from './router'

Vue.config.productionTip = false

// 引入mockjs
require('.mock/api.js')

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  template: '<App/>',
  components: {
    App
  }
})
```

```

。vue  页面使用个

<template>
  <div class="index">
    <div v-for="(item, key) in newsListShow">
      <news-cell :newsDate="item" :key="key"></news-cell>
    </div>
  </div>
</template>

<script>
// import api from './../axios/api.js'
import NewsCell from './NewsCell.vue'
import axios from 'axios'

export default {
  name: 'index',
  data () {
    return {
      newsListShow: [],
    }
  },
  components: {
    NewsCell
  },
  created () {
    this.setNewsApi();
  },
  methods: {
    setNewsApi: function () {
      // api.JH_news('/news/index', 'type=top&key=123456')
      //   .then(res => {
      //     console.log(res);
      //     this.newsListShow = res.articles;
      //   });

      axios.post('/news/index').then((res) => {        console.log('xxxxx', res);
      }).catch((err) => {
        console.log('xxx', err);

      })
    },
  }
}
</script>
```

```
mock  使用方式二 

使用  express 方式 
否则直接请求你会出现苦于问题

```

```
import Vue from 'vue'
import App from './App.vue'
// require('./mock/api.js')   这种方式不需要映入 
直接在后台开启微服务  express  本地服务器

// \
Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app')

```

```
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      For a guide and recipes on how to configure / customize this project,<br>
      check out the
      <a href="https://cli.vuejs.org" target="_blank" rel="noopener">vue-cli documentation</a>.
    </p>
    <h3>Installed CLI Plugins</h3>
    <ul>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-babel" target="_blank"
          rel="noopener">babel</a></li>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-eslint" target="_blank"
          rel="noopener">eslint</a></li>
    </ul>
    <h3>Essential Links</h3>
    <ul>
      <li><a href="https://vuejs.org" target="_blank" rel="noopener">Core Docs</a></li>
      <li><a href="https://forum.vuejs.org" target="_blank" rel="noopener">Forum</a></li>
      <li><a href="https://chat.vuejs.org" target="_blank" rel="noopener">Community Chat</a></li>
      <li><a href="https://twitter.com/vuejs" target="_blank" rel="noopener">Twitter</a></li>
      <li><a href="https://news.vuejs.org" target="_blank" rel="noopener">News</a></li>
    </ul>
    <h3>Ecosystem</h3>
    <ul>
      <li><a href="https://router.vuejs.org" target="_blank" rel="noopener">vue-router</a></li>
      <li><a href="https://vuex.vuejs.org" target="_blank" rel="noopener">vuex</a></li>
      <li><a href="https://github.com/vuejs/vue-devtools#vue-devtools" target="_blank" rel="noopener">vue-devtools</a>
      </li>
      <li><a href="https://vue-loader.vuejs.org" target="_blank" rel="noopener">vue-loader</a></li>
      <li><a href="https://github.com/vuejs/awesome-vue" target="_blank" rel="noopener">awesome-vue</a></li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios'
//按需引入api文件, 但后面会导致删除麻烦的问题.
// import '../mock/api.js'    不再使用这种弄方式 

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  mounted () {
    axios.post('http://localhost:8090/user/info').then((res) => {
      if (res.status == 200) {
        console.log(
          'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx', res.data.dataSource
        );

      }
    })

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>

```

```
const Mock = require('mockjs');
const express = require('express');   //引入express


let app = express();        //实例化express


// const url = {
//   tableDataOne: '/news/index',
//   // tableDataTwo: 'http://20181024Mock.com/mode1/tableDataTwo',
//   // tableDataThi: 'http://20181024Mock.com/mode1/tableDataThi',
// }

//允许跨域
app.use(function (req, res, next) {
  res.header("Access-Control-Allow-Origin", "*");
  res.header('Access-Control-Allow-Methods', 'PUT, GET, POST, DELETE, OPTIONS');
  res.header("Access-Control-Allow-Headers", "X-Requested-With");
  res.header('Access-Control-Allow-Headers', 'Content-Type');
  next();
});



app.use('/user/info', function (req, res) {
  res.json(Mock.mock({
    'dataSource|5': [{
      'key|+1': 1,
      'mockTitle|1': ['哑巴', 'Butter-fly', '肆无忌惮', '摩天大楼', '初学者'],
      'mockContent|1': ['你翻译不了我的声响', '数码宝贝主题曲', '摩天大楼太稀有', '像海浪撞破了山丘'],
      'mockAction|1': ['下载', '试听', '喜欢']
    }]
  }))
})


// Mock.mock(url.tableDataOne, 'post', {
//   'dataSource|5': [{
//     'key|+1': 1,
//     'mockTitle|1': ['哑巴', 'Butter-fly', '肆无忌惮', '摩天大楼', '初学者'],
//     'mockContent|1': ['你翻译不了我的声响', '数码宝贝主题曲', '摩天大楼太稀有', '像海浪撞破了山丘'],
//     'mockAction|1': ['下载', '试听', '喜欢']
//   }]
// })

// express

app.listen('8090', () => {
  console.log('监听端口 8090')
})
```

```
{
  "name": "vue1",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint",
    "mock": "node ./src/mock/api.js",  packjson  使用方式   可以一个但是俩个同时会出现问题
    "mock-serve": "vue-cli-service serve &&  node ./src/mock/api.js "  无法使用
  },
  "dependencies": {
    "axios": "^0.21.1",
    "core-js": "^3.6.5",
    "express": "^4.17.1",
    "mockjs": "^1.1.0",
    "vue": "^2.6.11"
  },
  "devDependencies": {
    "@vue/cli-plugin-babel": "~4.5.0",
    "@vue/cli-plugin-eslint": "~4.5.0",
    "@vue/cli-service": "~4.5.0",
    "babel-eslint": "^10.1.0",
    "eslint": "^6.7.2",
    "eslint-plugin-vue": "^6.2.2",
    "vue-template-compiler": "^2.6.11"
  },
  "eslintConfig": {
    "root": true,
    "env": {
      "node": true
    },
    "extends": [
      "plugin:vue/essential",
      "eslint:recommended"
    ],
    "parserOptions": {
      "parser": "babel-eslint"
    },
    "rules": {}
  },
  "browserslist": [
    "> 1%",
    "last 2 versions",
    "not dead"
  ]
}
```

