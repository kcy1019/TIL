# 연말정산 잔해 치우기

요즘 브라우저와 통신하는 확장 프로그램들은 포트를 열고 웹소켓이나 http로 통신하는 방법을 이용합니다.
그렇기 때문에 
```bash
$ netstat -na | grep LISTEN
$ sudo lsof -i :12345
```
이런 식으로 열려있는 포트를 보고 해당 포트를 어떤 프로세스가 열고 있는지 확인해 봄으로써 비교적 쉽게 찾을 수 있습니다.

프로세스를 찾았다면 이제 날려야겠죠?

```bash
$ launchctl list | grep -i inisafe
$ launchctl stop (위에서 찾은 이름)
$ launchctl remove (역시 같은 이름)
$ sudo killall -9 (프로세스명)
```

추가적으로 삭제까지 하려면 `rm -rf /Application/프로세스명.app` 을 이용하면 됩니다.

저는 오늘 인증서랑 연말정산을 처리하고 나니 결국 astx, astxd, magicline, inisafecrosswebexsvc, 그리고 toucheenkey를 지우게 되더군요.
