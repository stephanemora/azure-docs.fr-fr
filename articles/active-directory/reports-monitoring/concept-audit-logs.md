---
title: Rapports d’activité d’audit dans le portail Azure Active Directory | Microsoft Docs
description: Présentation des rapports d’activité d’audit dans le portail Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad399fc24b2cdfbdc51e7feccba2c05786216b19
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648423"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Rapports d’activité d’audit dans le portail Azure Active Directory 

Avec les rapports Azure Active Directory (Azure AD), vous pouvez obtenir toutes les informations dont vous avez besoin pour déterminer l’état de votre environnement.

Cette architecture de création de rapports est constituée des composants suivants :

- **Activité** 
    - **Connexions** – le [rapport de connexions](concept-sign-ins.md) fournit des informations sur l’utilisation des applications managées et les activités de connexion des utilisateurs.
    - **Journaux d’audit** : traçabilité proposée via des journaux d’activité pour toutes les modifications effectuées par diverses fonctionnalités au sein d’Azure AD. Par exemple, les journaux d’audit peuvent inclure les modifications apportées à des ressources dans Azure AD comme l’ajout ou la suppression d’utilisateurs, d’applications, de groupes, de rôles, de stratégies, etc.
- **Sécurité** 
    - **Connexions risquées** : une [connexion risquée](concept-risky-sign-ins.md) est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur. 
    - **Utilisateurs avec indicateur de risque** : un [utilisateur à risque](concept-user-at-risk.md) correspond à un indicateur de compte d’utilisateur susceptible d’être compromis.

Cet article présente une vue d’ensemble du rapport d’audit.
 
## <a name="who-can-access-the-data"></a>Qui peut accéder aux données ?

* Les utilisateurs des rôles **Administrateur de la sécurité**, **Lecteur Sécurité**, **Lecteur de rapports**, **Lecteur général** ou **Administrateur général**

## <a name="audit-logs"></a>Journaux d’audit

Les journaux d’audit Azure AD fournissent des enregistrements des activités du système pour la conformité. Pour accéder au rapport d’audit, sélectionnez **Journaux d’audit** dans la section **Surveillance** d’**Azure Active Directory**. Notez que les journaux d’audit peuvent avoir une latence allant jusqu’à une heure, et il peut s’écouler un certain temps avant que les données d’activité d’audit s’affichent dans le portail une fois que vous avez terminé la tâche.



Un journal d’audit inclut un mode Liste par défaut, qui indique :

- la date et l’heure de l’occurrence
- le service qui a enregistré l’occurrence
- la catégorie et le nom de l’activité (*laquelle*) 
- l’état de l’activité (réussite ou échec)
- la cible
- l’initiateur/intervenant d’une activité (qui)

![Journaux d’audit](./media/concept-audit-logs/listview.png "Journaux d’audit")

Vous pouvez personnaliser le mode Liste en cliquant sur **Colonnes** dans la barre d’outils.

![Journaux d’audit](./media/concept-audit-logs/columns.png "Journaux d’audit")

Cela vous permet d’afficher des champs supplémentaires, ou de supprimer des champs qui sont déjà affichés.

![Journaux d’audit](./media/concept-audit-logs/columnselect.png "Journaux d’audit")

Sélectionnez un élément dans la vue sous forme de liste pour obtenir des informations plus détaillées.

![Journaux d’audit](./media/concept-audit-logs/details.png "Journaux d’audit")


## <a name="filtering-audit-logs"></a>Filtrage des journaux d’audit

Vous pouvez filtrer les données d’audit des champs suivants :

- Service
- Category
- Activité
- Statut
- Cible
- Initié par (intervenant)
- Plage de dates

![Journaux d’audit](./media/concept-audit-logs/filter.png "Journaux d’audit")

Le filtre **Service** vous permet de sélectionner les services suivants dans une liste déroulante :

- Tous
- AAD Management UX
- Révisions d’accès
- Approvisionnement des comptes
- Application Proxy (Proxy d’application)
- Méthodes d’authentification
- B2C
- Accès conditionnel
- Annuaire principal
- Gestion des droits d’utilisation
- Authentification hybride
- Identity Protection
- Utilisateurs invités
- Service MIM
- MyApps
- PIM
- Gestion des groupes en libre-service
- Gestion des mots de passe en libre-service
- Conditions d’utilisation

Le filtre **Catégorie** vous permet de sélectionner un des filtres suivants :

- Tous
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Autorisation
- Contact
- Appareil
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- KeyManagement
- Étiquette
- Autres
- PermissionGrantPolicy
- Stratégie
- ResourceManagement
- RoleManagement
- UserManagement

Le filtre **Activité** est basé sur la catégorie et le type de ressource d’activité que vous choisissez. Vous pouvez sélectionner une activité spécifique que vous souhaitez voir ou toutes les choisir. 

Vous pouvez récupérer la liste de toutes les activités d’audit en utilisant l’API Graph : `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Le filtre **État** vous permet de filtrer en fonction de l’état d’une opération d’audit. Il peut s’agir d'un des états suivants :

- Tous
- Succès
- Échec

Le filtre **Cible** vous permet de rechercher une cible donnée par début de nom ou de nom d’utilisateur principal (UPN). Le nom et le nom d'utilisateur principal cibles sont sensibles à la casse. 

Le filtre **Initié par** vous permet de définir le début du nom d’un intervenant ou du nom d’utilisateur principal (UPN). Le nom et le nom d'utilisateur principal sont sensibles à la casse.

Le filtre **Plage de dates** vous permet de définir un intervalle de temps pour les données renvoyées.  
Les valeurs possibles sont les suivantes :

- 7 jours
- 24 heures
- Custom

Lorsque vous sélectionnez une plage personnalisée, vous pouvez configurer une heure de début et une heure de fin.

Vous pouvez également télécharger les données filtrées, jusqu’à 250 000 enregistrements, en sélectionnant le bouton **Télécharger**. Vous pouvez télécharger les journaux d’activité au format CSV ou JSON. Le nombre d’enregistrements que vous pouvez télécharger est limité par les [stratégies de rétention de rapport Azure Active Directory](reference-reports-data-retention.md).

![Journaux d’audit](./media/concept-audit-logs/download.png "Journaux d’audit")

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

Si vous souhaitez simplement consulter les données d’audit connexes aux utilisateurs, vous pouvez filtrer l’affichage dans **Journaux d’audit** dans la section **Surveillance** de l’onglet **Utilisateurs**. La catégorie présélectionnée de ce point d'entrée est **UserManagement**.

![Journaux d’audit](./media/concept-audit-logs/users.png "Journaux d’audit")

Si vous souhaitez simplement consulter les données d’audit connexes aux groupes, vous pouvez filtrer l’affichage dans **Journaux d’audit** dans la section **Surveillance** de l’onglet **Groupes**. La catégorie présélectionnée de ce point d'entrée est **GroupManagement**.

![Journaux d’audit](./media/concept-audit-logs/groups.png "Journaux d’audit")

### <a name="enterprise-applications-audit-logs"></a>Journaux d’audit d’applications d’entreprise

Les rapports d’audit basés sur les applications vous permettent d’obtenir des réponses aux questions telles que :

* Quelles applications ont été ajoutées ou mises à jour ?
* Quelles applications ont été supprimées ?
* Le principal de service d’une application a-t-il été modifié ?
* Les noms des applications ont-ils été modifiés ?
* Qui a donné son consentement à une application ?

Si vous souhaitez consulter les données d’audit associées à vos applications, vous pouvez trouver une vue filtrée sous **Journaux d’audit** dans la section **Activité** du panneau **Applications d’entreprise**. Dans ce point d’entrée, **Applications d’entreprise** est présélectionné comme **Type d'application**.

![Journaux d’audit](./media/concept-audit-logs/enterpriseapplications.png "Journaux d’audit")

## <a name="office-365-activity-logs"></a>Journaux d’activité Office 365

Vous pouvez consulter les journaux d’activité Office 365 dans le [centre d’administration Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Bien que les journaux d’activité Office 365 et Azure AD partagent une grande partie des ressources du répertoire, seul le centre d’administration Microsoft 365 offre une vue complète des journaux d’activité Office 365. 

Vous pouvez également accéder par programme aux journaux d’activité d’Office 365 en utilisant les [API de gestion Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Étapes suivantes

- [Référence sur l’activité d’audit Azure AD](reference-audit-activities.md)
- [Référence de la rétention des rapports Azure AD](reference-reports-data-retention.md)
- [Référencent des latences de journal Azure AD](reference-reports-latencies.md)
