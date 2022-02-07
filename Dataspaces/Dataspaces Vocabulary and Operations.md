# Dataspaces Vocabulary and Operations

## How to read this document
EDC-specific terms and concepts are marked with bold text.

The terms and concepts come from two separate but related sources:
- Business perspective of organizations that use dataspaces for B2B data sharing with their partners and customers.
- EDC’s own choice of architecture and implementation

## Dataspace Activities described in this document
- Establishing a Dataspace
- Joining a Dataspace
- Publishing Data
    - **Assets**, **Policies** and **Contract Definitions**
- Discovering Data
    - Data Contracts
- Sharing Data
    - Establishing trust
    - Contract negotiation and data transfer

## Scenario Assumptions
- Multiple companies are members of a dataspace; they collaborate on sharing their data with each other. They are multi-nationals that need to protect their data across geopolitical boundaries.
- Need a way to advertise available data and usage requirements
- Need a way to enforce data usage requirements
- Need access control since some data may be confidential to a subset of participants 
- Each company must be in control over who they trust
- Need an interoperable way to transfer data across heterogenous clouds
- Need a full audit history

# Establishing a dataspace

- Founding companies create a dataspace entity called a **dataspace authority**
    - Establish rules of participation.
    - Optionally establish a set of third-party **verifier organizations**.
    - Define a common set of **policies** that must be supported by all members.
    - Define a **trust model**. A trust model may be centralized or decentralized (more on this later).
- The **dataspace authority** defines a registration process
    - Web Application where companies can apply for membership
    - Membership applications are certified by a verifier organization or an automated ruleset.
    - On verification, the new member company becomes a **participant**, and an entry is added into the **dataspace member registry**
- The only software systems run by the dataspace authority are:
    - A custom registration web app 
    - A **dataspace member registry** that contains information about the participants and serves as a participant directory
    - **Decentralization** is the default operation mode of a dataspace, however a centralized mode is possible, starting with a central Identity Service, and potentially centralizing other services (Catalog, Observer,...) as well.
- Companies adopt EDC, a set of open-source software components 
    - The EDC **dataspace member registry** is operated by the dataspace authority

# Joining a Dataspace
- The company choses and deploys EDC components
    - **Federated Catalog Service** – advertises data to other **participants** and discovers data made available by other **participants**.
    - **Connector** – orchestrates the sharing and transfer of data in a secure way with other **participants** 
    - The components may be deployed in the cloud, on premises, or in a hybrid model. 
    - The components can also be deployed across multiple clouds (for companies using more than one cloud provider) 
- The member company applies with the *dataspace authority*
    - The company provides its Decentralized Identity (DID), which points to the participants **self-description**, which includes connectivity information
    - On approval, the company is entered in the **dataspace member registry** and a Verifiable Credential (VC) is issued, proving membership in the dataspace

# Roles within a dataspace participant
This list is a non-exhaustive example of the most common roles and might differ from your actual organisational setup.

- Data Officer
    - Responsible for data security and trust
    - Defines policies for access and sharing
    - Monitors and tracks adherence to policies
    - Wants to view data lineage
        -Which data has been shared with other organizations
        -Which data has been consumed by their organization
- Data Owner
    - Acts as the steward of  particular data sets
    - Registers one or multiple data sets as a **Data Asset** in the connector
- Data Consumer
    - Proves membership in the dataspace and adherence to policies
    - Requests and uses a **Data Contract**, which contains **Usage Policies** and the **Data Asset** from another participant
- System Integrator
    - A composite role, responsible for setting up and running EDC components

# Publishing Data Preliminaries
- Data is made available to other **Participants** as a **Data Contract**
- A **Data Contract** always has an **Access Policy**, which defines which **Participants** have access to the contained **Data Asset**.
- A **Data Contract** always has a **Data Usage Policy**, which defines the duties, obligations and rules that apply to the use of the contained **Data Asset**.
In order to access a **Data Contract**, a **Participant** must satisfy both the **Access Policy** and **Usage Policy**.

# Publishing an Asset

- **Assets** are not published “raw”, the are wrapped in **Data Contracts**
    - **Data Contracts** are either **Contract Offers** (query & negotiation) or **Contract Agreements** (sharing)
- All **Data Contracts** have an **Access Policy** and **Usage Policy**
    - If we had to define separate **Access Policy** and **Usage policy** for each **Data Asset** that would be
        - Tedious and error prone
        - A security risk 
        - Difficult for the data officer to set corporate standards
        - Overly complex when defining different policies for the same asset (e.g., for different audiences)
The contract definition solves these issues

## Contract Definition as a template for Data Contracts

A **Contract Definition** specifies
- the **Access Policy** on a **Data Asset** - which **Participants** have access to (e.g., "my partners", "any organization in Europe", "anyone", etc.)
- the **Usage Policy** - duties, rules, and obligations one must follow (e.g., "store data in Europe", "delete the data after 30 days", etc.) 

A **Contract Definition** can be associated with many **Data Assets**
- follows a top-down model
- a **Contract Definition** has an **Asset Selector** (this **Contract Definition** applies to these **Data Assets**)
- A **Data Asset** can be published unter multiple **Contract Definitions** if it needs to be exposed with different **Policies** 

#Publishing Data in a Dataspace

## Step 1: Participant Setup
1. The **Participant** registers their DID with the **Dataspace Authority**, which issues a Verifiable Credential (VC) of membership to the **Participant**
2. The **Participant** or their System Integrator/Operator installs **EDC**, which can be run on-premises or in the cloud. It then configures the deployment with the received VC and the pointer to the **Dataspace Participant Registry**
3. The **Federated Catalog Node (FCN)** and the **Federated Catalog Crawler (FCC)** are configured. (e.g.; catalog storage, scheduling of the crawler)
4. The **FCN** publishes a list of **Data Contracts** that the **Participant** wants to make available to other **Participants** - see next step for details

## Step 2: Publishing Data
1. The data officer defines **Contract Definitions* in EDC
This **Contract Definition** is for all parts (individual data sources) od the **Data Asset** (**Asset Selector**)
Examples:
    - "Can be accessed only by a given member company's partners (**Access POlicy**)
    - "Must be stored in Europe and used only for maintenance purposes (**Usage Policy**"
2. The data owner creates a **Data Asset Entry** in EDC
    - The **Data Asset Entry** is not the actual data source, rather it points to where the **Data Asset** is stored (e.g., Object Storage)
    - The EDC automatically "associates" the **Data Asset** with the **Contract Definition** in the system
3. The **Data Contract (Data Asset + Contract Definition)** is now available to other **Participants** that satisfy the policies contained.

# Discovering Data: Participant Catalog Setup
1. Install and configure **EDC, FCN and FCC**
    - **EDC** receives the URL to the **Dataspace Participant Registry**
    - **FCN** is configured to store and publish catalog entries
    - **EDC** receives the membership VC to authenticate itself and its services as a valid member of the dataspace (e.g., when querying other **Participants** catalogs)
    - **FCC** is a scheduled task which is performed regularly
2. **FCC** uses the **Dataspace Participant Registry** to find other **Participants**
    - **FCC** starts crawling the list of **Participants**
    - for each **Participant** FCC reads the DID Document of their DID to find the self-description (service entry in the DID Document) which then provides all information necessary to access the services of other **Participants' EDCs**
    - **FCC** proves its dataspace membership and **Access Policy** attributes to other **Participants** through the issued VC and the self-description of the **Participant**
3. The **FCC** crawls the **FCNs** from other **Participants** for available (published) **Data Contracts**
    - If the credentials and self-description fulfill the **Access Policy** of a **Data Contract**, it becomes visible to the requesting **Participant** in their **FCN**
4. The **FCC** caches the results of periodic crawling in the local **FCN** and provides means to query the local cache.
    - **Participants** query their local **FCN** for **Data Contracts** available in the dataspace

# Sharing Data
1. A **Participant** queries their **FCN** cache for available **Data Contracts**
2. The **Participant** selects which **Data Contract Offers** they would like to consume
3. The providing and the consuming **Participant** negotiate the **Data Contract Agreement**
    - The contract negotiation may be automatic or involve manual workflow
    - When the contract negotiation is completed, a **Contract Agreement** is created for the requested **Data Contract** and it's preserved for future audit. The **Data Contract Agreement** contains the **Data Contract** which contains the **Usage Policy**.
4. The data consumer **Participant** initiates a data transfer request with the **Connector** from the providing **Participant**
5. The **Connector** component orchestrates the data transfer using specific data transfer technologies, depending on the extensions in use and the underlying data storage and processcing technologies.
    - The consuming and providing **Connectors** jointly orchestrate the transfer of data associated with the **Data Asset**
    - Both **Connectors** record an audit history of the transaction

![Mental Model of Data Transfer in a Dataspace](/images/Dataspaces_mental_model.png "Dataspacs Mental Model")
