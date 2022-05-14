## REST API sa FastApi & Pydantic
### MATERIJAL U IZRADI
![](/img/fastapi_pydantic.png)

### 1. Uvod

U ovoj vježbi kreirat ćemo jednostavanu REST API aplikaciju koji simulira prodaju
proizvoda na samoposlužnom aparatu (eng. vending machine).

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
sa nazivom `fastapi_pydantic` i pozicionirati se unutar njega.
```cmd
mkdir fastapi_pydantic && cd fastapi_pydantic
```
Instalirati novo izolirano Python okruženje sa `virtualenv`-om:
```cmd
virtualenv venv_fastapi_pydantic
```
Ukoliko je virtualno okruženje uspješno instalirano cmd će ispisati ovakvu poruku:
```cmd
created virtual environment CPython3.10.4.final.0-64 in 6330ms
```

### MATERIJAL U IZRADI
-
