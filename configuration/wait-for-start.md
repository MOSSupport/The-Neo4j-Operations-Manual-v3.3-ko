## 3.4. Neo4j 조사

```
이 섹션에서는 Neo4j 시작 후, 요청 준비가 여부를 확인하기 위해 Neo4j를 폴링하는 방법을 알아봅니다.
```

Neo4j 시작 후, 데이터베이스가 요청을 처리할 준비가 되었는지 확인하려면 시간이 필요합니다. 데이터 베이스에 의존하는 시스템은 네트워크 결함 및 다른 일시적인 중단에 대응할 수 없을 때 재시도할 수 있어야합니다. 시작 후, Neo4j가 작동하려면 Bolt나 HTTP 앤드포인트가 성공 반응을 줄때까지 기다려야 합니다. 

폴링 방법에 대한 자세한 내용은 다음을 참조하십시오.:

+ 클라이언트가 HTTP나 Bolt를 사용하는지의 여부
+ 암호화나 인증이 가능한지 여부

Neo4j가 시작을 실패할 경우를 대비해서 타임 아웃을 포함하는 것이 중요합니다. 일반적으로, 10초는 충분하지만 데이터 베이스 회복이나 업그레이드는 저장소의 용량에 따라 더 오래걸릴 수도 있습니다. 인스턴스가 클러스터의 일부라면 앤드포인트는 다른 인스턴스가 시작되고 클러스터가 생성되기 전에는 사용할 수 없습니다. 

아래는 암호화 및 인증이 비활성화 된 HTTP 앤드포인트를 사용하는 폴링의 예입니다.

```
end="$((SECONDS+10))"
while true; do
    [[ "200" = "$(curl --silent --write-out %{http_code} --output /dev/null http://localhost:7474)" ]] && break
    [[ "${SECONDS}" -ge "${end}" ]] && exit 1
    sleep 1
done
```

아래는 HTTP 대신 Bolt를 사용한 예입니다. 

```
end="$((SECONDS+60))"
while true; do
    nc -w 2 localhost 7687 && break
    [[ "${SECONDS}" -ge "${end}" ]] && exit 1
    sleep 1
done
```
