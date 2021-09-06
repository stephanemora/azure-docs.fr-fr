---
title: Configurer la sécurité pour accorder l’accès aux données - Azure Time Series Insights | Microsoft Docs
description: Découvrez comment configurer la sécurité, les autorisations et gérer des stratégies d’accès aux données dans votre environnement Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: tedvilutis
ms.author: tvilutis
manager: dviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: ebbb3c02263d860822482e2e19293d9a032274ef
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113135358"
---
# <a name="grant-data-access-to-an-environment"></a>Accorder l’accès aux données dans un environnement

Cet article décrit les deux types de stratégies d’accès d’Azure Time Series Insights.

> [!Warning]
> Les stratégies d’accès accordent aux utilisateurs et/ou groupes Azure AD l’accès au plan de données de votre environnement Time Series Insights.
> Un répertoire Azure Active Directory est lié à un locataire. Par conséquent, si vous décidez de déplacer votre abonnement entre les locataires, veillez à suivre la procédure décrite dans [la section ci-dessous](#procedure-for-when-the-subscription-is-moved-across-tenants).

## <a name="sign-in-to-azure-time-series-insights"></a>Connectez-vous à Azure Time Series Insights

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Localisez votre environnement Azure Time Series Insights en entrant `Time Series Insights environments` dans la **boîte de recherche**. Sélectionnez `Time Series Insights environments` dans les résultats de la recherche.
1. Sélectionnez votre environnement Azure Time Series Insights dans la liste.

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

Le rôle `Guest` n’est pas à un rôle de gestion. Il est utilisé pour désigner un compte qui a été invité par un autre locataire. Une fois que le compte invité est invité dans le répertoire du locataire, le même contrôle d’accès peut lui être appliqué comme pour tout autre compte. Vous pouvez accorder à l’administration l’accès à un environnement Azure Time Series Insights en utilisant le Panneau Contrôle d’accès (IAM). Vous pouvez également autoriser l’accès aux données de l’environnement via le panneau Stratégies d’accès aux données. Pour plus d’informations sur l’accès invité Azure Active Directory (Azure AD), lisez [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le Portail Azure](../active-directory/external-identities/add-users-administrator.md).

Suivez ces étapes pour que l’accès invité à un environnement Azure Time Series Insights soit accordé à un utilisateur Azure AD par un autre locataire.

1. Accédez à Portail Azure, cliquez sur **Azure Active Directory**, faites défiler l’onglet **Présentation**, puis sélectionnez **Utilisateur invité**.

    [![Sélectionner Stratégies d’accès aux données, puis +Inviter](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Entrez l’adresse de messagerie de l’utilisateur que vous souhaitez inviter. Cette adresse e-mail doit être associée à Azure AD. Vous pouvez inclure un message personnel dans l’invitation.

    [![Entrer l’adresse e-mail pour rechercher l’utilisateur sélectionné](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Recherchez la bulle de confirmation qui s’affiche sur l’écran. Vous pouvez également cliquer sur **Notifications** pour confirmer que l’utilisateur invité a été ajouté.

    [![Rechercher la bulle de confirmation qui doit s’afficher](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Revenez à votre environnement Time Series Insights pour ajouter l’utilisateur invité nouvellement créé. Cliquez sur **Stratégies d’accès aux données** comme décrit sous **Accorder l’accès aux données**. **Sélectionnez l’utilisateur**. Pour ajouter l’utilisateur que vous avez invité, recherchez son adresse e-mail. Cliquez ensuite sur **Sélectionner** pour confirmer la sélection.

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

1. L’utilisateur invité verra le locataire de l’administrateur en sélectionnant son icône de profil en haut à droite de l’écran dans l’Explorateur Time Series.

    [![Sélection d’un avatar sur insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)

    Une fois que l’utilisateur invité a sélectionné le locataire de l’administrateur, il a la possibilité de sélectionner l’environnement Azure Time Series Insights partagé.

    Il dispose désormais de toutes les fonctionnalités associées au rôle que vous lui avez fourni à l’**étape 5**.

    [![L’utilisateur invité sélectionne votre locataire Azure dans la liste déroulante](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="procedure-for-when-the-subscription-is-moved-across-tenants"></a>Procédure de déplacement de l’abonnement entre les locataires

Les stratégies d’accès aux données Time Series Insights sont associées à Azure Active Directory, puis liées à un locataire Azure dans lequel l’abonnement réside.

Les objets Azure AD auxquels vous accordez des stratégies d’accès aux données et l’environnement Time Series Insights doivent figurer sous le même locataire. Si ce n’est pas le cas, ces objets n’ont pas accès à l’environnement.

Si vous envisagez de déplacer l’abonnement dans lequel réside l’environnement dans un autre locataire, vous devez vous assurer que les stratégies d’accès aux données sont mises à jour pour refléter les objets de Azure AD sous le nouveau locataire.

Pour rendre ce processus plus lisse, suivez les étapes ci-dessous.

### <a name="before-moving-a-subscription-to-another-tenant"></a>Avant de déplacer un abonnement vers un autre locataire

- Veillez à conserver une liste des affectations de stratégies d’accès aux données actuelles à partir de l’environnement tant que les données sont dans le locataire source.
- Assurez-vous que les utilisateurs, les groupes ou les applications dont vous souhaitez maintenir l’accès à l’environnement après l’abonnement sont migrés vers Active Directory dans le locataire cible.
- Assurez-vous de disposer d’une personne possédant un accès contributeur (au moins) à l’abonnement après qu’il a été déplacé. Les stratégies d’accès aux données peuvent ainsi être réappliquées dans l’environnement du locataire cible.

### <a name="after-moving-a-subscription-to-another-tenant"></a>Après avoir déplacé un abonnement vers un autre locataire

Si vous avez un accès contributeur à l’abonnement dans le locataire cible, vous pouvez

- Supprimer toutes les stratégies d’accès aux données qui ont été migrées avec l’environnement, car elles appartiennent au locataire source.
- Réattribuer les stratégies d’accès à l’environnement à l’aide des étapes ci-dessus, en pointant à présent sur les objets Azure AD dans le locataire cible.

## <a name="next-steps"></a>Étapes suivantes

* Lisez [Authentification et autorisation](time-series-insights-authentication-and-authorization.md) pour connaître les étapes d’inscription de l’application Azure Active Directory.

* Affichez [votre environnement dans l’Explorateur Azure Time Series Insights](./concepts-ux-panels.md).
