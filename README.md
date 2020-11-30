# simpleshop-zasilkovna

Integrace výběru pobočky Zásilkovny do Simpleshop formuláře

## Co je potřeba

- Účet pro Simpleshop (https://www.simpleshop.cz/)
- Účet pro Zásilkovnu (https://www.zasilkovna.cz/)

## Konfigurace v Simpleshopu

### Vytvoření variant dopravy

K produktu vytvořte položky doplňkového prodeje pro dopravu. Jedna z položek bude představovat dodání přes Zásilkovnu. 

![Ukázka nastavení doplňkového prodeje](https://github.com/MiliusCZ/simpleshop-zasilkovna/blob/main/doplnkovy%20prodej.png?raw=true)

### Vytvoření políčka ve formuláři

V záložce *Formulář* v úpravě produktu vytvořte nové pole typu *Jednořádkový text*. Název je libovolný - například **Výdejna zásilkovny**

### Kód

Kód integrace vložte v úpravě produktu do záložky *Ostatní*, *pole JS, CSS a jiné kódy*. Před vložením je třeba v kódu upravit několik položek.
Hodnoty je třeba vyčíst ze zdrojového kódu stránky s formulářem (například pomocí pravého kliku na pole a vybrání položky *prozkoumat*)

*inputName* - název input elementu pole **Výdejna zásilkovny** 
*inputName* - název hidden input elementu pole **Výdejna zásilkovny** 
*transportSelectorName* - název atributu pro výběr doplňkového prodeje 
*zasilkovnaValue* - pořadí zásilkovny v seznamu doplňkového prodeje. Počítá se od nuly, takže například bude-li Zásilkovna na druhém místě, hodnota je "1"

Nahraďte řetězec YOURAPIKEY v posledním řádku skriptu Vaším API klíčem, který najdete na portálu Zásilkovny

```
<script type="text/javascript">
/* CONFIG PROPERTIES BEGIN */
var inputName = 'payment[-::jIzui4tg::value]';
var hiddenFieldName = 'payment[-::jIzui4tg::name]';
var transportSelectorName = 'payment[items::1::key_radio]';
var zasilkovnaValue = "2";
/* CONFIG PROPERTIES END */

var zasilkovnaInput = document.querySelector("input[name='" + inputName + "']");
var zasilkovnaHidden = document.querySelector("input[name='" + hiddenFieldName + "']");

var zasilkovnaParent = zasilkovnaInput.parentNode.parentNode;

var selectZasilkovnaButton = document.createElement('button');
selectZasilkovnaButton.textContent = 'Vybrat pobočku';
selectZasilkovnaButton.setAttribute('class', 'packeta-selector-open');
selectZasilkovnaButton.setAttribute('type', 'button');

zasilkovnaInput.parentNode.insertBefore(selectZasilkovnaButton, zasilkovnaInput);
zasilkovnaInput.setAttribute('class', 'packeta-selector-branch-name');
zasilkovnaHidden.setAttribute('class', 'packeta-selector-branch-id');

zasilkovnaInput.setAttribute('readonly', 'readonly');
zasilkovnaInput.parentNode.style.display = "flex";

console.log(document.querySelector("input[name='" + transportSelectorName + "']").value);
var showHideZasilkovna = function (value) {
   var state = value === zasilkovnaValue ? "block" : "none";
   zasilkovnaParent.style.display = state;

   zasilkovnaInput.value = state === "block" ? "" : "N/A";
}

var transportSelectors = document.querySelectorAll("input[name='" + transportSelectorName + "']");
var zasilkovnaSelected = false;
for (var i = 0; i < transportSelectors.length; i++) {
   transportSelectors[i].addEventListener('change', function () { showHideZasilkovna(this.value); });
   if ((transportSelectors[i].value === zasilkovnaValue) && !transportSelectors[i].checked) {
     zasilkovnaParent.style.display = "none";
   }
}

</script>

<script>
   var packetaSelectorOpen = '.packeta-selector-open';
   var packetaSelectorBranchName = '.packeta-selector-branch-name';
   var packetaSelectorBranchId = '.packeta-selector-branch-id';
   var packetaCountry = 'cz';
   var packetaWidgetLanguage = 'cs';

   var packetaPrimaryButtonColor = '#39b54a';
   var packetaBackgroundColor = '#ffffff';
   var packetaFontColor = '#555555';
   var packetaFontFamily = 'Arial';
</script>

<script src="https://widget.packeta.com/www/js/packetaWidget.js" data-api-key="YOURAPIKEY"></script>
```

## Závěrem

Po uložení by měla integrace fungovat. Postup je třeba zopakovat pro každý produkt v Simpleshopu, který lze zasílat přes Zásilkovnu. Pokud potřebujete pomoc s nastavením, ozvěte se mi (https://milosturek.cz).
