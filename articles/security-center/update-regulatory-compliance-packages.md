---
title: Procédure de mise à jour de l’analyse de conformité réglementaire dynamique dans votre tableau de bord de conformité réglementaire Azure Security Center | Microsoft Docs
description: Mise à jour de vos packages de conformité réglementaire (préversion)
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: cfa39799e44cee0a2d36efccd454ccf8ca15157f
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603223"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard-preview"></a>Mise à jour des packages de conformité dynamique dans votre tableau de bord de conformité réglementaire (préversion)

Azure Security Center compare continuellement la configuration de vos ressources avec les exigences des normes, réglementations et tests d’évaluation du secteur. Le **tableau de bord de conformité réglementaire** fournit des analyses sur votre situation de conformité selon la façon dont vous répondez aux exigences et contrôles de conformité.

L’une des normes pour lesquelles vous pouvez effectuer le suivi de votre situation de conformité est [Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (plus formellement, « CIS Microsoft Azure Foundations Benchmark version 1.1.0 »). 

La représentation d’Azure CIS qui s’affiche initialement dans votre tableau de bord de conformité repose sur un ensemble statique de règles inclus avec Security Center.

Avec la fonctionnalité de **packages de conformité dynamiques (préversion)** , Security Center améliore automatiquement la couverture des normes du secteur au fil du temps. Les packages de conformité sont essentiellement des initiatives définies dans Azure Policy. Ils peuvent être attribués à l’étendue sélectionnée (abonnement, groupe d’administration, etc.). Pour afficher les données de conformité mappées en tant qu’évaluations dans votre tableau de bord, ajoutez un package de conformité à votre groupe d’administration ou à votre abonnement à partir de la stratégie de sécurité. L’ajout d’un package de conformité affecte l’initiative de conformité réglementaire à l’étendue sélectionnée. De cette façon, vous pouvez suivre les initiatives réglementaires récemment publiées comme des normes de conformité dans votre tableau de bord. Lorsque Microsoft publie du nouveau contenu pour l’initiative (nouvelles stratégies qui se mappent à d’autres contrôles dans la norme), le contenu supplémentaire s’affiche automatiquement dans votre tableau de bord.

Le package de conformité dynamique pour le test d’évaluation d’Azure CIS, **Azure CIS 1.1.0 (nouveau)** , améliore la version *statique* d’origine en :

* Ajoutant des stratégies supplémentaires
* Se mettant automatiquement à jour avec une nouvelle couverture lors de son ajout 

Mettez à jour vers le nouveau package dynamique en procédant comme suit.

## <a name="adding-a-dynamic-compliance-package"></a>Ajout d’un package de conformité dynamique

Les étapes suivantes expliquent comment ajouter le package dynamique pour la surveillance de votre conformité avec le test d’évaluation d’Azure CIS v1.1.0.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Mise à jour vers le package de conformité dynamique Azure CIS 1.1.0 (nouveau) 

1. Ouvrez la page **Stratégie de sécurité**. Cette page affiche le nombre de groupes d’administration, d’abonnements et d’espaces de travail, ainsi que votre structure de groupes d’administration.

1. Sélectionnez l’abonnement ou le groupe d’administration pour lequel vous souhaitez gérer la situation de conformité réglementaire. Nous vous recommandons de sélectionner l’étendue la plus élevée pour laquelle la norme est applicable afin que les données de conformité soient agrégées et suivies pour toutes les ressources imbriquées. 

1. Dans la section Normes réglementaires et du secteur (préversion), vous verrez qu’Azure CIS 1.1.0 peut être mis à jour pour du nouveau contenu. Cliquez sur **Mettre à jour maintenant**. 

1. Si vous le souhaitez, cliquez sur **Ajouter d’autres normes** pour ouvrir la page **Ajouter des normes de conformité réglementaire**. À partir de là, vous pouvez effectuer une recherche manuelle pour **Azure CIS 1.1.0 (nouveau)** et des packages dynamiques pour d’autres normes de conformité, comme **NIST SP 800-53 R4**, **SWIFT CSP CSCF-v2020**, **UKO et UK NHS**, et **Canada PBMM**.
    
    ![Ajout de packages réglementaires au tableau de bord de conformité réglementaire d’Azure Security Center](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. Dans la barre latérale de Security Center, sélectionnez **Conformité réglementaire** pour ouvrir le tableau de bord de conformité réglementaire. 
    * Azure CIS 1.1.0 (nouveau) s’affiche désormais dans la liste des normes réglementaires et du secteur. 
    * La vue *statique* d’origine de votre conformité Azure CIS 1.1.0 restera également à ses côtés. Elle pourrait être supprimée automatiquement à l’avenir.

    > [!NOTE]
    > L’affichage d’une norme nouvellement ajoutée dans le tableau de bord de conformité peut prendre quelques heures.


    [![Tableau de bord de conformité réglementaire présentant l’ancienne et la nouvelle version d’Azure CIS](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris :

* Comment **mettre à niveau les normes** présentées dans votre tableau de bord de conformité réglementaire vers les nouveaux packages *dynamiques*
* Comment **ajouter des packages de conformité** pour surveiller votre conformité avec des normes supplémentaires. 

Pour d’autres informations connexes, consultez les articles suivants : 

- [Tableau de bord de conformité réglementaire de Security Center](security-center-compliance-dashboard.md)
- [Utilisation de stratégies de sécurité](tutorial-security-policy.md)
- [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment utiliser les recommandations proposées par Azure Security Center pour protéger vos ressources Azure.