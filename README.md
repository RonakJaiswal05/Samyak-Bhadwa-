# Katha Studios — website

Static site: HTML, one stylesheet, plain JavaScript. No build step, no dependencies.

```
node serve.mjs        # http://localhost:5173  (opening index.html directly also works)
```

## Hosting on GitHub Pages

The contents of this folder go at the root of the repository (so `index.html` sits at the top level,
next to `assets/`). In the repository settings, Pages → deploy from the main branch, root folder.
The empty `.nojekyll` file tells GitHub to publish the files as they are, without Jekyll processing.

## Coming soon + preview password

`index.html` is the coming soon page, so it is what GitHub Pages shows at the site address.
Every visit starts there, whichever page was opened. Entering the preview password
(**Katha101**, case-sensitive) leads to the home page (`home.html`), and the whole site stays
open for the rest of that browser session. Closing the browser (or the tab) means the next visit
begins on the coming soon page again. *Lock* on the coming soon page ends access early.

- **Go live:** in `assets/js/gate.js` set `enabled: false`. The site address then forwards
  straight to `home.html`.
- **Change the password:** open any page, run `KATHA_GATE.hash('NewPassword')` in the browser
  console, and paste the result into `hash` in `gate.js`.
- **Security:** this is a browser-side gate on a static site. It keeps the public out, but a
  technical visitor could bypass it. For real protection, also enable password protection on the
  host (for example Netlify or Vercel site passwords, Cloudflare Access, or basic auth).

## Structure

```
index.html              Coming soon page with the preview password (site root)
home.html               Home
about.html              About Katha
shibasish.html          Shibasish Sarkar — about, timeline, titles
businesses.html         All businesses
business.html?id=…      One business (rendered from content.js)
team.html               Promoter + team  (#promoter, #team, #<person-id>)
journal.html            News
announcement.html?id=…  One announcement
contact.html            Contact form

assets/brand/           Logo, web-sized; reversed version for dark backgrounds
assets/css/katha.css    Design system + all components
assets/js/gate.js       Preview gate (password, on/off switch)
assets/js/content.js    ALL editable content  ← start here
assets/js/components.js Render functions (poster, portrait, reel, timeline, treatments…)
assets/js/motion.js     Scroll loop, poster reel, business treatments
assets/js/home.js       Home: opening, About Katha, Shibasish, sectors, upcoming, team, news
assets/js/pages.js      Inner pages
assets/js/site.js       Navigation, footer, reveals, cursor, page transitions
```

The home page runs in this order: opening, **01** About Katha, **02** Shibasish Sarkar,
**03** What we do, **04** Upcoming, **05** The team, **06** News.

## Editing content

Everything lives in `assets/js/content.js`. Navigation, footer, home, business pages
and contact topics all read from it.

- **Add a business** — add an object to `KATHA.businesses`. It appears in the nav, footer,
  home, businesses page and contact form. Pick a `treatment`: `cinema`, `vertical`,
  `network`, `archive`, or `editorial` for anything new.
- **Add an upcoming title** — add to `KATHA.projects`. Only `title`, `format` and `business`
  are required; `palette` and `variant` choose the poster's colour and composition.
- **Shibasish Sarkar** — `KATHA.promoter` drives both the home section and `shibasish.html`:
  `about` (confirmed biography) and `titles` (the films and series he has been part of, oldest
  first). These titles are his own, shown under his name — never as Katha Studios work.
  `shibasish.html` lays them out year by year with a root — one wire that threads down through
  every year marker and grows as the page is scrolled (`KATHA.motion.roots`, drawn through the
  real position of each marker, so it always meets them). The home page shows an even spread of
  eighteen of them in the moving reel.
- **Add or change a title** — edit `KATHA.promoter.titles`:
  `{ title, type: 'Film' | 'Web Series', year, cast, poster }`. `poster` is a file stem in
  `assets/images/titles/`, stored at two widths (4:5), e.g. `gangaajal-360.jpg` and
  `gangaajal-720.jpg`. A title with no `poster` renders as a typeset panel marked
  *No artwork* — add the two files and the stem to swap it for the real thing.
- **Add an announcement** — add to `KATHA.announcements` with an ISO `date` (`2026-09-11`).
- **Portraits** — register an image in `KATHA.images`, then set `portrait: 'key'` on the person.
- **Contact details** — fill `KATHA.site.contact`. `formEndpoint` receives the form as a POST
  (e.g. Formspree); with only `email` set, the form opens the visitor's mail app.

## Placeholders — before launch

This site carries **no invented information**. Where real content does not exist yet, it uses
clearly-labelled placeholders:

- Upcoming titles are "Title to be announced" posters marked *To be announced*.
- Shibasish Sarkar's biography, career, filmography (95 titles, 2003–2026) and photographs are
  all real, from the supplied documents. 31 titles have no artwork yet and render as typeset
  panels marked *No artwork*.
- The studio's own work — Main Vaapas Aaunga, Tribe and Forgotten — is real too, in
  `KATHA.projects`.
- Poster artwork is published exactly as supplied — nothing is cropped or retouched.
- Announcements are marked `placeholder: true`.
- Portraits are empty sittings with initials, not photographs.
- Photography is temporary reference imagery from Unsplash (`temp: true` in `KATHA.images`).

Launch checklist:

1. Fill in contact details and the form endpoint in `KATHA.site.contact`.
2. Set `KATHA.site.showPlaceholders = false`. Placeholder items disappear and empty states show instead.
3. Replace `temp` images with studio photography (`src: 'assets/images/…'`).
4. Add portraits, contact details and the form endpoint.

## Notes

- Type: Bodoni Moda (Didot-style display, low optical size to keep the hairlines sturdy) with
  Schibsted Grotesk. Both are loaded from Google Fonts.
- Motion is deliberately sparse and never tied to the scrollbar: the opening plays once on
  load (lines rise, the still clips open, the index staggers in), sections fade in as they
  arrive, and two things move on their own — the opening stills cross-fade every 5.6s and the
  promoter's poster reel scrolls. Both pause when off screen or when the tab is hidden.
  Everything respects `prefers-reduced-motion`.
- The opening stills live in `KATHA.heroScenes` (image key + caption). Add, remove or reorder
  them freely; with one scene the cross-fade simply does not run.
- Hovering a name in the home page's team list shows that person's portrait beside the pointer.
  Anyone without a photograph registered in `KATHA.images` and set as `portrait` shows the
  labelled "Portrait to come" sitting with their initials instead.
- Shibasish Sarkar's two photographs are in `assets/images/people/`. Both are square and small
  (400 and 447 px), so they are shown at 1:1 and never blown up far — drop larger files in at
  the same paths when they exist and nothing else needs changing.
- Content is rendered with JavaScript, so page headings and descriptions are static in each HTML file for search engines.
