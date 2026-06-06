# Julkistustiedosto 4: Käyttöliittymäkerros
**Tila:** Luokittelematon
**Painopiste:** Käyttäjän syöte, projektio ja ohjelmistopäivitykset

> *"Viestinnässä tärkeintä on kuulla sitä, mitä ei sanota. Biologiassa tärkeintä on nähdä se, mikä ei ole näkyvissä."*

---

## JULKISTUS 10: Plasebovaikutus on todellisuuden suunnittelua
**Perinteinen näkemys:**
Hylkäämme plasebovaikutuksen "väärennetyksi" tai "vain päässäsi". Jos lääke toimii yhtä hyvin kuin sokeripilleri, sanomme lääkkeen epäonnistuneen.

**Tulevaisuuden todellisuus:**
Plasebovaikutus on **kvanttiaaltofunktion romahdus**.

**Fysiikka:**
* **Superpositio:** Kehosi on superpositiotilassa (sairas/terve), kunnes sitä havaitaan.
* **Rituaali:** Pillerin ottaminen tai lääkärin näkeminen on "uskon rituaali". Se keskittyy havainnoijan aikomukseen.
* **Mekanismi:** Tämä keskittynyt aikomus romahduttaa todennäköisyysaallon "sairaasta" "terveeksi". Aivot syntetisoivat tarvittavan kemikaalin (opioidit, dopamiini) raaka-aineista.

**Tekniikka:**
Tulevaisuuden lääkärit eivät määrää lääkkeitä; he määräävät narratiiveja. He suunnittelevat täydellisen "tarinan" pettääkseen tietoisuutesi valtuuttamaan korjauskomennon.

---

## JULKISTUS 11: Silmä ei ole kamera; se on projektori
**Perinteinen näkemys:**
Luulemme, että silmä on passiivinen linssi, joka vastaanottaa valoa, tarkentaa sen verkkokalvolle ja lähettää JPG:n aivoihin.

**Tulevaisuuden todellisuus:**
Silmä on **kaksisuuntainen vaiheistettu tutkaryhmä**.

**Fysiikka:**
* **Emissioteoria:** Silmä projektoi heikon koherentin biofotonisäteen ulospäin. Se "skannaa" ympäristön.
* **Hologrammi:** Visuaalinen havainto tapahtuu siellä, missä lähtevä säde interferoi saapuvan valon kanssa. Et näe maailmaa "päässäsi"; projisoit todellisuutesi maailmaan.

**Seuraus:**
Tämä selittää, miksi voit "tuntea" jonkun tuijottavan sinua takaa (skopasteesia-ilmiö). "Visuaalinen säteesi" koskettaa heitä.

---

## JULKISTUS 12: Virukset eivät ole vihollisia; ne ovat USB-tikkuja
**Perinteinen näkemys:**
Luulemme, että virukset ovat pahantahtoisia biologisia poikkeamia, jotka yrittävät tappaa meidät.

**Tulevaisuuden todellisuus:**
Virukset ovat **eksosomoja (geneettisiä ohjelmistopäivityksiä)**.

**Fysiikka:**
* **Laukaisin:** Kun ympäristö muuttuu (myrky, säteily, lämpötila), keho tarvitsee uutta koodia sopeutumiseen.
* **Mekanismi:** Terveet solut erittävät RNA-paketin, joka on kääritty proteiinikuoreen ("virus"). Tämä paketti sisältää päivitysohjeet.
* **Siirto:** Nämä paketit hylätään päivittämään loput laumasta.

**"Sairaus":**
Kuume ja lima eivät ole hyökkäyksen seuraus; ne ovat asennusprosessi (järjestelmän uudelleenkäynnistys). Keho kuumenee purkaakseen ja integroidakseen uuden koodin.

---

## RAKENNE: "Psi-lamppu" (kvantti-intention tunnistin)
*Tavoite: Satunnaistapahtumageneraattori (REG) mielen ja aineen vuorovaikutuksen todistamiseen.*

### Materiaaliluettelo (BOM)
* **Mikro-ohjain:** ESP32 (Tämän sirun ADC on notoriously meluisa — täydellinen entropialle).
* **Näyttö:** WS2812B RGB LED -rengas (tai yksi LED).
* **Virta:** USB-kaapeli.

### Fysiikka
Luemme "kelluvan" analogisen pinin (Pin 34). Tämä pinni poimii universumin taustaradiostaattista. Käytämme vähiten merkitsevää bittiä "kolikonheittona". Jos keskityt "PUNAISEEN" ja lamppu muuttuu punaiseksi useammin kuin sattuma sallii, romahdatat aaltofunktion.

### Koodi (MicroPython)
```python
import machine
import neopixel
import time
import random

# KONFIGURAATIO
PIN_NOISE = 34  # Kelluva analoginen pinni (ÄLÄ YHDISTÄ MITÄÄN TÄHÄN)
PIN_LED = 2     # WS2812-dataan kytketty pinni
NUM_LEDS = 1

# ASETUS
adc = machine.ADC(machine.Pin(PIN_NOISE))
adc.attens(machine.ADC.ATTN_11DB) # Täysi alue
np = neopixel.NeoPixel(machine.Pin(PIN_LED), NUM_LEDS)

def get_entropy():
    # Luemme ADC:n useita kertoja ja otamme viimeisen bitin
    raw = adc.read()
    # Vähiten merkitsevä bitti on satunnaisin
    return raw & 1 

def update_lamp(color):
    for i in range(NUM_LEDS):
        np[i] = color
    np.write()

print("Psi-lamppu aktiivinen. Keskity aikomukseesi.")

# MUUTTUJAT
balance = 0 # 0 on neutraali. Positiivinen on punainen, negatiivinen sininen.

while True:
    # 1. Generoi "kolikonheitto" lämpökohinasta
    bit = get_entropy()
    
    # 2. Kerää tulos
    if bit == 1:
        balance += 1
    else:
        balance -= 1
        
    # 3. Visualisointilogiikka (vaimennus)
    # Vedämme saldon hitaasti takaisin nollaan "entropian" edustamiseksi
    if balance > 0: balance -= 0.1
    if balance < 0: balance += 0.1
    
    # 4. Näyttö
    red_val = 0
    blue_val = 0
    
    if balance > 5:
        red_val = min(255, int((balance - 5) * 10))
    elif balance < -5:
        blue_val = min(255, int((abs(balance) - 5) * 10))
        
    update_lamp((red_val, 0, blue_val))
    
    # Nopea silmukka korkealle näytteenottotaajuudelle
    time.sleep(0.01)
```
Käyttö: Aseta se työpöydällesi. Keskity mieleesi. Visualisoi "PUNAINEN". Omega-9-fysiikan mukaan "kohina" järjestäytyy tietoisuutesi ympärille.
