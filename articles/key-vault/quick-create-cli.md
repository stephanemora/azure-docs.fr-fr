---
title: Démarrage rapide Azure - Définir et récupérer un secret depuis Key Vault à l’aide d’Azure CLI | Microsoft Docs
description: Démarrage rapide montrant comment définir et récupérer un secret depuis Azure Key Vault à l’aide d’Azure CLI
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 4acc894f-fee0-4c2f-988e-bc0eceea5eda
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/08/2019
ms.author: barclayn
ms.openlocfilehash: 9b57dfcd6ecd00f9f1bb8ec752e0996ee52305db
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159041"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide d’Azure CLI

Azure Key Vault est un service cloud qui fonctionne comme un magasin des secrets sécurisé. Vous pouvez stocker des clés, des mots de passe, des certificats et d’autres secrets en toute sécurité. Pour plus d’informations sur Key Vault, consultez la [présentation](key-vault-overview.md). Azure CLI vous permet de créer et gérer des ressources Azure à l’aide de commandes ou de scripts. Dans ce démarrage rapide, vous allez créer un coffre de clés. Ensuite, vous y stockerez un secret.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande en local, ce guide de démarrage rapide nécessite au minimum la version 2.0.4 d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

Pour vous connecter à Azure à l’aide de l’interface CLI, vous pouvez taper la commande suivante :

```azurecli
az login
```

Pour plus d’informations sur les options de connexion par le biais de l’interface CLI, consultez [Se connecter avec Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple ci-après crée un groupe de ressources nommé *ContosoResourceGroup* à l’emplacement *eastus*.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Créer un coffre de clés

Ensuite, vous allez créer un coffre de clés dans le groupe de ressources créé à l’étape précédente. Vous devrez fournir certaines informations :

- Pour ce démarrage rapide, nous utilisons **Contoso-vault2**. Vous devez fournir un nom unique dans votre test.
- Nom du groupe de ressources **ContosoResourceGroup**.
- Emplacement **USA Est**.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

La sortie de cette cmdlet affiche les propriétés du coffre de clés que vous venez de créer. Notez les deux propriétés ci-dessous :

- **Nom du coffre** : dans l’exemple, il s’agit de **Contoso-Vault2**. Vous allez utiliser ce nom pour les autres commandes Key Vault.
- **URI du coffre** : dans l’exemple, il s’agit de https://contoso-vault2.vault.azure.net/. Les applications qui utilisent votre coffre via son API REST doivent utiliser cet URI.

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

## <a name="add-a-secret-to-key-vault"></a>Ajouter un secret au coffre de clés

Pour ajouter un secret au coffre, vous devez effectuer deux autres opérations. Ce mot de passe peut être utilisé par une application. Le mot de passe sera appelé **ExamplePassword** et stockera la valeur **Pa$$w0rd**.

Tapez les commandes ci-après pour créer un secret dans le coffre de clés, appelé **ExamplePassword**, qui stockera la valeur **Pa$$w0rd** :

```azurecli
az keyvault secret set --vault-name "Contoso-Vault2" --name "ExamplePassword" --value "Pa$$w0rd"
```

Vous pouvez maintenant référencer ce mot de passe que vous avez ajouté à Azure Key Vault à l’aide de son URI. Utilisez **https://ContosoVault.vault.azure.net/secrets/ExamplePassword** pour obtenir la version actuelle. 

Pour afficher sous forme de texte brut la valeur contenue dans le secret :

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "Contoso-Vault2"
```

Vous venez de créer un coffre de clés, d’y stocker un secret et de récupérer ce dernier.

## <a name="clean-up-resources"></a>Supprimer des ressources

D’autres guides de démarrage rapide et didacticiels de cette collection reposent sur ce guide. Si vous prévoyez d’utiliser d’autres démarrages rapides et didacticiels, il peut être utile de conserver ces ressources.
Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#delete) pour supprimer le groupe de ressources, ainsi que toutes les ressources associées. Vous pouvez supprimer les ressources comme suit :

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un coffre de clés et vous y avez stocké un secret. Pour en savoir plus sur Key Vault et sur son utilisation avec vos applications, passez au didacticiel sur les applications web qui utilisent Key Vault.

> [!div class="nextstepaction"]
> Pour apprendre à lire un secret dans un coffre de clés à partir d’une application web en utilisant des identités managées pour les ressources Azure, passez au tutoriel suivant, [Configurer une application web Azure pour lire un secret dans le coffre de clés](quick-create-net.md)
