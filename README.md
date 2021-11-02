# IOS2021
VUT FIT IOS project 1

1. Úloha IOS (2021)
Popis úlohy
Cílem úlohy je vytvořit skript pro analýzu záznamu systému pro obchodování na burze. Skript bude filtrovat záznamy a poskytovat statistiky podle zadání úživatele.

Zjednodušený úvod do problematiky
Na burze se obchoduje s cennými papíry (např. akcie společností, dluhopisy), komoditami (např. ropa, zelí) apod. Každý obchodovaný artikl má jednoznačný identifikátor, tzv. ticker (např. akcie firmy Intel mají na burze NASDAQ ticker INTC, bitcoin může mít přiřazený ticker BTC). Cena artiklů se mění v čase. Obchodník na burze vstupuje do pozic, buď tak, že koupí artikl a očekává, že jeho cena poroste, aby jej pak prodal za vyšší cenu (tzv. dlouhá pozice), nebo že artikl prodá a očekává, že jeho cena klesne, aby jej poté mohl koupit levněji (tzv. krátká pozice). Obchodník může prodat i artikl, který právě nevlastní (v reálu to funguje tak, že si ho od někoho, kdo ho vlastní, “vypůjčí”, prodá jej, a potom ho koupí za nižší cenu a “vrátí”). V našem případě budeme uvažovat, že obchodníkův systém posílá na burzu příkazy k nákupu (buy) nebo prodeji (sell) určitého množství jednotek artiklu označeného nějakým tickerem.

Specifikace rozhraní skriptu
JMÉNO

tradelog - analyzátor logů z obchodování na burze
POUŽITÍ

tradelog [-h|--help] [FILTR] [PŘÍKAZ] [LOG [LOG2 [...]]
VOLBY

PŘÍKAZ může být jeden z:
list-tick – výpis seznamu vyskytujících se burzovních symbolů, tzv. “tickerů”.
profit – výpis celkového zisku z uzavřených pozic.
pos – výpis hodnot aktuálně držených pozic seřazených sestupně dle hodnoty.
last-price – výpis poslední známé ceny pro každý ticker.
hist-ord – výpis histogramu počtu transakcí dle tickeru.
graph-pos – výpis grafu hodnot držených pozic dle tickeru.
FILTR může být kombinace následujících:
-a DATETIME – after: jsou uvažovány pouze záznamy PO tomto datu (bez tohoto data). DATETIME je formátu YYYY-MM-DD HH:MM:SS.
-b DATETIME – before: jsou uvažovány pouze záznamy PŘED tímto datem (bez tohoto data).
-t TICKER – jsou uvažovány pouze záznamy odpovídající danému tickeru. Při více výskytech přepínače se bere množina všech uvedených tickerů.
-w WIDTH – u výpisu grafů nastavuje jejich šířku, tedy délku nejdelšího řádku na WIDTH. Tedy, WIDTH musí být kladné celé číslo. Více výskytů přepínače je chybné spuštění.
-h a --help vypíšou nápovědu s krátkým popisem každého příkazu a přepínače.
Popis
Skript filtruje záznamy z nástroje pro obchodování na burze. Pokud je skriptu zadán také příkaz, nad filtrovanými záznamy daný příkaz provede.
Pokud skript nedostane ani filtr ani příkaz, opisuje záznamy na standardní výstup.
Skript umí zpracovat i záznamy komprimované pomocí nástroje gzip (v případě, že název souboru končí .gz).
V případě, že skript na příkazové řádce nedostane soubory se záznamy (LOG, LOG2 …), očekává záznamy na standardním vstupu.
Pokud má skript vypsat seznam, každá položka je vypsána na jeden řádek a pouze jednou. Není-li uvedeno jinak, je pořadí řádků dáno abecedně dle tickerů. Položky se nesmí opakovat.
Grafy jsou vykresleny pomocí ASCII a jsou otočené doprava. Každý řádek histogramu udává ticker. Kladná hodnota či četnost jsou vyobrazeny posloupností znaku mřížky #, záporná hodnota (u graph-pos) je vyobrazena posloupností znaku vykřičníku !.
Podrobné požadavky
Skript analyzuje záznamy (logy) pouze ze zadaných souborů v daném pořadí.

Formát logu je CSV kde oddělovačem je znak středníku ;. Formát je řádkový, každý řádek odpovídá záznamu o jedné transakci ve tvaru

DATUM A CAS;TICKER;TYP TRANSAKCE;JEDNOTKOVA CENA;MENA;OBJEM;ID
kde

DATUM A CAS jsou ve formátu YYYY-MM-DD HH:MM:SS
TICKER je řetězec neobsahující bílé znaky a znak středníku
TYP TRANSAKCE nabývá hodnoty buy nebo sell značící nákup resp. prodej
JEDNOTKOVA CENA je cena za jednu akcii, jednotku komodity, atp. s přesností na maximálně dvě desetinná místa; jako oddělovač jednotek a desetin slouží znak tečky .; Např. 1234567.89
MENA je třípísmenný kód měny, např USD, EUR, CZK, SEK, GBP atd.
OBJEM značí množství jednotek (akcií, jednotek komodity atp.) v transakci
ID je identifikátor transakce (řetězec neobsahující bílé znaky a znak středníku)
Hodnota transakce je JEDNOTKOVA CENA * OBJEM. Příklad záznamů:

2021-07-29 23:43:13;TSM;buy;667.90;USD;306;65fb53f6-7943-11eb-80cb-8c85906a186d
2021-07-29 23:43:15;BTC;sell;50100;USD;5;65467d26-7943-11eb-80cb-8c85906a186d
První záznam značí nákup 306 akcií firmy TSMC (ticker TSM) za cenu 667.90 USD / akcie. Hodnota transakce je tedy 204377.40 USD.
Druhý záznam značí prodej 5 bitcoinů (ticker BTC) za cenu 50 100 USD / bitcoin. Hodnota transakce je tedy 250500.00 USD.
Předpokládejte, že měna je u všech záznamů stejná (není potřeba ověřovat).

Skript žádný soubor nemodifikuje. Skript nepoužívá dočasné soubory.

Můžete předpokládat, že záznamy jsou ve vstupních souborech uvedeny chronologicky a je-li na vstupu více souborů, jejich pořadí je také chronologické.

Celkový zisk z uzavřených pozic (příkaz profit) se spočítá jako suma hodnot sell transakcí - suma hodnot buy transakcí.

Hodnota aktuálně držených pozic (příkazy pos a graph-pos) se pro každý ticker spočítá jako počet držených jednotek * jednotková cena z poslední transakce, kde počet držených jednotek je dán jako suma objemů buy transakcí - suma objemů sell transakcí.

Pokud není při použití příkazu hist-ord uvedena šířka WIDTH, pak každá pozice v histogramu odpovídá jedné transakci.

Pokud není při použití příkazu graph-pos uvedena šířka WIDTH, pak každá pozice v histogramu odpovídá hodnotě 1000 (zaokrouhleno na tisíce směrem k nule, tj. hodnota 2000 bude reprezentována jako ## zatímco hodnota 1999.99 jako # a hodnota -1999.99 jako !.

U příkazů hist-ord a graph-pos s uvedenou šířkou WIDTH při dělení zaokrouhlujte směrem k nule (tedy např. při graph-pos -w 6 a nejdelším řádku s hodnotou 1234 bude řádek s hodnotou 1234 vypadat takto ######, řádek s hodnotou 1233.99 takto ##### a řádek s hodnotou -1233.99 takto !!!!!).

Pořadí argumentů stačí uvažovat takové, že nejřív budou všechny přepínače, pak (volitelně) příkaz a nakonec seznam vstupních souborů (lze tedy použít getopts). Podpora argumentů v libovolném pořadí je nepovinné rozšíření, jehož implementace může kompenzovat případnou ztrátu bodů v jiné časti projektu.

Předpokládejte, že vstupní soubory nemůžou mít jména odpovídající některému příkazu nebo přepínači.

V případě uvedení přepínače -h nebo --help se vždy pouze vypíše nápověda a skript skončí (tedy, pokud by za přepínačem následoval nějaký příkaz nebo soubor, neprovede se).

Při výpisu pomocí příkazů pos, last-price, hist-ord a graph-pos musí být tickery zarovnány doleva a dvojtečka na 11. pozici na řádku (výplň proveďte pomocí mezer). U příkazů hist-ord a graph-pos je za dvojtečkou na všech řádcích právě jedna mezera (případně žádná, pokud v pravém sloupci daného řádku nic není), u příkazů pos a last-price jsou hodnoty v pravé části výpisu formátovány tak, aby (v případě neprázdného výpisu) byla na řádku s nejdelší řetězcovou reprezentací hodnoty (tj. včetně znaménka) mezi dvojtečkou a hodnotou právě jedna mezera a ostatní řádky byly zarovnány doprava vzhledem k délce tohoto řádku (vizte příklady výpisů níže).

Návratová hodnota
Skript vrací úspěch v případě úspěšné operace. Interní chyba skriptu nebo chybné argumenty budou doprovázeny chybovým hlášením a neúspěšným návratovým kódem.
Implementační detaily
Skript by měl mít v celém běhu nastaveno POSIXLY_CORRECT=yes.

Skript by měl běžet na všech běžných shellech (dash, ksh, bash). Pokud použijete vlastnost specifickou pro nějaký shell, uveďte to pomocí direktivy interpretu na prvním řádku souboru, např. #!/bin/bash nebo #!/usr/bin/env bash pro bash. Můžete použít GNU rozšíření pro sed či awk. Jazyky Perl, Python, Ruby, atd. povoleny nejsou.

UPOZORNĚNÍ: některé servery, např. merlin.fit.vutbr.cz, mají symlink /bin/sh -> bash. Ověřte si proto, že skript skutečně testujete daným shellem. Doporučuji ověřit správnou funkčnost pomocí virtuálního stroje níže.

Skript musí běžet na běžně dostupných OS GNU/Linux, BSD a MacOS. Studentům je k dispozici virtuální stroj s obrazem ke stažení zde: http://www.fit.vutbr.cz/~lengal/public/trusty.ova (pro VirtualBox, login: trusty / heslo: trusty), na kterém lze ověřit správnou funkčnost projektu.

Skript nesmí používat dočasné soubory. Povoleny jsou však dočasné soubory nepřímo tvořené jinými příkazy (např. příkazem sed -i).

Čísla vypisujte v desítkovém zápisu s přesností na dvě desetinná místa. Pozor, některé nástroje (např. awk) mohou větší čísla vypisovat implicitně pomocí vědeckého zápisu.
