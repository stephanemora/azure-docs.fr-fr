---
title: 'Didacticiel Azure Security Center : définir et évaluer les stratégies de sécurité | Microsoft Docs'
description: 'Didacticiel Azure Security Center : définir et évaluer les stratégies de sécurité'
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: terrylan
ms.openlocfilehash: 15c69bce87ede96eb3a7bc0bada4e4f6a6669abb
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358606"
---
# <a name="tutorial-define-and-assess-security-policies"></a>Didacticiel : définir et évaluer les stratégies de sécurité
Security Center garantit la conformité aux exigences de l’entreprise et de sécurité réglementaires à l’aide de stratégies de sécurité, afin de définir la configuration souhaitée de vos charges de travail. Une fois les stratégies de vos abonnements Azure définies et adaptées au type de charge de travail ou à la sensibilité de vos données, Security Center peut fournir des recommandations de sécurité pour votre ordinateur, mais aussi sur la mise en réseau, les données et le stockage, ainsi que l’identité et l’accès aux ressources. Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer une stratégie de sécurité
> * Évaluer la sécurité de vos ressources

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

## <a name="prerequisites"></a>Prérequis
Pour parcourir les fonctionnalités traitées dans ce didacticiel, vous devez avoir accès au niveau tarifaire Standard de Security Center. Vous pouvez essayer Security Center Standard sans frais pendant 60 jours. Le démarrage rapide [Intégrer votre abonnement Azure à Security Center Standard](security-center-get-started.md) vous guide dans la mise à niveau vers le plan Standard.

## <a name="configure-security-policy"></a>Configurer une stratégie de sécurité
Security Center crée automatiquement une stratégie de sécurité par défaut, pour chacun de vos abonnements Azure. Les stratégies de sécurité sont constituées de recommandations que vous pouvez activer ou désactiver en fonction des exigences de sécurité de cet abonnement. Pour modifier la stratégie de sécurité par défaut, vous devez avoir le rôle de propriétaire, de collaborateur ou d’administrateur de la sécurité de l’abonnement.

1. Dans le menu principal de Security Center, sélectionnez **Stratégie de sécurité**.
2. Sélectionnez l’abonnement à utiliser.

  ![Stratégie de sécurité](./media/tutorial-security-policy/tutorial-security-policy-fig1.png)  

3. Dans la section **COMPOSANTS DE LA STRATÉGIE**, sélectionnez **Stratégie de sécurité**
4. Pour chaque configuration de sécurité que vous souhaitez analyser, sélectionnez **Activer**. Security Center évalue en permanence la configuration de votre environnement, et quand il existe une vulnérabilité, Security Center génère une recommandation de sécurité. Sélectionnez **Désactiver** si la configuration de sécurité n’est pas recommandée ou n’est pas pertinente. Par exemple, dans un environnement de développement et de test, vous n’aurez pas besoin du même niveau de sécurité que pour un environnement de production. Après avoir sélectionné les stratégies qui s’appliquent à votre environnement, cliquez sur **Enregistrer**.

  ![Configuration de la sécurité](./media/tutorial-security-policy/tutorial-security-policy-fig6.png)  

Attendez que Security Center traite ces stratégies et génère des recommandations. Certaines configurations, telles que les mises à jour système et les configurations de système d’exploitation, peuvent prendre jusqu'à 12 heures, tandis que les groupes de sécurité réseau et les configurations de chiffrement peuvent être évalués presque instantanément. Une fois que vous voyez les recommandations s’afficher sur le tableau de bord de Security Center, vous pouvez passer à l’étape suivante.

## <a name="assess-security-of-resources"></a>Évaluer la sécurité des ressources
1. Selon les stratégies de sécurité qui ont été activées, Security Center fournit un ensemble de recommandations de sécurité en fonction des besoins. Vous devez commencer par examiner la machine virtuelle et les recommandations d’ordinateurs. Sur le tableau de bord de Security Center, cliquez sur **Vue d’ensemble**, puis sur **Calculs et applications**.

  ![Calcul](./media/tutorial-security-policy/tutorial-security-policy-fig2.png)

  Passez en revue chaque recommandation en donnant la priorité aux recommandations en rouge (priorité haute). Certaines de ces recommandations possèdent des mises à jour qui peuvent être implémentées directement depuis Security Center, telles que [les problèmes de protection de point de terminaison](https://docs.microsoft.com/azure/security-center/security-center-install-endpoint-protection). D’autres recommandations n’ont que des instructions pour réaliser la mise à jour, telles que la recommandation de chiffrement de disque manquant.

2. Une fois toutes les recommandations de calcul appropriées traitées, vous devez passer à la charge de travail suivante : la mise en réseau. Sur le tableau de bord de Security Center, cliquez sur **Vue d’ensemble**, puis sur **Mise en réseau**.

  ![Mise en réseau](./media/tutorial-security-policy/tutorial-security-policy-fig3.png)

  La page de recommandations relatives à la mise en réseau possède une liste de problèmes de sécurité pour la configuration du réseau, les points de terminaison sur Internet et la topologie du réseau. Comme pour **Calculs et applications**, certaines recommandations concernant la mise en réseau possèdent une mise à jour intégrée, tandis que d’autres non.

3. Une fois toutes les recommandations de mise en réseau appropriées traitées, vous devez passer à la charge de travail suivante : stockage et données. Sur le tableau de bord de Security Center, cliquez sur **Vue d’ensemble**, puis sur **Stockage et données**.

  ![Ressources de données](./media/tutorial-security-policy/tutorial-security-policy-fig4.png)

  La page **Ressources de données** comporte des recommandations centrées autour de l’audit des bases de données et des serveurs SQL Azure, de l’activation du chiffrement pour les bases de données SQL et de l’activation du chiffrement pour votre compte de stockage Azure. Si vous n’avez pas ces charges de travail, vous ne verrez pas de recommandation. Comme pour **Calculs et applications**, certaines recommandations concernant le stockage et les données possèdent une mise à jour intégrée, tandis que d’autres non.

4. Une fois toutes les recommandations de stockage et de données traitées, vous devez passer à la charge de travail suivante : Identité et accès. Sur le tableau de bord de Security Center, cliquez sur **Vue d’ensemble**, puis sur **Identité et accès**.

  ![Identité et accès](./media/tutorial-security-policy/tutorial-security-policy-fig5.png)

  La page **Identité et accès** contient des recommandations telles que :

   - Activer l’authentification multi-facteur pour les comptes privilégiés sur votre abonnement
   - Supprimer les comptes externes disposant d’autorisations d’écriture de votre abonnement
   - Supprimer des comptes externes disposant de privilèges de votre abonnement

## <a name="clean-up-resources"></a>Supprimer les ressources
D’autres guides de démarrage rapide et didacticiels de cette collection reposent sur ce guide. Si vous envisagez de suivre les didacticiels et guides de démarrage rapide suivants, conservez le niveau Standard et gardez l’approvisionnement automatique activé. Dans le cas contraire, ou si vous voulez revenir au niveau Gratuit :

1. Revenez au menu principal de Security Center et sélectionnez **Stratégie de sécurité**.
2. Sélectionnez la stratégie ou l’abonnement pour lequel vous voulez revenir au niveau Gratuit. La fenêtre **Stratégie de sécurité** s’ouvre.
3. Dans **COMPOSANTS DE LA STRATÉGIE**, sélectionnez **Niveau tarifaire**.
4. Sélectionnez **Gratuit** pour modifier l’abonnement et passer du niveau Standard au niveau Gratuit.
5. Sélectionnez **Enregistrer**.

Si vous voulez désactiver l’approvisionnement automatique :

1. Revenez au menu principal de Security Center et sélectionnez **Stratégie de sécurité**.
2. Sélectionnez l’abonnement pour lequel vous souhaitez désactiver l’approvisionnement automatique.
3. Dans **Stratégie de sécurité : collecte de données**, sélectionnez **Désactivé** sous **Intégration** pour désactiver l’approvisionnement automatique.
4. Sélectionnez **Enregistrer**.

>[!NOTE]
> La désactivation de l’approvisionnement automatique ne supprime pas Microsoft Monitoring Agent des machines virtuelles Azure sur lesquelles l’agent a été approvisionné. La désactivation de l’approvisionnement automatique limite la surveillance de la sécurité pour vos ressources.
>

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez développé des connaissances à propos de la définition de stratégie de base et de l’évaluation de la sécurité de votre charge de travail avec Security Center, telles que :

> [!div class="checklist"]
> * La configuration de stratégie de sécurité pour assurer leur conformité avec les exigences de votre entreprise ou de sécurité réglementaires
> * L’évaluation de la sécurité de votre ordinateur, de la mise en réseau, de SQL et du stockage et des ressources d’application

Passer au didacticiel suivant pour apprendre à utiliser Security Center pour protéger vos ressources.

> [!div class="nextstepaction"]
> [Protéger vos ressources](tutorial-protect-resources.md)
