---
title: Présentation de Files d’attente Azure - Stockage Azure
description: Consultez une introduction aux Files d’attente Azure, un service permettant de stocker un grand nombre de messages. Un service de file d’attente contient un format d’URL, un compte de stockage, une file d’attente et un message.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: dineshm
ms.openlocfilehash: f618b7203cdbf99d79079cf81de38c6d15c91981
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345565"
---
# <a name="what-are-azure-queues"></a>Que sont les files d’attente Azure ?

Stockage File d’attente Azure est un service permettant de stocker un grand nombre de messages. Vous accédez aux messages depuis n’importe où dans le monde par le biais d’appels authentifiés à l’aide du protocole HTTP ou HTTPS. La taille maximale d’un message de file d’attente est de 64 Ko. Une file d’attente peut contenir des millions de messages, dans la limite de la capacité totale d’un compte de stockage. Les files d’attente sont couramment utilisées pour créer un backlog de travail à traiter de façon asynchrone.

## <a name="queue-service-concepts"></a>Concepts du service File d’attente

Le service de file d’attente contient les composants suivants :

![Diagramme montrant la relation qui existe entre un compte de stockage, les files d’attente et les messages.](./media/storage-queues-introduction/queue1.png)

- **Format d’URL :** les files d’attente sont adressables à l’aide du format d’URL suivant :

  `https://<storage account>.queue.core.windows.net/<queue>`

  L'URL suivante désigne une file d'attente du schéma :

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Compte de stockage :** Tous les accès à Azure Storage passent par un compte de stockage. Pour plus d’informations sur la capacité du compte de stockage, consultez [Cibles de scalabilité et de performances pour les comptes de stockage standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

- **File d’attente :** une file d’attente contient un ensemble de messages. Le nom de la file d’attente **doit** être en minuscules. Pour plus d'informations sur l’affectation de noms à des files d’attente, consultez [Affectation de noms pour les files d'attente et les métadonnées](/rest/api/storageservices/Naming-Queues-and-Metadata).

- **Message :** message dans n’importe quel format d’une taille maximale de 64 Ko. Avant la version du 29 juillet 2017, la durée de vie maximale autorisée est de sept jours. Pour les versions du 29 juillet 2017 ou ultérieures, la durée de vie maximale peut être n’importe quel nombre positif, ou -1 indiquant que le message n’expire pas. Si ce paramètre est omis, la valeur par défaut de la durée de vie est de sept jours.

## <a name="next-steps"></a>Étapes suivantes

- [Créez un compte de stockage](../common/storage-account-create.md?toc=%252fazure%252fstorage%252fqueues%252ftoc.json)
- [Bien démarrer avec les files d’attente en utilisant .NET](storage-dotnet-how-to-use-queues.md)
