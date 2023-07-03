# 설치
[`snapd` 설치](https://snapcraft.io/docs/installing-snap-on-fedora)
```sh
sudo dnf install -y snapd
```
[`lxd` 설치](https://linuxcontainers.org/lxd/docs/latest/installing/)
```sh
sudo snap install lxd
```
설정
```sh
sudo lxd init --preseed <<- 'EOF'
config:
images.auto_update_interval: "0"
networks:
- config:
	ipv4.address: auto
	ipv6.address: auto
description: ""
name: lxdbr0
type: ""
project: default
storage_pools:
- config:
	source: /var/lib/lxd/storage-pools/default
description: ""
name: default
driver: btrfs
profiles:
- config: {}
description: ""
devices:
	eth0:
	name: eth0
	network: lxdbr0
	type: nic
	root:
	path: /
	pool: default
	type: disk
name: default
projects: []
cluster: null
EOF

sudo lxc config set \
images.auto_update_cached=false

sudo lxc profile set default \
boot.autostart=false \
security.nesting=true \
security.privileged=false
```
# 사용
리모트/이미지 탐색
```
lxc remote list
lxc image list ${REMOTE}:${SEARCH_TERM}
```
instance 생성
```
sudo lxc init ${REMOTE}:${IMAGE} ${NAME}
```
instance 생성 뒤 시작
```
sudo lxc launch ${REMOTE}:${IMAGE} ${NAME}
```
instance 시작/중단/재시작
```
sudo lxc start ${NAME}
sudo lxc stop ${NAME}
sudo lxc restart ${NAME}
```
instance 정보
```
sudo lxc info ${NAME}
```
커맨드 실행
```
sudo lxc exec ${NAME} -- ${COMMAND}
```
console 열기
```
sudo lxc console ${NAME}
```
instance 삭제
```
sudo lxc delete --force ${NAME}
```