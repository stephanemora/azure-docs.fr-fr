---
title: Rapports d’activité d’audit dans le portail Azure Active Directory | Microsoft Docs
description: Présentation des rapports d’activité d’audit dans le portail Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf069e053068eaee3f36153c3888e6c711724267
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182164"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Rapports d’activité d’audit dans le portail Azure Active Directory 

Avec les rapports Azure Active Directory (Azure AD), vous pouvez obtenir toutes les informations dont vous avez besoin pour déterminer l’état de votre environnement.

Cette architecture de création de rapports est constituée des composants suivants :

- **Activité** 
    - **Connexions** – le [rapport de connexions](concept-sign-ins.md) fournit des informations sur l’utilisation des applications managées et les activités de connexion des utilisateurs.
    - **Journaux d’audit** : traçabilité proposée via des journaux pour toutes les modifications effectuées par diverses fonctionnalités au sein d’Azure AD. Par exemple, les journaux d’audit peuvent inclure les modifications apportées à des ressources dans Azure AD comme l’ajout ou la suppression d’utilisateurs, d’applications, de groupes, de rôles, de stratégies, etc.
- **Sécurité** 
    - **Connexions risquées** : une [connexion risquée](concept-risky-sign-ins.md) est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur. 
    - **Utilisateurs avec indicateur de risque** : un [utilisateur à risque](concept-user-at-risk.md) correspond à un indicateur de compte d’utilisateur susceptible d’être compromis.

Cet article présente une vue d’ensemble du rapport d’audit.
 
## <a name="who-can-access-the-data"></a>Qui peut accéder aux données ?

* Les utilisateurs dans le **administrateur de sécurité**, **lecteur sécurité**, **lecteur de rapport** ou **administrateur général** rôles
* En outre, tous les utilisateurs (non-administrateurs) peuvent voir leurs propres activités d’audit

## <a name="audit-logs"></a>Journaux d’audit

Les journaux d’audit Azure AD fournissent des enregistrements des activités du système pour la conformité. Pour accéder au rapport d’audit, sélectionnez **Journaux d’audit** dans la section **Activité** d’**Azure Active Directory**. Notez que les journaux d’audit peuvent avoir une latence de jusqu’à une heure, et il peut s’écouler un certain temps avant que les données d’activité d’audit s’affichent dans le portail une fois que vous avez terminé la tâche.

![Journaux d’audit](./media/concept-audit-logs/61.png "Journaux d’Audit")

Un journal d’audit a une vue de liste par défaut qui indique :

- la date et l’heure de l’occurrence
- le service qui a enregistré l’incident
- la catégorie et le nom de l’activité (*que*) 
- l’état de l’activité (succès ou échec)
- la cible
- l’initiateur/intervenant d’une activité (qui)

![Journaux d’audit](./media/concept-audit-logs/listview.png "Journaux d’Audit")

Vous pouvez personnaliser la vue sous forme de liste en cliquant sur **Colonnes** dans la barre d’outils.

![Journaux d’audit](./media/concept-audit-logs/columns.png "Journaux d’Audit")

Cela vous permet d’afficher des champs supplémentaires ou de supprimer des champs qui sont déjà affichés.

![Journaux d’audit](./media/concept-audit-logs/columnselect.png "Journaux d’Audit")

Sélectionnez un élément dans la vue sous forme de liste pour obtenir des informations plus détaillées.

![Journaux d’audit](./media/concept-audit-logs/details.png "Journaux d’Audit")


## <a name="filtering-audit-logs"></a>Filtrage des journaux d’audit

Vous pouvez filtrer les données d’audit des champs suivants :

- de diffusion en continu
- Catégorie
- Activité
- Statut
- Cible
- Initié par (intervenant)
- Plage de dates

![Journaux d’audit](./media/concept-audit-logs/filter.png "Journaux d’Audit")

Le **Service** filtre vous permet de sélectionner dans une liste déroulante des services suivants :

- Tous
- Révisions d’accès
- Approvisionnement des comptes 
- Application SSO
- Méthodes d’authentification
- B2C
- Accès conditionnel
- Annuaire principal
- Gestion des droits d'utilisation
- Identity Protection
- Utilisateurs invités
- PIM
- Gestion des groupes en libre-service
- Gestion de Passord libre-service
- Conditions d’utilisation

Le **catégorie** filtre vous permet de sélectionner l’un des filtres suivants :

- Tous
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Authorization
- Contact
- Appareil
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Autres
- Stratégie
- ResourceManagement
- RoleManagement
- UserManagement

Le **activité** filtre est basé sur la catégorie et l’activité resource type que vous choisissez. Vous pouvez sélectionner une activité spécifique que vous souhaitez voir ou toutes les choisir. 

Vous pouvez obtenir la liste de toutes les activités d’audit à l’aide de l’API Graph https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta où $tenantdomain correspond à votre nom de domaine. Vous pouvez également consulter l’article sur [les évènements de rapports d’audit](reference-audit-activities.md).

Le **état** filtre vous permet de filtrer en fonction de l’état d’une opération d’audit. L’état peut être une des opérations suivantes :

- Tous
- Succès
- Échec

Le **cible** filtre vous permet de rechercher une cible particulière par nom ou nom d’utilisateur principal (UPN). Le nom de la cible et l’UPN respectent la casse. 

Le **initié par** filtre vous permet de définir le nom d’un intervenant ou un nom principal universel (UPN). Le nom et l’UPN respectent la casse.

Le **plage de dates** filtre vous permet de définir un intervalle de temps pour les données renvoyées.  
Les valeurs possibles sont les suivantes :

- 1 mois
- 7 jours
- 24 heures
- Personnalisée

Lorsque vous sélectionnez une plage personnalisée, vous pouvez configurer une heure de début et une heure de fin.

Vous pouvez également choisir de télécharger les données filtrées, jusqu'à 250 000 enregistrements, en sélectionnant le **télécharger** bouton. Vous pouvez choisir de télécharger les journaux au format CSV ou JSON. Le nombre d’enregistrements que vous pouvez télécharger est limité par les [stratégies de rétention de rapport Azure Active Directory](reference-reports-data-retention.md).

![Journaux d’audit](./media/concept-audit-logs/download.png "Journaux d’Audit")

## <a name="audit-logs-shortcuts"></a>Raccourcis de journaux d’audit

En plus d’**Azure Active Directory**, le portail Azure vous offre deux autres points d’entrée pour les données d’audit :

- Utilisateurs et groupes
- Applications d’entreprise

### <a name="users-and-groups-audit-logs"></a>Journaux d’audit des utilisateurs et des groupes

Les rapports d’audit basés sur les utilisateurs et les groupes vous permettent d’obtenir des réponses aux questions telles que :

- Quels types de mises à jour ont été appliquées aux utilisateurs ?

- Combien d’utilisateurs ont été modifiés ?

- Combien de mots de passe ont été modifiés ?

- Qu’a fait un administrateur dans un répertoire ?

- Quels sont les groupes qui ont été ajoutés ?

- Existe-t-il des groupes comportant des modifications d’adhésion ?

- Les propriétaires de groupe ont-ils été modifiés ?

- Quelles licences ont été attribuées à un groupe ou un utilisateur ?

Si vous souhaitez simplement consulter les données d’audit relatives aux utilisateurs, vous trouverez une vue filtrée sous **journaux d’Audit** dans le **activité** section de la **utilisateurs** onglet. Ce point d’entrée **usermanagement** en tant que catégorie présélectionnée.

![Journaux d’audit](./media/concept-audit-logs/users.png "Journaux d’Audit")

Si vous souhaitez simplement consulter les données d’audit qui sont liées à des groupes, vous trouverez une vue filtrée sous **journaux d’Audit** dans le **activité** section de la **groupes** onglet. Ce point d’entrée **GroupManagement** en tant que catégorie présélectionnée.

![Journaux d’audit](./media/concept-audit-logs/groups.png "Journaux d’Audit")

### <a name="enterprise-applications-audit-logs"></a>Journaux d’audit d’applications d’entreprise

Les rapports d’audit basés sur les applications vous permettent d’obtenir des réponses aux questions telles que :

* Quelles applications ont été ajoutées ou mises à jour ?
* Quelles applications ont été supprimées ?
* Le principal de service d’une application a-t-il été modifié ?
* Les noms des applications ont-ils été modifiés ?
* Qui a donné son consentement à une application ?

Si vous souhaitez consulter les données d’audit associées à vos applications, vous pouvez trouver une vue filtrée sous **Journaux d’audit** dans la section **Activité** du panneau **Applications d’entreprise**. Ce point d’entrée **applications d’entreprise** est présélectionné en tant que le **Type d’Application**.

![Journaux d’audit](./media/concept-audit-logs/enterpriseapplications.png "Journaux d’Audit")

## <a name="office-365-activity-logs"></a>Journaux d’activité Office 365

Vous pouvez afficher les journaux d’activité Office 365 à partir de la [centre d’administration Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Même si Office 365 Azure AD activité et journaux partagent un grand nombre de ressources du répertoire, uniquement le centre d’administration Microsoft 365 fournit une vue complète des journaux d’activité Office 365. 

Vous pouvez également accéder par programme aux journaux d’activité d’Office 365 avec l’[API de gestion Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Étapes suivantes

- [Référence sur l’activité d’audit Azure AD](reference-audit-activities.md)
- [Référence de la rétention des rapports Azure AD](reference-reports-data-retention.md)
- [Référencent des latences de journal Azure AD](reference-reports-latencies.md)
