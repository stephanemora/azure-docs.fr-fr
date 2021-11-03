---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: kengaderdus
ms.openlocfilehash: eba3d57bab13cf573cd6e4478c0df672e3b34458
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007580"
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