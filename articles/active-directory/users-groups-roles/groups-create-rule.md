---
title: Création d’un groupe dynamique et vérification d’état - Azure Active Directory | Microsoft Docs
description: Comment créer des règles d’appartenance à un groupe dans le portail Azure et comment vérifier l’état.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5651d5e37613abcef8c8f5448af38637f91ebe30
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193626"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Créer un groupe dynamique et vérifier l’état

Dans Azure Active Directory (Azure AD), vous pouvez créer des groupes en appliquant une règle permettant de déterminer l’appartenance à partir des propriétés de l’utilisateur ou de l’appareil. En cas de modification des attributs d’un utilisateur ou d’un appareil, Azure AD évalue toutes les règles de groupe dynamique dans l’abonné Azure AD et effectue les éventuels ajouts ou suppressions nécessaires. Si un utilisateur ou un appareil satisfait à la règle définie pour un groupe, il est ajouté en tant que membre. Il est supprimé dès lors qu’il ne respecte plus la règle.

Cet article explique comment configurer une règle dans le portail Azure pour une appartenance dynamique à des groupes de sécurité ou à des groupes Office 365. Pour obtenir des exemples de syntaxe de règle et une liste complète des propriétés prises en charge, les opérateurs et les valeurs d’une règle d’appartenance, consultez [Créer des groupes dynamiques avec une appartenance basée sur des attributs dans Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Pour créer une règle d’appartenance à un groupe

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com) en utilisant un compte du rôle Administrateur général, Administrateur de services fédérés Intune ou Administrateur des comptes d’utilisateur dans le locataire.
2. Sélectionnez **Groupes**.
3. Sélectionnez **Tous les groupes**, puis **Nouveau groupe**.

   ![Ajouter un nouveau groupe](./media/groups-create-rule/new-group-creation.png)

4. Dans le panneau **Groupe** , saisissez un nom et une description pour le nouveau groupe. Sélectionnez un **Type d’appartenance** entre **Utilisateur dynamique** et **Appareil dynamique**, selon que vous souhaitiez créer une règle pour des utilisateurs ou des périphériques, puis sélectionnez **Ajouter une requête dynamique**. Vous pouvez utiliser le générateur de règle pour créer une règle simple, ou écrire une règle d’appartenance vous-même. Cet article contient plus d’informations sur les attributs d’utilisateur et d’appareil disponibles, ainsi que des exemples de règles d’appartenance.

   ![Ajouter une règle d’appartenance dynamique](./media/groups-create-rule/add-dynamic-group-rule.png)

5. Pour afficher la liste complète des propriétés d’extension personnalisées que vous pouvez ajouter à votre requête d’appartenance, sélectionnez **Obtenir des propriétés d’extension personnalisées**, entrez l’ID d’application, puis sélectionnez **Actualiser les propriétés**. Vous pouvez désormais sélectionner des éléments de la liste des propriétés.
6. Après avoir créé la règle, sélectionnez **Ajouter une requête** dans le bas du panneau.
7. Sélectionnez **Créer** on the **Groupe** panneau pour créer le groupe.

> [!TIP]
> La création du groupe échoue si la règle que vous avez entrée est incorrecte ou non valide. Une notification s’affiche alors dans le coin supérieur droit du portail. Elle contient une explication de la raison pour laquelle la règle n’a pas pu être traitée. Lisez-la avec attention pour comprendre comment vous devez ajuster la règle pour la rendre valide.

## <a name="check-processing-status-for-a-membership-rule"></a>Vérifier l’état de traitement d’une règle d’appartenance

Vous pouvez voir l’état du traitement de l’appartenance et la date de la dernière mise à jour dans la page **Vue d’ensemble** du groupe.
  
  ![affichage de l’état du groupe dynamique](./media/groups-create-rule/group-status.png)

Les messages d’état suivants peuvent être affichés pour l’état **Traitement de l’appartenance** :

* **Évaluation** :  le changement de groupe a été reçu et les mises à jour sont en cours d’évaluation.
* **Traitement** : les mises à jour sont en cours de traitement.
* **Mise à jour terminée** : le traitement est terminé et toutes les mises à jour applicables ont été effectuées.
* **Erreur de traitement** : une erreur s’est produite lors de l’évaluation de la règle d’appartenance et le traitement n’a pas pu être effectué.
* **Mise à jour suspendue** : les mises à jour de la règle d’appartenance dynamique ont été suspendues par l’administrateur. Le paramètre MembershipRuleProcessingState est défini sur « Suspendu ».

Les messages d’état suivants peuvent être affichés pour l’état **Dernière mise à jour de l’appartenance** :

* &lt;**Date et heure**&gt; : date et heure de la dernière mise à jour de l’appartenance.
* **En cours** : les mises à jour sont en cours d’exécution.
* **Inconnue** : impossible de récupérer l’heure de la dernière mise à jour. Cela tient peut-être au groupe qui vient d’être créé.

Si une erreur se produit lors du traitement de la règle d’appartenance pour un groupe spécifique, une alerte s’affiche en haut de la page **Vue d’ensemble** du groupe. Si aucune mise à jour d’appartenance dynamique en attente ne peut être traitée pour tous les groupes au sein du locataire pendant plus de 24 heures, une alerte s’affiche en haut de **Tous les groupes**.

![message d’erreur de traitement](./media/groups-create-rule/processing-error.png)

Ces articles fournissent des informations supplémentaires sur les groupes dans Azure Active Directory.

* [Consulter les groupes existants](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Création d’un nouveau groupe et ajout de membres](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gérer les paramètres d’un groupe](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gérer l’appartenance à un groupe](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](groups-dynamic-membership.md)
