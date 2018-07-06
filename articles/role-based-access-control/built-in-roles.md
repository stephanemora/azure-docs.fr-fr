---
title: Rôles intégrés dans Azure | Microsoft Docs
description: Décrit les rôles intégrés pour le contrôle d’accès en fonction du rôle (RBAC) dans Azure. Répertorie les actions, notActions, dataActions et notDataActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/06/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 861b4ca360ef3fb9bc752d79009570ee2cfc9ade
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294494"
---
# <a name="built-in-roles-in-azure"></a>Rôles intégrés dans Azure
Le [contrôle d’accès en fonction du rôle (RBAC)](overview.md) a plusieurs définitions de rôles intégrés que vous pouvez affecter aux utilisateurs, groupes et principaux de service. Les attributions de rôles vous permettent de contrôler l’accès aux ressources dans Azure. Si les rôles intégrés ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer vos propres [rôles personnalisés](custom-roles.md).

Les rôles intégrés sont en constante évolution. Pour obtenir les dernières définitions de rôle, utilisez la commande [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) ou [az role definition list](/cli/azure/role/definition#az-role-definition-list).

## <a name="built-in-role-descriptions"></a>Descriptions des rôles intégrés
Le tableau ci-dessous fournit de brèves descriptions des rôles intégrés. Cliquez sur le nom d’un rôle pour voir la liste de `actions`, `notActions`, `dataActions` et `notDataActions` concernant ce rôle.


| Rôle intégré | Description |
| --- | --- |
| [Propriétaire](#owner) | Permet de tout gérer, notamment l’accès aux ressources. |
| [Collaborateur](#contributor) | Permet de tout gérer, à l’exception de l’accès aux ressources. |
| [Lecteur](#reader) | Vous permet de tout afficher, mais sans apporter de modifications. |
| [AcrImageSigner](#acrimagesigner) | signataire d’image ACR |
| [AcrQuarantineReader](#acrquarantinereader) | lecteur de données de quarantaine ACR |
| [AcrQuarantineWriter](#acrquarantinewriter) | écriture de données de quarantaine ACR |
| [Collaborateur du service Gestion des API](#api-management-service-contributor) | Peut gérer le service et les API |
| [Rôle d’opérateur du service Gestion des API](#api-management-service-operator-role) | Peut gérer le service, mais pas les API |
| [Rôle de lecteur du service Gestion des API](#api-management-service-reader-role) | Accès en lecture seule au service et aux API |
| [Collaborateur de composants Application Insights](#application-insights-component-contributor) | Gérer les composants Application Insights |
| [Débogueur de capture instantanée d’Application Insights](#application-insights-snapshot-debugger) | Autorise l’utilisateur à utiliser les fonctionnalités du débogueur de capture instantanée d’Application Insights. |
| [Opérateur de travaux Automation](#automation-job-operator) | Permet de créer et de gérer des travaux avec des runbooks Automation. |
| [Opérateur Automation](#automation-operator) | Les opérateurs d’Automation sont en mesure de démarrer, d’arrêter, de suspendre et de reprendre des travaux |
| [Opérateur de runbook Automation](#automation-runbook-operator) | Propriétés de lecture du runbook : pour pouvoir créer des travaux depuis le runbook. |
| [Propriétaire de l’inscription Azure Stack](#azure-stack-registration-owner) | Permet de gérer les inscriptions Azure Stack. |
| [Contributeur de sauvegarde](#backup-contributor) | Permet de gérer le service de sauvegarde, mais pas de créer des coffres, ni de permettre l’accès à d’autres personnes |
| [Opérateur de sauvegarde](#backup-operator) | Permet de gérer des services de sauvegarde, à l’exception de la suppression de la sauvegarde, de la création de coffres et de l’octroi d’autorisations d’accès à d’autres personnes |
| [Lecteur de sauvegarde](#backup-reader) | Peut afficher des services de sauvegarde, mais pas apporter des modifications |
| [Lecteur de facturation](#billing-reader) | Autorise l’accès en lecture aux données de facturation |
| [Collaborateur BizTalk](#biztalk-contributor) | Permet de gérer des services BizTalk, mais pas d’y accéder. |
| [Contributeur de point de terminaison CDN](#cdn-endpoint-contributor) | Peut gérer les points de terminaison CDN, mais ne peut pas accorder l’accès à d’autres utilisateurs. |
| [Lecteur de point de terminaison CDN](#cdn-endpoint-reader) | Peut afficher des points de terminaison CDN, mais ne peut pas effectuer de modifications. |
| [Contributeur de profil CDN](#cdn-profile-contributor) | Peut gérer des profils CDN et leurs points de terminaison, mais ne peut pas accorder l’accès à d’autres utilisateurs. |
| [Lecteur de profil CDN](#cdn-profile-reader) | Peut afficher des profils CDN et leurs points de terminaison, mais ne peut pas y apporter des modifications. |
| [Collaborateur de réseau classique](#classic-network-contributor) | Permet de gérer des réseaux classiques, mais pas d’y accéder. |
| [Collaborateur de compte de stockage classique](#classic-storage-account-contributor) | Permet de gérer des comptes de stockage classiques, mais pas d’y accéder. |
| [Rôle de service d’opérateur de clé de compte de stockage classique](#classic-storage-account-key-operator-service-role) | Les opérateurs de clés de comptes de stockage classiques sont autorisés à lister et à régénérer des clés sur des comptes de stockage classiques |
| [Collaborateur de machine virtuelle classique](#classic-virtual-machine-contributor) | Permet de gérer des machines virtuelles classiques, mais pas d’y accéder, ni au réseau virtuel ou au compte de stockage auquel elles sont connectées. |
| [Collaborateur de base de données ClearDB MySQL](#cleardb-mysql-db-contributor) | Permet de gérer des bases de données ClearDB MySQL, mais pas d’y accéder. |
| [Rôle de lecteur de compte Cosmos DB](#cosmos-db-account-reader-role) | Lire les données de comptes Azure Cosmos DB. Consultez [Collaborateur de compte DocumentDB](#documentdb-account-contributor) pour en savoir plus sur la gestion des comptes Azure Cosmos DB. |
| [Collaborateurs de fabrique de données](#data-factory-contributor) | Créer et gérer des fabriques de données, ainsi que les ressources enfants qu’elles contiennent. |
| [Développeur Data Lake Analytics](#data-lake-analytics-developer) | Permet d’envoyer, de surveiller et de gérer vos propres travaux, mais pas de créer ni de supprimer des comptes Data Lake Analytics. |
| [Videur de données](#data-purger) | Peut vider les données d’analytique |
| [Utilisateur de DevTest Labs](#devtest-labs-user) | Permet de connecter, de démarrer, de redémarrer et d’arrêter vos machines virtuelles dans votre Azure DevTest Labs. |
| [Contributeur de Zone DNS](#dns-zone-contributor) | Permet de gérer des zones DNS et des jeux d’enregistrements dans Azure DNS, mais pas de contrôler qui y a accès. |
| [Collaborateur de compte DocumentDB](#documentdb-account-contributor) | Gérer des comptes Azure Cosmos DB. Azure Cosmos DB était auparavant appelé DocumentDB. |
| [Collaborateur de compte Intelligent Systems](#intelligent-systems-account-contributor) | Permet de gérer des comptes Intelligent Systems, mais pas d’y accéder. |
| [Contributeur Key Vault](#key-vault-contributor) | Permet de gérer des coffres de clés, mais pas d’y accéder. |
| [Créateur Lab](#lab-creator) | Permet de créer, de gérer et de supprimer des labs gérés dans vos comptes Azure Lab. |
| [Contributeur Log Analytics](#log-analytics-contributor) | Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. La modification des paramètres de surveillance inclut l’ajout de l’extension de machine virtuelle aux machines virtuelles, la lecture des clés de comptes de stockage permettant de configurer la collection de journaux du stockage Azure, la création et la configuration de comptes Automation, l’ajout de solutions et la configuration de diagnostics Azure sur toutes les ressources Azure. |
| [Lecteur Log Analytics](#log-analytics-reader) | Peut afficher et rechercher toutes les données de surveillance, ainsi qu’afficher les paramètres de surveillance, notamment la configuration des diagnostics Azure sur toutes les ressources Azure. |
| [Contributeur d’application logique](#logic-app-contributor) | Permet de gérer l’application logique, mais pas d’y accéder. |
| [Opérateur d’application logique](#logic-app-operator) | Permet de lire, d’activer et de désactiver l’application logique. |
| [Contributeur d’identités gérées](#managed-identity-contributor) | Peut créer, lire, mettre à jour et supprimer une identité attribuée à l’utilisateur. |
| [Opérateur d’identités gérées](#managed-identity-operator) | Peut lire et assigner une identité attribuée à l’utilisateur. |
| [Contributeur de surveillance](#monitoring-contributor) | Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Lecteur de surveillance](#monitoring-reader) | Peut lire toutes les données de surveillance (métriques, journaux, etc.) Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Collaborateur de réseau](#network-contributor) | Permet de gérer des réseaux, mais pas d’y accéder. |
| [Collaborateur de compte NewRelic APM](#new-relic-apm-account-contributor) | Vous permet de gérer des comptes et applications New Relic Application Performance Management, mais pas d’y accéder. |
| [Lecteur et accès aux données](#reader-and-data-access) | Permet d’afficher tous les éléments, mais pas de supprimer ou de créer un compte de stockage ou une ressource contenue. En outre, autorise l’accès en lecture/écriture à toutes les données contenues dans un compte de stockage via l’accès aux clés de compte de stockage. |
| [Collaborateur Cache Redis](#redis-cache-contributor) | Permet de gérer des caches Redis, mais pas d’y accéder. |
| [Collaborateur de stratégie de ressource (préversion)](#resource-policy-contributor-preview) | (Préversion) Utilisateurs renvoyés de EA, avec des droits pour créer ou modifier une stratégie de ressource, créer un ticket de support et lire des ressources ou la hiérarchie. |
| [Collaborateur des collections de travaux du planificateur](#scheduler-job-collections-contributor) | Permet de gérer des collections de tâches du planificateur, mais pas d’y accéder. |
| [Collaborateur du service de recherche](#search-service-contributor) | Permet de gérer des services de recherche, mais pas d’y accéder. |
| [Administrateur de la sécurité](#security-admin) | Dans Security Center uniquement : peut afficher des stratégies de sécurité, afficher des états de sécurité, modifier des stratégies de sécurité, afficher des alertes et des recommandations, ignorer les alertes et les recommandations |
| [Gestionnaire de sécurité (hérité)](#security-manager-legacy) | Il s’agit d’un rôle hérité. Utilisez plutôt l’administrateur de sécurité. |
| [Lecteur de sécurité](#security-reader) | Dans Security Center uniquement : peut afficher des recommandations et des alertes, afficher des stratégies de sécurité, afficher des états de la sécurité, mais ne peut pas apporter des modifications |
| [Contributeur Site Recovery](#site-recovery-contributor) | Permet de gérer le service Site Recovery sauf la création de coffre et l’attribution de rôle |
| [Opérateur Site Recovery](#site-recovery-operator) | Permet de basculer et de restaurer mais pas d’effectuer d’autres opérations de gestion de Site Recovery |
| [Lecteur Site Recovery](#site-recovery-reader) | Permet d’afficher l’état de Site Recovery mais pas d’effectuer d’autres opérations de gestion |
| [Collaborateur de base de données SQL](#sql-db-contributor) | Permet de gérer des bases de données SQL, mais pas d’y accéder. Vous ne pouvez pas non plus gérer leurs stratégies de sécurité ni leurs serveurs SQL parents. |
| [Gestionnaire de sécurité SQL](#sql-security-manager) | Permet de gérer les stratégies de sécurité des serveurs et bases de données SQL, mais pas d’y accéder. |
| [Collaborateur SQL Server](#sql-server-contributor) | Permet de gérer des serveurs et bases de données SQL, mais pas d’y accéder, ni de gérer leurs stratégies de sécurité. |
| [Collaborateur de compte de stockage](#storage-account-contributor) | Permet de gérer des comptes de stockage, mais pas d’y accéder. |
| [Rôle de service d’opérateur de clé de compte de stockage](#storage-account-key-operator-service-role) | Les opérateurs de clés de comptes de stockage sont autorisés à lister et à régénérer des clés sur des comptes de stockage |
| [Contributeur aux données blob du stockage (préversion)](#storage-blob-data-contributor-preview) | Permet l'accès en lecture, en écriture et pour suppression aux conteneurs blob et aux données du stockage Azure |
| [Lecteur des données Blob du stockage (préversion)](#storage-blob-data-reader-preview) | Permet l'accès en lecture aux conteneurs blob et aux données du stockage Azure |
| [Contributeur aux données en file d'attente du stockage (préversion)](#storage-queue-data-contributor-preview) | Permet l'accès en lecture, en écriture et pour suppression aux files d'attente et aux messages en file d'attente du stockage Azure |
| [Lecteur des données en file d'attente du stockage (préversion)](#storage-queue-data-reader-preview) | Permet l'accès en lecture aux files d'attente et aux messages en file d'attente du stockage Azure |
| [Contributeur de demande de support](#support-request-contributor) | Permet de créer et de gérer des demandes de support |
| [Contributeur Traffic Manager](#traffic-manager-contributor) | Permet de gérer des profils Traffic Manager, mais pas de contrôler qui y a accès. |
| [Administrateur de l'accès utilisateur](#user-access-administrator) | Vous permet de gérer l'accès utilisateur aux ressources Azure. |
| [Connexion de l’administrateur aux machines virtuelles](#virtual-machine-administrator-login) | - Les utilisateurs disposant de ce rôle peuvent se connecter à une machine virtuelle avec des privilèges d’administrateur Windows ou d’utilisateur racine Linux. |
| [Collaborateur de machine virtuelle](#virtual-machine-contributor) | Permet de gérer des machines virtuelles, mais pas d’y accéder, ni au réseau virtuel ou au compte de stockage auquel elles sont connectées. |
| [Connexion de l’utilisateur aux machines virtuelles](#virtual-machine-user-login) | Les utilisateurs disposant de ce rôle ont la possibilité de se connecter à une machine virtuelle en tant qu’utilisateur standard. |
| [Collaborateur de plan web](#web-plan-contributor) | Permet de gérer des plans web pour des sites web, mais pas d’y accéder. |
| [Collaborateur de site web](#website-contributor) | Permet de gérer des sites web (pas des plans web), mais pas d’y accéder. |


## <a name="owner"></a>Propriétaire
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de tout gérer, notamment l’accès aux ressources. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Actions** |  |
> | * | Créer et gérer les ressources de tous les types |

## <a name="contributor"></a>Contributeur
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de tout gérer, à l’exception de l’accès aux ressources. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Actions** |  |
> | * | Créer et gérer les ressources de tous les types |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Ne peut pas supprimer des rôles ni des attributions de rôles. |
> | Microsoft.Authorization/*/Write | Ne peut pas créer des rôles ni des attributions de rôles. |
> | Microsoft.Authorization/elevateAccess/Action | Accorde à l’appelant un accès Administrateur de l’accès utilisateur au niveau de la portée du client |
> | Microsoft.Blueprint/blueprintAssignments/write |  |
> | Microsoft.Blueprint/blueprintAssignments/delete |  |

## <a name="reader"></a>Lecteur
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Vous permet de tout afficher, mais sans apporter de modifications. |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | signataire d’image ACR |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | lecteur de données de quarantaine ACR |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | écriture de données de quarantaine ACR |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="api-management-service-contributor"></a>Collaborateur du service de gestion des API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut gérer le service et les API |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Actions** |  |
> | Microsoft.ApiManagement/service/* | Créer et gérer le service Gestion des API |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="api-management-service-operator-role"></a>Rôle d’opérateur du service Gestion des API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut gérer le service, mais pas les API |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Actions** |  |
> | Microsoft.ApiManagement/service/*/read | Lire les instances du service Gestion des API |
> | Microsoft.ApiManagement/service/backup/action | Sauvegarder le service Gestion des API dans le conteneur spécifié dans un compte de stockage fourni par l’utilisateur |
> | Microsoft.ApiManagement/service/delete | Supprimer l’instance du service Gestion des API |
> | Microsoft.ApiManagement/service/managedeployments/action | Modifier une référence/unité, ajouter/supprimer des déploiements régionaux du service Gestion des API |
> | Microsoft.ApiManagement/service/read | Lire les métadonnées d’une instance du service Gestion des API |
> | Microsoft.ApiManagement/service/restore/action | Restaurer le service Gestion des API à partir du conteneur spécifié dans un compte de stockage fourni par l’utilisateur |
> | Microsoft.ApiManagement/service/updatecertificate/action | Télécharger le certificat SSL pour un service Gestion des API |
> | Microsoft.ApiManagement/service/updatehostname/action | Configurer, mettre à jour ou supprimer des noms de domaine personnalisés pour un service Gestion des API |
> | Microsoft.ApiManagement/service/write | Créer une instance du service Gestion des API |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obtenir la liste des clés utilisateur |

## <a name="api-management-service-reader-role"></a>Rôle de lecteur du service Gestion des API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Accès en lecture seule au service et aux API |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Actions** |  |
> | Microsoft.ApiManagement/service/*/read | Lire les instances du service Gestion des API |
> | Microsoft.ApiManagement/service/read | Lire les métadonnées d’une instance du service Gestion des API |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obtenir la liste des clés utilisateur |

## <a name="application-insights-component-contributor"></a>Collaborateur de composants Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Gérer les composants Application Insights |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.Insights/components/* | Créer et gérer les composants Insights |
> | Microsoft.Insights/webtests/* | Créer et gérer les tests web |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="application-insights-snapshot-debugger"></a>Débogueur de capture instantanée d’Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Autorise l’utilisateur à utiliser les fonctionnalités du débogueur de capture instantanée d’Application Insights. |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="automation-job-operator"></a>Opérateur de travaux Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de créer et de gérer des travaux avec des runbooks Automation. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Automation/automationAccounts/jobs/read | Obtient un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Reprend un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Arrête un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lit des ressources Runbook Worker hybrides |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtient un flux de travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspend un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/write | Crée un travail Azure Automation |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="automation-operator"></a>Opérateur Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Les opérateurs d’Automation sont en mesure de démarrer, d’arrêter, de suspendre et de reprendre des travaux |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lit des ressources Runbook Worker hybrides |
> | Microsoft.Automation/automationAccounts/jobs/read | Obtient un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Reprend un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Arrête un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtient un flux de travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspend un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/write | Crée un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Obtient une planification du travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Crée une planification du travail Azure Automation |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtient l’espace de travail lié au compte Automation. |
> | Microsoft.Automation/automationAccounts/read | Obtient un compte Azure Automation |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obtient un runbook Azure Automation |
> | Microsoft.Automation/automationAccounts/schedules/read | Obtient une ressource de planification Azure Automation |
> | Microsoft.Automation/automationAccounts/schedules/write | Crée ou met à jour une ressource de planification Azure Automation |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Obtient le résultat d’un travail |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="automation-runbook-operator"></a>Opérateur de runbook Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Propriétés de lecture du runbook : pour pouvoir créer des travaux depuis le runbook. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obtient un runbook Azure Automation |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="azure-stack-registration-owner"></a>Propriétaire de l’inscription Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer les inscriptions Azure Stack. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Actions** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Récupère les détails étendus d’un produit de la place de marché Azure Stack. |
> | Microsoft.AzureStack/registrations/products/read | Obtient les propriétés d’un produit de la place de marché Azure Stack. |
> | Microsoft.AzureStack/registrations/read | Obtient les propriétés d’une inscription Azure Stack. |

## <a name="backup-contributor"></a>Contributeur de sauvegarde
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer le service de sauvegarde, mais pas de créer des coffres, ni de permettre l’accès à d’autres personnes |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Gérer les résultats des opérations de gestion des sauvegardes |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Créer et gérer des conteneurs de sauvegarde dans les structures de sauvegarde du coffre Recovery Services |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Créer et gérer des travaux de sauvegarde |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Travaux d’exportation |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Créer et gérer des métadonnées associées à la gestion des sauvegardes |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Créer et gérer les résultats des opérations de gestion des sauvegardes |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Créer et gérer des stratégies de sauvegarde |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Créer et gérer les éléments qui peuvent être sauvegardés |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Créer et gérer les éléments sauvegardés |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Créer et gérer les conteneurs contenant les éléments de sauvegarde |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Créer et gérer des certificats associés à la sauvegarde dans le coffre Recovery Services |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Créer et gérer des informations étendues associées au coffre |
> | Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/* | Gérer les opérations de découverte pour récupérer les conteneurs récemment créés |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Créer et gérer les identités inscrites |
> | Microsoft.RecoveryServices/Vaults/usages/* | Créer et gérer l’utilisation du coffre Recovery Services |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Renvoie des résumés pour les éléments protégés et les serveurs protégés d’un coffre Recovery Services. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Renvoie le résultat de l’opération de travail d’exportation. |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="backup-operator"></a>Opérateur de sauvegarde
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des services de sauvegarde, à l’exception de la suppression de la sauvegarde, de la création de coffres et de l’octroi d’autorisations d’accès à d’autres personnes |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Renvoie l’état de l’opération. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtient les résultats de l’opération effectuée sur le conteneur de protection. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Effectue la sauvegarde d’un élément protégé. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtient les résultats de l’opération effectuée sur les éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Renvoie l’état de l’opération effectuée sur les éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Renvoie des détails d’objet de l’élément protégé. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obtenir les points de récupération des éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurer les points de récupération des éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Créer un élément protégé de sauvegarde. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Renvoie tous les conteneurs inscrits. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Créer et gérer des travaux de sauvegarde |
> | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Annuler le travail. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Renvoie le résultat de l’opération de travail. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Renvoie tous les objets de travail. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Travaux d’exportation |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Renvoie les métadonnées de gestion des sauvegardes pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Créer et gérer les résultats des opérations de gestion des sauvegardes |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obtenir les résultats de l’opération de stratégie. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Renvoie toutes les stratégies de protection. |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Créer et gérer les éléments qui peuvent être sauvegardés |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Renvoie la liste de tous les éléments pouvant être protégés. |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Renvoie la liste de tous les éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Renvoie tous les conteneurs appartenant à l’abonnement. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Renvoie des résumés pour les éléments protégés et les serveurs protégés d’un coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/* | Gérer les opérations de découverte pour récupérer les conteneurs récemment créés |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | L’opération d’obtention des résultats d’une opération peut être utilisée pour obtenir l’état de l’opération et le résultat de l’opération envoyée de manière asynchrone. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | L’opération d’obtention de conteneurs peut être utilisée pour obtenir les conteneurs inscrits pour une ressource. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | L’opération d’inscription d’un conteneur de service peut être utilisée pour inscrire un conteneur avec Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Approvisionner la récupération d’éléments instantanée pour l’élément protégé. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Révoquer la récupération d’éléments instantanée pour l’élément protégé. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Renvoie le résultat de l’opération de travail d’exportation. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
> | Microsoft.RecoveryServices/Vaults/certificates/write | L’opération de mise à jour de certificat de ressource met à jour le certificat d’informations d’identification du coffre/de la ressource. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="backup-reader"></a>Lecteur de sauvegarde
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut afficher des services de sauvegarde, mais pas apporter des modifications |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Renvoie l’état de l’opération. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtient les résultats de l’opération effectuée sur le conteneur de protection. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtient les résultats de l’opération effectuée sur les éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Renvoie l’état de l’opération effectuée sur les éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Renvoie des détails d’objet de l’élément protégé. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Renvoie tous les conteneurs inscrits. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Renvoie le résultat de l’opération de travail. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Renvoie tous les objets de travail. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Travaux d’exportation |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Renvoie les métadonnées de gestion des sauvegardes pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Renvoie le résultat de l’opération de sauvegarde pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obtenir les résultats de l’opération de stratégie. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Renvoie toutes les stratégies de protection. |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Renvoie la liste de tous les éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Renvoie tous les conteneurs appartenant à l’abonnement. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Renvoie des résumés pour les éléments protégés et les serveurs protégés d’un coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | L’opération d’obtention des résultats d’une opération peut être utilisée pour obtenir l’état de l’opération et le résultat de l’opération envoyée de manière asynchrone. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | L’opération d’obtention de conteneurs peut être utilisée pour obtenir les conteneurs inscrits pour une ressource. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obtenir les points de récupération des éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Renvoie le résultat de l’opération de travail d’exportation. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |

## <a name="billing-reader"></a>Lecteur de facturation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Autorise l’accès en lecture aux données de facturation |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Billing/*/read | Lire les informations de facturation |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Management/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="biztalk-contributor"></a>Collaborateur BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des services BizTalk, mais pas d’y accéder. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.BizTalkServices/BizTalk/* | Créer et gérer BizTalk Services |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="cdn-endpoint-contributor"></a>Contributeur de point de terminaison CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut gérer les points de terminaison CDN, mais ne peut pas accorder l’accès à d’autres utilisateurs. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="cdn-endpoint-reader"></a>Lecteur de point de terminaison CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut afficher des points de terminaison CDN, mais ne peut pas effectuer de modifications. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="cdn-profile-contributor"></a>Contributeur de profil CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut gérer des profils CDN et leurs points de terminaison, mais ne peut pas accorder l’accès à d’autres utilisateurs. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="cdn-profile-reader"></a>Lecteur de profil CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut afficher des profils CDN et leurs points de terminaison, mais ne peut pas y apporter des modifications. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="classic-network-contributor"></a>Collaborateur de réseau classique
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des réseaux classiques, mais pas d’y accéder. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.ClassicNetwork/* | Créer et gérer des réseaux classiques |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="classic-storage-account-contributor"></a>Collaborateur de compte de stockage classique
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des comptes de stockage classiques, mais pas d’y accéder. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.ClassicStorage/storageAccounts/* | Créer et gérer les comptes de stockage |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="classic-storage-account-key-operator-service-role"></a>Rôle de service d’opérateur de clé de compte de stockage classique
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Les opérateurs de clés de comptes de stockage classiques sont autorisés à lister et à régénérer des clés sur des comptes de stockage classiques |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Actions** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Répertorie les clés d’accès des comptes de stockage. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Régénère les clés d’accès existantes du compte de stockage. |

## <a name="classic-virtual-machine-contributor"></a>Collaborateur de machine virtuelle classique
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des machines virtuelles classiques, mais pas d’y accéder, ni au réseau virtuel ou au compte de stockage auquel elles sont connectées. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.ClassicCompute/domainNames/* | Créer et gérer des noms de domaine de calcul classique |
> | Microsoft.ClassicCompute/virtualMachines/* | Créer et gérer les machines virtuelles |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Lier une adresse IP réservée |
> | Microsoft.ClassicNetwork/reservedIps/read | Obtient les adresses IP réservées |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Joint le réseau virtuel. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Obtenez le réseau virtuel. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Retourne le disque du compte de stockage. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Retourne l’image du compte de stockage. |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Répertorie les clés d’accès des comptes de stockage. |
> | Microsoft.ClassicStorage/storageAccounts/read | Retourne le compte de stockage avec le compte spécifique. |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="cleardb-mysql-db-contributor"></a>Collaborateur de base de données ClearDB MySQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des bases de données ClearDB MySQL, mais pas d’y accéder. |
> | **Id** | 9106cda0-8a86-4e81-b686-29a22c54effe |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | successbricks.cleardb/databases/* | Créer et gérer les bases de données ClearDB MySQL |

## <a name="cosmos-db-account-reader-role"></a>Rôle de lecteur de compte Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lire les données de comptes Azure Cosmos DB. Consultez [Collaborateur de compte DocumentDB](#documentdb-account-contributor) pour en savoir plus sur la gestion des comptes Azure Cosmos DB. |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les attributions de rôle, lire les autorisations accordées à chaque utilisateur |
> | Microsoft.DocumentDB/*/read | Lire n’importe quelle collection |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Lire les clés en lecture seule du compte de base de données. |
> | Microsoft.Insights/MetricDefinitions/read | Lire les définitions des mesures |
> | Microsoft.Insights/Metrics/read | Lire des mesures |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="data-factory-contributor"></a>Collaborateurs de fabrique de données
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Créer et gérer des fabriques de données, ainsi que les ressources enfants qu’elles contiennent. |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.DataFactory/dataFactories/* | Créer et gérer des fabriques de données ainsi que leurs ressources enfants |
> | Microsoft.DataFactory/factories/* | Créer et gérer des fabriques de données ainsi que leurs ressources enfants |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="data-lake-analytics-developer"></a>Développeur Data Lake Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet d’envoyer, de surveiller et de gérer vos propres travaux, mais pas de créer ni de supprimer des comptes Data Lake Analytics. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Supprime un compte Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Accorde des autorisations pour annuler des travaux soumis par d’autres utilisateurs. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Crée ou met à jour un compte Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Crée ou met à jour un compte Data Lake Store lié d’un compte Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Dissocie un compte Data Lake Store d’un compte Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Crée ou met à jour un compte de stockage lié d’un compte Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Dissocie un compte de stockage d’un compte Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Créer ou mettre à jour une règle de pare-feu. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Supprimer une règle de pare-feu. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Crée ou met à jour une stratégie de calcul. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Supprime une stratégie de calcul. |

## <a name="data-purger"></a>Videur de données
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut vider les données d’analytique |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Actions** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Vider des données d’Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Supprime les données spécifiées de l’espace de travail |

## <a name="devtest-labs-user"></a>Utilisateur de DevTest Labs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de connecter, de démarrer, de redémarrer et d’arrêter vos machines virtuelles dans votre Azure DevTest Labs. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Compute/availabilitySets/read | Obtenir les propriétés d’un groupe à haute disponibilité |
> | Microsoft.Compute/virtualMachines/*/read | Lire les propriétés d’une machine virtuelle (tailles de machine virtuelle, état de l’exécution, extensions de machine virtuelle, etc.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Mettre hors tension la machine virtuelle et libérer les ressources de calcul |
> | Microsoft.Compute/virtualMachines/read | Obtenir les propriétés d’une machine virtuelle |
> | Microsoft.Compute/virtualMachines/restart/action | Redémarrer la machine virtuelle |
> | Microsoft.Compute/virtualMachines/start/action | Démarrer la machine virtuelle |
> | Microsoft.DevTestLab/*/read | Lire les propriétés d’un laboratoire |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Créer des machines virtuelles dans un laboratoire. |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Revendiquer une machine virtuelle exigible aléatoire dans le laboratoire. |
> | Microsoft.DevTestLab/labs/formulas/delete | Supprimer des formules. |
> | Microsoft.DevTestLab/labs/formulas/read | Lire des formules. |
> | Microsoft.DevTestLab/labs/formulas/write | Ajouter ou modifier des formules. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Évaluer la stratégie de laboratoire. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Prendre possession d’une machine virtuelle existante. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Joint un pool d’adresses principales d’équilibrage de charge. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joint une règle NAT entrante d’équilibrage de charge. |
> | Microsoft.Network/networkInterfaces/*/read | Lire les propriétés d’une interface réseau (par exemple, tous les équilibreurs de charge dont l’interface réseau fait partie) |
> | Microsoft.Network/networkInterfaces/join/action | Joint une machine virtuelle à une interface réseau. |
> | Microsoft.Network/networkInterfaces/read | Obtient une définition d’interface réseau.  |
> | Microsoft.Network/networkInterfaces/write | Crée une interface réseau ou met à jour une interface réseau existante.  |
> | Microsoft.Network/publicIPAddresses/*/read | Lire les propriétés d’une adresse IP publique |
> | Microsoft.Network/publicIPAddresses/join/action | Joint une adresse IP publique. |
> | Microsoft.Network/publicIPAddresses/read | Obtient une définition de l’adresse IP publique. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Joint un réseau virtuel. |
> | Microsoft.Resources/deployments/operations/read | Obtient ou répertorie les opérations de déploiement. |
> | Microsoft.Resources/deployments/read | Obtient ou répertorie les déploiements. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Répertorier les tailles disponibles pour la mise à jour de la machine virtuelle |

## <a name="dns-zone-contributor"></a>Contributeur de Zone DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des zones DNS et des jeux d’enregistrements dans Azure DNS, mais pas de contrôler qui y a accès. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.Network/dnsZones/* | Créer et gérer des enregistrements et zones DNS |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="documentdb-account-contributor"></a>Collaborateur de compte DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Gérer des comptes Azure Cosmos DB. Azure Cosmos DB était auparavant appelé DocumentDB. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.DocumentDb/databaseAccounts/* | Créer et gérer des comptes Azure Cosmos DB |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="intelligent-systems-account-contributor"></a>Collaborateur de compte Intelligent Systems
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des comptes Intelligent Systems, mais pas d’y accéder. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.IntelligentSystems/accounts/* | Créer et gérer les comptes Intelligent Systems |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="key-vault-contributor"></a>Contributeur Key Vault
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des coffres de clés, mais pas d’y accéder. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Vider un coffre Key Vault supprimé de manière réversible |
> | Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Créateur Lab
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de créer, de gérer et de supprimer des labs gérés dans vos comptes Azure Lab. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Crée un lab dans un compte lab. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="log-analytics-contributor"></a>Contributeur Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. La modification des paramètres de surveillance inclut l’ajout de l’extension de machine virtuelle aux machines virtuelles, la lecture des clés de comptes de stockage permettant de configurer la collection de journaux du stockage Azure, la création et la configuration de comptes Automation, l’ajout de solutions et la configuration de diagnostics Azure sur toutes les ressources Azure. |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Répertorie les clés d’accès des comptes de stockage. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Insights/diagnosticSettings/* | Crée, met à jour ou lit le paramètre de diagnostic pour Analysis Server. |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="log-analytics-reader"></a>Lecteur Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut afficher et rechercher toutes les données de surveillance, ainsi qu’afficher les paramètres de surveillance, notamment la configuration des diagnostics Azure sur toutes les ressources Azure. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Effectue les recherches à l’aide d’un nouveau moteur. |
> | Microsoft.OperationalInsights/workspaces/search/action | Exécute une requête de recherche. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Récupère les clés partagées de l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail. |

## <a name="logic-app-contributor"></a>Contributeur d’application logique
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer l’application logique, mais pas d’y accéder. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Répertorie les clés d’accès des comptes de stockage. |
> | Microsoft.ClassicStorage/storageAccounts/read | Retourne le compte de stockage avec le compte spécifique. |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Insights/diagnosticSettings/* | Crée, met à jour ou lit le paramètre de diagnostic pour Analysis Server. |
> | Microsoft.Insights/logdefinitions/* | Cette autorisation est nécessaire pour les utilisateurs qui doivent accéder aux journaux d’activité via le portail. Répertorier les catégories de journaux dans le journal d’activité. |
> | Microsoft.Insights/metricDefinitions/* | Lire des définitions de mesure (liste de types de mesure disponibles pour une ressource). |
> | Microsoft.Logic/* | Gère les ressources Logic Apps. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Web/connectionGateways/* | Crée et gère une passerelle de connexion. |
> | Microsoft.Web/connections/* | Crée et gère une connexion. |
> | Microsoft.Web/customApis/* | Crée et gère une API personnalisée. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Récupère les propriétés d’un plan App Service. |
> | Microsoft.Web/sites/functions/listSecrets/action | Répertorie les fonctions Web Apps des clés secrètes. |

## <a name="logic-app-operator"></a>Opérateur d’application logique
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de lire, d’activer et de désactiver l’application logique. |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/*/read | Lit les règles d’alerte d’Insights. |
> | Microsoft.Insights/diagnosticSettings/*/read | Obtient les paramètres de diagnostic de Logic Apps. |
> | Microsoft.Insights/metricDefinitions/*/read | Obtient les mesures disponibles pour Logic Apps. |
> | Microsoft.Logic/*/read | Lit les ressources Logic Apps. |
> | Microsoft.Logic/workflows/disable/action | Désactive le workflow. |
> | Microsoft.Logic/workflows/enable/action | Active le workflow. |
> | Microsoft.Logic/workflows/validate/action | Valide le workflow. |
> | Microsoft.Resources/deployments/operations/read | Obtient ou répertorie les opérations de déploiement. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Web/connectionGateways/*/read | Lit les passerelles de connexion. |
> | Microsoft.Web/connections/*/read | Lit les connexions. |
> | Microsoft.Web/customApis/*/read | Lit l’API personnalisée. |
> | Microsoft.Web/serverFarms/read | Récupère les propriétés d’un plan App Service. |

## <a name="managed-identity-contributor"></a>Contributeur d’identités gérées
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut créer, lire, mettre à jour et supprimer une identité attribuée à l’utilisateur. |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Actions** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="managed-identity-operator"></a>Opérateur d’identités gérées
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut lire et assigner une identité attribuée à l’utilisateur. |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Actions** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="monitoring-contributor"></a>Contributeur d’analyse
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/AlertRules/* | Règles d’alerte en lecture/écriture/suppression. |
> | Microsoft.Insights/components/* | Lire/écrire/supprimer des composants Application Insights. |
> | Microsoft.Insights/DiagnosticSettings/* | Paramètres de diagnostic en lecture/écriture/suppression. |
> | Microsoft.Insights/eventtypes/* | Événements du journal d’activité, (événements de gestion) dans un abonnement. Cette autorisation est applicable pour l’accès par programme et portail dans le journal d’activité. |
> | Microsoft.Insights/LogDefinitions/* | Cette autorisation est nécessaire pour les utilisateurs qui doivent accéder aux journaux d’activité via le portail. Répertorier les catégories de journaux dans le journal d’activité. |
> | Microsoft.Insights/MetricDefinitions/* | Lire des définitions de mesure (liste de types de mesure disponibles pour une ressource). |
> | Microsoft.Insights/Metrics/* | Lire des mesures pour une ressource. |
> | Microsoft.Insights/Register/Action | Inscrire le fournisseur Microsoft.Insights |
> | Microsoft.Insights/webtests/* | Lire/écrire/supprimer des tests web Application Insights. |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Lire/écrire/supprimer des packs de solution Log Analytics. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Lire/écrire/supprimer des recherches enregistrées Log Analytics. |
> | Microsoft.OperationalInsights/workspaces/search/action | Exécute une requête de recherche. |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Récupère les clés partagées de l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Lire/écrire/supprimer les configurations des insights de stockage Log Analytics. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Lecteur d’analyse
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut lire toutes les données de surveillance (métriques, journaux, etc.) Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | Microsoft.OperationalInsights/workspaces/search/action | Exécute une requête de recherche. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="network-contributor"></a>Collaborateur de réseau
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des réseaux, mais pas d’y accéder. |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.Network/* | Créer et gérer des réseaux |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="new-relic-apm-account-contributor"></a>Collaborateur de compte NewRelic APM
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Vous permet de gérer des comptes et applications New Relic Application Performance Management, mais pas d’y accéder. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | NewRelic.APM/accounts/* |  |

## <a name="reader-and-data-access"></a>Lecteur et accès aux données
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet d’afficher tous les éléments, mais pas de supprimer ou de créer un compte de stockage ou une ressource contenue. En outre, autorise l’accès en lecture/écriture à toutes les données contenues dans un compte de stockage via l’accès aux clés de compte de stockage. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |

## <a name="redis-cache-contributor"></a>Collaborateur Cache Redis
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des caches Redis, mais pas d’y accéder. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Cache/redis/* | Créer et gérer les caches Redis |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="resource-policy-contributor-preview"></a>Collaborateur de stratégie de ressource (préversion)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | (Préversion) Utilisateurs renvoyés de EA, avec des droits pour créer ou modifier une stratégie de ressource, créer un ticket de support et lire des ressources ou la hiérarchie. |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | Microsoft.Authorization/policyassignments/* | Créer et gérer des attributions de stratégies |
> | Microsoft.Authorization/policydefinitions/* | Créer et gérer des définitions de stratégies |
> | Microsoft.Authorization/policysetdefinitions/* | Créer et gérer des ensembles de stratégies |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="scheduler-job-collections-contributor"></a>Collaborateur des collections de travaux du planificateur
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des collections de tâches du planificateur, mais pas d’y accéder. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Scheduler/jobcollections/* | Créer et gérer des collections de travaux |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="search-service-contributor"></a>Collaborateur du service de recherche
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des services de recherche, mais pas d’y accéder. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Search/searchServices/* | Créer et gérer les services de recherche |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="security-admin"></a>Administrateur de la sécurité
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Dans Security Center uniquement : peut afficher des stratégies de sécurité, afficher des états de sécurité, modifier des stratégies de sécurité, afficher des alertes et des recommandations, ignorer les alertes et les recommandations |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Authorization/policyAssignments/* | Créer et gérer des attributions de stratégies |
> | Microsoft.Authorization/policyDefinitions/* | Créer et gérer des définitions de stratégies |
> | Microsoft.Authorization/policySetDefinitions/* | Créer et gérer des ensembles de stratégies |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.operationalInsights/workspaces/*/read | Afficher les données Log Analytics |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Security/*/read | Lire des stratégies et des composants de sécurité |
> | Microsoft.Security/locations/alerts/dismiss/action | Ignore une alerte de sécurité. |
> | Microsoft.Security/locations/alerts/activate/action | Active une alerte de sécurité |
> | Microsoft.Security/locations/tasks/dismiss/action | Ignorer une recommandation de sécurité. |
> | Microsoft.Security/locations/tasks/activate/action | Activer une recommandation de sécurité. |
> | Microsoft.Security/policies/write | Met à jour la stratégie de sécurité. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="security-manager-legacy"></a>Gestionnaire de sécurité (hérité)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Il s’agit d’un rôle hérité. Utilisez plutôt l’administrateur de sécurité. |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.ClassicCompute/*/read | Lire les informations de configuration relatives aux machines virtuelles classiques |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Écrire la configuration des machines virtuelles classiques |
> | Microsoft.ClassicNetwork/*/read | Lire les informations de configuration relatives au réseau classique |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Security/* | Créer et gérer des stratégies et des composants de sécurité |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="security-reader"></a>Lecteur de sécurité
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Dans Security Center uniquement : peut afficher des recommandations et des alertes, afficher des stratégies de sécurité, afficher des états de la sécurité, mais ne peut pas apporter des modifications |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Actions** |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.operationalInsights/workspaces/*/read | Afficher les données Log Analytics |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Security/*/read | Lire des stratégies et des composants de sécurité |

## <a name="site-recovery-contributor"></a>Contributeur Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer le service Site Recovery sauf la création de coffre et l’attribution de rôle |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp est une opération interne utilisée par le service. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | L’opération de mise à jour de certificat de ressource met à jour le certificat d’informations d’identification du coffre/de la ressource. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Créer et gérer des informations étendues associées au coffre |
> | Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Créer et gérer les identités inscrites |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Créer ou mettre à jour les paramètres d’alerte de réplication |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Lire des événements. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Créer et gérer des structures de réplication |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Créer et gérer des travaux de réplication |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Créer et gérer des stratégies de réplication |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Créer et gérer des plans de récupération |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Créer et gérer la configuration de stockage du coffre Recovery Services |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Renvoie des informations de jeton pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | L’opération de jeton de coffre peut être utilisée pour obtenir un jeton de coffre pour les opérations de serveur principal au niveau du coffre. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Lire les alertes pour le coffre Recovery Services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="site-recovery-operator"></a>Opérateur Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de basculer et de restaurer mais pas d’effectuer d’autres opérations de gestion de Site Recovery |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp est une opération interne utilisée par le service. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | L’opération d’obtention des résultats d’une opération peut être utilisée pour obtenir l’état de l’opération et le résultat de l’opération envoyée de manière asynchrone. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | L’opération d’obtention de conteneurs peut être utilisée pour obtenir les conteneurs inscrits pour une ressource. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lire des paramètres d’alertes. |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Lire des événements. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Vérifie la cohérence de la structure. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Lire des structures. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Réassocier une passerelle. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renouveler le certificat pour Fabric |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Lire des réseaux. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Lire des mappages de réseau. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Lire des conteneurs de protection. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Lire des éléments pouvant être protégés. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Appliquer un point de récupération. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Validation du basculement. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Basculement planifié. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Lire des éléments protégés. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Lire des points de récupération de réplication. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Réparer la réplication. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Reprotéger l’élément protégé. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test Failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Nettoyage de basculement test. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Basculement |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Mettre à jour le service Mobilité. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Lire des mappages de conteneurs de protection. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Lire des fournisseurs Recovery Services. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Actualiser un fournisseur. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Lire des classifications de stockage. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Lire des mappages de classifications de stockage. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Lire des travaux. |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Créer et gérer des travaux de réplication |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Lire des stratégies. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plan de récupération de validation de basculement. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plan de récupération de basculement planifié. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Lire des plans de récupération. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Reprotéger le plan de récupération. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plan de récupération de basculement test. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plan de récupération de nettoyage de basculement test. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan de récupération de basculement. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Lire les alertes pour le coffre Recovery Services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Renvoie des informations de jeton pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | L’opération de jeton de coffre peut être utilisée pour obtenir un jeton de coffre pour les opérations de serveur principal au niveau du coffre. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="site-recovery-reader"></a>Lecteur Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet d’afficher l’état de Site Recovery mais pas d’effectuer d’autres opérations de gestion |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | L’opération d’obtention des résultats d’une opération peut être utilisée pour obtenir l’état de l’opération et le résultat de l’opération envoyée de manière asynchrone. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | L’opération d’obtention de conteneurs peut être utilisée pour obtenir les conteneurs inscrits pour une ressource. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lire des paramètres d’alertes. |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Lire des événements. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Lire des structures. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Lire des réseaux. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Lire des mappages de réseau. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Lire des conteneurs de protection. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Lire des éléments pouvant être protégés. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Lire des éléments protégés. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Lire des points de récupération de réplication. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Lire des mappages de conteneurs de protection. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Lire des fournisseurs Recovery Services. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Lire des classifications de stockage. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Lire des mappages de classifications de stockage. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Lire des travaux. |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Lire des travaux. |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Lire des stratégies. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Lire des plans de récupération. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Renvoie des informations de jeton pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | L’opération de jeton de coffre peut être utilisée pour obtenir un jeton de coffre pour les opérations de serveur principal au niveau du coffre. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="sql-db-contributor"></a>Collaborateur de base de données SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des bases de données SQL, mais pas d’y accéder. Vous ne pouvez pas non plus gérer leurs stratégies de sécurité ni leurs serveurs SQL parents. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Créer et gérer les bases de données SQL |
> | Microsoft.Sql/servers/read | Retourner la liste des serveurs ou obtenir les propriétés pour le serveur spécifié. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Impossible de modifier les stratégies d’audit |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Impossible de modifier les paramètres d’audit |
> | Microsoft.Sql/servers/databases/auditRecords/read | Récupère les enregistrements d’audit d’objet blob de base de données. |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Impossible de modifier les stratégies de connexion |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Impossible de modifier les stratégies de masquage des données |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Impossible de modifier les stratégies d’alerte de sécurité |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Impossible de modifier les mesures de sécurité |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>Gestionnaire de sécurité SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer les stratégies de sécurité des serveurs et bases de données SQL, mais pas d’y accéder. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture Microsoft |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Sql/servers/auditingPolicies/* | Créer et gérer les stratégies d’audit de serveur SQL |
> | Microsoft.Sql/servers/auditingSettings/* | Créer et gérer les paramètres d’audit de serveur SQL |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Créer et gérer les stratégies d’audit de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Créer et gérer les paramètres d’audit de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/auditRecords/read | Lire les enregistrements d’audit |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Créer et gérer les stratégies de connexion de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Créer et gérer les stratégies de masquage de données de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/read | Retourner la liste des bases de données ou obtenir les propriétés pour la base de données spécifiée. |
> | Microsoft.Sql/servers/databases/schemas/read | Récupérer la liste des schémas d’une base de données |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Récupère la liste des colonnes d’une table. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Récupérer la liste des tables d’une base de données |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Créer et gérer les stratégies d’alerte de sécurité de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Créer et gérer les mesures de sécurité de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Retourner la liste des serveurs ou obtenir les propriétés pour le serveur spécifié. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Créer et gérer les stratégies d’alerte de sécurité de serveur SQL |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="sql-server-contributor"></a>Collaborateur SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des serveurs et bases de données SQL, mais pas d’y accéder, ni de gérer leurs stratégies de sécurité. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Créer et gérer les serveurs SQL |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Impossible de modifier les stratégies d’audit de serveur SQL |
> | Microsoft.Sql/servers/auditingSettings/* | Impossible de modifier les paramètres d’audit de serveur SQL |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Impossible de modifier les stratégies d’audit de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Impossible de modifier les paramètres d’audit de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/auditRecords/read | Impossible de lire les enregistrements d’audit |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Impossible de modifier les stratégies de connexion de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Impossible de modifier les stratégies de masquage de données de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Impossible de modifier les stratégies d’alerte de sécurité de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Impossible de modifier les mesures de sécurité de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Impossible de modifier les stratégies d’alerte de sécurité de serveur SQL |

## <a name="storage-account-contributor"></a>Collaborateur de compte de stockage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des comptes de stockage, mais pas d’y accéder. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture totale |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Insights/diagnosticSettings/* | Gérer les paramètres de diagnostic |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/* | Créer et gérer les comptes de stockage |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="storage-account-key-operator-service-role"></a>Rôle de service d’opérateur de clé de compte de stockage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Les opérateurs de clés de comptes de stockage sont autorisés à lister et à régénérer des clés sur des comptes de stockage |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Régénère les clés d’accès au compte de stockage spécifié. |

## <a name="storage-blob-data-contributor-preview"></a>Contributeur aux données Blob du stockage (préversion)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet l'accès en lecture, en écriture et pour suppression aux conteneurs blob et aux données du stockage Azure |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Retourne le résultat de la suppression d’un conteneur |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retourne un conteneur ou une liste de conteneurs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retourne le résultat du placement ou du bail du conteneur d’objets blob |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Retourne le résultat de la suppression d'un objet blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retourne un objet blob ou une liste d'objets blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Retourne le résultat de l'écriture d'un objet blob |

## <a name="storage-blob-data-reader-preview"></a>Lecteur des données Blob du stockage (préversion)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet l'accès en lecture aux conteneurs blob et aux données du stockage Azure |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retourne un conteneur ou une liste de conteneurs |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retourne un objet blob ou une liste d'objets blob |

## <a name="storage-queue-data-contributor-preview"></a>Contributeur aux données en file d'attente du stockage (préversion)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet l'accès en lecture, en écriture et pour suppression aux files d'attente et aux messages en file d'attente du stockage Azure |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Retourne le résultat de la suppression d’une file d’attente |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Retourne une file d’attente ou une liste de files d’attente. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Retourne le résultat de l’écriture d’une file d’attente |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Retourne le résultat de la suppression d'un message |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Retourne un message |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Retourne le résultat de l'écriture d'un message |

## <a name="storage-queue-data-reader-preview"></a>Lecteur des données en file d'attente du stockage (préversion)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet l'accès en lecture aux files d'attente et aux messages en file d'attente du stockage Azure |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Retourne une file d’attente ou une liste de files d’attente. |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Retourne un message |

## <a name="support-request-contributor"></a>Contributeur de demande de support
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de créer et de gérer des demandes de support |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="traffic-manager-contributor"></a>Contributeur Traffic Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des profils Traffic Manager, mais pas de contrôler qui y a accès. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="user-access-administrator"></a>Administrateur de l'accès utilisateur
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Vous permet de gérer l'accès utilisateur aux ressources Azure. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | Microsoft.Authorization/* | Gérer les autorisations |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="virtual-machine-administrator-login"></a>Connexion de l’administrateur aux machines virtuelles
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | - Les utilisateurs disposant de ce rôle peuvent se connecter à une machine virtuelle avec des privilèges d’administrateur Windows ou d’utilisateur racine Linux. |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Actions** |  |
> | Microsoft.Network/publicIPAddresses/read | Obtient une définition de l’adresse IP publique. |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.Network/loadBalancers/read | Obtient une définition d’équilibrage de charge. |
> | Microsoft.Network/networkInterfaces/read | Obtient une définition d’interface réseau.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Se connecter à la machine virtuelle comme utilisateur normal |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Se connecter à une machine virtuelle avec des privilèges d’administrateur Windows ou d’utilisateur racine Linux |

## <a name="virtual-machine-contributor"></a>Collaborateur de machine virtuelle
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des machines virtuelles, mais pas d’y accéder, ni au réseau virtuel ou au compte de stockage auquel elles sont connectées. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.Compute/availabilitySets/* | Créer et gérer des groupes à haute disponibilité de calcul |
> | Microsoft.Compute/locations/* | Créer et gérer des emplacements de calcul |
> | Microsoft.Compute/virtualMachines/* | Créer et gérer les machines virtuelles |
> | Microsoft.Compute/virtualMachineScaleSets/* | Créez et gérez des jeux de mise à l’échelle des machines virtuelles |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Joint un pool d’adresses principales de passerelle d’application. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Joint un pool d’adresses principales d’équilibrage de charge. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Joint un pool NAT entrant d’équilibrage de charge. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joint une règle NAT entrante d’équilibrage de charge. |
> | Microsoft.Network/loadBalancers/probes/join/action | Autorise l’utilisation des sondes d’un équilibreur de charge. Par exemple, avec cette autorisation, la propriété healthProbe du groupe de machines virtuelles identiques peut faire référence à la sonde. |
> | Microsoft.Network/loadBalancers/read | Obtient une définition d’équilibrage de charge. |
> | Microsoft.Network/locations/* | Créer et gérer des emplacements réseau |
> | Microsoft.Network/networkInterfaces/* | Créer et gérer des interfaces réseau |
> | Microsoft.Network/networkSecurityGroups/join/action | Joint un groupe de sécurité réseau. |
> | Microsoft.Network/networkSecurityGroups/read | Obtient une définition de groupe de sécurité réseau. |
> | Microsoft.Network/publicIPAddresses/join/action | Joint une adresse IP publique. |
> | Microsoft.Network/publicIPAddresses/read | Obtient une définition de l’adresse IP publique. |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Joint un réseau virtuel. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Renvoie des détails d’objet de l’élément protégé. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Créer un élément protégé de sauvegarde. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Crée une intention de protection de sauvegarde. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Renvoie toutes les stratégies de protection. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Crée une stratégie de protection. |
> | Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/write | L’opération de création de coffre entraîne la création d’une ressource Azure de type « coffre ». |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |

## <a name="virtual-machine-user-login"></a>Connexion de l’utilisateur aux machines virtuelles
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Les utilisateurs disposant de ce rôle ont la possibilité de se connecter à une machine virtuelle en tant qu’utilisateur standard. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Actions** |  |
> | Microsoft.Network/publicIPAddresses/read | Obtient une définition de l’adresse IP publique. |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.Network/loadBalancers/read | Obtient une définition d’équilibrage de charge. |
> | Microsoft.Network/networkInterfaces/read | Obtient une définition d’interface réseau.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Se connecter à la machine virtuelle comme utilisateur normal |

## <a name="web-plan-contributor"></a>Collaborateur de plan web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des plans web pour des sites web, mais pas d’y accéder. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Web/serverFarms/* | Créer et gérer des batteries de serveurs |

## <a name="website-contributor"></a>Collaborateur de site web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des sites web (pas des plans web), mais pas d’y accéder. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Insights/components/* | Créer et gérer les composants Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Web/certificates/* | Créer et gérer les certificats de site web |
> | Microsoft.Web/listSitesAssignedToHostName/read | Récupère les noms de sites affectés à un nom d’hôte. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Récupère les propriétés d’un plan App Service. |
> | Microsoft.Web/sites/* | Créer et gérer des sites web (la création de sites nécessite également des autorisations d’écriture pour le plan App Service associé) |

## <a name="next-steps"></a>Étapes suivantes

- [Rôles personnalisés](custom-roles.md)
- [Gérer les attributions de rôle à l’aide du portail Azure](role-assignments-portal.md)
- [Autorisations dans Azure Security Center](../security-center/security-center-permissions.md)
