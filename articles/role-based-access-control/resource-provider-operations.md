---
title: Opérations du fournisseur Azure Resource Manager | Microsoft Docs
description: Décrit en détails les opérations disponibles sur les fournisseurs de ressources Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.openlocfilehash: 80724a24fe7cf2565334a5212a0877589eb1eecf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Opérations du fournisseur de ressources Azure Resource Manager

Ce document répertorie les opérations disponibles pour chaque fournisseur de ressources Microsoft Azure Resource Manager. Celles-ci peuvent être utilisées dans des rôles personnalisés pour fournir des autorisations de contrôle d’accès en fonction du rôle (RBAC) granulaires aux ressources dans Azure. Notez que cette liste n’est pas exhaustive et que des opérations peuvent être ajoutées ou supprimées à mesure que chaque fournisseur est mis à jour. Les chaînes d’opération sont au format `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. 

> [!NOTE]
> Pour obtenir une liste complète et actuelle, utilisez `Get-AzureRmProviderOperation` (dans PowerShell) ou `az provider operation list` (dans Azure CLI v2) pour répertorier les opérations des fournisseurs de ressources Azure.

## <a name="microsoftaad"></a>Microsoft.AAD

| Opération | Description |
|---|---|
|/domainServices/delete|Supprime les services de domaine|
|/domainServices/read|Lit les services de domaine|
|/domainServices/write|Écrit les services de domaine|
|/locations/operationresults/read|Lit l’état d’une opération asynchrone|
|/Operations/read|Description conviviale localisée pour l’opération, telle qu’elle doit s’afficher à l’utilisateur.|

## <a name="microsoftaadiam"></a>microsoft.aadiam

| Opération | Description |
|---|---|
|/diagnosticsettings/delete|Désactive un paramètre de diagnostic|
|/diagnosticsettings/read|Lit un paramètre de diagnostic|
|/diagnosticsettings/write|Écrit un paramètre de diagnostic|
|/diagnosticsettingscategories/read|Lit les catégories d’un paramètre de diagnostic|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/tenants/providers/Microsoft.Insights/logDefinitions/read|Obtient les journaux disponibles pour les clients|

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Opération | Description |
|---|---|
|/configuration/action|Met à jour la configuration du client.|
|/configuration/read|Lit la configuration du client.|
|/configuration/write|Crée une configuration de client.|
|/services/action|Met à jour une instance de service dans le client.|
|/services/alerts/read|Lit les alertes pour un service.|
|/services/alerts/read|Lit les alertes pour un service.|
|/services/delete|Supprime une instance de service dans le client.|
|/services/read|Lit les instances de service dans le client.|
|/services/servicemembers/action|Crée une instance de membre de service dans le service.|
|/services/servicemembers/alerts/read|Lit les alertes pour un membre de service.|
|/services/servicemembers/delete|Supprime une instance de membre de service dans le service.|
|/services/servicemembers/read|Lit l’instance de membre de service dans le service.|
|/services/write|Crée une instance de service dans le client.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Opération | Description |
|---|---|
|/configurations/read|Obtenir des configurations|
|/configurations/write|Crée/met à jour la configuration|
|/generateRecommendations/action|Génère des recommandations|
|/generateRecommendations/read|Obtient l’état de génération de recommandations|
|/operations/read|Obtient les opérations pour le Microsoft Advisor|
|/recommendations/read|Lit les recommandations|
|/recommendations/suppressions/delete|Supprime la suppression|
|/recommendations/suppressions/read|Obtient les suppressions|
|/recommendations/suppressions/write|Crée/met à jour des suppressions|
|/register/action|Inscrit l’abonnement pour Microsoft Advisor|
|/suppressions/delete|Supprime la suppression|
|/suppressions/read|Obtient les suppressions|
|/suppressions/write|Crée/met à jour des suppressions|
|/unregister/action|Désinscrit l’abonnement pour le Microsoft Advisor|

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

| Opération | Description |
|---|---|
|/alerts/read|Obtient toutes les alertes pour les filtres d’entrée|
|/alerts/resolve/action|Modifie l’état de l’alerte en Résoudre|
|/alertsSummary/read|Obtient le résumé des alertes|
|/Operations/read|Lit les opérations fournies|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Opération | Description |
|---|---|
|/locations/checkNameAvailability/action|Vérifie que le nom d’Analysis Server donné est valide et non utilisé.|
|/locations/operationresults/read|Récupère les informations du résultat de l’opération spécifiée|
|/locations/operationstatuses/read|Récupère les informations de l’état de l’opération spécifiée|
|/operations/read|Récupère les informations d’opérations|
|/register/action|Inscrit le fournisseur de ressources Analysis Services|
|/servers/delete|Supprime l’Analysis Server.|
|/servers/listGatewayStatus/action|Affiche l’état de la passerelle associée au serveur|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour Analysis Server|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour Analysis Server|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Obtient les journaux disponibles pour la ressource|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour Analysis Server|
|/servers/read|Récupère les informations de l’Analysis Server spécifié.|
|/servers/resume/action|Reprend l’Analysis Server.|
|/servers/skus/read|Récupère les informations de référence (SKU) disponibles pour le serveur|
|/servers/suspend/action|Suspend l’Analysis Server.|
|/servers/write|Crée ou met à jour l’Analysis Server spécifié.|
|/skus/read|Récupère les informations de références (SKU)|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Opération | Description |
|---|---|
|/checkNameAvailability/read|Vérifie si le nom du service indiqué est disponible|
|/operations/read|Lit toutes les opérations API disponibles pour la ressource Microsoft.ApiManagement|
|/register/action|Inscrire l’abonnement pour le fournisseur de ressources Microsoft.ApiManagement|
|/reports/read|Obtient des rapports agrégés par période de temps, région géographique, développeur, produit, API, opération, abonnement et demande.|
|/service/apis/delete|Supprimer l’API existante|
|/service/apis/diagnostics/delete|Supprime le diagnostic existant|
|/service/apis/diagnostics/loggers/delete|Supprime le mappage d’un enregistreur d’événements avec un paramètre de diagnostic|
|/service/apis/diagnostics/loggers/read|Obtient la liste des enregistreurs d’événements de diagnostic existants|
|/service/apis/diagnostics/loggers/write|Mappe un enregistreur d’événements à un paramètre de diagnostic|
|/service/apis/diagnostics/read|Obtient la liste des diagnostics ou obtient les détails d’un diagnostic|
|/service/apis/diagnostics/write|Ajoute nouveau diagnostic ou met à jour les détails du diagnostic existant|
|/service/apis/operations/delete|Supprimer l’opération d’API existante|
|/service/apis/operations/policies/delete|Supprime la configuration de stratégie des stratégies d’opération API|
|/service/apis/operations/policies/read|Obtient les stratégies pour l’opération API ou les détails de configuration de stratégie pour l’opération API|
|/service/apis/operations/policies/write|Définit les détails de configuration de l’opération API|
|/service/apis/operations/policy/delete|Supprimer la configuration de la stratégie de l’opération|
|/service/apis/operations/policy/read|Obtenir les détails de configuration de la stratégie pour l’opération|
|/service/apis/operations/policy/write|Définir les détails de configuration de la stratégie pour l’opération|
|/service/apis/operations/read|Obtenir la liste des opérations d’API existantes ou obtenir les détails de l’opération d’API|
|/service/apis/operations/tags/delete|Supprime l’association d’une balise existante avec une opération existante|
|/service/apis/operations/tags/read|Obtient les balises associées à l’opération ou les détails d’une balise|
|/service/apis/operations/tags/write|Associe une balise existante à une opération existante|
|/service/apis/operations/write|Créer une nouvelle opération d’API ou mettre à jour une opération d’API existante|
|/service/apis/operationsByTags/read|Obtient la liste des associations opération/balise|
|/service/apis/policies/delete|Supprime la configuration de stratégie des stratégies d’API|
|/service/apis/policies/read|Obtient les stratégies ou les détails de configuration de stratégie pour l’API|
|/service/apis/policies/write|Définir les détails de configuration de la stratégie pour l’API|
|/service/apis/policy/delete|Supprimer la configuration de la stratégie de l’API|
|/service/apis/policy/read|Obtenir les détails de configuration de la stratégie pour l’API|
|/service/apis/policy/write|Définir les détails de configuration de la stratégie pour l’API|
|/service/apis/products/read|Obtient tous les produits dont l’API fait partie|
|/service/apis/read|Obtenir la liste de toutes les API inscrites ou obtenir les détails de l’API|
|/service/apis/releases/delete|Supprime toutes les mises en production de l’API ou une mise en production de l’API|
|/service/apis/releases/read|Obtient les mises en production pour une API ou les détails d’une mise en production de l’API|
|/service/apis/releases/write|Crée une mise en production de l’API ou met à jour une mise en production existante de l’API|
|/service/apis/revisions/delete|Supprime toutes les révisions d’une API|
|/service/apis/revisions/read|Obtient les révisions appartenant à une API|
|/service/apis/schemas/delete|Supprime un schéma existant|
|/service/apis/schemas/document/read|Obtient le document décrivant le schéma|
|/service/apis/schemas/document/write|Met à jour le document décrivant le schéma|
|/service/apis/schemas/read|Obtient tous les schémas pour une API donnée ou les schémas utilisés par l’API|
|/service/apis/schemas/write|Définit les schémas utilisés par l’API|
|/service/apis/tagDescriptions/delete|Supprime la description de balise de l’API|
|/service/apis/tagDescriptions/read|Obtient les descriptions de balises ou une description de balise dans l’étendue d’une API|
|/service/apis/tagDescriptions/write|Crée/modifie une description de balise dans l’étendue de l’API|
|/service/apis/tags/delete|Supprime une association API/balise existante|
|/service/apis/tags/read|Obtient toutes les associations API/balise pour l’API ou les détails d’une association API/balise|
|/service/apis/tags/write|Ajoute une nouvelle association API/balise|
|/service/apis/write|Créer une nouvelle API ou mettre à jour les détails de l’API existante|
|/service/apisByTags/read|Obtient la liste des associations API/balise|
|/service/api-version-sets/delete|Supprime un VersionSet existant|
|/service/api-version-sets/read|Obtient la liste des entités d’un groupe de version ou les détails d’un VersionSet|
|/service/api-version-sets/versions/read|Obtient la liste des entités de version|
|/service/api-version-sets/write|Crée un VersionSet ou met à jour les détails d’un VersionSet existant|
|/service/applynetworkconfigurationupdates/action|Met à jour les ressources Microsoft.ApiManagement exécutées dans le réseau virtuel pour sélectionner les paramètres réseau mis à jour.|
|/service/authorizationServers/delete|Supprimer le serveur d’autorisation existant|
|/service/authorizationServers/read|Obtenir la liste des serveurs d’autorisation ou obtenir les détails du serveur d’autorisation|
|/service/authorizationServers/write|Créer un nouveau serveur d’autorisation ou mettre à jour les détails d’un serveur d’autorisation existant|
|/service/backends/delete|Supprimer le serveur principal existant|
|/service/backends/read|Obtenir la liste des serveurs principaux ou obtenir les détails du serveur principal|
|/service/backends/reconnect/action|Crée une demande de reconnexion|
|/service/backends/write|Ajouter un nouveau serveur principal ou mettre à jour les détails du serveur principal existant|
|/service/backup/action|Sauvegarder le service Gestion des API dans le conteneur spécifié dans un compte de stockage fourni par l’utilisateur|
|/service/certificates/delete|Supprimer le certificat existant|
|/service/certificates/read|Obtenir la liste des certificats ou obtenir les détails du certificat|
|/service/certificates/write|Ajouter un nouveau certificat|
|/service/delete|Supprimer l’instance du service Gestion des API|
|/service/diagnostics/delete|Supprime le diagnostic existant|
|/service/diagnostics/loggers/delete|Supprime le mappage d’un enregistreur d’événements avec un paramètre de diagnostic|
|/service/diagnostics/loggers/read|Obtient la liste des enregistreurs d’événements de diagnostic existants|
|/service/diagnostics/loggers/write|Mappe un enregistreur d’événements à un paramètre de diagnostic|
|/service/diagnostics/read|Obtient la liste des diagnostics ou obtient les détails d’un diagnostic|
|/service/diagnostics/write|Ajoute nouveau diagnostic ou met à jour les détails du diagnostic existant|
|/service/getssotoken/action|Obtient le jeton d’authentification unique qui peut être utilisé pour se connecter au portail hérité du service Gestion des API en tant qu’administrateur|
|/service/groups/delete|Supprimer le groupe existant|
|/service/groups/read|Obtenir la liste des groupes ou obtient les détails d’un groupe|
|/service/groups/users/delete|Supprimer un utilisateur existant d’un groupe existant|
|/service/groups/users/read|Obtenir la liste des utilisateurs du groupe|
|/service/groups/users/write|Ajouter un utilisateur existant à un groupe existant|
|/service/groups/write|Créer un nouveau groupe ou mettre à jour les détails du groupe existant|
|/service/identityProviders/delete|Supprimer le fournisseur d’identité existant|
|/service/identityProviders/read|Obtenir la liste des fournisseurs d’identité ou obtenir les détails du fournisseur d’identité|
|/service/identityProviders/write|Créer un nouveau fournisseur d’identité ou mettre à jour les détails d’un fournisseur d’identité existant|
|/service/locations/networkstatus/read|Obtient l’état de l’accès réseau des ressources dont le service dépend dans l’emplacement|
|/service/loggers/delete|Supprimer l’enregistreur existant|
|/service/loggers/read|Obtenir la liste des enregistreurs ou obtenir les détails d’un enregistreur|
|/service/loggers/write|Ajouter un nouvel enregistreur ou mettre à jour les détails d’un enregistreur existant|
|/service/managedeployments/action|Modifier une référence/unité, ajouter/supprimer des déploiements régionaux du service Gestion des API|
|/service/networkstatus/read|Obtient l’état de l’accès réseau des ressources dont le service dépend|
|/service/notifications/action|Envoie une notification à un utilisateur spécifié|
|/service/notifications/read|Obtient toutes les notifications de l’éditeur Gestion des API ou des détails de notification de l’éditeur Gestion des API|
|/service/notifications/recipientEmails/delete|Supprime un e-mail associé à une notification|
|/service/notifications/recipientEmails/read|Obtient les destinataires associés à une notification par e-mail de l’éditeur Gestion des API|
|/service/notifications/recipientEmails/write|Crée un destinataire de la notification par e-mail|
|/service/notifications/recipientUsers/delete|Supprime l’utilisateur associé aux destinataires de la notification|
|/service/notifications/recipientUsers/read|Obtient les utilisateurs destinataires associés à la notification|
|/service/notifications/recipientUsers/write|Ajoute l’utilisateur aux destinataires de la notification|
|/service/notifications/write|Crée ou met à jour une notification de l’éditeur Gestion des API|
|/service/openidConnectProviders/delete|Supprimer le fournisseur OpenID Connect existant|
|/service/openidConnectProviders/read|Obtenir la liste des fournisseurs OpenID Connect ou obtenir les détails du fournisseur OpenID Connect|
|/service/openidConnectProviders/write|Créer un nouveau fournisseur OpenID Connect ou mettre à jour les détails d’un fournisseur OpenID Connect existant|
|/service/operationresults/read|Obtient l’état actuel d’une opération longue|
|/service/policies/delete|Supprime une configuration de stratégie des stratégies du client|
|/service/policies/read|Obtient les stratégies ou les détails de configuration de stratégie pour le client|
|/service/policies/write|Définit les détails de configuration de stratégie pour le client|
|/service/policySnippets/read|Obtient tous les extraits de code de stratégie|
|/service/portalsettings/read|Obtient les paramètres d’inscription, de connexion ou de délégation pour le portail|
|/service/portalsettings/write|Met à jour les paramètres d’inscription, de connexion ou de délégation|
|/service/products/apis/delete|Supprimer une API existante d’un produit existant|
|/service/products/apis/read|Obtenir la liste des API ajoutées à un produit existant|
|/service/products/apis/write|Ajouter une API existante à un produit existant|
|/service/products/delete|Supprimer un produit existant|
|/service/products/groups/delete|Supprime l’association d’un groupe de développeurs existant à un produit existant|
|/service/products/groups/read|Obtenir la liste des groupes de développeurs associés au produit|
|/service/products/groups/write|Associer un groupe de développeurs existant à un produit existant|
|/service/products/policies/delete|Supprime une configuration de stratégie des stratégies de produit|
|/service/products/policies/read|Obtient les stratégies ou les détails de configuration de stratégie pour un produit|
|/service/products/policies/write|Définit les détails de configuration de stratégie pour un produit|
|/service/products/policy/delete|Supprimer la configuration de stratégie d’un produit existant|
|/service/products/policy/read|Obtenir la configuration de stratégie d’un produit existant|
|/service/products/policy/write|Définir la configuration de stratégie pour un produit existant|
|/service/products/read|Obtenir la liste des produits ou obtenir les détails du produit|
|/service/products/subscriptions/read|Obtenir la liste des abonnements de produit|
|/service/products/tags/delete|Supprime l’association d’une balise existante à un produit existant|
|/service/products/tags/read|Obtient les balises associées au produit ou des détails de balise|
|/service/products/tags/write|Associe une balise existante à un produit existant|
|/service/products/write|Créer un nouveau produit ou mettre à jour des détails du produit existants|
|/service/properties/delete|Supprime la propriété existante|
|/service/properties/read|Obtient la liste de toutes les propriétés ou obtient les détails de la propriété spécifiée|
|/service/properties/write|Crée une nouvelle propriété ou met à jour la valeur de la propriété spécifiée|
|/service/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour le service Gestion des API|
|/service/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour le service Gestion des API|
|/service/providers/Microsoft.Insights/logDefinitions/read|Obtient les journaux disponibles pour le service Gestion des API|
|/service/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour le service Gestion des API|
|/service/quotas/periods/read|Obtient la valeur de compteur de quota pour une période|
|/service/quotas/periods/write|Définit la valeur actuelle du compteur de quota|
|/service/quotas/read|Obtient les valeurs pour le quota|
|/service/quotas/write|Définit la valeur actuelle du compteur de quota|
|/service/read|Lire les métadonnées d’une instance du service Gestion des API|
|/service/reports/read|Obtient un rapport agrégé par période de temps, par région géographique ou par développeur Ou obtient un rapport agrégé par produit Ou obtient un rapport agrégé par API, par opération ou par abonnement Ou obtient des données pour le rapport de demandes|
|/service/restore/action|Restaurer le service Gestion des API à partir du conteneur spécifié dans un compte de stockage fourni par l’utilisateur|
|/service/subscriptions/delete|Supprimez l’abonnement. Cette opération peut être utilisée pour supprimer l’abonnement|
|/service/subscriptions/read|Obtenir la liste des abonnements de produit ou obtenir les détails de l’abonnement de produit|
|/service/subscriptions/regeneratePrimaryKey/action|Régénérer la clé primaire d’abonnement|
|/service/subscriptions/regenerateSecondaryKey/action|Régénérer la clé secondaire d’abonnement|
|/service/subscriptions/write|Abonnez un utilisateur existant à un produit existant ou mettez à jour les détails de l’abonnement existant. Cette opération peut être utilisée pour renouveler l’abonnement|
|/service/tagResources/read|Obtient la liste des balises avec les ressources associées|
|/service/tags/delete|Supprime la balise existante|
|/service/tags/read|Obtient la liste des balises ou les détails d’une balise|
|/service/tags/write|Ajoute une balise ou met à jour les détails d’une balise existante|
|/service/templates/delete|Réinitialise le modèle d’e-mail par défaut de Gestion des API|
|/service/templates/read|Obtient tous les modèles d’e-mail ou les détails d’un modèle d’e-mail de Gestion des API|
|/service/templates/write|Crée ou met à jour le modèle d’e-mail de Gestion des API|
|/service/tenant/delete|Supprimer la configuration de la stratégie du client|
|/service/tenant/deploy/action|Exécute une tâche de déploiement pour appliquer les modifications de la branche git spécifiée à la configuration dans la base de données.|
|/service/tenant/operationResults/read|Obtenir la liste des résultats d’opération ou obtenir le résultat d’une opération spécifique|
|/service/tenant/read|Obtient la configuration de stratégie pour le client ou les détails des informations d’accès du client|
|/service/tenant/regeneratePrimaryKey/action|Régénérer la clé d’accès primaire|
|/service/tenant/regenerateSecondaryKey/action|Régénérer la clé d’accès secondaire|
|/service/tenant/save/action|Crée une validation avec un instantané de configuration dans la branche spécifiée du référentiel|
|/service/tenant/syncState/read|Obtenir l’état de la dernière synchronisation git|
|/service/tenant/validate/action|Valide les modifications de la branche git spécifiée|
|/service/tenant/write|Définit la configuration de stratégie pour le client ou met à jour les détails des informations d’accès du client|
|/service/updatecertificate/action|Télécharger le certificat SSL pour un service Gestion des API|
|/service/updatehostname/action|Configurer, mettre à jour ou supprimer des noms de domaine personnalisés pour un service Gestion des API|
|/service/users/action|Enregistre un nouvel utilisateur|
|/service/users/applications/attachments/delete|Supprime une pièce jointe|
|/service/users/applications/attachments/read|Obtient les pièces jointes de l’application ou une pièce jointe|
|/service/users/applications/attachments/write|Ajoute une pièce jointe à l’application|
|/service/users/applications/delete|Supprime une application existante|
|/service/users/applications/read|Obtient la liste de toutes les applications de l’utilisateur ou les détails de l’application Gestion des API|
|/service/users/applications/write|Inscrit une application auprès de Gestion des API ou met à jour les détails d’une application|
|/service/users/delete|Supprimer le compte d’utilisateur|
|/service/users/generateSsoUrl/action|Générez l’URL d’authentification unique. L’URL peut être utilisée pour accéder au portail d’administration|
|/service/users/groups/read|Obtenir la liste des groupes d’utilisateurs|
|/service/users/keys/read|Obtenir la liste des clés utilisateur|
|/service/users/read|Obtenir la liste des utilisateurs inscrits ou obtenir les informations de compte d’un utilisateur|
|/service/users/subscriptions/read|Obtenir la liste des abonnements utilisateur|
|/service/users/token/action|Obtient un jeton d’accès pour un utilisateur|
|/service/users/write|Inscrire un nouvel utilisateur ou mettre à jour les informations de compte d’un utilisateur existant|
|/service/write|Créer une instance du service Gestion des API|
|/unregister/action|Annuler l’inscription de l’abonnement pour le fournisseur de ressources Microsoft.ApiManagement|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Opération | Description |
|---|---|
|/checkAccess/action|Vérifie si l’appelant est autorisé à effectuer une action particulière|
|/classicAdministrators/delete|Supprime l’administrateur de l’abonnement.|
|/classicAdministrators/read|Lit les administrateurs de l’abonnement.|
|/classicAdministrators/write|Ajoutez ou modifiez un administrateur à un abonnement.|
|/elevateAccess/action|Accorde à l’appelant un accès Administrateur de l’accès utilisateur au niveau de la portée du client|
|/locks/delete|Supprime des verrous au niveau de la portée spécifiée.|
|/locks/read|Obtient des verrous au niveau de la portée spécifiée.|
|/locks/write|Ajoute des verrous au niveau de la portée spécifiée.|
|/permissions/read|Répertorie toutes les autorisations dont dispose l’appelant au niveau d’une portée donnée.|
|/policyAssignments/delete|Supprimez une affectation de stratégie au niveau de la portée spécifiée.|
|/policyAssignments/read|Obtenez des informations sur une affectation de stratégie.|
|/policyAssignments/write|Créez une affectation de stratégie au niveau de la portée spécifiée.|
|/policyDefinitions/delete|Supprimez une définition de stratégie.|
|/policyDefinitions/read|Obtenez des informations sur une définition de stratégie.|
|/policyDefinitions/write|Créez une définition de stratégie personnalisée.|
|/policySetDefinitions/delete|Supprime une définition d’ensemble de stratégies|
|/policySetDefinitions/read|Obtient des informations sur une définition d’ensemble de stratégies|
|/policySetDefinitions/write|Crée une définition d’ensemble de stratégies personnalisé|
|/providerOperations/read|Obtenez des opérations pour tous les fournisseurs de ressources qui peuvent être utilisées dans les définitions de rôles.|
|/roleAssignments/delete|Supprimez une affectation de rôle au niveau de la portée spécifiée.|
|/roleAssignments/read|Obtenez des informations sur une affectation de rôle.|
|/roleAssignments/write|Créez une affectation de rôle au niveau de la portée spécifiée.|
|/roleDefinitions/delete|Supprimez la définition de rôle personnalisé spécifiée.|
|/roleDefinitions/read|Obtenez des informations sur une définition de rôle.|
|/roleDefinitions/write|Créez ou mettez à jour une définition de rôle personnalisé avec des autorisations spécifiées et des portées pouvant être affectées.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Opération | Description |
|---|---|
|/automationAccounts/agentRegistrationInformation/read|Lit les informations d’inscription d’Azure Automation DSC|
|/automationAccounts/agentRegistrationInformation/regenerateKey/action|Écrit une demande de régénération de clés Azure Automation DSC|
|/automationAccounts/certificates/delete|Supprime une ressource de certificat Azure Automation|
|/automationAccounts/certificates/read|Obtient une ressource de certificat Azure Automation|
|/automationAccounts/certificates/write|Crée ou met à jour une ressource de certificat Azure Automation|
|/automationAccounts/compilationjobs/read|Lit une compilation d’Azure Automation DSC|
|/automationAccounts/compilationjobs/write|Écrit une compilation d’Azure Automation DSC|
|/automationAccounts/configurations/delete|Supprime un contenu d’Azure Automation DSC|
|/automationAccounts/configurations/getCount/action|Lit le nombre d’un contenu d’Azure Automation DSC|
|/automationAccounts/configurations/read|Obtient un contenu d’Azure Automation DSC|
|/automationAccounts/configurations/write|Écrit un contenu d’Azure Automation DSC|
|/automationAccounts/connections/delete|Supprime une ressource de connexion Azure Automation|
|/automationAccounts/connections/read|Obtient une ressource de connexion Azure Automation|
|/automationAccounts/connections/write|Crée ou met à jour une ressource de connexion Azure Automation|
|/automationAccounts/connectionTypes/delete|Supprime une ressource de type de connexion Azure Automation|
|/automationAccounts/connectionTypes/read|Obtient une ressource de type de connexion Azure Automation|
|/automationAccounts/connectionTypes/write|Crée une ressource de type de connexion Azure Automation|
|/automationAccounts/credentials/delete|Supprime une ressource d’information d’identification Azure Automation|
|/automationAccounts/credentials/read|Obtient une ressource d’information d’identification Azure Automation|
|/automationAccounts/credentials/write|Crée ou met à jour une ressource d’information d’identification Azure Automation|
|/automationAccounts/delete|Supprime un compte Azure Automation|
|/automationAccounts/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/automationAccounts/diagnosticSettings/write|Obtient le paramètre de diagnostic pour la ressource|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Supprime des ressources Runbook Worker hybrides|
|/automationAccounts/hybridRunbookWorkerGroups/read|Lit des ressources Runbook Worker hybrides|
|/automationAccounts/jobs/output/action|Obtient le résultat d’un travail|
|/automationAccounts/jobs/output/action|Obtient le résultat d’un travail|
|/automationAccounts/jobs/read|Obtient un travail Azure Automation|
|/automationAccounts/jobs/read|Obtient un travail Azure Automation|
|/automationAccounts/jobs/resume/action|Reprend un travail Azure Automation|
|/automationAccounts/jobs/resume/action|Reprend un travail Azure Automation|
|/automationAccounts/jobs/runbookContent/action|Obtient le contenu du runbook Azure Automation au moment de l’exécution du travail|
|/automationAccounts/jobs/runbookContent/action|Obtient le contenu du runbook Azure Automation au moment de l’exécution du travail|
|/automationAccounts/jobs/stop/action|Arrête un travail Azure Automation|
|/automationAccounts/jobs/stop/action|Arrête un travail Azure Automation|
|/automationAccounts/jobs/streams/read|Obtient un flux de travail Azure Automation|
|/automationAccounts/jobs/streams/read|Obtient un flux de travail Azure Automation|
|/automationAccounts/jobs/suspend/action|Suspend un travail Azure Automation|
|/automationAccounts/jobs/suspend/action|Suspend un travail Azure Automation|
|/automationAccounts/jobs/write|Crée un travail Azure Automation|
|/automationAccounts/jobs/write|Crée un travail Azure Automation|
|/automationAccounts/jobSchedules/delete|Supprime une planification du travail Azure Automation|
|/automationAccounts/jobSchedules/read|Obtient une planification du travail Azure Automation|
|/automationAccounts/jobSchedules/write|Crée une planification du travail Azure Automation|
|/automationAccounts/linkedWorkspace/read|Obtient l’espace de travail lié au compte Automation|
|/automationAccounts/logDefinitions/read|Obtient les journaux disponibles pour le compte Automation|
|/automationAccounts/modules/activities/read|Obtient les activités d’Azure Automation|
|/automationAccounts/modules/delete|Supprime un module Azure Automation|
|/automationAccounts/modules/read|Obtient un module Azure Automation|
|/automationAccounts/modules/write|Crée ou met à jour un module Azure Automation|
|/automationAccounts/nodeConfigurations/delete|Supprime une configuration de nœud d’Azure Automation DSC|
|/automationAccounts/nodeConfigurations/read|Lit une configuration de nœud d’Azure Automation DSC|
|/automationAccounts/nodeConfigurations/readContent/action|Lit le contenu d’une configuration de nœud d’Azure Automation DSC|
|/automationAccounts/nodeConfigurations/write|Écrit une configuration de nœud d’Azure Automation DSC|
|/automationAccounts/nodes/delete|Supprime des nœuds Azure Automation DSC|
|/automationAccounts/nodes/read|Lit des nœuds Azure Automation DSC|
|/automationAccounts/nodes/reports/read|Lit le contenu d’un rapport Azure Automation DSC|
|/automationAccounts/nodes/reports/read|Lit un rapport Azure Automation DSC|
|/automationAccounts/objectDataTypes/fields/read|Obtient les TypeFields Azure Automation|
|/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read|Obtient les définitions de métrique d’Automation|
|/automationAccounts/read|Obtient un compte Azure Automation|
|/automationAccounts/runbooks/delete|Supprime un runbook Azure Automation|
|/automationAccounts/runbooks/draft/publish/action|Publie un brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/read|Obtient un brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/readContent/action|Obtient le contenu d’un brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/testJob/read|Obtient un travail de test de brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/testJob/resume/action|Reprend un travail de test de brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/testJob/stop/action|Arrête un travail de test de brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Suspend un travail de test de brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/testJob/write|Crée un travail de test de brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/undoEdit/action|Annule les modifications apportées à un brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/draft/writeContent/action|Crée le contenu d’un brouillon de runbook Azure Automation|
|/automationAccounts/runbooks/read|Obtient un runbook Azure Automation|
|/automationAccounts/runbooks/readContent/action|Obtient le contenu d’un runbook Azure Automation|
|/automationAccounts/runbooks/write|Crée ou met à jour un runbook Azure Automation|
|/automationAccounts/schedules/delete|Supprime une ressource de planification Azure Automation|
|/automationAccounts/schedules/read|Obtient une ressource de planification Azure Automation|
|/automationAccounts/schedules/write|Crée ou met à jour une ressource de planification Azure Automation|
|/automationAccounts/statistics/read|Obtient les statistiques d’Azure Automation|
|/automationAccounts/usages/read|Obtient l’utilisation d’Azure Automation|
|/automationAccounts/variables/delete|Supprime une ressource de variable Azure Automation|
|/automationAccounts/variables/read|Lit une ressource de variable Azure Automation|
|/automationAccounts/variables/write|Crée ou met à jour une ressource de variable Azure Automation|
|/automationAccounts/watchers/streams/read|Obtient un flux de tâches Automation Watcher Azure|
|/automationAccounts/webhooks/delete|Supprime un webhook Azure Automation |
|/automationAccounts/webhooks/generateUri/action|Génère un URI pour un webhook Azure Automation|
|/automationAccounts/webhooks/read|Lit un webhook Azure Automation|
|/automationAccounts/webhooks/write|Crée ou met à jour un webhook Azure Automation|
|/automationAccounts/write|Crée ou met à jour un compte Azure Automation|
|/automationAccounts/write|Crée ou met à jour un compte Azure Automation|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

| Opération | Description |
|---|---|
|/b2cDirectories/delete|Supprime une ressource de répertoire B2C|
|/b2cDirectories/read|Affiche une ressource de répertoire B2C|
|/b2cDirectories/write|Crée ou met à jour une ressource de répertoire B2C|
|/operations/read|Lit toutes les opérations d’API disponibles pour le fournisseur de ressources Microsoft.AzureActiveDirectory|
|/register/action|Enregistre l’abonnement à un fournisseur de ressources Microsoft.AzureActiveDirectory|

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

| Opération | Description |
|---|---|
|/Operations/read|Obtient les propriétés d’une opération de fournisseur de ressources|
|/register/action|Inscrit l’abonnement auprès du fournisseur de ressources Microsoft.AzureStack|
|/registrations/customerSubscriptions/delete|Supprime un abonnement client Azure Stack|
|/registrations/customerSubscriptions/read|Obtient les propriétés d’un abonnement client Azure Stack|
|/registrations/customerSubscriptions/write|Crée ou met à jour un abonnement client Azure Stack|
|/registrations/delete|Vérifie une inscription Azure Stack|
|/registrations/getActivationKey/action|Obtient la dernière clé d’activation Azure Stack|
|/registrations/products/listDetails/action|Récupère les détails étendus d’un produit de la place de marché Azure Stack|
|/registrations/products/read|Obtient les propriétés d’un produit de la place de marché Azure Stack|
|/registrations/read|Obtient les propriétés d’une inscription Azure Stack|
|/registrations/write|Crée ou met à jour une inscription Azure Stack|

## <a name="microsoftbatch"></a>Microsoft.Batch

| Opération | Description |
|---|---|
|/batchAccounts/applications/delete|Supprime une application|
|/batchAccounts/applications/read|Répertorie les applications ou obtient les propriétés d’une application|
|/batchAccounts/applications/versions/activate/action|Active un package d’application|
|/batchAccounts/applications/versions/delete|Supprime un package d’application|
|/batchAccounts/applications/versions/read|Obtient les propriétés d’un package d’application|
|/batchAccounts/applications/versions/write|Crée une nouveau package d’application ou met à jour un package d’application existant|
|/batchAccounts/applications/write|Crée une nouvelle application ou met à jour une application existante|
|/batchAccounts/certificateOperationResults/read|Obtient les résultats d’une opération de certificat de longue durée sur un compte Batch|
|/batchAccounts/certificates/cancelDelete/action|Annule la suppression manquée d’un certificat sur un compte Batch|
|/batchAccounts/certificates/delete|Supprime un certificat d’un compte Batch|
|/batchAccounts/certificates/read|Répertorie les certificats sur un compte Batch ou obtient les propriétés d’un certificat|
|/batchAccounts/certificates/write|Crée un certificat sur un compte Batch ou met à jour un certificat existant|
|/batchAccounts/delete|Supprime un compte Batch|
|/batchAccounts/listkeys/action|Répertorie les clés d’accès pour un compte Batch|
|/batchAccounts/operationResults/read|Obtient les résultats d’une opération de compte Batch de longue durée|
|/batchAccounts/poolOperationResults/read|Obtient les résultats d’une opération de pool de longue durée sur un compte Batch|
|/batchAccounts/pools/delete|Supprime un pool d’un compte Batch|
|/batchAccounts/pools/disableAutoscale/action|Désactive la mise à l’échelle automatique pour un pool de comptes Batch|
|/batchAccounts/pools/read|Répertorie les pools sur un compte Batch ou obtient les propriétés d’un pool|
|/batchAccounts/pools/stopResize/action|Arrête une opération de redimensionnement en cours sur un pool de comptes Batch|
|/batchAccounts/pools/upgradeOs/action|Met à niveau le système d’exploitation d’un pool de comptes Batch|
|/batchAccounts/pools/write|Crée un pool sur un compte Batch ou met à jour un pool existant|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/batchAccounts/providers/Microsoft.Insights/logDefinitions/read|Obtient les journaux disponibles pour le service Batch|
|/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour le service Batch|
|/batchAccounts/read|Répertorie les comptes Batch ou obtient les propriétés d’un compte Batch|
|/batchAccounts/regeneratekeys/action|Régénère les clés d’accès pour un compte Batch|
|/batchAccounts/syncAutoStorageKeys/action|Synchronise les clés d’accès pour le compte de stockage automatique configuré pour un compte Batch|
|/batchAccounts/write|Crée un nouveau compte Batch ou met à jour un compte Batch existant|
|/locations/checkNameAvailability/action|Vérifie que le nom de compte est valide et qu’il n’est pas utilisé|
|/locations/quotas/read|Obtient les quotas Batch de l’abonnement spécifié au niveau de la région Azure spécifiée|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources Batch et permet la création de comptes Batch|
|/unregister/action|Désinscrit l’abonnement pour le fournisseur de ressources Batch en empêchant la création de comptes Batch|

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

| Opération | Description |
|---|---|
|/clusters/delete|Supprime un cluster Batch AI|
|/clusters/read|Répertorie les clusters Batch AI ou obtient les propriétés d’un cluster Batch AI|
|/clusters/remoteLoginInformation/action|Répertorie les informations de connexion à distance pour un cluster Batch AI|
|/clusters/write|Crée un cluster Batch AI ou met à jour un cluster Batch AI existant|
|/fileservers/delete|Supprime un serveur de fichiers Batch AI|
|/fileservers/read|Répertorie les serveurs de fichiers Batch AI ou obtient les propriétés d’un serveur de fichiers Batch AI|
|/fileservers/resume/action|Reprend l’exécution d’un serveur de fichiers Batch AI|
|/fileservers/suspend/action|Interrompt un serveur de fichiers Batch AI|
|/fileservers/write|Crée ou met à jour un serveur de fichiers Batch AI|
|/jobs/delete|Supprime une tâche Batch AI|
|/jobs/read|Répertorie les tâches Batch AI ou obtient les propriétés d’une tâche Batch AI|
|/jobs/remoteLoginInformation/action|Affiche les informations de connexion à distance pour une tâche Batch AI|
|/jobs/terminate/action|Met fin à une tâche Batch AI|
|/jobs/write|Crée ou met à jour une tâche Batch AI|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources Batch AI et permet la création de ressources Batch AI|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Opération | Description |
|---|---|
|/billingPeriods/read|Répertorie les périodes de facturation disponibles|
|/invoices/read|Répertorie les factures disponibles|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Opération | Description |
|---|---|
|/mapApis/Delete|Opération de suppression|
|/mapApis/listSecrets/action|Répertorie les secrets|
|/mapApis/listSingleSignOnToken/action|Lire le jeton d’autorisation d’authentification unique pour la ressource|
|/mapApis/Read|Opération de lecture|
|/mapApis/regenerateKey/action|Régénère la clé|
|/mapApis/Write|Opération d’écriture|
|/Operations/read|Description de l’opération.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Opération | Description |
|---|---|
|/checknameavailability/action|Vérifie si un nom peut être utilisé avec un nouveau cache Redis|
|/operations/read|Répertorie les opérations que le fournisseur Microsoft.Cache prend en charge.|
|/redis/delete|Supprimer l’intégralité du cache Redis|
|/redis/export/action|Exporter des données Redis vers des objets blob préfixés dans un format spécifié|
|/redis/firewallRules/delete|Supprimer des règles de pare-feu IP d’un cache Redis|
|/redis/firewallRules/read|Obtenir les règles de pare-feu IP d’un cache Redis|
|/redis/firewallRules/write|Modifier les règles de pare-feu IP d’un cache Redis|
|/redis/forceReboot/action|Forcez le redémarrage d’instance de cache, potentiellement avec une perte de données.|
|/redis/import/action|Importer des données d’un format spécifié à partir de plusieurs objets blob dans Redis|
|/redis/linkedservers/delete|Supprimer un serveur lié d’un cache Redis|
|/redis/linkedservers/read|Obtenez les serveurs liés associés à un cache Redis.|
|/redis/linkedservers/write|Ajouter un serveur lié à un cache Redis|
|/redis/listKeys/action|Afficher la valeur des clés d’accès du cache Redis dans le portail de gestion|
|/redis/listUpgradeNotifications/read|Répertoriez les dernières notifications de mise à niveau du client de cache.|
|/redis/locations/operationresults/read|Obtient le résultat d’une opération de longue durée pour laquelle l’en-tête Location a été précédemment retourné au client|
|/redis/metricDefinitions/read|Obtient les mesures disponibles pour un cache Redis|
|/redis/patchSchedules/delete|Supprimer la planification de correctif d’un cache Redis|
|/redis/patchSchedules/read|Obtient la planification de mise à jour corrective d’un cache Redis|
|/redis/patchSchedules/write|Modifier la planification de mise à jour corrective d’un cache Redis|
|/redis/read|Afficher les paramètres et la configuration du cache Redis dans le portail de gestion|
|/redis/regenerateKey/action|Modifier la valeur des clés d’accès du cache Redis dans le portail de gestion|
|/redis/start/action|Démarrez une instance de cache.|
|/redis/stop/action|Arrêtez une instance de cache.|
|/redis/write|Modifier les paramètres et la configuration du cache Redis dans le portail de gestion|
|/register/action|Inscrit le fournisseur de ressources « Microsoft.Cache » à un abonnement|
|/unregister/action|Annule l’inscription du fournisseur de ressources « Microsoft.Cache » à un abonnement|

## <a name="microsoftcapacity"></a>Microsoft.Capacity

| Opération | Description |
|---|---|
|/register/action|Inscrit le fournisseur de ressources de capacité et active la création de ressources de capacité.|
|/reservationorders/action|Met à jour une réservation|
|/reservationorders/delete|Supprime une réservation|
|/reservationorders/read|Lit toutes les réservations|
|/reservationorders/reservations/action|Met à jour une réservation|
|/reservationorders/reservations/delete|Supprime une réservation|
|/reservationorders/reservations/read|Lit toutes les réservations|
|/reservationorders/reservations/revisions/read|Lit toutes les réservations|
|/reservationorders/reservations/write|Crée une réservation|
|/reservationorders/write|Crée une réservation|

## <a name="microsoftcdn"></a>Microsoft.Cdn

| Opération | Description |
|---|---|
|/CheckNameAvailability/action||
|/CheckResourceUsage/action||
|/edgenodes/delete||
|/edgenodes/read||
|/edgenodes/write||
|/operationresults/delete||
|/operationresults/profileresults/CheckResourceUsage/action||
|/operationresults/profileresults/delete||
|/operationresults/profileresults/endpointresults/CheckResourceUsage/action||
|/operationresults/profileresults/endpointresults/customdomainresults/delete||
|/operationresults/profileresults/endpointresults/customdomainresults/ DisableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/ EnableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/read||
|/operationresults/profileresults/endpointresults/customdomainresults/write||
|/operationresults/profileresults/endpointresults/delete||
|/operationresults/profileresults/endpointresults/Load/action||
|/operationresults/profileresults/endpointresults/originresults/delete||
|/operationresults/profileresults/endpointresults/originresults/read||
|/operationresults/profileresults/endpointresults/originresults/write||
|/operationresults/profileresults/endpointresults/Purge/action||
|/operationresults/profileresults/endpointresults/read||
|/operationresults/profileresults/endpointresults/Start/action||
|/operationresults/profileresults/endpointresults/Stop/action||
|/operationresults/profileresults/endpointresults/ValidateCustomDomain/action||
|/operationresults/profileresults/endpointresults/write||
|/operationresults/profileresults/GenerateSsoUri/action||
|/operationresults/profileresults/GetSupportedOptimizationTypes/action||
|/operationresults/profileresults/read||
|/operationresults/profileresults/write||
|/operationresults/read||
|/operationresults/write||
|/operations/read||
|/profiles/CheckResourceUsage/action||
|/profiles/delete||
|/profiles/endpoints/CheckResourceUsage/action||
|/profiles/endpoints/customdomains/delete||
|/profiles/endpoints/customdomains/DisableCustomHttps/action||
|/profiles/endpoints/customdomains/EnableCustomHttps/action||
|/profiles/endpoints/customdomains/read||
|/profiles/endpoints/customdomains/write||
|/profiles/endpoints/delete||
|/profiles/endpoints/Load/action||
|/profiles/endpoints/origins/delete||
|/profiles/endpoints/origins/read||
|/profiles/endpoints/origins/write||
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read|Obtient les paramètres de diagnostic pour la ressource|
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour les paramètres de diagnostic pour la ressource|
|/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read|Obtient les journaux disponibles pour Microsoft.Cdn|
|/profiles/endpoints/Purge/action||
|/profiles/endpoints/read||
|/profiles/endpoints/Start/action||
|/profiles/endpoints/Stop/action||
|/profiles/endpoints/ValidateCustomDomain/action||
|/profiles/endpoints/write||
|/profiles/GenerateSsoUri/action||
|/profiles/GetSupportedOptimizationTypes/action||
|/profiles/read||
|/profiles/write||
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources CDN et active la création de profils CDN|
|/ValidateProbe/action||

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Opération | Description |
|---|---|
|/certificateOrders/certificates/Delete|Supprimer un certificat existant|
|/certificateOrders/certificates/Read|Obtenir la liste des certificats|
|/certificateOrders/certificates/Write|Ajouter un nouveau certificat ou en mettre un existant à jour|
|/certificateOrders/Delete|Supprimer un AppServiceCertificate existant|
|/certificateOrders/operations/Read|Liste de toutes les opérations de l’enregistrement de certificat de service d’application|
|/certificateOrders/Read|Obtenir la liste des CertificateOrders|
|/certificateOrders/reissue/Action|Réémettre un certificateorder existant|
|/certificateOrders/renew/Action|Renouveler un certificateorder existant|
|/certificateOrders/resendEmail/Action|Renvoyer l’e-mail de certificat|
|/certificateOrders/resendRequestEmails/Action|Renvoyer des e-mails de demande à une autre adresse de messagerie|
|/certificateOrders/resendRequestEmails/Action|Récupérer le Seal de site pour un App Service Certificate émis|
|/certificateOrders/retrieveCertificateActions/Action|Récupérer la liste des actions de certificat|
|/certificateOrders/retrieveEmailHistory/Action|Récupérer l’historique d’e-mails de certificat|
|/certificateOrders/verifyDomainOwnership/Action|Vérifier la propriété du domaine|
|/certificateOrders/Write|Ajouter un nouveau certificateOrder ou en mettre un existant à jour|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|Approvisionner le principal de service pour un principal d’application de service|
|/register/action|Inscrire le fournisseur de ressources de certificats Microsoft pour l’abonnement|
|/validateCertificateRegistrationInformation/Action|Valider l’objet d’achat de certificat sans le soumettre|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Opération | Description |
|---|---|
|/capabilities/read|Affiche les fonctionnalités|
|/checkDomainNameAvailability/action|Vérifie la disponibilité d’un nom de domaine donné.|
|/domainNames/active/write|Définit le nom de domaine actif.|
|/domainNames/availabilitySets/read|Affichez le groupe à haute disponibilité de la ressource.|
|/domainNames/capabilities/read|Affiche les fonctionnalités de nom de domaine|
|/domainNames/delete|Supprimez les noms de domaine pour les ressources.|
|/domainNames/extensions/delete|Supprimez les extensions de nom de domaine.|
|/domainNames/extensions/operationStatuses/read|Lit l’état de l’opération pour les extensions de noms de domaine.|
|/domainNames/extensions/read|Retourne les extensions de nom de domaine.|
|/domainNames/extensions/write|Ajoutez les extensions de nom de domaine.|
|/domainNames/internalLoadBalancers/delete|Supprimez un nouvel équilibrage de charge interne.|
|/domainNames/internalLoadBalancers/operationStatuses/read|Lit l’état de l’opération pour les équilibreurs de charge interne de noms de domaine.|
|/domainNames/internalLoadBalancers/read|Obtient les équilibreurs de charge interne.|
|/domainNames/internalLoadBalancers/write|Crée un nouvel équilibrage de charge interne.|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|Lit l’état de l’opération pour les ensembles de points de terminaison à charge équilibrée de noms de domaine.|
|/domainNames/loadBalancedEndpointSets/read|Affiche les ensembles de points de terminaison à charge équilibrée|
|/domainNames/read|Retournez les noms de domaine pour les ressources.|
|/domainNames/serviceCertificates/delete|Supprimez les certificats de service utilisés.|
|/domainNames/serviceCertificates/operationStatuses/read|Lit l’état de l’opération pour les certificats de service de noms de domaine.|
|/domainNames/serviceCertificates/read|Retourne les certificats de service utilisés.|
|/domainNames/serviceCertificates/write|Ajoutez ou modifiez les certificats de service utilisés.|
|/domainNames/slots/delete|Supprime un emplacement de déploiement donné.|
|/domainNames/slots/operationStatuses/read|Lit l’état de l’opération pour les emplacements de noms de domaine.|
|/domainNames/slots/read|Affiche les emplacements de déploiement.|
|/domainNames/slots/roles/extensionReferences/delete|Supprimez la référence d’extension pour le rôle d’emplacement de déploiement.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|Lit l’état de l’opération pour les références d’extension de rôles d’emplacements de noms de domaine.|
|/domainNames/slots/roles/extensionReferences/read|Retourne la référence d’extension pour le rôle d’emplacement de déploiement.|
|/domainNames/slots/roles/extensionReferences/write|Ajoutez ou modifiez la référence d’extension pour le rôle d’emplacement de déploiement.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read|Obtient les paramètres des diagnostics.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write|Ajoutez ou modifiez des paramètres de diagnostics.|
|/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read|Obtient les définitions de mesures.|
|/domainNames/slots/roles/read|Obtenez le rôle de l’emplacement de déploiement.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|Lit l’état de l’opération pour les instances de rôle de rôles d’emplacements de noms de domaine.|
|/domainNames/slots/roles/roleInstances/read|Obtenez l’instance de rôle.|
|/domainNames/slots/roles/roleInstances/rebuild/action|Regénère l’instance de rôle|
|/domainNames/slots/roles/roleInstances/reimage/action|Réinitialise l’instance de rôle.|
|/domainNames/slots/roles/roleInstances/restart/action|Redémarre des instances de rôle.|
|/domainNames/slots/start/action|Démarre un emplacement de déploiement.|
|/domainNames/slots/state/start/write|Modifie l’état d’emplacement de déploiement sur arrêté.|
|/domainNames/slots/state/stop/write|Modifie l’état d’emplacement de déploiement sur démarré.|
|/domainNames/slots/stop/action|Suspend l’emplacement de déploiement.|
|/domainNames/slots/upgradeDomain/write|Parcourez la mise à niveau du domaine.|
|/domainNames/slots/write|Crée ou met à jour le déploiement.|
|/domainNames/swap/action|Permute l’emplacement intermédiaire vers l’emplacement de production.|
|/domainNames/write|Ajoutez ou modifiez les noms de domaine pour les ressources.|
|/moveSubscriptionResources/action|Déplacez toutes les ressources classiques vers un autre abonnement.|
|/operatingSystemFamilies/read|Répertorie les familles de systèmes d’exploitation invités disponibles dans Microsoft Azure ainsi que les versions de système d’exploitation disponibles pour chaque famille|
|/operatingSystems/read|Répertorie les versions de système d’exploitation invité qui sont actuellement disponibles dans Microsoft Azure.|
|/quotas/read|Obtient le quota de l’abonnement.|
|/register/action|S’inscrire à Classic Compute|
|/resourceTypes/skus/read|Obtient la liste des références (SKU) pour les types de ressources pris en charge.|
|/validateSubscriptionMoveAvailability/action|Validez la disponibilité de l’abonnement pour l’opération de déplacement classique.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|Supprime le groupe de sécurité réseau associé à la machine virtuelle.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|Lit l’état de l’opération pour les machines virtuelles associées à des groupes de sécurité réseau.|
|/virtualMachines/associatedNetworkSecurityGroups/read|Obtient le groupe de sécurité réseau associé à la machine virtuelle.|
|/virtualMachines/associatedNetworkSecurityGroups/write|Ajoute un groupe de sécurité réseau associé à la machine virtuelle.|
|/virtualMachines/asyncOperations/read|Obtient les opérations asynchrones possibles|
|/virtualMachines/attachDisk/action|Attache un disque de données à une machine virtuelle.|
|/virtualMachines/delete|Supprime des machines virtuelles.|
|/virtualMachines/detachDisk/action|Détache un disque de données d’une machine virtuelle.|
|/virtualMachines/disks/read|Récupère la liste des disques de données|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|Télécharge le fichier RDP pour la machine virtuelle.|
|/virtualMachines/extensions/operationStatuses/read|Lit l’état de l’opération pour les extensions de machines virtuelles.|
|/virtualMachines/extensions/read|Obtient l’extension de machine virtuelle.|
|/virtualMachines/extensions/write|Place l’extension de machine virtuelle.|
|/virtualMachines/metrics/read|Obtient les mesures.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete|Supprime le groupe de sécurité réseau associé à l’interface réseau.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/ operationStatuses/read|Lit l’état de l’opération pour les machines virtuelles associées à des groupes de sécurité réseau.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read|Obtient le groupe de sécurité réseau associé à l’interface réseau.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write|Ajoute un groupe de sécurité réseau associé à l’interface réseau.|
|/virtualMachines/operationStatuses/read|Lit l’état de l’opération pour les machines virtuelles.|
|/virtualMachines/performMaintenance/action|Effectue la maintenance de la machine virtuelle|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|Obtient les paramètres des diagnostics.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|Ajoutez ou modifiez des paramètres de diagnostics.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Obtient les définitions de mesures.|
|/virtualMachines/read|Récupère la liste des machines virtuelles.|
|/virtualMachines/redeploy/action|Redéploie la machine virtuelle.|
|/virtualMachines/restart/action|Redémarre des machines virtuelles.|
|/virtualMachines/shutdown/action|Arrêtez la machine virtuelle.|
|/virtualMachines/start/action|Démarrez la machine virtuelle.|
|/virtualMachines/stop/action|Arrête la machine virtuelle.|
|/virtualMachines/write|Ajouter ou modifier des machines virtuelles.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Opération | Description |
|---|---|
|/gatewaySupportedDevices/read|Récupère la liste des appareils pris en charge.|
|/networkSecurityGroups/delete|Supprime le groupe de sécurité réseau.|
|/networkSecurityGroups/operationStatuses/read|Lit l’état de l’opération pour le groupe de sécurité réseau.|
|/networkSecurityGroups/read|Obtient le groupe de sécurité réseau.|
|/networkSecurityGroups/securityRules/delete|Supprime la règle de sécurité.|
|/networkSecurityGroups/securityRules/operationStatuses/read|Lit l’état de l’opération pour les règles de sécurité du groupe de sécurité réseau.|
|/networkSecurityGroups/securityRules/read|Obtient la règle de sécurité.|
|/networkSecurityGroups/securityRules/write|Ajoute ou met à jour une règle de sécurité.|
|/networkSecurityGroups/write|Ajoute un nouveau groupe de sécurité réseau.|
|/quotas/read|Obtient le quota de l’abonnement.|
|/register/action|S’inscrire à un réseau classique|
|/reservedIps/delete|Supprimez une adresse IP réservée.|
|/reservedIps/join/action|Joindre une adresse IP réservée|
|/reservedIps/link/action|Lier une adresse IP réservée|
|/reservedIps/operationStatuses/read|Lit l’état de l’opération pour les adresses IP réservées.|
|/reservedIps/read|Obtient les adresses IP réservées|
|/reservedIps/write|Ajouter une nouvelle adresse IP réservée|
|/virtualNetworks/capabilities/read|Affiche les fonctionnalités|
|/virtualNetworks/checkIPAddressAvailability/action|Vérifie la disponibilité d’une adresse IP donnée dans un réseau virtuel.|
|/virtualNetworks/delete|Supprime le réseau virtuel.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|Annule la révocation d’un certificat client.|
|/virtualNetworks/gateways/clientRevokedCertificates/read|Lisez les certificats clients révoqués.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|Révoque un certificat client.|
|/virtualNetworks/gateways/clientRootCertificates/delete|Supprime le certificat client de passerelle de réseau virtuel.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|Télécharge le certificat par empreinte.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|Répertorie le package de certificat de passerelle de réseau virtuel.|
|/virtualNetworks/gateways/clientRootCertificates/read|Recherchez les certificats racine client.|
|/virtualNetworks/gateways/clientRootCertificates/write|Télécharge un nouveau certificat racine client.|
|/virtualNetworks/gateways/connections/connect/action|Établit une connexion de passerelle site à site.|
|/virtualNetworks/gateways/connections/disconnect/action|Déconnecte une connexion de passerelle site à site.|
|/virtualNetworks/gateways/connections/read|Récupère la liste des connexions.|
|/virtualNetworks/gateways/connections/test/action|Teste une connexion de passerelle site à site.|
|/virtualNetworks/gateways/delete|Supprime la passerelle de réseau virtuel.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|Télécharge le script de configuration d’appareil.|
|/virtualNetworks/gateways/downloadDiagnostics/action|Télécharge les diagnostics de la passerelle.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|Récupère la clé de service du circuit.|
|/virtualNetworks/gateways/listPackage/action|Répertorie le package de passerelle de réseau virtuel.|
|/virtualNetworks/gateways/operationStatuses/read|Lit l’état de l’opération pour les passerelles de réseaux virtuels.|
|/virtualNetworks/gateways/packages/read|Obtient le package de passerelle de réseau virtuel.|
|/virtualNetworks/gateways/read|Obtient les passerelles de réseau virtuel.|
|/virtualNetworks/gateways/startDiagnostics/action|Démarre le diagnostic pour la passerelle de réseau virtuel.|
|/virtualNetworks/gateways/stopDiagnostics/action|Arrête le diagnostic pour la passerelle de réseau virtuel.|
|/virtualNetworks/gateways/write|Ajoute une passerelle de réseau virtuel.|
|/virtualNetworks/join/action|Joint le réseau virtuel.|
|/virtualNetworks/operationStatuses/read|Lit l’état de l’opération pour les réseaux virtuels.|
|/virtualNetworks/peer/action|Appaire un réseau virtuel avec un autre réseau virtuel.|
|/virtualNetworks/read|Obtenez le réseau virtuel.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete|Supprime le groupe de sécurité réseau associé au sous-réseau.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read|Lit l’état de l’opération pour le sous-réseau de réseau virtuel associé au groupe de sécurité réseau.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/read|Obtient le groupe de sécurité réseau associé au sous-réseau.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/write|Ajoute un groupe de sécurité réseau associé au sous-réseau.|
|/virtualNetworks/write|Ajoutez un nouveau réseau virtuel.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Opération | Description |
|---|---|
|/capabilities/read|Affiche les fonctionnalités|
|/checkStorageAccountAvailability/action|Vérifie la disponibilité d’un compte de stockage.|
|/disks/read|Retourne le disque du compte de stockage.|
|/images/read|Retourne l’image.|
|/osImages/read|Retourne l’image du système d’exploitation.|
|/publicImages/read|Obtient l’image de machine virtuelle publique.|
|/quotas/read|Obtient le quota de l’abonnement.|
|/register/action|S’inscrire à un stockage classique|
|/storageAccounts/delete|Supprime le compte de stockage.|
|/storageAccounts/disks/delete|Supprime un disque de compte de stockage spécifique.|
|/storageAccounts/disks/operationStatuses/read|Lit l’état de l’opération pour la ressource.|
|/storageAccounts/disks/read|Retourne le disque du compte de stockage.|
|/storageAccounts/disks/write|Ajoute un disque de compte de stockage.|
|/storageAccounts/images/delete|Supprime une image du compte de stockage spécifique.|
|/storageAccounts/images/read|Retourne l’image du compte de stockage.|
|/storageAccounts/listKeys/action|Répertorie les clés d’accès des comptes de stockage.|
|/storageAccounts/operationStatuses/read|Lit l’état de l’opération pour la ressource.|
|/storageAccounts/osImages/delete|Supprime une image du système d’exploitation de compte de stockage spécifique.|
|/storageAccounts/osImages/read|Retourne l’image du système d’exploitation de compte de stockage.|
|/storageAccounts/read|Retourne le compte de stockage avec le compte spécifique.|
|/storageAccounts/regenerateKey/action|Régénère les clés d’accès existantes du compte de stockage.|
|/storageAccounts/services/diagnosticSettings/read|Obtient les paramètres des diagnostics.|
|/storageAccounts/services/diagnosticSettings/write|Ajoutez ou modifiez des paramètres de diagnostics.|
|/storageAccounts/services/metricDefinitions/read|Obtient les définitions de mesures.|
|/storageAccounts/services/metrics/read|Obtient les mesures.|
|/storageAccounts/services/read|Obtient les services disponibles.|
|/storageAccounts/write|Ajoute un nouveau compte de stockage.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Opération | Description |
|---|---|
|/accounts/delete|Supprime les comptes d’API|
|/accounts/listKeys/action|Afficher la liste des clés|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour Cognitive Services|
|/accounts/read|Lit les comptes d’API.|
|/accounts/regenerateKey/action|Régénérer la clé|
|/accounts/skus/read|Lit les références disponibles pour une ressource existante.|
|/accounts/usages/read|Obtenir l’utilisation du quota pour une ressource existante.|
|/accounts/write|Écrit les comptes d’API.|
|/locations/checkSkuAvailability/action|Lit les références (SKU) disponibles pour un abonnement|
|/Operations/read|Répertorie toutes les opérations disponibles|
|/register/action|Enregistre l’abonnement à Cognitive Services|
|/skus/read|Lit les références (SKU) disponibles pour Cognitive Services|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Opération | Description |
|---|---|
|/RateCard/read|Retourne les données, les métadonnées de ressource/compteur et les taux d’offre de l’abonnement concerné.|
|/UsageAggregates/read|Récupère l’utilisation de Microsoft Azure par un abonnement. Le résultat contient des données d’utilisation, des informations sur l’abonnement et la ressource agrégées, sur un intervalle de temps donné.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Opération | Description |
|---|---|
|/availabilitySets/delete|Supprimer le groupe à haute disponibilité|
|/availabilitySets/read|Obtenir les propriétés d’un groupe à haute disponibilité|
|/availabilitySets/vmSizes/read|Répertorier les tailles disponibles pour la création ou la mise à jour d’une machine virtuelle dans un groupe à haute disponibilité|
|/availabilitySets/write|Créer ou mettre à jour un groupe à haute disponibilité|
|/disks/beginGetAccess/action|Obtenir l’URI SAP du disque pour l’accès aux objets blob|
|/disks/delete|Supprimer le disque|
|/disks/endGetAccess/action|Révoquer l’URI SAP du disque|
|/disks/read|Obtenir les propriétés d’un disque|
|/disks/write|Créer ou mettre à jour un disque|
|/images/delete|Supprimer l’image|
|/images/read|Obtenir les propriétés de l’image|
|/images/write|Créer ou mettre à jour une image|
|/locations/capsOperations/read|Obtient l’état d’une opération de plafonnement asynchrone|
|/locations/diskOperations/read|Obtient l’état d’une opération de disque asynchrone|
|/locations/operations/read|Afficher l’état d’une opération asynchrone|
|/locations/publishers/artifacttypes/offers/read|Obtient les propriétés d’une offre d’image de plateforme|
|/locations/publishers/artifacttypes/offers/skus/read|Obtient les propriétés d’une référence (SKU) d’image de plateforme|
|/locations/publishers/artifacttypes/offers/skus/versions/read|Obtient les propriétés d’une version d’image de plateforme|
|/locations/publishers/artifacttypes/types/read|Obtient les propriétés d’un type d’extension de machine virtuelle|
|/locations/publishers/artifacttypes/types/versions/read|Obtient les propriétés d’une version d’extension de machine virtuelle|
|/locations/publishers/read|Obtient les propriétés d’un éditeur|
|/locations/runCommands/read|Répertorie les commandes d’exécution disponibles dans un emplacement|
|/locations/usages/read|Afficher les limites du service et les quantités en cours d’utilisation pour les ressources de calcul de l’abonnement dans un emplacement|
|/locations/vmSizes/read|Répertorier les tailles de machines virtuelles disponibles dans un emplacement|
|/operations/read|Répertorier les opérations disponibles dans le fournisseur de ressources Microsoft.Compute|
|/register/action|Inscrit l’abonnement auprès du fournisseur de ressources Microsoft.Compute|
|/restorePointCollections/delete|Supprimer la collection de points de restauration et les points de restauration contenus|
|/restorePointCollections/read|Obtenir les propriétés d’une collection de points de restauration|
|/restorePointCollections/restorePoints/delete|Supprimer le point de restauration|
|/restorePointCollections/restorePoints/read|Obtenir les propriétés d’un point de restauration|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Obtenir les propriétés d’un point de restauration, ainsi que les URI SAP des objets blob|
|/restorePointCollections/restorePoints/write|Créer un nouveau point de restauration|
|/restorePointCollections/write|Crée une nouvelle collection de points de restauration ou met à jour une collection existante|
|/sharedVMImages/delete|Supprime l’image de machine virtuelle partagée|
|/sharedVMImages/read|Obtient les propriétés d’une image de machine virtuelle partagée|
|/sharedVMImages/versions/delete|Supprime une version d’image de machine virtuelle partagée|
|/sharedVMImages/versions/read|Obtient les propriétés d’une version image de machine virtuelle partagée|
|/sharedVMImages/versions/replicate/action|Réplique une version d’image de machine virtuelle partagée dans des régions cibles|
|/sharedVMImages/versions/write|Crée ou met à jour une version d’image de machine virtuelle partagée|
|/sharedVMImages/write|Crée ou met à jour une image de machine virtuelle partagée|
|/skus/read|Obtient la liste des références (SKU) Microsoft.Compute disponibles pour votre abonnement|
|/snapshots/beginGetAccess/action|Obtient l’URI SAP de la capture instantanée pour l’accès aux objets blob|
|/snapshots/delete|Supprimer une capture instantanée|
|/snapshots/endGetAccess/action|Révoque l’URI SAP de la capture instantanée|
|/snapshots/read|Obtenir les propriétés d’une capture instantanée|
|/snapshots/write|Créer ou mettre à jour une capture instantanée|
|/virtualMachines/capture/action|Capturer la machine virtuelle en copiant les disques durs virtuels et générer un modèle qui peut être utilisé pour créer des machines virtuelles similaires|
|/virtualMachines/convertToManagedDisks/action|Convertir les disques basés sur des objets blob de la machine virtuelle en disques gérés|
|/virtualMachines/deallocate/action|Mettre hors tension la machine virtuelle et libérer les ressources de calcul|
|/virtualMachines/delete|Supprimer la machine virtuelle|
|/virtualMachines/extensions/delete|Supprimer l’extension de machine virtuelle|
|/virtualMachines/extensions/read|Afficher les propriétés d’une extension de machine virtuelle|
|/virtualMachines/extensions/write|Créer ou mettre à jour une extension de machine virtuelle|
|/virtualMachines/generalize/action|Définir l’état de la machine virtuelle sur Généralisée et préparer la machine virtuelle pour la capture|
|/virtualMachines/instanceView/read|Afficher l’état d’exécution détaillé de la machine virtuelle et de ses ressources|
|/virtualMachines/performMaintenance/action|Performs Maintenance Operation on the VM.|
|/virtualMachines/powerOff/action|Mettre hors tension la machine virtuelle. Notez que la machine virtuelle sera toujours facturée.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Lit les définitions de métrique de machine virtuelle|
|/virtualMachines/read|Obtenir les propriétés d’une machine virtuelle|
|/virtualMachines/redeploy/action|Redéployer la machine virtuelle|
|/virtualMachines/reimage/action|Réimage une machine virtuelle utilisant un disque de différenciation|
|/virtualMachines/restart/action|Redémarrer la machine virtuelle|
|/virtualMachines/runCommand/action|Exécute un script prédéfini sur la machine virtuelle|
|/virtualMachines/start/action|Démarrer la machine virtuelle|
|/virtualMachines/vmSizes/read|Répertorier les tailles disponibles pour la mise à jour de la machine virtuelle|
|/virtualMachines/write|Créer ou mettre à jour une machine virtuelle|
|/virtualMachineScaleSets/deallocate/action|Met hors tension et libère les ressources de calcul pour les instances du groupe de machines virtuelles identiques |
|/virtualMachineScaleSets/delete|Supprime le groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/delete/action|Supprime les instances du groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/extensions/delete|Supprime l’extension de groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/extensions/read|Obtient les propriétés d’une extension de groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/extensions/write|Crée ou met à jour une extension de groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action|Nécessite de parcourir manuellement les domaines de mise à jour de plateforme d’un groupe de machines virtuelles identiques Service Fabric pour achever une mise à jour en attente bloquée|
|/virtualMachineScaleSets/instanceView/read|Obtient la vue d’instance du groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/manualUpgrade/action|Nécessite de mettre à jour manuellement les instances avec le dernier modèle du groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/networkInterfaces/read|Obtient les propriétés de toutes les interfaces réseau d’un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/osUpgradeHistory/read|Obtient l’historique des mises à niveau du système d’exploitation pour un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/performMaintenance/action|Effectue une maintenance planifiée sur les instances du groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/powerOff/action|Met hors tension les instances du groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read|Lit les définitions de métrique du groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/publicIPAddresses/read|Obtient les propriétés de toutes les adresses IP publiques d’un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/read|Obtient les propriétés d’un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/redeploy/action|Redéploie les instances du groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/reimage/action|Réimage les instances du groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/restart/action|Redémarre les instances du groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/rollingUpgrades/cancel/action|Annule la mise à niveau propagée d’un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/rollingUpgrades/read|Obtient l’état de la dernière mise à niveau propagée d’un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/scale/action|Vérifie si un groupe de machines virtuelles identiques existant peut être mis à l’échelle vers un nombre d’instances spécifié|
|/virtualMachineScaleSets/skus/read|Répertorie les références SKU valides pour un groupe de machines virtuelles identiques existant|
|/virtualMachineScaleSets/start/action|Démarre les instances du groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|Mettre hors tension et libérer les ressources de calcul d’une machine virtuelle d’un groupe de machines virtuelles identiques.|
|/virtualMachineScaleSets/virtualMachines/delete|Supprimer une machine virtuelle spécifique d’un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|Récupérer la vue d’instance d’une machine virtuelle dans un groupe de machines virtuelles identiques.|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ ipConfigurations/publicIPAddresses/read|Obtient les propriétés de l’adresse IP publique créée à l’aide d’un groupe de machines virtuelles identiques. Un groupe de machines virtuelles identiques peut créer au maximum une adresse IP par configuration IP (adresse IP privée)|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read|Obtient les propriétés d’une ou de la totalité des configurations IP d’une interface réseau créée à l’aide d’un groupe de machines virtuelles identiques. Les configurations IP représentent des adresses IP privées|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/read|Obtient les propriétés d’une ou de la totalité des interfaces réseau d’une machine virtuelle créée à l’aide d’un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/virtualMachines/performMaintenance/action|Effectue une maintenance planifiée sur une instance de machine virtuelle dans un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|Mettre hors tension une instance de machine virtuelle dans un groupe de machines virtuelles identiques.|
|/virtualMachineScaleSets/virtualMachines/providers/ Microsoft.Insights/metricDefinitions/read|Lit la machine virtuelle dans les définitions de métrique d’un groupe identique|
|/virtualMachineScaleSets/virtualMachines/read|Récupérer les propriétés d’une machine virtuelle dans un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/virtualMachines/redeploy/action|Redéploie une instance de machine virtuelle dans un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/virtualMachines/reimage/action|Réimage une instance de machine virtuelle dans un groupe de machines virtuelles identiques.|
|/virtualMachineScaleSets/virtualMachines/restart/action|Redémarrer une instance de machine virtuelle dans un groupe de machines virtuelles identiques.|
|/virtualMachineScaleSets/virtualMachines/start/action|Démarrer une instance de machine virtuelle dans un groupe de machines virtuelles identiques.|
|/virtualMachineScaleSets/virtualMachines/write|Met à jour les propriétés d’une machine virtuelle dans un groupe de machines virtuelles identiques|
|/virtualMachineScaleSets/write|Crée ou met à jour un groupe de machines virtuelles identiques|

## <a name="microsoftconsumption"></a>Microsoft.Consumption

| Opération | Description |
|---|---|
|/balances/read|Affiche le récapitulatif d’utilisation d’une période de facturation pour un groupe d’administration.|
|/budgets/read|Affiche les budgets par abonnement ou un groupe d’administration.|
|/budgets/write|Crée, met à jour et supprime les budgets par abonnement ou groupe d’administration.|
|/marketplaces/read|Affiche les détails d’utilisation des ressources de la place de marché pour une étendue en lien avec des abonnements EA et WebDirect.|
|/operations/read|Répertorie toutes les opérations prises en charge par un fournisseur de ressources Microsoft.Consumption.|
|/pricesheets/read|Affiche les données de grille tarifaire pour un abonnement ou un groupe d’administration.|
|/reservationDetails/read|Affiche les détails d’utilisation d’instances réservées par ordre de réservation ou groupe d’administration. Les données sont détaillées par instance par jour.|
|/reservationSummaries/read|Affiche le récapitulatif d’utilisation d’instances réservées par ordre de réservation ou groupe d’administration. Les données récapitulatives sont affichées au niveau mensuel ou quotidien.|
|/reservationTransactions/read|Affiche l’historique des transactions pour les instances réservées par groupe d’administration.|
|/terms/read|Affiche les termes d’un abonnement ou d’un groupe d’administration.|
|/usageDetails/read|Affiche les détails d’utilisation pour une étendue en lien avec des abonnements EA et WebDirect.|

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

| Opération | Description |
|---|---|
|/containerGroups/containers/logs/read|Obtient les journaux pour un conteneur spécifique.|
|/containerGroups/delete|Supprime le groupe de conteneurs spécifique.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour le groupe de conteneurs.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour le groupe de conteneurs.|
|/containerGroups/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour le groupe de conteneurs.|
|/containerGroups/read|Obtient tous les groupes de conteneurs.|
|/containerGroups/write|Crée ou met à jour un groupe de conteneurs spécifique.|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Opération | Description |
|---|---|
|/checkNameAvailability/read|Vérifie si le nom de registre de conteneurs est disponible pour utilisation.|
|/locations/operationResults/read|Obtient un résultat d’opération asynchrone|
|/operations/read|Répertorie toutes les opérations disponibles de l’API REST Azure Container Registry|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources du registre de conteneurs et active la création de registres de conteneurs.|
|/registries/delete|Supprime un registre de conteneurs.|
|/registries/eventGridFilters/delete|Supprime un filtre Event Grid d’un registre de conteneurs.|
|/registries/eventGridFilters/read|Obtient les propriétés du filtre Event Grid spécifié ou répertorie tous les filtres Event Grid pour le registre de conteneurs spécifié.|
|/registries/eventGridFilters/write|Crée ou met à jour un filtre Event Grid pour un registre de conteneurs avec les paramètres spécifiés.|
|/registries/listCredentials/action|Répertorie les informations d’identification pour le registre de conteneurs spécifié.|
|/registries/listUsages/read|Affiche les utilisations du quota pour le registre de conteneurs spécifié.|
|/registries/operationStatuses/read|Obtient un état d’opération asynchrone du registre|
|/registries/read|Obtient les propriétés du registre de conteneurs spécifié ou affiche tous les registres de conteneurs sous le groupe de ressources ou l’abonnement spécifiés.|
|/registries/regenerateCredential/action|Régénère l’une des informations d’identification pour le registre de conteneurs spécifié.|
|/registries/replications/delete|Supprime une réplication d’un registre de conteneurs.|
|/registries/replications/operationStatuses/read|Obtient un état d’opération asynchrone de réplication|
|/registries/replications/read|Obtient les propriétés de la réplication spécifiée ou affiche toutes les réplications pour le registre de conteneurs spécifié.|
|/registries/replications/write|Crée ou met à jour une réplication pour un registre de conteneurs avec les paramètres spécifiés.|
|/registries/webhooks/delete|Supprime un webhook d’un registre de conteneurs.|
|/registries/webhooks/getCallbackConfig/action|Obtient la configuration de l’URI de service et les en-têtes personnalisés pour le webhook.|
|/registries/webhooks/listEvents/action|Affiche les événements récents pour le webhook spécifié.|
|/registries/webhooks/operationStatuses/read|Obtient un état de l’opération asynchrone de webhook|
|/registries/webhooks/ping/action|Déclenche un événement de test ping à envoyer au webhook.|
|/registries/webhooks/read|Obtient les propriétés du webhook spécifié ou affiche tous les webhooks pour le registre de conteneurs spécifié.|
|/registries/webhooks/write|Crée ou met à jour un webhook pour un registre de conteneurs avec les paramètres spécifiés.|
|/registries/write|Crée ou met à jour un registre de conteneurs avec les paramètres spécifiés.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Opération | Description |
|---|---|
|/containerServices/delete|Supprime le service de conteneur spécifié|
|/containerServices/read|Affiche le service de conteneur spécifié|
|/containerServices/write|Ajoute ou met à jour le service de conteneur spécifié|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Opération | Description |
|---|---|
|/applications/delete|Opération de suppression|
|/applications/listSecrets/action|Afficher la liste des clés secrètes|
|/applications/listSingleSignOnToken/action|Afficher les jetons d’authentification unique|
|/applications/read|Opération de lecture|
|/applications/write|Opération d’écriture|
|/applications/write|Opération d’écriture|
|/listCommunicationPreference/action|Répertorier les préférences de communication|
|/operations/read|opérations de lecture|
|/updateCommunicationPreference/action|Mettre à jour les préférences de communication|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Opération | Description |
|---|---|
|/hubs/adobemetadata/action|Crée ou met à jour des métadonnées Adobe Azure Customer Insights|
|/hubs/adobemetadata/read|Lit des métadonnées Adobe Azure Customer Insights|
|/hubs/authorizationPolicies/delete|Supprimer une stratégie de signature d’accès partagé Azure Customer Insights|
|/hubs/authorizationPolicies/read|Afficher une stratégie de signature d’accès partagé Azure Customer Insights|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Régénérer la clé primaire d’une stratégie de signature d’accès partagé Azure Customer Insights|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Régénérer la clé secondaire d’une stratégie de signature d’accès partagé Azure Customer Insights|
|/hubs/authorizationPolicies/write|Créer ou mettre à jour une stratégie de signature d’accès partagé Azure Customer Insights|
|/hubs/connectors/activate/action|Active un connecteur Azure Customer Insights|
|/hubs/connectors/activate/action|Active un connecteur Azure Customer Insights|
|/hubs/connectors/delete|Supprimer un connecteur Azure Customer Insights|
|/hubs/connectors/getruntimestatus/action|Obtient un état de runtime du connecteur Azure Customer Insights|
|/hubs/connectors/mappings/activate/action|Active un mappage de connecteur Azure Customer Insights|
|/hubs/connectors/mappings/delete|Supprimer un mappage de connecteur Azure Customer Insights|
|/hubs/connectors/mappings/operations/read|Lit le résultat d’une opération de mappage de connecteur Azure Customer Insights|
|/hubs/connectors/mappings/read|Afficher un mappage de connecteur Azure Customer Insights|
|/hubs/connectors/mappings/write|Créer ou mettre à jour un mappage de connecteur Azure Customer Insights|
|/hubs/connectors/operations/read|Lit le résultat d’une opération de connecteur Azure Customer Insights|
|/hubs/connectors/read|Afficher un connecteur Azure Customer Insights|
|/hubs/connectors/saveauthinfo/action|Crée ou met à jour des informations d’authentification du connecteur Azure Customer Insights|
|/hubs/connectors/update/action|Met à jour un connecteur Azure Customer Insights|
|/hubs/connectors/write|Créer ou mettre à jour un connecteur Azure Customer Insights|
|/hubs/crmmetadata/action|Crée ou met à jour des métadonnées Crm Azure Customer Insights|
|/hubs/crmmetadata/read|Lit des métadonnées Crm Azure Customer Insights|
|/hubs/delete|Supprimer un concentrateur Azure Customer Insights|
|/hubs/gdpr/delete|Supprime un Rgpd Azure Customer Insights|
|/hubs/gdpr/read|Lit un Rgpd Azure Customer Insights|
|/hubs/gdpr/write|Crée ou met à jour un Rgpd Azure Customer Insights|
|/hubs/getbillingcredits/read|Obtient des crédits de facturation du hub Azure Customer Insights|
|/hubs/getbillinghistory/read|Obtient l’historique de facturation du hub Azure Customer Insights|
|/hubs/images/delete|Supprime une image Azure Customer Insights|
|/hubs/images/read|Lit une image Azure Customer Insights|
|/hubs/images/write|Crée ou met à jour une image Azure Customer Insights|
|/hubs/interactions/delete|Supprime des interactions Azure Customer Insights|
|/hubs/interactions/operations/read|Lit le résultat d’une opération d’interaction Azure Customer Insights|
|/hubs/interactions/read|Afficher une interaction Azure Customer Insights|
|/hubs/interactions/suggestrelationshiplinks/action|Propose des liens de relation pour des interactions Azure Customer Insights|
|/hubs/interactions/write|Créer ou mettre à jour une interaction Azure Customer Insights|
|/hubs/kpi/delete|Supprimer un indicateur de performance clé Azure Customer Insights|
|/hubs/kpi/operations/read|Lit un résultat d’opération d’indicateurs de performance clés Azure Customer Insights|
|/hubs/kpi/read|Afficher un indicateur de performance clé Azure Customer Insights|
|/hubs/kpi/reprocess/action|Retraite des indicateurs de performance clés Azure Customer Insights|
|/hubs/kpi/write|Créer ou mettre à jour un indicateur de performance clé Azure Customer Insights|
|/hubs/links/delete|Supprime des liens Azure Customer Insights|
|/hubs/links/operations/read|Lit un résultat d’opération de liens Azure Customer Insights|
|/hubs/links/read|Lit des liens Azure Customer Insights|
|/hubs/links/write|Crée ou met à jour des liens Azure Customer Insights|
|/hubs/msemetadata/action|Crée ou met à jour des métadonnées Mse Azure Customer Insights|
|/hubs/msemetadata/read|Lit des métadonnées Mse Azure Customer Insights|
|/hubs/operationresults/read|Obtient les résultats d’opération de hub Azure Customer Insights|
|/hubs/predictions/delete|Supprime des prédictions Azure Customer Insights|
|/hubs/predictions/operations/read|Lit un résultat d’opération de prédictions Azure Customer Insights|
|/hubs/predictions/read|Lit des prédictions Azure Customer Insights|
|/hubs/predictions/write|Crée ou met à jour des prédictions Azure Customer Insights|
|/hubs/predictivematchpolicies/delete|Supprime des stratégies de correspondance prédictive Azure Customer Insights|
|/hubs/predictivematchpolicies/operations/read|Lit un résulat d’opération de stratégies de correspondance prédictive Azure Customer Insights|
|/hubs/predictivematchpolicies/read|Lit des stratégies de correspondance prédictive Azure Customer Insights|
|/hubs/predictivematchpolicies/write|Crée ou met à jour des stratégies de correspondance prédictive Azure Customer Insights|
|/hubs/profiles/delete|Supprime un profil Azure Customer Insights|
|/hubs/profiles/operations/read|Lit un résultat d’opération de profil Azure Customer Insights|
|/hubs/profiles/read|Afficher un profil Azure Customer Insights|
|/hubs/profiles/write|Écrire un profil Azure Customer Insights|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|Afficher le paramètre de diagnostic pour la ressource|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|Créer ou mettre à jour le paramètre de diagnostic pour la ressource|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|Obtenir les journaux disponibles pour la ressource|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|Afficher les mesures disponibles pour la ressource|
|/hubs/read|Afficher un concentrateur Azure Customer Insights|
|/hubs/relationshiplinks/delete|Supprime des liens de relation Azure Customer Insights|
|/hubs/relationshiplinks/operations/read|Lit un résultat d’opération de liens de relation Azure Customer Insights|
|/hubs/relationshiplinks/read|Lit des liens de relation Azure Customer Insights|
|/hubs/relationshiplinks/write|Crée ou met à jour des liens de relation Azure Customer Insights|
|/hubs/relationships/delete|Supprime des relations Azure Customer Insights|
|/hubs/relationships/operations/read|Lit un résultat d’opération de relations Azure Customer Insights|
|/hubs/relationships/read|Lit des relations Azure Customer Insights|
|/hubs/relationships/write|Crée ou met à jour des relations Azure Customer Insights|
|/hubs/roleAssignments/delete|Supprimer une affectation RBAC Azure Customer Insights|
|/hubs/roleAssignments/operations/read|Lit un résultat d’opération d’affectation Rbac Azure Customer Insights|
|/hubs/roleAssignments/read|Afficher une affectation RBAC Azure Customer Insights|
|/hubs/roleAssignments/write|Créer ou mettre à jour une affectation RBAC Azure Customer Insights|
|/hubs/roles/read|Lit des rôles Rbac Azure Customer Insights|
|/hubs/salesforcemetadata/action|Crée ou met à jour des métadonnées SalesForce Azure Customer Insights|
|/hubs/salesforcemetadata/read|Lit des métadonnées SalesForce Azure Customer Insights|
|/hubs/segments/delete|Supprime des segments Azure Customer Insights|
|/hubs/segments/dynamic/action|Gère des segments dynamiques Azure Customer Insights|
|/hubs/segments/read|Lit des segments Azure Customer Insights|
|/hubs/segments/static/action|Gère des segments statiques Azure Customer Insights|
|/hubs/segments/write|Crée ou met à jour des segments Azure Customer Insights|
|/hubs/sqlconnectionstrings/delete|Supprime des chaînes de connexion SQL Azure Customer Insights|
|/hubs/sqlconnectionstrings/read|Lit des chaînes de connexion SQL Azure Customer Insights|
|/hubs/sqlconnectionstrings/write|Crée ou met à jour des chaînes de connexion SQL Azure Customer Insights|
|/hubs/suggesttypeschema/action|Génère un schéma de type de suggestion à partir d’un échantillon de données|
|/hubs/tenantmanagement/read|Gère des paramètres de hub Azure Customer Insights|
|/hubs/views/delete|Supprimer une vue d’application Azure Customer Insights|
|/hubs/views/read|Afficher une vue d’application Azure Customer Insights|
|/hubs/views/write|Créer ou mettre à jour une vue d’application Azure Customer Insights|
|/hubs/widgettypes/read|Lit des types de widgets d’application Azure Customer Insights|
|/hubs/write|Créer ou mettre à jour un concentrateur Azure Customer Insights|
|/operations/read|Lit des métadonnées d’API Azure Customer Insights|
|/register/action|Enregistre l’abonnement pour le fournisseur de ressources Customer Insights et permet la création de ressources Customer Insights|
|/unregister/action|Désinscrit l’abonnement pour le fournisseur de ressources Customer Insights|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Opération | Description |
|---|---|
|/catalogs/delete|Supprime le catalogue.|
|/catalogs/read|Affiche les propriétés du ou des catalogues associés à l’abonnement ou au groupe de ressources.|
|/catalogs/write|Crée un catalogue ou met à jour les balises et les propriétés du catalogue.|
|/checkNameAvailability/action|Vérifie la disponibilité du nom de catalogue pour le locataire.|
|/operations/read|Répertorie les opérations disponibles sur le fournisseur de ressources Microsoft.DataCatalog.|
|/register/action|Inscrit l’abonnement auprès du fournisseur de ressources Microsoft.DataCatalog.|
|/unregister/action|Désinscrit un abonnement du fournisseur de ressources Microsoft.DataCatalog.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Opération | Description |
|---|---|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/datafactories/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour les fabriques|
|/factories/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/factories/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/factories/providers/Microsoft.Insights/logDefinitions/read|Obtient les journaux disponibles pour les fabriques|
|/factories/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour les fabriques|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Opération | Description |
|---|---|
|/accounts/computePolicies/delete|Supprime une stratégie de calcul.|
|/accounts/computePolicies/read|Obtient des informations sur une stratégie de calcul.|
|/accounts/computePolicies/write|Crée ou met à jour une stratégie de calcul.|
|/accounts/dataLakeStoreAccounts/delete|Dissocie un compte Data Lake Store d’un compte Data Lake Analytics.|
|/accounts/dataLakeStoreAccounts/read|Obtient des informations sur un compte Data Lake Store lié d’un compte Data Lake Analytics.|
|/accounts/dataLakeStoreAccounts/write|Crée ou met à jour un compte Data Lake Store lié d’un compte Data Lake Analytics.|
|/accounts/delete|Supprime un compte Data Lake Analytics.|
|/accounts/firewallRules/delete|Supprimer une règle de pare-feu.|
|/accounts/firewallRules/read|Obtenir des informations sur une règle de pare-feu.|
|/accounts/firewallRules/write|Créer ou mettre à jour une règle de pare-feu.|
|/accounts/operationResults/read|Obtient le résultat d’une opération de compte Data Lake Analytics.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Obtient les paramètres de diagnostic pour le compte Data Lake Analytics.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour les paramètres de diagnostic pour le compte Data Lake Analytics.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Obtient les journaux disponibles pour le compte Data Lake Analytics.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour le compte Data Lake Analytics.|
|/accounts/read|Obtient des informations sur un compte Data Lake Analytics existant.|
|/accounts/storageAccounts/Containers/listSasTokens/action|Affiche les jetons SAP pour des conteneurs de stockage d’un compte de stockage lié d’un compte Data Lake Analytics.|
|/accounts/storageAccounts/Containers/read|Obtient les conteneurs d’un compte de stockage lié d’un compte Data Lake Analytics.|
|/accounts/storageAccounts/delete|Dissocie un compte de stockage d’un compte Data Lake Analytics.|
|/accounts/storageAccounts/read|Obtient des informations sur un compte de stockage lié d’un compte Data Lake Analytics.|
|/accounts/storageAccounts/write|Crée ou met à jour un compte de stockage lié d’un compte Data Lake Analytics.|
|/accounts/TakeOwnership/action|Accorde des autorisations pour annuler des travaux soumis par d’autres utilisateurs.|
|/accounts/write|Crée ou met à jour un compte Data Lake Analytics.|
|/locations/capability/read|Obtient les informations de fonctionnalité d’un abonnement concernant l’utilisation de Data Lake Analytics.|
|/locations/checkNameAvailability/action|Vérifie la disponibilité d’un nom de compte Data Lake Analytics.|
|/locations/operationResults/read|Obtient le résultat d’une opération de compte Data Lake Analytics.|
|/operations/read|Obtient les opérations disponibles de Data Lake Analytics.|
|/register/action|Inscrit un abonnement à Data Lake Analytics.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Opération | Description |
|---|---|
|/accounts/delete|Supprime un compte Data Lake Store.|
|/accounts/enableKeyVault/action|Active le coffre de clés pour un compte Data Lake Store.|
|/accounts/firewallRules/delete|Supprimer une règle de pare-feu.|
|/accounts/firewallRules/read|Obtenir des informations sur une règle de pare-feu.|
|/accounts/firewallRules/write|Créer ou mettre à jour une règle de pare-feu.|
|/accounts/operationResults/read|Obtient le résultat d’une opération de compte Data Lake Store.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Obtient les paramètres de diagnostic pour le compte Data Lake Store.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour les paramètres de diagnostic pour le compte Data Lake Store.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Obtient les journaux disponibles pour le compte Data Lake Store.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour le compte Data Lake Store.|
|/accounts/read|Obtient des informations sur un compte Data Lake Store existant.|
|/accounts/Superuser/action|Accorde des privilèges de superutilisateur sur Data Lake Store quand accordés avec Microsoft.Authorization/roleAssignments/write.|
|/accounts/trustedIdProviders/delete|Supprimer un fournisseur d’identité approuvé.|
|/accounts/trustedIdProviders/read|Obtenir des informations sur un fournisseur d’identité approuvé.|
|/accounts/trustedIdProviders/write|Créer ou mettre à jour un fournisseur d’identité approuvé.|
|/accounts/write|Crée ou met à jour un compte Data Lake Store.|
|/locations/capability/read|Obtient les informations de fonctionnalité d’un abonnement concernant l’utilisation de Data Lake Store.|
|/locations/checkNameAvailability/action|Vérifie la disponibilité d’un nom de compte Data Lake Store.|
|/locations/operationResults/read|Obtient le résultat d’une opération de compte Data Lake Store.|
|/operations/read|Obtient les opérations disponibles de Data Lake Store.|
|/register/action|Inscrit un abonnement à Data Lake Store.|

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

| Opération | Description |
|---|---|
|/locations/performanceTiers/read|Retourne la liste des niveaux de performances disponibles.|
|/performanceTiers/read|Retourne la liste des niveaux de performances disponibles.|
|/servers/delete|Supprime un serveur existant.|
|/servers/firewallRules/delete|Supprime une règle de pare-feu existante.|
|/servers/firewallRules/read|Retourne la liste des règles de pare-feu pour un serveur ou obtient les propriétés de la règle de pare-feu spécifiée.|
|/servers/firewallRules/write|Crée une règle de pare-feu avec les paramètres spécifiés ou met à jour une règle existante.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Renvoie les types de mesures disponibles pour les bases de données|
|/servers/read|Retourner la liste des serveurs ou obtenir les propriétés pour le serveur spécifié.|
|/servers/recoverableServers/read|Retourne les informations du serveur MySQL récupérables|
|/servers/virtualNetworkRules/delete|Supprime une règle de réseau virtuel existante|
|/servers/virtualNetworkRules/read|Retourne la liste des règles de réseau virtuel ou obtient les propriétés de la règle de réseau virtuel spécifiée.|
|/servers/virtualNetworkRules/write|Crée une règle de réseau virtuel avec les paramètres spécifiés ou met à jour les propriétés ou balises de la règle de réseau virtuel spécifiée.|
|/servers/write|Crée un serveur avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le serveur spécifié.|

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

| Opération | Description |
|---|---|
|/locations/performanceTiers/read|Retourne la liste des niveaux de performances disponibles.|
|/performanceTiers/read|Retourne la liste des niveaux de performances disponibles.|
|/servers/delete|Supprime un serveur existant.|
|/servers/firewallRules/delete|Supprime une règle de pare-feu existante.|
|/servers/firewallRules/read|Retourne la liste des règles de pare-feu pour un serveur ou obtient les propriétés de la règle de pare-feu spécifiée.|
|/servers/firewallRules/write|Crée une règle de pare-feu avec les paramètres spécifiés ou met à jour une règle existante.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Retourne les types de journaux disponibles pour les bases de données|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Renvoie les types de mesures disponibles pour les bases de données|
|/servers/read|Retourner la liste des serveurs ou obtenir les propriétés pour le serveur spécifié.|
|/servers/recoverableServers/read|Retourne les informations du serveur PostgreSQL récupérables|
|/servers/virtualNetworkRules/delete|Supprime une règle de réseau virtuel existante|
|/servers/virtualNetworkRules/read|Retourne la liste des règles de réseau virtuel ou obtient les propriétés de la règle de réseau virtuel spécifiée.|
|/servers/virtualNetworkRules/write|Crée une règle de réseau virtuel avec les paramètres spécifiés ou met à jour les propriétés ou balises de la règle de réseau virtuel spécifiée.|
|/servers/write|Crée un serveur avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le serveur spécifié.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Opération | Description |
|---|---|
|/checkNameAvailability/Action|Vérifier que le nom IotHub est disponible|
|/checkProvisioningServiceNameAvailability/Action|Vérifier que le nom IotHub est disponible|
|/ElasticPools/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/ElasticPools/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/elasticPools/iotHubTenants/Delete|Supprime la ressource du locataire IotHub|
|/ElasticPools/IotHubTenants/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/ElasticPools/IotHubTenants/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete|Supprimer le groupe de consommateurs EventHub|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read|Afficher les groupes de consommateurs EventHub|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write|Créer un groupe de consommateurs EventHub|
|/elasticPools/iotHubTenants/exportDevices/Action|Exporter des appareils|
|/elasticPools/iotHubTenants/getStats/Read|Obtient la ressource de statistiques du locataire IotHub|
|/elasticPools/iotHubTenants/importDevices/Action|Importer des appareils|
|/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action|Obtient la clé du locataire IotHub|
|/elasticPools/iotHubTenants/jobs/Read|Obtenir des informations sur les travaux soumis à un concentrateur IotHub donné|
|/elasticPools/iotHubTenants/listKeys/Action|Obtient les clés du locataire IotHub|
|/ElasticPools/IotHubTenants/logDefinitions/read|Obtient les définitions de journal disponibles pour le service IotHub|
|/ElasticPools/IotHubTenants/metricDefinitions/read|Obtenir les mesures disponibles pour le service IotHub|
|/elasticPools/iotHubTenants/quotaMetrics/Read|Obtenir les mesures de quota|
|/elasticPools/iotHubTenants/Read|Obtient la ressource du locataire IotHub|
|/elasticPools/iotHubTenants/routing/routes/$testall/Action|Tester un message sur l’ensemble des itinéraires existants|
|/elasticPools/iotHubTenants/routing/routes/$testnew/Action|Tester un message sur un itinéraire test fourni|
|/elasticPools/iotHubTenants/routingEndpointsHealth/Read|Afficher l’état de tous les points de terminaison de routage pour un concentrateur IotHub|
|/elasticPools/iotHubTenants/Write|Crée ou met à jour la ressource du locataire IotHub|
|/ElasticPools/metricDefinitions/read|Obtenir les mesures disponibles pour le service IotHub|
|/iotHubs/certificates/generateVerificationCode/Action|Génère le code de vérification|
|/iotHubs/certificates/verify/Action|Vérifie la ressource de certificat|
|/iotHubs/Delete|Supprimer une ressource IotHub|
|/IotHubs/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/IotHubs/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/iotHubs/eventGridFilters/Delete|Supprime le filtre Event Grid|
|/iotHubs/eventGridFilters/Read|Obtient le filtre Event Grid|
|/iotHubs/eventGridFilters/Write|Crée ou met à jour un filtre Event Grid existant|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|Supprimer le groupe de consommateurs EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Afficher les groupes de consommateurs EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Créer un groupe de consommateurs EventHub|
|/iotHubs/exportDevices/Action|Exporter des appareils|
|/iotHubs/importDevices/Action|Importer des appareils|
|/iotHubs/iotHubKeys/listkeys/Action|Obtenir la clé IotHub pour le nom spécifié|
|/iotHubs/iotHubStats/Read|Afficher les statistiques IotHub|
|/iotHubs/jobs/Read|Obtenir des informations sur les travaux soumis à un concentrateur IotHub donné|
|/iotHubs/listkeys/Action|Obtenir toutes les clés IotHub|
|/IotHubs/logDefinitions/read|Obtient les définitions de journal disponibles pour le service IotHub|
|/IotHubs/metricDefinitions/read|Obtenir les mesures disponibles pour le service IotHub|
|/iotHubs/quotaMetrics/Read|Obtenir les mesures de quota|
|/iotHubs/Read|Afficher les ressources IotHub|
|/iotHubs/routing/$testall/Action|Tester un message sur l’ensemble des itinéraires existants|
|/iotHubs/routing/$testnew/Action|Tester un message sur un itinéraire test fourni|
|/iotHubs/routingEndpointsHealth/Read|Afficher l’état de tous les points de terminaison de routage pour un concentrateur IotHub|
|/iotHubs/skus/Read|Afficher les références IotHub valides|
|/iotHubs/Write|Créer ou mettre à jour une ressource IotHub|
|/operations/Read|Afficher toutes les opérations du fournisseur de ressources|
|/provisioningServices/certificates/generateVerificationCode/Action|Génère le code de vérification|
|/provisioningServices/certificates/verify/Action|Vérifie la ressource de certificat|
|/provisioningServices/Delete|Supprime la ressource IotDps|
|/provisioningServices/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/provisioningServices/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/provisioningServices/listkeys/Action|Obtient toutes les clés IotDps|
|/provisioningServices/logDefinitions/read|Obtient les définitions de journal disponibles pour le service d’approvisionnement|
|/provisioningServices/metricDefinitions/read|Obtient les métriques disponibles pour le service d’approvisionnement|
|/provisioningServices/ProvisioningServiceKeys/listkeys/Action|Obtient les clés IotDps pour le nom de clé|
|/provisioningServices/Read|Obtient la ressource IotDps|
|/provisioningServices/skus/Read|Obtient les références (SKU) IotDps valides|
|/provisioningServices/Write|Crée une ressource IotDps|
|/register/action|Inscrire l’abonnement pour le fournisseur de ressources IotHub et activer la création de ressources IotHub|
|/register/action|Inscrire l’abonnement pour le fournisseur de ressources IotHub et activer la création de ressources IotHub|
|/usages/Read|Obtenir des informations sur l’utilisation de l’abonnement pour ce fournisseur|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Opération | Description |
|---|---|
|/labCenters/delete|Supprime les centres lab.|
|/labCenters/read|Lit les centres lab.|
|/labCenters/write|Ajoute ou modifie les centres lab.|
|/labs/artifactSources/armTemplates/read|Lire les modèles Azure Resource Manager.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|Générer un modèle ARM pour l’artefact donné, télécharger les fichiers requis vers un compte de stockage et valider l’artefact généré.|
|/labs/artifactSources/artifacts/read|Lire des artefacts.|
|/labs/artifactSources/delete|Supprimer des sources d’artefact.|
|/labs/artifactSources/read|Lire des sources d’artefact.|
|/labs/artifactSources/write|Ajouter ou modifier des sources d’artefact.|
|/labs/ClaimAnyVm/action|Revendiquer une machine virtuelle exigible aléatoire dans le laboratoire.|
|/labs/costs/read|Lire les coûts.|
|/labs/costs/write|Ajouter ou modifier les coûts.|
|/labs/CreateEnvironment/action|Créer des machines virtuelles dans un laboratoire.|
|/labs/customImages/delete|Supprimer des images personnalisées.|
|/labs/customImages/read|Lire des images personnalisées.|
|/labs/customImages/write|Ajouter ou modifier des images personnalisées.|
|/labs/delete|Supprimer des laboratoires.|
|/labs/ExportResourceUsage/action|Exporter les données d’utilisation des ressources du laboratoire vers un compte de stockage.|
|/labs/formulas/delete|Supprimer des formules.|
|/labs/formulas/read|Lire des formules.|
|/labs/formulas/write|Ajouter ou modifier des formules.|
|/labs/galleryImages/read|Lire des images de la galerie.|
|/labs/GenerateUploadUri/action|Générer un URI pour charger des images de disque personnalisées dans un laboratoire.|
|/labs/ImportVirtualMachine/action|Importe une machine virtuelle dans un autre laboratoire.|
|/labs/ListVhds/action|Répertorier les images de disque disponibles pour la création d’images personnalisées.|
|/labs/notificationChannels/delete|Supprimer des canaux de notification.|
|/labs/notificationChannels/Notify/action|Envoyer une notification au canal fourni.|
|/labs/notificationChannels/read|Lire les canaux de notification.|
|/labs/notificationChannels/write|Ajouter ou modifier des canaux de notification.|
|/labs/policySets/EvaluatePolicies/action|Évaluer la stratégie de laboratoire.|
|/labs/policySets/policies/delete|Supprimer des stratégies.|
|/labs/policySets/policies/read|Lire les stratégies.|
|/labs/policySets/policies/write|Ajouter ou modifier des stratégies.|
|/labs/read|Afficher des laboratoires.|
|/labs/schedules/delete|Supprimer des planifications.|
|/labs/schedules/Execute/action|Exécuter une planification.|
|/labs/schedules/ListApplicable/action|Répertorier toutes les planifications applicables.|
|/labs/schedules/read|Lire les planifications.|
|/labs/schedules/write|Ajouter ou modifier des planifications.|
|/labs/serviceRunners/delete|Supprimer des exécuteurs de service.|
|/labs/serviceRunners/read|Lire des exécuteurs de service.|
|/labs/serviceRunners/write|Ajouter ou modifier des exécuteurs de service.|
|/labs/users/delete|Supprimer des profils utilisateur.|
|/labs/users/disks/Attach/action|Créer le bail du disque et l’associer à la machine virtuelle.|
|/labs/users/disks/delete|Supprimer des disques.|
|/labs/users/disks/Detach/action|Casser et dissocier le bail du disque associé à la machine virtuelle.|
|/labs/users/disks/delete|Afficher les disques.|
|/labs/users/disks/write|Ajouter ou modifier des disques.|
|/labs/users/environments/delete|Supprimer des environnements.|
|/labs/users/environments/read|Afficher les environnements.|
|/labs/users/environments/write|Ajouter ou modifier des environnements.|
|/labs/users/read|Afficher les profils utilisateur.|
|/labs/users/secrets/delete|Supprimer des clés secrètes.|
|/labs/users/secrets/read|Afficher les clés secrètes.|
|/labs/users/secrets/write|Ajouter ou modifier des clés secrètes.|
|/labs/users/serviceFabrics/delete|Supprime les structures Service Fabric.|
|/labs/users/serviceFabrics/ListApplicableSchedules/action|Répertorier toutes les planifications applicables.|
|/labs/users/serviceFabrics/read|Lit les structures Service Fabric.|
|/labs/users/serviceFabrics/schedules/delete|Supprimer des planifications.|
|/labs/users/serviceFabrics/schedules/Execute/action|Exécuter une planification.|
|/labs/users/serviceFabrics/schedules/read|Lire les planifications.|
|/labs/users/serviceFabrics/schedules/write|Ajouter ou modifier des planifications.|
|/labs/users/serviceFabrics/Start/action|Démarre une structure Service Fabric.|
|/labs/users/serviceFabrics/Stop/action|Arrête une structure Service Fabric.|
|/labs/users/serviceFabrics/write|Ajoute ou modifie des structures Service Fabric.|
|/labs/users/write|Ajouter ou modifier des profils utilisateur.|
|/labs/virtualMachines/AddDataDisk/action|Attacher un nouveau disque de données ou un disque de données existant à la machine virtuelle.|
|/labs/virtualMachines/ApplyArtifacts/action|Appliquer des artefacts à une machine virtuelle.|
|/labs/virtualMachines/Claim/action|Prendre possession d’une machine virtuelle existante.|
|/labs/virtualMachines/delete|Supprimer des machines virtuelles.|
|/labs/virtualMachines/DetachDataDisk/action|Détacher le disque spécifié de la machine virtuelle.|
|/labs/virtualMachines/ListApplicableSchedules/action|Répertorier toutes les planifications applicables.|
|/labs/virtualMachines/read|Lire les machines virtuelles.|
|/labs/virtualMachines/Restart/action|Redémarre une machine virtuelle.|
|/labs/virtualMachines/schedules/delete|Supprimer des planifications.|
|/labs/virtualMachines/schedules/Execute/action|Exécuter une planification.|
|/labs/virtualMachines/schedules/read|Lire les planifications.|
|/labs/virtualMachines/schedules/write|Ajouter ou modifier des planifications.|
|/labs/virtualMachines/Start/action|Démarrer une machine virtuelle.|
|/labs/virtualMachines/Stop/action|Arrêt d’une machine virtuelle|
|/labs/virtualMachines/TransferDisks/action|Transfère la propriété de disques de données de machine virtuelle à vous-même|
|/labs/virtualMachines/UnClaim/action|Cesse d’être propriétaire d’une machine virtuelle existante|
|/labs/virtualMachines/write|Ajouter ou modifier des machines virtuelles.|
|/labs/virtualNetworks/delete|Supprimer des réseaux virtuels.|
|/labs/virtualNetworks/read|Lire les réseaux virtuels.|
|/labs/virtualNetworks/write|Ajouter ou modifier des réseaux virtuels.|
|/labs/write|Ajouter ou modifier des laboratoires.|
|/locations/operations/read|Opérations de lecture.|
|/register/action|Enregistrer l’abonnement.|
|/schedules/delete|Supprimer des planifications.|
|/schedules/Execute/action|Exécuter une planification.|
|/schedules/read|Lire les planifications.|
|/schedules/Retarget/action|Mettre à jour l’ID de ressource cible d’une planification.|
|/schedules/write|Ajouter ou modifier des planifications.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Opération | Description |
|---|---|
|/databaseAccountNames/read|Vérifier la disponibilité du nom.|
|/databaseAccounts/changeResourceGroup/action|Modifier le groupe de ressources d’un compte de base de données.|
|/databaseAccounts/databases/collections/metricDefinitions/read|Lire les définitions de mesures de la collection.|
|/databaseAccounts/databases/collections/metrics/read|Lire les mesures de la collection.|
|/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read|Lit les métriques de niveau de clé de partition de compte de base de données|
|/databaseAccounts/databases/collections/partitions/metrics/read|Lit les métriques de niveau de partition de compte de base de données|
|/databaseAccounts/databases/collections/partitions/usages/read|Lit les utilisations de niveau de partition de compte de base de données|
|/databaseAccounts/databases/collections/usages/read|Lire les données d’utilisation de la collection.|
|/databaseAccounts/databases/metricDefinitions/read|Lire les définitions de mesures de la base de données.|
|/databaseAccounts/databases/metrics/read|Lire les mesures de la base de données.|
|/databaseAccounts/databases/usages/read|Lire les données d’utilisation de la base de données.|
|/databaseAccounts/delete|Supprimer les comptes de base de données.|
|/databaseAccounts/failoverPriorityChange/action|Modifier les priorités de basculement des régions pour un compte de base de données. Cela permet d’effectuer un basculement manuel.|
|/databaseAccounts/listConnectionStrings/action|Obtenir les chaînes de connexion d’un compte de base de données.|
|/databaseAccounts/listKeys/action|Répertorier les clés d’un compte de base de données.|
|/databaseAccounts/metricDefinitions/read|Lire les définitions de mesures du compte de base de données.|
|/databaseAccounts/metrics/read|Lire les mesures du compte de base de données.|
|/databaseAccounts/operationResults/read|Lit l’état de l’opération asynchrone|
|/databaseAccounts/percentile/metrics/read|Lit les métriques de latence|
|/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read|Lit les métriques de latence pour une source et une région cible spécifiques|
|/databaseAccounts/percentile/targetRegion/metrics/read|Lit les métriques de latence pour une région cible spécifique|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read|Obtient les catégories de journal disponibles pour le compte de base de données|
|/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour le compte de base de données|
|/databaseAccounts/read|Lire un compte de base de données.|
|/databaseAccounts/readonlykeys/action|Lire les clés en lecture seule du compte de base de données.|
|/databaseAccounts/readonlykeys/read|Lire les clés en lecture seule du compte de base de données.|
|/databaseAccounts/regenerateKey/action|Régénérer les clés d’un compte de base de données.|
|/databaseAccounts/region/databases/collections/metrics/read|Lit les métriques de collection régionales.|
|/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read|Lit les métriques régionales de niveau de clé de partition de compte de base de données|
|/databaseAccounts/region/databases/collections/partitions/metrics/read|Lit les métriques régionales de niveau de partition de compte de base de données|
|/databaseAccounts/region/databases/collections/partitions/read|Lit les partitions de compte de base de données dans une collection|
|/databaseAccounts/region/metrics/read|Lit les métriques de compte de région et de base de données.|
|/databaseAccounts/usages/read|Lire les données d’utilisation du compte de base de données.|
|/databaseAccounts/write|Mettre à jour les comptes de base de données.|
|/locations/deleteVirtualNetworkOrSubnets/action|Informe Microsoft.DocumentDB qu’un sous-réseau ou réseau virtuel est en cours de suppression|
|/operationResults/read|Lit l’état de l’opération asynchrone|
|/operations/read|Lit les opérations disponibles pour Microsoft DocumentDB |
|/register/action| Inscrit le fournisseur de ressources Microsoft DocumentDB pour l’abonnement|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Opération | Description |
|---|---|
|/checkDomainAvailability/Action|Vérifier si un domaine est disponible à l’achat.|
|/domains/Delete|Supprimer un domaine existant.|
|/domains/domainownershipidentifiers/Delete|Supprime l’identificateur de propriété|
|/domains/domainownershipidentifiers/Read|Affiche les identificateurs de propriété|
|/domains/domainownershipidentifiers/Read|Obtient l’identificateur de propriété|
|/domains/domainownershipidentifiers/Write|Crée ou met à jour l’identificateur|
|/domains/operationresults/Read|Obtenir une opération de domaine.|
|/domains/operations/Read|Affiche toutes les opérations de l’inscription de domaine de service d’application|
|/domains/Read|Obtenir la liste des domaines.|
|/domains/Read|Obtient le domaine|
|/domains/renew/Action|Renouvelle un domaine existant.|
|/domains/Write|Ajouter ou mettre à jour un domaine.|
|/generateSsoRequest/Action|Générer une demande de connexion au centre de contrôle du domaine.|
|/listDomainRecommendations/Action|Récupérer les recommandations de domaine en fonction de mots clés.|
|/register/action|Inscrire le fournisseur de ressources de domaines Microsoft pour l’abonnement.|
|/topLevelDomains/listAgreements/Action|Affiche l’action d’accord|
|/topLevelDomains/Read|Obtient les domaines de niveau supérieur|
|/topLevelDomains/Read|Obtient le domaine de niveau supérieur|
|/validateDomainRegistrationInformation/Action|Valider l’objet d’achat de domaine sans le soumettre.|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Opération | Description |
|---|---|
|/lcsprojects/clouddeployments/read|Afficher les déploiements de test Microsoft Dynamics AX 2012 R3 dans un projet Microsoft Dynamics Lifecycle Services appartenant à un utilisateur|
|/lcsprojects/clouddeployments/write|Créer un déploiement de test Microsoft Dynamics AX 2012 R3 dans un projet Microsoft Dynamics Lifecycle Services appartenant à un utilisateur. Les déploiements peuvent être gérés depuis le portail de gestion Azure.|
|/lcsprojects/connectors/read|Lire les connecteurs appartenant à un projet Microsoft Dynamics Lifecycle Services|
|/lcsprojects/connectors/write|Créer et mettre à jour des connecteurs appartenant à un projet Microsoft Dynamics Lifecycle Services|
|/lcsprojects/delete|Supprimer des projets Microsoft Dynamics Lifecycle Services appartenant à l’utilisateur|
|/lcsprojects/read|Afficher les projets Microsoft Dynamics Lifecycle Services appartenant à un utilisateur|
|/lcsprojects/write|Créer et mettre à jour des projets Microsoft Dynamics Lifecycle Services appartenant à l’utilisateur. Seules les propriétés de nom et de description peuvent être mises à jour. L’abonnement et l’emplacement associés au projet ne peuvent pas être mis à jour après la création.|

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

| Opération | Description |
|---|---|
|/eventSubscriptions/delete|Supprime un abonnement à un événement|
|/eventSubscriptions/getFullUrl/action|Obtient l’URL complète de l’abonnement à l’événement|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour les abonnements aux événements|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour les abonnements aux événements|
|/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour les abonnements aux événements|
|/eventSubscriptions/read|Lit un abonnement à un événement|
|/eventSubscriptions/write|Crée ou met à jour un abonnement à un événement|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour des rubriques|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour des rubriques|
|/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour les rubriques|
|/register/action|Enregistre l’abonnement à un événement pour le fournisseur de ressources Event Grid et permet la création d’abonnements Event Grid.|
|/topics/delete|Supprimer une rubrique|
|/topics/listKeys/action|Affiche les clés pour la rubrique|
|/topics/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour des rubriques|
|/topics/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour des rubriques|
|/topics/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour les rubriques|
|/topics/read|Lit une rubrique|
|/topics/regenerateKey/action|Régénère une clé pour la rubrique|
|/topics/write|Crée ou met à jour une rubrique|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Opération | Description |
|---|---|
|/checkNameAvailability/action|Vérifier la disponibilité d’un espace de noms sous un abonnement donné|
|/checkNamespaceAvailability/action|Vérifier la disponibilité d’un espace de noms sous un abonnement donné Cette API étant déconseillée, utilisez CheckNameAvailabiltiy à la place.|
|/namespaces/authorizationRules/action|Met à jour une règle d’autorisation d’espace de noms. Cette API est déconseillée. Utilisez un appel PUT à la place pour mettre à jour la règle d’autorisation d’espace de noms... Cette opération n’est pas prise en charge sur la version d’API 2017-04-01.|
|/namespaces/authorizationRules/delete|Supprimer une règle d’autorisation de ressource Namespace. La règle d’autorisation de ressource Namespace par défaut ne peut pas être supprimée. |
|/namespaces/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à la ressource Namespace|
|/namespaces/authorizationRules/read|Obtenir la liste des descriptions des règles d’autorisation des ressources Namespaces.|
|/namespaces/authorizationRules/regenerateKeys/action|Régénère la clé primaire ou secondaire pour la ressource.|
|/namespaces/authorizationRules/write|Créer des règles d’autorisation au niveau d’une ressource Namespace et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour.|
|/namespaces/Delete|Supprimer une ressource Namespace|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Obtient les clés de règles d’autorisation pour l’espace de noms principal de récupération d’urgence|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Obtient les règles d’autorisation de l’espace de noms principal de récupération d’urgence|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Désactive la récupération d’urgence et arrête la réplication des changements de l’espace de noms principal sur l’espace de noms secondaire.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Vérifie la disponibilité d’un alias d’espace de noms sous un abonnement donné.|
|/namespaces/disasterRecoveryConfigs/delete|Supprime la configuration de récupération d’urgence associée à l’espace de noms. Cette opération peut être appelée uniquement via l’espace de noms principal.|
|/namespaces/disasterRecoveryConfigs/failover/action|Appelle un basculement de géorécupération d’urgence et reconfigure l’alias d’espace de noms pour qu’il pointe vers l’espace de noms secondaire.|
|/namespaces/disasterRecoveryConfigs/read|Obtient la configuration de récupération d’urgence associée à l’espace de noms.|
|/namespaces/disasterRecoveryConfigs/write|Crée ou met à jour la configuration de récupération d’urgence associée à l’espace de noms.|
|/namespaces/eventhubs/authorizationRules/action|Met à jour EventHub. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. Règles d’autorisation. Utilisez un appel PUT pour mettre à jour la règle d’autorisation.|
|/namespaces/eventhubs/authorizationRules/delete|Supprimer des règles d’autorisation EventHub|
|/namespaces/eventhubs/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à EventHub|
|/namespaces/eventhubs/authorizationRules/read| Obtenir la liste des règles d’autorisation EventHub|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|Régénère la clé primaire ou secondaire pour la ressource.|
|/namespaces/eventhubs/authorizationRules/write|Créer des règles d’autorisation EventHub et mettre à jour leurs propriétés. Les droits d’accès des règles d’autorisation peuvent être mis à jour.|
|/namespaces/eventHubs/consumergroups/Delete|Supprimer des ressources du groupe de consommateurs|
|/namespaces/eventHubs/consumergroups/read|Obtenir la liste des descriptions de ressources du groupe de consommateurs|
|/namespaces/eventHubs/consumergroups/write|Créer ou mettre à jour les propriétés d’un groupe de consommateurs|
|/namespaces/eventhubs/Delete|Supprimer des ressources EventHub|
|/namespaces/eventhubs/read|Obtenir la liste des descriptions de ressources EventHub|
|/namespaces/eventhubs/write|Créer ou mettre à jour des propriétés EventHub|
|/namespaces/messagingPlan/read|Obtient le plan de messagerie pour un espace de noms. Cette API est déconseillée. Les propriétés exposées via la ressource MessagingPlan sont déplacées vers la ressource d’espace de noms (parente) dans les versions ultérieures de l’API. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01.|
|/namespaces/messagingPlan/write|Met à jour le plan de messagerie pour un espace de noms. Cette API est déconseillée. Les propriétés exposées via la ressource MessagingPlan sont déplacées vers la ressource d’espace de noms (parente) dans les versions ultérieures de l’API. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01.|
|/namespaces/operationresults/read|Obtient l’état de l’opération d’espace de noms|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Obtenir la liste des descriptions des ressources des paramètres de diagnostics Namespace.|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Obtenir la liste des descriptions des ressources des paramètres de diagnostics Namespace.|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Obtenir la liste des descriptions des ressources des journaux Namespace.|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Obtenir la liste des descriptions des mesures de ressource Namespace.|
|/namespaces/read|Obtenir la liste des descriptions des ressources Namespace.|
|/namespaces/write|Créer une ressource Namespace et mettre à jour ses propriétés. Les balises et la capacité de l’espace de noms sont les propriétés qui peuvent être mises à jour.|
|/operations/read|Obtient les opérations|
|/register/action|Inscrire l’abonnement pour le fournisseur de ressources EventHub et activer la création de ressources EventHub|
|/sku/read|Obtient la liste des descriptions des ressources de référence (SKU).|
|/sku/regions/read|Obtient la liste des descriptions de ressources SkuRegions|
|/unregister/action|Enregistre le fournisseur de ressources EventHub|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Opération | Description |
|---|---|
|/features/read|Afficher les fonctionnalités d’un abonnement|
|/providers/features/read|Afficher les fonctionnalités d’un abonnement pour un fournisseur de ressources donné|
|/providers/features/register/action|Enregistrer les fonctionnalités d’un abonnement pour un fournisseur de ressources donné|
|/providers/features/unregister/action|Désinscrit la fonctionnalité pour un abonnement dans un fournisseur de ressources donné.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Opération | Description |
|---|---|
|/clusters/changerdpsetting/action|Modifier le paramètre RDP du cluster HDInsight|
|/clusters/configurations/action|Mettre à jour la configuration du cluster HDInsight|
|/clusters/configurations/read|Afficher les configurations du cluster HDInsight|
|/clusters/delete|Supprimer un cluster HDInsight|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour le cluster HDInsight de ressource|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour le cluster HDInsight de ressource|
|/clusters/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour le cluster HDInsight|
|/clusters/read|Obtenir des informations sur un cluster HDInsight|
|/clusters/roles/resize/action|Redimensionner un cluster HDInsight|
|/clusters/write|Créer ou mettre à jour un cluster HDInsight|
|/locations/capabilities/read|Afficher les fonctionnalités de l’abonnement|
|/locations/checkNameAvailability/read|Vérifier la disponibilité du nom|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Opération | Description |
|---|---|
|/jobs/delete|Supprime un travail existant.|
|/jobs/listBitLockerKeys/action|Génère les clés BitLocker pour le travail spécifié.|
|/jobs/read|Affiche les propriétés du travail spécifié ou retourne la liste des travaux.|
|/jobs/write|Crée un travail avec les paramètres spécifiés ou met à jour les propriétés ou les balises pour le travail spécifié.|
|/locations/read|Affiche les propriétés de l’emplacement spécifié ou retourne la liste des emplacements.|
|/register/action|Registers the subscription for the import/export resource provider and enables the creation of import/export jobs.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| Opération | Description |
|---|---|
|/ActionGroups/Delete|Supprime un groupe d’actions|
|/ActionGroups/Read|Lit un groupe d’actions|
|/ActionGroups/Write|Écrit un groupe d’actions|
|/ActivityLogAlerts/Activated/Action|Déclencher une alerte de journal d’activité|
|/ActivityLogAlerts/Delete|Supprime une alerte du journal d’activité|
|/ActivityLogAlerts/Read|Lit une alerte du journal d’activité|
|/ActivityLogAlerts/Write|Écrit une alerte du journal d’activité|
|/AlertRules/Activated/Action|Règle d’alerte activée|
|/AlertRules/Delete|Supprimer une configuration de règle d’alerte|
|/AlertRules/Incidents/Read|Lire une configuration d’incident de règle d’alerte|
|/AlertRules/Read|Lire une configuration de règle d’alerte|
|/AlertRules/Resolved/Action|Règle d’alerte résolue|
|/AlertRules/Throttled/Action|Règle d’alerte limitée|
|/AlertRules/Write|Écrire dans une configuration de règle d’alerte|
|/AutoscaleSettings/Delete|Supprimer une configuration de mise à l’échelle|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|Lire les définitions des mesures|
|/AutoscaleSettings/Read|Lire une configuration de mise à l’échelle|
|/AutoscaleSettings/Scaledown/Action|Effectuer une opération de descente en puissance|
|/AutoscaleSettings/Scaleup/Action|Effectuer une opération de montée en puissance|
|/AutoscaleSettings/Write|Écrire dans une configuration de mise à l’échelle|
|/Components/AnalyticsItems/Delete|Supprime un élément analytique Application Insights|
|/Components/AnalyticsItems/Read|Lit un élément analytique Application Insights|
|/Components/AnalyticsItems/Write|Écrit un élément analytique Application Insights|
|/Components/AnalyticsTables/Action|Action de table analytique Application Insights|
|/Components/AnalyticsTables/Delete|Supprime un schéma de table analytique Application Insights|
|/Components/AnalyticsTables/Read|Lit un schéma de table analytique Application Insights|
|/Components/AnalyticsTables/Write|Écrit un schéma de table analytique Application Insights|
|/Components/Annotations/Delete|Supprime une annotation Application Insights|
|/Components/Annotations/Read|Lit une annotation Application Insights|
|/Components/Annotations/Write|Écrit une annotation Application Insights|
|/Components/Api/Read|Lit l’API Données de composant Application Insights|
|/Components/ApiKeys/Action|Génère une clé d’API Application Insights|
|/Components/ApiKeys/Delete|Supprime une clé d’API Application Insights|
|/Components/ApiKeys/Read|Lit une clé d’API Application Insights|
|/Components/BillingPlanForComponent/Read|Lit un plan de facturation pour le composant Application Insights|
|/Components/CurrentBillingFeatures/Read|Lit les fonctionnalités de facturation actuelles pour le composant Application Insights|
|/Components/CurrentBillingFeatures/Write|Écrit les fonctionnalités de facturation actuelles pour le composant Application Insights|
|/Components/DefaultWorkItemConfig/Read|Lit une configuration d’intégration ALM Application Insights par défaut|
|/Components/Delete|Supprime une configuration du composant Application Insights|
|/Components/ExportConfiguration/Action|Action de paramètres d’exportation Application Insights|
|/Components/ExportConfiguration/Delete|Supprime les paramètres d’exportation Application Insights|
|/Components/ExportConfiguration/Read|Lit les paramètres d’exportation Application Insights|
|/Components/ExportConfiguration/Write|Écrit les paramètres d’exportation Application Insights|
|/Components/ExtendQueries/Read|Lit les résultats de requête étendue du composant Application Insights|
|/Components/Favorites/Delete|Supprime un favori Application Insights|
|/Components/Favorites/Read|Lit un favori Application Insights|
|/Components/Favorites/Write|Écrit un favori Application Insights|
|/Components/FeatureCapabilities/Read|Lit les capacités de fonctionnalité du composant Application Insights|
|/Components/GetAvailableBillingFeatures/Read|Lit les fonctionnalités de facturation disponibles du composant Application Insights|
|/Components/GetToken/Read|Lit un jeton du composant Application Insights|
|/Components/ListMigrationDate/Action|Récupère la date de migration de l’abonnement|
|/Components/ListMigrationDate/Read|Récupère la date de migration de l’abonnement|
|/Components/MetricDefinitions/Read|Lit les définitions de métrique du composant Application Insights|
|/Components/Metrics/Read|Lit les métriques du composant Application Insights|
|/Components/MigrateToNewpricingModel/Action|Migre un abonnement vers un nouveau modèle de tarification|
|/Components/Move/Action|Déplace un composant Application Insights vers un autre groupe de ressources ou abonnement|
|/Components/MyAnalyticsItems/Delete|Supprime un élément analytique personnel Application Insights|
|/Components/MyAnalyticsItems/Read|Lit un élément analytique personnel Application Insights|
|/Components/MyAnalyticsItems/Write|Écrit un élément analytique personnel Application Insights|
|/Components/MyFavorites/Read|Lit un favori personnel Application Insights|
|/Components/PricingPlans/Read|Lit un plan de tarification du composant Application Insights|
|/Components/PricingPlans/Write|Écrit un plan de tarification du composant Application Insights|
|/Components/ProactiveDetectionConfigs/Read|Lit une configuration de détection proactive Application Insights|
|/Components/ProactiveDetectionConfigs/Write|Écrit une configuration de détection proactive Application Insights|
|/Components/providers/Microsoft.Insights/MetricDefinitions/Read|Lire les définitions des mesures|
|/Components/QuotaStatus/Read|Lit l’état de quota du composant Application Insights|
|/Components/Read|Lit une configuration du composant Application Insights|
|/Components/RollbackToLegacyPricingModel/Action|Restaure un abonnement au modèle de tarification hérité|
|/Components/SyntheticMonitorLocations/Read|Lit les emplacements de test web Application Insights|
|/Components/WorkItemConfigs/Delete|Supprime une configuration d’intégration ALM Application Insights|
|/Components/WorkItemConfigs/Read|Lit une configuration d’intégration ALM Application Insights|
|/Components/WorkItemConfigs/Write|Écrit une configuration d’intégration ALM Application Insights|
|/Components/Write|Écrit dans une configuration du composant Application Insights|
|/DiagnosticSettings/Delete|Supprimer la configuration de diagnostic|
|/DiagnosticSettings/Read|Lire la configuration de diagnostic|
|/DiagnosticSettings/Write|Écrire dans la configuration de diagnostic|
|/EventCategories/Read|Lit une catégorie d’événement|
|/eventtypes/digestevents/Read|Lire le résumé des types d’événement de gestion|
|/eventtypes/values/Read|Lire les valeurs de types d’événement de gestion|
|/ExtendedDiagnosticSettings/Delete|Supprimer la configuration de diagnostic étendue|
|/ExtendedDiagnosticSettings/Read|Lire une configuration de diagnostic étendue|
|/ExtendedDiagnosticSettings/Write|Écrire dans la configuration de diagnostic étendue|
|/LogDefinitions/Read|Lire les définitions de journal|
|/LogProfiles/Delete|Supprimer la configuration de profil de journal|
|/LogProfiles/Read|Lire les profils de journal|
|/LogProfiles/Write|Écrire dans une configuration de profil de journal|
|/MetricAlerts/Delete|Supprime une alerte Métrique|
|/MetricAlerts/Read|Lit une alerte Métrique|
|/MetricAlerts/Write|Écrit une alerte Métrique|
|/MetricDefinitions/Microsoft.Insights/Read|Lire les définitions des mesures|
|/MetricDefinitions/providers/Microsoft.Insights/Read|Lire les définitions des mesures|
|/MetricDefinitions/Read|Lire les définitions des mesures|
|/Metrics/providers/Metrics/Read|Lire des mesures|
|/Metrics/Read|Lire des mesures|
|/Metrics/Write|Écrit des métriques|
|/Operations/Read|Lit des opérations|
|/Register/Action|Inscrire le fournisseur Microsoft.Insights|
|/Tenants/Register/Action|Initialise le fournisseur Microsoft Insights|
|/Unregister/Action|Inscrire le fournisseur Microsoft.Insights|
|/Webtests/Delete|Supprime une configuration de test web|
|/Webtests/GetToken/Read|Lit un jeton de test web|
|/Webtests/MetricDefinitions/Read|Lit des définitions de métrique de test web|
|/Webtests/Metrics/Read|Lit des métriques de test web|
|/Webtests/Read|Lit une configuration de test web|
|/Webtests/Write|Écrit dans une configuration de test web|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Opération | Description |
|---|---|
|/checkNameAvailability/read|Vérifier que le nom du coffre Key Vault est valide et qu’il n’est pas déjà utilisé|
|/deletedVaults/read|Afficher les propriétés de coffres Key Vault supprimés de manière réversible|
|/hsmPools/delete|Supprime un pool HSM|
|/hsmPools/joinVault/action|Joint un coffre de clés à un pool HSM|
|/hsmPools/read|Affiche les propriétés d’un pool HSM|
|/hsmPools/write|Crée un pool HSM pour mettre à jour les propriétés d’un pool HSM existant|
|/locations/deletedVaults/purge/action|Vider un coffre Key Vault supprimé de manière réversible|
|/locations/deletedVaults/read|Afficher les propriétés d’un coffre Key Vault supprimé de manière réversible|
|/locations/deleteVirtualNetworkOrSubnets/action|Avertit Microsoft.KeyVault de la suppression d’un réseau virtuel ou d’un sous-réseau|
|/locations/operationResults/read|Vérifier le résultat d’une opération longue|
|/operations/read|Répertorie les opérations disponibles sur le fournisseur de ressources Microsoft.KeyVault|
|/register/action|Enregistrer un abonnement|
|/unregister/action|Désinscrit un abonnement|
|/vaults/accessPolicies/write|Mettre à jour une stratégie d’accès existante via une fusion ou un remplacement, ou ajouter une nouvelle stratégie d’accès à un coffre|
|/vaults/delete|Supprimer un coffre Key Vault|
|/vaults/deploy/action|Permettre l’accès aux clés secrètes dans un coffre Key Vault lors du déploiement de ressources Azure|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Read|Obtient le paramètre de diagnostic pour la ressource|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/vaults/providers/Microsoft.Insights/logDefinitions/read|Obtient les journaux disponibles pour un coffre de clés|
|/vaults/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour un coffre de clés|
|/vaults/read|Afficher les propriétés d’un coffre Key Vault|
|/vaults/secrets/read|Afficher les propriétés d’une clé secrète, mais pas sa valeur|
|/vaults/secrets/write|Créer une nouvelle clé sécrète ou mettre à jour la valeur d’une clé secrète existante|
|/vaults/write|Créer un coffre Key Vault ou mettre à jour les propriétés d’un coffre Key Vault existant|

## <a name="microsoftlabservices"></a>Microsoft.LabServices

| Opération | Description |
|---|---|
|/labAccounts/CreateLab/action|Crée un lab dans un compte lab.|
|/labAccounts/delete|Supprime des comptes de laboratoire.|
|/labAccounts/labs/delete|Supprimer des laboratoires.|
|/labAccounts/labs/environmentSettings/delete|Supprime un paramètre d’environnement.|
|/labAccounts/labs/environmentSettings/environments/delete|Supprimer des environnements.|
|/labAccounts/labs/environmentSettings/environments/read|Afficher les environnements.|
|/labAccounts/labs/environmentSettings/environments/write|Ajouter ou modifier des environnements.|
|/labAccounts/labs/environmentSettings/Publish/action|Approvisionne/déprovisionne des ressources requises pour un paramètre d’environnement en fonction de l’état actuel du paramètre de laboratoire/d’environnement.|
|/labAccounts/labs/environmentSettings/read|Lit le paramètre environnement.|
|/labAccounts/labs/environmentSettings/write|Ajoute ou modifie un paramètre d’environnement.|
|/labAccounts/labs/read|Afficher des laboratoires.|
|/labAccounts/labs/users/delete|Supprime des utilisateurs.|
|/labAccounts/labs/users/read|Lit des utilisateurs.|
|/labAccounts/labs/users/write|Ajoute ou modifie des utilisateurs.|
|/labAccounts/labs/write|Ajouter ou modifier des laboratoires.|
|/labAccounts/read|Lit les comptes de laboratoire.|
|/labAccounts/write|Ajoute ou modifie les comptes de laboratoire.|
|/locations/operations/read|Opérations de lecture.|
|/register/action|Enregistrer l’abonnement.|

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

| Opération | Description |
|---|---|
|/accounts/delete|Supprime un compte Location Based Services.|
|/accounts/listKeys/action|Répertorie les clés de compte Location Based Services|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour les comptes Location Based Services|
|/accounts/read|Obtient un compte Location Based Services.|
|/accounts/regenerateKey/action|Génère une nouvelle clé primaire ou secondaire de compte Location Based Services|
|/accounts/write|Crée ou met à jour un compte Location Based Services.|
|/register/action|Inscrit le fournisseur|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Opération | Description |
|---|---|
|/integrationAccounts/agreements/delete|Supprime l’accord dans le compte d’intégration.|
|/integrationAccounts/agreements/listContentCallbackUrl/action|Obtient l’URL de rappel pour le contenu de l’accord dans le compte d’intégration.|
|/integrationAccounts/agreements/read|Lit l’accord dans le compte d’intégration.|
|/integrationAccounts/agreements/write|Crée ou met à jour l’accord dans le compte d’intégration.|
|/integrationAccounts/assemblies/delete|Supprime l’assembly dans le compte d’intégration.|
|/integrationAccounts/assemblies/listContentCallbackUrl/action|Obtient l’URL de rappel pour le contenu d’assembly dans le compte d’intégration.|
|/integrationAccounts/assemblies/read|Lit l’assembly dans le compte d’intégration.|
|/integrationAccounts/assemblies/write|Crée ou met à jour l’assembly dans le compte d’intégration.|
|/integrationAccounts/batchConfigurations/delete|Supprime la configuration du lot dans le compte d’intégration.|
|/integrationAccounts/batchConfigurations/read|Lit la configuration du lot dans le compte d’intégration.|
|/integrationAccounts/batchConfigurations/write|Crée ou met à jour la configuration du lot dans le compte d’intégration.|
|/integrationAccounts/certificates/delete|Supprime le certificat dans le compte d’intégration.|
|/integrationAccounts/certificates/read|Lit le certificat dans le compte d’intégration.|
|/integrationAccounts/certificates/write|Crée ou met à jour le certificat dans le compte d’intégration.|
|/integrationAccounts/delete|Supprime le compte d’intégration.|
|/integrationAccounts/listCallbackUrl/action|Obtient l’URL de rappel pour le compte d’intégration.|
|/integrationAccounts/listKeyVaultKeys/action|Obtient les clés dans le coffre de clés.|
|/integrationAccounts/logTrackingEvents/action|Journalise les événements de suivi dans le compte d’intégration.|
|/integrationAccounts/maps/delete|Supprime le mappage dans le compte d’intégration.|
|/integrationAccounts/maps/listContentCallbackUrl/action|Obtient l’URL de rappel pour le mappage de contenu dans le compte d’intégration.|
|/integrationAccounts/maps/read|Lit le mappage dans le compte d’intégration.|
|/integrationAccounts/maps/write|Crée ou met à jour le mappage dans le compte d’intégration.|
|/integrationAccounts/partners/delete|Supprime le partenaire dans le compte d’intégration.|
|/integrationAccounts/partners/listContentCallbackUrl/action|Obtient l’URL de rappel pour le contenu partenaire dans le compte d’intégration.|
|/integrationAccounts/partners/read|Lit le partenaire dans le compte d’intégration.|
|/integrationAccounts/partners/write|Crée ou met à jour le partenaire dans le compte d’intégration.|
|/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read|Lit les définitions de journal du compte d’intégration.|
|/integrationAccounts/read|Lit le compte d’intégration.|
|/integrationAccounts/regenerateAccessKey/action|Régénère les secrets de clé d’accès.|
|/integrationAccounts/schemas/delete|Supprime le schéma dans le compte d’intégration.|
|/integrationAccounts/schemas/listContentCallbackUrl/action|Obtient l’URL de rappel pour le contenu de schéma dans le compte d’intégration.|
|/integrationAccounts/schemas/read|Lit le schéma dans le compte d’intégration.|
|/integrationAccounts/schemas/write|Crée ou met à jour le schéma dans le compte d’intégration.|
|/integrationAccounts/sessions/delete|Supprime la session dans le compte d’intégration.|
|/integrationAccounts/sessions/read|Lit la configuration du lot dans le compte d’intégration.|
|/integrationAccounts/sessions/write|Crée ou met à jour la session dans le compte d’intégration.|
|/integrationAccounts/write|Crée ou met à jour le compte d’intégration.|
|/locations/workflows/validate/action|Valide le workflow.|
|/operations/read|Obtient l’opération.|
|/register/action|Inscrit le fournisseur de ressources Microsoft.Logic pour un abonnement donné.|
|/workflows/accessKeys/delete|Supprime la clé d’accès.|
|/workflows/accessKeys/list/action|Répertorie les secrets de clé d’accès.|
|/workflows/accessKeys/read|Lit la clé d’accès.|
|/workflows/accessKeys/regenerate/action|Régénère les secrets de clé d’accès.|
|/workflows/accessKeys/write|Crée ou met à jour la clé d’accès.|
|/workflows/delete|Supprime le workflow.|
|/workflows/disable/action|Désactive le workflow.|
|/workflows/enable/action|Active le workflow.|
|/workflows/listCallbackUrl/action|Obtient l’URL de rappel pour le workflow.|
|/workflows/listSwagger/action|Obtient les définitions Swagger du workflow.|
|/workflows/move/action|Déplace le workflow vers un ID d’abonnement, un groupe de ressources et/ou un nom différent.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/read|Lit les paramètres de diagnostic de flux de travail.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic de flux de travail.|
|/workflows/providers/Microsoft.Insights/logDefinitions/read|Lit les définitions de journal de flux de travail.|
|/workflows/providers/Microsoft.Insights/metricDefinitions/read|Lit les définitions de métrique de flux de travail.|
|/workflows/read|Lit le workflow.|
|/workflows/regenerateAccessKey/action|Régénère les secrets de clé d’accès.|
|/workflows/run/action|Démarre l’exécution du workflow.|
|/workflows/runs/actions/listExpressionTraces/action|Obtient les traces d’expression d’action d’exécution de flux de travail.|
|/workflows/runs/actions/read|Lit l’action d’exécution d’un workflow.|
|/workflows/runs/actions/repetitions/listExpressionTraces/action|Obtient les traces d’expression de répétition d’action d’exécution de flux de travail.|
|/workflows/runs/actions/repetitions/read|Lit la répétition d’action d’exécution de flux de travail.|
|/workflows/runs/actions/scoperepetitions/read|Lit la répétition de l’étendue de l’action d’exécution de flux de travail.|
|/workflows/runs/cancel/action|Annule l’exécution d’un workflow.|
|/workflows/runs/operations/read|Lit l’état d’exécution d’un workflow.|
|/workflows/runs/read|Lit l’exécution d’un workflow.|
|/workflows/suspend/action|Suspend le flux de travail.|
|/workflows/triggers/histories/read|Lit l’historique du déclencheur.|
|/workflows/triggers/histories/resubmit/action|Soumet à nouveau le déclencheur du workflow.|
|/workflows/triggers/listCallbackUrl/action|Obtient l’URL de rappel pour le déclencheur.|
|/workflows/triggers/read|Lit le déclencheur.|
|/workflows/triggers/reset/action|Réinitialise le déclencheur.|
|/workflows/triggers/run/action|Exécute le déclencheur.|
|/workflows/triggers/setState/action|Définit l’état du déclencheur.|
|/workflows/validate/action|Valide le workflow.|
|/workflows/versions/read|Lit la version du workflow.|
|/workflows/versions/triggers/listCallbackUrl/action|Obtient l’URL de rappel pour le déclencheur.|
|/workflows/write|Crée ou met à jour le workflow.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Opération | Description |
|---|---|
|/commitmentPlans/commitmentAssociations/move/action|Déplacer une association de plans d’engagement Machine Learning|
|/commitmentPlans/commitmentAssociations/read|Lire une association de plans d’engagement Machine Learning|
|/commitmentPlans/delete|Supprimer un plan d’engagement Machine Learning|
|/commitmentPlans/join/action|Rejoindre un plan d’engagement Machine Learning|
|/commitmentPlans/read|Lire un plan d’engagement Machine Learning|
|/commitmentPlans/write|Créer ou mettre à jour un plan d’engagement Machine Learning|
|/locations/operationresults/read|Obtient le résultat d’une opération de Machine Learning|
|/locations/operationsstatus/read|Obtient l’état d’une opération de Machine Learning en cours|
|/operations/read|Obtient les opérations de Machine Learning|
|/register/action|Enregistrer l’abonnement pour le fournisseur de ressources du service web Machine Learning et activer la création de services web|
|/skus/read|Obtient les références (SKU) de plan d’engagement de Machine Learning|
|/webServices/action|Créer des propriétés régionales de service web pour les régions prises en charge|
|/webServices/delete|Supprimer un service web Machine Learning|
|/webServices/read|Afficher un service web Machine Learning|
|/webServices/write|Créer ou mettre à jour un service web Machine Learning|
|/Workspaces/delete|Supprimer un espace de travail Machine Learning|
|/Workspaces/listworkspacekeys/action|Répertorier les clés d’un espace de travail Machine Learning|
|/Workspaces/read|Afficher un espace de travail Machine Learning|
|/Workspaces/resyncstoragekeys/action|Resynchroniser les clés d’un compte de stockage configuré pour un espace de travail Machine Learning|
|/Workspaces/write|Créer ou mettre à jour un espace de travail Machine Learning|

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

| Opération | Description |
|---|---|
|/operationalizationClusters/checkUpdate/action|Vérifie si des mises à jour sont disponibles pour les services système pour le cluster d’opérationnalisation|
|/operationalizationClusters/delete|Supprime un compte d’hébergement|
|/operationalizationClusters/listKeys/action|Affiche les clés associées au cluster d’opérationnalisation|
|/operationalizationClusters/read|Lit un compte d’hébergement|
|/operationalizationClusters/updateSystem/action|Met à jour les services système dans un cluster d’opérationnalisation|
|/operationalizationClusters/write|Crée ou met à jour un compte d’hébergement|
|/register/action|Inscrit l’ID d’abonnement auprès du fournisseur de ressources et permet la création de ressources de calcul de Machine Learning|

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

| Opération | Description |
|---|---|
|/accounts/delete|Supprime un compte d’hébergement|
|/accounts/read|Lit un compte d’hébergement|
|/accounts/write|Crée ou met à jour un compte d’hébergement|
|/register/action|Inscrit l’ID d’abonnement auprès du fournisseur de ressources et permet la création d’un compte d’hébergement|

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

| Opération | Description |
|---|---|
|/userAssignedIdentities/assign/action|Action RBAC pour l’affectation de l’identité assignée d’un utilisateur existant à une ressource|
|/userAssignedIdentities/delete|Supprime l’identité assignée d’un utilisateur existant|
|/userAssignedIdentities/read|Obtient l’identité assignée d’un utilisateur existant|
|/userAssignedIdentities/write|Crée une identité assignée d’utilisateur ou met à jour les balises associées à l’identité assignée d’un utilisateur existant|

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

| Opération | Description |
|---|---|
|/labAccounts/CreateLab/action|Crée un lab dans un compte lab.|
|/labAccounts/delete|Supprime des comptes de laboratoire.|
|/labAccounts/labs/delete|Supprimer des laboratoires.|
|/labAccounts/labs/environmentSettings/delete|Supprime un paramètre d’environnement.|
|/labAccounts/labs/environmentSettings/environments/delete|Supprimer des environnements.|
|/labAccounts/labs/environmentSettings/environments/read|Afficher les environnements.|
|/labAccounts/labs/environmentSettings/environments/write|Ajouter ou modifier des environnements.|
|/labAccounts/labs/environmentSettings/read|Lit le paramètre environnement.|
|/labAccounts/labs/environmentSettings/write|Ajoute ou modifie un paramètre d’environnement.|
|/labAccounts/labs/labVms/delete|Supprime les machines virtuelles lab.|
|/labAccounts/labs/labVms/read|Lit les machines virtuelles lab.|
|/labAccounts/labs/labVms/write|Ajoute ou modifie des machines virtuelles lab.|
|/labAccounts/labs/read|Afficher des laboratoires.|
|/labAccounts/labs/write|Ajouter ou modifier des laboratoires.|
|/labAccounts/read|Lit les comptes de laboratoire.|
|/labAccounts/write|Ajoute ou modifie les comptes de laboratoire.|
|/locations/operations/read|Opérations de lecture.|
|/register/action|Enregistrer l’abonnement.|

## <a name="microsoftmanagement"></a>Microsoft.Management

| Opération | Description |
|---|---|
|/checkNameAvailability/action|Vérifie si le nom de groupe d’administration spécifié est valide et unique.|
|/getEntities/action|Affiche toutes les entités (groupes d’administration, abonnements, etc.) pour l’utilisateur authentifié.|
|/managementGroups/delete|Supprime un groupe d’administration.|
|/managementGroups/read|Répertorie les groupes d’administration de l’utilisateur authentifié.|
|/managementGroups/subscriptions/delete|Dissocie un abonnement du groupe d’administration.|
|/managementGroups/subscriptions/write|Associe un abonnement existant au groupe d’administration.|
|/managementGroups/write|Crée ou met à jour un groupe d’administration.|

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

| Opération | Description |
|---|---|
|/ClassicDevServices/delete|Effectue une opération DELETE sur une ressource de service de développement classique.|
|/ClassicDevServices/listSecrets/action|Obtient les clés de gestion des ressources d’un service de développement classique.|
|/ClassicDevServices/listSingleSignOnToken/action|Obtient l’URL d’authentification unique pour un service de développement classique.|
|/ClassicDevServices/read|Effectue une opération GET sur un service de développement classique.|
|/ClassicDevServices/regenerateKey/action|Génère les clés de gestion des ressources d’un service de développement classique.|
|/Operations/read|Lit les opérations pour tous les types de ressources.|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Opération | Description |
|---|---|
|/agreements/offers/plans/cancel/action|Annuler un contrat pour un élément de place de marché donné|
|/agreements/offers/plans/read|Renvoyer un contrat pour un élément de place de marché donné|
|/agreements/offers/plans/sign/action|Signer un contrat pour un élément de place de marché donné|
|/agreements/read|Retourne tous les accords sous un abonnement spécifié|
|/offertypes/publishers/offers/plans/agreements/read|Obtient un accord pour un élément spécifié de machine virtuelle de la place de marché|
|/offertypes/publishers/offers/plans/agreements/write|Signe ou annule un accord pour un élément spécifié de machine virtuelle de la place de marché|

## <a name="microsoftmedia"></a>Microsoft.Media

| Opération | Description |
|---|---|
|/checknameavailability/action|Vérifie si un nom de compte Media Services est disponible|
|/mediaservices/delete|Supprime un compte Media Services|
|/mediaservices/listKeys/action|Affiche les clés ACS pour le compte Media Services|
|/mediaservices/read|Lit un compte Media Services|
|/mediaservices/regenerateKey/action|Régénère une clé ACS Media Services|
|/mediaservices/syncStorageKeys/action|Synchronise les clés de stockage pour un compte de stockage Azure attaché|
|/mediaservices/write|Crée ou met à jour un compte Media Services|
|/operations/read|Lit un compte Media Services|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources Media Services et active la création de comptes Media Services|

## <a name="microsoftmigrate"></a>Microsoft.Migrate

| Opération | Description |
|---|---|
|/Operations/read|Lit les opérations exposées|

## <a name="microsoftnetwork"></a>Microsoft.Network

| Opération | Description |
|---|---|
|/applicationGatewayAvailableSslOptions/predefinedPolicies/read|Stratégie SSL prédéfinie d’Application Gateway|
|/applicationGatewayAvailableSslOptions/read|Options SSL disponibles d’Application Gateway|
|/applicationGatewayAvailableWafRuleSets/read|Obtient les ensembles de règles WAF disponibles pour Application Gateway.|
|/applicationGateways/backendAddressPools/join/action|Joint un pool d’adresses principales de passerelle d’application.|
|/applicationGateways/backendhealth/action|Obtient l’intégrité du serveur principal d’une passerelle d’application.|
|/applicationGateways/delete|Supprime une passerelle d’application.|
|/applicationGateways/effectiveNetworkSecurityGroups/action|Obtient la table de routage configurée sur Application Gateway|
|/applicationGateways/effectiveRouteTable/action|Obtient la table de routage configurée sur Application Gateway|
|/applicationGateways/providers/Microsoft.Insights/logDefinitions/read|Obtient les événements pour Application Gateway|
|/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour Application Gateway|
|/applicationGateways/read|Obtient une passerelle d’application.|
|/applicationGateways/setSecurityCenterConfiguration/action|Définit la configuration du centre de sécurité d’Application Gateway|
|/applicationGateways/start/action|Démarre une passerelle d’application.|
|/applicationGateways/stop/action|Arrête une passerelle d’application.|
|/applicationGateways/write|Crée une passerelle d’application ou met à jour une passerelle d’application.|
|/applicationSecurityGroups/delete|Supprime un groupe de sécurité d’application|
|/applicationSecurityGroups/joinIpConfiguration/action|Joint une configuration IP aux groupes de sécurité d’application.|
|/applicationSecurityGroups/joinNetworkSecurityRule/action|Joint une règle de sécurité aux groupes de sécurité d’application.|
|/applicationSecurityGroups/read|Obtient un ID de groupe de sécurité d’application.|
|/applicationSecurityGroups/write|Crée un groupe de sécurité d’application ou met à jour un groupe de sécurité d’application existant.|
|/bgpServiceCommunities/read|Obtenir les communautés de services BGP.|
|/checkTrafficManagerNameAvailability/action|Vérifier la disponibilité d’un nom DNS relatif dans Traffic Manager.|
|/connections/delete|Supprime une VirtualNetworkGatewayConnection.|
|/connections/read|Obtient une VirtualNetworkGatewayConnection.|
|/connections/sharedkey/action|Obtient une clé partagée de connexion de passerelle de réseau virtuel|
|/connections/sharedKey/read|Obtient une SharedKey VirtualNetworkGatewayConnection.|
|/connections/sharedKey/write|Crée une SharedKey VirtualNetworkGatewayConnection ou met à jour une SharedKey VirtualNetworkGatewayConnection existante.|
|/connections/vpndeviceconfigurationscript/read|Obtient la configuration de l’appareil VPN d’une connexion de passerelle de réseau virtuel|
|/connections/write|Crée une VirtualNetworkGatewayConnection ou met à jour une VirtualNetworkGatewayConnection existante.|
|/ddosProtectionPlans/ddosProtectionPlanProxies/delete|Supprime un proxy de plan de protection DDoS|
|/ddosProtectionPlans/ddosProtectionPlanProxies/read|Obtient une définition de proxy de plan de protection DDoS|
|/ddosProtectionPlans/ddosProtectionPlanProxies/write|Crée un proxy de plan de protection DDoS ou met à jour un proxy de plan de protection DDoS existant|
|/ddosProtectionPlans/delete|Supprime un plan de protection DDoS|
|/ddosProtectionPlans/join/action|Joint un plan de protection DDoS|
|/ddosProtectionPlans/read|Obtient un plan de protection DDoS|
|/ddosProtectionPlans/write|Crée ou met à jour un plan de protection DDoS |
|/dnsoperationresults/read|Obtient les résultats d’une opération DNS.|
|/dnsoperationstatuses/read|Obtient l’état d’une opération DNS. |
|/dnszones/A/delete|Supprimer le jeu d’enregistrements d’un nom donné et de type « A » d’une zone DNS.|
|/dnszones/A/read|Obtenir le jeu d’enregistrements de type « A » au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag.|
|/dnszones/A/write|Créer ou mettre à jour un jeu d’enregistrements de type « A » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements.|
|/dnszones/AAAA/delete|Supprimer le jeu d’enregistrements d’un nom donné et de type « AAAA » d’une zone DNS.|
|/dnszones/AAAA/read|Obtenir le jeu d’enregistrements de type « AAAA » au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag.|
|/dnszones/AAAA/write|Créer ou mettre à jour un jeu d’enregistrements de type « AAAA » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements.|
|/dnszones/all/read|Obtient les jeux d’enregistrements DNS parmi les types.|
|/dnszones/CAA/delete|Supprime le jeu d’enregistrements d’un nom donné et le type CAA d’une zone DNS.|
|/dnszones/CAA/read|Obtient le jeu d’enregistrements de type CAA au format JSON. Le jeu d’enregistrements contient la durée de vie, les balises et l’etag.|
|/dnszones/CAA/write|Crée ou met à jour un jeu d’enregistrements de type CAA à l’intérieur d’une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements.|
|/dnszones/CNAME/delete|Supprimer le jeu d’enregistrements d’un nom donné et de type « CNAME » d’une zone DNS.|
|/dnszones/CNAME/read|Obtenir le jeu d’enregistrements de type « CNAME » au format JSON. Le jeu d’enregistrements contient la durée de vie, les balises et l’etag.|
|/dnszones/CNAME/write|Créer ou mettre à jour un jeu d’enregistrements de type « CNAME » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements.|
|/dnszones/delete|Supprimer la zone DNS au format JSON. Exemples de propriétés de la zone : balises, etag, numberOfRecordSets et maxNumberOfRecordSets.|
|/dnszones/MX/delete|Supprimer le jeu d’enregistrements d’un nom donné et de type « MX » d’une zone DNS.|
|/dnszones/MX/read|Obtenir le jeu d’enregistrements de type « MX » au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag.|
|/dnszones/MX/write|Créer ou mettre à jour un jeu d’enregistrements de type « MX » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements.|
|/dnszones/NS/delete|Supprimer le jeu d’enregistrements DNS de type NS|
|/dnszones/NS/read|Obtenir le jeu d’enregistrements DNS de type NS|
|/dnszones/NS/write|Créer ou mettre à jour un jeu d’enregistrements DNS de type NS|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/read|Obtient les paramètres de diagnostic de la zone DNS|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour les paramètres de diagnostic de la zone DNS|
|/dnszones/providers/Microsoft.Insights/metricDefinitions/read|Obtient les définitions de métrique de la zone DNS|
|/dnszones/PTR/delete|Supprimer le jeu d’enregistrements d’un nom donné et de type « PTR » d’une zone DNS.|
|/dnszones/PTR/read|Obtenir le jeu d’enregistrements de type « PTR » au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag.|
|/dnszones/PTR/write|Créer ou mettre à jour un jeu d’enregistrements de type « PTR » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements.|
|/dnszones/read|Obtenir la zone DNS au format JSON. Exemples de propriétés de la zone : balises, etag, numberOfRecordSets et maxNumberOfRecordSets. Notez que cette commande ne récupère pas les jeux d’enregistrements contenus dans la zone.|
|/dnszones/recordsets/read|Obtient les jeux d’enregistrements DNS parmi les types.|
|/dnszones/SOA/read|Obtenir le jeu d’enregistrements DNS de type SOA|
|/dnszones/SOA/write|Créer ou mettre à jour un jeu d’enregistrements DNS de type SOA|
|/dnszones/SRV/delete|Supprimer le jeu d’enregistrements d’un nom donné et de type « SRV » d’une zone DNS.|
|/dnszones/SRV/read|Obtenir le jeu d’enregistrements de type « SRV » au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag.|
|/dnszones/SRV/write|Créer ou mettre à jour un jeu d’enregistrements de type SRV|
|/dnszones/TXT/delete|Supprimer le jeu d’enregistrements d’un nom donné et de type « TXT » d’une zone DNS.|
|/dnszones/TXT/read|Obtenir le jeu d’enregistrements de type « TXT », au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag.|
|/dnszones/TXT/write|Créer ou mettre à jour un jeu d’enregistrements de type « TXT » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements.|
|/dnszones/write|Créer ou mettre à jour une zone DNS dans un groupe de ressources.  Permet de mettre à jour les balises dans une ressource de la zone DNS. Notez que cette commande ne peut pas être utilisée pour créer ou mettre à jour des jeux d’enregistrements au sein de la zone.|
|/expressRouteCircuits/authorizations/delete|Supprime une autorisation ExpressRouteCircuit.|
|/expressRouteCircuits/authorizations/read|Obtient une autorisation ExpressRouteCircuit.|
|/expressRouteCircuits/authorizations/write|Crée une autorisation ExpressRouteCircuit ou met à jour une autorisation ExpressRouteCircuit existante.|
|/expressRouteCircuits/delete|Supprime un ExpressRouteCircuit.|
|/expressRouteCircuits/peerings/arpTables/action|Obtient une ArpTable d’homologation ExpressRouteCircuit.|
|/expressRouteCircuits/peerings/connections/delete|Supprime une connexion ExpressRouteCircuit|
|/expressRouteCircuits/peerings/connections/read|Obtient une connexion ExpressRouteCircuit|
|/expressRouteCircuits/peerings/connections/write|Crée ou met à jour une ressource de connexion ExpressRouteCircuit existante|
|/expressRouteCircuits/peerings/delete|Supprime une homologation ExpressRouteCircuit.|
|/expressRouteCircuits/peerings/read|Obtient une homologation ExpressRouteCircuit.|
|/expressRouteCircuits/peerings/routeTables/action|Obtient une RouteTable d’homologation ExpressRouteCircuit.|
|/expressRouteCircuits/peerings/routeTablesSummary/action|Obtient un résumé RouteTable d’homologation ExpressRouteCircuit.|
|/expressRouteCircuits/peerings/stats/read|Obtient les statistiques d’homologation d’un ExpressRouteCircuit.|
|/expressRouteCircuits/peerings/write|Crée une homologation ExpressRouteCircuit ou met à jour une homologation ExpressRouteCircuit existante.|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read|Obtient des paramètres de diagnostic pour les circuits ExpressRoute|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour des paramètres de diagnostic pour les circuits ExpressRoute|
|/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read|Obtient les événements pour les circuits ExpressRoute|
|/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read|Obtient les définitions de métrique pour les circuits ExpressRoute|
|/expressRouteCircuits/read|Obtenir un ExpressRouteCircuit.|
|/expressRouteCircuits/stats/read|Obtient les statistiques d’un ExpressRouteCircuit.|
|/expressRouteCircuits/write|Crée un ExpressRouteCircuit ou met à jour un ExpressRouteCircuit existant.|
|/expressRouteCrossConnections/delete|Supprime une connexion croisée ExpressRoute|
|/expressRouteCrossConnections/join/action|Joint une connexion croisée ExpressRoute|
|/expressRouteCrossConnections/peerings/arpTables/action|Obtient une table ARP d’appairage de connexion croisée ExpressRoute|
|/expressRouteCrossConnections/peerings/delete|Supprime un appairage de connexion croisée ExpressRoute|
|/expressRouteCrossConnections/peerings/read|Obtient un appairage de connexion croisée ExpressRoute|
|/expressRouteCrossConnections/peerings/routeTables/action|Obtient une table de routage d’appairage de connexion croisée ExpressRoute|
|/expressRouteCrossConnections/peerings/routeTableSummary/action|Obtient un résumé de table de routage d’appairage de connexion croisée ExpressRoute|
|/expressRouteCrossConnections/peerings/stats/read|Obtient une statistique d’appairage de connexion croisée ExpressRoute|
|/expressRouteCrossConnections/peerings/write|Crée un appairage de connexion croisée ExpressRoute ou met à jour un appairage de connexion croisée ExpressRoute existant|
|/expressRouteCrossConnections/read|Obtient une connexion croisée ExpressRoute|
|/expressRouteCrossConnections/write|Crée ou met à jour une connexion croisée ExpressRoute|
|/expressRouteServiceProviders/read|Obtient les fournisseurs de services ExpressRoute.|
|/loadBalancers/backendAddressPools/join/action|Joint un pool d’adresses principales d’équilibrage de charge.|
|/loadBalancers/backendAddressPools/read|Obtient une définition de pool d’adresses principales d’équilibrage de charge.|
|/loadBalancers/delete|Supprime un équilibrage de charge.|
|/loadBalancers/frontendIPConfigurations/read|Obtient une définition de configuration d’adresse IP frontale d’équilibrage de charge.|
|/loadBalancers/inboundNatPools/join/action|Joint un pool NAT entrant d’équilibrage de charge.|
|/loadBalancers/inboundNatPools/read|Obtient une définition de pool NAT entrant d’équilibrage de charge.|
|/loadBalancers/inboundNatRules/delete|Supprime une règle NAT entrante d’équilibrage de charge.|
|/loadBalancers/inboundNatRules/join/action|Joint une règle NAT entrante d’équilibrage de charge.|
|/loadBalancers/inboundNatRules/read|Obtient une définition de règle NAT entrante d’équilibrage de charge.|
|/loadBalancers/inboundNatRules/write|Crée une règle NAT entrante d’équilibrage de charge ou met à jour une règle NAT entrante d’équilibrage de charge existante.|
|/loadBalancers/loadBalancingRules/read|Obtient une définition de règle d’équilibrage de charge.|
|/loadBalancers/networkInterfaces/read|Obtient les références à toutes les interfaces réseau sous un équilibrage de charge.|
|/loadBalancers/outboundNatRules/read|Obtient une définition de règle NAT sortante d’équilibrage de charge.|
|/loadBalancers/probes/join/action|Autorise l’utilisation des sondes d’un équilibreur de charge. Par exemple, avec cette autorisation, la propriété healthProbe du groupe de machines virtuelles identiques peut faire référence à la sonde.|
|/loadBalancers/probes/read|Obtient une sonde d’équilibrage de charge.|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read|Obtient les paramètres de diagnostic de l’équilibreur de charge|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour les paramètres de diagnostic de l’équilibreur de charge|
|/loadBalancers/providers/Microsoft.Insights/logDefinitions/read|Obtient les événements pour l’équilibreur de charge|
|/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour l’équilibreur de charge|
|/loadBalancers/read|Obtient une définition d’équilibrage de charge.|
|/loadBalancers/virtualMachines/read|Obtient les références à toutes les machines virtuelles sous un équilibrage de charge.|
|/loadBalancers/write|Crée un équilibrage de charge ou met à jour un équilibrage de charge existant.|
|/localnetworkgateways/delete|Supprime une LocalNetworkGateway.|
|/localnetworkgateways/read|Obtient une LocalNetworkGateway.|
|/localnetworkgateways/write|Crée une LocalNetworkGateway ou met à jour une LocalNetworkGateway existante.|
|/locations/checkDnsNameAvailability/read|Vérifie si le nom DNS est disponible à l’emplacement spécifié.|
|/locations/operationResults/read|Obtient le résultat d’une opération DELETE ou POST asynchrone.|
|/locations/operations/read|Obtient la ressource d’opération qui représente l’état d’une opération asynchrone.|
|/locations/usages/read|Obtient les mesures d’utilisation des ressources.|
|/locations/virtualNetworkAvailableEndpointServices/read|Obtient la liste des services de point de terminaison de réseau virtuel disponibles|
|/networkInterfaces/delete|Supprime une interface réseau.|
|/networkInterfaces/diagnosticIdentity/read|Obtient l’identité de diagnostic de la ressource|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|Obtenir les groupes de sécurité réseau configurés sur l’interface réseau de la machine virtuelle.|
|/networkInterfaces/effectiveRouteTable/action|Obtenir la table de routage configurée sur l’interface réseau de la machine virtuelle.|
|/networkInterfaces/ipconfigurations/read|Obtient une définition de la configuration de l’adresse IP de l’interface réseau. |
|/networkInterfaces/join/action|Joint une machine virtuelle à une interface réseau.|
|/networkInterfaces/loadBalancers/read|Obtient tous les équilibrages de charge auxquels l’interface réseau appartient.|
|/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour l’interface réseau|
|/networkInterfaces/read|Obtient une définition d’interface réseau. |
|/networkInterfaces/write|Crée une interface réseau ou met à jour une interface réseau existante. |
|/networkSecurityGroups/defaultSecurityRules/read|Obtient une définition de règle de sécurité par défaut.|
|/networkSecurityGroups/delete|Supprime un groupe de sécurité réseau.|
|/networkSecurityGroups/join/action|Joint un groupe de sécurité réseau.|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read|Obtient les paramètres de diagnostic des groupes de sécurité réseau|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour les paramètres de diagnostic des groupes de sécurité réseau, cette opération étant complétée par le fournisseur de ressources Insights.|
|/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read|Obtient les événements pour le groupe de sécurité réseau|
|/networkSecurityGroups/read|Obtient une définition de groupe de sécurité réseau.|
|/networkSecurityGroups/securityRules/delete|Supprime une règle de sécurité.|
|/networkSecurityGroups/securityRules/read|Obtient une définition de règle de sécurité.|
|/networkSecurityGroups/securityRules/write|Crée une règle de sécurité ou met à jour une règle de sécurité existante.|
|/networkSecurityGroups/write|Crée un groupe de sécurité réseau ou met à jour un groupe de sécurité réseau existant.|
|/networkWatchers/availableProvidersList/action|Retourne tous les fournisseurs de services Internet disponibles pour une région Azure spécifiée.|
|/networkWatchers/azureReachabilityReport/action|Retourne le score de la latence relatif pour les fournisseurs de services Internet d’un emplacement spécifié vers les régions Azure.|
|/networkWatchers/configureFlowLog/action|Configure la journalisation des flux pour une ressource cible.|
|/networkWatchers/connectionMonitors/delete|Supprime un moniteur de connexion|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/read|Obtient les paramètres de diagnostic du moniteur de connexion|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/write|Crée ou met à jour les paramètres de diagnostic du moniteur de connexion|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ metricDefinitions/read|Obtient les métriques disponibles pour le moniteur de connexion|
|/networkWatchers/connectionMonitors/query/action|Interroge la connectivité de surveillance entre des points de terminaison spécifiés|
|/networkWatchers/connectionMonitors/read|Obtient les détails du moniteur de connexion|
|/networkWatchers/connectionMonitors/start/action|Démarre la surveillance de la connectivité entre des points de terminaison spécifiés|
|/networkWatchers/connectionMonitors/stop/action|Arrête/suspend la surveillance de la connectivité entre des points de terminaison spécifiés|
|/networkWatchers/connectionMonitors/write|Crée un moniteur de connexion|
|/networkWatchers/connectivityCheck/action|Vérifie la possibilité d’établir une connexion TCP directe à partir d’une machine virtuelle à un point de terminaison spécifié, notamment une autre machine virtuelle ou un serveur distant arbitraire.|
|/networkWatchers/delete|Supprime un observateur de réseau.|
|/networkWatchers/ipFlowVerify/action|Indique si le paquet est autorisé ou refusé vers ou depuis une destination particulière.|
|/networkWatchers/lenses/delete|Supprime un filtre|
|/networkWatchers/lenses/query/action|Interroge le trafic réseau de surveillance sur un point de terminaison spécifié|
|/networkWatchers/lenses/read|Obtient les détails du filtre|
|/networkWatchers/lenses/start/action|Démarre la surveillance du trafic réseau sur un point de terminaison spécifié|
|/networkWatchers/lenses/stop/action|Arrête/suspend la surveillance du trafic réseau sur un point de terminaison spécifié|
|/networkWatchers/lenses/write|Crée un filtre|
|/networkWatchers/nextHop/action|Pour une adresse IP de destination et une cible spécifiées, indique le type de saut suivant et l’adresse IP de saut suivant.|
|/networkWatchers/packetCaptures/delete|Supprimer une capture de paquets.|
|/networkWatchers/packetCaptures/queryStatus/action|Obtient des informations sur les propriétés et l’état d’une ressource de capture de paquets.|
|/networkWatchers/packetCaptures/read|Obtenir la définition de la capture de paquets.|
|/networkWatchers/packetCaptures/stop/action|Arrêter la session de capture de paquets en cours d’exécution.|
|/networkWatchers/packetCaptures/write|Crée une capture de paquets.|
|/networkWatchers/queryFlowLogStatus/action|Obtient l’état de la journalisation des flux sur une ressource.|
|/networkWatchers/queryTroubleshootResult/action|Obtient le résultat de la résolution des problèmes à partir de l’opération de résolution des problèmes en cours d’exécution ou exécutée précédemment.|
|/networkWatchers/read|Obtient la définition d’un observateur de réseau.|
|/networkWatchers/securityGroupView/action|Afficher les règles de groupe de sécurité réseau configurées et en vigueur appliquées à une machine virtuelle.|
|/networkWatchers/topology/action|Obtient une vue au niveau du réseau des ressources et de leurs relations dans un groupe de ressources.|
|/networkWatchers/troubleshoot/action|Démarre la résolution des problèmes sur une ressource réseau dans Azure.|
|/networkWatchers/write|Crée un observateur de réseau ou met à jour un observateur de réseau existant.|
|/operations/read|Obtenir les opérations disponibles.|
|/publicIPAddresses/delete|Supprime une adresse IP publique.|
|/publicIPAddresses/join/action|Joint une adresse IP publique.|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read|Obtient les paramètres de diagnostic de l’adresse IP publique|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour les paramètres de diagnostic de l’adresse IP publique|
|/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read|Obtient les définitions de journal de l’adresse IP publique|
|/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read|Obtient les définitions de métrique de l’adresse IP publique|
|/publicIPAddresses/read|Obtient une définition de l’adresse IP publique.|
|/publicIPAddresses/write|Crée une adresse IP publique ou met à jour une adresse IP publique existante. |
|/register/action|Enregistrer l’abonnement.|
|/routeFilters/delete|Supprime une définition de filtre de routage.|
|/routeFilters/join/action|Joint un filtre de routage.|
|/routeFilters/read|Obtient une définition de filtre de routage.|
|/routeFilters/routeFilterRules/delete|Supprime une définition de règle de filtre de routage.|
|/routeFilters/routeFilterRules/read|Obtient une définition de règle de filtre de routage.|
|/routeFilters/routeFilterRules/write|Crée une règle de filtre de routage ou met à jour une règle de filtre de routage existante.|
|/routeFilters/write|Crée un filtre de routage ou met à jour un filtre de routage existant.|
|/routeTables/delete|Supprime une définition de table de routage.|
|/routeTables/join/action|Joint une table de routage.|
|/routeTables/read|Obtient une définition de table de routage.|
|/routeTables/routes/delete|Supprime une définition de routage.|
|/routeTables/routes/read|Obtient une définition de routage.|
|/routeTables/routes/write|Crée un routage ou met à jour un routage existant.|
|/routeTables/write|Crée une table de routage ou met à jour une table de routage existante.|
|/securegateways/applicationRuleCollections/delete|Supprime un regroupement de règles d’application pour une passerelle sécurisée|
|/securegateways/applicationRuleCollections/read|Récupère un regroupement de règles d’application pour une passerelle sécurisée spécifiée|
|/securegateways/applicationRuleCollections/write|Crée ou met à jour un regroupement de règles d’application pour une passerelle sécurisée|
|/securegateways/delete|Supprime une passerelle sécurisée|
|/securegateways/networkRuleCollections/delete|Supprime un regroupement de règles de réseau pour une passerelle sécurisée|
|/securegateways/networkRuleCollections/read|Récupère un regroupement de règles de réseau pour une passerelle sécurisée spécifiée|
|/securegateways/networkRuleCollections/write|Crée ou met à jour un regroupement de règles de réseau pour une passerelle sécurisée|
|/securegateways/read|Obtient une passerelle sécurisée|
|/securegateways/write|Crée ou met à jour une passerelle sécurisée|
|/serviceEndpointPolicies/delete|Supprime une stratégie de point de terminaison de service|
|/serviceEndpointPolicies/join/action|Joint une stratégie de point de terminaison de service|
|/serviceEndpointPolicies/joinSubnet/action|Joint un sous-réseau aux stratégies de point de terminaison de service|
|/serviceEndpointPolicies/read|Obtient une description de stratégie de point de terminaison de service|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete|Supprime une définition de stratégie de point de terminaison de service|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read|Obtient une description de définition de stratégie de point de terminaison de service|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write|Crée une définition de stratégie de point de terminaison de service ou met à jour une définition de stratégie de point de terminaison de service existante|
|/serviceEndpointPolicies/write|Crée une stratégie de point de terminaison de service ou met à jour une stratégie de point de terminaison de service existante|
|/trafficManagerGeographicHierarchies/read|Obtient la hiérarchie géographique Traffic Manager contenant des régions qui peuvent être utilisées dans la méthode de routage du trafic géographique.|
|/trafficManagerProfiles/azureEndpoints/delete|Supprime un point de terminaison Azure d’un profil Traffic Manager existant. Traffic Manager cesse de router le trafic vers le point de terminaison Azure supprimé.|
|/trafficManagerProfiles/azureEndpoints/read|Obtient un point de terminaison Azure qui appartient à un profil Traffic Manager, incluant toutes les propriétés de ce point de terminaison Azure.|
|/trafficManagerProfiles/azureEndpoints/write|Ajoute un nouveau point de terminaison Azure dans un profil Traffic Manager existant ou met à jour les propriétés d’un point de terminaison Azure existant dans ce profil Traffic Manager.|
|/trafficManagerProfiles/delete|Supprimer le profil Traffic Manager. Tous les paramètres associés au profil Traffic Manager seront perdus et le profil ne pourra plus être utilisé pour acheminer le trafic.|
|/trafficManagerProfiles/externalEndpoints/delete|Supprime un point de terminaison externe d’un profil Traffic Manager existant. Traffic Manager cesse de router le trafic vers le point de terminaison externe supprimé.|
|/trafficManagerProfiles/externalEndpoints/read|Obtient un point de terminaison externe qui appartient à un profil Traffic Manager, incluant toutes les propriétés de ce point de terminaison externe.|
|/trafficManagerProfiles/externalEndpoints/write|Ajoute un nouveau point de terminaison externe dans un profil Traffic Manager existant ou met à jour les propriétés d’un point de terminaison externe existant dans ce profil Traffic Manager.|
|/trafficManagerProfiles/heatMaps/read|Obtient la carte thermique Traffic Manager pour le profil Traffic Manager spécifié qui contient les données de nombre et de latence des requêtes par emplacement et IP source.|
|/trafficManagerProfiles/nestedEndpoints/delete|Supprime un point de terminaison imbriqué d’un profil Traffic Manager existant. Traffic Manager cesse de router le trafic vers le point de terminaison imbriqué supprimé.|
|/trafficManagerProfiles/nestedEndpoints/read|Obtient un point de terminaison imbriqué qui appartient à un profil Traffic Manager, incluant toutes les propriétés de ce point de terminaison imbriqué.|
|/trafficManagerProfiles/nestedEndpoints/write|Ajoute un nouveau point de terminaison imbriqué dans un profil Traffic Manager existant ou met à jour les propriétés d’un point de terminaison imbriqué existant dans ce profil Traffic Manager.|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read|Obtient les paramètres de diagnostic de Traffic Manager|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour les paramètres de diagnostic de Traffic Manager, cette opération étant complétée par le fournisseur de ressources Insights.|
|/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read|Obtient les événements pour Traffic Manager|
|/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour Traffic Manager.|
|/trafficManagerProfiles/read|Obtient la configuration du profil Traffic Manager. Cela inclut les paramètres DNS, les paramètres de routage du trafic, les paramètres de surveillance des points de terminaison et la liste des points de terminaison acheminés par ce profil Traffic Manager.|
|/trafficManagerProfiles/write|Créer un profil Traffic Manager ou modifier la configuration d’un profil Traffic Manager existant. Cela inclut l’activation ou la désactivation d’un profil et la modification des paramètres DNS, des paramètres de routage du trafic ou des paramètres de surveillance des points de terminaison. Les points de terminaison acheminés par le profil Traffic Manager peuvent être ajoutés, supprimés, activés ou désactivés.|
|/trafficManagerUserMetricsKeys/delete|Supprime la clé de niveau d’abonnement utilisée pour la collecte de métriques utilisateur en temps réel.|
|/trafficManagerUserMetricsKeys/read|Obtient la clé de niveau d’abonnement utilisée pour la collecte de métriques utilisateur en temps réel.|
|/trafficManagerUserMetricsKeys/write|Crée une nouvelle clé de niveau d’abonnement à utiliser pour la collecte de métriques utilisateur en temps réel.|
|/unregister/action|Désenregistrer l’abonnement.|
|/virtualHubs/delete|Supprime un hub virtuel|
|/virtualHubs/hubVirtualNetworkConnections/delete|Supprime une connexion de réseau virtuel hub|
|/virtualHubs/hubVirtualNetworkConnections/read|Obtient une connexion de réseau virtuel hub|
|/virtualHubs/hubVirtualNetworkConnections/write|Crée ou met à jour une connexion de réseau virtuel hub|
|/virtualHubs/read|Obtient un hub virtuel|
|/virtualHubs/write|Crée ou met à jour un hub virtuel|
|/virtualnetworkgateways/connections/read|Obtient une connexion de passerelle de réseau virtuel|
|/virtualNetworkGateways/delete|Supprime une passerelle de réseau virtuel|
|/virtualnetworkgateways/generatevpnclientpackage/action|Génère le package du client VPN pour la passerelle de réseau virtuel|
|/virtualnetworkgateways/generatevpnprofile/action|Génère le package du profil VPN pour la passerelle de réseau virtuel|
|/virtualnetworkgateways/getadvertisedroutes/action|Obtient les itinéraires annoncés de la passerelle de réseau virtuel|
|/virtualnetworkgateways/getbgppeerstatus/action|Obtient l’état d’homologue BGP de la passerelle de réseau virtuel|
|/virtualnetworkgateways/getlearnedroutes/action|Obtient les itinéraires appris de la passerelle de réseau virtuel|
|/virtualnetworkgateways/getvpnclientipsecparameters/action|Obtient les paramètres IPsec de client VPN pour le client P2S de la passerelle de réseau virtuel.|
|/virtualnetworkgateways/getvpnprofilepackageurl/action|Obtient l’URL d’un package de profil de client VPN prégénéré|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read|Obtient les paramètres de diagnostic de la passerelle de réseau virtuel|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour les paramètres de diagnostic de la passerelle de réseau virtuel, cette opération étant complétée par le fournisseur de ressources Insights.|
|/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read|Obtient les événements pour la passerelle de réseau virtuel|
|/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour la passerelle de réseau virtuel|
|/virtualNetworkGateways/read|Obtient une passerelle de réseau virtuel|
|/virtualnetworkgateways/reset/action|Réinitialise une passerelle de réseau virtuel|
|/virtualnetworkgateways/setvpnclientipsecparameters/action|Définit les paramètres IPsec de client VPN pour le client P2S de la passerelle de réseau virtuel.|
|/virtualnetworkgateways/supportedvpndevices/action|Affiche les appareils VPN pris en charge|
|/virtualNetworkGateways/write|Crée ou met à jour une passerelle de réseau virtuel|
|/virtualNetworks/checkIpAddressAvailability/read|Vérifier si l’adresse IP est disponible sur le réseau virtuel spécifié.|
|/virtualNetworks/customViews/get/action|Obtient un contenu d’affichage personnalisé de réseau virtuel|
|/virtualNetworks/customViews/read|Obtient une définition d’affichage personnalisé de réseau virtuel|
|/virtualNetworks/delete|Supprime un réseau virtuel.|
|/virtualNetworks/peer/action|Appaire un réseau virtuel avec un autre réseau virtuel.|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read|Obtient les paramètres de diagnostic du réseau virtuel|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour les paramètres de diagnostic du réseau virtuel|
|/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read|Obtient les définitions de journal du réseau virtuel|
|/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read|Obtient les définitions de métrique de réseau virtuel|
|/virtualNetworks/read|Obtenir la définition de réseau virtuel.|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete|Supprime un proxy d’appairage de réseau virtuel|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/read|Obtient une définition de proxy d’appairage de réseau virtuel|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/write|Crée un proxy d’appairage de réseau virtuel ou met à jour un proxy d’appairage de réseau virtuel existant|
|/virtualNetworks/subnets/delete|Supprime un sous-réseau de réseau virtuel.|
|/virtualNetworks/subnets/join/action|Joint un réseau virtuel.|
|/virtualNetworks/subnets/joinViaServiceEndpoint/action|Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau.|
|/virtualNetworks/subnets/read|Obtient une définition de sous-réseau de réseau virtuel.|
|/virtualNetworks/subnets/resourceNavigationLinks/delete|Supprime un lien de navigation dans les ressources|
|/virtualNetworks/subnets/resourceNavigationLinks/read|Obtient la définition du lien de navigation dans les ressources|
|/virtualNetworks/subnets/resourceNavigationLinks/write|Crée un lien de navigation dans les ressources ou met à jour un lien de navigation dans les ressources existant|
|/virtualNetworks/subnets/virtualMachines/read|Obtient les références à toutes les machines virtuelles dans un sous-réseau de réseau virtuel.|
|/virtualNetworks/subnets/write|Crée un sous-réseau de réseau virtuel ou met à jour un sous-réseau de réseau virtuel existant.|
|/virtualNetworks/taggedTrafficConsumers/delete|Supprime un consommateur de trafic avec balise|
|/virtualNetworks/taggedTrafficConsumers/read|Obtient la définition du consommateur de trafic avec balise|
|/virtualNetworks/taggedTrafficConsumers/validate/action|Valide un consommateur de trafic avec balise|
|/virtualNetworks/taggedTrafficConsumers/write|Crée un consommateur de trafic avec balise ou met à jour un consommateur de trafic avec balise existant|
|/virtualNetworks/usages/read|Obtient les utilisations d’IP pour chaque sous-réseau du réseau virtuel|
|/virtualNetworks/virtualMachines/read|Obtient les références à toutes les machines virtuelles dans un réseau virtuel.|
|/virtualNetworks/virtualNetworkPeerings/delete|Supprime une homologation de réseau virtuel.|
|/virtualNetworks/virtualNetworkPeerings/read|Obtient une définition d’homologation de réseau virtuel.|
|/virtualNetworks/virtualNetworkPeerings/write|Crée une homologation de réseau virtuel ou met à jour une homologation de réseau virtuel existante.|
|/virtualNetworks/write|Crée un réseau virtuel ou met à jour un réseau virtuel existant.|
|/virtualNetworkTaps/delete|Supprime un TAP de réseau virtuel|
|/virtualNetworkTaps/join/action|Joint un TAP de réseau virtuel|
|/virtualNetworkTaps/read|Obtient un TAP de réseau virtuel|
|/virtualNetworkTaps/write|Crée ou met à jour un TAP de réseau virtuel|
|/virtualwans/delete|Supprime un WAN virtuel|
|/virtualwans/read|Obtient un WAN virtuel|
|/virtualWans/virtualHubProxies/delete|Supprime un proxy de hub virtuel|
|/virtualWans/virtualHubProxies/read|Obtient une définition de proxy de hub virtuel|
|/virtualWans/virtualHubProxies/write|Crée ou met à jour un proxy de hub virtuel|
|/virtualwans/virtualHubs/read|Obtient tous les hubs virtuels associés à un WAN virtuel.|
|/virtualwans/vpnconfiguration/read|Obtient une configuration VPN|
|/virtualWans/vpnSiteProxies/delete|Supprime un proxy de site VPN|
|/virtualWans/vpnSiteProxies/read|Obtient une définition de proxy de site VPN|
|/virtualWans/vpnSiteProxies/write|Crée ou met à jour un proxy de site VPN|
|/virtualwans/vpnSites/read|Obtient tous les sites VPN associés à un WAN virtuel.|
|/virtualwans/write|Crée ou met à jour un WAN virtuel|
|/vpnGateways/read|Obtient une passerelle VPN.|
|/vpnGateways/vpnConnections/read|Obtient une connexion VPN.|
|/vpnGateways/vpnConnections/write|Place une connexion VPN.|
|/vpnGateways/write|Place une passerelle VPN.|
|/vpnsites/delete|Supprime une ressource de site VPN.|
|/vpnsites/read|Obtient une ressource de site VPN.|
|/vpnsites/write|Crée ou met à jour une ressource de site VPN.|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Opération | Description |
|---|---|
|/CheckNamespaceAvailability/action|Vérifie si un nom de ressource Namespace donné est disponible dans le service NotificationHub.|
|/Namespaces/authorizationRules/action|Obtenir la liste des descriptions des règles d’autorisation des ressources Namespaces.|
|/Namespaces/authorizationRules/delete|Supprimer une règle d’autorisation de ressource Namespace. La règle d’autorisation de ressource Namespace par défaut ne peut pas être supprimée. |
|/Namespaces/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à la ressource Namespace|
|/Namespaces/authorizationRules/read|Obtenir la liste des descriptions des règles d’autorisation des ressources Namespaces.|
|/Namespaces/authorizationRules/regenerateKeys/action|Règle d’autorisation de ressource Namespace pour régénérer les clés primaires et secondaires ; spécifier la clé qui doit être régénérée.|
|/Namespaces/authorizationRules/write|Créer des règles d’autorisation au niveau d’une ressource Namespace et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour.|
|/Namespaces/CheckNotificationHubAvailability/action|Vérifie si un nom de NotificationHub donné est disponible à l’intérieur d’une ressource Namespace.|
|/Namespaces/Delete|Supprimer une ressource Namespace|
|/Namespaces/NotificationHubs/authorizationRules/action|Obtenir la liste des règles d’autorisation de concentrateur de notification.|
|/Namespaces/NotificationHubs/authorizationRules/delete|Supprimer les règles d’autorisation de concentrateur de notification.|
|/Namespaces/NotificationHubs/authorizationRules/listkeys/action|Obtenir la chaîne de connexion au concentrateur de notification.|
|/Namespaces/NotificationHubs/authorizationRules/read|Obtenir la liste des règles d’autorisation de concentrateur de notification.|
|/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action|Règle d’autorisation de concentrateur de notification pour régénérer les clés primaires et secondaires ; spécifier la clé qui doit être régénérée.|
|/Namespaces/NotificationHubs/authorizationRules/write|Créer des règles d’autorisation de concentrateur de notification et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour.|
|/Namespaces/NotificationHubs/debugSend/action|Envoyer une notification Push de test.|
|/Namespaces/NotificationHubs/Delete|Supprimer une ressource de concentrateur de notification.|
|/Namespaces/NotificationHubs/metricDefinitions/read|Obtenir la liste des descriptions des mesures de ressource Namespace.|
|/Namespaces/NotificationHubs/pnsCredentials/action|Obtenir toutes les informations d’identification PNS du concentrateur de notification. Cela inclut les informations d’identification WNS, MPNS, APNS, GCM et BAIDU.|
|/Namespaces/NotificationHubs/read|Obtenir la liste des descriptions des ressources de concentrateur de notification.|
|/Namespaces/NotificationHubs/write|Créer un concentrateur de notification et mettre à jour ses propriétés. Ses propriétés incluent principalement des informations d’identification PNS. Durée de vie et règles d’autorisation|
|/Namespaces/read|Obtenir la liste des descriptions des ressources Namespace.|
|/Namespaces/write|Créer une ressource Namespace et mettre à jour ses propriétés. Les balises et la capacité de l’espace de noms sont les propriétés qui peuvent être mises à jour.|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources NotificationHubs et active la création de ressources Namespaces et NotificationHubs.|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Opération | Description |
|---|---|
|/linkTargets/read|Répertorie les comptes existants qui ne sont pas associés à un abonnement Azure. Pour lier cet abonnement Azure à un espace de travail, utilisez un ID de client renvoyé par cette opération dans la propriété d’ID de client de l’opération de création d’un espace de travail.|
|/register/action|Inscrire un abonnement à un fournisseur de ressources.|
|/workspaces/analytics/query/action|Effectue les recherches à l’aide d’un nouveau moteur.|
|/workspaces/analytics/query/schema/read|Obtient le schéma de recherche V2.|
|/workspaces/api/query/action|Effectue les recherches à l’aide d’un nouveau moteur.|
|/workspaces/api/query/schema/read|Obtient le schéma de recherche V2.|
|/workspaces/configurationScopes/delete|Supprimer l’étendue de la configuration.|
|/workspaces/configurationScopes/read|Obtenir l’étendue de la configuration.|
|/workspaces/configurationScopes/write|Définir l’étendue de la configuration.|
|/workspaces/datasources/delete|Supprimer des sources de données sous un espace de travail.|
|/workspaces/datasources/read|Obtenir des sources de données sous un espace de travail.|
|/workspaces/datasources/write|Créer/mettre à jour des sources de données sous un espace de travail.|
|/workspaces/delete|Supprime un espace de travail. Si l’espace de travail a été lié à un espace de travail existant au moment de la création, l’espace de travail auquel il a été lié n’est pas supprimé.|
|/workspaces/generateregistrationcertificate/action|Génère le certificat d’inscription de l’espace de travail. Ce certificat est utilisé pour connecter Microsoft System Center Operation Manager à l’espace de travail.|
|/workspaces/intelligencepacks/disable/action|Désactive un Intelligence Pack pour un espace de travail donné.|
|/workspaces/intelligencepacks/enable/action|Active un Intelligence Pack pour un espace de travail donné.|
|/workspaces/intelligencepacks/read|Répertorie tous les Intelligence Packs visibles pour un espace de travail donné et répertorie également si le pack est activé ou désactivé pour cet espace de travail.|
|/workspaces/linkedServices/delete|Supprime les services liés sous un espace de travail spécifié.|
|/workspaces/linkedServices/read|Obtient les services liés sous un espace de travail spécifié.|
|/workspaces/linkedServices/write|Crée/met à jour les services liés sous un espace de travail spécifié.|
|/workspaces/listKeys/action|Récupère les clés de liste pour l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail.|
|/workspaces/listKeys/read|Récupère les clés de liste pour l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail.|
|/workspaces/managementGroups/read|Obtient les noms et les métadonnées des groupes d’administration System Center Operations Manager connectés à cet espace de travail.|
|/workspaces/metricDefinitions/read|Obtient les définitions de métrique sous un espace de travail|
|/workspaces/notificationSettings/delete|Supprime les paramètres de notification de l’utilisateur pour l’espace de travail.|
|/workspaces/notificationSettings/read|Obtient les paramètres de notification de l’utilisateur pour l’espace de travail.|
|/workspaces/notificationSettings/write|Définit les paramètres de notification de l’utilisateur pour l’espace de travail.|
|/workspaces/purge/action|Supprime les données spécifiées de l’espace de travail|
|/workspaces/read|Obtient un espace de travail existant.|
|/workspaces/savedSearches/delete|Supprime une requête de recherche enregistrée.|
|/workspaces/savedSearches/read|Obtient une requête de recherche enregistrée.|
|/workspaces/savedSearches/write|Crée une requête de recherche enregistrée.|
|/workspaces/schema/read|Obtient le schéma de recherche de l’espace de travail.  Le schéma de recherche inclut les champs exposés et leurs types.|
|/workspaces/search/action|Exécute une requête de recherche.|
|/workspaces/sharedKeys/action|Récupère les clés partagées de l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail.|
|/workspaces/sharedKeys/read|Récupère les clés partagées de l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail.|
|/workspaces/storageinsightconfigs/delete|Supprime une configuration de stockage. Cela empêchera Microsoft Operational Insights de lire des données du compte de stockage.|
|/workspaces/storageinsightconfigs/read|Obtient une configuration de stockage.|
|/workspaces/storageinsightconfigs/write|Crée une configuration de stockage. Ces configurations sont utilisées pour extraire des données à partir d’un emplacement dans un compte de stockage existant.|
|/workspaces/usages/read|Obtient les données d’utilisation d’un espace de travail, y compris la quantité de données lues par l’espace de travail.|
|/workspaces/write|Crée un espace de travail ou lie un espace de travail existant en fournissant l’ID de client à partir de l’espace de travail existant.|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Opération | Description |
|---|---|
|/managementAssociations/delete|Supprime une association de gestion existante|
|/managementAssociations/read|Obtient une association de gestion existante|
|/managementAssociations/write|Crée une association de gestion|
|/managementConfigurations/delete|Supprime une configuration de gestion existante|
|/managementConfigurations/read|Obtient une configuration de gestion existante|
|/managementConfigurations/write|Crée une configuration de gestion|
|/register/action|Inscrire un abonnement à un fournisseur de ressources.|
|/solutions/delete|Supprimer la solution OMS existante.|
|/solutions/read|Obtenir la solution OMS existante.|
|/solutions/write|Créer une solution OMS.|

## <a name="microsoftportal"></a>Microsoft.Portal

| Opération | Description |
|---|---|
|/dashboards/delete|Supprime le tableau de bord de l’abonnement.|
|/dashboards/read|Lit les tableaux de bord pour l’abonnement.|
|/dashboards/write|Ajoute ou modifie un tableau de bord à un abonnement.|

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

| Opération | Description |
|---|---|
|/capacities/checkNameAvailability/action|Vérifie que le nom de la capacité dédiée Power BI spécifié est valide et non utilisé.|
|/capacities/delete|Supprime la capacité dédiée Power BI.|
|/capacities/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour la capacité dédiée Power BI.|
|/capacities/read|Récupère les informations de la capacité dédiée Power BI spécifiée.|
|/capacities/write|Crée ou met à jour la capacité dédiée Power BI spécifiée.|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Opération | Description |
|---|---|
|/locations/allocatedStamp/read|GetAllocatedStamp est une opération interne utilisée par le service.|
|/locations/allocateStamp/action|AllocateStamp est une opération interne utilisée par le service.|
|/locations/backupPreValidateProtection/action||
|/locations/backupStatus/action|Vérifie l’état de la sauvegarde pour les coffres Recovery Services|
|/locations/backupValidateFeatures/action|Valide des fonctionnalités|
|/operations/read|Retourne la liste d’opérations pour un fournisseur de ressources|
|/register/action|Inscrit un abonnement pour le fournisseur de ressources spécifié|
|/Vaults/backupconfig/read|Renvoie la configuration pour le coffre Recovery Services.|
|/Vaults/backupconfig/write|Met à jour la configuration pour le coffre Recovery Services.|
|/Vaults/backupEngines/read|Retourne tous les serveurs d’administration de sauvegarde inscrits auprès du coffre.|
|/Vaults/backupFabrics/{fabricName}/protectionContainers/{containerName}/items/read|Obtient tous les éléments figurant dans un conteneur|
|/Vaults/backupFabrics/backupProtectionIntent/write|Crée une intention de protection de sauvegarde.|
|/Vaults/backupFabrics/operationResults/read|Renvoie l’état de l’opération.|
|/Vaults/backupFabrics/protectableContainers/read|Obtient tous les conteneurs protégeables|
|/Vaults/backupFabrics/protectionContainers/inquire/action|Recherche les charges de travail dans un conteneur|
|/Vaults/backupFabrics/protectionContainers/operationResults/read|Obtient les résultats de l’opération effectuée sur le conteneur de protection.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action|Effectue la sauvegarde d’un élément protégé.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/delete|Supprime un élément protégé.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read|Obtient les résultats de l’opération effectuée sur les éléments protégés.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read|Renvoie l’état de l’opération effectuée sur les éléments protégés.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/read|Renvoie des détails d’objet de l’élément protégé.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/provisionInstantItemRecovery/action|Approvisionner la récupération d’éléments instantanée pour l’élément protégé.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read|Obtenir les points de récupération des éléments protégés.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/restore/action|Restaurer les points de récupération des éléments protégés.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/revokeInstantItemRecovery/action|Révoquer la récupération d’éléments instantanée pour l’élément protégé.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/write|Créer un élément protégé de sauvegarde.|
|/Vaults/backupFabrics/protectionContainers/read|Renvoie tous les conteneurs inscrits.|
|/Vaults/backupFabrics/protectionContainers/write|Crée un conteneur inscrit|
|/Vaults/backupFabrics/refreshContainers/action|Actualise la liste de conteneurs.|
|/Vaults/backupJobs/cancel/action|Annuler le travail.|
|/Vaults/backupJobs/operationResults/read|Renvoie le résultat de l’opération de travail.|
|/Vaults/backupJobs/read|Renvoie tous les objets de travail.|
|/Vaults/backupJobsExport/action|Travaux d’exportation|
|/Vaults/backupJobsExport/operationResults/read|Renvoie le résultat de l’opération de travail d’exportation.|
|/Vaults/backupManagementMetaData/read|Renvoie les métadonnées de gestion des sauvegardes pour le coffre Recovery Services.|
|/Vaults/backupOperationResults/read|Renvoie le résultat de l’opération de sauvegarde pour le coffre Recovery Services.|
|/Vaults/backupOperations/read|Renvoie l’état de l’opération de sauvegarde pour le coffre Recovery Services.|
|/Vaults/backupPolicies/delete|Supprimer une stratégie de protection.|
|/Vaults/backupPolicies/operationResults/read|Obtenir les résultats de l’opération de stratégie.|
|/Vaults/backupPolicies/operations/read|Obtenir l’état de l’opération de stratégie.|
|/Vaults/backupPolicies/read|Renvoie toutes les stratégies de protection.|
|/Vaults/backupPolicies/write|Crée une stratégie de protection.|
|/Vaults/backupProtectableItems/read|Renvoie la liste de tous les éléments pouvant être protégés.|
|/Vaults/backupProtectedItems/read|Renvoie la liste de tous les éléments protégés.|
|/Vaults/backupProtectionContainers/read|Renvoie tous les conteneurs appartenant à l’abonnement.|
|/Vaults/backupSecurityPIN/action|Renvoie les informations relatives au code PIN de sécurité pour le coffre Recovery Services.|
|/Vaults/backupstorageconfig/read|Renvoie la configuration de stockage pour le coffre Recovery Services.|
|/Vaults/backupstorageconfig/write|Met à jour la configuration de stockage pour le coffre Recovery Services.|
|/Vaults/backupUsageSummaries/read|Renvoie des résumés pour les éléments protégés et les serveurs protégés d’un coffre Recovery Services.|
|/Vaults/certificates/write|L’opération de mise à jour de certificat de ressource met à jour le certificat d’informations d’identification du coffre/de la ressource.|
|/Vaults/delete|L’opération de suppression de coffre supprime la ressource Azure de type « coffre » spécifiée.|
|/Vaults/extendedInformation/delete|L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ».|
|/Vaults/extendedInformation/read|L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ».|
|/Vaults/extendedInformation/write|L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ».|
|/Vaults/monitoringAlerts/read|Obtient les alertes pour le coffre Recovery Services.|
|/Vaults/monitoringAlerts/write|Résout l’alerte.|
|/Vaults/monitoringConfigurations/read|Obtient la configuration de notification du coffre Recovery Services.|
|/Vaults/monitoringConfigurations/write|Configure les notifications par e-mail au coffre Recovery Services.|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/read|Diagnostics de sauvegarde Azure|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/write|Diagnostics de sauvegarde Azure|
|/Vaults/providers/Microsoft.Insights/logDefinitions/read|Journaux de sauvegarde Azure|
|/Vaults/providers/Microsoft.Insights/metricDefinitions/read|Métriques de sauvegarde Azure|
|/Vaults/read|L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ».|
|/Vaults/registeredIdentities/delete|L’opération d’annulation de l’inscription d’un conteneur peut être utilisée pour annuler l’inscription d’un conteneur.|
|/Vaults/registeredIdentities/operationResults/read|L’opération d’obtention des résultats d’une opération peut être utilisée pour obtenir l’état de l’opération et le résultat de l’opération envoyée de manière asynchrone.|
|/Vaults/registeredIdentities/read|L’opération d’obtention de conteneurs peut être utilisée pour obtenir les conteneurs inscrits pour une ressource.|
|/Vaults/registeredIdentities/write|L’opération d’inscription d’un conteneur de service peut être utilisée pour inscrire un conteneur avec Recovery Services.|
|/vaults/replicationAlertSettings/read|Lire des paramètres d’alertes.|
|/vaults/replicationAlertSettings/write|Créer ou mettre à jour des paramètres d’alertes.|
|/vaults/replicationEvents/read|Lire des événements.|
|/vaults/replicationFabrics/checkConsistency/action|Vérifie la cohérence de la structure.|
|/vaults/replicationFabrics/delete|Supprimer des structures.|
|/vaults/replicationFabrics/deployProcessServerImage/action|Déployer une image de serveur de traitement.|
|/vaults/replicationFabrics/read|Lire des structures.|
|/vaults/replicationFabrics/reassociateGateway/action|Réassocier une passerelle.|
|/vaults/replicationFabrics/remove/action|Supprimer une structure.|
|/vaults/replicationFabrics/renewcertificate/action|Renouveler le certificat pour Fabric|
|/vaults/replicationFabrics/replicationNetworks/read|Lire des réseaux.|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete|Supprimer des mappages de réseau.|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read|Lire des mappages de réseau.|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write|Créer ou mettre à jour des mappages de réseau.|
|/vaults/replicationFabrics/replicationProtectionContainers/ discoverProtectableItem/action|Découvrir un élément pouvant être protégé.|
|/vaults/replicationFabrics/replicationProtectionContainers/read|Lire des conteneurs de protection.|
|/vaults/replicationFabrics/replicationProtectionContainers/remove/action|Supprimer un conteneur de protection.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectableItems/read|Lire des éléments pouvant être protégés.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/applyRecoveryPoint/action|Appliquer un point de récupération.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/delete|Supprimer des éléments protégés.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/failoverCommit/action|Validation du basculement.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/plannedFailover/action|Basculement planifié.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/read|Lire des éléments protégés.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/recoveryPoints/read|Lire des points de récupération de réplication.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/remove/action|Supprimer un élément protégé.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/repairReplication/action|Réparer la réplication.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/reProtect/action|Reprotéger l’élément protégé.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailover/action|Test Failover|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailoverCleanup/action|Nettoyage de basculement test.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/unplannedFailover/action|Basculement|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/updateMobilityService/action|Mettre à jour le service Mobilité.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/write|Créer ou mettre à jour des éléments protégés.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/delete|Supprimer des mappages de conteneurs de protection.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/read|Lire des mappages de conteneurs de protection.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/remove/action|Supprimer un mappage de conteneurs de protection.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/write|Créer ou mettre à jour des mappages de conteneurs de protection.|
|/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action|Basculer un conteneur de protection.|
|/vaults/replicationFabrics/replicationProtectionContainers/write|Créer ou mettre à jour des conteneurs de protection.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete|Supprimer des fournisseurs Recovery Services.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/read|Lire des fournisseurs Recovery Services.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/ refreshProvider/action|Actualiser un fournisseur.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action|Supprimer un fournisseur Recovery Services.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/write|Crée ou met à jour des fournisseurs Recovery Services|
|/vaults/replicationFabrics/replicationStorageClassifications/read|Lire des classifications de stockage.|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/delete|Supprimer des mappages de classifications de stockage.|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/read|Lire des mappages de classifications de stockage.|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/write|Créer ou mettre à jour des mappages de classifications de stockage.|
|/vaults/replicationFabrics/replicationvCenters/delete|Supprimer des travaux.|
|/vaults/replicationFabrics/replicationvCenters/read|Lire des travaux.|
|/vaults/replicationFabrics/replicationvCenters/write|Créer ou mettre à jour des travaux.|
|/vaults/replicationFabrics/write|Créer ou mettre à jour des structures.|
|/vaults/replicationJobs/cancel/action|Annuler un travail.|
|/vaults/replicationJobs/read|Lire des travaux.|
|/vaults/replicationJobs/restart/action|Redémarrer un travail.|
|/vaults/replicationJobs/resume/action|Reprendre un travail.|
|/vaults/replicationPolicies/delete|Supprimer des stratégies.|
|/vaults/replicationPolicies/read|Lire des stratégies.|
|/vaults/replicationPolicies/write|Créer ou mettre à jour des stratégies.|
|/vaults/replicationRecoveryPlans/delete|Supprimer des plans de récupération.|
|/vaults/replicationRecoveryPlans/failoverCommit/action|Plan de récupération de validation de basculement.|
|/vaults/replicationRecoveryPlans/plannedFailover/action|Plan de récupération de basculement planifié.|
|/vaults/replicationRecoveryPlans/read|Lire des plans de récupération.|
|/vaults/replicationRecoveryPlans/reProtect/action|Reprotéger le plan de récupération.|
|/vaults/replicationRecoveryPlans/testFailover/action|Plan de récupération de basculement test.|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|Plan de récupération de nettoyage de basculement test.|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|Plan de récupération de basculement.|
|/vaults/replicationRecoveryPlans/write|Créer ou mettre à jour des plans de récupération.|
|/Vaults/tokenInfo/read|Renvoie des informations de jeton pour le coffre Recovery Services.|
|/vaults/usages/read|Lire des utilisations de coffre.|
|/Vaults/usages/read|Renvoie des détails d’utilisation d’un coffre Recovery Services.|
|/Vaults/vaultTokens/read|L’opération de jeton de coffre peut être utilisée pour obtenir un jeton de coffre pour les opérations de serveur principal au niveau du coffre.|
|/Vaults/write|L’opération de création de coffre entraîne la création d’une ressource Azure de type « coffre ».|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Opération | Description |
|---|---|
|/checkNameAvailability/action|Vérifier la disponibilité d’un espace de noms sous un abonnement donné|
|/checkNamespaceAvailability/action|Vérifier la disponibilité d’un espace de noms sous un abonnement donné Cette API étant déconseillée, utilisez CheckNameAvailabiltiy à la place.|
|/namespaces/authorizationRules/action|Met à jour une règle d’autorisation d’espace de noms. Cette API est déconseillée. Utilisez un appel PUT à la place pour mettre à jour la règle d’autorisation d’espace de noms... Cette opération n’est pas prise en charge sur la version d’API 2017-04-01.|
|/namespaces/authorizationRules/delete|Supprimer une règle d’autorisation de ressource Namespace. La règle d’autorisation de ressource Namespace par défaut ne peut pas être supprimée. |
|/namespaces/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à la ressource Namespace|
|/namespaces/authorizationRules/read|Obtenir la liste des descriptions des règles d’autorisation des ressources Namespaces.|
|/namespaces/authorizationRules/regenerateKeys/action|Régénère la clé primaire ou secondaire pour la ressource.|
|/namespaces/authorizationRules/write|Créer des règles d’autorisation au niveau d’une ressource Namespace et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour.|
|/namespaces/Delete|Supprimer une ressource Namespace|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Obtient les clés de règles d’autorisation pour l’espace de noms principal de récupération d’urgence|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Obtient les règles d’autorisation de l’espace de noms principal de récupération d’urgence|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Désactive la récupération d’urgence et arrête la réplication des changements de l’espace de noms principal sur l’espace de noms secondaire.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Vérifie la disponibilité d’un alias d’espace de noms sous un abonnement donné.|
|/namespaces/disasterRecoveryConfigs/delete|Supprime la configuration de récupération d’urgence associée à l’espace de noms. Cette opération peut être appelée uniquement via l’espace de noms principal.|
|/namespaces/disasterRecoveryConfigs/failover/action|Appelle un basculement de géorécupération d’urgence et reconfigure l’alias d’espace de noms pour qu’il pointe vers l’espace de noms secondaire.|
|/namespaces/disasterRecoveryConfigs/read|Obtient la configuration de récupération d’urgence associée à l’espace de noms.|
|/namespaces/disasterRecoveryConfigs/write|Crée ou met à jour la configuration de récupération d’urgence associée à l’espace de noms.|
|/namespaces/HybridConnections/authorizationRules/action|Met à jour HybridConnection. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. Règles d’autorisation. Utilisez un appel PUT pour mettre à jour la règle d’autorisation.|
|/namespaces/HybridConnections/authorizationRules/delete|Opération de suppression des règles d’autorisation HybridConnection.|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à HybridConnection.|
|/namespaces/HybridConnections/authorizationRules/read| Obtient la liste des règles d’autorisation de HybridConnection|
|/namespaces/HybridConnections/authorizationRules/regeneratekeys/action|Régénère la clé primaire ou secondaire pour la ressource.|
|/namespaces/HybridConnections/authorizationRules/write|Créer des règles d’autorisation HybridConnection et mettre à jour leurs propriétés. Les droits d’accès des règles d’autorisation peuvent être mis à jour.|
|/namespaces/HybridConnections/Delete|Opération de suppression des ressources HybridConnection.|
|/namespaces/HybridConnections/read|Obtenir la liste des descriptions des ressources HybridConnection.|
|/namespaces/HybridConnections/write|Créer ou mettre à jour des propriétés HybridConnection.|
|/namespaces/messagingPlan/read|Obtient le plan de messagerie pour un espace de noms. Cette API est déconseillée. Les propriétés exposées via la ressource MessagingPlan sont déplacées vers la ressource d’espace de noms (parente) dans les versions ultérieures de l’API. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01.|
|/namespaces/messagingPlan/write|Met à jour le plan de messagerie pour un espace de noms. Cette API est déconseillée. Les propriétés exposées via la ressource MessagingPlan sont déplacées vers la ressource d’espace de noms (parente) dans les versions ultérieures de l’API. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01.|
|/namespaces/operationresults/read|Obtient l’état de l’opération d’espace de noms|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Obtenir la liste des descriptions des mesures de ressource Namespace.|
|/namespaces/read|Obtenir la liste des descriptions des ressources Namespace.|
|/namespaces/WcfRelays/authorizationRules/action|Met à jour WcfRelay. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. Règles d’autorisation. Utilisez un appel PUT pour mettre à jour la règle d’autorisation.|
|/namespaces/WcfRelays/authorizationRules/delete|Opération de suppression des règles d’autorisation WcfRelay.|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à WcfRelay.|
|/namespaces/WcfRelays/authorizationRules/read| Obtient la liste des règles d’autorisation de WcfRelay|
|/namespaces/WcfRelays/authorizationRules/regeneratekeys/action|Régénère la clé primaire ou secondaire pour la ressource.|
|/namespaces/WcfRelays/authorizationRules/write|Créer des règles d’autorisation WcfRelay et mettre à jour leurs propriétés. Les droits d’accès des règles d’autorisation peuvent être mis à jour.|
|/namespaces/WcfRelays/Delete|Opération de suppression des ressources WcfRelay.|
|/namespaces/WcfRelays/read|Obtenir la liste des descriptions des ressources WcfRelay.|
|/namespaces/WcfRelays/write|Créer ou mettre à jour des propriétés WcfRelay.|
|/namespaces/write|Créer une ressource Namespace et mettre à jour ses propriétés. Les balises et la capacité de l’espace de noms sont les propriétés qui peuvent être mises à jour.|
|/operations/read|Obtient les opérations|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources Relay et active la création de ressources Relay.|
|/unregister/action|Inscrit l’abonnement pour le fournisseur de ressources Relay et active la création de ressources Relay.|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Opération | Description |
|---|---|
|/AvailabilityStatuses/current/read|Obtient l’état de disponibilité de la ressource spécifiée.|
|/AvailabilityStatuses/read|Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée.|
|/healthevent/action|Indique le changement d’état d’intégrité de la ressource spécifiée|
|/healthevent/Activated/action|Indique le changement d’état d’intégrité de la ressource spécifiée|
|/healthevent/InProgress/action|Indique le changement d’état d’intégrité de la ressource spécifiée|
|/healthevent/Pending/action|Indique le changement d’état d’intégrité de la ressource spécifiée|
|/healthevent/Resolved/action|Indique le changement d’état d’intégrité de la ressource spécifiée|
|/healthevent/Updated/action|Indique le changement d’état d’intégrité de la ressource spécifiée|
|/register/action|Inscrit l’abonnement à Microsoft ResourceHealth|

## <a name="microsoftresources"></a>Microsoft.Resources

| Opération | Description |
|---|---|
|/checkResourceName/action|Vérifier la validité du nom de la ressource.|
|/deployments/cancel/action|Annule un déploiement.|
|/deployments/delete|Supprime un déploiement.|
|/deployments/operations/read|Obtient ou répertorie les opérations de déploiement.|
|/deployments/read|Obtient ou répertorie les déploiements.|
|/deployments/validate/action|Valide un déploiement.|
|/deployments/write|Crée ou met à jour un déploiement.|
|/links/delete|Supprime un lien de ressource.|
|/links/read|Obtient ou répertorie les liens des ressources.|
|/links/write|Crée ou met à jour un lien de ressource.|
|/marketplace/purchase/action|Achète une ressource sur la place de marché.|
|/providers/read|Obtient la liste des fournisseurs.|
|/resources/read|Obtenir la liste des ressources en fonction des filtres.|
|/subscriptions/locations/read|Obtient la liste des emplacements pris en charge.|
|/subscriptions/operationresults/read|Obtenir les résultats de l’opération de l’abonnement.|
|/subscriptions/providers/read|Obtient ou répertorie les fournisseurs de ressources.|
|/subscriptions/read|Obtient la liste des abonnements.|
|/subscriptions/resourceGroups/delete|Supprime un groupe de ressources, ainsi que toutes ses ressources.|
|/subscriptions/resourcegroups/deployments/operations/read|Obtient ou répertorie les opérations de déploiement.|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|Obtient ou répertorie les états de l’opération de déploiement.|
|/subscriptions/resourcegroups/deployments/read|Obtient ou répertorie les déploiements.|
|/subscriptions/resourcegroups/deployments/write|Crée ou met à jour un déploiement.|
|/subscriptions/resourceGroups/moveResources/action|Déplace les ressources d’un groupe de ressources à un autre.|
|/subscriptions/resourceGroups/read|Obtient ou répertorie les groupes de ressources.|
|/subscriptions/resourcegroups/resources/read|Obtient les ressources du groupe de ressources.|
|/subscriptions/resourceGroups/validateMoveResources/action|Valide le déplacement des ressources d’un groupe de ressources à un autre.|
|/subscriptions/resourceGroups/write|Crée ou met à jour un groupe de ressources.|
|/subscriptions/resources/read|Obtient les ressources d’un abonnement.|
|/subscriptions/tagNames/delete|Supprime une balise d’abonnement.|
|/subscriptions/tagNames/read|Obtient ou répertorie les balises d’abonnement.|
|/subscriptions/tagNames/tagValues/delete|Supprime une valeur de balise d’abonnement.|
|/subscriptions/tagNames/tagValues/read|Obtient ou répertorie les valeurs des balises d’abonnement.|
|/subscriptions/tagNames/tagValues/write|Ajoute une valeur de balise d’abonnement.|
|/subscriptions/tagNames/write|Ajoute une balise d’abonnement.|
|/tenants/read|Obtient la liste des clients.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Opération | Description |
|---|---|
|/jobcollections/delete|Supprime une collection de travaux.|
|/jobcollections/disable/action|Désactive une collection de travaux.|
|/jobcollections/enable/action|Active une collection de travaux.|
|/jobcollections/jobs/delete|Supprime un travail.|
|/jobcollections/jobs/generateLogicAppDefinition/action|Génère une définition d’application logique basée sur un travail de Scheduler.|
|/jobcollections/jobs/jobhistories/read|Affiche l’historique des travaux.|
|/jobcollections/jobs/read|Obtient un travail.|
|/jobcollections/jobs/run/action|Exécute un travail.|
|/jobcollections/jobs/write|Crée ou met à jour un travail.|
|/jobcollections/read|Obtenir une collection de travaux.|
|/jobcollections/write|Crée ou met à jour une collection de travaux.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Opération | Description |
|---|---|
|/checkNameAvailability/action|Vérifie la disponibilité du nom du service.|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources de recherche et active la création de services de recherche.|
|/searchServices/createQueryKey/action|Crée la clé de requête.|
|/searchServices/delete|Supprime le service de recherche.|
|/searchServices/diagnosticSettings/read|Obtient le paramètre de diagnostic lu pour la ressource|
|/searchServices/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/searchServices/listAdminKeys/action|Lit les clés d’administration.|
|/searchServices/logDefinitions/read|Obtient les journaux disponibles pour le service de recherche|
|/searchServices/metricDefinitions/read|Obtient les métriques disponibles pour le service de recherche|
|/searchServices/queryKey/delete|Supprime la clé de requête.|
|/searchServices/queryKey/read|Lit les clés de requête.|
|/searchServices/read|Lit le service de recherche.|
|/searchServices/regenerateAdminKey/action|Régénère la clé d’administration.|
|/searchServices/start/action|Démarre le service de recherche.|
|/searchServices/stop/action|Arrête le service de recherche.|
|/searchServices/write|Crée ou met à jour le service de recherche.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Opération | Description |
|---|---|
|/alerts/read|Obtient toutes les alertes de sécurité disponibles|
|/applicationWhitelistings/read|Obtient les listes blanches des applications.|
|/applicationWhitelistings/write|Crée une liste verte des applications ou met à jour une liste verte des applications existante.|
|/complianceResults/read|Obtient les résultats de conformité pour la ressource|
|/locations/alerts/activate/action|Active une alerte de sécurité|
|/locations/alerts/dismiss/action|Ignore une alerte de sécurité.|
|/locations/alerts/read|Obtient toutes les alertes de sécurité disponibles|
|/locations/jitNetworkAccessPolicies/initiate/action|Initie une stratégie d’accès réseau immédiat.|
|/locations/jitNetworkAccessPolicies/read|Obtient les stratégies d’accès réseau immédiat.|
|/locations/jitNetworkAccessPolicies/write|Crée une stratégie d’accès réseau immédiat ou met à jour une stratégie d’accès réseau immédiat existante.|
|/locations/read|Obtient l’emplacement des données de sécurité|
|/locations/tasks/activate/action|Activer une recommandation de sécurité.|
|/locations/tasks/dismiss/action|Ignorer une recommandation de sécurité.|
|/locations/tasks/read|Obtient toutes les recommandations de sécurité disponibles.|
|/locations/tasks/resolve/action|Résout une recommandation de sécurité|
|/locations/tasks/start/action|Démarre une recommandation de sécurité|
|/policies/read|Obtient la stratégie de sécurité.|
|/policies/write|Met à jour la stratégie de sécurité.|
|/pricings/delete|Supprime les paramètres de tarification pour l’étendue|
|/pricings/read|Obtient les paramètres de tarification pour l’étendue|
|/pricings/write|Met à jour les paramètres de tarification pour l’étendue|
|/register/action|Enregistre l’abonnement pour Azure Security Center|
|/securityContacts/delete|Supprime le contact de sécurité|
|/securityContacts/read|Obtient le contact de sécurité|
|/securityContacts/write|Met à jour le contact de sécurité|
|/securitySolutions/delete|Supprime une solution de sécurité.|
|/securitySolutions/read|Obtient les solutions de sécurité.|
|/securitySolutions/write|Crée une solution de sécurité ou met à jour une solution de sécurité existante.|
|/securitySolutionsReferenceData/read|Obtient les données de référence des solutions de sécurité.|
|/securityStatuses/read|Obtient les états d’intégrité de la sécurité des ressources Azure.|
|/securityStatusesSummaries/read|Obtient les résumés d’état de sécurité pour l’étendue|
|/tasks/read|Obtient toutes les recommandations de sécurité disponibles.|
|/webApplicationFirewalls/delete|Supprime un pare-feu d’applications web.|
|/webApplicationFirewalls/read|Obtient le pare-feu d’applications web.|
|/webApplicationFirewalls/write|Crée un pare-feu d’applications web nouvelles ou met à jour un pare-feu d’applications web existant.|
|/workspaceSettings/connect/action|Modifie les paramètres de reconnexion de l’espace de travail|
|/workspaceSettings/delete|Supprime les paramètres de l’espace de travail|
|/workspaceSettings/read|Obtient les paramètres de l’espace de travail|
|/workspaceSettings/write|Met à jour les paramètres de l’espace de travail|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Opération | Description |
|---|---|
|/checkNameAvailability/action|Vérifier la disponibilité d’un espace de noms sous un abonnement donné|
|/checkNamespaceAvailability/action|Vérifier la disponibilité d’un espace de noms sous un abonnement donné Cette API étant déconseillée, utilisez CheckNameAvailabiltiy à la place.|
|/namespaces/authorizationRules/action|Met à jour une règle d’autorisation d’espace de noms. Cette API est déconseillée. Utilisez un appel PUT à la place pour mettre à jour la règle d’autorisation d’espace de noms... Cette opération n’est pas prise en charge sur la version d’API 2017-04-01.|
|/namespaces/authorizationRules/delete|Supprimer une règle d’autorisation de ressource Namespace. La règle d’autorisation de ressource Namespace par défaut ne peut pas être supprimée. |
|/namespaces/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à la ressource Namespace|
|/namespaces/authorizationRules/read|Obtenir la liste des descriptions des règles d’autorisation des ressources Namespaces.|
|/namespaces/authorizationRules/regenerateKeys/action|Régénère la clé primaire ou secondaire pour la ressource.|
|/namespaces/authorizationRules/write|Créer des règles d’autorisation au niveau d’une ressource Namespace et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour.|
|/namespaces/Delete|Supprimer une ressource Namespace|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Obtient les clés de règles d’autorisation pour l’espace de noms principal de récupération d’urgence|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Obtient les règles d’autorisation de l’espace de noms principal de récupération d’urgence|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Désactive la récupération d’urgence et arrête la réplication des changements de l’espace de noms principal sur l’espace de noms secondaire.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Vérifie la disponibilité d’un alias d’espace de noms sous un abonnement donné.|
|/namespaces/disasterRecoveryConfigs/delete|Supprime la configuration de récupération d’urgence associée à l’espace de noms. Cette opération peut être appelée uniquement via l’espace de noms principal.|
|/namespaces/disasterRecoveryConfigs/failover/action|Appelle un basculement de géorécupération d’urgence et reconfigure l’alias d’espace de noms pour qu’il pointe vers l’espace de noms secondaire.|
|/namespaces/disasterRecoveryConfigs/read|Obtient la configuration de récupération d’urgence associée à l’espace de noms.|
|/namespaces/disasterRecoveryConfigs/write|Crée ou met à jour la configuration de récupération d’urgence associée à l’espace de noms.|
|/namespaces/eventGridFilters/delete|Supprime le filtre Event Grid associé à l’espace de noms.|
|/namespaces/eventGridFilters/read|Obtient le filtre Event Grid associé à l’espace de noms.|
|/namespaces/eventGridFilters/write|Crée ou met à jour le filtre Event Grid associé à l’espace de noms.|
|/namespaces/eventhubs/read|Obtenir la liste des descriptions de ressources EventHub|
|/namespaces/messagingPlan/read|Obtient le plan de messagerie pour un espace de noms. Cette API est déconseillée. Les propriétés exposées via la ressource MessagingPlan sont déplacées vers la ressource d’espace de noms (parente) dans les versions ultérieures de l’API. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01.|
|/namespaces/messagingPlan/write|Met à jour le plan de messagerie pour un espace de noms. Cette API est déconseillée. Les propriétés exposées via la ressource MessagingPlan sont déplacées vers la ressource d’espace de noms (parente) dans les versions ultérieures de l’API. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01.|
|/namespaces/migrate/action|Migre une opération d’espace de noms|
|/namespaces/operationresults/read|Obtient l’état de l’opération d’espace de noms|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Obtenir la liste des descriptions des ressources des paramètres de diagnostics Namespace.|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Obtenir la liste des descriptions des ressources des paramètres de diagnostics Namespace.|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Obtenir la liste des descriptions des ressources des journaux Namespace.|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Obtenir la liste des descriptions des mesures de ressource Namespace.|
|/namespaces/queues/authorizationRules/action|Met à jour la file d’attente. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. Règles d’autorisation. Utilisez un appel PUT pour mettre à jour la règle d’autorisation.|
|/namespaces/queues/authorizationRules/delete|Opération de suppression des règles d’autorisation de file d’attente.|
|/namespaces/queues/authorizationRules/listkeys/action|Obtenir la chaîne de connexion à la file d’attente.|
|/namespaces/queues/authorizationRules/read| Obtenir la liste des règles d’autorisation de file d’attente.|
|/namespaces/queues/authorizationRules/regenerateKeys/action|Régénère la clé primaire ou secondaire pour la ressource.|
|/namespaces/queues/authorizationRules/write|Créer des règles d’autorisation de file d’attente et mettre à jour leurs propriétés. Les droits d’accès des règles d’autorisation peuvent être mis à jour.|
|/namespaces/queues/Delete|Opération de suppression des ressources de file d’attente.|
|/namespaces/queues/read|Obtenir la liste des descriptions des ressources de file d’attente.|
|/namespaces/queues/write|Créer ou mettre à jour des propriétés de file d’attente.|
|/namespaces/read|Obtenir la liste des descriptions des ressources Namespace.|
|/namespaces/topics/authorizationRules/action|Met à jour la rubrique. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. Règles d’autorisation. Utilisez un appel PUT pour mettre à jour la règle d’autorisation.|
|/namespaces/topics/authorizationRules/delete|Opération de suppression des règles d’autorisation de rubrique|
|/namespaces/topics/authorizationRules/listkeys/action|Récupère la chaîne de connexion à la rubrique.|
|/namespaces/topics/authorizationRules/read| Récupère la liste des règles d’autorisation de rubrique.|
|/namespaces/topics/authorizationRules/regenerateKeys/action|Régénère la clé primaire ou secondaire pour la ressource.|
|/namespaces/topics/authorizationRules/write|Crée des règles d’autorisation de rubrique et met à jour leurs propriétés. Les droits d’accès des règles d’autorisation peuvent être mis à jour.|
|/namespaces/topics/Delete|Opération de suppression des ressources de rubrique.|
|/namespaces/topics/read|Obtenir la liste des descriptions des ressources de rubrique.|
|/namespaces/topics/subscriptions/Delete|Opération de suppression d’une ressource TopicSubscription|
|/namespaces/topics/subscriptions/read|Récupère la liste des descriptions des ressources TopicSubscription.|
|/namespaces/topics/subscriptions/rules/Delete|Opération de suppression d’une ressource de règle.|
|/namespaces/topics/subscriptions/rules/read|Récupère la liste des descriptions des ressources de règle.|
|/namespaces/topics/subscriptions/rules/write|Crée ou met à jour les propriétés de règle.|
|/namespaces/topics/subscriptions/write|Crée ou met à jour les propriétés TopicSubscription.|
|/namespaces/topics/write|Créer ou mettre à jour des propriétés de rubrique.|
|/namespaces/write|Créer une ressource Namespace et mettre à jour ses propriétés. Les balises et la capacité de l’espace de noms sont les propriétés qui peuvent être mises à jour.|
|/operations/read|Obtient les opérations|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources ServiceBus et active la création de ressources ServiceBus.|
|/sku/read|Obtient la liste des descriptions des ressources de référence (SKU).|
|/sku/regions/read|Obtient la liste des descriptions de ressources SkuRegions|
|/unregister/action|Inscrit l’abonnement pour le fournisseur de ressources ServiceBus et active la création de ressources ServiceBus.|

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

| Opération | Description |
|---|---|
|/clusters/applications/delete|Supprime une application|
|/clusters/applications/read|Lit une application|
|/clusters/applications/services/delete|Supprime un service|
|/clusters/applications/services/partitions/read|Lit une partition|
|/clusters/applications/services/partitions/replicas/read|Lit un réplica|
|/clusters/applications/services/read|Lit un service|
|/clusters/applications/services/statuses/read|Lit un état de service|
|/clusters/applications/services/write|Crée ou met à jour un service|
|/clusters/applications/write|Crée ou met à jour une application|
|/clusters/applicationTypes/delete|Supprime un type d’application|
|/clusters/applicationTypes/read|Lit un type d’application|
|/clusters/applicationTypes/versions/delete|Supprime une version de type d’application|
|/clusters/applicationTypes/versions/read|Lit une version de type d’application|
|/clusters/applicationTypes/versions/write|Crée ou met à jour une version de type d’application|
|/clusters/applicationTypes/write|Crée ou met à jour un type d’application|
|/clusters/delete|Supprime un cluster|
|/clusters/nodes/read|Lit un nœud|
|/clusters/read|Lit un cluster|
|/clusters/statuses/read|Lit un état de cluster|
|/clusters/write|Crée ou met à jour un cluster|
|/locations/clusterVersions/read|Lit une version de cluster|
|/locations/environments/clusterVersions/read|Lit une version de cluster pour un environnement spécifique|
|/locations/operationresults/read|Lit les résultats d’une opération|
|/locations/operations/read|Lit des opérations par emplacement|
|/operations/read|Lit des opérations disponibles|
|/register/action|Inscrit une action|

## <a name="microsoftsolutions"></a>Microsoft.Solutions

| Opération | Description |
|---|---|
|/applicationDefinitions/delete|Supprime une définition d’application.|
|/applicationDefinitions/read|Récupère une liste de définitions d’application.|
|/applicationDefinitions/write|Ajoute ou modifie une définition d’application.|
|/applications/delete|Supprime une application.|
|/applications/read|Récupère une liste d’applications.|
|/applications/write|Crée une application.|
|/locations/operationStatuses/read|Lit l’état de l’opération pour la ressource.|
|/register/action|Inscrit à Solutions.|

## <a name="microsoftsql"></a>Microsoft.Sql

| Opération | Description |
|---|---|
|/checkNameAvailability/action|Vérifie si le nom du serveur spécifié est disponible pour l’approvisionnement dans le monde entier pour un abonnement donné.|
|/locations/auditingSettingsAzureAsyncOperation/read|Récupère le résultat de l’opération de configuration de la stratégie d’audit des objets blob de serveur.|
|/locations/auditingSettingsOperationResults/read|Récupérer le résultat l’opération de configuration de la stratégie d’audit des objets blob de serveur|
|/locations/capabilities/read|Obtient les capacités pour cet abonnement dans un emplacement donné|
|/locations/databaseAzureAsyncOperation/read|Obtient l’état d’une opération de base de données.|
|/locations/databaseOperationResults/read|Obtient l’état d’une opération de base de données.|
|/locations/deletedServerAsyncOperation/read|Obtient les opérations en cours d’exécution sur le serveur supprimé|
|/locations/deletedServerOperationResults/read|Obtient les opérations en cours d’exécution sur le serveur supprimé|
|/locations/deletedServers/read|Retourne la liste des serveurs supprimés ou obtient les propriétés du serveur supprimé spécifié.|
|/locations/deletedServers/recover/action|Récupère un serveur supprimé|
|/locations/deleteVirtualNetworkOrSubnets/action|Supprime les règles de réseau virtuel associés à un réseau virtuel ou à un sous-réseau|
|/locations/elasticPoolAzureAsyncOperation/read|Obtient l’opération asynchrone Azure pour une opération asynchrone de pool élastique|
|/locations/elasticPoolOperationResults/read|Obtient le résultat d’une opération de pool élastique.|
|/locations/extendedAuditingSettingsAzureAsyncOperation/read|Récupère le résultat de l’opération de configuration de la stratégie d’audit des objets blob de serveur.|
|/locations/extendedAuditingSettingsOperationResults/read|Récupère le résultat de l’opération de configuration de la stratégie d’audit des objets blob de serveur.|
|/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action|Achève l’opération de restauration de base de données managée|
|/locations/managedTransparentDataEncryptionAzureAsyncOperation/read|Obtient les opérations en cours d’exécution sur le chiffrement transparent des données de base de données managée|
|/locations/managedTransparentDataEncryptionOperationResults/read|Obtient les opérations en cours d’exécution sur le chiffrement transparent des données de base de données managée|
|/locations/read|Obtient les emplacements disponibles pour un abonnement spécifié|
|/locations/syncAgentOperationResults/read|Récupère le résultat de l’opération de ressource de l’agent de synchronisation|
|/locations/syncDatabaseIds/read|Récupère les ID de base de données de synchronisation pour une région et un abonnement particuliers|
|/locations/syncGroupOperationResults/read|Récupère le résultat de l’opération de ressource de groupe de synchronisation|
|/locations/syncMemberOperationResults/read|Récupère le résultat de l’opération de ressource de membre de synchronisation|
|/locations/usages/read|Obtient une collection de métriques d’utilisation pour cet abonnement dans un emplacement|
|/locations/virtualNetworkRulesAzureAsyncOperation/read|Retourne les résultats de l’opération asynchrone Azure effectuée selon les règles de réseau virtuel spécifiées |
|/locations/virtualNetworkRulesOperationResults/read|Retourne les détails de l’opération effectuée selon les règles de réseau virtuel spécifiées |
|/managedInstances/administrators/delete|Supprime un administrateur existant de l’instance gérée.|
|/managedInstances/administrators/read|Obtient la liste des administrateurs de l’instance gérée.|
|/managedInstances/administrators/write|Crée ou met à jour l’administrateur d’instance gérée avec les paramètres spécifiés.|
|/managedInstances/databases/delete|Supprime une base de données managée existante|
|/managedInstances/databases/read|Obtient une base de données managée existante|
|/managedInstances/databases/securityAlertPolicies/read|Récupère les détails de la stratégie de détection des menaces de base de données configurée sur une base de données managée spécifique|
|/managedInstances/databases/securityAlertPolicies/write|Modifie la stratégie de détection des menaces pour une base de données managée spécifiée|
|/managedInstances/databases/securityEvents/read|Récupère les événements de sécurité de base de données managée|
|/managedInstances/databases/transparentDataEncryption/read|Récupère les détails de chiffrement transparent des données sur une base de données managée spécifiée|
|/managedInstances/databases/transparentDataEncryption/write|Modifie le chiffrement transparent des données pour une base de données managée spécifiée|
|/managedInstances/databases/write|Crée une base de données ou met à jour une base de données existante.|
|/managedInstances/delete|Supprime une instance gérée existante.|
|/managedInstances/metricDefinitions/read|Obtient les définitions de métrique de l’instance gérée|
|/managedInstances/metrics/read|Obtient les métriques de l’instance gérée|
|/managedInstances/read|Retourne la liste des instances gérées ou obtient les propriétés de l’instance gérée spécifiée.|
|/managedInstances/securityAlertPolicies/read|Récupère les détails de la stratégie de détection des menaces de serveur configurée sur un serveur géré spécifique|
|/managedInstances/securityAlertPolicies/write|Modifie la stratégie de détection des menaces pour un serveur géré spécifié|
|/managedInstances/write|Crée une instance gérée avec les paramètres spécifiés ou met à jour les propriétés ou balises pour l’instance gérée spécifiée.|
|/operations/read|Obtient les opérations REST disponibles|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources Microsoft SQL Database et permet la création de bases de données Microsoft SQL.|
|/servers/administratorOperationResults/read|Obtient les opérations en cours sur les administrateurs du serveur|
|/servers/administrators/delete|Supprime un administrateur du serveur|
|/servers/administrators/read|Récupère les détails d’administrateur du serveur.|
|/servers/administrators/write|Crée ou met à jour un administrateur de serveur.|
|/servers/advisors/read|Renvoie la liste des conseillers disponibles pour le serveur|
|/servers/advisors/recommendedActions/read|Renvoie la liste des actions recommandées du conseiller spécifié pour le serveur|
|/servers/advisors/recommendedActions/write|Appliquer l’action recommandée sur le serveur|
|/servers/advisors/write|Met à jour l’état d’exécution automatique d’un conseiller au niveau du serveur.|
|/servers/auditingPolicies/read|Récupérer les détails de la stratégie d’audit de table du serveur par défaut configurée sur un serveur donné|
|/servers/auditingPolicies/write|Modifier l’audit de table du serveur par défaut pour un serveur donné|
|/servers/auditingSettings/operationResults/read|Récupérer le résultat l’opération de configuration de la stratégie d’audit des objets blob de serveur|
|/servers/auditingSettings/read|Récupérer les détails de la stratégie d’audit des objets blob de serveur configurée sur un serveur donné|
|/servers/auditingSettings/write|Modifier l’audit des objets blob de serveur pour un serveur donné|
|/servers/automaticTuning/read|Retourne les paramètres de réglage automatique pour le serveur|
|/servers/automaticTuning/write|Met à jour les paramètres de réglage automatique pour le serveur et retourne les paramètres mis à jour|
|/servers/backupLongTermRetentionVaults/delete|Supprime un coffre d’archivage de sauvegarde existant.|
|/servers/backupLongTermRetentionVaults/read|Cette opération est utilisée pour obtenir un coffre de rétention de sauvegarde à long terme. Retourne des informations sur le coffre inscrit sur ce serveur|
|/servers/backupLongTermRetentionVaults/write|Cette opération permet d’inscrire un coffre de rétention de sauvegarde à long terme auprès d’un serveur|
|/servers/communicationLinks/delete|Supprime un lien de communication de serveur existant.|
|/servers/communicationLinks/read|Retourne la liste des liens de communication d’un serveur spécifié.|
|/servers/communicationLinks/write|Crée ou met à jour un lien de communication du serveur.|
|/servers/connectionPolicies/read|Retourne la liste des stratégies de connexion d’un serveur spécifié.|
|/servers/connectionPolicies/write|Crée ou met à jour une stratégie de connexion du serveur.|
|/servers/databases/advisors/read|Retourne la liste des conseillers disponibles pour la base de données.|
|/servers/databases/advisors/recommendedActions/read|Retourne la liste des actions recommandées du conseiller spécifié pour la base de données.|
|/servers/databases/advisors/recommendedActions/write|Applique l’action recommandée sur la base de données.|
|/servers/databases/advisors/write|Met à jour l’état d’exécution automatique d’un conseiller au niveau de la base de données.|
|/servers/databases/auditingPolicies/read|Récupère les détails de la stratégie d’audit des tables configurée dans une base de données spécifique.|
|/servers/databases/auditingPolicies/write|Modifie la stratégie d’audit des tables pour une base de données spécifique.|
|/servers/databases/auditingSettings/read|Récupère les détails de la stratégie d’audit d’objets blob configurée dans une base de données spécifique.|
|/servers/databases/auditingSettings/write|Modifie la stratégie d’audit d’objets blob pour une base de données spécifique.|
|/servers/databases/auditRecords/read|Récupère les enregistrements d’audit d’objet blob de base de données.|
|/servers/databases/automaticTuning/read|Retourne les paramètres de réglage automatique pour une base de données|
|/servers/databases/automaticTuning/write|Met à jour les paramètres de réglage automatique pour une base de données et retourne les paramètres mis à jour|
|/servers/databases/azureAsyncOperation/read|Obtient l’état d’une opération de base de données.|
|/servers/databases/backupLongTermRetentionPolicies/read|Retourne la liste des stratégies d’archivage de sauvegarde d’une base de données spécifiée.|
|/servers/databases/backupLongTermRetentionPolicies/write|Crée ou met à jour une stratégie d’archivage de sauvegarde de base de données.|
|/servers/databases/connectionPolicies/read|Récupère les détails de la stratégie de connexion configurée d’une base de données spécifique.|
|/servers/databases/connectionPolicies/write|Modifie la stratégie de connexion d’une base de données spécifique.|
|/servers/databases/dataMaskingPolicies/read|Retourne la liste des stratégies de masquage des données de base de données.|
|/servers/databases/dataMaskingPolicies/rules/delete|Supprime une règle de stratégie de masquage de données pour une base de données spécifique|
|/servers/databases/dataMaskingPolicies/rules/read|Récupérer les détails de la règle de stratégie de masquage des données configurée sur une base de données spécifique|
|/servers/databases/dataMaskingPolicies/rules/write|Modifier la règle de stratégie de masquage des données pour une base de données spécifique|
|/servers/databases/dataMaskingPolicies/write|Modifier la stratégie de masquage des données pour une base de données spécifique|
|/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read|Retourne les informations d’étape de requête distribuée de la requête de l’entrepôt de données pour l’ID d’étape sélectionné.|
|/servers/databases/dataWarehouseQueries/read|Retourne les informations de requête de distribution d’entrepôt de données pour l’ID de requête sélectionné.|
|/servers/databases/dataWarehouseUserActivities/read|Récupère les activités d’utilisateur d’une instance SQL Data Warehouse qui inclut les requêtes en cours d’exécution et suspendues|
|/servers/databases/delete|Supprime une base de données existante.|
|/servers/databases/export/action|Exporte Azure SQL Database|
|/servers/databases/extendedAuditingSettings/read|Récupère les détails de la stratégie étendue d’audit d’objets blob configurée dans une base de données spécifique|
|/servers/databases/extendedAuditingSettings/write|Modifie la stratégie étendue d’audit d’objets blob pour une base de données spécifique|
|/servers/databases/extensions/read|Obtient une collection d’extensions pour la base de données.|
|/servers/databases/extensions/write|Modifie l’extension d’une base de données spécifiée|
|/servers/databases/geoBackupPolicies/read|Récupère les stratégies de géosauvegarde pour une base de données spécifiée|
|/servers/databases/geoBackupPolicies/write|Crée ou met à jour une stratégie de géosauvegarde de base de données|
|/servers/databases/importExportOperationResults/read|Obtient les opérations d’importation/exportation en cours|
|/servers/databases/metricDefinitions/read|Renvoie les types de mesures disponibles pour les bases de données|
|/servers/databases/metrics/read|Retourne les métriques pour les bases de données|
|/servers/databases/move/action|Renomme Azure SQL Database|
|/servers/databases/operationResults/read|Obtient l’état d’une opération de base de données.|
|/servers/databases/operations/cancel/action|Annule une opération asynchrone de base de données Azure SQL qui n’est pas encore terminée.|
|/servers/databases/operations/read|Retourne la liste des opérations effectuées sur la base de données|
|/servers/databases/pause/action|Suspend la base de données de l’entrepôt de données Azure SQL|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/servers/databases/providers/Microsoft.Insights/logDefinitions/read|Obtient les journaux disponibles pour les bases de données|
|/servers/databases/providers/Microsoft.Insights/metricDefinitions/read|Renvoie les types de mesures disponibles pour les bases de données|
|/servers/databases/queryStore/queryTexts/read|Retourne la collection de textes de requêtes qui correspondent aux paramètres spécifiés.|
|/servers/databases/queryStore/read|Retourne les valeurs actuelles des paramètres du magasin des requêtes pour la base de données.|
|/servers/databases/queryStore/write|Met à jour les paramètres de magasin de requêtes pour la base de données.|
|/servers/databases/read|Retourner la liste des bases de données ou obtenir les propriétés pour la base de données spécifiée.|
|/servers/databases/replicationLinks/delete|Mettre fin à la relation de réplication volontairement avec une perte de données potentielle|
|/servers/databases/replicationLinks/failover/action|Bascule après synchronisation toutes les modifications de la base de données principale, transformant celle-ci en base de données principale dans la relation de réplication, et la base de données principale distante en base de données secondaire|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|Bascule immédiatement avec perte de données potentielle, transformant cette base de données en base de données principale dans la relation de réplication, et la base de données principale distante en base de données secondaire|
|/servers/databases/replicationLinks/read|Renvoie des informations sur les liens de réplication établis pour une base de données spécifique|
|/servers/databases/replicationLinks/unlink/action|Mettre fin à la relation de réplication volontairement ou après la synchronisation avec le partenaire|
|/servers/databases/replicationLinks/updateReplicationMode/action|Mettre à jour le mode de réplication pour le lien vers un mode synchrone ou asynchrone|
|/servers/databases/restorePoints/action|Créer un nouveau point de restauration|
|/servers/databases/restorePoints/read|Retourne les points de restauration pour la base de données.|
|/servers/databases/resume/action|Reprend la base de données de l’entrepôt de données Azure SQL|
|/servers/databases/schemas/read|Récupérer la liste des schémas d’une base de données|
|/servers/databases/schemas/tables/columns/read|Récupère la liste des colonnes d’une table.|
|/servers/databases/schemas/tables/columns/sensitivityLabels/delete|Supprime l’étiquette de sensibilité d’une colonne donnée|
|/servers/databases/schemas/tables/columns/sensitivityLabels/read|Obtient l’étiquette de sensibilité d’une colonne donnée|
|/servers/databases/schemas/tables/columns/sensitivityLabels/write|Crée ou met à jour l’étiquette de sensibilité d’une colonne donnée|
|/servers/databases/schemas/tables/read|Récupérer la liste des tables d’une base de données|
|/servers/databases/schemas/tables/recommendedIndexes/read|Récupère la liste des recommandations d’index d’une base de données.|
|/servers/databases/schemas/tables/recommendedIndexes/write|Applique une recommandation d’index.|
|/servers/databases/securityAlertPolicies/read|Récupérer les détails de la stratégie de détection des menaces configurée sur une base de données spécifique|
|/servers/databases/securityAlertPolicies/write|Modifier la stratégie de détection des menaces pour une base de données spécifique|
|/servers/databases/securityMetrics/read|Obtient une collection de métriques de sécurité de base de données|
|/servers/databases/sensitivityLabels/read|Affiche les étiquettes de sensibilité d’une base de données spécifiée|
|/servers/databases/serviceTierAdvisors/read|Retourne une suggestion sur la montée ou la descente en puissance d’une base de données en fonction des statistiques d’exécution de requête afin d’améliorer les performances ou de réduire les coûts.|
|/servers/databases/syncGroups/cancelSync/action|Annule la synchronisation du groupe de synchronisation|
|/servers/databases/syncGroups/delete|Supprime un groupe de synchronisation existant.|
|/servers/databases/syncGroups/hubSchemas/read|Retourne la liste des schémas de base de données du hub de synchronisation|
|/servers/databases/syncGroups/logs/read|Retourne la liste des journaux du groupe de synchronisation|
|/servers/databases/syncGroups/read|Retourne la liste des groupes de synchronisation ou obtient les propriétés du groupe de synchronisation spécifié.|
|/servers/databases/syncGroups/refreshHubSchema/action|Actualise le schéma de base de données du hub de synchronisation|
|/servers/databases/syncGroups/refreshHubSchemaOperationResults/read|Récupère le résultat de l’opération d’actualisation du schéma du hub de synchronisation|
|/servers/databases/syncGroups/syncMembers/delete|Supprime un membre de synchronisation existant.|
|/servers/databases/syncGroups/syncMembers/read|Retourne la liste des membres de synchronisation ou obtient les propriétés du membre de synchronisation spécifié.|
|/servers/databases/syncGroups/syncMembers/refreshSchema/action|Actualise le schéma du membre de synchronisation|
|/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read|Récupère le résultat de l’opération d’actualisation du schéma du membre de synchronisation|
|/servers/databases/syncGroups/syncMembers/schemas/read|Retourne la liste des schémas de base de données du membre de synchronisation|
|/servers/databases/syncGroups/syncMembers/write|Crée un membre de synchronisation avec les paramètres spécifiés ou met à jour les propriétés du membre de synchronisation spécifié.|
|/servers/databases/syncGroups/triggerSync/action|Déclenche la synchronisation du groupe de synchronisation|
|/servers/databases/syncGroups/write|Crée un groupe de synchronisation avec les paramètres spécifiés ou met à jour les propriétés du groupe de synchronisation spécifié.|
|/servers/databases/topQueries/queryText/action|Retourne le texte Transact-SQL pour l’ID de requête sélectionné|
|/servers/databases/topQueries/read|Renvoie les statistiques d’exécution agrégées de la requête sélectionnée dans la période choisie|
|/servers/databases/topQueries/statistics/read|Renvoie les statistiques d’exécution agrégées de la requête sélectionnée dans la période choisie|
|/servers/databases/transparentDataEncryption/operationResults/read|Obtient les opérations en cours d’exécution sur le chiffrement transparent des données|
|/servers/databases/transparentDataEncryption/read|Récupère l’état et les détails de la fonctionnalité de sécurité Transparent Data Encryption pour une base de données spécifique.|
|/servers/databases/transparentDataEncryption/write|Modifie l’état de chiffrement transparent des données|
|/servers/databases/upgradeDataWarehouse/action|Met à niveau la base de données de l’entrepôt de données Azure SQL|
|/servers/databases/usages/read|Obtient les informations d’utilisation d’Azure SQL Database|
|/servers/databases/vulnerabilityAssessments/delete|Supprime l’évaluation des vulnérabilités d’une base de données spécifiée|
|/servers/databases/vulnerabilityAssessments/read|Récupère les détails de l’évaluation des vulnérabilités configurée sur une base de données spécifique|
|/servers/databases/vulnerabilityAssessments/rules/baselines/delete|Supprime la ligne de base de la règle d’évaluation des vulnérabilités pour une base de données spécifiée|
|/servers/databases/vulnerabilityAssessments/rules/baselines/read|Obtient la ligne de base de la règle d’évaluation des vulnérabilités pour une base de données spécifiée|
|/servers/databases/vulnerabilityAssessments/rules/baselines/write|Modifie la ligne de base de la règle d’évaluation des vulnérabilités pour une base de données spécifiée|
|/servers/databases/vulnerabilityAssessments/scans/action|Exécuter l’analyse de la base de données d’évaluation de la vulnérabilité.|
|/servers/databases/vulnerabilityAssessments/scans/export/action|Convertit un résultat d’analyse existant en un format lisible. S’il existe déjà, rien ne se passe|
|/servers/databases/vulnerabilityAssessments/scans/read|Retourne la liste des enregistrements d’analyse d’évaluation des vulnérabilités de la base de données ou obtient l’enregistrement d’analyse pour l’ID d’analyse spécifié.|
|/servers/databases/vulnerabilityAssessments/write|Modifie l’évaluation des vulnérabilités pour une base de données|
|/servers/databases/vulnerabilityAssessmentScans/action|Exécuter l’analyse de la base de données d’évaluation de la vulnérabilité.|
|/servers/databases/vulnerabilityAssessmentScans/operationResults/read|Récupère le résultat de l’opération d’analyse d’évaluation des vulnérabilités de la base de données|
|/servers/databases/vulnerabilityAssessmentSettings/read|Récupère les détails de l’évaluation des vulnérabilités configurée sur une base de données spécifique|
|/servers/databases/vulnerabilityAssessmentSettings/write|Modifie l’évaluation des vulnérabilités pour une base de données|
|/servers/databases/write|Crée une base de données avec les paramètres spécifiés ou met à jour les propriétés ou balises de la base de données spécifiée.|
|/servers/delete|Supprime un serveur existant.|
|/servers/disasterRecoveryConfiguration/delete|Supprime une configuration de récupération d’urgence existante pour un serveur spécifié|
|/servers/disasterRecoveryConfiguration/failover/action|Bascule une configuration de récupération d’urgence|
|/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action|Force le basculement d’une configuration de récupération d’urgence|
|/servers/disasterRecoveryConfiguration/read|Obtient une collection de configurations de récupération d’urgence qui incluent ce serveur|
|/servers/disasterRecoveryConfiguration/write|Modifie la configuration de récupération d’urgence du serveur|
|/servers/elasticPoolEstimates/read|Renvoie la liste des estimations du pool élastique déjà créée pour ce serveur|
|/servers/elasticPoolEstimates/write|Crée la nouvelle estimation du pool élastique pour la liste des bases de données fournie|
|/servers/elasticPools/advisors/read|Retourne la liste des conseillers disponibles pour le pool élastique.|
|/servers/elasticPools/advisors/recommendedActions/read|Retourne la liste des actions recommandées du conseiller spécifié pour le pool élastique.|
|/servers/elasticPools/advisors/recommendedActions/write|Appliquer l’action recommandée sur le pool élastique|
|/servers/elasticPools/advisors/write|Met à jour l’état d’exécution automatique d’un conseiller au niveau du pool élastique.|
|/servers/elasticPools/databases/read|Obtient une liste de bases de données pour un pool élastique|
|/servers/elasticPools/delete|Supprime un pool élastique existant|
|/Servers/elasticPools/elasticPoolActivity/Read|Récupérer les activités et les détails sur un pool de bases de données élastique donné|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Récupère les activités et les détails d’une base de données spécifique faisant partie d’un pool de bases de données élastique.|
|/servers/elasticPools/metricDefinitions/read|Retourne les types de mesure disponibles pour les pools de bases de données élastiques.|
|/servers/elasticPools/metrics/read|Retourne des métriques pour des pools de bases de données élastiques|
|/servers/elasticPools/operations/cancel/action|Annule une opération asynchrone de pool élastique SQL qui n’est pas encore terminée.|
|/servers/elasticPools/operations/read|Retourne la liste des opérations effectuées sur le pool élastique|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read|Retourne les types de mesure disponibles pour les pools de bases de données élastiques.|
|/servers/elasticPools/read|Récupère les détails d’un pool élastique sur un serveur spécifié|
|/servers/elasticPools/skus/read|Obtient une collection de références (SKU) disponibles pour ce pool élastique|
|/servers/elasticPools/write|Crée ou modifie les propriétés d’un pool élastique existant|
|/servers/encryptionProtector/read|Retourne la liste des protecteurs de chiffrement de serveur ou obtient les propriétés pour le protecteur du chiffrement du serveur spécifié.|
|/servers/encryptionProtector/write|Met à jour les propriétés pour le protecteur du chiffrement du serveur spécifié.|
|/servers/extendedAuditingSettings/read|Récupère les détails de la stratégie étendue d’audit des objets blob de serveur configurée sur un serveur spécifié|
|/servers/extendedAuditingSettings/write|Modifie l’audit étendu des objets blob de serveur pour un serveur spécifié|
|/servers/failoverGroups/delete|Supprime un groupe de basculement existant.|
|/servers/failoverGroups/failover/action|Exécute un basculement planifié dans un groupe de basculement existant.|
|/servers/failoverGroups/forceFailoverAllowDataLoss/action|Exécute un basculement forcé dans un groupe de basculement existant.|
|/servers/failoverGroups/read|Retourne la liste des groupes de basculement ou obtient les propriétés du groupe de basculement spécifié.|
|/servers/failoverGroups/write|Crée un groupe de basculement avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le groupe de basculement spécifié.|
|/servers/firewallRules/delete|Supprime une règle de pare-feu de serveur existante.|
|/servers/firewallRules/read|Retourne la liste des règles de pare-feu de serveur ou obtient les propriétés pour la règle de pare-feu du serveur spécifié.|
|/servers/firewallRules/write|Crée une règle de pare-feu de serveur avec les paramètres spécifiés, met à jour les propriétés de la règle spécifiée, ou remplace toutes les règles existantes par des règles de pare-feu de serveur plus récentes.|
|/servers/import/action|Créer une nouvelle base de données sur le serveur et de déployer le schéma et les données à partir d’un package DacPac|
|/servers/importExportOperationResults/read|Obtient les opérations d’importation/exportation en cours|
|/servers/keys/delete|Supprime une clé de serveur existante.|
|/servers/keys/read|Retourne la liste des clés de serveur ou obtient les propriétés de la clé de serveur spécifiée.|
|/servers/keys/write|Crée une clé avec les paramètres spécifiés ou met à jour les propriétés ou balises pour la clé de serveur spécifiée.|
|/servers/operationResults/read|Obtient les opérations du serveur en cours|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Retourne les types de métriques disponibles pour les serveurs|
|/servers/read|Retourner la liste des serveurs ou obtenir les propriétés pour le serveur spécifié.|
|/servers/recommendedElasticPools/databases/read|Récupère les mesures pour les pools de bases de données élastiques recommandés pour un serveur donné.|
|/servers/recommendedElasticPools/read|Récupère une recommandation pour les pools de bases de données élastiques afin de réduire les coûts ou d’améliorer les performances en fonction de l’utilisation des ressources d’historique.|
|/servers/recoverableDatabases/read|Cette opération est utilisée pour la récupération d’urgence de base de données active pour restaurer la base de données au point de sauvegarde correct connu. Elle retourne des informations sur la dernière sauvegarde correcte, mais ne restaure pas réellement la base de données.|
|/servers/restorableDroppedDatabases/read|Récupère la liste des bases de données supprimées d’un serveur donné, qui figurent toujours dans la stratégie de rétention.|
|/servers/securityAlertPolicies/operationResults/read|Récupère les résultats de l’opération d’écriture de la stratégie de détection des menaces de serveur|
|/servers/securityAlertPolicies/read|Récupère les détails de la stratégie de détection des menaces configurée sur un serveur donné.|
|/servers/securityAlertPolicies/write|Modifie la stratégie de détection des menaces de serveur pour un serveur donné|
|/servers/serviceObjectives/read|Récupère la liste des objectifs de niveau de service (également appelés niveaux de performance) disponibles sur un serveur donné.|
|/servers/syncAgents/delete|Supprime un agent de synchronisation existant.|
|/servers/syncAgents/generateKey/action|Génère la clé d’inscription de l’agent de synchronisation|
|/servers/syncAgents/linkedDatabases/read|Retourne la liste des bases de données liées à l’agent de synchronisation|
|/servers/syncAgents/read|Retourne la liste des agents de synchronisation ou obtient les propriétés de l’agent de synchronisation spécifié.|
|/servers/syncAgents/write|Crée un agent de synchronisation avec les paramètres spécifiés ou met à jour les propriétés de l’agent de synchronisation spécifié.|
|/servers/usages/read|Renvoyer le quota DTU du serveur et la consommation DTU actuelle par toutes les bases de données au sein du serveur|
|/servers/virtualNetworkRules/delete|Supprime une règle de réseau virtuel existante|
|/servers/virtualNetworkRules/read|Retourne la liste des règles de réseau virtuel ou obtient les propriétés de la règle de réseau virtuel spécifiée.|
|/servers/virtualNetworkRules/write|Crée une règle de réseau virtuel avec les paramètres spécifiés ou met à jour les propriétés ou balises de la règle de réseau virtuel spécifiée.|
|/servers/write|Crée un serveur avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le serveur spécifié.|
|/unregister/action|Désinscrit l’abonnement pour le fournisseur de ressources Microsoft SQL Database et permet la création de bases de données Microsoft SQL.|
|/virtualClusters/read|Retourne la liste des clusters virtuels ou obtient les propriétés du serveur virtuel spécifié.|
|/virtualClusters/write|Met à jour des balises de cluster virtuel.|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Opération | Description |
|---|---|
|/checknameavailability/read|Vérifie que le nom du valide est valide et qu’il n’est pas déjà utilisé.|
|/locations/deleteVirtualNetworkOrSubnets/action|Informe Microsoft.Storage que le sous-réseau ou le réseau virtuel est en cours de suppression.|
|/operations/read|Interroge l’état d’une opération asynchrone.|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources de stockage et permet la création de comptes de stockage.|
|/skus/read|Répertorie les références prises en charge par Microsoft.Storage.|
|/storageAccounts/blobServices/containers/clearLegalHold/action|Efface la conservation légale du conteneur d’objets blob|
|/storageAccounts/blobServices/containers/delete|Retourne le résultat de la suppression d’un conteneur|
|/storageAccounts/blobServices/containers/immutabilityPolicies/delete|Supprime la stratégie d’immuabilité du conteneur d’objets blob|
|/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action|Étend la stratégie d’immuabilité du conteneur d’objets blob|
|/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action|Verrouille la stratégie d’immuabilité du conteneur d’objets blob|
|/storageAccounts/blobServices/containers/immutabilityPolicies/read|Obtient la stratégie d’immuabilité du conteneur d’objets blob|
|/storageAccounts/blobServices/containers/immutabilityPolicies/write|Place la stratégie d’immuabilité du conteneur d’objets blob|
|/storageAccounts/blobServices/containers/read|Retourne un conteneur ou une liste de conteneurs|
|/storageAccounts/blobServices/containers/setLegalHold/action|Définit la conservation légale du conteneur d’objets blob|
|/storageAccounts/blobServices/containers/write|Retourne le résultat du placement ou du bail du conteneur d’objets blob|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read|Obtient la liste des définitions des métriques de stockage Microsoft.|
|/storageAccounts/blobServices/read|Retourne les propriétés ou statistiques du service blob|
|/storageAccounts/blobServices/write|Retourne le résultat des propriétés du service blob de placement|
|/storageAccounts/delete|Supprime un compte de stockage existant.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read|Obtient la liste des définitions des métriques de stockage Microsoft.|
|/storageAccounts/listAccountSas/action|Retourne le jeton SAS du compte de stockage spécifié.|
|/storageAccounts/listkeys/action|Retourne les clés d’accès au compte de stockage spécifié.|
|/storageAccounts/listServiceSas/action|Retourne le jeton SAS de service pour le compte de stockage spécifié.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read|Obtient la liste des définitions des métriques de stockage Microsoft.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read|Obtient la liste des définitions des métriques de stockage Microsoft.|
|/storageAccounts/queueServices/queues/delete|Retourne le résultat de la suppression d’une file d’attente|
|/storageAccounts/queueServices/queues/read|Retourne une file d’attente ou une liste de files d’attente.|
|/storageAccounts/queueServices/queues/write|Retourne le résultat de l’écriture d’une file d’attente|
|/storageAccounts/queueServices/read|Retourne les propriétés ou les statistiques du service de file d’attente.|
|/storageAccounts/queueServices/write|Retourne le résultat de la définition des propriétés du service de file d’attente|
|/storageAccounts/read|Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié.|
|/storageAccounts/regeneratekey/action|Régénère les clés d’accès au compte de stockage spécifié.|
|/storageAccounts/services/diagnosticSettings/write|Crée/met à jour les paramètres de diagnostic de compte de stockage.|
|/storageAccounts/storageAccounts/queueServices/providers/ Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read|Obtient la liste des définitions des métriques de stockage Microsoft.|
|/storageAccounts/write|Crée un compte de stockage avec les paramètres spécifiés ou met à jour les propriétés ou les balises, ou ajoute un domaine personnalisé pour le compte de stockage spécifié.|
|/usages/read|Retourne la limite et le nombre actuel d’utilisations des ressources dans l’abonnement spécifié.|

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

| Opération | Description |
|---|---|
|/storageSyncServices/delete|Supprime des services de synchronisation de stockage|
|/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour les services de synchronisation de stockage|
|/storageSyncServices/read|Lit des services de synchronisation de stockage|
|/storageSyncServices/registeredServers/delete|Supprime un serveur inscrit|
|/storageSyncServices/registeredServers/read|Lit un serveur inscrit|
|/storageSyncServices/registeredServers/write|Crée ou met à jour un serveur inscrit|
|/storageSyncServices/syncGroups/cloudEndpoints/delete|Supprime des points de terminaison cloud|
|/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read|API d’emplacement pour les appels de sauvegarde asynchrone|
|/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action|Appelle cette action après une sauvegarde|
|/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action|Appelle cette action après une restauration|
|/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action|Appelle cette action avant une sauvegarde|
|/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action|Appelle cette action avant une restauration|
|/storageSyncServices/syncGroups/cloudEndpoints/read|Lit des points de terminaison cloud|
|/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action|Restaure la pulsation|
|/storageSyncServices/syncGroups/cloudEndpoints/write|Crée ou met à jour des points de terminaison cloud|
|/storageSyncServices/syncGroups/delete|Supprime des groupes de synchronisation|
|/storageSyncServices/syncGroups/read|Lit des groupes de synchronisation|
|/storageSyncServices/syncGroups/serverEndpoints/delete|Supprime des points de terminaison de serveur|
|/storageSyncServices/syncGroups/serverEndpoints/read|Lit des points de terminaison de serveur|
|/storageSyncServices/syncGroups/serverEndpoints/recallAction/action|Appelle cette action pour rappeler des fichiers sur un serveur|
|/storageSyncServices/syncGroups/serverEndpoints/write|Crée ou met à jour des points de terminaison de serveur|
|/storageSyncServices/syncGroups/write|Crée ou met à jour des groupes de synchronisation|
|/storageSyncServices/write|Crée ou met à jour des services de synchronisation de stockage|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Opération | Description |
|---|---|
|/managers/accessControlRecords/delete|Supprime les enregistrements de contrôle d’accès.|
|/managers/accessControlRecords/read|Répertorie ou récupère les enregistrements de contrôle d’accès.|
|/managers/accessControlRecords/write|Crée ou met à jour les enregistrements de contrôle d’accès.|
|/managers/alerts/read|Répertorie ou récupère les alertes.|
|/managers/bandwidthSettings/delete|Supprime des paramètres de bande passante existants (série 8000 uniquement).|
|/managers/bandwidthSettings/read|Répertorie les paramètres de bande passante (série 8000 uniquement).|
|/managers/bandwidthSettings/write|Crée ou met à jour les paramètres de bande passante (série 8000 uniquement).|
|/Managers/certificates/write|L’opération de mise à jour de certificat de ressource met à jour le certificat d’informations d’identification du coffre/de la ressource.|
|/managers/clearAlerts/action|Efface toutes les alertes associées au gestionnaire d’appareils.|
|/managers/cloudApplianceConfigurations/read|Répertorie les configurations prises en charge d’appliance cloud.|
|/managers/configureDevice/action|Configure un appareil.|
|/managers/delete|Supprime les gestionnaires d’appareils.|
|/Managers/delete|L’opération de suppression de coffre supprime la ressource Azure de type « coffre » spécifiée.|
|/managers/devices/alertSettings/read|Répertorie ou récupère les paramètres d’alerte.|
|/managers/devices/alertSettings/write|Crée ou met à jour les paramètres d’alerte.|
|/managers/devices/backupPolicies/backup/action|Exécute une sauvegarde manuelle pour créer une sauvegarde à la demande de tous les volumes protégés par la stratégie.|
|/managers/devices/backupPolicies/delete|Supprime des stratégies de sauvegarde existantes (série 8000 uniquement).|
|/managers/devices/backupPolicies/read|Répertorie les stratégies de sauvegarde (série 8000 uniquement).|
|/managers/devices/backupPolicies/schedules/delete|Supprime une planification existante.|
|/managers/devices/backupPolicies/schedules/read|Répertorie les planifications.|
|/managers/devices/backupPolicies/schedules/write|Crée une planification ou met à jour des planifications existantes.|
|/managers/devices/backupPolicies/write|Crée ou met à jour des stratégies de sauvegarde (série 8000 uniquement).|
|/managers/devices/backups/delete|Supprime le jeu de sauvegarde.|
|/managers/devices/backups/elements/clone/action|Clone un partage ou un volume à l’aide d’un élément de sauvegarde.|
|/managers/devices/backups/read|Répertorie ou récupère le jeu de sauvegarde.|
|/managers/devices/backups/restore/action|Restaure tous les volumes d’un jeu de sauvegarde.|
|/managers/devices/backupScheduleGroups/delete|Supprime les groupes de planification de sauvegarde.|
|/managers/devices/backupScheduleGroups/read|Répertorie ou récupère les groupes de planification de sauvegarde.|
|/managers/devices/backupScheduleGroups/write|Crée ou met à jour les groupes de planification de sauvegarde.|
|/managers/devices/chapSettings/delete|Supprime les paramètres CHAP.|
|/managers/devices/chapSettings/read|Répertorie ou récupère les paramètres CHAP.|
|/managers/devices/chapSettings/write|Crée ou met à jour les paramètres CHAP.|
|/managers/devices/deactivate/action|Désactive un appareil.|
|/managers/devices/delete|Supprime les appareils.|
|/managers/devices/download/action|Télécharge les mises à jour d’un appareil.|
|/managers/devices/failover/action|Basculement de l’appareil.|
|/managers/devices/fileservers/backup/action|Exécute la sauvegarde d’un serveur de fichiers.|
|/managers/devices/fileservers/delete|Supprime les serveurs de fichiers.|
|/managers/devices/fileservers/metrics/read|Répertorie ou récupère les mesures.|
|/managers/devices/fileservers/metricsDefinitions/read|Répertorie ou récupère les définitions de mesures.|
|/managers/devices/fileservers/read|Répertorie ou récupère les serveurs de fichiers.|
|/managers/devices/fileservers/shares/delete|Supprime les partages.|
|/managers/devices/fileservers/shares/metrics/read|Répertorie ou récupère les mesures.|
|/managers/devices/fileservers/shares/metricsDefinitions/read|Répertorie ou récupère les définitions de mesures.|
|/managers/devices/fileservers/shares/read|Répertorie ou récupère les partages.|
|/managers/devices/fileservers/shares/write|Crée ou met à jour les partages.|
|/managers/devices/fileservers/write|Crée ou met à jour les serveurs de fichiers.|
|/managers/devices/hardwareComponentGroups/changeControllerPowerState/action|Modifie l’état d’alimentation du contrôleur des groupes de composants matériels.|
|/managers/devices/hardwareComponentGroups/read|Répertorie les groupes de composants matériels.|
|/managers/devices/install/action|Installe des mises à jour sur un appareil.|
|/managers/devices/installUpdates/action|Installe des mises à jour sur les appareils.|
|/managers/devices/iscsiservers/backup/action|Exécute la sauvegarde d’un serveur iSCSI.|
|/managers/devices/iscsiservers/delete|Supprime les serveurs iSCSI.|
|/managers/devices/iscsiservers/disks/delete|Supprime les disques.|
|/managers/devices/iscsiservers/disks/metrics/read|Répertorie ou récupère les mesures.|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|Répertorie ou récupère les définitions de mesures.|
|/managers/devices/iscsiservers/disks/read|Répertorie ou récupère les disques.|
|/managers/devices/iscsiservers/disks/write|Crée ou met à jour les disques.|
|/managers/devices/iscsiservers/metrics/read|Répertorie ou récupère les mesures.|
|/managers/devices/iscsiservers/metricsDefinitions/read|Répertorie ou récupère les définitions de mesures.|
|/managers/devices/iscsiservers/read|Répertorie ou récupère les serveurs iSCSI.|
|/managers/devices/iscsiservers/write|Crée ou met à jour les serveurs iSCSI.|
|/managers/devices/jobs/cancel/action|Annule un travail en cours d’exécution.|
|/managers/devices/jobs/read|Répertorie ou récupère les travaux.|
|/managers/devices/listFailoverSets/action|Répertorie les ensembles de basculement d’un appareil existant.|
|/managers/devices/listFailoverTargets/action|Répertorie les cibles de basculement des appareils.|
|/managers/devices/metrics/read|Répertorie ou récupère les mesures.|
|/managers/devices/metricsDefinitions/read|Répertorie ou récupère les définitions de mesures.|
|/managers/devices/migrationSourceConfigurations/confirmMigration/action|Vérifie et valide une migration réussie.|
|/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action|Extrait l’état de confirmation de la migration.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action|Extrait l’état du travail d’estimation de la migration.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action|Extrait l’état de la migration.|
|/managers/devices/migrationSourceConfigurations/import/action|Importe les configurations de source pour la migration.|
|/managers/devices/migrationSourceConfigurations/startMigration/action|Démarre la migration à l’aide des configurations de source.|
|/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action|Démarre un travail pour estimer la durée du processus de migration.|
|/managers/devices/networkSettings/read|Répertorie ou récupère les paramètres réseau.|
|/managers/devices/networkSettings/write|Crée ou met à jour les paramètres réseau.|
|/managers/devices/publicEncryptionKey/action|Répertorie les clés de chiffrement publiques d’un gestionnaire d’appareils.|
|/managers/devices/publishSupportPackage/action|Publie le package de support d’un appareil pour la résolution des problèmes du Support Microsoft.|
|/managers/devices/read|Répertorie ou récupère les appareils.|
|/managers/devices/scanForUpdates/action|Recherche des mises à jour sur un appareil.|
|/managers/devices/securitySettings/read|Répertorie les paramètres de sécurité.|
|/managers/devices/securitySettings/syncRemoteManagementCertificate/action|Synchronise le certificat de gestion à distance d’un appareil.|
|/managers/devices/securitySettings/update/action|Met à jour les paramètres de sécurité.|
|/managers/devices/securitySettings/write|Crée ou met à jour les paramètres de sécurité.|
|/managers/devices/sendTestAlertEmail/action|Envoie un test d’alerte par e-mail aux destinataires de courrier électronique configurés.|
|/managers/devices/timeSettings/read|Répertorie ou récupère les paramètres horaires.|
|/managers/devices/timeSettings/write|Crée ou met à jour les paramètres horaires.|
|/managers/devices/updateSummary/read|Répertorie ou récupère le résumé de la mise à jour.|
|/managers/devices/volumeContainers/delete|Supprime des conteneurs de volumes existants (série 8000 uniquement).|
|/managers/devices/volumeContainers/listEncryptionKeys/action|Répertorie les clés de chiffrement des conteneurs de volumes.|
|/managers/devices/volumeContainers/metrics/read|Répertorie les mesures.|
|/managers/devices/volumeContainers/metricsDefinitions/read|Répertorie les définitions de mesures.|
|/managers/devices/volumeContainers/read|Répertorie les conteneurs de volumes (série 8000 uniquement).|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|Clés de chiffrement de substitution des conteneurs de volumes|
|/managers/devices/volumeContainers/volumes/delete|Supprime un volume existant.|
|/managers/devices/volumeContainers/volumes/metrics/read|Répertorie les mesures.|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|Répertorie les définitions de mesures.|
|/managers/devices/volumeContainers/volumes/read|Répertorie les volumes.|
|/managers/devices/volumeContainers/volumes/write|Crée un volume ou met à jour des volumes existants.|
|/managers/devices/volumeContainers/write|Crée ou met à jour des conteneurs de volumes (série 8000 uniquement).|
|/managers/devices/write|Crée ou met à jour les appareils.|
|/managers/encryptionSettings/read|Répertorie ou récupère les paramètres de chiffrement.|
|/Managers/extendedInformation/delete|L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ».|
|/Managers/extendedInformation/read|L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ».|
|/Managers/extendedInformation/write|L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ».|
|/managers/getActivationKey/action|Récupère la clé d’activation du gestionnaire d’appareils.|
|/managers/getEncryptionKey/action|Récupère la clé de chiffrement du gestionnaire d’appareils.|
|/managers/listActivationKey/action|Récupère la clé d’activation du gestionnaire StorSimple Device Manager.|
|/managers/listPrivateEncryptionKey/action|Récupère la clé de chiffrement publique d’un gestionnaire StorSimple Device Manager.|
|/managers/listPublicEncryptionKey/action|Répertorie les clés de chiffrement publiques d’un gestionnaire StorSimple Device Manager.|
|/managers/metrics/read|Répertorie ou récupère les mesures.|
|/managers/metricsDefinitions/read|Répertorie ou récupère les définitions de mesures.|
|/managers/provisionCloudAppliance/action|Crée une appliance cloud.|
|/managers/read|Répertorie ou récupère les gestionnaires d’appareils.|
|/Managers/read|L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ».|
|/managers/regenarateRegistationCertificate/action|Régénère le certificat d’inscription du gestionnaire d’appareils.|
|/managers/regenerateActivationKey/action|Régénère la clé d’activation du gestionnaire d’appareils.|
|/managers/storageAccountCredentials/delete|Supprime les informations d’identification du compte de stockage.|
|/managers/storageAccountCredentials/listAccessKey/action|Répertorie les clés d’accès des informations d’identification de compte de stockage.|
|/managers/storageAccountCredentials/read|Répertorie ou récupère les informations d’identification du compte de stockage.|
|/managers/storageAccountCredentials/write|Crée ou met à jour les informations d’identification du compte de stockage.|
|/managers/storageDomains/delete|Supprime les domaines de stockage.|
|/managers/storageDomains/read|Répertorie ou récupère les domaines de stockage.|
|/managers/storageDomains/write|Crée ou met à jour les domaines de stockage.|
|/managers/write|Crée ou met à jour les gestionnaires d’appareils.|
|/Managers/write|L’opération de création de coffre entraîne la création d’une ressource Azure de type « coffre ».|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Opération | Description |
|---|---|
|/locations/quotas/Read|Lit le quota d’abonnement Stream Analytics|
|/operations/Read|Lit les opérations Stream Analytics|
|/Register/action|Inscrit l’abonnement auprès du fournisseur de ressources Stream Analytics|
|/streamingjobs/Delete|Supprime une tâche Stream Analytics.|
|/streamingjobs/functions/Delete|Supprime la fonction de tâche Stream Analytics|
|/streamingjobs/functions/operationresults/Read|Lit les résultats d’opération pour la fonction de tâche Stream Analytics|
|/streamingjobs/functions/Read|Lit la fonction de tâche Stream Analytics|
|/streamingjobs/functions/RetrieveDefaultDefinition/action|Récupère la définition par défaut d’une fonction de tâche Stream Analytics|
|/streamingjobs/functions/Test/action|Teste la fonction de tâche Stream Analytics|
|/streamingjobs/functions/Write|Écrit la fonction de tâche Stream Analytics|
|/streamingjobs/inputs/Delete|Supprime une entrée de tâche Stream Analytics.|
|/streamingjobs/inputs/operationresults/Read|Lit les résultats d’opération pour l’entrée de la tâche Stream Analytics|
|/streamingjobs/inputs/Read|Lit une entrée de tâche Stream Analytics.|
|/streamingjobs/inputs/Sample/action|Échantillonne une entrée de tâche Stream Analytics|
|/streamingjobs/inputs/Test/action|Teste une entrée de tâche Stream Analytics|
|/streamingjobs/inputs/Write|Écrit une entrée de tâche Stream Analytics.|
|/streamingjobs/metricdefinitions/Read|Lit les définitions des métriques|
|/streamingjobs/operationresults/Read|Lit les résultats d’opération pour la tâche Stream Analytics|
|/streamingjobs/outputs/Delete|Supprime une sortie de tâche Stream Analytics.|
|/streamingjobs/outputs/operationresults/Read|Lit les résultats d’opération pour la sortie de tâche Stream Analytics|
|/streamingjobs/outputs/Read|Lit une sortie de tâche Stream Analytics.|
|/streamingjobs/outputs/Test/action|Teste une sortie de tâche Stream Analytics|
|/streamingjobs/outputs/Write|Écrit une sortie de tâche Stream Analytics.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|Lit le paramètre de diagnostic.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|Écrit un paramètre de diagnostic.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|Récupère les journaux disponibles pour les tâches de diffusion de contenu.|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|Récupère les mesures disponibles pour les tâches de diffusion de contenu.|
|/streamingjobs/Read|Lit une tâche Stream Analytics.|
|/streamingjobs/Start/action|Démarre une tâche Stream Analytics.|
|/streamingjobs/Stop/action|Arrête une tâche Stream Analytics.|
|/streamingjobs/transformations/Delete|Supprime une transformation de tâche Stream Analytics.|
|/streamingjobs/transformations/Read|Lit une transformation de tâche Stream Analytics.|
|/streamingjobs/transformations/Write|Écrit une transformation de tâche Stream Analytics.|
|/streamingjobs/Write|Écrit une tâche Stream Analytics.|

## <a name="microsoftsubscription"></a>Microsoft.Subscription

| Opération | Description |
|---|---|
|/SubscriptionDefinitions/read|Obtient une définition d’abonnement Azure dans un groupe d’administration.|
|/SubscriptionDefinitions/write|Crée une définition d’abonnement Azure|

## <a name="microsoftsupport"></a>Microsoft.Support

| Opération | Description |
|---|---|
|/register/action|S’inscrit auprès du fournisseur de ressources de support.|
|/supportTickets/read|Récupère les détails du ticket de support (notamment l’état, la gravité, les détails du contact et les communications) ou la liste des tickets de support des abonnements.|
|/supportTickets/write|Crée ou met à jour un ticket de support. Vous pouvez créer un ticket de support pour les problèmes techniques ou les problèmes liés à la facturation, aux quotas ou à la gestion des abonnements. Vous pouvez mettre à jour la gravité des problèmes, les détails du contact et les communications pour les tickets de support existants.|

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

| Opération | Description |
|---|---|
|/environments/accesspolicies/delete|Supprime la stratégie d’accès.|
|/environments/accesspolicies/read|Obtient les propriétés d’une stratégie d’accès.|
|/environments/accesspolicies/write|Crée une stratégie d’accès ou met à jour une stratégie d’accès existante pour un environnement.|
|/environments/delete|Supprime l’environnement.|
|/environments/eventsources/delete|Supprime la source de l’événement.|
|/environments/eventsources/eventsources/providers/Microsoft.Insights/ diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour les sources d’événements|
|/environments/eventsources/read|Obtient les propriétés d’une source de l’événement.|
|/environments/eventsources/write|Crée une source d’événement ou met à jour une source d’événement existante pour un environnement.|
|/environments/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/environments/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/environments/providers/Microsoft.Insights/metricDefinitions/read|Obtient les métriques disponibles pour les environnements|
|/environments/read|Obtient les propriétés d’un environnement.|
|/environments/referencedatasets/delete|Supprime le jeu de données de référence.|
|/environments/referencedatasets/read|Obtient les propriétés d’un jeu de données de référence.|
|/environments/referencedatasets/write|Crée un jeu de données de référence ou met à jour un jeu de données de référence existant pour un environnement.|
|/environments/status/read|Obtient l’état de l’environnement, l’état de ses opérations associées comme l’entrée.|
|/environments/write|Crée un environnement ou met à jour un environnement existant.|
|/register/action|Inscrit l’abonnement pour le fournisseur de ressources Time Series Insights et permet la création d’environnements Time Series Insights.|

## <a name="microsoftweb"></a>microsoft.web

| Opération | Description |
|---|---|
|/apimanagementaccounts/apiacls/read|Obtient des Apiacl de comptes de gestion des API.|
|/apimanagementaccounts/apis/apiacls/delete|Supprime des Apiacl de comptes de gestion des API.|
|/apimanagementaccounts/apis/apiacls/read|Obtient des Apiacl d’API de comptes de gestion des API.|
|/apimanagementaccounts/apis/apiacls/write|Met à jour des Apiacl d’API de comptes de gestion des API.|
|/apimanagementaccounts/apis/connectionacls/read|Obtient des Connectionacl d’API de comptes de gestion des API.|
|/apimanagementaccounts/apis/connections/confirmconsentcode/action|Vérifie les connexions d’API des comptes de gestion des API de code de confirmation.|
|/apimanagementaccounts/apis/connections/connectionacls/delete|Supprime des Connectionacl de connexions d’API de comptes de gestion des API.|
|/apimanagementaccounts/apis/connections/connectionacls/read|Obtient des Connectionacl de connexions d’API de comptes de gestion des API.|
|/apimanagementaccounts/apis/connections/connectionacls/write|Met à jour des Connectionacl de connexions d’API de comptes de gestion des API.|
|/apimanagementaccounts/apis/connections/delete|Supprime des connexions d’API de comptes de gestion des API.|
|/apimanagementaccounts/apis/connections/getconsentlinks/action|Obtient des liens de confirmation pour les connexions d’API des comptes de gestion des API.|
|/apimanagementaccounts/apis/connections/listconnectionkeys/action|Affiche les connexions d’API des comptes de gestion des API des clés de connexion.|
|/apimanagementaccounts/apis/connections/listsecrets/action|Affiche les connexions d’API des comptes de gestion des API des secrets.|
|/apimanagementaccounts/apis/connections/read|Obtient des connexions d’API de comptes de gestion des API.|
|/apimanagementaccounts/apis/connections/write|Met à jour des connexions d’API de comptes de gestion des API.|
|/apimanagementaccounts/apis/delete|Supprime des API de comptes de gestion des API.|
|/apimanagementaccounts/apis/localizeddefinitions/delete|Supprime des définitions localisées d’API de comptes de gestion des API.|
|/apimanagementaccounts/apis/localizeddefinitions/read|Obtient des définitions localisées d’API de comptes de gestion des API.|
|/apimanagementaccounts/apis/localizeddefinitions/write|Met à jour des définitions localisées d’API de comptes de gestion des API.|
|/apimanagementaccounts/apis/read|Obtient des API de comptes de gestion des API.|
|/apimanagementaccounts/apis/write|Met à jour des API de comptes de gestion des API.|
|/apimanagementaccounts/connectionacls/read|Obtient des Connectionacl de comptes de gestion des API.|
|/availablestacks/read|Récupère les piles disponibles.|
|/billingmeters/read|Obtient la liste des compteurs de facturation.|
|/certificates/Delete|Supprime un certificat existant.|
|/certificates/Read|Récupère la liste des certificats.|
|/certificates/Write|Ajoute un nouveau certificat ou en met un à jour.|
|/checknameavailability/read|Vérifie si le nom de la ressource est disponible.|
|/classicmobileservices/read|Récupère Classic Mobile Services.|
|/connectionGateways/Delete|Supprime une passerelle de connexion.|
|/connectionGateways/Join/Action|Joint une passerelle de connexion.|
|/connectiongateways/liststatus/action|Affiche les passerelles de connexion d’état.|
|/connectionGateways/ListStatus/Action|Affiche l’état d’une passerelle de connexion.|
|/connectionGateways/Move/Action|Déplace une passerelle de connexion.|
|/connectionGateways/Read|Récupère la liste des passerelles de connexions.|
|/connectionGateways/Write|Crée ou met à jour une passerelle de connexion.|
|/connections/confirmconsentcode/action|Vérifie le code de consentement des connexions.|
|/connections/Delete|Supprime une connexion.|
|/connections/Join/Action|Joint une connexion.|
|/connections/listconsentlinks/action|Répertorie les liens de consentement pour les connexions.|
|/connections/Move/Action|Déplace une connexion.|
|/connections/Read|Récupère la liste des connexions.|
|/connections/Write|Crée ou met à jour une connexion.|
|/customApis/Delete|Supprime une API personnalisée.|
|/customApis/extractApiDefinitionFromWsdl/Action|Extrait une définition d’API de WSDL.|
|/customApis/Join/Action|Joint une API personnalisée.|
|/customApis/listWsdlInterfaces/Action|Affiche les interfaces WSDL pour une API personnalisée.|
|/customApis/Move/Action|Déplace une API personnalisée.|
|/customApis/Read|Obtient la liste des API personnalisées.|
|/customApis/Write|Crée ou met à jour une API personnalisée.|
|/deploymentlocations/read|Récupère les emplacements de déploiement.|
|/geoRegions/Read|Récupère la liste des zones géographiques.|
|/hostingenvironments/capacities/read|Récupère les fonctionnalités des environnements d’hébergement.|
|/hostingEnvironments/Delete|Supprime un environnement App Service.|
|/hostingenvironments/diagnostics/read|Récupère les diagnostics des environnements d’hébergement.|
|/hostingenvironments/inboundnetworkdependenciesendpoints/read|Obtient les points de terminaison réseau de toutes les dépendances entrantes.|
|/hostingenvironments/metricdefinitions/read|Récupère les définitions métriques des environnements d’hébergement.|
|/hostingenvironments/multirolepools/metricdefinitions/read|Récupère les définitions métriques des pools multirôles des environnements d’hébergement.|
|/hostingenvironments/multirolepools/metrics/read|Récupère les mesures des pools multirôles des environnements d’hébergement.|
|/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/ metricDefinitions/Read|Obtient les métriques disponibles pour le multirôle d’environnement App Service|
|/hostingEnvironments/multiRolePools/Read|Récupère les propriétés d’un pool frontal dans un environnement App Service.|
|/hostingenvironments/multirolepools/skus/read|Récupère les références des pools multirôles des environnements d’hébergement.|
|/hostingenvironments/multirolepools/usages/read|Récupère les utilisations des pools multirôles des environnements d’hébergement.|
|/hostingEnvironments/multiRolePools/Write|Crée un pool frontal dans un environnement App Service ou en met un à jour.|
|/hostingenvironments/operations/read|Récupère les opérations des environnements d’hébergement.|
|/hostingenvironments/outboundnetworkdependenciesendpoints/read|Obtient les points de terminaison réseau de toutes les dépendances sortantes.|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/hostingEnvironments/Read|Récupère les propriétés d’un environnement App Service.|
|/hostingEnvironments/reboot/Action|Redémarre tous les ordinateurs dans un environnement App Service.|
|/hostingenvironments/resume/action|Reprend les environnements d’hébergement.|
|/hostingenvironments/serverfarms/read|Récupère les plans App Service des environnements d’hébergement.|
|/hostingenvironments/sites/read|Récupère les applications web des environnements d’hébergement.|
|/hostingenvironments/suspend/action|Interrompt les environnements d’hébergement.|
|/hostingenvironments/usages/read|Récupère les utilisations des environnements d’hébergement.|
|/hostingenvironments/workerpools/metricdefinitions/read|Récupère les définitions métriques des pools de workers des environnements d’hébergement.|
|/hostingenvironments/workerpools/metrics/read|Récupère les mesures des pools de workers des environnements d’hébergement.|
|/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read|Obtient les métriques disponibles pour le pool de workers d’environnement App Service|
|/hostingEnvironments/workerPools/Read|Récupère les propriétés d’un pool de workers dans un environnement App Service.|
|/hostingenvironments/workerpools/skus/read|Récupère les références des pools de workers des environnements d’hébergement.|
|/hostingenvironments/workerpools/usages/read|Récupère les utilisations des pools de workers des environnements d’hébergement.|
|/hostingEnvironments/workerPools/Write|Crée un pool de workers dans un environnement App Service ou en met un à jour.|
|/hostingEnvironments/Write|Crée un environnement App Service ou en met un à jour.|
|/ishostingenvironmentnameavailable/read|Récupère des éléments si le nom de l’environnement d’hébergement est disponible.|
|/ishostnameavailable/read|Vérifie si le nom d’hôte est disponible.|
|/isusernameavailable/read|Vérifie si le nom d’utilisateur est disponible.|
|/listSitesAssignedToHostName/Read|Récupère les noms de sites affectés à un nom d’hôte.|
|/locations/apioperations/read|Récupère les opérations API d’emplacements.|
|/locations/connectiongatewayinstallations/read|Récupère les installations de la passerelle de connexion des emplacements.|
|/locations/extractapidefinitionfromwsdl/action|Extrait la définition d’API de WSDL pour les emplacements.|
|/locations/listwsdlinterfaces/action|Affiche les interfaces WSDL pour les emplacements.|
|/locations/managedapis/apioperations/read|Obtient les opérations API managée d’emplacements.|
|/locations/managedapis/Join/Action|Joint une API managée.|
|/locations/managedapis/read|Récupère les API managées des emplacements.|
|/operations/read|Récupère les opérations.|
|/publishingusers/read|Récupère la publication des utilisateurs.|
|/publishingusers/write|Met à jour la publication des utilisateurs.|
|/recommendations/Read|Récupère la liste des recommandations pour les abonnements.|
|/register/action|Inscrit le fournisseur de ressources Microsoft.Web pour l’abonnement.|
|/resourcehealthmetadata/read|Obtient les métadonnées de Resource Health.|
|/serverfarms/capabilities/read|Récupère les fonctionnalités des plans App Service.|
|/serverfarms/Delete|Supprimer un plan App Service existant|
|/serverfarms/firstpartyapps/settings/delete|Supprime les paramètres des applications principales des plans App Service.|
|/serverfarms/firstpartyapps/settings/read|Récupère les paramètres des applications principales des plans App Service.|
|/serverfarms/firstpartyapps/settings/write|Met à jour les paramètres des applications principales des plans App Service.|
|/serverfarms/hybridconnectionnamespaces/relays/delete|Supprime les relais d’espace de noms de connexion hybride de plans App Service.|
|/serverfarms/hybridconnectionnamespaces/relays/read|Obtient les relais d’espace de noms de connexion hybride de plans App Service.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|Récupère les applications web des relais d’espace de noms de connexion hybride des plans App Service.|
|/serverfarms/hybridconnectionplanlimits/read|Récupère les limites de plan de connexion hybride des plans App Service.|
|/serverfarms/hybridconnectionrelays/read|Récupère les relais de connexion hybride des plans App Service.|
|/serverfarms/metricdefinitions/read|Récupère les définitions métriques des plans App Service.|
|/serverfarms/metrics/read|Récupère les mesures des plans App Service.|
|/serverfarms/operationresults/read|Récupère les résultats de l’opération des plans App Service.|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read|Obtient les métriques disponibles pour un plan App Service|
|/serverfarms/Read|Récupère les propriétés d’un plan App Service.|
|/serverfarms/restartSites/Action|Redémarre toutes les applications web d’un plan App Service.|
|/serverfarms/sites/read|Récupère les applications web des plans App Service.|
|/serverfarms/skus/read|Récupère les références des plans App Service.|
|/serverfarms/usages/read|Récupère les usages des plans App Service.|
|/serverfarms/virtualnetworkconnections/gateways/write|Met à jour les passerelles de connexions de réseau virtuel des plans App Service.|
|/serverfarms/virtualnetworkconnections/read|Récupère les connexions de réseau virtuel des plans App Service.|
|/serverfarms/virtualnetworkconnections/routes/delete|Supprime les itinéraires des connexions de réseau virtuel des plans App Service.|
|/serverfarms/virtualnetworkconnections/routes/read|Récupère les itinéraires des connexions de réseau virtuel des plans App Service.|
|/serverfarms/virtualnetworkconnections/routes/write|Met à jour les itinéraires des connexions de réseau virtuel des plans App Service.|
|/serverfarms/workers/reboot/action|Redémarre les Workers des plans App Service.|
|/serverfarms/Write|Crée un plan App Service ou en met un à jour.|
|/sites/analyzecustomhostname/read|Analyse le nom d’hôte personnalisé.|
|/sites/applySlotConfig/Action|Applique la configuration d’emplacement d’application web de l’emplacement cible à l’application web actuelle.|
|/sites/backup/Action|Crée une sauvegarde d’application web.|
|/sites/backup/read|Récupère la sauvegarde Web Apps.|
|/sites/backup/write|Met à jour la sauvegarde Web Apps.|
|/sites/backups/delete|Supprime les sauvegardes Web Apps.|
|/sites/backups/list/action|Répertorie les sauvegardes Web Apps.|
|/sites/backups/Read|Récupère les propriétés de sauvegarde d’une application web.|
|/sites/backups/restore/action|Restaure les sauvegardes Web Apps.|
|/sites/config/delete|Supprime la configuration de Web Apps.|
|/sites/config/list/Action|Répertorie les paramètres sensibles de sécurité d’application web, tels que les informations d’identification de publication, les paramètres d’application et les chaînes de connexion.|
|/sites/config/Read|Récupère les paramètres de configuration des applications web.|
|/sites/config/Write|Met à jour les paramètres de configuration d’application web.|
|/sites/continuouswebjobs/delete|Supprime des tâches web continues Web Apps.|
|/sites/continuouswebjobs/read|Récupère des tâches web continues Web Apps.|
|/sites/continuouswebjobs/start/action|Démarre des tâches web continues Web Apps.|
|/sites/continuouswebjobs/stop/action|Arrête des tâches web continues Web Apps.|
|/sites/Delete|Supprimer une application web existante|
|/sites/deployments/delete|Supprime les déploiements de Web Apps.|
|/sites/deployments/log/read|Récupère le journal des déploiements de Web Apps.|
|/sites/deployments/read|Récupère les déploiements de Web Apps.|
|/sites/deployments/write|Met à jour les déploiements de Web Apps.|
|/sites/diagnostics/analyses/execute/Action|Exécute l’analyse de diagnostics Web Apps.|
|/sites/diagnostics/analyses/read|Obtient l’analyse des diagnostics Web Apps.|
|/sites/diagnostics/aspnetcore/read|Obtient des diagnostics Web Apps pour une application ASP.NET Core.|
|/sites/diagnostics/autoheal/read|Obtient l’Autoheal des diagnostics Web Apps.|
|/sites/diagnostics/deployment/read|Obtient le déploiement des diagnostics Web Apps.|
|/sites/diagnostics/deployments/read|Obtient des déploiements des diagnostics Web Apps.|
|/sites/diagnostics/detectors/execute/Action|Exécute le détecteur de diagnostics Web Apps.|
|/sites/diagnostics/detectors/read|Obtient le détecteur des diagnostics Web Apps.|
|/sites/diagnostics/failedrequestsperuri/read|Obtient les échecs de requêtes de diagnostics Web Apps par Uri.|
|/sites/diagnostics/frebanalysis/read|Récupère l’analyse FREB dans les diagnostics de Web Apps.|
|/sites/diagnostics/loganalyzer/read|Obtient l’analyseur de journal des diagnostics Web Apps.|
|/sites/diagnostics/read|Obtient les catégories de diagnostics Web Apps.|
|/sites/diagnostics/runtimeavailability/read|Récupère la disponibilité d’exécution des diagnostics Web Apps.|
|/sites/diagnostics/servicehealth/read|Récupère l’état du service dans les diagnostics de Web Apps.|
|/sites/diagnostics/sitecpuanalysis/read|Obtient l’analyse d’UC du site des diagnostics Web Apps.|
|/sites/diagnostics/sitecrashes/read|Obtient les pannes du site des diagnostics Web Apps.|
|/sites/diagnostics/sitelatency/read|Obtient la latence du site des diagnostics Web Apps.|
|/sites/diagnostics/sitememoryanalysis/read|Obtient l’analyse de mémoire du site des diagnostics Web Apps.|
|/sites/diagnostics/siterestartsettingupdate/read|Obtient la mise à jour du paramètre de redémarrage du site des diagnostics Web Apps.|
|/sites/diagnostics/siterestartuserinitiated/read|Obtient le redémarrage du site des diagnostics Web Apps que l’utilisateur a déclenché.|
|/sites/diagnostics/siteswap/read|Obtient l’échange de sites des diagnostics Web Apps.|
|/sites/diagnostics/threadcount/read|Obtient le nombre de threads des diagnostics Web Apps.|
|/sites/diagnostics/workeravailability/read|Récupère la disponibilité Worker des diagnostics Web Apps.|
|/sites/diagnostics/workerprocessrecycle/read|Récupère le recyclage du processus Worker des diagnostics Web Apps.|
|/sites/domainownershipidentifiers/read|Récupère l’identificateur de propriété de domaine Web Apps.|
|/sites/domainownershipidentifiers/write|Met à jour l’identificateur de propriété de domaine Web Apps.|
|/sites/functions/action|Fonctions Web Apps.|
|/sites/functions/delete|Supprime les fonctions Web Apps.|
|/sites/functions/listsecrets/action|Répertorie les fonctions Web Apps des clés secrètes.|
|/sites/functions/masterkey/read|Obtient la clé principale des fonctions Web Apps.|
|/sites/functions/read|Récupère les fonctions Web Apps.|
|/sites/functions/token/read|Obtient le jeton des fonctions Web Apps.|
|/sites/functions/write|Met à jour les fonctions Web Apps.|
|/sites/hostnamebindings/delete|Supprime les liaisons de nom d’hôte Web Apps.|
|/sites/hostnamebindings/read|Récupère les liaisons de nom d’hôte Web Apps.|
|/sites/hostnamebindings/write|Met à jour les liaisons de nom d’hôte Web Apps.|
|/sites/hybridconnection/delete|Supprime une connexion hybride Web Apps.|
|/sites/hybridconnection/read|Récupère une connexion hybride Web Apps.|
|/sites/hybridconnection/write|Met à jour une connexion hybride Web Apps.|
|/sites/hybridconnectionnamespaces/relays/delete|Supprime les relais d’espaces de noms de connexion hybride Web Apps.|
|/sites/hybridconnectionnamespaces/relays/listkeys/action|Affiche les relais d’espaces de noms de connexion hybride Web Apps de clés.|
|/sites/hybridconnectionnamespaces/relays/read|Obtient les relais d’espaces de noms de connexion hybride Web Apps.|
|/sites/hybridconnectionnamespaces/relays/write|Met à jour les relais d’espaces de noms de connexion hybride Web Apps.|
|/sites/hybridconnectionrelays/read|Récupère les relais de connexions hybrides Web Apps.|
|/sites/instances/deployments/delete|Supprime les déploiements d’instances de Web Apps.|
|/sites/instances/deployments/read|Récupère les déploiements des instances Web Apps.|
|/sites/instances/extensions/log/read|Obtient le journal des extensions des instances Web Apps.|
|/sites/instances/extensions/read|Obtient les extensions des instances Web Apps.|
|/sites/instances/processes/delete|Supprime les processus des instances Web Apps.|
|/sites/instances/processes/read|Récupère les processus des instances Web Apps.|
|/sites/instances/read|Récupère des instances Web Apps.|
|/sites/listsyncfunctiontriggerstatus/action|Répertorie le statut de déclenchement de fonction de synchronisation Web Apps.|
|/sites/metricdefinitions/read|Récupère les définitions métriques Web Apps.|
|/sites/metrics/read|Récupère les mesures Web Apps.|
|/sites/metricsdefinitions/read|Obtient les définitions de métrique de Web Apps.|
|/sites/migratemysql/action|Migre MySql Web Apps.|
|/sites/migratemysql/read|Obtient MySql de migration Web Apps.|
|/sites/networktrace/action|Trace réseau Web Apps.|
|/sites/newpassword/action|Newpassword Web Apps.|
|/sites/operationresults/read|Récupère les résultats de l’opération Web Apps.|
|/sites/operations/read|Obtient les opérations Web Apps.|
|/sites/perfcounters/read|Récupère les compteurs de performances Web Apps.|
|/sites/premieraddons/delete|Supprime les modules complémentaires Premier Web Apps.|
|/sites/premieraddons/read|Récupère les modules complémentaires Premier Web Apps.|
|/sites/premieraddons/write|Met à jour les modules complémentaires Premier Web Apps.|
|/sites/processes/read|Obtient les processus Web Apps.|
|/sites/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/sites/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/sites/providers/Microsoft.Insights/metricDefinitions/Read|Obtient les métriques disponibles pour Web Apps|
|/sites/publiccertificates/delete|Supprime les certificats publics Web Apps.|
|/sites/publiccertificates/read|Obtient les certificats publics Web Apps.|
|/sites/publiccertificates/write|Met à jour les certificats publics Web Apps.|
|/sites/publish/Action|Publie une application web.|
|/sites/publishxml/Action|Récupère le profil de publication xml pour une application web.|
|/sites/publishxml/read|Récupère publication XML Web Apps.|
|/sites/Read|Récupère les propriétés d’une application web.|
|/sites/recommendationhistory/read|Récupère un historique des recommandations Web Apps.|
|/sites/recommendations/disable/action|Désactive les recommandations des applications web.|
|/sites/recommendations/Read|Récupère la liste des recommandations pour l’application web.|
|/sites/recover/action|Récupère Web Apps.|
|/sites/resetSlotConfig/Action|Réinitialise la configuration d’application web.|
|/sites/resourcehealthmetadata/read|Obtient les métadonnées de Resource Health Web Apps.|
|/sites/restart/Action|Redémarre une application web.|
|/sites/restore/read|Récupère la restauration Web Apps.|
|/sites/restore/write|Restaure Web Apps.|
|/sites/siteextensions/delete|Supprime les extensions de site Web Apps.|
|/sites/siteextensions/read|Obtient les extensions de site Web Apps.|
|/sites/siteextensions/write|Met à jour les extensions de site Web Apps.|
|/sites/slots/analyzecustomhostname/read|Récupère le nom d’hôte personnalisé d’analyse des emplacements Web Apps.|
|/sites/slots/applySlotConfig/Action|Applique la configuration d’emplacement d’application web de l’emplacement cible à l’emplacement actuel.|
|/sites/slots/backup/Action|Crée une sauvegarde d’emplacement d’application web.|
|/sites/slots/backup/read|Obtient la sauvegarde des emplacements de Web Apps.|
|/sites/slots/backup/write|Met à jour la sauvegarde des emplacements Web Apps.|
|/sites/slots/backups/delete|Supprime les sauvegardes des emplacements de Web Apps.|
|/sites/slots/backups/list/action|Répertorie les sauvegardes des emplacements Web Apps.|
|/sites/slots/backups/Read|Récupère les propriétés de sauvegarde des emplacements d’une application web.|
|/sites/slots/backups/restore/action|Restaure les sauvegardes des emplacements Web Apps.|
|/sites/slots/config/delete|Supprime la configuration des emplacements Web Apps.|
|/sites/slots/config/list/Action|Répertorie les paramètres sensibles de sécurité de l’emplacement d’application web, tels que les informations d’identification de publication, les paramètres d’application et les chaînes de connexion.|
|/sites/slots/config/Read|Récupère les paramètres de configuration de l’emplacement d’application web.|
|/sites/slots/config/Write|Met à jour les paramètres de configuration de l’emplacement d’application web.|
|/sites/slots/continuouswebjobs/delete|Supprime des tâches web continues dans les emplacements Web Apps.|
|/sites/slots/continuouswebjobs/read|Récupère des tâches web continues dans les emplacements Web Apps.|
|/sites/slots/continuouswebjobs/start/action|Démarre des tâches web continues dans les emplacements Web Apps.|
|/sites/slots/continuouswebjobs/stop/action|Arrête des tâches web continues dans les emplacements Web Apps.|
|/sites/slots/Delete|Supprime un emplacement d’application web existant.|
|/sites/slots/deployments/delete|Supprime les déploiements des emplacements Web Apps.|
|/sites/slots/deployments/log/read|Récupère le journal des déploiements des emplacements Web Apps.|
|/sites/slots/deployments/read|Récupère les déploiements des emplacements Web Apps.|
|/sites/slots/deployments/write|Met à jour les déploiements des emplacements Web Apps.|
|/sites/slots/diagnostics/analyses/execute/Action|Exécuter l’analyse des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/analyses/read|Obtient l’analyse des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/aspnetcore/read|Obtient les diagnostics des emplacements Web Apps pour l’application ASP.NET Core.|
|/sites/slots/diagnostics/autoheal/read|Obtient l’Autoheal des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/deployment/read|Obtient le déploiement des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/deployments/read|Obtient les déploiements des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/detectors/execute/Action|Exécute le détecteur des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/detectors/read|Obtient le détecteur des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/frebanalysis/read|Obtient l’analyse FREB des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/loganalyzer/read|Obtient l’analyseur de journal des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/read|Obtient les diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/runtimeavailability/read|Obtient la disponibilité du runtime des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/servicehealth/read|Obtient l’état du service des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/sitecpuanalysis/read|Obtient l’analyse d’UC du site des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/sitecrashes/read|Obtient les pannes du site des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/sitelatency/read|Obtient la latence du site des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/sitememoryanalysis/read|Obtient l’analyse de mémoire du site des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/siterestartsettingupdate/read|Obtient la mise à jour du paramètre de redémarrage du site des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/siterestartuserinitiated/read|Obtient le redémarrage du site des diagnostics des emplacements Web Apps que l’utilisateur a déclenché.|
|/sites/slots/diagnostics/siteswap/read|Obtient l’échange de sites des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/threadcount/read|Obtient le nombre de threads des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/workeravailability/read|Obtient la disponibilité du processus de travail des diagnostics des emplacements Web Apps.|
|/sites/slots/diagnostics/workerprocessrecycle/read|Obtient le recyclage du processus de travail des diagnostics des emplacements Web Apps.|
|/sites/slots/domainownershipidentifiers/read|Obtient les identificateurs de propriété de domaine des emplacements Web Apps.|
|/sites/slots/hostnamebindings/delete|Supprime les liaisons de nom d’hôte dans les emplacements Web Apps.|
|/sites/slots/hostnamebindings/read|Récupère les liaisons de nom d’hôte des emplacements Web Apps.|
|/sites/slots/hostnamebindings/write|Met à jour les liaisons de nom d’hôte des emplacements Web Apps.|
|/sites/slots/hybridconnection/delete|Supprime une connexion hybride dans les emplacements Web Apps.|
|/sites/slots/hybridconnection/read|Récupère une connexion hybride dans les emplacements Web Apps.|
|/sites/slots/hybridconnection/write|Met à jour une connexion hybride dans les emplacements Web Apps.|
|/sites/slots/hybridconnectionnamespaces/relays/delete|Supprime les relais d’espaces de noms de connexion hybride des emplacements Web Apps.|
|/sites/slots/hybridconnectionnamespaces/relays/write|Met à jour les relais d’espaces de noms de connexion hybride des emplacements Web Apps.|
|/sites/slots/hybridconnectionrelays/read|Obtient les relais de connexion hybride des emplacements Web Apps.|
|/sites/slots/instances/deployments/read|Récupère les déploiements des instances des emplacements Web Apps.|
|/sites/slots/instances/processes/delete|Supprime les processus d’instances des emplacements Web Apps.|
|/sites/slots/instances/processes/read|Récupère les processus des instances des emplacements Web Apps.|
|/sites/slots/instances/read|Récupère les instances des emplacements Web Apps.|
|/sites/slots/metricdefinitions/read|Récupère les définitions métriques des emplacements Web Apps.|
|/sites/slots/metrics/read|Récupère les mesures des emplacements Web Apps.|
|/sites/slots/migratemysql/read|Obtient MySql de migration des emplacements Web Apps.|
|/sites/slots/networktrace/action|Obtient la trace réseau des emplacements Web Apps.|
|/sites/slots/newpassword/action|Emplacements Newpassword Web Apps.|
|/sites/slots/operationresults/read|Récupère les résultats de l’opération des emplacements Web Apps.|
|/sites/slots/operations/read|Obtient les opérations des emplacements Web Apps.|
|/sites/slots/perfcounters/read|Obtient les compteurs de performances des emplacements Web Apps.|
|/sites/slots/phplogging/read|Récupère Phplogging des emplacements Web Apps.|
|/sites/slots/premieraddons/delete|Supprime les modules complémentaires Premier dans les emplacements Web Apps.|
|/sites/slots/premieraddons/read|Récupère les modules complémentaires Premier dans les emplacements Web Apps.|
|/sites/slots/premieraddons/write|Met à jour les modules complémentaires Premier dans les emplacements Web Apps.|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read|Obtient le paramètre de diagnostic pour la ressource|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write|Crée ou met à jour le paramètre de diagnostic pour la ressource|
|/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read|Obtient les métriques disponibles pour un emplacement Web Apps|
|/sites/slots/publiccertificates/read|Obtient les certificats publics des emplacements Web Apps.|
|/sites/slots/publiccertificates/write|Crée ou met à jour les certificats publics des emplacements Web Apps.|
|/sites/slots/publish/Action|Publie un emplacement d’application web.|
|/sites/slots/publishxml/Action|Récupère le profil de publication xml pour l’emplacement d’application web.|
|/sites/slots/Read|Récupère les propriétés d’un emplacement de déploiement d’une application web.|
|/sites/slots/resetSlotConfig/Action|Réinitialise la configuration d’emplacement de l’application web.|
|/sites/slots/resourcehealthmetadata/read|Obtient les métadonnées de Resource Health des emplacements Web Apps.|
|/sites/slots/restart/Action|Redémarre un emplacement d’application web.|
|/sites/slots/restore/read|Récupère la restauration des emplacements Web Apps.|
|/sites/slots/restore/write|Restaure les emplacements Web Apps.|
|/sites/slots/siteextensions/delete|Supprime les extensions de site des emplacements Web Apps.|
|/sites/slots/siteextensions/read|Obtient les extensions de site des emplacements Web Apps.|
|/sites/slots/siteextensions/write|Met à jour les extensions de site des emplacements Web Apps.|
|/sites/slots/slotsdiffs/Action|Récupère les différences de configuration entre l’application web et les emplacements.|
|/sites/slots/slotsswap/Action|Permute des emplacements de déploiement d’application web.|
|/sites/slots/snapshots/read|Obtient les captures instantanées des emplacements Web Apps.|
|/sites/slots/sourcecontrols/Delete|Supprime les paramètres de configuration du contrôle de code source de l’emplacement d’application web.|
|/sites/slots/sourcecontrols/Read|Récupère les paramètres de configuration du contrôle de code source de l’emplacement d’application Web.|
|/sites/slots/sourcecontrols/Write|Met à jour les paramètres de configuration du contrôle de code source de l’emplacement d’application web.|
|/sites/slots/start/Action|Démarre un emplacement d’application web.|
|/sites/slots/stop/Action|Arrête un emplacement d’application web.|
|/sites/slots/sync/action|Synchronise les emplacements Web Apps.|
|/sites/slots/triggeredwebjobs/delete|Supprime les tâches web déclenchées dans les emplacements Web Apps.|
|/sites/slots/triggeredwebjobs/read|Récupère les tâches web déclenchées dans les emplacements Web Apps.|
|/sites/slots/triggeredwebjobs/run/action|Exécute les tâches web déclenchées dans les emplacements Web Apps.|
|/sites/slots/usages/read|Récupère les utilisations des emplacements Web Apps.|
|/sites/slots/virtualnetworkconnections/delete|Supprime les connexions de réseau virtuel dans les emplacements Web Apps.|
|/sites/slots/virtualnetworkconnections/gateways/write|Met à jour les passerelles de connexions de réseau virtuel dans les emplacements Web Apps.|
|/sites/slots/virtualnetworkconnections/read|Récupère les connexions de réseau virtuel dans les emplacements Web Apps.|
|/sites/slots/virtualnetworkconnections/write|Met à jour les connexions de réseau virtuel dans les emplacements Web Apps.|
|/sites/slots/webjobs/read|Récupère WebJobs des emplacements Web Apps.|
|/sites/slots/Write|Crée un emplacement d’application web ou en met un à jour.|
|/sites/slotsdiffs/Action|Récupère les différences de configuration entre l’application web et les emplacements.|
|/sites/slotsswap/Action|Permute des emplacements de déploiement d’application web.|
|/sites/snapshots/read|Récupère les captures instantanées de Web Apps.|
|/sites/sourcecontrols/Delete|Supprime les paramètres de configuration du contrôle de code source d’application web.|
|/sites/sourcecontrols/Read|Récupère les paramètres de configuration du contrôle de code source d’application web.|
|/sites/sourcecontrols/Write|Met à jour les paramètres de configuration du contrôle de code source d’application web.|
|/sites/start/Action|Démarre une application web.|
|/sites/stop/Action|Arrête une application web.|
|/sites/sync/action|Synchronise Web Apps.|
|/sites/syncfunctiontriggers/action|Synchronise les déclencheurs de fonction pour Web Apps.|
|/sites/triggeredwebjobs/delete|Supprime les tâches web déclenchées dans Web Apps.|
|/sites/triggeredwebjobs/history/read|Obtient l’historique des tâches web déclenchées de Web Apps.|
|/sites/triggeredwebjobs/read|Récupère les tâches web déclenchées dans Web Apps.|
|/sites/triggeredwebjobs/run/action|Exécute les tâches web déclenchées dans Web Apps.|
|/sites/usages/read|Récupère les utilisations Web Apps.|
|/sites/virtualnetworkconnections/delete|Supprime les connexions de réseau virtuel Web Apps.|
|/sites/virtualnetworkconnections/gateways/read|Récupère les passerelles de connexions de réseau virtuel Web Apps.|
|/sites/virtualnetworkconnections/gateways/write|Met à jour les passerelles de connexions de réseau virtuel Web Apps.|
|/sites/virtualnetworkconnections/read|Récupère les connexions de réseau virtuel Web Apps.|
|/sites/virtualnetworkconnections/write|Met à jour les connexions de réseau virtuel Web Apps.|
|/sites/webjobs/read|Récupère WebJobs de Web Apps.|
|/sites/Write|Crée une application web ou en met une à jour.|
|/skus/read|Récupère des références.|
|/sourcecontrols/read|Récupère les contrôles de code source.|
|/sourcecontrols/write|Met à jour les contrôles de code source.|
|/unregister/action|Désinscrit le fournisseur de ressources Microsoft.Web pour l’abonnement.|
|/validate/action|Valide.|
|/verifyhostingenvironmentvnet/action|Vérifie le réseau virtuel de l’environnement d’hébergement.|

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

| Opération | Description |
|---|---|
|/components/read|Lit les ressources d’opérations|
|/healthInstances/read|Lit les ressources d’opérations|
|/Operations/read|Lit les ressources d’opérations|
|/workloads/delete|Supprime une ressource de charge de travail|
|/workloads/read|Lit une ressource de charge de travail|
|/workloads/write|Écrit une ressource de charge de travail|

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer un rôle personnalisé](custom-roles.md).
- Passez en revue les [rôles RBAC intégrés](built-in-roles.md).
- Découvrez comment gérer les affectations d’accès [par utilisateur](role-assignments-users.md) ou [par ressource](role-assignments-portal.md). 
- Découvrez comment [afficher les journaux d’activité pour auditer les actions sur les ressources](~/articles/azure-resource-manager/resource-group-audit.md).
