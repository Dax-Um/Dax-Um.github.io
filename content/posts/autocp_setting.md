---
title: "IntelliJ 알고리즘 문제 풀이 환경 세팅"
date: 2025-07-27
draft: false
categories: ["Algorithm"]
tags: ["Settings"]
thumbnail: "/images/autocp/intellij.png"
thumbnail_alt: "IntelliJ icon"  
---

### AutoCP
알고리즘 문제 풀이를 할 때 해당 사이트에서 테스트 케이스 결과를 확인하기에는 불편한 부분이 있다.
이런 문제를 해결할 수 있는 방법으로 Jetbrain사의 IDE에서 사용할 수 있는 플러그인으로 Auto CP가 있다.
Auto CP는 백준(BOJ), Codeforces, AtCoder 등 경쟁 프로그래밍 문제를 더 빠르고 효율적으로 풀 수 있게 도와주는 플러그인이다.

<img src="/images/autocp/autocp.png" alt="auto cp" width="250" />
<a href="https://pushpavel.github.io/AutoCp/" target="_blank" rel="noopener noreferrer" style="color: #1e90ff;">[AutoCP 바로 가기] 
</a><br><br>

### 환경 설정 (on IntelliJ)
현재 Java로 문제풀이를 하는 관계로 IntelliJ에서 세팅하는 법을 기록한다.
IntelliJ 설치 및 환경 설정은 되어 있다는 전제로 한다.

1. 좌측 상단 톱니바퀴(setting)에서 플러그인 버튼을 클릭한다.
<img src="/images/autocp/setting_plugin.png" alt="settting to plugin" width="250" /><br>

2. autocp를 검색하고 설치한다. 현재 나는 이미 설치되어 있는 상태로 '비활성화'로 보인다.  
<img src="/images/autocp/down_autocp.png" alt="settting to plugin" width="450" /><br>

3. Competitive Companion을 설치한다. (Chrome 확장 프로그램으로 웹사이트 문제를 인식해서 IDE로 전달하는 역할을 한다.)
<br><a href="https://chromewebstore.google.com/detail/competitive-companion/cjnmckjndlpiamhfimnnjmnckgghkjbl" target="_blank" rel="noopener noreferrer" style="color: #1e90ff;">[Competitive Companion 설치 바로 가기] 
</a><br><br>
설치 완료하면 크롬 브라우저 주소창 옆에서 해당 icon을 확인할 수 있다.
<img src="/images/autocp/competitive.png" alt="competitive" width="250" /><br>

4. 환경 세팅을 완료했으니 활용해보자.
백준 사이트에 들어가서 설치한 확장 프로그램 icon을 클릭하면 자동으로 IntelliJ로 알림창이 생기고 확인 클릭 시, 폴더와 파일이 생성된는 걸 확인할 수 있다.
<img src="/images/autocp/online_judge.png" alt="online_judge" width="450" />
<img src="/images/autocp/generate_files.png" alt="generate_files" width="250" />
<img src="/images/autocp/load_online_judgement.png" alt="load_online_judgement" width="450" /><br>

5. 문제 풀이 후 'Alt + x' 단축키로 테스트 결과를 볼 수 있다.
<img src="/images/autocp/result.png" alt="result" width="450" /><br>

### 정리
오늘은 온라인 저지 프로그램 테스트를 IDE에서 쉽고 빠르게 할 수 있는 방법에 대해 포스팅했다. 위에 기술한 내용은 가장 기본이 되는 기능이다. Auto CP 아이콘을 클릭해보면 TC를 추가하는 기능도 있다. 동시에 여러 TC를 돌릴 때 유용할 거 같다. 별로 어렵지 않은 부분이라 본인의 목적에 맞게 사용하면 좋을 거 같다.

