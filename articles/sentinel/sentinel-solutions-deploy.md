---
title: Déployer des solutions Azure Sentinel | Microsoft Docs
description: Cet article montre comment les clients peuvent facilement rechercher et déployer des outils d’analyse de données empaquetés avec des connecteurs de données.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/05/2021
ms.author: yelevin
ms.openlocfilehash: ce1620982aac833472102dce8a80b0c4195eb61d
ms.sourcegitcommit: ce9178647b9668bd7e7a6b8d3aeffa827f854151
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109811753"
---
# <a name="discover-and-deploy-azure-sentinel-solutions"></a>Découvrir et déployer des solutions Azure Sentinel

> [!IMPORTANT]
>
> L’expérience des solutions Azure Sentinel est actuellement en **préversion**, tout comme les packages de solutions individuels. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Les solutions Azure Sentinel assurent la détectabilité des produits, le déploiement en une seule étape et l’activation de scénarios de produits, de domaines et/ou verticaux de bout en bout dans Azure Sentinel. Cette expérience est basée sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace) pour la détectabilité, le déploiement et l’activation des solutions, ainsi que sur l’[Espace partenaires Microsoft](/partner-center/overview) pour la création et la publication de solutions.

Les solutions peuvent se composer de tout ou partie des composants suivants :

- **Connecteurs de données**, certains avec des **analyseurs** associés
- **Classeurs**
- **Règles analytiques**
- **Requêtes de chasse**
- **Playbooks**

## <a name="find-your-solution"></a>Trouver votre solution

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Solutions (préversion)** .

1. Le panneau **Solutions** affiche une liste de solutions pouvant faire l’objet d’une recherche.

    :::image type="content" source="./media/sentinel-solutions-deploy/solutions-list.png" alt-text="Liste des solutions":::

    - Si vous faites défiler la liste vers le bas, mais que vous ne trouvez pas ce que vous recherchez, cliquez sur le lien **Charger plus** en bas pour développer la liste.

        :::image type="content" source="./media/sentinel-solutions-deploy/load-more.png" alt-text="Charger plus de solutions":::

1. Pour affiner vos choix et trouver la solution qui vous intéresse plus facilement, entrez une partie du nom de la solution dans le champ **Rechercher** en haut de la liste. (Le moteur de recherche ne reconnaît que les mots entiers.)

    :::image type="content" source="./media/sentinel-solutions-deploy/solutions-search-1.png" alt-text="Rechercher des solutions":::

1. Sélectionnez la solution de votre choix dans la liste pour la déployer. La page des détails de la solution s’ouvre sous l’onglet **Vue d’ensemble**, qui affiche des informations essentielles et importantes sur la solution.

    :::image type="content" source="./media/sentinel-solutions-deploy/proofpoint-tap-solution.png" alt-text="Solution Proofpoint Tap à la demande":::

1. Vous pouvez consulter d’autres informations utiles sur votre solution dans les onglets **Plans** et **Informations d’utilisation + Support**, et vous pouvez consulter les impressions d’autres des clients sous l’onglet **Avis**.

## <a name="deploy-your-solution"></a>Déployer votre solution

1. Sélectionnez le bouton **Créer** pour lancer l’Assistant Déploiement de solution, qui s’ouvre dans l’onglet **De base**.

    :::image type="content" source="./media/sentinel-solutions-deploy/wizard-basics.png" alt-text="Onglet De base de l’Assistant Déploiement":::

1. Entrez l’abonnement, le groupe de ressources et l’espace de travail dans lequel vous souhaitez déployer la solution. 

1. Cliquez sur **Suivant** pour parcourir les autres onglets (correspondant aux composants inclus dans la solution), où vous pouvez en savoir plus sur et, dans certains cas, configurer, chacun des composants.

    > [!NOTE]
    > Les onglets répertoriés ci-dessous correspondent aux composants proposés par la solution indiquée dans les captures d’écran qui l’accompagnent. Différentes solutions peuvent avoir différents types de composants. Par conséquent, vous ne verrez peut-être pas les mêmes onglets dans chaque solution et vous risquez de voir des onglets non présentés ci-dessous.

    1. Onglet **Analytique** de l’:::image type="content" source="./media/sentinel-solutions-deploy/wizard-analytics.png" alt-text="onglet Analytique de l’Assistant Déploiement":::

    1. Onglet **Workbooks** :::image type="content" source="./media/sentinel-solutions-deploy/wizard-workbooks.png" alt-text="Onglet Workbooks de l’Assistant Déploiement":::

    1. Onglet **Playbooks** : vous devez entrer ici des informations d’identification Proofpoint TAP valides, afin que le playbook puisse s’authentifier auprès de votre système Proofpoint pour prendre les mesures de réponse prescrites.
        :::image type="content" source="./media/sentinel-solutions-deploy/wizard-playbooks.png" alt-text="Onglet Playbooks de l’Assistant Déploiement":::

1. Enfin, dans l’onglet **Vérifier + créer**, attendez le message « Validation réussie », puis cliquez sur **Créer** pour déployer la solution. Vous pouvez également sélectionner le lien **Télécharger un modèle d’automatisation** afin d’obtenir un lien pour déployer la solution en tant que code.

    :::image type="content" source="./media/sentinel-solutions-deploy/wizard-create.png" alt-text="Onglet Vérifier et créer de l’Assistant Déploiement":::

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert les solutions Azure Sentinel et appris à les trouver et à les déployer.

- En savoir plus sur les [solutions Azure Sentinel](sentinel-solutions.md).
- Consultez le [catalogue complet des solutions Sentinel](sentinel-solutions-catalog.md).
