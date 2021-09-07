---
title: Se connecter à Azure Allemagne avec Azure CLI | Microsoft Docs
description: Informations sur la gestion de votre abonnement dans Azure Allemagne avec Azure CLI
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs, devx-track-azurecli
ms.openlocfilehash: b5fb2f9b55fbe949e2c171ee7bea989c1741ab5d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122531350"
---
# <a name="connect-to-azure-germany-by-using-azure-cli"></a>Se connecter à Azure Allemagne avec Azure CLI

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Pour utiliser Azure CLI, vous devez vous connecter à Azure Allemagne au lieu d’Azure global. Vous pouvez utiliser Azure CLI pour gérer un grand abonnement via des scripts ou accéder à des fonctionnalités qui ne sont pas actuellement disponibles dans le portail Azure. Si vous avez utilisé Azure CLI dans Azure global, tout est très similaire.  

## <a name="azure-cli"></a>Azure CLI
Vous disposez de plusieurs méthodes [pour installer l’interface de ligne de commande Azure](/cli/azure/install-az-cli2).  

Pour vous connecter à Azure Allemagne, définissez le cloud :

```azurecli
az cloud set --name AzureGermanCloud
```

Une fois que le cloud est défini, vous pouvez vous connecter :

```azurecli
az login --username your-user-name@your-tenant.onmicrosoft.de
```

Pour vérifier que le cloud est correctement défini sur AzureGermanCloud, exécutez l’une des commandes suivantes, puis vérifiez que l’indicateur `isActive` a la valeur `true` pour l’élément AzureGermanCloud :

```azurecli
az cloud list
```

```azurecli
az cloud list --output table
```

## <a name="azure-classic-cli"></a>Azure Classic CLI
Vous disposez de plusieurs méthodes pour [installer l’interface de ligne de commande Azure classique](/cli/azure/install-azure-cli). Si vous avez déjà installé Node, la méthode la plus simple consiste à installer le package npm.

Pour installer l’interface CLI à partir d’un package npm, vérifiez que vous avez téléchargé et installé les [derniers fichiers Node.js et npm](https://nodejs.org/en/download/package-manager/). Ensuite, exécutez **npm install** pour installer le package **azure-cli** :

```bash
npm install -g azure-cli
```

Sur les distributions Linux, vous devrez peut-être utiliser **sudo** pour exécuter la commande **npm**, comme suit :

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Si vous devez installer ou mettre à jour Node.js et npm sur votre distribution Linux ou votre système d’exploitation, nous vous recommandons d’installer la dernière version de Node.js LTS (4.x). Si vous utilisez une version antérieure, vous pouvez obtenir des erreurs d’installation.


Une fois Azure CLI installé, connectez-vous à Azure Allemagne :

```console
azure login --username your-user-name@your-tenant.onmicrosoft.de  --environment AzureGermanCloud
```

Une fois que vous êtes connecté, vous pouvez exécuter les commandes Azure CLI comme vous le feriez normalement :

```console
azure webapp list my-resource-group
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la connexion à Azure Allemagne, consultez les ressources suivantes :

* [Se connecter à Azure Allemagne avec PowerShell](./germany-get-started-connect-with-ps.md)
* [Se connecter à Azure Allemagne avec Visual Studio](./germany-get-started-connect-with-vs.md)
* [Se connecter à Azure Allemagne avec le portail Azure](./germany-get-started-connect-with-portal.md)