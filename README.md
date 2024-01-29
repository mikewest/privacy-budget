> [!NOTE]
> The Privacy Budget proposal is no longer active.

# **Combating Fingerprinting with a Privacy Budget**

## Current state of affairs

Browsers have been making changes to how cookies are treated. Blunt approaches to cookie blocking have been tried, and in response we have seen some user-tracking efforts move underground, employing harder-to-detect methods that subvert cookie controls. These methods, known as ‘fingerprinting’, rely on various techniques to examine what makes a given user’s browser unique.

Because fingerprinting is neither transparent nor under the user’s control, it results in tracking that doesn’t respect user choice.

## End state to aim for

Fundamentally, we want to limit how much information about individual users is exposed to sites so that in total it is insufficient to identify and track users across the web, except for possibly as part of large, heterogeneous groups. 

There are several ways to quantify the degree to which each user is partially identifiable from the information shared with third parties, including _[k-anonymity](https://en.wikipedia.org/wiki/K-anonymity)_ (where _k_ is the number of other users with identical information), [entropy](https://en.wikipedia.org/wiki/Entropy_(information_theory)) (an information-theoretic measure of uncertainty), and [differential privacy](https://en.wikipedia.org/wiki/Differential_privacy) (ensuring that aggregated data does not reveal the inclusion of an individual’s data in the set). Our maximum tolerance for revealing information about each user is termed the privacy budget.


### What is a fingerprinting surface?

A fingerprinting surface is an interaction point where a website can learn something that is either stable or semi-stable for a given user or device and varies between users or devices. A somewhat obvious example of this is anything that reveals the device model or specific device hardware. These can be data returned from Javascript APIs, network identifiers such as headers or IP address, the user’s language, timing differences etc. Put another way, anything that reveals any information about the user can be used as a fingerprinting surface, but for the most part only useful when combined with multiple other sources of information.


## How to get there


### Measure information exposed by each fingerprinting surface

Each browser can start by instrumenting itself to estimate the information revealed by each surface and report it back through telemetry. This data will be used for the next phase.


### Measure total information exposed to each site

This is yet another measurement step where each browser can use the data collected in the previous step to account for the total information exposed to each site and report that back through telemetry. This data will help determine what sort of privacy budget can be enforced without creating large scale web breakage. This measurement will need to include the entirety of a user's interaction with a domain, until site data is cleared. If the telemetry system allows for reporting data about individual domains, it could be used for outreach to sites who are accessing data above the proposed limit to see if changes can be made to bring them under the limit.


### Enforce the privacy budget

Once we’re ready to enforce the privacy budget, subsequent API calls that violate the budget will either result in an error being thrown or, if possible, will be replaced with a privacy-preserving version of the API that either returns imprecise or noisy results or a generic result that doesn’t vary between users. Another option to consider is to deny storage and network requests to the page after the budget is hit such that it cannot exfiltrate any new information it learns.


### Exceptions

There are certain applications, such as 3D games or video conferencing, that may never be able to run within any reasonable privacy budget. For such applications, there will need to be an “escape valve,” an appropriately worded permission prompt informing the user that granting additional API access may allow the site to identify them. 

Certain permission-prompt-inducing API calls may in themselves reveal more information than the privacy budget would allow. Granting permission to such a prompt (e.g. getUserMedia) may grant a budget exemption by itself, but we will need to ensure the prompts communicate that as well.


### Passive surfaces

Some fingerprinting surfaces, such as UA string, IP addresses, and accept-language header, are passive in that they are available to every website whether they ask for them or not. For the purposes of privacy budget accounting, we will have to assume that each of these are being consumed by the site and therefore eat into the budget. 

Since we would like to provide the web with as much functionality as possible, and since many developers would rather spend their budget on more useful things than knowing the OS of the user’s machine (for example), we should work to remove passive fingerprinting surfaces and replace them with active ways to query the specific information developers need. For the previous example of the user’s OS version, if a website such as a download site had a need for that information we propose allowing them to request it via client hints rather than continue to send it with every request in the UA string. Client hints can similarly be used to query the user’s language for websites that need it, rather than delivering that information to every server via the accept-language header.
