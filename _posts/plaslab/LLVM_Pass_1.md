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

#### 소스코드
- 다른 c++ 프로젝트처럼 하나의 Pass당 .h와 .cpp가 하나씩 구성
- ModulePass를 상속받는 클래스 생성
- static char ID; SkeletonPass() : ModulePass(ID) {} 이 부분은 형식상(?) 필요
- 실질적인 동작 수행은 runOnModule에서 이루어짐
- header 파일은 필요한 만큼만 추가하면 됨

#### Pass 등록
Pass를 사용하기 위해 Pass를 등록(?)하기 위한 코드 (반드시 있어야 함)



