---
title: Pare-feu Azure Premium - Préversion dans le portail Azure
description: Apprenez-en davantage sur le Pare-feu Azure Premium - Préversion dans le portail Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3d56fc73faeb0d48ba7e5b21449c61d6213afa40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100549414"
---
# <a name="azure-firewall-premium-preview-in-the-azure-portal"></a>Pare-feu Azure Premium - Préversion dans le portail Azure

> [!IMPORTANT]
> Pare-feu Azure Premium est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Le Pare-feu Azure Premium - Préversion est un pare-feu de nouvelle génération avec des fonctionnalités qui sont nécessaires pour les environnements hautement sensibles et réglementés. Microsoft System Center ASP.NET 2.0 2007 offre les fonctionnalités suivantes :

- **Inspection TLS** : déchiffre le trafic sortant, traite les données, puis les chiffre et les transmet à la destination.
- **IDPS** : un système IDPS (Intrusion Detection and Prevention System) vous permet de surveiller les activités malveillantes, de consigner des informations sur ces activités, de les signaler, voire de les bloquer.
- **Filtrage d’URL** : étend la fonctionnalité de filtrage de nom de domaine complet du Pare-feu Azure pour prendre en compte une URL entière. Par exemple, `www.contoso.com/a/c` plutôt que `www.contoso.com`.
- **Catégories web** : permettent aux administrateurs d’autoriser ou de refuser aux utilisateurs l’accès aux catégories de sites web telles que les sites web de jeux d’argent, les sites web de réseaux sociaux, etc.

Pour plus d’informations, consultez [Fonctionnalités du Pare-feu Azure Premium](premium-features.md).

## <a name="deploy-the-firewall"></a>Déployer le pare-feu

Le déploiement du Pare-feu Azure Premium - Préversion est similaire au déploiement du Pare-feu Azure Standard :

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="Déploiement du portail":::

Pour **Niveau de pare-feu**, sélectionnez **Premium (préversion)** et pour **Stratégie de pare-feu**, sélectionnez une stratégie Premium existante ou créez-en une.

## <a name="configure-the-premium-policy"></a>Configurer la stratégie Premium

La configuration d’une stratégie de Pare-feu Premium est similaire à la configuration d’une stratégie de Pare-feu Standard. Une stratégie Premium vous permet de configurer les fonctionnalités Premium :

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="Déploiement d’une stratégie Premium":::

### <a name="rule-configuration"></a>Configuration des règles

Lorsque vous configurez des règles d’application dans une stratégie Premium, vous pouvez configurer des fonctionnalités Premium supplémentaires :

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="Règle Premium":::

## <a name="next-steps"></a>Étapes suivantes

Pour voir les fonctionnalités du Pare-feu Azure Premium - Préversion en action, consultez [Déployer et configurer le Pare-feu Azure Premium - Préversion](premium-deploy.md).