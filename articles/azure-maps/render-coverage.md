---
title: Couverture du rendu dans Azure Maps | Microsoft Docs
description: Découvrez la couverture du rendu dans Azure Maps
author: jingjing-z
ms.author: jinzh
ms.date: 03/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 6e4ebd5bfd7225537046d34dd885d04e8a94878f
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2018
ms.locfileid: "42146350"
---
# <a name="azure-maps-render-coverage"></a>Couverture du rendu Azure Maps

Azure Maps utilise des vignettes Raster et Vecteurs pour créer des cartes. Dans la résolution la plus faible, le monde entier tient sur une seule vignette. Dans la résolution la plus élevée, une seule vignette représente 38 mètres carrés. Lorsque vous effectuez un zoom avant sur une carte, vous pouvez voir de plus en plus de détails sur les continents, les régions, les villes et les rues. Pour plus d’informations, consultez [Niveaux de zoom et grille mosaïque](zoom-levels-and-tile-grid.md).

Toutefois, Maps n’a pas le même niveau d’information et de précision pour toutes les régions. Les tableaux suivants fournissent des informations sur le niveau de détail rendu que vous pouvez attendre pour chaque région.

## <a name="legend"></a>Légende

| Symbole | Signification |
|--------|---------|
| ✓ | La région est représentée par des données détaillées.   |
| Ø | La région est représentée par des données simplifiées. |


## <a name="africa"></a>Afrique 


| Région | Vignettes Raster unifiées | Vignettes Vecteur unifiées |
| ------ | :------------------: | :------------------: |
| Algérie                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Bénin                            | ✓ | ✓ |
| Botswana                         | ✓ | ✓ |
| Burkina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Cabo Verde                       |   | ✓ |
| Cameroun                         | ✓ | ✓ |
| République centrafricaine         |   | Ø |
| Tchad                             |   | Ø |
| Comores                          |   | Ø |
| Congo                            | ✓ | ✓ |
| République démocratique du Congo | ✓ | ✓ |
| Côte d’Ivoire                    |   | Ø |
| Djibouti                         |   | Ø |
| Égypte                            | ✓ | ✓ |
| Guinée équatoriale                |   | Ø |
| Érythrée                          |   | Ø |
| Éthiopie                         |   | Ø |
| Gabon                            | ✓ | ✓ |
| Gambie                           |   | Ø |
| Ghana                            | ✓ | ✓ |
| Guinée                           |   | Ø |
| Guinée-Bissau                    |   | Ø |
| Kenya                            | ✓ | ✓ |
| Lesotho                          | ✓ | ✓ |
| Liberia                          |   | Ø |
| Libye                            |   | Ø |
| Madagascar                       |   | Ø |
| Malawi                           | ✓ | ✓ |
| Mali                             | ✓ | ✓ |
| Mauritanie                       | ✓ | ✓ |
| Maurice (île)                        | ✓ | ✓ |
| Mayotte                          | ✓ | ✓ |
| Maroc                          | ✓ | ✓ |
| Mozambique                       | ✓ | ✓ |
| Namibie                          | ✓ | ✓ |
| Niger                            | ✓ | ✓ |
| Nigeria                          | ✓ | ✓ |
| La Réunion                          | ✓ | ✓ |
| Rwanda                           | ✓ | ✓ |
| Sainte-Hélène, Ascension et Tristan da Cunha |   | Ø |
| Sao Tome et Principe            |   | Ø |
| Sénégal                          | ✓ | ✓ |
| Sierra Leone                     |   | Ø |
| Somalie                          |   | Ø |
| Afrique du Sud                     | ✓ | ✓ |
| Soudan du Sud                      |   | Ø |
| Soudan                            |   | Ø |
| Swaziland                        | ✓ | ✓ |
| République unie de Tanzanie      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Tunisie                          | ✓ | ✓ |
| Ouganda                           | ✓ | ✓ |
| Zambie                           | ✓ | ✓ |
| Zimbabwe                         | ✓ | ✓ |

## <a name="americas"></a>Amérique

| Région | Vignettes Raster unifiées | Vignettes Vecteur unifiées |
| ------ | :------------------: | :------------------: |
| Anguilla                  | ✓ | ✓ |
| Antigua-et-Barbuda       | ✓ | ✓ |
| Argentine                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Les Bahamas                   | ✓ | ✓ |
| Barbade                  | ✓ | ✓ |
| Belize                    | ✓ | ✓ |
| Bermudes                   |   | ✓ |
| État plurinational de Bolivie |   | ✓ |
| Bonaire, Saint-Eustache et Saba |   | ✓ |
| Brésil                    | ✓ | ✓ |
| Canada                    | ✓ | ✓ |
| Caïmans (îles)            | ✓ | ✓ |
| Chili                     | ✓ | ✓ |
| Îles Clipperton         |   | ✓ |
| Colombie                  | ✓ | ✓ |
| Costa Rica                |   | ✓ |
| Cuba                      | ✓ | ✓ |
| Curaçao                   | ✓ | ✓ |
| Dominique                  | ✓ | ✓ |
| République dominicaine        | ✓ | ✓ |
| Équateur                   |   | ✓ |
| Îles Falkland (Malouines) |   | ✓ |
| Guyane française             | ✓ | ✓ |
| Groenland                 |   | Ø |
| Grenade                   | ✓ | ✓ |
| Guadeloupe                | ✓ | ✓ |
| Guatemala                 |   | ✓ |
| Guyane                    | ✓ | ✓ |
| Haïti                     | ✓ | ✓ |
| Honduras                  | ✓ | ✓ |
| Jamaïque                   | ✓ | ✓ |
| Martinique                | ✓ | ✓ |
| Mexique                    | ✓ | ✓ |
| Montserrat                | ✓ | ✓ |
| Nicaragua                 | ✓ | ✓ |
| Îles Marianne du Nord  |   | ✓ |
| Panama                    | ✓ | ✓ | 
| Paraguay                  |   | ✓ |
| Pérou                      | ✓ | ✓ |
| Porto Rico               | ✓ | ✓ |
| Québec (Canada)           |   | ✓ |
| Saint-Barthélemy          | ✓ | ✓ |
| Saint-Christophe-et-Niévès     | ✓ | ✓ |
| Sainte-Lucie               | ✓ | ✓ |
| Saint-Martin (partie française)     | ✓ | ✓ |
| Saint Pierre et Miquelon |   | ✓ |
| Saint-Vincent-et-les-Grenadines | ✓ | ✓ |
| Saint-Martin (partie néerlandaise)      | ✓ | ✓ |
| Géorgie du Sud et îles Sandwich du Sud |   | ✓ |
| Surinam                  |   | ✓ |
| Trinité-et-Tobago       | ✓ | ✓ |
| Turques-et-Caïques (îles)  | ✓ | ✓ |
| États-Unis             | ✓ | ✓ |
| Uruguay                   | ✓ | ✓ |
| Venezuela                 | ✓ | ✓ |
| Vierges britanniques (îles)   | ✓ | ✓ |
| Vierges américaines (îles)      | ✓ | ✓ |

## <a name="asia"></a>Asie 

| Région | Vignettes Raster unifiées | Vignettes Vecteur unifiées |
| ------ | :------------------: | :------------------: |
| Afghanistan               |   | Ø |
| Bahreïn                   | ✓ | ✓ |
| Bangladesh                |   | Ø |
| Bhoutan                    |   | Ø |
| Territoire britannique de l'océan Indien |   | Ø |
| Brunei                    | ✓ | ✓ |
| Cambodge                  |   | Ø |
| Chine                     |   | Ø |
| Îles Cocos (Keeling)   |   | Ø |
| République populaire démocratique de Corée |   | Ø |
| Dokdo et Takeshima       |   | Ø |
| Hong Kong (R.A.S.)                 | ✓ | ✓ |
| Indonésie                 | ✓ | ✓ |
| Iran                      |   | Ø |
| Irak                      | ✓ | ✓ |
| Israël                    |   | ✓ |
| Japon                     |   | Ø |
| Jordanie                    | ✓ | ✓ |
| Kazakhstan                |   | ✓ |
| Koweït                    | ✓ | ✓ |
| Kirghizistan                |   | Ø |
| Laos, République démocratique |   | Ø |
| Liban                   | ✓ | ✓ |
| Macao (R.A.S.)                     | ✓ | ✓ |
| Malaisie                  | ✓ | ✓ |
| Maldives                  |   | Ø |
| Mongolie                  |   | Ø |
| Myanmar                   |   | Ø |
| Népal                     |   | Ø |
| Oman                      | ✓ | ✓ |
| Pakistan                  |   | Ø |
| Philippines               | ✓ | ✓ |
| Qatar                     | ✓ | ✓ |
| République de Corée         | ✓ | Ø |
| Arabie Saoudite              | ✓ | ✓ |
| Îles Senkaku/Diaoyutai           |   | ✓ |
| Singapour                 | ✓ | ✓|
| Sri Lanka                 |   | Ø |
| République arabe syrienne      |   | Ø |
| Taïwan                    | ✓ | ✓ |
| Tadjikistan                |   | Ø |
| Thaïlande                  | ✓ | ✓ |
| Timor-Leste               |   | Ø |
| Turkménistan              |   | Ø |
| Émirats Arabes Unis      | ✓ | ✓ |
| Îles mineures éloignées des États-Unis |   | Ø |
| Ouzbékistan                |   | Ø |
| Vietnam                   | ✓ | ✓ |
| Yémen                     | ✓ | ✓ |

## <a name="oceania"></a>Océanie

| Région | Vignettes Raster unifiées | Vignettes Vecteur unifiées |
| ------ | :------------------: | :------------------: |
| Samoa américaines            |   | ✓ |
| Australie                 | ✓ | ✓ |
| Cook (îles)              |   | Ø |
| Fidji                      |   | Ø |
| Polynésie française          |   | Ø |
| Guam                      | ✓ | ✓ |
| Kiribati                  |   | Ø |
| Marshall (îles)          |   | Ø |
| Micronésie                |   | Ø |
| Nauru                     |   | Ø |
| Nouvelle-Calédonie             |   | Ø |
| Nouvelle-Zélande               | ✓ | ✓ |
| Niue                      |   | Ø |
| Norfolk (île)            |   | Ø |
| Palau                     |   | Ø |
| Papouasie Nouvelle Guinée          |   | Ø |
| Île Pitcairn                  |   | Ø |
| Samoa                     |   | Ø |
| Salomon (îles)           |   | Ø|
| Tokelau                   |   | Ø |
| Tonga                     |   | Ø |
| Tuvalu                    |   | Ø |
| Vanuatu                   |   | Ø |
| Wallis et Futuna         |   | Ø |


## <a name="europe"></a>Europe

| Région | Vignettes Raster unifiées | Vignettes Vecteur unifiées |
| ------ | :------------------: | :------------------: |
| Albanie                   | ✓ | ✓ |
| Andorre                   | ✓ | ✓ |
| Arménie                   |   | Ø |
| Autriche                   | ✓ | ✓ |
| Azerbaïdjan                |   | Ø |
| Bélarus                   | Ø | ✓ |
| Belgique                   | ✓ | ✓ |
| Bosnia-Herzégovine        | ✓ | ✓ |
| Bulgarie                  | ✓ | ✓ |
| Croatie                   | ✓ | ✓ |
| Chypre                    | ✓ | ✓ |
| République tchèque            | ✓ | ✓ |
| Danemark                   | ✓ | ✓ |
| Estonie                   | ✓ | ✓ |
| Féroé (îles)             |   | Ø |
| Finlande                   | ✓ | ✓ |
| France                    | ✓ | ✓ |
| Géorgie                   |   | Ø |
| Allemagne                   | ✓ |✓ |
| Gibraltar                 | ✓ |   |
| Grèce                    | ✓ | ✓ |
| Guernesey                  |   | ✓ |
| Hongrie                   | ✓ | ✓ |
| Islande                   | ✓ | ✓ |
| Irlande     | ✓ | ✓ |
| Île de Man               |   | ✓ |
| Italie                     | ✓ | ✓ |
| Jan Mayen                 |   | ✓ |
| Jersey                    |   | ✓ |
| Lettonie                    | ✓ | ✓ |
| Liechtenstein             | ✓ | ✓ |
| Lituanie                 | ✓ | ✓ |
| Luxembourg                | ✓ | ✓ |
| République de Macédoine                 | ✓ | ✓ |
| Malte                     | ✓ | ✓ |
| Moldova                   | ✓ | ✓ |
| Monaco                    | ✓ | ✓ |
| Monténégro                | ✓ | ✓ |
| Pays-bas               | ✓ | ✓ |
| Norvège                    | ✓ | ✓ |
| Pologne                    | ✓ | ✓ |
| Portugal                  | ✓ | ✓ |
| Roumanie                   | ✓ | ✓ |
| Fédération de Russie        | ✓ | ✓ |
| Saint-Marin                | ✓ | ✓ |
| Serbie                    | ✓ | ✓ |
| Slovaquie                  | ✓ | ✓ |
| Slovénie                  | ✓ | ✓ |
| Îles Kouriles           |   | ✓ |
| Espagne                     | ✓ | ✓ |
| Svalbard                  |   | ✓ |
| Suède                    | ✓ |   |
| Suisse               | ✓ | ✓ |
| Turquie                    | ✓ | ✓ |
| Ukraine                   | ✓ | ✓ |
| Royaume-Uni            | ✓ | ✓ |
| Cité du Vatican              | ✓ | ✓ |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le rendu Azure Maps, consultez [Niveaux de zoom et grille mosaïque](zoom-levels-and-tile-grid.md).

Découvrez les [zones de couverture pour le service de routage Maps](routing-coverage.md). 