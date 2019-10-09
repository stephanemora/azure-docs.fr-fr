---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 10/01/2019
ms.author: marsma
ms.openlocfilehash: 67a0832f868fecd47983aa8cddff9cdf139b3f2a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702182"
---
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
