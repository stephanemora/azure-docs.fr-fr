---
title: Couverture du géocodage dans Azure Maps | Microsoft Docs
description: Découvrez la couverture de géocodage dans Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a5e5f4ab286289e223a2fe10ff8cf45f43309f04
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785936"
---
# <a name="azure-maps-geocoding-coverage"></a>Couverture de géocodage Azure Maps

Lorsque vous recherchez un emplacement avec Azure Maps, le service de recherche accepte les termes de recherche et retourne les coordonnées de latitude et longitude. Ce processus est appelé « géocodage ». Toutefois, Maps n’a pas le même niveau d’information et de précision pour tous les pays et régions. Utilisez cet article pour déterminer quels types d’emplacements vous pouvez rechercher de manière fiable dans chaque région. 

La possibilité de Géocode dans un pays/région dépend de la couverture de données de route et la précision du géocodage du service de géocodage. Les catégorisations suivantes servent spécifier le niveau de prise en charge du géocodage dans chaque pays/région.
* **Points d’adresse** : Les données d’adresses peuvent être résolues en coordonnées latitude/longitude dans la parcelle de l’adresse (limite de propriété). Ils sont parfois désignés comme ayant une précision « rooftop » (maximale). Il s’agit du niveau de précision le plus haut disponible pour les adresses. 
* **Numéros de résidence** : Les adresses sont interpolées en coordonnées latitude/longitude dans la rue.
* **Niveau de rue** : Les adresses sont résolues en coordonnées latitude/longitude de la rue qui contient l’adresse. Le numéro de résidence ne peut pas être traité.
* **Niveau de ville** : Les noms de ville sont pris en charge.

## <a name="americas"></a>Amérique

| Pays ou région                                       | Points d’adresse | Numéros de résidence | Niveau de rue | Niveau ville | Points d’intérêt |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antarctique                                          |                 |                |              |      ✓     |          ✓         |
| Antigua-et-Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentine                                           |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahamas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbade (La)                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermudes                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolivie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Saint-Eustache et Saba                   |                 |                |              |      ✓     |          ✓         |
| Brésil                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Canada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Caïmans (Îles)                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chili                                               |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Colombie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Costa Rica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Cuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominique                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominicana                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Équateur                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Le Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Falkland (îles Falkland ou Malouines)                                    |                 |                |              |      ✓     |          ✓         |
| Guyana                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenade                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadeloupe                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Guyane                                              |                |             |           |      ✓     |                 |
| Haïti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras (Le)                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamaïque                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinique                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mexique                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nicaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguay                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Pérou                                                |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Puerto Rico                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Saint-Barthélemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Saint-Kitts-et-Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Sainte-Lucie                                         |                 |                |              |      ✓     |          ✓         |
| Saint-Martin (Royaume des Pays-Bas)                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Saint-Pierre-et-Miquelon                           |                 |                |       ✓      |      ✓     |          ✓         |
| Saint-Vincent-et-les Grenadines                    |                 |                |              |      ✓     |          ✓         |
| Saint-Martin (partie néerlandaise)                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Géorgie du Sud et les Îles Sandwich du Sud        |                 |                |              |      ✓     |          ✓         |
| Suriname                                            |                 |                |              |      ✓     |          ✓         |
| Trinité-et-Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Îles mineures éloignées des États-Unis                |                 |                |              |      ✓     |          ✓         |
| États-Unis d'Amérique                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Vierges britanniques (îles)                              |                 |                |              |      ✓     |          ✓         |
| Données Îles vierges                                 |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Asie-Pacifique

| Pays ou région                                      | Points d’adresse |Numéros de résidence | Niveau de rue | Niveau ville | Points d’intérêt |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Samoa américaines                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Australie                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladesh                                          |                 |                |              |      ✓     |          ✓         |
| Bhoutan                                              |                 |                |              |      ✓     |          ✓         |
| Territoires britanniques de l'Océan Indien                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Cambodge                                            |                 |                |              |      ✓     |          ✓         |
| Chine                                               |                 |                |              |      ✓     |          ✓         |
| Christmas (Île)                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Cocos-Keeling (îles)                             |                 |                |              |      ✓     |          ✓         |
| Comores (Les)                                             |                 |                |              |      ✓     |          ✓         |
| Cook (Îles)                                        |                 |                |              |      ✓     |          ✓         |
| Fidji                                                |                  |                |              |      ✓     |          ✓        |
| Polynésie française                                    |                 |                |              |      ✓     |          ✓         |
| Heard et McDonald (îles)                   |                 |                |              |      ✓     |          ✓         |
| Hong Kong (R.A.S.)                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonésie                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Inde                                               |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Japon                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Corée du Sud                                         |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| Macao R.A.S.                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malaysie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Micronésie                                          |                 |                |              |      ✓     |          ✓         |
| Mongolie                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Népal                                               |                 |                |              |      ✓     |          ✓         |
| Nouvelle-Calédonie                                       |                 |                |              |      ✓     |          ✓         |
| Nouvelle-Zélande                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niué                                                |                 |                |              |      ✓     |          ✓         |
| Norfolk (île)                                      |                 |                |              |      ✓     |          ✓         |
| Corée du Nord                                         |                 |                |              |      ✓     |          ✓         |
| Mariannes du Nord (Îles)                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakistan                                            |                 |                |              |      ✓     |          ✓         |
| Palau                                               |                 |                |              |      ✓     |          ✓         |
| Papouasie-Nouvelle-Guinée                                    |                 |                |              |      ✓     |          ✓         |
| Îles Paracels                                     |                 |                |              |      ✓     |                    |
| Philippines                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Île Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Îles Senkaku/Diaoyutai                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapour                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Salomon (Îles)                                     |                 |                |              |      ✓     |          ✓         |
| Îles Kouriles                                     |        ✓        |                |              |      ✓     |          ✓         |
| Îles Spratleys                                     |                 |                |              |      ✓     |                    |
| Sri Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Taiwan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Thaïlande                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Turks et Caicos (Îles)                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietnam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis-et-Futuna                                   |                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Europe

| Pays ou région                                      | Points d’adresse |Numéros de résidence | Niveau de rue | Niveau ville | Points d’intérêt |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albanie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorre                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Arménie                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| L'Autriche                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbaïdjan                                          |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Belgique                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bosnie-Herzégovine                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bulgarie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Biélorussie                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Croatie                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Chypre                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| République tchèque                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Danemark                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estonie                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Féroé (Îles)                                       |                 |                |              |      ✓     |          ✓         |
| Finlande                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| France                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Géorgie                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Allemagne                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibraltar                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grèce                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Groenland                                           |                 |                |              |      ✓     |          ✓         |
| Guernesey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Hongrie                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Islande                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Irlande                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Île de Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Italie                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazakhstan                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kosovo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kirghizistan                                          |                 |                |              |      ✓     |          ✓         |
| Lettonie                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lituanie                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Luxembourg                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Macédoine du Nord                                     |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malte                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldavie                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Monaco                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Monténégro                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Pays-Bas                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Norvège                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pologne                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugal                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| +Açores et Madère                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Roumanie                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Fédération de Russie                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Saint-Marin                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Serbie                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovaquie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovénie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Espagne                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Suède                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Suisse                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tdjikistan                                          |                 |                |              |      ✓     |          ✓         |
| Turquie                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turkménistan                                        |                 |                |              |      ✓     |          ✓         |
| Ukraine                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Royaume-Uni                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ouzbékistan                                          |                 |                |              |      ✓     |          ✓         |
| Vatican (État de la Cité du)                                        |                 |                |       ✓      |      ✓     |          ✓         |


## <a name="middle-east-and-africa"></a>Moyen-Orient et Afrique

| Pays ou région                                      | Points d’adresse |Numéros de résidence | Niveau de rue | Niveau ville | Points d’intérêt |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afghanistan                                         |                 |                |              |      ✓     |          ✓         |
| Algérie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahreïn                                             |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Bénin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botswana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Bouvet (Île)                                       |                 |                |              |      ✓     |          ✓         |
| Burkina-Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Cameroun                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde                                          |                 |                |       ✓      |      ✓     |          ✓         |
| République centrafricaine                            |                 |                |       ✓      |      ✓     |          ✓         |
| Tchad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Congo                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d'Ivoire                                       |                 |                |       ✓      |      ✓     |          ✓         |
| République démocratique du Congo                    |                 |                |       ✓      |      ✓     |          ✓         |
| Djibouti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Égypte                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guinée équatoriale, République de                      |                 |                |       ✓      |      ✓     |          ✓         |
| Érythrée                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Éthiopie                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Terres australes et antarctiques françaises|                        |                |              |      ✓     |          ✓         |
| Gabon                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambie                                              |                 |                |              |      ✓     |          ✓         |
| Ghana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guinée                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Guinée-Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Iran                                                |                 |                |              |      ✓     |          ✓         |
| Irak                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Israël                                              |        ✓        |       ✓        |              |      ✓     |          ✓         |
| Jordanie                                              |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Kenya                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Koweït                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Liban                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Liberia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Lybie                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Madagascar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maldives                                            |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Marshall (Îles)                                    |                 |                |              |      ✓     |          ✓         |
| Mauritanie                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Maurice (Île)                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Maroc                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mozambique                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar (Union de)                                             |                 |                |              |      ✓     |          ✓         |
| Namibie                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigéria                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Oman                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Qatar                                               |        ✓        |                |       ✓      |      ✓     |          ✓         |
| La Réunion                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Rwanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Sainte-Hélène                                        |                 |                |              |      ✓     |          ✓         |
| Arabie saoudite                                        |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Sénégal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seychelles                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somalie                                             |                 |                |              |      ✓     |          ✓         |
| Afrique du Sud                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Soudan du Sud                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Soudan                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Swaziland                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Syrie                                               |                 |                |              |      ✓     |          ✓         |
| São Tomé e Príncipe                               |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzanie                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunisie                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Ouganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Émirats Arabes Unis                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Yémen                                               |                 |                |              |      ✓     |          ✓         |
| Zambie                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |



## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le géocodage Azure Maps, consultez les pages de référence sur la [recherche](https://docs.microsoft.com/rest/api/maps/search).

Découvrez les [zones de couverture pour les services de trafic Maps](traffic-coverage.md). 

