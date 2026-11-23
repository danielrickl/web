# Example run

A complete example of what one run of the skill produces, so you can see the result before installing anything.

**Everything here is fictional**: the businesses, people, addresses, e-mail domains (`.example`) and phone numbers (UK numbers reserved for drama, `0113 496 0xxx`). No real business was contacted or evaluated.

Request: *"Find me 2 clients with a poor website: joiners in Leeds, UK."*

```
2026-11-23-joiners-leeds/
├── prospects.json                        # 2 candidates found and evaluated (Agent 1)
├── emails/
│   ├── oakline-joinery-leeds.md          # e-mail draft with {{DEMO_URL}} (Agent 2)
│   └── _no-email.md                      # sole trader without e-mail → phone only
├── demos/
│   └── oakline-joinery-leeds/
│       ├── index.html                    # tailored demo – open it in a browser (Agent 3)
│       ├── style.css
│       └── _data-used.md                 # what is verified and what is generic
└── final/
    ├── _overview.md                      # summary with the UK legal note (Agent 4)
    └── oakline-joinery-leeds/
        ├── email.md                      # final e-mail with the demo link filled in
        ├── preview-desktop.png
        └── preview-mobile.png
```

Things to notice:

- **Only verified facts.** The demo uses the services, area and hours from `prospects.json`; prices, reviews and photos are unknown, so they are not there.
- **Clearly labelled.** The demo says in a banner and in the footer that it is a design proposal, not the business's website, and has `noindex,nofollow`.
- **Legal check.** Under UK PECR the limited company can get an e-mail, while the sole trader is a phone contact only.
- **`[FILL IN]`.** The sender's company number was not provided, so it is left for you to fill in, not invented.

In a real run `final/<slug>/` also contains a copy of the demo (`demo/`); it is left out here to avoid duplication.
