---
author: baanders
description: Fichier include pour Azure Digital Twins - Configurer Cloud Shell et l’extension IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 76480959d94b9e2a2e04857c85b7359105e73e4f
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84611472"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Configurer une session Cloud Shell

Après avoir ouvert une fenêtre Cloud Shell, la première chose à faire est de se connecter et de définir votre abonnement comme contexte d’interpréteur de commandes pour cette session. 

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Si vous utilisez cet abonnement avec Azure Digital Twins pour la première fois, exécutez cette commande pour vous inscrire auprès de l’espace de noms Azure Digital Twins. (En cas de doute, il est possible de la réexécuter même si vous l’avez fait par le passé.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Exécutez ensuite la commande suivante dans votre instance Cloud Shell afin d’ajouter l’extension Microsoft Azure IoT pour Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

> [!NOTE]
> Cet article utilise la version la plus récente de l’extension Azure IoT, appelée `azure-iot`. La version héritée est appelée `azure-iot-cli-ext`. Une seule version doit être installée à la fois. Vous pouvez utiliser la commande `az extension list` pour valider les extensions actuellement installées.
> Utilisez `az extension remove --name azure-cli-iot-ext` pour supprimer la version héritée de l’extension.
> Utilisez `az extension add --name azure-iot` pour ajouter la nouvelle version de l’extension. Pour voir les extensions que vous avez installées, utilisez `az extension list`.

> [!TIP]
> Vous pouvez exécuter `az dt -h` pour voir les commandes Azure Digital Twins de niveau supérieur.