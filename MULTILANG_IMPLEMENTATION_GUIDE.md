# Průvodce implementací jazykových mutací pro Landing Pages

Tento dokument poskytuje kompletní návod pro rychlou replikaci vícejazyčného systému do dalších landing pages.

## 📋 Rychlý přehled

Tento systém umožňuje:
- ✅ Automatickou detekci jazyka prohlížeče
- ✅ Manuální přepínání mezi jazyky
- ✅ SEO optimalizaci s hreflang tagy
- ✅ Samostatnou indexaci každé jazykové verze
- ✅ Odesílání kódu země do webhooků

## 🗂️ Struktura složek

```
/your-landing-page/
  ├── index.html          (CS - výchozí)
  ├── thank-you.html      (CS)
  ├── pl/
  │   ├── index.html
  │   └── thank-you.html
  ├── hu/
  │   ├── index.html
  │   └── thank-you.html
  ├── sk/
  │   ├── index.html
  │   └── thank-you.html
  └── ro/
      ├── index.html
      └── thank-you.html
```

## 🚀 Krok za krokem implementace

### KROK 1: Vytvoření struktury složek

```bash
mkdir -p pl hu sk ro
cp index.html pl/index.html
cp index.html hu/index.html
cp index.html sk/index.html
cp index.html ro/index.html
```

### KROK 2: Změna lang atributu

V každém souboru změňte atribut jazyka:

```html
<!-- CS (root index.html) -->
<html lang="cs">

<!-- PL (pl/index.html) -->
<html lang="pl">

<!-- HU (hu/index.html) -->
<html lang="hu">

<!-- SK (sk/index.html) -->
<html lang="sk">

<!-- RO (ro/index.html) -->
<html lang="ro">
```

### KROK 3: Přidání SEO tagů

Do `<head>` sekce každého souboru přidejte:

#### Pro českou verzi (root):

```html
<meta name="description" content="[Váš popis v češtině]">

<!-- Canonical URL -->
<link rel="canonical" href="https://bewit.love/your-page/">

<!-- Hreflang tagy -->
<link rel="alternate" hreflang="cs" href="https://bewit.love/your-page/">
<link rel="alternate" hreflang="pl" href="https://bewit.love/pl/your-page/">
<link rel="alternate" hreflang="hu" href="https://bewit.love/hu/your-page/">
<link rel="alternate" hreflang="sk" href="https://bewit.love/sk/your-page/">
<link rel="alternate" hreflang="ro" href="https://bewit.love/ro/your-page/">
<link rel="alternate" hreflang="x-default" href="https://bewit.love/your-page/">

<!-- Open Graph -->
<meta property="og:title" content="[Váš title]">
<meta property="og:description" content="[Váš popis]">
<meta property="og:type" content="website">
<meta property="og:url" content="https://bewit.love/your-page/">
<meta property="og:image" content="[URL vašeho obrázku]">
<meta property="og:locale" content="cs_CZ">
<meta property="og:locale:alternate" content="pl_PL">
<meta property="og:locale:alternate" content="hu_HU">
<meta property="og:locale:alternate" content="sk_SK">
<meta property="og:locale:alternate" content="ro_RO">
```

#### Pro ostatní jazyky:

Změňte:
- `og:locale` podle jazyka (pl_PL, hu_HU, sk_SK, ro_RO)
- `og:url` na správnou URL s jazykovou prefixem
- `canonical` na správnou URL

### KROK 4: Přidání jazykového přepínače

Do CSS (před `</style>`):

```css
/* === JAZYKOVÝ PŘEPÍNAČ === */
.language-switcher {
    position: fixed;
    top: 20px;
    right: 20px;
    z-index: 9999;
    background: rgba(255, 255, 255, 0.95);
    padding: 10px 15px;
    border-radius: 8px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    display: flex;
    gap: 8px;
    align-items: center;
    border: 1px solid var(--color-border);
}

.language-switcher a {
    text-decoration: none;
    font-size: 20px;
    padding: 5px 8px;
    border-radius: 4px;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 3px;
    font-weight: 600;
    color: var(--color-text);
}

.language-switcher a:hover {
    background-color: var(--color-light-bg);
    transform: scale(1.1);
}

.language-switcher a.active {
    background-color: var(--color-accent);
    color: var(--color-white);
}

@media (max-width: 768px) {
    .language-switcher {
        top: 10px;
        right: 10px;
        padding: 8px 12px;
        gap: 6px;
    }
    
    .language-switcher a {
        font-size: 18px;
        padding: 4px 6px;
    }
}
```

Do HTML (hned za `<body>`):

```html
<!-- Pro českou verzi (root) -->
<div class="language-switcher">
    <a href="./index.html" class="active" title="Čeština">🇨🇿</a>
    <a href="./pl/index.html" title="Polski">🇵🇱</a>
    <a href="./hu/index.html" title="Magyar">🇭🇺</a>
    <a href="./sk/index.html" title="Slovenčina">🇸🇰</a>
    <a href="./ro/index.html" title="Română">🇷🇴</a>
</div>

<!-- Pro polskou verzi (pl/index.html) -->
<div class="language-switcher">
    <a href="../index.html" title="Čeština">🇨🇿</a>
    <a href="../pl/index.html" class="active" title="Polski">🇵🇱</a>
    <a href="../hu/index.html" title="Magyar">🇭🇺</a>
    <a href="../sk/index.html" title="Slovenčina">🇸🇰</a>
    <a href="../ro/index.html" title="Română">🇷🇴</a>
</div>

<!-- A podobně pro ostatní jazyky - vždy označte správný jazyk jako .active -->
```

### KROK 5: Automatická detekce jazyka

Do **root index.html** (ne do jazykových verzí!) přidejte před `</body>`:

```javascript
// === AUTOMATICKÁ DETEKCE A PŘESMĚROVÁNÍ PODLE JAZYKA PROHLÍŽEČE ===
(function() {
    const hasVisited = localStorage.getItem('bewit-lang-redirect');
    
    if (!hasVisited) {
        const userLang = (navigator.language || navigator.userLanguage).toLowerCase();
        
        const langMap = {
            'pl': './pl/index.html',
            'hu': './hu/index.html',
            'sk': './sk/index.html',
            'ro': './ro/index.html'
        };
        
        for (const [lang, path] of Object.entries(langMap)) {
            if (userLang.startsWith(lang)) {
                localStorage.setItem('bewit-lang-redirect', 'true');
                window.location.href = path;
                return;
            }
        }
        
        localStorage.setItem('bewit-lang-redirect', 'true');
    }
})();
```

### KROK 6: Úprava webhooků - Přidání kódu země

V každém souboru najděte webhook data a přidejte pole `country`:

```javascript
// Pro affiliate formulář
const webhookData = {
    customerId: customerId,
    firstName: firstName,
    lastName: lastName,
    email: email,
    phone: phone,
    country: 'PL', // PŘIDEJTE TENTO ŘÁDEK - změňte podle jazyka (CZ, PL, HU, SK, RO)
    // ... zbytek dat
};

// Pro coffee formulář (nebo jakýkoliv jiný)
const webhookData = {
    customerId: window.getBewitCustomerId ? window.getBewitCustomerId() : null,
    clickDate: formatClickDate(),
    firstName: formData.get('firstName'),
    lastName: formData.get('lastName'),
    email: formData.get('email'),
    phone: formData.get('phone'),
    country: 'PL', // PŘIDEJTE TENTO ŘÁDEK - změňte podle jazyka
    // ... zbytek dat
};
```

**Kódy zemí:**
- Čeština: `'CZ'`
- Polština: `'PL'`
- Maďarština: `'HU'`
- Slovenština: `'SK'`
- Rumunština: `'RO'`

## ✅ Checklist implementace

```
[ ] 1. Vytvořit strukturu složek (pl, hu, sk, ro)
[ ] 2. Zkopírovat index.html do všech složek
[ ] 3. Změnit lang atribut v každém souboru
[ ] 4. Přidat SEO tagy (canonical, hreflang, Open Graph)
[ ] 5. Přidat CSS pro jazykový přepínač
[ ] 6. Přidat HTML jazykového přepínače do všech verzí
[ ] 7. Přidat automatickou detekci jazyka do root index.html
[ ] 8. Přidat kód země do všech webhooků
[ ] 9. Přeložit texty (nebo použít profesionálního překladatele)
[ ] 10. Otestovat detekci jazyka v různých prohlížečích
[ ] 11. Otestovat přepínač jazyků
[ ] 12. Ověřit odesílání správného kódu země do webhooků
```

## 🌍 Tipy pro profesionální překlady

### Marketingové fráze - Polština

- "Začít vydělávat" → "Zacznij zarabiać"
- "Pasivní příjem" → "Pasywny dochód"
- "Provize" → "Prowizja"
- "Zdarma" → "Za darmo"
- "Partnerský program" → "Program partnerski"

### Marketingové fráze - Maďarština

- "Začít vydělávat" → "Kezdj el keresni"
- "Pasivní příjem" → "Passzív jövedelem"
- "Provize" → "Jutalék"
- "Zdarma" → "Ingyen"
- "Partnerský program" → "Partner program"

### Marketingové fráze - Slovenština

- "Začít vydělávat" → "Začať zarábať"
- "Pasivní příjem" → "Pasívny príjem"
- "Provize" → "Provízia"
- "Zdarma" → "Zadarmo"
- "Partnerský program" → "Partnerský program"

### Marketingové fráze - Rumunština

- "Začít vydělávat" → "Începe să câștigi"
- "Pasivní příjem" → "Venit pasiv"
- "Provize" → "Comision"
- "Zdarma" → "Gratuit"
- "Partnerský program" → "Program de afiliere"

## 📝 Příklady pro různé scénáře

### Scénář 1: Landing page s jedním formulářem

```javascript
// Pouze jeden webhook - přidejte country do jednoho místa
const webhookData = {
    // ... vaše data
    country: 'PL', // Pro polskou verzi
    // ... zbytek dat
};
```

### Scénář 2: Landing page s více formuláři

```javascript
// Affiliate formulář
const affiliateWebhookData = {
    // ... data
    country: 'PL',
    // ...
};

// Coffee/Newsletter formulář
const coffeeWebhookData = {
    // ... data
    country: 'PL', // Stejný kód pro všechny formuláře na stejné stránce
    // ...
};
```

### Scénář 3: Bez thank-you page

Pokud nemáte thank-you page:
- Vynechejte kopírování thank-you.html
- Upravte pouze index.html v každé složce

### Scénář 4: Různé webhook URL pro různé jazyky

```javascript
// Pokud potřebujete různé webh

ooky pro různé jazyky
const WEBHOOK_CONFIG = {
    cs: 'https://webhook-url-for-czech',
    pl: 'https://webhook-url-for-polish',
    // atd.
};

const currentLang = 'pl'; // Nastavte podle aktuálního jazyka
fetch(WEBHOOK_CONFIG[currentLang], {
    // ... konfigurace
});
```

## 🔍 Testování

### 1. Test automatické detekce

- Změňte jazyk prohlížeče na polštinu
- Vyčistěte localStorage: `localStorage.clear()`
- Načtěte root stránku
- Měli byste být přesměrováni na /pl/

### 2. Test jazykového přepínače

- Klikněte na každou vlajku
- Ověřte, že se načte správná jazyková verze
- Zkontrolujte, že správná vlajka má třídu `.active`

### 3. Test webhooků

- Otevřete DevTools (F12) → Network tab
- Odešlete formulář
- Najděte webhook request
- Zkontrolujte, že obsahuje správné pole `country`

## 🚨 Časté problémy a řešení

### Problém: Přepínač jazyků nefunguje na jazykových verzích

**Řešení:** Zkontrolujte relativní cesty. Pro jazykové verze (pl/, hu/, atd.) použijte `../index.html` pro návrat na root.

### Problém: Automatická detekce přesměrovává stále dokola

**Řešení:** Ujistěte se, že automatická detekce je POUZE v root index.html, ne v jazykových verzích.

### Problém: Hreflang tagy se neindexují

**Řešení:** 
1. Zkontrolujte, že URL jsou absolutní (včetně https://)
2. Ověřte v Google Search Console
3. Počkejte 2-4 týdny na přeindexování

### Problém: Kód země se neposílá do webhooku

**Řešení:** 
1. Zkontrolujte umístění - pole `country` musí být uvnitř objektu `webhookData`
2. Ověřte v DevTools, že se skutečně odesílá
3. Ujistěte se, že používáte správný kód (CZ, PL, HU, SK, RO)

## 📚 Další zdroje

- Hreflang tester: https://technicalseo.com/tools/hreflang/
- Open Graph debugger: https://developers.facebook.com/tools/debug/
- Google Search Console: https://search.google.com/search-console

## 💡 Best Practices

1. **Vždy používejte absolutní URL** v hreflang tazích
2. **Testujte na mobilních zařízeních** - jazykový přepínač musí být přístupný
3. **Používejte profesionální překladatele** pro marketingové texty
4. **Zachovejte konzistenci** v názvech složek (pl, hu, sk, ro - malá písmena)
5. **Dokumentujte změny** - poznamenejte si, co jste upravili
6. **Verzujte soubory** - použijte Git pro sledování změn

---

## 📞 Podpora

Pro otázky nebo problémy kontaktujte vývojový tým.

**Vytvořeno:** 2025-01-21
**Verze:** 1.0
**Autor:** BEWIT Development Team





