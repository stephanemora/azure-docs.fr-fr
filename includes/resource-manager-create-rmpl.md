---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/20/2021
ms.author: tomfitz
ms.openlocfilehash: 0375a43e2cee9c973a957d96a9f74353d9f1ac2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910464"
---
Les liaisons privées vous permettent d’accéder aux services Azure sur un point de terminaison privé de votre réseau virtuel. Lorsque vous combinez des liaisons privées avec des opérations d’Azure Resource Manager, vous empêchez les utilisateurs qui ne se trouvent pas sur le point de terminaison spécifique de gérer les ressources. Si un utilisateur malveillant obtient les informations d’identification d’un compte de votre abonnement, il ne peut pas gérer les ressources sans être sur le point de terminaison spécifique.

Une liaison privée procure les avantages de sécurité suivants :

* **Accès privé** : les utilisateurs peuvent gérer les ressources à partir d’un réseau privé via un point de terminaison privé.
* **Exfiltration des données** : l’accès aux ressources non incluses dans l’étendue est refusé aux utilisateurs.

> [!NOTE]
> Actuellement, Azure Kubernetes Service (AKS) ne prend pas en charge l’implémentation du point de terminaison privé ARM.

## <a name="understand-architecture"></a>Comprendre l’architecture

Pour cette version, vous pouvez uniquement appliquer l’accès de gestion des liaisons privées au niveau du [groupe d’administration](../articles/governance/management-groups/overview.md) racine. Cette limitation signifie que l’accès aux liaisons privées s’applique à votre locataire.

Vous utiliserez deux types de ressources lors de l’implémentation de la gestion via une liaison privée.

* Liaison privée de gestion des ressources (Microsoft.Authorization/resourceManagementPrivateLinks)
* Association de liaison privée (Microsoft.Authorization/privateLinkAssociations)

L’image suivante montre comment construire une solution qui limite l’accès pour la gestion des ressources.

:::image type="content" source="./media/resource-manager-create-rmpl/resource-management-private-link.svg" alt-text="Schéma d’une liaison privée de gestion des ressources":::

L’association de liaison privée étend le groupe d’administration racine. L’association de liaison privée et les points de terminaison privés font référence à la liaison privée de gestion des ressources.

## <a name="workflow"></a>Workflow

Pour configurer une liaison privée pour les ressources, procédez comme suit. Les étapes sont décrites plus en détails ultérieurement dans cet article.

1. Créez la liaison privée de gestion des ressources.
1. Créez une association de liaison privée. L’association de liaison privée étend le groupe d’administration racine. Elle fait également référence à l’ID de ressource de la liaison privée de gestion des ressources.
1. Ajoutez un point de terminaison privé qui fait référence à la liaison privée de gestion des ressources.

Lorsque ces étapes sont terminées, vous pouvez gérer les ressources Azure qui se trouvent dans la hiérarchie de l’étendue. Vous utilisez un point de terminaison privé qui est connecté au sous-réseau.

Vous pouvez surveiller l’accès à la liaison privée. Pour plus d’informations, consultez [Journalisation et surveillance](../articles/private-link/private-link-overview.md#logging-and-monitoring).

## <a name="required-permissions"></a>Autorisations requises

Pour configurer la liaison privée pour la gestion des ressources, vous avez besoin de l’accès suivant :

* Propriétaire sur l'abonnement. Cet accès est nécessaire pour créer une ressource de liaison privée de gestion des ressources.
* Propriétaire ou Contributeur du groupe d’administration racine. Cet accès est nécessaire pour créer la ressource d’association de liaison privée.
* L’administrateur général d'Azure Active Directory n'est pas automatiquement autorisé à attribuer des rôles au niveau du groupe d'administration racine. Pour permettre la création de liaisons privées de gestion des ressources, l’administrateur général doit disposer d’une autorisation de lecture sur le groupe d’administration racine et d’un [accès élevé](../articles/role-based-access-control/elevate-access-global-admin.md) pour disposer de l’autorisation Administrateur de l’accès utilisateur sur tous les abonnements et groupes d’administration du locataire. Après avoir obtenu l’autorisation Administrateur de l’accès utilisateur, l’administrateur général doit accorder à l’utilisateur qui crée l’association de liaison privée l’autorisation Propriétaire ou Collaborateur au niveau du groupe d’administration racine.