---
author: baanders
description: Fichier include pour Azure Digital Twins - Configurer Cloud Shell et l’extension IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 6f472865c131b873f1ae0a21fa9ec55865fb2b29
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277745"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Configurer une session Cloud Shell

Après avoir ouvert une fenêtre Cloud Shell, la première chose à faire est de se connecter et de définir votre abonnement comme contexte d’interpréteur de commandes pour cette session. Dans votre instance Cloud Shell, exécutez ces commandes :

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```
> [!TIP]
> Vous pouvez également définir votre abonnement à l’aide de votre nom d’abonnement. Utilisez la commande suivante : 
> ```azurecli
> az account set --subscription "your-Azure-subscription-name"
> 
Si vous utilisez cet abonnement avec Azure Digital Twins pour la première fois, exécutez cette commande pour vous inscrire auprès de l’espace de noms Azure Digital Twins. (En cas de doute, il est possible de la réexécuter même si vous l’avez fait par le passé.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Ensuite, vous allez ajouter l’[**extension Microsoft Azure IoT pour Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) à votre instance Cloud Shell, afin d’activer les commandes permettant d’interagir avec Azure Digital Twins et d’autres services IoT. 

Tout d’abord, exécutez cette commande pour voir la liste de toutes les extensions que vous avez déjà installées.

```azurecli-interactive
az extension list
```

Dans la sortie, recherchez le champ `"name"` pour chaque entrée de liste afin de voir les noms des extensions.

Utilisez la sortie pour déterminer lesquelles des commandes suivantes doivent être exécutées pour la configuration de l’extension (vous pouvez en exécuter plusieurs).
* Si la liste contient `azure-iot` : vous disposez déjà de l’extension. Exécutez cette commande pour vérifier que vous disposez de la dernière mise à jour :

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