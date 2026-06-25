# Dalsverk — initial setup runbook

End-to-end checklist to bring the Dalsverk site live. Run from the top.

| Constant            | Value                                                           |
| ------------------- | --------------------------------------------------------------- |
| Account ID          | `4681478`                                                       |
| Location entity ID  | `1980`                                                          |
| Primary locale      | `is` (Icelandic)                                                |
| Alternate locale    | `en` (English) — enable in Yext UI before step 4               |
| Site repo           | <https://github.com/flyinghighindustries/-Dalsverk-ehf>         |
| Config repo (this)  | <https://github.com/flyinghighindustries/-Dalsverk-ehf-config> |

Set this once per shell session so the curl blocks below work — use a
**Management API key** from Yext → Account Settings → Developer → API
Credentials (the key needs Knowledge Graph read+write scope):

```bash
export YEXT_API_KEY="<your-key>"
```

> Never commit the literal key. The site itself doesn't need this key —
> it's only for the one-time content load and any later API-driven content
> updates. The site uses a separately-scoped Events key (see step 9).

---

## 1. Create the GitHub config repo

```bash
gh repo create flyinghighindustries/-Dalsverk-ehf-config --private \
  --description "Yext config-as-code for Dalsverk-húsasmíði"
```

(Or via the GitHub UI: New repository → `flyinghighindustries/-Dalsverk-ehf-config` → empty, no README.)

## 2. Push both local repos

The local repos are already initialized and committed at `~/dalsverk-config`
and `~/dalsverk-site`. After fixing `gh auth login -h github.com`:

```bash
cd ~/dalsverk-config
git remote add origin https://github.com/flyinghighindustries/-Dalsverk-ehf-config.git
git push -u origin main

cd ~/dalsverk-site
git push -u origin main
```

## 3. Apply custom field definitions in Yext (config-as-code)

1. Yext → **Account Settings → Resources** → **Connect GitHub Repository**.
2. Pick `flyinghighindustries/-Dalsverk-ehf-config`, branch `main`.
3. Click **Repull** to fetch the JSON files.
4. Click **Apply**. Iterate until all 20 files report green (19 fields + 1
   eligibility group). Common gotchas — see [`README.md`](README.md) for the
   exact schema shapes that are accepted; this repo has been written to match.

After this step, `entity 1980` has 19 new `c_*` fields available — empty.

## 4. Enable English as alternate locale

Yext → **Account Settings → Languages** → enable **English (`en`)** as an
alternate language. Icelandic (`is`) is already primary.

## 5. POST Icelandic content (primary profile)

Inline payload — write to `/tmp/dalsverk-is.json`:

```bash
cat > /tmp/dalsverk-is.json <<'JSON'
{
  "c_tagline": "Heildarlausnir í viðhaldi fasteigna",
  "c_heroEyebrow": "Faglegt viðhald síðan 2012",
  "c_heroSubheadline": "Dalsverk sérhæfir sig í faglegu viðhaldi fasteigna fyrir húsfélög, sveitarfélög, fasteignafélög og einkaaðila. Við tökum að okkur fjölbreytt verkefni og leggjum áherslu á vönduð vinnubrögð, áreiðanleika og langtímalausnir.",
  "c_servicesIntro": "Við bjóðum upp á heildstæða viðhaldsþjónustu fyrir fasteignir – allt frá þakskiptum til endurmúrunar og málningar utanhúss. Sérhæft fagfólk fyrir hvert verkefni.",
  "c_serviceBodies": [
    "Heildarendurnýjun þaks. Við gerum úttekt á ástandi þaks og bjóðum viðeigandi lausnir, hvort sem um er að ræða báruþak, stálþak eða annað efni.",
    "Hraðvirkar viðgerðir á þakleka, brotnum þakplötum og þéttingum. Lágmörkun á tjóni innanhúss og varanleg lausn á rótum vandans.",
    "Skipting á gömlum gluggum fyrir nýja og betur einangraða. Bætt einangrun, hljóðvist og útlit fasteignarinnar.",
    "Skipting á útihurðum og innihurðum. Aukin öryggi og einangrun með vönduðum búnaði sem þolir íslenskar aðstæður.",
    "Endurmúrun og viðgerðir á steyptum útveggjum. Tryggir langlífi og útlit hússins og kemur í veg fyrir frekara tjón.",
    "Almennar viðgerðir og viðhald á fasteignum. Allt sem þarf til að halda eigninni í toppstandi – stórt sem smátt.",
    "Sérhæfðar viðgerðir á útveggjum og sprungum. Þéttingar gegn raka og íslensku veðri.",
    "Málun og þéttingar utanhúss. Fagleg vinna með efnum sem þola íslenskt veðurfar og halda lit og útliti.",
    "Uppsetning og viðhald álklæðninga. Endingargóð og viðhaldslétt lausn fyrir útveggi sem lyftir útliti og verndar bygginguna."
  ],
  "c_statValues": ["15+", "500+", "100%"],
  "c_statLabels": ["Ára reynsla", "Verkefni unnin", "Ánægðir viðskiptavinir"],
  "c_aboutHeading": "Um Dalsverk",
  "c_aboutBody": "Dalsverk-húsasmíði er íslenskt fyrirtæki með yfir 15 ára reynslu af viðhaldi og endurnýjun fasteigna. Við sérhæfum okkur í þakviðgerðum, gluggaskiptum, endurmúrun, málun utanhúss og uppsetningu álklæðninga – fyrir húsfélög, sveitarfélög, fasteignafélög og einkaaðila.\n\nMarkmið okkar er einfalt: að tryggja að eignir haldi verðmæti sínu og þoli íslenska veðráttu. Við leggjum áherslu á vönduð vinnubrögð, áreiðanleika og langtímalausnir í hverju verki – stóru sem smáu.",
  "c_valueTitles": [
    "Vönduð vinnubrögð",
    "Skýr verkstjórn",
    "Tímanleg afhending",
    "Náið samstarf"
  ],
  "c_valueBodies": [
    "Hver vinnustaður er afhentur með sömu nákvæmni og við myndum vilja sjá á eigin húsi. Engin afsláttur á gæðum.",
    "Þú færð skýr svör um tímalínu, kostnað og næstu skref. Engin óvissa, engin óvænt útgjöld á leiðinni.",
    "Við stöndum við dagsetningar og uppfærum stöðu jafnóðum. Verkefni eru kláruð á réttum tíma.",
    "Við vinnum þétt með húsfélögum, eigendum og verkfræðingum. Þínir hagsmunir eru leiðarljósið."
  ],
  "c_ctaHeadline": "Tilbúin að hefja næsta verkefni?",
  "c_ctaBody": "Hvort sem þú stjórnar húsfélagi, rekur fasteignafélag eða ert einstaklingur með viðhaldsverkefni – við gerum þér tilboð án skuldbindingar. Sendu okkur tölvupóst eða hringdu og við svörum eins fljótt og auðið er.",
  "c_privacyPolicyContent": "Persónuverndaryfirlýsing\n\nDalsverk-húsasmíði ehf., Turnahvarf 6s, 203 Kópavogur, er ábyrgðaraðili vinnslu persónuupplýsinga á þessari vefsíðu samkvæmt lögum nr. 90/2018 og almennu persónuverndarreglugerðinni (GDPR).\n\nHvaða gögn söfnum við?\n• Tengiliðsupplýsingar sem þú gefur okkur þegar þú hefur samband (nafn, netfang, sími, skilaboð).\n• Tæknilegar upplýsingar um heimsóknina ef þú samþykkir vafrakökur (sjá flipa Vafrakökur).\n\nTilgangur og lagaheimild\nVið vinnum gögnin til að svara fyrirspurnum, gera tilboð og veita þjónustu. Heimildir okkar eru samningur við þig og lögmætir hagsmunir.\n\nGeymslutími\nFyrirspurnir og tölvupóstssamskipti eru geymd í allt að 3 ár frá síðustu samskiptum, nema lagaskyldur kveði á um annað.\n\nÞín réttindi\nÞú átt rétt á aðgangi að gögnum þínum, leiðréttingu, eyðingu, takmörkun vinnslu og flytjanleika. Sendu beiðni á dalsverk@dalsverk.is.\n\nUmkvörtun\nEf þú telur að við virðum ekki persónuvernd þína getur þú lagt fram kvörtun til Persónuverndar (postur@personuvernd.is, www.personuvernd.is).",
  "c_cookiesContent": "Vafrakökur\n\nVið notum fyrsta-aðila greiningarvafrakökur (Yext Analytics) til að skilja hvernig síðan er notuð og bæta hana. Engin þriðju-aðila rakning, engin auglýsingakökur.\n\nHvað er skráð ef þú samþykkir?\n• Síðuheimsóknir (URL, tími)\n• Smelluatburðir á tengla og hnappa\n• Lotuauðkenni og gróf staðsetning (land)\n• Vafri og stýrikerfi\n\nGögn eru geymd hjá Yext (þjónustuaðili í Bandaríkjunum) í allt að 13 mánuði. Flutningur til Bandaríkjanna byggir á EU-US Data Privacy Framework og stöðluðum samningsákvæðum (SCCs).\n\nÞú getur hafnað og notað síðuna eðlilega. Sú eina vafrakaka sem er skráð án samþykkis er sjálft samþykkismetið þitt (geymslulykill 'dalsverk:consent') sem hindrar að borðinn birtist aftur og aftur.\n\nÞú getur breytt ákvörðun þinni hvenær sem er með því að smella á 'Vafrakökustillingar' í síðufæti.\n\nFyrirspurnir um vafrakökur og persónuvernd: dalsverk@dalsverk.is."
}
JSON
```

Then PUT it to the primary (`is`) profile:

```bash
curl -X PUT "https://api.yextapis.com/v2/accounts/me/entityprofiles/1980/is?api_key=${YEXT_API_KEY}&v=20231201" \
     -H "Content-Type: application/json" -d @/tmp/dalsverk-is.json
```

Expect a 200 with the updated entity body. If you see `code 2230 / Invalid entity field` you skipped step 3 (Apply config first).

## 6. POST English content (alternate profile)

Only valid after step 4 (English enabled). Write the payload:

```bash
cat > /tmp/dalsverk-en.json <<'JSON'
{
  "c_tagline": "Complete solutions in property maintenance",
  "c_heroEyebrow": "Professional maintenance since 2012",
  "c_heroSubheadline": "Dalsverk specializes in professional property maintenance for housing associations, municipalities, property firms and individuals. We take on a wide range of projects and put quality workmanship, reliability and long-term solutions at the center of every job.",
  "c_servicesIntro": "We offer comprehensive maintenance services for buildings — from roof replacement and repointing to exterior painting and aluminum cladding. Specialist trades for every part of the job.",
  "c_serviceBodies": [
    "Full roof replacement. We survey the roof's condition and recommend the right system — corrugated, steel sheet or otherwise — then deliver the install.",
    "Fast roof repairs for leaks, broken panels and damaged seals. Minimize indoor damage and fix the root cause, not just the symptom.",
    "Replacement of old windows with modern, better-insulated units. Improved insulation, sound performance and exterior appearance.",
    "Replacement of exterior and interior doors. Better security and insulation with hardware built for Icelandic conditions.",
    "Repointing and repair of concrete exterior walls. Extends the building's life and prevents weather damage from getting worse.",
    "General repairs and maintenance for buildings of any size. The day-to-day work that keeps a property in good condition.",
    "Specialist repairs to exterior walls and cracks. Sealing against moisture and Icelandic weather.",
    "Exterior painting and weatherproofing. Professional work using paints that hold up to Icelandic conditions.",
    "Installation and maintenance of aluminum cladding. A long-lasting, low-maintenance solution that protects the building and lifts its appearance."
  ],
  "c_statValues": ["15+", "500+", "100%"],
  "c_statLabels": ["Years experience", "Projects completed", "Satisfied customers"],
  "c_aboutHeading": "About Dalsverk",
  "c_aboutBody": "Dalsverk-húsasmíði is an Icelandic company with over 15 years of experience in maintaining and renovating buildings. We specialize in roof repairs, window replacement, repointing, exterior painting and aluminum cladding — for housing associations, municipalities, property firms and individuals.\n\nOur goal is simple: to make sure properties hold their value and stand up to Icelandic weather. We focus on quality workmanship, reliability and long-term solutions on every job, big or small.",
  "c_valueTitles": [
    "Quality workmanship",
    "Clear project management",
    "On-time delivery",
    "Close collaboration"
  ],
  "c_valueBodies": [
    "Every site is handed over with the same care we'd want on our own building. No corners cut on quality.",
    "You get clear answers on timeline, cost and next steps. No uncertainty, no surprise expenses along the way.",
    "We hit the dates we commit to and keep you updated as work progresses. Projects finish on time.",
    "We work closely with housing associations, owners and engineers. Your interests guide every decision."
  ],
  "c_ctaHeadline": "Ready to start your next project?",
  "c_ctaBody": "Whether you manage a housing association, run a property firm or have a private maintenance project — we'll quote it with no commitment. Send us an email or call and we'll get back to you as soon as possible.",
  "c_privacyPolicyContent": "Privacy Notice\n\nDalsverk-húsasmíði ehf., Turnahvarf 6s, 203 Kópavogur, Iceland, is the data controller for personal data processed via this website under Act No. 90/2018 and the General Data Protection Regulation (GDPR).\n\nWhat data we collect\n• Contact details you give us when you reach out (name, email, phone, message).\n• Technical information about your visit if you accept cookies (see the Cookies tab).\n\nPurpose and legal basis\nWe process this data to answer enquiries, prepare quotes and provide services. Our legal bases are the contract with you and our legitimate interests.\n\nRetention\nEnquiries and email correspondence are kept for up to 3 years after the last interaction, unless a legal obligation requires otherwise.\n\nYour rights\nYou have the right to access, correct, erase, restrict the processing of, and port your data. Send a request to dalsverk@dalsverk.is.\n\nComplaints\nIf you believe we are not respecting your privacy rights, you can file a complaint with the Icelandic Data Protection Authority (Persónuvernd — postur@personuvernd.is, www.personuvernd.is).",
  "c_cookiesContent": "Cookies\n\nWe use first-party analytics cookies (Yext Analytics) to understand how the site is used and to improve it. No third-party tracking, no advertising cookies.\n\nWhat is recorded if you accept?\n• Page views (URL, time)\n• Click events on links and buttons\n• Session identifier and coarse location (country)\n• Browser and operating system\n\nData is stored by Yext (a US-based service provider) for up to 13 months. Transfer to the United States is based on the EU-US Data Privacy Framework and Standard Contractual Clauses (SCCs).\n\nYou can reject and continue using the site normally. The only cookie set without consent is the consent record itself (localStorage key 'dalsverk:consent'), which prevents the banner from reappearing.\n\nYou can change your decision at any time by clicking 'Cookie settings' in the footer.\n\nQuestions about cookies and privacy: dalsverk@dalsverk.is."
}
JSON
```

PUT to the `en` alternate profile:

```bash
curl -X PUT "https://api.yextapis.com/v2/accounts/me/entityprofiles/1980/en?api_key=${YEXT_API_KEY}&v=20231201" \
     -H "Content-Type: application/json" -d @/tmp/dalsverk-en.json
```

## 7. Set per-language slug values

Yext UI → Knowledge Graph → entity `Dalsverk-húsasmíði` → switch language tab → edit **Slug** field:

| Profile        | Slug value | Resulting URL |
| -------------- | ---------- | ------------- |
| Icelandic (primary) | *empty* | `/` (template falls through to `index`) |
| English (alternate) | `en`    | `/en`         |

## 8. Connect the site repo to Yext Pages

Yext → **Pages → Sites → New Site** → **Connect Git Repository** → pick
`flyinghighindustries/-Dalsverk-ehf`, branch `main`, build config `config.yaml`.

Set Site Settings → Environment Variables:

| Variable                              | Value                                                    |
| ------------------------------------- | -------------------------------------------------------- |
| `YEXT_PUBLIC_LOCATION_ENTITY_ID`      | `1980`                                                   |
| `YEXT_PUBLIC_LOCATION_LOCALE_CODE`    | `is,en`                                                  |
| `YEXT_PUBLIC_EVENTS_API_KEY`          | (Yext Events API key — see step 9; optional otherwise)   |

Trigger a deploy. First build takes 2–4 minutes.

## 9. (Optional) Yext Analytics

Yext → Developer Console → API Keys → new key with **Events read + write**
scope. Paste into `YEXT_PUBLIC_EVENTS_API_KEY` (step 8), re-deploy. The site
will only fire events after the user clicks **Accept** on the cookie banner
(consent-gated via `AnalyticsProvider requireOptIn={true}`).

Verify in browser: open the deployed URL → Accept → DevTools → Network → look
for `POST https://us.yextevents.com/accounts/me/events`. Or append
`?xYextDebug=true`.

## 10. Verify

- Visit the deployed root URL → renders Icelandic, language switcher shows `EN`.
- Visit `/en` → renders English, language switcher shows `IS`.
- View source on each → JSON-LD `LocalBusiness` schema present with correct
  `inLanguage`, NAP, hours, services.
- Reject cookies → no requests to `yextevents.com`.
- Accept cookies → events fire.

## Replace logo + add hero image (Yext UI)

The site falls back to `photoGallery[0]` for the hero image if `c_heroImage`
is empty, and to `logo` for the favicon if `c_favicon` is empty. To use
purpose-built assets:

1. Upload an image via Yext → Knowledge Graph → entity → field `c_heroImage`.
2. Upload a square 512×512 favicon via field `c_favicon`.
3. Upload an OG share image (1200×630 recommended) via field `c_ogImage`.
