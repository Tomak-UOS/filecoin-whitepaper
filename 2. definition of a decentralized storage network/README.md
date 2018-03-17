# 2. Definintion of a Decentralized Storage Network

 DSN(Decentralized Storage Network)의 개념을 소개한다. DSN은 독립 스토리지 공급자들이 제공하는 스토리지 통합하고, 자체 조정하여 클라이언트에 데이터 저장과 검색을 제공한다. 조정은 분산화되어, 신뢰할 수 있는 조직, 그룹을 필요로 하지 않는다. 이 시스템의 보안은 개인들이 수행한 작업들을 조정, 검증하는 프로토콜에 의해 이루어진다. DSN은 시스템의 요구사항에 따라 Byzantine Agreement, Gossip Protocol, CRDT를 포함한 다양한 해결법을 조정을 위해 선택할 수 있다. 나중에 4절에서 Filecoin DSN을 위한 구성을 제공한다.

**Definition 2.1.** DSN 스키마 Π는 스토리지 공급자와 클라이언트가 실행하는 프로토콜의 튜플(tuple)이다.

(Put, Get, Manage)

* Put(data) → key: Put 프로토콜을 이용해서 data를 고유 식별자 key로 저장한다.
* Get(key) → data: Get 프로토콜은 현재 key를 사용하여 저장된 data를 검색합니다.
* Manage(): Network of Participants는 사용가능한 스토리지를 제어하고, 공급자가 제공한 서비스들을 검사하고, 수정가능한 오류들을 수정하는 Manage 프로토콜을 통해 조정한다. Manage 프로토콜은 클라이언트와 네트워크 auditor들과 함께 스토리지 공급자들에 의해 실행된다[^1].

 DSN 스키마 Π는 다음절에서 정의되는 관리와 저장소 오류(**management** and **storage fault**)를 허용할 뿐만 아니라  데이터 무결성(integraity)과 검색 가능성(retrievability)을 보장해야 한다.

## 2.1 Fault tolerance

### 2.1.1 Management faults

 우리는 Management fault를 Manage 프로토콜에서 참가자에 의해 일어난 비잔틴 오류로 정의한다. DSN 스키마는 (underlining) Manage 프로토콜의 fault tolerance에 의존한다. Management fault에 대한 fault tolerance 가정에 대한 Violation은 시스템의 동시성과 안정성을 저해한다. 

 예를 들어 Manage protocol이 스토리지 공급자들을 관리하기 위해 Byzantine Agreement(BA)를 필요로 하는 DSN 스키마 Π를 고려해보자. 그러한 프로토콜에서 네트워크는 스토리지 공급자들에게서 proof-of-storage를 받고, BA를 실행시켜 proof-of-storage의 유효성에 동의한다. 만약 BA가 n개의 노드 중 f개까지의 fault를 허용하면, DSN은  f < n/2개의 faulty node를 허용할 수 있다. 이러한 가정이 위반될 경우 검사가 올바르게 이루이지지 않을 것이다.

### 2.1.2 Storage faults

 We define storage faults to be byzantine faults that prevent clients from retrieving the data: i.e. StorageMiners lose their pieces, Retrieval Miners stop serving pieces. A successful Put execution is (f,m)-tolerantif it results in its input data being stored in m independent storage providers (out of n total) and it cantolerate up to f byzantine providers. The parameters f and m depend on protocol implementation; protocoldesigners can fix f and m or leave the choice to the user, extending Put(data) into Put(data, f, m). A Getexecution on stored data is successful if there are fewer than f faulty storage providers.

 For example, consider a simple scheme, where the Put protocol is designed such that each storage providerstores all of the data. In this scheme m = n and f = m−1. Is it always f = m−1? No, some schemes canbe designed using erasure coding, where each storage providers store a special portion of the data, such thatx out of m storage providers are required to retrieve the data; in this case f = m − x.

## 2.2 Properties

We describe the two required properties for a DSN scheme and then present additional properties requiredby the Filecoin DSN.

### 2.2.1 Data Integrity

This property requires that no bounded adversary A can convince clients to accept altered or falsified dataat the end of a Get execution.

**Definition 2.2.** A DSN scheme Π provides data integrity if: for any successful Put execution for some datad under key k, there is no computationally-bounded adversary A that can convince a client to accept d′, ford′ ̸= d at the end of a Get execution for identifier k.

### 2.2.2 Retrievability

This property captures the requirement that, given our fault-tolerance assumptions of Π, if some data hasbeen successfully stored in Π and storage providers continue to follow the protocol, then clients can eventuallyretrieve the data.

**Definition 2.3.** A DSN scheme Π provides retrievability if: for any successful Put execution for data underkey, there exists a successful Get execution for key for which a client retrieves data.2.

### 2.2.3 Other Properties

DSNs can provide other properties specific to their application. We define three key properties required bythe Filecoin DSN: public verifiability, auditability, and incentive-compatibility.

**Definition 2.4.** A DSN scheme Π is publicly verifiable if: for each successful Put, the network of storageproviders can generate a proof that the data is currently being stored. The Proof-of-Storage must convinceany efficient verifier, which only knows key and does not have access to data.

**Definition 2.5.** A DSN scheme Π is auditable, if it generates a verifiable trace of operation that can bechecked in the future to confirm storage was indeed stored for the right duration of time.

**Definition 2.6.** A DSN scheme Π is incentive-compatible, if: storage providers are rewarded for successfullyoffering storage and retrieval service, or penalized for misbehaving, such that the storage providers’ dominantstrategy is to store data.

---

[^1]: In the case where the Manage protocol relies on a blockchain, we consider the miners as auditors, since they verify and coordinate storage providers
