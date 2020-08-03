---
author: baanders
description: Fichier include pour Azure Digital Twins - Configurer Cloud Shell et l’extension IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b7c91d648c06970d53799c6ff505919dea17b3c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032207"
---
Pour commencer à utiliser Azure Digital Twins dans une fenêtre [Azure Cloud Shell](https://shell.azure.com) ouverte, la première chose à faire est de se connecter et de définir votre abonnement comme contexte d’interpréteur de commandes pour cette session. Dans votre instance Cloud Shell, exécutez ces commandes :

```azurecli
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> Vous pouvez également utiliser le nom de votre abonnement à la place de l’ID dans la commande ci-dessus. 

Si vous utilisez cet abonnement avec Azure Digital Twins pour la première fois, exécutez cette commande pour vous inscrire auprès de l’espace de noms Azure Digital Twins. (En cas de doute, il est possible de la réexécuter même si vous l’avez fait par le passé.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Ensuite, vous allez ajouter l’[**extension Microsoft Azure IoT pour Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) à votre instance Cloud Shell, afin d’activer les commandes permettant d’interagir avec Azure Digital Twins et d’autres services IoT. 

Tout d’abord, exécutez cette commande pour voir la liste de toutes les extensions que vous avez déjà installées.

```azurecli-interactive
az extension list
```

La sortie est un tableau de toutes les extensions dont vous disposez actuellement. Recherchez le champ `"name"` pour chaque entrée de liste afin de voir les noms des extensions.

Utilisez la sortie pour déterminer lesquelles des commandes suivantes doivent être exécutées pour la configuration de l’extension (vous pouvez en exécuter plusieurs).
* Si la liste contient `azure-iot` : vous disposez déjà de l’extension. Exécutez cette commande pour vérifier que vous disposez de la dernière mise à jour et qu’il n’y a plus de mises à jour disponibles :

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Si la liste ne contient **pas** `azure-iot` : Vous devez installer l’extension. Utilisez la commande suivante :

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Si la liste contient `azure-iot-cli-ext` : il s’agit de la version héritée de l’extension. Une seule version de l’extension doit être installée à la fois. Vous devez donc désinstaller l’extension héritée. Utilisez la commande suivante :

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

Vous êtes maintenant prêt à utiliser Azure Digital Twins dans Cloud Shell.

Vous pouvez le vérifier en exécutant `az dt -h` à tout moment pour voir la liste des commandes Azure Digital Twins de niveau supérieur disponibles.