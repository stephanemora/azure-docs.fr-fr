---
title: Fichier include
description: Fichier include
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: a2f5264db1f95bcea524a87a61735cf730af23ba
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645452"
---
## <a name="enable-event-grid-resource-provider"></a>Activer le fournisseur de ressources Event Grid

Si vous n’avez jamais utilisé Event Grid dans votre abonnement Azure, vous devrez peut-être inscrire le fournisseur de ressources Event Grid. Exécutez la commande suivante :

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

L’inscription peut prendre un certain temps. Pour vérifier l’état, exécutez :

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Lorsque `RegistrationStatus` est `Registered`, vous êtes prêt à continuer.
