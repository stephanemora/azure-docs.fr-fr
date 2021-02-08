---
title: Activer une identité managée pour les événements de routage (préversion)
titleSuffix: Azure Digital Twins
description: Découvrez comment activer une identité affectée par le système pour Azure Digital Twins et l’utiliser pour transférer des événements.
author: baanders
ms.author: baanders
ms.date: 1/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7de8e1eb1fd5311059bfb35d22b679a8c1f5ba9d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98949033"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview"></a>Activer une identité managée pour les événements de routage Azure Digital Twins (préversion)

Cet article explique comment activer une [identité affectée par le système pour une instance Azure Digital Twins](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (actuellement en préversion) et utiliser l’identité lors du transfert d’événements vers des destinations prises en charge, par exemple les destinations [Event Hub](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) , et [Conteneur Stockage Azure](../storage/blobs/storage-blobs-introduction.md).

Les étapes décrites dans cet article sont les suivantes : 

1. Créez une instance Azure Digital Twins avec une identité affectée par le système, ou activez l’identité affectée par le système sur une instance Azure Digital Twins existante. 
1. Ajoutez un rôle ou plusieurs rôles appropriés à l’identité. Par exemple, affectez le rôle **Expéditeur de données Azure Event Hub** à l’identité si le point de terminaison est Event Hub, ou le rôle **Expéditeur de données Azure Service Bus** si le point de terminaison est Service Bus.
1. Créez un point de terminaison dans Azure Digital Twins capable d’utiliser des identités affectées par le système pour l’authentification.

## <a name="enable-system-managed-identities-for-an-instance"></a>Activer des identités gérées par le système pour une instance 

Lorsque vous activez une identité affectée par le système sur votre instance Azure Digital Twins, Azure crée automatiquement une identité pour celle-ci dans [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Cette identité peut ensuite être utilisée pour s’authentifier auprès des points de terminaison Azure Digital Twins en vue du transfert d’événements.

Vous pouvez activer des identités gérées par le système pour une instance Azure Digital Twins **dans le cadre de la configuration initiale de l’instance**, ou **l’activer ultérieurement sur une instance existante**.

L’une ou l’autre de ces méthodes de création donnera les mêmes options de configuration et le même résultat final pour votre instance. Cette section décrit comment effectuer ces deux opérations.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Ajouter une identité gérée par le système lors de la création de l’instance

Dans cette section, vous allez apprendre à activer une identité gérée par le système sur une instance Azure Digital Twins en cours de création. Cette section se concentre sur l’étape de l’identité managée du processus de création. Pour une procédure pas à pas complète de création d’une instance Azure Digital Twins, consultez [*Guide pratique : Configurer une instance et l’authentification*](how-to-set-up-instance-portal.md).

L’option d’identité gérée par le système se trouve dans l’onglet **Avancé** de la configuration de l’instance.

Dans cet onglet, sélectionnez l’option **On** (Activer) pour **Identité gérée par le système** afin d’activer cette fonctionnalité.

:::image type="content" source="media/how-to-enable-managed-identities/create-instance-advanced.png" alt-text="Capture d’écran du portail Azure, montrant l’onglet Avancé de la boîte de dialogue Créer une ressource pour Azure Digital Twins. Une mise en surbrillance est appliquée au nom de l’onglet, à l’option On pour Identité gérée par le système, et aux boutons de navigation (Vérifier+ créer, Précédent, Suivant : Avancé).":::

Vous pouvez ensuite utiliser les boutons de navigation inférieurs pour continuer le reste de la configuration de l’instance.

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Ajouter une identité gérée par le système à une instance existante

Dans cette section, vous allez utiliser le [portail Azure](https://portal.azure.com) pour ajouter une identité gérée par le système à une instance Azure Digital Twins existante.

1. Tout d’abord, accédez au [portail Azure](https://portal.azure.com) dans un navigateur.

1. Recherchez le nom de votre instance dans la barre de recherche du portail, puis sélectionnez-le pour afficher plus de détails.

1. Sélectionnez **Identité (préversion)** dans le menu de gauche.

1. Dans cette page, sélectionnez l’option **On** (Activer) pour activer cette fonctionnalité.

1. Cliquez sur le bouton **Enregistrer** puis sur **Oui** pour confirmer.

    :::image type="content" source="media/how-to-enable-managed-identities/identity-digital-twins.png" alt-text="Capture d’écran du portail Azure, montrant la page Identité (préversion) pour une instance Azure Digital Twins. Une mise en surbrillance est appliquée au nom de la page dans le menu de l’instance Azure Digital Twins, à l’option On pour État, au bouton Enregistrer, et au bouton de confirmation Oui.":::

Une fois la modification enregistrée, d’autres champs s’affichent sur cette page pour les champs **ID d’objet** et **Autorisations** de la nouvelle identité.

Vous pouvez copier l’**ID d’objet** à partir d’ici, si nécessaire, et utiliser le bouton **Autorisations** pour afficher les rôles Azure affectés à l’identité. Pour configurer certains rôles, passez à la section suivante.

## <a name="assign-azure-roles-to-the-identity"></a>Affecter des rôles Azure à l’identité 

Une fois l’identité affectée par le système créée pour votre instance Azure Digital Twins, vous devez lui attribuer les rôles appropriés pour l’authentification avec différents types de [points de terminaison](concepts-route-events.md) pour transférer des événements vers des destinations prises en charge. Cette section décrit les options de rôle et comment les assigner à l’identité affectée par le système.

>[!NOTE]
> Il s’agit d’une étape importante : sans elle, l’identité ne pourra pas accéder à vos points de terminaison et les événements ne seront pas remis.

### <a name="supported-destinations-and-azure-roles"></a>Destinations et rôles Azure pris en charge 

Voici les rôles minimaux dont une identité a besoin pour accéder à un point de terminaison, en fonction du type de destination. Les rôles avec des autorisations plus élevées (comme les rôles de propriétaire des données) conviennent également.

| Destination | Rôle Azure |
| --- | --- |
| Hubs d'événements Azure | Expéditeur de données Azure Event Hubs |
| Azure Service Bus | Expéditeur de données Azure Service Bus |
| Conteneur de stockage Windows Azure | Contributeur aux données Blob du stockage |

Pour plus d’informations sur les points de terminaison, les routes et les types de destinations pris en charge pour le routage dans Azure Digital Twins, consultez [*Concepts : Routes d’événements*](concepts-route-events.md).

### <a name="assign-the-role"></a>Attribuer le rôle

>[!NOTE]
> Cette section doit être effectuée par un utilisateur Azure disposant des autorisations nécessaires pour gérer l’accès utilisateur aux ressources Azure (y compris l’octroi et la délégation d’autorisations). Les rôles communs qui répondent à cette exigence sont *Propriétaire*, *Administrateur de compte* ou la combinaison des rôles *Administrateur de l’accès utilisateur* et *Contributeur*. Pour plus d’informations sur les exigences d’autorisation pour les rôles Azure Digital Twins, consultez [*Guide pratique : Configurer une instance et l’authentification*](how-to-set-up-instance-portal.md#prerequisites-permission-requirements).

Pour affecter un rôle à l’identité, commencez par ouvrir le [portail Azure](https://portal.azure.com).

1. Accédez à votre ressource de point de terminaison (votre rubrique Event Hub, Service Bus, ou votre conteneur de stockage) en recherchant son nom dans la barre de recherche du portail. 
1. Sélectionnez **Contrôle d’accès (IAM)** dans le menu de gauche.
1. Sélectionnez le bouton **+ Ajouter** pour ajouter une nouvelle attribution de rôle.

    :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-1.png" alt-text="Capture d’écran du portail Azure, montrant la page Contrôle d’accès (IAM) pour une rubrique Event Hub. Le bouton +Ajouter est mis en surbrillance." lightbox="media/how-to-enable-managed-identities/add-role-assignment-1.png":::

1. Sur la page **Ajouter une attribution de rôle** suivante, spécifiez les valeurs :
    * **Rôle** : Sélectionnez le rôle souhaité dans le menu déroulant
    * **Attribuer l’accès à** : Choisissez **Utilisateur, groupe ou principal de service**
    * **Select** : Ici, vous allez sélectionner l’identité gérée de votre instance Azure Digital Twins affectée au rôle. Le nom de l’identité gérée correspond au nom de l’instance. Par conséquent, recherchez le nom de votre instance Azure Digital Twins. Lorsque vous sélectionnez le résultat, l’identité de l’instance s’affiche dans une section **Membres sélectionnés**.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-2.png" alt-text="Remplissage des champs répertoriés dans la boîte de dialogue « Ajouter une attribution de rôle »":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Lorsque vous avez fini d’entrer les détails, sélectionnez **Enregistrer**.

## <a name="create-an-endpoint-with-identity-based-authorization"></a>Créer un point de terminaison avec une autorisation basée sur l’identité

Après la configuration d’une identité gérée par le système pour votre instance Azure Digital Twins et l’affectation du ou des rôles appropriés, vous pouvez créer des [points de terminaison](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) Azure Digital Twins capables d’utiliser l’identité pour l’authentification. Cette option est uniquement disponible pour les points de terminaison de type Event Hub et Service Bus (elle n’est pas prise en charge pour Event Grid).

>[!NOTE]
> Vous ne pouvez pas modifier un point de terminaison déjà créé avec une identité basée sur une clé pour passer à l’authentification basée sur l’identité. Vous devez choisir le type d’authentification lorsque le point de terminaison est créé pour la première fois.

Suivez les [instructions pour créer un point de terminaison Azure Digital Twins](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins).

Lorsque vous accédez à l’étape consistant à fournir les détails de votre type de point de terminaison, veillez à sélectionner **Basée sur l’identité** comme type d’authentification.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Capture d’écran de la création d’un point de terminaison de type Event Hub." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Terminez la configuration de votre point de terminaison et sélectionnez **Enregistrer**.

## <a name="considerations-for-disabling-system-managed-identities"></a>Considérations relatives à la désactivation des identités gérées par le système

Étant donné qu’une identité est gérée séparément des points de terminaison qui l’utilisent, il est important de prendre en compte les effets que toute modification apportée à l’identité ou à ses rôles peut avoir sur les points de terminaison de votre instance Azure Digital Twins. Si l’identité est désactivée, ou si un rôle nécessaire à un point de terminaison est supprimé de celui-ci, le point de terminaison peut devenir inaccessible, et le flux des événements est interrompu.

Pour continuer à utiliser un point de terminaison configuré avec une identité gérée maintenant désactivée, vous devez supprimer le point de terminaison et [le recréer](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) avec un autre type d’authentification. L’opération peut prendre jusqu’à une heure avant que les événements ne soient remis au point de terminaison après cette modification.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les identités managées dans Azure AD : 
* [*Identités gérées pour les ressources Azure*](../active-directory/managed-identities-azure-resources/overview.md)