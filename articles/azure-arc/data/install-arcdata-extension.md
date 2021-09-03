---
title: Installer l’extension `arcdata`
description: Installez l’extension `arcdata` pour Azure CLI (az)
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 5e1ddfdc13ff7bf11a2a1293ebf39c4c527c6d0f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563244"
---
# <a name="install-arcdata-azure-cli-extension"></a>Installer l’extension Azure CLI `arcdata`

> [!IMPORTANT]
> Si vous effectuez une mise à jour vers une nouvelle version, veillez à effectuer une mise à jour vers la dernière version de Azure CLI et l'extension `arcdata`.


## <a name="install-latest-azure-cli"></a>Installer la version la plus récente d’Azure CLI 

Pour installer la dernière version Azure CLI, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).


## <a name="add-arcdata-extension"></a>Ajouter une extension `arcdata`

Pour ajouter l’extension, exécutez la commande suivante : 

```azurecli
az extension add --name arcdata 
```

[En savoir plus sur les extensions de Azure CLI](/cli/azure/azure-cli-extensions-overview).

## <a name="update-arcdata-extension"></a>Mettre à jour l'extension `arcdata`

Si vous avez déjà l’extension, vous pouvez la mettre à jour en exécutant la commande suivante :

```azurecli
az extension update --name arcdata
```

## <a name="next-steps"></a>Étapes suivantes

[Créer le contrôleur de données Azure Arc](create-data-controller.md)
