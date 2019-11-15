---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 43bcd1f11eb228bd1454b2ad0f2addb851029f2f
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799812"
---
#### <a name="applicationstabapplications"></a>[Applications](#tab/applications/)

1. Dans la page de vue d’ensemble **Application inscrite**, sélectionnez **Paramètres**.
1. Sous **ACCÈS D’API**, sélectionnez **Autorisations requises**.
1. Sélectionnez **Windows Azure Active Directory**.
1. Sous **AUTORISATIONS DE L’APPLICATION**, sélectionnez **Accéder en lecture et en écriture aux données de l’annuaire**.
1. Sélectionnez **Enregistrer**.
1. Sélectionnez **Accorder des autorisations**, puis **Oui**. La propagation complète des autorisations peut prendre quelques minutes.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Inscriptions d’applications (préversion)](#tab/app-reg-preview/)

1. Sous **Gérer**, sélectionnez **Autorisations de l’API**.
1. Sous **Autorisations configurées**, sélectionnez **Ajouter une autorisation**.
1. Sélectionnez **Azure Active Directory Graph**.
1. Sélectionnez **Autorisations de l’application**.
1. Développez **Répertoire** puis cochez la case **Directory. ReadWrite. All**.
1. Sélectionnez **Ajouter des autorisations**. Comme vous l’indiquent les instructions, patientez quelques minutes avant de passer à l’étape suivante.
1. Sélectionnez **Accorder le consentement de l’administrateur pour (nom de votre abonné)** .
1. Sélectionnez un compte d’administrateur abonné.
1. Sélectionnez **Accepter**.
1. Sélectionnez **Actualiser**, puis vérifiez que la mention « Accordé pour ... » apparaît sous **État**. La propagation des autorisations peut prendre quelques minutes.