---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: 74c9ea66cfe94a8c856f1a73e968b5f4ea5bb049
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227636"
---
1. Sélectionnez l’application **my-api1** que vous avez créée (**ID d’application : 2**) pour ouvrir sa page **Vue d’ensemble**.

1. Sous **Gérer**, sélectionnez **Exposer une API**.
1. A côté d’**URI d’ID d’application**, sélectionnez le lien **Définir**. Remplacez la valeur par défaut (GUID) par un nom unique (par exemple, **tasks-api**), puis sélectionnez **Enregistrer**. 
 
   Lorsque votre application web demande un jeton d’accès pour l’API web, elle doit ajouter cet URI en tant que préfixe de chaque étendue que vous définissez pour l’API.
1. Sous **Étendues définies par cette API**, sélectionnez **Ajouter une étendue**.
1. Pour créer une étendue qui définit l’accès en lecture à l’API :

    1. Pour **Nom de l’étendue**, entrez **tâches.lecture**.  
    1. Pour **Nom d’affichage du consentement administrateur**, entrez **Accès en lecture à l’API de tâches**.  
    1. Pour **Description du consentement administrateur**, entrez **Autorise l’accès en lecture à l’API de tâches**.

1. Sélectionnez **Ajouter une étendue**.

1. Sélectionnez **Ajouter une étendue**, puis ajoutez une étendue qui définit l’accès en écriture à l’API : 

    1. Pour **Nom de l’étendue**, entrez **tâches.écriture**.  
    1. Pour **Nom d’affichage du consentement administrateur**, entrez **Accès en écriture à l’API de tâches**.
    1. Pour **Description du consentement administrateur**, entrez **Autorise l’accès en écriture à l’API de tâches**.
    
1. Sélectionnez **Ajouter une étendue**.
