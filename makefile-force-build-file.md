# Makefile - 특정 파일을 매번 강제 빌드하도록 하는 방법

물론 이런 일이 생기지 않게 하는 것이 가장 좋지만.. 임시 방편으로 특정한 파일만 매번 강제로 빌드하게 해야 할 경우, 다음과 같은 방법으로 해당 파일의 수정 시각을 업데이트 함으로써 Makefile이 파일을 다시 빌드하게 만들 수 있다.

### 1. `shell touch <file>`

```make
all: $(TARGETS)
    $(shell touch some/file)
    ...
```
