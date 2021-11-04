---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.openlocfilehash: 549104e9a0721d8edb58ad57cede0e4790cad4cf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455856"
---
## <a name="add-azure-cli-extensions"></a>Ajouter des extensions Azure CLI

Lancez l’environnement Bash dans [Azure Cloud Shell](../articles/cloud-shell/quickstart.md).

[![Lancer Cloud Shell dans une nouvelle fenêtre](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com)

Comme ces commandes CLI ne font pas encore partie de l’ensemble principal de l’interface CLI, ajoutez-les avec les commandes suivantes :

```azurecli-interactive
az extension add --upgrade --yes --name customlocation
az extension remove --name appservice-kube
az extension add --upgrade --yes --name appservice-kube
```