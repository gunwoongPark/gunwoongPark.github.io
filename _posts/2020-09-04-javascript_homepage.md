---
layout: single
title: "자바스크립트 홈페이지 만들기(feat. express.js axios.js)"
categories: 
   - Javascript
author_profile: true
read_time: true
comments: true
---

자유주제로 홈페이지를 구현했다.

평소에 좋아하는 게임인 오버워치를 테마로 구성하였고, 회원가입, 로그인, 로그아웃, 게시판, 글쓰기 그리고 글삭제 등과 같이 필수적인 기능만 우선 구현했다.

홈페이지를 만들려면 데이터베이스 프론트엔드 백엔드가 모두 잘 어우러져야 한다.

프론트엔드는 HTML CSS JavaScript 를 통해 구현하고 Bootstrap을 활용하였다.

백엔드의 서버는 자바스크립트의 웹 프레임워크 중 하나인 Express.js 를 활용하여 서버 코딩을 하였고 이를 클라이언트와 Axios 라는 모듈을 활용하여 통신 한다.

> Express.js 란 또는 간단히 익스프레스(Express)는 Node.js를 위한 웹 프레임워크의 하나로, MIT 허가서로 라이선스되는 자유-오픈 소스 소프트웨어로 출시되었다.<br/>웹 애플리케이션, API 개발을 위해 설계되었다.<br/>Node.js의 사실상의 표준 서버 프레임워크로 불리고 있다.<br/>원 제작자 TJ Holowaychuk는 이 소프트웨어를 시나트라에 영향을 받은 서버로 기술하고 있으며 이는 플러그인으로 이용 가능한 수많은 기능들을 갖추면서 크기는 상대적으로 최소한임을 의미한다.<br/>Express 는 몽고DB 데이터베이스 소프트웨어, AngularJS 프론트엔드 프레임워크와 함께 MEAN 스택의 백엔드 구성 요소이다.

> Axios 란 axios는 HTTP 클라이언트 라이브러리로써, 비동기 방식으로 HTTP 데이터 요청을 실행합니다.<br/>내부적으로 AXIOS는 직접적으로 XMLHttpRequest 를 다루지 않고 “AJAX 호출”을 할 수 있습니다.

데이터베이스는 따로 구현하진 않고 해당 디렉토리 내에서 JSON 파일로 관리하였다.

> JSON 이란 속성-값 쌍 또는 "키-값 쌍"으로 이루어진 데이터 오브젝트를 전달하기 위해 인간이 읽을 수 있는 텍스트를 사용하는 개방형 표준 포맷이다. 

이번 프로젝트의 핵심은 **서버**와 **통신**을 주제로 코드를 리뷰하겠다.

# Code

우선 가장 기본적인 서버코딩을 한 부분이다.

라우터로 나누지 않고 한 파일에 모두 작성했으므로 가독성에 유의하기 바란다.

## Server.js

```javascript
const fs = require('fs');
const express = require('express');
const app = express();

app.use(express.json())
app.use(express.static('./'))

app.get('/', (req, res) => {
    res.sendFile(__dirname + "/main.html");
})

app.get('/login', (req, res) => {
    res.sendFile(__dirname + "/login.html")
})

app.post('/server_login', (req, res) => {
    let userObj = {
        id: req.body.id,
        pwd: req.body.pwd
    }

    if (!fs.existsSync('users.json')) {
        res.send('회원가입된 정보가 없습니다.');
    }

    else {
        fs.readFile('users.json', 'utf8', function (err, data) {
            let flag = false;
            let jsonArray = JSON.parse(data);

            for (let idx = 0; idx < jsonArray.length; ++idx) {
                if (jsonArray[idx].id === userObj.id && jsonArray[idx].pwd === userObj.pwd) {
                    flag = true;
                    res.send(`${jsonArray[idx].nickName}`);
                }
            }

            if (!flag) {
                res.send('회원가입된 정보가 없습니다.');
            }

        })
    }
})

app.get('/signup', (req, res) => {
    res.sendFile(__dirname + "/signup.html")
})

app.post('/server_signup', (req, res) => {

    // 회원 정보
    let userObj = {
        nickName: req.body.nickName,
        id: req.body.id,
        pwd: req.body.pwd
    }

    // 파일이 존재하면
    if (fs.existsSync('users.json')) {
        fs.readFile('users.json', 'utf8', function (err, data) {
            let jsonArray = JSON.parse(data);

            if (jsonArray.filter(user => user.id == userObj.id).length > 0)
                res.send('중복된 아이디입니다!');

            // 닉네임 중복검사
            else if (jsonArray.filter(user => user.nickName == userObj.nickName).length > 0)
                res.send('중복된 닉네임입니다!');

            else {
                jsonArray.push(userObj);
                const jsonObj = JSON.stringify(jsonArray);
                fs.writeFileSync('users.json', jsonObj);
                res.send(`${userObj.nickName}님 가입을 환영합니다.`);
            }


        })
    }

    // 존재하지 않으면 (최초)
    else {
        let jsonArray = new Array();
        jsonArray.push(userObj);

        const jsonObj = JSON.stringify(jsonArray);

        fs.writeFileSync('users.json', jsonObj)
        res.send(`${userObj.nickName}님 가입을 환영합니다.`);
    }

})

app.get('/post', (req, res) => {
    res.sendFile(__dirname + "/post.html")
})

app.get('/server_post', (req, res) => {
    if (fs.existsSync('posts.json')) {
        fs.readFile('posts.json', 'utf8', function (err, data) {
            let postArray = JSON.parse(data);
            res.send(postArray);
        })
    }
    else {
        res.send('게시물이 존재하지 않습니다.');
    }
})

app.get('/write', (req, res) => {
    res.sendFile(__dirname + "/write.html")
})

function guid() {
    function s4() {
        return Math.floor((1 + Math.random()) * 0x10000)
            .toString(16)
            .substring(1);
    }
    return s4() + s4() + '-' + s4() + '-' + s4() + '-' +
        s4() + '-' + s4() + s4() + s4();
}

app.post('/server_write', (req, res) => {
    let postObj = {
        nickName: req.body.nickName,
        title: req.body.title,
        contents: req.body.contents
    }

    const date = new Date();

    postObj.guid = guid();
    postObj.date = `${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()} ${date.getHours()}:${date.getMinutes()}`


    if (fs.existsSync('posts.json')) {
        console.log('있어!');

        fs.readFile('posts.json', 'utf8', function (err, data) {
            let jsonArray = JSON.parse(data);

            jsonArray.push(postObj);
            const jsonObj = JSON.stringify(jsonArray);
            fs.writeFileSync('posts.json', jsonObj);

        })
    }

    // 게시글 최초 등록
    else {
        console.log('없어!');

        let jsonArray = new Array();
        jsonArray.push(postObj);

        const jsonObj = JSON.stringify(jsonArray);

        fs.writeFileSync('posts.json', jsonObj)
    }

    res.send('게시글이 등록되었습니다.');
})

app.get('/postDetail', (req, res) => {
    res.sendFile(__dirname + '/postDetail.html')
})

app.get('/postDetail/:guid', (req, res) => {
    fs.readFile('posts.json', 'utf8', function (err, data) {
        const jsonArray = JSON.parse(data);

        const contentsObj = jsonArray.filter(el => el.guid === req.params.guid);
        res.send(contentsObj);
    })
})

app.delete('/delete/:guid', (req, res) => {
    fs.readFile('posts.json', 'utf8', function (err, data) {
        const jsonArray = JSON.parse(data);
        const index = jsonArray.findIndex(el => el.guid === req.params.guid);
        jsonArray.splice(index, 1);

        const jsonObj = JSON.stringify(jsonArray);

        fs.writeFileSync('posts.json', jsonObj);

        res.send('게시물이 삭제되었습니다.');

    })

})

app.listen(3000, () => {
    console.log('open server')
})
```

JSON 파일로 데이터베이스를 관리하기 때문에 자바스크립트의 파일 입출력 모듈인 fs 를 활용하였다.

기본적인 구조는 특정 url 로 진입하면 거기에 맞는 HTML 파일을 뿌려주는 형식으로 하였고, 서버쪽은 따로 함수 url 파라미터의 앞부분에 server 가 붙어있다.

포트는 3000 으로 고정한다.

핵심적인 부분은 최초의 데이터베이스 JSON 파일이 생성되지 않았을 때 생성과 동시에 정보를 저장.

게시글은 구분하기 위한 고유 아이디를 생성하여 JSON 파일에 같이 저장.

JSON 파일은 회원가입을 한 유저의 정보를 저장하는 JSON 파일이 있고, 게시물의 정보를 저장하는 JSON 파일도 존재.

자바스크립트 배열 내장함수를 잘 활용하여 중복검사같은 예외처리.

게시글 내부로 들어가는 행위와 게시글을 삭제하는 행위는 서버측과의 통신을 params 로 통일.

<br/>

다음은 각 페이지별 통신 부분이다.

## 회원가입

```javascript
function chkID() {
    var id = document.querySelector('#id').value;
    var num = id.search(/[0-9]/g);
    var eng = id.search(/[a-z]/ig);

    if (id.length < 8 || id.length > 20) {
        alert("아이디는 8자리 ~ 20자리 이내로 입력해주세요.");
        return false;
    } else if (id.search(/\s/) != -1) {
        alert("아이디는 공백 없이 입력해주세요.");
        return false;
    } else if (num < 0 || eng < 0) {
        alert("아이디는 영문,숫자를 혼합하여 입력해주세요.");
        return false;
    } else {
        console.log("통과");
        return true;
    }
}

function chkPW() {
    var pw = document.querySelector('#pwd').value;
    var num = pw.search(/[0-9]/g);
    var eng = pw.search(/[a-z]/ig);
    var spe = pw.search(/[`~!@@#$%^&*|₩₩₩'₩";:₩/?]/gi);

    if (pw.length < 8 || pw.length > 20) {
        alert("비밀번호를 8자리 ~ 20자리 이내로 입력해주세요.");
        return false;
    } else if (pw.search(/\s/) != -1) {
        alert("비밀번호는 공백 없이 입력해주세요.");
        return false;
    } else if (num < 0 || eng < 0 || spe < 0) {
        alert("비밀번호는 영문,숫자, 특수문자를 혼합하여 입력해주세요.");
        return false;
    } else {
        console.log("통과");
        return true;
    }
}

function initBtn() {
    document.querySelector('#registerBtn').addEventListener('click', () => {

        if (document.querySelector('#id').value === "" || document.querySelector('#pwd').value === "" || document.querySelector('#pwdConfirm').value === "" || document.querySelector('#nickName') === "")
            alert('폼을 제대로 입력해주세요.');

        else if (!chkID());

        else if (!chkPW());

        else if (document.querySelector('#pwd').value !== document.querySelector('#pwdConfirm').value) {
            alert('비밀번호가 다릅니다.');
            document.querySelector('#pwd').value = "";
            document.querySelector('#pwdConfirm').value = "";
        }

        else {
            axios.post('http://localhost:3000/server_signup', {
                id: document.querySelector('#id').value,
                pwd: document.querySelector('#pwd').value,
                pwdConfirm: document.querySelector('#pwdConfirm').value,
                nickName: document.querySelector('#nickName').value
            }).then(res => {

                if (res.data === "중복된 아이디입니다!")
                    alert(`${res.data}`);

                else if (res.data === "중복된 닉네임입니다!") {
                    alert(`${res.data}`);
                }

                else {
                    alert(`${res.data}님 회원가입 축하드립니다.`);
                    location.href = "http://localhost:3000/login";
                }

            }).catch(err => {

                console.log(err);

            })

        }

    })
}

initBtn();
```

회원가입시에 해야할 예외처리가 많아 통신쪽 코드 중 가장 분량이 많다.

기본적으로 우리가 아는 최소 몇 자리 이내의 입력과 아이디의 경우 영문과 숫자를 혼합하고 비밀번호의 경우에는 추가적으로 특수문자까지 혼합하여 입력을 해주어야한다.

또한 '비밀번호'와 '비밀번호 확인' 부분이 같아야 한다는 많은 예외처리 조건이 존재한다.

아이디나 닉네임은 중복처리를 서버측에서 유저 정보가 있는 JSON 파일에 존재하는지 검사하였고 돌아온 정보를 바탕으로 클라이언트에게 알맞은 에러 메시지를 출력한다.

모든 예외처리를 지나면 서버측에서 온 사용자의 닉네임을 클라이언트측에 회원가입을 축하한다는 메시지를 함께 출력하고, 로그인 페이지로 이동한다.

<br/>

## 로그인

```javascript
document.querySelector('#loginBtn').addEventListener('click', () => {

    if (document.querySelector('#id').value === "") {
        alert("아이디를 입력해주세요.");
    }

    else if (document.querySelector('#pwd').value === "") {
        alert('비밀번호를 입력해주세요.');
    }

    else {
        axios.post("http://localhost:3000/server_login", {
            id: document.querySelector('#id').value,
            pwd: document.querySelector('#pwd').value
        }).then(res => {
            if (res.data === "회원가입된 정보가 없습니다.") {
                alert(`${res.data}`);
                document.querySelector('#id').value = "";
                document.querySelector('#pwd').value = "";
            }
            else {
                alert(`${res.data}님 환영합니다!`);
                console.log(res);
                sessionStorage.setItem('nickName', res.data)

                if (document.referrer === "http://localhost:3000/signup") {
                    location.href = "http://localhost:3000/";
                }
                else {
                    window.history.back();
                }

            }

        }).catch(err => {
            console.log(err);
        })
    }

})
```

로그인은 딱히 특별하진 않고, 통신으로 서버측에 넘겨주는 정보를 토대로 로그인을 진행한다.

아이디와 비밀번호가 일치하는 것이 유저 정보가 있는 JSON 파일에 존재하면 로그인을 성공했다는 메시지와 함께 Session Storage 에 사용자의 닉네임을 저장한다.

이 후, 페이지를 메인 페이지로 이 전 페이지로 이동하는데 만약 이전 페이지가 회원가입 페이지일 경우 홈으로 이동시킨다.

<br/>

## 게시물 리스트

```javascript
function initTable() {
    let table = document.querySelector('#postList');

    axios.get('http://localhost:3000/server_post').then(res => {
        if (res.data === "게시물이 존재하지 않습니다.")
            alert(`${res.data}`);
        else {
            let postList = document.querySelector('#postList');

            for (let idx = 0; idx < res.data.length; ++idx) {
                let tr = document.createElement('tr');

                tr.setAttribute('class', 'listRow');

                let number = document.createElement('td');
                let title = document.createElement('td');
                let nickName = document.createElement('td');
                let date = document.createElement('td');

                number.innerHTML = idx + 1;
                title.innerHTML = res.data[idx].title;
                nickName.innerHTML = res.data[idx].nickName;
                date.innerHTML = res.data[idx].date;

                tr.appendChild(number);
                tr.appendChild(title);
                tr.appendChild(nickName);
                tr.appendChild(date);

                tr.addEventListener('click', () => {
                    location.href = "http://localhost:3000/postDetail?id=" + res.data[idx].guid
                })

                table.appendChild(tr);
            }
        }
    }).catch(err => {
        console.log(err);
    })
}

initTable();
```

다음은 게시물들이 테이블에 출력되는 형태를 띄우는 게시물 리스트 부분이다.

유저의 정보를 저장하는 JSON 파일 외에도 게시물의 정보를 저장하는 JSON 파일이 존재하는 것을 위에서 설명했다.

서버측에서 보내온 게시물 정보(배열)를 바탕으로 요소에 DOM 으로 접근하여 테이블을 구성한다.

각 테이블을 클릭 시 해당 게시물의 상세정보를 보여주는 게시물 내부로 이동해야 하기 때문에 해당 페이지로 이동하는 이벤트 리스너를 추가하는데, url 에 Query String 형식으로 게시물의 고유 아이디를 추가하여 각 게시물들을 구분할 수 있도록 한다.

<br/>

## 게시물 내부

```javascript
function getParameterByName(name) {
    name = name.replace(/[\[]/, "\\[").replace(/[\]]/, "\\]");
    var regex = new RegExp("[\\?&]" + name + "=([^&#]*)"),
        results = regex.exec(location.search);
    return results === null ? "" : decodeURIComponent(results[1].replace(/\+/g, " "));
}

function initPost() {
    const guid = getParameterByName('id')

    axios.get(`http://localhost:3000/postDetail/${guid}`).then(res => {
        document.querySelector('#title').innerHTML = res.data[0].title;
        document.querySelector('#nickName').innerHTML = `작성자 : ${res.data[0].nickName}`;
        document.querySelector('#date').innerHTML = `작성일 : ${res.data[0].date}`;
        document.querySelector('#contents').innerHTML = res.data[0].contents;

        if (sessionStorage.getItem('nickName') !== res.data[0].nickName)
            document.querySelector('#deleteBtn').style.display = "none";

        else {
            document.querySelector('#deleteBtn').addEventListener('click', () => {

                axios.delete(`http://localhost:3000/delete/${guid}`).then(res => {
                    if (res.data === "게시물이 삭제되었습니다.") {
                        alert(`${res.data}`);
                        location.href = "http://localhost:3000/post";
                    }
                }).catch(err => {
                    console.log(err);
                })

            })
        }

    }).catch(err => {
        console.log(err);
    })
}

initPost();
```

게시물 내부 부분으로 서버측과 유일하게 params 로 통신한다.

params 로 전달된 고유 아이디를 토대로 서버측에서는 해당 고유 아이디와 같은 게시물의 정보를 보내준다.

받은 정보를 기반으로 요소의 DOM 으로 접근하여 게시물의 상세한 정보(작성자, 작성일, 내용 등..)을 띄운다.

추가적으로 게시물 삭제는 Session Storage 에 저장되어 있는 로그인 정보인 닉네임이 게시물의 작성자와 같을 때만 보이게 하고 해당 게시물을 삭제하는 통신 또한 위와 마찬가지로 진행한다.

삭제가 완료되면 게시물 리스트 페이지로 돌아온다.

<br/>

## 게시물 작성

```javascript
function initBtn() {
    document.querySelector('#registerBtn').addEventListener('click', () => {
        if (document.querySelector('#title').value === "")
            alert('제목을 입력해주세요.');

        else if (document.querySelector('#contents').value === "")
            alert('내용을 입력해주세요.');

        else {

            axios.post('http://localhost:3000/server_write', {
                nickName: sessionStorage.getItem('nickName'),
                title: document.querySelector('#title').value,
                contents: document.querySelector('#contents').value
            }).then(res => {
                if (res.data === "게시글이 등록되었습니다.") {
                    alert(`${res.data}`);
                    location.href = "http://localhost:3000/post";
                }

            }).catch(err => {
                console.log(err);
            })
        }

    })

}

initBtn();
```

게시물 작성은 비교적 간단하다.

폼으로 입력한 정보들을 서버측으로 넘겨주면 서버측에서 해당 정보를 바탕으로 게시물 JSON 파일로 저장한다.

정상적으로 저장이 완료되면 클라이언트측에 알리고 게시물 리스트 페이지로 돌아온다.

<br/>

# Result

![Axios Express Homepage](/../assets/img/axios_express_homepage.gif)