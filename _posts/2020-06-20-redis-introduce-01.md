---
title: Redis - Redis!?
date: 2020-06-20 11:21:50
photos:
 - https://media.vlpt.us/images/jbb9229/post/226b55e4-efa7-4601-9c1f-580ca8e46a63/1100px_Redis_Logo_01.png
categories:
 - Redis
tags: 
 - NoSQL
 - Redis
---
# REDIS (*RE*mote *DI*ctionary *S*erver)

최근 접한 프로젝트에 Redis가 적용되어 있었습니다. <br/>
이전까지 Redis를 사용해 본 적이 없기 때문에 관련 내용 정리합니다.

## NoSQL ?
일반적인 관계형 데이터베이스와는 다른 형태로 데이터를 저장 및 검색하는 데이터베이스 이며, 비 관계형 데이터베이스라고 표현하기도 합니다.<br/>
기존의 관계형 데이터베이스 시스템이 기하급수적으로 증가하는 데이터를 처리하기에는 많은 부담과 문제가 있었습니다. <br/>
특히 장비의 성능이 좋을수록 성능을 향상 `(Scale-up: 수직적 확장)` 시키는 비용이 기하급수적으로 증가하기 때문입니다.<br/>
따라서, 데이터를 분산하여 저장 `(Scale-out: 수평적 확장)` 을 목표로 NoSQL이 등장하게 됩니다.<br/>
이 NoSQL의 특징은 단순 검색 및 추가 작업을 위해 최적화된 키 값 저장 공간으로, latency(지연 시간)와 Throughput(처리 량) 관련하여 이익을 내는 것이 목적입니다.<br/>
NoSQL에는 많은 종류가 있고 기본적인 분류는 데이터 모델에 기반을 둡니다. 그 중 Redis는 Key-Value 형태의 비 관계형 데이터베이스 입니다.

## In-Memory Database ?
Redis는 앞서 말했듯 `Key-Value` 형태의 데이터 모델을 저장하는 비 관계형 데이터베이스 이며, InMemory DB 입니다. <br/>
InMemory DB는 데이터베이스가 메인 메모리에 설치되어 운영되는 방식의 데이터베이스 입니다. <br/>
메인 메모리에 데이터베이스를 설치하는 이유는 디스크 접근이 메모리 접근보다 느리기 때문이며, <br/>
데이터 양의 빠른 증가로 기존 데이터베이스 응답 속도가 떨어지는 문제를 해결할 수 있는 대안입니다.<br/>
단점으로는 매체가 휘발성 입니다. 그래서 보통은 캐싱, 세션과 같은, 임시 데이터에 주로 사용되며, <br/>
메인 메모리의 용량이 부족할 경우 가상메모리를 사용하여 역효과가 일어나기도 합니다.

## Data Type
Redis는 Key로 참조되는 Value 타입을 다양하게 지정하여 저장할 수 있습니다.<br/>
List, String, Set, Sorted Set 등 여러 데이터를 지정하여 손쉽고 편리하게 데이터를 저장할 수 있습니다.

## Persistence (영속성)
본문에 내용에 의하면 In-Memory Database의 단점으로 데이터의 휘발성을 언급하였습니다.<br/>
그러나 Redis의 경우 SnapShot, AOF 와 같은 방식으로 일정 주기와 명령어를 통해 데이터를 보존할 수 있습니다.

>
*SnapShot* <br/>
SnapShot는 기존 RDBMS에서도 사용하고 있는 어떤 특정 시점의 데이터를 DISK에 옮겨담는 방식을 뜻합니다.

SnapShot 사용 시 다음과 같은 고려사항이 있습니다
- 레디스에 저장되는 데이터 크기와 물리 메모리의 비율 고려
- 리눅스 운영체제의 경우 vm.overcommit_memory=1 설정 확인
- Swqp/SnapShot에 대한 Disk I/O가 많아질 수 있음

>
*AOF*  <br/>
Redis의 모든 write/update 연산 자체를 모두 log 파일에 기록하는 형태입니다. <br/>
서버가 재 시작할 시 write/update를 순차적으로 재실행, 복구 합니다.

AOF 사용 시 다음과 같은 고려사항이 있습니다.
- 적당한 디스크 용량과 IO속도를 보장해야 함
- Redis 재시작 시 SnapShot보다 느리게 시작 됨.

>
[레디스 공식문서](https://redis.io/topics/persistence) 에서의 권장사항은 RDBMS 수준의 안전성을 원한다면 두 가지 지속성 방법을 모두 사용해야 한다는 것 입니다. <br/>
주기적으로 SnapShot으로 백업하고 SnapShot 이후 다음 SnapShot까지의 저장을 AOF 방식으로 수행하는 것입니다.

## Sharding
Sharding은 각 데이터를 특정 조건에 따라서 서버에 분산 저장하는 기법입니다. <br/>
단일의 데이터를 다수의 데이터베이스로 쪼개어 나누는 걸 말합니다. <br/>
단일의 데이터베이스의 저장중인 데이터가 너무 클 때, 데이터를 구간별로 쪼개어 나눔으로써 <br/>
데이터를 빠르게 검증할 수 있어 속도를 향상시킬 수 있습니다. <br/>
Redis는 읽기 성능 증대를 위한 서버 측 복제와 쓰기 성능 증대를 위한 클라이언트 측 샤딩을 지원합니다.

