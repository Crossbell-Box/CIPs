---
CIP: 1
title: CIP Purpose and Guidelines
status: Living
type: Meta
created: 2022-7-26
---

## CIP Rationale

Crossbell is an open platfrom that needs to iterate and grow. CIPs should describe every primary changes, so CIPs can be viewed as a complete historical record of how Corssbell has grown and evolved. For new implementations, CIPs should provide concise technical specification and help community to better understand and assess their rationale.


## CIP Types

There are two types of CIP:

- A **Meta CIP** include procedures, guidelines, changes to the decision-making process.
- A **Standards Track CIP** describes primary changes of Crossbell that affects most or all Crossbell implementations, including protocol changes, application changes and changes in nework configuartions.
  Furthermore, Standards Track CIPs can be broken down into the following categories:
  - **Protocol**: includes improvements and modifications in protocols, mostly in the form of smart contracts modifications and additions.
  - **Networking**: includes modifications in network configuarations, such as—a change to the block gas rules and a modification of consensus mechanism.
  - **Indexer**: includes changes in indexer api.
  - **Application** launches of new applications. 


## CIP Process 

The following is the standardization process for all CIPs in all tracks:

**Idea** - An idea that is pre-draft. This is not tracked within the CIP Repository.

**Draft** - The first formally tracked stage of an CIP in development. An CIP is merged by an CIP Editor into the CIP repository when properly formatted.

**Review** - An CIP Author marks an CIP as ready for and requesting Peer Review.

**Last Call** - This is the final review window for an CIP before moving to `Final`. An CIP editor will assign `Last Call` status and set a review end date (`last-call-deadline`), typically 14 days later.

If this period results in necessary normative changes it will revert the CIP to `Review`.

**Final** - This CIP represents the final standard. A Final CIP exists in a state of finality and should only be updated to correct errata and add non-normative clarifications.

**Stagnant** - Any CIP in `Draft` or `Review` or `Last Call` if inactive for a period of 6 months or greater is moved to `Stagnant`. An CIP may be resurrected from this state by Authors or CIP Editors through moving it back to `Draft` or it's earlier status. If not resurrected, a proposal may stay forever in this status. 

>*CIP Authors are notified of any algorithmic change to the status of their CIP*

**Withdrawn** - The CIP Author(s) have withdrawn the proposed CIP. This state has finality and can no longer be resurrected using this CIP number. If the idea is pursued at later date it is considered a new proposal.

**Living** - A special status for CIPs that are designed to be continually updated and not reach a state of finality. This includes most notably CIP-1.
