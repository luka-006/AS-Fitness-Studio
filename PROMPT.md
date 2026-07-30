# PROMPT: One-page fitness / lokalni biznis website (production skeleton)

Napravi **jednu samostalnu `index.html` stranicu** (sve CSS + JS u istom fileu). Bez frameworka, bez builda, bez bundlera. Jedina vanjska ovisnost: Google Fonts.

Cilj: tamna, sportska, branded one-page stranica za lokalni fitness / studio. Struktura i UX moraju pratiti ovaj skeleton. **Ne izmišljaj klijentske podatke.** Sve što nije dano ostaje kao jasan placeholder.

Referentna implementacija (vizual + patterni): Alive Fitness one-pager u ovom workspaceu — kopiraj **strukturu i ponašanje**, ne sadržaj klijenta.

---

## 0) Kada stigne Google Business link (obavezno)

Ako korisnik da `share.google/...`, Google Maps / Business URL:

1. **Sam povuci** javne podatke: ime, adresa, telefon, radno vrijeme, website, socials, opis, fotografije/logo ako su dostupni.
2. Spremi ih u `CLIENT-DATA.md` u rootu projekta (izvor + datum).
3. Preuzmi dostupne slike u `slike/` (ili `logo.png` ako je avatar/logo).
4. Ako Google blokira (CAPTCHA / bot wall):
   - koristi javne rezervacijske profile (npr. Zoyya), službeni web, Instagram — **samo ako je očito isti biznis**
   - u `CLIENT-DATA.md` označi što je verificirano, a što treba ručno potvrditi
   - **ne izmišljaj** sate, broj, adresu ni galeriju
5. Tek onda popuni skeleton placeholdere 1:1.

---

## 1) Tehnički stack

- 1× `index.html` (inline `<style>` + inline `<script>` na dnu)
- Fontovi: **Fraunces** (display/headings) + **Inter** (body/UI)
- `lang="hr"` (ili jezik klijenta ako je dan)
- Lokalni asseti (kad postoje): `logo.png`, `logo-clear.png` (fallback preko `onerror`)
- Opcionalno: Google Maps embed, video embed (samo ako URL postoji)
- Meta + Open Graph + opcionalni `schema.org` JSON-LD — samo s poznatim podacima

---

## 2) Design system (default palette — zamijeni accent hex ako klijent da brand boju)

```css
:root{
  --bg:#0B0B0B;
  --card:#161616;
  --ink:#F2F2F2;
  --soft:#8A8A8A;
  --accent:#EB4E19;          /* brand accent — zamijeni po logu */
  --accent2:#F06A3A;
  --accent-bg:rgba(235,78,25,.14);
  --line:rgba(235,78,25,.28);
  --ease:cubic-bezier(.4,0,.2,1);
  --radius:24px;
  --shadow:0 18px 50px -18px rgba(235,78,25,.35);
}
```

- Layout: `.wrap` max-width ~1150px
- Section padding ~6rem (manje na mobitelu)
- Gumbi: `.btn-primary` (pill, sheen na hover), `.btn-ghost`, `.btn-call`
- `.kicker` (uppercase + accent bar koji se “crta” kad parent dobije `.in`)
- Kartice samo gdje treba interakcija (FAQ, form, info-card, map HUD) — **ne** card-heavy hero

### Frontend design hard rules

- Prvi viewport = **jedna kompozicija**, ne dashboard
- Brand/logo = hero-level signal (ne samo nav text)
- Hero budget: brand, 1 headline, 1 short support, 1 CTA group (+ sitni trust line OK)
- **Nema** hero video animacije; **nema** inset hero image cardova
- Tamna atmosfera: gradienti / blur blobs / subtilni pattern — ne flat single color
- Izbjegavaj generički AI look (purple gradients, cream+terracotta newspaper, glow spam, pill clusters, emoji)

---

## 3) Redoslijed sekcija (obavezno)

1. `#loader` — full-screen intro
2. `#floatLogo` — floating logo → dock
3. `#nav` — fixed navigacija
4. `#hero`
5. `#o-nama`
6. `#usluge`
7. `#galerija`
8. `#proces`
9. `#faq`
10. `#lokacija`
11. `#kontakt`
12. `footer`

**Nav linkovi:** O nama, Usluge, Galerija, Lokacija, FAQ + call CTA + primary CTA → Kontakt  
**Proces** je na stranici, ali nije u navu.

---

## 4) UI patterni (kako se pravi)

### Loader

- Tamni fullscreen, samo logo fade in/out (~0.9s)
- **NE** narančasti expand-dot / splash prije loga
- JS dismiss ~1100ms ili click → `#loader.done` + `body.go`

### Floating logo dock

- Desktop rest: veliki, low opacity (~0.17), desno mid, `pointer-events:none`
- Scroll `y>60` → `.docked`: mali (~48px) top-left, klikabilan → `#hero`
- ≤880px: uvijek docked
- Nav ima prazan `.brand-slot` za mjesto docked loga
- Logo img: `onerror` fallback na `logo-clear.png`

### Nav

- Transparent → `.scrolled` (blur dark bar) nakon `y>20`
- Desktop: linkovi + call + primary
- Mobile: sakrij link listu; CTA kratki label (npr. “Termin”)

### Hero

- Eyebrow pill + H1 + 1 support + CTA group + trust line
- 3 statična blur **blob**a + statične čestice (**bez** float loop animacija)
- Tekst stagger `.h-item` kad `body.go`
- Placeholderi: `[[BRAND]]`, `[[HEADLINE]]`, `[[SUB]]`, `[[TRUST]]`

### O nama

- Kicker + H2 s typewriter efektom (`#typeTarget` + `data-text`) kad sekcija uđe u viewport
- 1–2 paragrafa + signature (ime/uloga) — placeholderi dok nema teksta
- Type speed ~45ms/char; cursor blink OK

### Usluge

- Frame s borderom; gornji rub zamijenjen **jednom full-width SVG** crtom (EKG/heartbeat stil) — jedna path, bez “pločice” / spoja na uglovima
- CSS columns lista `.service` (circular icon well + naslov + kratki opis)
- Italic `.moto` + “unsure” CTA band → `#kontakt`
- Ikone **bez** breathe/loop animacije
- Placeholderi: `[[USLUGA_n]]`, `[[MOTO]]`

### Galerija

- Uska masonry (~360px), video-first (9:16), hover lift + caption chip
- Dodaj item **samo** ako postoji realan embed/URL/slika
- Ne izmišljaj stock fotke ni fake reels

### Proces

- Tamniji band (`#111`), 4 koraka s brojevima
- Brojevi scale-in kad `.step.in`

### FAQ

- Accordion, **jedan** otvoren istovremeno
- `max-height` expand; `+` → `×`
- Pitanja/odgovori = placeholderi dok klijent ne da sadržaj

### Lokacija / mapa

- Stage: glow + grayscale/dimmed Maps iframe + soft mask
- Center pin: logo u krugu + pointer trokut; **statični** ring (bez pulse loop)
- Bottom HUD: ime + (opcionalno) Google Business ikona + adresa + CTA “Kreni prema …”
- **Bez** finish-line dashed route / lane animacije
- Embed/query samo s poznatom adresom; inače placeholder shell

### Kontakt

- 2 kolone: demo forma + info-card
- Forma polja: ime, telefon/email, usluga `<select>`, poruka + honeypot
- **Samo client-side validacija** + success panel; **nema** backend submit
- Info-card: adresa, telefon, socials, **živo radno vrijeme**

### Živo radno vrijeme (widget)

- Shell uvijek postoji
- JS schedule: dan → `[openH,openM,closeH,closeM]` ili `null` = zatvoreno
- Status: Otvoreno/Zatvoreno + countdown (“zatvara se za…”, “otvara se …”)
- Lista dana Pon→Ned, highlight danas
- Refresh svakih 30s
- Dok nema sati: placeholder raspored — **ne izmišljaj** sate

### Footer

- Brand blurb + social ikone
- Quick links + kontakt kolona
- © + auto `#year`
- `.demo-note` (npr. vizualni prijedlog / u izradi) ako treba
- Credit slot: “Izrada: Kasalo Digital” + studio mark (`kasalo-logo.png`) + link `https://kasalo-digital.hr`

---

## 5) Animacije (filozofija — mirnije, ne spektakl)

**Drži:**

- Kratki loader logo in/out
- Hero stagger (~0.5s, mali Y)
- Scroll reveal `.rv` (~0.55s, ~12px Y; delay d1–d4 mali)
- Kicker bar, step number pop, FAQ open, button hover
- Typewriter + cursor blink

**Nemoj:**

- Orange expand prije loga
- Blob/particle float loops
- Icon breathe, hours-dot blink, map pin pulse loops
- Duge reveal distance/duration
- Previše simultaneous motion

---

## 6) Integrity / content rules (obavezno)

- Ne izmišljaj: recenzije, telefone, adrese, sate, social URL-ove, video, citate
- **Nema** reviews sekcije osim ako klijent da prave recenzije
- Missing data → vidljivi placeholderi `[[...]]` ili izostavi opcionalni blok
- Forma je demo
- Logo fallbacks dozvoljeni; fake stock gallery nije

---

## 7) JS moduli (implementiraj sve)

1. Loader dismiss → `body.go`
2. Nav `.scrolled` + floatLogo `.docked`
3. IntersectionObserver reveal (`.rv`, `.step`)
4. Typewriter na about H2
5. FAQ accordion (exclusive)
6. Contact form demo + honeypot + success
7. Auto year
8. Live hours renderer (30s tick)

Null-safe: svaki modul `if (!el) return`.

---

## 8) Responsive

- Overflow-x clip na html/body
- Mobile: nav CTA short, hero CTA full-width, services frame manji padding, map HUD stack, footer 1 kolona
- Float logo uvijek vidljiv (docked) na uskom ekranu

---

## 9) Output

- Jedan kompletan `index.html` skeleton **ili** popunjena stranica ako su podaci dostupni
- Ako podaci nisu kompletni: `[[PLACEHOLDER]]` + `CLIENT-DATA.md`
- Komentari u HTML-u uz ključne sekcije (kratko)
- Na vrhu kratki “DATA NEEDED” popis: brand, logo, boja, adresa, telefon, sati, socials, copy, usluge, FAQ, media, maps URL

---

## 10) Kad stignu klijentski podaci

Zamijeni placeholdere 1:1. Nemoj mijenjati skeleton/layout/animacijsku filozofiju osim ako se eksplicitno traži. Accent hex uskladi s logom ako je dan.
