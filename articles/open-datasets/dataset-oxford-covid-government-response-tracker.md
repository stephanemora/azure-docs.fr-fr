---
title: Tracker des réponses du gouvernement au COVID-19 d’Oxford
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données Oxford COVID-19 Government Response Tracker dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: a39c8915027cddeb168f5bb0c63f915492ece398
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444611"
---
# <a name="oxford-covid-19-government-response-tracker"></a>Tracker des réponses du gouvernement au COVID-19 d’Oxford

Le [jeu de données Oxford Covid-19 Government Response Tracker (OxCGRT)](https://github.com/OxCGRT/covid-policy-tracker/) contient des informations systématiques sur les gouvernements qui ont pris telle ou telle mesure, et à quel moment.

Ces informations peuvent aider les décideurs et les citoyens à comprendre les réponses gouvernementales de manière cohérente, contribuant ainsi aux efforts de lutte contre la pandémie. L'OxCGRT recueille systématiquement les informations relatives à différentes réponses politiques communes prises par les gouvernements, place ces politiques sur une échelle afin de refléter l'étendue de l'action gouvernementale, et agrège ces scores en une série d'indices politiques.


[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>Groupes de données

Des versions modifiées du jeu de données sont disponibles aux formats CSV, JSON, JSON-Lines et Parquet ; celles-ci sont mises à jour quotidiennement :
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet

Des codes iso_pays et des temps de chargement ont été ajoutés à toutes les versions modifiées. En outre, celles-ci utilisent des noms de colonnes en minuscules et des tirets bas en guise de séparateurs.

Données brutes : https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_policy_tracker/latest/CovidPolicyTracker.csv

Versions précédentes des données modifiées et brutes : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/ https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_policy_tracker/

## <a name="data-volume"></a>Volume de données

Au 8 juin 2020, on comptait 27 919 lignes (CSV 4,9 Mo, JSON 20,9 Mo, JSONL 20,8 Mo, Parquet 133 Ko).

## <a name="data-source"></a>Paramètres

La source de ces données est attribuée à Thomas Hale, Sam Webster, Anna Petherick, Toby Phillips et Beatriz Kira. (2020). OxCGRT (Oxford Covid-19 Government Response Tracker). [Blavatnik School of Government](https://www.bsg.ox.ac.uk/). Les données brutes sont ingérées quotidiennement à partir du [fichier .csv d’OxCGRT le plus récent](https://github.com/OxCGRT/covid-policy-tracker/blob/master/data/OxCGRT_latest.csv). Pour plus d’informations sur ce jeu de données, notamment sur la façon dont il est collecté, consultez le [site relatif au dispositif de suivi des réponses des gouvernements](https://www.bsg.ox.ac.uk/research/research-projects/covid-19-government-response-tracker).

## <a name="data-quality"></a>Qualité des données
L'OxCGRT ne garantit pas l'exactitude ou la pertinence des données. Pour plus d’informations, reportez-vous à la [déclaration relative à la qualité des données](https://github.com/OxCGRT/covid-policy-tracker#data-quality).

## <a name="license-and-use-rights-attribution"></a>Attribution des licences et des droits d’utilisation

Ces données sont concédées sous licence dans le cadre de la [licence internationale Creative Commons Attribution 4.0](https://github.com/OxCGRT/covid-policy-tracker/blob/master/LICENSE.txt).

Noms à citer : Thomas Hale, Sam Webster, Anna Petherick, Toby Phillips et Beatriz Kira. (2020). OxCGRT (Oxford Covid-19 Government Response Tracker). Blavatnik School of Government.

## <a name="contact"></a>Contact

Pour toute question ou tout commentaire sur ce jeu de données ou sur d’autres jeux de données dans le lac de données COVID-19, contactez askcovid19dl@microsoft.com.

## <a name="columns"></a>Colonnes

| Nom                                  | Type de données | Unique | Valeurs (exemple)            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|---------------------------------------|-----------|--------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| c1_flag                               | boolean   | 3      | True                       | Indicateur binaire pour l'étendue géographique. 0 - Ciblé 1 - Général Vide - Absence de données                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| c1_school_closing                     | double    | 5      | 3 2                    | Fermeture des écoles et des universités. 0 - Aucune mesure 1 - Fermeture recommandée 2 - Fermeture exigée (pour certains niveaux ou certaines catégories uniquement, par exemple uniquement les lycées ou uniquement les écoles privées) 3 - Fermeture de tous les niveaux exigée Vide - Absence de données                                                                                                                                                                                                                                                                                                 |
| c2_flag                               | boolean   | 3      | True                       | Indicateur binaire pour l'étendue géographique. 0 - Ciblé ; 1 - Général ; Vide - Absence de données                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| c2_workplace_closing                  | double    | 5      | 2 1                    | Fermeture des lieux de travail. 0 - Aucune mesure 1 - Fermeture ou télétravail recommandés 2 - Fermeture ou télétravail exigés dans certains secteurs ou pour certaines catégories de travailleurs 3 - Fermeture ou télétravail exigés pour tous les lieux de travail non essentiels (par exemple, magasins d’alimentation, médecins) Vide - Absence de données                                                                                                                                                                                                                          |
| c3_cancel_public_events               | double    | 4      | 2 1                    | Annulation des événements publics. 0 - Aucune mesure 1 - Annulation recommandée 2 - Annulation exigée Vide - Absence de données                                                                                                                                                                                                                                                                                                                                                                                                                    |
| c3_flag                               | boolean   | 3      | True                       | Indicateur binaire pour l'étendue géographique. 0 - Ciblé 1 - Général Vide - Absence de données                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| c4_flag                               | boolean   | 3      | True                       | Indicateur binaire pour l'étendue géographique 0 - Ciblé 1 - Général Vide - Absence de données                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c4_restrictions_on_gatherings         | double    | 6      | 4 3                    | Limitation des rassemblements privés. 0 - Aucune restriction 1 - Restrictions portant sur les très grands rassemblements (limitation supérieure à 1 000 personnes) 2 - Restrictions portant sur les rassemblements de 101 à 1 000 personnes 3 - Restrictions portant sur les rassemblements de 11 à 100 personnes 4 - Restrictions portant sur les rassemblements de 10 personnes ou moins Vide - Absence de données                                                                                                                                                                                                                 |
| c5_close_public_transport             | double    | 4      | 1,0 2,0                    | Fermeture des transports publics 0 - Aucune mesure 1 - Fermeture recommandée (ou forte réduction du volume/des trajets/des moyens de transport disponibles) 2 - Fermeture exigée (ou interdiction de les emprunter pour la plupart des citoyens) Vide - Absence de données                                                                                                                                                                                                                                                                                                          |
| c5_flag                               | boolean   | 3      | True                       | Indicateur binaire pour l'étendue géographique 0 - Ciblé 1 - Général Vide - Absence de données                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c6_flag                               | boolean   | 3      | True                       | Indicateur binaire pour l'étendue géographique 0 - Ciblé 1 - Général Vide - Absence de données                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c6_stay_at_home_requirements          | double    | 5      | 1,0 2,0                    | Mesures de confinement 0 - Aucune mesure 1 - Confinement à domicile recommandé 2 - Interdiction de sortir de chez soi, sauf pour l’exercice quotidien, les courses et les déplacements « essentiels » 3 - Interdiction de sortir de chez soi, avec très peu d’exceptions (par exemple, autorisation de déplacement une fois par semaine, ou déplacement d’une seule personne à la fois, etc.) Vide - Absence de données                                                                                                                                                |
| c7_flag                               | boolean   | 3      | True                       | Indicateur binaire pour l'étendue géographique 0 - Ciblé 1 - Général Vide - Absence de données                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c7_restrictions_on_internal_movement  | double    | 4      | 2 1                    | Mesures de restriction en matière de déplacements intérieurs entre les villes/régions 0 - Aucune mesure 1 - Recommandation de ne pas voyager entre les régions/villes 2 - Restrictions en vigueur sur les déplacements intérieurs Vide - Absence de données                                                                                                                                                                                                                                                                                                                                      |
| c8_international_travel_controls      | double    | 6      | 3 4                    | Restrictions sur les voyages internationaux. Remarque : Politique s’appliquant aux voyageurs étrangers, et non aux citoyens 0 - Aucune restriction 1 - Contrôle des arrivées 2 - Mise en quarantaine des personnes en provenance de certaines régions ou du monde entier 3 - Interdiction d’entrée des personnes en provenance de certaines régions 4 - Interdiction d’entrée des personnes en provenance du monde entier ou fermeture totale des frontières Vide - Absence de données                                                                                                                                                                                                                           |
| confirmedcases                        | SMALLINT  | 18 238 | 1 2                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| confirmeddeaths                       | SMALLINT  | 14 906 | 1 2                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| countrycode                           | string    | 186.    | USA BRA                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| countryname                           | string    | 186.    | États-Unis Brésil       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Date                                  | Date      | 478    | 2020-08-25 2021-03-30      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| e1_flag                               | boolean   | 3      | True                       | Indicateur binaire pour l’étendue sectorielle 0 - Travailleurs du secteur formel uniquement 1 - Transferts aux travailleurs du secteur informel également Vide - Absence de données                                                                                                                                                                                                                                                                                                                                                                                                          |
| e1_income_support                     | double    | 4      | 1,0 2,0                    | Politique gouvernementale en matière d'indemnités aux personnes qui perdent leur emploi ou ne peuvent pas travailler. Remarque : inclut uniquement les indemnités versées aux entreprises si elles sont explicitement liées aux salaires 0 - Aucune aide 1 - Le gouvernement compense moins de 50 % du salaire perdu (ou moins de 50 % du salaire médian en cas de montant forfaitaire) 2 - Le gouvernement compense 50 % ou plus du salaire perdu (ou plus de 50 % du salaire médian en cas de montant forfaitaire) Vide - Absence de données                                                                        |
| e2_debt/contract_relief               | double    | 4      | 1,0 2,0                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| e3_fiscal_measures                    | double    | 819    | -0,01 3                  | Dépenses de relance économique annoncées Remarque : Montant qui s’ajoute aux dépenses déjà annoncées uniquement Valeur en USD des mesures de relance budgétaire, y compris toute dépense ou réduction d’impôt NON incluse dans E4, H4 ou H5 0 - Pas de nouvelle dépense ce jour-là Vide - Absence de données                                                                                                                                                                                                                                                               |
| e4_international_support              | double    | 113    | -0,02 5000000            | Annonce d’aides en lien avec la Covid-19 à d’autres pays Remarque : Montant qui s’ajoute aux dépenses déjà annoncées uniquement Valeur en USD 0 - Pas de nouvelles dépenses ce jour-là Vide - Absence de données                                                                                                                                                                                                                                                                                                                 |
| h1_flag                               | boolean   | 3      | True                       | Indicateur binaire pour l'étendue géographique 0 - Ciblé 1 - Général Vide - Absence de données                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| h1_public_information_campaigns       | double    | 4      | 2 1                    | Campagnes d’information publique 0 - Aucune campagne d’information publique sur la Covid-19 1 - Les autorités publiques incitent à la prudence concernant la Covid-19 2- Campagne d’information publique coordonnée (par exemple, dans les médias traditionnels et sur les réseaux sociaux) Vide - Absence de données                                                                                                                                                                                                                                                                                         |
| h2_testing_policy                     | double    | 5      | 2 1                    | Politique gouvernementale en matière d’accès aux tests Remarque : Politiques relatives aux tests de dépistage de l’infection (tests PCR) et non aux tests d’immunité (tests sérologiques recherchant des anticorps) 0 - Aucune politique de dépistage 1 - Uniquement les personnes qui (a) présentent des symptômes ET (b) répondent à des critères spécifiques (par exemple, personnes exerçant des professions essentielles, admises à l’hôpital, ayant été en contact avec un cas avéré ou revenant de l’étranger) 2 - Dépistage de toute personne présentant des symptômes de la Covid-19 3 - Dépistage à grande échelle (par exemple, tests en « drive » accessibles aux personnes asymptomatiques) Vide - Absence de données |
| h3_contact_tracing                    | double    | 4      | 2 1                    | Politique gouvernementale en matière de recherche des cas contacts après un diagnostic positif Remarque : nous recherchons les politiques qui permettraient d'identifier toutes les personnes potentiellement exposées à la Covid-19 ; il est peu probable que les applications Bluetooth basées sur le volontariat permettent d'atteindre cet objectif 0 - Pas de recherche des cas contacts 1 - Recherche limitée des cas contacts ; pas pour tous les cas 2 - Recherche approfondie des cas contacts ; pour tous les cas identifiés                                                                                                                                                          |
| h4_emergency_investment_in_healthcare | double    | 462    | 35 562                 | Dépenses à court terme annoncées pour le système de santé (hospitalisations, masques, etc.) Remarque : Montant qui s’ajoute aux dépenses déjà annoncées uniquement Valeur en USD 0 - Pas de nouvelles dépenses ce jour-là Vide - Absence de données                                                                                                                                                                                                                                                                                                         |
| h5_investment_in_vaccines             | double    | 133    | 1 191                  | Dépenses publiques annoncées pour la mise au point d'un vaccin contre la Covid-19 Remarque : montant qui s'ajoute aux dépenses déjà annoncées uniquement Valeur en USD 0 - Pas de nouvelles dépenses ce jour-là Vide - Absence de données                                                                                                                                                                                                                                                                                                                            |
| iso_country                           | string    | 186.    | US BR                      | Code pays ou région ISO 3166                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| load_date                             | timestamp | 1      | 2021-04-26 00:06:25.157000 | Les données relatives à la date et à l'heure ont été chargées à partir d'une source externe                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| stringencyindex                       | double    | 188    | 11,11 60,19                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| stringencyindexfordisplay             | double    | 188    | 11,11 60,19                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="preview"></a>Préversion

| countryname | countrycode | Date       | c1_school_closing | c2_workplace_closing | c3_cancel_public_events | c4_restrictions_on_gatherings | c5_close_public_transport | c6_stay_at_home_requirements | c7_restrictions_on_internal_movement | c8_international_travel_controls | e1_income_support | e2_debt/contract_relief | e3_fiscal_measures | e4_international_support | h1_public_information_campaigns | h2_testing_policy | h3_contact_tracing | h4_emergency_investment_in_healthcare | h5_investment_in_vaccines | m1_wildcard | stringencyindex | stringencyindexfordisplay | iso_country | load_date             |
|-------------|-------------|------------|-------------------|----------------------|-------------------------|-------------------------------|---------------------------|------------------------------|--------------------------------------|----------------------------------|-------------------|-------------------------|--------------------|--------------------------|---------------------------------|-------------------|--------------------|---------------------------------------|---------------------------|-------------|-----------------|---------------------------|-------------|-----------------------|
| Aruba       | ABW         | 2020-01-01 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 26/04/2021 12:06:25 |
| Aruba       | ABW         | 2020-01-02 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 26/04/2021 12:06:25 |
| Aruba       | ABW         | 2020-01-03 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 26/04/2021 12:06:25 |
| Aruba       | ABW         | 2020-01-04 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 26/04/2021 12:06:25 |
| Aruba       | ABW         | 2020-01-05 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 26/04/2021 12:06:25 |
| Aruba       | ABW         | 2020-01-06 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 26/04/2021 12:06:25 |
| Aruba       | ABW         | 2020-01-07 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 26/04/2021 12:06:25 |
| Aruba       | ABW         | 2020-01-08 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 26/04/2021 12:06:25 |
| Aruba       | ABW         | 09-01-2020 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 26/04/2021 12:06:25 |
| Aruba       | ABW         | 2020-01-10 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 26/04/2021 12:06:25 |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=oxford-covid-19-government-response-tracker -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=oxford-covid-19-government-response-tracker)** .

## <a name="this-notebook-documents-the-urls-and-sample-code-to-access-the-oxford-covid-19-government-response-tracker-oxcgrt-dataset"></a>Ce notebook documente les URL et les exemples de code permettant d’accéder au jeu de données Oxford Covid-19 Government Response Tracker (OxCGRT)

URL de différents formats de fichiers hébergés sur Stockage Blob Azure :

CSV : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.csv 

JSON : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.json

JSONL : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.jsonl 

Parquet : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet

Téléchargez le fichier de jeu de données à l’aide de la capacité intégrée permettant de télécharger à partir d’une URL HTTP dans Pandas. Pandas comprend des visionneuses pour différents formats de fichiers :

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


Commencez par charger le fichier de jeu de données dans un tableau pandas et consultez des exemples de lignes.

```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet")
df.head(10)
```

Vérifions les types de données des différents champs et vérifions que la colonne mise à jour est au format DateHeure.

```python
df.dtypes
```

Ce jeu de données contient des données pour de nombreux pays. Vérifions les pays pour lesquels nous avons des données.

Nous allons commencer par examiner les données les plus récentes pour chaque pays :

```python
df.groupby('countryname').first().filter(['confirmedcases ', 'confirmeddeaths','h5_investment_in_vaccines',
    'c6_stay_at_home_requirements','h4_emergency_investment_in_healthcare','c4_restrictions_on_gatherings', 'load_date'])
```

Ensuite, nous allons effectuer quelques agrégations pour nous assurer que les colonnes telles que `confirmedcases` et `confirmeddeaths` correspondent aux données les plus récentes. Vous devriez voir que les nombres de cas positifs et de décès pour la dernière date dans le tableau ci-dessus correspondent à l’agrégation de `confirmedcases` et `confirmeddeaths`.


```python
df.groupby('countryname').agg({'countryname': 'count','confirmedcases': 'sum','confirmeddeaths': 'sum',
                               'h5_investment_in_vaccines': 'count', 'c6_stay_at_home_requirements':'sum'})
```

Faisons quelques visualisations de base pour quelques pays.

```python
import plotly.graph_objects as go
import plotly.express as px
import matplotlib.pyplot as plt

df.loc[: , ['countryname', 'confirmedcases', 
'confirmeddeaths']].groupby(['countryname']).max().sort_values(by='confirmedcases', 
                                           ascending=False).reset_index()[:15].style.background_gradient(cmap='rainbow')
```

```python
df_US = df.groupby(df.date).agg({'confirmedcases': 'sum','confirmeddeaths':'sum'}).reset_index()

df_US.plot(kind='line',x='date',y="confirmedcases",grid=True)
df_US.plot(kind='line',x='date',y="confirmeddeaths",grid=True)

```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

Exemple non disponible pour cette combinaison de plateforme et de package.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=oxford-covid-19-government-response-tracker -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=oxford-covid-19-government-response-tracker)** .

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=oxford-covid-19-government-response-tracker -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=oxford-covid-19-government-response-tracker)** .

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres jeux de données du [catalogue Open Datasets](dataset-catalog.md).
