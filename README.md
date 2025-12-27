# split-dns-landing-page
Public landing page for split DNS. This is intended to be used when you manage split-horizon DNS for an internal network/VPN but want to have a helpful public landing page (e.g. network users who may forget to turn on VPN).

## Configuration

Copy `config.js.example` to `config.js`, and populate the values. `domain` will show up on the page itself, while `defaultRedirect` will be the default redirect target when no `?redirect` is provided

## Deployment

Deploy `index.html` and `config.js` to a public-facing server and setup DNS for your domain name to point there. Set up the server/provider to 301 or 302 redirect `*.example.com` (replace with your domain) to `example.com/?redirect=$1`. How you accomplish this is up to you, but I use Cloudflare which allows me to solve this completely on the free tier.

### Cloudflare Pages

I have this setup on Cloudflare Pages. Rough steps:

- Upload the two files to a new Pages site
- Setup the automatic proxied DNS to the pages site by adding your `example.com` to the Settings -> Domains & Routes
- Add a wildcard CNAME entry in your DNS to redirect all `*.example.com` to your `example.com`
- On the Rules Overview page, create a new Redirect Rule. Set it to use a Custom Filter, click Edit Expression and enter `(http.host wildcard "*example.com" and http.host ne "example.com")`. Set the URL Redirect to `concat("https://example.com/?redirect=", http.host)`. Use Status Code 302 (Temporary Redirect), since we don't want to continue to use the cached values after connecting to the VPN.

After deploying, try `curl -v test.example.com`, and you should see a 302 redirect to `https://examplecom/?redirect=test.example.com`. Give it a few minutes and then try visiting `example.com`/`test.example.com` and you should see the landing page with dynamic link.

## Split Horizon DNS
Good luck, and remember, "it was DNS"!
