---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: cd6bddb6988555b336cb920c481fe675e9f7a276
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073001"
---
1. Sélectionnez l’application *my-api1* que vous avez créée pour ouvrir sa page **Vue d’ensemble**.
1. Sous **Gérer**, sélectionnez **Exposer une API**.
1. A côté d’**URI d’ID d’application**, sélectionnez le lien **Définir**. Remplacez la valeur par défaut (un GUID) par un nom unique, par exemple `tasks-api`, puis sélectionnez **Enregistrer**. Lorsque votre application web demande un jeton d’accès pour l’API web, elle doit ajouter cet URI en tant que préfixe de chaque étendue que vous définissez pour l’API.
1. Sous **Étendues définies par cette API**, sélectionnez **Ajouter une étendue**.
1. Entrez les valeurs suivantes pour créer une étendue qui définit l’accès en lecture à l’API, puis sélectionnez **Ajouter une étendue** :
    1. **Nom de l’étendue** : `tasks.read`
    1. **Nom d’affichage du consentement administrateur** : `Read access to tasks API`
    1. **Description du consentement de l’administrateur** : `Allows read access to the tasks API`
1. Sélectionnez **Ajouter une étendue**, entrez les valeurs suivantes pour ajouter une étendue qui définit l’accès en écriture à l’API, puis sélectionnez **Ajouter une étendue** :
    1. **Nom de l’étendue** : `tasks.write`
    1. **Nom d’affichage du consentement administrateur** : `Write access to tasks API`
    1. **Description du consentement de l’administrateur** : `Allows write access to the tasks API`