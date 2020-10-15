---
title: Exemple de script Azure CLI - Importer dans un magasin App Configuration
titleSuffix: Azure App Configuration
description: Utiliser un script Azure CLI – Importation de la configuration dans Azure App Configuration
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe4c22042c31d55575320d9c3823783902700f9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494772"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importer vers un magasin Azure App Configuration

Cet exemple de script importe des paramètres de paires clé-valeur dans un magasin Azure App Configuration.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande Azure localement, vous devez exécuter Azure CLI version 2.0 ou ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Pour installer ou mettre à niveau Azure CLI, consultez [Installer l’interface de ligne de commande Microsoft Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemple de script

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour importer dans un magasin App Configuration. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az appconfig kv import](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Importe dans une ressource de magasin App Configuration. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts d’interface de ligne de commande App Configuration dans les [exemples d’interface de ligne de commande Azure App Configuration](../cli-samples.md).
