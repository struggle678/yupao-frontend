<template>
  <Top />
  <user-card-list :user-list="userList" :loading="loading"/>
  <van-empty v-if="!userList || userList.length < 1" description="搜索结果为空" />
  <Bottom />
</template>

<script setup lang="ts">
import {onMounted, ref} from 'vue';
import {useRoute} from "vue-router";
import myAxios from "../plugins/myAxios";
import {Toast} from "vant";
import qs from 'qs';
import UserCardList from "../components/UserCardList.vue";
import Bottom from "../layouts/Bottom.vue";
import Top from "../layouts/Top.vue";

const route = useRoute();
const {tags} = route.query;
const loading = ref(true);

const userList = ref([]);
console.log("搜索结果userList：",userList)
onMounted(async () => {
  const userListData = await myAxios.get('/user/search/tags', {
    params: {
      tagNameList: tags
    },
    paramsSerializer: params => {
      return qs.stringify(params, {indices: false})
    }
  })
      .then(function (response) {
        console.log('/user/search/tags succeed', response);
        return response?.data;
      })
      .catch(function (error) {
        console.error('/user/search/tags error', error);
        Toast.fail('请求失败');
      })
  console.log("搜索结果userListData：",userListData)

  if (userListData) {
    userListData.forEach(user => {
      if (user.tags) {
        user.tags = JSON.parse(user.tags);
      }
    })
    userList.value = userListData;
  }
  loading.value = false;
})



</script>

<style scoped>

</style>
