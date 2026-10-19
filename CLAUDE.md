# artifacts-manager (ancien nom : craft-platform ; artifactId Maven inchangé)

Dépôt de bibliothèques Maven multi-modules. Aucune application : rien n'y est déployable, et aucun module ne
contient `@SpringBootApplication`. Lire d'abord [README.md](README.md). Règles communes du socle :
`platform-handbook`.

## Commandes

- Build : `mvn -pl craft-parent,craft-build-config -am install`
- Vérification (tout le réacteur) : `mvn verify`

## Nexus, seul accès entrant et sortant

- Aucun poste ni pipeline ne résout depuis Maven Central, ni ne publie ailleurs, en direct : tout passe par Nexus
  (proxy `maven-central` -> groupe `maven-public` ; dépôts `maven-releases` / `maven-snapshots` pour ce qu'on publie).
- Le point de contrôle est [`settings.xml`](settings.xml) (`<mirror>` générique), pas un `pom.xml`.
- `craft-parent` et l'agrégateur déclarent `<distributionManagement>` vers Nexus ; un service en hérite et ne le
  redéclare pas.

## Rôles des artefacts (ne pas les mélanger)

1. `craft-parent` : POM **hérité** (`<parent>`).
2. BOM (prévu, pas encore créé) : POM **importé** (`<dependencyManagement>`, scope `import`).
3. Configurations qualité (`craft-build-config`) : jars consommés par les plugins, jamais en dépendance de compilation.

Un module ne joue qu'un seul de ces rôles.

## Règles pour `craft-parent`

- Aucun code, aucune dépendance métier (bibliothèque utilisée à l'exécution) : seulement le cycle de build
  (plugins, `pluginManagement`, propriétés).
- Tout changement est une release : version augmentée et entrée dans `craft-parent/CHANGELOG.md`, même pour une
  petite modification.
