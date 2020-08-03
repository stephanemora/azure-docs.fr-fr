---
title: Créer et récupérer les attributs d’une clé dans Azure Key Vault - Azure CLI
description: Démarrage rapide montrant comment définir et récupérer une clé dans Azure Key Vault à l’aide d’Azure CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.openlocfilehash: e9d56f0ad2da696eb683ea393ad75254ba3ce954
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87061304"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Démarrage rapide : Définir et récupérer une clé dans Azure Key Vault avec Azure CLI

Dans ce guide de démarrage rapide, vous créez un coffre de clés dans Azure Key Vault avec Azure CLI. Azure Key Vault est un service cloud qui fonctionne comme un magasin des secrets sécurisé. Vous pouvez stocker des clés, des mots de passe, des certificats et d’autres secrets en toute sécurité. Pour plus d’informations sur Key Vault, consultez la [présentation](../general/overview.md). Azure CLI vous permet de créer et gérer des ressources Azure à l’aide de commandes ou de scripts. Une fois que vous avez terminé, vous allez stocker une clé.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande en local, ce guide de démarrage rapide nécessite au minimum la version 2.0.4 d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

Pour vous connecter à Azure à l’aide de l’interface CLI, vous pouvez taper la commande suivante :

```azurecli
az login
```

Pour plus d’informations sur les options de connexion par le biais de l’interface CLI, consultez [Se connecter avec Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

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

## <a name="add-a-key-to-key-vault"></a>Ajouter une clé à Key Vault

Pour ajouter une clé au coffre, vous devez effectuer deux autres opérations. Cette clé peut être utilisée par une application. 

Tapez les commandes ci-dessous pour créer une clé appelée **ExampleKey** :

```azurecli
az keyvault key create --vault-name "Contoso-Vault2" -n ExampleKey --protection software
```

Vous pouvez maintenant référencer cette clé que vous avez ajoutée à Azure Key Vault à l’aide de son URI. Utilisez **https://Contoso-Vault2.vault.azure.net/keys/ExampleKey** pour obtenir la version actuelle. 

Pour afficher la clé stockée précédemment :

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "Contoso-Vault2"
```

Vous venez de créer un coffre de clés, d’y stocker une clé et de récupérer cette dernière.

## <a name="clean-up-resources"></a>Nettoyer les ressources

D’autres guides de démarrage rapide et didacticiels de cette collection reposent sur ce guide. Si vous prévoyez d’utiliser d’autres démarrages rapides et didacticiels, il peut être utile de conserver ces ressources.
Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group) pour supprimer le groupe de ressources, ainsi que toutes les ressources associées. Vous pouvez supprimer les ressources comme suit :

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés et vous y avez stocké une clé. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- Consulter la référence des [commandes az keyvault Azure CLI](/cli/azure/keyvault?view=azure-cli-latest)
- Passer en revue les [bonnes pratiques relatives à Azure Key Vault](../general/best-practices.md)
