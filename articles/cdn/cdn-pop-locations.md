---
title: Emplacements POP du CDN Azure par région | Microsoft Docs
description: Cet article répertorie les emplacements POP CDN Azure, triés par région, pour les produits du CDN Azure.
services: cdn
documentationcenter: ''
author: sohamnchatterjee
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2021
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: e58ce2b4fa016a79050425dad4e4dd0b784f0c9c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110472356"
---
# <a name="azure-cdn-coverage-by-metro"></a>Couverture Azure CDN par Metro 
> [!div class="op_single_selector"]
> * [Emplacements POP par région](cdn-pop-locations.md)
> * [Emplacements Verizon POP par abréviation](cdn-pop-abbreviations.md)
> * [Emplacements Microsoft POP par abréviation](microsoft-pop-abbreviations.md)
>


Cet article répertorie les éléments métros actuels contenant des emplacements de points de présence (POP), triés par région, pour les produits du réseau de distribution de contenu (CDN) Azure. Chaque élément métro peut contenir plusieurs emplacements POP. Par exemple, l’élément Azure CDN de Microsoft comporte 116 emplacements POP sur 100 zones urbaines. 

> [!IMPORTANT]
> Chaque produit du CDN Azure possède une méthode distincte de génération de son infrastructure de CDN, Microsoft déconseille donc l’utilisation des emplacements POP pour déterminer quel produit CDN Azure utiliser. Considérez plutôt les fonctionnalités du produit et les performances de l’utilisateur final. Testez les performances avec chaque produit du CDN Azure pour choisir celui qui vous semble le mieux adapté à vos utilisateurs. 
> 

## <a name="microsoft"></a>Microsoft

[!INCLUDE [front-door-edge-location](../../includes/front-door-edge-locations.md)]

## <a name="partners"></a>Partenaires

> [!IMPORTANT]
> Les emplacements des villes POP pour **CDN Azure fournis par Akamai** ne sont pas divulgués individuellement.  
> 

| Région | Verizon | Akamai |
|--|--|--|
| Amérique du Nord | Guadalajara, Mexique<br />Mexico, Mexique<br />Puebla, Mexico<br />Querétaro, Mexico<br />Atlanta, GA, États-Unis<br />Boston, MA, États-Unis<br />Chicago, IL, États-Unis<br />Dallas, TX, États-Unis<br />Denver, CO, États-Unis<br />Detroit, MI, États-Unis<br />Los Angeles, CA, États-Unis<br />Miami, FL, États-Unis<br />New York, NY, États-Unis<br />Philadelphie, PA, États-Unis<br />San Jose, CA, États-Unis<br />Seattle, WA, États-Unis<br />Washington, DC, États-Unis <br /> Ashburn, VA, États-Unis <br /> Phoenix, AZ, États-Unis | Canada<br />Mexique<br />États-Unis |
| Amérique du Sud | Buenos Aires, Argentine<br />Rio de Janeiro, Brésil<br />São Paulo, Brésil<br />Valparaíso, Chili<br />Bogota, Colombie<br />Barranquilla, Colombie<br />Medellin, Colombie<br />Quito, Équateur<br />Lima, Pérou | Argentine<br />Brésil<br />Chili<br />Colombie<br />Équateur<br />Pérou<br />Uruguay |
| Europe | Vienne, Autriche<br />Copenhague, Danemark<br />Helsinki, Finlande<br />Marseille, France<br />Paris, France<br />Frankfort, Allemagne<br />Milan, Italie<br />Riga, Lettonie<br />Amsterdam, Pays-Bas<br />Varsovie, Pologne<br />Madrid, Espagne<br />Stockholm, Suède<br />Londres, Royaume-Uni <br /> Manchester, Royaume-Uni | Autriche<br />Bulgarie<br />Danemark<br />Finlande<br />France<br />Allemagne<br />Grèce<br />Irlande<br />Italie<br />Pays-Bas<br />Pologne<br />Russie<br />Espagne<br />Suède<br />Suisse<br />Royaume-Uni |
| Afrique | Johannesbourg, Afrique du Sud <br/> Nairobi, Kenya | Afrique du Sud |
| Moyen-Orient | Muscat, Oman<br />Fujirah, Émirats Arabes Unis | Qatar<br />Émirats Arabes Unis |
| Inde | Bengaluru (Bangalore), Inde<br />Chennai, Inde<br />Mumbai, Inde<br />New Delhi, Inde<br /> | Inde |
| Asia | Hong Kong (R.A.S.)<br />Jakarta, Indonésie<br />Osaka, Japon<br />Tokyo, Japon<br />Singapour<br />Séoul, Corée du Sud<br />Kaohsiung, Taïwan<br />Taipei, Taiwan <br />Manille, Philippines | Hong Kong (R.A.S.)<br />Indonésie<br />Israël<br />Japon<br />Macao (R.A.S.)<br />Malaisie<br />Philippines<br />Singapour<br />Corée du Sud<br />Taïwan<br />Thaïlande<br />Turquie<br />Vietnam |
| Australie et Nouvelle Zélande | Melbourne, Australie<br />Sydney, Australie<br />Auckland, Nouvelle-Zélande | Australie<br />Nouvelle-Zélande |

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les dernières adresses IP à ajouter à la liste d’autorisation, consultez [API de nœuds de périphérie Azure CDN](/rest/api/cdn/cdn/edgenodes).
