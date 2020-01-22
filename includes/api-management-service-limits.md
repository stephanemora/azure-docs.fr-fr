---
ms.openlocfilehash: ee44cd4ee5ec6fedfec46cbbb68b0c30118a4b9f
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904475"
---
---

title: include file description: include file services: api-management author: vladvino

ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04 ms.service: api-management ms.topic: include ms.date: 01/10/2020 ms.author: vlvinogr ms.custom: include file
---| Ressource | Limite |
| ---------------------------------------------------------------------- | -------------------------- |
| Nombre maximal d’unités d’échelle | 10 par région<sup>1</sup> |
| Taille du cache | 5 Gio par unité<sup>2</sup> |
| Connexions back-ends simultanées<sup>3</sup> par autorité HTTP | 2 048 par unité<sup>4</sup> |
| Taille maximale de la réponse mise en cache | 2 Mio |
| Taille maximale du document de stratégie | 256 Kio<sup>5</sup> |
| Nombre maximal de domaines de passerelle personnalisés par instance de service<sup>6</sup> | 20 |
| Nombre maximal de certificats de site par instance de service | 10 |
| Nombre maximal d’instances de service par abonnement<sup>7</sup> | 20 |
| Nombre maximal d’abonnements par instance de service<sup>7</sup> | 500 |
| Nombre maximal de certificats clients par instance de service<sup>7</sup> | 50 |
| Nombre maximal d’API par instance de service<sup>7</sup> | 50 |
| Nombre maximal d’opérations d’API par instance de service<sup>7</sup> | 1 000 |
| Durée totale maximale des requêtes<sup>7</sup> | 30 secondes |
| Taille maximale de la charge utile mise en mémoire tampon<sup>7</sup> | 2 Mio |
| Taille maximale de l’URL de requête<sup>8</sup> | 4096 octets |

<sup>1</sup>Les limites de mise à l’échelle varient selon le niveau tarifaire. Pour consulter les niveaux tarifaires et leurs limites de mise à l’échelle, voir [Tarification de la gestion des API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup>La taille du cache par unité varie selon le niveau tarifaire. Pour consulter les niveaux tarifaires et leurs limites de mise à l’échelle, voir [Tarification de la gestion des API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup>Les connexions sont regroupées et réutilisées, sauf si elles sont explicitement fermées par le back-end.<br/>
<sup>4</sup>Cette limite est appliquée par unité des niveaux De base, Standard et Premium. Le niveau Développeur est limité à 1 024. Cette limite ne s’applique pas au niveau Consommation.<br/>
<sup>5</sup>Cette limite est appliquée aux niveaux De base, Standard et Premium. Dans le niveau Consommation, la taille des documents de stratégies est limitée à 4 Kio.<br/>
<sup>6</sup>Cette ressource est disponible dans le niveau Premium uniquement.<br/>
<sup>7</sup>Cette ressource s’applique au niveau Consommation uniquement.<br/>
<sup>8</sup>S’applique au niveau Consommation uniquement. Comprend une chaîne de requête longue allant jusqu’à 2048 octets.<br/>
