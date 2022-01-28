### LLVM을 이용한 Function Link
LLVM을 이용해 코드에 function을 link하는 실습을 해보았다.  
**function link는 내가 실행시키고 있는 코드의 원하는 위치에, C(또 다른 소스코드)로 작성된 함수 코드를 연결하여 끼워넣어 실행**시켜주는 것을 말한다.  
  
이 실습을 하게 된 계기는 하고 있던 프로젝트의 \*BufferCheckPass 작성이 약간 좌절되면서이다.
*(**\*BufferCheckPass**:  Buffer Overflow를 방지하기위해 경계 검사 코드를 LLVM을 통해 생성해 끼워넣어주는 솔루션을 고안했고, 이를 수행하는 pass가 BufferCheckPass)*
LLVM을 이용한 Code Generation의 난이도가 꽤.. 많이 높다는 것을 알게되었다.
특히 생성하려는 코드가 if문이다보니 분기가 되면서 베이직 블럭을 새로 만들고 어쩌구저쩌구 이런 작업이 들어가는데, 이게 굉장히 어려웠다.  
  
그래서 좀 더 쉬운 방법으로 프로젝트를 해결, 마무리하기 위해 고안한 방법이 function link이다. code generation보다 성능은 약간 낮아도 구현은 훨씬 수월한 것 같다.  




### 실습 코드
![image](https://user-images.githubusercontent.com/88612547/151503375-848656bf-063f-4fb1-8c32-16590a4e7fd6.png)

#### skeleton.cpp
pass 작성을 앞에서 사용했던 skeleton pass에다가 원하는 코드만 추가하는 방식으로 작성 되었기 때문에 이름이 skeleton이다.
(pass 세팅이 생각보다 일이 많고 신경쓸게 있어서) 빠르게 짜고 결과를 확인해 보고싶은 때는 보통 이런식으로 하면된다고 박사 오빠가 알려주셨다.  
![image](https://user-images.githubusercontent.com/88612547/151503521-02c247f9-9ab5-40ae-9e8d-bdc28ffa5d57.png)

runOnModule에 구현이 되어있다.  
![image](https://user-images.githubusercontent.com/88612547/151503870-89ca76dd-1bf5-4c76-90cf-8f3f7fdd2367.png)




### how to use
#### 1) rt 디렉토리는 런타임 라이브러리  
add.c 가 런타임 라이브러리로 빌드되며 make 명령어를 통해서 바로 빌드될 수 있게 하였음  
![image](https://user-images.githubusercontent.com/88612547/151502981-ddac0447-fc17-475e-ae58-cb4721e295a4.png)  
여기서는 add.c 의 함수를 수정함으로써 원하는 코드를 넣을 수 있음  
add.a와 add.so 가 생성됨, 각각 정적 라이브러리, 동적 라이브러리임  
여기서는 정적 라이브러리를 사용함  

#### 2) test하려면 test 디렉토리로 이동 
"opt -load=your_pass.so -skeleton --enable-new-pm=0 test.bc -o out.bc"  
로 빌드하면 out.bc 가 생성되고 llvm-dis를 사용하면  
"  %add = add nsw i32 %0, %1"           의 앞에  
"  %2 = call i32 @add(i32 %0, i32 %1)"  이 생성된 것을 볼 수 있음  
"  store i32 %2, i32* %c, align 4"      에서 %add 대신에 %2가 들어가 있음  

#### 3) 하지만 out.bc 에는 add 함수가 없기 때문에 out.bc 를 그대로 빌드하면 사용할 수 없음
따라서, 1)에서 생성한 런타임 라이브러리를 빌드할 때 링크해줘야 함  
"clang out.bc path/add.a" 로 빌드하면 프로그램을 실행할 수 있음  






