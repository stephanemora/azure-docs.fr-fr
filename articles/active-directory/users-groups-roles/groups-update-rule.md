---
title: Mettre à jour et gérer une règle de groupe dynamique et résoudre les problèmes d'appartenance - Azure Active Directory | Microsoft Docs
description: Comment créer une règle d’appartenance à un groupe dans le portail Azure et comment vérifier l’état.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/30/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c387e2d78adcaebc430073a2a45818c4a0928b9f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422351"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Mettre à jour un groupe dynamique pour gérer l'appartenance dans Azure Active Directory

Dans Azure Active Directory (Azure AD), vous pouvez utiliser des règles pour déterminer l’appartenance aux groupes à partir des propriétés de l’utilisateur ou de l’appareil. Cet article explique comment configurer une règle pour un groupe dynamique dans le portail Azure.
L’appartenance dynamique est prise en charge pour les groupes de sécurité ou les groupes Office 365. Lorsqu’une règle d’appartenance de groupe est appliquée, les correspondances avec la règle d’appartenance des attributs utilisateur et appareil sont évaluées. Lorsqu’un attribut d’utilisateur ou d’appareil change, toutes les règles de groupe dynamique au sein de l’organisation sont traitées pour tenir compte de toutes les modifications d’appartenance. Les utilisateurs et les appareils sont ajoutés ou supprimés s’ils remplissent les conditions pour un groupe.

## <a name="rule-builder-in-the-azure-portal"></a>Générateur de règles dans le portail Azure

Azure AD fournit un générateur de règles pour créer et mettre à jour plus rapidement vos règles importantes. Le générateur de règles prend en charge la construction jusqu’à cinq expressions. Le générateur de règles facilite la création d’une règle avec quelques expressions simples. Toutefois, il ne peut pas être utilisé pour reproduire chaque règle. Si le générateur de règles ne prend pas en charge la règle que vous souhaitez créer, vous pouvez utiliser la zone de texte.

Voici quelques exemples de règles avancées ou de syntaxe pour lesquelles nous vous recommandons de construire à l’aide de la zone de texte :

- Règle avec plus de cinq expressions
- Règle de collaborateurs directs
- Définition de la [priorité des opérateurs](groups-dynamic-membership.md#operator-precedence)
- [Règles avec des expressions complexes](groups-dynamic-membership.md#rules-with-complex-expressions), par exemple `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Le générateur de règles peut ne pas être en mesure d’afficher certaines règles construites dans la zone de texte. Un message peut s’afficher lorsque le générateur de règles n’est pas en mesure d’afficher la règle. Le générateur de règles ne modifie en aucune façon la syntaxe, la validation ou le traitement des règles de groupe dynamique pris en charge.

![Ajouter une règle d’appartenance au groupe dynamique](./media/groups-update-rule/update-dynamic-group-rule.png)

Pour obtenir des exemples de syntaxe des règles, des propriétés prises en charge, des opérateurs et des valeurs pris en charge pour une règle d’appartenance, consultez [Règles d’appartenance dynamique pour les groupes dans Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-update-a-group-membership-rule"></a>Pour mettre à jour une règle d'appartenance à un groupe

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com) avec un compte du rôle Administrateur général, Administrateur Intune ou administrateur d’utilisateurs dans l’abonné.
1. Sélectionnez **Groupes** > **Tous les groupes**.
1. Sélectionnez un groupe pour ouvrir son profil.
1. Sur la page de profil du groupe, sélectionnez **Règles d'appartenance dynamique**. Le générateur de règles prend en charge jusqu'à cinq expressions. Pour ajouter plus de cinq expressions, vous devez utiliser la zone de texte.

   ![Ajouter une règle d’appartenance au groupe dynamique](./media/groups-update-rule/update-dynamic-group-rule.png)

1. Pour afficher les propriétés d'extension personnalisées disponibles pour votre règle d'appartenance :
   1. Sélectionnez **Obtenir les propriétés d’extension personnalisée**
   1. Entrez l’ID de l’application, puis sélectionnez **Actualiser les propriétés**.
1. Une fois la règle mise à jour, sélectionnez **Enregistrer**.

Si la règle que vous avez entrée n’est pas valide, une explication de la raison pour laquelle la règle n’a pas pu être traitée s’affiche dans une notification Azure du portail. Lisez-la avec attention pour savoir comment corriger la règle.

## <a name="check-processing-status-for-a-rule"></a>Vérifier l’état de traitement d’une règle

Vous pouvez voir l’état du traitement de l’appartenance et la date de la dernière mise à jour dans la page **Vue d’ensemble** du groupe.
  
  ![afficher l’état du groupe dynamique](./media/groups-create-rule/group-status.png)

Les messages d’état suivants peuvent être affichés pour l’état **Traitement de l’appartenance** :

- **Évaluation** :  le changement de groupe a été reçu et les mises à jour sont en cours d’évaluation.
- **Traitement** : les mises à jour sont en cours de traitement.
- **Mise à jour terminée** : le traitement est terminé et toutes les mises à jour applicables ont été effectuées.
- **Erreur de traitement** :  Impossible de terminer le traitement en raison d’une erreur d’évaluation de la règle d’appartenance.
- **Mise à jour suspendue** : les mises à jour de la règle d’appartenance dynamique ont été suspendues par l’administrateur. Le paramètre MembershipRuleProcessingState est défini sur « Suspendu ».

Les messages d’état suivants peuvent être affichés pour l’état **Dernière mise à jour de l’appartenance** :

- **Date et heure** : date et heure de la dernière mise à jour de l’appartenance.
- **En cours** : les mises à jour sont en cours d’exécution.
- **Inconnue** : impossible de récupérer l’heure de la dernière mise à jour. Le groupe est peut-être nouveau.

Si une erreur se produit lors du traitement de la règle d’appartenance pour un groupe spécifique, une alerte s’affiche en haut de la page **Vue d’ensemble** du groupe. Si aucune mise à jour d’appartenance dynamique en attente ne peut être traitée pour tous les groupes au sein du locataire pendant plus de 24 heures, une alerte s’affiche en haut de **Tous les groupes**.

![alertes de message d’erreur de traitement](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>Étapes suivantes

Ces articles fournissent des informations supplémentaires sur l'utilisation des groupes dynamiques dans Azure AD.

- Pour des références complètes sur la structure des règles dynamiques, consultez [Syntaxe des règles d'appartenance dynamique](groups-dynamic-membership.md).
- [Créez un groupe d'appartenance statique et ajoutez-y des membres](../fundamentals/active-directory-groups-create-azure-portal.md).
