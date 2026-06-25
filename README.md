# dalsverk-config

Yext **configuration-as-code** for Dalsverk-húsasmíði (account `4681478`,
location entity `1980`). Yext pulls this repo from GitHub via Account Settings
→ Resources.

Companion repo: [`-Dalsverk-ehf`](https://github.com/flyinghighindustries/-Dalsverk-ehf) — the React/Tailwind app that reads the Knowledge Graph entity defined here.

## Layout (matches katted24-config / sendoplex-config — verified working)

```
dalsverk-config/
├── c/
│   └── km/
│       └── field/                          # 19 custom field definitions
├── ce/
│   └── km/
│       └── field-eligibility-group/
│           └── location.default.json       # All 19 fields attached to location
└── default/
    ├── km/
    │   ├── entity-type-extension/
    │   │   └── location.json               # Enables location entity type
    │   └── settings.json                   # Binds defaultFieldEligibilityGroup
    └── platform/
        └── account-features.json
```

Resources sit at **repo root**, NOT under a `platform-config/` prefix. Earlier
versions tried `platform-config/` (copied from older Maasik repo) — Yext's
Resources connector doesn't recognize that layout.

## Field set (19 custom fields, all on `location` entity)

| Field                       | Type                       | Used in section          |
| --------------------------- | -------------------------- | ------------------------ |
| `c_tagline`                 | string · SIMPLE            | Hero headline            |
| `c_heroEyebrow`             | string · SIMPLE            | Small text above hero    |
| `c_heroSubheadline`         | string · MULTILINE         | Hero body                |
| `c_heroImage`               | image                      | Hero photo               |
| `c_servicesIntro`           | string · MULTILINE         | Above services grid      |
| `c_serviceBodies`           | list<string · MULTILINE>   | Pairs with built-in `services` titles |
| `c_statValues`              | list<string · SIMPLE>      | Stats row numbers        |
| `c_statLabels`              | list<string · SIMPLE>      | Stats row labels         |
| `c_aboutHeading`            | string · SIMPLE            | About section heading    |
| `c_aboutBody`               | string · MULTILINE         | About body text          |
| `c_aboutImage`              | image                      | About image              |
| `c_valueTitles`             | list<string · SIMPLE>      | Core values titles       |
| `c_valueBodies`             | list<string · MULTILINE>   | Core values descriptions |
| `c_ctaHeadline`             | string · SIMPLE            | Closing CTA              |
| `c_ctaBody`                 | string · MULTILINE         | CTA body                 |
| `c_favicon`                 | image                      | Browser favicon          |
| `c_ogImage`                 | image                      | Social share image       |
| `c_privacyPolicyContent`    | string · MULTILINE         | Privacy policy text      |
| `c_cookiesContent`          | string · MULTILINE         | Cookie policy text       |

All fields are `LOCALE_SPECIFIC` (translatable per language profile).

The site **also reads built-in fields** that already exist on the entity:
`name`, `slug`, `address`, `mainPhone`, `emails`, `hours`, `description`,
`services`, `photoGallery`, `logo`, `yearEstablished`, `websiteUrl`.

## Apply workflow

1. `git push` this repo to GitHub.
2. Yext → **Account Settings → Resources** → connect this GitHub repo, branch `main`.
3. **Repull → Apply**. Iterate until all files report green.
4. Yext → **Account Settings → Languages** → enable **English (`en`)** as
   alternate. Icelandic (`is`) is already primary.
5. Populate content via the curl blocks in [`RUNBOOK.md`](RUNBOOK.md).
