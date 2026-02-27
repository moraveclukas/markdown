**Verze dokumentu:** 1.0  
**Poslední aktualizace:** 13.02.2026  
**Určeno pro:** Dodavatelé energií  

---

## Obsah

1. [Úvod](#úvod)
2. [Začínáme](#začínáme)
3. [Autentizace a oprávnění](#autentizace-a-oprávnění)
4. [Omezení rychlosti (Rate Limiting)](#omezení-rychlosti-rate-limiting)
5. [Příklady použití](#příklady-použití)
6. [Chybové stavy](#chybové-stavy)
7. [Časté problémy a řešení](#časté-problémy-a-řešení)
8. [Best Practices](#best-practices)

---

## Úvod

Tento soubor slouží pouze jako návod, jak pracovat s ERU Supplier API.
Pro podrobný přehled dostupných API endpointů přejděte prosím na https://eru-supplier-v1.apidog.io/.

### Co je ERU Supplier API?

ERU Supplier API je rozhraní, které umožňuje dodavatelům energií automatizovaně spravovat své produkty a ceníky v systému ERU. API poskytuje bezpečný a efektivní způsob, jak:

- **Vytvářet a aktualizovat produkty** (elektřina, plyn)
- **Spravovat ceníky** pro různé typy odběrů
- **Nahrávat dokumenty** (smlouvy, obchodní podmínky, ceníky)
- **Kontrolovat stav produktů** a jejich verzí

### Kdo může API používat?

API je určeno pro:
- Registrované dodavatele energií v systému ERU
- Technické týmy dodavatelů s přidělenými API klíči
- Integrační systémy dodavatelů

### Architektura API

API je postaveno na následujících principech:
- **REST architektura** - standardní HTTP metody (GET, POST, PUT)
- **JSON formát** - všechna data jsou přenášena ve formátu JSON
- **Autentizace pomocí API klíče** - každý požadavek musí obsahovat platný API klíč
- **Oprávnění na úrovni akcí** - granulární kontrola přístupu k jednotlivým operacím
- **Rate limiting** - ochrana před nadměrným používáním

---

## Začínáme

### Předpoklady

Před začátkem práce s API budete potřebovat:

1. **Registrovaný účet dodavatele** v systému ERU
2. **Platný API klíč** vystavený ERU administrátorem
3. **Přidělená oprávnění** pro požadované operace
4. **Vývojové prostředí** schopné provádět HTTP požadavky

### Získání API klíče

API klíč získáte následujícím způsobem:

1. Kontaktujte správce systému ERU
2. Požádejte o vytvoření API klíče pro vašeho dodavatele
3. Správce vám vystaví API klíč s potřebnými oprávněními
4. API klíč obdržíte zabezpečeným způsobem

**DŮLEŽITÉ:** API klíč uchovávejte v bezpečí a nikdy jej nesdílejte veřejně.

### Základní URL

Všechny API endpointy jsou dostupné na následující základní URL:

```
https://{base-URL}/supplier-api
```

Kde `{base-URL}` se liší podle typu prostředí:
- Produkční prostředí: `webadmin.novysrovnavac.eru.gov.cz`
- Testovací prostředí: `dev1-admin.eru.client.puxdesign.cz`

### Formát požadavků

Všechny požadavky musí:
- Obsahovat API klíč v hlavičce `x-eru-api-key`
- Pro POST/PUT požadavky obsahovat `Content-Type: application/json-patch+json`
- Používat správnou HTTP metodu (GET, POST, PUT)

**Příklad hlavičky požadavku:**
```http
POST /product
'x-eru-api-key': 'váš-api-klíč'
'Content-Type': 'application/json-patch+json'

```

---

## Autentizace a oprávnění

### Autentizace pomocí API klíče

Každý požadavek na API musí být autentizován pomocí API klíče. Klíč se předává v HTTP hlavičce:

```http
x-eru-api-key: váš-api-klíč
```

**Bezpečnostní doporučení:**
- Ukládejte API klíč v zabezpečeném úložišti (např. environment variables, Azure Key Vault)
- Nikdy API klíč nezapisujte přímo do zdrojového kódu
- Pravidelně provádějte rotaci API klíčů
- Okamžitě nahlaste kompromitovaný API klíč

### Systém oprávnění

API používá granulární systém oprávnění. Každá operace vyžaduje specifické oprávnění:

| Oprávnění | Popis | Potřebné pro |
|-----------|-------|--------------|
| `Čtení ceníků` | Získání existujících ceníků | GET endpointy pro ceníky |
| `Čtení produktů` | Získání existujících produktů | GET endpointy pro produkty |
| `Import produktů a ceníků` | Vytváření nových/aktualizace existujících produktů a ceníků, nahrávání dokumentů | POST, PUT endpointy |

---

## Omezení rychlosti (Rate Limiting)

### Přehled

API implementuje rate limiting pro zajištění spravedlivého přístupu a ochrany před nadměrným používáním. Každý API klíč je omezen na určitý počet požadavků v časovém období.

### Limity

**Aktuální nastavení:**
- **100 požadavků** za **20 sekund**
- Používá se Fixed Window strategie
- Žádná fronta - přebytečné požadavky jsou okamžitě odmítnuty

### Hlavičky odpovědi

Při překročení limitu API vrací:

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 15
Content-Type: text/plain

Too many requests. Please try again later.
```

**Význam hlaviček:**
- `Retry-After` - počet sekund, po kterých můžete zkusit požadavek znovu

## Příklady použití

### Příklad 1: Kompletní workflow - Vytvoření produktu elektřiny
Uvedené hodnoty jsou jen ilustrační a nemusejí dávat smysl - ukazují pouze postup a způsob vytvoření produktu, ceníku a nahrání dokumentů.

```bash
# 1. Vytvoření produktu
curl -X POST https://{base-URL}/supplier-api/product \
  -H "x-eru-api-key: váš-api-klíč" \
  -H "Content-Type: application/json-patch+json" \
  -d '{
    "productName": "Název produktu",
    "customerCategory": "New",
    "onlineContractAvailable": true,
    "productValidityStart": "2024-03-01T00:00:00Z",
    "productCategory": "IndefiniteContract",
    "productType": "HomeElectricity",
    "contractLenght": {
      "lengthInMonths": 24
    },
    "automaticContractRenewalType": "BecomesIndefinite",
    "noticePeriod": "1 měsíc",
    "penalties": [],
    "bonuses": []
  }'

# Odpověď: "product12345"

# 2. Vytvoření ceníku pro produkt
curl -X POST https://{base-URL}/supplier-api/home-electricity/price-list \
  -H "x-eru-api-key: váš-api-klíč" \
  -H "Content-Type: application/json-patch+json" \
  -d '{
    "productId": "product12345",
    "priceListName": "Název ceníku",
    "validityPeriod": {
      "validFrom": "2024-03-01T00:00:00Z"
    },
    "priceFrames": [
      {
        "priceFrameType": "Standard",
        "priceValidity": {
          "validFrom": "2024-03-01T00:00:00Z"
        },
        "framePrices": [
          {
            "highTarrifPrice": 123.45,
            "fixedPayment": 67.89
          }
        ]
      }
    ]
  }'

# Odpověď: "priceList12345"

# 3. Nahrání smlouvy
curl -X POST https://{base-URL}/supplier-api/product/product12345/contract \
  -H "x-eru-api-key: váš-api-klíč" \
  -F "smlouva.pdf"

# 4. Nahrání obchodních podmínek
curl -X POST https://{base-URL}/supplier-api/product/product12345/terms-of-service \
  -H "x-eru-api-key: váš-api-klíč" \
  -F "obchodni_podminky.pdf"

# 5. Nahrání dokumentu ceníku
curl -X POST https://{base-URL}/supplier-api/price-list/priceList12345/upload-price-list-document \
  -H "x-eru-api-key: váš-api-klíč" \
  -F "cenik.pdf"
```

## Chybové stavy

### HTTP stavové kódy

API používá standardní HTTP stavové kódy pro indikaci výsledku požadavku:

| Kód | Název | Popis |
|-----|-------|-------|
| 200 | OK | Požadavek byl úspěšně zpracován |
| 400 | Bad Request | Neplatná struktura požadavku nebo validační chyby |
| 401 | Unauthorized | Chybí hlavička `x-eru-api-key` s API klíčem |
| 403 | Forbidden | API klíč je neplatný nebo nemá dostatečná oprávnění |
| 404 | Not Found | Požadovaný zdroj nebyl nalezen |
| 429 | Too Many Requests | Překročen rate limit - zkuste to znovu později |
| 500 | Internal Server Error | Interní chyba serveru |

### Formát chybové odpovědi

V případě chyby API vrací strukturovanou chybovou odpověď (ve formátu ProblemDetails podle RFC 9457 standardu https://www.rfc-editor.org/rfc/rfc9457):

```json
{
  "type": "https://datatracker.ietf.org/doc/html/rfc9110#section-15.5.1",
  "title": "Bad request",
  "status": 400,
  "detail": "Request contains invalid data.",
  "instance": "{base-URL}/supplier-api/product",
  "logRecordId": "00000000-0000-0000-0000-000000000000",
  "errors": [
    {
      "key": "ProductName",
      "value": [
        "Pole 'ProductName' je povinné"
      ]
    },
    {
      "key": "CustomerCategory",
      "value": [
        "Hodnota '10' není validní pro CustomerCategory."
      ]
    }
  ]
}
```

**Popis polí:**
- `type` - URI reference na typ problému
- `title` - Čitelné shrnutí problému
- `status` - HTTP status kód
- `detail` - Čitelný popis problému
- `instance` - URI reference, na které došlo k problému
- `logRecordId` - Jedinečný identifikátor requestu ve formátu GUID (použijte při kontaktování podpory)
- `errors` - Seznam konkrétních validačních chyb (pouze u 400 Bad Request)

---

## Časté problémy a řešení

### Problém: 400 Bad Request s validačními chybami

**Příznaky:** API vrací HTTP 400 s detailními chybami v poli `errors`

**Možné příčiny:**
1. Neplatná struktura JSON
2. Chybí povinná pole
3. Hodnoty mimo povolený rozsah
4. Neplatné enum hodnoty

**Řešení:**
- Zkontrolujte tělo požadavku podle uvedených chyb v odpovědi
- Ověřte datové typy všech polí
- Zkontrolujte, že všechna povinná pole jsou vyplněna
- Pro enum pole použijte pouze povolené hodnoty (viz API dokumentace)

**Příklad:**
```json
// Špatně - neplatná enum hodnota
{
  "customerCategory": 10  // Číselná hodnota mimo rozsah enumu
}
{
  "customerCategory": "Test"  // Neplatný název enumu
}

// Správně
{
  "customerCategory": 1  // Číselná hodnota v rozsahu enumu
}
{
  "customerCategory": "New"  // Správný název enumu
}
```

### Problém: 401 Unauthorized

**Příznaky:** Všechny požadavky vracejí HTTP 401

**Možné příčiny:**
1. Chybí hlavička `x-eru-api-key` s API klíčem
2. API klíč je prázdný nebo obsahuje pouze mezery

**Řešení:**
- Zkontrolujte, že hlavička je správně nastavena:
```bash
curl -v https://{base-URL}/supplier-api/product \
  -H "x-eru-api-key: váš-api-klíč"
```
- Ověřte, že hodnota API klíče není prázdná

### Problém: 403 Forbidden

**Příznaky:** API vrací HTTP 403 pro konkrétní operaci

**Možné příčiny:**
1. API klíč je neplatný:
   - Prázdná hodnota v hlavičce
   - Jednorázový klíč už byl použitý
   - Časově omezený klíč vypršel
   - Klíč byl revokován
2. API klíč nemá přidělené požadované oprávnění pro danou operaci

**Řešení:**
- Zkontrolujte hlavičku, že obsahuje správný klíč
- Pozor na mezery nebo neviditelné/formátovací znaky při kopírování
- Kontaktujte správce systému a požádejte o:
  - Přidělení chybějícího oprávnění
  - Vytvoření nového klíče (pokud je stávající neplatný)

### Problém: 404 Not Found

**Příznaky:** GET/PUT/POST operace vracejí HTTP 404

**Možné příčiny:**
1. Špatná URL adresa
2. Nesprávné ID produktu/ceníku
3. Produkt/ceník patří jinému dodavateli
4. Produkt/ceník byl vymazán

**Řešení:**
- Zkontrolujte doménu a cestu v URL adrese
- Ověřte, že používáte správné ID
- Zkontrolujte, že produkt/ceník existuje a patří vám
- Pro vlastní produkty použijte GET endpoint pro ověření

**Bezpečnostní poznámka:**
API záměrně vrací 404 (místo 403) pro neautorizované přístupy, aby neprozradilo existenci produktů/ceníků jiných dodavatelů.

### Problém: 429 Too Many Requests

**Příznaky:** API vrací HTTP 429 s message "Too many requests. Please try again later."

**Možné příčiny:**
1. Překročen rate limit (100 requestů za 20 sekund)
2. Burst requestů na začátku časového okna
3. Paralelní requesty z více zdrojů se stejným API klíčem
4. Chybějící pauzy mezi požadavky

**Řešení:**

**1. Respektujte Retry-After header:**
```csharp
if (response.StatusCode == HttpStatusCode.TooManyRequests)
{
    if (response.Headers.TryGetValues("Retry-After", out var values))
    {
        int retryAfter = int.Parse(values.First());
        await Task.Delay(TimeSpan.FromSeconds(retryAfter));
        // Zkuste požadavek znovu
    }
}
```

**2. Implementujte exponential backoff:**
```csharp
int retryCount = 0;
while (retryCount < maxRetries)
{
    var response = await SendRequestAsync();
    if (response.StatusCode != 429) break;
    
    await Task.Delay(TimeSpan.FromSeconds(Math.Pow(2, retryCount)));
    retryCount++;
}
```

**3. Přidejte pauzy mezi požadavky:**
```csharp
foreach (var item in items)
{
    await ProcessItemAsync(item);
    await Task.Delay(250); // 250ms pauza
}
```

**4. Request batching:**
- Seskupujte operace tam, kde je to možné
- Distribuujte požadavky rovnoměrně v čase
- Vyhněte se paralelnímu odesílání mnoha requestů najednou

**5. Cachování:**
- Cachujte GET odpovědi na straně klienta
- Minimalizujte opakované dotazy na stejná data

**6. Monitoring:**
- Sledujte počet 429 responses
- Upravte frekvenci požadavků podle potřeby
- Při trvalých problémech kontaktujte administrátora

### Problém: 500 Internal Server Error

**Příznaky:** API vrací HTTP 500 Internal Server Error

**Možné příčiny:**
1. Server narazil na neočekávanou chybu
2. Problém s databází nebo závislými službami
3. Chyba v aplikační logice

**Řešení:**
- Zkuste požadavek znovu po krátké pauze
- Pokud chyba přetrvává, kontaktujte administrátora ERU
- Poskytněte co nejvíce informací pro rychlejší diagnostiku:
  - Datum a čas volání API
  - Který endpoint byl volaný
  - Request payload (bez citlivých dat)
  - Response včetně `logRecordId` (pokud je k dispozici)
  - HTTP status kód

---

## Verzování dokumentu

| Verze | Datum | Změny | Autor |
|-------|-------|-------|-------|
| 1.0 | 13.02.2026 | Vytvoření dokumentu | ERU Team |

---

## Právní upozornění

Tento dokument je určen pouze pro registrované dodavatele energií v systému ERU. Neoprávněné použití API nebo sdílení API klíčů je zakázáno a může vést k pozastavení přístupu.

Všechna data přenášená přes API jsou chráněna dle GDPR a dalších platných předpisů o ochraně osobních údajů.

© 2025 ERU - Všechna práva vyhrazena.
