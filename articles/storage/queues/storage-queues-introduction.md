---
title: Présentation du Stockage File d’attente Azure | Microsoft Docs
description: Présentation du Stockage File d’attente Azure
services: storage
author: tamram
ms.service: storage
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: b173934db17b8c3ac5a48e599b75478fb214c240
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458395"
---
# <a name="introduction-to-queues"></a>Présentation des files d’attente

Les files d’attente de stockage Azure sont un service permettant de stocker un grand nombre de messages accessibles depuis n’importe où dans le monde via des appels authentifiés avec HTTP ou HTTPS. Un simple message de file d’attente peut avoir une taille de 64 Ko et une file d’attente peut contenir des millions de messages, jusqu’à la limite de capacité totale d’un compte de stockage.

## <a name="common-uses"></a>Utilisations courantes

Voici quelques utilisations courantes des files d’attente de stockage :

* Création d'un journal des travaux en souffrance de travail à traiter de manière asynchrone
* Transmission de messages d’un rôle web Azure à un rôle de travail Azure

## <a name="queue-service-concepts"></a>Concepts du service File d’attente

Le service de file d’attente contient les composants suivants :

![Concepts de File d’attente](./media/storage-queues-introduction/queue1.png)

* **Format d’URL** : Les files d’attente sont adressables à l’aide du format d’URL suivant :   
    https://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    L'URL suivante désigne une file d'attente du schéma :  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Compte de stockage :** Tous les accès à Azure Storage passent par un compte de stockage. Pour plus d’informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité du stockage Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) .

* **File d’attente :** Une file d’attente contient un ensemble de messages. Tous les messages doivent être dans une file d’attente. Notez que le nom de la file d’attente doit être en minuscules. Pour plus d'informations sur l’affectation de noms à des files d’attente, consultez [Affectation de noms pour les files d'attente et les métadonnées](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **Message :** Message dans n’importe quel format d’une taille maximale de 64 Ko. La durée maximale pendant laquelle un message peut rester dans la file d’attente est de sept jours.

## <a name="next-steps"></a>Étapes suivantes

* [Créez un compte de stockage](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Bien démarrer avec les files d’attente en utilisant .NET](storage-dotnet-how-to-use-queues.md)
