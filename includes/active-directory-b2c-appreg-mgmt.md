---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 77c3aee4fdb5f8b2ee7ed83f92917573ad6ad529
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643609"
---
Pour inscrire une application dans votre abonné Azure AD B2C, vous pouvez utiliser l’expérience **Applications** actuelle ou notre nouvelle expérience unifiée **Inscriptions d’applications (préversion)** . [En savoir plus sur la nouvelle expérience](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Applications](#tab/applications/)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l'annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure Active Directory** (et *non* Azure AD B2C). Vous pouvez également sélectionner **Tous les services**, puis rechercher et sélectionner **Azure Active Directory**.
1. Sous **Gérer**, sélectionnez **Inscriptions des applications (héritées)** .
1. Sélectionnez **Nouvelle inscription d’application**.
1. Entrez un nom pour l’application. Par exemple, *managementapp1*.
1. Pour **Type d’application**, sélectionnez **Application web/API**.
1. Entrez une URL valide dans **URL de connexion**. Par exemple : `https://localhost`. Il doit s’agir d’une URL valide, mais pas nécessairement un point de terminaison accessible.
1. Sélectionnez **Create** (Créer).
1. Notez l’**ID de l’application** qui s’affiche dans la page de vue d’ensemble **Application inscrite**. Vous utiliserez cette valeur ultérieurement.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Inscriptions d’applications (préversion)](#tab/app-reg-preview/)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications (préversion)** , puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, *managementapp1*.
1. Sélectionnez **Comptes dans ce répertoire organisationnel uniquement**.
1. Sous **Autorisations**, décochez la case *Accorder le consentement administrateur aux autorisations openid et offline_access*.
1. Sélectionnez **Inscription**.
1. Enregistrez l’**ID d’application (client)** indiqué dans la page d’aperçu de l’application. Vous utiliserez cette valeur ultérieurement.