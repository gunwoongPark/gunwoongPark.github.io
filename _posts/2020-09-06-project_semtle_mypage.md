---
layout: single
title: "셈틀꾼(동아리) 홈페이지 제작 프로젝트 - 마이페이지"
categories: 
   - Project
author_profile: true
read_time: true
comments: true
---

할당받은 일들이 생각보다 더 일찍 끝났다.

그래서 아직 맡은 작업을 끝내지 못한 다른 팀원들의 코드를 대신 짜주게 되었고, 그게 마이페이지이다.

항상 드는생각이지만 아직 남의 코드 그것도 숙련된 사람이 아닌 사람의 코드를 보는 것은 헷갈린다..

하지만 만약 내가 앞으로 회사에 들어간다면 다른 팀원들과의 협업은 필수라서 어느정도 익숙해져야 한다고 생각한다.

***

구조는 큰 틀인 views 파일 하나에 4개의 컴포넌트(유저 이미지, 닉네임, 전화번호, 비밀번호)를 부착한다.

항상 props 는 적은게 가장 좋지만 굳이 이렇게 컴포넌트를 나눈 이유는 뷰티파이의 v-dialog 를 사용하는데 복잡할 것 같아 일부러 나누었다.

하단에는 사용자가 진행했던 프로젝트들을 리스트로 띄워주는데 이 부분은 컴포넌트로 빼지 않았다.

# 주요 코드

우선 전체적인 틀을 구성하는 views 파일의 HTML 코드 부분이 있는 template 태그를 보겠다.

## views template tag

```vue
<template>
<v-container class="px-0">
    <v-row cols="12">
        <h1 class="mt-3 ml-2">😶프로필 수정</h1>
    </v-row>
    <v-row>
        <v-col cols="12" md="6">
            <UserImg />
            <br />
            <MyPageNickName />
        </v-col>
        <v-col cols="12" md="6">
            <PhoneNum />
            <br />
            <Pw />
        </v-col>
    </v-row>
    <v-row>
        <v-col cols="12">
            <h1 class="mt-3">💻나의 프로젝트</h1>
        </v-col>
    </v-row>
    <v-row>
        <v-col cols="12">
            <v-data-table :headers="headers" :items="projects" :items-per-page="5" @click:row="clickRow">
                <template v-slot:header.name="{ header }">{{ header.text.toUpperCase() }}</template>
            </v-data-table>
        </v-col>
    </v-row>
</v-container>
</template>
</script>
```

***

다음은 로직이 존재하는 script 부분의 태그이다.

## views script tag

```vue
<script>
import ipObj from "../key";

import Pw from "../components/Password.vue";
import UserImg from "../components/UserImg.vue";
import PhoneNum from "../components/PhoneNum.vue";
import MyPageNickName from "../components/MyPageNickName.vue";

export default {
    data: () => ({
        dialog: false,
        headers: [{
                text: "프로젝트 이름",
                align: "start",
                value: "projectName",
            },
            {
                text: "팀명",
                value: "teamName",
            },
            {
                text: "프로젝트 기간",
                value: "term",
            },
            {
                text: "참여인원",
                value: "memberNum",
            },
        ],
        projects: [],
    }),
    components: {
        Pw,
        PhoneNum,
        UserImg,
        MyPageNickName,
    },
    created() {
        this.initPortfolio();
    },
    methods: {
        clickRow(row) {
            this.$router.push({
                name: "project",
                params: {
                    id: row.projectLink,
                },
            });
        },
        initPortfolio() {
            let config = {
                headers: {
                    token: sessionStorage.getItem("token"),
                },
            };

            this.axios
                .get(`${ipObj.ip}/api/mypage`, config)
                .then((res) => {
                    console.log(res);
                    for (let idx = 0; idx < res.data.student.pfList.length; ++idx) {
                        var tempObj = {
                            projectName: "",
                            teamName: "",
                            term: "",
                            memberNum: "",
                            projectLink: "",
                        };
                        tempObj.projectName = res.data.student.pfList[idx].projectTitle;
                        tempObj.teamName = res.data.student.pfList[idx].projectTeamName;
                        tempObj.term =
                            res.data.student.pfList[idx].projectStartDate +
                            "~" +
                            res.data.student.pfList[idx].projectEndDate;
                        tempObj.memberNum = res.data.student.pfList[idx].students.length;
                        tempObj.projectLink = res.data.student.pfList[idx]._id;

                        this.projects.unshift(tempObj);
                    }
                })
                .catch((err) => {
                    console.log(err);
                });
        },
    },
};
</script>
```

***

다음은 각 컴포넌트들로 template 태그와 script 태그를 구분하지 않고 한개로 올리겠다.

## 유저 이미지

```vue
<template>
<div>
    <v-dialog v-model="dialog" width="500">
        <template v-slot:activator="{on, attrs}">
            <!-- 회원정보 변경 화면에서 보여질 카드 -->
            <v-card height="200" light="light">
                <div class="text-center">
                    <img height="145" :src="imageUrl" />
                </div>

                <v-card-actions class="justify-center">
                    <v-btn color="rgb(80, 130, 155)" v-bind="attrs" v-on="on" dark class="btn" @click="openDialog">이미지 변경</v-btn>
                </v-card-actions>
            </v-card>
        </template>

        <v-card class="white lighten-1">
            <v-card-title class="headline lighten-2">이미지 변경</v-card-title>

            <v-divider></v-divider>
            <!-- 이미지 변경 다이얼로그 창 내용구성 -->
            <v-container>
                <v-row>
                    <v-col>
                        <v-file-input label="이미지 첨부" accept="image/*" prepend-icon="mdi-camera" v-model="selectImg"></v-file-input>
                    </v-col>
                </v-row>
            </v-container>

            <v-divider></v-divider>

            <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn class="btn btn-dark" text="text" @click="submit">확인</v-btn>
            </v-card-actions>
        </v-card>
    </v-dialog>
</div>
</template>

<script>
import ipObj from "../key";

export default {
    data: () => ({
        dialog: false,
        img: "../assets/sample.png",
        data: null,
        imageUrl: null,
        selectImg: null,
    }),
    created() {
        this.initImage();
    },
    methods: {
        initImage() {
            let config = {
                headers: {
                    token: sessionStorage.getItem("token"),
                },
            };

            this.axios
                .get(`${ipObj.ip}/api/mypage`, config)
                .then((res) => {
                    this.imageUrl = `${ipObj.ip}/api/student/images/`;
                    this.imageUrl = this.imageUrl.concat(res.data.student.image);
                })
                .catch((err) => {
                    console.log(err);
                });
        },
        openDialog() {
            this.selectImg = null;
        },
        submit() {
            let config = {
                headers: {
                    token: sessionStorage.getItem("token"),
                },
            };

            let form = new FormData();
            form.append("image", this.selectImg);

            this.axios
                .put(`${ipObj.ip}/api/mypage/picture/update`, form, config)
                .then((res) => {
                    console.log(res);
                    this.dialog = false;
                    this.$router.go();
                })
                .catch((err) => {
                    console.log(err);
                });
        },
    },
};
</script>
```

***

## 전화번호

```vue
<template>
<div>
    <v-dialog v-model="dialog" width="500">
        <template v-slot:activator="{on, attrs}">
            <v-card height="200" light="light" class="pa-5">
                <v-card-title primary="primary" class="com-title justify-center">전화번호</v-card-title>
                <v-card-text class="show text-center">{{outputPhonenum}}</v-card-text>
                <v-card-actions class="justify-center">
                    <v-btn color="rgb(80, 130, 155)" v-bind="attrs" v-on="on" dark="dark" class="btn btn-dark m-3" @click="openDialog">변경</v-btn>
                </v-card-actions>
            </v-card>
        </template>

        <div class="white lighten-1">
            <v-card-title class="headline lighten-2">전화번호 변경</v-card-title>

            <v-divider></v-divider>

            <v-row>
                <v-col offset="1">
                    <v-text-field placeholder="010" v-model="phoneNum1" solo="solo" maxlength="3" @keypress="checkNumber" @keyup="checkHan"></v-text-field>
                </v-col>
                <v-col>
                    <v-text-field placeholder="1234" v-model="phoneNum2" solo="solo" maxlength="4" @keypress="checkNumber" @keyup="checkHan"></v-text-field>
                </v-col>
                <v-col>
                    <v-text-field placeholder="5678" v-model="phoneNum3" solo="solo" maxlength="4" @keypress="checkNumber" @keyup="checkHan"></v-text-field>
                </v-col>
                <v-col cols="1"></v-col>
            </v-row>

            <v-divider></v-divider>

            <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn class="btn btn-dark" text="text" @click="submit">확인</v-btn>
            </v-card-actions>
        </div>
    </v-dialog>
    <v-dialog v-model="dialog2" persistent="persistent" max-width="400">
        <v-card>
            <v-card-title class="headline error">
                <p style="color:white !important;">Error</p>
            </v-card-title>
            <ul class="mt-5">
                <li v-for="(error,i) in errMsg" :key="i">{{error}}</li>
            </ul>
            <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="error" text="text" @click="dialog2 = false">OK</v-btn>
            </v-card-actions>
        </v-card>
    </v-dialog>
</div>
</template>

<script>
import ipObj from "../key";

export default {
    data: () => ({
        dialog: false,
        dialog2: false,
        errMsg: [],

        resultPhoneNum: "",
        phoneNum1: "",
        phoneNum2: "",
        phoneNum3: "",
        outputPhonenum: "",
    }),
    created() {
        this.initNumber();
    },
    methods: {
        initNumber() {
            let config = {
                headers: {
                    token: sessionStorage.getItem("token"),
                },
            };

            this.axios.get(`${ipObj.ip}/api/mypage`, config).then((res) => {
                this.outputPhonenum = res.data.student.phoneNum;
            });
        },
        openDialog() {
            this.phoneNum1 = "";
            this.phoneNum2 = "";
            this.phoneNum3 = "";
        },
        submit() {
            this.errMsg = [];
            if (
                this.phoneNum1.length < 3 ||
                this.phoneNum2.length < 4 ||
                this.phoneNum3.length < 4
            ) {
                this.errMsg.push("휴대폰 번호를 정확하게 입력해주세요.");
            }

            if (this.errMsg.length !== 0) {
                this.dialog2 = true;
            } else {
                this.resultPhoneNum = this.phoneNum1 + this.phoneNum2 + this.phoneNum3;

                let sendObj = {
                    phoneNum: this.resultPhoneNum,
                };

                let config = {
                    headers: {
                        token: sessionStorage.getItem("token"),
                    },
                };

                this.axios
                    .put(`${ipObj.ip}/api/mypage/phoneNum/update`, sendObj, config)
                    .then((res) => {
                        if (res.status === 200) {
                            this.dialog = false;
                            this.$router.go();
                        }
                    });
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

<style scoped>
@font-face {
    font-family: "NEXON Lv1 Gothic OTF";
    src: url("https://cdn.jsdelivr.net/gh/projectnoonnu/noonfonts_20-04@2.1/NEXON Lv1 Gothic OTF.woff") format("woff");
    font-weight: normal;
    font-style: normal;
}

.com-title {
    font-family: "NEXON Lv1 Gothic OTF";
}

#inputPhonNum {
    width: 1px;
}

li {
    list-style: none;
}

.show {
    font-size: 150%;
}
</style>
```

***

## 닉네임

```vue
<template>
<div>
    <v-card height="200" light="light" class="pa-5">
        <v-card-title primary="primary" class="com-title justify-center">학번+이름</v-card-title>
        <v-card-text class="show mt-4 text-center">{{outputNick}}</v-card-text>
    </v-card>
</div>
</template>

<script>
import ipObj from "../key";
export default {
    data() {
        return {
            outputNick: "",
        };
    },
    created() {
        this.initNickName();
    },
    methods: {
        initNickName() {
            let config = {
                headers: {
                    token: sessionStorage.getItem("token"),
                },
            };

            this.axios.get(`${ipObj.ip}/api/mypage`, config).then((res) => {
                this.outputNick = res.data.student.nick;
                console.log(res);
            });
        },
    },
};
</script>

<style scoped>
@font-face {
    font-family: "NEXON Lv1 Gothic OTF";
    src: url("https://cdn.jsdelivr.net/gh/projectnoonnu/noonfonts_20-04@2.1/NEXON Lv1 Gothic OTF.woff") format("woff");
    font-weight: normal;
    font-style: normal;
}

.com-title {
    font-family: "NEXON Lv1 Gothic OTF";
}

.show {
    font-size: 150%;
}
</style>
```

***

## 비밀번호

```vue
<template>
<div>
    <v-dialog v-model="dialog" width="500">
        <template v-slot:activator="{on, attrs}">
            <v-card height="200" light="light" class="pa-5">
                <v-card-title primary="primary" class="com-title justify-center">비밀번호</v-card-title>

                <v-card-actions class="justify-center">
                    <v-btn color="rgb(80, 130, 155)" v-bind="attrs" v-on="on" dark="dark" class="btn btn-dark m-3 mt-1" @click="openDialog">변경</v-btn>
                </v-card-actions>
            </v-card>
        </template>

        <div class="white lighten-1">
            <v-card-title class="headline lighten-2">비밀번호 변경</v-card-title>

            <v-divider></v-divider>

            <v-col>
                <v-text-field type="password" v-model="before" placeholder="이전 비밀번호" solo="solo"></v-text-field>

                <v-text-field type="password" v-model="after" placeholder="새 비밀번호" solo="solo"></v-text-field>

                <v-text-field type="password" v-model="checkafter" placeholder="비밀번호 확인" solo="solo"></v-text-field>
            </v-col>

            <v-divider></v-divider>

            <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn class="btn btn-dark" text="text" @click="submit()">확인</v-btn>
            </v-card-actions>
        </div>
    </v-dialog>
    <v-dialog v-model="dialog2" persistent="persistent" max-width="400">
        <v-card>
            <v-card-title class="headline error">
                <p style="color:white !important;">Error</p>
            </v-card-title>
            <ul class="mt-5">
                <li v-for="(error,i) in errMsg" :key="i">{{error}}</li>
            </ul>
            <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="error" text="text" @click="dialog2 = false">OK</v-btn>
            </v-card-actions>
        </v-card>
    </v-dialog>
</div>
</template>

<script>
import ipObj from "../key";

export default {
    data: () => ({
        dialog: false,
        dialog2: false,

        before: "",
        after: "",
        checkafter: "",

        errMsg: [],
    }),

    methods: {
        openDialog() {
            (this.before = ""), (this.after = ""), (this.checkafter = "");
        },
        submit() {
            this.errMsg = [];
            // 데이터베이스에서 불러온 비밀번호와 이전 비밀번호가 다를 경우 에러 추가
            if (this.after != this.checkafter) {
                this.errMsg.push("비밀번호가 다릅니다.");
                this.after = "";
                this.before = "";
                this.checkafter = "";
            }

            if (this.errMsg.length !== 0) {
                this.dialog2 = true;
            } else {
                let sendObj = {
                    currentPW: this.before,
                    changePW: this.after,
                };

                let config = {
                    headers: {
                        token: sessionStorage.getItem("token"),
                    },
                };

                this.axios
                    .put(`${ipObj.ip}/api/mypage/pw/update`, sendObj, config)
                    .then((res) => {
                        if (res.status === 200) {
                            this.dialog = false;
                        }
                    })
                    .catch((err) => {
                        if (err.response.status === 400) {
                            this.errMsg.push("비밀번호가 틀립니다!");
                            this.dialog2 = true;
                        }
                    });
            }
        },
    },
};
</script>

<style scoped>
@font-face {
    font-family: "NEXON Lv1 Gothic OTF";
    src: url("https://cdn.jsdelivr.net/gh/projectnoonnu/noonfonts_20-04@2.1/NEXON Lv1 Gothic OTF.woff") format("woff");
    font-weight: normal;
    font-style: normal;
}

.com-title {
    font-family: "NEXON Lv1 Gothic OTF";
}

li {
    list-style: none;
}
</style>
```

***

# Result

![MyPage](/../assets/img/mypage.gif)