# Introduction to User Story

Meet Francesca.

Francesca is the newly appointed Sustainability Program Manager at Italian Fictitious Foods Company (FFC). As such, her first and highest priority is to devise a system that enables FFC to put a CO2 label on every item in the company's worldwide stores, so environmentally conscious customers can make informed purchasing decisions (as part of brand strategy as well as anticipated future regulatory requirements).

To do so, Francesca needs data. Lots of data. Most of FCC’s suppliers will not be able to give her a per-item CO2 number, so she will need data going all the way back to source materials. For example, to calculate the CO2 footprint for FCC’s popular dry-aged beef prime rib, she will need

* The lifetime amount of water consumed by the cow
* The cow’s age
* The cow’s weight
* The weight of the piece of prime rib steak
* The lifetime amount of food consumed by the cow
* The type of food fed to the cow
* The annual energy usage of the rancher
* The energy mix used by the rancher
* The animal food supplier (its entire data to calculate the CO2 footprint of the feed)
* The entire logistics chain (its entire data to calculate the CO2 footprint of logistics)
* And so on…

The above data is, of course, by no means exhaustive. And that’s just for one store item!

“Where can I get all that data?” Francesca wonders. Looking for advice, she contacts André, a former colleague at French Marvelous Organic Foods (MOF), only to find out that he too struggles with the same question.
“You know what? Let’s build a dataspace together!” Francesca proposes. “A dataspace where all food retail stores and chains can get data from (and exchange data with) their suppliers, and suppliers in turn may get information on the turnover of their product to anticipate demand. A dataspace where each member can decide what data to share with whom, and under which usage policies, so that everyone profits.”
After some discussions between FFC and MOF, the two companies decide to join forces and create a Food Retail Dataspace.

As both companies have some overlapping suppliers, they decide to approach those together to convince one of them to engage in a pilot project. Rancho Relaxo, a large Argentinian organic cattle rancher, decides to participate. 

To start out, Francesca and André sit down and define the basic requirements for their dataspace.

# Basic Requirements

As FFC and MOF have different infrastructural environments (MOF run everything in their own small data center, while FFC are in a hybrid environment with some legacy applications running on-premises, and the rest on AWS), not to mention Rancho Relaxo and all the other environments of their suppliers, it is clear to Francesca and André from the outset that their solution must be infrastructure-agnostic.

Security is a big concern to all involved. After all, they each plan to divulge to each other more data than they have ever done before. And they want to do so in an automated, reliable, auditable way. A big part of security is knowing who you are sharing data with and trusting that the entity you are connecting with really his who they claim to be. Therefore, identification, and certification and verification of identity, will be paramount. 

While Francesca is aware that the pilot with Rancho Relaxo will initially be of limited scope, she wants to plan ahead for a dataspace where numerous participants can join in, each with their own data on offer, and with their own need for data from the other participants. This means that there will be the need for some sort of catalog, where participants in the space can look up not only who is participating, but also what sort of data all those participants have on offer, and with what policies attached. While this could be a central database that each participant publishes their data on offer to, André argues that this could soon become complicated to manage, a single attack vector from a security perspective, and slow to search. Also, he believes that participants might not want all other participants to even see the same data on offer from them. He advocates for some form of decentralized catalog, where each participant only sees the data that other participants want to offer that particular partner. 

After some discussion, Francesca, André, and Carlos decide on the following high-level requirements for their dataspace:

* The dataspace must be infrastructure-agnostic. No matter on what environment each participant in the space runs, all should be able to join.
* There must be some sort of digital identity management. This would ideally be decentralized and based on trusted certification from one or many third-party certification providers. Each participant must be able to independently verify the identity of all other participants.
* There must be a decentralized catalog where all participants can look up all data offered specifically to them by other participants in the space.
* There must be some way of making sure that agreed-upon usage policies for shared data is enforced automatically.
