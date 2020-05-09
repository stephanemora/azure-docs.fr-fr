---
title: Fichier include
description: Fichier include
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: fc5f4d2c10cac23600025a72fedf7fe2cec5a34e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684070"
---
Pour lire des données dans le stockage archive, vous devez d’abord attribuer un niveau chaud ou froid à l’objet blob. Ce processus est appelé « réalimentation » et peut prendre des heures. Nous recommandons des tailles d’objet blob importantes pour obtenir des performances de réalimentation optimales. La réalimentation simultanée de plusieurs petits objets blob peut demander du temps supplémentaire. Il existe actuellement deux priorités de réalimentation, haute et standard, qui peuvent être définies via la propriété facultative *x-ms-rehydrate-priority* sur une opération [Définir un niveau d’objet blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) ou [Copier l’objet blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob).

* **Priorité standard** : La requête de réalimentation est traitée dans l’ordre dans lequel elle a été reçue et peut prendre jusqu’à 15 heures.
* **Haute priorité** : La requête de réalimentation est prioritaire par rapport aux requêtes standard et peut se terminer en moins de 1 heure. La haute priorité peut durer plus de 1 heure en fonction de la taille de l’objet blob et de la requête actuelle. Les requêtes haute priorité sont assurées d’être prioritaires sur les requêtes de priorité standard.

> [!NOTE]
> La priorité standard est l’option de réalimentation par défaut pour les archives. La haute priorité est une option plus rapide plus coûteuse que la réalimentation de priorité standard et est généralement réservée à une utilisation dans des situations de restauration de données urgentes.

Une fois qu’une requête de réalimentation est lancée, elle ne peut pas être annulée. Pendant le processus de réalimentation, la propriété blob *x-ms-access-tier* continue à s’afficher en tant qu’archive jusqu’à ce que la réalimentation soit terminée sur un niveau en ligne. Pour confirmer l’état et la progression de la réalimentation, vous pouvez appeler [Get Blob Properties](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) pour vérifier les propriétés blob *x-ms-archive-status* et *x-ms-rehydrate-priority*. L’état de l’archive peut afficher « réalimentation-vers-chaud » ou « réalimentation-vers-froid » selon le niveau de réalimentation choisi. La priorité de réalimentation indique la vitesse « haute » ou « standard ». Une fois l’opération terminée, les propriétés État de l’archive et Priorité de réalimentation sont supprimées, et la propriété blob du niveau d’accès est mise à jour pour refléter le niveau chaud ou froid sélectionné.
