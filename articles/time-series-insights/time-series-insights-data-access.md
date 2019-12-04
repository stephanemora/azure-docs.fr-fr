---
title: Configurer la sécurité pour accorder l’accès aux données - Azure Time Series Insights en préversion | Microsoft Docs
description: Découvrez comment configurer la sécurité, les autorisations et gérer des stratégies d’accès aux données dans votre environnement Azure Time Series Insights en préversion.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/20/2019
ms.custom: seodec18
ms.openlocfilehash: b79ca1d93baf1941d5de8db0c314f9cd21e51056
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328261"
---
# <a name="grant-data-access-to-an-environment"></a>Accorder l’accès aux données dans un environnement

Cet article décrit les deux types de stratégies d’accès d’Azure Time Series Insights en préversion.

> [!TIP]
> Lisez [Authentification et autorisation](time-series-insights-authentication-and-authorization.md) pour connaître les étapes d’inscription de l’application Azure Active Directory.

## <a name="sign-in-to-time-series-insights"></a>Se connecter à Time Series Insights

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
1. Recherchez votre environnement Time Series Insights. Entrez `Time Series` dans la zone **Recherche**. Sélectionnez **Environnement Time Series** dans les résultats de recherche.
1. Sélectionnez votre environnement Time Series Insights dans la liste.

## <a name="grant-data-access"></a>Accorder l’accès aux données

Effectuez les étapes suivantes pour accorder l’accès aux données à un utilisateur principal.

1. Sélectionnez **Stratégies d’accès aux données**, puis **+Ajouter**.

    [![Sélectionner et ajouter une stratégie d’accès aux données](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Choisissez **Sélectionner un utilisateur**. Recherchez le nom d’utilisateur ou l’adresse e-mail pour accéder à l’utilisateur que vous voulez ajouter. Sélectionnez **Sélectionner** pour confirmer la sélection.

    [![Sélectionner un utilisateur à ajouter](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Choisissez **Sélectionner un rôle**. Choisissez le rôle d’accès approprié pour l’utilisateur :

    * Sélectionnez **Contributeur** si vous voulez autoriser l’utilisateur à changer les données de référence, et à partager des requêtes et des perspectives enregistrées avec d’autres utilisateurs de l’environnement.

    * Sinon, sélectionnez **Lecteur** pour autoriser l’utilisateur à interroger les données dans l’environnement et à enregistrer des requêtes personnelles non partagées dans l’environnement.

   Sélectionnez **OK** pour confirmer le choix du rôle.

    [![Confirmer le rôle sélectionné](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Sélectionnez **OK** dans la page **Sélectionner un rôle utilisateur**.

    [![Sélectionner OK sur la page Sélectionner un rôle utilisateur](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Confirmez que la page **Stratégies d’accès aux données** répertorie les utilisateurs, et les rôles pour chaque utilisateur.

    [![Vérifier les utilisateurs et les rôles appropriés](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Fournir l’accès invité à partir d’un autre locataire Azure AD

Le rôle `Guest` n’est pas à un rôle de gestion. Il est utilisé pour désigner un compte qui a été invité par un autre locataire. Une fois que le compte invité est invité dans le répertoire du locataire, le même contrôle d’accès peut lui être appliqué comme pour tout autre compte. Vous pouvez accorder à l’administration l’accès à un environnement Time Series Insights en utilisant le Panneau Contrôle d’accès (IAM). Vous pouvez également autoriser l’accès aux données de l’environnement via le panneau Stratégies d’accès aux données. Pour plus d’informations sur l’accès invité Azure Active Directory (Azure AD), lisez [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le Portail Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Suivez ces étapes pour que l’accès invité à un environnement Time Series Insights soit accordé à un utilisateur Azure AD par un autre locataire.

1. Sélectionnez **Stratégies d’accès aux données**, puis **+Inviter**.

    [![Sélectionner Stratégies d’accès aux données, puis +Inviter](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Entrez l’adresse de messagerie de l’utilisateur que vous souhaitez inviter. Cette adresse e-mail doit être associée à Azure AD. Vous pouvez inclure un message personnel dans l’invitation.

    [![Entrer l’adresse e-mail pour rechercher l’utilisateur sélectionné](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Recherchez la bulle de confirmation qui s’affiche sur l’écran.

    [![Rechercher la bulle de confirmation qui doit s’afficher](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Choisissez **Sélectionner un utilisateur**. Pour ajouter l’utilisateur que vous avez invité, recherchez son adresse e-mail. Cliquez ensuite sur **Sélectionner** pour confirmer la sélection.

    [![Sélectionner l’utilisateur et confirmer la sélection](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Choisissez **Sélectionner un rôle**. Choisissez le rôle d’accès approprié pour l’utilisateur invité :

    * Sélectionnez **Contributeur** si vous voulez autoriser l’utilisateur à changer les données de référence, et à partager des requêtes et des perspectives enregistrées avec d’autres utilisateurs de l’environnement.

    * Sinon, sélectionnez **Lecteur** pour autoriser l’utilisateur à interroger les données dans l’environnement et à enregistrer des requêtes personnelles non partagées dans l’environnement.

   Sélectionnez **OK** pour confirmer le choix du rôle.

    [![Confirmer le choix du rôle](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Sélectionnez **OK** dans la page **Sélectionner un rôle utilisateur**.

1. Confirmez que la page **Stratégies d’accès aux données** liste l’utilisateur invité et les rôles pour chaque utilisateur invité.

    [![Vérifier que les utilisateurs et les rôles sont correctement attribués](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. À présent, l’utilisateur invité recevra un e-mail d’invitation à l’adresse e-mail spécifiée ci-dessus. L’utilisateur invité sélectionnera **Démarrer** pour confirmer qu’il accepte et se connecter à Azure Cloud.

    [![L’invité sélectionne Démarrer pour accepter ](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Une fois que vous avez sélectionné **Démarrer**, l’utilisateur invité verra s’afficher une zone d’autorisations associée à l’organisation de l’administrateur. Une fois l’autorisation accordée en sélectionnant **Accepter**, il sera connecté.

    [![L’invité passe en revue les autorisations et les accepte](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. L’administrateur [partage l’URL d’environnement](time-series-insights-parameterized-urls.md) avec son invité.

1. Une fois que l’utilisateur invité est connecté à l’adresse e-mail que vous avez utilisée pour l’inviter et qu’il accepte l’invitation, il sera dirigé vers le Portail Azure. 

1. L’invité peut maintenant accéder à l’environnement partagé à l’aide de l’URL d’environnement fournie par l’administrateur. Pour un accès immédiat, il peut entrer cette URL dans son navigateur web.

1. L’utilisateur invité verra le locataire de l’administrateur en sélectionnant son icône de profil dans l’angle supérieur droit de l’Explorateur Time Series.

    [![Sélection d’un avatar sur insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    Une fois que l’utilisateur invité a sélectionné le locataire de l’administrateur, il a la possibilité de sélectionner l’environnement Time Series Insights partagé. 
    
    Il dispose désormais de toutes les fonctionnalités associées au rôle que vous lui avez fourni à l’**étape 5**.

    [![L’utilisateur invité sélectionne votre locataire Azure dans la liste déroulante](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez le [Guide pratique pour ajouter une source d’événement Azure Event Hub](./time-series-insights-how-to-add-an-event-source-eventhub.md) à votre environnement Time Series Insights.

* Envoyez [des événements à la source d’événement](./time-series-insights-send-events.md).

* Accédez à [votre environnement dans l’explorateur Time Series Insights en préversion](./time-series-insights-update-explorer.md).
