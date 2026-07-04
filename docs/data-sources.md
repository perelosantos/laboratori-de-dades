# Fonts de dades obertes — seccions censals (CUSEC)

*Document viu, compartit entre tots els projectes que treballen amb dades a nivell de secció censal (Barcelona/Terrassa/Vallès). Actualitza aquesta pàgina en comptes de duplicar-la a cada projecte.*

---

## 1. Datasets en ús actiu

Tots es creuen per la clau **CUSEC** (codi de 10 dígits, format INE/Idescat estàndard).

| Dataset | Font | Cobertura | Descripció |
|---|---|---|---|
| `ist_bcn_2015_2024.csv` | Idescat — Índex Socioeconòmic Territorial | 1.068 seccions BCN, 2015–2024 (5.108 a Catalunya) | Índex sintètic (renda, educació, ocupació, composició llar). Escala Catalunya=100. Correlació amb renda IRPF: r=0,844. **Saturat** a l'extrem alt (125–135 comprimeix rendes molt diverses per sobre de ~25.000€). |
| `renda_bcn_seccions_cleaned.csv` | INE — Atlas de Distribución de la Renta de los Hogares (taula 30896) | 1.068 seccions, 2015–2023 | Renda neta/bruta per càpita, llar i unitat de consum (UC). Preferir *neta* per anàlisi social. Excel comença a la fila 8, retallar a les 19 primeres columnes. |
| `demografic_bcn_seccions.csv` | INE/Idescat | 1.068 seccions, 2015–2023 | Edat mitjana, % menors 18, % 65+, mida llar, % llars unipersonals, % població espanyola. |
| `bcn_seccions_IST_Gini_trajectories_pous_2015_2024.csv` | Derivat (IST + Gini) | 1.068 seccions, 2015–2024 | Trajectòries IST i Gini per secció + classificació de "pous de potencial" (zones ancorades en desavantatge). |
| `opendatabcn_educacio_ensenyament_reglat.csv` | OpenData BCN | 1.404 registres, per adreça | Centres educatius reglats. **No ve geocodificat a CUSEC** — cal join espacial per adreça/coordenades. Fitxer en **UTF-16**. |

**Nota metodològica compartida:** el gradient discret ∇IST (contigüitat *queen*, ponderació 1/d²) i l'IDAD (Índex d'Accessibilitat a la Diversitat, llindar ΔIST=1σ municipal) es documenten a `annex1_metodologia_v3.md` i `nota_xarxa_idad_metodologia.md`. Reutilitza aquests fitxers en comptes de reimplementar-los.

---

## 2. Catàleg curat — OpenData BCN

Filtrat manualment de `cataleg_slim.csv` (555 datasets totals). Criteri: rellevància per a dinàmiques socioeconòmiques/espacials a nivell de secció censal o barri. **Exclosos deliberadament:** ~130 datasets de cadastre detallat i parc de vehicles (massa granulars/administratius, disponibles al catàleg complet si calen).

**Unitats territorials**
- `20170706-districtes-barris` — Districtes, barris, AEB i seccions censals de BCN (crosswalk base)
- `taula-direle-sec-cens` — Adreces per secció censal (útil per geocodificar altres fonts)

**Renda i desigualtat**
- `atles-renda-index-gini` — Índex de Gini per secció
- `atles-renda-mitjana` / `atles-renda-mediana` — Renda per unitat de consum
- `atles-renda-p80-p20-distribucio` — Ràtio de distribució (proxy de polarització)
- `renda-disponible-llars-bcn` — Renda disponible per càpita

**Vulnerabilitat i exposició climàtica**
- `factor-de-vulnerabilitat` — Vulnerabilitat davant onada de calor
- `poblacio-vulnerable` — Exposició a impacte de calor
- `formacio-poblacio-insuficient` — Vulnerabilitat per situació socioeconòmica insuficient
- `equipaments-i-parcs-refugi` — Espais refugi climàtic
- `deficit-proximitat-espai-verd` — Dèficit d'accés a espai verd
- `contaminacio-atmosferica` / `risc-contaminacio-acustica` — Exposició ambiental
- `temperatures-hist-bcn` — Sèrie de temperatures des de 1780

**Habitatge**
- `exclusio-residencial` — Àrees amb risc d'exclusió residencial
- `manteniment-lloguer` — Esforç econòmic per lloguer
- `densitat-huts` / `habitatges-us-turistic` — Pressió turística sobre habitatge
- `est-cadastre-habitatges-edat-mitjana` — Edat mitjana del parc d'habitatge (proxy d'estat/resiliència estructural)

**Demografia fina (Padró)**
- `pad_mdba_sexe_edat-q` — Població per sexe i edat quinquennal
- `pad_mdb_nacionalitat-g_edat-q_sexe` — Població per nacionalitat i edat

**Geografia política** *(paral·lel estructural per homofília espacial — no assumir causalitat)*
- `est-eleccions-generals-seccio-censal` / `est-eleccions-locals-seccio-censal` — Resultats electorals per secció censal

---

## 3. Fonts específiques — casos municipals (Terrassa, Sabadell, Sant Cugat...)

Aplicables quan un projecte necessita cartografia, zonificació o equipaments a nivell de municipi concret, no només indicadors socioeconòmics.

| Dataset | Font | Descripció |
|---|---|---|
| Cartografia censal | INE — *Cartografía censal*, província de Barcelona | Shapefile, EPSG:25830 (UTM fus 30N). 3.656 seccions/província. Càlculs geoespacials sempre en metres (UTM); conversió a WGS84 (EPSG:4326) només per a visualització Leaflet. Parseig amb Python `struct` (sense geopandas); filtrar per `CUMUN`. |
| Zones de planificació educativa | Departament d'Educació (Gencat), dades obertes | Geometries en WKT per comarca (p. ex. `zones_Vallès_Occidental.csv`). Assignació secció→zona per intersecció del centroide amb el polígon. |
| Matrícula per centre | Departament d'Educació | `Alumnes_matriculats_per_ensenyament_i_unitats...xlsx`. Codificació original **latin-1**. |
| Plantilles docents / condició CMC | Departament d'Educació | Base per identificar Centres de Màxima Complexitat. |
| Distàncies a peu | OSRM (recomanat) vs. haversine (aprox., insuficient per a publicació) | Haversine sobreestima connectivitat real en trames urbanes irregulars — usar només com a primera aproximació, mai com a xifra final publicable. |

---

## 4. Notes tècniques transversals (errors ja resolts, no repetir)

- **Conversió de codis INE↔Idescat:** Idescat fa servir codis de secció de **11 caràcters**, amb un `8` inserit a la posició 5 respecte al CUSEC estàndard de l'INE (10 caràcters). Ex.: INE `0818701001` → Idescat `08187801001`.
- **Codis de centre educatiu:** el camp `codi_centre` als fitxers d'escoles té 7 dígits; els fitxers de matrícula el fan servir amb 8 dígits (zero-padded). Cal `zfill(8)` sobre el fitxer d'escoles abans de fer el join. Filtrar matrícula pel conjunt conegut de centres, no pel codi de municipi.
- **Encodings variables per font:** CSV educatiu BCN → UTF-16; plantilles docents → `utf-8-sig`; Excel INE 30896 → dades útils a partir de la fila 8, primeres 19 columnes.
- **Agrupacions censals ≠ seccions censals:** Idescat publica IST a dos nivells (853 agrupacions vs. 5.108 seccions a Catalunya). Les agrupacions suavitzen la variabilitat local — per a qualsevol anàlisi de gradient o de talls administratius, usar sempre seccions, no agrupacions.
- **Contigüitat espacial:** criteri *queen* (inclou diagonals), no *rook* — estàndard per a tesselacions urbanes irregulars com les seccions censals. Implementació pràctica: arrodonir coordenades a 1m de precisió i buscar coincidències de vèrtex.
- **Adreces sense geocodificar:** alguns datasets (p. ex. centres educatius OpenData BCN) venen per adreça, no per CUSEC. Cal geocodificar i fer join espacial (punt dins polígon de secció) abans d'incorporar-los a qualsevol anàlisi per secció.

---

*Mantenidor: Pere Losantos · perelosantos.com · repo: laboratori-de-dades*
