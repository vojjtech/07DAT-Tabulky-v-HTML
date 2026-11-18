# Vysvětlení řešení – Ceník aquaparku

Postup řešení krok za krokem. Čísla odpovídají úkolům v [ZADANI.md](../ZADANI.md).

## Část A – HTML

### 1. Tabulka a titulek

```html
<table>
  <caption>Ceník vstupného – Aquapark Vlnka</caption>
  ...
</table>
```

`<caption>` musí být **první** prvek uvnitř `<table>`. Není to jen nadpis nad tabulkou: je s tabulkou pevně spojený, takže ho čtečka obrazovky přečte jako její název. Obyčejný `<h2>` nad tabulkou by tohle nezajistil.

### 2. Sloupce

```html
<colgroup>
  <col span="2">
  <col span="3" class="prices">
</colgroup>
```

Tabulka má 5 sloupců. První `<col>` pokrývá sloupce Zóna a Doba, druhý tři cenové sloupce. Třída `prices` slouží v CSS k podbarvení všech cenových sloupců najednou (úkol 10). Bez `<col>` bychom museli dát třídu každé cenové buňce zvlášť.

### 3. Hlavička

```html
<thead>
  <tr>
    <th rowspan="2" scope="col">Zóna</th>
    <th rowspan="2" scope="col">Doba</th>
    <th colspan="3" scope="colgroup">Cena (Kč)</th>
  </tr>
  <tr>
    <th scope="col">Dospělý</th>
    <th scope="col">Dítě</th>
    <th scope="col">Senior</th>
  </tr>
</thead>
```

- **1. řádek:** Zóna (1) + Doba (1) + Cena (`colspan="3"`) = 5 sloupců.
- **2. řádek:** napíšeme jen 3 buňky. Zbývající 2 místa jsou obsazená buňkami Zóna a Doba, které díky `rowspan="2"` zasahují i sem. 3 + 2 = 5.
- `Cena (Kč)` je záhlaví pro **skupinu sloupců**, proto `scope="colgroup"`. Ostatní záhlaví platí pro jeden sloupec pod sebou, proto `scope="col"`.

### 4. Tělo – dvě `<tbody>`

```html
<tbody>
  <tr>
    <th rowspan="3" scope="rowgroup">Bazény</th>
    <th scope="row">1 h</th>
    <td>150</td>
    <td>90</td>
    <td>100</td>
  </tr>
  <tr>
    <th scope="row">3 h</th>
    <td>290</td>
    <td>170</td>
    <td>200</td>
  </tr>
  <tr>
    <th scope="row">celý den</th>
    <td>390</td>
    <td>230</td>
    <td>270</td>
  </tr>
</tbody>

<tbody>
  <tr>
    <th rowspan="2" scope="rowgroup">Wellness</th>
    <th scope="row">2 h</th>
    <td>350</td>
    <td>–</td>
    <td>280</td>
  </tr>
  <tr>
    <th scope="row">celý den</th>
    <td>550</td>
    <td>–</td>
    <td>450</td>
  </tr>
</tbody>
```

- **Proč dvě `tbody`:** `tbody` je skupina řádků. Bazény a Wellness jsou dvě logické skupiny, každá má tedy vlastní `tbody`. Díky tomu má smysl `scope="rowgroup"` a v CSS mezi skupiny můžeme dát oddělovací čáru (`tbody + tbody`, úkol 11).
- **`rowspan`:** buňka Bazény zabírá 3 řádky, Wellness 2. V dalších řádcích skupiny se název zóny **už nepíše**, jeho místo je obsazené. Proto mají tyto řádky jen 4 buňky.
- **`scope`:** Bazény a Wellness jsou záhlaví celé skupiny řádků (`scope="rowgroup"`), doba je záhlaví jednoho řádku (`scope="row"`).
- **`th` vs. `td`:** název zóny i doba popisují řádek, jsou to tedy záhlaví (`th`). Ceny jsou data (`td`).
- **Pomlčka:** wellness pro děti neexistuje. Buňku nevynecháváme (vznikla by díra v mřížce), ale napíšeme do ní `–`.

### 5. Patička

```html
<tfoot>
  <tr>
    <th colspan="2" scope="row">Rodinné vstupné 2+2, bazény 3 h</th>
    <td colspan="3">790 Kč</td>
  </tr>
</tfoot>
```

2 + 3 = 5 sloupců. Popis zabírá sloupce Zóna a Doba, cena všechny tři cenové sloupce, protože platí pro celou rodinu.

## Část B – CSS

Barvy jsou na začátku `style.css` připravené jako CSS proměnné v `:root` (např. `--color-primary: #0b6e8a;`) a v pravidlech se používají přes `var(--color-primary)`. Výhoda: každá barva je definovaná jen na jednom místě, takže když ji změníte v `:root`, změní se všude, kde se proměnná používá.

### 6. Tabulka

```css
table {
  width: 100%;
  border-collapse: collapse;
}
```

Bez `border-collapse: collapse` by měla každá buňka vlastní rámeček a mezi buňkami by byla mezera (dvojité čáry). `collapse` slije sousední rámečky do jedné čáry. Je to také podmínka pro okraje na `tbody` a `tfoot` v úkolu 11.

### 7. Titulek

```css
caption {
  caption-side: top;
  text-align: left;
  font-size: 1.2rem;
  font-weight: bold;
  padding-bottom: 12px;
}
```

`caption-side: top` je výchozí hodnota, zapsáním ji ale ukážeme, že víme, kde se umístění titulku nastavuje (`bottom` by ho dal pod tabulku). Titulek je standardně na střed, proto `text-align: left`.

### 8. Buňky

```css
th,
td {
  border: 1px solid var(--color-border);
  padding: 8px 12px;
}
```

Rámeček dáváme na buňky, ne jen na `table`: rámeček na `table` by orámoval jen tabulku jako celek. `padding` nahrazuje zastaralý atribut `cellpadding`.

### 9. Záhlaví

```css
thead th {
  background-color: var(--color-primary);
  color: var(--color-white);
  text-align: center;
}

tbody th,
tfoot th {
  text-align: left;
}

th[scope="rowgroup"] {
  vertical-align: top;
}
```

- `th` je standardně tučně a **na střed**. Záhlaví řádků (zóna, doba, rodinné vstupné) se lépe čtou zarovnaná vlevo.
- Buňka sloučená přes `rowspan` je standardně svisle uprostřed (`vertical-align: middle`). U skupiny o třech řádcích by název zóny „plaval“ uprostřed, `top` ho zarovná k prvnímu řádku skupiny.
- `th[scope="rowgroup"]` je atributový selektor: vybere jen `th`, které mají `scope="rowgroup"`, tedy názvy zón.

### 10. Čísla, sloupce a zebra

```css
td {
  text-align: right;
}

col.prices {
  background-color: var(--color-prices-bg);
}

tbody tr:nth-child(even) {
  background-color: var(--color-zebra);
}
```

- Čísla se zarovnávají doprava, aby stovky, desítky a jednotky byly pod sebou.
- `text-align` **nejde** dát na `col.prices`. Na `<col>` fungují jen `background`, `border`, `width` a `visibility`, proto zarovnání nastavujeme na `td`.
- **Vrstvy pozadí:** pozadí řádku (`tr`) leží nad pozadím sloupce (`col`). Kdyby zebra měla plnou barvu, pozadí cenových sloupců by v sudých řádcích zmizelo. Proto je `--color-zebra` poloprůhledná barva `rgba(11, 110, 138, 0.08)`, která ho nechá prosvítat.
- `:nth-child(even)` počítá řádky uvnitř rodiče, takže v každém `tbody` začíná počítání znovu.

### 11. Skupiny řádků a patička

```css
tbody + tbody {
  border-top: 3px solid var(--color-primary);
}

tfoot {
  border-top: 3px double var(--color-primary);
  font-weight: bold;
  background-color: var(--color-tfoot-bg);
}
```

- `tbody + tbody` je selektor sourozence: vybere `tbody`, které následuje hned po jiném `tbody`. Čáru tak dostane jen Wellness, ne první skupina.
- Okraj na `tbody` a `tfoot` funguje jen při `border-collapse: collapse` (úkol 6). Když se potká okraj 3px s rámečkem buňky 1px, vyhraje širší.
- `double` vykreslí dvojitou čáru, jak bývá zvykem nad součty a souhrny.
