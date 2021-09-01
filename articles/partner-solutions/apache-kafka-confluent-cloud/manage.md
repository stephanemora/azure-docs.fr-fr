---
title: Gérer un Confluent Cloud – Solutions partenaires Azure
description: Cet article décrit la gestion d’un Confluent Cloud sur le portail Azure. Comment configurer l’authentification unique, supprimer une organisation Confluent et obtenir de l’assistance.
ms.service: partner-services
ms.topic: conceptual
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: c1e53382b6f399bccac53a75595eda4e61a915a4
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112520991"
---
# <a name="manage-the-confluent-cloud-resource"></a>Gérer la ressource Confluent Cloud

Cet article explique comment gérer votre instance d’Apache Kafka pour Confluent Cloud sur Azure. Il montre comment configurer l’authentification unique (SSO) et supprimer une organisation Confluent.

## <a name="single-sign-on"></a>Authentification unique

Pour implémenter l’authentification unique pour votre organisation, votre administrateur client peut importer l’application de galerie. Cette étape est facultative. Pour plus d’informations sur l’importation d’une application, consultez [Démarrage rapide : Ajout d’une application à un locataire Azure Active Directory (Azure AD)](../../active-directory/manage-apps/add-application-portal.md). Quand l’administrateur client importe l’application, les utilisateurs n’ont pas besoin de consentir explicitement à l’autorisation d’accès pour le portail Confluent Cloud.

Pour activer l’authentification unique, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à la **Vue d’ensemble** de votre instance de la ressource Confluent Cloud.
1. Sélectionnez le lien pour **Gérer sur le Confluent Cloud**.

   :::image type="content" source="media/manage/sso-link.png" alt-text="Authentification unique du portail Confluent.":::

1. Si l’administrateur client n’a pas importé l’application de galerie pour le consentement à l’authentification unique, accordez des autorisations et un consentement. Cette étape n’est nécessaire que la première fois que vous accédez au lien vers **Gérer sur Confluent Cloud**.

   :::image type="content" source="media/manage/permissions-requested.png" alt-text="Accorder des autorisations.":::

1. Choisissez un compte de Azure AD pour l’authentification unique sur le portail Confluent Cloud.
1. Une fois le consentement fourni, vous êtes redirigé vers le portail Confluent Cloud.

## <a name="set-up-cluster"></a>Configurer un cluster

Pour plus d’informations sur la configuration de votre cluster, consultez [Créer un cluster dans Confluent Cloud – Documentation Confluent](https://docs.confluent.io/cloud/current/clusters/create-cluster.html).

## <a name="delete-confluent-organization"></a>Supprimer une organisation Confluent

Lorsque vous n’avez plus besoin de votre ressource Confluent Cloud, supprimez-la ressource dans Azure et Confluent Cloud.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour supprimer les ressources dans Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Toutes les ressources** et **Filtrer par le nom** de la ressource Confluent Cloud ou le **Type de ressource** _Organisation Confluent_. Dans le portail Azure, recherchez le nom de la ressource.
1. Dans la **Vue d’ensemble** de la ressource, sélectionnez **Supprimer**.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="Icône de suppression de ressource.":::

1. Pour confirmer la suppression, tapez le nom de la ressource, puis sélectionnez **Supprimer**.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="Invite pour confirmer la suppression de la ressource.":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Commencez par préparer votre environnement pour Azure CLI :

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Après vous être connecté, utilisez la commande [az confluent organization delete](/cli/azure/confluent#az_confluent_organization_delete) pour supprimer la ressource d’organisation par nom :

```azurecli
az confluent organization delete --name "myOrganization" --resource-group "myResourceGroup"
```

Ou par ID de ressource :

```azurecli
az confluent organization delete --id "/subscriptions/{SubID}/resourceGroups/{myResourceGroup}/providers/Microsoft.Confluent/organizations/{myOrganization}"
```

---

Pour supprimer la ressource sur Confluent Cloud, consultez la documentation relative aux [Environnements Confluent Cloud – Documentation Confluent](https://docs.confluent.io/current/cloud/using/environments.html) et [Concepts de base de Confluent Cloud – Documentation Confluent](https://docs.confluent.io/current/cloud/using/cloud-basics.html).

Le cluster et toutes les données qu’il contient sont définitivement supprimés. Si votre contrat comprend une clause de rétention des données, Confluent conserve vos données pendant la période spécifiée dans les [Conditions d’utilisation du service – Documentation Confluent](https://www.confluent.io/confluent-cloud-tos).

Vous êtes facturé au prorata de l’utilisation jusqu’à l’heure de suppression du cluster. Une fois votre cluster supprimé définitivement, Confluent vous envoie une confirmation par e-mail.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir de l’aide pour la résolution des problèmes, consultez [Résolution des problèmes des solutions Apache Kafka pour Confluent Cloud](troubleshoot.md).

Si vous avez besoin de contacter le support, consultez [Obtenir du support pour la ressource Confluent Cloud](get-support.md).
