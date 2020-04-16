---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: cea3245176e6c38137d68e3ad4b47477bedc78be
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529183"
---
Pour inscrire une application dans votre locataire Azure AD B2C, vous pouvez utiliser l’expérience **Applications** actuelle ou notre nouvelle expérience unifiée **Inscriptions d’applications (préversion)** . [En savoir plus sur la nouvelle expérience](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Applications](#tab/applications/)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Applications**, puis **Ajouter**.
1. Entrez un nom pour l’application. Par exemple, *ROPC_Auth_app*.
1. Pour **Client natif**, sélectionnez **Oui**.
1. Laissez les autres valeurs telles quelles, puis sélectionnez **Créer**.
1. Enregistrez l’**ID D’APPLICATION** pour l’utiliser dans une étape ultérieure.

#### <a name="app-registrations-preview"></a>[Inscriptions d’applications (préversion)](#tab/app-reg-preview/)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications (préversion)** , puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, *ROPC_Auth_app*.
1. Laissez les autres valeurs telles quelles, puis sélectionnez **Inscrire**.
1. Enregistrez l’**ID d’application (client)** pour l’utiliser dans une étape ultérieure.
1. Sous **Gérer**, sélectionnez **Authentification**.
1. Sélectionnez **Essayer la nouvelle expérience** (si elle est indiquée).
1. Sous **Type de client par défaut**, sélectionnez **Oui** pour considérer l’application comme un client public. Ce paramètre est obligatoire pour le flux ROPC.
1. Sélectionnez **Enregistrer**.
1. Dans le menu de gauche, sélectionnez **Manifeste** pour ouvrir l’éditeur de manifeste. 
1. Définissez l’attribut **oauth2AllowImplicitFlow** sur *true* :
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Sélectionnez **Enregistrer**.