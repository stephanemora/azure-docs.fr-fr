---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: bc3417f0ce0fae54c86c66bf3e55fd183d497c21
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227773"
---
Pour accorder des autorisations à votre application (**ID d’application : 1**), procédez comme suit : 

1. Sélectionnez **Inscriptions d’applications**, puis l’application que vous avez créée (**ID d’application : 1**).
1. Sous **Gérer**, sélectionnez **Autorisations de l’API**.
1. Sous **Autorisations configurées**, sélectionnez **Ajouter une autorisation**.
1. Sélectionnez l’onglet **Mes API**.
1. Sélectionnez l’API (**ID d’application : 2**) à laquelle l’application web doit être autorisée à accéder. Par exemple, saisissez **my-api1**.
1. Sous **Autorisation**, développez **tâches**, puis sélectionnez les étendues que vous avez définies auparavant (par exemple, **tasks.read** et **tasks.write**).
1. Sélectionnez **Ajouter des autorisations**.
1. Sélectionnez **Accorder le consentement administrateur pour \<*your tenant name*>** .
1. Sélectionnez **Oui**.
1. Sélectionnez **Actualiser**, puis vérifiez que la mention **Accordé pour ...** apparaît sous **État** pour les deux étendues.
1. Dans la liste **Autorisations configurées**, sélectionnez votre étendue, puis copiez le nom complet de celle-ci. 

    ![Capture d’écran du volet des autorisations configurées, qui affiche les autorisations d’accès en lecture accordées.](./media/active-directory-b2c-app-integration-grant-permissions/get-azure-ad-b2c-app-permissions.png)  