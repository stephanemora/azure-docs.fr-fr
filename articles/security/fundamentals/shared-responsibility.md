---
title: Responsabilité partagée dans le cloud – Microsoft Azure
description: Découvrez le modèle de responsabilité partagée et apprenez à distinguer les tâches de sécurité qui dépendent du fournisseur de cloud de celles qui vous incombent.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: terrylan
ms.openlocfilehash: 5d9d3878c34bd203b6c14b45e1196245ef8524f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99548947"
---
# <a name="shared-responsibility-in-the-cloud"></a>Responsabilité partagée dans le cloud

Quand vous considérez et évaluez les services de cloud public, il est essentiel de comprendre le modèle de responsabilité partagée et de savoir distinguer les tâches de sécurité qui dépendent du fournisseur de cloud de celles qui vous incombent. Les responsabilités en ce qui concerne une charge de travail varient selon qu’elle est hébergée sur un logiciel SaaS (Software as a Service), une plateforme PaaS (Platform as a Service), une infrastructure IaaS (Infrastructure as a Service) ou dans un centre de données local.

## <a name="division-of-responsibility"></a>Répartition de la responsabilité
Dans un centre de données local, vous être propriétaire de la pile entière. Quand vous migrez dans le cloud, certaines responsabilités sont transférées à Microsoft. Le schéma suivant illustre les domaines de responsabilité entre vous et Microsoft, selon le type de déploiement de votre pile.

![Zones de responsabilité](./media/shared-responsibility/shared-responsibility.png)

Vous avez vos données et les identités pour tous les types de déploiement dans le cloud. Vous êtes chargé de protéger la sécurité de vos données et des identités, des ressources locales, et des composants du cloud que vous contrôlez (qui varient selon le type de service).

Quel que soit le type de déploiement, vous conservez toujours les responsabilités suivantes :

- Données
- Points de terminaison
- Compte
- Gestion de l’accès

## <a name="cloud-security-advantages"></a>Avantages du cloud en matière de sécurité
Le cloud offre des avantages significatifs quand il s’agit de faire face à l’éternel problème de la sécurité des informations. Dans un environnement local, les organisations ont probablement des obligations non respectées et des ressources limitées pour investir dans la sécurité, avec pour résultat un environnement où les pirates informatiques sont en mesure d’exploiter des vulnérabilités à tous les niveaux.

Le schéma suivant illustre une approche traditionnelle où un grand nombre de responsabilités de sécurité ne sont pas satisfaites en raison de ressources limitées. Dans l’approche basée sur le cloud, vous pouvez transférer les responsabilités de sécurité quotidiennes à votre fournisseur de cloud et réallouer vos ressources.

![Les avantages de l’ère du cloud en matière de sécurité](./media/shared-responsibility/cloud-enabled-security.png)

Dans l’approche basée sur le cloud, vous pouvez aussi tirer parti des fonctionnalités de sécurité du cloud pour plus d’efficacité et utiliser l’intelligence cloud pour améliorer la détection des menaces et votre temps de réponse. En transférant les responsabilités au fournisseur de cloud, les organisations peuvent optimiser leur couverture de sécurité, ce qui leur permet de réaffecter des ressources de sécurité et leur budget à d'autres priorités de l’entreprise.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la répartition des responsabilités entre vous et Microsoft dans un déploiement SaaS, PaaS et IaaS, consultez [Shared Responsibilities for Cloud Computing](https://azure.microsoft.com/resources/shared-responsibility-for-cloud-computing/).
