---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 0ab9302909f86ff4559ece217f8d702710733691
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367997"
---
Pour inscrire une application dans votre locataire Azure AD B2C, vous pouvez utiliser l’expérience **Applications** actuelle ou notre nouvelle expérience unifiée **Inscriptions d’applications (préversion)** . [En savoir plus sur la nouvelle expérience](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Applications](#tab/applications/)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez le répertoire qui contient votre locataire Azure AD B2C.
1. Dans le Portail Azure, recherchez et sélectionnez **Azure Active Directory**.
1. Sous **Gérer**, sélectionnez **Inscriptions des applications (héritées)** .
1. Sélectionnez **Nouvelle inscription d’application**.
1. Entrez un nom pour l’application. Par exemple, *managementapp1*.
1. Pour **Type d’application**, sélectionnez **Application web/API**.
1. Entrez une URL valide dans **URL de connexion**. Par exemple : `https://localhost`. Il doit s’agir d’une URL valide, mais pas nécessairement un point de terminaison accessible.
1. Sélectionnez **Create** (Créer).
1. Notez l’**ID de l’application** qui s’affiche dans la page de vue d’ensemble **Application inscrite**. Vous utiliserez cette valeur ultérieurement.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Inscriptions d’applications (préversion)](#tab/app-reg-preview/)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez le répertoire qui contient votre locataire Azure AD B2C.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications (préversion)** , puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, *managementapp1*.
1. Sélectionnez **Comptes dans ce répertoire organisationnel uniquement**.
1. Sous **Autorisations**, décochez la case *Accorder le consentement administrateur aux autorisations openid et offline_access*.
1. Sélectionnez **Inscription**.
1. Enregistrez l’**ID d’application (client)** indiqué dans la page d’aperçu de l’application. Vous utiliserez cette valeur ultérieurement.