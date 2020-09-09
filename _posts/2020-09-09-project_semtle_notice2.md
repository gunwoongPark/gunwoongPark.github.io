---
layout: single
title: "셈틀꾼(동아리) 홈페이지 제작 프로젝트 - 공지사항 2"
categories: 
   - Project
author_profile: true
read_time: true
comments: true
---

이전에 가장 먼저 할당받은 작업 중 하나인 공지사항을 조금(많이) 수정했다.

아무래도 전체적인 테마와 맞추고, 통신도 연결을 해야하기 때문이다.

무엇보다도 쓸데없는 props 들 때문에 코드가 마음에 들지 않았다.

이러한 부분들을 바꾸기 위해 대대적인 수정에 들어가 완성했다.

# 코드

우선 HTML 코드들이 있는 template 태그를 살펴보겠다.

## template tag

```html
<template>
<v-container class="px-0">
    <v-row>
        <v-col cols="12" lg="3">
            <SubTitle :subTitleObj="subTitleObj" />
        </v-col>
        <v-col cols="12" lg="9">
            <div id="noticeRead">
                <v-container class="px-0">
                    <v-row>
                        <v-col cols="12">
                            <v-card id="contentBox">
                                <v-alert outlined color="#365164">
                                    <v-row class="py-0">
                                        <v-col cols="12" class="py-0">
                                            <v-card-title class="font-weight-black">{% raw %}{{title}}{% endraw %}</v-card-title>
                                        </v-col>
                                        <v-col class="text-right" cols="12" v-show="admin">
                                            <v-btn color="error" @click="deleteNotice">삭제</v-btn>
                                        </v-col>
                                    </v-row>
                                    <v-divider class="mb-2"></v-divider>
                                    <ul class="noticeInfo">
                                        <li>
                                            <b>작성자</b>{% raw %}{{writer}}{% endraw %}
                                        </li>
                                        <li>
                                            <b>작성일</b>{% raw %}{{date}}{% endraw %}
                                        </li>

                                        <li>
                                            <v-icon small>mdi-eye</v-icon>{% raw %}{{views}}{% endraw %}
                                        </li>
                                    </ul>
                                    <v-card-text style="color: #000;">
                                        <vue-markdown :source="contents" class="ml-2"></vue-markdown>
                                    </v-card-text>
                                    <v-card-text>
                                        <div id="imageContainer">
                                            <img :src="imageUrl" width="100%;" />
                                        </div>
                                    </v-card-text>
                                </v-alert>
                            </v-card>
                        </v-col>
                    </v-row>
                </v-container>
            </div>
        </v-col>
    </v-row>
</v-container>
</template>
```

***

<br/>

다음은 로직이 있는 script 태그를 살펴보겠다.

## script tag

```vue
<script>
import ipObj from "../key";
import VueMarkdown from "vue-markdown";
import SubTitle from "../components/SubTitle.vue";

export default {
    created() {
        this.admin = JSON.parse(sessionStorage.getItem("admin"));
        this.noticeID = this.$route.params.id;
        this.axios
            .get(`${ipObj.ip}/api/notice/${this.noticeID}`)
            .then((res) => {
                console.log(res);

                if (res.status === 200) {
                    this.title = res.data.notice.title;
                    this.writer = res.data.notice.writer;
                    this.views = res.data.notice.view;
                    this.date = res.data.notice.date;
                    this.contents = res.data.notice.contents;
                    this.imageUrl = `${ipObj.ip}/api/notice/images/`.concat(
                        res.data.notice.image
                    );
                    // 이미지도 추가
                }
            })
            .catch((err) => {
                console.log(err);
            });
    },
    data: () => ({
        admin: false,
        noticeID: "",
        title: "",
        writer: "",
        date: "",
        views: 0,
        imageUrl: "",

        contents: "",
        subTitleObj: {
            title: "📌공지사항",
            contents: "셈틀꾼의 공지사항을 올리는 공간입니다.",
        },
    }),

    components: {
        VueMarkdown,
        SubTitle,
    },
    methods: {
        deleteNotice() {
            let result = confirm("정말로 삭제하시겠습니까?");
            if (result) {
                this.axios
                    .delete(`${ipObj.ip}/api/notice/${this.noticeID}`, {
                        headers: {
                            token: sessionStorage.getItem("token"),
                        },
                    })
                    .then((res) => {
                        if (res.status === 200) {
                            this.$router.push({
                                name: "noticeList",
                            });
                        }
                    });
            }
        },
    },
};
</script>
```

***

<br/>

마지막으로 스타일태그를 보고 마치겠다.

## style tag

```css
<style>
#noticeRead hr {
    border-top: 1px solid #365164;
}

#noticeRead b {
    margin-right: 3px;
}

#noticeRead ul {
    padding-left: 15px;
    font-size: 12px;
}

.noticeInfo {
    color: gray;
    margin-bottom: 30px;
}

.noticeInfo li {
    list-style: none;
    display: inline;
    margin-right: 10px;
}
</style>
```