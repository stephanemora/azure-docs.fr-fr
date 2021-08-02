---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 6d62dda341c6acb783bd2525c2f6ba7a76b312ec
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072997"
---
1. Sous **Gérer**, sélectionnez **Autorisations de l’API**.
1. Sous **Autorisations configurées**, sélectionnez **Ajouter une autorisation**.
1. Sélectionnez l’onglet **Mes API**.
1. Sélectionnez l’API à laquelle l’application web doit être autorisée à accéder. Par exemple, *my-api1*.
1. Sous **Autorisation**, développez **tâches**, puis sélectionnez les étendues que vous avez définies auparavant. Par exemple, *tasks.read* et *tasks.write*.
1. Sélectionnez **Ajouter des autorisations**.
1. Sélectionnez **Accorder le consentement de l’administrateur pour (nom de votre abonné)** .
1. Sélectionnez **Oui**.
1. Sélectionnez **Actualiser**, puis vérifiez que la mention « Accordé pour ... » apparaît dans **État** pour les deux étendues.
1. Dans la liste des **autorisations configurées**, sélectionnez votre étendue et copiez le nom complet de l’étendue. 

    ![Obtenir l’étendue de votre application web](./media/active-directory-b2c-app-integration-grant-permissions/get-azure-ad-b2c-app-permissions.png)  