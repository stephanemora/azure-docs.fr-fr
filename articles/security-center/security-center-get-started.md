---
title: Mettre à niveau vers Azure Defender – Azure Security Center
description: Ce guide de démarrage rapide explique comment effectuer une mise à niveau vers Azure Defender de Security Center pour renforcer la sécurité.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f37a59d5db3883754b602b2b2525e07b57206b7
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099350"
---
# <a name="quickstart-set-up-azure-security-center"></a>Démarrage rapide : Configurer Azure Security Center

Azure Security Center propose des fonctionnalités unifiées de gestion de la sécurité et de protection contre les menaces sur l’ensemble des charges de travail multiclouds et hybrides. Alors que les fonctionnalités gratuites offrent une sécurité limitée pour vos ressources Azure uniquement, l’activation d’Azure Defender étend ces fonctions aux ressources locales et à d’autres clouds. Azure Defender aide à rechercher et à corriger les failles de sécurité, applique des contrôles d’accès et d’application pour bloquer les activités malveillantes, détecte les menaces à l’aide de l’analytique et de l’analyse décisionnelle et répond rapidement en cas d’attaque. Vous pouvez essayer gratuitement Azure Defender. Pour en savoir plus, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/).

Cette section du démarrage rapide vous guide tout au long des étapes recommandées pour activer Azure Security Center et Azure Defender. Une fois que vous avez terminé toutes les étapes du démarrage rapide, vous disposez des éléments suivants :

> [!div class="checklist"]
> * Security Center activé sur vos abonnements Azure
> * Azure Defender activé sur vos abonnements Azure
> * Configuration de la collecte automatique des données
> * Configuration des notifications par e-mail pour les alertes de sécurité
> * Vos machines hybrides et multiclouds connectées à Azure

## <a name="prerequisites"></a>Prérequis
Pour utiliser le Centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire pour avoir un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

Pour activer Azure Defender dans le cadre d’un abonnement, vous devez avoir le rôle de propriétaire de l’abonnement, de collaborateur de l’abonnement ou d’administrateur de la sécurité.

## <a name="enable-security-center-on-your-azure-subscription"></a>Activer Security Center sur votre abonnement Azure

> [!TIP]
> Pour activer Security Center sur tous les abonnements au sein d’un groupe d’administration, consultez [Activer Security Center sur plusieurs abonnements Azure](onboard-management-group.md).

1. Connectez-vous au [portail Azure](https://azure.microsoft.com/features/azure-portal/).

1. Dans le menu du portail, sélectionnez **Centre de sécurité**. 

    La page de présentation de Security Center s’ouvre.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Tableau de bord de présentation de Security Center" lightbox="./media/security-center-get-started/overview.png":::

    **Security Center - Vue d’ensemble** donne un aperçu unifié de l’état de sécurité des charges de travail cloud hybrides, ce qui permet de détecter et d’évaluer leur sécurité, ainsi que d’identifier et d’atténuer les risques. Security Center active automatiquement et gratuitement les abonnements Azure qui n’ont pas été intégrés par vous ni par aucun autre utilisateur de l’abonnement.

Pour afficher et filtrer la liste des abonnements, sélectionnez l’élément de menu **Abonnements**. Security Center ajuste l’affichage pour refléter la position de sécurité des abonnements sélectionnés. 

Quelques minutes après le premier lancement de Security Center peuvent s’afficher :

- des **recommandations** de méthodes visant à améliorer la sécurité de vos ressources connectées ;
- un inventaire de vos ressources en cours d’évaluation par Security Center, avec la posture de sécurité de chacune.

Pour tirer pleinement parti de Security Center, passez aux étapes suivantes de la section du démarrage rapide.



## <a name="next-steps"></a>Étapes suivantes
Dans ce démarrage rapide, vous avez activé Azure Security Center. L’étape suivante consiste à activer Azure Defender pour la gestion unifiée de la sécurité et la protection contre les menaces dans vos charges de travail de cloud hybride.

> [!div class="nextstepaction"]
> [Démarrage rapide : Activer Azure Defender](enable-azure-defender.md)