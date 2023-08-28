# Shutdown Timer 바꾸기
아래의 스크립트는 모두 `root shell`(`sudo su -`) 속에서 실행해야 합니다.
## 배경
```sh
INSTANCE_NAME="t0"
lxc init images:fedora/38/cloud "${INSTANCE_NAME}"
lxc start "${INSTANCE_NAME}"
lxc exec "${INSTANCE_NAME}" -- sh << 'END'
cat - > "/etc/systemd/system/slowstop.service" <<- 'EOF'
[Service]
ExecStart=
ExecStop=sleep 60
Type=oneshot
RemainAfterExit=true
EOF

while $(systemctl is-system-running &>/dev/null); (($?==1)); do
	:
done

systemctl is-system-running --wait

systemctl daemon-reload
systemctl start slowstop
systemctl stop slowstop --no-block
systemctl poweroff
END
lxc console "${INSTANCE_NAME}"
```
(`CTRL+a q`를 눌러서 나가기)

이와 같이 어떤 서비스가 오래동안 시스템의 셧다운을 막을 수 있습니다. 현재 Fedora 38에서는 기본 최대 셧다운 타이머가 45초입니다.

## 미션: Shutdown Timer를 5초로 바꾸세요

힌트
- [Fedora Changes/Shorter Shutdown Timer](https://fedoraproject.org/wiki/Changes/Shorter_Shutdown_Timer)
- Google에 검색할 때 `site:freedesktop.org/software/systemd/man`를 추가하면(예시: `site:freedesktop.org/software/systemd/man systemctl`) `systemd` 공식 문서를 검색할 수 있습니다

## setup
환경을 만드는 스크립트입니다.
```sh
INSTANCE_NAME="t0"
lxc init images:fedora/38/cloud "${INSTANCE_NAME}"
lxc start "${INSTANCE_NAME}"
lxc exec "${INSTANCE_NAME}" -- sh << END

cat - > "/etc/systemd/system/slowstop.service" <<- 'EOF'
[Service]
ExecStart=
ExecStop=sleep 60
Type=oneshot
RemainAfterExit=true
EOF

while $(systemctl is-system-running &>/dev/null); (($?==1)); do
	:
done

systemctl is-system-running --wait
systemctl daemon-reload
systemctl enable --now slowstop
END
```

## delete
환경을 삭제하는 스크립트입니다. 초기화하려면 `delete` 스크립트를 실행한 다음 `setup` 스크립트를 실행하세요.
```sh
INSTANCE_NAME="t0"
lxc delete "${INSTANCE_NAME}" --force
```

## shell
환경 속 `shell`을 얻는 스크립트입니다. 나가려면 `exit`을 실행하세요
```sh
INSTANCE_NAME="t0"
lxc shell "${INSTANCE_NAME}"
```

## check
설정을 제대로 바꾸었는지 확인하는 스크립트입니다. 마지막 줄이 `FAILURE`이면 실패, `SUCCESS`이면 성공입니다.
```sh
INSTANCE_NAME="t0"
lxc start "${INSTANCE_NAME}"
lxc exec "${INSTANCE_NAME}" -- systemctl daemon-reload

if [ "$(lxc exec "${INSTANCE_NAME}" -- systemctl show -P DefaultTimeoutStartUSec)" = 5s ]; then
	printf '%s\n' "SUCCESS"
else
	printf '%s\n' "FAILURE"
fi
```