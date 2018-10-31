---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 58d81bd4c1ce4b3af91a335039f62df08b340576
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399989"
---
Le stockage localement redondant (LRS) est conçu pour offrir une durabilité des objets d’au moins 99,999999999 % (11 9) sur une année donnée en répliquant vos données au sein d’une unité d’échelle de stockage. Une unité d’échelle de stockage est hébergée dans un centre de données dans la région dans laquelle vous avez créé votre compte de stockage. Une demande d’écriture adressée à un compte de stockage LRS n’est renvoyée avec succès qu’après l’écriture des données dans tous les réplicas. Ces réplicas se trouvent dans des domaines d’erreur et de mise à jour distincts d’une unité d’échelle de stockage.

Une unité d’échelle de stockage est un ensemble de racks de nœuds de stockage. Un domaine d’erreur est un groupe de nœuds qui représentent une unité physique d’incident et peuvent être considérés comme des nœuds appartenant au même rack physique. Un domaine de mise à niveau est un groupe de nœuds qui sont mis à jour ensemble au cours d’un processus de mise à niveau de service (déploiement). Les réplicas sont répartis entre les domaines de mise à niveau et d’erreur au sein d’une même unité d’échelle de stockage. Cette architecture garantit que vos données sont disponibles même en cas de défaillance matérielle d’un rack ou de mise à niveau des nœuds lors d’un déploiement.

Le stockage LRS est l’option de réplication la moins coûteuse et offre la durabilité la plus faible par rapport aux autres options. Si un sinistre se produit au niveau du centre de données (par exemple, un incendie ou une inondation), tous les réplicas risquent d’être perdus ou irrécupérables. Pour atténuer ce risque, Microsoft recommande d’utiliser le stockage redondant interzone (ZRS) ou le stockage géoredondant (GRS).

* En revanche, si votre application stocke des données qui peuvent être recréées facilement en cas de perte, vous pouvez opter pour un stockage LRS.
* Certaines applications sont limitées à la réplication des données dans un pays en raison des exigences de gouvernance des données. Dans certains cas, les régions associées dans lesquelles les données sont répliquées pour les comptes GRS peuvent se trouver dans un autre pays. Pour plus d’informations sur les régions associées, consultez la page [Régions Azure](https://azure.microsoft.com/regions/).
