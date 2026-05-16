---
title: Documenting and Managing DNSSEC Algorithm Lifecycles
abbrev: DNSSEC Algorithm Lifecycles
docname: draft-crocker-dnsop-dnssec-algorithm-lifecycle-latest
submissiontype: independent
date: 2026-05-13
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
  org: Google, LLC
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

Cryptographic algorithms for DNSSEC go through multiple phases during
their lifetime.  They are experimental, adopted, generally available,
in mainstream use, phasing out, deprecated, and obsoleted.  This
document defines phases for the DNSSEC algorithm lifecycle, and
criteria that can be used by the IETF for moving an algorithm from one
phase to the next.

--- middle

# Background

Each DNSSEC cryptographic algorithm is used in two distinct but
interconnected ways.  The first occurs when a domain owner signs their
zone using a DNSSEC algorithm.  The second occurs when a validating
resolver verifies that the DNSSEC signatures are correct.  If someone
uses a DNSSEC algorithm to sign DNS records, the party that receives
that signed set of DNS records should be able to validate the
signatures.  Importantly, this means receiving parties need to
implement the validation algorithm before the sending parties can
expect to use it effectively.  Equally, the receiving parties have to
keep the validation algorithm in service until all signing parties
stop using it.

These relationships seem obvious in hindsight, but there has not been
an organized way to communicate the current state of a DNSSEC
algorithm within the Internet community regarding DNSSEC algorithm
transitions.  This document builds upon the enhancements defined in
{{RFC9904}} to the IANA "DNS Security Algorithm Numbers" registry
{{DNSKEY-IANA}} and the IANA "DNSSEC Delegation Signer (DS) Resource
Record (RR) Type Digest Algorithms" registry {{DS-IANA}}; the values
in these registries enable us to describe the lifecycle phase that an
algorithm is in. This document adds additional structure to those
tables by discussing the values that need to be encoded within them to
document the expected phasing into and out of algorithms as they
traverse from a new state to eventually being considered obsolete.  In
addition, it discusses a way that the DNSSEC ecosystem as a whole
could ensure it is left in a resilient cryptographic state at all
times, where publishers and verifies agree to a minimal set of
algorithms widely, if not completely, available for use as multiple
algorithms simultaneously traverse through their lifecycles.

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
: The algorithm is nearing the end of its lifecycle, but it is still in use.  Implementers are advised to transition to other recommended algorithms.  Signing should be phased out.

6. Deprecated:
: All use for signing should have stopped, but signature validation is still supported.

7. Obsolete:
: No support for signing or signature validation is expected.

# Process and Criteria for transitions {#criteria}

The previous section does not specify the process and criteria for advancing a
DNSSEC algorithm through these lifecycle phases.  There are six transition points,
labeled A through F, between these seven lifecycle phases.  The
following sections describe a process and criteria for each of these transitions.

## A. Algorithm Inclusion

 - Prerequisites:
    * Algorithm has been given a Mnemonic and number in the "DNS Security Algorithm Numbers" registry {{DNSKEY-IANA}}.
    * Cryptographic community has determined that the algorithm as suitable to use for DNSSEC.
    * Documentation and implementations are widely available and stable.
 - IETF determines the algorithm is suitable for use with DNSSEC.
 - Action: IETF publishes notice that the algorithm is suitable for use and may be deployed for signature validation.

## B. Ready for Use

 - Prerequisites:
    * Deployment has been measured.
    * Deployment is deemed to have reached an acceptable level.
 - IETF reaches consensus that algorithm has been widely deployed for DNSSEC.
 - Action: IETF publishes notice that algorithm is available for DNSSEC signing.

## C. Mainstream

 - IETF reaches consensus that algorithm has reached mainstream status; deployment is essentially universal.
 - Actions:
    * IETF publishes notice that algorithm has reached mainstream status.
    * Signers using older algorithms, particularly algorithms in the Phaseout or later phases should transition to a mainstream algorithm.

## D. Phaseout

 - Prerequisites:
    * Cryptographic community has determined the algorithm is reaching its end of life.
 - IETF determines it is time to announce the phaseout.
 - Action: IETF publishes notice to signing operators to transition away from the algorithm and begin signing with a mainstream algorithm.

## E. Deprecation

 - Prerequisites:
    * Measure signing activity.
    * Signing activity is deemed to have largely subsided.
 - IETF determines it is time to deprecate the algorithm for use with DNSSEC.
 - Action: IETF publishes notice that use of the algorithm is now inappropriate for DNSSEC signing.

## F. Obsolescence

 - Prerequisite: Measurement of signing is at the lowest achievable level.
 - IETF determines the algorithm is obsolete.
 - Action: IETF publishes notice that algorithm is obsolete and ought be removed from implementations.

# Lifecycle Phase and the IANA Registry

The enhancements to the IANA registry of DNSSEC algorithms defined in
{{RFC9904}}.  Table 1 suggests the values to be placed into each of the
IANA registry columns "Use for DNSSSEC Signing", "Use for DNSSSEC
Validation", "Implement for DNSSSEC Signing", and "Implement for DNSSSEC
Validation" for each phase in the algorithms lifecycle defined in
{{phases}}.  The IETF is encouraged to follow Table 1 when assigning the
IANA registry values.

~~~

+=======+===========================+===========================+
|       |    DNSSEC Validation      |      DNSSEC Signing       |
|       +-------------+-------------+-------------+-------------+
| Phase |  Implement  |     Use     |  Implement  |     Use     |
+=======+=============+=============+=============+=============+
| 1     |     MAY     |     MAY     |     MAY     |     MAY     |
+-------+-------------+-------------+-------------+-------------+
| 2     | RECOMMENDED |     MAY     | RECOMMENDED |     MAY     |
+-------+-------------+-------------+-------------+-------------+
| 3     |     MUST    | RECOMMENDED |     MUST    |     MAY     |
+-------+-------------+-------------+-------------+-------------+
| 4     |     MUST    |     MUST    |     MUST    | RECOMMENDED |
+-------+-------------+-------------+-------------+-------------+
| 5     |     MUST    | RECOMMENDED | RECOMMENDED |     NOT     |
|       |             |             |             | RECOMMENDED |
+-------+-------------+-------------+-------------+-------------+
| 6     | RECOMMENDED |     NOT     |     NOT     |   MUST NOT  |
|       |             | RECOMMENDED | RECOMMENDED |             |
+-------+-------------+-------------+-------------+-------------+
| 7     |     NOT     |   MUST NOT  |   MUST NOT  |   MUST NOT  |
|       | RECOMMENDED |             |             |             |
|       |  -- or --   |             |             |             |
|       |  MUST NOT   |             |             |             |
+-------+-------------+-------------+-------------+-------------+

  Table 1.  Determine lifecycle phase from the IANA registry.

~~~

# Considerations for maintaining a robust DNSSEC algorithm state

The above recommendations consider the values associated with a particular
algorithm in the IANA registry for "DNS Security Algorithm Numbers" {{DNSKEY-IANA}}
and the IANA registry for "DNSSEC Delegation Signer (DS) Resource Record (RR)
Type Digest Algorithms" {{DS-IANA}}.  It is equally as important to ensure
that as algorithms come into favor and out of favor that the current set
of available algorithms always include some that are the Mainstream
state.  As the IETF community considers transitioning a particular
algorithm beyond the Mainstream state, it ought to simultaneously ensure
that at least one other algorithm is already present in the Mainstream state
or that one other algorithm is in the Ready to Use state and available
to become a Mainstream algorithm.  Specifically, at no time should
there be zero algorithms in the Mainstream state.

# IANA Considerations

IANA has no actions related to this document.

# Security Considerations

This document proposes a lifecycle for DNSSEC algorithms.  By following
the criteria presented in {{criteria}}, Internet-wide deployment of new
DNSSEC algorithm will occur in a smooth manner that ensures all implementations
will be able to validate signatures.  Likewise, following the criteria will
ensure that out-of-date DNSSEC algorithm are retired in a graceful manner.  The
criteria associated with the transition between phases of the lifecycle will
depend on the process that makes changes to the IANA registry as defined in
{{RFC9904}}.

If the industry fails to achieve global consensus on the state of any
one algorithm such that domain owners deploying signing zones disagree
with the deployed validating resolvers then it likely that DNS
resolutions will fail, rendering the DNS unusable.  As such, vendors
of both authoritative and recursive resolvers, and the operating
systems that deploy them, are encouraged to strictly follow the
current guidance to avoid DNS interoperability issues.

--- back

# Acknowledgments
{:numbered="false"}

Thanks to Warren Kumari for constructive comments.
