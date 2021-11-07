#### ! 이번 학부생 프로젝트에서 사용하는 LLVM version은 12.0.0으로 통일하도록 함  

---

#### LLVM Source Code Download
- 다음 링크에 접속하여 원하는 소스 파일을 다운로드하면됨  
링크 : https://releases.llvm.org/download.html

- terminal에서 ` wget 파일주소 `를 통해 다운로드해도 되고,  
- 위 링크에서 직접 소스코드 압축파일을 다운로드해서 사용해도 됨

필요한 파일은 LLVM과 Clang인데, 이 두 가지를 각각 다운로드 받아도 되고,  
아니면 GitHub 맨 밑에 전체 다 합쳐져 있는 source.tar.gz를 다운 받아서 그 안의 llvm과 clang만 이용해도 됨

#### Install
- 압축 푼 코드 llvm -> llvm 디렉토리에 들어가서 하위 디렉토리로 llvm/tools/clang을 찾을 수 있음
- 압축 푼 코드 clang -> 위의 llvm/tools/clang으로 이동

#### Build
빌드 디렉토리 만들기 ` mkdir 빌드디렉토리 `
cmake로 LLVM을 빌드해줌
- ``` cmake "llvm source code root" ```
- llvm 빌드 ` cmake --build ` (시간 굉장히 오래 소요됨 약 3시간)
- 전역으로 llvm을 사용할 수 있게해줌 ` sudo cmake --build . --target install `
- 전역으로 설치되었는지 확인 -> terminal에서 ` llvm-config ` 쳐보면 뜰 것임
- 전역으로 설치하지 않고 사용시 install path를 지정하는 방법 ` cmake -DCMAKE_INSTALL_PREFIX="설치디렉토리" `로 설정 가능

