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
ms.openlocfilehash: ace22a6896a121f5cd8af838c7b0e427bd0287dc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "67176745"
---
## <a name="enable-event-grid-resource-provider"></a>Activer le fournisseur de ressources Event Grid

Si vous n’avez jamais utilisé Event Grid dans votre abonnement Azure, vous devrez peut-être inscrire le fournisseur de ressources Event Grid. Exécutez la commande suivante pour enregistrer le fournisseur :

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

L’inscription peut prendre un certain temps. Pour vérifier l’état, exécutez :

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Lorsque `registrationState` est `Registered`, vous êtes prêt à continuer.