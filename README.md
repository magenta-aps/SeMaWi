SeMaWi bygger på MediaWiki og Semantic MediaWiki med formålet at implementere en model for forvaltning af en kommunal softwareportefølje. SeMaWi indeholder:

1. En levende datamodel der afspejler praktisk og anvendeligt softwareportefølje domænet i de danske kommuner, med udgangspunkt i en prototype som Ballerup Kommunes Center for Miljø og Teknik har bidraget med, og
2. Analytiske elementer der har formålet at understøtte evidensbaseret og kvantitativ porteføljeforvaltning.

SeMaWi er udviklet i samarbejde med [Josef Assad](mailto:josef@josefassad.com).

# Infrastruktur
1. Debian 8
2. Apache, MySQL, og php fra standard Debian repositorier
3. Mediawiki (herefter MW) 1.26.2; [installationsvejledning](https://www.mediawiki.org/wiki/Manual:Installing_MediaWiki)
4. Semantic Mediawiki (hereafter SMW) 2.3 [installationsvejledning](https://semantic-mediawiki.org/wiki/Help:Installation/Using_Composer_with_MediaWiki_1.22%2B)

# Installation

## Docker installation (anbefalet)

Docker er den anbefalede installations- og driftsmetode for SeMaWi. Følg vejledningen i folderen `docker/README.md`.

## Manuel installation (legacy)

1. Installer [Semantic Mediawiki](https://semantic-mediawiki.org/wiki/Help:Installation/Using_Composer_with_MediaWiki_1.22%2B)
2. Installer  [DataTransfer](https://www.mediawiki.org/wiki/Extension:Data_Transfer) udvidelsen til MW; installationsvejledning på samme side
3. Installer [SemanticForms](https://www.mediawiki.org/wiki/Extension:Semantic_Forms/Download_and_installation)
4. Installer [Semantic Result formats](https://semantic-mediawiki.org/wiki/Semantic_Result_Formats#Installation).
5. Deaktiver property caching i SMW; i SemanticMediawiki.settings.php skift `'smwgPropertiesCache' => true,` til `'smwgPropertiesCache' => false,`.
6. I SMW Special:Import, importer filen structure.xml
7. I Linux kommandolinjen, navigér til MW maintenance folderen og kør kommandoen `php runJobs.php`
8. Skift sidelogo efter behov; instruktioner [her](https://www.mediawiki.org/wiki/Manual:$wgLogo).
9. Referer til filen `docker/LocalSettings.php` for at konfigurere SeMaWi korrekt.
11. Installer [MasonryMainPage](https://github.com/enterprisemediawiki/MasonryMainPage).
15. (optionelt) Installer [Chameleon tema'et](https://www.mediawiki.org/wiki/Skin:Chameleon). Det aktiveres ved at finde linjen i `LocalSettings.php` som siger `$wgDefaultSkin = "vector";` of udskifte `vector` med `chameleon`.

Til brugeroprettelse kan det anbefales at installere udvidelsen [ImportUsers](https://www.mediawiki.org/wiki/Extension:ImportUsers). Dog er det smart at afinstallere eller som minimum deaktivere udvidelsen igen umiddelbart efter; import af brugere burde ikke være en øvelse der skal gentages for ofte, og målet på sigt er at anvende enten en AD/LDAP eller [OS2MO](http://www.os2web.dk/projekter/os2mo) som ekstern autoritativ brugerkilde.

## Mapcentia GC2 Tabeller

SeMaWi understøtter integration til Mapcentia GeoCloud2. Geodata tabeller kan vises som sider i Geodata kategoriet i SeMaWi, hvilket gør det muligt at lave analyser på de tabeller og at se dem i sammenhæng med øvrige entiteter som KLE emner eller systemer eller brugere.

### Batchimport

Der er udviklet et python script som skal køre i cronjob på SeMaWi serveren for at opdatere SeMaWi med de geodata tabeller GC2 indeholder. Installation foregår således:

1. I `LocalSettings.php` skal der tilføjes `$wgRawHtml = true;`. Det er så kortene kan vises.
2. I SeMaWi skal der oprettes en ny bruger med brugernavn Sitebot. Denne bruger skal være medlem af følgende brugergrupper: robot, administrator, bureaukrat
3. Scriptet `gc2/gc2smwdaemon.py` skal kaldes fra et cronjob så det kører på et passende tidspunkt med de korrekte SeMaWi Sitebot login og GC2 API oplysninger. Oplysningerne om Sitebot og GC2 API endpoint skrives i de relevante variabler i `gc2/gc2smwdaemon.py` filen. Bemærk, `gc2/gc2smwdaemon.py` skal køre fra et `virtualenv` som har alle afhængigheder fra `gc2/requirements.txt` installeret korrekt. Det er ude for scope i denne vejledning at dokumentere hvordan man anvender `virtualenv` eller opretter et cronjob i Linux.

Optionelt kan det anbefales at køre de to maintenance scripts `SMW_refreshData.php` og derefter `runJobs.php` efter.

### Engangsimport

Hvis det ønskes kan tabellerne fra Mapcentia GC2 indlæses en gang og derefter holdes ajour manuelt på begge sider, SeMaWi og GC2. Det er langt de færreste tilfælde hvor dette er ønskværdigt.

1. I `LocalSettings.php` skal der tilføjes `$wgRawHtml = true;`. Det er så kortene kan vises.
2. Download json filen fra GC2
3. Anvend scriptet `gc2/gc2smw.py` som genererer en CSV fil med tabellerne. Det anbefales at bruge et python virtualenv; filen `gc2/requirements.txt` lister krævede biblioteker.
4. Indlæs `gc2/geodata-struktur.xml`
5. Indlæs den genererede CSV fil
6. Kør de to backend scripts `SMW_refreshData.php` og derefter `runJobs.php`.

# Opgradering

Se dokumentation under docker mappen.

# Noter

MediaWiki som er fundamentet for SeMaWi er designet til store sites. Mange deployments kan være relativ små ift. MediaWiki's primære use case som er WikiPedia. Som følge kan det blive nødvendigt med nogle små workarounds som fx. kørsel af `runJobs.php` og/eller `SMW_refreshData.php` i cronjobs.

# Licens

Kode og data i SeMaWi er copyright Ballerup Kommune 2016 med mindre andet er specificeret i de enkelte filer.

SeMaWi er open source. Der er frit valg mellem [GPLv3](http://www.gnu.org/licenses/gpl-3.0.en.html) licensen eller [Creative Commons Attribution-ShareAlike 3.0 Unported](http://creativecommons.org/licenses/by-sa/3.0/).
