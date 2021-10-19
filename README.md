Support for CDS/CDNSKEY/CSYNC updates
=====================================

Support in domain registries and registrars
-------------------------------------------

|Registry|CDS|CDNSKEY|Delete|Bootstrap from insecure|CSYNC|Notes|
|--------|---|-------|------|-----------------------|--|-----|
|[.ch](https://www.nic.ch/security/cds/)|Yes|No|Yes|72 hours TCP-only|No|[guidelines](https://www.nic.ch/export/shared/.content/files/SWITCH_CDS_Manual_en.pdf)|
|.cr|No |Yes    |Yes   |7 days TCP-only|No|No documentation found; FRED is used|
|[.cz](https://www.nic.cz/page/383/faq/#faq45)|No |Yes    |Yes   |7 days TCP-only|No|[FRED is used](https://fred.nic.cz/documentation/html/Concepts/AKM.html)|
|[.li](https://www.nic.li/security/cds/)|Yes|No|Yes|72 hours TCP-only|No|[guidelines](https://www.nic.li/export/shared/.content/files/SWITCH_CDS_Manual_en.pdf)|
|[.nu](https://internetstiftelsen.se/domaner/domannamnsbranschen/teknik/automatiserad-dnssec/)|Yes|No|Yes|72 hours TCP-only|No|[Policy and Guidelines](https://internetstiftelsen.se/domaner/domannamnsbranschen/teknik/policy-and-guidelines-for-automated-dnssec-provisioning/)|
|[.se](https://internetstiftelsen.se/domaner/domannamnsbranschen/teknik/automatiserad-dnssec/)|Yes|No|Yes|72 hours TCP-only|No|[Policy and Guidelines](https://internetstiftelsen.se/domaner/domannamnsbranschen/teknik/policy-and-guidelines-for-automated-dnssec-provisioning/)|
|[.sk](https://sk-nic.sk/wp-content/uploads/2019/12/DNSSEC_CDS_EN.pdf)|Yes|No|Yes|72 hours|No|No clear information about using TCP for bootstrapping|
|[RIPE NCC](https://www.ripe.net/manage-ips-and-asns/db/support/configuring-reverse-dns#4--automated-update-of-dnssec-delegations)|Yes|No|Yes|No|No||

Support in DNS providers
------------------------

|Provider|CDS|CDNSKEY|Delete|Notes|
|--------|---|-------|------|-----|
|[Cloudflare](https://blog.cloudflare.com/automatically-provision-and-maintain-dnssec/)|Yes|Yes|Yes||
|[deSEC](https://desec.io/)|Yes|Yes|Yes|[docs](https://desec.readthedocs.io/en/latest/dns/rrsets.html#dnskey-caveat)|
|[DNSimple](https://support.dnsimple.com/articles/dnssec/#cdscdnskey)|Yes|Yes||[blog post](https://blog.dnsimple.com/2019/02/cds_cdnskey/)|
|[GoDaddy](https://uk.godaddy.com/help/enable-dnssec-in-my-premium-dns-account-6420)|Yes|Yes||[presentation at ICANN 68](https://68.schedule.icann.org/meetings/EqJCzT5N6kcZhh2TT)|

Parent-side software
--------------------

### dnssec-cds(8)
 - [part of BIND 9](https://github.com/isc-projects/bind9/blob/main/bin/dnssec/dnssec-cds.rst)
 - can use both CDS and CDNSKEY
 - can produce DSset file or script for `nsupdate`
 - no support for bootstrapping from insecure
 - no support for DNSSEC delete

### [cdnskey-scanner](https://gitlab.nic.cz/fred/cdnskey-scanner)
 - part of [FRED](https://fred.nic.cz/documentation/html/Concepts/AKM.html)
 - only CDNSKEY records
 - supports bootstrapping from insecure
 - almost zero documentation :(

### akm-multi-scanner
 - rewritten `cdnskey-scanner` part of FRED
 - supports scanning from multiple locations
 - source code location unknown :(
 - there is [diploma thesis](https://dspace.cvut.cz/bitstream/handle/10467/87860/F8-DP-2020-Shchavleva-Marina-thesis.pdf?sequence=-1&isAllowed=y) and [presentation in Czech](https://www.nic.cz/files/nic/it_20/prezentace/Shchavleva.pdf)

### [rcdss](https://github.com/RIPE-NCC/rcdss) (RIPE NCC CDS Scanner)
 - written in Python using [dnspython](https://www.dnspython.org/)
 - reads RIPE Database objects
 - produces RPSL-like diff objects
 - multithreaded scanning
 - no support for bootstrapping from insecure

Child-side software
-------------------

### [Knot](https://www.knot-dns.cz/docs/3.0/singlehtml/index.html#automatic-ksk-management)
 - publishes both CDS and CDNSKEY records
 - automated KSK rollover based on feedback from the parent
 - controlled by [`cds-cdnskey-publish`](https://www.knot-dns.cz/docs/3.0/singlehtml/index.html#policy-cds-cdnskey-publish) config option
 - can also submit DS change directly using DDNS

### [BIND9](https://bind9.readthedocs.io/en/latest/dnssec-guide.html#the-cds-and-cdnskey-resource-records)
 - publishes both CDS and CDNSKEY records
 - requires `rndc dnssec -checkds published` to advance the KSK rollover

### [PowerDNS](https://docs.powerdns.com/authoritative/guides/kskrollcdnskey.html)
 - publishes both CDS and CDNSKEY records
 - controlled by `pdnsutil set-publish-cds`
 - requires manual KSK rollover

Other links
-----------
 - https://archive.fosdem.org/2019/schedule/event/dns_dnssec_security_without_maintenance/
 - https://jpmens.net/2017/09/21/parents-children-cds-cdnskey-records-and-dnssec-cds/
