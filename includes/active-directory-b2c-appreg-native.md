---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 0b5c5fdddeea961858a2c3b8cd69c365bb28f26e
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799811"
---
Pour inscrire une application dans votre abonné Azure AD B2C, vous pouvez utiliser l’expérience **Applications** actuelle ou notre nouvelle expérience unifiée **Inscriptions d’applications (préversion)** . [En savoir plus sur la nouvelle expérience](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Applications](#tab/applications/)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Applications**, puis **Ajouter**.
1. Entrez un nom pour l’application. Par exemple, *nativeapp1*.
1. Pour **Client natif**, sélectionnez **Oui**.
1. Entrez un **URI de redirection personnalisé** avec un schéma unique. Par exemple : `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Deux points importants sont à prendre en compte quand vous choisissez un URI de redirection :
    * **Unique** : le schéma de l'URI de redirection doit être propre à chaque application. Dans l’exemple `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` est le schéma. Ce modèle doit être suivi. Si deux applications partagent le même schéma, l’utilisateur doit choisir une application. Si l'utilisateur fait le mauvais choix, la connexion échoue.
    * **Complet** : l'URI de redirection doit être doté d'un schéma et d'un chemin. Le chemin d’accès doit contenir au moins une barre oblique après le domaine. Par exemple, `//oauth/` fonctionne tandis que `//oauth` échoue. N'incluez pas de caractères spéciaux, tels que des traits de soulignement, dans l'URI.
1. Sélectionnez **Create** (Créer).

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Inscriptions d’applications (préversion)](#tab/app-reg-preview/)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications (préversion)** , puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, *nativeapp1*.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un répertoire organisationnel ou un fournisseur d'identité**.
1. Sous **URI de redirection**, utilisez la liste déroulante pour sélectionner **client public/natif (Bureau et mobile)** .
1. Entrez un URI de redirection avec un schéma unique. Par exemple : `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Deux points importants sont à prendre en compte quand vous choisissez un URI de redirection :
    * **Unique** : le schéma de l'URI de redirection doit être propre à chaque application. Dans l’exemple `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` est le schéma. Ce modèle doit être suivi. Si deux applications partagent le même schéma, l’utilisateur doit choisir une application. Si l'utilisateur fait le mauvais choix, la connexion échoue.
    * **Complet** : l'URI de redirection doit être doté d'un schéma et d'un chemin. Le chemin d’accès doit contenir au moins une barre oblique après le domaine. Par exemple, `//oauth/` fonctionne tandis que `//oauth` échoue. N'incluez pas de caractères spéciaux, tels que des traits de soulignement, dans l'URI.
1. Sous **Autorisations**, cochez la case *Accorder le consentement administrateur aux autorisations openid et offline_access*.
1. Sélectionnez **Inscription**.