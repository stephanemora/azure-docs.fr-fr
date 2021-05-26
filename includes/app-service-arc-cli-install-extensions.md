---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.openlocfilehash: f400c7dbfcf3307ccc283a8e11c5d905dd152b85
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371863"
---
## <a name="add-azure-cli-extensions"></a>Ajouter des extensions Azure CLI

Lancez l’environnement Bash dans [Azure Cloud Shell](../articles/cloud-shell/quickstart.md).

[![Lancer Cloud Shell dans une nouvelle fenêtre](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com)

Comme ces commandes CLI ne font pas encore partie de l’ensemble principal de l’interface CLI, ajoutez-les avec les commandes suivantes :

```azurecli-interactive
az extension add --upgrade --yes --name customlocation
az extension remove --name appservice-kube
az extension add --yes --source "https://aka.ms/appsvc/appservice_kube-latest-py2.py3-none-any.whl"
```