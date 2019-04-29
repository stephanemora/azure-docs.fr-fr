---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754246"
---
Votre appareil est associé à un compte de stockage qui est utilisé en tant que destination pour vos données dans Azure. Accès au compte de stockage est contrôlé par l’abonnement et de stockage de 512 bits deux associés à ce compte de stockage de clés d’accès.

Une des clés est utilisée pour l’authentification lors de l’appareil de périphérie de zone de données accède au compte de stockage. L’autre clé est gardée en réserve, donc vous pouvez faire pivoter les clés périodiquement.

Pour des raisons de sécurité, nombreux centres de données nécessitent la rotation des clés. Nous vous recommandons de suivre ces méthodes recommandées pour la rotation des clés :

- Votre clé de compte de stockage est similaire au mot de passe racine pour votre compte de stockage. Protégez soigneusement votre clé de compte. Ne pas distribuer à d’autres utilisateurs, le mot de passe, dur coder ou enregistrer n’importe où dans le texte brut qui est accessible à d’autres personnes.
- [Régénérez votre clé de compte](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) via Azure portal si vous pensez qu’il pourrait être compromise.
- Votre administrateur Azure doit régulièrement modifier ou régénérer la clé primaire ou secondaire à l’aide de la section stockage du portail Azure pour accéder au compte de stockage directement.