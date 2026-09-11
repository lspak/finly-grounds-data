# finly-grounds-data

Hranice a reprezentatívne body rybárskych revírov SRZ (Slovenský rybársky zväz) pre appku Finly, plus export registra revírov a miestnych mier, ktorý appka nesie v sebe.

## Licencia

Geometria v `geometry/` a body v `points.json` sú databáza odvodená z OpenStreetMap a sú zverejnené pod [Open Database License (ODbL) 1.0](LICENSE): © prispievatelia OpenStreetMap. Register v `register/` je prepis rybárskych poriadkov SRZ 2026 (kaprový zväzový, lipňový zväzový a poriadok pre držiteľov zvláštneho povolenia); jeho reprezentatívne body pochádzajú z OSM, preto je zverejnený tu pod tou istou licenciou. Miery v `register/rules.json` nie sú právne poradenstvo: platí vyhláška č. 381/2018 Z. z. a aktuálne znenie poriadku revíru.

## Súbory

- `manifest.json`: `format`, `release` (rastie s každým vydaním), `updatedAt`, `note`, `attribution`, `files` s `url`, `bytes`, `sha256` a `features` pre každý súbor s geometriou. Appka sťahuje manifest, porovná `release` a stiahne len súbory so zmeneným `sha256`.
- `geometry/region-N.geojson`: `FeatureCollection` pre SRZ oblasť N (1 Bratislavská, 2 Západoslovenská, 3 Stredoslovenská, 4 Východoslovenská). Každý `Feature` nesie `number` (číslo revíru SRZ), `name`, `shape` (`area` alebo `line`), `osmIds`, `verifiedAt`, `checkedByHuman`, `bbox`, `point` a `vertices`. Geometria je zjednodušená (Douglas-Peucker 5 m).
- `points.json`: číslo revíru → reprezentatívny bod (`latitude`, `longitude`), ktorý appka nesie v registri ako `point` so `pointSource: 'osm'`.
- `register/grounds.json`, `register/rules.json`: export tabuliek `src/config/grounds/` appky.

## Ako vzniká

Skript `tools/grounds/build_geometry.py` v repozitári appky ([lspak/Finly](https://github.com/lspak/Finly)) z ručne písaných výberov `tools/grounds/sources/<číslo>.json` (ktoré prvky OSM tvoria revír a kto ich skontroloval) stiahne geometriu z OSM API, zjednoduší ju a zapíše sem. Revír bez záznamu o kontrole sa nezverejňuje. Postup je v `tools/grounds/README.md` tam.
