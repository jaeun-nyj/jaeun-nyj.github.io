### disassembly code - function call  
  
``` c
#include <stdio.h>

void function(int a, int b, int c)
{
    printf("fuction is called\n");
}

void main()
{
    printf("main is called");
    function(1, 2, 3);
}
```
위 코드를 gcc로 컴파일하고, gdb로 디스어셈블 해보았다.  

#### disassemble main
``` bash
(gdb) disassemble main
Dump of assembler code for function main:
   0x0000121c <+0>:     endbr32 
   0x00001220 <+4>:     lea    0x4(%esp),%ecx
   0x00001224 <+8>:     and    $0xfffffff0,%esp
   0x00001227 <+11>:    pushl  -0x4(%ecx)
   0x0000122a <+14>:    push   %ebp
   0x0000122b <+15>:    mov    %esp,%ebp
   0x0000122d <+17>:    push   %ebx
   0x0000122e <+18>:    push   %ecx
   0x0000122f <+19>:    call   0x1269 <__x86.get_pc_thunk.ax>
   0x00001234 <+24>:    add    $0x2da0,%eax
   0x00001239 <+29>:    sub    $0xc,%esp
   0x0000123c <+32>:    lea    -0x1fba(%eax),%edx
   0x00001242 <+38>:    push   %edx
   0x00001243 <+39>:    mov    %eax,%ebx
   0x00001245 <+41>:    call   0x1080 <printf@plt>
   0x0000124a <+46>:    add    $0x10,%esp
   0x0000124d <+49>:    sub    $0x4,%esp
   0x00001250 <+52>:    push   $0x3
   0x00001252 <+54>:    push   $0x2
   0x00001254 <+56>:    push   $0x1
   0x00001256 <+58>:    call   0x11ed <function>
   0x0000125b <+63>:    add    $0x10,%esp
   0x0000125e <+66>:    nop
   0x0000125f <+67>:    lea    -0x8(%ebp),%esp
   0x00001262 <+70>:    pop    %ecx
   0x00001263 <+71>:    pop    %ebx
   0x00001264 <+72>:    pop    %ebp
   0x00001265 <+73>:    lea    -0x4(%ecx),%esp
   0x00001268 <+76>:    ret    
End of assembler dump.
```

1. ` 0x0000121c <+0>:     endbr32 `  
CET의 indirect branch tracking이라는 jmp/call 이후 비정상적인 행동을 검사하는 보호기법이 있고,
이 보호 기법에서 endbr32나 endbr64라는 새로운 명령어를 사용한다고 한다.  
CET가 활성화되면 각 함수 프롤로그에 ENDBR32/ENDBR64 명령어를 삽입하고
JMP와 CALL이후에 ENDBR32/ENDBR64 명령어가 나오지 않는다면 control protection 예외(#CP)가 발생한다.
CET를 지원하지 않는 프로세서에서는 nop로 처리된다.  
(여기서 CET는 Control-Flow Enforcement Technology Specification의 약자로, ROP/JOP 공격시 보통 흐름을 제어하는 명령인 ret, call, jmp를 
사용하는데, 이를 막기 위한 메모리 보호기법이 CET이다. shadow stack, indirect branch tracking을 통해 ROP/JOP 형태의 흐름제어를 막는다고 한다.)  

여튼 main 함수가 call 되었고, 이후 비정상적인 행동을 하는가를 검사하는 보호기법이 적용되어서 맨 앞에 이런 명령이 나온 것 같다.  

2. ` 0x00001220 <+4>:     lea    0x4(%esp),%ecx`
AT&T syntax에서 0x4(%esp)는 %esp+0x4를 의미하므로, %ecx에 %esp+0x4를 넣어주는 명령이다.  

3. ` 0x00001224 <+8>:     and    $0xfffffff0,%esp `


6. ` 0x00001227 <+11>:    pushl  -0x4(%ecx) `
7. ` 0x0000122a <+14>:    push   %ebp `
8. ` 0x0000122b <+15>:    mov    %esp,%ebp `
9. `  `
10. ``
9 ``
11. ``
12. ``
13. ``
14. ``
15. ``
16. ``
17. ``
18. ``
19. ``
20. ``

#### Reference
- https://core-research-team.github.io/2020-05-01/memory (CET indirect branch tracking - endbr32)
- https://stackoverflow.com/questions/3481207/what-does-lea-0x4esp-ecx-mean-in-att-syntax (AT&T syntax - 0x4(%esp))
- 
