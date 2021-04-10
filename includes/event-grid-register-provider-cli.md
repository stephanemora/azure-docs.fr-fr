---
title: Fichier include
description: Fichier Include
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd778da5fd53cb8744a9f267384fcc8e11941582
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645405"
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
