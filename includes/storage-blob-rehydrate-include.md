---
title: Fichier Include
description: Fichier Include
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 81ffc87ce97a936e693c59bca6caf721cb8599cf
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780236"
---
Pour lire des données dans le stockage archive, vous devez d’abord attribuer un niveau chaud ou froid à l’objet blob. Ce processus est appelé « réalimentation » et peut prendre des heures. Nous recommandons des tailles d’objet blob importantes pour obtenir des performances de réalimentation optimales. La réalimentation simultanée de plusieurs petits objets blob peut demander du temps supplémentaire. Il existe actuellement deux priorités de réalimentation, haute (préversion) et standard, qui peuvent être définies via la propriété facultative *x-ms-rehydrate-priority* sur une opération [Définir un niveau d’objet blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) ou [Copier l’objet blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob).

* **Priorité standard** : La requête de réalimentation est traitée dans l’ordre dans lequel elle a été reçue et peut prendre jusqu’à 15 heures.
* **Haute priorité (préversion)** : La requête de réalimentation est prioritaire par rapport aux requêtes standard et peut se terminer en moins de 1 heure. La haute priorité peut durer plus de 1 heure en fonction de la taille de l’objet blob et de la requête actuelle. Les requêtes haute priorité sont assurées d’être prioritaires sur les requêtes de priorité standard.

> [!NOTE]
> La priorité standard est l’option de réalimentation par défaut pour les archives. La haute priorité est une option plus rapide plus coûteuse que la réalimentation de priorité standard et est généralement réservée à une utilisation dans des situations de restauration de données urgentes.

Une fois qu’une requête de réalimentation est lancée, elle ne peut pas être annulée. Pendant la réalimentation, vous pouvez consulter la propriété *État archive* de l’objet blob pour vous assurer que le niveau a changé. L’état affiche « réalimentation-vers-chaud » ou « réalimentation-vers-froid » selon le niveau choisi. Une fois le processus terminé, la propriété d’état archive est supprimée, et la propriété *Niveau d’accès* de l’objet blob indique le niveau chaud ou froid.
