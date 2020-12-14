# FAQ

Do you have a question that's not answered in this FAQ? [Open an
issue](https://github.com/bslassey/privacy-budget/issues).

**Disclaimer:** Answers below are subject to substantial change.

### What's the status of the privacy budget? What browsers support it?

At the time of writing (December 2020), the privacy budget is an early stage proposal and does not
yet have a browser implementation.

**Note on Chrome**: A [study](https://github.com/bslassey/privacy-budget#how-to-get-there) is being
conducted by Chrome to measure the identifiability of APIs exposed to websites. The next step for
Chrome is to publish the findings from their measurement study (size of a feasible privacy budget,
best ways to calculate and maintain the budget).

### What APIs are consuming / will consume my site's budget?

First, note that **consuming some of your site's budget is not a problem**. In many cases, sites are
expected to consume some or even a large part of their budget, because they need to access some
information about the agent to guarantee a great user experience or use specific features. You can
think of it like an actual budget: it belongs to your site, and you as a site developer will decide
how to allocate it.

How much budget specific APIs will consume isn't defined yet. Many APIs are expected to consume at
least some of the privacy budget, but the handful of APIs that are **likely** to consume larger
amounts of the privacy budget are APIs that access browser settings, APIs that enable to read the
WebGL render name or mobile phone device name, **and others**. Before the privacy budget is
enforced, **tooling** should be made available to help you figure out which APIs consume your site's
budget.

### Can I try out the privacy budget?

Not yet, as of December 2020 (see [Status](link to status)).

### What will I need to do on my site to adjust to the privacy budget? What about APIs that are so powerful that they will blow through the budget, even if they're not used for tracking?

First, note that **consuming some of your site's budget is not a problem**. In many cases, sites are
expected to consume some or even a large part of their budget, because they need to access some
information about the agent to guarantee a great user experience or use specific features. You can
think of it like an actual budget: it belongs to your site, and you as a site developer will decide
how to allocate it. Limitations will kick in only if you spend all of your site's budget, because
this will mean that your site is accessing a combination of APIs that can be used to uniquely
identify users. Typically:

- Sites that covertly track users will hit their budget, and they'll need to change.
- Some applications, such as 3D games or video conferencing, may not be able to run within any
  reasonable privacy budget. For such applications, there will need to be an "escape valve": a
  permission prompt informing the user that granting additional API access may allow the site to
  identify them. [Read more](https://github.com/bslassey/privacy-budget#exceptions).

### What can I do today to prepare for the privacy budget? What are alternatives to things that are consuming my budget?

There is no action to take that is specific to the privacy budget at this point. However, you can
proactively start making use of less-identifying APIs in your site. For example, consider making use
of [User-Agent Client Hints](https://web.dev/user-agent-client-hints/) over the User-Agent string to
only request the specific bits of data you need. If you're implementing or proposing APIs, ensure
that you are considering the identifying data the API may expose and how you can mitigate that. For
example, consider designing a configuration that allows an API to expose only uniform functionality
by default, but lets users optionally take advantage of platform-specific features if required.

### What happens if my site goes over budget? Will my API calls be blocked?

The enforcement mechanisms for the privacy budget aren't defined yet. Potential options are to block
API calls, to noise or uniformize the output of APIs, or to use another enforcement mechanism. This
decision will be informed by measurements [link to "measure"] that will help to assess the impact of
different enforcement mechanisms.

### What will users see if my site hits the budget limit?

Browsers may consider adding a permission prompt informing the user that they can grant additional
API access, and that this may allow the site to identify them. See details in [ What will I need to
do to adjust?](link to above).

### Will there be a way for my site to opt out?

Generally, no, since this may allow sites to knowingly hinder user privacy. However:

- Sites that cannot operate within the budget because they need access to powerful APIs may rely on
  permissions (link to above).
- Browsers may offer mechanisms to prevent third-parties from [spending a site's budget](link to
  below).

### Will the privacy budget be enforced on localhost? If so can I opt-out? If not, can I opt-in?

Browsers that implement the privacy budget will make tooling available (such as privacy budget
controls) to facilitate local development.

### Can I limit third-parties and iframes from using up my budget? What happens when a 3P library uses up my budget?

To help sites ensure that third-party content doesn't make them exceed their privacy budget,
browsers may want to offer sites the ability to opt-in to certain constraints (such as iframe
sandboxes or [feature
policies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Feature-Policy)).

### Is the privacy budget sufficient to prevent covert tracking, since IP Addresses can also be used to track users?

No, the privacy budget would need to be combined with other measures. There's a proposal to mitigate
IP-based tracking: [Willful IP blindness](https://github.com/bslassey/ip-blindness).

### How will the privacy budget's assessment of the "cost" of APIs change over time? On what basis?

_API "cost" = How much of the privacy budget would be spent when a site uses the API._

Identifiability of APIs and features will change with time as browser / device ecosystems and user
populations change. In the long term, browsers will need to periodically adjust the costing strategy
to keep up, which will in turn require periodic surveys. How and who will do this is not determined
at this time. Any resulting changes to the costing strategy will be handled in the same way as an
API change (link to below).

### Will the privacy budget make web APIs less reliable? Will the APIs I'm using be constrained tomorrow even though I'm within the budget today?

_API "cost" = How much of the privacy budget would be spent when a site uses the API._

Since an API's cost would affect its availability, it may effectively become part of the API
surface. In this case: when an API's cost would change, the same process as for any other API change
would need to be followed (public announcements, appropriate reviews...). Note: an API's cost change
may be induced by an API change itself (e.g. if the API, for the sake of enabling more capabilities,
accesses more browser information) or in more rare cases by browsers detecting that an API is more
identifying than previously evaluated.

### How will browser vendors maintain transparency and consistency over the privacy budget's costing strategy?

This is an open question, and it will need to be publicly established in the future. It depends on
the approach different browser vendors decide to take.

### Is the privacy budget feasible?

This proposal is at an early stage and exact privacy budget limits are to be determined. But we
think that a privacy budget is feasible: it takes about **32 bits of entropy** to uniquely identify
people on the web, but mobile web users (which make up most of web users) can only expose at most
about **13 bits** of entropy.

About these numbers:

- There are about [4.6 billion of web
  users](https://www.statista.com/statistics/617136/digital-population-worldwide/). So it takes
  log2(4.6 billion) = about 32 bits of entropy to uniquely identify people on the web.
- Mobile web users make up [most of web
  users](https://gs.statcounter.com/platform-market-share/desktop-mobile-tablet/).
- There's an order of 10,000 different mobile device models (estimates range from 4,000 to 60,000+).
  Devices of the same model have the same fingerprint. So mobile web users can only expose at most
  log2(10,000) = about 13 bits of entropy. Note that the mobile device population is very unevenly
  distributed over the thousands of device models, so the entropy of knowing a mobile device model
  may be significantly lower than 13 bits.

[Read more about entropy](https://www.eff.org/deeplinks/2010/01/primer-information-theory-and-privacy).
