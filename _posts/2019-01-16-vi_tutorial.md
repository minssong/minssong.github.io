---
layout: post
title:  "vi tutorial 교육 정리"
date:   2019-01-16 21:15:15
categories: [Basecamp]
comments: true
---

# Vi tutorial

### 커서 이동

h,j,k,l로 네 방향 이동
$ : 한 행의 맨 뒤
0 or ^ : 한 행의 맨 앞
i : insert 모드로 전환
I : 행의 맨 앞에 삽입
a: append
o : open
ctrl + [  : normal 모드로 전환

dd : 행 전체 삭제
dw : 단어 삭제
D : 현재 위치에서 행 끝까지 삭제
U : 변경이 있었던 마지막 행 복원
Ctrl-r : 잘못 편집했을 때 되돌리기 (Redo)
dG : 현재 부터 맨 뒤까지 삭제
. : 마지막 명령 재실행

### 파일
* Save and/or die
:wq : 저장후 나가기
:x : 저장 후 나가기
:w <filename> 다른 파일 이름으로 저장하고 나가기
:q! : 파일 변경사항 무시하고 그냥 나가기

* search
/ : 특정 단어 찾기 ('n' : 다음 단어로 이동 가능-순방향, 'N' : 역방향)

* Replace
:%s/hello/music/ : 파일 전체의 범위에 대해서
:10,25s/world$/java : 10행에서 25행까지 중에서
:.,$s/coffee/tea : 현재행부터 파일의 마지막까지 coffee를 tea로 치환 (.:현재행, $s : 파일의 마지막)

* Shell
vi 바깥에서 shell command를 실행해보기

* Buffer
-Visual Block
v : block 단위로 block 지정 가능(복사하여), p로 붙여넣기 가능
V : line by line으로 block 지정 가능

### Customization
:set nu : 행번호 앞에 표기
:set ts=4 : \t 문자의 폭
:syntax on : highlighing이 가능함
:set hlsearch : 검색할때마다 highlighting 되어서 검색됨
.vimrc : 첫글자로 오는 :를 제외한 ex 명령을 기입하면 됨

### 특징
* 치환할 때, 한 행에 같은 단어가 두개 이상 나오면 맨 앞의 단어만 치환 (맨 끝에 g를 붙여서 global하게 가능)


### VIM의 특수 기능
* 탭 분할

* 윈도우 분할
:split
:vsplit
Ctrl-w : 방향키
Ctrl-w Ctrl-w

* 스트림 편집
ls | vim - 
curl http://t0a.st/2GkI | vim - : 다운로드 후 바로 vim으로 편집

* 매핑 (매크로)
:ㅡmap <F2> GoDate : <Esc>:read !date<CR>kJ : F2를 누를때 마다 편집 이력 남기기

-다른 매핑을 피하기 위해 map 대신 noremap 추천
imap & noremap => noremap

* Autocompletion
1. Ctrl -n
2. Ctrl-p
3. 기능이 제한적임
(본인인 작성한 문서 내에서만 유효)
