<template>
  <div>
    <user-card-list
      :user-list="userList"
      :loading="isLoading"
    />
    <van-empty v-if="!userList || userList.length < 1" description="搜索结果为空" />
  </div>
</template>

<script setup lang="ts">
import {onMounted, ref} from 'vue';
import {useRoute} from "vue-router";
import myAxios from "../plugins/myAxios";
import {Toast} from "vant";
import qs from 'qs';
import UserCardList from "../components/UserCardList.vue";

const route = useRoute();
const {tags} = route.query;

const userList = ref([]);
const isLoading = ref(true); // 新增 isLoading 变量

onMounted(async () => {
  try {
    const userListData = await myAxios.get('/user/search/tags', {
      params: {
        tagNameList: tags
      },
      paramsSerializer: params => {
        return qs.stringify(params, {indices: false})
      }
    }).then(function (response) {
      console.log('/user/search/tags succeed', response);
      return response?.data;
    });

    console.log(userListData);

    if (userListData) {
      userListData.forEach(user => {
        if (user.tags) {
          user.tags = JSON.parse(user.tags);
        }
      });
      userList.value = userListData;
      isLoading.value = false; // 数据加载完成后设置为 false
    }
  } catch (error) {
    console.error('/user/search/tags error', error);
    Toast.fail('请求失败');
    isLoading.value = false; // 如果发生错误，也要确保设置为 false
  }
});

</script>

<style scoped>

</style>