# EnergyHub dashboards

Tämä hakemisto sisältää EnergyHub-projektin Grafana-dashboardit JSON-muodossa.

Dashboardit ovat esimerkkikonfiguraatioita kotitalouden energiaseurantaan, päätösanalyysiin, laitekohtaiseen seurantaan ja talousseurantaan. Ne eivät ole valmis yleispaketti kaikkiin ympäristöihin, vaan vaativat yleensä omien `entity_id`-nimien, InfluxDB-bucketin ja datasource-nimen tarkistamisen.

## Dashboardit

| Tiedosto | Dashboard | Kuvaus |
|---|---|---|
| `energyhub_dashboard.json` | EnergyHub — Yleiskatsaus | Verkkoteho, PV-tuotanto, vaihevirrat, EV-lataus, Thermia-lämpötilat ja spot-hinta |
| `energyhub_dashboard2_paatosanalyysi.json` | EnergyHub — Päätösanalyysi | Spot-hinta, hintakynnykset, sulakerajat, PV + verkko, EV + SOC, HP EVU/Boost ja PV-rajoitus |
| `energyhub_dashboard3_laitteet.json` | EnergyHub — Laitteiden seuranta | Thermia, Sungrow ja EV-laitekohtainen seuranta |
| `energyhub_dashboard4_talous.json` | EnergyHub — Talous | PV-tuotanto, osto/myynti, spot-hinta, kustannusarvio ja negatiiviset hintatunnit |

## Vaatimukset

Dashboardit olettavat seuraavan tyyppisen ympäristön:

- Grafana
- InfluxDB datasource Grafanassa nimellä `influxdb`
- InfluxDB bucket nimellä `energyhub`
- Home Assistantista InfluxDB:hen kirjoitetut sensoritiedot
- EnergyHubissa käytetyt mittaus- ja apusensorit

Jos oma ympäristö käyttää eri nimiä, muuta ne JSON-tiedostoista ennen importointia tai Grafanan query-editorissa importoinnin jälkeen.

## Importointi Grafanaan

1. Avaa Grafana.
2. Valitse vasemmalta **Dashboards**.
3. Valitse **New**.
4. Valitse **Import**.
5. Lataa haluttu JSON-tiedosto kohdasta **Upload dashboard JSON file**.
6. Tarkista datasource-valinta.
7. Valitse **Import**.
8. Avaa dashboard ja tarkista paneelien queryt.

Jos paneelit eivät näytä dataa, yleisimmät syyt ovat:

- datasource ei ole nimeltään `influxdb`
- bucket ei ole nimeltään `energyhub`
- Home Assistantin `entity_id`-nimet eivät vastaa esimerkkikonfiguraatiota
- dataa ei ole vielä kirjoitettu InfluxDB:hen kyseiseltä aikaväliltä

## Muutettavat kohdat omassa ympäristössä

Tarkista erityisesti seuraavat kohdat:

### Datasource

Kaikissa dashboardeissa käytetään datasource-nimeä:

```text
influxdb
```

Jos Grafanassa datasource on eri nimellä, muuta `datasource`-kentät vastaamaan omaa nimeä.

### InfluxDB bucket

Kaikissa Flux-queryissä käytetään bucketia:

```text
energyhub
```

Jos oma bucket on eri nimellä, muuta kaikki kohdat:

```flux
from(bucket: "energyhub")
```

### Home Assistant entity_id:t

Dashboardit käyttävät projektikohtaisia Home Assistant -entityjä. Osa on loogisia EnergyHub-mittausentiteettejä, kuten:

```text
m_grid_power_w
m_pv_power_w
m_spot_price_eur_kwh
m_phase_l1_a
m_phase_l2_a
m_phase_l3_a
```

Osa viittaa suoraan laitekohtaisiin sensoreihin, kuten Sungrow-, Thermia-, Shelly- ja Nissan Leaf -sensoreihin. Nämä pitää vaihtaa vastaamaan omaa Home Assistant -ympäristöä.

## Jaettavuus ja anonymisointi

Tiedostoissa ei ole API-avaimia, salasanoja, IP-osoitteita, sähköpostiosoitteita tai URL-osoitteita.

Huomioitavaa ennen julkaisua:

- Shelly-entityissä näkyy laitekohtaisia tunnisteita, esimerkiksi muodossa `shellypro..._<laite-id>...`.
- Nämä eivät ole salasanoja, mutta voivat yksilöidä laitteen tai asennuksen.
- Julkiseen GitHub-repoon suositellaan joko hyväksymään tämä riskinä tai korvaamaan ne geneerisillä EnergyHub-entiteeteillä, esimerkiksi `m_ev_charging_power_w`, `m_hp_power_w`, `m_hp_evu_relay` ja `m_hp_boost_relay`.

Suositeltu periaate on julkaista dashboardit esimerkkeinä ja ohjata käyttäjää muuttamaan `entity_id`-nimet omaan järjestelmään sopiviksi.

## Suositeltu hakemistorakenne

```text
energyhub-homeassistant/
├── dashboards/
│   ├── energyhub_dashboard.json
│   ├── energyhub_dashboard2_paatosanalyysi.json
│   ├── energyhub_dashboard3_laitteet.json
│   └── energyhub_dashboard4_talous.json
└── README.md
```

Vaihtoehtoisesti tiedostot voi nimetä järjestysnumerolla:

```text
01_energyhub_yleiskatsaus.json
02_energyhub_paatosanalyysi.json
03_energyhub_laitteet.json
04_energyhub_talous.json
```

## Lisenssi

Voit käyttää näitä dashboardeja oman EnergyHub- tai Home Assistant -ympäristön pohjana. Tarkista mahdollinen projektin varsinainen lisenssi repositorion päätasolta.

