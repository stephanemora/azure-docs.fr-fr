---
title: Passer au niveau Standard – Azure Security Center
description: Ce démarrage rapide explique comment passer au niveau tarifaire Standard de Security Center pour renforcer la sécurité.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 550c9ff57b9c558f2f175165c7f06ead45991be9
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226008"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Démarrage rapide : Intégrer un abonnement Azure à Security Center Standard
Azure Security Center propose des fonctionnalités unifiées de gestion de la sécurité et de protection contre les menaces sur l’ensemble des charges de travail cloud hybrides. Alors que le niveau Gratuit offre une sécurité limitée aux seules ressources Azure, le niveau Standard étend ces fonctions aux ressources locales et à d’autres clouds. Security Center Standard aide à rechercher et à corriger les failles de sécurité, applique des contrôles d’accès et d’application pour bloquer les activités malveillantes, détecte les menaces à l’aide de l’analytique et de l’analyse décisionnelle et répond rapidement en cas d’attaque. Vous pouvez essayer Security Center Standard gratuitement. Pour en savoir plus, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/).

Cet article montre comment passer au niveau Standard pour renforcer la sécurité et comment installer l’agent Log Analytics sur les machines virtuelles pour surveiller les menaces et les failles de sécurité.

## <a name="prerequisites"></a>Prérequis
Pour utiliser le Centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire pour avoir un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

Pour mettre à niveau un abonnement et passer au niveau Standard, vous devez avoir le rôle de propriétaire de l’abonnement, de collaborateur de l’abonnement ou d’administrateur de la sécurité.

## <a name="enable-your-azure-subscription"></a>Activer un abonnement Azure

1. Connectez-vous au [portail Azure](https://azure.microsoft.com/features/azure-portal/).

1. Dans le menu **Microsoft Azure**, sélectionnez **Security Center**. La fenêtre **Security Center - Vue d’ensemble** s’ouvre.

   ![Vue d’ensemble de Security Center][2]

**Security Center - Vue d’ensemble** donne un aperçu unifié de l’état de sécurité des charges de travail cloud hybrides, ce qui permet de détecter et d’évaluer leur sécurité, ainsi que d’identifier et d’atténuer les risques. Security Center active automatiquement les abonnements Azure que ni vous ni aucun autre utilisateur de l’abonnement n’avez intégrés au niveau Gratuit.

Pour afficher et filtrer la liste des abonnements, cliquez sur l’élément de menu **Abonnements**. Security Center commence alors l’évaluation de la sécurité de ces abonnements pour identifier les failles de sécurité. Pour personnaliser les types d’évaluations, vous pouvez modifier la stratégie de sécurité. Une stratégie de sécurité définit la configuration souhaitée de vos charges de travail, tout en garantissant leur conformité aux exigences de sécurité réglementaires.

Quelques minutes après le premier lancement de Security Center peuvent s’afficher :

- des **Recommandations** pour améliorer la sécurité des abonnements Azure ; si vous cliquez sur la vignette **Recommandations**, une liste hiérarchisée apparaît ;
- un inventaire des ressources **Calcul et applications**, **Mise en réseau**, **Sécurité des données** et **Identité et accès** en cours d’évaluation par Security Center, avec l’état de sécurité de chacune.

Pour tirer pleinement parti de Security Center, vous devez suivre les étapes ci-dessous afin de passer au niveau Standard et d’installer l’agent Log Analytics.


## <a name="upgrade-to-the-standard-tier"></a>Passer au niveau Standard

Dans le cadre des guides de démarrage rapide et des didacticiels de Security Center, vous devez passer au niveau Standard. Il existe un essai gratuit de Security Center Standard. Pour en savoir plus, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/). 

1. Dans la barre latérale de Security Center, sélectionnez **Bien démarrer**.
 
   ![Prise en main](./media/security-center-get-started/get-started-upgrade-tab.png)

    L’onglet **Mise à jour** répertorie les abonnements et les espaces de travail éligibles à l’intégration.

1. Dans la liste **Sélectionner les espaces de travail pour activer le niveau standard sur**, sélectionnez les espaces de travail à mettre à niveau.


    > [!TIP]
    > Si vous sélectionnez un espace de travail éligible à un essai gratuit, la prochaine étape activera un essai. Si les espaces de travail ne sont pas éligibles à l’essai, une mise à jour sera appliquée et entraînera des frais.

1. Sélectionnez **Mettre à niveau** pour mettre à niveau les espaces de travail sélectionnés vers le niveau standard.


## <a name="automate-data-collection"></a>Automatiser la collecte de données
Azure Security Center collecte des données à partir de machines virtuelles Azure et d’ordinateurs extérieurs à Azure pour effectuer un monitoring des menaces et des failles de sécurité. Les données sont collectées à l’aide de l’agent Log Analytics, qui lit divers journaux d’événements et configurations liées à la sécurité de la machine et copie les données dans votre espace de travail à des fins d’analyse. Par défaut, Security Center vous crée un nouvel espace de travail.

Quand le provisionnement automatique est activé, Security Center installe l’agent Log Analytics sur toutes les machines virtuelles Azure prises en charge et sur toutes les nouvelles. L’approvisionnement automatique est vivement conseillé.

Pour activer le provisionnement automatique de l’agent Log Analytics :

1. Dans le menu principal de Security Center, sélectionnez **Tarification et paramètres**.
1. Cliquez sur l’abonnement dont vous souhaitez changer les paramètres.
1. Dans l’onglet **Collecte des données**, définissez **Provisionnement automatique** sur **On** (activé).
1. Sélectionnez **Enregistrer**.
---
  ![Activer l’approvisionnement automatique][6]

Grâce à ces nouvelles informations sur les machines virtuelles Azure, Security Center peut fournir des recommandations supplémentaires sur l’état de mise à jour du système, les configurations de la sécurité du système d’exploitation et la protection des points de terminaison, et générer des alertes de sécurité supplémentaires.

  ![Recommandations][8]

## <a name="clean-up-resources"></a>Nettoyer les ressources
D’autres guides de démarrage rapide et didacticiels de cette collection reposent sur ce guide. Si vous prévoyez de suivre les tutoriels et les guides de démarrage rapide suivants, conservez le niveau Standard et gardez le provisionnement automatique activé. Dans le cas contraire, ou si vous voulez revenir au niveau Gratuit :

1. Revenez au menu principal de Security Center et sélectionnez **Tarifs et paramètres**.
2. Cliquez sur l’abonnement que vous souhaitez remplacer par le niveau gratuit.
3. Sélectionnez **Niveau tarifaire** puis **Gratuit** pour modifier l’abonnement et passer du niveau Standard au niveau Gratuit.
5. Sélectionnez **Enregistrer**.

Si vous voulez désactiver l’approvisionnement automatique :

1. Revenez au menu principal de Security Center et sélectionnez **Tarifs et paramètres**.
2. Nettoyez l’abonnement pour lequel vous souhaitez désactiver le provisionnement automatique.
3. Dans l’onglet **Collecte des données**, définissez **Provisionnement automatique** sur **Off** (désactivé).
4. Sélectionnez **Enregistrer**.

>[!NOTE]
> La désactivation du provisionnement automatique ne supprime pas l’agent Log Analytics des machines virtuelles Azure sur lesquelles l’agent a été provisionné. La désactivation de l’approvisionnement automatique limite la surveillance de la sécurité pour vos ressources.
>

## <a name="next-steps"></a>Étapes suivantes
Dans ce démarrage rapide, vous avez effectué une mise à niveau vers le niveau Standard et provisionné l’agent Log Analytics pour profiter de fonctionnalités unifiées de gestion de la sécurité et de protection contre les menaces sur l’ensemble de vos charges de travail cloud hybrides. Pour en savoir plus sur Security Center, enchaînez avec le guide de démarrage rapide sur l’intégration d’ordinateurs Windows en local et dans d’autres clouds.

> [!div class="nextstepaction"]
> [Démarrage rapide : Intégrer des ordinateurs Windows à Azure Security Center](quick-onboard-windows-computer.md)

Vous souhaitez optimiser et réduire vos coûts de cloud ?

> [!div class="nextstepaction"]
> [Démarrer l’analyse des coûts avec Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
