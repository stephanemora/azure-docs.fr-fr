---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: 587a4bfed00415499bcca6d6054d4ab25cddf0b8
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298915"
---
#### <a name="app-registrations-preview"></a>[Inscriptions d’applications (préversion)](#tab/app-reg-preview/)

1. Sous **Gérer**, sélectionnez **Autorisations de l’API**.
1. Sous **Autorisations configurées**, sélectionnez **Ajouter une autorisation**.
1. Sélectionnez l’onglet **API Microsoft**, puis **Microsoft Graph**.
1. Sélectionnez **Autorisations de l’application**.
1. Développez le groupe d’autorisations approprié et activez la case à cocher de l’autorisation à accorder à votre application de gestion. Par exemple :
    * **AuditLog** > **AuditLog.Read.All** : Pour lire les journaux d’audit de l’annuaire.
    * **Directory** > **Directory.ReadWrite.All** : Pour les scénarios de migration ou de gestion d’utilisateurs.
    * **Policy** > **Policy.ReadWrite.TrustFramework** : Pour les scénarios d’intégration continue/livraison continue (CI/CD). Par exemple, pour le déploiement de stratégies personnalisées avec Azure Pipelines.
1. Sélectionnez **Ajouter des autorisations**. Comme vous l’indiquent les instructions, patientez quelques minutes avant de passer à l’étape suivante.
1. Sélectionnez **Accorder le consentement de l’administrateur pour (nom de votre abonné)** .
1. Sélectionnez le compte administrateur actuellement connecté ou connectez-vous avec un compte de votre locataire Azure AD B2C qui possède au minimum le rôle *Administrateur d’application cloud*.
1. Sélectionnez **Accepter**.
1. Sélectionnez **Actualiser**, puis vérifiez que la mention « Accordé pour ... » apparaît sous **État**. La propagation des autorisations peut prendre quelques minutes.

#### <a name="applications"></a>[Applications](#tab/applications/)

1. Dans la page de vue d’ensemble **Application inscrite**, sélectionnez **Paramètres**.
1. Sous **Accès d’API**, sélectionnez **Autorisations requises**.
1. Sélectionnez **Microsoft Graph**.
1. Sous **Autorisations de l’application**, activez la case à cocher de l’autorisation à accorder à votre application de gestion. Par exemple :
    * **Lire toutes les données du journal d’audit** : Sélectionnez cette autorisation pour lire les journaux d’audit de l’annuaire.
    * **Accéder en lecture et en écriture aux données de l’annuaire** : Sélectionnez cette autorisation pour les scénarios de migration utilisateur ou de gestion des utilisateurs.
    * **Accéder en lecture et en écriture aux stratégies d’infrastructure de confiance de votre organisation** : Sélectionnez cette autorisation pour les scénarios d’intégration continue/livraison continue (CI/CD). Par exemple, pour le déploiement de stratégies personnalisées avec Azure Pipelines.
1. Sélectionnez **Enregistrer**.
1. Sélectionnez **Accorder des autorisations**, puis **Oui**. La propagation complète des autorisations peut prendre quelques minutes.
