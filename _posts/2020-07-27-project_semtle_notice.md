---
layout: single
title: "셈틀꾼(동아리) 홈페이지 제작 프로젝트 - 공지사항"
categories: 
   - Project
author_profile: true
read_time: true
comments: true
---

내가 맡은바 임무중 하나인 공지사항 컴포넌트이다.

공지사항 컴포넌트는 오직 읽을수만 생성 후 읽기만 가능한 'CR'만 만족하면 되기 때문에 비교적 간단한 컴포넌트이다.

또한, 아직 'vuex' 를 사용하라는 팀장의 말이 없었기에 컴포넌트끼리 데이터를 주고 받는다.

레이아웃은 그리드 레이아웃을 채택하였다.

# 주요 코드

공지사항 페이지의 큰 틀을 담고있는 컴포넌트이다.

```vue
<template>
  <v-container>
    <Header :noticeHeader="noticeHeader" />

    <Body :noticeBody="noticeBody" />
  </v-container>
</template>

<script>
import Header from "../components/Header.vue";
import Body from "../components/Body.vue";

export default {
  components: {
    Header,
    Body,
  },
  data() {
    return {
      noticeHeader: {
        title: "제목",
        writerName: "홍길동",
        date: "2020년 7월 14일",
        views: "999",
        writerImage:
          "https://previews.123rf.com/images/pedrolieb/pedrolieb1205/pedrolieb120500010/13528955-%EC%9D%B5%EB%AA%85-%EA%B7%B8%EB%A6%BC.jpg",
      },
      noticeBody: {
        mdText: `Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.`,
        imagePath: "https://upload.wikimedia.org/wikipedia/ko/2/24/Lenna.png",
      },
    };
  },
};
</script>
```

아직 백엔드팀과의 소통이 되지 않아 우선 겉 부분만 작성하였기 때문에 모든 데이터는 임의의 문자열로 대체하였다.

공지사항 컴포넌트는 두 개의 자식 컴포넌트로 나뉜다.

하나는 제목, 작성자, 작성일, 조회수 정보를 나타내는 'Header.vue' 컴포넌트와

다른 하나는 마크다운표기될 문자열, 사진의 정보를 나타내는 'Body.vue' 컴포넌트이다.

코드를 보면 해당 컴포넌트들을 추가한 것을 볼 수 있다.

이 후 자식 컴포넌트들에게 'noticeHeader' 라는 객체와 'noticeBody' 라는 객체로 컴포넌트들을 이룰 정보를 'props' 로 넘겨준다.

## Header.vue

제목, 작성자, 작성일, 조회수 데이터를 포함하는 컴포넌트이다.

```vue
<template>
  <v-row>
    <v-col cols="12">
      <h1>{% raw %}{{noticeHeader.title}}{% endraw %}</h1>
      <hr />
      <table>
        <tr>
          <td rowspan="2">
            <img :src="noticeHeader.writerImage" width="50px" />
          </td>
          <td style="vertical-align:top;">작성자:{% raw %}{{noticeHeader.writerName}}{% endraw %}</td>
        </tr>
        <tr style="vertical-align:bottom;">
          <td>작성일:{% raw %}{{noticeHeader.date}}{% endraw %}</td>
          <td>|조회수:{% raw %}{{noticeHeader.views}}{% endraw %}</td>
        </tr>
      </table>
    </v-col>
  </v-row>
</template>

<script>
export default {
  props: {
    noticeHeader: {
      type: Object,
      required: true,
    },
  },
};
</script>

<style scoped>
hr {
  background-color: #fff;
  border-top: 2px dashed #8c8b8b;
}
</style>
```

상당히 간단하다.

아직 데이터베이스가 어떻게 이루어질지 몰라 겉만 구현했기 때문이다.

부모 컴포넌트에게 받은 정보를 'props' 로 받아 템플릿에 적용한 것을 볼 수 있다.

## Body.vue

마크다운문법으로 작성된 글, 사진 데이터를 담고있는 컴포넌트이다.

```vue
<template>
  <v-row>
    <v-col cols="12">
      <VueMarkDown>{% raw %}{{noticeBody.mdText}}{% endraw %}</VueMarkDown>
      <img :src="noticeBody.imagePath" alt="Lena" width="300px" />
    </v-col>
  </v-row>
</template>

<script>
import VueMarkDown from "vue-markdown";
export default {
  props: {
    noticeBody: {
      type: Object,
      required: true,
    },
  },
  components: {
    VueMarkDown,
  },
};
</script>
```

마찬가지로 간단하다.

'Header.vue' 컴포넌트와 대부분 비슷하지만 글이 작성되는 부분을 마크다운 표현식으로 맞춰 표현하기 위해 마크다운을 표현할 수 있도록 터미널에서 다운로드 받아 이용하였다.

## Vue.js_markdown 

터미널에서 해당 프로젝트가 있는 디렉토리로 이동한 후 다음과 같이 입력한다.

```terminal
npm i vue-markdown
```

입력한 후 엔터를 입력하면 다운로드가 진행된다.

터미널 내부에서 다운로드가 완료되면 마크다운을 사용할 컴포넌트에 마크다운 컴포넌트를 'import' 한다.

```vue
import VueMarkDown from "vue-markdown";
```

이 후 다른 컴포넌트를 사용하듯 사용하면 된다.

```vue
<VueMarkDown>{% raw %}{{noticeBody.mdText}}{% endraw %}</VueMarkDown>
```

## Result

![semtle notice_1_test](/../assets/img/semtle_notice_1.PNG)
