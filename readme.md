# Tvorba uživatelských rozhraní v Bash (dialog)

Tento návod je určen pro **studenty střední školy** a ukazuje, jak v prostředí Linuxu
vytvářet **jednoduchá textová uživatelská rozhraní** pomocí shell skriptů v **Bash**
a utility **dialog**.

---

## 1. Proč řešit uživatelské rozhraní v Bash?

Bash skripty se běžně ovládají pomocí příkazové řádky. Pokud ale chceme, aby:
- skript byl přehlednější,
- uživatel nemusel znát příkazy,
- bylo možné vybírat z menu,

je vhodné použít **textové uživatelské rozhraní (TUI)**.

---

## 2. Jednoduché menu bez dialogu (ukázka problému)

Následující skript vytváří jednoduché menu pouze pomocí příkazů `echo` a `read`:

```bash
#!/bin/bash
while :
do
    clear
    echo "-------------------------------------"
    echo " Hlavní nabídka "
    echo "-------------------------------------"
    echo "[1] Datum a čas"
    echo "[2] Soubory v adresáři"
    echo "[3] Kalendář"
    echo "[4] Editor"
    echo "[5] Konec"
    echo "-------------------------------------"
    read -p "Zadejte volbu [1-5]: " volba

    case $volba in
        1) date; read ;;
        2) ls -l; read ;;
        3) cal; read ;;
        4) vi ;;
        5) exit 0 ;;
        *) echo "Neplatná volba"; read ;;
    esac
done
```

❌ Nevýhody:
- nepřehledný kód
- žádná grafická struktura
- špatná práce s chybami
- omezené množství prvkú (listy/switch/check listy... nutné doprogramovat) 
---

## 3. Utilita `dialog`

`dialog` je program, který umožňuje vytvářet **okna, menu, formuláře a dialogy**
přímo v terminálu.

### Instalace
Na systémech Debian/Ubuntu:
```bash
sudo apt install dialog
```


případně doinstalujeme chybějící balíček kalendář (´cal´)
```bash
sudo apt install ncal
```
---

## 4. První dialog – Infobox
<img width="692" height="497" alt="image" src="https://github.com/user-attachments/assets/92da4609-45d0-46b8-9b15-6edd5f804b3c" />

```bash
#!/bin/bash
dialog --title "Pokusný dialog" --backtitle "Můj první dialog" --infobox "Toto je informační okno." 7 50
sleep 3
```

- `7` = výška okna
- `50` = šířka okna

---

## 5. Typy dialogových oken

Obecná syntaxe:
```bash
dialog --title "Název" --backtitle "Pozadí" TYP_DIALOGU parametry
```

### Přehled:
- `--msgbox` – zpráva
- `--yesno` – ano / ne
- `--inputbox` – textový vstup
- `--menu` – menu
- `--calendar` – kalendář
- `--form` – formulář

---

## 6. MessageBox (msgbox)
<img width="692" height="497" alt="image" src="https://github.com/user-attachments/assets/fe142a55-47ee-4f89-9115-26f455171576" />

```bash
#!/bin/bash
dialog --title "Zpráva" --msgbox "Toto je důležitá informace." 7 50
```

✔ Použití: zobrazení informace

---

## 7. Yes/No dialog
<img width="692" height="497" alt="image" src="https://github.com/user-attachments/assets/5c5771fb-2104-4c01-97e5-b5f455be9288" />


```bash
#!/bin/bash
dialog --title "Otázka" --yesno "Chcete pokračovat?" 7 50

volba=$?
case $volba in
    0) echo "Ano" ;;
    1) echo "Ne" ;;
    255) echo "ESC" ;;
esac
```

✔ Použití: potvrzení akce

---

## 8. InputBox – vstup od uživatele
<img width="692" height="497" alt="image" src="https://github.com/user-attachments/assets/ad524d05-421b-4e32-81ff-4c272522aa55" />

```bash
#!/bin/bash
dialog --inputbox "Zadejte své jméno:" 7 50 2>/tmp/jmeno.$$
jmeno=$(cat /tmp/jmeno.$$)
rm -f /tmp/jmeno.$$
echo "Vítej $jmeno"
```

✔ Použití: zadávání textu

---

## 9. Menu
<img width="692" height="497" alt="image" src="https://github.com/user-attachments/assets/2a16df9d-b25c-4cf8-9105-f6aeee49091d" />

```bash
#!/bin/bash
dialog --menu "Vyberte možnost:" 15 50 3 1 "Datum a čas" 2 "Kalendář" 3 "Editor" 2>/tmp/volba.$$

volba=$(cat /tmp/volba.$$)
rm -f /tmp/volba.$$

case $volba in
    1) date ;;
    2) cal ;;
    3) vi ;;
esac
```

✔ Použití: hlavní nabídka programu

---

## 10. Kalendář
<img width="692" height="497" alt="image" src="https://github.com/user-attachments/assets/3b309a45-131b-4d05-bafa-7bbb0c4115cb" />

```bash
#!/bin/bash
dialog --calendar "Vyberte datum" 0 0 2>/tmp/datum.$$
datum=$(cat /tmp/datum.$$)
rm -f /tmp/datum.$$
echo "Vybrané datum: $datum"
```

---

## 11. Formulář (Form)
<img width="692" height="497" alt="image" src="https://github.com/user-attachments/assets/9b398954-584b-477d-ab40-84025a2514ff" />


```bash
#!/bin/bash
dialog --form "Vyplňte údaje:" 12 50 5 Jméno: 1 2 "" 1 12 20 20 Příjmení: 2 2 "" 2 12 20 20 Město: 3 2 "" 3 12 20 20 PSC: 4 2 "" 4 12 5 5 2>/tmp/form.$$

cat /tmp/form.$$
rm -f /tmp/form.$$
```

✔ Použití: zadávání více údajů najednou

---

## 12. Doporučení

- vždy používej `#!/bin/bash`
- uklízej dočasné soubory (`/tmp`)
- testuj návratové hodnoty (`$?`) zejména textové vstupy od uživatelů
- skript pište **čitelný a komentovaný**

---

## 13. Shrnutí

Pomocí utility **dialog** lze:
- vytvářet přehledná menu
- zlepšit ovladatelnost skriptů
- simulovat jednoduché aplikace v terminálu

---
