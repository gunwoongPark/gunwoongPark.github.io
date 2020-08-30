---
layout: single
title: "셈틀꾼(동아리) 홈페이지 제작 프로젝트 - 메뉴바"
categories: 
   - Project
author_profile: true
read_time: true
comments: true
---

사람마다 능률은 다르다.

같이 협업을 하더라도 어떤 사람은 하루면 끝낼 일도 어떤 사람은 1달이 걸려도 정상적인 결과물을 도출하지 못한다.

의외로 내가 이번 프로젝트에서 하루만에 끝내는 사람이 되었다.

같이 작업하는 어린 친구가 상단의 메인 메뉴바를 맡았다.

상당히 복잡한 구조로 로고에 마우스 포인터가 올라가면 오른쪽으로 펼쳐지며 메뉴의 요소들을 나열하는 식의 메뉴바이다.

그 친구가 낸 아이디어지만 상당히 복잡하다.

그래서 그런지 한달이 지나도 적당한 결과물을 내지못해 내가 맡게 되었다.

원래 팀이란 

# 주요 코드

전체적인 구조는 부모 컴포넌트인 MenuBar 자체와 자식 컴포넌트인 MenuElement 가 부모컴포넌트에게 props로 필요한 데이터를 받는다.

우선 부모 컴포넌트 MainMenuBar 의 template 태그 부터 살펴보겠다.

## MainMenuBar template tag

```vue
<template>
    <div id="container">
      <router-link to="/">
        <img src="../assets/mainLogo.png" 
          width="150px" id="logo"
        @mouseover="menuOpen" 
        @mouseleave="menuClose"/>
      </router-link>
      
      <div id="transBox"
      @mouseover="menuOpen"
      @mouseleave="menuClose">
        <div id="box">
        
          <div class="dummy1"></div>

          <MenuElement
            :functions = "functions"
            v-for="(item, index) in Attributes"
            :key="index"
            :Attribute="item"
            ref="args"
          />          

          <div class="dummy2"></div>
        </div>
      </div>
    </div>
</template>
```

***

로고 뒤에 보이지 않도록 div 를 첨부했고 마우스 포인터가 올라가면 해당 div 가 transition 으로 길어지는 시간을 주었다.

메뉴 요소에는 dummy 요소가 두 개 존재하는데 이것은 중간을 맞추기 위해 끼워넣은 것이다.

dummy 데이터 사이에 MenuElement 컴포넌트를 삽입하여 'v-for' 를 활용해 불러올 메뉴 요소들을 반복문을 통해 차례로 자식 컴포넌트로 넘겨준다.

다음으로는 logic 들이 존재하는 script 태그를 살펴보겠다.

## MainMenuBar script tag

```vue
<script>
import MenuElement from "./MenuElement.vue";
import {mapMutations, mapGetters} from 'vuex';
export default {
  computed:{
    ...mapGetters([
      'getLogin',
      'getAdmin'
    ]),
  },
  components: {
    MenuElement,
  },
  data() {
    return {
      // 현재 스크롤 위치와 호버에 대한 제어를 두기 위한 변수
      controller: false,
      subMenu:false,
      Attributes: [
        {
          Items: [],
          url: '/notice/list',
          Title: "공지사항",
          method: ()=>{}
        },
        {
          Items: [
            { title: "목록", url:'/project/list' },
            { title: "공고", url: '/project/announce/list' },
          ],
          url: '',
          Title: "프로젝트",
          method: ()=>{}
        },
        {
          Items: [],
          url: '/management',
          Title: "역대 간부",
          method: ()=>{}
        },
        {
          Items: [],
          url: '/qna/list',
          Title: "Q&A",
          method: ()=>{}
        },
      ],
      functions:{
        menuOpen : this.menuOpen,
        menuClose : this.menuClose,
        subMenuOpen : this.subMenuOpen,
        subMenuClose : this.subMenuClose
      },
      // 로그아웃 시 들어갈 리스트 아이템과 버튼 제목
      logOutMenu:[
        {
          Items: [],
          url: '/login',
          Title: "로그인",
          method: ()=>{}
        },
      ],
      // 로그인 시 들어갈 리스트 아이템과 버튼 제목
    logInMenu:[
        {
          Items: [],
          url: '/mypage',
          Title: "마이페이지",
          method: ()=>{}
        },
        {
          Items: [],
          url: '',
          Title: "로그아웃",
          method: ()=>{this.setLogout()}
        },
      ],

      adminMenu:{
          Items: null,
              url: "/admin/menu",
              Title: "관리자페이지",
              method: () => {},
      }
    };
  },
  methods: {
    ...mapMutations([
      'setLogout'
    ]),
    // 로그인 됐는 지 확인하는 함수
    isLogin(){
      if (this.getLogin){ // 로그인일 때
        // console.log("login success")
        if(this.getAdmin){
          this.logInMenu[0] = this.adminMenu;
        }
        this.Attributes = this.Attributes.concat(this.logInMenu) // 로그인 메뉴 받음
      }
      else { // 로그아웃일 때
        // console.log("login fail")
        this.Attributes = this.Attributes.concat(this.logOutMenu) // 로그아웃 메뉴 받음
      }
    },
    subMenuOpen(){
      this.subMenu = true;
    },
    subMenuClose(){
      this.subMenu = false;
    },
    // 메뉴가 펼쳐지는 함수
    menuOpen() {
      if (this.controller) {
        let boxObj = document.querySelector("#transBox");
        boxObj.style.width = "1000px";
        boxObj.style.transition = ".5s";
      }
      else if(this.subMenu){
        let boxObj = document.querySelector("#transBox");
        boxObj.style.width = "1000px";
      }
      // console.log(this.subMenu)
    },

    // 메뉴가 닫히는 함수
    menuClose() {
      if (this.controller) {
        let boxObj = document.querySelector("#transBox");
        boxObj.style.width = "0px";
        boxObj.style.transition = ".5s";
      }
    },

    // 스크롤 시 수행되는 함수
    handleScroll() {
      if ((window.scrollY || document.documentElement.scrollTop) < 10) {
        this.menuOpen();
        this.controller = false;
      } else {
        this.controller = true;
        this.menuClose();
      }
    },
  },

  // 스크롤 시 수행되는 함수를 인스턴스 생성 시 호출
  created() {
    window.addEventListener("scroll", this.handleScroll);
    this.isLogin();
  },

  // 사용한 함수는 인스턴스 소멸 시 제거
  destroyed() {
    window.removeEventListener("scroll", this.handleScroll);
  },
};
</script>
```

***

대부분이 마우스가 로고부분에 올라갔을 때 이벤트가 작동하도록 하는 메소드들이 대부분을 이룬다.

또한 스크롤 시 펼쳐친 메뉴 부분이 다시 로고 부분으로 들어가도록 transition 을 넣어주었다.

다음으로는 style 태그를 살펴보겠다.

```css
<style scoped>
             @font-face {
    font-family: 'HangeulNuri-Bold';
    src: url('https://cdn.jsdelivr.net/gh/projectnoonnu/noonfonts_three@1.0/HangeulNuri-Bold.woff') format('woff');
    font-weight: normal;
    font-style: normal;
}
            
            
*{
  font-family: 'HangeulNuri-Bold';
}
#transBox{
  transform: translateY(-105%);
  height: 150px;
  width: 1000px;
  border-radius: 100px 0 0 100px;
}

#container {
  height: 150px;
}

#logo {
  position: relative;
  z-index: 100;
  border-radius: 100%;
}

#box {
  transform: translateY(137%);
  background-color: #50829b;
  
  height: 40.5px;
  transition: 0.4s;
  margin-left: 4px;
  border-radius: 20px;
  
  display: flex;
  justify-content: space-between;
  align-items: center;

  overflow: hidden;
}

.dummy1 {
  width: 150px;
}

.dummy2 {
  width: 0px;
}

</style>
```

***

부분 부분 하드코딩한 흔적이 보이지만 어쩔수없었다.

로고의 크기는 고정적이기 때문에 하드코딩으로 div 요소의 높이를 지정해줘야만 했다.

또한, 사용자가 메뉴바에서 마우스 포인터를 실수로 뗄 가능성을 염두하여 container 라는 id를 가진 div 태그를 메뉴바 뒤에 두었고, 해당 div 요소에 마우스를 올려도 메뉴바가 그대로 펼쳐져 있도록 하였다.

마지막으로 자식 컴포넌트인 MenuElement 의 코드를 살펴보겠다.

## MenuElement code

```vue
<template>
  <v-menu offset-y open-on-hover>
    <template v-slot:activator="{ on, attrs }">
      <router-link :to="Attribute.url">
        <v-btn
          id="menuBtn"
          class="child white--text"
          text
          large
          v-bind="attrs"
          v-on="on"
          @click="Attribute.method"
        >
        {{Attribute.Title}}
        </v-btn>
      </router-link>
    </template>
    <v-list flat class="pa-0 ma-0 text-center">
      <v-list-item-group>
        <router-link
          v-for="(item, index) in Attribute.Items" 
          :key="index" :to="item.url">
          <v-list-item @mouseover="()=>{
            functions.subMenuOpen();
            functions.menuOpen();
          }" 
          @mouseleave="()=>{
            functions.subMenuClose();
            functions.menuClose();  
          }">
            <v-list-item-title>
              {{ item.title }}
            </v-list-item-title>
          </v-list-item>
        </router-link>
      </v-list-item-group>
    </v-list>
  </v-menu>
</template>

<script>
export default {
  props: {
    functions: Object,
    Attribute: {
      type: Object,
      required: true,
    },
  },
  methods: {
    
  },
};
</script>
```

***

props 로 부모 컴포넌트로부터 받은 데이터들을 이용하여 메뉴바의 메뉴 요소들을 구성하였다.

해당 요소들에 마우스를 올리면 아래로 리스트들이 뜨도록 구성하였다.

# Result

![Main Menu Bar](/../assets/img/MainMenuBar.gif)