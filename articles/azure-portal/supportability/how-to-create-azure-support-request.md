---
title: Création d’une demande de support Azure | Microsoft Docs
description: Les clients qui ont besoin d’aide peuvent utiliser le portail Azure pour trouver des solutions en libre-service et pour créer et gérer des demandes de support.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 01/23/2020
ms.author: kfollis
ms.openlocfilehash: 6218a1089352ce9ee3e2d1d10f3282e64eae8c51
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547657"
---
# <a name="how-to-create-an-azure-support-request"></a>Création d’une demande de support Azure

## <a name="overview"></a>Vue d’ensemble

Les clients Azure peuvent créer et gérer des demandes de support dans le [portail Azure](https://portal.azure.com).

> [!NOTE]
> L’URL du portail Azure est spécifique au cloud Azure sur lequel votre organisation est déployée.
>
>* Portail Azure pour une utilisation commerciale : [https://portal.azure.com](https://portal.azure.com)
>* Portail Azure pour l’Allemagne : [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Portail Azure pour le gouvernement des États-Unis : [https://portal.azure.us](https://portal.azure.us)
>
>

Suite aux commentaires des clients, nous avons mis à jour le processus de demande de support en fonction de trois objectifs principaux :

* **Optimisation** : nous avons facilité la recherche de support et la résolution des problèmes et simplifié la soumission d’une demande de support.
* **Intégration** : vous pouvez facilement ouvrir une demande de support quand vous travaillez à la résolution d’un problème lié à une ressource Azure, sans changer de contexte.
* **Efficacité** : collectez les informations clés dont votre agent de support a besoin pour résoudre votre problème.

## <a name="getting-started"></a>Prise en main

Vous pouvez accéder à **Aide + support** dans le portail Azure. Cette fonctionnalité est disponible dans le menu du portail Azure, dans l’en-tête global ou dans le menu des ressources d’un service. Avant de pouvoir faire une demande de support, vous devez disposer des autorisations appropriées.

### <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Pour créer une demande de support, vous devez être administrateur ou être affecté au rôle [Contributeur de demande de support](../../role-based-access-control/built-in-roles.md#support-request-contributor).

### <a name="go-to-help--support-from-the-global-header"></a>Accédez à Aide + support dans l’en-tête global

Pour démarrer une demande de support à partir de n’importe où dans le portail Azure :

1. Sélectionnez **?** dans l’en-tête global. Ensuite, sélectionnez **Aide + support**.

   ![Aide et support](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Sélectionnez **Nouvelle demande de support**. Suivez les invites pour nous fournir des informations sur votre problème. Nous vous suggérerons des solutions possibles, collecterons des détails sur le problème et vous aiderons à soumettre et à suivre la demande de support.

   ![Nouvelle demande de support](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Accéder à Aide + support dans le menu d’une ressource

Pour démarrer une demande de support dans le contexte de la ressource en cours d’utilisation :

1. Dans le menu de la ressource, dans la section **Support + dépannage**, sélectionnez **Nouvelle demande de support**.

   ![En contexte](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Suivez les invites pour nous fournir des informations sur le problème que vous rencontrez. Quand vous démarrez le processus de demande de support à partir de la ressource, certaines options sont présélectionnées pour vous.

## <a name="create-a-support-request"></a>Création d’une demande de support

Nous allons vous guider tout au long des étapes à suivre pour recueillir des informations sur votre problème et vous aider à le résoudre. Chaque étape est décrite dans les sections suivantes.

### <a name="basics"></a>Concepts de base

La première étape du processus de demande de support collecte des informations sur votre problème et votre plan de support.

Sous l’onglet **Bases** de **Nouvelle demande de support**, utilisez les sélecteurs pour commencer à nous faire part du problème. Tout d’abord, vous allez identifier des catégories générales pour le type de problème et choisir l’abonnement associé. Sélectionnez le service, par exemple **Machine virtuelle exécutant Windows**. Sélectionnez la ressource, par exemple le nom de votre machine virtuelle. Décrivez le problème avec vos propres mots, puis **sélectionnez un type de problème** pour être plus spécifique.

![Panneau Informations de base](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure offre une prise en charge illimitée de la gestion des abonnements, ce qui inclut la facturation, le réglage des quotas et les transferts de compte. Pour bénéficier du support, vous devez disposer d’un plan de support. [En savoir plus sur les plans de support Azure](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Solutions

Après avoir collecté les informations de base, nous vous présenterons des solutions à essayer vous-même. Dans certains cas, nous pouvons même exécuter un diagnostic rapide. Écrites par des ingénieurs Azure, les solutions résolvent les problèmes les plus courants.

### <a name="details"></a>Détails

Nous recueillons ensuite des détails supplémentaires sur le problème. En fournissant des informations complètes et détaillées à cette étape, vous nous aidez à acheminer votre demande de support vers l’agent approprié.

Si possible, indiquez-nous quand le problème a commencé et les étapes permettant de le reproduire. Vous pouvez charger un fichier, tel qu’un fichier journal ou le résultat d’un diagnostic.

Une fois que vous disposez de toutes les informations sur le problème, choisissez comment obtenir de l’aide. Dans la section **Méthode de support** sous **Détails**, sélectionnez la gravité de l’impact. Indiquez votre méthode de contact préférée, l’heure à laquelle nous pouvons vous contacter et la langue de votre support.

Ensuite, complétez la section **Informations de contact** afin que nous sachions comment vous contacter.

### <a name="review--create"></a>Vérifier + créer

Renseignez toutes les informations requises sous chaque onglet, puis sélectionnez **Vérifier + créer**. Vérifiez les détails que vous allez envoyer au support. Si nécessaire, vous pouvez revenir à n’importe quel onglet pour apporter une modification. Une fois que la demande de support est complète, sélectionnez **Créer**.

Un agent de support vous contactera en utilisant la méthode que vous avez indiquée. Pour plus d’informations sur le temps de réponse initial, consultez [Étendue du support et réactivité](https://azure.microsoft.com/support/plans/response/).

## <a name="all-support-requests"></a>Toutes les demandes de support

Vous pouvez afficher les détails et l’état des demandes de support en accédant à **Aide + support** >  **Toutes les demandes de support**.

![Toutes les demandes de support](./media/how-to-create-azure-support-request/allrequestslower.png)

Dans cette page, vous pouvez filtrer les demandes de support par **Abonnement**, **Date de création (UTC)** et **État**. En outre, vous pouvez trier et rechercher des demandes de support sur cette page.

Sélectionnez une demande de support pour en afficher les détails, avec la gravité et l’estimation du temps dont l’agent de support a besoin pour y répondre.

Si vous souhaitez modifier la gravité de la demande, sélectionnez **Impact commercial**. Faites votre choix parmi une liste de gravités à affecter.

> [!NOTE]
> Le niveau de gravité maximale dépend de votre plan de support. [En savoir plus sur les plans de support Azure](https://azure.microsoft.com/support/plans).
>
>
Pour en savoir plus sur les options de support en libre-service dans Azure, regardez cette vidéo :

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Étapes suivantes

* [Nous envoyer vos commentaires et suggestions](https://feedback.azure.com/forums/266794-support-feedback)
* Nous contacter sur [Twitter](https://twitter.com/azuresupport)
* Obtenir de l’aide auprès de vos pairs sur les [forums MSDN](https://social.msdn.microsoft.com/Forums/azure)
* En découvrir plus dans le [Forum aux questions du support Azure](https://azure.microsoft.com/support/faq)
