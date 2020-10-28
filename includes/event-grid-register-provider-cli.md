---
title: Fichier Include
description: Fichier Include
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 6c5ab187eb1307a95bbae4ff39d387221e094ea5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736868"
---
## <a name="enable-the-event-grid-resource-provider"></a>Activer le fournisseur de ressources Event Grid

Si vous n’avez jamais utilisé Event Grid dans votre abonnement Azure, vous risquez de devoir inscrire le fournisseur de ressources Event Grid. Exécutez la commande suivante pour enregistrer le fournisseur :

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

L’inscription peut prendre un certain temps. Pour vérifier l’état, exécutez :

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Lorsque `registrationState` est `Registered`, vous êtes prêt à continuer.
