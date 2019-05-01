---
title: Tarification des offres de machine virtuelle | Place de marché Azure
description: Décrit les trois méthodes disponibles la tarification des offres de machine virtuelle.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: dde3e04dc8f30ea5657139d50dd4456e5dfb57c2
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935721"
---
<a name="pricing-for-virtual-machine-offers"></a>Tarification des offres de machine virtuelle
==================================

Trois méthodes sont disponibles la tarification des offres de machine virtuelle : la tarification de cœur personnalisée, la tarification par cœur et la tarification par feuille de calcul.


<a name="customized-core-pricing"></a>Tarification de cœur personnalisée
-----------------------

La tarification est spécifique à chaque combinaison de région et de cœur. Chacune des régions de la liste doit être spécifiée dans la section **virtualMachinePricing**/**regionPrices** de la définition.  Dans votre requête, utilisez les codes devise correspondant à chaque [région](#regions).  L’exemple suivant illustre ces exigences :

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 2,
                    "1core": 2,
                    "2core": 3,
                    "4core": 4,
                    "6core": 5,
                    "8core": 2,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 4,
                    "36core": 4,
                    "40core": 4,
                    "64core": 4,
                    "128core": 4
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Tarification par cœur
----------------

Dans ce cas, les éditeurs spécifient un prix en dollars américains pour leur référence SKU, et tous les autres prix sont générés automatiquement. Le prix par cœur est spécifié dans le paramètre **single** de la requête.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>Tarification par feuille de calcul
-------------------

L'éditeur peut également charger sa feuille de calcul de tarification sur un emplacement de stockage temporaire, puis inclure l’URI dans la requête comme pour d'autres artefacts de fichier. La feuille de calcul est ensuite chargée et traduite pour évaluer la grille de prix spécifiée. Puis l'offre est mise à jour avec les informations de tarification. Les requêtes GET suivantes relatives à l’offre renverront l’URI de la feuille de calcul et les évaluations de prix correspondant à la région.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="regions"></a>Régions
-------

Le tableau suivant présente les différentes régions que vous pouvez spécifier pour la tarification de cœur personnalisée, ainsi que les codes devise correspondants.

| **Région** | **Name**             | **Code devise** |
|------------|----------------------|-------------------|
| DZ         | Algérie              | DZD               |
| AR         | Argentine            | ARS               |
| AU         | Australie            | AUD               |
| AT         | Autriche              | EUR               |
| BH         | Bahreïn              | BHD               |
| BY         | Bélarus              | RUB               |
| BE         | Belgique              | EUR               |
| BR         | Brésil               | USD               |
| BG         | Bulgarie             | BGN               |
| CA         | Canada               | CAD               |
| CL         | Chili                | CLP               |
| CO         | Colombie             | COP               |
| CR         | Costa Rica           | CRC               |
| HR         | Croatie              | HRK               |
| CY         | Chypre               | EUR               |
| CZ         | République tchèque       | CZK               |
| DK         | Danemark              | DKK               |
| DO         | République dominicaine   | USD               |
| EC         | Équateur              | USD               |
| EG         | Égypte                | EGP               |
| SV         | El Salvador          | USD               |
| EE         | Estonie              | EUR               |
| FI         | Finlande              | EUR               |
| FR         | France               | EUR               |
| DE         | Allemagne              | EUR               |
| GR         | Grèce               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | Hong Kong (R.A.S.)        | HKD               |
| HU         | Hongrie              | HUF               |
| IS         | Islande              | ISK               |
| IN         | Inde                | INR               |
| ID         | Indonésie            | IDR               |
| IE         | Irlande              | EUR               |
| IL         | Israël               | ILS               |
| IT         | Italie                | EUR               |
| JP         | Japon                | JPY               |
| JO         | Jordanie               | JOD               |
| KZ         | Kazakhstan           | KZT               |
| KE         | Kenya                | KES               |
| KR         | Corée du Sud                | KRW               |
| KW         | Koweït               | KWD               |
| LV         | Lettonie               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Lituanie            | EUR               |
| LU         | Luxembourg           | EUR               |
| MK         | Macédoine du Nord      | MKD               |
| MY         | Malaisie             | MYR               |
| MT         | Malte                | EUR               |
| MX         | Mexique               | MXN               |
| ME         | Monténégro           | EUR               |
| MA         | Maroc              | MAD               |
| NL         | Pays-bas          | EUR               |
| NZ         | Nouvelle-Zélande          | NZD               |
| NG         | Nigeria              | NGN               |
| NON         | Norvège               | NOK               |
| OM         | Oman                 | OMR               |
| PK         | Pakistan             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | PYG               |
| PE         | Pérou                 | PEN               |
| PH         | Philippines          | PHP               |
| PL         | Pologne               | PLN               |
| PT         | Portugal             | EUR               |
| PR         | Porto Rico          | USD               |
| QA         | Qatar                | QAR               |
| RO         | Roumanie              | RON               |
| RU         | Russie               | RUB               |
| SA         | Arabie Saoudite         | SAR               |
| RS         | Serbie               | RSD               |
| SG         | Singapour            | SGD               |
| SK         | Slovaquie             | EUR               |
| SI         | Slovénie             | EUR               |
| ZA         | Afrique du Sud         | ZAR               |
| ES         | Espagne                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | Suède               | SEK               |
| CH         | Suisse          | CHF               |
| TW         | Taïwan               | TWD               |
| MJ         | Thaïlande             | THB               |
| TT         | Trinité-et-Tobago  | TTD               |
| TN         | Tunisie              | TND               |
| TR         | Turquie               | TRY               |
| UA         | Ukraine              | UAH               |
| AE         | Émirats Arabes Unis | EUR               |
| GB         | Royaume-Uni       | GBP               |
| FR         | États-Unis        | USD               |
| UY         | Uruguay              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
