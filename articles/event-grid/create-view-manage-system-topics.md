---
title: Créer, afficher et gérer des rubriques système dans Azure Event Grid
description: Cet article explique comment afficher une rubrique système existante et créer des rubriques système Azure Event Grid à l’aide du portail Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: ac22afb351973397960e66c2a8fe86031e0b213a
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84316340"
---
# <a name="create-view-and-manage-system-topics-in-azure-event-grid"></a>Créer, afficher et gérer des rubriques système dans Azure Event Grid
Cet article explique comment effectuer les tâches suivantes :

- Créer une rubrique système
- Afficher toutes les rubriques système existantes 
- Supprime une rubrique système
- Créer un abonnement à un événement pour une rubrique système


## <a name="create-a-system-topic"></a>Créer une rubrique système
Vous pouvez créer une rubrique système pour une ressource Azure de deux manières :

- À l’aide de la page de ressources, par exemple page Compte de stockage ou page Espace de noms Event Hubs. 
- À l’aide de la page **Rubriques système Event Grid**. 

Consultez [ce guide de démarrage rapide](blob-event-quickstart-portal.md) pour un exemple de création de rubrique système à l’aide d’une page de ressources (onglet **Événements** d’une page de ressources dans le portail Azure). Les étapes suivantes permettent de créer une rubrique système à l’aide de la page **Rubrique système Event Grid**. 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la zone de recherche située en haut, saisissez**Rubriques système Event Grid**, puis appuyez sur **Entrée**. 

    ![Rechercher des rubriques système](./media/create-view-manage-system-topics/search-system-topics.png)
3. Dans la page **Rubriques système Event Grid**, sélectionnez **+ Ajouter** dans la barre d’outils.

    ![Ajouter une rubrique système - bouton de la barre d’outils](./media/create-view-manage-system-topics/add-system-topic-menu.png)
4. Dans la page **Créer une rubrique système Event Grid**, procédez comme suit :
    1. Sélectionnez le **type de rubrique**. Dans l’exemple suivant, l’option **Comptes de stockage** est sélectionnée. 
    2. Sélectionnez l’**abonnement Azure** contenant votre ressource de compte de stockage. 
    3. Sélectionnez le **groupe de ressources** contenant le compte de stockage. 
    4. Sélectionnez le **compte de stockage**. 
    5. Entrez un **nom** pour la rubrique système à créer. 
    
        > [!NOTE]
        > Vous pouvez utiliser ce nom de rubrique système pour rechercher des métriques et des journaux de diagnostic.
    6. Sélectionnez **Revoir + créer**.

        ![Créer une rubrique système](./media/create-view-manage-system-topics/create-event-grid-system-topic-page.png)
    5. Passez en revue les paramètres et sélectionnez **Créer**. 
        
        ![Examiner et créer une rubrique système](./media/create-view-manage-system-topics/system-topic-review-create.png)
    6. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource** pour voir la page **Rubrique système Event Grid** pour la rubrique système que vous avez créée. 

        ![Page des rubriques système](./media/create-view-manage-system-topics/system-topic-page.png)

## <a name="view-all-system-topics"></a>Afficher toutes les rubriques système
Procédez comme suit pour afficher toutes les rubriques système Event Grid existantes. 

> [!NOTE]
> Auparavant, lorsque vous créiez un abonnement pour un événement déclenché par des sources Azure, le service Event Grid créait automatiquement une rubrique système avec un nom généré de manière aléatoire. Vous pouvez désormais spécifier un nom pour la rubrique système lors de la création de cette rubrique. Vous pouvez utiliser cette ressource de rubrique système pour découvrir des métriques et des journaux de diagnostic.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la zone de recherche située en haut, saisissez**Rubriques système Event Grid**, puis appuyez sur **Entrée**. 

    ![Rechercher des rubriques système](./media/create-view-manage-system-topics/search-system-topics.png)
3. Dans la page **Rubriques système Event Grid**, vous voyez toutes les rubriques système. 

    ![Liste des rubriques système](./media/create-view-manage-system-topics/list-system-topics.png)
4. Sélectionnez une **rubrique système** dans la liste pour en afficher les détails. 

    ![Détails sur la rubrique système](./media/create-view-manage-system-topics/system-topic-details.png)

    Cette page affiche des informations sur la rubrique système, notamment : 
    - Source. Nom de la ressource sur laquelle la rubrique système a été créée.
    - Type de source. Type de la ressource. Par exemple, `Microsoft.Storage.StorageAccounts`, `Microsoft.EventHub.Namespaces`, `Microsoft.Resources.ResourceGroups`, etc.
    - Tous les abonnements créés pour la rubrique système.

    Cette page autorise des opérations telles que les suivantes :
    - Créer un abonnement aux événements Sélectionnez **+Abonnement aux événements** dans la barre d'outils. 
    - Supprimer un abonnement à un événement. Sélectionnez **Supprimer** dans la barre d'outils. 
    - Ajouter des balises pour la rubrique système. Sélectionnez **Balises** dans le menu de gauche, puis spécifiez les noms et les valeurs des balises. 


## <a name="delete-a-system-topic"></a>Supprime une rubrique système
1. Suivez les instructions de la section [Afficher les rubriques système](#view-all-system-topics) pour afficher toutes les rubriques système, puis sélectionnez la rubrique système que vous souhaitez supprimer de la liste. 
2. Dans la page **Rubrique système Event Grid**, sélectionnez **Supprimer** dans la barre d’outils. 

    ![Rubrique système - Bouton Supprimer](./media/create-view-manage-system-topics/system-topic-delete-button.png)
3. Dans la page Confirmation, sélectionnez **OK** pour confirmer la suppression. Cela supprime la rubrique système, ainsi que tous les abonnements aux événements de celle-ci.  

## <a name="create-an-event-subscription"></a>Créer un abonnement d’événement
1. Suivez les instructions de la section [Afficher les rubriques système](#view-all-system-topics) pour afficher toutes les rubriques système, puis sélectionnez la rubrique système que vous souhaitez supprimer de la liste. 
2. Dans la page **Rubrique système Event Grid**, sélectionnez **+ Abonnement à un événement** dans la barre d’outils. 

    ![Rubrique système - Bouton Ajouter un abonnement à un événement](./media/create-view-manage-system-topics/add-event-subscription-button.png)
3. Vérifiez que les sections **Type de rubrique**, **Ressource source** et **Nom de rubrique** sont automatiquement renseignées. Entrez un nom, sélectionnez un **Type de point de terminaison** et spécifiez le **point de terminaison**. Sélectionnez ensuite **Créer** pour créer l’abonnement à un événement. 

    ![Rubrique système - Abonnement à un événement](./media/create-view-manage-system-topics/create-event-subscription.png)


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les rubriques système et les types de rubriques pris en charge par Azure Event Grid, consultez la section [Rubriques système dans Azure Event Grid](system-topics.md). 
