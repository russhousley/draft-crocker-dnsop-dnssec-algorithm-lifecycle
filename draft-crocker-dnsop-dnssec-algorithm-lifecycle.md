---
title: Documenting and Managing DNSSEC Algorithm Lifecycles
abbrev: DNSSEC Algorithm Lifecycles
docname: draft-crocker-dnsop-dnssec-algorithm-lifecycle-latest
submissiontype: independent
date: 2026-06-05
category: info
consensus: false
v: 3
ipr: trust200902
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

venue:
  github: "russhousley/draft-crocker-dnsop-dnssec-algorithm-lifecycle"

author:
- name: Steve Crocker
  org: Edgemoor Research Institute
  email: steve@shinkuro.com
- name: Russ Housley
  org: Vigil Security, LLC
  abbrev: Vigil Security
  email: housley@vigilsec.com
- name: Wes Hardaker
  org: Google LLC
  abbrev: Google
  email: ietf@hardakers.net

informative:
  RFC9904:
  DNSKEY-IANA:
    target: https://www.iana.org/assignments/dns-sec-alg-numbers
    title: "DNS Security Algorithm Numbers"
    author:
    - org: IANA
  DS-IANA:
    target: https://www.iana.org/assignments/ds-rr-types
    title: "Delegation Signer (DS) Resource Record (RR) Type Digest Algorithms"
    author:
    - org: IANA

--- abstract

Cryptographic algorithms for go through multiple phases during their
lifetime: experimental, adopted, generally available, in mainstream
use, phasing out, deprecated, and obsoleted.  This document defines
phases for algorithm deployment lifecycles within DNSSEC, and criteria
that can be used by the IETF for moving an algorithm from one phase to
the next.

--- middle

# Background

Each DNSSEC cryptographic algorithm is used in two distinct but
interconnected ways.  The first occurs when a domain owner signs their
DNS records using a DNSSEC algorithm.  The second occurs when a DNSSEC
validator verifies that the DNSSEC signatures are correct.  If someone
uses a DNSSEC algorithm to sign DNS records, the party that receives
that signed set of DNS records needs to be able to validate the
signatures for them to be useful.  Importantly, this means receiving
parties need to implement a validation algorithm before the sending
parties can expect to use it effectively.  Equally, the receiving
parties have to keep the validation algorithm in service until all
signing parties stop using it.

These relationships seem obvious in hindsight, but there has not been
an organized way to communicate the current phase of a DNSSEC
algorithm within the Internet community and when transitions between
them should and do occur.  This document builds upon the enhancements
defined in {{RFC9904}} to the IANA "DNS Security Algorithm Numbers"
registry {{DNSKEY-IANA}} and the IANA "DNSSEC Delegation Signer (DS)
Resource Record (RR) Type Digest Algorithms" registry {{DS-IANA}}; the
columns in these registries enable us to describe the lifecycle phase
that an algorithm is in. This document suggests additional structure
to those tables by stating the values that need to be encoded within
them. In turn, this enables the IETF to document the phasing points as
algorithms traverse into and out states during their lifetimes.

This document also discusses how the IETF can ensure the DNSSEC
ecosystem as a whole remains in a resilient cryptographic state at all
times, where publishers and verifies widely, if not completely, agree
to a minimal set of algorithms that must be available for use even as
the collection of algorithms simultaneously traverse through
independent lifecycles.

# The Seven Phases in the Lifecycle of a DNSSEC Algorithm {#phases}

This document defines seven phases in the lifecycle of an individual DNSSEC algorithm:

1. Experimental
: The algorithm is under development by the cryptographic community and is not yet ready for general use.

2. Adopted
: The algorithm is ready to be used by the Internet community.  It is listed in the IANA registry.  Implementers are expected to support the algorithm for signature validation.

3. Available:
: The algorithm is ready for use by all parties.  Implementers are expected to support the algorithm for signing and signature validation.

4. Mainstream:
: The algorithm has reached "recommended" status.  Implementers are expected to support the algorithm for signing and signature validation.

5. Phaseout:
: The algorithm is nearing the end of its lifecycle, but it is still
  in use.  Implementers are advised to ensure other algorithms are
  available for signers and versifiers can transition to.  Signing
  should be phased out.

6. Deprecated:
: All use for signing should have stopped, but signature validation is
  still supported to support the last signers that are delayed in transitioning.

7. Obsolete:
: No support for signing or signature validation is expected.

# Process and Criteria for transitions {#criteria}

The previous section does not specify the process and criteria for
advancing a DNSSEC algorithm through these lifecycle phases.  There
are sever transition points, labeled A through G, between these seven
lifecycle phases.  The following subsections describe a process and
criteria for each of these transitions.

These transitions points are idealistic in nature and describe when
algorithms are safely able to transition with reasonable stable times
between each of the steps.  External factors, such as sudden advances
in cryptographic attacks, may lead to some algorithms transitioning
more rapidly than desired, or even jumping states entirely in extreme
cases (for example transitioning from Adopted to Depreciated if a
newer algorithm is suddenly determined to be insecure).  Similarly,
very experimental algorithms may never even reach an Adopted state if
they fail to show promise for use within DNSSEC.

Note: in the text below there are descriptions indicating that the
IETF should perform some action (such as "the IETF publishes notice").
This document does not define how these actions should be implemented.
Some actions may require simple mailing list discussions, some may
require formal standards actions, etc.  This document concentrates on
the goals for proper communicating phasing and not the formality
semantics required to do so.

For each of the steps below, in addition to the actions listed for
each step, the IETF will need to publish updates to the "DNS Security
Algorithm Numbers" registry {{DNSKEY-IANA}} and the IANA "DNSSEC
Delegation Signer (DS) Resource Record (RR) Type Digest Algorithms"
registry {{DS-IANA}} using values from Table 1.

## A. Algorithm Experimentation

 - Prerequisites:
    * An algorithm has been created along with a document describing
      how it can be used within DNSSEC.

## B. Algorithm Inclusion

 - Prerequisites:
    * The algorithm has been given a Mnemonic and code-point
      assignment in the "DNS Security Algorithm Numbers" registry
      {{DNSKEY-IANA}}.
    * The cryptographic community has determined that the algorithm as
      suitable to use for DNSSEC.
    * Documentation and implementations are widely available and stable.
 - The IETF has also determined that the algorithm is suitable for use
   with DNSSEC.
 - Action: The IETF publishes notice that the algorithm is suitable
   for use and may be deployed for signature validation.

## C. Ready for Use

 - Prerequisites:
    * Deployment has been measured.
    * Deployment is deemed to have reached an acceptable level.
 - The IETF reaches consensus that the algorithm has been widely
   deployed for DNSSEC.
 - Action: The IETF publishes notice that the algorithm is available for
   DNSSEC signing.

## D. Mainstream

 - The IETF reaches consensus that the algorithm has reached mainstream
   status as deployment is essentially universal.
 - Actions:
    * Deployment has been measured.
    * The IETF publishes notice that the algorithm has reached
      mainstream status.
    * Signers using older algorithms, particularly algorithms in the
      Phaseout or later phases should transition to a mainstream the
      algorithm.

## E. Phaseout

 - Prerequisites:
    * The cryptographic community has determined the algorithm is
      reaching its end of life.
 - The IETF determines announces the DNSSEC algorithm is being phased out.
 - Action: The IETF publishes notice to signing operators that they
   should transition away from the algorithm and begin signing with
   an algorithm listed as mainstream.

## F. Deprecation

 - Prerequisites:
    * Measure signing activity.
    * Deployment has been measured.
    * Signing activity is deemed to have largely subsided.
 - The IETF determines the DNSSEC algorithm should be deprecated for
   usage.
 - Action: The IETF publishes notice that use of the algorithm is now
   inappropriate for DNSSEC signing.

## G. Obsolescence

 - Prerequisite:
    * Deployment has been measured.
    * Deployment is deemed to have reached the lowest achievable level
      of signing.
 - The IETF determines the algorithm is obsolete.
 - Action: The IETF publishes notice that algorithm is obsolete and
   ought be removed from implementations.

# Lifecycle Phase and the IANA Registry

This document provides guidance about the values to be encoded within
the implementation and usage columns from the IANA registry of DNSSEC
algorithms defined in {{RFC9904}}.  Specifically, Table 1 suggests the
values to be placed into each of the IANA registry columns "Use for
DNSSSEC Signing", "Use for DNSSSEC Validation", "Implement for DNSSSEC
Signing", and "Implement for DNSSSEC Validation" columns for each
phase in algorithm lifecycles defined in {{phases}}.  The IETF is
encouraged to follow Table 1 when assigning the IANA registry values.

Note that at times, particular for past assignments, the column
numbers in the registries may not match the guidance encoded in this
document.  This might be due to values created prior to this guidance
being offered, or when the IETF needs to document very unusual corner
cases that deviate from the guidance this document offers.

Note that in the first phase, the Experimental Phase, the algorithm
should only be used in a controlled or experimental environment.

~~~

+=======+===========================+===============================+
|       |    DNSSEC Validation      | DNSSEC Delegation and Signing |
|       +-------------+-------------+-------------+-----------------+
| Phase |  Implement  |     Use     |  Implement  |     Use         |
+=======+=============+=============+=============+=================+
| 1     |     MAY     |     MAY     |     MAY     |     MAY         |
+-------+-------------+-------------+-------------+-----------------+
| 2     | RECOMMENDED |     MAY     | RECOMMENDED |     MAY         |
+-------+-------------+-------------+-------------+-----------------+
| 3     |     MUST    | RECOMMENDED |     MUST    |     MAY         |
+-------+-------------+-------------+-------------+-----------------+
| 4     |     MUST    |     MUST    |     MUST    | RECOMMENDED     |
+-------+-------------+-------------+-------------+-----------------+
| 5     |     MUST    | RECOMMENDED | RECOMMENDED |     NOT         |
|       |             |             |             | RECOMMENDED     |
+-------+-------------+-------------+-------------+-----------------+
| 6     | RECOMMENDED |     NOT     |     NOT     |   MUST NOT      |
|       |             | RECOMMENDED | RECOMMENDED |                 |
+-------+-------------+-------------+-------------+-----------------+
| 7     |     NOT     |   MUST NOT  |   MUST NOT  |   MUST NOT      |
|       | RECOMMENDED |             |             |                 |
|       |  -- or --   |             |             |                 |
|       |  MUST NOT   |             |             |                 |
+-------+-------------+-------------+-------------+-----------------+

  Table 1.  Determine lifecycle phase from the IANA registry.

~~~

# Considerations for maintaining a robust DNSSEC algorithm state

The above sections consider the values associated with a particular
algorithm in the IANA registry for "DNS Security Algorithm Numbers"
{{DNSKEY-IANA}} and the IANA registry for "DNSSEC Delegation Signer
(DS) Resource Record (RR) Type Digest Algorithms" {{DS-IANA}} using
values from Table 1.  It is equally as important to ensure that as
algorithms come into and out of favor that the current set of
available algorithms always includes at least one algorithm that is in
the Mainstream state.  As the IETF community considers transitioning a
particular algorithm beyond the Mainstream state, it ought to
simultaneously ensure that at least one other algorithm is already
present in the Mainstream state or that one other algorithm is in the
Ready to Use state and available to simultaneously become a Mainstream
algorithm.  Specifically, at no time should there be zero algorithms
in the Mainstream state.

# IANA Considerations

IANA has no actions related to this document.

# Security Considerations

This document proposes a lifecycle for DNSSEC algorithms.  By
following the criteria presented in {{criteria}}, Internet-wide
deployment of new DNSSEC algorithms will occur in a smooth manner that
ensures deployed implementations will be able to validate published
signatures.  Likewise, following the criteria will ensure that
out-of-date DNSSEC algorithms are retired in a graceful manner.  The
criteria associated with how to effect documenting the transition
between phases of the lifecycle will depend on the process that makes
changes to the IANA registry as defined in {{RFC9904}}.

If the industry fails to achieve global consensus on the state of any
one algorithm such that domain owners deploying signing zones disagree
with the deployed validating resolvers, then it likely that DNS
resolutions will fail which in turn will render some portion of the
DNS as unusable.  As such, vendors of both authoritative and recursive
resolvers, and the operating systems that deploy them, are encouraged
to collaborate about the current phases and transitions of DNSSEC
algorithms and to strictly follow the guidance in order to avoid DNS
interoperability issues.

--- back

# Acknowledgments
{:numbered="false"}

Thanks to Warren Kumari for constructive comments.
