---
title: Activer une identité managée sur la rubrique système Azure Event Grid
description: Cet article explique comment activer une identité de service managée pour une rubrique système Azure Event Grid.
ms.topic: how-to
ms.date: 08/20/2021
ms.openlocfilehash: d5d8c15c818f1a9735b6cf32fe48276fb311e8ea
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633562"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Attribuer une identité managée par le système à une rubrique système Event Grid
Dans cet article, vous allez apprendre à attribuer une identité attribuée par le système ou attribuée par l’utilisateur pour une rubrique système Event Grid existante. Pour découvrir les identités managées, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md).  

> [!IMPORTANT]
> Vous pouvez activer l’identité attribuée par le système ou l’identité attribuée par l’utilisateur pour une rubrique système, mais pas les deux. Vous pouvez avoir au maximum deux identités attribuées par l’utilisateur attribuées à une rubrique du système. 

## <a name="enable-managed-identity-for-an-existing-system-topic"></a>Activer l’identité managée pour une rubrique système existante
Cette section vous montre comment activer une identité managée pour une rubrique système existante. 

1. Accédez au [portail Azure](https://portal.azure.com).
2. Dans la barre de recherche en haut, recherchez **Rubriques système Event Grid**.
3. Sélectionnez la **rubrique système** pour laquelle vous souhaitez activer l’identité managée. 
4. Sélectionnez **Identité** dans le menu de gauche. Cette option ne s’affiche pas pour une rubrique système située dans l’emplacement global. 

### <a name="enable-system-assigned-identity"></a>Activer l’identité attribuée par le système
1. **Activez** le commutateur pour activer l’identité. 
1. Sélectionnez **Enregistrer** dans la barre d’outils pour enregistrer le paramètre. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="Page Identité pour une rubrique système."::: 
1. Sélectionnez **Oui** sur le message de confirmation. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="Attribuer une identité à une rubrique système - confirmation."::: 
1. Vérifiez que vous voyez l’ID d’objet de l’identité managée attribuée par le système, de même qu’un lien permettant d’attribuer des rôles. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="Attribuer une identité à une rubrique du système - terminé."::: 

### <a name="enable-user-assigned-identity"></a>Activer l’identité affectée par l’utilisateur

1. Créez d’abord une identité attribuée par l’utilisateur en suivant les instructions de l’article [Gérer les identités managées attribuées par l’utilisateur](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md). 
1. Dans la page **Identité**, basculez vers l’onglet **Attribué par l’utilisateur** dans le volet de droite, puis sélectionnez **+ Ajouter** dans la barre d’outils.

    :::image type="content" source="./media/managed-service-identity/system-topic-user-identity-add-button.png" alt-text="Image qui montre le bouton Ajouter sélectionné dans l’onglet Attribué par l’utilisateur de la page Identité.":::
1. Dans la fenêtre **Ajouter une identité managée de l’utilisateur**, procédez comme suit :
    1. Sélectionnez l’**abonnement Azure** qui a l’identité attribuée par l’utilisateur. 
    1. Sélectionnez l’**identité attribuée par l’utilisateur**. 
    1. Sélectionnez **Ajouter**. 
1. Actualisez la liste dans l’onglet **Attribué par l’utilisateur** pour voir l’identité attribuée par l’utilisateur ajoutée.

## <a name="enable-managed-identity-when-creating-a-system-topic"></a>Activer l’identité managée lors de la création d’une rubrique système

1. Dans le Portail Azure, dans la barre de recherche, recherchez et sélectionnez **Rubriques système Event Grid**. 
1. Dans la page **Rubriques système Event Grid**, sélectionnez **Créer** dans la barre d’outils. 
1. Dans la page **De base** de l’assistant de création, procédez comme suit : 
    1. Pour **Types de rubrique**, sélectionnez le type de la rubrique qui prend en charge une rubrique système. Dans l’exemple suivant, **Comptes de stockage** est sélectionnée. 
    2. Pour **Abonnement**, sélectionnez votre l’abonnement Azure qui contient la ressource Azure. 
    1. Pour **Groupe de ressources**, sélectionnez le groupe de ressources qui contient la ressource Azure. 
    1. Pour **Ressource**, sélectionnez la ressource. 
    1. Spécifiez un **nom** pour la rubrique système.
    1. Activer une identité managée :
        1. Pour activer l’identité attribuée par le système, sélectionnez **Activer l’identité attribuée par le système**. 
        
            :::image type="content" source="./media/managed-service-identity/system-topic-creation-enable-managed-identity.png" alt-text="Image montrant la capture d’écran de l’Assistant de création de la rubrique système avec l’option d’identité attribuée par le système sélectionnée.":::            
        1. Pour activer l’identité affectée par l’utilisateur : 
            1. Sélectionnez **Identité attribuée par l’utilisateur** et sélectionnez **Ajouter l’identité utilisateur**. 
        
                :::image type="content" source="./media/managed-service-identity/system-topic-creation-enable-user-identity.png" alt-text="Image montrant la capture d’écran de l’Assistant de création de la rubrique système avec l’option d’identité attribuée par l’utilisateur sélectionnée.":::            
            1. Dans la fenêtre **Ajouter une identité managée de l’utilisateur**, procédez comme suit :
                1. Sélectionnez l’**abonnement Azure** qui a l’identité attribuée par l’utilisateur. 
                1. Sélectionnez l’**identité attribuée par l’utilisateur**. 
                1. Sélectionnez **Ajouter**.                         

> [!NOTE]
> Actuellement, vous ne pouvez pas activer une identité managée pour une nouvelle rubrique système lors de la création d’une souscription d’événements sur une ressource Azure qui prend en charge des rubriques système. 


## <a name="global-azure-sources"></a>Sources Azure globales
Vous pouvez activer l’identité managée par le système uniquement pour les ressources Azure régionales. Vous ne pouvez pas l’activer pour les rubriques système associées aux ressources Azure globales, telles que les abonnements Azure, groupes de ressources ou Azure Maps. De plus, les rubriques système pour ces sources globales ne sont pas associées à une région spécifique. Vous ne voyez pas la page **Identité** pour la rubrique système dont l’emplacement est défini sur **Global**. 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="Rubrique système avec emplacement défini sur Global"::: 



## <a name="next-steps"></a>Étapes suivantes
Ajouter l’identité à un rôle approprié (par exemple, expéditeur de données Service Bus) sur la destination (par exemple, une file d’attente Service Bus). Pour obtenir des instructions détaillées, consultez [Accorder à une identité managée l’accès à une destination Event Grid](add-identity-roles.md). 