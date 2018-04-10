---
title: Rapports d’activité d’audit dans le portail Azure Active Directory | Microsoft Docs
description: Présentation des rapports d’activité d’audit dans le portail Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/31/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 97ea32a1e0f8815accff6201251771ab8c088859
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Rapports d’activité d’audit dans le portail Azure Active Directory 

Dans Azure Active Directory (Azure AD), vous pouvez obtenir toutes les informations dont vous avez besoin pour déterminer l’état de votre environnement.

L’architecture de création de rapports dans Azure AD comprend les composants suivants :

- **Activité** 
    - **Activités de connexion** – Informations sur l’utilisation des applications gérées et les activités de connexion des utilisateurs
    - **Activités du système** – Informations sur les activités du système liées aux utilisateurs et à la gestion des groupes, à vos applications gérées et aux activités de répertoire.
- **Sécurité** 
    - **Connexions risquées** : une connexion risquée est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur. Pour en savoir plus, consultez Connexions risquées.
    - **Utilisateurs avec indicateur de risque** : il s’agit d’un compte d’utilisateur susceptible d’être compromis. Pour en savoir plus, consultez Utilisateurs avec indicateur de risque.

Cette rubrique vous donne une vue d’ensemble des activités d’audit.
 
## <a name="who-can-access-the-data"></a>Qui peut accéder aux données ?
* Utilisateurs ayant le rôle d’administrateur de sécurité ou de lecteur de la sécurité
* Administrateurs généraux
* Les utilisateurs individuels (non administrateurs) peuvent voir leurs propres activités


## <a name="audit-logs"></a>Journaux d’audit

Les journaux d’audit dans Azure Active Directory fournissent des enregistrements des activités du système pour la conformité.  
Les **Journaux d’audit** dans la section **Activité** **d’Azure Active Directory** constituent votre premier point d’entrée pour toutes les données d’audit.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/61.png "Journaux d’Audit")

Un journal d’audit a une vue de liste par défaut qui indique :

- la date et l’heure de l’occurrence
- l’initiateur / intervenant (*qui*) d’une activité 
- l’activité (*quoi*) 
- la cible

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/18.png "Journaux d’Audit")

Vous pouvez personnaliser la vue sous forme de liste en cliquant sur **Colonnes** dans la barre d’outils.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/19.png "Journaux d’Audit")

Cela vous permet d’afficher des champs supplémentaires ou de supprimer des champs qui sont déjà affichés.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/21.png "Journaux d’Audit")


En cliquant sur un élément dans la vue sous forme de liste, vous pouvez obtenir plus d’informations sur cet élément.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/22.png "Journaux d’Audit")


## <a name="filtering-audit-logs"></a>Filtrage des journaux d’audit

Pour limiter les données transmises à un niveau qui vous convient, vous pouvez filtrer les données d’audit à l’aide des champs suivants :

- Plage de dates
- Initié par (intervenant)
- Catégorie
- Type de ressource d’activité
- Activité

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/23.png "Journaux d’Audit")


Le filtre **Plage de dates** vous permet de définir un intervalle de temps pour les données renvoyées.  
Les valeurs possibles sont les suivantes :

- 1 mois
- 7 jours
- 24 heures
- Personnalisée

Lorsque vous sélectionnez une plage personnalisée, vous pouvez configurer une heure de début et une heure de fin.

Le filtre **Initié par** vous permet de définir le nom d’un intervenant ou son nom principal universel (UPN).

Le filtre **Catégorie** vous permet de sélectionner un des filtres suivants :

- Tous
- Catégorie principale
- Répertoire principal
- Gestion des mots de passe en libre-service
- Gestion des groupes en libre service
- Approvisionnement de comptes - Substitution de mot de passe automatique
- Utilisateurs invités
- Service MIM
- Identity Protection
- B2C

Le filtre **Type de ressource d’activité** vous permet de sélectionner un des filtres suivants :

- Tous 
- Groupe
- Répertoire
- Utilisateur
- Application
- Stratégie
- Appareil
- Autres

Lorsque vous sélectionnez le **type de ressource d’activité** **Groupe**, vous obtenez une catégorie de filtre supplémentaire qui vous permet de fournir également une **source** :

- Azure AD
- O365


Le filtre **Activité** est basé sur la catégorie et le type de ressource d’activité que vous choisissez. Vous pouvez sélectionner une activité spécifique que vous souhaitez voir ou toutes les choisir. 

Vous pouvez obtenir la liste de toutes les activités d’audit à l’aide de l’API Graph https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta où $tenantdomain correspond à votre nom de domaine. Vous pouvez également consulter l’article sur [les évènements de rapports d’audit](active-directory-reporting-audit-events.md).


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

Si vous souhaitez simplement consulter les données d’audit connexes aux utilisateurs et aux groupes, vous pouvez trouver une vue filtrée sous **Journaux d’audit** dans la section **Activité** de **Utilisateurs et groupes**. Dans ce point d’entrée, **Utilisateurs et groupes** est présélectionné comme **Type de ressource d’activité**.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/93.png "Journaux d’Audit")

### <a name="enterprise-applications-audit-logs"></a>Journaux d’audit d’applications d’entreprise

Les rapports d’audit basés sur les applications vous permettent d’obtenir des réponses aux questions telles que :

* Quelles applications ont été ajoutées ou mises à jour ?
* Quelles applications ont été supprimées ?
* Le principal du service d’une application a-t-il été modifié ?
* Les noms des applications ont-ils été modifiés ?
* Qui a donné son consentement à une application ?

Si vous souhaitez simplement consulter les données d’audit connexes à vos applications, vous pouvez trouver une vue filtrée sous **Journaux d’audit** dans la section **Activité** du panneau **Applications d’entreprise**. Dans ce point d’entrée, **Applications d’entreprise** est présélectionné comme **Type de ressource d’activité**.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/134.png "Journaux d’Audit")

Vous pouvez filtrer davantage cette vue pour afficher uniquement les **groupes** ou les **utilisateurs**.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/25.png "Journaux d’Audit")



## <a name="azure-ad-audit-activity-list"></a>Liste des activités d’audit Azure AD

Cette section vous fournit une liste de toutes les activités qui peuvent être journalisées. 


|Nom du service|Catégorie d’audit|Type de ressource d’activité|Activité|
|---|---|---|---|
|Approvisionnement des comptes|Gestion des applications|Application|Administration|
|Approvisionnement des comptes|Gestion des applications|Application|Opération sur le répertoire|
|Approvisionnement des comptes|Gestion des applications|Application|Exportation|
|Approvisionnement des comptes|Gestion des applications|Application|Importer|
|Approvisionnement des comptes|Gestion des applications|Application|Autres|
|Approvisionnement des comptes|Gestion des applications|Application|Dépôt de processus|
|Approvisionnement des comptes|Gestion des applications|Application|Action de règles de synchronisation|
|Proxy d’application|Gestion des applications|Application|Ajouter l’application|
|Proxy d’application|Ressource|Ressource|Ajouter le certificat SSL de l’application|
|Proxy d’application|Ressource|Ressource|Supprimer une liaison SSL|
|Proxy d’application|Gestion des applications|Application|Supprimer l’application|
|Proxy d’application|Gestion de répertoires|Répertoire|Désactiver l’authentification unique Bureau|
|Proxy d’application|Gestion de répertoires|Répertoire|Désactiver l’authentification unique Bureau pour un domaine spécifique|
|Proxy d’application|Gestion de répertoires|Répertoire|Désactiver le proxy d’application|
|Proxy d’application|Gestion de répertoires|Répertoire|Désactiver l’authentification directe|
|Proxy d’application|Gestion de répertoires|Répertoire|Activer l’authentification unique Bureau|
|Proxy d’application|Gestion de répertoires|Répertoire|Activer l’authentification unique Bureau pour un domaine spécifique|
|Proxy d’application|Gestion de répertoires|Répertoire|Activer le proxy d’application|
|Proxy d’application|Gestion de répertoires|Répertoire|Activer l’authentification directe|
|Proxy d’application|Ressource|Ressource|Inscrire le connecteur|
|Proxy d’application|Gestion des applications|Application|Mettre à jour l’application|
|Proxy d’application|Gestion des applications|Application|Mettre à jour le mode d’authentification unique de l’application|
|Substitution de mot de passe automatique|Gestion des applications|Application|Substitution de mot de passe automatique|
|B2C|Gestion des applications|Application|Ajouter des autorisations de l’application V2|
|B2C|Authorization|Authorization|Ajouter des autorisations de l’application V2|
|B2C|Clé|Clé|Ajouter une clé basée sur un secret ASCII à un conteneur de clés CPIM|
|B2C|Authorization|Authorization|Ajouter une clé basée sur un secret ASCII à un conteneur de clés CPIM|
|B2C|Clé|Clé|Ajouter une clé à un conteneur de clés CPIM|
|B2C|Authorization|Authorization|Ajouter une clé à un conteneur de clés CPIM|
|B2C|Ressource|Ressource|AdminPolicyDatas-RemoveResources|
|B2C|Authorization|Authorization|AdminPolicyDatas-SetResources|
|B2C|Ressource|Ressource|AdminPolicyDatas-SetResources|
|B2C|Ressource|Ressource|AdminUserJourneys-GetResources|
|B2C|Authorization|Authorization|AdminUserJourneys-GetResources|
|B2C|Authorization|Authorization|AdminUserJourneys-RemoveResources|
|B2C|Ressource|Ressource|AdminUserJourneys-RemoveResources|
|B2C|Ressource|Ressource|AdminUserJourneys-SetResources|
|B2C|Authorization|Authorization|AdminUserJourneys-SetResources|
|B2C|Authorization|Authorization|Créer un IdentityProvider|
|B2C|Ressource|Ressource|Créer un IdentityProvider|
|B2C|Authorization|Authorization|Créer une application V1|
|B2C|Gestion des applications|Application|Créer une application V1|
|B2C|Gestion des applications|Application|Créer une application V2|
|B2C|Authorization|Authorization|Créer une application V2|
|B2C|Gestion de répertoires|Répertoire|Créer un domaine personnalisé dans le locataire|
|B2C|Authorization|Authorization|Créer un domaine personnalisé dans le locataire|
|B2C|Authorization|Authorization|Créer un AdminUserJourney|
|B2C|Ressource|Ressource|Créer un AdminUserJourney|
|B2C|Ressource|Ressource|Créer une ressource json localisée|
|B2C|Authorization|Authorization|Créer une ressource json localisée|
|B2C|Authorization|Authorization|Créer un IDP personnalisé|
|B2C|Ressource|Ressource|Créer un IDP personnalisé|
|B2C|Ressource|Ressource|Créer un IDP|
|B2C|Authorization|Authorization|Créer un IDP|
|B2C|Authorization|Authorization|Créer ou mettre à jour une ressource de répertoire B2C|
|B2C|Ressource|Ressource|Créer ou mettre à jour une ressource de répertoire B2C|
|B2C|Ressource|Ressource|Créer une stratégie|
|B2C|Authorization|Authorization|Créer une stratégie|
|B2C|Authorization|Authorization|Créer une stratégie trustFramework|
|B2C|Ressource|Ressource|Créer une stratégie trustFramework|
|B2C|Authorization|Authorization|Créer une stratégie trustFramework avec un préfixe configurable|
|B2C|Ressource|Ressource|Créer une stratégie trustFramework avec un préfixe configurable|
|B2C|Ressource|Ressource|Créer un attribut utilisateur|
|B2C|Authorization|Authorization|Créer un attribut utilisateur|
|B2C|Authorization|Authorization|CreateTrustFrameworkPolicy|
|B2C|Ressource|Ressource|CreateTrustFrameworkPolicy|
|B2C|Authorization|Authorization|Crée ou met à jour un AdminUserJourney|
|B2C|Ressource|Ressource|Supprimer un IDP|
|B2C|Authorization|Authorization|Supprimer un IDP|
|B2C|Ressource|Ressource|Supprimer un IdentityProvider|
|B2C|Authorization|Authorization|Supprimer un IdentityProvider|
|B2C|Gestion des applications|Application|Supprimer une application V1|
|B2C|Authorization|Authorization|Supprimer une application V1|
|B2C|Gestion des applications|Application|Supprimer une application V2|
|B2C|Authorization|Authorization|Supprimer une application V2|
|B2C|Authorization|Authorization|Supprimer une autorisation de l’application V2|
|B2C|Gestion des applications|Application|Supprimer une autorisation de l’application V2|
|B2C|Ressource|Ressource|Supprimer une ressource de répertoire B2C|
|B2C|Authorization|Authorization|Supprimer une ressource de répertoire B2C|
|B2C|Clé|Clé|Supprimer un conteneur de clés CPIM|
|B2C|Authorization|Authorization|Supprimer un conteneur de clés CPIM|
|B2C|Clé|Clé|Supprimer un conteneur de clés|
|B2C|Ressource|Ressource|Supprimer une stratégie trustFramework|
|B2C|Authorization|Authorization|Supprimer une stratégie trustFramework|
|B2C|Ressource|Ressource|Supprimer un attribut utilisateur|
|B2C|Authorization|Authorization|Supprimer un attribut utilisateur|
|B2C|Authorization|Authorization|Activer la fonctionnalité B2C|
|B2C|Gestion de répertoires|Répertoire|Activer la fonctionnalité B2C|
|B2C|Ressource|Ressource|Obtenir des ressources de répertoire B2C dans un groupe de ressources|
|B2C|Ressource|Ressource|Obtenir des ressources de répertoire B2C dans un abonnement|
|B2C|Authorization|Authorization|Obtenir des ressources de répertoire B2C dans un abonnement|
|B2C|Authorization|Authorization|Obtenir un IDP personnalisé|
|B2C|Ressource|Ressource|Obtenir un IDP personnalisé|
|B2C|Ressource|Ressource|Obtenir un IDP|
|B2C|Authorization|Authorization|Obtenir un IDP|
|B2C|Authorization|Authorization|Obtenir des applications V1 et V2|
|B2C|Gestion des applications|Application|Obtenir des applications V1 et V2|
|B2C|Authorization|Authorization|Obtenir une application V1|
|B2C|Gestion des applications|Application|Obtenir une application V1|
|B2C|Authorization|Authorization|Obtenir des applications V1|
|B2C|Gestion des applications|Application|Obtenir des applications V1|
|B2C|Gestion des applications|Application|Obtenir une application V2|
|B2C|Authorization|Authorization|Obtenir une application V2|
|B2C|Gestion des applications|Application|Obtenir des applications V2|
|B2C|Authorization|Authorization|Obtenir des applications V2|
|B2C|Ressource|Ressource|Obtenir une ressource de répertoire B2C|
|B2C|Authorization|Authorization|Obtenir une ressource de répertoire B2C|
|B2C|Authorization|Authorization|Obtenir une liste de domaines personnalisés dans le locataire|
|B2C|Gestion de répertoires|Répertoire|Obtenir une liste de domaines personnalisés dans le locataire|
|B2C|Authorization|Authorization|Obtenir un parcours utilisateur|
|B2C|Ressource|Ressource|Obtenir un parcours utilisateur|
|B2C|Ressource|Ressource|Obtenir des réclamations de l’application autorisées pour le parcours utilisateur|
|B2C|Authorization|Authorization|Obtenir des réclamations de l’application autorisées pour le parcours utilisateur|
|B2C|Ressource|Ressource|Obtenir des réclamations déclarées automatiquement autorisées pour le parcours utilisateur|
|B2C|Authorization|Authorization|Obtenir des réclamations déclarées automatiquement autorisées pour le parcours utilisateur|
|B2C|Ressource|Ressource|Obtenir des réclamations déclarées automatiquement autorisées pour la stratégie|
|B2C|Authorization|Authorization|Obtenir des réclamations déclarées automatiquement autorisées pour la stratégie|
|B2C|Ressource|Ressource|Obtenir la liste des revendications de sortie disponibles|
|B2C|Authorization|Authorization|Obtenir la liste des revendications de sortie disponibles|
|B2C|Ressource|Ressource|Obtenir les définitions de contenu pour le parcours utilisateur|
|B2C|Authorization|Authorization|Obtenir les définitions de contenu pour le parcours utilisateur|
|B2C|Authorization|Authorization|Obtenir des IDP pour un flux administrateur spécifique|
|B2C|Ressource|Ressource|Obtenir des IDP pour un flux administrateur spécifique|
|B2C|Clé|Clé|Obtenir les métadonnées de clé active du conteneur de clés au format JWK|
|B2C|Authorization|Authorization|Obtenir les métadonnées de clé active du conteneur de clés au format JWK|
|B2C|Clé|Clé|Obtenir des métadonnées de conteneur de clés|
|B2C|Ressource|Ressource|Obtenir une liste de tous les flux d’administration|
|B2C|Authorization|Authorization|Obtenir une liste de tous les flux d’administration|
|B2C|Authorization|Authorization|Obtenir une liste des balises de tous les flux d’administration pour l’ensemble des utilisateurs|
|B2C|Ressource|Ressource|Obtenir une liste des balises de tous les flux d’administration pour l’ensemble des utilisateurs|
|B2C|Ressource|Ressource|Obtenir une liste des locataires pour un utilisateur|
|B2C|Authorization|Authorization|Obtenir une liste des locataires pour un utilisateur|
|B2C|Ressource|Ressource|Obtenir des revendications déclarées automatiquement des comptes locaux|
|B2C|Authorization|Authorization|Obtenir des revendications déclarées automatiquement des comptes locaux|
|B2C|Ressource|Ressource|Obtenir une ressource json localisée|
|B2C|Authorization|Authorization|Obtenir une ressource json localisée|
|B2C|Authorization|Authorization|Obtenir des opérations du fournisseur de ressources Microsoft.AzureActiveDirectory|
|B2C|Ressource|Ressource|Obtenir des opérations du fournisseur de ressources Microsoft.AzureActiveDirectory|
|B2C|Ressource|Ressource|Obtenir des stratégies|
|B2C|Authorization|Authorization|Obtenir des stratégies|
|B2C|Ressource|Ressource|Obtenir une stratégie|
|B2C|Authorization|Authorization|Obtenir une stratégie|
|B2C|Gestion de répertoires|Répertoire|Obtenir des propriétés de ressource d’un locataire|
|B2C|Authorization|Authorization|Obtenir des propriétés de ressource d’un locataire|
|B2C|Ressource|Ressource|Obtenir une liste d’IDP pris en charge|
|B2C|Authorization|Authorization|Obtenir une liste d’IDP pris en charge|
|B2C|Ressource|Ressource|Obtenir une liste d’IDP pris en charge du parcours utilisateur|
|B2C|Authorization|Authorization|Obtenir une liste d’IDP pris en charge du parcours utilisateur|
|B2C|Gestion de répertoires|Répertoire|Obtenir des informations de locataire|
|B2C|Authorization|Authorization|Obtenir des informations de locataire|
|B2C|Gestion de répertoires|Répertoire|Obtenir des fonctionnalités autorisées de locataire|
|B2C|Authorization|Authorization|Obtenir des fonctionnalités autorisées de locataire|
|B2C|Authorization|Authorization|Obtenir une liste d’IDP personnalisés définie par le locataire|
|B2C|Ressource|Ressource|Obtenir une liste d’IDP personnalisés définie par le locataire|
|B2C|Ressource|Ressource|Obtenir une liste d’IDP définie par le locataire|
|B2C|Authorization|Authorization|Obtenir une liste d’IDP définie par le locataire|
|B2C|Ressource|Ressource|Obtenir une liste d’IDP locaux définie par le locataire|
|B2C|Authorization|Authorization|Obtenir une liste d’IDP locaux définie par le locataire|
|B2C|Ressource|Ressource|Obtenir des informations sur un locataire pour un utilisateur afin de créer des ressources|
|B2C|Authorization|Authorization|Obtenir des informations sur un locataire pour un utilisateur afin de créer des ressources|
|B2C|Authorization|Authorization|Obtenir une liste de locataires|
|B2C|Authorization|Authorization|Obtenir tenantDomains|
|B2C|Gestion de répertoires|Répertoire|Obtenir tenantDomains|
|B2C|Ressource|Ressource|Obtenir la culture par défaut prise en charge pour CPIM|
|B2C|Authorization|Authorization|Obtenir la culture par défaut prise en charge pour CPIM|
|B2C|Ressource|Ressource|Obtenir les informations d’un flux d’administration|
|B2C|Authorization|Authorization|Obtenir les informations d’un flux d’administration|
|B2C|Authorization|Authorization|Obtenir la liste des UserJourneys pour ce client|
|B2C|Ressource|Ressource|Obtenir la liste des UserJourneys pour ce locataire|
|B2C|Authorization|Authorization|Obtient l’ensemble des cultures disponibles prises en charge pour CPIM|
|B2C|Ressource|Ressource|Obtient l’ensemble des cultures disponibles prises en charge pour CPIM|
|B2C|Authorization|Authorization|Obtenir une stratégie trustFramework|
|B2C|Ressource|Ressource|Obtenir une stratégie trustFramework|
|B2C|Authorization|Authorization|Obtenir une stratégie trustFramework au format xml|
|B2C|Ressource|Ressource|Obtenir une stratégie trustFramework au format xml|
|B2C|Ressource|Ressource|Obtenir un attribut utilisateur|
|B2C|Authorization|Authorization|Obtenir un attribut utilisateur|
|B2C|Authorization|Authorization|Obtenir des attributs utilisateur|
|B2C|Ressource|Ressource|Obtenir des attributs utilisateur|
|B2C|Authorization|Authorization|Obtenir une liste de parcours utilisateur|
|B2C|Ressource|Ressource|Obtenir une liste de parcours utilisateur|
|B2C|Authorization|Authorization|GetIEFPolicies|
|B2C|Ressource|Ressource|GetIEFPolicies|
|B2C|Authorization|Authorization|GetIdentityProviders|
|B2C|Ressource|Ressource|GetIdentityProviders|
|B2C|Ressource|Ressource|GetTrustFrameworkPolicy|
|B2C|Authorization|Authorization|GetTrustFrameworkPolicy|
|B2C|Clé|Clé|Obtient un conteneur de clés CPIM au format jwk|
|B2C|Authorization|Authorization|Obtient un conteneur de clés CPIM au format jwk|
|B2C|Clé|Clé|Obtient une liste de conteneurs de clés dans le locataire|
|B2C|Authorization|Authorization|Obtient une liste de conteneurs de clés dans le locataire|
|B2C|Authorization|Authorization|Obtient le type de locataire|
|B2C|Gestion de répertoires|Répertoire|Obtient le type de locataire|
|B2C|Authentification|Authentification|Émettre un jeton d’accès à l’application|
|B2C|Authentification|Authentification|Émettre un code d’autorisation pour l’application|
|B2C|Autres|Autres|Émettre un code d’autorisation pour l’application|
|B2C|Authentification|Authentification|Émettre un id_token pour l’application|
|B2C|Autres|Autres|Émettre un id_token pour l’application|
|B2C|Authorization|Authorization|MigrateTenantMetadata|
|B2C|Ressource|Ressource|MigrateTenantMetadata|
|B2C|Ressource|Ressource|Déplacer des ressources|
|B2C|Authorization|Authorization|Corriger un IdentityProvider|
|B2C|Ressource|Ressource|Corriger un IdentityProvider|
|B2C|Ressource|Ressource|PutTrustFrameworkPolicy|
|B2C|Authorization|Authorization|PutTrustFrameworkPolicy|
|B2C|Authorization|Authorization|PutTrustFrameworkpolicy|
|B2C|Ressource|Ressource|PutTrustFrameworkpolicy|
|B2C|Ressource|Ressource|Supprimer un parcours utilisateur|
|B2C|Authorization|Authorization|Supprimer un parcours utilisateur|
|B2C|Authorization|Authorization|Restaurer une sauvegarde de conteneur de clés CPIM|
|B2C|Clé|Clé|Restaurer une sauvegarde de conteneur de clés CPIM|
|B2C|Gestion des applications|Application|Récupérer des autorisations de l’application V2|
|B2C|Authorization|Authorization|Récupérer des autorisations de l’application V2|
|B2C|Gestion des applications|Application|Récupérer des principaux de service d’application V2 dans le locataire actuel|
|B2C|Authorization|Authorization|Récupérer des principaux de service d’application V2 dans le locataire actuel|
|B2C|Clé|Clé|Enregistrer un conteneur de clés|
|B2C|Authorization|Authorization|Mettre à jour un IDP personnalisé|
|B2C|Ressource|Ressource|Mettre à jour un IDP personnalisé|
|B2C|Ressource|Ressource|Mettre à jour un IDP|
|B2C|Authorization|Authorization|Mettre à jour un IDP|
|B2C|Ressource|Ressource|Mettre à jour un IDP local|
|B2C|Authorization|Authorization|Mettre à jour un IDP local|
|B2C|Gestion des applications|Application|Mettre à jour une application V1|
|B2C|Authorization|Authorization|Mettre à jour une application V1|
|B2C|Gestion des applications|Application|Mettre à jour une application V2|
|B2C|Authorization|Authorization|Mettre à jour une application V2|
|B2C|Gestion des applications|Application|Mettre à jour une autorisation de l’application V2|
|B2C|Authorization|Authorization|Mettre à jour une autorisation de l’application V2|
|B2C|Ressource|Ressource|Mettre à jour une ressource de répertoire B2C|
|B2C|Ressource|Ressource|Mettre à jour la stratégie|
|B2C|Authorization|Authorization|Mettre à jour la stratégie|
|B2C|Ressource|Ressource|Mettre à jour un état d’abonnement|
|B2C|Ressource|Ressource|Mettre à jour un attribut utilisateur|
|B2C|Authorization|Authorization|Mettre à jour un attribut utilisateur|
|B2C|Clé|Clé|Charger une clé chiffrée CPIM|
|B2C|Authorization|Authorization|Charger une clé chiffrée CPIM|
|B2C|Authorization|Authorization|Autorisation utilisateur : l’API est désactivée pour l’ensemble de fonctionnalités du locataire|
|B2C|Authorization|Authorization|Autorisation utilisateur : accès accordé à l’utilisateur en tant que « Tenant Admin »|
|B2C|Authorization|Authorization|Autorisation utilisateur : les droits d’accès « Authenticated Users » ont été accordés à l’utilisateur|
|B2C|Authentification|Authentification|Valider les informations d’identification de compte local|
|B2C|Ressource|Ressource|Valider le déplacement des ressources|
|B2C|Authentification|Authentification|Valider l’authentification de l’utilisateur|
|B2C|Gestion de répertoires|Répertoire|Vérifier si la fonctionnalité B2C est activée|
|B2C|Authorization|Authorization|Vérifier si la fonctionnalité B2C est activée|
|B2C|Authorization|Authorization|Vérifier si la fonctionnalité est activée|
|B2C|Gestion de répertoires|Répertoire|Vérifier si la fonctionnalité est activée|
|Annuaire principal|Gestion des applications|Application|Ajouter OAuth2PermissionGrant|
|Annuaire principal|Gestion des unités administratives|AdministrativeUnit|Ajouter une unité administrative|
|Annuaire principal|User Management|Utilisateur|Ajouter une autorisation d’attribution de rôle d’application à un utilisateur|
|Annuaire principal|Gestion des groupes|Groupe|Ajouter une attribution de rôle d’application à un groupe|
|Annuaire principal|Gestion des applications|Application|Ajouter une attribution de rôle d’application à un principal de service|
|Annuaire principal|Gestion des applications|Application|Ajouter l’application|
|Annuaire principal|Ressource|Ressource|Ajouter un appareil|
|Annuaire principal|Ressource|Ressource|Ajouter une configuration d’appareil|
|Annuaire principal|Gestion des rôles|Rôle|Ajouter un membre éligible au rôle|
|Annuaire principal|Gestion des groupes|Groupe|Ajouter un groupe|
|Annuaire principal|Gestion des unités administratives|AdministrativeUnit|Ajouter un membre à une unité administrative|
|Annuaire principal|Gestion des groupes|Groupe|Ajouter un membre à un groupe|
|Annuaire principal|Gestion des rôles|Rôle|Ajouter un membre à un rôle|
|Annuaire principal|Gestion des applications|Application|Ajouter un propriétaire à une application|
|Annuaire principal|Gestion des groupes|Groupe|Ajouter un propriétaire à un groupe|
|Annuaire principal|Gestion des stratégies|Stratégie|Ajouter un propriétaire à une stratégie|
|Annuaire principal|Gestion des applications|Application|Ajouter un propriétaire à un principal de service|
|Annuaire principal|Gestion de répertoires|Répertoire|Ajouter un partenaire à l'entreprise|
|Annuaire principal|Gestion des stratégies|Stratégie|Add policy|
|Annuaire principal|Gestion des applications|Application|Ajouter une stratégie à un principal de service|
|Annuaire principal|Ressource|Ressource|Ajouter un propriétaire enregistré à un appareil|
|Annuaire principal|Ressource|Ressource|Ajouter des utilisateurs enregistrés à un appareil|
|Annuaire principal|Gestion des rôles|Rôle|Ajouter une attribution de rôle à une définition de rôle|
|Annuaire principal|Gestion des rôles|Rôle|Ajouter un rôle à partir d’un modèle|
|Annuaire principal|Gestion des rôles|Rôle|Ajouter un membre inclus dans une étendue à un rôle|
|Annuaire principal|Gestion des applications|Application|Ajouter un principal du service|
|Annuaire principal|Gestion des applications|Application|Ajouter les informations d'identification du principal du service|
|Annuaire principal|Gestion de répertoires|Répertoire|Ajouter un domaine non vérifié|
|Annuaire principal|User Management|Utilisateur|Ajouter un utilisateur|
|Annuaire principal|User Management|Utilisateur|Ajouter des informations d’application de téléphone d’authentification renforcée pour des utilisateurs|
|Annuaire principal|Gestion de répertoires|Répertoire|Ajouter un domaine vérifié|
|Annuaire principal|User Management|Utilisateur|Modifier la licence de l'utilisateur|
|Annuaire principal|User Management|Utilisateur|Modifier le mot de passe de l'utilisateur|
|Annuaire principal|Gestion des applications|Application|Consentement pour une application|
|Annuaire principal|User Management|Utilisateur|Convertir un utilisateur fédéré en utilisateur géré|
|Annuaire principal|User Management|Utilisateur|Créer un mot de passe d’application pour un utilisateur|
|Annuaire principal|Gestion de répertoires|Répertoire|Créer une entreprise|
|Annuaire principal|Gestion de répertoires|Répertoire|Création de paramètres d’entreprise.|
|Annuaire principal|Gestion des groupes|Groupe|Créer des paramètres de groupe|
|Annuaire principal|Gestion des unités administratives|AdministrativeUnit|Supprimer une unité administrative|
|Annuaire principal|Gestion des applications|Application|Supprimer l’application|
|Annuaire principal|User Management|Utilisateur|Supprimer un mot de passe d’application pour un utilisateur|
|Annuaire principal|Gestion de répertoires|Répertoire|Suppression de paramètres d’entreprise.|
|Annuaire principal|Ressource|Ressource|Supprimer un appareil|
|Annuaire principal|Ressource|Ressource|Supprimer la configuration de l’appareil|
|Annuaire principal|Gestion des groupes|Groupe|Supprimer un groupe|
|Annuaire principal|Gestion des groupes|Groupe|Supprimer des paramètres de groupe|
|Annuaire principal|Gestion des stratégies|Stratégie|Supprimer la stratégie|
|Annuaire principal|User Management|Utilisateur|Supprimer un utilisateur|
|Annuaire principal|Gestion de répertoires|Répertoire|Rétrograder un partenaire|
|Annuaire principal|Ressource|Ressource|Un appareil n’est plus conforme|
|Annuaire principal|Ressource|Ressource|Un appareil n’est plus géré|
|Annuaire principal|Gestion de répertoires|Répertoire|Répertoire supprimé|
|Annuaire principal|Gestion de répertoires|Répertoire|Répertoire supprimé définitivement|
|Annuaire principal|Gestion de répertoires|Répertoire|Suppression du répertoire planifiée|
|Annuaire principal|User Management|Utilisateur|Désactiver un compte|
|Annuaire principal|User Management|Utilisateur|Activer l’authentification renforcée|
|Annuaire principal|Gestion des groupes|Groupe|Terminer l’application d’une licence basée sur le groupe à des utilisateurs|
|Annuaire principal|Gestion des applications|Application|Supprimer définitivement une application|
|Annuaire principal|Gestion des groupes|Groupe|Supprimer définitivement un groupe|
|Annuaire principal|User Management|Utilisateur|Supprimer définitivement un utilisateur|
|Annuaire principal|Gestion de répertoires|Répertoire|Promouvoir une entreprise auprès d’un partenaire|
|Annuaire principal|Gestion de répertoires|Répertoire|Vider des propriétés de gestion des droits|
|Annuaire principal|Gestion des applications|Application|Supprimer OAuth2PermissionGrant|
|Annuaire principal|Gestion des groupes|Groupe|Supprimer une attribution de rôle d’application d’un groupe|
|Annuaire principal|Gestion des applications|Application|Supprimer une attribution de rôle d’application d’un principal de service|
|Annuaire principal|User Management|Utilisateur|Supprimer une attribution de rôle d’application d’un utilisateur|
|Annuaire principal|Gestion des rôles|Rôle|Supprimer un membre éligible d’un rôle|
|Annuaire principal|Gestion des unités administratives|AdministrativeUnit|Supprimer un membre d’une unité administrative|
|Annuaire principal|Gestion des groupes|Groupe|Supprimer un membre d’un groupe|
|Annuaire principal|Gestion des rôles|Rôle|Supprimer un membre d’un rôle|
|Annuaire principal|Gestion des applications|Application|Supprimer un propriétaire d’une application|
|Annuaire principal|Gestion des groupes|Groupe|Supprimer un propriétaire d’un groupe|
|Annuaire principal|Gestion des applications|Application|Supprimer un propriétaire d’un principal de service|
|Annuaire principal|Gestion de répertoires|Répertoire|Supprimer un partenaire d’une entreprise|
|Annuaire principal|Gestion des stratégies|Stratégie|Supprimer des informations d’identification de stratégie|
|Annuaire principal|Gestion des applications|Application|Supprimer une stratégie d’un principal de service|
|Annuaire principal|Ressource|Ressource|Supprimer un propriétaire enregistré d’un appareil|
|Annuaire principal|Ressource|Ressource|Supprimer des utilisateurs enregistrés d’un appareil|
|Annuaire principal|Gestion des rôles|Rôle|Supprimer une attribution de rôle d’une définition de rôle|
|Annuaire principal|Gestion des rôles|Rôle|Supprimer un membre inclus dans une étendue d’un rôle|
|Annuaire principal|Gestion des applications|Application|Supprimer le principal du service|
|Annuaire principal|Gestion des applications|Application|Supprimer les informations d'identification du principal du service|
|Annuaire principal|Gestion de répertoires|Répertoire|Supprimer un domaine non vérifié|
|Annuaire principal|User Management|Utilisateur|Supprimer des informations d’application de téléphone d’authentification renforcée pour des utilisateurs|
|Annuaire principal|Gestion de répertoires|Répertoire|Supprimer un domaine vérifié|
|Annuaire principal|User Management|Utilisateur|Réinitialiser le mot de passe de l'utilisateur|
|Annuaire principal|Gestion des groupes|Groupe|Restaurer un groupe|
|Annuaire principal|Gestion des applications|Application|Restaurer une application|
|Annuaire principal|User Management|Utilisateur|Restaurer un utilisateur|
|Annuaire principal|Gestion des applications|Application|Révoquer un consentement|
|Annuaire principal|Gestion de répertoires|Répertoire|Définir les informations de l'entreprise|
|Annuaire principal|Gestion de répertoires|Répertoire|Définir la fonctionnalité DirSync|
|Annuaire principal|Gestion de répertoires|Répertoire|Définir l’indicateur DirSyncEnabled|
|Annuaire principal|Gestion de répertoires|Répertoire|Définition d’un partenariat|
|Annuaire principal|Gestion de répertoires|Répertoire|Définir un seuil de suppression accidentelle|
|Annuaire principal|Gestion de répertoires|Répertoire|Définir un emplacement de données autorisé|
|Annuaire principal|Gestion de répertoires|Répertoire|Définir l’activation de la fonctionnalité multinationale|
|Annuaire principal|Gestion de répertoires|Répertoire|Définir la fonctionnalité de répertoire sur un locataire|
|Annuaire principal|Gestion de répertoires|Répertoire|Définir l'authentification de domaine|
|Annuaire principal|Gestion de répertoires|Répertoire|Définir les paramètres de fédération du domaine|
|Annuaire principal|User Management|Utilisateur|Définir le mot de passe utilisateur|
|Annuaire principal|Gestion des groupes|Groupe|Définir une licence de groupe|
|Annuaire principal|Gestion des groupes|Groupe|Définition d’un groupe à gérer par un utilisateur.|
|Annuaire principal|Gestion de répertoires|Répertoire|Définir une stratégie de mot de passe|
|Annuaire principal|Gestion de répertoires|Répertoire|Définir des propriétés de gestion des droits|
|Annuaire principal|User Management|Utilisateur|Définir un gestionnaire d’utilisateurs|
|Annuaire principal|User Management|Utilisateur|Définir l’activation des métadonnées de jeton oath d’utilisateurs|
|Annuaire principal|Gestion des groupes|Groupe|Commencer à appliquer une licence basée sur un groupe à des utilisateurs|
|Annuaire principal|Gestion des groupes|Groupe|Déclencher un nouveau calcul de licence de groupe|
|Annuaire principal|User Management|Utilisateur|Mettre à jour un timestamp StsRefreshTokenValidFrom|
|Annuaire principal|Gestion des unités administratives|AdministrativeUnit|Mettre à jour une unité administrative|
|Annuaire principal|Gestion des applications|Application|Mettre à jour l’application|
|Annuaire principal|Gestion de répertoires|Répertoire|Mettre à jour une entreprise|
|Annuaire principal|Gestion de répertoires|Répertoire|Mettre à jour des paramètres d’entreprise|
|Annuaire principal|Ressource|Ressource|Mettre à jour un appareil|
|Annuaire principal|Ressource|Ressource|Mettre à jour une configuration d’appareil|
|Annuaire principal|Gestion de répertoires|Répertoire|Mettre à jour le domaine|
|Annuaire principal|User Management|Utilisateur|Mise à jour de clés secrètes externes.|
|Annuaire principal|Gestion des applications|Application|Mise à jour de clés secrètes externes.|
|Annuaire principal|Gestion des groupes|Groupe|Mettre à jour un groupe|
|Annuaire principal|Gestion des groupes|Groupe|Mettre à jour des paramètres d’un groupe|
|Annuaire principal|Gestion des stratégies|Stratégie|Mettre à jour la stratégie|
|Annuaire principal|Gestion des rôles|Rôle|Mettre à jour un rôle|
|Annuaire principal|Gestion des applications|Application|Mettre à jour un principal de service|
|Annuaire principal|User Management|Utilisateur|Mettre à jour l'utilisateur|
|Annuaire principal|Gestion de répertoires|Répertoire|Vérifier le domaine|
|Annuaire principal|Gestion de répertoires|Répertoire|Vérifier le domaine vérifié par courrier électronique|
|Identity Protection|User Management|Utilisateur|Un administrateur génère un mot de passe temporaire|
|Identity Protection|User Management|Utilisateur|L’administrateur demande à l’utilisateur de réinitialiser son mot de passe|
|Identity Protection|Autres|Autres|Télécharger un type d’événement à risque unique|
|Identity Protection|Autres|Autres|Télécharger les administrateurs et l’état d’acceptation de synthèse hebdomadaire|
|Identity Protection|Autres|Autres|Télécharger tous les types d’événements à risque|
|Identity Protection|Autres|Autres|Télécharger les événements à risque d’utilisateur gratuit|
|Identity Protection|Autres|Autres|Télécharger les utilisateurs avec indicateur de risque|
|Identity Protection|Gestion de répertoires|Répertoire|Mise en route|
|Identity Protection|Gestion des stratégies|Stratégie|Définir la stratégie d’inscription MFA|
|Identity Protection|Gestion des stratégies|Stratégie|Définir une stratégie en matière de risque à la connexion|
|Identity Protection|Gestion des stratégies|Stratégie|Définir une stratégie en matière de risque d’utilisateur|
|Identity Protection|Gestion de répertoires|Répertoire|Mettre à jour des paramètres d’alerte|
|Identity Protection|Gestion de répertoires|Répertoire|Mettre à jour des paramètres de synthèse hebdomadaire|
|Utilisateurs invités|User Management|Utilisateur|Assigner un utilisateur externe à une application|
|Utilisateurs invités|Autres|Autres|Lot d’invitations traité|
|Utilisateurs invités|Autres|Autres|Lot d’invitations chargé|
|Utilisateurs invités|User Management|Utilisateur|E-mail non envoyé, utilisateur désabonné|
|Utilisateurs invités|User Management|Utilisateur|Inviter un utilisateur externe|
|Utilisateurs invités|User Management|Utilisateur|Utiliser une invitation d’utilisateur externe|
|Utilisateurs invités|User Management|Utilisateur|Création d’un locataire viral|
|Utilisateurs invités|User Management|Utilisateur|Création d’un utilisateur viral|
|Microsoft Identity Manager (MIM)|Gestion des groupes|Groupe|Ajout d’un membre|
|Microsoft Identity Manager (MIM)|Gestion des groupes|Groupe|Créer un groupe|
|Microsoft Identity Manager (MIM)|Gestion des groupes|Groupe|Suppression d’un groupe|
|Microsoft Identity Manager (MIM)|Gestion des groupes|Groupe|Suppression d’un membre|
|Microsoft Identity Manager (MIM)|Gestion des groupes|Groupe|Mise à jour d’un groupe|
|Microsoft Identity Manager (MIM)|User Management|Utilisateur|Enregistrement de mot de passe utilisateur|
|Microsoft Identity Manager (MIM)|User Management|Utilisateur|Réinitialisation de mot de passe utilisateur|
|Privileged Identity Management|Gestion des rôles|Rôle|AccessReview_Review|
|Privileged Identity Management|Gestion des rôles|Rôle|AccessReview_Update|
|Privileged Identity Management|Gestion des rôles|Rôle|ActivationAborted|
|Privileged Identity Management|Gestion des rôles|Rôle|ActivationApproved|
|Privileged Identity Management|Gestion des rôles|Rôle|ActivationCanceled|
|Privileged Identity Management|Gestion des rôles|Rôle|ActivationRequested|
|Privileged Identity Management|Gestion des rôles|Rôle|Ajouté|
|Privileged Identity Management|Gestion des rôles|Rôle|Assigner|
|Privileged Identity Management|Gestion des rôles|Rôle|Élever|
|Privileged Identity Management|Gestion des rôles|Rôle|Supprimé|
|Privileged Identity Management|Gestion des rôles|Rôle|Modifications des paramètres de rôle|
|Privileged Identity Management|Gestion des rôles|Rôle|ScanAlertsNow|
|Privileged Identity Management|Gestion des rôles|Rôle|Connecter|
|Privileged Identity Management|Gestion des rôles|Rôle|Baisser les privilèges|
|Privileged Identity Management|Gestion des rôles|Rôle|UpdateAlertSettings|
|Privileged Identity Management|Gestion des rôles|Rôle|UpdateCurrentState|
|Gestion des groupes en libre-service|Gestion des groupes|Groupe|Approuver une demande en attente pour rejoindre un groupe|
|Gestion des groupes en libre-service|Gestion des groupes|Groupe|Annuler une demande en attente pour rejoindre un groupe|
|Gestion des groupes en libre-service|Gestion des groupes|Groupe|Créer une stratégie de gestion du cycle de vie|
|Gestion des groupes en libre-service|Gestion des groupes|Groupe|Supprimer une demande en attente pour rejoindre un groupe|
|Gestion des groupes en libre-service|Gestion des groupes|Groupe|Rejeter une demande en attente pour rejoindre un groupe|
|Gestion des groupes en libre-service|Gestion des groupes|Groupe|Renouveler un groupe|
|Gestion des groupes en libre-service|Gestion des groupes|Groupe|Demander à rejoindre un groupe|
|Gestion des groupes en libre-service|Gestion des groupes|Groupe|Définir des propriétés de groupe dynamiques|
|Gestion des groupes en libre-service|Gestion des groupes|Groupe|Mettre à jour une stratégie de gestion du cycle de vie|
|Gestion des mots de passe en libre-service|User Management|Utilisateur|Blocage suite à une réinitialisation de mot de passe en libre-service|
|Gestion des mots de passe en libre-service|User Management|Utilisateur|Modifier un mot de passe (libre-service)|
|Gestion des mots de passe en libre-service|Gestion de répertoires|Répertoire|Désactiver la réécriture de mot de passe pour un répertoire|
|Gestion des mots de passe en libre-service|Gestion de répertoires|Répertoire|Activer la réécriture de mot de passe pour un répertoire|
|Gestion des mots de passe en libre-service|User Management|Utilisateur|Réinitialiser un mot de passe (par l’administrateur)|
|Gestion des mots de passe en libre-service|User Management|Utilisateur|Réinitialiser un mot de passe (libre-service)|
|Gestion des mots de passe en libre-service|User Management|Utilisateur|Progression de l’activité du flux de réinitialisation de mot de passe en libre-service|
|Gestion des mots de passe en libre-service|User Management|Utilisateur|Progression de l’activité du flux de réinitialisation de mot de passe en libre-service|
|Gestion des mots de passe en libre-service|User Management|Utilisateur|Déverrouiller un compte d’utilisateur (libre-service)|
|Gestion des mots de passe en libre-service|User Management|Utilisateur|Utilisateur enregistré pour la réinitialisation de mot de passe en libre-service|
|Conditions d’utilisation|Gestion des stratégies|Stratégie|Accepter des conditions d’utilisation|
|Conditions d’utilisation|Gestion des stratégies|Stratégie|Créer des conditions d’utilisation|
|Conditions d’utilisation|Gestion des stratégies|Stratégie|Refuser des conditions d’utilisation|
|Conditions d’utilisation|Gestion des stratégies|Stratégie|Supprimer des conditions d’utilisation|
|Conditions d’utilisation|Gestion des stratégies|Stratégie|Modifier des conditions d’utilisation|
|Conditions d’utilisation|Gestion des stratégies|Stratégie|Publier des conditions d’utilisation|
|Conditions d’utilisation|Gestion des stratégies|Stratégie|Annuler la publication des conditions d’utilisation|




## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble des rapports, consultez [Création de rapports Azure Active Directory](active-directory-reporting-azure-portal.md).

