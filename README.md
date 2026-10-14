# UAS Login Gateway

Losse inlogpagina voor het UAS HR Platform. Draait op GitHub Pages
(`https://munderinsights.github.io/uas-login/login.html`).

## Waarom

De Google-inlogknop kan niet in de Apps Script-webapp zelf draaien:
Google weigert het `…-script.googleusercontent.com`-adres als toegestane
bron. Deze gateway draait op een eigen domein dat wél als Authorized
JavaScript Origin geregistreerd kan worden.

## Werking

1. De app opent `login.html?n=<nonce>&o=<origin>` in een popup.
2. De gateway toont de Google One Tap account-kiezer (FedCM) én een
   inlogknop als vangnet. Beide wegen gebruiken dezelfde callback;
   het token wordt maximaal één keer verstuurd.
3. De gateway stuurt het ID-token via `postMessage` terug naar de app.
4. De gateway sluit zichzelf.

## Veiligheidseisen

1. Het token wordt **alleen** naar vaste, hardgecodeerde doeladressen
   gestuurd (`ALLOWED_TARGETS`), nooit naar een adres uit een URL-parameter.
2. De app accepteert berichten alleen van `https://munderinsights.github.io`
   met het juiste type, nonce en token-formaat.
3. Nonce: 16 willekeurige bytes via `crypto.getRandomValues`, hex.
   De gateway geeft de nonce door aan Google (`nonce` in `initialize`),
   de app controleert de nonce in het ontvangen token.
4. Token nooit in een URL, nooit in `localStorage`, nooit gelogd.
5. Parameter `o` (herkomst) wordt alleen voor diagnose getoond
   (via `textContent`, nooit `innerHTML`) en nooit als doelwit gebruikt.
6. Geen opslag van het token in de gateway; na verzenden `window.close()`.

## Bevat geen geheimen

Dit project bevat geen geheimen. De `CLIENT_ID` is een publieke waarde
(zichtbaar in elke browser die de app laadt). Het daadwerkelijke token
wordt alleen in het geheugen verwerkt en nooit opgeslagen.
