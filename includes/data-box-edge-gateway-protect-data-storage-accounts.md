---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 23587e617c62cfe4aa24256330c5f7673dd1c674
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684419"
---
Votre appareil est associé à un compte de stockage qui est utilisé en tant que destination pour vos données dans Azure. Accès au compte de stockage est contrôlé par l’abonnement et de stockage de 512 bits deux associés à ce compte de stockage de clés d’accès.

Une des clés est utilisée pour l’authentification lors de l’appareil de périphérie de zone de données accède au compte de stockage. L’autre clé est gardée en réserve, ce qui permet une rotation régulière des clés.

Pour des raisons de sécurité, nombreux centres de données nécessitent la rotation des clés. Nous vous recommandons de suivre ces méthodes recommandées pour la rotation des clés :

- Votre clé de compte de stockage est similaire au mot de passe racine pour votre compte de stockage. Protégez soigneusement votre clé de compte. Ne pas distribuer le mot de passe à d’autres utilisateurs, dur coder ou enregistrer n’importe où dans le texte en clair est accessible à d’autres personnes.
- [Régénérez votre clé de compte](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) à l’aide du portail Azure si vous pensez qu’il a peut-être été compromise.
- Votre administrateur Azure doit changer ou régénérer régulièrement la clé primaire ou secondaire à l’aide de la section Stockage du portail Azure pour accéder directement au compte de stockage.