---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: 6b4ee09212bc62f43c583c73299ac33a842dc942
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524055"
---
1. Sélectionnez l’application *my-api1* que vous avez créée (ID d’application : 2) pour ouvrir sa page **Vue d’ensemble**.

1. Sous **Gérer**, sélectionnez **Exposer une API**.
1. A côté d’**URI d’ID d’application**, sélectionnez le lien **Définir**. Remplacez la valeur par défaut (GUID) par un nom unique (par exemple, *tasks-api*), puis sélectionnez **Enregistrer**. 
 
   Lorsque votre application web demande un jeton d’accès pour l’API web, elle doit ajouter cet URI en tant que préfixe de chaque étendue que vous définissez pour l’API.
1. Sous **Étendues définies par cette API**, sélectionnez **Ajouter une étendue**.
1. Pour créer une étendue qui définit l’accès en lecture à l’API, entrez les valeurs suivantes :

    a. **Nom de l’étendue** : *tasks.read*  
    b. **Nom d’affichage du consentement administrateur** : *accès en lecture à l’API de tâches*  
    c. **Description du consentement administrateur** : *autorise l’accès en lecture à l’API de tâches*

1. Sélectionnez **Ajouter une étendue**.

1. Sélectionnez **Ajouter une étendue**, puis ajoutez une étendue qui définit l’accès en écriture à l’API en entrant les valeurs suivantes : 

    a. **Nom de l’étendue** : *tasks.write*  
    b. **Nom d’affichage du consentement administrateur** : *accès en écriture à l’API de tâches*  
    c. **Description du consentement administrateur** : *autorise l’accès en écriture à l’API de tâches*
1. Sélectionnez **Ajouter une étendue**.