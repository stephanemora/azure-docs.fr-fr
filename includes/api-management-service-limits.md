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
ms.openlocfilehash: e01eebe41010135d0dc0a2cb4170e6b6687ff546
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292681"
---
| Ressource | Limite |
| --- | --- |
| Nombre maximal d’unités d’échelle | 10 par région<sup>1</sup> |
| Taille du cache | 5 Go par unité<sup>2</sup> |
| Connexions back-ends simultanées<sup>3</sup> par autorité HTTP | 2 048 par unité<sup>4</sup> |
| Taille maximale de la réponse mise en cache | 2 Mo |
| Taille maximale du document de stratégie | 256 Ko<sup>5</sup> | 
| Nombre maximal de domaines de passerelle personnalisés par instance de service<sup>6</sup> | 20 | 
| Nombre maximal d’instances de service par abonnement<sup>7</sup> | 5. | 
| Nombre maximal d’abonnements par instance de service<sup>7</sup> | 500 |
| Nombre maximal de certificats clients par instance de service<sup>7</sup> | 50 | 
| Nombre maximal d’API par instance de service<sup>7</sup> | 50 | 
| Nombre maximal d’opérations d’API par instance de service<sup>7</sup> | 1 000 | 
| Durée totale maximale des demandes<sup>7</sup> | 30 secondes | 
| Taille maximale de la charge utile mise en mémoire tampon<sup>7</sup> | 2 Mo | 


<sup>1</sup> Les limites d’échelle varient selon le niveau tarifaire. Pour consulter les niveaux tarifaires et leurs limites d’échelle, consultez [Tarification de la gestion des API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> La taille du cache par unité varie selon le niveau tarifaire. Pour consulter les niveaux tarifaires et leurs limites d’échelle, consultez [Tarification de la gestion des API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> Les connexions sont regroupées et réutilisées, sauf si elles sont explicitement fermées par le back-end.<br/>
<sup>4</sup> Par unité des niveaux De base, Standard et Premium. Le niveau Développeur est limité à 1 024. Ne s’applique pas au niveau Consommation.<br/> 
<sup>5</sup> Dans les niveaux De base, Standard et Premium. Dans le niveau Consommation, la taille de document des stratégies est limitée à 4 Ko.<br/>
<sup>6</sup> Disponible dans le niveau Premium uniquement.<br/>
<sup>7</sup> S’applique au niveau Consommation uniquement.<br/>



