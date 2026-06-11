# Kotiverkon Tallennusjärjestelmät (NAS & DAS) – Dokumentaatio

Tämä dokumentti sisältää kotiverkon tallennuslaitteiden nimeämiskäytännöt, konfiguraatiot, käyttötarkoitukset ja tekniset tiedot. Järjestelmä on jaettu suomalaisen jylhän nimistön mukaiseen hierarkiaan datan kriittisyyden ja vikasietoisuuden mukaan.

---

## Järjestelmän yleiskuvaus

| Järjestelmän nimi | Laite | RAID-taso / Tiedostojärjestelmä | Käyttötarkoitus |
| :--- | :--- | :--- | :--- |
| **1. Kallio-SHR2** | Synology DS1621+ | SHR-2 / Btrfs | Kaikkein kriittisin ja korvaamaton data |
| **2. Sora-R5** | WD My Cloud Pro PR4100 | RAID 5 / WD:n oma| Tärkeä arkidata ja medialirastot |
| **Murske-1-R5** | Promise Pegasus2 R6 | RAID 5 / APFS | Aktiivinen videoeditointi & työtila (Mac Mini M1) |
| **Murske-2-R5** | Promise Pegasus2 R6 | RAID 5 / APFS | Aktiivinen työtila & lokaali varmistus (Mac Mini M1) |

---

## Kallio-SHR2

* **Motto:** *Tämä ei hievahda.*
* **Sijainti:** Verkkolevy (NAS)

### Tekniset tiedot
* **Laitteisto:** Synology DS1621+ (6-paikkainen tehotason NAS, AMD Ryzen -prosessori, ECC-muistit).
* **Vikasietoisuus:** **SHR-2** (Synology Hybrid RAID 2). Järjestelmä kestää **kahden (2) minkä tahansa levyn samanaikaisen hajoamisen** ilman datan menetystä.
* **Tiedostojärjestelmä:** **Btrfs** (sisältää automaattisen bit rot -suojauksen ja datan korruptoitumisen eston yhdessä ECC-muistin kanssa).

### Käyttökohde ja datan kriittisyys
* **Kriittisyystaso:** Maksimaalinen turva.
* **Sisältö:** Kaikki täysin korvaamaton data, jonka häviäminen olisi katastrofi.
    * Perheen valokuvat ja videomuistot
    * Tärkeät dokumentit, verotustiedot ja sopimukset
    * Muiden laitteiden järjestelmänäköistiedostot ja kriittisimmät varmuuskopiot

---

## Sora-R5

* **Motto:** *Kestää kävelyä, mutta saattaa vähän liikahtaa.*
* **Sijainti:** Verkkolevy (NAS)

### Tekniset tiedot
* **Laitteisto:** Western Digital 4100 (4-paikkainen työjuhta).
* **Vikasietoisuus:** **RAID 5**. Järjestelmä kestää **yhden (1) levyn hajoamisen**. Jos levy rikkoontuu, se on korvattava välittömästi uudella, jotta pakka saadaan palautettua (*rebuild*).

### Käyttökohde ja datan kriittisyys
* **Kriittisyystaso:** Normaali arkisuoja.
* **Sisältö:** Tärkeät tiedostot ja mediat, joiden katoaminen harmittaisi, mutta ei kaataisi maailmaa.
    * Elokuvat, sarjat ja musiikkikirjastot
    * Netistä saatavat asennusmediat lokaakikäyttöön
    * Aktiiviset ei-kriittiset projektit
    * Jaetut verkkokansiot yleiskäyttöön

---

## 3. Työyksiköt: Murskeet (`Murske-1-R5` & `Murske-2-R5`)

* **Motto:** *Tehty kovaan rouskutukseen ja työstöön.*
* **Sijainti:** Suorakytketty tallennus (DAS) Mac Mini M1 -koneen perässä (Thunderbolt 2 -> Thunderbolt 3 -adaptereilla)

### Tekniset tiedot
* **Laitteisto:** 2kpl Promise Pegasus2 R6 (6-paikkaiset Thunderbolt-pöntöt).
* **Vikasietoisuus:** **RAID 5** molemmissa purnukoissa. Kumpikin laite kestää yhden (1) levyn hajoamisen lennosta ilman, että työt keskeytyvät.
* **Tiedostojärjestelmä:** **APFS** (Apple File System, standardiversio, *ei* Case-sensitive).
* **Kapasiteetti:** Noin **20 TB** puhdasta tallennustilaa per purkki RAID-muodostuksen jälkeen.

### Suorituskyky ja editoinnin optimointi
* **Yhteys:** Ketjutettu TB2 kaapelointi (TBD: Kytketty erillisillä Thunderbolt-kaapeleilla suoraan Mac Minin omiin portteihin maksimaalisen kaistan takaamiseksi)
* **Nopeus:** RAID 5 -konfiguraatio tarjoaa laitteistotason (Hardware RAID) ohjaimella noin **500–650 MB/s kirjoitusnopeuden** ja **yli 700 MB/s lukunopeuden**.
* **Tiedostojärjestelmän valinta:** Tavallinen APFS takaa täyden yhteensopivuuden Adobe Creative Cloudin (Premiere Pro, After Effects), Final Cut Pron ja DaVinci Resolven kanssa ilman case-sensitive-alustojen aiheuttamia välimuisti- tai plugivirheitä.

### Käyttökohde
* **Murske-1-R5:** Aktiivinen videoeditointi, rendaus, 4K-materiaalin "scratch disk" ja projektitiedostot.
* **Murske-2-R5:** Aktiiviset työtiedostot, materiaalipankit sekä lokaalit varmistukset (esim. Time Machine, joka alustaa taustalla oman osionsa tarvittaessa vaatimaansa Case-sensitive-muotoon).

---

## Huolto- ja ylläpito-ohjeet

1. **Levyrikon sattuessa:**
   * **Kallio (SHR-2):** Laite piippaa, mutta hätää ei ole. Korvaa viallinen levy uudenveroisella mahdollisimman pian. Järjestelmä sietää vielä toisenkin levyn tippumisen palautuksen aikana.
   * **Sora & Murskeet (RAID 5):** Datan menetys on yhden levyn päässä. Älä aja raskaita rendauksia tai siirtoja ennen kuin vioittunut levy on vaihdettu ja pakka on synkronoitunut kokonaan (*Rebuilding* valmis).
2. **Murskeiden alustus:**
   * Älä koskaan tee tiedostojärjestelmäosioita Promisen oman apuohjelman kautta. Anna Promisen vain rakentaa fyysinen RAID 5 -alusta, ja hoida varsinainen APFS-alustus aina macOS:n omalla **Levytyökalulla (Disk Utility)** muodossa *GUID Partition Map*.
