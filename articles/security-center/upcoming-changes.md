---
title: Changements importants à venir sur Azure Security Center
description: Changements à venir sur Azure Security Center dont vous devez peut-être tenir compte et pour lesquels une planification peut être nécessaire
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: memildin
ms.openlocfilehash: df863372cbf7abfb6fee145b7d13bb00d8deb074
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380163"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Changements importants à venir sur Azure Security Center

> [!IMPORTANT]
> Les informations de cette page concernent des produits ou fonctionnalités en préversion, qui peuvent être modifiés de façon substantielle avant d’être commercialisés, le cas échéant. Microsoft n’offre aucun engagement ni garantie, formel ou implicite, concernant les informations contenues dans ce document.

Dans cette page, vous allez découvrir les changements planifiés pour Security Center. Elle décrit les changements planifiés du produit qui peuvent avoir un impact sur votre degré de sécurisation ou vos workflows.

Si vous recherchez les notes de publication les plus récentes, vous les trouverez dans [Nouveautés d’Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Changements planifiés

### <a name="system-updates-should-be-installed-on-your-machines-recommendation-getting-sub-recommendations"></a>Apport de sous-recommandations à la recommandation « Les mises à jour système doivent être installées sur vos machines »

#### <a name="summary"></a>Résumé

| Aspect | Détails |
|---------|---------|
|Date d’annonce | 9 novembre 2020  |
|Date de ce changement  |  Deuxième quinzaine de novembre 2020 |
|Impact     | Lors de la transition de la version actuelle de cette recommandation à sa remplaçante, votre niveau de sécurité peut changer. |
|  |  |

Nous publions une version améliorée de la recommandation **Les mises à jour système doivent être installées sur vos machines**. La nouvelle version va *remplacer* la version actuelle dans le contrôle de sécurité Appliquer les mises à jour système, en apportant les améliorations suivantes :

- Sous-recommandations pour chaque mise à jour manquante
- Une expérience repensée dans les pages Azure Security Center du portail Azure
- Données enrichies pour la recommandation provenant d’Azure Resource Graph

#### <a name="transition-period"></a>Période de transition

Il y aura une période de transition de 36 heures (environ). Afin de réduire les interruptions potentielles, nous avons planifié la mise à jour pour qu’elle ait lieu un week-end. Au cours de la transition, vos niveaux de sécurité peuvent être affectés.

#### <a name="redesigned-portal-experience"></a>Expérience du portail repensée

La page Détails de la recommandation pour **Les mises à jour système doivent être installées sur vos machines** comprend la liste des résultats, comme illustré ci-dessous. Lorsque vous sélectionnez une recherche unique, le volet d’informations s’ouvre avec un lien vers des informations de correction et une liste de ressources affectées.

:::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Ouverture de l’une des sous-recommandations dans l’expérience du portail pour la recommandation mise à jour":::


#### <a name="richer-data-from-azure-resource-graph"></a>Données enrichies à partir d’Azure Resource Graph

Azure Resource Graph (ARG) est un service Azure conçu pour fournir une exploration efficace des ressources. Vous pouvez utiliser ARG pour effectuer une requête à grande échelle sur un ensemble d’abonnements donné, et ainsi gérer de façon optimale votre environnement. 

Pour Azure Security Center, vous pouvez utiliser ARG et le [langage de requête Kusto (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) pour interroger un large éventail de données relatives à la posture de sécurité.

Si vous interrogez la version actuelle de la recommandation **Les mises à jour système doivent être installées sur vos machines**, les seules informations disponibles à partir de ARG signalent une correction de la recommandation à faire sur une machine. Lorsque la version mise à jour sera publiée, la requête suivante retournera toutes les mises à jour système manquantes, regroupées par machine.

```kusto
securityresources
| where type =~ "microsoft.security/assessments/subassessments"
| where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
| where properties.status.code == "Unhealthy"
```

## <a name="next-steps"></a>Étapes suivantes

Pour tous les changements récents du produit, consultez [Nouveautés d’Azure Security Center](release-notes.md).