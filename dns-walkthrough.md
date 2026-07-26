# DNS Walkthrough

**Note:** this is written ahead of time, before I actually have a `flyrank.ai` subdomain. When Ops provisions mine, this doc is basically my checklist for what to do and what should be happening under the hood.

## What's a CNAME record anyway

DNS is basically the internet's contact list — it maps a name people type (like `pasha.flyrank.ai`) to where that thing actually lives. Most records ("A records") just point a name straight at a fixed IP address, kind of like saving someone's actual phone number.

A CNAME is different — it points at *another name* instead of a number. It's the DNS equivalent of saving a contact as "same number as Alex" instead of typing the digits yourself. So instead of my subdomain pointing at some raw IP, it points at `pashaakrilian.github.io`, and whoever's asking has to go look that name up too. The upside: GitHub can change their servers' IPs behind the scenes all they want, and my subdomain still works, because it was never tied to an IP in the first place — just to GitHub's name.

## What value mine will actually hold

My subdomain name isn't final yet, but whatever it ends up being (`something.flyrank.ai`), the CNAME record Ops creates for it will point to:

pashaakrilian.github.io

which is where this site currently lives (`https://pashaakrilian.github.io/Ship-the-Ugly-One/`). On my side, once that's set up, I still have to go into the repo's GitHub Pages settings and add the subdomain as a custom domain — that step is what actually makes GitHub accept traffic for that name and issue it an HTTPS cert. It also auto-generates a `CNAME` file inside the repo itself.

## What happens between typing the address and the page showing up

Say someone types `myname.flyrank.ai` in their browser:

1. **Browser asks a resolver.** The browser has no idea where anything is — it just hands the name off to a resolver, usually run by whoever provides your internet (or a public one like Cloudflare's or Google's).
2. **Resolver goes up the chain.** It works its way through DNS's hierarchy — root, then `.ai`, then down to whoever's actually authoritative for `flyrank.ai` — until it finds the nameserver holding the real records for that domain.
3. **That nameserver answers.** It looks up `myname.flyrank.ai`, finds the CNAME pointing to `pashaakrilian.github.io`, and hands that back.
4. **Resolver has to look that up too.** Since a CNAME is just another name, not an address, the resolver now resolves *that* name, which eventually lands on GitHub's actual IP address.
5. **The IP comes back to the browser.**
6. **Browser connects and loads the page.** It opens an HTTPS connection straight to GitHub's server, asks for the page, and GitHub Pages serves back `index.html`. The padlock shows up once the cert for my subdomain checks out.

All of this happens in a fraction of a second, and browsers/resolvers cache the answer for a while so they're not repeating this whole chain on every single page load.
