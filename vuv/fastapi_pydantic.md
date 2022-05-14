## REST API sa FastApi & Pydantic
### MATERIJAL U IZRADI
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

### 2. Inicijalni setup aplikacije
Otvoriti `cmd` i provjeriti dali je instaliran Python.
```cmd
python --version
```
Unutar cmd-a pozicionirati se u željeni direktorij na računalu te kreirati folder
sa nazivom `vuv_vend` i pozicionirati se unutar njega.
```cmd
mkdir fastapi_pydantic && cd fastapi_pydantic
```
Kreirati novo izolirano virtualno Python okruženje samo za ovaj projekt sa `virtualenv`-om:
```cmd
virtualenv venv_vuv_vend
```
Ukoliko je virtualno okruženje uspješno instalirano `cmd` će ispisati ovakvu poruku:
```cmd
created virtual environment CPython3.10.4.final.0-64 in 6330ms
```
Rezultat naredbe za kreiranje virtualnog okruženja je kreiran folder sa nazivom
`venv_vuv_vend` koji u sebi sadrži Python interpreter te repozitorij biblioteka
koje ćemo instalirati tijekom izrade aplikacije. Koristeći virtualna okruženja
svaki projekt će imati svoje izolirano okruženje, što znači da se verzije
biblioteka među projektima neće sukobljavati.
Upravljanje sa virtualnim okruženjima je jednostavno, koristit ćemo 2 naredbe u `cmd`-u:
- Aktivacija -  `venv_vuv_vend\Scripts\activate`
- Deaktivacija - `deactivate`

Budući da je virtualno okruženje kreirano potrebno ga je aktivirati kako bi mogli
raditi nad njime:
```cmd
venv_vuv_vend\Scripts\activate
```
Nakon pokrenute naredbe pojavit će se `(venv_vuv_vend)` sa lijeve strane putanje
u `cmd`-u što je indikator trenutno aktivnog virtualnog okruženja:
![](/img/venv_activate.jpg)
Svako novo Python virtualno okruženje dolazi sa tri predinstalirane biblioteke.

Provjeru instaliranih biblioteka unutar Python virtualnog okruženja moguće je sa
naredbom
```
pip list
```
a kao rezultat dobijamo:
```
Package    Version
---------- -------
pip        22.0.4
setuptools 62.1.0
wheel      0.37.1
```
Navedene biblioteke se koriste kod preuzimanja, kreiranja i instalacije
Python biblioteka. Kod izrade ove aplikacije ih nećemo koristiti stoga se nećemo
kod njih zadržavati.

Sljedeći korak je kreiranje foldera `api_vuv_vend` (unutar foldera `vuv_vend`)
u koji ćemo spremati izvorni kod aplikacije.
```cmd
mkdir api_vuv_vend && cd api_vuv_vend
```
Sada struktura dosad kreiranih foldera izgleda ovako:
```
- vuv_vend
    - venv_vuv_vend
        - ...
    - api_vuv_vend
```

Sada je potrebno popisati i instalirati sve potrebne Python biblioteke za rad aplikacije u
virtualnom okruženje sa `pip`-om. Potrebne biblioteke sa odgovarajućim verzijama
ćemo zapisati u datoteke tako da i drugi developeri mogu jednostavno setup-ati
aplikaciju na svom računalu (isto tako da i devopsi mogu postaviti aplikaciju
na serversku infrastrukturu za produkciju).

Kreirat ćemo sljedeću strukturu foldera i datoteka unutar `api_vuv_vend`:
```
- requirements
    - base.txt
    - dev.txt
    - test.txt
```
Dodati sadržaj u datoteke `base.txt`, `dev.txt` i `test.txt`

`base.txt`:
```
fastapi==0.78.0
uvicorn==0.17.6
SQLAlchemy==1.4.36
psycopg2-binary==2.9.3
```

`dev.txt`
```
-r base.txt

flake8==4.0.1
black==22.3.0
```

`test.txt`
```
-r base.txt

pytest==7.1.2
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

Nakon što smo popisali potrebne biblioteke možemo ih instalirati sa naredbom:
```cmd
pip install -r requirements\dev.txt
```
U `cmd`-u će se ispisati svaka bibloteka koja se trenutno instalira te na kraju
instalacije će se ispisati sve biblioteke koje su se instalirale te greške
koje su se dogodile tijekom instalacije.

Sada opet možemo provjeriti listu svih instaliranih biblioteka:
```
˙pip list
```
I kao rezultat dobit ćemo:
```
Package           Version
----------------- -------
anyio             3.6.1
asgiref           3.5.1
atomicwrites      1.4.0
attrs             21.4.0
black             22.3.0
click             8.1.3
colorama          0.4.4
fastapi           0.78.0
flake8            4.0.1
greenlet          1.1.2
h11               0.13.0
idna              3.3
iniconfig         1.1.1
mccabe            0.6.1
mypy-extensions   0.4.3
packaging         21.3
pathspec          0.9.0
pip               22.1
platformdirs      2.5.2
pluggy            1.0.0
psycopg2-binary   2.9.3
py                1.11.0
pycodestyle       2.8.0
pydantic          1.9.0
pyflakes          2.4.0
pyparsing         3.0.9
pytest            7.1.2
setuptools        62.1.0
sniffio           1.2.0
SQLAlchemy        1.4.36
starlette         0.19.1
tomli             2.0.1
typing_extensions 4.2.0
uvicorn           0.17.6
wheel             0.37.1
```
