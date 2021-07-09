---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 05/26/2021
ms.author: mimart
ms.openlocfilehash: b4bcb2da8efdf4943c758aaa2ad739e220cfcc8d
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110575048"
---
Pour inscrire une application dans votre locataire Azure AD B2C, vous pouvez utiliser notre nouvelle expérience unifiée **Inscriptions d’applications** ou notre expérience héritée **Applications (héritées)** . [En savoir plus sur la nouvelle expérience](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Inscriptions des applications](#tab/app-reg-ga/)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, *nativeapp1*.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un répertoire organisationnel ou un fournisseur d'identité**.
1. Sous **URI de redirection**, utilisez la liste déroulante pour sélectionner **client public/natif (Bureau et mobile)** .
1. Entrez un URI de redirection avec un schéma unique. Par exemple : `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Quelques points importants sont à prendre en compte quand vous choisissez un URI de redirection :
    * **Développement** Pour le développement et les **applications de bureau**, vous pouvez affecter la valeur `http://localhost` à l’URI de redirection. Azure AD B2C respecte tous les ports de la requête. Si l’URI inscrit contient un port, Azure AD B2C utilisera uniquement ce port. Par exemple, si l’URI de redirection inscrit est `http://localhost`, l’URI de redirection dans la requête peut être `http://localhost:<randomport>`. Si l’URI de redirection inscrit est `http://localhost:8080`, l’URI de redirection dans la requête doit être `http://localhost:8080`.
    * **Unique** : le schéma de l'URI de redirection doit être propre à chaque application. Dans l’exemple `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` est le schéma. Ce modèle doit être suivi. Si deux applications partagent le même schéma, l’utilisateur doit choisir une application. Si l'utilisateur fait le mauvais choix, la connexion échoue.
    * **Complet** : l'URI de redirection doit être doté d'un schéma et d'un chemin. Le chemin d’accès doit contenir au moins une barre oblique après le domaine. Par exemple, `//oauth/` fonctionne tandis que `//oauth` échoue. N'incluez pas de caractères spéciaux, tels que des traits de soulignement, dans l'URI.
1. Sous **Autorisations**, activez la case à cocher *Accorder le consentement administrateur aux autorisations openid et offline_access*.
2. Sélectionnez **Inscription**.

#### <a name="applications-legacy"></a>[Applications (héritées)](#tab/applications-legacy/)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Applications (héritées)** , puis **Ajouter**.
1. Entrez un nom pour l’application. Par exemple, *nativeapp1*.
1. Pour **Client natif**, sélectionnez **Oui**.
1. Entrez un **URI de redirection personnalisé** avec un schéma unique. Par exemple : `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Deux points importants sont à prendre en compte quand vous choisissez un URI de redirection :
    * **Unique** : le schéma de l'URI de redirection doit être propre à chaque application. Dans l’exemple `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` est le schéma. Ce modèle doit être suivi. Si deux applications partagent le même schéma, l’utilisateur doit choisir une application. Si l'utilisateur fait le mauvais choix, la connexion échoue.
    * **Complet** : l'URI de redirection doit être doté d'un schéma et d'un chemin. Le chemin d’accès doit contenir au moins une barre oblique après le domaine. Par exemple, `//oauth/` fonctionne tandis que `//oauth` échoue. N'incluez pas de caractères spéciaux, tels que des traits de soulignement, dans l'URI.
1. Sélectionnez **Create** (Créer).
