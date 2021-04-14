---
author: baanders
description: Fichier include pour Azure Digital Twins - Configurer Cloud Shell et l’extension IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: d4d9efd99a60c93dbfef2d6f45971781d71e83fb
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105055"
---
Pour commencer à utiliser Azure Digital Twins dans une fenêtre [Azure Cloud Shell](https://shell.azure.com) ouverte, la première chose à faire est de se connecter et de définir votre abonnement comme contexte d’interpréteur de commandes pour cette session. Dans votre instance Cloud Shell, exécutez ces commandes :

```azurecli-interactive
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> Vous pouvez également utiliser le nom de votre abonnement à la place de l’ID dans la commande ci-dessus. 

Si vous utilisez cet abonnement avec Azure Digital Twins pour la première fois, exécutez cette commande pour vous inscrire auprès de l’espace de noms Azure Digital Twins. (En cas de doute, il est possible de la réexécuter même si vous l’avez fait par le passé.)

```azurecli-interactive
az provider register --namespace 'Microsoft.DigitalTwins'
```

Ensuite, vous allez ajouter l’[**extension Microsoft Azure IoT pour Azure CLI**](/cli/azure/service-page/azure%20iot) à votre instance Cloud Shell, afin d’activer les commandes permettant d’interagir avec Azure Digital Twins et d’autres services IoT. Exécutez cette commande pour vous assurer de disposer de la version la plus récente de l’extension :

```azurecli-interactive
az extension add --upgrade -n azure-iot
```

Vous êtes maintenant prêt à utiliser Azure Digital Twins dans Cloud Shell.

Vous pouvez le vérifier en exécutant `az dt -h` à tout moment pour voir la liste des commandes Azure Digital Twins de niveau supérieur disponibles.