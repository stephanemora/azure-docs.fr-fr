---
title: Fichier Include
description: Fichier Include
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b48e5e28b15d17796138d493a7d9c628791df2e6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87830121"
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