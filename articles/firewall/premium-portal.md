---
title: Pare-feu Azure Premium dans le Portail Azure
description: En savoir plus sur le Pare-feu Azure Premium dans le Portail Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: victorh
ms.openlocfilehash: cfb9f76751bbf5966b4aaa29e382a0946bf7cfd6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114440427"
---
# <a name="azure-firewall-premium-in-the-azure-portal"></a>Pare-feu Azure Premium dans le Portail Azure


 Pare-feu Azure Premium est un pare-feu de nouvelle génération dont les capacités sont requises pour les environnements hautement sensibles et réglementés. Microsoft System Center ASP.NET 2.0 2007 offre les fonctionnalités suivantes :

- **Inspection TLS** : déchiffre le trafic sortant, traite les données, puis les chiffre et les transmet à la destination.
- **IDPS** : un système IDPS (Intrusion Detection and Prevention System) vous permet de surveiller les activités malveillantes, de consigner des informations sur ces activités, de les signaler, voire de les bloquer.
- **Filtrage d’URL** : étend la fonctionnalité de filtrage de nom de domaine complet du Pare-feu Azure pour prendre en compte une URL entière. Par exemple, `www.contoso.com/a/c` plutôt que `www.contoso.com`.
- **Catégories web** : permettent aux administrateurs d’autoriser ou de refuser aux utilisateurs l’accès aux catégories de sites web telles que les sites web de jeux d’argent, les sites web de réseaux sociaux, etc.

Pour plus d’informations, consultez [Fonctionnalités du Pare-feu Azure Premium](premium-features.md).

## <a name="deploy-the-firewall"></a>Déployer le pare-feu

Le déploiement du Pare-feu Azure Premium est similaire au déploiement du Pare-feu Azure Standard :

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="Déploiement du portail":::

Pour **Niveau de pare-feu**, sélectionnez **Premium** et pour **Stratégie de pare-feu**, sélectionnez une stratégie Premium existante ou créez-en une nouvelle.

## <a name="configure-the-premium-policy"></a>Configurer la stratégie Premium

La configuration d’une stratégie de Pare-feu Premium est similaire à la configuration d’une stratégie de Pare-feu Standard. Une stratégie Premium vous permet de configurer les fonctionnalités Premium :

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="Déploiement d’une stratégie Premium":::

### <a name="rule-configuration"></a>Configuration des règles

Lorsque vous configurez des règles d’application dans une stratégie Premium, vous pouvez configurer des fonctionnalités Premium supplémentaires :

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="Règle Premium":::

## <a name="next-steps"></a>Étapes suivantes

Pour voir les fonctionnalités du Pare-feu Azure Premium en action, consultez [Déployer et configurer le Pare-feu Azure Premium](premium-deploy.md).