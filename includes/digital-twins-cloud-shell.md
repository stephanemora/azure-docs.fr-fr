---
author: baanders
description: Fichier include pour Azure Digital Twins - Configurer Cloud Shell et l’extension IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 4aa016294f0ef3bd26f7f3ef6fa374e9367b672d
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296965"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Configurer une session Cloud Shell

Après avoir ouvert une fenêtre Cloud Shell, la première chose à faire est de se connecter et de définir votre abonnement comme contexte d’interpréteur de commandes pour cette session. Dans votre instance Cloud Shell, exécutez ces commandes :

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Si vous utilisez cet abonnement avec Azure Digital Twins pour la première fois, exécutez cette commande pour vous inscrire auprès de l’espace de noms Azure Digital Twins. (En cas de doute, il est possible de la réexécuter même si vous l’avez fait par le passé.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Ensuite, vous allez ajouter l’[**extension Microsoft Azure IoT pour Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) à votre instance Cloud Shell, afin d’activer les commandes permettant d’interagir avec Azure Digital Twins et d’autres services IoT. Utilisez cette commande pour ajouter l’extension :

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

Si vous aviez installé l’extension, la sortie peut indiquer que « l’extension 'azure-iot' est déjà installée ». Dans ce cas, exécutez la commande suivante pour vérifier que vous disposez de la dernière mise à jour : 

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

Vous êtes maintenant prêt à utiliser Azure Digital Twins dans Cloud Shell.

Vous pouvez le vérifier en exécutant `az dt -h` à tout moment pour voir la liste des commandes Azure Digital Twins de niveau supérieur disponibles.