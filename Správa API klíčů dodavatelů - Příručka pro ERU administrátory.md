**Verze dokumentu:** 1.0  
**Poslední aktualizace:** 13.02.2026  
**Určeno pro:** ERU administrátoři  

---

## Obsah

1. [Úvod](#úvod)
2. [Přístup ke správě API klíčů](#přístup-ke-správě-api-klíčů)
3. [Vytvoření nového API klíče](#vytvoření-nového-api-klíče)
4. [Úprava existujícího API klíče](#úprava-existujícího-api-klíče)
5. [Další operace s API klíči](#další-operace-s-api-klíči)

---

## Úvod

### Co je API klíč dodavatele?

API klíč dodavatele je autentizační token, který umožňuje dodavateli energií přistupovat k Supplier API systému ERU. Každý API klíč:

- Je **jednoznačně přiřazen jednomu dodavateli**
- Má **definovaná oprávnění**, která určují, jaké operace může provádět
- Má **nastavenou dobu platnosti** (neomezenou, časově omezenou, nebo jednorázovou)
- Může být **odvolán nebo smazán** administrátorem

### Proč spravovat API klíče?

Správa API klíčů je klíčová pro:
- **Bezpečnost** - kontrola přístupu k citlivým datům a operacím
- **Audit** - sledování, kdo a kdy přistupuje k API
- **Flexibilita** - možnost měnit oprávnění bez nutnosti vytvářet nové klíče
- **Údržba** - odvolávání nebo mazání kompromitovaných či nepoužívaných klíčů

---

## Přístup ke správě API klíčů

### Navigace v systému

1. Po přihlášení do administračního rozhraní ERU najděte v levé postranní nabídce sekci **"API dodavatelů"**
2. Klikněte na položku **"API klíče"**

Otevře se stránka se seznamem všech API klíčů v systému.

### Přehled API klíčů

Tabulka zobrazuje následující informace o každém API klíči:

| Sloupec | Popis |
|---------|-------|
| **Dodavatel** | Název dodavatele, kterému je API klíč přiřazen |
| **Oprávnění** | Seznam oprávnění, která má tento API klíč povolena |
| **Stav** | Aktuální stav klíče (Aktivní, Zrušený, Expirovaný, atd.) |
| **Délka platnosti** | Typ platnosti (Neomezená, Časově omezená, Jednorázová) |
| **Akce** | Dostupné operace s API klíčem |

### Dostupné akce

Pro každý API klíč jsou k dispozici následující akce:

- **Zkopírovat API klíč** - zkopíruje token do schránky (pro předání dodavateli)
- **Zrušit** - odvolá API klíč (klíč již nebude funkční)
- **Edit** - upraví oprávnění API klíče
- **Delete** - trvale smaže API klíč ze systému

---

## Vytvoření nového API klíče

### Postup vytvoření

1. Na stránce se seznamem API klíčů klikněte na tlačítko **"Create"** v pravém horním rohu
2. Vyplňte formulář pro vytvoření nového API klíče
3. Po vyplnění všech povinných údajů klikněte na tlačítko **"Vytvořit"**

### Formulářová pole

#### 1. Dodavatel
- Výběr dodavatele z rozbalovacího seznamu
- Povinné pole

> **Poznámka:** Po vytvoření API klíče nelze změnit přiřazení k dodavateli. Pro jiného dodavatele je třeba vytvořit nový API klíč.

#### 2. Oprávnění
- Výběr oprávnění API klíče
- Povinné pole - musí být zaškrtnutýý aspoň jeden checkbox

**Dostupná oprávnění:**

| Oprávnění | Popis | Kdy použít |
|-----------|-------|------------|
| **Import produktů a ceníků** | Umožňuje nahrávat a aktualizovat produkty a ceníky | Pro dodavatele, kteří budou aktivně spravovat své nabídky |
| **Čtení produktů** | Umožňuje pouze číst informace o produktech | Pro systémy, které potřebují kontrolovat stav produktů |
| **Čtení spotových cen** | Umožňuje číst aktuální spotové ceny elektřiny | Pro dodavatele používající spotové ceny ve svých produktech |

> **Bezpečnostní tip:** Přidělujte pouze ta oprávnění, která dodavatel skutečně potřebuje (princip nejmenších oprávnění).

#### 3. Doba platnosti
- Volba typu platnosti API klíče
- Povinné pole

**Dostupné možnosti:**

##### a) Neomezená (Unlimited)
- API klíč **nikdy nevyprší**
- Platný, dokud není explicitně odvolán nebo smazán

##### b) Časově omezená (TimeLimited)
- API klíč má **definované datum vypršení**
- Po uplynutí data přestane být funkční
- Vyžaduje vyplnění pole **"Platné do"**

##### c) Jednorázová (OneTime)
- API klíč lze **použít pouze jednou**
- Po použití se automaticky deaktivuje
- Volitelně lze omezit časovou platnost zaškrtnutím **"Omezit jednorázový token"** a vyplněním pole **"Platné do"**

#### 5. Platné do

- **Typ pole:** Výběr data
- **Viditelné:** 
  - Vždy při výběru "Časově omezená" doby platnosti
  - Při výběru "Jednorázová" a zaškrtnutí "Omezit jednorázový token"
- **Povinnost:** Ano (když je viditelné)
- **Popis:** Datum, do kterého bude API klíč platný
- **Formát:** YYYY-MM-DD (datum se interpretuje jako UTC)
- **Omezení:** Musí být minimálně zítřejší datum

> **Důležité:** Zadané datum se vždy interpretuje jako UTC časová zóna. Berte to v úvahu při nastavování doby platnosti.

### Validační pravidla

Systém kontroluje následující pravidla před vytvořením API klíče:

1. **Dodavatel musí být vybrán**
2. **Při "Časově omezené" platnosti musí být vyplněno "Platné do"**
3. **Při "Jednorázové" platnosti s časovým omezením musí být vyplněno "Platné do"**
4. **Datum "Platné do" musí být v budoucnosti** (minimálně zítřejší den)
5. **Alespoň jedno oprávnění musí být vybráno**

Při porušení těchto pravidel se zobrazí chybová hláška a formulář nebude odeslán.

### Po vytvoření API klíče

Po úspěšném vytvoření:

1. **Zobrazí se potvrzující zpráva** o vytvoření API klíče
2. **Budete přesměrováni zpět na seznam** API klíčů
3. **Nový API klíč se zobrazí v tabulce**
4. **Můžete zkopírovat token** kliknutím na tlačítko "Zkopírovat API klíč"

> **Důležité:** Token se generuje pouze při vytvoření a nelze jej později zobrazit. Zkopírujte jej ihned a bezpečně předejte dodavateli.

---

## Úprava existujícího API klíče

### Postup úpravy

1. V tabulce API klíčů najděte klíč, který chcete upravit
2. Klikněte na tlačítko **"Edit"** v sloupci Akce
3. Upravte oprávnění podle potřeby
4. Klikněte na tlačítko **"Upravit"** pro uložení změn

### Co lze upravit

Při úpravě existujícího API klíče můžete změnit **pouze oprávnění**.

#### Oprávnění (editovatelné)

- **Typ pole:** Zaškrtávací políčka (více možností)
- **Popis:** Můžete přidat nebo odebrat oprávnění
- **Současná oprávnění:** Zobrazí se předvyplněná podle aktuálního nastavení

**Postup změny oprávnění:**
1. Zaškrtněte oprávnění, která chcete přidat
2. Odškrtněte oprávnění, která chcete odebrat
3. Uložte změny

> **Tip:** Změny oprávnění jsou okamžité. Dodavatel může nová oprávnění používat ihned po uložení.

### Co nelze upravit

Následující vlastnosti **nelze po vytvoření změnit**:

| Vlastnost | Důvod |
|-----------|-------|
| **Dodavatel** | API klíč je trvale vázán na jednoho dodavatele |
| **Token (samotný klíč)** | Token je generován při vytvoření a nelze jej změnit |
| **Doba platnosti (typ)** | Nelze změnit z Neomezené na Časově omezenou atd. |
| **Datum "Platné do"** | Nelze prodloužit nebo zkrátit dobu platnosti |

> **Poznámka:** Pokud potřebujete změnit některou z těchto vlastností, musíte vytvořit nový API klíč a starý odvolat nebo smazat.

### Audit změn

Každá změna oprávnění je **zaznamenána do logu** s následujícími informacemi:
- Datum a čas změny
- Administrátor, který změnu provedl
- Původní oprávnění
- Nová oprávnění

---

## Další operace s API klíči

### Odvolání API klíče (Zrušit)

Odvolání API klíče ho **deaktivuje bez smazání** ze systému.

**Postup:**
1. V tabulce API klíčů najděte klíč, který chcete odvolat
2. Klikněte na tlačítko **"Zrušit"**
3. API klíč bude označen jako "Odvolaný"

**Co se stane:**
- API klíč změní stav na **Zrušený** 
- Dodavatel **již nemůže API používat**
- Záznam **zůstává v databázi** pro auditní účely
- Změna je **zaznamenána do logu**
- Operaci **nelze vrátit zpět** (klíč nelze znovu aktivovat)

> **Bezpečnostní tip:** Při podezření na kompromitaci API klíče jej okamžitě odvolejte a vytvořte nový.

### Smazání API klíče (Delete)

Smazání API klíče ho **trvale odstraní** ze systému.

**Postup:**
1. V tabulce API klíčů najděte klíč, který chcete smazat
2. Klikněte na tlačítko **"Delete"**
3. API klíč a jeho token budou trvale smazány

**Co se stane:**
- API klíč je **úplně odstraněn z databáze**
- Dodavatel **již nemůže API používat**
- Operace je **zaznamenána do logu** (než je klíč smazán)
- Operaci **nelze vrátit zpět**
- Záznam **není možné obnovit**

> **Varování:** Smazání je nevratné. Zvažte použití odvolání místo smazání, pokud chcete zachovat historii.

### Zkopírování API klíče

Zkopírování tokenu do schránky pro předání dodavateli.

**Postup:**
1. V tabulce API klíčů najděte požadovaný klíč
2. Klikněte na tlačítko **"Zkopírovat API klíč"**
3. Token je zkopírován do schránky
