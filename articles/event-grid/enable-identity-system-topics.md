---
title: Activer une identité managée sur la rubrique système Azure Event Grid
description: Cet article explique comment activer une identité de service managée pour une rubrique système Azure Event Grid.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: d8219cf9cba4ce0a4fb5ddbcf695f1ec72de36fe
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280511"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Attribuer une identité managée par le système à une rubrique système Event Grid
Dans cet article, vous allez apprendre à activer une identité managée par le système pour une rubrique système Event Grid existante. Pour découvrir les identités managées, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md).  

> [!IMPORTANT]
> Actuellement, vous ne pouvez pas activer une identité managée par le système lors de la création d’une nouvelle rubrique système, à savoir lors de la création d’un abonnement à un événement sur une ressource Azure prenant en charge les rubriques système. 


## <a name="use-azure-portal"></a>Utiliser le portail Azure
La procédure suivante montre comment activer une identité managée par le système pour une rubrique système. 

1. Accédez au [portail Azure](https://portal.azure.com).
2. Dans la barre de recherche en haut, recherchez **Rubriques système Event Grid**.
3. Sélectionnez la **rubrique système** pour laquelle vous souhaitez activer l’identité managée. 
4. Sélectionnez **Identité** dans le menu de gauche. Cette option ne s’affiche pas pour une rubrique système située dans l’emplacement global. 
5. **Activez** le commutateur pour activer l’identité. 
1. Sélectionnez **Enregistrer** dans la barre d’outils pour enregistrer le paramètre. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="Page Identité pour une rubrique système"::: 
1. Sélectionnez **Oui** sur le message de confirmation. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="Attribuer une identité à une rubrique système - confirmation"::: 
1. Vérifiez que vous voyez l’ID d’objet de l’identité managée attribuée par le système, de même qu’un lien permettant d’attribuer des rôles. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="Attribuer une identité à une rubrique du système - terminé"::: 

## <a name="global-azure-sources"></a>Sources Azure globales
Vous pouvez activer l’identité managée par le système uniquement pour les ressources Azure régionales. Vous ne pouvez pas l’activer pour les rubriques système associées aux ressources Azure globales, telles que les abonnements Azure, groupes de ressources ou Azure Maps. De plus, les rubriques système pour ces sources globales ne sont pas associées à une région spécifique. Vous ne voyez pas la page **Identité** pour la rubrique système dont l’emplacement est défini sur **Global**. 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="Rubrique système avec emplacement défini sur Global"::: 



## <a name="next-steps"></a>Étapes suivantes
Ajouter l’identité à un rôle approprié (par exemple, expéditeur de données Service Bus) sur la destination (par exemple, une file d’attente Service Bus). Pour obtenir des instructions détaillées, consultez [Accorder à une identité managée l’accès à une destination Event Grid](add-identity-roles.md). 