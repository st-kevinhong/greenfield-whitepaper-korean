# 파트 1 BNB 그린필드(Greenfield) 디자인 원칙 및 탈중앙화 저장소 경제

## 목차

- [1 디자인 원칙](#1-design-principles)
- [2 전제](#2-assumptions)
- [3 전반적인 아키텍처](#3-the-architecture-in-general)
  - [3.1 그린필드 코어](#31-greenfield-core)
  - [3.2 BNB 그린필드 dApps](#32-bnb-greenfield-dapps)
  - [3.3 BSC와 크로스 체인](#33-the-cross-chain-with-bsc)
  - [3.4 삼위일체](#34-the-trinity)
- [4 BNB Greenfield Core](#4-bnb-greenfield-core)
  - [4.1 The BNB Greenfield Blockchain](#41-the-bnb-greenfield-blockchain)
  - [4.2 The Storage Providers, SPs](#42-the-storage-providers-sps)
  - [4.3 The Pair Synergy](#43-the-pair-synergy)
- [5 The Greenfield Data Storage](#5-the-greenfield-data-storage)
  - [5.1 Data with Consensus](#51-data-with-consensus)
    - [5.1.1 Accounts and Balance](#511-accounts-and-balance)
    - [5.1.2 Validator and SP Metadata](#512-validator-and-sp-metadata)
    - [5.1.3 Storage Metadata](#513-storage-metadata)
    - [5.1.4 Permission Metadata](#514-permission-metadata)
    - [5.1.5 Billing Metadata](#515-billing-metadata)
  - [5.2 Off-Chain Payload Object Data Storage](#52-off-chain-payload-object-data-storage)
    - [5.2.1 Primary and Secondary SPs](#521-primary-and-secondary-sps)
    - [5.2.2 Data Redundancy](#522-data-redundancy)
- [6 Storage Economics and Its Primitives](#6-storage-economics-and-its-primitives)
  - [6.1 Account Creation](#61-account-creation)
  - [6.2 Data Object Creation](#62-data-object-creation)
  - [6.3 Data Storage](#63-data-storage)
  - [6.4 Data Read and Download](#64-data-read-and-download)
  - [6.5 Permissions and Group](#65-permissions-and-group)
  - [6.6 Fees and Payments](#66-fees-and-payments)
  - [6.7 Data Integrity and Availability Challenge](#67-data-integrity-and-availability-challenge)
  - [6.8 Data Delete](#68-data-delete)
- [7 Economy of Data Assets](#7-economy-of-data-assets)
  - [7.1 Cross-Chain with BSC](#71-cross-chain-with-bsc)
  - [7.2 Framework](#72-framework)
  - [7.3 Communication Layer](#73-communication-layer)
  - [7.4 Resource Mirror Layer](#74-resource-mirror-layer)
    - [7.4.1 Resource Entity Mirror](#741-resource-entity-mirror)
    - [7.4.2 Cross-Chain Operating Primitives](#742-cross-chain-operating-primitives)
- [8 "Not" Ending for the Design](#8-not-ending-for-the-design)
  - [8.1 Acknowledgement](#81-acknowledgement)

파트 1은 BNB 그린필드(Greenfield)를 디자인 할 때 사용된 기본적인 개념들과 고려사항들에 대하여 설명합니다.
해당 부분에서는 구조적 및 기능적인 분석에 관하여 다룹니다.
실제 모델의 혁신은 BSC와 크로스 체인을 하는 데에 있지만, 고유의 저장소 기반들도 중요하게 다뤄야 할 부분 중 하나입니다.

## 1 디자인 원칙

1. **간결함** - 간결함은 다른 전제조건보다도 우선해야 합니다. 간단한 솔루션들은 적용,운영, 유지, 확징이 쉬운 것만이 아니라
   디자인의 주 목표인 소프트웨어 성능에도 우호적입니다. 예를 들어 [파일코인](https://filecoin.io/filecoin.pdf)에서 사용하는 같이 높은 컴퓨팅 증명 방식으로의 방식은 원칙에 의해 채용되지 않았습니다.
   
2. **업그레이드 가능 및 지속적인 진화** - 처음부터 완벽한 시스템을 구현하는 것은 디자인의 목표가 아닙니다.
3. 우리는 전체 아키텍처, 여러 구성요소, 심지어 이 백서도 커뮤니티 및 시장의 피드백과 향후 기술 개발로 인해 진화할 것을 예상합니다.
   해당 인프라는 시간이 지남에 따라 개발 및 업그레이드를 할 수 있도록 "충분히" 구축도어야 합니다.

3. **오픈 플랫폼** - 암호화폐 산업과 BNB 생태계에서 배운 가장 큰 교훈은 커뮤니티가 자체적인 방식으로 더 많은 애플리케이션과 인프라를 구축할 수 있는 최고의 인재와 역량을 보유하고 있다는 것입니다. 따라서 설계 시 핵심 플랫폼과 충분한 인터페이스, 도구 및 기타 개발자 커뮤니티에게 편의를 제공하는 기술적 기반을 제공하는데에 초점을 맞춰야 합니다.

4. **대중 수용** - 해당 생태계는 기존 BNB 고객들을 넘어 전통적인 웹2 고객 및 개발자들을 목표로 합니다. 시스템 디자인은 주요 웹2 및 웹3 표준과 최대한 호환되어야 합니다.

5. **탈중앙화는 여행입니다** - 저장소 시스템을 예시로 들면, 탈중앙화 정도에 양 극단이 존재합니다.
   한 끝에서는 사용자들이 하나의 서비스 제공자를 통해서만 데이터를 생성하고 저장할 수 있습니다.
   다른 한 쪽에서 사용자는 모든 가정의 컴퓨팅 단말기를 통해 데이터를 생성하고 저장할 수 있습니다 (데스크톱이 아니어도 됩니다).
   2안의 디자인은 처음부터 강제로 적용되지 않을 것입니다. 
   우선은 탈중앙화를 증가시키는 해결책 중 가장 간단한 것을 선택하며, 시간이 지날 수록 단계적으로 발전해 나갈 것입니다.
   이에따라 첫 단계인 그린필드(Greenfield)는 데이터를 직접 소유하기에 언제나 적은 비용으로 서비스 공급자들을 선택할 수 있도록 만들었습니다.

<div align="center"><img src="./assets/1%20Decentralization%20Spectrum.png" height="80%" width="80%"></div>
<div align="center"><i>Figure 1.1: Decentralization Spectrum</i></div>

## 2 전제

해당 디자인의 최대 전제는:

***그린필드는 경제적으로 지속 가능하며, 서비스 지향적인 생태계입니다.***

**자체적으로 지속 가능**하다는 것은, 서비스 공급자들 및 대응되는 서비스 소비자들이 합리적이란 것을 의미합니다.
제공자들과 블록체인 검증자들은 제공하는 서비스에 대하여 적절한 금액을 받는 반면, 사용자들은 그들이 사용하는 서비스에 대해 기꺼이 지불합니다.

**서비스 지향적**이라는 것은, 생태계의 사용자들에게 서비스를 제공하면서 그린필드의 가치가 생성되었음을 의미합니다.
그린필드 그 자체로 내재된 가치가 없습니다.

The implicit assumption underlying these two traits is that the majority
of the providers and blockchain validators are reasonable entities and
individuals. They will not do evil given the profit they earn is larger
than the fortune they can plunder. 이 두 가지 특성의 기초가 되는 암묵적인 가정은 대다수가
제공자와 블록체인 검증자의 합리적인 실체이며 개인들. 그들이 버는 이익이 더 크기 때문에 그들은 나쁜 짓을 하지 않을 것이다
그들이 약탈할 수 있는 재산보다 더 많다.

This is a self-justifiable trust for the whole ecosystem to exist: if a
substantial percentage of providers and blockchain validators do evil
and the ecosystem cannot heal itself by ruling out these malicious
players, the whole ecosystem will not be used and have no value to
existing. If that happens, nobody wins even for a short time.

다음과 같이 내장된 밑음이 전제되면, 아래 부분에서 설명하는대로 설계가 단순화 되었습니다.

Another assumption is that both the service provider and consumer sides
would expect the actual "service contracts" between the two to allow
limited liability and provide exit options, even if the contracts are
carried out mostly by code. In consumers' interest, they do not want to
pay a large size of fund upfront and would like to choose a better
provider within the ecosystems or even outside whenever they want. On
the other side, in the service providers' interest, they do not want to
become a data wasteyard or help circulate any content against their own
principles.

지불, 데이터 가용성 확인 및 여러 주요 기능들은 해당 전제를 기반으로 설계되었습니다.

마지막 큰 전제는 데이터는 가치를 지니며, 사용자들은 스마트 컨트랙트 자동화, 보안 및 투명성을 바탕으로 가치를 추출할 것을 전제로 합니다.
이는 BNB 그린필드와 BNB 스마트 체인(BSC) 간 크로스 체인을 구현하는데 상당한 설계를 요구하였습니다.
이는 가장 중요한 전제 조건이며 바라건대 진실에 가깝습니다.

## 3 전반적인 아키텍처

<div align="center"><img src="./assets/3%20Greenfield%20Economy%20General%20Architecture.png"></div>
<div align="center"><i>Figure 3.1: Greenfield Economy General Architecture</i></div>

그린필드의 생태계는 위 그림과 같이 '삼위일체'입니다.

### 3.1 그린필드 코어

BNB 그린필드 코어는 해당 문서에 자세히 설명된 새로운 시스템 인프라를 의미합니다.
이는 새로운 데이터 생태계의 중심이며, 두 개의 층으로 이루어져 있습니다.

1. 새로운 저장소 기반 블록체인, 및

2. "저장소 공급자"로 이뤄진 네트워크

The BNB Greenfield blockchain maintains the ledger for the users and the
storage metadata as the common blockchain state data. It has BNB,
transferred from BNB Smart Chain, as its native token for gas and
governance. BNB Greenfield blockchain also has its own staking logic for
governance.

Storage Providers (SP) are storage service infrastructures that
organizations or individuals provide and the corresponding roles they
play. They use Greenfield as the ledger and the single source of truth.
Each SP can and will respond to users' requests to write (upload) and
read (download) data, and serve as the gatekeeper for user rights and
authentications.

Initially, a number of validators, run either by the BNB community or
SPs, go through the genesis to launch BNB Greenfield, while a few SPs
will also launch the corresponding storage infrastructure and register
themselves onto the Greenfield blockchain. SPs form another P2P network
to provide the full feature set to applications and users to create,
store, read, and trade data while using Greenfield blockchain as the
metadata and ledger layer.

<div align="center"><img src="./assets/3.2%20BNB%20Greenfield%20Core.jpg"></div>
<div align="center"><i>Figure 3.2: BNB Greenfield Core</i></div>

BNB Greenfield blockchain and the SPs together comprise the center of
this new economy, which is actually a decentralized, object storage
system (with EVM connectivity as explained later).

In contrast to the centralized alternative, this object storage system
manages its data in two collaborative parts, on-chain, and off-chain.

The Greenfield blockchain contains two categories of states "on-chain":

1. 계정 및 BNB 잔고 원장

2. 객체 스토리지 시스템 및 메타데이터 The metadata of the object storage system and SPs, the metadata of the objects stored on this storage system, and the
   permission and billing information associated with this storage system.

Greenfield blockchain transactions can change the above states. These
states and the transactions comprise the major economic data of BNB
Greenfield.

While the metadata is stored on-chain, the object storage system stores
all the object content data (the payload) off-chain, more precisely, on
SPs' off-chain systems in a decentralized and redundant way.

When users want to create and use the data on Greenfield, they may
interact with the BNB Greenfield Core Infrastructure via BNB Greenfield
dApps (decentralized applications).

### 3.2 BNB 그린필드 디앱

BNB 그린필드 디앱은 새로운 방식의 탈중앙화 어플리케이션입니다. They
can be the client toolings that facilitate users to interact with the
decentralized storage system, Greenfield Core Infra; or applications
that bring real values to users' real life by using Greenfield systems
as their infrastructure. These applications will use blockchain
addresses as user identifiers and interact with features and smart
contracts on the Greenfield blockchain, Greenfield SPs, and BSC.

There are data endpoints, transaction interfaces, P2P networks, and
corresponding SDKs to help developers to build BNB Greenfield dApps.

BNB 그린필드 디앱은  should be part of the establishment of BNB Chain
infrastructure built mostly by the community and ecosystem partners.
They can be decentralized or centralized as they prefer.

파트 2는 그린필드 디앱의 예시들을 통해 고려 사항에 관하여 보여줍니다.

### 3.3 BSC와 크로스체인

BSC와 BNB 그린필드 블록체인 사이에는 네이티브 크로스 체인 브릿지가 존재합니다. 데이터는 그린필드 코어 인프라에서 더 저렴하게 생성하고 읽을 수 있지만,
관련 데이터 처리는 BSC로 전송되어 스마트 컨트랙트와 
Greenfield Core Infra, the relevant data operation can be transferred to
BSC and integrated with smart contract systems there, such as DeFi, to
create new business models.

### 3.4 삼위일체

From the viewpoint of BNB Greenfield dApps, these applications can help
users to create, read, and execute data on the BNB Greenfield,
Greenfield SPs, and BSC, and serve a purpose to users' needs.

From the viewpoint of BNB Greenfield Core Infrastructure, they accept
requests and observations from the Greenfield dApps on behalf of the
users, and also instructions from BSC to operate together for different
business scenarios.

From the viewpoint of BSC, they can accept transferred data assets from
BNB Greenfield, and provide more business scenarios via smart contracts
to new types of Greenfield dApps.

The users can interact with all parts of the trinity for different
purposes directly or/and indirectly.

## 4 BNB Greenfield Core

### 4.1 The BNB Greenfield Blockchain

BNB Greenfield blockchain uses Proof-of-Stake based on
Tendermint-consensus for its own network security. Blocks are created
every 2 seconds on the Greenfield chain by a group of validators.

BNB will be the gas and governance token on this blockchain. There is a
native cross-chain bridge between the Greenfield blockchain and BSC. The
initial BNB will be locked on BSC and re-minted on Greenfield. BNB and
data operation primitives can flow between Greenfield and BSC.

Total circulation of BNB will stay unchanged as it is now but flow among
BNB Beacon Chain, BSC, and Greenfield.

The validator election and governance are based on a proposal-vote
mechanism, which is revised based on Cosmos SDK's governance module:
anyone can create and propose to become a validator, and the election
into the active set will be based on the stake ranking (initially new
validators may request the existing validator set's votes to be
qualified for election). As validators will host all the critical
metadata and respond to all data operation transactions, they should run
professionally in terms of performance and stability.

To facilitate cross-chain operation and convenient asset management, the
address format of the Greenfield blockchain will be fully compatible
with BSC (and Ethereum). It also accepts EIP712 transaction signing and
verification. These enable the existing wallet infrastructure to
interact with Greenfield at the beginning naturally.

### 4.2 The Storage Providers, SPs

SPs play a different role from Greenfield validators, although the same
organizations or individuals can run both SPs and validators if they
follow all the rules and procedures to get elected.

SPs store the objects' real data, i.e. the payload data. Each SP runs its own object storage system. Similar to Amazon
S3 and other object store systems, the objects stored on SPs are immutable. The users may delete and re-create the
object (under the different ID, or under the same ID after certain publicly declared settings), but they cannot modify
it.

SPs have to register themselves first by depositing on the Greenfield
blockchain as their "Service Stake". Greenfield validators will go
through a dedicated governance procedure to vote for the SPs of their
election. SPs are encouraged to advertise their information and prove to
the community their capability, as SPs have to provide a professional
storage system with high-quality SLA.

SPs provide publicly accessible APIs for users to upload, download, and
manage data. These APIs are very similar to Amazon S3 APIs so that
existing developers may feel familiar enough to write code for it.
Meanwhile, they provide each other REST APIs and form another
white-listed P2P network to communicate with each other to ensure data
availability and redundancy. There will also be a P2P-based
upload/download network across SPs and user-end client software to
facilitate easy connections and fast data download, which is similar to
BitTorrent.

More SPs are welcome to ensure decentralization and data redundancy. But
too many SPs may make SPs unable to make enough to sustain the business.
Greenfield has an incentive design to make a proper number of SPs
available. While SPs can choose to stay with the network or leave.

When the SPs join and leave the network, they have to follow a series of
actions to ensure data redundancy for the users; otherwise, their
"Service Stake" will be fined. This is achieved through the data
availability challenge (described in detail in Part 3) and validator
governance votes.

### 4.3 The Pair Synergy

Greenfield validators and SPs form a pair synergy to provide the whole
storage service. Validators store the metadata and financial ledger with
consensus, while SPs provide real data storage and downloading.
Validators have the motivation to ensure enough good SPs to provide
decent service, while users and SPs rely on a stable and decentralized
Greenfield blockchain as a single source of truth on metadata.

## 5 The Greenfield Data Storage

The data stored on Greenfield has two main categories:

1. data with blockchain consensus - They are Greenfield blockchain data.
   All Greenfield validators have such active data in full (at least the latest state).
   Anyone can join the blockchain as full nodes to synchronize these data for free.

2. object payload data - object data for short. This is the data that users store on Greenfield.
   Such data has access control and requires fees to store.
   They are not stored on the blockchain but on enough instances of SPs off-chain.

### 5.1 Data with Consensus

These data are on-chain and can be only changed through transactions
onto the Greenfield blockchain. It has several types as described below.

#### 5.1.1 Accounts and Balance

Each user has their "Owner Address" as the identifier for their owner
account to "own" the data resources. There is another "payment account"
type dedicated to billing and payment purposes and owned by owner
addresses.

Both owner accounts and payment accounts can hold the BNB balance on
Greenfield. Users can deposit BNB from BSC, accept transfers from other
users, and spend them on transaction gas and storage usage.

#### 5.1.2 Validator and SP Metadata

These are the basic information about the Greenfield validators and
Greenfield SPs. SPs may have more information, as it has to publish
their service information for users' data operations. There should be a
reputation mechanism for SPs as well.

#### 5.1.3 Storage Metadata

The "storage metadata" includes size, ownership, checksum hashes, and
distribution location among SPs. Similar to AWS S3, the basic unit of
the storage is an "*object*", which can be a piece of binary data, text
files, photos, videos, or any other format. Users can create their
objects under their "*bucket*". A bucket is globally unique. The object
can be referred to via the bucket name and the object ID. It can also be
located by the bucket name, the prefix tag, and the object ID via
off-chain facilitations.

#### 5.1.4 Permission Metadata

Data resources on Greenfield, such as the data objects and the buckets,
all have access control, such as which address can create, read, list,
or even execute the resources, and which address can grant/revoke these
permissions.

Two other data resources also have access control. One is "Group". A
group represents a group of user addresses that have the same
permissions to the same resources. It can be used in the same way as an
address in the access control. Meanwhile, it requires permission too to
change the group. The other is "payment account". They are created by
the owner accounts.

Here the access control is enforced by the SPs off-chain. People can
test and challenge the SPs if they mess up the control. Slash and reward
will happen to keep the SPs sticking to the principles.

#### 5.1.5 Billing Metadata

Users have to pay fees to store data objects on Greenfield. While each
object enjoys a free quota to download by users who are permitted to,
the excessive download will require extra data packages to be paid for
the bandwidth. Besides the owner address, users can derive multiple
"Payment Addresses" to pay these fees. Objects are stored under buckets,
while each bucket can be associated with these payment addresses, and
the system will charge these accounts for storing and/or downloading.
Many buckets can share the same payment address. Such association
information is also stored on chains with consensus as well.

The payment of Greenfield is on a stream pay model, which will greatly
reduce the complexity to implement the billing logic - more described in
Part 3.

### 5.2 Off-Chain Payload Object Data Storage

The payload data of the object, i.e. the bytes comprising the data
files, photos, and videos, are stored off-chain in multiple SPs with a
data redundancy design. Each SP can have its edition of an object
storage system with a good SLA and high-performance interface to
interact with the users and other SPs.

#### 5.2.1 Primary and Secondary SPs

Among the multiple SPs that one object is stored on, one SP will be the
"Primary SP", while the others are "Secondary SP".

When users want to write an object into Greenfield, they or the client
software they use must specify the primary SP. Primary SP should be used
as the only SP to download the data. Users can change the primary SP for
their objects later if they are not satisfied with their service.

#### 5.2.2 Data Redundancy

After the users issue a "write" request, Primary SP should respond to
the client upload request to accept the user upload, chop the object
data into segments, verify the data integrity and store all the segments.
After that, Primary SP computes a data redundancy solution for these segments
based on Erasure Coding (EC). Then Primary SP or the users will select a
few secondary SPs to store these segment replicas and their EC parity pieces.
This data distribution communication will be done via the p2p network and REST
APIs among SPs.

The data redundancy setup is to ensure that even if the primary SP and a
few secondary SPs become unavailable at a later time, Greenfield can
still recover the full data.

## 6 Storage Economics and Its Primitives

In this section, the underlying economics and the operating primitives
are discussed aligned with the lifecycle of a data object. The below
primitives can be executed after the genesis of the Greenfield
blockchain and enough SPs have registered themselves and started working
properly.

### 6.1 계정 

To write a data object into Greenfield, the users must have an account,
or more specifically, an address on the BNB Greenfield blockchain. This
can be done by transferring some BNB either from other addresses on
Greenfield or from BSC.

This may be a special route that users can skip this step but issue the
"transfer and write" request directly from BSC. As Greenfield and BSC
share the same address protocol, this action de facto creates an account
on Greenfield with the same address on BSC.

### 6.2 Data Object Creation

Users must create "Buckets" before creating objects. Similar to the
"Bucket" concepts in AWS S3, "buckets" here are a data resource to group
data objects from a user. All the objects under the same bucket will be
stored on the same primary SP and downloadable from that SP. Users can
create many buckets and store their data objects in different ones.

Each bucket has a Primary SP associated with it, which means all the
objects under this bucket will use that SP as the Primary SP. If the
chosen Primary SP cannot serve the requests well, the user may choose to
migrate the bucket to another SP completely via an on-chain transaction.

Data object creation is performed in two phases.

1. Request phase:

a. Users establish the connection with the primary SP and send a
"get approval" message to ask if it is willing to store the object; The
primary SP acknowledges the request by signing a message about the
operation and returns it to the client;

b. After the primary SP determines to accept the request to store the data
as the primary SP, the client constructs a "write" transaction message with
the primary SP's signature and the initial object metadata, such as the
object name, the bucket name, size, checksum, while optionally content type
and the storage preference, etc; Users sign the transaction and submit it
to Greenfield chain;

c. Users get the result of the transaction and transaction hash;

d. Greenfield accepts the "create" request and locks fees from users.

2. Seal phase:

a. Users connect to the primary SP and send transaction hash; The primary SP
uses the transaction hash to check if the object metadata is already created
on Greenfield chain;

b. Users start uploading the object payload data to the primary SP if the
object metadata is already created; The primary SP checks if the payload data
matches the object's metadata by comparing the checksum of the object on
Greenfield chain and the checksum of the payload data; If it matches the
primary SP will sign the "uploaded" confirmation to the users;

c. The primary SP syncs with secondary SPs to set up the data redundancy,
and then it signs a "Seal" transaction with the finalized metadata for storage.
If the primary SP determines that it doesn't want to store the file due to
whatever reason, it can also "SealReject" the request.

d. Greenfield processes the "Seal" or "SealReject" transaction to begin the
storage life cycle for the object. Users can still "CancelRequest" to give up
the creation request and get partially refunded.

There are scenarios in which the primary SP doesn't cooperate with the user well: 1. The primary SP acknowledges the
upload request, but doesn't accept the upload in time; 2. the primary SP signs the "uploaded" confirmation but doesn't
seal the transaction in time. Greenfield expects the primary SP to finish the object creation by either "Seal" or
"SealReject" transaction in a predefined time window; otherwise, the primary SP will be punished with a fine. Primary SP
has no rational reasons to not acknowledge the upload request or doesn't seal in time, while the users have no rational
reasons to create the requests but do not upload in time either.

There may be a special case in which the "create" is triggered from BSC
as a cross-chain transaction, the primary SP cannot get requests
directly. The primary SP can observe such requests and perform other
actions in the same way.

### 6.3 Data Storage

Once a data object is "Sealed", the owner of the object has de facto
entered a contract with the SPs for the storage, in which case the
owners should pay fees for such storage and the SPs should guarantee the
data availability.

The data object owners always have the right to change the primary SP to
store their data, after settling the outstanding fees.

The SPs, especially the primary SPs, can also inform people to stop
storing the data, due to either their own opinions or decision. Both
other SPs and the owners can observe corresponding notifications. Other
SPs can voluntarily propose themselves to be successors to store the
data objects on Greenfield if the owners do not react to the
notification in a predefined time, as other SPs do like to take the
business.

### 6.4 Data Read and Download

By design, the bytes that are stored and downloaded later for the object
are exactly the same bytes that were originally uploaded. SPs may use
their encryption logic as they wish, but when the data is being
downloaded, it shows the same bytes as it was uploaded. Users may choose
their own encryption scheme or use the default one provided by the
client software if they want the data to be unrecognizable by SPs or any
others, even though SPs have the obligation to not circulate these data
out of users' instruction.

Data objects can be only read and downloaded by the addresses with
proper read permissions. The Primary SP of the objects is the main
source to download from. If Primary SP is not available, the owner and
Greenfield blockchain validators can challenge (described in Part 3) and
change the object's primary SP to recover the downloading.

Each object has a time-based traffic bandwidth quota, which is provided
free, i.e. nobody needs to pay for downloading a certain amount within a
certain period. It is expected that this quota can satisfy most of the
individual download needs as part of the normal usage conditions.

For extra bandwidth to download the object, someone has to pay for the
data package, which is covered in the payment section.

### 6.5 Permissions and Group

Permission is the main logic introduced in Greenfield to enable
potential business models.

The data resources, including the objects, buckets, payment accounts,
and groups, all have permissions related. These permissions define
whether each account can perform particular actions.

Group is a list of accounts that can be treated in the same way as a
single account.

Examples of permissions are:

- Put, List, Get, Delete, Copy, and Execute data objects;

- Create, Delete, and List buckets

- Create, Delete, ListMembersOf, Leave groups

- Create, Associate payment accounts

- Grant, Revoke the above permissions

These permissions are associated with the data resources and
accounts/groups, and the group definitions are stored on the Greenfield
blockchain publicly. Now they are in plain text. Later a privacy mode
will be introduced based on Zero Knowledge Proof technology.

One thing that makes the permission operation more interesting is that
they can be operated from BSC directly, either through smart contracts
or by an EOA.

### 6.6 수수료 및 지불

<div align="center"><img src="./assets/6.6%20Payment%20Stream%20Flow.png" height="80%" width="80%"></div>
<div align="center"><i>Figure 6.1: Payment Stream Flow</i></div>

그린필드에서 저장소 수수료는 *[Superfluid](https://docs.superfluid.finance/superfluid/protocol-overview/in-depth-overview/super-agreements/constant-flow-agreement-cfa)*와 같은 스팀 지불 스타일로 부과될 것입니다.

The fees are paid on Greenfield in the style of "Stream" from users to
receiver accounts at a constant rate. The fees are "charged" every
second as they are used.

그린필드에는 두 종류의 수수료가 존재합니다: 객체 저장 비용 및 데이터 패키지 비용

저장소의 경우 그린필드에 저장된 모든 객체는 사이즈, 복제 수, 기본 가격 비율 및 다른 매개변수로 인해서 결정됩니다.
객체가 저장이 된 후 총 비용은 시간과 기본 가격과 연관이 있습니다.

For data downloading, there is a free, time-based quota for each bucket
of users' objects. If it's exceeded, users can promote their data
package to get more quota. Every data package has a fixed price for the
defined period. Once the data package is picked, the total charge of
downloading will be only related to time and the data package price,
until the data package setting is changed again.

Here there is trust between the users and the SPs for data download. As
the extra downloading bandwidth will charge a fee and the download
journal is not fully stored on the Greenfield blockchain. SPs should
provide an endpoint interface for users to query the download billing
details with detailed logs and downloaders' signatures. If the users and
the SPs cannot agree on the bill, users may just select another Primary
SP.

By default, the object owner's address will be used to pay for the
objects it owns. But users can also create multiple "payment accounts"
and associate objects to different payment accounts to pay for storage
and bandwidth.

The address format of the payment account is the same as normal
accounts. It's derived by the hash of the user address and payment
account index. However, the payment accounts are actually only logical
ones and only exist in the storage payment module. Users can deposit
into, withdraw from and query the balance of payment accounts on the
Greenfield blockchain, but users cannot use payment accounts to perform
staking or other on-chain transactions. Payment accounts can be set as
"non-refundable". Users cannot withdraw funds from such payment
accounts.

Other users can also sponsor the payment by donating a "stream payment"
flow to selected "non-refundable" payment accounts. They can pause at
any time they want.

All the storage fees and data packages are priced and paid in BNB. There
are system parameters and price oracles from the Greenfield Relayers
(described below) to adjust the pricing based on the marketing
situation.

Once the payment accounts run out of BNB, the objects associated with
these payment accounts will suffer from a downgraded service of
downloading, i.e. the download speed and connection numbers will be
limited. Once the fund is transferred to the payment accounts, the
service quality can be resumed right away. If the service is not resumed
for a long time, it is the SPs' discretionary decision to clear the data
out, in a similar way to how SPs claim to stop services to certain
objects. In such a case, the data may be gone from Greenfield
completely.

### 6.7 데이터 무결성 및 가용성 도전

데이터 무결성, 가용성 및 중복에 관해서는 아래와 같이 3가지 측면이 있습니다:

1. 최초 서비스 제공자는 사용자가 올린 옯바른 객체를 저장합니다.

2. 서비스 제공자는 지정된 데이터 조각들을 1차 혹은 2차 서비스 제공자로 저장합니다. 저장된 조각들은 분실하거나, 훼손되거나, 위조되면 안됩니다.

3. 2차 서비스 제공자에 저장된 Erasure 코딩 조각은 1차 서비스 제공자에 저장된 원조 객체를 복구할 수 있습니다.

데이터 무결성과 중복성은 먼저 개체의 체크섬 및 중복성 설정에 의해 보호되어야 합니다.
이들은 데이터 객체 메타데이터의 일부로, 객체 생성 시 서비스 제공자와 사용자에 의해 검증을 받아야 합니다.
해당 메타데이터는 그린필드 블록체인에도 저장될 것입니다.

특히 위에 2번 때문에 그린필드와 서비스 제공자들은 함께 협력하여 데이터 무결성과 가용성을 유지해야 합니다.
다른 탈중앙화 저장소 시스템과 달리, "Proof-of-Challenge(도전 증명)"이란 개념을 통해 사용자들이 약속된 대로 데이터를 보관하고 있음을 알 수 있습니다.

도전자는 다른 이해관계자들로부터 올 수 있습니다. 첫째로, 사용자는 도전 트랜잭션을 접수합니다.
그 다음, 사용자와 비슷하게 서비스 제공자는 다른 서비스 제공자에게 도전 트랜잭션을 접수할 수 있습니다.
마지막으로, 그린필드 블록체인은 임의로 내부 도전 이벤트를 발행할 것입니다.

도전은 그린필드 트랜잭션이나 블록 생성 끝에 내부 이벤트로 신청할 수 있습니다.
그린필드 블록체인 검증인들이 해당 첼린지를 조회하면, 이들은 표준 오프 체인 상 체크를 서비스 제공자의 데이터와 비교합니다. 
해당 검증인들은 These validators will vote for the
challenge results via an aggregated multisig via an off-chain P2P
network and submit them to the Greenfield blockchain. The failed result
for a challenge will slash the corresponding SPs. The submitter and
validators will get rewards for such challenges.

도전을 실패한 데이터는 서비스 제공자가 회복할 때까지 정해 시간동안 재도전이 불가능합니다.

파트 3의 또 다른 섹션에서 데이터 가용성 문제에 관한 내용을 더 자세히 다룰 예정입니다.

### 6.8 데어터 삭제

사용자들은 자신의 데이터 객체들을 지우도록 요청할 수 있습니다. 그린필드는 블록체인 상태에서 메타 데이터를 삭제하며,
초기 서비스 제공자는 해당 요청에 응답하여 복제 데이터와 관련 세그멘트들을 해제합니다. 
지불 스트림이 향후 삭제를 권장하기 위해 보상 리베이트와 함께 종료됩니다.

## 7 데이터 자산의 경제

실제 그린필드 경제의 힘은 데이터를 저장하기 위한 플랫폼에만 있는 것이 아닌, 데이터 자산과 연관된 경제 체제에 가치를 더하는 것을 돕는데에 있습니다.

데이터의 자산적 특성을 권한에 의해 결정됩니다. 데이터를 읽는 권한을 부여하는 것이 그 예시입니다. 
권한이 데이터 자체로 부터 독립될 때, 이는 교환 가능한 자산이 되며 데이터의 가치를 극대화합니다.
이는 데이터 자체가 실행 가능할 때(새로운 종류의 스마트 코드)나, 서로 상호작용하거나, 새로운 데이터를 생성할 때 증폭됩니다.
이를 통해 새롭고, 데이터 직약적이면서 신뢰가 필요없는 컴퓨팅 환경을 구축하는 것을 상상할 수 있습니다.

또한 데이터 권한은 크로스 체인을 통해 BSC로 전송되며, 디지털 자산이 될 수 있습니다. 
이를 통해 기존 BSC 상에 존재한 DeFi 프로토콜이나 모델들에 해당 자산들과 함께 적용될 수 있는 다양한 가능성을 제시합니다.

데이터 권한은 그린필드 블록체인 계정과 같은 주소 체계를 갖고, 데이터 객체들을 소유하며, 다양한 권한들을 상속할 수 있는 BSC의 스마트 컨트랙트들로 인해 더욱 향상됩니다. 
이런 특징을 통해 데이터와 해당 작업들은 수 많은 새로운 비즈니스 기회들을 제공할 것입니다.

### 7.1 BSC와 크로스 체인

크로스 체인 모델은 다음과 같은 목표를 달성할 것으로 기대하고 있습니다:

- 기존 시스템과 통합 가능: 최대한 현재 인프라와 디앱들을 활용합니다. NFT 마켓플레이스나, 데이터 인덱싱, 블록체인 탐색기 등이 그 예시입니다.

- 프로그래밍 가능: dApp들은 그린필드에서 자산을 어떻게 래핑할지 정의할 수 있습니다.
- 
- 안전하고 복구 가능.

네이티브 크로스 체인 브릿지는 그린필드 검증인들에 의해 유지되고 보호됩니다. 
이 부분은 집계된 다중서명 체계를 기반으로 한 새로운 릴레이어 시스템에 의해 만들어졌습니다(앞으로 세션들에서 자세히 다룰 예정입니다).
그린필드 검증인들은 더 넓은 대역폭과 빠른 브릿지를 촉진하기 위해 릴레이어들을 운영합니다.

첫 크로스체인 활동으로 BSC에서 그린필드로 BNB가 전송될 것입니다. 초기에 설정되는 그린필드 최초 검증인 집합은 정해진 개수의 BNB를 BSC상의 "그린필드 토큰 허브" 컨트랙트에 잠깁니다.
이 컨트랙트는 초기 이후에 BNB를 전송하는 네이티브 브릿지의 일부로 사용될 것입니다. 다음과 같이 최초로 잠긴 BNB는 검증인의 자기 지분 예치 및 초기 가스 비용으로 사용됩니다.

### 7.2 프레임워크

<div align="center"><img src="./assets/7.1%20Cross-chain%20Architecture.jpg"  height="80%" width="80%"></div>
<div align="center"><i>Figure 7.1: Cross-chain Architecture</i></div>

크로스 체인의 하부 레이어는 크로스 체인 **커뮤니케이션 레이어**로, 프리미티브(primitive) 통신 패키지를 다루거나 검증하는 역할을 합니다.
중간 레이어는 **리소스 미러**가 적용되었습니다. 그린필드에 정의되었지만 BSC에 미러링되지 않은 리소스 자산들을 책입집니다. 
상위 레이어는 **어플리케이션 레이어**로, BSC에 미러링된 리소스 독립체와 프리미티를 통해 which are the smart
contracts implemented by community developers on BSC to operate the
mirrored resource entities with their primitives; 
그린필드에는 프로그래밍 기능이 없기 때문에 어플리케이션 레이어가 존재하지 않습니다.
실제 디앱은 어플리케이션 레이어에 일부 존재할 것이며, 그린필드 코어 및 보조 인프라와 통신할 것입니다.

비대칭적인 프레임워크로 인해 BSC는 그린필드인 데이터 계층보다 어플리케이션/제어 계층에 주목하였습니다.
상태의 경주를 막기 위해, 다음과 같은 규칙이 제정되었습니다:

- BSC에서 생성을 시작하는 모든 리소스들은 BSC에 의해서만 제어할 수 있습니다.

- BSC가 제어하는 모든 리소스들은 그린필드에 제어 계층에 전송할 수 없습니다.

- 그린필드가 제어하는 모든 리소스는 BSC 상으로 제어 권한을 전송할 수 있습니다.

### 7.3 커뮤니케이션 레이어

커뮤니케이션 레이어는 **그린필드 릴레이어**의 집합으로 이루어져 있습니다 :

- 각 검증인은 릴레이어를 운영해야 합니다. 각 릴레이어는 검증자의 필수 정보의 일부로 체인에 저장된 키의 주소와 함께 BLS 개인 키를 가지고 있다.

- 릴레이어는 BSC와 그린필드 블록체인에서 발생하는 모든 크로스 체인 이벤트를 개별적으로 조회합니다.
  블록 확인이 완결성에 도달했을 때, 릴레이어는 이벤트를 확정하기 위해 BLS키로 메세지를 서명합니다.
  이후 "the vote(투표)"라고 불리는 증명은 p2p 네트워크를 통해 다른 릴레이어들로 전달됩니다.

- 릴레이어에서 충분한 투표가 모였을 때, 릴레이어는 크로스 체인 패키지 트랜잭션을 구성하여 BSC나 그린필드 네트워크에 제출할 것입니다.

### 7.4 리소스 미러 레이어

#### 7.4.1 리소스 독립체 미러 (Resource Entity Mirror)

대부분의 크로스 체인 패키지의 목적은 그린필드 블록체인 상 리소스 독립체의 상태를 변환하기 위한 것입니다.
따라서 다음 리소스 독립체들은 BSC상 미러링 될 수 있습니다:

1. 계정(Account)

2. BNB

3. 버켓(Bucket)

4. 객체(Object)

5. 그룹(Group)

BSC와 그린필드가 같은 주소 체계를 사용하기 때문에 계정 매핑은 자연스러운 것입니다.
양쪽의 같은 주소값은 실제로 같은 주소를 나타냅니다. 해당 부분에는 미러가 필요 없습니다.

BNB는 그린필드 탄생부터 자체적으로 패깅된 토큰입니다. 
BSC상의 스마트 컨트랙트인 "Token Hub" 컨트랙트는 그린필드가 BNB를 주조할 수 없게 만들도 총 유통량을 안전하게 유지할 수 있도록 돕습니다.

버켓, 객체 및 그룹은 BSC에 ERC-721 표준을 개선한 새로운 BEP 형태의 NFT로 미러된다. 
해당 NFT에는 연결된 리소스에 해당되는 메타데이터 정보가 존재합니다.
BSC에 해당 NFT를 소유하고 있다는 것은 그린필드 상 해당 리소스에 대한 소유하고 있다는 것을 의미합니다.
해당 소유권은 그린필드에서 전송할 수 없기에, BSC 상으로 전송되지 않습니다.

#### 7.4.2 크로스 체인 운영 프리미티브

크로스 체인 프리미티브(Primitives)를 통해 dApp에서 해당 객체를 호출하고 실행할 수 있도록 정의되었습니다.

스마트 컨트랙트는 해당 인터페이스 계정을 EOA와 비슷한 방식으로 호출할 수 있습니다.

계정

- BSC 상 지불 계정을 생성

BNB:

- BSC 와 그린필드 계정 사이 양방향으로 전송됩니다
  (지불 계정 포함)

버켓(Bucket):

- BSC 상에서 버켓 생성

- 그린필드에서 BSC로 버켓 미러

객체(Object):

- 그린필드에서 BSC로 객체들 복사

- BSC에서 객체 생성

- BSC에서 계정/그룹의 객체 접근 허용/거절 권한

- BSC에서 객체 복사

- BSC에서 객체 실행

- BSC의 지불 계정과 버켓 연결

그룹(Group):

- 그린필드에서 BSC로 그룹 미러

- BSC에서 그룹 생성

- BSC에서 그룹 멤버 변경

- BSC에서 그룹 탈퇴

해당 요소들이 EOA나 스마트 컨트랙트에서 호출되었을 때, 미리 정의된 이벤트가 호출될 것입니다.
그린필드 릴레이어는 해당 이벤트를 받고 그린필드에서 BSC로 전달할 것입니다.
해당 변화가 비동기적으로 발생하므로,  특정 크로스 체인 패키지에서 승인 혹은 에러로 인해 콜백을 발생시킬 수 있습니다.
프리미티브 호출자는 크로스 체인에 대한 수수료를 미리 지불해야 하며, 잠재적인 콜백에 관해서도 비용을 지불해야 합니다.
더 자세한 사항은 파트 3에서 다룰 예정입니다.

## 8 디자인으로 끝나지 않습니다

자세한 부분들이 파트 1에서 다뤄지지 않았습니다. 파트 3에서 여러 토픽들에 관해 추가되고 확장될 것이지만,
어떤 부분들은 현재 팀에서 전략적으로 고려하기에 너무 멀리 있는 경우가 있습니다.

예를 들어 데이터 객체들의 "실행" 부분입니다. 해당 컨셉은 데이터 중 일부는 실행 가능한 프로그램입니다.
그린필드는 더 투명한 컴퓨팅 환경을 만들 수 있습니다. 그린필드에 저장된 프로그램은 검증되고,
확인 없이 변경될 가능성이 없으며, 그린필드에서 제공한 데이터로만 실행되기 때문에 사용자들이 안심하고 데이터를 사용하거나 제공할 수 있습니다.

위와 같은 기능이나 이외에 새로운 기능들은 BNB 연구 및 분석 후에 향후 BNB 그린필드 개발 로드맵에 포함될 예정입니다.

### 8.1 감사의 말씀

우리는 BNB 그린필드의 기반이 되는 노력 및 아이디어를 제공한 다음 팀들과 커뮤니티에게 감사 인사를 전합니다 (순서 상관 없이 배치하였으며, 완전한 목록이 아닙니다).
BNB 그린필드는 다음과 같은 선구자들의 발견을 토대로 만들어졌습니다.

1. 이더리움

2. 코스모스 SDK

3. Superfluid

4. 아마존 웹 서비스(AWS)

5. MinIO 및 여러 오픈 소스 저장 시스템

6. 파일코인, Arweave, StorJ 및 여러 탈중앙화 저장소 네트워크

7. 비트코인, 및

8. 새로운 웹3 경제를 구축하기 위해 노력하는 모든 사람 및 프로젝트들
