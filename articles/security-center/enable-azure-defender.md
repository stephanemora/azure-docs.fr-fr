---
title: Activer les protections de charge de travail intégrées d’Azure Security Center
description: Apprenez à utiliser Azure Defender pour étendre les protections d'Azure Security Center à vos ressources hybrides et multicloud.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 06/07/2021
ms.openlocfilehash: bc8449950134ba8c247c57df06b7495a48558def
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112238591"
---
# <a name="quickstart-enable-azure-defender"></a>Démarrage rapide : Activer Azure Defender

Pour en savoir plus sur les avantages d’Azure Defender, consultez [Security Center gratuit ou Azure Defender activé](security-center-pricing.md).

## <a name="prerequisites"></a>Prérequis

Pour suivre les guides de démarrage rapide et les tutoriels Security Center, vous devez activer Azure Defender. 

Vous pouvez protéger l’intégralité d’un abonnement Azure avec Azure Defender et les protections seront héritées par toutes les ressources de l’abonnement.

Une version d’évaluation gratuite de 30 jours est disponible. Pour plus d’informations sur les prix dans la devise de votre choix et en fonction de votre région, consultez la page [Tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender-from-the-azure-portal"></a>Activer Azure Defender à partir du portail Azure

Pour activer les fonctionnalités de protection contre les menaces de Security Center, vous devez activer Azure Defender sur l’abonnement contenant les charges de travail applicables. L’activation au niveau espace de travail espace de travail n’active pas l’accès juste-à-temps à la machine virtuelle, les contrôles d’application adaptatifs et les détections réseau pour les ressources Azure. De plus, les seuls plans Azure Defender disponibles au niveau de l’espace de travail sont Azure Defender pour les serveurs et Azure Defender pour les serveurs SQL sur les machines.

- Vous pouvez activer **Azure Defender pour comptes de stockage** au niveau de l’abonnement ou de la ressource.
- Vous pouvez activer **Azure Defender pour SQL** au niveau de l’abonnement ou de la ressource.
- Il n’est possible d’activer **Azure Defender pour les bases de données relationnelles open source** qu’au niveau de la ressource.

### <a name="to-enable-azure-defender-on-your-subscriptions-and-workspaces"></a>Pour activer Azure Defender sur vos abonnements et espaces de travail :

- Pour activer Azure Defender sur un abonnement :

    1. Dans le menu principal de Security Center, sélectionnez **Tarification et paramètres**.
    1. Sélectionnez l’abonnement ou l’espace de travail que vous souhaitez protéger.
    1. Sélectionnez **Azure Defender activé** pour mettre à niveau.
    1. Sélectionnez **Enregistrer**.

    > [!TIP]
    > Vous remarquerez que chaque plan dans Azure Defender est facturé séparément et peut être défini individuellement sur activé ou désactivé. Par exemple, vous pouvez désactiver Azure Defender pour App Service sur les abonnements auxquels aucun plan Azure App Service n’est associé. 

    :::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Page de tarification de Security Center dans le portail":::

- Pour activer Azure Defender sur plusieurs abonnements ou espaces de travail :

    1. Dans la barre latérale de Security Center, sélectionnez **Bien démarrer**.

        L’onglet **Mise à jour** répertorie les abonnements et les espaces de travail éligibles à l’intégration.

        :::image type="content" source="./media/enable-azure-defender/get-started-upgrade-tab.png" alt-text="Onglet Mise à niveau de la page Prise en main."::: 

    1. Dans la liste **Sélectionner les abonnements et les espaces de travail sur lesquels activer Azure Defender**, sélectionnez les abonnements et les espaces de travail à mettre à niveau, puis sélectionnez **Mettre à niveau** pour activer Azure Defender.

       - Si vous sélectionnez des abonnements et des espaces de travail qui ne sont pas éligibles pour la version d’évaluation, l’étape suivante les met à niveau et des frais seront facturés depuis lors.
       - Si vous sélectionnez un espace de travail éligible à un essai gratuit, la prochaine étape activera un essai.

        :::image type="content" source="./media/enable-azure-defender/upgrade-selected-workspaces-and-subscriptions.png" alt-text="Mettre à niveau tous les espaces de travail et abonnements sélectionnés à partir de la page Prise en main.":::


## <a name="disable-azure-defender"></a>Désactivation d’Azure Defender

Si vous devez désactiver Azure Defender pour un abonnement, la procédure est identique, à ceci près que vous sélectionnez **Azure Defender désactivé** :
 
1. Dans le menu de Security Center, sélectionnez **Tarification et paramètres**.
1. Sélectionnez l’abonnement approprié.
1. Si votre abonnement a activé Azure Defender, ouvrez **Plans Azure Defender** et sélectionnez **Azure Defender désactivé**.

    :::image type="content" source="./media/enable-azure-defender/disable-plans.png" alt-text="Activer ou désactiver Azure Defender.":::

1. Sélectionnez **Enregistrer**.

> [!NOTE]
> Une fois que vous avez désactivé Azure Defender (un seul plan ou tous à la fois), il peut arriver que la collecte des données se poursuive pendant une courte durée. 

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez activé Azure Defender, activez la collecte automatique de données par les agents et extensions nécessaires décrits dans [Attribution automatique d’agents et d’extensions](security-center-enable-data-collection.md).