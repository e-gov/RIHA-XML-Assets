# RIHA XML varamu

RIHA XML varamu on veebirakendus, mille kaudu tehakse avalikult kättesaadavaks asutustevahelises dokumendivahetuses kasutatavad XML varad (metaandmete andmekirjeldused).

## Varamust lähemalt

- RIHA XML varamu ülesandeks on kontrollida ja säilitada dokumendiliigi ning metaandmete andmekirjeldusi, mida edastatakse [asutustevahelise dokumendivahetuslahenduse (DVK/DHX)](https://www.ria.ee/ee/dokumendivahetus-dhx.html) kaudu.
- Kui andmekirjeldus on koostatud ja kooskõlastatud [vastavalt nõuetele](https://mkm.ee/sites/default/files/juhis_dokumendiliigi_xml_andmekirjelduse_koostamiseks.pdf), siis lähtutakse sellest seda liiki dokumentide koostamisel mistahes vormingus.
- Asutuse loodud dokumendi elementide koosseis lähtub vastava dokumendiliigi andmekirjeldusest, kui see on RIHAs registreeritud. Sellist liiki dokumendi ja selle veebivormide koostamisel võetakse aluseks andmekirjeldus.
- Dokument hoitakse alal koos dokumenti, selle seoseid ja haldamise ajalugu kirjeldavate metaandmetega.
- Dokumendi metaandmed peavad olema kooskõlas RIHAs registreeritud dokumendihalduse metaandmeloendi ja dokumendiliigi andmekirjeldusega.
- Otseste avalike teenuste osutamiseks vajaliku teabe kindlaksmääramist, jagamist ja vahetamist koordineeriv asutus on [Majandus- ja Kommunikatsiooniministeerium](https://www.mkm.ee/et/tegevused-eesmargid/infouhiskond/dokumendihaldusest-infohalduseni) ning RIHA XML varamu tegutseb [teenuste korraldamise ja teabehalduse alustel](https://www.riigiteataja.ee/akt/131052017007?leiaKehtiv).

## Kasutuslood

Kasutaja (varadega tutvuja, nende kasutaja) saab:

- sirvida varasid
- alla laadida varade faile.

Vara omanik saab:

- esitada taotluse uue vara lisamiseks.

RIA haldur saab:

- lisada uue vara või täiendada olemasolevat, lisades vara faile RIA taristu failisüsteemi.

## Arhitektuur

![](docs/Architecture.PNG)

## Vara kirjeldamine

Vara kirjeldatakse järgmiselt:

```
[
    {
    "Name_est": "Õigusakt",
    "Name_eng": "Basic Act",
    "Version": "10.02.2010",
    "Start_date": "",
    "End_date": "",
    "Description_est": "Skeemid XML struktuuriga õigusaktide koostamiseks ja avaldamiseks Riigi Teatajas.",
    "Description_eng": "XML schemas for creating and publishing legal acts in Riigi Teataja",
    "Resource_type": "Dokumendiliigi XML andmekirjeldus",
    "Owner": "Justiitsministeerium",
    "Status": "Registreeritud"
  }
]
```

- vara ametlik nimetus eesti keeles
- vara ametlik nimetus inglise keeles
- versiooni tähis
- kehtivuse alguskuupäev
- kehtivuse lõpukuupäev
- vara kirjeldus eesti keeles
- vara kirjeldus inglise keeles
- vara liik
  - standardsed metaandmed
  - dokumendiliigi XML andmekirjeldus
  - XBRL taksonoomia
  - andmevahetuse konteiner
  - standardelement
  - rahvusvaheline standard
  - klassifikaatori XML vorming
- haldaja (asutus, kes vastutab XML vara arendamise ja RIHAs kirjeldamise eest)
- staatus
  - registreeritud (tulevikus võib kasutusele võtta ka muid staatusi).

## Vajalikud tehnoloogiad

- PHP (Töötab versioonil 7.1.6)
- Python 3.x.x
- Mikroveebiraamistik Bottle
- rakendus kasutab tehnoloogiaid: Bootstrap 4, Chart.js, jQuery, Datatables.

## Veebiserverisse paigaldamine

Paigalda veebiserver:
```
apt install apache2 libapache2-mod-php php-mbstring
```

Navigeeri veebiserveri kausta ning otsi kausta `htdocs`.
Tõsta kõik XMLVaramu failid kausta `htdocs`, seejärel loo samasse kausta fail nimega `.htaccess`.

Faili sisu:
```
ErrorDocument 404 /404.html

Redirect Detailview.php /404.html
```
`.htaccess`-i kasutamiseks tuleb veidi apache conf-i muuta järgides järgnevaid juhiseid.

`cd /etc/apache2/sites-enabled`
`sudo vim 000-default.conf`

Faili tuleb lisada:

```
<Directory /var/www/>
 Options Indexes FollowSymLinks MultiViews
 AllowOverride All
 Order allow,deny
 allow from all
</Directory>

```
Pärast apache restarti on rakendus edukalt paigaltatud.
## Uute varade lisamine

Ava veebiserveri kaust ning navigeeri `XMLVarad` kausta:

```
resources/XMLVarad/
```

Tee `XMLVarad` kausta uus kaust, mille nimi on: `XMLVara_ + varamu nimi`.

**TÄHTIS:** Vaata, et kaustanimes kõik tühikud oleks asendatud alamkriipsuga `_`.

Kui XML varamu nimi on näiteks: `AT teadaanne`, siis selle varamu kausta nimi oleks: `XMLVara_AT_teadaanne
`.

Kui uue varamu kaust on valmis, siis kausta sisu struktuur jaguneb kaheks sõltuvalt, kas varamul on mitu  versiooni või mitte.

**TÄHTIS:** Ka failide puhul on tähtis, et tühikuid ei oleks.

*Näidete puhul on varamu nimeks AT teadaanne.*

Kui kaustal on ainult üks versioon:

```
* resources
    * XMLVarad
        * XMLVara_AT_teadaanne
            * failid
                * fail.xml
                * fail2.xsd
                * fail3.pdf
            * varamu.json
```

Kui kaustal on näiteks kaks versiooni:

```
* resources
    * XMLVarad
        * XMLVara_AT_teadaanne
            * V1.0
                * failid
                    * fail.xml
                    * fail2.xsd
                    * fail3.pdf
                * varamu.json
            * V2.1
                * failid
                    * fail.xml
                    * fail2.xsd
                    * fail3.pdf
                * varamu.json
```

Lisa nende skeemide järgi vajalikud failid uue varamu kausta. Küsimuste korral tasub vaadata juba olemas olevate varamute kaustasid.

## REST API paigaldamine ja konfigureerimine

RIHA XML varamu rakendab kasutusloos "uue vara taotluse esitamine" mikroteenust taotluse salvestamiseks.
Mikroteenuse paigaldamiseks on kõige otstarbekam rakendada Pythoni paketihaldurit pip. Debian/Ubuntu operatsioonisüsteemil tuleb pip installeerimiseks käivitada:
```
sudo apt-get install python-pip
```
Paketihalduriga installeerime mikroveebiraamistiku Bottle:
```
pip install bottle
```
Mikroteenuse skript asub /API kaustas. Seal paiknev fail nimega XML-API.py tuleb asetada failisüsteemis kausta, kuhu on skripti vahendusel võimalik faile maha salvestada. Skripti käivitatakse käsuga:
```
python3 XML-API.py 
```
Skriptis saab konfigureerida mikroteenust käitava masina host IP-d ja porti:
```
app.run(host='localhost', port=8080)
```

## Autorid

* **Janar Peterson** - RIA praktikant
* **Raimond Roosalu** - RIA praktikant
* **Taavi Meinberg** - RIA praktikant
