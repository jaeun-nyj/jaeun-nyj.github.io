#### LLVM Pass로 Bit code 파일 분석하기

LLVM pass를 이용하여 Bit code 파일 분석을 해보았다.  
LLVM pass를 실행? 해봤다고 할 수 있겠다.  

방학동안 연구실에서 공부를 할 때 간단하고 직관적인 pass 사용을 위해 skeleton pass를 이용해
.c파일로 .bc와 .ll 파일을 생성하는 것은 해보았었다.

하지만 llvm pass로 분석한 내용을 눈으로 보거나 아니면 pass를 통해 code generation을 하거나 했던적은 없었는데,
드디어 하게되었다.
일단 어떤 기능이든 상관 없으니까 pass 실행결과를 눈으로 봐보고 싶었는데 그 방법을 몰라서 그동안은 굉장히 답답한 느낌을 많이 받았던 것 같다.

사실 이미 어느정도 짜깁기하면서 pass 코드 작성하는 것은 이미 해보았는데, 내가 짠 코드의 실행결과를 못보다 보니 뭐 내가 잘 짰는지 
아님 잘못 짰는지 디버깅도 못했다. 허헣  
여튼 연구실 오자마자 llvm pass 실행먼저 해봤는데 이게 진짜 사람 난감하게 했다.
그냥 opt 명령어 하나 실행시키는건데 알 수 없는 에러들이 계속 발생했다.

일단 내가 배운 명령어는 다음과 같다.  
``` opt -load="pass 절대경로.so" -"pass 이름" source.bc -o out.bc ```  

(위에서 이미 비트코드 생성까지 다했다고 가정)  
저 명령어를 실행시켰을 때, 무슨 당황스러운 에러가 발생했다.  
.so파일에 대해서 파일을 찾을 수 없습니다 에러가 계속 발생했다.  
아니 내눈으로 .so 파일 똑똑히 보이고, 애초에 명쳥어 작성할 때도 자동완성으로 .so까지 바로 인식해서 했는데 어떻게 이런 에러가 발생할 수 있는지 
정말 난감했다. 특히 같은 에러가 발생한 케이스가 인터넷에서 잘 안보였던 것 같다.  

구글링을 통해서 찾아보고 무슨 짓을 분명 했는데 기억이 안난다. 기억이 나면 쓰도록하겠다.  
여튼 그러고 다시 실행해보았는데 에러가 바뀌었다.  

``` Undefined Symbol: _ZTIN4llvm12FunctionPassE ```   
![image](https://user-images.githubusercontent.com/88612547/142770718-91571fa0-21a8-42a4-a8ca-b749b2fa2fab.png)  
File not found에서 undefined symbol 에러로 바뀌었다.  

이제 이러한 에러로 바뀌었다. 
https://github.com/sampsyo/llvm-pass-skeleton/issues/27  
처음에는 github issue에서 같은 문제에 대해 올린 글이 있어서 확인해보았는데  
![image](https://user-images.githubusercontent.com/88612547/142770438-c23193a2-88a0-416a-831b-a49f2fa43477.png)  
이게 답변이었다.  

https://github.com/sampsyo/llvm-pass-skeleton/pull/24  
버전 문제라고 하는데 저기 #24로 가보면   
![image](https://user-images.githubusercontent.com/88612547/142770474-33f8ee13-f441-411a-afbb-425878ce7d4a.png)  

Skeleton.cpp와 CMakeLists.txt에 대한 commit이 있다.
Skeleton.cpp 수정 내용은 그냥 출력에 \n 추가해준거라 무시하고,  

https://github.com/sampsyo/llvm-pass-skeleton/pull/24/commits/a5819b98d2af7f0fe763b48880f9990884182a56  
CMakeList.txt 수정 내용을 확인해보니  
![image](https://user-images.githubusercontent.com/88612547/142770537-825dcd7c-d950-4949-9463-0555b0955aeb.png)  
``` set(CMAKE_CXX_STANDARD 14) ``` 코드가 추가되어 있었다.  

![image](https://user-images.githubusercontent.com/88612547/142770598-2f32106c-6dc4-4e2a-8925-124e908f31e1.png)  
근데 이미 저 코드가 들어가 있었다.  
(처음엔 그것도 모르고 코드 추가 했다가 또 안돼서 보니까 있길래 추가한거 지웠다. ㅋㅋㅋㅋㅋㅋ 코드를 좀 더 꼼꼼히 볼 필요가 있을 것 같다.)  

(+ 참고로 이 사진상에 있는 명령어는 내가 배운 명령어는 아닌데 stackoverflow랑 github에서 계속 저 코드로 알려주길래 저걸로 함 해봤다. 
해보니까 동작은 똑같은 것 같다. 
코드는 다음과 같다.  ``` opt -load ".so 파일 경로" -"pass 이름" source.bc -o out.bc ```
그냥 =이 빠진 거다.)

stackoverflow를 돌아다니면서 사람들의 이야기를 들어보니 ri와 관련된 문제라고 하는 것 같았다.  
그리고 이와 관련해서 나름의 해결 방안을 제시해주는 글을 찾았다.  
https://stackoverflow.com/questions/17225956/developing-an-llvm-pass-with-cmake-out-of-llvm-source-directory  


![image](https://user-images.githubusercontent.com/88612547/142771064-ce296e3e-8fa7-4142-a7cf-7262f83c741e.png)  
CMakeLists.txt에 저 코드를 추가 했다는데, 
![image](https://user-images.githubusercontent.com/88612547/142771137-f00044bc-2623-48f8-bb85-5119b8d54e00.png)  
나도 추가하고 돌려봤는데 여전히 안됐다. 


![image](https://user-images.githubusercontent.com/88612547/142771112-afcaebc3-82f4-45aa-9d3e-14bf0deef966.png)  
그래서 cmake할 때 옵션을 주는 방식으로 해보았는데 됐다!!!

![image](https://user-images.githubusercontent.com/88612547/142771206-a28090ae-1c34-430a-8b68-fc5cde27cbec.png)  
코드는 이렇다. 첫 cmake 때 옵션을 주어야한다.  
``` cmake ../ -DLLVM_REQUIRES_RTTI=1 ```  
뒤는 그대로 해주면 된다.
``` cmake --build . ```

![image](https://user-images.githubusercontent.com/88612547/142771267-d90d1c91-4eee-4207-ac44-1198095fd54d.png)  
유후~~~
기억 상으로 skeleton pass 코드가 함수 이름 출력하는 코드였던 것 같다.

![image](https://user-images.githubusercontent.com/88612547/142771286-6b2de048-1b06-44dd-bc39-1ffe93355611.png)  
박사오빠가 알려준 명령어로 해봐도 된다.  
근데 상대경로로 해도 잘 실행된다.

![image](https://user-images.githubusercontent.com/88612547/142771319-3597d040-0413-41c4-9548-599de5d3675a.png)  
생성된 out.bc 파일 확인도 하고, .ll로 바꿔서 내용도 구경 좀 해보고 했다.
