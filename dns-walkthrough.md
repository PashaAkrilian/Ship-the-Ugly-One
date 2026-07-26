# DNS Walkthrough

**DRAFT — to be rewritten in my own words before submission.**

This explains how my site's address will eventually work once I have a `myname.flyrank.ai` subdomain pointed at my GitHub Pages site. It's written now, before I need it, so it's ready as a checklist when the subdomain is provisioned.

## What a CNAME record is

Think of the internet's address book (DNS) as a giant contacts list that maps human-friendly names, like `pasha.flyrank.ai`, to the actual location of a server. Most entries in that list ("A records") point a name directly at a fixed numeric address (an IP address), the way you'd save a friend's phone number.

A **CNAME record** is different: instead of pointing at a number, it points at *another name*. It's like saving a contact as "same number as Alex" instead of writing the number down yourself. So `pasha.flyrank.ai` wouldn't point at some IP address directly — it would point at `pashaakrilian.github.io`, and whoever is asking would then look up *that* name to find the real address. This is useful because GitHub's servers can change IP addresses behind the scenes, and as long as the CNAME still points at `pashaakrilian.github.io`, my subdomain keeps working without me having to update anything.

## What value my CNAME will point to

Once FlyRank Ops provisions `myname.flyrank.ai` (my actual subdomain name isn't finalized yet), the CNAME record they create will point to:

```
pashaakrilian.github.io
```

That's the GitHub Pages address that currently serves this repo at `https://pashaakrilian.github.io/Ship-the-Ugly-One/`. On my end, I'll need to add `myname.flyrank.ai` as a custom domain in the repo's GitHub Pages settings (which also creates a `CNAME` file in the repo itself) so GitHub knows to accept traffic for that name and issue an HTTPS certificate for it.

## What actually happens, step by step, when someone visits my site

Say someone types `myname.flyrank.ai` into their browser. Here's the chain of events, in plain terms:

1. **The browser asks a resolver.** The browser doesn't know where anything lives — it hands the name to a "resolver," usually run by the person's internet provider or a public service like Google's or Cloudflare's DNS. The resolver's job is to figure out the answer on the browser's behalf.

2. **The resolver asks the nameservers.** The resolver works its way through the DNS system's hierarchy (starting from the root, down to `.ai`, down to `flyrank.ai`) until it reaches the **nameserver** that's actually responsible for `flyrank.ai`. That nameserver holds the official records for anything under that domain, including my subdomain.

3. **The nameserver returns the record.** The `flyrank.ai` nameserver looks up `myname.flyrank.ai` in its records and finds the CNAME entry pointing to `pashaakrilian.github.io`. It hands that back to the resolver.

4. **The resolver follows the chain to an actual address.** Since a CNAME just points to another name, the resolver now has to look *that* name up too, which eventually resolves to the numeric IP address of GitHub's servers.

5. **The response comes back to the browser.** The resolver hands the final IP address back to the browser.

6. **The browser connects and loads the page.** The browser opens a secure (HTTPS) connection directly to GitHub's server at that address, requests the page, and GitHub Pages serves back `index.html` for my site. The padlock appears once the HTTPS certificate for `myname.flyrank.ai` is confirmed valid.

All of this typically happens in well under a second, and it happens every time someone loads the page — though browsers and resolvers cache the answer for a while so they don't have to repeat every step on every single visit.
