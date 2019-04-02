## Fusionner des géométries

On souhaite créer une seule géométrie qui est issue de la **fusion de toutes les géométries** regroupées par un critère (nature, code, etc.)

Par exemple un polygone fusionnant les zonages qui partagent le même type

```sql
SELECT count(id_zonage) AS nb_objets, typezone,
ST_Union(geom) AS geom
FROM urbanisme.zonage
GROUP BY typezone
```

On souhaite parfois **fusionner toutes les géométries qui sont jointives**.
Par exemple, on veut fusionner **toutes les parcelles jointives** pour créer des blocs.

```sql
SELECT row_number() OVER() AS id, string_agg(idpar::text, ',') AS ids, t.geom
FROM (
        SELECT
        (St_Dump(St_Union(a.geom))).geom AS geom
        FROM urbanisme.parcelle AS a
        WHERE TRUE
        AND ST_IsValid(a.geom)
) t
INNER JOIN urbanisme.parcelle AS p
        ON ST_Intersects(p.geom, t.geom)
GROUP BY t.geom
;
```