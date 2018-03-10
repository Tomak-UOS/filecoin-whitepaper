# Filecoin Whitepaper

[Filecoin Whitepaper (Filecoin: A Decentralized Storage Network)](https://filecoin.io/filecoin.pdf)를 읽고 일부분을 번역 및 정리한 저장소입니다.

번역하기 애매하거나, 자연스레 옮기기 힘든 단어들은 영어 단어 그대로 적어놓았습니다.

## Table of Contents

1. Introduction
    1. Elementary Components
    2. Protocol Overview
    3. Paper organization
2. [Definition of a Decentralized Storage Network](./2.\ definition\ of\ a\ decentralized\ storage\ network/README,md#2.\ Definintion\ of\ a\ Decentralized\ Storage\ Network)
    1. Fault tolerance
    2. Properties
3. Proof-of-Replication and Proof-of-Spacetime
    1. Motivation
    2. Proof of Replication
    3. Proof of Spacetime
    4. Practical PoRep and PoSt
    5. Usage in Filecoin
4. Filecoin: a DSN Construction
    1. Setting 
    2. Data Structures
    3. Protocol
    4. Guarantees and Requirements
5. Filecoin Storage and Retrieval Markets
    1. Verifiable Markets
    2. Storage Market
    3. Retrieval Market
6. Useful Work Consensus
    1. Motivation
    2. FilecoinConsensus
7. Smart Contracts
    1. Contractsin Filecoin
    2. Integration with other systems
8. Future Work
    1. On-going Work
    2. Open Questions
    3. Proofs and Formal Verification

## Abstract

 현재 인터넷은 아래와 같은 형태를 보이며 급속도로 발전하는 중이다.

* 중앙 집중형 서비스는 분산된 개방형 서비스로 대체됨.
* 신뢰할 수 있는 단체, 그룹은 검증가능한 연산으로 대체됨.
* brittle한 location address가 융퉁성있는 content address로 대체됨.
* 비효율적인 모놀로식 서비스가 peer-to-peer 알고리즘 마켓으로 대체됨.

 비트코인, 이더리움 등에서 유용성을 입증해오고 있는 분산거래장부, 즉 공개장부를 통해 정교한 smart contract를 처리하고, 수십억불의 암호자산을 처리할 수 있다. 이 시스템들이 참가자들이 중앙 관리자, 신뢰할 수 있는 단체 없이 분산된 네트워크를 구성하여 유료 서비스를 제공하는  첫번째 공개 서비스였다. 또한 [IPFS](https://ipfs.io)는 전세계적인 p2p 네트워크를 통해 content address의 유용성을 웹 자체를 분산화시켜서 보여주었다.

 파일코인은 클라우드 스토리지를 algorithmic market으로 전환시키는 **분산형 스토리지 네트워크**이다. market은 네이티브 프로토콜 토큰(Filecoin이라고 불림)과 함께 블럭체인에서 실행된다. 채굴자들은 클라이언트들에게 스토리지를 제공함으로써 보상을 받는다. 반대로 클라이언트들은 채굴자들의 스토리지를 이용해 데이터를 저장하거나 배포하면서 Filecoin을 사용하게 된다. 비트코인처럼 채굴자들은 상당한 보상으로 블럭을 채굴하기 위해 경쟁한다. 그러나, Filecoin 채굴은 클라이언트에게 직접적으로 유용한 서비스를 제공할 수 있는 active한 스토리지에 비례한다. (블럭체인 컨센서스를 유지하는 것으로 제한되는 비트코인의 유용성과는 다르다) 채굴자들은 많은 스토리지를 모아 클라이언트들에게 제공함으로써 큰 보상을 얻게 된다. Filecoin의 프로토콜은 이 수많은 자원들을 전세계 모든 사람들이 신뢰할 수 있는 self-healing 스토리지 네트워크로 통합시킨다. 네트워크는 자동으로 복제본의 오류들을 감지하고 복구하면서 컨텐츠를 복제하고 분산시키며 견고해진다. 클라이언트는 다른 위험으로부터 보호하기 위해 replication parameter를 선택한다. 프로토콜의 클라우드 스토리지 네트워크는 보안을 제공하는데, 스토리지 제공자가 복호화키에 접근할 수 없도록 컨텐츠가 클라이언트에서 암호화되는 방식이다. Filecoin은 IPFS[^1] 위에서 어떤 데이터던 스토리지 인프라를 제공할 수 있는 보상 계층으로 작동한다. 특히 데이터 분산화, 분산 application의 빌드와 실행, Smart Contract의 구현에 유용하게 쓰일 수 있다.

 이 Whitepaper에서,

1. Filecoin 네트워크를 소개하고, 프로토콜에 대해서 훑어보며, 여러 구성 요소들을 자세히 살펴봅니다.
2. DSN(Decentralized Storage Network) 스키마(Scheme)와 속성을 formalize하고, Filecoin을 DSN으로 구성합니다.
3. 데이터의 복제본을 물리적으로 독립된 스토리지에 저장하는 것을 증명할 수 있는 proof-of-repplication이라 불리는 새로운 형식의 proof-of-storage scheme를 소개합니다.
4. Introduces a novel useful-work consensus based on sequential proofs-of-replication and storage as a measure of power.
5. 검증할 수 있는 market을 formalize하고, 데이터를 Filecoin으로부터 쓰고 읽을 수 있는 방법을 조절하는 두 market, 스토리지 market과 Retrieval market을 구성합니다.
6. 사용 사례, 다른 시스템과의 연결점, 프로토콜 사용법을 논의합니다. 

Note: Filecoin is a work in progress. Active research is under way, and new versions of this paper will appear at [https://filecoin.io](https://filecoin.io). For comments and suggestions, contact us at <research@filecoin.io>.

---

[^1]: <https://ipfs.io>