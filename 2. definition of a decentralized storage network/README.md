# 2. Definintion of a Decentralized Storage Network

 We introduce the notion of a Decentralized Storage Network (DSN) scheme. DSNs aggregate storage offeredby multiple independent storage providers and self-coordinate to provide data storage and data retrieval toclients. Coordination is decentralized and does not require trusted parties: the secure operation of thesessystems is achieved through protocols that coordinate and verify operations carried out by individual parties.DSNs can employ different strategies for coordination, including Byzantine Agreement, gossip protocols, orCRDTs, depending on the requirements of the system. Later, in Section 4, we provide a construction forthe Filecoin DSN.

**Definition 2.1.** A DSN scheme Π is a tuple of protocols run by storage providers and clients:

(Put, Get, Manage)

* Put(data) → key: Clients execute the Put protocol to store data under a unique identifier key.
* Get(key) → data: Clients execute the Get protocol to retrieve data that is currently stored using key.
* Manage(): The network of participants coordinates via the Manage protocol to: control the availablestorage, audit the service offered by providers and repair possible faults. The Manage protocol is runby storage providers often in conjunction with clients or a network of auditors[^1].

 A DSN scheme Π must guarantee data integrity and retrievability as well as tolerate management and storagefaults defined in the following sections.

## 2.1 Fault tolerance

### 2.1.1 Management faults

 We define management faults to be byzantine faults caused by participants in the Manage protocol. A DSNscheme relies on the fault tolerance of its underlining Manage protocol. Violations on the faults toleranceassumptions for management faults can compromise liveness and safety of the system.

 For example, consider a DSN scheme Π, where the Manage protocol requires Byzantine Agreement (BA)to audit storage providers. In such protocol, the network receives proofs of storage from storage providersand runs BA to agree on the validity of these proofs. If the BA tolerates up to f faults out of n totalnodes, then our DSN can tolerate f < n/2 faulty nodes. On violations of these assumptions, audits can becompromised.

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

[^1]: In the case where the Manage protocol relies on a blockchain, we consider the miners as auditors, since they verify andcoordinate storage providers
