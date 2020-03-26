---
title: Passer au niveau Standard – Azure Security Center
description: Ce guide de démarrage rapide explique comment passer au niveau tarifaire Standard de Security Center pour renforcer la sécurité.
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
ms.openlocfilehash: 26d62f2c027a093ba518b98fa37ce3a31a14f175
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "73664285"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Guide de démarrage rapide : Intégrer un abonnement Azure à Security Center Standard
Azure Security Center propose des fonctionnalités unifiées de gestion de la sécurité et de protection contre les menaces sur l’ensemble des charges de travail cloud hybrides. Alors que le niveau Gratuit offre une sécurité limitée aux seules ressources Azure, le niveau Standard étend ces fonctions aux ressources locales et à d’autres clouds. Security Center Standard aide à rechercher et à corriger les failles de sécurité, applique des contrôles d’accès et d’application pour bloquer les activités malveillantes, détecte les menaces à l’aide de l’analytique et de l’analyse décisionnelle et répond rapidement en cas d’attaque. Vous pouvez essayer Security Center Standard gratuitement. Pour en savoir plus, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/).

Cet article montre comment passer au niveau Standard pour renforcer la sécurité et comment installer Microsoft Monitoring Agent sur les machines virtuelles pour surveiller les menaces et les failles de sécurité.

## <a name="prerequisites"></a>Conditions préalables requises
Pour utiliser le Centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire pour avoir un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

Pour mettre à niveau un abonnement et passer au niveau Standard, vous devez avoir le rôle de propriétaire de l’abonnement, de collaborateur de l’abonnement ou d’administrateur de la sécurité.

## <a name="enable-your-azure-subscription"></a>Activer un abonnement Azure

1. Connectez-vous au [portail Azure](https://azure.microsoft.com/features/azure-portal/).
2. Dans le menu **Microsoft Azure**, sélectionnez **Security Center**. La fenêtre **Security Center - Vue d’ensemble** s’ouvre.

   ![Vue d’ensemble de Security Center][2]

**Security Center - Vue d’ensemble** donne un aperçu unifié de l’état de sécurité des charges de travail cloud hybrides, ce qui permet de détecter et d’évaluer leur sécurité, ainsi que d’identifier et d’atténuer les risques. Security Center active automatiquement les abonnements Azure que ni vous ni aucun autre utilisateur de l’abonnement n’avez intégrés au niveau Gratuit.

Pour afficher et filtrer la liste des abonnements, cliquez sur l’élément de menu **Abonnements**. Security Center commence alors l’évaluation de la sécurité de ces abonnements pour identifier les failles de sécurité. Pour personnaliser les types d’évaluations, vous pouvez modifier la stratégie de sécurité. Une stratégie de sécurité définit la configuration souhaitée de vos charges de travail, tout en garantissant leur conformité aux exigences de sécurité réglementaires.

Quelques minutes après le premier lancement de Security Center peuvent s’afficher :

- des **Recommandations** pour améliorer la sécurité des abonnements Azure ; si vous cliquez sur la vignette **Recommandations**, une liste hiérarchisée apparaît ;
- un inventaire des ressources **Calcul et applications**, **Mise en réseau**, **Sécurité des données** et **Identité et accès** en cours d’évaluation par Security Center, avec l’état de sécurité de chacune.

Pour tirer pleinement parti de Security Center, vous devez suivre les étapes ci-dessous afin de passer au niveau Standard et d’installer Microsoft Monitoring Agent.

## <a name="upgrade-to-the-standard-tier"></a>Passer au niveau Standard
Dans le cadre des guides de démarrage rapide et des didacticiels de Security Center, vous devez passer au niveau Standard. Il existe un essai gratuit de Security Center Standard. Pour en savoir plus, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/). 

1. Dans le menu principal de Security Center, sélectionnez **Prise en main**.
 
   ![Bien démarrer][4]

2. Dans **Mise à niveau**, Security Center liste les abonnements et les espaces de travail éligibles à l’intégration. 
   - Vous pouvez cliquer sur le menu déroulant **Appliquer votre version d’évaluation** pour afficher la liste de tous les abonnements et espaces de travail avec leur éligibilité pour la version d'évaluation.
   -    Vous pouvez mettre à niveau des abonnements et espaces de travail qui ne sont pas éligibles pour la version d’évaluation.
   -    Vous pouvez sélectionner des espaces de travail et abonnements éligibles pour démarrer votre évaluation.
3. Cliquez sur **Démarrer la version d'évaluation** pour démarrer votre version d’évaluation sur les abonnements sélectionnés.


  ![Alertes de sécurité][9]

## <a name="automate-data-collection"></a>Automatiser la collecte de données
Azure Security Center collecte des données à partir de machines virtuelles Azure et d’ordinateurs extérieurs à Azure pour effectuer un monitoring des menaces et des failles de sécurité. Les données sont collectées à l’aide de Microsoft Monitoring Agent, qui lit divers journaux d’événements et configurations liées à la sécurité de la machine et copie les données dans votre espace de travail à des fins d’analyse. Par défaut, Security Center vous crée un nouvel espace de travail.

Lorsque l’approvisionnement automatique est activé, Security Center installe Microsoft Monitoring Agent sur toutes les machines virtuelles Azure prises en charge et sur toutes les nouvelles. L’approvisionnement automatique est vivement conseillé.

Pour activer l’approvisionnement automatique de Microsoft Monitoring Agent :

1. Dans le menu principal de Security Center, sélectionnez **Tarification et paramètres**.
2. Cliquez sur l’abonnement dont vous souhaitez changer les paramètres.
3. Dans l’onglet **Collecte des données**, définissez **Provisionnement automatique** sur **On** (activé).
4. Sélectionnez **Enregistrer**.
---
  ![Activer l’approvisionnement automatique][6]

Grâce à ces nouvelles informations sur les machines virtuelles Azure, Security Center peut fournir des recommandations supplémentaires sur l’état de mise à jour du système, les configurations de la sécurité du système d’exploitation et la protection des points de terminaison, et générer des alertes de sécurité supplémentaires.

  ![Recommandations][8]

## <a name="clean-up-resources"></a>Nettoyer les ressources
D’autres guides de démarrage rapide et didacticiels de cette collection reposent sur ce guide. Si vous envisagez de suivre les didacticiels et guides de démarrage rapide suivants, conservez le niveau Standard et gardez l’approvisionnement automatique activé. Dans le cas contraire, ou si vous voulez revenir au niveau Gratuit :

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
> La désactivation de l’approvisionnement automatique ne supprime pas Microsoft Monitoring Agent des machines virtuelles Azure sur lesquelles l’agent a été approvisionné. La désactivation de l’approvisionnement automatique limite la surveillance de la sécurité pour vos ressources.
>

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez effectué une mise à niveau vers le niveau Standard et approvisionné Microsoft Monitoring Agent pour profiter de fonctionnalités unifiées de gestion de la sécurité et de protection contre les menaces sur l’ensemble de vos charges de travail cloud hybrides. Pour en savoir plus sur Security Center, enchaînez avec le guide de démarrage rapide sur l’intégration d’ordinateurs Windows en local et dans d’autres clouds.

> [!div class="nextstepaction"]
> [Démarrage rapide : Intégrer des ordinateurs Windows à Azure Security Center](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
