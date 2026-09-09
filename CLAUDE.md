# Vlast na školách — web

Statický web neziskové iniciativy. Bez buildu, bez frameworku, bez závislostí.
Otevřeš `index.html` v prohlížeči a vidíš výsledek.

## Kontext projektu

Vzdělávací iniciativa, která vozí do základních škol bezplatné workshopy
o občanské angažovanosti, moderních dějinách a kritickém myšlení. Cílovka:
žáci 8. a 9. tříd, hlavně v odlehlejších regionech. Financováno z Evropského
sboru solidarity (projekt č. 2026-1-CZ01-ESC30-SOL-000404388, přes DZS,
06/2026–05/2027). Pětičlenný tým, všichni to dělají vedle školy a práce.

Hlavní účel webu: aby se škola mohla přihlásit o workshop.

## Struktura

```
index.html    jednostránkovka (hero, témata, průběh, tým, přihláška)
gdpr.html     zásady ochrany osobních údajů
style.css     celý design systém, proměnné nahoře
assets/       logo, ikona, loga EU a ESC
podpisy/      obrázky pro e-mailové podpisy, servírované na /podpisy/
```

## Design systém

Barvy a písma jsou v `:root` v `style.css`. Vychází z brand manuálu
(červenec 2026) a **neměň je bez vyžádání**:

```
--indigo  #190881   primární, nadpisy, tmavé sekce
--red     #d80300   akcent, tlačítka, linky nad kartami
--green   #16532e   --yellow #f4b200   --orange #ff8e09   doplňkové
```

Nadpisy: **Jost** (Google Fonts) — náhrada za Joost Schmidt z brand manuálu,
který není volně dostupný. Text: **Inter**.

Vizuální jazyk je bauhausový: kruhy a obdélníky, ostré hrany, hodně bílého
prostoru, velká typografie. Žádné stíny, žádné gradienty, žádné zaoblené
rohy kromě kruhů. Nové sekce drž v tomhle jazyce.

Web záměrně nemá fotky — použitelné fotografie z workshopů zatím neexistují.
Až budou, patří do `assets/`.

## Co se nesmí rozbít

**EU viditelnost v patičce je grantová povinnost.** Logo „Spolufinancováno
Evropskou unií" i celý disclaimer o názorech autorů musí zůstat na každé
stránce. Totéž číslo projektu.

**`[hidden] { display: none !important; }`** v `style.css` musí zůstat.
Formulář má `display: grid`, což by jinak přebilo skrývání a po odeslání
by pod poděkováním zůstal viset prázdný formulář.

**Logo.** Používej `assets/logo.png` (barevné) a `logo-white.png` (na indigo).
Zdrojová loga jsou v `~/Documents/vlast/LOGA/novy branding/`, ale jsou to
čtverce 2000×2000 s velkým prázdným okolím — před použitím ořezat na bbox.
Staré modré logo připomínající ikonu Wi-Fi je zavržené, nepoužívat.

## Formulář

Přihláška jede přes **Web3Forms** — žádný backend. `access_key` je přímo
v HTML (je to tak zamýšlené, není to tajemství).

Odesílá se přes `fetch` v inline skriptu na konci `index.html`. Po úspěchu
se formulář skryje a odkryje se `#prihlaska-done`. Při chybě zůstane
vyplněný, tlačítko se odemkne a ukáže se `#form-error`.

Posílá se i `souhlas_gdpr`, aby byl v e-mailu doklad o uděleném souhlasu.

Resend ani jiná e-mailová služba není potřeba a nezaváděj ji — vyžadovala by
serverless funkci. Bude relevantní až u e-shopu, na automatické potvrzení
objednávky z vlastní domény.

## Nasazení

GitHub `martinhrouda/vlastnaskolach` → Vercel (projekt `vlastnaskolach`,
preset Other, root `./`). **Push na `main` = automatický deploy**, nic víc.

Doména `vlastnaskolach.cz` běží u Webglobe, DNS míří na Vercel:
`A @ → 216.198.79.1`, `CNAME www → 3752dbed6a0c44b2.vercel-dns-017.com`.
Apex přesměrovává na www.

**Do DNS nesahat kromě těch dvou záznamů.** `MX smtp.google.com` drží firemní
Gmail, nameservery drží doménu. Změna kteréhokoli z nich shodí týmu poštu.

## Návyk, který se tady vyplatil

Po každé změně **ověř skutečný stav na živém webu**, ne návratový kód zápisu
ani `git status`. Tenhle projekt už dvakrát tiše nasadil starou verzi —
jednou kvůli duplicitnímu repu, jednou kvůli necommitnutým změnám. Rychlá
kontrola v konzoli prohlížeče:

```js
document.querySelector('input[name=access_key]').value
/fetch\(form\.action/.test(document.documentElement.innerHTML)
```

## Jak psát texty

Česky, stručně, střední rejstřík — ani úřední, ani hovorové. Bez marketingových
frází a klišé. Cílem není znít dospěle a seriózně, ale srozumitelně pro učitele
a ředitele, kteří web čtou mezi hodinami.

## Co je v plánu

Řazeno podle toho, co nejvíc pomůže přihláškám škol.

**Reference a fotky z workshopů.** Sekce „Odezva ze škol" v `index.html`
stojí na pilotu na ZŠ Žulová: citace z článku školy a tři citace
z anonymního dotazníku od 9 žáků. Sbírat další — jedna škola je málo.

**Mobilní navigace.** Pod 620 px se `.nav-links` schová a nic ji nenahradí,
takže na telefonu se nedá dostat na Témata ani na Tým. Chybí hamburger.

**Dohledatelnost.** Chybí `sitemap.xml`, `robots.txt` a strukturovaná data
(JSON-LD, typ EducationalOrganization). Učitel hledající „workshop občanská
výchova ZŠ zdarma" web nenajde.

**Skutečné měření.** Zatím se počítaly jen bajty a počty spojení, ne reálné
časy. Pustit PageSpeed Insights na živé URL a ve Vercel Analytics sledovat
jedno číslo: kolik návštěvníků odešle přihlášku. Nízká priorita.

**Fáze 2: prodejní sekce pro karetní vzdělávací hru** (jediný produkt).
Bez platební brány — objednávkový formulář, pak faktura a QR platba převodem.
Stripe ani Comgate nezavádět, dokud objem objednávek neporoste.
Pozor: Vercel má u tarifu Hobby zakázané komerční použití, e-shop si vyžádá
placený tarif nebo přesun jinam.

## Fotky žáků

**Souhlasy se zveřejněním podobizny nemáme a mít nebudeme.** Z toho plyne
tvrdé pravidlo: na web nesmí fotka, na které by šel kdokoli poznat.

Nestačí, že není vidět obličej. Rozhoduje, jestli člověka pozná někdo,
kdo ho zná — spolužák nebo rodič pozná mikinu, vlasy a místo v lavici.
U fotky z konkrétní třídy, konkrétní školy a konkrétního dne to platí
i pro záběry zezadu. Ty proto taky ne.

Projde jen detail bez člověka: ruce, pracovní list, materiály na stole,
prázdná učebna. Jediná použitá fotka (`assets/workshop/pracovni-list.jpg`)
je ořez ruky s pracovním listem — hlava ani ramena na ní nejsou.

Až budou souhlasy, dá se to uvolnit. Nejjednodušší cesta: přidat do
domluvy se školou dotaz, jestli má od zákonných zástupců souhlas
s fotografováním pro propagaci, a odpověď si zaznamenat.

## Vztah k Akademii Díky, že můžem

Celý tým jsou absolventi Akademie Díky, že můžem. **Se spolkem ani
s Akademií ale nejsme nijak spojení** — žádné partnerství, žádná
zastřešující organizace. Na webu proto nesmí být nic, co by spojení
naznačovalo: ani loga, ani samolepky a slidy s brandingem DŽM na fotkách.

Pozor na článek ZŠ Žulová, který jako lektory uvádí „mladé lektory
z Akademie Díky, že můžem". Je to omyl školy. Proto z něj na webu
citujeme jen větu o reakci žáků a na článek neodkazujeme.
Stálo by za to poprosit školu o opravu.

Být absolventem Akademie je osobní fakt jednotlivce — v medailonku
u člena týmu se uvést dá, jako organizační vazba ne.
