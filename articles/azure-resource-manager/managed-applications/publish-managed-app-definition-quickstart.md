---
title: Créer une définition d’application managée
description: Montre comment créer une application managée Azure destinée aux membres de votre organisation.
author: tfitzmac
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: tomfitz
ms.openlocfilehash: 5168dab7501cdb2907b3eede5877dce39e9e5c97
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79473045"
---
# <a name="quickstart-publish-an-azure-managed-application-definition"></a>Démarrage rapide : Publier une définition d’application managée Azure

Ce démarrage rapide fournit une introduction sur l’utilisation des applications managées. Vous ajoutez une définition d’application managée à un catalogue interne pour les utilisateurs dans votre organisation. Pour simplifier l’introduction, nous avons déjà créé les fichiers de votre application managée. Ces fichiers sont disponibles dans GitHub. Vous apprenez à générer ces fichiers dans le didacticiel [Créer l’application de catalogue de service](publish-service-catalog-app.md).

Lorsque vous avez terminé, vous disposez d’un groupe de ressources nommé **appDefinitionGroup** qui contient la définition d’application managée.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Créer un groupe de ressources pour définition

La définition de votre application managée existe dans un groupe de ressources. Un groupe de ressources est une collection logique dans laquelle des ressources Azure sont déployées et gérées.

Pour créer un groupe de ressources, utilisez la commande suivante :

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>Créer la définition d’application gérée

Lors de la définition de l’application managée, vous sélectionnez un utilisateur, un groupe ou une application qui gère les ressources pour le consommateur. Cette identité dispose d’autorisations sur le groupe de ressources managé en fonction du rôle attribué. En général, vous créez un groupe Azure Active Directory pour gérer les ressources. Toutefois, pour cet article, utilisez votre propre identité.

Pour obtenir l’ID d’objet de votre identité, fournissez votre nom d’utilisateur principal dans la commande suivante :

```azurecli-interactive
userid=$(az ad user show --id example@contoso.org --query objectId --output tsv)
```

Ensuite, vous avez besoin de l’ID de définition de rôle du rôle RBAC intégré auquel vous souhaitez accorder l’accès à l’utilisateur. La commande suivante montre comment obtenir l’ID de définition de rôle pour le rôle Propriétaire :

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

À présent, créez la ressource de définition de l’application managée. L’application managée contient uniquement un compte de stockage.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://github.com/Azure/azure-managedapp-samples/raw/master/Managed%20Application%20Sample%20Packages/201-managed-storage-account/managedstorage.zip"
```

Une fois la commande terminée, vous avez une définition de l’application managée dans votre groupe de ressources. 

Certains des paramètres utilisés dans cet exemple sont les suivants :

* **resource-group** : nom du groupe de ressources dans lequel la définition de l’application managée est créée.
* **lock-level** : type de verrou placé sur le groupe de ressources managées. Il empêche le client d’effectuer des opérations indésirables sur ce groupe de ressources. Actuellement, ReadOnly est le seul niveau de verrou pris en charge. Lorsque ReadOnly est spécifié, le client peut lire uniquement les ressources présentes dans le groupe de ressources gérées. Les identités de l’éditeur qui ont accès au groupe de ressources managé sont exemptées du verrou.
* **authorizations** : décrit l’ID principal et l’ID de définition de rôle utilisés pour accorder des autorisations au groupe de ressources managées. Il est spécifié sous la forme `<principalId>:<roleDefinitionId>`. Si plusieurs valeurs sont nécessaires, spécifiez-les sous la forme `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Les valeurs sont séparées par un espace.
* **package-file-uri** : emplacement d’un package .zip contenant les fichiers nécessaires. Le package doit contenir les fichiers **mainTemplate.json** et **createUiDefinition.json**. **mainTemplate.json** définit les ressources Azure créées pour l’application managée. Le modèle n’est en rien différent d’un modèle Resource Manager normal. **createUiDefinition.json** : génère l’interface utilisateur pour les clients qui créent l’application managée par le biais du portail.

## <a name="next-steps"></a>Étapes suivantes

Vous avez publié la définition d’application managée. Découvrez maintenant comment déployer une instance de cette définition.

> [!div class="nextstepaction"]
> [Démarrage rapide : Déployer une application de catalogue de services](deploy-service-catalog-quickstart.md)
