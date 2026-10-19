# artifacts-manager

Socle Maven de la plateforme (ancien nom du dépôt : `craft-platform` ; l'artifactId de l'agrégateur reste
`craft-platform`). Ce dépôt ne contient aucune application : il produit ce dont les services héritent pour se
construire de la même façon, et la configuration Maven qui fait de Nexus le seul accès aux dépendances.
Vue d'ensemble du socle : [platform-handbook](https://github.com/myc-public/platform-handbook).

## Contenu

| Module / fichier | Rôle |
|---|---|
| [`craft-parent`](craft-parent/pom.xml) | POM parent hérité par tous les services (`<parent>`) : Java 21, Spring Boot 4.0.0, plugins (compiler, surefire, failsafe…), `distributionManagement` vers Nexus. Version courante : `2.3.0-SNAPSHOT`, historique dans [craft-parent/CHANGELOG.md](craft-parent/CHANGELOG.md) |
| [`craft-build-config`](craft-build-config/pom.xml) | Configurations qualité packagées (checkstyle, spotbugs…) consommées par les plugins ; module encore vide |
| [`settings.xml`](settings.xml) | `settings.xml` de référence : un `<mirror>` générique envoie toute résolution vers le groupe Nexus `maven-public` |

Un BOM (alignement des versions par `import`) est prévu ; il n'existe pas encore.

Chemin d'une dépendance : Maven Central -> proxy Nexus -> dépôt Nexus -> `~/.m2` (poste ou Jenkins) -> build.

## Utiliser le socle

Poste de développement :

```powershell
Copy-Item D:\workspace\public\artifacts-manager\settings.xml $HOME\.m2\settings.xml -Confirm
# NEXUS_URL, NEXUS_USERNAME, NEXUS_PASSWORD en variables d'environnement (jamais dans un fichier versionné)
```

Jenkins : le même contenu est un « Managed file » (id `nexus-maven-settings`), identifiants `nexus-credentials`.

Service consommateur : hériter de `craft-parent` ; ne pas redéclarer `distributionManagement`.

## Build et publication

```powershell
mvn -pl craft-parent,craft-build-config -am install   # installation locale
mvn verify                                            # tout le réacteur
mvn deploy                                            # publication dans Nexus (maven-snapshots / maven-releases)
```

Aucun job Jenkins ne construit ce dépôt : la publication se fait depuis le poste.

## Liens

- Nexus, tunnel et dettes associées (3, 8, 12) : [roadmap/cicd.md](https://github.com/myc-public/platform-handbook/blob/develop/roadmap/cicd.md),
  [dettes.md](https://github.com/myc-public/platform-handbook/blob/develop/dettes.md).
