
# 소켓 TIME_WAIT



---
출처:  [심플하게 개발](https://limmmee.tistory.com/27)

특정 시간대 request가 많아지면서 소켓 쪽 이슈가 있는것으로 보여진다.

```bash
netstat -nao | grep "TIME_WAIT" | wc -l 
```
---

