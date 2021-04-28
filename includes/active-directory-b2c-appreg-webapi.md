---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 85883cd465626764d7af0c20af480b6075e13f9e
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108070572"
---
Les ressources des API web doivent être inscrites auprès de votre locataire avant de pouvoir accepter et répondre aux demandes de ressources protégées émanant des applications clientes qui présentent un jeton d'accès.

Pour inscrire une application dans votre locataire Azure AD B2C, vous pouvez utiliser notre nouvelle expérience unifiée **Inscriptions d’applications** ou notre expérience héritée **Applications (héritées)** . [En savoir plus sur la nouvelle expérience](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Inscriptions des applications](#tab/app-reg-ga/)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, *webapi1*.
1. Sous **URI de redirection**, sélectionnez **Web**, puis entrez un point de terminaison où Azure AD B2C doit retourner les jetons demandés par votre application. Dans ce tutoriel, l'exemple s'exécute localement et écoute `http://localhost:5000`.
1. Sélectionnez **Inscription**.
1. Enregistrez l’**ID d’application (client)** pour l’utiliser dans une étape ultérieure.

Ensuite, activez le flux d’octroi implicite :

1. Sous **Gérer**, sélectionnez **Authentification**.
1. Sélectionnez **Essayer la nouvelle expérience** (si elle est indiquée).
1. Sous **Octroi implicite**, cochez les deux cases **Jetons d’accès** et **Jetons d’ID**.
1. Sélectionnez **Enregistrer**.

#### <a name="applications-legacy"></a>[Applications (héritées)](#tab/applications/)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Applications (héritées)** , puis **Ajouter**.
1. Entrez un nom pour l’application. Par exemple, *webapi1*.
1. Pour **Application web/API web**, sélectionnez **Oui**.
1. Pour **Autoriser un flux implicite**, sélectionnez **Oui**.
1. Pour l’**URL de réponse**, entrez un point de terminaison où Azure AD B2C doit retourner les jetons demandés par votre application. Dans ce tutoriel, l'exemple s'exécute localement et écoute `https://localhost:5000`.
1. Pour **URI ID d’application**, ajoutez un identificateur de point de terminaison d’API pour l’URI affiché. Pour ce tutoriel, entrez `api`, pour que l’URI complet soit similaire à `https://contosob2c.onmicrosoft.com/api`.
1. Sélectionnez **Create** (Créer).
1. Enregistrez l’**ID D’APPLICATION** pour l’utiliser dans une étape ultérieure.