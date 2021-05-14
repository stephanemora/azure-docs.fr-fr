---
title: Utilisation du tableau de bord de conformité réglementaire dans Azure Security Center
description: Découvrez comment ajouter des normes réglementaires au tableau de bord de conformité réglementaire dans Security Center et comment les supprimer.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: f1fe1f15d2a2a2a3da7a7978b2d7645db65beae1
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108146686"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Personnaliser l’ensemble de normes du tableau de bord de conformité réglementaire

Azure Security Center compare continuellement la configuration de vos ressources avec les exigences des normes, réglementations et tests d’évaluation du secteur. Le **tableau de bord de conformité réglementaire** fournit des analyses sur votre situation de conformité selon la façon dont vous répondez aux exigences de conformité.

> [!TIP]
> Pour en savoir plus sur le tableau de bord de conformité réglementaire de Security Center, consultez la [FAQ](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard).

## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Comment les normes de conformité réglementaire sont-elles représentées dans Security Center ?

Les normes du secteur d’activité, les normes réglementaires et les points de référence sont représentés dans le tableau de bord de conformité réglementaire de Security Center. Chaque norme correspond à une initiative définie dans Azure Policy.

Pour voir la correspondance entre les données de conformité et les évaluations dans votre tableau de bord, ajoutez une norme de conformité à votre groupe d’administration ou à votre abonnement sur la page **Stratégie de sécurité**. Pour en savoir plus sur Azure Policy et les initiatives, consultez [Utilisation des stratégies de sécurité](tutorial-security-policy.md).

Une fois que vous avez attribué une norme ou un point de référence à l’étendue sélectionnée, la norme s’affiche dans votre tableau de bord de conformité réglementaire avec toutes les données de conformité associées en tant qu’évaluations. Vous pouvez également télécharger des rapports de synthèse pour toutes les normes qui ont été attribuées.

Microsoft suit les normes réglementaires et améliore automatiquement sa couverture dans certains packages au fur et à mesure. Lorsque Microsoft publie du nouveau contenu pour l’initiative, il s’affiche automatiquement dans votre tableau de bord en tant que nouvelles stratégies mappées aux contrôles de la norme.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Quelles sont les normes de conformité réglementaire disponibles dans Security Center ?

Par défaut, le **Benchmark de sécurité Azure** est attribué à chaque abonnement. Il s’agit de l’ensemble des directives propres à Azure et créées par Microsoft contenant les meilleures pratiques de sécurité et de conformité basées sur les infrastructures de conformité courantes. [En savoir plus sur le Benchmark de sécurité Azure](/security/benchmark/azure/introduction).

Vous pouvez également ajouter des normes telles que les suivantes :

- NIST SP 800-53
- SWIFT CSP CSCF-v2020
- UK Official et UK NHS
- PBMM fédéral du Canada
- Azure CIS 1.3.0
- CMMC niveau 3
- New Zealand ISM Restricted

Les normes sont ajoutées au tableau de bord dès qu’elles sont disponibles.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Ajouter une norme réglementaire au tableau de bord

La procédure suivante permet d’ajouter un package pour surveiller la conformité avec l’une des normes réglementaires prises en charge.

> [!NOTE]
> Pour ajouter des normes à votre tableau de bord, Azure Defender doit être activé sur l’abonnement. En outre, seuls les utilisateurs qui sont propriétaires ou contributeurs de stratégie disposent des autorisations nécessaires pour ajouter des normes de conformité. 

1. Dans la barre latérale de Security Center, sélectionnez **Conformité réglementaire** pour ouvrir le tableau de bord de conformité réglementaire. Ici, vous pouvez voir les normes de conformité actuellement attribuées aux abonnements actuellement sélectionnés.   

1. En haut de la page, sélectionnez **Gérer les stratégies de conformité**. La page Gestion des stratégies s’affiche.

1. Sélectionnez l’abonnement ou le groupe d’administration pour lequel vous souhaitez gérer la situation de conformité réglementaire. 

    > [!TIP]
    > Nous vous recommandons de sélectionner l’étendue la plus élevée pour laquelle la norme est applicable afin que les données de conformité soient agrégées et suivies pour toutes les ressources imbriquées. 

1. Pour ajouter les normes et standards correspondant à votre organisation, cliquez sur **Ajouter d’autres normes et standards**. 

1. Sur la page **Ajouter des normes de conformité réglementaire**, vous pouvez rechercher les normes disponibles, notamment :

    - **NIST SP 800-53**
    - **NIST SP 800 171**
    - **SWIFT CSP CSCF v2020**
    - **UKO et UK NHS**
    - **PBMM fédéral du Canada**
    - **HIPAA HITRUST**
    - **Azure CIS 1.3.0**
    - **CMMC niveau 3**
    - **New Zealand ISM Restricted**
    
    ![Ajout de normes réglementaires au tableau de bord de conformité réglementaire d’Azure Security Center](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Sélectionnez **Ajouter** et entrez tous les détails nécessaires pour l’initiative spécifique, comme l’étendue, les paramètres et la correction.

1. Dans la barre latérale de Security Center, sélectionnez à nouveau **Conformité réglementaire** pour revenir au tableau de bord de conformité réglementaire.

    Votre nouvelle norme s’affiche à présent dans la liste des normes réglementaires et des standards du secteur. 

    > [!NOTE]
    > L’affichage d’une norme nouvellement ajoutée dans le tableau de bord de conformité peut prendre quelques heures.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Tableau de bord de conformité réglementaire" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>Supprimer une norme de votre tableau de bord

Si certaines des normes réglementaires fournies ne sont pas pertinentes pour votre organisation, il est facile de les supprimer de l’interface utilisateur. Cela vous permet de personnaliser davantage le tableau de bord de conformité réglementaire et de vous concentrer uniquement sur les normes qui vous sont applicables.

Pour supprimer une norme :

1. Dans le menu de Security Center, sélectionnez **Stratégie de sécurité**.

1. Sélectionnez l’abonnement approprié à partir duquel vous souhaitez supprimer une norme.

    > [!NOTE]
    > Vous pouvez supprimer une norme d’un abonnement, mais pas d’un groupe d’administration. 

    La page Stratégie de sécurité s’ouvre. Pour l’abonnement sélectionné, il affiche la stratégie par défaut, le secteur et les normes réglementaires, ainsi que les initiatives personnalisées que vous avez créées.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Suppression d’une norme réglementaire de votre tableau de bord de conformité réglementaire dans Azure Security Center":::

1. Pour la norme que vous souhaitez supprimer, sélectionnez **Désactiver**. Une fenêtre de confirmation s’ouvre.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Confirmez que vous voulez vraiment supprimer la norme réglementaire que vous avez sélectionnée":::.

1. Sélectionnez **Oui**. La norme est supprimée. 


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert comment **ajouter des normes de conformité** pour surveiller votre conformité avec des normes réglementaires et d’autres du secteur d’activité.

Pour obtenir des informations connexes, consultez les pages suivantes :

- [Benchmark de sécurité Azure](/security/benchmark/azure/introduction)
- [Tableau de bord de conformité réglementaire de Security Center](security-center-compliance-dashboard.md) : découvrez comment suivre et exporter vos données de conformité réglementaire avec Security Center et des outils externes
- [Utilisation de stratégies de sécurité](tutorial-security-policy.md)