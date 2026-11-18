# 07 – Tabulky v HTML

[Zadání úlohy](ZADANI.md)

**Obsah**

- [Teorie](#teorie)
  - [1. Struktura tabulky](#1-struktura-tabulky)
  - [2. Vzhled tabulky pomocí CSS](#2-vzhled-tabulky-pomocí-css)
  - [3. Slučování buněk](#3-slučování-buněk)
- [Struktura složky](#struktura-složky)

---

# Teorie

## 1. Struktura tabulky

### Kdy tabulku použít

Tabulka (`<table>`) slouží k zobrazení **tabulkových dat**, tedy údajů, které dávají smysl v řádcích a sloupcích: ceník, rozvrh, výsledky, přehled známek.

Na **rozvržení stránky** (menu vlevo, obsah vpravo) se tabulka **nepoužívá**. Kolem roku 2000 se tak weby stavěly, dnes na to slouží CSS (flexbox, grid). Tabulkový layout je špatně přístupný pro čtečky obrazovky, těžko se upravuje a nefunguje na mobilu. Výjimkou jsou HTML e-maily, kde se kvůli starým poštovním programům tabulky na layout používají dodnes.

### Základní stavba

Tabulka se zapisuje **po řádcích**, ne po sloupcích. Sloupce vzniknou samy tím, že buňky na stejné pozici leží pod sebou.

```html
<table>
  <tr>
    <th>Jméno</th>
    <th>Třída</th>
  </tr>
  <tr>
    <td>Jana</td>
    <td>4.B</td>
  </tr>
</table>
```

| Element | Význam |
|---|---|
| `<table>` | celá tabulka |
| `<tr>` | řádek (*table row*) |
| `<th>` | záhlavní buňka (*table header*), popisuje řádek nebo sloupec; prohlížeč ji standardně zobrazí tučně a na střed |
| `<td>` | datová buňka (*table data*) |

Text se nikdy nepíše přímo do `<table>` ani do `<tr>`, vždy do buňky `<th>` nebo `<td>`.

### Titulek a sekce tabulky

| Element | Význam |
|---|---|
| `<caption>` | titulek tabulky, musí být **první** uvnitř `<table>` |
| `<colgroup>`, `<col>` | popis sloupců (viz níže) |
| `<thead>` | hlavička: řádky se záhlavím sloupců |
| `<tbody>` | tělo: skupina řádků s daty, **může jich být víc** |
| `<tfoot>` | patička: součty, souhrny, poznámky |

Pořadí uvnitř `<table>`: `caption` → `colgroup` → `thead` → `tbody` (jedno nebo více) → `tfoot`.

```html
<table>
  <caption>Známky – 4.B</caption>
  <thead>
    <tr>
      <th scope="col">Předmět</th>
      <th scope="col">Známka</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">Matematika</th>
      <td>2</td>
    </tr>
    <tr>
      <th scope="row">Programování</th>
      <td>1</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <th scope="row">Průměr</th>
      <td>1,5</td>
    </tr>
  </tfoot>
</table>
```

**Proč může být víc `<tbody>`:** `tbody` neznamená „tělo tabulky jako celek“, ale **skupinu řádků**. Když data tvoří logické skupiny (v ceníku Bazény a Wellness), dostane každá skupina vlastní `tbody`. Prohlížeč i čtečka pak vědí, které řádky k sobě patří, skupiny jde stylovat zvlášť (`tbody + tbody { border-top: … }`) a sloučená buňka (`rowspan`) nikdy nepřesáhne hranici skupiny.

**Pozor:** Když `<tbody>` nenapíšete, prohlížeč ho do stránky **doplní sám**. Proto CSS selektor `table > tr` nic nenajde: řádky jsou ve skutečnosti v `table > tbody > tr`.

### Atribut `scope`

Říká, na které buňky se záhlaví (`<th>`) vztahuje. **Vizuálně nedělá nic**, je kvůli přístupnosti: čtečka obrazovky díky němu u každé buňky přečte i její záhlaví, například „Bazény, 3 h, Dítě: 170“.

| Hodnota | Záhlaví platí pro | Příklad v ceníku |
|---|---|---|
| `col` | sloupec pod ním | „Dospělý“ |
| `row` | řádek vpravo od něj | „3 h“ |
| `colgroup` | skupinu sloupců (záhlaví sloučené přes `colspan`) | „Cena (Kč)“ |
| `rowgroup` | skupinu řádků, tedy celé `tbody` | „Bazény“ |

U hodně složitých tabulek se místo `scope` používá dvojice **`id` + `headers`**: záhlaví dostane `id` a datová buňka v atributu `headers` vyjmenuje id všech svých záhlaví.

```html
<th id="dite">Dítě</th>
...
<td headers="bazeny tri-hodiny dite">170</td>
```

Atribut `abbr` na `<th>` nabízí čtečce kratší název záhlaví: `<th abbr="Cena">Cena vstupného v Kč</th>`.

### Sloupce: `<colgroup>` a `<col>`

Sloupec v HTML jako element neexistuje, buňky patří vždy do řádků. Když potřebujeme sloupec nastylovat najednou (pozadí, šířka), popíšeme sloupce pomocí `<colgroup>` a `<col>`. Atribut `span` říká, kolik sloupců jeden `<col>` pokrývá.

```html
<colgroup>
  <col span="2">               <!-- 1. a 2. sloupec -->
  <col span="3" class="prices">  <!-- 3. až 5. sloupec -->
</colgroup>
```

Na `<col>` fungují **jen** vlastnosti `background`, `border` (jen při `border-collapse: collapse`), `width` a `visibility`. Zarovnání textu ani barva písma přes `<col>` nefungují, ty se nastavují přímo na buňky.

### Přístupnost – shrnutí

- Tabulka má `<caption>`, čtečka ho přečte jako její název.
- Záhlaví jsou v `<th>`, ne v `<td>` s tučným písmem.
- `<th>` mají `scope`, u složitých tabulek se použije `id` + `headers`.
- Tabulka se nepoužívá na rozvržení stránky.

## 2. Vzhled tabulky pomocí CSS

Bez CSS nemá tabulka žádné čáry ani odsazení. Všechno, co se dřív psalo do HTML atributů (`border="1"`, `cellpadding`…), se dnes dělá v CSS.

### Okraje: `border`, `border-collapse`, `border-spacing`

```css
th, td {
  border: 1px solid #999;
}

table {
  border-collapse: collapse;
}
```

- `border-collapse: separate` (výchozí): každá buňka má vlastní rámeček a mezi buňkami je mezera, takže čáry vypadají dvojitě. Velikost mezery určuje `border-spacing`, např. `border-spacing: 4px;`.
- `border-collapse: collapse`: rámečky sousedních buněk se slijí do jedné čáry. Když se potkají dva různé okraje, vyhraje širší. Jen v tomto režimu jde dát rámeček i na `tr`, `thead`, `tbody`, `tfoot` a `col`.

```
separate            collapse
┌───┐ ┌───┐         ┌───┬───┐
│ A │ │ B │         │ A │ B │
└───┘ └───┘         └───┴───┘
```

### Odsazení a zarovnání

| Vlastnost | Co dělá |
|---|---|
| `padding` | vnitřní odsazení buněk (dřív atribut `cellpadding`) |
| `text-align` | vodorovné zarovnání: `left`, `center`, `right`; čísla se zarovnávají **doprava**, aby jednotky, desítky a stovky byly pod sebou |
| `vertical-align` | svislé zarovnání v buňce: `top`, `middle` (výchozí), `bottom`; hodí se u buněk sloučených přes `rowspan` |
| `white-space: nowrap` | text v buňce se nezalomí |
| `font-variant-numeric: tabular-nums` | všechny číslice mají stejnou šířku, čísla ve sloupci lícují |

### Titulek

`<caption>` jde stylovat jako každý jiný element (písmo, barva, `padding`, `text-align`). Navíc má vlastnost `caption-side`:

- `caption-side: top` (výchozí): titulek nad tabulkou,
- `caption-side: bottom`: titulek pod tabulkou.

`caption` leží mimo rámeček tabulky, takže pozadí ani `border` nastavené na `table` ho nezahrnou. Pokud ho chcete podbarvit, musíte mu pozadí nastavit zvlášť.

### Šířka

- `width`: šířka tabulky nebo sloupce, např. `table { width: 100%; }`.
- `table-layout: auto` (výchozí): šířky sloupců se řídí obsahem.
- `table-layout: fixed`: šířky se řídí prvním řádkem nebo `<col>` a obsah je nezmění. Sloupce jsou předvídatelné a velká tabulka se vykreslí rychleji.

### Pozadí, zebra a zvýraznění řádku

```css
tbody tr:nth-child(even) {   /* každý sudý řádek */
  background-color: #f2f2f2;
}

tbody tr:hover {             /* řádek pod myší */
  background-color: #ffeeba;
}
```

`:nth-child` počítá řádky uvnitř rodiče, v každém `tbody` se tedy počítá znovu od 1.

**Vrstvy pozadí.** Pozadí se v tabulce vrství, spodní vrstva je první:

```
table → colgroup → col → thead / tbody / tfoot → tr → th / td
```

Vyšší vrstva zakryje nižší. Pozadí `tr` tedy překryje pozadí `col`. Pokud má pozadí sloupce prosvítat, dáme řádku poloprůhlednou barvu, např. `rgba(0, 0, 0, 0.05)`.

`empty-cells: hide` skryje rámeček a pozadí prázdných buněk (funguje jen při `border-collapse: separate`).

### Velké tabulky

- **Responzivita:** tabulku obalíme prvkem `<div>` s `overflow-x: auto;`. Na úzkém displeji se pak tabulka posouvá do strany a nerozbije stránku.
- **Lepivá hlavička:** `thead th { position: sticky; top: 0; }`. Při posouvání dlouhé tabulky zůstává hlavička nahoře.

### Zastaralé atributy a jejich náhrada v CSS

V HTML5 se **nepoužívají**, validátor je hlásí jako chybu. Vzhled patří do CSS.

| Zastaralý atribut | Náhrada v CSS |
|---|---|
| `border="1"` | `border` na `table`, `th`, `td` |
| `cellpadding` | `padding` na `th`, `td` |
| `cellspacing` | `border-spacing` nebo `border-collapse` |
| `align` | `text-align`, u celé tabulky `margin: 0 auto` |
| `valign` | `vertical-align` |
| `bgcolor` | `background-color` |
| `width`, `height` | `width`, `height` |
| `summary` | popis do `<caption>` nebo do textu kolem tabulky |
| `frame`, `rules` | `border` na konkrétních prvcích |

### Tabulkové zobrazení bez tabulky

Vlastnosti `display: table`, `display: table-row` a `display: table-cell` umí jakýkoli element vykreslit „jako tabulku“. Je to ale jen vzhled bez významu `<table>`. Pro data se proto vždy použije skutečná `<table>`, pro rozvržení stránky flexbox nebo grid.

## 3. Slučování buněk

| Atribut | Sloučí | Směr |
|---|---|---|
| `colspan="n"` | n sloupců | vodorovně → |
| `rowspan="n"` | n řádků | svisle ↓ |

Atributy se píšou na `<th>` i `<td>`.

```html
<table>
  <tr>
    <td colspan="2">A</td>   <!-- přes 2 sloupce -->
    <td rowspan="2">B</td>   <!-- přes 2 řádky -->
  </tr>
  <tr>
    <td>C</td>
    <td>D</td>
    <!-- B se tu znovu NEPÍŠE, jeho místo je už obsazené -->
  </tr>
</table>
```

```
┌───────┬───┐
│   A   │   │
├───┬───┤ B │
│ C │ D │   │
└───┴───┴───┘
```

### Pravidlo mřížky

Tabulka je mřížka. V **každém řádku** musí platit:

> součet `colspan` buněk napsaných v řádku + místa obsazená `rowspan` z řádků nad ním = počet sloupců tabulky

Buňka bez `colspan` se počítá jako 1. Kontrola na ceníku z praktické úlohy (5 sloupců):

| Řádek | Napsané buňky | Obsazeno shora | Celkem |
|---|---|---|---|
| 1. řádek hlavičky | Zóna 1 + Doba 1 + Cena `colspan=3` = 5 | 0 | 5 |
| 2. řádek hlavičky | Dospělý + Dítě + Senior = 3 | Zóna, Doba = 2 | 5 |
| Bazény, 1 h | Bazény + 1 h + 3 ceny = 5 | 0 | 5 |
| Bazény, 3 h | 3 h + 3 ceny = 4 | Bazény = 1 | 5 |
| Patička | Rodinné `colspan=2` + 790 Kč `colspan=3` = 5 | 0 | 5 |

### Pravidla a typické chyby

- `rowspan` **nepřesáhne hranici sekce**: buňka v `thead` se neroztáhne do `tbody` a buňka v jednom `tbody` nezasáhne do dalšího.
- **Buňka navíc:** řádek je delší a tabulka na konci „vyčuhuje“.
- **Chybějící buňka:** v řádku vznikne díra.
- **Sloučená buňka napsaná znovu:** při `rowspan` se buňka v dalších řádcích už nepíše. Když ji tam napíšete, všechno v řádku se posune doprava.
- Slučovat jde v hlavičce (víceúrovňová hlavička: „Cena“ nad „Dospělý / Dítě / Senior“), v těle i v patičce.

**Tip pro ladění:** dočasně dejte všem buňkám výrazný rámeček, chyby v mřížce jsou pak hned vidět:

```css
th, td { border: 1px solid red; }
```

---

# Struktura složky

```
07DAT/
├─ README.md            teorie (tento soubor)
├─ ZADANI.md            zadání praktické úlohy
├─ images/
│  ├─ vysledek.png      celá hotová stránka
│  └─ detail-tabulky.png
├─ zadani/
│  ├─ index.html        sem píšete tabulku (úkoly 1–5)
│  └─ style.css         sem píšete vzhled tabulky (úkoly 6–11)
└─ reseni/
   ├─ index.html
   ├─ style.css
   └─ VYSVETLENI.md     vysvětlení řešení krok za krokem
```
