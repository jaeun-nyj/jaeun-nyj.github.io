## LLVM Pass 1

#### 컴파일러의 각 단계
![image](https://user-images.githubusercontent.com/88612547/136651029-05da5301-dbfd-44e8-9678-e1111c6a2366.png)

#### LLVM Pass란?
- 프로그램을 변환(Transformation)과 최적화(Optimization)을 수행하거나, 프로그램을 분석하는 LLVM의 프레임워크
  
- 위의 그림에서 컴파일러 후반부(Back End)의 Code optmization(코드 최적화), Code generation(코드 생성) 부분의 역할을 LLVM Pass로 수행할 수 있음
- 이 단계에서 자신이 원하는 코드를 프로그램에 임의로 삽입하거나, 변환시킬 수 있음
- 프로그램을 중간코드까지 변환한 이후에 수행이 됨
- 프로그램에 대한 분석도 가능
- 단, target code 생성은 불가능함

#### LLVM Pass 시작하기  
- github에서 LLVM skeleton pass를 검색하여, clone해보자  
` git clone https://github.com/manggoguy/llvm-pass-skeleton.git `  
  
- Build 방법 및 사용법은 https://github.com/manggoguy/llvm-pass-skeleton.git 링크 참조
- IDE는 Visual Studio Code 추천

#### LLVM Pass 빌드하는 방법
1) ` git clone "repo" `  
2) ` cd "clone한 path" `  
3) ` mkdir build `  
4) ` cmake ../ `  
5) ` cmake --build . `  

#### LLVM Pass 사용하는 방법
1) target이 될 source file을 준비  
2) ` clang -emit-llvm -c "source_file.c"  
3) ` opt -load="abs_path_of_llvm_pass_lib.so" -skeleton source.bc -o out.bc `  

#### 소스코드
![image](https://user-images.githubusercontent.com/88612547/136653105-5fc1dfb8-4ce3-4d10-884a-b63fd732a2ed.png)

- 다른 c++ 프로젝트처럼 하나의 Pass당 .h와 .cpp가 하나씩 구성
- ModulePass를 상속받는 클래스 생성
- skeleton.h의 ` static char ID; SkeletonPass() : ModulePass(ID) {} ` 이 부분은 형식상(?) 필요
- 실질적인 동작 수행은 runOnModule에서 이루어짐
- header 파일은 필요한 만큼만 추가하면 됨

#### Pass 등록
Pass를 사용하기 위해 Pass를 등록(?)하기 위한 코드 (반드시 있어야 함)



