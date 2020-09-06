---
layout: single
title: "셈틀꾼(동아리) 홈페이지 제작 프로젝트 - 역대 간부 관리 페이지(admin side)"
categories: 
   - Project
author_profile: true
read_time: true
comments: true
---

생각보다 일이 빠르고 순조롭게 진행되어 내가 도맡은 임무를 모두 성공적으로 수행했다.

그래서 추가적으로 임무를 하달 받은 것 중 하나인 역대 간부 관리 페이지를 관리자로 로그인 했을 경우 'CRUD'가 모두 가능한 페이지를 제작하는 것이다.

> CRUD란 CRUD는 대부분의 컴퓨터 소프트웨어가 가지는 기본적인 데이터 처리 기능인 Create(생성), Read(읽기), Update(갱신), Delete(삭제)를 묶어서 일컫는 말이다. 사용자 인터페이스가 갖추어야 할 기능(정보의 참조/검색/갱신)을 가리키는 용어로서도 사용된다.

셈틀꾼은 컴퓨터공학과 학술 동아리로 회장, 부회장 이외에도 여러 과목의 학습을 도와주는 멘토들이 존재한다.

필자 또한 지금껏 동아리에 가입 후 쭉 멘토분들의 수업을 들어왔고 최근에는 C++ 멘토를 맡은적이 있다.

# 주요 코드

우선 전체적인 틀을 구성하는 'html' 코드가 있는 'template' 태그 내부를 살펴보겠다.

## template tag

```vue
<template>
  <v-container>
    <v-row>
      <v-col>
        <v-data-table
          :headers="headers"
          :items="mentors"
          :search="search"
          sort-by="position"
          class="elevation-1"
        >
          <template v-slot:top>
            <v-toolbar flat color="white">
              <v-toolbar-title>역대 간부 관리</v-toolbar-title>
              <v-divider class="mx-4" inset vertical></v-divider>
              <v-spacer></v-spacer>
              <v-text-field
                v-model="search"
                append-icon="mdi-magnify"
                label="Search"
                single-line
                hide-details
                clearable
              ></v-text-field>

              <v-spacer></v-spacer>

              <v-dialog v-model="dialog" width="3000px">
                <template v-slot:activator="{ on, attrs }">
                  <v-btn color="rgb(80,130,155)" dark class="mb-2" v-bind="attrs" v-on="on">새로 등록</v-btn>
                </template>
                <v-card>
                  <v-card-title>
                    <span class="headline">{{formTitle}}</span>
                  </v-card-title>

                  <v-card-text>
                    <v-container>
                      <v-row>
                        <v-col cols="12" sm="6" md="4">
                          <v-text-field
                            color="rgb(80,130,155)"
                            v-model="editedItem.studentCode"
                            label="학번"
                            @keypress="checkNumber"
                            @keyup="checkHan"
                            counter
                            maxlength="8"
                          ></v-text-field>
                        </v-col>
                        <v-col cols="12" sm="6" md="4">
                          <v-select
                            color="rgb(80,130,155)"
                            v-model="editedItem.position"
                            :items="positionItems"
                            label="직책"
                          ></v-select>
                        </v-col>
                        <v-col cols="12" sm="6" md="4">
                          <v-select
                            color="rgb(80,130,155)"
                            v-if="editedItem.position ==='멘토' "
                            v-model="editedItem.language"
                            :items="languageItems"
                            label="과목"
                          ></v-select>
                        </v-col>
                        <v-col cols="12" sm="6" md="6">
                          <v-text-field
                            color="rgb(80,130,155)"
                            v-model="editedItem.activeYear"
                            label="활동년도"
                            counter
                            maxlength="4"
                            @keypress="checkNumber"
                            @keyup="checkHan"
                          ></v-text-field>
                        </v-col>
                        <v-col cols="12" sm="6" md="6">
                          <v-select
                            color="rgb(80,130,155)"
                            v-model="editedItem.season"
                            :items="seasonItems"
                            label="활동학기"
                          ></v-select>
                        </v-col>
                        <v-col cols="12" sm="6" md="12">
                          <v-text-field
                            color="rgb(80,130,155)"
                            filled
                            v-model="editedItem.contents"
                            label="내용"
                          ></v-text-field>
                        </v-col>
                      </v-row>
                    </v-container>
                  </v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="rgb(80,130,155)" text @click="close">Cancel</v-btn>
                    <v-btn color="rgb(80,130,155)" text @click="save">Save</v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
            </v-toolbar>
          </template>
          <template v-slot:item.actions="{ item }">
            <v-icon small class="mr-2" @click="editItem(item)">mdi-pencil</v-icon>
            <v-icon small @click="deleteItem(item)">mdi-delete</v-icon>
          </template>
          <template v-slot:no-data>
            <v-btn color="rgb(80,130,155)" @click="initialize">Reset</v-btn>
          </template>
        </v-data-table>
      </v-col>
    </v-row>
  </v-container>
</template>
```

***

뷰티파이를 활용하여 작성하니 처음에는 헷갈리는 부분도 많고 공식 document 도 전부 영어인 마당에 사람들이 작성한 코드의 양도 참고하기엔 부족해서 힘든 점이 많았다.

특히 이번코드에 뷰티파이에서 제공하는 'v-data-table' 태그를 사용했는데, document 에도 너무 복잡하게 적혀져 있어 사용하는 데 애를 먹었다.

하지만 여러번 시행착오를 겪어 잘 활용할 수 있도록 되었다.

더 유용하게 그리고 잘 사용하기 위해 많은 사이트를 클론코딩을 해봐야겠다.

다음으로는 logic 들이 존재하는 script 태그를 살펴보겠다.

## script tag

```vue
<script>
export default {
  data: () => ({
    search: "",
    dialog: false,
    headers: [
      { text: "이름", value: "name", align: "start" },
      { text: "학번", value: "studentCode" },
      { text: "직책", value: "position" },
      { text: "과목", value: "language" },
      { text: "활동년도", value: "activeYear" },
      { text: "활동학기", value: "season" },
      { text: "Actions", value: "actions", sortable: false },
    ],
    mentors: [],

    editedIndex: -1,

    editedItem: {
      studentCode: "",
      language: "",
      position: "",
      activeYear: "",
      season: "",
      contents: "",
      _id: "",
    },

    defaultItem: {
      studentCode: "",
      language: "",
      position: "",
      activeYear: "",
      season: "",
      contents: "",
    },

    positionItems: ["회장", "부회장", "멘토"],
    languageItems: [
      "C",
      "C++",
      "Python",
      "Java",
      "Linux",
      "Computer Architecture",
      "Data Structure",
      "Web Programming",
    ],
    seasonItems: ["1학기 (여름학기)", "2학기 (겨울학기)"],

    errorMsg: [],
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "새로 등록" : "수정";
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
  },

  created() {
    this.initialize();
  },

  methods: {
    initialize() {
      // axios 로 데이터 받아와서 this.mentors 데이터 배열에 삽입 (+)

      this.axios.get("http://49.50.166.64/api/management/list").then((res) => {
        for (let idx = 0; idx < res.data.management.length; ++idx) {
          var tempObj = {
            name: "",
            studentCode: "",
            position: "",
            language: "",
            activeYear: "",
            season: "",
            contents: "",
            _id: "",
          };
          tempObj.name = res.data.management[idx].name;
          tempObj.studentCode = res.data.management[idx].studentCode;
          tempObj.position = res.data.management[idx].position;
          tempObj.language = res.data.management[idx].language;
          tempObj.activeYear = res.data.management[idx].activeYear;
          tempObj.season = res.data.management[idx].season;
          tempObj.contents = res.data.management[idx].contents;
          tempObj._id = res.data.management[idx]._id;

          this.mentors.push(tempObj);
        }
      });
    },

    editItem(item) {
      this.editedIndex = this.mentors.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      if (confirm("정말 지우시겠습니까?")) {
        // 여기 DELETE 비동기 함수 작성

        this.axios
          .delete("http://49.50.166.64/api/management/delete", {
            headers: { token: sessionStorage.getItem("token") },
            data: { _id: item._id },
          })
          .then((res) => {
            if (res.status === 200) {
              alert("성공적으로 삭제!");
              this.$router.go();
            }
          })
          .catch((err) => {
            if (err.response.status === 400) {
              alert("유효하지 않은 학생입니다.");
            } else if (err.response.status === 401) {
              alert("권한이 없습니다.");
            } else if (err.response.status === 500) {
              alert("서버에 문제가 있습니다.");
            }
          });
      }
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    save() {
      // 기존 간부 수정
      if (this.editedIndex > -1) {
        // 여기 PUT 비동기 함수 작성
        this.modifyMentor();
      }
      // 새로 간부 추가
      else {
        this.addMentor();
      }
    },

    modifyMentor() {
      let sendObj = {
        activeYear: this.editedItem.activeYear,
        season: this.editedItem.season,
        position: this.editedItem.position,
        language: this.editedItem.language,
        studentCode: this.editedItem.studentCode,
        contents: this.editedItem.contents,
        _id: this.editedItem._id,
      };

      console.log(sendObj);

      let config = {
        headers: { token: sessionStorage.getItem("token") },
      };

      this.axios
        .put("http://49.50.166.64/api/management/update", sendObj, config)
        .then((res) => {
          if (res.status === 200) {
            alert("수정 성공!");
            this.$router.go();
          }
        })
        .catch((err) => {
          if (err.response.status === 400) {
            alert("유효하지 않은 학생입니다.");
          } else if (err.response.status === 401) {
            alert("권한이 없습니다.");
          } else if (err.response.status === 500) {
            alert("서버에 문제가 있습니다.");
          }
        });

      this.close();
    },

    // 여기 POST 비동기 함수 작성
    addMentor() {
      this.errorMsg = [];

      if (this.editedItem.studentCode.length != 8)
        this.errorMsg.push("학번을 정확하게 입력해주세요.");

      if (this.editedItem.position === "")
        this.errorMsg.push("직책을 선택해주세요.");

      if (this.editedItem.activeYear.length != 4)
        this.errorMsg.push("활동 년도를 정확하게 입력해주세요.");

      if (this.editedItem.season === "")
        this.errorMsg.push("활동한 학기를 선택해주세요.");

      if (this.editedItem.contents === "")
        this.errorMsg.push("간단하게라도 내용을 적어주세요.");

      if (
        this.editedItem.position === this.positionItems[2] &&
        this.editedItem.language === ""
      )
        this.errorMsg.push("멘토의 과목을 선택해 주세요.");

      // 예외처리에 걸린 경우 -> 알람 띄우고 다시 폼 입력
      if (this.errorMsg.length !== 0) {
        let errString = "";
        for (let idx = 0; idx < this.errorMsg.length; ++idx) {
          errString = errString.concat(this.errorMsg[idx]);

          if (idx === this.errorMsg.length - 1) {
            break;
          }
          errString = errString.concat("\n");
        }

        alert(errString);
      }
      // 예외처리에 걸리지 않을 경우 -> 데이터 전송 후 닫기
      else {
        //멘토 아니면 선택된 과목값 삭제
        if (this.editedItem.position !== "멘토") {
          this.editedItem.language = "";
        }

        let sendObj = {
          activeYear: this.editedItem.activeYear,
          season: this.editedItem.season,
          position: this.editedItem.position,
          language: this.editedItem.language,
          studentCode: this.editedItem.studentCode,
          contents: this.editedItem.contents,
        };

        console.log(sendObj);


        // 관리자임을 알 수 있도록 헤더 추가
        let config = {
          headers: { token: sessionStorage.getItem("token") },
        };

        this.axios
          .post("http://49.50.166.64/api/management/input", sendObj, config)
          .then((res) => {
            if (res.status === 200) {
              alert("등록 성공!");
              this.$router.go();
            }
          })
          .catch((err) => {
            if (err.response.status === 400) {
              alert("유효하지 않은 학생입니다.");
            } else if (err.response.status === 401) {
              alert("권한이 없습니다.");
            } else if (err.response.status === 500) {
              alert("서버에 문제가 있습니다.");
            }
          });

        this.close();
      }
    },

    checkNumber(e) {
      if (e.keyCode < 48 || e.keyCode > 57) {
        e.returnValue = false;
      }
    },

    checkHan(e) {
      e = e || window.e;
      var keyID = e.which ? e.which : e.keyCode;
      if (keyID == 8 || keyID == 46 || keyID == 37 || keyID == 39) return;
      else e.target.value = e.target.value.replace(/[^0-9]/g, "");
    },
  },
};
</script>
```

***

script 부분에서는 딱히 logic 이라고 할 부분은 폼에 대한 예외처리를 특정 배열 변수에 저장하여 출력하는 것과

axios 모듈을 활용하여 서버와 통신하는 부분이다.

사용자 입력 폼에 대한 예외처리는 숫자만 폼 입력 부분에 입력할 수 밖에 없도록 하는 예외처리를 함수(checkNumber)를 사용하여 예외처리했다.

하지만 한글을 입력하면 입력이 유지되기 때문에 따로 한글만 함수(checkHan)를 사용하여 2중으로 예외처리했다.

폼에 예외가 있을 경우에 발생하는 에러 메시지를 한 배열변수(errorMsg)에 push 하여 해당 변수가 비어있지 않을 경우 외에는 에러 메시지를 띄어주도록 했다. 

>axios 란 뷰에서 권고하는 HTTP 통신 라이브러리는 액시오스(Axios)입니다. Promise 기반의 HTTP 통신 라이브러리이며 상대적으로 다른 HTTP 통신 라이브러리들에 비해 문서화가 잘되어 있고 API가 다양합니다. 나중에 더 자세히 다루겠습니다.