---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: dc75d78f2e73d1aa42f5fc84d39a8b22d6a121bb
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73642603"
---
Pour inscrire une application dans votre locataire Azure AD B2C, vous pouvez utiliser l’expérience **Applications** actuelle ou notre nouvelle expérience unifiée **Inscriptions d’applications (préversion)** . [En savoir plus sur la nouvelle expérience](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Applications](#tab/applications/)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Applications**, puis **Ajouter**.
1. Entrez un nom pour l’application. Par exemple, *testapp1*.
1. Pour **Application web/API web**, sélectionnez **Oui**.
1. Pour **URL de réponse**, entrez `https://jwt.ms`.
1. Sélectionnez **Create** (Créer).

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Inscriptions d’applications (préversion)](#tab/app-reg-preview/)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications (préversion)** , puis **Nouvelle inscription**.
1. Entrez un **nom** pour l’application. Par exemple, *testapp1*.
1. Sélectionnez **Comptes dans un annuaire d’organisation ou un fournisseur d’identité**.
1. Sous **URI de redirection**, sélectionnez **Web**, puis entrez `https://jwt.ms` dans la zone de texte de l’URL.
1. Sous **Autorisations**, activez la case à cocher *Accorder le consentement administrateur aux autorisations openid et offline_access*.
1. Sélectionnez **Inscription**.

Une fois l’inscription de l’application terminée, activez le flux d’octroi implicite :

1. Sous **Gérer**, sélectionnez **Authentification**.
1. Sélectionnez **Essayer la nouvelle expérience** (si elle est indiquée).
1. Sous **Octroi implicite**, cochez les deux cases **Jetons d’accès** et **Jetons d’ID**.
1. Sélectionnez **Enregistrer**.