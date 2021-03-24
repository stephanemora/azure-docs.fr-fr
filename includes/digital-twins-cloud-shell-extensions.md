---
author: baanders
description: Fichier include pour Azure Digital Twins – Configurer la dernière extension IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 984739a728f6ac5e28eeb561e0d7b6ec0485ca13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90606235"
---
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