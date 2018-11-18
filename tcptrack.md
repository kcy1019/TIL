# tcptrack

```bash
sudo apt install tcptrack
if0=$(ip -o link show | awk '$9=="UP"&&X++<1{print $2}'); sudo tcptrack -i ${if0/%?/}
```
<img src="https://raw.githubusercontent.com/kcy1019/TIL/images/tcptrack.png" width="300px">
`tcptrack`을 이용하면 이렇게 네트워크 커넥션과 트래픽을 실시간으로 볼 수 있습니다.
`netstat` 보다 좀 더 친절한 UI가 좋더라구요.

```bash
sudo tcptrack -i ... "dst port 80"
```
이런 식으로 `tcpdump`의 필터 포맷을 이용할 수도 있습니다.
