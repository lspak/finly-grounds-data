# finly-grounds-data

Hranice a reprezentatívne body rybárskych revírov SRZ (Slovenský rybársky zväz) pre appku Finly, plus export registra revírov a miestnych mier, ktorý appka nesie v sebe.

## Zdroje geometrie

Od vydania 3 sú zdroje dva a každý `Feature` hovorí vlastnosťou `source`, z ktorého pochádza:

- `osm`: vodné plochy (štrkoviská, nádrže) obtiahnuté v OpenStreetMap.
- `svp`: úseky tokov vyrezané zo siete vodných tokov mapovej služby Slovenského vodohospodárskeho podniku, š. p.

## Licencia

Geometria v `geometry/` a body v `points.json` sú zverejnené pod [Open Database License (ODbL) 1.0](LICENSE).

- Prvky so `source: "osm"` sú databáza odvodená z OpenStreetMap: © prispievatelia OpenStreetMap.
- Prvky so `source: "svp"` sú odvodené z mapovej služby Slovenského vodohospodárskeho podniku, š. p., a sú tu zverejnené s jeho súhlasom: © Slovenský vodohospodársky podnik, š. p.

Register v `register/` je prepis rybárskych poriadkov SRZ 2026 (kaprový zväzový, lipňový zväzový a poriadok pre držiteľov zvláštneho povolenia); jeho reprezentatívne body pochádzajú z oboch zdrojov vyššie, preto je zverejnený tu pod tou istou licenciou. Miery v `register/rules.json` nie sú právne poradenstvo: platí vyhláška č. 381/2018 Z. z. a aktuálne znenie poriadku revíru.

Hranica revíru je orientačná pomôcka, nie právny podklad. Pri hranici, ktorú opis poriadku určuje orientačným bodom (`boundaryAccuracy: "landmark"`), sú konce úseku presné rádovo na stovky metrov.

## Súbory

- `manifest.json`: `format`, `release` (rastie s každým vydaním), `updatedAt`, `note`, `attribution` (pri viacerých zdrojoch nesie credit každého z nich, uvedený hodnotou `source`) a `files` s `url`, `bytes`, `sha256` a `features` pre každý súbor s geometriou. Appka sťahuje manifest, porovná `release` a stiahne len súbory so zmeneným `sha256`.
- `geometry/region-N.geojson`: `FeatureCollection` pre SRZ oblasť N (1 Bratislavská, 2 Západoslovenská, 3 Stredoslovenská, 4 Východoslovenská), s vlastným `attribution` pre zdroje, ktoré sú v ňom. Každý `Feature` nesie `number` (číslo revíru SRZ), `name`, `shape` (`area` alebo `line`), `source`, `verifiedAt`, `checked` (`by`, `on`, `how`: kto, kedy a ako porovnal geometriu s opisom revíru v poriadku SRZ; doteraz je to vždy porovnanie od stola, nie obhliadka na vode), `bbox`, `point` a `vertices`. Geometria je zjednodušená (Douglas-Peucker 5 m).
  - Prvok so `source: "osm"` nesie navyše `osmIds`.
  - Prvok so `source: "svp"` nesie navyše `idTok` (identifikátor toku v službe), `boundaryAccuracy` (`rkm`, `confluence`, `source` alebo `landmark` podľa toho, ako bol určený slabší z dvoch koncov) a `ends` (ktorým spôsobom bol prečítaný každý koniec).
- `points.json`: číslo revíru → reprezentatívny bod (`latitude`, `longitude`), ktorý appka nesie v registri ako `point`. Pri vodnej ploche je to centroid, pri úseku toku stred úseku; appka k nemu dopisuje `pointSource` (`osm` alebo `svp`).
- `register/grounds.json`, `register/rules.json`: export tabuliek `src/config/grounds/` appky.

## Ako vzniká

Skripty sú v repozitári appky ([lspak/Finly](https://github.com/lspak/Finly)), postup v `tools/grounds/README.md` tam.

- `tools/grounds/build_geometry.py` z ručne písaných výberov `tools/grounds/sources/<číslo>.json` (ktoré prvky OSM tvoria revír a kto ich skontroloval) stiahne geometriu z OSM API a zjednoduší ju.
- `tools/grounds/build_rivers.py` z opisu revíru v poriadku SRZ nájde tok v službe SVP, určí oba konce úseku, vyreže ho a zapíše vydanie spolu s vodnými plochami.

Revír bez záznamu o kontrole (`checked`) sa nezverejňuje v ani jednom z oboch prípadov.
