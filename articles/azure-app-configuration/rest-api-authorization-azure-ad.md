---
title: API REST Azure App Configuration – Autorisation Azure Active Directory
description: Utiliser Azure Active Directory pour l’autorisation auprès d’Azure App Configuration à l’aide de l’API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: bebab7c06062726f7b5c7868f984cadda3b4c98e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423656"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Autorisation Azure Active Directory – Référence d’API REST

Quand l’authentification Azure Active Directory (Azure AD) est utilisée, l’autorisation est gérée par un contrôle d’accès en fonction du rôle (RBAC) Azure. Un RBAC Azure requiert que les utilisateurs soient assignés à des rôles pour leur accorder l’accès aux ressources. Chaque rôle contient un ensemble d’actions que les utilisateurs assignés au rôle pourront effectuer.

## <a name="roles"></a>Rôles

Les rôles suivants sont des rôles intégrés disponibles dans les abonnements Azure par défaut :

- **Propriétaire des données Azure App Configuration** : ce rôle donne un accès complet à toutes les opérations.
- **Lecteur des données Azure App Configuration** : ce rôle permet les opérations de lecture.

## <a name="actions"></a>Actions

Les rôles contiennent une liste d’actions que les utilisateurs qui y sont assignés peuvent effectuer. Azure App Configuration prend en charge les actions suivantes :

- `Microsoft.AppConfiguration/configurationStores/keyValues/read` : cette action autorise l’accès en lecture aux ressources de clé-valeur d’App Configuration telles que /kv et /labels.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write` : cette action autorise l’accès en écriture aux ressources de clé-valeur d’App Configuration.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete` : Cette action autorise la suppression de ressources de clé-valeur d’App Configuration. Notez que la suppression d’une ressource renvoie la clé-valeur supprimée.

## <a name="errors"></a>Erreurs

```http
HTTP/1.1 403 Forbidden
```

**Raison :** le principal qui effectue la demande n’a pas les autorisations requises pour effectuer l’opération demandée.
**Solution :** attribuez le rôle requis pour effectuer l’opération demandée au principal qui effectue la demande.

## <a name="managing-role-assignments"></a>Gestion des attributions de rôles

La gestion des attributions de rôles s’effectue à l’aide des procédures de [RBAC Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) qui sont identiques dans tous les services Azure. Vous pouvez l’effectuer notamment via Azure CLI, PowerShell et le portail Azure. La documentation officielle sur la façon d’effectuer les attributions de rôles est disponible [ici](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
