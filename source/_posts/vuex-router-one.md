---
title: vuex路由刷新数据重置和F5刷新数据保持
date: 2018-10-18 22:00:31
categories: front-end
tags: vuex
---

项目中遇到的问题，记录一下解决方案。
![pic](https://blog-10039692.file.myqcloud.com/1504751009021_60_1504751009399.jpeg)

<!-- more -->
### **一、vuex路由刷新数据更新**
vuex 的设计是将数据存在一个对象树的变量中，我们的应用（vue应用）从这个变量中取数据，然后供应用使用，当将当前页面关闭， vuex 中的变量会随着消失，重新打开页面的时候，需要重新生成。
而，浏览器缓存（cookie，localstorage等）是将数据存到浏览器的某个地方，关闭页面，不会自动清空这些数据，当再次打开这个页面时，还是能取到之前存在浏览器上的数据（cookie，localstorage等）。
要使用 vuex 还是使用浏览器缓存，要看具体的业务场景。比如：像用户校验的 token 就可以存在 cookie 中，因为用户再次登录的时候能用到。而像用户的权限数据，这些是有一定安全性考虑，且不同用户的权限不同，放在 vuex 中更合理，用户退出时，自动销毁。

#### **1）组件刷新**
provider、reject的组件刷新方式
作用：允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。
#### **App.vue:**
```
<template>
    <div class="qb-app">
        <div class="qb-content">
            <qb-header></qb-header>
            <router-view v-if="isRouterAlive"></router-view>
        </div>
        <qb-footer></qb-footer>
    </div>
</template>

<script>
import QbHeader from '@/components/QbHeader'
import QbFooter from '@/components/QbFooter'
import store from '@/store/index'


export default {
    name: 'App',
    provide(){
        return {
            reload:this.reload
        }
    },
    data(){
        return {
            isRouterAlive:true
        }
    },
    methods:{
        reload(){
            this.isRouterAlive = false
            this.$nextTick(function(){
            this.isRouterAlive = true
            })
        },
        resetStates() {
            store.dispatch('changeResetState', {
            })
        }
    },
    // beforeRouteLeave(to,from,next){

    // },
    watch : {
        $route(to,from) {
           this.resetStates(); 
           console.log('watch正在监控路由变化')
        }
    },
    components: {
        QbHeader,
        QbFooter
    }
}
</script>
```
#### **createpage/index.vue**
```
<template>
    <div class="create-page">
        <el-row :gutter="20">
            <el-col :span="8" class="style-aside-left" style="padding-left: 20px;padding-right: 20px;">
                <style-select class="style-select" ></style-select>
                <style-setting ></style-setting>
            </el-col>
            <el-col :span="16" class="style-aside-right" style="padding-left: 0px;padding-right: 0px;">
                <style-preview ></style-preview>
            </el-col>
        </el-row> 
    </div>

</template>
<script type="text/javascript">
    import StyleSelect from '@/page/createPage/StyleSelect'
    import StyleSetting from '@/page/createPage/StyleSetting'
    import StylePreview from '@/page/createPage/StylePreview'
    import store from '@/store/index'
    import axios from 'axios'

    export default {
        inject: ['reload'],
        data(){
            return {
                // isReload:false
            }
        },
        components: {
            StylePreview,
            StyleSetting,
            StyleSelect
        },
        mounted () {
            if(this.$route.query.id !== undefined){
                axios.get('/api/itemData', {
                    params: {
                        id: this.$route.query.id
                    }
                }).then(response => {
                    // console.log('itemresponse',response)
                        store.dispatch('changeStyleId', {
                            styleId: response.data.data[0].TempName
                        })
                        store.dispatch('changeValueFromDatabase', {
                            styleId: response.data.data[0].TempName,
                            formData:response.data.data[0].formData
                        })
                        // console.log('response.data.data[0].formData',response.data.data[0].formData)
                        // this.formDataBase = response.data.data[0].formData
                    }).catch(function (error) {
                        console.log(error);
                        console.log('id___error')
                    });
            }else if (this.$route.query.index == 'new' ){
                this.$route.query.index = 'done'
                this.reload();
                return;
            }
        }
    }
</script>
```
#### **2）vuex路由跳转数据重置**
专门用一个新的resetState，来处理需要被重置的内容
#### **store/state.js**
_//截取部分代码_
```
import formConfig from '../common/formConfig'
import initState from './resetState'
export default {
	initState(){
		return initState
	},
	styleId: 'floatlayer'
```
#### **store/mutation.js**
```
	//重新路由 数据重置mutation
	changeResetState(state,data){
	  	if (state.initState && typeof state.initState === 'function') {
		    // const initState = state.initState();
		    const initState = state.initState()
		    console.log('state',state)
		    for (const key in initState) {
	      		state[key] = initState[key];
		    }
	  	}
	}
```
**会出现一个问题**：只在第一次路由跳转时数据重置
**原因**：两个state数据其实是引用数据类型，会指向同一块堆内存，因此需要在数据重置时，在mutation里面进行深拷贝:
#### **store/mutation.js**
```
	//重新路由 数据重置mutation
	changeResetState(state,data){
	  	if (state.initState && typeof state.initState === 'function') {
		    // const initState = state.initState();
		    const initState = JSON.parse(JSON.stringify(state.initState()))
		    console.log('state',state)
		    for (const key in initState) {
	      		state[key] = initState[key];
		    }
	  	}
	}
```
### **二、vuex F5刷新页面 数据保持**
事实上并没有什么有效的方法让vuex在刷新的时候数据保持，现在试过唯一可行的办法就是利用浏览器的localstotrage，在数据更新store时保存一份到localstorage中，代码如下：
#### **store/mutation.js**
```
	changeValue (state, data) {
		const item = state.styleSettingDataMap[data.styleId].find(item => item.key === data.key)
		// localStorage.setItem(data.key, data.value); 
		item.value = data.value

	}
```
#### **store/state.js**
```
{
			name: '主标题',
			key: 'title',
			type: 'text',
			value: localStorage.getItem("title") || '您使用的浏览器存在安全隐患'
		},{
			name: '副标题',
			key: 'desc',
			type: 'text',
			value: localStorage.getItem("desc") || '微软已停止更新和安全维护，建议升级快速安全稳定的 QQ浏览器'
		}
```
                
完结  项目中遇到的两个小问题，是以自己的项目为例，记录以为自用。

    

