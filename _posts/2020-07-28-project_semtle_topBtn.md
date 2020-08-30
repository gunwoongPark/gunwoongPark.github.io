---
layout: single
title: "셈틀꾼(동아리) 홈페이지 제작 프로젝트 - 위로 올라가는 버튼"
categories: 
   - Project
author_profile: true
read_time: true
comments: true
---

요즘 쇼핑몰이나 카테고리가 많은 홈페이지들을 보면 대부분 우측하단에 보고있는 페이지의 최상단으로 올라가는 버튼이 있다.

스크롤이 많이 내려간 상태에서 한번에 페이지의 최상단으로 이동하기 때문에 상당히 편리한 기능이다.

이번 셈틀꾼 홈페이지에도 상당히 유용하게 쓰일 것으로 보이며 이 버튼 구현을 내가 도맡았다.

# 주요코드

```vue
<template>
  <div>
    <v-btn color="primary" class="top_btn" @click="go_top" fab x-large dark>
      <v-icon>mdi-arrow-up-thick</v-icon>
    </v-btn>
  </div>
</template>

<script>
export default {
  methods: {
    go_top() {
      window.scrollTo({
        top: 0,
        left: 0,
        behavior: "smooth",
      });
    },
  },
};
</script>

<style scoped>
.top_btn {
  right: 1%;
  bottom: 2%;
  position: fixed;
}
</style>
```

자바스크립트의 기본적인 요소를 활용하여 간단하게 구현이 가능하다.

# Result

![Top Button](/../assets/img/TopBtn.gif)