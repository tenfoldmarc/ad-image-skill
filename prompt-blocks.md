# Proof card prompt blocks

Drop-in `{{PROOF_CARD_BLOCK}}` fillers for the main `SKILL.md` prompt template.

---

## Analytics card (followers, revenue, growth metrics)

```
CRITICAL element: a SLIGHTLY TILTED dark UI iPhone-style analytics card floating in 3D space, rotated about -8 degrees with realistic perspective and depth. Rounded corners, soft drop shadow, glowing accent edge halo, dimensional 3D look. Card shows: title "{{CARD_TITLE}}", number "{{BIG_NUMBER}}" prominently, label "{{TIMEFRAME}}", green +{{DELTA}}% delta, smooth line chart sloping up to the right with dim grid background.
```

---

## Dashboard card (using a screenshot reference)

```
CRITICAL element: in the empty space next to the subject, float a SLIGHTLY TILTED dark UI dashboard card based on the THIRD reference image (a dashboard screenshot the user provided). Card rotated about -7 degrees, rounded corners, soft drop shadow, glowing accent edge halo, very legible and crisp showing actual chart cards and KPI panels. NOT a background — a discrete proof element.
```

When using this, add the screenshot as a 3rd `medias[]` entry in the generate_image call.

---

## Notification stack (work-being-done signal)

```
CRITICAL element: float a SLIGHTLY TILTED dark UI notification stack card showing three iOS-style notifications, each with the brand avatar from the second reference image. Top notification reads in bold: "{{BRAND_NAME}} — 1m ago" with body "{{TASK_COMPLETED_MESSAGE}}". Below that, two dimmer notifications with shorter bodies: "{{TASK_2}}" and "{{TASK_3}}". Card rotated about -8 degrees, rounded corners, glowing accent edge halo.
```

Example task messages: "Reply sent to 12 leads", "Posted today's reel", "Drafted weekly newsletter", "Booked 3 calls".

---

## Ads Manager card (for ad/campaign promises)

```
CRITICAL element: in the empty space between the subject and the headline, float a SLIGHTLY TILTED dark UI ads manager-style card showing campaign performance: metric cards reading "Spend" "$X,XXX", "Reach" "XXX,XXX", "CPL" "$X.XX", a line chart trending up, and one row labeled "Active Campaign". Card rotated about 6 degrees, rounded corners, soft drop shadow, dimensional 3D look.
```

---

## Inbox card (for email/messaging promises)

```
CRITICAL element: float a SLIGHTLY TILTED dark UI email inbox card showing 4–5 email rows, each with sender avatar, subject line, and snippet of automated AI-drafted reply text. The first row is highlighted with a subtle accent glow and tagged "AI DRAFTED". Card rotated -7 degrees, rounded corners, soft drop shadow.
```

---

## Calendar card (for booking/scheduling promises)

```
CRITICAL element: float a SLIGHTLY TILTED dark UI calendar card showing a week view with several time slots filled in, each labeled with a meeting name and a small green "BOOKED" indicator. Card rotated 6 degrees, rounded corners, soft drop shadow, glowing accent edge.
```

---

## Phone screen card (generic mobile UI)

```
CRITICAL element: a SLIGHTLY TILTED dark UI phone-screen mockup floating in 3D space showing the user's app or interface. Rotated -8 degrees, rounded corners with realistic phone bezel, soft drop shadow, glowing accent edge halo.
```

Add a screenshot reference image as a 3rd `medias[]` entry.

---

## No proof card

If the headline alone carries the ad, omit `{{PROOF_CARD_BLOCK}}` entirely from the prompt and add: "Generous breathing room around the headline so it commands the frame."
