---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 92f638666d9ac832ee5e6a7d4dccf9a9e669f908
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72427970"
---
## <a name="what-is-queue-storage"></a>Présentation du stockage File d’attente

Les files d’attente de stockage Azure sont un service permettant de stocker un grand nombre de messages accessibles depuis n’importe où dans le monde via des appels authentifiés avec HTTP ou HTTPS. Un simple message de file d’attente peut avoir une taille de 64 Ko et une file d’attente peut contenir des millions de messages, jusqu’à la limite de capacité totale d’un compte de stockage. Le stockage File d’attente est couramment utilisé pour créer un backlog de travail à traiter de façon asynchrone.

## <a name="queue-service-concepts"></a>Concepts du service File d’attente

Le service de File d’attente Azure contient les composants suivants :

![Composants du service File d’attente Azure](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Format d’URL** : Les files d’attente sont adressables à l’aide du format d’URL suivant :   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    L'URL suivante désigne une file d'attente du schéma :  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Compte de stockage :** Tous les accès à Azure Storage passent par un compte de stockage. Pour plus d’informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité du stockage Azure](../articles/storage/common/storage-scalability-targets.md) .
* **File d’attente :** Une file d’attente contient un ensemble de messages. Tous les messages doivent être dans une file d’attente. Notez que le nom de la file d’attente doit être en minuscules. Pour plus d'informations sur l’affectation de noms à des files d’attente, consultez [Affectation de noms pour les files d'attente et les métadonnées](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Message :** Message dans n’importe quel format d’une taille maximale de 64 Ko. La durée maximale pendant laquelle un message peut rester dans la file d’attente est de 7 jours.

