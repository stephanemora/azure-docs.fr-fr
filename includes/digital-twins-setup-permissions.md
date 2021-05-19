---
author: baanders
description: Fichier include relatif aux prérequis des autorisations dans le programme d’installation d’Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: fc18bd3e7c62d699fd7c081e835ff33ea543e891
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2021
ms.locfileid: "108759914"
---
Pour être en mesure d’effectuer toutes les étapes qui suivent, vous devez disposer d’un [rôle dans votre abonnement](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) qui dispose des autorisations suivantes :
* Créer et gérer des ressources Azure
* Gérer l’accès des utilisateurs aux ressources Azure (y compris l’octroi et la délégation des autorisations)

Les rôles communs qui répondent à cette exigence sont **Propriétaire**, **Administrateur de compte** ou la combinaison des rôles **Administrateur de l’accès utilisateur** et **Contributeur**. Pour obtenir une explication complète des rôles et des autorisations, notamment les autorisations qui sont incluses avec d’autres rôles, consultez l’article [Rôles d’administrateur d’abonnement classique, rôles Azure et rôles Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) dans la documentation relative à Azure RBAC.

Pour consulter votre rôle dans l’abonnement, accédez à la page [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) du portail Azure (vous pouvez utiliser ce lien ou rechercher **Abonnements** dans la barre de recherche du portail). Recherchez le nom de l’abonnement que vous utilisez et affichez votre rôle dans la colonne **Mon rôle** :

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Vue de la page Abonnements dans le portail Azure, affichant l’utilisateur en tant que propriétaire" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Si vous constatez que la valeur est **Contributeur** ou un autre rôle qui ne dispose pas des autorisations requises décrites ci-dessus, vous pouvez contacter l’utilisateur de votre abonnement qui *en dispose* (par exemple, le propriétaire de l’abonnement ou l’administrateur du compte) et procéder de l’une des façons suivantes :
* Demandez-leur d’effectuer les étapes d’attribution de rôle en votre nom.
* Demandez qu’il élève votre rôle sur l’abonnement pour que vous disposiez des autorisations nécessaires pour continuer la procédure. Le fait que cela soit approprié dépend de votre organisation et de votre rôle au sein de celle-ci.