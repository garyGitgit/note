이 글은 Windows (OA) 환경에서 vue, vuetify 그리고 electron을 설치하는 가이드입니다.

 

Index



 

 

1. Setup

 

//Prerequisite: node, npm, yarn 그리고 vue (vue-cli) 은 설치가 되어있어야 합니다.
//Guide spec
//    node: v14.15.3
//    npm : v6.14.8
//    yarn: v1.22.10
//    vue : v4.5.8
 
* 중요: 아래 proxy 설정을 한 후 진행 필요
 
0. 윈도우 cmd 창 열기
1. npm proxy 설정: 링크 참고 -> http://12.54.158.60:9090/display/AT/How+to+set+up+npm+proxy 
2. yarn proxy 설정: 링크 참고 -> http://12.54.158.60:9090/display/AT/How+to+set+up+npm+proxy  
3. electron proxy 설정: 
  > set ELECTRON_GET_USE_PROXY="1" (1 말고 아무 값 줘도 됨) 
  > echo %ELECTRON_GET_USE_PROXY% (확인)
  > set GLOBAL_AGENT_HTTP_PROXY=http://12.26.226.2:8080
  > echo %GLOBAL_AGENT_HTTP_PROXY% (확인) 
 
(출처: https://github.com/gajus/global-agent/blob/v2.1.5/README.md#environment-variables)

 

 

2-1. Typescript가 필요없는 경우

 

//Prerequisite: node, npm, yarn 그리고 vue (vue-cli) 은 설치가 되어있어야 합니다.
//Guide spec
//    node: v14.15.3
//    npm : v6.14.8
//    yarn: v1.22.10
//    vue : v4.5.8
 
> cd 작업할 디렉토리
> vue create 프로젝트명(my-project)
  > 옵션 선택
    > Manually selected features
    > Router, Vuex 만 추가
    > 2.x 
    > no (history)
    > Standard config
    > Lint on save
    > In dedicated config file
    > No

 

 

2-2. Typescript 가 필요한 경우

 

//Prerequisite: node, npm, yarn 그리고 vue (vue-cli) 은 설치가 되어있어야 합니다.
//Guide spec
//    node: v14.15.3
//    npm : v6.14.8
//    yarn: v1.22.10
//    vue : v4.5.8
 
* 중요: 아래 proxy 설정을 한 후 진행 필요
 
0. 윈도우 cmd 창 열기
1. npm proxy 설정: 링크 참고 -> http://12.54.158.60:9090/display/AT/How+to+set+up+npm+proxy 
2. yarn proxy 설정: 링크 참고 -> http://12.54.158.60:9090/display/AT/How+to+set+up+npm+proxy  
3. electron proxy 설정: 
  > set ELECTRON_GET_USE_PROXY "1" (1 말고 아무 값 줘도 됨) 
  > echo %ELECTRON_GET_USE_PROXY% (확인)
  > set GLOBAL_AGENT_HTTP_PROXY=http://12.26.226.2:8080
  > echo %GLOBAL_AGENT_HTTP_PROXY% (확인) 
 
(출처: https://github.com/gajus/global-agent/blob/v2.1.5/README.md#environment-variables)
 
> cd 작업할 디렉토리
> vue create 프로젝트명(my-project)
  > 옵션 선택
    > Manually selected features
    > Router, Vuex, Typescript 추가
    > 2.x 
    > class-style component syntax: yes (default)
    > Babel alongside TypeScript: yes (default)
    > no (history)
    > Standard config
    > Lint on save
    > In dedicated config file
    > No

 

 

3. Vuetify 설치

 

//Prerequisite: node, npm, yarn 그리고 vue (vue-cli) 은 설치가 되어있어야 합니다.
//Guide spec
//    node: v14.15.3
//    npm : v6.14.8
//    yarn: v1.22.10
//    vue : v4.5.8
 
> cd my-project
> vue add vuetify
  > (advanced) Configure ... 
    > Yes (HelloWorld.vue)
    > No (No custom theme)
    > No (No custome properties)
    > Material Design Icons
    > Yes (Use fonts)
    > Yes (a-ala-carte)
    > English

 

 

4. Electron 설치

 

> vue add electron-builder
    > 9.0.0
    > 10.0.0
    > 11.0.0
//버전은 계속 업데이트 될 듯 

 

 

5. Electron 실행

 

> yarn electron:serve

 
 

 

6. Electron 실행

미리 정의된 package.json 에 있는 파일들을 다운로드 받고 싶으면, 백승범님 자료 참고 (vue.js 오프라인 패키지 설치)

 

7. Troubleshooting

 


에러

에러 상황

해결법


This dependency was not found:

* @mdi/font/css/materialdesignicons.css in ./src/main.js

Error from chokidar (C:\): Error: EBUSY: resource busy or locked, lstat 'C:\hiberfil.sys'
 yarn electron:serve 
yarn add @mdi/font

특정 패키지가 정상적으로 설치가 안되어서 발생한 문제로 보임.

위 명령어로 추가하면 package.json 에 @mdi/font 가 생기고 (그 전에는 없음)

yarn electron:serve 명령어로 정상적으로 application 이 실행된다.
 
      

 
