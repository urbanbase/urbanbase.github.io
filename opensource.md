---
layout: page
title: kakao의 오픈소스
image: /files/covers/opensource.jpg
permalink: /opensource/
sitemap: yes
excerpt: kakao의 오픈소스를 소개합니다.
tags: [opensource,s2graph,mrte,hbase-tools,hbase-region-inspector,adt,daum-editor]
---
<a id="forkme" href="https://github.com/kakao"></a>

## Apache S2Graph

* [대용량 분산 그래프DB "S2Graph"](http://tech.kakao.com/2016/01/29/opensource-1-s2graph/)
* [아파치 인큐베이터 프로젝트](http://s2graph.incubator.apache.org)로 선정(2015/11/29)
* [http://github.com/kakao/s2graph](http://github.com/kakao/s2graph) -> [https://github.com/apache/incubator-s2graph](https://github.com/apache/incubator-s2graph)
* [Scala], [Play 프레임웍], [HBase] 외

---

## HBase Tools

* [HBase 명령행 도구 모음](http://tech.kakao.com/2016/03/24/opensource-4-hbase-tools/)
* [https://github.com/kakao/hbase-tools](https://github.com/kakao/hbase-tools)
* [Clojure], [HBase]

## HBase Region Inspector
* [HBase 리젼 통계 시각화 도구](http://tech.kakao.com/2016/03/11/opensource-3-hri/)
* [https://github.com/kakao/hbase-region-inspector](https://github.com/kakao/hbase-region-inspector)
* [Clojure], [HBase]

## HBase Packet Inspector

* [HBase 리젼서버의 네트워크 패킷을 분석해 요청과 응답 정보를 추출하는 툴](http://tech.kakao.com/2017/09/22/opensource-8-hbase-packet-inspector/)
* [https://github.com/kakao/hbase-packet-inspector](https://github.com/kakao/hbase-packet-inspector)
* [Clojure], [HBase]

---

## CMUX

* [CMUX: CLI에 날개를 달자!](http://tech.kakao.com/2017/07/12/opensource-7-cmux/)
* Cloudera Manager 기반의 하둡 클러스터를 관리하는데 필요한 대화형 커맨드라인 인터페이스 도구들
* [https://github.com/kakao/cmux](https://github.com/kakao/cmux)
* [Cloudera Manager], [TMUX], [FZF], [Ruby]

---

## MRTE(MySQL Realtime Traffic Emulator)

* [MySQL 트래픽 테스트 환경 구축 도구](http://tech.kakao.com/2016/02/16/opensource-2-mtre/)
* 실제 서비스의 MySQL 트래픽을 실시간으로 수집하고
* [https://github.com/kakao/MRTE-Collector](https://github.com/kakao/MRTE-Collector)
* 실제 서비스에서 수집한 데이터를 테스트 환경의 MySQL에서 재현
* [https://github.com/kakao/MRTE-Player](https://github.com/kakao/MRTE-Player)
* [Go], [MySQL]

---

## ADT - Almighty Data Transmitter

* [MySQL 기반 만능 데이터 전송 도구](http://tech.kakao.com/2016/06/27/opensource-5-adt/)
* MySQL 샤드 재분배, 실시간 마이그레이션
* [https://github.com/kakao/adt](https://github.com/kakao/adt)

---

## N2 - approximate Nearest Neighbor

* N차원 벡터에서 최근접 이웃을 빠르게 찾기 위해 Hierarchical Navigable Small World graphs(HNSW) 알고리즘을 구현한 라이브러리
* nmslib 기반으로 개선한 버전으로 사용이 쉽고, 검색속도와 인덱스 생성 시간이 빠름
* [https://github.com/kakao/n2](https://github.com/kakao/n2)
* [HNSW], [NMSLIB], [Annoy], [Go]

---

> [http://github.com/kakao](http://github.com/kakao) 에서 다양한 kakao의 오픈소스 프로젝트를 만나보세요.

[Scala]:http://scala-lang.org
[Play 프레임웍]:https://www.playframework.com
[Clojure]:http://clojure.org
[Go]:https://golang.org
[HBase]:https://hbase.apache.org
[MySQL]:http://www.mysql.com
[GitHub API]:https://developer.github.com/enterprise/2.8/v3/
[Kubernetes]:http://kubernetes.io/docs/
[Docker]:https://docs.docker.com/
[Cloudera Manager]:https://www.cloudera.com/products/product-components/cloudera-manager.html
[TMUX]:https://github.com/tmux/tmux/wiki
[FZF]:https://github.com/junegunn/fzf
[Ruby]:https://www.ruby-lang.org
[HNSW]:https://arxiv.org/abs/1603.09320
[NMSLIB]:https://github.com/searchivarius/NMSLIB
[Annoy]:https://github.com/spotify/annoy
