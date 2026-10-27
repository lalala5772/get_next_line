# Get Next Line

## 1. 프로젝트 개요

파일 디스크립터(File Descriptor)를 통해 입력 데이터를 읽고, 호출할 때마다 한 줄씩 반환하는 `get_next_line` 함수를 구현한 프로젝트.

`read()` 시스템 콜을 활용해 데이터를 버퍼 단위로 읽고, 개행 문자(`\n`)를 기준으로 반환할 문자열과 다음 호출에 사용할 나머지 데이터를 분리하도록 구현함.

C 언어의 파일 입출력, 정적 변수, 동적 메모리 할당 및 문자열 처리에 대한 이해를 목적으로 함.

## 2. 주요 기능

- 파일 디스크립터를 통한 데이터 읽기
- 개행 문자(`\n`)를 기준으로 한 줄씩 반환
- 한 번에 읽은 데이터 중 현재 반환할 부분과 나머지 데이터 분리
- 정적 변수를 활용한 호출 간 데이터 유지
- `BUFFER_SIZE`에 따른 버퍼 단위 입력 처리
- Bonus 구현에서 파일 디스크립터별 입력 상태 관리

## 3. 기술 스택

| 항목 | 내용 |
|---|---|
| Language | C |
| System Call | `read()` |
| Memory Management | `malloc()`, `free()` |
| String Processing | 사용자 정의 문자열 함수 |
| Input | File Descriptor |
| Build Configuration | `BUFFER_SIZE` 매크로 |

## 4. 핵심 구현 로직

### 4.1. 데이터 읽기

`ft_read_and_save()`

- `read()`를 이용해 `BUFFER_SIZE`만큼 데이터를 읽음.
- 읽은 데이터를 기존에 저장된 문자열에 연결함.
- 저장된 데이터에 개행 문자가 포함되거나 더 이상 읽을 데이터가 없을 때까지 반복함.

### 4.2. 반환할 한 줄 추출

`ft_get_line()`

- 저장된 문자열에서 개행 문자가 나타나는 위치를 탐색함.
- 개행 문자까지의 문자열을 동적으로 할당해 반환할 라인을 생성함.
- 개행 문자가 존재하면 해당 문자까지 결과에 포함함.

### 4.3. 다음 호출을 위한 데이터 보존

`ft_save()`

- 현재 반환한 라인 이후에 남은 문자열을 분리함.
- 남은 데이터가 있으면 다음 호출에서 사용할 수 있도록 저장함.
- 더 이상 남은 데이터가 없으면 기존 저장 공간을 해제함.

### 4.4. 문자열 유틸리티

`get_next_line_utils.c`에서 문자열 처리에 필요한 함수를 별도로 구현함.

- `ft_strlen()`: 문자열 길이 계산
- `ft_strchr()`: 문자열에서 특정 문자 탐색
- `ft_strjoin()`: 두 문자열 연결

## 5. 파일 구성

| 파일 | 역할 |
|---|---|
| `get_next_line.c` | 한 줄 읽기, 라인 추출 및 잔여 데이터 관리 |
| `get_next_line.h` | 함수 선언 및 헤더 정의 |
| `get_next_line_utils.c` | 문자열 길이 계산, 문자 탐색, 문자열 연결 |
| `get_next_line_bonus.c` | Bonus 버전의 한 줄 읽기 구현 |
| `get_next_line_bonus.h` | Bonus 버전의 함수 선언 |
| `get_next_line_utils_bonus.c` | Bonus 버전에서 사용하는 문자열 유틸리티 |

## 6. 동작 흐름

1. `get_next_line(fd)` 호출
2. 저장된 데이터에 개행 문자가 없으면 `read()`로 추가 데이터 읽기
3. 읽은 데이터를 기존 저장 문자열에 연결
4. 개행 문자를 기준으로 한 줄 추출
5. 반환한 라인 이후의 데이터를 저장
6. 다음 호출에서 남은 데이터부터 이어서 처리

## 7. 컴파일 및 사용 예시

`BUFFER_SIZE`를 지정해 소스 파일과 함께 컴파일할 수 있음.

```bash
cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 \
  get_next_line.c get_next_line_utils.c main.c \
  -o get_next_line
```

`main.c`에서 `get_next_line()`을 호출해 사용함. 위 예시의 `main.c`는 직접 작성해야 하는 테스트용 파일임.

```c
#include "get_next_line.h"
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    int fd;
    char *line;

    fd = open("test.txt", O_RDONLY);
    if (fd < 0)
        return (1);

    while ((line = get_next_line(fd)) != NULL)
    {
        printf("%s", line);
        free(line);
    }

    close(fd);
    return (0);
}
```

## 8. 학습 내용

- `read()` 시스템 콜을 통한 파일 입출력
- 정적 변수(`static`)를 활용한 함수 호출 간 상태 유지
- 동적 메모리 할당과 해제를 통한 문자열 관리
- 개행 문자 기반 문자열 분리
- 버퍼 크기에 따른 입력 처리
- 함수별 책임 분리와 문자열 유틸리티 구현
- Bonus 버전의 파일 디스크립터별 상태 관리 방식
