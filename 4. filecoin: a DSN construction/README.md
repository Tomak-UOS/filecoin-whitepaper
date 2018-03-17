# 4 Filecoin: a DSN Construction

The Filecoin DSN is a decentralized storage network that is auditable, publicly verifiable and designed on incentives. Clients pay a network of miners for data storage and retrieval; miners offer disk space and bandwidth in exchange of payments. Miners receive their payments only if the network can audit that their service was correctly provided.

In this section, we present the Filecoin DSN construction, based on the DSN definition and Proof-of-Spacetime.

## 4.1 Setting

### 4.1.1 Participants

Any user can participate as a Client, a Storage Miner, and/or a Retrieval Miner.

* Clients pay to store data and to retrieve data in the DSN, via Put and Get requests.
* Storage Miners provide data storage to the network. Storage Miners participate in Filecoin by offeringtheir disk space and serving Put requests. To become Storage Miners, users must pledge their storageby depositing collateral proportional to it. Storage Miners respond to Put requests by committing tostore the client’s data for a specified time. Storage Miners generate Proofs-of-Spacetime and submitthem to the blockchain to prove to the Network that they are storing the data through time. In caseof invalid or missing proofs, Storage Miners are penalized and loose part of their collateral. StorageMiners are also eligible to mine new blocks, and in doing so they hence receive the mining reward forcreating a block and transaction fees for the transactions included in the block.
* Retrieval Miners provide data retrieval to the Network. Retrieval Miners participate in Filecoin byserving data that users request via Get. Unlike Storage Miners, they are not required to pledge,commit to store data, or provide proofs of storage. It is natural for Storage Miners to also participateas Retrieval Miners. Retrieval Miners can obtain pieces directly from clients, or from the RetrievalMarket.

### 4.1.2 The Network, N

We personify all the users that run Filecoin full nodes as one single abstract entity: The Network. TheNetwork acts as an intermediary that runs the Manage protocol; informally, at every new block in theFilecoin blockchain, full nodes manage the available storage, validate pledges, audit the storage proofs, andrepair possible faults.

### 4.1.3 The Ledger

Our protocol is applied on top of a ledger-based currency; for generality we refer to this as the Ledger, L. At any given time t (referred to as epoch), all users have access to Lt, the ledger at epoch t, which is a sequenceof transactions. The ledger is append-only3. The Filecoin DSN protocol can be implemented on any ledgerthat allows for the verification of Filecoin’s proofs; we show how we can construct a ledger based on usefulwork in Section 6.

### 4.1.4 The Markets

Demand and supply of storage meet at the two Filecoin Markets: Storage Market and Retrieval Market. The markets are two decentralized exchanges and are explained in detail in Section 5. In brief, clients and miners set the prices for the services they request or provide by submitting orders to the respective markets. The exchanges provide a way for clients and miners to see matching offers and initiate deals. By running the Manage protocol, the Network guarantees that miners are rewarded and clients are charged if the service requested has been successfully provided.

### 4.2 Data Structures

**Pieces.** A piece is some part of data that a client is storing in the DSN. For example, data can be deliber-ately divided into many pieces and each piece can be stored by a different set of Storage Miners.

**Sectors.** A sector is some disk space that a Storage Miner provides to the network. Miners store piecesfrom clients in their sectors and earn tokens for their services. In order to store pieces, Storage Miners mustpledge their sectors to the network.

**AllocationTable.** The AllocTable is a data structure that keeps track of pieces and their assigned sectors. The AllocTable is updated at every block in the ledger and its Merkle root is stored in the latest block. Inpractice, the table is used to keep the state of the DSN, allowing for quick look-ups during proof verification.For more details, see Figure 5.

**Orders.** An order is a statement of intent to request or offer a service. Clients submit bid orders to the markets to request a service (resp. Storage Market for storing data and Retrieval Market for retrieving data)and Miners submit ask orders to offer a service. The order data structures are shown in Figure 10. TheMarket Protocols are detailed in Section 5.

**Orderbook.** Orderbooks are sets of orders. See the Storage Market orderbook in Section 5.2.2 and Retrieval Market orderbook in Section 5.3.2 for details.

**Pledge.** A pledge is a commitment to offer storage (specifically a sector) to the network. Storage Miners must submit their pledge to the ledger in order to start accepting orders in the Storage Market. A pledge consists of the size of the pledged sector and the collateral deposited by the Storage Miner (see Figure 5 for more details).

### 4.3 Protocol

In this Section, we give an overview of the Filecoin DSN by describing the operations performed by the clients, the Network and the miners. We present the methods of the Get and the Put protocol in Figure 7 and the Manage protocol in Figure 8. An example protocol execution is shown in Figure 6. The overall Filecoin Protocol is presented in Figure 1.

### 4.3.1 Client Cycle

We give a brief overview of the client cycle; an in-depth explanation of the following protocols is given in Section 5.