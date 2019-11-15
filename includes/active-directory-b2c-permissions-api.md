---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: ce49e9e26160017d64d745c9c7ad5402bc3ae2ca
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641550"
---
#### <a name="applicationstabapplications"></a>[Applications](#tab/applications/)

1. Sélectionnez **Applications**, puis sélectionnez l’application web qui doit avoir accès à l’API. Par exemple, *webapp1*.
1. Sélectionnez **Accès aux API**, puis **Ajouter**.
1. Dans la liste déroulante **Sélectionner une API**, sélectionnez l’API à laquelle l’application web doit être autorisée à accéder. Par exemple, *webapi1*.
1. Dans la liste déroulante **Sélectionner des étendues**, sélectionnez les étendues que vous avez définies précédemment. Par exemple, *demo.read* et *demo.write*.
1. Sélectionnez **OK**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Inscriptions d’applications (préversion)](#tab/app-reg-preview/)

1. Sélectionnez **Inscriptions d’applications (préversion)** , puis sélectionnez l’application web qui doit avoir accès à l’API. Par exemple, *webapp1*.
1. Sous **Gérer**, sélectionnez **Autorisations de l’API**.
1. Sous **Autorisations configurées**, sélectionnez **Ajouter une autorisation**.
1. Sélectionnez l’onglet **Mes API**.
1. Sélectionnez l’API à laquelle l’application web doit être autorisée à accéder. Par exemple, *webapi1*.
1. Sous **Autorisation**, développez **demo**, puis sélectionnez les étendues que vous avez définies auparavant. Par exemple, *demo.read* et *demo.write*.
1. Sélectionnez **Ajouter des autorisations**. Comme vous l’indiquent les instructions, patientez quelques minutes avant de passer à l’étape suivante.
1. Sélectionnez **Accorder le consentement administrateur pour (nom de votre locataire)** .
1. Sélectionnez le compte administrateur actuellement connecté ou connectez-vous avec un compte de votre locataire Azure AD B2C qui possède au minimum le rôle *Administrateur d’application cloud*.
1. Sélectionnez **Accepter**.
1. Sélectionnez **Actualiser**, puis vérifiez que la mention « Accordé pour ... » apparaît dans **État** pour les deux étendues. La propagation des autorisations peut prendre quelques minutes.