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
ms.openlocfilehash: fc945a7e9389c8aec48a6a1dba969fbf92002d3a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553542"
---
| Ressource | Limite |
| --- | --- |
| Nombre maximal d’unités d’échelle | 10 par région<sup>1</sup> |
| Taille du cache | 5 Go par unité<sup>2</sup> |
| Connexions simultanées de back-end<sup>3</sup> par autorité HTTP | 2 048 par unité<sup>4</sup> |
| Taille maximale de la réponse mise en cache | 2 Mo |
| Taille maximale du document de stratégie | 256 KO<sup>5</sup> | 
| Nombre maximal de domaines de passerelle personnalisés par instance de service<sup>6</sup> | 20 |
| Nombre maximal de certificats d’autorité de certification par instance de service | 10 | 
| Nombre maximal d’instances de service par abonnement<sup>7</sup> | 20 | 
| Nombre maximal d’abonnements par instance de service<sup>7</sup> | 500 |
| Nombre maximal de certificats clients par instance de service<sup>7</sup> | 50 | 
| Nombre maximal d’API par instance de service<sup>7</sup> | 50 | 
| Nombre maximal d’opérations d’API par instance de service<sup>7</sup> | 1 000 | 
| Durée totale maximale de la demande<sup>7</sup> | 30 secondes | 
| Taille maximale de la charge utile mise en mémoire tampon<sup>7</sup> | 2 Mo | 


<sup>1</sup>les limites de mise à l’échelle varient selon le niveau de tarification. Pour voir les niveaux de tarification et leurs limites de mise à l’échelle, consultez [tarification gestion des API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup>cache d’unité par taille varie selon le niveau de tarification. Pour voir les niveaux de tarification et leurs limites de mise à l’échelle, consultez [tarification gestion des API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup>connexions sont regroupées et réutilisées, sauf si explicitement fermée par le serveur principal.<br/>
<sup>4</sup>cette limite est par unité des niveaux de base, Standard et Premium. Le niveau développeur est limité à 1 024. Cette limite ne s’applique pas au niveau de la consommation.<br/> 
<sup>5</sup>cette limite s’applique aux niveaux de base, Standard et Premium. Dans le niveau de consommation, la taille de document de stratégie est limitée à 4 Ko.<br/>
<sup>6</sup>cette ressource est disponible dans le niveau Premium uniquement.<br/>
<sup>7</sup>cette ressource s’applique uniquement au niveau de la consommation.<br/>



