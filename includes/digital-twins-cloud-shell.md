---
author: baanders
description: Fichier include pour Azure Digital Twins - Configurer Cloud Shell et l’extension IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b664303d86f8588fc210b11b363b21d523d63295
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87507442"
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

[!INCLUDE [digital-twins-cloud-shell-extensions.md](digital-twins-cloud-shell-extensions.md)]

Vous êtes maintenant prêt à utiliser Azure Digital Twins dans Cloud Shell.

Vous pouvez le vérifier en exécutant `az dt -h` à tout moment pour voir la liste des commandes Azure Digital Twins de niveau supérieur disponibles.