---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8908ded31b96aac50db1fc25e92c2c0a8e960453
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219030"
---
Le stockage localement redondant (LRS) réplique vos données trois fois au sein d’un même centre de données. Le stockage localement redondant offre une durabilité des objets d’au moins 99,999999999 % (11 « neuf ») sur une année donnée. LRS est l’option de réplication la moins coûteuse et offre la durabilité la plus faible en comparaison des autres options.

Si un sinistre se produit au niveau du centre de données (par exemple, un incendie ou une inondation), tous les réplicas d’un même compte de stockage qui utilise le LRS risquent d’être perdus ou irrécupérables. Pour atténuer ce risque, Microsoft recommande d’utiliser le stockage redondant interzone (ZRS), le stockage géoredondant (GRS) ou le stockage géoredondant interzone (GZRS).

Une requête d’écriture adressée à un compte de stockage LRS ne retourne un indicateur de réussite que lorsque les données ont été écrites sur les trois réplicas.

Vous pouvez utiliser le stockage LRS dans les scénarios suivants :

* En revanche, si votre application stocke des données qui peuvent être recréées facilement en cas de perte, vous pouvez opter pour un stockage LRS.
* Certaines applications sont limitées à la réplication des données dans un pays/une région en raison des exigences de gouvernance des données. Dans certains cas, les régions appairées dans lesquelles les données sont répliquées pour les comptes GRS peuvent se trouver dans un autre pays/une autre région. Pour plus d’informations sur les régions associées, consultez la page [Régions Azure](https://azure.microsoft.com/regions/).
