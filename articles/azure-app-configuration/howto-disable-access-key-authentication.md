---
title: Désactiver l’authentification par clé d’accès pour une instance de d’Azure App Configuration (préversion)
titleSuffix: Azure App Configuration
description: Découvrez comment désactiver l’authentification par clé d’accès pour une instance d’Azure App Configuration (préversion)
ms.service: azure-app-configuration
author: jimmyca15
ms.author: jimmyca
ms.topic: how-to
ms.date: 5/14/2021
ms.openlocfilehash: 451d9701b62cf46fe81bdd17f4eded63a38d1dce
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482766"
---
# <a name="disable-access-key-authentication-for-an-azure-app-configuration-instance-preview"></a>Désactiver l’authentification par clé d’accès pour une instance de d’Azure App Configuration (préversion)

Chaque requête à une ressource de configuration Azure App doit être authentifiée. Par défaut, les demandes peuvent être authentifiées soit avec des informations d’identification Azure Active Directory (Azure AD), soit à l’aide d’une clé d'accès. Entre ces deux types d’authentification, Azure AD offre une sécurité et une facilité d’utilisation supérieures par rapport à une clé d’accès, et est recommandé par Microsoft. Pour exiger que les clients utilisent Azure AD pour authentifier les demandes, vous pouvez désactiver l’utilisation des clés d’accès pour une ressource d’Azure App Configuration.

Lorsque vous désactivez l’authentification par clé d’accès pour une ressource de d’Azure App Configuration, les clés d’accès existantes pour cette ressource sont supprimées. Toutes les demandes ultérieures à la ressource à l’aide des clés d’accès précédemment existantes seront rejetées. Seules les demandes authentifiées à l’aide d’Azure AD sont exécutées. Pour plus d’informations sur l’utilisation d’Azure AD, consultez [Autoriser l’accès à Azure App Configuration avec Azure Active Directory](./concept-enable-rbac.md).

## <a name="disable-access-key-authentication"></a>Désactiver l’authentification par clé d’accès

La désactivation de l’authentification par clé d’accès entraînera la suppression de toutes les clés d’accès. Si des applications en cours d’exécution utilisent des clés d’accès pour l’authentification, elles commencent à échouer une fois l’authentification par clé d’accès désactivée. L’activation de l’authentification par clé d’accès générera à nouveau un nouvel ensemble de clés d’accès et toute application qui tentera d’utiliser les anciennes clés d’accès échouera toujours.

> [!WARNING]
> Si des clients accèdent actuellement aux données de votre ressource Azure App Configuration avec des clés d’accès, Microsoft vous recommande de migrer ces clients vers [Azure AD](./concept-enable-rbac.md) avant de désactiver l’authentification par clé d’accès.
> En outre, il est recommandé de lire la section [limitations](#limitations) ci-dessous pour vérifier que les limitations n’affectent pas l’utilisation prévue de la ressource.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour interdire l’authentification par clé d’accès pour une ressource Azure App Configuration dans le Portail Azure, procédez comme suit :

1. Accédez à votre ressource Azure App Configuration dans le Portail Azure.
2. Localisez le paramètre **Clé d'accès** sous **Paramètres**.

    :::image type="content" border="true" source="./media/access-keys-blade.png" alt-text="Capture d’écran montrant comment accéder à un panneau de clé d'accès de ressources Azure App Configuration":::

3. Définissez le bouton **Activer les touches d’accès** sur **Désactivé**.

    :::image type="content" border="true" source="./media/disable-access-keys.png" alt-text="Capture d’écran montrant comment désactiver l’authentification par clé d’accès pour Azure App Configuration":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

La possibilité de désactiver l’authentification par clé d’accès à l’aide de l’Azure CLI est en cours de développement.

---

### <a name="verify-that-access-key-authentication-is-disabled"></a>Vérifier que l’authentification de la clé d’accès est désactivée

Pour vérifier que l’authentification de la clé d’accès n’est plus autorisée, une requête peut être faite pour répertorier les clés d’accès pour la ressource Azure App Configuration. Si l’authentification par clé d’accès est désactivée, il n’y aura aucune clé d’accès et l’opération de liste renverra une liste vide.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour vérifier que l’authentification par clé d’accès est désactivée pour une ressource Azure App Configuration dans le Portail Azure, procédez comme suit :

1. Accédez à votre ressource Azure App Configuration dans le Portail Azure.
2. Localisez le paramètre **Clé d'accès** sous **Paramètres**.

    :::image type="content" border="true" source="./media/access-keys-blade.png" alt-text="Capture d’écran montrant comment accéder à un panneau de clé d'accès de ressources Azure App Configuration":::

3. Vérifiez qu’aucune clé d’accès n’est affichée et que l’option **Activation des clés d’accès** est réglée sur **Désactivé**.

    :::image type="content" border="true" source="./media/access-keys-disabled-portal.png" alt-text="Capture d’écran montrant la désactivation des clés d’accès pour une ressource Azure App Configuration":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour vérifier que l’authentification par clé d’accès est désactivée pour une ressource Azure App Configuration dans le Portail Azure, utilisez la commande suivante. La commande répertorie les clés d’accès pour une ressource Azure App Configuration et si l’authentification de la clé d’accès est désactivée, la liste sera vide.

```azurecli-interactive
az appconfig credential list \
    --name <app-configuration-name> \
    --resource-group <resource-group>
```

Si l’authentification de la clé d’accès est désactivée, une liste vide sera retournée.

```
C:\Users\User>az appconfig credential list -g <resource-group> -n <app-configuration-name>
[]
```

---

## <a name="permissions-for-allowing-or-disallowing-access-key-authentication"></a>Autorisations pour activer ou désactiver l’authentification par clé d’accès

Pour modifier l’état de l’authentification de la clé d’accès pour une ressource Azure App Configuration, un utilisateur doit disposer des autorisations nécessaires pour créer et gérer des ressources Azure App Configuration. Les rôles du contrôle d’accès en fonction du rôle Azure (Azure RBAC) offrant ces autorisations incluent l’action **Microsoft.AppConfiguration/configurationStores/write** ou **Microsoft.AppConfiguration/configurationStores/\*** . Parmi les rôles intégrés comportant cette action figurent :

- Le rôle [Propriétaire](../role-based-access-control/built-in-roles.md#owner) d’Azure Resource Manager
- Le rôle [Contributeur](../role-based-access-control/built-in-roles.md#contributor) d’Azure Resource Manager

Ces rôles ne donnent pas accès aux données dans une ressource Azure App Configuration par le biais d’Azure Active Directory (Azure AD). Toutefois, elles incluent l’autorisation d’action **Microsoft.AppConfiguration/configurationStores/listKeys/action**, qui accorde l’accès aux clés d’accès de la ressource. Avec cette autorisation, un utilisateur peut utiliser les clés d’accès pour accéder à toutes les données dans la ressource.

Les assignations de rôle doivent être étendues au niveau de la ressource Azure App Configuration ou à un niveau plus élevé pour permettre à un utilisateur d’activer ou de désactiver l’authentification par clé d'accès à la ressource. Pour plus d’informations sur l’étendue des rôles, consultez [Présentation de l’étendue pour Azure RBAC](../role-based-access-control/scope-overview.md).

Veillez à limiter l’attribution de ces rôles aux seules personnes qui ont besoin de créer une ressource App Configuration ou de mettre à jour ses propriétés. Appliquez le principe des privilèges minimum pour que les utilisateurs disposent des autorisations nécessaires les plus faibles possibles pour accomplir leurs tâches. Pour plus d’informations sur la gestion de l’accès avec Azure RBAC, consultez [Meilleures pratiques pour Azure RBAC](../role-based-access-control/best-practices.md).

> [!NOTE]
> Les rôles d’administrateur d’abonnement classique Administrateur de service et Co-administrateur incluent l’équivalent du rôle [Propriétaire](../role-based-access-control/built-in-roles.md#owner) d’Azure Resource Manager. Le rôle **Propriétaire** comprend toutes les actions. Par conséquent, un utilisateur disposant de l’un de ces rôles d’administration peut également créer et gérer des ressources App Configuration. Pour plus d’informations, consultez [Rôles d’administrateur d’abonnement classique, rôles Azure et rôles d’administrateur Azure AD](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="limitations"></a>Limites

La possibilité de désactiver l’authentification par clé d’accès est disponible en préversion. Les limitations suivantes s'appliquent pour le moment.

### <a name="arm-template-access"></a>Accès du modèle ARM

Lorsque l’authentification par clé d’accès est désactivée, la capacité à lire/écrire des valeurs de clé dans un [modèle ARM](./quickstart-resource-manager.md) est également désactivée. Cela est dû au fait que l’accès à la ressource Microsoft. AppConfiguration/configurationStores/keyValues utilisée dans les modèles ARM requiert un rôle Azure Resource Manager, tel que contributeur ou propriétaire. Lorsque l’authentification par clé d’accès est désactivée, l’accès à la ressource requiert l’un des [rôles du plan de données](concept-enable-rbac.md) d’Azure App Configuration. par conséquent, l’accès au modèle ARM est rejeté.

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser des clés gérées par le client pour chiffrer vos données App Configuration](concept-customer-managed-keys.md)
- [Utilisation de points de terminaison privés pour Azure App Configuration](concept-private-endpoint.md)