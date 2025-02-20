<script setup>
import { defineProps } from "vue";
import { useLoggedInStore } from "@/stores/LoggedInStore";
import { DELETE_POST_API } from "@/api/PostApi";

const loggedInStroe = useLoggedInStore();

// === 解析參數 ===
const { post } = defineProps(["post"]);
const { memberName, postText, createdDate, memberPhoto, replies, resources } =
  post;

// === 刪除貼文 ===
async function deletePost(postId) {
  await DELETE_POST_API(postId);
  location.reload();
}
</script>

<template>
  <v-container>
    <v-row>
      <v-col>
        <!-- === 貼文內容 === -->
        <v-card>
          <!-- 貼文者與時間 -->
          <template #title>
            <div class="title">
              <div class="member_home">
                <img :src="memberPhoto" class="avatar" />
                {{ memberName }}
              </div>
              <div class="createdDate">{{ createdDate }}</div>
            </div>
          </template>

          <!-- 貼文內容 -->
          <template #text>
            <div class="text">{{ postText }}</div>
          </template>

          <!-- 貼文圖片 -->
          <div class="image_home">
            <template v-for="resource in resources">
              <img :src="resource.content" class="post_image" />
            </template>
          </div>

          <!-- 功能按鈕 -->
          <v-card-actions>
            <div class="functional_buttons" v-show="loggedInStroe.isLoggedIn">
              <!-- 刪除按鈕 -->
              <v-btn
                rounded="xl"
                color="red"
                variant="outlined"
                v-if="post.memberId == loggedInStroe.memberId"
                @click="deletePost(post.postId)"
              >
                刪除
              </v-btn>
              <!-- 留言按鈕 -->
              <v-btn rounded="xl" color="primary" variant="outlined">
                留言
              </v-btn>
            </div>
          </v-card-actions>

          <!-- === 分隔線 === -->
          <v-divider
            :thickness="5"
            class="border-opacity-75"
            color="#a8a8a8"
            inset
          ></v-divider>

          <!-- === 留言區 === -->
          <v-container>
            <v-row v-for="reply in replies" :key="reply.replyId">
              <v-col>
                <v-card variant="tonal">
                  <!-- 留言者 -->
                  <template #title>
                    <div class="title">
                      <div class="member_home">
                        <img :src="reply.memberPhoto" class="avatar" />
                        {{ reply.memberName }}
                      </div>
                      <div class="createdDate">{{ reply.createdDate }}</div>
                    </div>
                  </template>

                  <!-- 留言文字 -->
                  <template #text>
                    <div class="text">{{ reply.replyText }}</div>
                  </template>
                </v-card>
              </v-col>
            </v-row>
          </v-container>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped>
.member_home {
  display: flex;
  align-items: center;
  gap: 10px;
}

.avatar {
  width: 50px;
}

.post_image {
  margin: 3px;
  border: 3px solid black;
  width: 200px;
  height: 200px;
}

.image_home {
  margin-left: 20px;
  margin-right: 20px;
  margin-bottom: 20px;
  display: flex;
  overflow-x: auto;
  scrollbar-width: thin;
}

.functional_buttons {
  display: flex;
  width: 100%;
  justify-content: end;
  padding-right: 20px;
  margin-bottom: 10px;
  gap: 10px;
}

.title {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
}

.createdDate {
  font-size: small;
  padding-top: 10px;
  color: grey;
}

.text {
  font-size: x-large;
}
</style>
