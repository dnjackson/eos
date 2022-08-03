---
author: "Daniel Jackson"
title: "Concept Piggybacking in DNS"
date: "2022-03-11"
description: "How overloading in DNS brings trouble"
ShowToc: false
TocOpen: false
hideMeta: false # removes date etc from post
# hideSummary: true
summary: "Or why basic sysadmin tasks are so hard."
editPost:
    URL: "https://forum.softwareconcepts.io"
    Text: "Comments" # edit text
    appendFilePath: false # to append file path to Edit link
categories:
- Cases
---

Basic sys admin tasks can be surprisingly hard. You find some magic incantations online, which you dutifully type into a terminal, but then they don't have the desired effect. What now? At that point, you're usually stuck with no recourse (except to start an endless descent into online forums).

I had this experience recently trying to install Jekyll, a static website generator. Despite trying for a few hours, I just couldn't do it. So I switched to Hugo, a similar tool that offers an executable image so you don't have to build it yourself. Downloading a file and executing it is fortunately something I'm capable of doing (although even that has become harder with Apple's security protections).

Why are these simple tasks often so challenging? In large part, it's because the underlying concepts are both complicated and fragile, so you need to know a lot—and understand them more deeply than should be necessary— to work around their rough edges.

### DNS and DKIM

Here's an example, involving two concepts—one you're probably familiar with and one that might be new to you. The first is *domain name resolution*, and is the central concept of DNS (the Domain Name System). Its purpose is to decouple long-lasting domain names from the ephemeral machine addresses that serve them. The operational principle, roughly, is that the owner of a domain provides the domain name and an IP address, and subsequent lookups for that domain resolve to that IP address. The concept works hand-in-hand with the *domain registration* concept, which manages the process by which an owner acquires a domain (along with permission to update its DNS records), and the concepts corresponding to the various protocols that use DNS, such as *http*.

The second is *domain keys identified mail* (DKIM), a concept whose purpose is to reduce email spoofing. The operational principle is that the owner of a domain signs the from-address of an outgoing email message (along with some other fields from the email header) with the domain's private key, and places the signature in the header; the recipient then uses the domain's public key to check the signature, rejecting the message if the check fails. Assuming no other server has access to the domain's private key, this ensures that only the legitimate servers of that domain can successfully sign messages, and messages that purport to come from that domain but are actually spoofing the from-address will be rejected.

### Getting the Key

How does the recipient obtain the domain's public key? It could send a request to the outgoing email server, but that would require the server to support a special protocol. Instead, the key is stored as a DNS record. In addition to the records used for the *domain name resolution* concept, the domain name system includes a special "TXT" record type that is used to hold protocol-specific details associated with a domain. Thus a domain owner can create a record for *DKIM* that holds the DKIM public key.

TXT records have proliferated with the growth of new applications. Unfortunately, the DNS query that returns TXT records returns *all* the TXT records associated with a domain, and the recipient has to sort through them to find the relevant record. For this reason (and others), a new approach has become popular, in which the DKIM key is placed not in a TXT record, but in a CNAME record. CNAME, which stands for "canonical name" is a form of record used for domain name aliasing: it lets you map one host name within your domain to another. It is commonly used, for example, to redirect a name such as [*www.dnj.photo*](https://www.dnj.photo) to [*dnj.photo*](https://dnj.photo).

So how is this done? Here's the trick. You create a CNAME record that maps a name like *s1.\_domainkey.foo.com* to the name of a host that, when queried, will provide the DKIM key (thus also introducing a useful layer of indirection, so you can change the DKIM key without changing the DNS record). In this name, *s1* is called a "selector" and is used by DKIM to select one key over another (so that you can rotate keys for example, or have different keys for different email services), and *foo.com* is the email-sending domain that is being authenticated. The funny part is *\_domainkey*. That's an actual string, not a placeholder, and its purpose is to ensure that the whole name is interpreted as a label for a DKIM key, and not as a regular domain name.

### Underscoring the Problem

This tactic risks polluting the namespace of your domain, and that's why the DKIM designers included the underscore. Perhaps you have a host name called *domainkey*, but surely you wouldn't want a host name that starts with an underscore? In fact, underscores cannot appear in host names according to IETF standards. But then, since CNAME records map host names, shouldn't an underscore be prohibited there?

[RFC2181](https://datatracker.ietf.org/doc/html/rfc2181) attempted to clarify this issue, insisting that DNS is a general database, and is not just for domain name resolution, and the names that appear as labels in DNS records can thus be arbitrary strings. That's all very well, but it's not consistent with the intended role of CNAME records, which is precisely to map host names! Another [article](http://domainkeys.sourceforge.net/underscore.html) noting this confusion explains that "there are many DNS entries that are not host names,"" which seems to imply that CNAME records, whose labels generally are host names, should indeed be excluded from the looser rule.

Not surprisingly, some DNS providers [reject](https://help.sharpspring.com/hc/en-us/articles/115001065988-Understanding-CNAME-Records) underscores in CNAME records, and this means that if your mail service needs DKIM keys to be stored in CNAME records, you're [hosed](https://serverfault.com/questions/834679/is-underscore-illegal-in-a-cname-record). ()

What's going on here? In short, to support DKIM, the *domain name resolution* concept has been overloaded. This is a form of overloading I call "piggybacking" in my book, in which a developer wanting to add some function finds some existing concept to support it, even though it doesn't quite fit.

Here, the new function is using DNS for mapping application-specific attributes to values. By squeezing this functionality into the existing *domain name resolution* concept, and the CNAME record in particular, we now have a mess in which some "host names" aren't in fact names of hosts, and DNS providers differ on how they interpret the rules about whether such a name can have an underscore.

What's the alternative? Instead of piggybacking, DNS might have been extended with a new concept, allowing lookups in which you give a domain name (*foo.com*) and an application-specific attribute (*dkim*, say), and the DNS server returns the associated value (the DKIM public key). A new standard for TXT records could support such a concept.

### No Big Deal?

Now you might say that I'm making a mountain out of a molehill, and that these are small complications. But in practice, it's a mass of small, self-inflicted wounds of this sort that make many systems so complex and fragile. It turns out that DKIM—as well as SPF and DMARC, the other protocols used to prevent mail spoofing—can often be [broken by hackers](https://www.usenix.org/conference/usenixsecurity20/presentation/chen-jianjun), because of exactly this kind of non-uniformity (for example, in parsing email addresses).

Piggybacking will always seem cheaper and easier than modifying a concept or creating a new one. But the eventual price may be much higher.

<!--
*As always, comments welcome, in the [concept forum](https://forum.softwareconcepts.io) or by [email](mailto:dnj@mit.edu).*
 -->

*From my newsletter: archives and signup [here](https://buttondown.email/essence-of-software).*

### Update

A few updates and corrections, following some further investigation and [input](https://forum.softwareconcepts.io/t/one-little-underscore-reveals-overloading/107/6) from DNS experts:

**DNS as a general database**. The earliest RFCs mention DNS holding information beyond host addresses—including phone numbers for CSNET, for example—and make it clear that the resource records were not to be limited to the initial types. It wasn't until later, though, that the idea of DNS as a general key/value store seems to have emerged explicitly. Jerry Saltzer, who developed a name service for Athena at MIT called Hesiod, told me that Paul Mockapetris added the TXT resource type to support more general lookups, as required by applications such as Hesiod.

**Domain names as intentional names**. Domain names that included property labels go back at least to [Hesiod](https://web.mit.edu/Saltzer/www/publications/athenaplan/e.2.3.pdf), which used an @-symbol to separate the property-specifying part from the rest, eg. finger-server@berkeley.mit.edu. A project at MIT in 1999 explored this general idea, in which a name does not designate a service directly, but rather specifies the properties for a desired service, and called it [intentional naming](http://nms.lcs.mit.edu/projects/ins/). In 2000, [RFC 2782](https://datatracker.ietf.org/doc/html/rfc2782) described the addition of the SRV resource type, which mapped domain names of the form _service._protocol.name to server/port names, allowing intentional names such as _ldap._tcp.foo.com.

**Domain names that include property keys**. A domain name like *domainkey.foo.com* is not an intentional name that specifies a service. The DKIM protocol does not require a service; all that's needed is for the DKIM key to provided for the domain. Instead, this domain name is a combination of a domain name (*foo.com*) and a key to be looked up in the DNS records of that domain name.

**Underscores in domain names**. The use of underscores in these extended forms of name prevented conflicts with hostnames, but introduced the new risk of the new labels conflicting. In 2019, [RFC 8552](https://datatracker.ietf.org/doc/html/rfc8552) described the convention of naming with underscored labels, and introduced a registry to avoid collisions.

**Underscore confusions**. The early RFCs said that domain names should be as general as possible, but confusing wording misled many people. A much-quoted statement from [RFC 882](https://datatracker.ietf.org/doc/html/rfc882) seems to say that underscores are not permitted in the labels that comprise domain names: "The labels must follow the rules for ARPANET host names.  They must start with a letter, end with a letter or digit, and have as interior characters only letters, digits, and hyphen." This statement, however, seems on closer reading to be an informal explanation for a grammar that is not intended to be mandatory: "The preferred syntax of domain names is given by the following BNF rules.  Adherence to this syntax will result in fewer problems with many applications that use domain names (e.g., mail, TELNET)." This complicated position is elaborated in [RFC 1035](https://datatracker.ietf.org/doc/html/rfc1035) which states:

*The DNS specifications attempt to be as general as possible in the rules
for constructing domain names.  The idea is that the name of any
existing object can be expressed as a domain name with minimal changes. However, when assigning a domain name for an object, the prudent user
will select a name which satisfies both the rules of the domain system
and any existing rules for the object, whether these rules are published
or implied by existing programs.**

Not surprisingly this has confused even experts; a [ballot](https://www.ssl.com/faqs/underscores-not-allowed-in-domain-names/) amongst a consortium of companies voted to sunset the use of underscores in DNS names appearing in certificates, citing the statement in 1035 that "labels must follow the rules for ARPANET host names" which it took, incorrectly, to specify "the characters which may be used in DNS domain names.""

**CNAME records for DKIM**. Use of CNAME resource records for DKIM is not (I believe), as I originally suggested above, to avoid the use of TXT records, but rather to provide an extra level of indirection so that a domain can delegate to a hosting service the job of assigning (and rotating) DKIM keys. The use of the *_domainkey* prefix alone would limit the number of TXT records returned, since the extended domain name has a different set of resource records associated with it than the base domain name.

**Refining the concept design analysis**. In summary, there are (at least) three distinct concepts in play here. First there is the concept of a **HierarchicalName**, which allows a name space to be divided into separately managed zones. This concept is familiar from file systems, and from the structure of many web APIs (which use so called "RESTful" names for resources). Second is the concept of **IntentionalName**, in which a name becomes a kind of specification. This also existed prior to DNS, although the concept has been less widely adopted. Third is the concept of **Metadata** in which an object has a collection of properties associated with it; a photo, for example, has its capture time and exposure; a file has its creation time; a DNS domain has its domain key.

The lens of concept design helps us recognize that much of the richness of DNS that we have explored comes from the fact that three distinct concepts are being offered. The familiarity of these existing concepts should make DNS easier to understand.

What is unusual is that all three concepts are implemented by the same mechanism. In concept lingo, the second and third concepts are "piggybacked" onto the first concept, with the properties and specifiers of **Metadata** and **IntentionalName** respectively both represented as labels in a prefix of a domain name. Like many piggybacking designs, this is ingenious and solves some problems. In particular, it allowed DNS itself to remain unchanged, without the need for new resource types or mechanisms (although it did require the creation of a new registry to avoid name clashes).

The downside is that piggybacked concepts generally cannot be fully supported by a mechanism that was not designed for them. A full implementation of **IntentionalName**, for example, would allow wildcard specifiers, so that for example one could request not only a color printer *_printer._color.local.foo.com* or a monochrome printer *_printer._mono.local.foo.com* but also any printer *_printer.\*.local.foo.com* whether color or monochrome. As noted in [RFC8552](https://datatracker.ietf.org/doc/html/rfc8552#section-1.4), DNS cannot support such wildcards. Another price paid for the piggybacking is some additional complexity involved in squeezing the new functionality into a Procrustean bed—here, the underscore, and all the confusion created about whether it is permitted.

Whether the DNS design is bad or good is not the main issue here—and now I have a better appreciation of the tradeoffs I am less inclined to insist that this piggybacking is a mistake. What my analysis shows, I hope, is that concept design can reveal the underlying issues and make clearer whatever tradeoffs are being made.

*Many thanks to Dave Crocker, Jerry Saltzer and Garrett Wollman who generously shared with me  their expertise on DNS.*
