---
layout: detailed-guidance
title: Operating a service.gov.uk subdomain
subtitle: Consistency across central government digital services
category: operations
type: guide
audience:
  primary: tech-archs
  secondary: service-managers, web-ops, developers
status: draft
breadcrumbs:
  -
    title: Home
    url: /service-manual
  -
    title: Operations
    url: /service-manual/operations
---

Government offers a number of different digital services to citizens. While the start and end of a user's journey will be on GOV.UK, the service itself will typically be hosted elsewhere, and will need a different domain name as a result. This page describes the use of `service.gov.uk` subdomains for hosting digital services.

> Note: This document is written as a 'standard', and as such uses the words MUST, SHOULD, MAY and MUST NOT as defined in [RFC 2119](http://www.ietf.org/rfc/rfc2119.txt).

## One entry point

Every digital service offered by the UK government MUST have a single, well-known place on the Internet where users can go to when they want to use the service. That well-known place will be the relevant start page on GOV.UK – for instance, the DVLA’s tax disc service is at [https://www.gov.uk/tax-disc](https://www.gov.uk/tax-disc).

Service managers MUST NOT advertise any URL other than that of the GOV.UK start page as the starting point for the relevant service. This is what gets printed on literature and used in email signatures, TV adverts, etc.

The start page URL for a given service will be allocated by GDS based on discussions with the service manager and analysis of user behaviour, search referrals and other relevant data.

## Creating a domain

The transactional part of a service – the dynamically generated pages where users interact with the service – will typically not be hosted on the `www.gov.uk` domain. That means that each service needs its own domain name for the transactional part of the service.

> Note: This does not apply to the set of interactive tools on GOV.UK known as 'smart answers' which are developed and maintained by GDS in partnership with other government departments.

For all new digital government services going live from 1 April 2013 GDS will create a domain name of the form `servicename.service.gov.uk` (where "servicename" is a plain English description of the service agreed between the relevant dept/agency and the Government Digital Service). This will introduce consistency across central government domains for digital services and remove the dependency on departmental subdomains (which are of course vulnerable to machinery of government changes) and the now-retired DirectGov and BusinessLink online brands.

The process of obtaining a `service.gov.uk` subdomain begins either when the service manager asks a Government Digital Service product manager for a start page on GOV.UK (for services already under development at 13 March 2013) or when the service manager asks for a subdomain to be created via the GOV.UK service desks’s government contact form (for services where development starts after 13 March 2013).  Subdomains of `service.gov.uk` SHOULD describe the service (eg `lastingpowerofattorney.service.gov.uk`) and SHOULD NOT contain the name of the service owning department or agency (eg `ministryofmagicwandregistration.service.gov.uk`)

The service-owning dept/agency will be given delegated authority to manage the domain and its subdomains, although in some cases this work will be carried out by third party suppliers.

## Subdomains

This section gives some guidance about which subdomains a service manager should create once they have been given control of `servicename.service.gov.uk`.

**Maximum number of visible subdomains**

The user-facing live service SHOULD be operated using at most three user-visible subdomains of `servicename.service.gov.uk`:

* `www.servicename.service.gov.uk` is for the public facing, dynamic web pages that make up your service.
* `assets.servicename.service.gov.uk` is for assets such as static images and shared javascript files needed to run your live service. Note: written content about the service, such as guides to eligibility or detailed guidance for applicants, should be on GOV.UK.
* `admin.servicename.service.gov.uk` is for features that enable non-technical staff to run the service (eg contact centre staff might use this subdomain to access and process work items where human judgement is needed).

You SHOULD NOT create separate domains for APIs unless there’s a really good reason to have a completely separate domain. (Really good reasons are few and far between.)

Service managers should notify the Government Digital Service technical architects (via your Transformation team contact) if you intend to create user-visible subdomains other than the three listed above. We’re developing some patterns for more unusual system designs as well as for mainstream transactional services, and we’re always up for a discussion about exceptions and edge cases.

**Usernames and passwords**

If the service is a private alpha or private beta release then it should be protected by a username and password known only to the development team and the users who are testing the service. If a service, or part of a service, is a public alpha or beta releases then it should be clearly marked as such with a text label on every page (ie don’t use an image containing the word alpha or beta) and in every API response.

**Multiple environments**

It is good practice to have multiple 'environments' for the development, testing and live (aka production) versions of any service. The [development and testing environments](/service-manual/making-software/sandbox-and-staging-servers.html) allow the team to assess the correctness and quality of the service before it goes live. Typically, the subdomains used to access a development or testing instance of the service are structured in the same way as the subdomains used in the live version of the service.

Therefore, you MAY create other subdomains of `servicename.service.gov.uk` for use in testing and development, such as `www-preview.` and `www-dev` or `www.preview.` and `www.dev.`. If there is a compelling reason to use a non `.gov.uk` domain for testing and/or development subdomains, that’s also acceptable.

Regardless of the domain name used, web-based services on testing and development domains (including APIs) should be protected by a username and password along the same lines as private alpha and beta releases.

## Transport Layer Security

Many services will collect personal information from users. It’s very important that this information can’t be intercepted by malicious third parties as it travels over the Internet.

Therefore, all services accessed through `service.gov.uk` domains (including APIs) MUST only be accessible through secure connections. For web-based services this means HTTPS only (often referred to by the acronyms TLS or SSL, which both refer to the protocol underpinning these secure connections). Services MUST NOT accept HTTP connections under any circumstances.

Once a service manager has verified that their HTTPS setup is working fine they SHOULD enable [HSTS](http://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security) on the production domains (`www.`, `admin.` and `assets.`), by setting an HTTP response header such as

    Strict-Transport-Security: max-age=1209600, includeSubDomains;

representing a commitment to HTTPS-only traffic for 14 days. Once the service manager is confident that HSTS is configured correctly, you SHOULD increase the commitment to months or years:

    Strict-Transport-Security: max-age=31536000, includeSubDomains;

## Cookies

Cookies used on `www.servicename.service.gov.uk` and `admin.servicename.service.gov.uk` MUST be scoped to the originating domain only. Cookies MUST NOT be scoped to the domain `servicename.service.gov.uk`.

Cookies SHOULD NOT be used on `assets.servicename.service.gov.uk` (they [introduce a browser overhead that slows down the response time for users](http://developer.yahoo.com/performance/rules.html#cookie_free) without providing any benefit for the service manager).

Cookies MUST be sent with the `Secure` attribute and SHOULD, where appropriate, be sent with the `HttpOnly` attribute. These flags [provide additional assurances about how cookies will be handled by browsers](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_and_HttpOnly).

## robots.txt and root level redirections

GOV.UK is the place for users to find all government services, so it’s important to ensure that users always start on the relevant GOV.UK page, rather than a different or duplicate start page on `www.servicename.service.gov.uk`.

As a result, services need to ask search engines not to index pages on their domains, so that the relevant GOV.UK page and the service domain don’t compete with each other in search engine results. This can be achieved by redirecting users to the relevant GOV.UK start page if they go directly to the service’s domain name, and by asking search engines not to index pages on the service’s domain name. Therefore, every service hosted on a service.gov.uk domain MUST:

* Have a `robots.txt` file on the `www`, `admin` and `assets` subdomains asking search engines not to index any part of the site. Example content for `robots.txt` is given below, and more details can be found on [The Web Robots Pages](http://www.robotstxt.org/faq/prevent.html):

      User-agent: *
      Disallow: /

* Have an HTTP 301 redirection from the top-level index page of the `www` and `assets` subdomains to the relevant start page on GOV.UK. (Note: this means that the service start page on GOV.UK SHOULD NOT link to the root of the `www` domain.)

## Origin Servers for CDN-based provider of DDOS Protection

If you have contracted with [CDN](https://en.wikipedia.org/wiki/Content_delivery_network)-based [DDOS](https://en.wikipedia.org/wiki/DDOS#Distributed_attack)-protection suppliers then you SHOULD register the following additional subdomains for use by your suppliers:

*[DDOS]: Distributed Denial of Service
*[CDN]: Content Delivery Network

- `www-production.servicename.service.gov.uk`
- `admin-production.servicename.service.gov.uk`
- `assets-production.servicename.service.gov.uk`

Your suppliers will use these subdomains to address your `www`, `admin` and `assets` services.

Detailed configuration advice for origin servers is outside of the scope of this document, but it is important to ensure that these 'origin domains' only listen for traffic from trusted source such as

- the DDOS protection provider’s servers
- the locations where the service itself is being developed and/or managed

At present we advise against allowing DDOS protection suppliers to terminate SSL connections for transactional services carrying personal information, but this behaviour is not prohibited at present. Although SSL termination on the third party network would allow the supplier(s) to carry out additional analysis and potentially extra mitigations against certain types of attack, it would also give the supplier access to all the personal information being submitted to your service. There are obvious downsides to allowing this level of access, especially if the supplier’s network and processes have not been accredited to the same level as the rest of the service. It’s a risk based decision, but if in doubt we suggest a presumption against SSL termination on third party networks.

Many suppliers offer IP forwarding DDOS protection, which does not have the same security issues as SSL termination, and is recommended in preference to SSL termination.  If your service requires transaction monitoring (which is not at all the same thing as DDOS protection) you should contact your CESG account manager for advice.  This is interim guidance and will be updated - check back in early May 2013 for an update.

## Emails sent to service users

Emails to users of your service SHOULD be sent from a human-monitored email address that originates from the domain `servicename.service.gov.uk` (and not the dept/agency or any other domain name).

You SHOULD enable [SPF](http://en.wikipedia.org/wiki/Sender_Policy_Framework) on the sending domain. You MAY also want to use [DKIM](http://www.dkim.org/) on the sending domain; it can provide additional guarantees about message delivery and help recipients to more easily distinguish genuine mail from forgery.

*[SPF]: Sender Policy Framework
*[DKIM]: DomainKeys Identified Mail
