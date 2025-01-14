---
layout: page
permalink: /python/poetry
title: Poetry ja riippuvuuksien hallinta
---

Laajoissa ja monimutkaisissa ohjelmistoprojekteissa kaiken koodin tuottaminen itse ei ole enää käytännöllistä. Ei ole esimerkiksi järkevää, että jokaisessa ohjelmistoprojektissa toteutetaan oma ohjelmointirajapinta tietokantaoperaatioille, tai sovelluskehys koodin testaamiseen. Jotta pyörää ei tarvitsisi aina keksiä uudelleen, ovat ohjelmistokehittäjät kehittäneet valtavan määrän avoimen lähdekoodin _kirjastoja_, joita jokainen voi hyödyntää projekteissaan.

Kirjastojen lähdekoodi on usein luettavissa versionhallinta-alustoilla, kuten GitHubissa. Usein kirjastoja päivitetään jatkuvasti ja nämä päivitykset synnyttävät kirjastoista uusia _versioita_. Kirjastojen versioita julkaistaan erilaisiin rekistereihin, joista ne ovat helposti asennettavissa. [The Python Package Index](https://pypi.org/) (PyPI) on eräs tämän kaltainen, Python-kirjastoille tarkoitettu rekisteri.

Projektissa käytettävät kirjastojen versiot ovat projektin _riippuvuuksia_. Riippuvuuksia asennetaan Python-projekteissa tyypillisesti projektikohtaisiin _virtuaaliympäristöihin_, jottei samalla tietokoneella olevien projektin riippuvuuksissa syntyisi ristiriitoja. Jotta riippuvuuksien hallinta virtuaaliympäristöissä sujuisi helposti, käytämme kurssilla [Poetry](https://python-poetry.org/)-komentorivityökalua.

### Huomioita komennoista

Monilla tietokoneilla Python-version kolme komennot suoritetaan `python3`-komennolla komennon `python` sijaan. Tarkistaa käytössäoleva versio komennolla:

```bash
python3 --version
```

Jos komentoa `python3` ei jostain syystä löydy, tarkista `python`-komennon käyttämä versio komennolla:

```bash
python --version
```

Jos molemmissa tapauksissa versio on alle 3.8, asenna tietokoneellesi [uusin Python-versio](https://www.python.org/downloads/). Muista varmista asennuksen jälkeen, että oikea versio on käytössä. Muussa tapauksessa käytä komentoa, jonka käyttämä versio on vähintään 3.8.

### Asennus

Ennen kuin pääsemme tutustumaan Poetryn käyttöön tarkemmin, tulee se ensin asentaa. Seuraa alla olevista ohjeista tietokoneesi käyttöjärjestelmälle sopivaa asennusohjetta.

**HUOM:** kaikki asennustavat saattavat vaatia terminaali-ikkunan sulkemisen ja uudelleen avaamisen, jotta Poetryn komennot alkavat toimia. Joissain tapauksissa on vaadittu jopa tietokoneen uudelleenkäynnistys.

#### Linux- ja macOS-asennus

Asenna Poetry suorittamalla terminaalissa seuraava komento:

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

**HUOM:** jos `python3`-komentoa ei löydy, käytä sen sijaan komennon lopussa `python`-komentoa. Varmista kuitenkin, että Python-versio on oikea edellisen ohjeen mukaisesti.

Asennuksen jälkeen Poetry-binäärin polku tulee asettaa `PATH`-muuttujaan. Tämä onnistuu lisäämällä kotihakemiston _.bashrc_-tiedoston loppuun seuraava rivi:

```bash
export PATH="$HOME/.local/bin:$PATH"
```

Lisääminen onnistuu esimerkiksi muokkaamalla tiedostoa nano-editorin avulla, tai suorittamalla seuraava komento:

```bash
echo "export PATH=\"\$HOME/.local/bin:\$PATH\"" >> $HOME/.bashrc
```

**HUOM:** jos käytössäsi on zsh-komentorivi, on oikea konfiguraatiotiedosto _.bashrc_-tiedoston sijaan _.zshrc_-tiedosto. Voit tarkistaa käytössä olevan komentirivin komennolla `echo $SHELL`. Käytä tässä tapauksessa edellisessä komennossa käytetyn `$HOME/.bashrc`-polun sijaan polkua `$HOME/.zshrc`.

**HUOM:** jos käytössäsi on macOS-käyttöjärjestelmä ja bash-komentorivi, käytä edellisessä komennossa käytetyn `$HOME/.bashrc`-polun sijaan polkua `$HOME/.bash_profile`.

**HUOM:** käytä melkki-palvelimella edellisessä komennossa käytetyn `$HOME/.bashrc`-polun sijaan polkua `$HOME/.profile`.

Käynnistä terminaali uudestaan ja varmista, että asennus onnistui suorittamalla komento `poetry --version`. Komennon pitäisi tulostaa asennettu versio.

#### Windows-asennus

Asenna Poetry suorittamalla terminaalissa seuraava komento:

```bash
(Invoke-WebRequest -Uri https://raw.githubusercontent.com/python-poetry/poetry/master/install-poetry.py -UseBasicParsing).Content | python -
```

Asennuksen jälkeen Poetry-binäärin polku tulee asettaa `PATH`-muuttujaan. Lisää [tämän](https://www.architectryan.com/2018/03/17/add-to-the-path-on-windows-10/) ohjeen mukaisesti `PATH`-muuttujaan polku `%APPDATA%\Python\Scripts`.

Käynnistä terminaali uudestaan ja varmista, että asennus onnistui suorittamalla komento `poetry --version`. Komennon pitäisi tulostaa asennettu versio.

### Projektin alustaminen

Harjoitellaan Poetryn käyttöä tekemällä pieni esimerkkiprojekti. Luo hakemisto _poetry-testi_ haluamaasi hakemistoon. Hakemiston ei tarvitse löytyä Labtooliin rekisteröimästäsi repositoriosta. Avaa hakemisto terminaalissa ja suorita siellä komento:

```bash
poetry init --python "^3.8"
```

Komennon yhteydessä annettu `--python "^3.8"`-asetus asettaa projektin Python-version vaatimukseksi vähintään version 3.8. Komennon suorittaminen alkaa kysymään kysymyksiä. Voit vastata kysymyksiin haluamallasi tavalla ja kaikkien kohtien vastauksia voi myös muokata myöhemmin. Tämän vuoksi kysymysten ohittaminen Enter-painiketta painamalla on täysin hyvä vaihtoehto.

Kun viimeiseen kysymykseen on vastattu, katso hakemiston sisältöä. Hakemistoon pitäisi ilmestyä _pyproject.toml_-tiedosto, jonka sisältö on kutakuinkin seuraava:

```
[tool.poetry]
name = "poetry-testi"
version = "0.1.0"
description = ""
authors = ["Kalle Ilves <kalle.ilves@helsinki.fi>"]

[tool.poetry.dependencies]
python = "^3.8"

[tool.poetry.dev-dependencies]

[build-system]
requires = ["poetry-core>=1.0.0"]
build-backend = "poetry.core.masonry.api"
```

Tiedoston `[tool.poetry]`-osio sisältää projektiin liittyviä yleistietoja, kuten sen nimen, kuvauksen ja ylläpitäjät. Osion alapuolella on osioita, jotka listaavat projektin riippuvuuksia. Osiossa `[tool.poetry.dependencies]` näemme `poetry init`-komennon suorituksen yhteydessä asettamamme Python-version vaatimuksen, joka on muotoa `python = "^3.8"`. `^3.8`-merkintä tarkoittaa, että projektin käyttö vaatii vähintään Python-version 3.8.

Kun _pyproject.toml_-tiedosto on tullut tutuksi, viimeistellään projektin alustaminen suorittamalla komento:

```bash
poetry install
```

Komennon suorittaminen tekee projektille vaadittavat alustustoimenpiteet, kuten virtuaaliympäristön alustamisen. Jos projektille olisi määritelty riippuvuuksia, komennon suorittaminen asentaisi myös ne. Tämän vuoksi komento tulee suorittaa aina ennen kuin uutta projekti aletaan käyttämään.

Komennon suorittamisen jälkeen hakemistoon pitäisi ilmestyä tiedosto _poetry.lock_. Tiedosto sisältää kaikkien asennettujen riippuvuuksien versiotiedot. Sen tietojen avulla Poetry pystyy aina asentamaan `poetry install`-komennolla riippuvuuksista täsmälleen oikeat versiot. Tästä syystä tiedosto tulee lisätä versionhallintaan.

### Riippuvuuksien asentaminen

Asennetaan seuraavaksi projektiimme ensimmäisen riippuvuus. Riippuvuuksien löytäminen onnistuu helpoiten Googlettamalla ja etsimällä hakutuloksista sopivia GitHub-repositorioita, tai PyPI-sivuja. Asennetaan esimerkkinä projektiimme [cowsay](https://pypi.org/project/cowsay/)-kirjasto. Tämä onnistu projektin juurihakemistossa (samassa hakemistossa, missä _pyproject.toml_-tiedosto sijaitsee) komennolla:

```bash
poetry add cowsay
```

Asennuksen komento on siis muotoa `poetry add <kirjasto>`. Komennon suorittamisen jälkeen huomaamme, että _pyproject.toml_-tiedoston `[tool.poetry.dependencies]`-osion alla on uutta sisältöä:

```
[tool.poetry.dependencies]
python = "^3.8"
cowsay = "^2.0.3"
```

`poetry add`-komento asentaa oletusarvoisesti kirjaston uusimman version, joka oli komennon suoritushetkellä `2.0.3`. Usein tämä on juuri se, mitä haluamme tehdä. Voimme kuitenkin asentaa halutessamme esimerkiksi cowsay-kirjaston version `1.0` komennolla:

```bash
poetry add cowsay==1.0
```

Jos haluaisimme poistaa kirjaston projektimme riippuvuuksien joukosta, se onnistuisi komennolla:

```bash
poetry remove cowsay
```

Pidetään kuitenkin cowsay-kirjasto toistaiseksi asennettuna.

### Komentojen suorittaminen virtuaaliympäristössä

Lisätään seuraavaksi _poetry-testi_-hakemistoon hakemisto _src_ ja sinne tiedosto _index.py_. Lisätään tiedostoon seuraavat koodirivit:

```python
import cowsay

cowsay.tux("Poetry is awesome!")
```

Koodissa käyttämme `import`-lausetta saadaksemme cowsay-kirjaston käyttöömme. Jos suoritamme tiedoston terminaalissa komennolla:

```bash
python3 src/index.py
```

On lopputuloksena seuravaa virheilmoitus:

```
ModuleNotFoundError: No module named 'cowsay'
```

Tämä johtuu siitä, että emme ole projektin virtuaaliympäristön sisällä, jonka vuoksi Python ei löydä projektimme riippuvuuksia. Asia korjaantuu käyttämällä [run](https://python-poetry.org/docs/cli/#run) komentoa:

```bash
poetry run python3 src/index.py
```

`poetry run`-komento siis suorittaa annetun komennon virtuaaliympäristössä, jonka sisällä Python löytää riippuvuutemme.

Kun projektia kehitetään aktiivisesti ja komentoja suoritetaan terminaalissa jatkuvasti, on kätevintä olla koko ajan virtuaaliympäristön sisällä. Voimme siirtyä virtuaaliympäristön sisään kommennolla [shell](https://python-poetry.org/docs/cli/#shell):

```bash
poetry shell
```

Kun olemme virtuaaliympäristössä, komentorivin syöterivin edessä on suluissa virtuaaliympäristön nimi:

```bash
$ (poetry-testi-IhtScY6W-py3.9)
```

Virtuaaliympäristön sisällä voimme suorittaa komennon "normaalisti", eli ilman `run`-komentoa:

```bash
python3 src/index.py
```

Voimme lähteä virtuaaliympäristöstä komennolla `exit`.

### Kehityksen aikaiset riippuvuudet

Jos olit tarkkana, huomasit, että _pyproject.toml_-tiedostosta löytyy `[tool.poetry.dependencies]`-osion lisäksi osio `[tool.poetry.dev-dependencies]`. Komennon `poetry add` suorittaminen asentaa oletusarvoisesti riippuvuudet `[tool.poetry.dependencies]`-osion alle. Näiden riippuvuuksien lisäksi voimme asentaa projektiimme riippuvuuksia, joita tarvitsemme vain kehityksen aikana. Näitä riippuvuuksia ovat kaikki ne, joita itse sovelluksen käynnistäminen (esimerkiksi `python3 src/index.py`-komennon suorittaminen) ei tarvitse.

Kehityksen aikaisten riippuvuuksien asentaminen onnistuu antamalla `poetry add`-komennolle `--dev`-flagi. Esimerkiksi pian tutuksi tulevan [pytest](https://pytest.org/)-kirjaston voi asentaa kehityksen aikaiseksi riippuvuudeksi seuraavalla komennolla:

```bash
poetry add pytest --dev
```

Komennon suorittaminen lisää pytest-kirjaston riippuvuudeksi `[tool.poetry.dev-dependencies]`-osion alle:

```
[tool.poetry.dev-dependencies]
pytest = "^6.1.2"
```

Kehityksen aikaisten riippuvuuksien määritteleminen on kätevää, koska se vähentää asennettavien riippuvuuksien määrää tapauksessa, jossa haluamme vain käynnistää sovelluksen. Tässä tilanteessa riippuvuuksien asentamisen voi tehdä komennolla `poetry install --without dev`.

### Ratkaisuja yleisiin ongelmiin

Usein Poetry-ongelmat ratkeavat seuraavilla toimenpiteillä:

1. Varmista, että Poetrysta on asennettu uusin versio suorittamalla komento `poetry self update`
2. Varmista, että _pyproject.toml_-tiedostossa on oikea Python version vaatimus:

   ```
   [tool.poetry.dependencies]
   python = "^3.8"
   ```

   **Jos versio on väärä**, muuta se oikeaksi ja suorita komento `poetry update`

3. Listaa projektissa käytössä olevat virtuaaliympäristöt komennolla `poetry env list` ja poista ne kaikki yksitellen komennolla `poetry env remove <nimi>`. Esimerkiksi seuraavasti:

   ```bash
   $ poetry env list
   unicafe-jLeQYxxf-py3.9 (Activated)
   $ poetry env remove unicafe-jLeQYxxf-py3.9
   Deleted virtualenv: /Users/kalleilv/Library/Caches/pypoetry/virtualenvs/unicafe-jLeQYxxf-py3.9
   ```

   Kun virtuaaliympäristöt on poistettu, suorita komento `poetry install`

Kun kaikki toimenpiteet on suoritettu, yritä suorittaa epäonnistunut Poetry-komento uudestaan.
