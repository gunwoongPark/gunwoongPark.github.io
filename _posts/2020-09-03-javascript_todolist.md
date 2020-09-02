---
layout: single
title: "자바스크립트 Todo List 만들기"
categories: 
   - Javascript
author_profile: true
read_time: true
comments: true
---

웹 언어를 공부할 때, '이것' 을 구현 할 수 있다면 어느정도 익숙해져있다고 볼 수 있다.

바로 **Todo List**(할 일 목록) 이다.

이번에는 자바스크립트를 활용하여 TodoList 를 구현해보았다.

## HTML

```html
<!DOCTYPE html>
<html lang="ko">

<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
        integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
    <link rel="stylesheet" href="./style.css">
    <script src="./main.js" defer></script>
</head>

<body>

    <div class="container Login">
        <div class="text-center mt-5" style="font-size: x-large;">Woong's Todo Application</div>
        <input type="text" class="w-100 p-2" placeholder="Type nickname" id="inputNickname" style="margin-top: 20%;" />
    </div>

    <div class="container Main" style="display: none;">

        <div class="row">
            <div class="col">
                <h1 class="text-center">Todo list</h1>
            </div>
        </div>

        <div class="row">
            <div class="col">
                <input type="text" class="w-100 p-2" id="inputTodo" />
            </div>

        </div>

        <hr class="first_hr" />

        <div class="row">

            <div class="col-md-6 pending">
                <p>Pending</p>
                <div class="row">
                    <div class="col">
                        <hr class="second_hr">
                    </div>
                </div>

            </div>

            <div class="col-md-6 finished">
                <p>Finished</p>
                <div class="row">
                    <div class="col">
                        <hr class="second_hr">
                    </div>
                </div>

            </div>

        </div>

        <div class="row">
            <div class="col">
                <div id="clock"></div>
            </div>
        </div>
    </div>
</body>

</html>
```

## CSS

```css
.first_hr{
    background-color: #8c8b8b;
}

.second_hr{
    background-color: #fff;
    border-top: 2px dashed #8c8b8b;
}

#clock{
    font-size: x-large;
    position:fixed;
    bottom:5%;
    right:5%;
}
```

## Javascript

```javascript
// localStorage.clear();

if (localStorage.length == 0) {
    localStorage.isLogin = false;
    localStorage.current = 'none';
}

let clock = document.querySelector('#clock');
setInterval(getTime, 1000);

function getTime() {
    const time = new Date();
    const hour = time.getHours();
    const minutes = time.getMinutes();
    const seconds = time.getSeconds();
    clock.innerHTML = `${hour < 10 ? `0${hour}` : hour}시 ${minutes < 10 ? `0${minutes}` : minutes}분 ${seconds < 10 ? `0${seconds}` : seconds}초`
}

if (localStorage.getItem('isLogin') === 'false') {
    let inputNickName = document.querySelector('#inputNickname');
    inputNickName.addEventListener('keyup', (e) => {
        if (e.keyCode === 13) {
            if (e.target.value.length > 0) {
                let inputText = e.target.value;
                e.target.value = "";
                if (localStorage.getItem(inputText) === null) {
                    var schedule = {
                        pending: [],
                        finished: []
                    }
                    localStorage[inputText] = JSON.stringify(schedule);
                }
                inputTodo.setAttribute('placeholder', 'Hi ' + inputText + ' please type your todo');
                localStorage.setItem('isLogin', 'true');
                localStorage.setItem('current', inputText);
                location.reload(true);
                loginPending();
                loginFinished()
            }
        }
    });
}

else {
    let divLogin = document.querySelector('.Login');
    let divMain = document.querySelector('.Main');
    divLogin.style.display = 'none';
    divMain.style.display = "";
    inputTodo.setAttribute('placeholder', 'Hi ' + localStorage.getItem('current') + ' please type your todo.    If you want to logout, then type in the logout');
    loginPending();
    loginFinished();
    inputTodo.addEventListener('keyup', (e) => {
        if (e.keyCode === 13) {
            if (e.target.value.length > 0) {
                if (e.target.value.toLowerCase() === "logout") {
                    localStorage.isLogin = false;
                    localStorage.current = 'none';
                    location.reload(true);
                }
                let inputText = e.target.value;
                e.target.value = "";
                let storageObject = localStorage[localStorage.getItem('current')];
                schedule = JSON.parse(storageObject);
                schedule.pending.push(inputText);
                localStorage[localStorage.getItem('current')] = JSON.stringify(schedule);
                addTodo();
                location.reload(true);
            }
        }
    });
    checkPend();
    deletePend();
    checkFinish();
    deleteFinish();
}

function loginPending() {
    let pending = document.querySelector('.pending');
    let storageObject = localStorage[localStorage.getItem('current')];
    schedule = JSON.parse(storageObject);
    schedule.pending.forEach((pend, index) => {
        let container = document.createElement('div');
        let divRow = document.createElement('div');
        let divCol1 = document.createElement('div');
        let input = document.createElement('input');
        let divCol2 = document.createElement('div');
        let divCol3 = document.createElement('div');
        let button = document.createElement('button');

        container.id = 'pendContainer';
        divRow.classList.add('row');
        divCol1.classList.add('col-md-1');
        input.setAttribute('type', 'checkbox');
        input.setAttribute('name', 'pendCheckList');
        divCol2.classList.add('col-md-9');
        divCol2.id = "pendText";
        divCol3.classList.add('col-md-1');
        button.classList.add('pendBtn');
        button.classList.add('btn-danger');
        button.classList.add('btn-sm');
        button.setAttribute('name', "pendBtnList");
        button.innerHTML = "Delete";

        divCol3.appendChild(button);
        divCol1.appendChild(input);
        divRow.appendChild(divCol1);
        divRow.appendChild(divCol2);
        divRow.appendChild(divCol3);
        container.appendChild(divRow);
        container.appendChild(document.createElement('br'));

        pending.appendChild(container);
        divCol2.innerHTML = pend;
    });
}

function loginFinished() {
    let finished = document.querySelector('.finished');
    let storageObject = localStorage[localStorage.getItem('current')];
    schedule = JSON.parse(storageObject);
    schedule.finished.forEach((finish, index) => {

        let container = document.createElement('div');
        let divRow = document.createElement('div');
        let divCol1 = document.createElement('div');
        let input = document.createElement('input');
        let divCol2 = document.createElement('div');
        let divCol3 = document.createElement('div');
        let button = document.createElement('button');

        container.id = 'finishContainer';
        divRow.classList.add('row');
        divCol1.classList.add('col-md-1');
        input.setAttribute('type', 'checkbox');
        input.setAttribute('name', 'finishCheckList');
        divCol2.classList.add('col-md-9');
        divCol2.id = "finishText";
        divCol3.classList.add('col-md-1');
        button.classList.add('finishBtn');
        button.classList.add('btn-danger');
        button.classList.add('btn-sm');
        button.setAttribute('name', "finishBtnList");
        button.innerHTML = "Delete";

        divCol3.appendChild(button);
        divCol1.appendChild(input);
        divRow.appendChild(divCol1);
        divRow.appendChild(divCol2);
        divRow.appendChild(divCol3);
        container.appendChild(divRow);
        container.appendChild(document.createElement('br'));

        finished.appendChild(container);
        divCol2.innerHTML = finish
    });
}

function addTodo() {
    let pending = document.querySelector('.pending');
    let storageObject = localStorage[localStorage.getItem('current')];
    schedule = JSON.parse(storageObject);

    let container = document.createElement('div');
    let divRow = document.createElement('div');
    let divCol1 = document.createElement('div');
    let input = document.createElement('input');
    let divCol2 = document.createElement('div');
    let divCol3 = document.createElement('div');
    let button = document.createElement('button');

    container.id = 'pendContainer';
    divRow.classList.add('row');
    divCol1.classList.add('col-md-1');
    input.setAttribute('type', 'checkbox');
    input.setAttribute('name', 'pendCheckList');
    divCol2.classList.add('col-md-9');
    divCol2.id = "pendText";
    divCol3.classList.add('col-md-1');
    button.classList.add('pendBtn');
    button.classList.add('btn-danger');
    button.classList.add('btn-sm');
    button.setAttribute('name', "pendBtnList");
    button.innerHTML = "Delete";

    divCol3.appendChild(button);
    divCol1.appendChild(input);
    divRow.appendChild(divCol1);
    divRow.appendChild(divCol2);
    divRow.appendChild(divCol3);
    container.appendChild(divRow);
    container.appendChild(document.createElement('br'));

    pending.appendChild(container);
    divCol2.innerHTML = schedule.pending[schedule.pending.length - 1];
}

function checkPend() {
    let checkList = document.getElementsByName('pendCheckList');
    checkList.forEach((pend, index) => {
        pend.addEventListener('click', (e) => {
            let delDiv = pend.parentElement.parentElement.parentElement;
            let delText = delDiv.querySelector('#pendText');
            let storageObject = localStorage[localStorage.getItem('current')];
            schedule = JSON.parse(storageObject);
            let delIndex = schedule.pending.indexOf(delText.innerHTML);
            schedule.pending.splice(delIndex, 1);
            schedule.finished.push(delText.innerHTML);
            localStorage[localStorage.getItem('current')] = JSON.stringify(schedule);
            location.reload(true);
        });
    });
}

function deletePend() {
    let btnList = document.getElementsByName('pendBtnList');
    btnList.forEach((btn, index) => {
        btn.addEventListener('click', (e) => {
            let delDiv = btn.parentElement.parentElement.parentElement;
            let delText = delDiv.querySelector('#pendText');
            let storageObject = localStorage[localStorage.getItem('current')];
            schedule = JSON.parse(storageObject);
            let delIndex = schedule.pending.indexOf(delText.innerHTML);
            schedule.pending.splice(delIndex, 1);
            localStorage[localStorage.getItem('current')] = JSON.stringify(schedule);
            location.reload(true);
        })
    })
}

function checkFinish() {
    let checkList = document.getElementsByName('finishCheckList');
    checkList.forEach((finish, index) => {
        finish.addEventListener('click', (e) => {
            let delDiv = finish.parentElement.parentElement.parentElement;
            let delText = delDiv.querySelector('#finishText');
            let storageObject = localStorage[localStorage.getItem('current')];
            schedule = JSON.parse(storageObject);
            let delIndex = schedule.finished.indexOf(delText.innerHTML);
            schedule.finished.splice(delIndex, 1);
            schedule.pending.push(delText.innerHTML);
            localStorage[localStorage.getItem('current')] = JSON.stringify(schedule);
            location.reload(true);
        })
    })
}

function deleteFinish() {
    let btnList = document.getElementsByName('finishBtnList');
    btnList.forEach((btn, index) => {
        btn.addEventListener('click', (e) => {
            let delDiv = btn.parentElement.parentElement.parentElement;
            let delText = delDiv.querySelector('#finishText');
            let storageObject = localStorage[localStorage.getItem('current')];
            schedule = JSON.parse(storageObject);
            let delIndex = schedule.finished.indexOf(delText.innerHTML);
            schedule.finished.splice(delIndex, 1);
            localStorage[localStorage.getItem('current')] = JSON.stringify(schedule);
            location.reload(true);
        });
    });
}
```

부트스트랩을 적극 활용하여 겉으로 보이는 스타일링을 더 효과적으로 하였고, 그리드 레이아웃을 채택하였다.

> 부트스트랩(Bootstrap)은 웹사이트를 쉽게 만들 수 있게 도와주는 HTML, CSS, JS 프레임워크이다.<br/>하나의 CSS 로 휴대폰, 태블릿, 데스크탑까지 다양한 기기에서 작동한다.<br/>다양한 기능을 제공하여 사용자가 쉽게 웹사이트를 제작, 유지, 보수할 수 있도록 도와준다.

간단하게 로그인 기능을 구현하였는데, 닉네임을 입력하고 들어가면 해당 닉네임이 로컬 스토리지 내에 저장되고, 해당 유저가 입력한 Todo(할 일)들을 입력한다면 해당 내용이 로컬 스토리지 내에 저장 되어 로그인 시 해당 유저에 대한 Todo 들이 출력되도록 하였다.

> Local Storage 란 HTML5에서 추가된 저장소입니다. 간단한 키와 값을 저장할 수 있습니다.<br/>비슷한 친구로 Session Storage 라는 것도 있는데 둘의 차이점은 데이터의 영구성입니다.<br/>Local Storage 의 데이터는 사용자가 지우지 않는 이상 계속 브라우저에 남아 있습니다.<br/>하지만 Session Storage 의 데이터는 윈도우나 브라우저 탭을 닫을 경우 제거됩니다.

물론 로그인이 있듯 로그아웃 기능도 추가하였다.

로컬 스토리지 내에 저장되는 정보들은 문자열 형태로 저장이되고, 우리가 해당 정보를 이용하기 위해서는 객체 형태로 받아와야한다.

따라서 'JSON.parse()' 메소드 와 'JSON.stringify()' 메소드 를 잘 활용하여 문자열과 객체의 변환을 자유롭게 하였다.

> JSON.parse() : JSON 문자열의 구문을 분석하고, 그 결과에서 Javascript 값이나 객체를 생성한다.<br/>JSON.stringify() : Javascript 값이나 객체를 JSON 문자열로 변환합니다.<br/>JSON 이라는 단어가 생소하실 수 있지만 우선은 넘어가고 나중에 따로 포스팅하겠습니다.

로컬 스토리지에서 받아온 정보들을 토대로 DOM 으로 접근하여 할일 목록들을 출력한다.

해야 할 일을 체크하면 일을 처리했다고 보고 한 일로 옮겨진다.

반대로도 작동하고, 해야 할 일 사이드와 한 일 사이드에서 모두 사용자가 입력한 Todo 들을 Delete 버튼을 통해 삭제할 수 있다.

아직 익숙하지 못한 자바스크립트 코딩이라 그런지 코드가 여전히 더럽고 비 효율적으로 느껴진다.

특히나 그리드 레이아웃을 채택하여 여러가지 HTML 요소를 DOM 을 통해 접근 하여 그런지 코드의 분량도 많고 그만큼 메모리 소모도 많다.

앞으로 더 많이 여러가지 클론코딩이나 자유 주제, 프로젝트를 통해 자바스크립트에 대해 더 능숙하고 효율적으로 코딩을 할 수 있도록 해야겠다.

추가적으로 부트스트랩이 더 익숙해져야겠다.. ㅎㅎ

![js todo](/../assets/img/js_todo.gif)

시계는 심심해서 추가해봤다.