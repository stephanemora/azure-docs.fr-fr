---
title: Fichier Include
description: Fichier Include
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: b40e2ba448236bcef72ffe501b4b14fa0dd64e1c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
ms.locfileid: "31434937"
---
| Ressource | Limite |
| --- | --- |
| Unités d'échelle | 10 par région<sup>1</sup> |
| Cache | 5 Go par unité<sup>1</sup> |
| Les connexions simultanées principales<sup>2</sup> par autorité HTTP | 2 048 par unité<sup>3</sup> |
| Taille maximale de la réponse mise en cache | 10 Mo |
| Taille maximale du document de stratégie | 256 Ko |
| Maximum de domaines de passerelle personnalisés | 20 par instance de service<sup>4</sup> |


<sup>1</sup>Les limites d'API Management sont différentes pour chaque niveau de tarification. Pour consulter les niveaux de tarification et leurs limites de mise à l'échelle, consultez [Tarification de la gestion des API](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> Les connexions sont regroupées et réutilisées, sauf si elles sont explicitement fermées par le serveur principal.
<sup>3</sup> Par unité des niveaux De base, Standard et Premium. Le niveau développeur est limité à 1 024.
<sup>4</sup> Disponible pour le niveau Premium uniquement.


