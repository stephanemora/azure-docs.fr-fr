---
title: Fichier Include
description: Fichier Include
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0946b703b45f69ede409167213f2629a796f18b5
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869625"
---
## <a name="enable-event-grid-resource-provider"></a>Activer le fournisseur de ressources Event Grid

Si vous n’avez jamais utilisé Event Grid dans votre abonnement Azure, vous devrez peut-être inscrire le fournisseur de ressources Event Grid. Exécutez la commande suivante :

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.EventGrid
```

L’inscription peut prendre un certain temps. Pour vérifier l’état, exécutez :

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Lorsque `RegistrationStatus` est `Registered`, vous êtes prêt à continuer.