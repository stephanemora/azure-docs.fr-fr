---
title: 'Recommandations de stockage et de données : Centre de sécurité Azure'
description: Ce document traite des recommandations d’Azure Security Center qui peuvent vous aider à protéger vos données et le service SQL Azure et à rester en conformité avec les stratégies de sécurité.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 74ed55e1d460495bfa8d3d4c00bd37bb7f05260e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552863"
---
# <a name="protect-azure-data-and-storage-services"></a>Protéger les données et services de stockage Azure
Quand Azure Security Center identifie des failles de sécurité potentielles, des suggestions sont émises pour vous guider tout au long du processus de configuration des contrôles nécessaires afin de renforcer et protéger vos ressources.

Cet article décrit la page **Sécurité des données** de la section sur la sécurité des ressources dans Security Center.

Pour obtenir la liste complète des recommandations que vous pouvez voir dans cette page, consultez [Recommandations relatives aux données et au stockage](recommendations-reference.md#recs-datastorage).


## <a name="view-your-data-security-information"></a>Afficher les informations sur la sécurité de vos données

1. Dans la section **Hygiène de sécurité de la ressource**, cliquez sur **Ressources de données et de stockage**.

    ![Ressources de données et de stockage](./media/security-center-monitoring/click-data.png)

    La page **Sécurité des données** s’ouvre avec des recommandations pour les ressources de données.

    [![Ressources de données](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    À partir de cette page, vous pouvez :

    * Cliquer sur l’onglet **Vue d’ensemble** pour lister toutes les recommandations relatives aux ressources de données. 
    * Cliquer sur chaque onglet et afficher les recommandations par type de ressource.

    > [!NOTE]
    > Pour plus d’informations sur le chiffrement du stockage, consultez la page [Chiffrement du stockage Azure pour les données au repos](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Appliquer une recommandation sur une ressource de données

1. À partir d’un onglet de ressource, cliquez sur une ressource. La page d’informations s’ouvre et affiche les recommandations à appliquer.

    ![Informations sur la ressource](./media/security-center-monitoring/sql-recommendations.png)

2. Cliquez sur une recommandation. La page Recommandation s’ouvre et affiche les **Étapes de correction** à effectuer pour implémenter la recommandation.

   ![Étapes de correction](./media/security-center-monitoring/remediate1.png)

3. Cliquez sur **Entreprendre une action**. La page de paramètres de ressource s’affiche.

    ![Activer une recommandation](./media/security-center-monitoring/remediate2.png)

4. Effectuez les **Étapes de correction** et cliquez sur **Enregistrer**.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les rubriques suivantes :

* [Liste complète des recommandations en matière de sécurité d’Azure Security Center](recommendations-reference.md)
* [Protection de vos machines et de vos applications dans Azure Security Center](security-center-virtual-machine-protection.md)
* [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)