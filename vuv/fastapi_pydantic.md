## REST API sa FastApi & Pydanticž

![](/img/fastapi_pydantic.png)

### 1. Uvod

U ovoj vježbi kreirat ćemo jednostavanu REST API aplikaciju koji simulira prodaju
proizvoda na samoposlužnom aparatu (eng. vending machine). Upute su rađene za rad na
Windows 10 OS-u.

![](/img/vending_machine.jpg)

Za pohađanje ove vježbe potrebno je poznavati osnove objektno orijentiranog
programiranja, osnove rada u Windows cmdu ili Linux bashu te poznavanje rada u
jednom od code editora (PyCharm, VS Code, Sublime Text, ...). Na računalu je
potrebno imati instaliran `Python` (u trenutku pisanja vježbe najnovija verzija
dostupna za Windows je `3.10.4`, moguće ga je preuzeti sa [https://www.python.org](https://www.python.org){:target="\_blank"}),
`pip` upravitelj Python paketa (dolazi u kompletu sa Pythonom) te globalno
instaliran `virtualenv` paket za kreiranje virtualnih Python okruženja
(`pip install virtualenv`).
Code editor koji ćemo koristiti u vježbi je VS Code - [https://code.visualstudio.com](https://code.visualstudio.com){:target="\_blank"}.
Python aplikacija koju ćemo izraditi će biti pogonjena sa sljedećim tehnologijama:
- `FastAPI` - Python web framework za izradu API-a
- `Pydantic` - alat za validaciju podataka
- `SQLAlchemy` - alat za komunikaciju Python aplikacije sa relacijskim bazama podataka (PostgreSQL)
Izvorni kod aplikacije se nalazi na Github repozitoriju.

### 2. Postavljanje Python virtualng okruženja
Otvoriti `cmd` i provjeriti dali je instaliran Python.
```cmd
python --version
```

![](/img/python_version.gif)

Unutar cmd-a pozicionirati se u željeni folder na računalu te kreirati novi folder
sa nazivom `vend_api` i pozicionirati se unutar njega.
```cmd
mkdir vend_api && cd vend_api
```

![](/img/mkdir_vend_api.gif)

Kreirati novo izolirano virtualno Python okruženje samo za ovaj projekt sa `virtualenv`-om:
```cmd
virtualenv venv
```

![](/img/virtualenv_venv.gif)

Rezultat naredbe za kreiranje virtualnog okruženja je kreiran folder sa nazivom
`venv` koji u sebi sadrži Python interpreter te repozitorij biblioteka
koje ćemo instalirati tijekom izrade aplikacije. Koristeći virtualna okruženja
svaki projekt će imati svoje izolirano okruženje, što znači da se verzije
biblioteka među projektima neće sukobljavati.
Upravljanje sa virtualnim okruženjima je jednostavno, koristit ćemo 2 naredbe u `cmd`-u:
- Aktivacija -  `venv\Scripts\activate`
- Deaktivacija - `deactivate`

Budući da je virtualno okruženje kreirano potrebno ga je aktivirati kako bi mogliSada je potrebno popisati i instalirati sve potrebne Python biblioteke za rad aplikacije u
virtualnom okruženje sa `pip`-om. Potrebne biblioteke sa odgovarajućim verzijama
su zapisane u `.txt` datotekama unutar `requirements` foldera tako da i drugi
developeri mogu jednostavno setup-ati aplikaciju na svom računalu (isto tako da
i devopsi mogu postaviti aplikaciju na serversku infrastrukturu).

Struktura datoteka unutar `requirements` foldera:
```
- requirements
    - base.txt
    - dev.txt
    - test.txt
```

Lista biblioteka je odvojena u 3 datoteke iz razloga što želimo razlikovati biblioteke
za različita okruženja u kojima će se koristiti aplikacija.
Osnovne biblioteke koje su nužan preduvjet za rad aplikacije su zapisane u
datoteci `base.txt` - svako okruženje mora imati biblioteke sa odgovarajućim
verzijama iz ove datoteke.
Prvo okruženje u kojem će se koristiti aplikacija je razvojno (dev) okruženje
koje je specifično po tome što zahtjeva biblioteke za formatiranje i lintanje
(prepoznavanje stilskih pokrešaka u kodu) koda. Ove biblioteke nisu potrebne niti
su preduvjet za rad aplikacije stoga nema potrebe da budu u `base.txt`-u.
U datoteci `test.txt` se nalaze biblioteke koje su nužne za pokretanje unit
testova.

Budući da radimo u dev okruženju kreirat instalirat ćemo biblioteke iz datoteke
`requirements/dev.txt` datoteke:
```cmd
pip install -r requirements\dev.txt
```
U `cmd`-u će se ispisati svaka bibloteka koja se trenutno instalira te na kraju
instalacije će se ispisati sve biblioteke koje su se instalirale te greške
koje su se dogodile tijekom instalacije.

Sada opet možemo provjeriti listu svih instaliranih biblioteka da potvrdimo da su
se instalirale sve biblioteke
```
pip list
```
raditi nad njim:
```cmd
venv\Scripts\activate
```

![](/img/activate_venv.gif)

Nakon pokrenute naredbe pojavit će se `(venv)` sa lijeve strane putanje
u `cmd`-u što je indikator trenutno aktivnog virtualnog okruženja

Svako novo Python virtualno okruženje dolazi sa tri predinstalirane biblioteke -
`pip`, `setuptools` i `whelel`. Navedene biblioteke se koriste kod
preuzimanja, kreiranja i instalacije Python biblioteka. Za potrebe ove
aplikacije ih nećemo koristiti stoga se nećemo kod njih zadržavati.
Provjeru instaliranih biblioteka unutar Python virtualnog okruženja moguće je sa
naredbom

```cmd
pip list
```

![](/img/pip_list.gif)


### 3. Postavljanje i pokretanje aplikacije
Nakon postavljanja virtualnog okruženja zadovoljen je preduvjet za pokretanje aplikacije.
Kreirat ćemo folder `vend_api` unutar već kreiranog istoimenog foldera `vend_api`.
koji će sadržati izvorni kod Python aplikacije.

```cmd
mkdir vend_api && cd vend_api
```

Sada struktura dosad kreiranih foldera izgleda ovako:
```
- vend_api
    - venv
        - ...
        - folderi virtualnog okruženja
        - ...
    - vend_api
```
Preuzeti izvorni kod aplikacije sa naredbom:
```cmd
git clone https://github.com/asarabok/vend_fastapi.git .
```

Sada je potrebno popisati i instalirati sve potrebne Python biblioteke za rad aplikacije u
virtualnom okruženje sa `pip`-om. Potrebne biblioteke sa odgovarajućim verzijama
su zapisane u `.txt` datotekama unutar `requirements` foldera tako da i drugi
developeri mogu jednostavno setup-ati aplikaciju na svom računalu (isto tako da
i devopsi mogu postaviti aplikaciju na serversku infrastrukturu).

Struktura datoteka unutar `requirements` foldera:
```
- requirements
    - base.txt
    - dev.txt
    - test.txt
```

Lista biblioteka je odvojena u 3 datoteke iz razloga što želimo razlikovati biblioteke
za različita okruženja u kojima će se koristiti aplikacija.
Osnovne biblioteke koje su nužan preduvjet za rad aplikacije su zapisane u
datoteci `base.txt` - svako okruženje mora imati biblioteke sa odgovarajućim
verzijama iz ove datoteke.
Prvo okruženje u kojem će se koristiti aplikacija je razvojno (dev) okruženje
koje je specifično po tome što zahtjeva biblioteke za formatiranje i lintanje
(prepoznavanje stilskih pokrešaka u kodu) koda. Ove biblioteke nisu potrebne niti
su preduvjet za rad aplikacije stoga nema potrebe da budu u `base.txt`-u.
U datoteci `test.txt` se nalaze biblioteke koje su nužne za pokretanje unit
testova.

Budući da radimo u dev okruženju kreirat instalirat ćemo biblioteke iz datoteke
`requirements/dev.txt` datoteke:

```cmd
pip install -r requirements\dev.txt
```

U `cmd`-u će se ispisati svaka bibloteka koja se trenutno instalira te na kraju
instalacije će se ispisati sve biblioteke koje su se instalirale te greške
koje su se dogodile tijekom instalacije.

Sada opet možemo provjeriti listu svih instaliranih biblioteka da potvrdimo da su
se instalirale sve biblioteke.
```
pip list
```

Kako je aplikacija dizajnirana na način da se može koristiti na više okruženja,
potrebno je kreirati datoteku `.env` u root-u projekta. U toj datoteci ćemo
dodijeliti vrijednosti environment varijablama potrebnim za rad. Zalijepiti sljedeći
sadržaj u datoteku `.env`:

```
DATABASE_USER = 'admin'
DATABASE_PASS = 'admin'
DATABASE_HOST = 'localhost'
DATABASE_NAME = 'vuv_vend'

DATABASE_URL = 'postgresql://${DATABASE_USER}:${DATABASE_PASS}@${DATABASE_HOST}/${DATABASE_NAME}'

PAGE_SIZE = 10
SECRET = "secret"
JWT_ALGORITHM = "HS256"
JWT_EXPIRE_DAYS = 7
```

Kreirati novu PostgreSQL bazu te upisati pripadajuće podatke za spajanje
u environment varijable  `DATABASE_USER`, `DATABASE_PASS`, `DATABASE_HOST` i `DATABASE_NAME`.
