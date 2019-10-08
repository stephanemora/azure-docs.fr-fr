---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/30/2019
ms.author: marsma
ms.openlocfilehash: 4ac69b51e69031a1a4ce83a2c09dc6cc010ccd62
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720278"
---
Les ressources des API web doivent être inscrites auprès de votre locataire avant de pouvoir accepter et répondre aux demandes de ressources protégées émanant des applications clientes qui présentent un jeton d'accès.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Applications**, puis **Ajouter**.
1. Entrez un nom pour l’application. Par exemple, *webapi1*.
1. Pour **Application web/API web**, sélectionnez **Oui**.
1. Pour **Autoriser un flux implicite**, sélectionnez **Oui**.
1. Pour l’**URL de réponse**, entrez un point de terminaison où Azure AD B2C doit retourner les jetons demandés par votre application. Dans ce tutoriel, l'exemple s'exécute localement et écoute `https://localhost:5000`.
1. Pour **URI ID d’application**, ajoutez un identificateur de point de terminaison d’API pour l’URI affiché. Pour ce tutoriel, entrez `api`, pour que l’URI complet soit similaire à `https://contosob2c.onmicrosoft.com/api`.
1. Sélectionnez **Create** (Créer).
1. Enregistrez l’**ID D’APPLICATION** pour l’utiliser dans une étape ultérieure.
