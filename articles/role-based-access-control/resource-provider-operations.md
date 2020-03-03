---
title: Opérations du fournisseur de ressources Azure Resource Manager | Microsoft Docs
description: Répertorie les opérations disponibles pour les fournisseurs de ressources Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6fb1716d93b01bcf89ce6e530bb3c18e418bdcfb
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562001"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Opérations du fournisseur de ressources Azure Resource Manager

Cet article répertorie les opérations disponibles pour chaque fournisseur de ressources Azure Resource Manager. Ces opérations peuvent être utilisées dans des [rôles personnalisés](custom-roles.md) pour fournir un [contrôle d’accès en fonction du rôle (RBAC)](overview.md) granulaire aux ressources dans Azure. Les chaînes d’opération suivent ce format : `{Company}.{ProviderName}/{resourceType}/{action}`. Pour obtenir la liste des correspondances entre les espaces de noms de fournisseur de ressources et les services Azure, consultez [Fournisseur de ressources correspondant au service](../azure-resource-manager/management/azure-services-resource-providers.md).

Les opérations du fournisseur de ressources évoluent sans cesse. Pour obtenir les dernières opérations, utilisez [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) ou [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.AAD/domainServices/delete | Supprimer le service de domaine |
> | Action | Microsoft.AAD/domainServices/oucontainer/delete | Supprimer le conteneur d’unité d’organisation |
> | Action | Microsoft.AAD/domainServices/oucontainer/read | Lire les conteneurs d’unité d’organisation |
> | Action | Microsoft.AAD/domainServices/oucontainer/write | Écrire le conteneur d’unité d’organisation |
> | Action | Microsoft.AAD/domainServices/read | Lire les services de domaine |
> | Action | Microsoft.AAD/domainServices/write | Écrire le service de domaine |
> | Action | Microsoft.AAD/locations/operationresults/read |  |
> | Action | Microsoft.AAD/Operations/read |  |
> | Action | Microsoft.AAD/register/action | Inscrire le service de domaine |
> | Action | Microsoft.AAD/unregister/action | Annule l’inscription du service de domaine |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | microsoft.aadiam/diagnosticsettings/delete | Désactive un paramètre de diagnostic |
> | Action | microsoft.aadiam/diagnosticsettings/read | Lit un paramètre de diagnostic |
> | Action | microsoft.aadiam/diagnosticsettings/write | Écrit un paramètre de diagnostic |
> | Action | microsoft.aadiam/diagnosticsettingscategories/read | Lit les catégories d’un paramètre de diagnostic |
> | Action | microsoft.aadiam/metricDefinitions/read | Lit les définitions des métriques au niveau du locataire |
> | Action | microsoft.aadiam/metrics/read | Lit les métriques au niveau du locataire |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Addons/operations/read | Obtient les opérations du fournisseur de ressources, qui sont prises en charge. |
> | Action | Microsoft.Addons/register/action | Enregistre l’abonnement spécifié avec Microsoft.Addons |
> | Action | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Répertorie les informations de plan de support en cours pour l’abonnement spécifié. |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Supprime le plan de support Canonical spécifié |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/read | Obtient l’état du plan de support Canonical spécifié. |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/write | Ajoute le type de plan de support Canonical spécifié. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.ADHybridHealthService/addsservices/action | Crée une forêt pour le locataire. |
> | Action | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Obtient tous les serveurs pour le nom de service spécifié. |
> | Action | Microsoft.ADHybridHealthService/addsservices/alerts/read | Obtient les détails de l’alerte pour la forêt, comme l’ID d’alerte, la date de déclenchement de l’alerte, la dernière alerte détectée, la description de l’alerte, la dernière alerte mise à jour, le niveau d’alerte, l’état de l’alerte, les liens de résolution des problèmes relatifs à l’alerte, etc. |
> | Action | Microsoft.ADHybridHealthService/addsservices/configuration/read | Obtient la configuration de service pour la forêt. Exemple : nom de la forêt, niveau fonctionnel, rôle FSMO du maître d’opérations des noms de domaine, rôle FSMO du contrôleur de schéma, etc. |
> | Action | Microsoft.ADHybridHealthService/addsservices/delete | Supprime un service et ses serveurs, ainsi que les données de contrôle d’intégrité. |
> | Action | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Obtient les détails des domaines et des sites de la forêt. Exemple : état d’intégrité, alertes actives, alertes résolues, propriétés telles que Niveau fonctionnel du domaine, Forêt, Maître d’infrastructure, Contrôleur de domaine principal, Maître RID, etc.  |
> | Action | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Obtient le paramètre de préférence utilisateur pour la forêt.<br>Exemple : MetricCounterName, comme ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes ou ldapsearches.<br>Paramètres des graphiques de l’interface utilisateur, etc. |
> | Action | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Obtient le résumé de la forêt donnée : nom de la forêt, nombre de domaines dans cette forêt, nombre de sites, détails des sites, etc. |
> | Action | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Obtient la liste des métriques prises en charge pour un service donné.<br>Exemple : verrouillages de comptes extranet, nombre total de requêtes ayant échoué, demandes de jeton en attente (proxy), demandes de jetons par seconde, etc. pour le service ADFS.<br>Authentifications NTLM par seconde, liaisons LDAP réussies par seconde, durée de liaison LDAP, nombre de threads LDAP actifs, authentifications Kerberos par seconde, nombre total de threads ATQ, etc. pour ADDomainService.<br>Latence du profil d’exécution, connexions TCP établies, octets privés de l’agent Insights, statistiques d’exportation vers Azure AD pour le service ADSync. |
> | Action | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Pour un service donné, cette API obtient les informations relatives aux métriques.<br>Par exemple, cette API permet d’obtenir des informations concernant : les verrouillages de comptes extranet, le nombre total de requêtes ayant échoué, les demandes de jeton en attente (proxy), les demandes de jetons par seconde, etc. pour le service ADFederation.<br>Authentifications NTLM par seconde, liaisons LDAP réussies par seconde, durée de liaison LDAP, nombre de threads LDAP actifs, authentifications Kerberos par seconde, nombre total de threads ATQ, etc. pour le service ADDomain.<br>Latence du profil d’exécution, connexions TCP établies, octets privés de l’agent Insights, statistiques d’exportation vers Azure AD pour le service de synchronisation. |
> | Action | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Cette API permet d’obtenir la liste de tous les ADDomainServices embarqués pour un locataire premium. |
> | Action | Microsoft.ADHybridHealthService/addsservices/read | Obtient les détails de service relatifs au nom de service spécifié. |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Obtient les détails de réplication de tous les serveurs pour le nom de service spécifié. |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Obtient le nombre de contrôleurs de domaine et leurs erreurs de réplication, le cas échéant. |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Obtient la liste complète des contrôleurs de domaine ainsi que les détails de la réplication pour la forêt donnée. |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Ajoute une instance de serveur au service. |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Pendant l’enregistrement du serveur d’ADDomainService, cette API est appelée pour obtenir les informations d’identification relatives à l’intégration de nouveaux serveurs. |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Supprime un serveur pour un service et un locataire donnés. |
> | Action | Microsoft.ADHybridHealthService/addsservices/write | Crée ou met à jour l’instance ADDomainService pour le locataire. |
> | Action | Microsoft.ADHybridHealthService/configuration/action | Met à jour la configuration du client. |
> | Action | Microsoft.ADHybridHealthService/configuration/read | Lit la configuration du client. |
> | Action | Microsoft.ADHybridHealthService/configuration/write | Crée une configuration de client. |
> | Action | Microsoft.ADHybridHealthService/logs/contents/read | Obtient le contenu des journaux d’activité d’installation et d’enregistrement de l’agent, qui sont stockés dans le blob. |
> | Action | Microsoft.ADHybridHealthService/logs/read | Obtient les journaux d’activité d’installation et d’enregistrement de l’agent pour le locataire. |
> | Action | Microsoft.ADHybridHealthService/operations/read | Obtient la liste des opérations prises en charge par le système. |
> | Action | Microsoft.ADHybridHealthService/register/action | Enregistre le fournisseur de ressources du service de contrôle d’intégrité ADHybrid et permet la création de la ressource du service de contrôle d’intégrité ADHybrid. |
> | Action | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Obtient la liste des régions disponibles, utilisées par DevOps pour prendre en charge les incidents clients. |
> | Action | Microsoft.ADHybridHealthService/reports/badpassword/read | Obtient la liste des tentatives incorrectes de mot de passe pour tous les utilisateurs dans Active Directory Federation Service. |
> | Action | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Obtient l’URI SAS d’un blob contenant l’état et le résultat final du travail de rapport qui vient d’être ajouté en file d’attente, pour la fréquence de tentatives incorrectes de nom d’utilisateur/mot de passe par ID d’utilisateur et par adresse IP par jour pour un locataire donné. |
> | Action | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Obtient la liste des locataires autorisés par DevOps. Sert généralement au support technique. |
> | Action | Microsoft.ADHybridHealthService/reports/isdevops/read | Obtient une valeur indiquant si le locataire est autorisé par DevOps ou non. |
> | Action | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Met à jour l’ID utilisateur (ID objet) en fonction du locataire DevOps sélectionné. |
> | Action | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Obtient le déploiement sélectionné pour le locataire donné. |
> | Action | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Pour un ID de locataire donné, obtient l’emplacement de stockage du locataire. |
> | Action | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Obtient l’emplacement géographique des données. |
> | Action | Microsoft.ADHybridHealthService/services/action | Met à jour une instance de service dans le client. |
> | Action | Microsoft.ADHybridHealthService/services/alerts/read | Lit les alertes pour un service. |
> | Action | Microsoft.ADHybridHealthService/services/alerts/read | Lit les alertes pour un service. |
> | Action | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Pour un nom de fonctionnalité donné, vérifie si un service dispose de tous les éléments requis afin d’utiliser cette fonctionnalité. |
> | Action | Microsoft.ADHybridHealthService/services/delete | Supprime une instance de service dans le client. |
> | Action | Microsoft.ADHybridHealthService/services/exporterrors/read | Obtient les erreurs d’exportation pour un service de synchronisation donné. |
> | Action | Microsoft.ADHybridHealthService/services/exportstatus/read | Obtient l’état d’exportation pour un service donné. |
> | Action | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Obtient les commentaires d’alerte pour un service et un serveur donnés. |
> | Action | Microsoft.ADHybridHealthService/services/metricmetadata/read | Obtient la liste des métriques prises en charge pour un service donné.<br>Exemple : verrouillages de comptes extranet, nombre total de requêtes ayant échoué, demandes de jeton en attente (proxy), demandes de jetons par seconde, etc. pour le service ADFS.<br>Authentifications NTLM par seconde, liaisons LDAP réussies par seconde, durée de liaison LDAP, nombre de threads LDAP actifs, authentifications Kerberos par seconde, nombre total de threads ATQ, etc. pour ADDomainService.<br>Latence du profil d’exécution, connexions TCP établies, octets privés de l’agent Insights, statistiques d’exportation vers Azure AD pour le service ADSync. |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Pour un service donné, cette API obtient la moyenne des métriques.<br>Par exemple, cette API permet d’obtenir des informations concernant : les verrouillages de comptes extranet, le nombre total de requêtes ayant échoué, les demandes de jeton en attente (proxy), les demandes de jetons par seconde, etc. pour le service ADFederation.<br>Authentifications NTLM par seconde, liaisons LDAP réussies par seconde, durée de liaison LDAP, nombre de threads LDAP actifs, authentifications Kerberos par seconde, nombre total de threads ATQ, etc. pour le service ADDomain.<br>Latence du profil d’exécution, connexions TCP établies, octets privés de l’agent Insights, statistiques d’exportation vers Azure AD pour le service de synchronisation. |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/read | Pour un service donné, cette API obtient les informations relatives aux métriques.<br>Par exemple, cette API permet d’obtenir des informations concernant : les verrouillages de comptes extranet, le nombre total de requêtes ayant échoué, les demandes de jeton en attente (proxy), les demandes de jetons par seconde, etc. pour le service ADFederation.<br>Authentifications NTLM par seconde, liaisons LDAP réussies par seconde, durée de liaison LDAP, nombre de threads LDAP actifs, authentifications Kerberos par seconde, nombre total de threads ATQ, etc. pour le service ADDomain.<br>Latence du profil d’exécution, connexions TCP établies, octets privés de l’agent Insights, statistiques d’exportation vers Azure AD pour le service de synchronisation. |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Pour un service donné, cette API obtient la vue agrégée des métriques.<br>Par exemple, cette API permet d’obtenir des informations concernant : les verrouillages de comptes extranet, le nombre total de requêtes ayant échoué, les demandes de jeton en attente (proxy), les demandes de jetons par seconde, etc. pour le service ADFederation.<br>Authentifications NTLM par seconde, liaisons LDAP réussies par seconde, durée de liaison LDAP, nombre de threads LDAP actifs, authentifications Kerberos par seconde, nombre total de threads ATQ, etc. pour le service ADDomain.<br>Latence du profil d’exécution, connexions TCP établies, octets privés de l’agent Insights, statistiques d’exportation vers Azure AD pour le service de synchronisation. |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Ajoute ou met à jour la configuration de surveillance d’un service. |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Obtient les configurations de surveillance d’un service donné. |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Ajoute ou met à jour les configurations de surveillance d’un service. |
> | Action | Microsoft.ADHybridHealthService/services/premiumcheck/read | Cette API permet d’obtenir la liste de tous les services embarqués pour un locataire premium. |
> | Action | Microsoft.ADHybridHealthService/services/read | Lit les instances de service dans le client. |
> | Action | Microsoft.ADHybridHealthService/services/reports/blobUris/read | Obtient tous les URI de rapports d’adresse IP risquée pour les 7 derniers jours. |
> | Action | Microsoft.ADHybridHealthService/services/reports/details/read | Obtient les rapports des 50 principaux utilisateurs ayant effectué des erreurs de mot de passe depuis les 7 derniers jours |
> | Action | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Génère un rapport d’adresse IP risquée et retourne un URI qui pointe dessus. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/action | Crée une instance de serveur dans le service. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Affiche les alertes correspondant à un serveur. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Pendant l’enregistrement du serveur, cette API est appelée pour obtenir les informations d’identification relatives à l’intégration de nouveaux serveurs. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Pour un serveur donné, cette API obtient la liste des types de données que les serveurs sont en train de charger, ainsi que l’heure de chaque chargement le plus récent. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/delete | Supprime une instance de serveur dans le service. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Obtient les informations détaillées relatives à l’erreur d’exportation de synchronisation pour un service de synchronisation donné. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Pour un service donné, cette API obtient les informations relatives aux métriques.<br>Par exemple, cette API permet d’obtenir des informations concernant : les verrouillages de comptes extranet, le nombre total de requêtes ayant échoué, les demandes de jeton en attente (proxy), les demandes de jetons par seconde, etc. pour le service ADFederation.<br>Authentifications NTLM par seconde, liaisons LDAP réussies par seconde, durée de liaison LDAP, nombre de threads LDAP actifs, authentifications Kerberos par seconde, nombre total de threads ATQ, etc. pour le service ADDomain.<br>Latence du profil d’exécution, connexions TCP établies, octets privés de l’agent Insights, statistiques d’exportation vers Azure AD pour le service de synchronisation. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | Obtient la liste des connecteurs et des noms de profil d’exécution pour le service et le membre de service donnés. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/read | Affiche l’instance de serveur du service. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Obtient la configuration de service pour un locataire donné. |
> | Action | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Obtient l’état de la liste verte des fonctionnalités pour un locataire donné. |
> | Action | Microsoft.ADHybridHealthService/services/write | Crée une instance de service dans le client. |
> | Action | Microsoft.ADHybridHealthService/unregister/action | Annule l’enregistrement de l’abonnement du fournisseur de ressources du service de contrôle d’intégrité ADHybrid. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Advisor/configurations/read | Obtenir des configurations |
> | Action | Microsoft.Advisor/configurations/write | Crée/met à jour la configuration |
> | Action | Microsoft.Advisor/generateRecommendations/action | Obtient l’état de génération de recommandations |
> | Action | Microsoft.Advisor/generateRecommendations/read | Obtient l’état de génération de recommandations |
> | Action | Microsoft.Advisor/metadata/read | Get Metadata |
> | Action | Microsoft.Advisor/operations/read | Obtient les opérations pour le Microsoft Advisor |
> | Action | Microsoft.Advisor/recommendations/available/action | La nouvelle recommandation est disponible dans Microsoft Advisor |
> | Action | Microsoft.Advisor/recommendations/read | Lit les recommandations |
> | Action | Microsoft.Advisor/recommendations/suppressions/delete | Supprime la suppression |
> | Action | Microsoft.Advisor/recommendations/suppressions/read | Obtient les suppressions |
> | Action | Microsoft.Advisor/recommendations/suppressions/write | Crée/met à jour des suppressions |
> | Action | Microsoft.Advisor/register/action | Inscrit l’abonnement pour Microsoft Advisor |
> | Action | Microsoft.Advisor/suppressions/delete | Supprime la suppression |
> | Action | Microsoft.Advisor/suppressions/read | Obtient les suppressions |
> | Action | Microsoft.Advisor/suppressions/write | Crée/met à jour des suppressions |
> | Action | Microsoft.Advisor/unregister/action | Désinscrit l’abonnement pour le Microsoft Advisor |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.AlertsManagement/actionRules/delete | Supprimer une règle d’action dans un abonnement donné. |
> | Action | Microsoft.AlertsManagement/actionRules/read | Obtenir toutes les règles d’action pour les filtres d’entrée. |
> | Action | Microsoft.AlertsManagement/actionRules/write | Créer ou mettre à jour une règle d’action dans un abonnement donné. |
> | Action | Microsoft.AlertsManagement/alerts/changestate/action | Modifie l’état de l’alerte. |
> | Action | Microsoft.AlertsManagement/alerts/diagnostics/read | Obtenir tous les diagnostics pour l’alerte |
> | Action | Microsoft.AlertsManagement/alerts/history/read | Obtenir l’historique de l’alerte |
> | Action | Microsoft.AlertsManagement/alerts/read | Obtient toutes les alertes pour les filtres d’entrée |
> | Action | Microsoft.AlertsManagement/alertsList/read | Obtenir toutes les alertes des filtres d’entrée dans des abonnements. |
> | Action | Microsoft.AlertsManagement/alertsMetaData/read | Obtenez les métadonnées d’alertes pour le paramètre d’entrée. |
> | Action | Microsoft.AlertsManagement/alertsSummary/read | Obtient le résumé des alertes |
> | Action | Microsoft.AlertsManagement/alertsSummaryList/read | Obtenir le résumé des alertes dans les abonnements. |
> | Action | Microsoft.AlertsManagement/Operations/read | Lit les opérations fournies |
> | Action | Microsoft.AlertsManagement/register/action | Inscrit l’abonnement pour la Gestion des alertes Microsoft |
> | Action | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | Supprimer la règle d’alerte Smart Detector dans un abonnement donné |
> | Action | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Obtenir toutes les règles d’alerte de Smart Detector pour les filtres d’entrée |
> | Action | Microsoft.AlertsManagement/smartDetectorAlertRules/write | Créer ou mettre à jour une règle d’alerte Smart Detector dans un abonnement donné |
> | Action | Microsoft.AlertsManagement/smartGroups/changestate/action | Modifie l’état du groupe intelligent |
> | Action | Microsoft.AlertsManagement/smartGroups/history/read | Obtenir l’historique du groupe intelligent |
> | Action | Microsoft.AlertsManagement/smartGroups/read | Obtient tous les groupes intelligents pour les filtres d’entrée |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Vérifie que le nom d’Analysis Server donné est valide et non utilisé. |
> | Action | Microsoft.AnalysisServices/locations/operationresults/read | Récupère les informations du résultat de l’opération spécifiée |
> | Action | Microsoft.AnalysisServices/locations/operationstatuses/read | Récupère les informations de l’état de l’opération spécifiée |
> | Action | Microsoft.AnalysisServices/operations/read | Récupère les informations d’opérations |
> | Action | Microsoft.AnalysisServices/register/action | Inscrit le fournisseur de ressources Analysis Services |
> | Action | Microsoft.AnalysisServices/servers/delete | Supprime l’Analysis Server. |
> | Action | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Affiche l’état de la passerelle associée au serveur |
> | Action | Microsoft.AnalysisServices/servers/read | Récupère les informations de l’Analysis Server spécifié. |
> | Action | Microsoft.AnalysisServices/servers/resume/action | Reprend l’Analysis Server. |
> | Action | Microsoft.AnalysisServices/servers/skus/read | Récupère les informations de référence (SKU) disponibles pour le serveur |
> | Action | Microsoft.AnalysisServices/servers/suspend/action | Suspend l’Analysis Server. |
> | Action | Microsoft.AnalysisServices/servers/write | Crée ou met à jour l’Analysis Server spécifié. |
> | Action | Microsoft.AnalysisServices/skus/read | Récupère les informations de références (SKU) |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.ApiManagement/checkNameAvailability/read | Vérifie si le nom du service indiqué est disponible |
> | Action | Microsoft.ApiManagement/operations/read | Lit toutes les opérations API disponibles pour la ressource Microsoft.ApiManagement |
> | Action | Microsoft.ApiManagement/register/action | Inscrire l’abonnement pour le fournisseur de ressources Microsoft.ApiManagement |
> | Action | Microsoft.ApiManagement/reports/read | Obtient des rapports agrégés par période de temps, région géographique, développeur, produit, API, opération, abonnement et demande. |
> | Action | Microsoft.ApiManagement/service/apis/delete | Supprime l’API spécifiée de l’instance de service de gestion des API. |
> | Action | Microsoft.ApiManagement/service/apis/diagnostics/delete | Supprime le diagnostic spécifié d’une API. |
> | Action | Microsoft.ApiManagement/service/apis/diagnostics/read | Répertorie tous les diagnostics d’une API. ou obtient les détails du diagnostic pour une API spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/apis/diagnostics/write | Crée un diagnostic pour une API ou met à jour celui qui existe. ou met à jour les détails du diagnostic pour une API spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Supprime le commentaire spécifié d’un problème. |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/read | Répertorie toutes les pièces jointes pour le problème associé à l’API spécifiée. ou obtient les détails de la pièce jointe du problème pour une API spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/write | Crée une nouvelle pièce jointe pour le problème dans une API ou la met à jour. |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/delete | Supprime le commentaire spécifié d’un problème. |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/read | Répertorie tous les commentaires pour le problème associé à l’API spécifiée. ou obtient les détails du commentaire sur le problème pour une API spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/write | Crée un nouveau commentaire pour le problème dans une API ou le met jour. |
> | Action | Microsoft.ApiManagement/service/apis/issues/delete | Supprime le problème spécifié d’une API. |
> | Action | Microsoft.ApiManagement/service/apis/issues/read | Répertorie tous les problèmes associés à l’API spécifiée. ou obtient les détails du problème pour une API spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/apis/issues/write | Crée un problème pour une API ou met à jour celui qui existe. ou met à jour un problème existant pour une API. |
> | Action | Microsoft.ApiManagement/service/apis/operations/delete | Supprime l’opération spécifiée dans l’API. |
> | Action | Microsoft.ApiManagement/service/apis/operations/policies/delete | Supprime la configuration de stratégie de l’opération d’API. |
> | Action | Microsoft.ApiManagement/service/apis/operations/policies/read | Obtient la liste de configurations de stratégie au niveau de l’opération d’API. ou obtient la configuration de stratégie au niveau de l’opération d’API. |
> | Action | Microsoft.ApiManagement/service/apis/operations/policies/write | Crée ou met à jour la configuration de stratégie pour le niveau de l’opération d’API. |
> | Action | Microsoft.ApiManagement/service/apis/operations/policy/delete | Supprimer la configuration de stratégie au niveau de l’opération |
> | Action | Microsoft.ApiManagement/service/apis/operations/policy/read | Obtenir la configuration de stratégie au niveau de l’opération |
> | Action | Microsoft.ApiManagement/service/apis/operations/policy/write | Créer la configuration de stratégie au niveau de l’opération |
> | Action | Microsoft.ApiManagement/service/apis/operations/read | Répertorie une collection des opérations pour l’API spécifiée. ou obtient les détails de l’opération d’API spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/delete | Détacher la balise de l’opération. |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/read | Répertorie toutes les balises associées à l’opération. ou obtenez la balise associée à l’opération. |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/write | Affectez la balise à l’opération. |
> | Action | Microsoft.ApiManagement/service/apis/operations/write | Crée une nouvelle opération dans l’API ou la met à jour. ou met à jour les détails de l’opération dans l’API spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/apis/operationsByTags/read | Répertorie un ensemble d’opérations associées aux balises. |
> | Action | Microsoft.ApiManagement/service/apis/policies/delete | Supprime la configuration de stratégie de l’API. |
> | Action | Microsoft.ApiManagement/service/apis/policies/read | Obtient la configuration de stratégie au niveau de l’API. ou obtient la configuration de stratégie au niveau de l’API. |
> | Action | Microsoft.ApiManagement/service/apis/policies/write | Crée ou met à jour la configuration de stratégie pour l’API. |
> | Action | Microsoft.ApiManagement/service/apis/policy/delete | Supprimer la configuration de stratégie au niveau de l’API |
> | Action | Microsoft.ApiManagement/service/apis/policy/read | Obtenir la configuration de stratégie au niveau de l’API |
> | Action | Microsoft.ApiManagement/service/apis/policy/write | Créer la configuration de stratégie au niveau de l’API |
> | Action | Microsoft.ApiManagement/service/apis/products/read | Répertorie tous les produits dont l’API fait partie. |
> | Action | Microsoft.ApiManagement/service/apis/read | Répertorie toutes les API de l’instance de service de gestion des API. ou obtient les détails de l’API spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/apis/releases/delete | Supprime toutes les versions de l’API ou la version spécifiée dans l’API. |
> | Action | Microsoft.ApiManagement/service/apis/releases/read | Répertorie toutes les versions d’une API.<br>Une version d’API est créée lorsqu’une révision d’API est rendue actuelle.<br>Les versions sont également utilisées pour restaurer une révision précédente.<br>Les résultats sont paginés et peuvent être contraints par les paramètres $top et $skip.<br>ou renvoie les détails d’une version de l’API. |
> | Action | Microsoft.ApiManagement/service/apis/releases/write | Crée une nouvelle version de l’API. ou met à jour les détails de la version de l’API spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/apis/revisions/delete | Supprime toutes les révisions d’une API |
> | Action | Microsoft.ApiManagement/service/apis/revisions/read | Répertorie toutes les révisions d’une API. |
> | Action | Microsoft.ApiManagement/service/apis/schemas/delete | Supprime la configuration du schéma de l’API. |
> | Action | Microsoft.ApiManagement/service/apis/schemas/read | Obtient la configuration du schéma au niveau de l’API. ou obtient la configuration du schéma au niveau de l’API. |
> | Action | Microsoft.ApiManagement/service/apis/schemas/write | Crée ou met à jour la configuration de schéma pour l’API. |
> | Action | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Supprimer la description de balise pour l’API. |
> | Action | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Répertorie toutes les descriptions de balises dans la portée de l’API. Modèle similaire à swagger - tagDescription est défini au niveau de l’API, mais la balise peut être affectée à la description Operations ou Get Tag de l’API |
> | Action | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Crée/met à jour une description de balise dans la portée de l’API. |
> | Action | Microsoft.ApiManagement/service/apis/tags/delete | Détache la balise de l’API. |
> | Action | Microsoft.ApiManagement/service/apis/tags/read | Répertorie toutes les balises associées à l’API. ou obtient la balise associée à l’API. |
> | Action | Microsoft.ApiManagement/service/apis/tags/write | Assigne une balise à l’API. |
> | Action | Microsoft.ApiManagement/service/apis/write | Crée de nouvelles API ou met à jour celles spécifiées pour l’instance de service de gestion des API. ou met à jour l’API spécifiée de l’instance de service de gestion des API. |
> | Action | Microsoft.ApiManagement/service/apisByTags/read | Répertorie un ensemble d’API associées aux balises. |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/delete | Supprime un ensemble de versions d’API spécifique. |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/read | Répertorie une collection d’ensembles de versions d’API dans l’instance de service spécifiée. ou obtient les détails de l’ensemble de versions d’API spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Obtient la liste des entités de version |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/write | Crée ou met à jour un ensemble de versions d’API. ou met à jour les détails de l’ensemble de versions d’API spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Met à jour les ressources Microsoft.ApiManagement exécutées dans le réseau virtuel pour sélectionner les paramètres réseau mis à jour. |
> | Action | Microsoft.ApiManagement/service/authorizationServers/delete | Supprime une instance de serveur d’autorisation spécifique. |
> | Action | Microsoft.ApiManagement/service/authorizationServers/listSecrets/action | Obtient les secrets pour le serveur d’autorisation. |
> | Action | Microsoft.ApiManagement/service/authorizationServers/read | Répertorie un ensemble de serveurs d’autorisation définis dans une instance de service. Ou obtient les détails du serveur d’autorisation sans secrets. |
> | Action | Microsoft.ApiManagement/service/authorizationServers/write | Crée un nouveau serveur d’autorisation ou met à jour un serveur d’autorisation existant. ou met à jour les détails du serveur d’autorisation spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/backends/delete | Supprime le back-end spécifié. |
> | Action | Microsoft.ApiManagement/service/backends/read | Répertorie un ensemble de serveurs principaux dans l’instance de service spécifié. ou obtient les détails du serveur principal spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/backends/reconnect/action | Indique au proxy APIM de créer une nouvelle connexion au serveur principal après le délai d’attente spécifié. Si aucun délai d’attente n’a été spécifié, le délai de 2 minutes est utilisé. |
> | Action | Microsoft.ApiManagement/service/backends/write | Crée ou met à jour un back-end. ou met à jour un serveur principal existant. |
> | Action | Microsoft.ApiManagement/service/backup/action | Sauvegarder le service Gestion des API dans le conteneur spécifié dans un compte de stockage fourni par l’utilisateur |
> | Action | Microsoft.ApiManagement/service/caches/delete | Supprime un cache spécifique. |
> | Action | Microsoft.ApiManagement/service/caches/read | Répertorie une collection de tous les caches externes de l’instance de service spécifiée. ou obtient les détails du cache spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/caches/write | Crée ou met à jour un cache externe à utiliser dans une instance de gestion des API. ou met à jour les détails du cache spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/certificates/delete | Supprime un certificat spécifique. |
> | Action | Microsoft.ApiManagement/service/certificates/read | Répertorie une collection de tous les certificats dans l’instance de service spécifiée. ou obtient les détails du certificat spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/certificates/write | Crée ou met à jour le certificat utilisé pour l’authentification avec le serveur principal. |
> | Action | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Supprime l’élément de contenu spécifié. |
> | Action | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Retourne la liste ou les détails des éléments de contenu |
> | Action | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Crée un élément de contenu ou met à jour un élément de contenu spécifié |
> | Action | Microsoft.ApiManagement/service/contentTypes/read | Retourne la liste des types de contenu |
> | Action | Microsoft.ApiManagement/service/delete | Supprimer l’instance du service Gestion des API |
> | Action | Microsoft.ApiManagement/service/diagnostics/delete | Supprime le diagnostic spécifié. |
> | Action | Microsoft.ApiManagement/service/diagnostics/read | Répertorie tous les diagnostics de l’instance de service de gestion des API. ou obtient les détails du diagnostic spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/diagnostics/write | Crée un diagnostic ou met à jour celui qui existe. ou met à jour les détails du diagnostic spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/gateways/action | Récupère la configuration de la passerelle. ou met à jour la pulsation de la passerelle. |
> | Action | Microsoft.ApiManagement/service/gateways/apis/delete | Supprime l’API spécifiée de la passerelle spécifié. |
> | Action | Microsoft.ApiManagement/service/gateways/apis/read | Répertorie une collection des API associées à une passerelle. |
> | Action | Microsoft.ApiManagement/service/gateways/apis/write | Ajoute une API à la passerelle spécifiée. |
> | Action | Microsoft.ApiManagement/service/gateways/delete | Supprime une passerelle spécifique. |
> | Action | Microsoft.ApiManagement/service/gateways/hostnameConfigurations/read | Répertorie la collection de configurations de nom d’hôte pour la passerelle spécifiée. |
> | Action | Microsoft.ApiManagement/service/gateways/keys/action | Récupère les clés de passerelle. |
> | Action | Microsoft.ApiManagement/service/gateways/read | Répertorie une collection de passerelles inscrites auprès de l’instance de service. ou obtient les détails de la passerelle spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/gateways/regeneratePrimaryKey/action | Régénère la clé de passerelle principale, ce qui invalide tous les jetons créés avec celle-ci. |
> | Action | Microsoft.ApiManagement/service/gateways/regenerateSecondaryKey/action | Régénère la clé de passerelle secondaire, ce qui invalide tous les jetons créés avec celle-ci. |
> | Action | Microsoft.ApiManagement/service/gateways/token/action | Obtient le jeton d’autorisation d’accès partagé pour la passerelle. |
> | Action | Microsoft.ApiManagement/service/gateways/write | Crée ou met à jour une passerelle à utiliser dans une instance de gestion des API. ou met à jour les détails de la passerelle spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/getssotoken/action | Obtient le jeton d’authentification unique qui peut être utilisé pour se connecter au portail hérité du service Gestion des API en tant qu’administrateur |
> | Action | Microsoft.ApiManagement/service/groups/delete | Supprime un groupe spécifique de l’instance de service de gestion des API. |
> | Action | Microsoft.ApiManagement/service/groups/read | Répertorie une collection de groupes définis dans une instance de service. ou obtient les détails du groupe spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/groups/users/delete | Supprimer un utilisateur existant d’un groupe existant. |
> | Action | Microsoft.ApiManagement/service/groups/users/read | Répertorie une collection d’entités utilisateur associées au groupe. |
> | Action | Microsoft.ApiManagement/service/groups/users/write | Ajouter un utilisateur existant à un groupe existant |
> | Action | Microsoft.ApiManagement/service/groups/write | Crée ou met à jour un groupe. ou met à jour ou les détails du groupe spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/identityProviders/delete | Supprime la configuration du fournisseur d’identité spécifié. |
> | Action | Microsoft.ApiManagement/service/identityProviders/listSecrets/action | Obtient les secrets du fournisseur d’identité. |
> | Action | Microsoft.ApiManagement/service/identityProviders/read | Répertorie un ensemble de fournisseurs d’identité configurés dans l’instance de service spécifié. Ou obtient les détails de configuration du fournisseur d’identité sans secrets. |
> | Action | Microsoft.ApiManagement/service/identityProviders/write | Crée ou met à jour la configuration du fournisseur d’identité. ou met à jour une configuration de fournisseur d’identité existante. |
> | Action | Microsoft.ApiManagement/service/issues/read | Répertorie l’ensemble des problèmes dans l’instance de service spécifiée. ou obtient les détails du problème de gestion des API |
> | Action | Microsoft.ApiManagement/service/locations/networkstatus/read | Obtient l’état de l’accès réseau des ressources dont le service dépend dans l’emplacement |
> | Action | Microsoft.ApiManagement/service/loggers/delete | Supprime l’enregistreur spécifié. |
> | Action | Microsoft.ApiManagement/service/loggers/read | Répertorie une collection d’enregistreurs d’événements dans l’instance de service spécifié. ou obtient les détails de l’enregistreur d’événements spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/loggers/write | Crée ou met à jour un enregistreur. ou met à jour un enregistreur d’événements existant. |
> | Action | Microsoft.ApiManagement/service/managedeployments/action | Modifier une référence/unité, ajouter/supprimer des déploiements régionaux du service Gestion des API |
> | Action | Microsoft.ApiManagement/service/namedValues/delete | Supprime une valeur nommée spécifique de l’instance de service Gestion des API. |
> | Action | Microsoft.ApiManagement/service/namedValues/listSecrets/action | Obtient les secrets de la valeur nommée spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/namedValues/read | Répertorie une collection de valeurs nommées définies au sein d’une instance de service. Ou obtient les détails de la valeur nommée spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/namedValues/write | Crée ou met à jour la valeur nommée. Ou met à jour la valeur nommée spécifique. |
> | Action | Microsoft.ApiManagement/service/networkstatus/read | Obtient l’état de l’accès réseau des ressources dont le service dépend |
> | Action | Microsoft.ApiManagement/service/notifications/action | Envoie une notification à un utilisateur spécifié |
> | Action | Microsoft.ApiManagement/service/notifications/read | Répertorie une collection de propriétés définies au sein d’une instance de service. ou obtient les détails de la notification spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Supprime l’e-mail de la liste de la notification. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Obtient la liste des e-mails de destinataires abonnés à une notification. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Ajoute l’adresse e-mail à la liste de destinataires pour la notification. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Supprime l’utilisateur de gestion des API de la liste de la notification. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Obtient la liste d’utilisateurs destinataires abonnés à la notification. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Ajoute l’utilisateur de gestion des API à la liste de destinataires pour la notification. |
> | Action | Microsoft.ApiManagement/service/notifications/write | Crée ou met à jour une notification de l’éditeur Gestion des API. |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/delete | Supprime un fournisseur OpenID Connect spécifique de l’instance de service de gestion des API. |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/listSecrets/action | Ou obtient les secrets du fournisseur OpenID Connect spécifique. |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/read | Répertorie tous les fournisseurs OpenId Connect. Ou obtient un fournisseur OpenID Connect spécifique sans secrets. |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/write | Crée ou met à jour le fournisseur OpenID Connect. ou met à jour le fournisseur OpenID Connect spécifique. |
> | Action | Microsoft.ApiManagement/service/operationresults/read | Obtient l’état actuel d’une opération longue |
> | Action | Microsoft.ApiManagement/service/policies/delete | Supprime la configuration de la stratégie globale du service de gestion des API. |
> | Action | Microsoft.ApiManagement/service/policies/read | Répertorie toutes les définitions de stratégie globale du service de gestion des API. ou obtient la définition de stratégie globale du service de gestion des API. |
> | Action | Microsoft.ApiManagement/service/policies/write | Crée ou met à jour la configuration de la stratégie globale du service de gestion des API. |
> | Action | Microsoft.ApiManagement/service/policy/delete | Supprimer la configuration de stratégie au niveau du locataire |
> | Action | Microsoft.ApiManagement/service/policy/read | Obtenir la configuration de stratégie au niveau du locataire |
> | Action | Microsoft.ApiManagement/service/policy/write | Créer la configuration de stratégie au niveau du locataire |
> | Action | Microsoft.ApiManagement/service/policyDescriptions/read | Liste toutes les descriptions de stratégies. |
> | Action | Microsoft.ApiManagement/service/policySnippets/read | Répertorie tous les extraits de code de stratégie. |
> | Action | Microsoft.ApiManagement/service/portalsettings/listSecrets/action | Obtient la clé de validation des paramètres de délégation du portail. |
> | Action | Microsoft.ApiManagement/service/portalsettings/read | Répertorie un ensemble de paramètres du portail. Ou permet d'obtenir les paramètres de connexion, d'inscription ou de délégation du portail. |
> | Action | Microsoft.ApiManagement/service/portalsettings/write | Met à jour les paramètres de connexion. ou crée ou met à jour les paramètres de connexion. ou met à jour les paramètres d’inscription ou de délégation. ou crée ou met à jour les paramètres de délégation. |
> | Action | Microsoft.ApiManagement/service/products/apis/delete | Supprime l’API spécifiée du produit spécifié. |
> | Action | Microsoft.ApiManagement/service/products/apis/read | Répertorie une collection d’API associées à un produit. |
> | Action | Microsoft.ApiManagement/service/products/apis/write | Ajoute une API au produit spécifié. |
> | Action | Microsoft.ApiManagement/service/products/delete | Supprime un produit. |
> | Action | Microsoft.ApiManagement/service/products/groups/delete | Supprime l’association entre le groupe et le produit spécifiés. |
> | Action | Microsoft.ApiManagement/service/products/groups/read | Répertorie la collection de groupes de développeurs associés au produit spécifié. |
> | Action | Microsoft.ApiManagement/service/products/groups/write | Ajoute l’association entre le groupe de développeurs spécifié et le produit spécifié. |
> | Action | Microsoft.ApiManagement/service/products/policies/delete | Supprime la configuration de stratégie du produit. |
> | Action | Microsoft.ApiManagement/service/products/policies/read | Obtient la configuration de stratégie au niveau du produit. ou obtient la configuration de stratégie au niveau du produit. |
> | Action | Microsoft.ApiManagement/service/products/policies/write | Crée ou met à jour de configuration de stratégie pour le produit. |
> | Action | Microsoft.ApiManagement/service/products/policy/delete | Supprimer la configuration de stratégie au niveau du produit |
> | Action | Microsoft.ApiManagement/service/products/policy/read | Obtenir la configuration de stratégie au niveau du produit |
> | Action | Microsoft.ApiManagement/service/products/policy/write | Créer la configuration de stratégie au niveau du produit |
> | Action | Microsoft.ApiManagement/service/products/read | Répertorie un ensemble de produits dans l’instance de service spécifiée. ou obtient les détails du produit spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/products/subscriptions/read | Répertorie la collection d’abonnements au produit spécifié. |
> | Action | Microsoft.ApiManagement/service/products/tags/delete | Détache la balise du produit. |
> | Action | Microsoft.ApiManagement/service/products/tags/read | Répertorie toutes les balises associées au produit. ou obtient la balise associée au produit. |
> | Action | Microsoft.ApiManagement/service/products/tags/write | Assigne une balise au produit. |
> | Action | Microsoft.ApiManagement/service/products/write | Crée ou met à jour un produit. ou met à jour les détails d’un produit existant. |
> | Action | Microsoft.ApiManagement/service/productsByTags/read | Répertorie un ensemble de produits associés à des balises. |
> | Action | Microsoft.ApiManagement/service/properties/delete | Supprime une propriété spécifique de l’instance de service de gestion des API. |
> | Action | Microsoft.ApiManagement/service/properties/listSecrets/action | Ou obtient les secrets de la propriété spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/properties/read | Répertorie une collection de propriétés définies au sein d’une instance de service. ou obtient les détails de la propriété spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/properties/write | Crée ou met à jour une propriété. ou met à jour la propriété spécifique. |
> | Action | Microsoft.ApiManagement/service/quotas/periods/read | Obtient la valeur de compteur de quota pour une période |
> | Action | Microsoft.ApiManagement/service/quotas/periods/write | Définit la valeur actuelle du compteur de quota |
> | Action | Microsoft.ApiManagement/service/quotas/read | Obtient les valeurs pour le quota |
> | Action | Microsoft.ApiManagement/service/quotas/write | Définit la valeur actuelle du compteur de quota |
> | Action | Microsoft.ApiManagement/service/read | Lire les métadonnées d’une instance du service Gestion des API |
> | Action | Microsoft.ApiManagement/service/regions/read | Répertorie toutes les régions Azure dans lesquelles le service existe. |
> | Action | Microsoft.ApiManagement/service/reports/read | Obtient un rapport agrégé par période de temps, par région géographique ou par développeur<br>Ou obtient un rapport agrégé par produit<br>Ou obtient un rapport agrégé par API, par opération ou par abonnement<br>Ou obtient des données pour le rapport de demandes |
> | Action | Microsoft.ApiManagement/service/restore/action | Restaurer le service Gestion des API à partir du conteneur spécifié dans un compte de stockage fourni par l’utilisateur |
> | Action | Microsoft.ApiManagement/service/subscriptions/delete | Supprime l’abonnement spécifié. |
> | Action | Microsoft.ApiManagement/service/subscriptions/listSecrets/action | Obtient les clés d’abonnement spécifiées. |
> | Action | Microsoft.ApiManagement/service/subscriptions/read | Répertorie tous les abonnements de l’instance de service de gestion des API. Ou obtient l’entité d’abonnement spécifiée (sans clés). |
> | Action | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Régénère la clé primaire d’un abonnement existant de l’instance de service de gestion des API. |
> | Action | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Régénère la clé secondaire d’un abonnement existant de l’instance de service de gestion des API. |
> | Action | Microsoft.ApiManagement/service/subscriptions/write | Crée ou met à jour de l’abonnement de l’utilisateur spécifié au produit spécifié. ou met à jour les détails d’un abonnement spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/tagResources/read | Répertorie un ensemble de ressources associées à des balises. |
> | Action | Microsoft.ApiManagement/service/tags/delete | Supprime une balise spécifique de l’instance de service de gestion des API. |
> | Action | Microsoft.ApiManagement/service/tags/read | Répertorie une collection de balises définies dans une instance de service. ou obtient les détails de la balise spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/tags/write | Crée une balise. ou met à jour ou les détails de la balise spécifiée par son identificateur. |
> | Action | Microsoft.ApiManagement/service/templates/delete | Réinitialise le modèle d’e-mail par défaut de Gestion des API |
> | Action | Microsoft.ApiManagement/service/templates/read | Obtient tous les modèles d’e-mail ou les détails d’un modèle d’e-mail de Gestion des API |
> | Action | Microsoft.ApiManagement/service/templates/write | Crée ou met à jour le modèle d’e-mail de Gestion des API |
> | Action | Microsoft.ApiManagement/service/tenant/delete | Supprimer la configuration de la stratégie du client |
> | Action | Microsoft.ApiManagement/service/tenant/deploy/action | Exécute une tâche de déploiement pour appliquer les modifications de la branche git spécifiée à la configuration dans la base de données. |
> | Action | Microsoft.ApiManagement/service/tenant/listSecrets/action | Obtenir les informations d’accès client |
> | Action | Microsoft.ApiManagement/service/tenant/operationResults/read | Obtenir la liste des résultats d’opération ou obtenir le résultat d’une opération spécifique |
> | Action | Microsoft.ApiManagement/service/tenant/read | Obtient la définition de stratégie globale du service de gestion des API. ou obtenir les informations d’accès client |
> | Action | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Régénérer la clé d’accès primaire |
> | Action | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Régénérer la clé d’accès secondaire |
> | Action | Microsoft.ApiManagement/service/tenant/save/action | Crée une validation avec un instantané de configuration dans la branche spécifiée du référentiel |
> | Action | Microsoft.ApiManagement/service/tenant/syncState/read | Obtenir l’état de la dernière synchronisation git |
> | Action | Microsoft.ApiManagement/service/tenant/validate/action | Valide les modifications de la branche git spécifiée |
> | Action | Microsoft.ApiManagement/service/tenant/write | Définit la configuration de stratégie pour le client ou met à jour les détails des informations d’accès du client |
> | Action | Microsoft.ApiManagement/service/updatecertificate/action | Télécharger le certificat SSL pour un service Gestion des API |
> | Action | Microsoft.ApiManagement/service/updatehostname/action | Configurer, mettre à jour ou supprimer des noms de domaine personnalisés pour un service Gestion des API |
> | Action | Microsoft.ApiManagement/service/users/action | Enregistre un nouvel utilisateur |
> | Action | Microsoft.ApiManagement/service/users/confirmations/send/action | Envoie une confirmation |
> | Action | Microsoft.ApiManagement/service/users/delete | Supprime un utilisateur spécifique. |
> | Action | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Récupère une URL de redirection contenant un jeton d’authentification pour connecter un utilisateur donné au portail des développeurs. |
> | Action | Microsoft.ApiManagement/service/users/groups/read | Répertorie tous les groupes d’utilisateurs. |
> | Action | Microsoft.ApiManagement/service/users/identities/read | Répertorie toutes les identités d’utilisateur. |
> | Action | Microsoft.ApiManagement/service/users/keys/read | Obtenir les clés associées à un utilisateur |
> | Action | Microsoft.ApiManagement/service/users/read | Répertorie une collection d’utilisateurs enregistrés dans l’instance de service spécifiée. ou obtient les détails de l’utilisateur spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/users/subscriptions/read | Répertorie la collection d’abonnements de l’utilisateur spécifié. |
> | Action | Microsoft.ApiManagement/service/users/token/action | Obtient le jeton d’autorisation d’accès partagé pour l’utilisateur. |
> | Action | Microsoft.ApiManagement/service/users/write | Crée ou met à jour un utilisateur. ou met à jour ou les détails de l’utilisateur spécifié par son identificateur. |
> | Action | Microsoft.ApiManagement/service/write | Crée ou met à jour une instance du service Gestion des API |
> | Action | Microsoft.ApiManagement/unregister/action | Annuler l’inscription de l’abonnement pour le fournisseur de ressources Microsoft.ApiManagement |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.AppConfiguration/checkNameAvailability/read | Vérifie que le nom de la ressource est disponible. |
> | Action | Microsoft.AppConfiguration/configurationStores/delete | Supprime un magasin de configuration. |
> | Action | Microsoft.AppConfiguration/configurationStores/eventGridFilters/delete | Supprime un filtre Event Grid de magasin de configuration. |
> | Action | Microsoft.AppConfiguration/configurationStores/eventGridFilters/read | Permet d'obtenir les propriétés du filtre Event Grid de magasin de configuration spécifié ou de répertorier tous les filtres Event Grid de magasin de configuration disponibles sous le magasin de configuration spécifié. |
> | Action | Microsoft.AppConfiguration/configurationStores/eventGridFilters/write | Crée ou met à jour un filtre Event Grid de magasin de configuration avec les paramètres spécifiés. |
> | DataAction | Microsoft.AppConfiguration/configurationStores/keyValues/delete | Supprime une paire clé-valeur existante du magasin de configuration. |
> | DataAction | Microsoft.AppConfiguration/configurationStores/keyValues/read | Lit une paire clé-valeur dans le magasin de configuration. |
> | DataAction | Microsoft.AppConfiguration/configurationStores/keyValues/write | Crée ou met à jour une paire clé-valeur dans le magasin de configuration. |
> | Action | Microsoft.AppConfiguration/configurationStores/ListKeys/action | Répertorie les clés API du magasin de configuration spécifié. |
> | Action | Microsoft.AppConfiguration/configurationStores/ListKeyValue/action | Répertorie une paire clé-valeur pour le magasin de configuration spécifié. |
> | Action | Microsoft.AppConfiguration/configurationStores/privateEndpointConnectionProxies/delete | Supprime un proxy de connexion de point de terminaison privé sous le magasin de configuration spécifié. |
> | Action | Microsoft.AppConfiguration/configurationStores/privateEndpointConnectionProxies/read | Permet d'obtenir un proxy de connexion de point de terminaison privé sous le magasin de configuration spécifié. |
> | Action | Microsoft.AppConfiguration/configurationStores/privateEndpointConnectionProxies/validate/action | Valide un proxy de connexion de point de terminaison privé sous le magasin de configuration spécifié. |
> | Action | Microsoft.AppConfiguration/configurationStores/privateEndpointConnectionProxies/write | Crée ou met jour un proxy de connexion de point de terminaison privé sous le magasin de configuration spécifié. |
> | Action | Microsoft.AppConfiguration/configurationStores/privateEndpointConnections/delete | Supprime une connexion de point de terminaison privé du magasin de configurations spécifié. |
> | Action | Microsoft.AppConfiguration/configurationStores/privateEndpointConnections/read | Obtient une connexion de point de terminaison privé ou répertorie les connexions de point de terminaison privé dans le magasin de configurations spécifié. |
> | Action | Microsoft.AppConfiguration/configurationStores/privateEndpointConnections/write | Approuve ou refuse une connexion de point de terminaison privé dans le magasin de configurations spécifié. |
> | Action | Microsoft.AppConfiguration/configurationStores/PrivateEndpointConnectionsApproval/action | Approuve automatiquement une connexion de point de terminaison privé dans le magasin de configurations spécifié. |
> | Action | Microsoft.AppConfiguration/configurationStores/privateLinkResources/read | Répertorie toutes les ressources de liaison privée dans le magasin de configurations spécifié. |
> | Action | Microsoft.AppConfiguration/configurationStores/providers/Microsoft.Insights/diagnosticSettings/read | Lit toutes les valeurs des paramètres de diagnostic d'un magasin de configuration. |
> | Action | Microsoft.AppConfiguration/configurationStores/providers/Microsoft.Insights/diagnosticSettings/write | Écrit/remplace les paramètres de diagnostic de Microsoft App Configuration. |
> | Action | Microsoft.AppConfiguration/configurationStores/providers/Microsoft.Insights/metricDefinitions/read | Récupère toutes les définitions de métriques de Microsoft App Configuration. |
> | Action | Microsoft.AppConfiguration/configurationStores/read | Permet d'obtenir les propriétés du magasin de configuration spécifié ou d'afficher tous les magasins de configuration disponibles sous le groupe de ressources ou l'abonnement spécifiés. |
> | Action | Microsoft.AppConfiguration/configurationStores/RegenerateKey/action | Régénère les clés API du magasin de configuration spécifié. |
> | Action | Microsoft.AppConfiguration/configurationStores/syncTasks/delete | Supprime une tâche de synchronisation de magasin de configuration. |
> | Action | Microsoft.AppConfiguration/configurationStores/syncTasks/read | Permet d'obtenir les propriétés de la tâche de synchronisation de magasin de configuration spécifiée ou de répertorier toutes les tâches de synchronisation de magasin de configuration disponibles sous le magasin de configuration spécifié. |
> | Action | Microsoft.AppConfiguration/configurationStores/syncTasks/write | Crée ou met à jour une tâche de synchronisation de magasin de configuration avec les paramètres spécifiés. |
> | Action | Microsoft.AppConfiguration/configurationStores/write | Crée ou met à jour un magasin de configuration avec les paramètres spécifiés. |
> | Action | Microsoft.AppConfiguration/locations/operationsStatus/read | Permet d'obtenir l'état d'une opération. |
> | Action | Microsoft.AppConfiguration/operations/read | Répertorie toutes les opérations prises en charge par Microsoft App Configuration. |
> | Action | Microsoft.AppConfiguration/register/action | Inscrit un abonnement pour utiliser Microsoft App Configuration. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Authorization/classicAdministrators/delete | Supprime l’administrateur de l’abonnement. |
> | Action | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Obtient les états de l’opération d’administrateur de l’abonnement. |
> | Action | Microsoft.Authorization/classicAdministrators/read | Lit les administrateurs de l’abonnement. |
> | Action | Microsoft.Authorization/classicAdministrators/write | Ajoutez ou modifiez un administrateur à un abonnement. |
> | Action | Microsoft.Authorization/denyAssignments/delete | Supprime un refus d’affectation dans l’étendue spécifiée. |
> | Action | Microsoft.Authorization/denyAssignments/read | Obtient des informations sur un refus d’affectation. |
> | Action | Microsoft.Authorization/denyAssignments/write | Crée un refus d’affectation dans l’étendue spécifiée. |
> | Action | Microsoft.Authorization/elevateAccess/action | Accorde à l’appelant un accès Administrateur de l’accès utilisateur au niveau de la portée du client |
> | Action | Microsoft.Authorization/locks/delete | Supprime des verrous au niveau de la portée spécifiée. |
> | Action | Microsoft.Authorization/locks/read | Obtient des verrous au niveau de la portée spécifiée. |
> | Action | Microsoft.Authorization/locks/write | Ajoute des verrous au niveau de la portée spécifiée. |
> | Action | Microsoft.Authorization/operations/read | Obtient la liste des opérations |
> | Action | Microsoft.Authorization/permissions/read | Répertorie toutes les autorisations dont dispose l’appelant au niveau d’une portée donnée. |
> | Action | Microsoft.Authorization/policies/audit/action | Action effectuée à la suite d’une évaluation d’Azure Policy avec effet « audit » |
> | Action | Microsoft.Authorization/policies/auditIfNotExists/action | Action effectuée à la suite d’une évaluation d’Azure Policy avec effet « auditIfNotExists » |
> | Action | Microsoft.Authorization/policies/deny/action | Action effectuée à la suite d’une évaluation d’Azure Policy avec effet « deny » |
> | Action | Microsoft.Authorization/policies/deployIfNotExists/action | Action effectuée à la suite d’une évaluation d’Azure Policy avec effet « deployIfNotExists » |
> | Action | Microsoft.Authorization/policyAssignments/delete | Supprimez une affectation de stratégie au niveau de la portée spécifiée. |
> | Action | Microsoft.Authorization/policyAssignments/read | Obtenez des informations sur une affectation de stratégie. |
> | Action | Microsoft.Authorization/policyAssignments/write | Créez une affectation de stratégie au niveau de la portée spécifiée. |
> | Action | Microsoft.Authorization/policyDefinitions/delete | Supprimez une définition de stratégie. |
> | Action | Microsoft.Authorization/policyDefinitions/read | Obtenez des informations sur une définition de stratégie. |
> | Action | Microsoft.Authorization/policyDefinitions/write | Créez une définition de stratégie personnalisée. |
> | Action | Microsoft.Authorization/policySetDefinitions/delete | Supprime une définition d’ensemble de stratégies |
> | Action | Microsoft.Authorization/policySetDefinitions/read | Obtient des informations sur une définition d’ensemble de stratégies |
> | Action | Microsoft.Authorization/policySetDefinitions/write | Crée une définition d’ensemble de stratégies personnalisé |
> | Action | Microsoft.Authorization/providerOperations/read | Obtenez des opérations pour tous les fournisseurs de ressources qui peuvent être utilisées dans les définitions de rôles. |
> | Action | Microsoft.Authorization/roleAssignments/delete | Supprimez une affectation de rôle au niveau de la portée spécifiée. |
> | Action | Microsoft.Authorization/roleAssignments/read | Obtenez des informations sur une affectation de rôle. |
> | Action | Microsoft.Authorization/roleAssignments/write | Créez une affectation de rôle au niveau de la portée spécifiée. |
> | Action | Microsoft.Authorization/roleDefinitions/delete | Supprimez la définition de rôle personnalisé spécifiée. |
> | Action | Microsoft.Authorization/roleDefinitions/read | Obtenez des informations sur une définition de rôle. |
> | Action | Microsoft.Authorization/roleDefinitions/write | Créez ou mettez à jour une définition de rôle personnalisé avec des autorisations spécifiées et des portées pouvant être affectées. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Lit les informations d’inscription d’Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Écrit une demande de régénération de clés Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/certificates/delete | Supprime une ressource de certificat Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/certificates/getCount/action | Affiche le nombre de certificats |
> | Action | Microsoft.Automation/automationAccounts/certificates/read | Obtient une ressource de certificat Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/certificates/write | Crée ou met à jour une ressource de certificat Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/read | Lit une compilation d’Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/read | Lit une compilation d’Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/write | Écrit une compilation d’Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/write | Écrit une compilation d’Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/configurations/content/read | Affiche le contenu de support de configuration |
> | Action | Microsoft.Automation/automationAccounts/configurations/delete | Supprime un contenu d’Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/configurations/getCount/action | Lit le nombre d’un contenu d’Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/configurations/read | Obtient un contenu d’Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/configurations/write | Écrit un contenu d’Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/connections/delete | Supprime une ressource de connexion Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/connections/getCount/action | Affiche le nombre de connexions |
> | Action | Microsoft.Automation/automationAccounts/connections/read | Obtient une ressource de connexion Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/connections/write | Crée ou met à jour une ressource de connexion Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/connectionTypes/delete | Supprime une ressource de type de connexion Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/connectionTypes/read | Obtient une ressource de type de connexion Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/connectionTypes/write | Crée une ressource de type de connexion Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/credentials/delete | Supprime une ressource d’information d’identification Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/credentials/getCount/action | Affiche le nombre d’informations d’identification |
> | Action | Microsoft.Automation/automationAccounts/credentials/read | Obtient une ressource d’information d’identification Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/credentials/write | Crée ou met à jour une ressource d’information d’identification Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/delete | Supprime un compte Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Supprime des ressources Runbook Worker hybrides |
> | Action | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lit des ressources Runbook Worker hybrides |
> | Action | Microsoft.Automation/automationAccounts/jobs/output/read | Obtient le résultat d’un travail |
> | Action | Microsoft.Automation/automationAccounts/jobs/read | Obtient un travail Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/jobs/resume/action | Reprend un travail Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Obtient le contenu du runbook Azure Automation au moment de l’exécution du travail |
> | Action | Microsoft.Automation/automationAccounts/jobs/stop/action | Arrête un travail Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtient un flux de travail Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtient un flux de travail Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspend un travail Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/jobs/write | Crée un travail Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/delete | Supprime une planification du travail Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/read | Obtient une planification du travail Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/write | Crée une planification du travail Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtient l’espace de travail lié au compte Automation. |
> | Action | Microsoft.Automation/automationAccounts/listKeys/action | Affiche les clés pour le compte Automation. |
> | Action | Microsoft.Automation/automationAccounts/modules/activities/read | Obtient les activités d’Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/modules/delete | Supprime un module PowerShell Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/modules/getCount/action | Obtient le nombre de modules PowerShell au sein du compte Automation |
> | Action | Microsoft.Automation/automationAccounts/modules/read | Obtient un module PowerShell Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/modules/write | Crée ou met à jour un module PowerShell Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Supprime une configuration de nœud d’Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Lit le contenu d’une configuration de nœud d’Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Lit une configuration de nœud d’Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Écrit une configuration de nœud d’Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/nodecounts/read | Affiche le résumé du nombre de nœuds pour le type spécifié |
> | Action | Microsoft.Automation/automationAccounts/nodes/delete | Supprime des nœuds Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/nodes/read | Lit des nœuds Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Affiche le contenu d’un rapport Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/nodes/reports/read | Lit un rapport Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/nodes/write | Crée ou met à jour des nœuds Azure Automation DSC |
> | Action | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Obtient les TypeFields Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/python2Packages/delete | Supprime un package Python 2 Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/python2Packages/read | Obtient un package Python 2 Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/python2Packages/write | Crée ou met à jour un package Python 2 Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/python3Packages/delete | Supprime un package Python 3 Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/python3Packages/read | Obtient un package Python 3 Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/python3Packages/write | Crée ou met à jour un package Python 3 Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/read | Obtient un compte Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/content/read | Obtient le contenu d’un runbook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/delete | Supprime un runbook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Crée le contenu d’un brouillon de runbook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Obtient les résultats d’opération de brouillon de runbook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/read | Obtient un brouillon de runbook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Obtient un travail de test de brouillon de runbook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Reprend un travail de test de brouillon de runbook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Arrête un travail de test de brouillon de runbook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Suspend un travail de test de brouillon de runbook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Crée un travail de test de brouillon de runbook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Annule les modifications apportées à un brouillon de runbook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Obtient le nombre de runbooks Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/publish/action | Publie un brouillon de runbook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/read | Obtient un runbook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/write | Crée ou met à jour un runbook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/schedules/delete | Supprime une ressource de planification Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/schedules/getCount/action | Obtient le nombre de planifications Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/schedules/read | Obtient une ressource de planification Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/schedules/write | Crée ou met à jour une ressource de planification Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/read | Obtient une exécution de machine de configuration de mise à jour de logiciel Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurationRuns/read | Obtient une exécution de configuration de mise à jour de logiciel Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Supprime une configuration de mise à jour de logiciel Azure Automation. |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Supprime une configuration de mise à jour de logiciel Azure Automation. |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Obtient une configuration de mise à jour de logiciel Azure Automation. |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Obtient une configuration de mise à jour de logiciel Azure Automation. |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Crée ou met à jour une configuration de mise à jour de logiciel Azure Automation. |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Crée ou met à jour une configuration de mise à jour de logiciel Azure Automation. |
> | Action | Microsoft.Automation/automationAccounts/statistics/read | Obtient les statistiques d’Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Obtient une machine de déploiement de mise à jour Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Obtient un travail de correctif de gestion de mise à jour Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/usages/read | Obtient l’utilisation d’Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/variables/delete | Supprime une ressource de variable Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/variables/read | Lit une ressource de variable Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/variables/write | Crée ou met à jour une ressource de variable Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/watchers/delete | Supprime un travail d’observateur Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/watchers/read | Obtient un travail d’observateur Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/watchers/start/action | Lance un travail d’observateur Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/watchers/stop/action | Interrompt un travail d’observateur Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/watchers/streams/read | Obtient un flux de tâches Automation Watcher Azure |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Supprime des actions de travail d’observateur Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Obtient des actions de travail d’observateur Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Crée des actions de travail d’observateur Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/watchers/write | Crée un travail d’observateur Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/webhooks/action | Génère un URI pour un webhook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/webhooks/delete | Supprime un webhook Azure Automation  |
> | Action | Microsoft.Automation/automationAccounts/webhooks/read | Lit un webhook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/webhooks/write | Crée ou met à jour un webhook Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/write | Crée ou met à jour un compte Azure Automation |
> | Action | Microsoft.Automation/operations/read | Obtient les opérations disponibles pour les ressources Azure Automation |
> | Action | Microsoft.Automation/register/action | Enregistre l’abonnement dans Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Supprime une ressource de répertoire B2C |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/read | Affiche une ressource de répertoire B2C |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/write | Créer ou mettre à jour une ressource d’annuaire B2C |
> | Action | Microsoft.AzureActiveDirectory/b2ctenants/read | Répertorie tous les locataires B2C où l’utilisateur est un membre |
> | Action | Microsoft.AzureActiveDirectory/operations/read | Lit toutes les opérations d’API disponibles pour le fournisseur de ressources Microsoft.AzureActiveDirectory |
> | Action | Microsoft.AzureActiveDirectory/register/action | Enregistre l’abonnement à un fournisseur de ressources Microsoft.AzureActiveDirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.AzureStack/Operations/read | Obtient les propriétés d’une opération de fournisseur de ressources |
> | Action | Microsoft.AzureStack/register/action | Inscrit l’abonnement auprès du fournisseur de ressources Microsoft.AzureStack |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Supprime un abonnement client Azure Stack |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/read | Obtient les propriétés d’un abonnement client Azure Stack |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/write | Crée ou met à jour un abonnement client Azure Stack |
> | Action | Microsoft.AzureStack/registrations/delete | Vérifie une inscription Azure Stack |
> | Action | Microsoft.AzureStack/registrations/getActivationKey/action | Obtient la dernière clé d’activation Azure Stack |
> | Action | Microsoft.AzureStack/registrations/products/getProduct/action | Récupère le produit de la place de marché Azure Stack |
> | Action | Microsoft.AzureStack/registrations/products/getProducts/action | Récupère une liste des produits de la place de marché Azure Stack |
> | Action | Microsoft.AzureStack/registrations/products/listDetails/action | Récupère les détails étendus d’un produit de la place de marché Azure Stack. |
> | Action | Microsoft.AzureStack/registrations/products/read | Obtient les propriétés d’un produit de la place de marché Azure Stack. |
> | Action | Microsoft.AzureStack/registrations/products/uploadProductLog/action | Enregistre l’horodateur et l’état de l’opération de produit de la place de marché Azure Stack |
> | Action | Microsoft.AzureStack/registrations/read | Obtient les propriétés d’une inscription Azure Stack. |
> | Action | Microsoft.AzureStack/registrations/write | Crée ou met à jour une inscription Azure Stack |
> | Action | Microsoft.AzureStack/verificationKeys/getCurrentKey/action | Obtient la version actuelle de la clé publique de signature Azure Stack |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Batch/batchAccounts/applications/delete | Supprime une application |
> | Action | Microsoft.Batch/batchAccounts/applications/read | Répertorie les applications ou obtient les propriétés d’une application |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Active un package d’application |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/delete | Supprime un package d’application |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/read | Obtient les propriétés d’un package d’application |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/write | Crée une nouveau package d’application ou met à jour un package d’application existant |
> | Action | Microsoft.Batch/batchAccounts/applications/write | Crée une nouvelle application ou met à jour une application existante |
> | Action | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Obtient les résultats d’une opération de certificat de longue durée sur un compte Batch |
> | Action | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Annule la suppression manquée d’un certificat sur un compte Batch |
> | Action | Microsoft.Batch/batchAccounts/certificates/delete | Supprime un certificat d’un compte Batch |
> | Action | Microsoft.Batch/batchAccounts/certificates/read | Répertorie les certificats sur un compte Batch ou obtient les propriétés d’un certificat |
> | Action | Microsoft.Batch/batchAccounts/certificates/write | Crée un certificat sur un compte Batch ou met à jour un certificat existant |
> | Action | Microsoft.Batch/batchAccounts/delete | Supprime un compte Batch |
> | DataAction | Microsoft.Batch/batchAccounts/jobs/delete | Supprime un travail d’un compte Batch |
> | DataAction | Microsoft.Batch/batchAccounts/jobs/read | Répertorie les travaux sur un compte Batch ou obtient les propriétés d’un travail |
> | DataAction | Microsoft.Batch/batchAccounts/jobs/write | Crée un travail sur un compte Batch ou met à jour un travail existant |
> | DataAction | Microsoft.Batch/batchAccounts/jobSchedules/delete | Supprime une planification de travail d’un compte Batch |
> | DataAction | Microsoft.Batch/batchAccounts/jobSchedules/read | Répertorie les planifications de travail sur un compte Batch ou obtient les propriétés d’une planification de travail |
> | DataAction | Microsoft.Batch/batchAccounts/jobSchedules/write | Crée une planification de travail sur un compte Batch ou met à jour une planification de travail existante |
> | Action | Microsoft.Batch/batchAccounts/listkeys/action | Répertorie les clés d’accès pour un compte Batch |
> | Action | Microsoft.Batch/batchAccounts/operationResults/read | Obtient les résultats d’une opération de compte Batch de longue durée |
> | Action | Microsoft.Batch/batchAccounts/poolOperationResults/read | Obtient les résultats d’une opération de pool de longue durée sur un compte Batch |
> | Action | Microsoft.Batch/batchAccounts/pools/delete | Supprime un pool d’un compte Batch |
> | Action | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Désactive la mise à l’échelle automatique pour un pool de comptes Batch |
> | Action | Microsoft.Batch/batchAccounts/pools/read | Répertorie les pools sur un compte Batch ou obtient les propriétés d’un pool |
> | Action | Microsoft.Batch/batchAccounts/pools/stopResize/action | Arrête une opération de redimensionnement en cours sur un pool de comptes Batch |
> | Action | Microsoft.Batch/batchAccounts/pools/write | Crée un pool sur un compte Batch ou met à jour un pool existant |
> | Action | Microsoft.Batch/batchAccounts/read | Répertorie les comptes Batch ou obtient les propriétés d’un compte Batch |
> | Action | Microsoft.Batch/batchAccounts/regeneratekeys/action | Régénère les clés d’accès pour un compte Batch |
> | Action | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Synchronise les clés d’accès pour le compte de stockage automatique configuré pour un compte Batch |
> | Action | Microsoft.Batch/batchAccounts/write | Crée un nouveau compte Batch ou met à jour un compte Batch existant |
> | Action | Microsoft.Batch/locations/accountOperationResults/read | Obtient les résultats d’une opération de compte Batch de longue durée |
> | Action | Microsoft.Batch/locations/checkNameAvailability/action | Vérifie que le nom de compte est valide et qu’il n’est pas utilisé |
> | Action | Microsoft.Batch/locations/quotas/read | Obtient les quotas Batch de l’abonnement spécifié au niveau de la région Azure spécifiée |
> | Action | Microsoft.Batch/operations/read | Répertorie les opérations disponibles sur le fournisseur de ressources Microsoft.Batch |
> | Action | Microsoft.Batch/register/action | Inscrit l’abonnement pour le fournisseur de ressources Batch et permet la création de comptes Batch |
> | Action | Microsoft.Batch/unregister/action | Désinscrit l’abonnement pour le fournisseur de ressources Batch en empêchant la création de comptes Batch |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Billing/billingAccounts/agreements/read |  |
> | Action | Microsoft.Billing/billingAccounts/billingPermissions/read |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/billingPermissions/read |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/customers/read |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/invoices/pricesheet/download/action |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections/billingPermissions/read |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/move/action |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/transfer/action |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/validateMoveEligibility/action |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections/products/move/action |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections/products/transfer/action |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections/products/validateMoveEligibility/action |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections/read |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections/write |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/download/action |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/read |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/write |  |
> | Action | Microsoft.Billing/billingAccounts/billingProfiles/write |  |
> | Action | Microsoft.Billing/billingAccounts/billingSubscriptions/read |  |
> | Action | Microsoft.Billing/billingAccounts/customers/billingPermissions/read |  |
> | Action | Microsoft.Billing/billingAccounts/customers/read |  |
> | Action | Microsoft.Billing/billingAccounts/departments/read |  |
> | Action | Microsoft.Billing/billingAccounts/enrollmentAccounts/billingPermissions/read |  |
> | Action | Microsoft.Billing/billingAccounts/enrollmentAccounts/read |  |
> | Action | Microsoft.Billing/billingAccounts/enrollmentDepartments/billingPermissions/read |  |
> | Action | Microsoft.Billing/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/action |  |
> | Action | Microsoft.Billing/billingAccounts/products/read |  |
> | Action | Microsoft.Billing/billingAccounts/read |  |
> | Action | Microsoft.Billing/billingAccounts/write |  |
> | Action | Microsoft.Billing/departments/read |  |
> | Action | Microsoft.Billing/invoices/download/action | Télécharge la facture à l’aide du lien de téléchargement de la liste |
> | Action | Microsoft.Billing/invoices/read |  |
> | Action | Microsoft.Billing/register/action |  |
> | Action | Microsoft.Billing/validateAddress/action |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.BingMaps/listCommunicationPreference/action | Obtient les préférences de communication pour le propriétaire de Microsoft.BingMaps |
> | Action | Microsoft.BingMaps/mapApis/Delete | Supprime la ressource pour Microsoft.BingMaps/mapApis |
> | Action | Microsoft.BingMaps/mapApis/listSecrets/action | Répertorie les secrets pour Microsoft.BingMaps/mapApis |
> | Action | Microsoft.BingMaps/mapApis/listUsageMetrics/action | Répertorie les métriques pour Microsoft.BingMaps/mapApis |
> | Action | Microsoft.BingMaps/mapApis/Read | Obtient la ressource pour Microsoft.BingMaps/mapApis |
> | Action | Microsoft.BingMaps/mapApis/regenerateKey/action | Régénère des clés pour Microsoft.BingMaps/mapApis |
> | Action | Microsoft.BingMaps/mapApis/Write | Met à jour la ressource pour Microsoft.BingMaps/mapApis |
> | Action | Microsoft.BingMaps/Operations/read | Répertorie les opérations pour Microsoft.BingMaps |
> | Action | Microsoft.BingMaps/updateCommunicationPreference/action | Met à jour les préférences de communication pour le propriétaire de Microsoft.BingMaps |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Blockchain/blockchainMembers/delete | Supprime un membre de blockchain existant. |
> | Action | Microsoft.Blockchain/blockchainMembers/listApiKeys/action | Obtient ou répertorie les clés API de membre de blockchain existantes. |
> | Action | Microsoft.Blockchain/blockchainMembers/read | Obtient ou répertorie les membres de blockchain existants. |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Connecte à un nœud de transaction d’un membre blockchain. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Supprime un nœud de transaction existant du membre blockchain. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | Obtient ou répertorie une ou plusieurs clés d’API de nœud de transaction du membre blockchain. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Crée ou répertorie un ou plusieurs nœuds de transaction existants du membre blockchain. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | Crée ou met à jour un nœud de transaction d’un membre blockchain. |
> | Action | Microsoft.Blockchain/blockchainMembers/write | Crée ou met à jour un membre de blockchain. |
> | Action | Microsoft.Blockchain/cordaMembers/delete | Supprime un membre Blockchain Corda existant. |
> | Action | Microsoft.Blockchain/cordaMembers/read | Obtient ou répertorie les membres Blockchain Corda existants. |
> | Action | Microsoft.Blockchain/cordaMembers/write | Crée ou met à jour un membre Blockchain Corda. |
> | Action | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Obtient les résultats de l’opération des membres de blockchain. |
> | Action | Microsoft.Blockchain/locations/checkNameAvailability/action | Vérifie que le nom de ressource est valide et qu’il n’est pas déjà utilisé. |
> | Action | Microsoft.Blockchain/operations/read | Répertorie toutes les opérations dans le fournisseur de ressources blockchain Microsoft. |
> | Action | Microsoft.Blockchain/register/action | Enregistre l’abonnement pour le fournisseur de ressources Blockchain. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | Lire tous les artefacts de blueprint |
> | Action | Microsoft.Blueprint/blueprintAssignments/delete | Supprimer tous les artefacts de blueprint |
> | Action | Microsoft.Blueprint/blueprintAssignments/read | Lire tous les artefacts de blueprint |
> | Action | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | Obtient l’ID d’objet principal du service Azure Blueprints. |
> | Action | Microsoft.Blueprint/blueprintAssignments/write | Créer ou mettre à jour tous les artefacts de blueprint |
> | Action | Microsoft.Blueprint/blueprints/artifacts/delete | Supprimer tous les artefacts de blueprint |
> | Action | Microsoft.Blueprint/blueprints/artifacts/read | Lire tous les artefacts de blueprint |
> | Action | Microsoft.Blueprint/blueprints/artifacts/write | Créer ou mettre à jour tous les artefacts de blueprint |
> | Action | Microsoft.Blueprint/blueprints/delete | Supprimer tous les blueprints |
> | Action | Microsoft.Blueprint/blueprints/read | Lire tous les blueprints |
> | Action | Microsoft.Blueprint/blueprints/versions/artifacts/read | Lire tous les artefacts de blueprint |
> | Action | Microsoft.Blueprint/blueprints/versions/delete | Supprimer tous les blueprints |
> | Action | Microsoft.Blueprint/blueprints/versions/read | Lire tous les blueprints |
> | Action | Microsoft.Blueprint/blueprints/versions/write | Créer ou mettre à jour tous les blueprints |
> | Action | Microsoft.Blueprint/blueprints/write | Créer ou mettre à jour tous les blueprints |
> | Action | Microsoft.Blueprint/register/action | Inscrire le fournisseur de ressources blueprint Azure |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.BotService/botServices/channels/delete | Supprime un canal de service Bot |
> | Action | Microsoft.BotService/botServices/channels/listchannelwithkeys/action | Répertorie les canaux de service Bot avec des secrets |
> | Action | Microsoft.BotService/botServices/channels/read | Lit un canal de service Bot |
> | Action | Microsoft.BotService/botServices/channels/write | Écrit un canal de service Bot |
> | Action | Microsoft.BotService/botServices/connections/delete | Supprime une connexion de service Bot |
> | Action | Microsoft.BotService/botServices/connections/listwithsecrets/write | Écrit une liste de connexions de service Bot  |
> | Action | Microsoft.BotService/botServices/connections/read | Lit une connexion de service Bot |
> | Action | Microsoft.BotService/botServices/connections/write | Écrit une connexion de service Bot |
> | Action | Microsoft.BotService/botServices/delete | Supprimer un service Bot |
> | Action | Microsoft.BotService/botServices/read | Lire un service Bot |
> | Action | Microsoft.BotService/botServices/write | Écrire un service Bot |
> | Action | Microsoft.BotService/checknameavailability/action | Vérifie la disponibilité du nom d’un bot |
> | Action | Microsoft.BotService/listauthserviceproviders/action | Répertorie les fournisseurs de services d’authentification |
> | Action | Microsoft.BotService/locations/operationresults/read | Lire l’état d’une opération asynchrone |
> | Action | Microsoft.BotService/Operations/read | Lire les opérations pour tous les types de ressources |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Cache/checknameavailability/action | Vérifie si un nom peut être utilisé avec un nouveau cache Redis |
> | Action | Microsoft.Cache/locations/operationresults/read | Obtient le résultat d’une opération de longue durée pour laquelle l’en-tête Location a été précédemment retourné au client |
> | Action | Microsoft.Cache/operations/read | Répertorie les opérations que le fournisseur Microsoft.Cache prend en charge. |
> | Action | Microsoft.Cache/redis/delete | Supprimer l’intégralité du cache Redis |
> | Action | Microsoft.Cache/redis/export/action | Exporter des données Redis vers des objets blob préfixés dans un format spécifié |
> | Action | Microsoft.Cache/redis/firewallRules/delete | Supprimer des règles de pare-feu IP d’un cache Redis |
> | Action | Microsoft.Cache/redis/firewallRules/read | Obtenir les règles de pare-feu IP d’un cache Redis |
> | Action | Microsoft.Cache/redis/firewallRules/write | Modifier les règles de pare-feu IP d’un cache Redis |
> | Action | Microsoft.Cache/redis/forceReboot/action | Forcez le redémarrage d’instance de cache, potentiellement avec une perte de données. |
> | Action | Microsoft.Cache/redis/import/action | Importer des données d’un format spécifié à partir de plusieurs objets blob dans Redis |
> | Action | Microsoft.Cache/redis/linkedservers/delete | Supprimer un serveur lié d’un cache Redis |
> | Action | Microsoft.Cache/redis/linkedservers/read | Obtenez les serveurs liés associés à un cache Redis. |
> | Action | Microsoft.Cache/redis/linkedservers/write | Ajouter un serveur lié à un cache Redis |
> | Action | Microsoft.Cache/redis/listKeys/action | Afficher la valeur des clés d’accès du cache Redis dans le portail de gestion |
> | Action | Microsoft.Cache/redis/metricDefinitions/read | Obtient les mesures disponibles pour un cache Redis |
> | Action | Microsoft.Cache/redis/patchSchedules/delete | Supprimer la planification de correctif d’un cache Redis |
> | Action | Microsoft.Cache/redis/patchSchedules/read | Obtient la planification de mise à jour corrective d’un cache Redis |
> | Action | Microsoft.Cache/redis/patchSchedules/write | Modifier la planification de mise à jour corrective d’un cache Redis |
> | Action | Microsoft.Cache/redis/read | Afficher les paramètres et la configuration du cache Redis dans le portail de gestion |
> | Action | Microsoft.Cache/redis/regenerateKey/action | Modifier la valeur des clés d’accès du cache Redis dans le portail de gestion |
> | Action | Microsoft.Cache/redis/start/action | Démarrez une instance de cache. |
> | Action | Microsoft.Cache/redis/stop/action | Arrêtez une instance de cache. |
> | Action | Microsoft.Cache/redis/write | Modifier les paramètres et la configuration du cache Redis dans le portail de gestion |
> | Action | Microsoft.Cache/register/action | Inscrit le fournisseur de ressources « Microsoft.Cache » à un abonnement |
> | Action | Microsoft.Cache/unregister/action | Annule l’inscription du fournisseur de ressources « Microsoft.Cache » à un abonnement |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Capacity/appliedreservations/read | Lit toutes les réservations |
> | Action | Microsoft.Capacity/calculateexchange/action | Calcule le montant de l’échange et le prix des nouveaux achats et retourne les erreurs de stratégie. |
> | Action | Microsoft.Capacity/calculateprice/action | Calculer tous les prix de réservation |
> | Action | Microsoft.Capacity/catalogs/read | Lire le catalogue de réservation |
> | Action | Microsoft.Capacity/checkoffers/action | Vérifier toutes les offres d’abonnement |
> | Action | Microsoft.Capacity/checkscopes/action | Vérifier tous les abonnements |
> | Action | Microsoft.Capacity/commercialreservationorders/read | Obtenir les demandes de réservation créées dans n’importe quel locataire |
> | Action | Microsoft.Capacity/exchange/action | Échanger toute réservation |
> | Action | Microsoft.Capacity/operations/read | Lire toutes les opérations |
> | Action | Microsoft.Capacity/register/action | Inscrit le fournisseur de ressources de capacité et active la création de ressources de capacité. |
> | Action | Microsoft.Capacity/reservationorders/action | Met à jour une réservation |
> | Action | Microsoft.Capacity/reservationorders/availablescopes/action | Rechercher toutes les étendues disponibles |
> | Action | Microsoft.Capacity/reservationorders/calculaterefund/action | Calcule le montant du remboursement et le prix des nouveaux achats et retourne les erreurs de stratégie. |
> | Action | Microsoft.Capacity/reservationorders/delete | Supprime une réservation |
> | Action | Microsoft.Capacity/reservationorders/merge/action | Fusionner toutes les réservations |
> | Action | Microsoft.Capacity/reservationorders/mergeoperationresults/read | Interroge une opération de fusion |
> | Action | Microsoft.Capacity/reservationorders/read | Lit toutes les réservations |
> | Action | Microsoft.Capacity/reservationorders/reservations/action | Met à jour une réservation |
> | Action | Microsoft.Capacity/reservationorders/reservations/availablescopes/action | Rechercher toutes les étendues disponibles |
> | Action | Microsoft.Capacity/reservationorders/reservations/delete | Supprime une réservation |
> | Action | Microsoft.Capacity/reservationorders/reservations/read | Lit toutes les réservations |
> | Action | Microsoft.Capacity/reservationorders/reservations/revisions/read | Lit toutes les réservations |
> | Action | Microsoft.Capacity/reservationorders/reservations/write | Crée une réservation |
> | Action | Microsoft.Capacity/reservationorders/return/action | Retourner toutes les réservations |
> | Action | Microsoft.Capacity/reservationorders/split/action | Diviser toutes les réservations |
> | Action | Microsoft.Capacity/reservationorders/splitoperationresults/read | Interroge une opération de fractionnement |
> | Action | Microsoft.Capacity/reservationorders/swap/action | Échanger toutes les réservations |
> | Action | Microsoft.Capacity/reservationorders/write | Crée une réservation |
> | Action | Microsoft.Capacity/tenants/register/action | Inscrire tous les locataires |
> | Action | Microsoft.Capacity/unregister/action | Annuler l’inscription de tous les locataires |
> | Action | Microsoft.Capacity/validatereservationorder/action | Valider toutes les réservations |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Cdn/cdnwebapplicationfirewallmanagedrulesets/delete |  |
> | Action | Microsoft.Cdn/cdnwebapplicationfirewallmanagedrulesets/read |  |
> | Action | Microsoft.Cdn/cdnwebapplicationfirewallmanagedrulesets/write |  |
> | Action | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/delete |  |
> | Action | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/read |  |
> | Action | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/write |  |
> | Action | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Action | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/edgenodes/delete |  |
> | Action | Microsoft.Cdn/edgenodes/read |  |
> | Action | Microsoft.Cdn/edgenodes/write |  |
> | Action | Microsoft.Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/read |  |
> | Action | Microsoft.Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/write |  |
> | Action | Microsoft.Cdn/operationresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Action | Microsoft.Cdn/operationresults/read |  |
> | Action | Microsoft.Cdn/operationresults/write |  |
> | Action | Microsoft.Cdn/operations/read |  |
> | Action | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/profiles/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Action | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Action | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/read |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/write |  |
> | Action | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Action | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Action | Microsoft.Cdn/profiles/read |  |
> | Action | Microsoft.Cdn/profiles/write |  |
> | Action | Microsoft.Cdn/register/action | Inscrit l’abonnement pour le fournisseur de ressources CDN et active la création de profils CDN |
> | Action | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Supprimer un certificat existant |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Obtenir la liste des certificats |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Ajouter un nouveau certificat ou en mettre un existant à jour |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Delete | Supprimer un AppServiceCertificate existant |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Read | Obtenir la liste des CertificateOrders |
> | Action | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Réémettre un certificateorder existant |
> | Action | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Renouveler un certificateorder existant |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Renvoyer l’e-mail de certificat |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Renvoyer des e-mails de demande à une autre adresse de messagerie |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Récupérer le Seal de site pour un App Service Certificate émis |
> | Action | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Récupérer la liste des actions de certificat |
> | Action | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Récupérer l’historique d’e-mails de certificat |
> | Action | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Vérifier la propriété du domaine |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Write | Ajouter un nouveau certificateOrder ou en mettre un existant à jour |
> | Action | Microsoft.CertificateRegistration/operations/Read | Liste de toutes les opérations de l’enregistrement de certificat de service d’application |
> | Action | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Approvisionner le principal de service pour un principal d’application de service |
> | Action | Microsoft.CertificateRegistration/register/action | Inscrire le fournisseur de ressources de certificats Microsoft pour l’abonnement |
> | Action | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Valider l’objet d’achat de certificat sans le soumettre |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.ClassicCompute/capabilities/read | Affiche les fonctionnalités |
> | Action | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Vérifie la disponibilité d’un nom de domaine donné. |
> | Action | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Obtient la disponibilité d’un nom de domaine donné. |
> | Action | Microsoft.ClassicCompute/domainNames/active/write | Définit le nom de domaine actif. |
> | Action | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Affichez le groupe à haute disponibilité de la ressource. |
> | Action | Microsoft.ClassicCompute/domainNames/capabilities/read | Affiche les fonctionnalités de nom de domaine |
> | Action | Microsoft.ClassicCompute/domainNames/delete | Supprimez les noms de domaine pour les ressources. |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Affiche les emplacements de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Obtenir le rôle sur l’emplacement de déploiement du nom de domaine |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Obtenir l’instance de rôle pour le rôle sur l’emplacement de déploiement du nom de domaine |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Obtenir l’état des emplacements de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Ajouter l’état des emplacements de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Obtenir le domaine de mise à niveau pour l’emplacement de déploiement sur le nom de domaine |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Mettre à jour le domaine de mise à niveau pour l’emplacement de déploiement sur le nom de domaine |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Crée ou met à jour le déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/delete | Supprimez les extensions de nom de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Lit l’état de l’opération pour les extensions de noms de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/read | Retourne les extensions de nom de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/write | Ajoutez les extensions de nom de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Supprimez un nouvel équilibrage de charge interne. |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Lit l’état de l’opération pour les équilibreurs de charge interne de noms de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Obtient les équilibreurs de charge interne. |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Crée un nouvel équilibrage de charge interne. |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Lit l’état de l’opération pour les ensembles de points de terminaison à charge équilibrée de noms de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Obtenir les jeux de points de terminaison à charge équilibrée. |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Ajouter le jeu de points de terminaison à charge équilibrée. |
> | Action | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Obtenir l’état des opérations du nom de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Lit l’état de l’opération pour les extensions de noms de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/read | Retournez les noms de domaine pour les ressources. |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Supprimez les certificats de service utilisés. |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Lit l’état de l’opération pour les certificats de service de noms de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Retourne les certificats de service utilisés. |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Ajoutez ou modifiez les certificats de service utilisés. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Abandonne la migration d’un emplacement de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Valide la migration d’un emplacement de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/delete | Supprime un emplacement de déploiement donné. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Lit l’état de l’opération pour les emplacements de noms de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Prépare la migration d’un emplacement de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/read | Affiche les emplacements de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Supprimez la référence d’extension pour le rôle d’emplacement de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Lit l’état de l’opération pour les références d’extension de rôles d’emplacements de noms de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Retourne la référence d’extension pour le rôle d’emplacement de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Ajoutez ou modifiez la référence d’extension pour le rôle d’emplacement de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Obtenir la définition de la métrique des rôles du nom de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Obtenir la métrique des rôles du nom de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | Obtenir l’état des opérations du rôle d’emplacement des noms de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Obtient les paramètres des diagnostics. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Ajoutez ou modifiez des paramètres de diagnostics. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Obtient les définitions de mesures. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/read | Obtenez le rôle de l’emplacement de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Télécharge le fichier de connexion Bureau à distance de l’instance de rôle sur le rôle d’emplacement de nom de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Obtient l’état des opérations de l’instance de rôle sur le rôle d’emplacement des noms de domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Obtenez l’instance de rôle. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Regénère l’instance de rôle |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Réinitialise l’instance de rôle. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Redémarre des instances de rôle. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Obtenir la référence SKU du rôle pour l’emplacement de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/write | Ajouter un rôle pour l’emplacement de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/start/action | Démarre un emplacement de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Modifie l’état d’emplacement de déploiement sur arrêté. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Modifie l’état d’emplacement de déploiement sur démarré. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/stop/action | Suspend l’emplacement de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Parcourez la mise à niveau du domaine. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Valide la migration d’un emplacement de déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/write | Crée ou met à jour le déploiement. |
> | Action | Microsoft.ClassicCompute/domainNames/swap/action | Permute l’emplacement intermédiaire vers l’emplacement de production. |
> | Action | Microsoft.ClassicCompute/domainNames/write | Ajoutez ou modifiez les noms de domaine pour les ressources. |
> | Action | Microsoft.ClassicCompute/moveSubscriptionResources/action | Déplacez toutes les ressources classiques vers un autre abonnement. |
> | Action | Microsoft.ClassicCompute/operatingSystemFamilies/read | Répertorie les familles de systèmes d’exploitation invités disponibles dans Microsoft Azure ainsi que les versions de système d’exploitation disponibles pour chaque famille |
> | Action | Microsoft.ClassicCompute/operatingSystems/read | Répertorie les versions de système d’exploitation invité qui sont actuellement disponibles dans Microsoft Azure. |
> | Action | Microsoft.ClassicCompute/operations/read | Obtient la liste des opérations. |
> | Action | Microsoft.ClassicCompute/operationStatuses/read | Lit l’état de l’opération pour la ressource. |
> | Action | Microsoft.ClassicCompute/quotas/read | Obtient le quota de l’abonnement. |
> | Action | Microsoft.ClassicCompute/register/action | S’inscrire à Classic Compute |
> | Action | Microsoft.ClassicCompute/resourceTypes/skus/read | Obtient la liste des références (SKU) pour les types de ressources pris en charge. |
> | Action | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Validez la disponibilité de l’abonnement pour l’opération de déplacement classique. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Supprime le groupe de sécurité réseau associé à la machine virtuelle. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Lit l’état de l’opération pour les machines virtuelles associées à des groupes de sécurité réseau. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Obtient le groupe de sécurité réseau associé à la machine virtuelle. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Ajoute un groupe de sécurité réseau associé à la machine virtuelle. |
> | Action | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Obtient les opérations asynchrones possibles |
> | Action | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Attache un disque de données à une machine virtuelle. |
> | Action | Microsoft.ClassicCompute/virtualMachines/capture/action | Capturer une machine virtuelle. |
> | Action | Microsoft.ClassicCompute/virtualMachines/delete | Supprime des machines virtuelles. |
> | Action | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Détache un disque de données d’une machine virtuelle. |
> | Action | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Obtenir les paramètres de diagnostic de machine virtuelle. |
> | Action | Microsoft.ClassicCompute/virtualMachines/disks/read | Récupère la liste des disques de données |
> | Action | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Télécharge le fichier RDP pour la machine virtuelle. |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Lit l’état de l’opération pour les extensions de machines virtuelles. |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/read | Obtient l’extension de machine virtuelle. |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/write | Place l’extension de machine virtuelle. |
> | Action | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Obtenir la définition de la métrique de machine virtuelle. |
> | Action | Microsoft.ClassicCompute/virtualMachines/metrics/read | Obtient les mesures. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Supprime le groupe de sécurité réseau associé à l’interface réseau. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Lit l’état de l’opération pour les machines virtuelles associées à des groupes de sécurité réseau. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Obtient le groupe de sécurité réseau associé à l’interface réseau. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Ajoute un groupe de sécurité réseau associé à l’interface réseau. |
> | Action | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Lit l’état de l’opération pour les machines virtuelles. |
> | Action | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Effectue la maintenance de la machine virtuelle |
> | Action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Obtient les paramètres des diagnostics. |
> | Action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Ajoutez ou modifiez des paramètres de diagnostics. |
> | Action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Obtient les définitions de mesures. |
> | Action | Microsoft.ClassicCompute/virtualMachines/read | Récupère la liste des machines virtuelles. |
> | Action | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Redéploie la machine virtuelle. |
> | Action | Microsoft.ClassicCompute/virtualMachines/restart/action | Redémarre des machines virtuelles. |
> | Action | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Arrêtez la machine virtuelle. |
> | Action | Microsoft.ClassicCompute/virtualMachines/start/action | Démarrez la machine virtuelle. |
> | Action | Microsoft.ClassicCompute/virtualMachines/stop/action | Arrête la machine virtuelle. |
> | Action | Microsoft.ClassicCompute/virtualMachines/write | Ajouter ou modifier des machines virtuelles. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Obtenir l'état d'une opération d’interconnexion ExpressRoute |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Supprimer le peering d’interconnexion ExpressRoute |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Obtenir l'état d'une opération de peering d’interconnexion ExpressRoute |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Obtenir un peering d’interconnexion ExpressRoute |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Ajouter un peering d’interconnexion ExpressRoute |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Obtenir les interconnexions ExpressRoute |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Ajouter des interconnexions ExpressRoute |
> | Action | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Récupère la liste des appareils pris en charge. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Supprime le groupe de sécurité réseau. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Lit l’état de l’opération pour le groupe de sécurité réseau. |
> | Action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Obtient les paramètres de diagnostic des groupes de sécurité réseau |
> | Action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Créer ou mettre à jour les paramètres de diagnostic des groupes de sécurité réseau, cette opération étant complétée par le fournisseur de ressources Insights. |
> | Action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Obtient les événements pour le groupe de sécurité réseau |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/read | Obtient le groupe de sécurité réseau. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Supprime la règle de sécurité. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Lit l’état de l’opération pour les règles de sécurité du groupe de sécurité réseau. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Obtient la règle de sécurité. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Ajoute ou met à jour une règle de sécurité. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/write | Ajoute un nouveau groupe de sécurité réseau. |
> | Action | Microsoft.ClassicNetwork/operations/read | Obtenir les opérations réseau classiques |
> | Action | Microsoft.ClassicNetwork/quotas/read | Obtient le quota de l’abonnement. |
> | Action | Microsoft.ClassicNetwork/register/action | S’inscrire à un réseau classique |
> | Action | Microsoft.ClassicNetwork/reservedIps/delete | Supprimez une adresse IP réservée. |
> | Action | Microsoft.ClassicNetwork/reservedIps/join/action | Joindre une adresse IP réservée |
> | Action | Microsoft.ClassicNetwork/reservedIps/link/action | Lier une adresse IP réservée |
> | Action | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Lit l’état de l’opération pour les adresses IP réservées. |
> | Action | Microsoft.ClassicNetwork/reservedIps/read | Obtient les adresses IP réservées |
> | Action | Microsoft.ClassicNetwork/reservedIps/write | Ajouter une nouvelle adresse IP réservée |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Abandonner la migration d'un réseau virtuel |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Affiche les fonctionnalités |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Vérifie la disponibilité d’une adresse IP donnée dans un réseau virtuel. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Confirmer la migration d'un réseau virtuel |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/delete | Supprime le réseau virtuel. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Annule la révocation d’un certificat client. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Lisez les certificats clients révoqués. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Révoque un certificat client. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Supprime le certificat client de passerelle de réseau virtuel. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Télécharge le certificat par empreinte. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Répertorie le package de certificat de passerelle de réseau virtuel. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Recherchez les certificats racine client. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Télécharge un nouveau certificat racine client. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Établit une connexion de passerelle site à site. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Déconnecte une connexion de passerelle site à site. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Récupère la liste des connexions. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Teste une connexion de passerelle site à site. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Supprime la passerelle de réseau virtuel. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Télécharge le script de configuration d’appareil. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Télécharge les diagnostics de la passerelle. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Récupère la clé de service du circuit. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Répertorie le package de passerelle de réseau virtuel. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Lit l’état de l’opération pour les passerelles de réseaux virtuels. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Obtient le package de passerelle de réseau virtuel. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Obtient les passerelles de réseau virtuel. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Démarre le diagnostic pour la passerelle de réseau virtuel. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Arrête le diagnostic pour la passerelle de réseau virtuel. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Ajoute une passerelle de réseau virtuel. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/join/action | Joint le réseau virtuel. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Lit l’état de l’opération pour les réseaux virtuels. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Appaire un réseau virtuel avec un autre réseau virtuel. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Préparer la migration d'un réseau virtuel |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/read | Obtenez le réseau virtuel. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Supprimer le proxy de peering de réseau virtuel distant |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Obtenir le proxy de peering de réseau virtuel distant |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Ajouter ou mettre à jour le proxy de peering de réseau virtuel distant |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Supprime le groupe de sécurité réseau associé au sous-réseau. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Lit l’état de l’opération pour le sous-réseau de réseau virtuel associé au groupe de sécurité réseau. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Obtient le groupe de sécurité réseau associé au sous-réseau. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Ajoute un groupe de sécurité réseau associé au sous-réseau. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Valider la migration d'un réseau virtuel |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Obtenir le peering de réseau virtuel |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/write | Ajoutez un nouveau réseau virtuel. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.ClassicStorage/capabilities/read | Affiche les fonctionnalités |
> | Action | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Vérifie la disponibilité d’un compte de stockage. |
> | Action | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Obtenir la disponibilité d'un compte de stockage |
> | Action | Microsoft.ClassicStorage/disks/read | Retourne le disque du compte de stockage. |
> | Action | Microsoft.ClassicStorage/images/operationstatuses/read | Obtenir l'état d'une opération d'image |
> | Action | Microsoft.ClassicStorage/images/read | Retourne l’image. |
> | Action | Microsoft.ClassicStorage/operations/read | Obtenir les opérations de stockage classique |
> | Action | Microsoft.ClassicStorage/osImages/read | Retourne l’image du système d’exploitation. |
> | Action | Microsoft.ClassicStorage/osPlatformImages/read | Obtenir l'image de plateforme du système d'exploitation |
> | Action | Microsoft.ClassicStorage/publicImages/read | Obtient l’image de machine virtuelle publique. |
> | Action | Microsoft.ClassicStorage/quotas/read | Obtient le quota de l’abonnement. |
> | Action | Microsoft.ClassicStorage/register/action | S’inscrire à un stockage classique |
> | Action | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Abandonner la migration d'un compte de stockage |
> | Action | Microsoft.ClassicStorage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read | Obtient les paramètres des diagnostics. |
> | Action | Microsoft.ClassicStorage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write | Ajoutez ou modifiez des paramètres de diagnostics. |
> | Action | Microsoft.ClassicStorage/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read | Obtient les définitions de mesures. |
> | Action | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Valider la migration d'un compte de stockage |
> | Action | Microsoft.ClassicStorage/storageAccounts/delete | Supprime le compte de stockage. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/delete | Supprime un disque de compte de stockage spécifique. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Lit l’état de l’opération pour la ressource. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/read | Retourne le disque du compte de stockage. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/write | Ajoute un disque de compte de stockage. |
> | Action | Microsoft.ClassicStorage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read | Obtient les paramètres des diagnostics. |
> | Action | Microsoft.ClassicStorage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write | Ajoutez ou modifiez des paramètres de diagnostics. |
> | Action | Microsoft.ClassicStorage/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read | Obtient les définitions de mesures. |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/delete | Supprime une image du compte de stockage spécifique. (Déconseillé. Utilisez « Microsoft.ClassicStorage/storageAccounts/vmImages ») |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Retourner l'état d'opération d'image du compte de stockage |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/read | Retourne l’image du compte de stockage. (Déconseillé. Utilisez « Microsoft.ClassicStorage/storageAccounts/vmImages ») |
> | Action | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Répertorie les clés d’accès des comptes de stockage. |
> | Action | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Lit l’état de l’opération pour la ressource. |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Supprime une image du système d’exploitation de compte de stockage spécifique. |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/read | Retourne l’image du système d’exploitation de compte de stockage. |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/write | Ajouter une image du système d'exploitation du compte de stockage donné |
> | Action | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Préparer la migration d'un compte de stockage |
> | Action | Microsoft.ClassicStorage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Obtient les paramètres des diagnostics. |
> | Action | Microsoft.ClassicStorage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Ajoutez ou modifiez des paramètres de diagnostics. |
> | Action | Microsoft.ClassicStorage/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read | Obtient les définitions de mesures. |
> | Action | Microsoft.ClassicStorage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read | Obtient les paramètres des diagnostics. |
> | Action | Microsoft.ClassicStorage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write | Ajoutez ou modifiez des paramètres de diagnostics. |
> | Action | Microsoft.ClassicStorage/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read | Obtient les définitions de mesures. |
> | Action | Microsoft.ClassicStorage/storageAccounts/read | Retourne le compte de stockage avec le compte spécifique. |
> | Action | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Régénère les clés d’accès existantes du compte de stockage. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Obtient les paramètres des diagnostics. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Ajoutez ou modifiez des paramètres de diagnostics. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Obtient les définitions de mesures. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Obtient les mesures. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/read | Obtient les services disponibles. |
> | Action | Microsoft.ClassicStorage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read | Obtient les paramètres des diagnostics. |
> | Action | Microsoft.ClassicStorage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write | Ajoutez ou modifiez des paramètres de diagnostics. |
> | Action | Microsoft.ClassicStorage/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read | Obtient les définitions de mesures. |
> | Action | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Confirmer la migration d'un compte de stockage |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Supprimer une image de machine virtuelle spécifique |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Obtenir l'état d'une opération d'image de machine virtuelle donnée |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Retourner l'image de machine virtuelle |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Ajouter une image de machine virtuelle donnée |
> | Action | Microsoft.ClassicStorage/storageAccounts/write | Ajoute un nouveau compte de stockage. |
> | Action | Microsoft.ClassicStorage/vmImages/read | Répertorier les images de machine virtuelle |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | DataAction | Microsoft.CognitiveServices/accounts/AnomalyDetector/timeseries/entire/detect/action | Cette opération génère un modèle à l’aide d’une série entière, chaque point étant détecté avec le même modèle.<br>Avec cette méthode, les points avant et après un certain point sont utilisés pour déterminer s’il s’agit d’une anomalie.<br>L’ensemble de la détection peut donner à l’utilisateur un état global de la série chronologique. |
> | DataAction | Microsoft.CognitiveServices/accounts/AnomalyDetector/timeseries/last/detect/action | Cette opération génère un modèle à l’aide de points avant le dernier point. Avec cette méthode, seuls les points historiques sont utilisés pour déterminer si le point cible est une anomalie. La dernière détection de points correspond au scénario de surveillance en temps réel des métriques métier. |
> | DataAction | Microsoft.CognitiveServices/accounts/Autosuggest/search/action | Cette opération propose des suggestions pour une requête donnée ou une requête partielle. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/analyze/action | Cette opération extrait un ensemble riche de caractéristiques visuelles basées sur le contenu de l’image.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/areaofinterest/action | Cette opération retourne un cadre englobant la zone la plus importante de l’image. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/describe/action | Cette opération génère une description d’une image dans un langage intelligible avec des phrases complètes.<br> La description est basée sur une collection de balises de contenu, qui sont également retournées par l’opération.<br>Plus d’une description peut être générée pour chaque image.<br> Les descriptions sont triées par score de confiance.<br>Toutes les descriptions sont en anglais. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/detect/action | Cette opération effectue la détection d’objets sur l’image spécifiée.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | Cette opération génère une image miniature avec la hauteur et la largeur spécifiées par l’utilisateur.<br> Par défaut, le service analyse l’image, identifie la région d’intérêt et génère des coordonnées de rognage intelligentes basées sur le retour sur la région d’intérêt.<br> Le rognage intelligent vous aide à spécifier des proportions différentes de celles de l’image d’entrée |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | Cette opération reconnaît le contenu au sein d’une image en appliquant un modèle spécifique à un domaine.<br> La liste des modèles spécifiques à un domaine pris en charge par l’API Vision par ordinateur peut être récupérée à l’aide de la requête GET /models.<br> Actuellement, l’API fournit les modèles propres au domaine suivants : célébrités, des points de repère. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/read | Cette opération renvoie la liste des modèles propres à un domaine qui sont pris en charge par l’API Vision par ordinateur.  Actuellement, l’API prend en charge les modèles propres au domaine suivants : module de reconnaissance de célébrités, module de reconnaissance d’éléments géographiques. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/ocr/action | Avec la reconnaissance optique de caractères (OCR), détectez du texte dans une image et extrayez les caractères reconnus dans un flux de caractères exploitable automatiquement.    |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/read/analyze/action | Utilisez cette interface pour effectuer une opération de lecture, en utilisant les algorithmes de reconnaissance optique de caractères (OCR) de pointe optimisés pour les documents contenant beaucoup de texte.<br>Elle peut traiter des documents manuscrits, imprimés ou mixtes.<br>Lorsque vous utilisez l’interface de lecture, la réponse contient un en-tête appelé « Operation-Location ».<br>L’en-tête « Opération-Location » contient l’URL que vous devez utiliser pour que votre opération de récupération des résultats de la lecture accède aux résultats de l’OCR. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/read/analyzeresults/read | Utilisez cette interface pour récupérer l’état et le résultat OCR d’une opération de lecture.  L’URL contenant « operationId » est retournée dans l’en-tête de réponse « Operation-Location » de l’opération de lecture. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/read/core/asyncbatchanalyze/action | Utilisez cette interface pour obtenir le résultat d’une opération de lecture de fichiers par lot, en utilisant la reconnaissance optique de caractères. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/read/operations/read | Cette interface est utilisée pour obtenir les résultats OCR de lecture. L’URL de cette interface doit être récupérée à partir du champ <b>« Operation-Location »</b> retourné par l’interface Lire des fichiers par lot. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | Utilisez cette interface pour obtenir le résultat d’une opération Reconnaître le texte. Lorsque vous utilisez l’interface Reconnaître le texte, la réponse contient un champ appelé « Operation-Location ». Le champ « Opération-Location » contient l’URL que vous devez utiliser pour que votre opération obtienne le résultat de l’opération Reconnaître le texte. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/tag/action | Cette opération génère une liste de mots, ou balises, qui sont pertinents pour le contenu de l’image fournie.<br>L’API Vision par ordinateur peut retourner des balises basées sur les objets, les êtres vivants, les paysages ou les actions trouvées dans les images.<br>Contrairement aux catégories, les balises ne sont pas organisées selon un système de classification hiérarchique, mais correspondent au contenu de l’image.<br>Les balises peuvent contenir des indications pour éviter toute ambiguïté ou fournir un contexte, par exemple la balise « cello » peut être accompagnée de la précision « instrument de musique ».<br>Toutes les balises sont en anglais. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/textoperations/read | Cette interface est utilisée pour l’obtention du résultat de l’opération de reconnaissance de texte. L’URL de cette interface doit être récupérée à partir du champ <b>« Operation-Location »</b> retourné par l’interface Reconnaître le texte. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/action | Crée une liste d’images. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/delete | Listes d’images – Supprimer |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/images/delete | Supprime une image de votre liste d’images. La liste d’images peut être utilisée pour effectuer une correspondance approximative avec d’autres images lors de l’utilisation de l’API Image/Match. Supprime toutes les images de votre liste. La liste d’images peut être utilisée pour effectuer une correspondance approximative avec d’autres images lors de l’utilisation de l’API Image/Match.* |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/images/read | Image – Obtenir tous les ID d’image |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/images/write | Ajoute une image à votre liste d’images. La liste d’images peut être utilisée pour effectuer une correspondance approximative avec d’autres images lors de l’utilisation de l’API Image/Match. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/read | Listes d’images – Obtenir les détails – Listes d’images – Obtenir tout |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/refreshindex/action | Listes d’images – Actualiser l’index de recherche |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/write | Listes d’images – Mettre à jour les détails |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/processimage/evaluate/action | Retourne les probabilités que l’image contienne du contenu osé ou pour adulte. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/processimage/findfaces/action | Détecte les visages dans les images. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/processimage/match/action | Recherche une correspondance approximative entre une image et l’une de vos listes d’images personnalisées.<br>Vous pouvez créer et gérer vos listes d’images personnalisées à l’aide de cette API.<br> |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/processimage/ocr/action | Retourne tout texte trouvé dans l’image pour la langue spécifiée. Si aucune langue n’est spécifiée en entrée, l’anglais est détecté par défaut. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/processtext/detectlanguage/action | Cette opération va détecter la langue du contenu d’entrée donné.<br>Retourne le code ISO 639-3 pour la langue prédominante du texte envoyé.<br>Plus de 110 langues prises en charge. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/processtext/screen/action | L’opération détecte un blasphème dans plus de 100 langues et compare à des listes noires personnalisées et partagées. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/jobs/action | Un ID de travail est retourné pour le contenu de l’image publié sur ce point de terminaison.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/jobs/read | Obtient les détails du travail pour un ID de travail. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/accesskey/read | Obtient la clé d’accès au contenu révisé pour votre équipe. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/action | Les révisions créées s’affichent pour les réviseurs de votre équipe. À mesure que les réviseurs terminent la révision, les résultats sont PUBLIÉS (par ex. : HTTP POST) sur le CallBackEndpoint spécifié. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/frames/read | *NotDefined* |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/frames/write | Utilisez cette méthode pour ajouter des images pour une révision de vidéo. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/publish/action | Les révisions de vidéo sont initialement créées dans un état non publié, ce qui signifie qu’elles ne sont pas encore disponibles pour les réviseurs de votre équipe. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/read | Retourne les détails de la révision pour l’ID de révision passé. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/transcript/action | Cette API ajoute un fichier de transcription (version texte de tous les mots prononcés dans une vidéo) à une révision de vidéo. Le fichier doit être dans un format WebVTT valide. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/transcriptmoderationresult/action | Cette API ajoute un fichier texte de transcription à l’écran pour une révision de vidéo. Le fichier texte de transcription à l’écran est le résultat de l’API Screen Text. Pour générer le fichier texte de transcription à l’écran, un fichier de transcription doit être passé au crible afin de déceler tout blasphème à l’aide de l’API Screen Text. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/settings/templates/delete | Supprime un modèle dans votre équipe |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/settings/templates/read | Retourne un tableau de modèles de révision approvisionnés dans cette équipe. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/settings/templates/write | Crée ou met à jour le modèle spécifié |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/workflows/read | Obtient les détails d’un workflow spécifique dans votre équipe. Obtient tous les workflows disponibles pour votre équipe* |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/workflows/write | Crée un nouveau workflow ou en met un à jour. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/action | Crée une liste de termes. |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/bulkupdate/action | Listes de termes – Mise à jour en bloc |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/delete | Listes de termes – Supprimer |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/read | Listes de termes – Obtenir tout – Listes de termes – Obtenir les détails |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/refreshindex/action | Listes de termes – Actualiser l’index de recherche |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/terms/delete | Terme – Supprimer – Terme – Supprimer tous les termes |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/terms/read | Terme – Obtenir tous les termes |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/terms/write | Terme – Ajouter un terme |
> | DataAction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/write | Listes de termes – Mettre à jour les détails |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/image/action | Classifie une image et enregistre le résultat. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/image/nostore/action | Classifie une image sans enregistrer le résultat. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/url/action | Classifie une URL d’image et enregistre le résultat. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/url/nostore/action | Classifie une URL d’image sans enregistrer le résultat. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/image/action | Détecte les objets dans une image et enregistre le résultat. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/image/nostore/action | Détecte les objets dans une image sans enregistrer le résultat. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/url/action | Détecte les objets dans une URL d’image et enregistre le résultat. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/url/nostore/action | Détecte les objets dans une URL d’image sans enregistrer le résultat. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/domains/read | Obtient des informations sur un domaine spécifique. Obtient la liste des domaines disponibles.* |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/labelproposals/setting/action | Définit la taille du pool de la proposition d’étiquette. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/labelproposals/setting/read | Obtient la taille de pool de la proposition d’étiquette pour ce projet. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/project/migrate/action | *NotDefined* |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/action | Crée un projet. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/delete | Supprime un projet spécifique. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/action | Cette API accepte le contenu du corps comme multipart/form-data et application/octet-stream. En cas d’utilisation de plusieurs parties |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/delete | Supprime des images de l’ensemble d’images de formation. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/files/action | Cette API accepte un lot de fichiers, et éventuellement des étiquettes, pour créer des images. Il existe une limite de 64 images et 20 étiquettes. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/id/read | Cette API retourne un ensemble d’images pour les étiquettes spécifiées et éventuellement une itération. Si aucune itération n’est spécifiée, |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/predictions/action | Cette API crée un lot d’images à partir d’images prédites spécifiées. Il existe une limite de 64 images et 20 étiquettes. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/regionproposals/action | Cette API permet d’obtenir des propositions de région pour une image, ainsi que des confiances pour la région. Elle retourne un tableau vide si aucune proposition n’est trouvée. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/regions/action | Cette API accepte un lot de régions d’images et éventuellement des étiquettes pour mettre à jour les images existantes avec les informations de région. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/regions/delete | Supprime un ensemble de régions d’images. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/suggested/action | Cette API récupère les images non étiquetées filtrées par les ID d’étiquettes suggérés. Elle retourne un tableau vide si aucune image n’est trouvée. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/suggested/count/action | Cette API prend en compte les ID des étiquettes pour obtenir le nombre d'images non étiquetées par suggestion d'étiquette pour un seuil donné. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/tagged/count/read | Le filtrage est paramétré sur une relation et/ou. Par exemple, si les ID d’étiquette fournis sont pour le « chien » et |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/tagged/read | Cette API prend en charge le traitement par lot et la sélection de plage. Par défaut, elle retourne uniquement les 50 premières images correspondant à des images. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/tags/action | Associe un ensemble d’images à un ensemble d’étiquettes. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/tags/delete | Supprime un ensemble d’étiquettes d’un ensemble d’images. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/untagged/count/read | Cette API retourne les images qui n’ont aucune étiquette pour un projet donné et éventuellement une itération. Si aucune itération n’est spécifiée, |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/untagged/read | Cette API prend en charge le traitement par lot et la sélection de plage. Par défaut, elle retourne uniquement les 50 premières images correspondant à des images. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/urls/action | Cette API accepte un lot d’URL, et éventuellement des étiquettes, pour créer des images. Il existe une limite de 64 images et 20 étiquettes. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/delete | Supprime une itération spécifique d’un projet. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/export/action | Exporte une itération formée. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/export/read | Obtient la liste des exportations pour une itération spécifique. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/performance/images/count/read | Le filtrage est paramétré sur une relation et/ou. Par exemple, si les ID d’étiquette fournis sont pour le « chien » et |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/performance/images/read | Cette API prend en charge le traitement par lot et la sélection de plage. Par défaut, elle retourne uniquement les 50 premières images correspondant à des images. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/performance/read | Obtient des informations détaillées sur les performances d’une itération. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/publish/action | Publie une itération spécifique. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/publish/delete | Annule la publication d’une itération spécifique. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/read | Obtient une itération spécifique. Obtient les itérations du projet.* |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/write | Met à jour une itération spécifique. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/predictions/delete | Supprime un ensemble d’images prédites et leurs résultats de prédiction associés. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/predictions/query/action | Obtient les images qui ont été envoyées à votre point de terminaison de prédiction. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/quicktest/image/action | Teste rapidement une image. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/quicktest/url/action | Teste rapidement une URL d’image. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/read | Obtient un projet spécifique. Obtient vos projets.* |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/action | Crée une étiquette pour le projet. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/delete | Supprime une étiquette du projet. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/read | Obtient des informations sur une étiquette spécifique. Obtient les étiquettes pour un projet et une itération donnés.* |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/write | Met à jour une étiquette. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tagsandregions/suggestions/action | Cette API recevra des suggestions d’étiquette et de région pour un tableau/lot d’images non étiquetées, ainsi que des confiances pour les étiquettes. Elle retourne un tableau vide si aucune étiquette n’est trouvée. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/train/action | Met en file d’attente le projet pour en effectuer l’apprentissage. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/write | Met à jour un projet spécifique. |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/quota/action | *NotDefined* |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/quota/delete | *NotDefined* |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/quota/refresh/write | *NotDefined* |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/usage/prediction/user/read | Obtient l’utilisation de la ressource de prédiction pour l’utilisateur Oxford |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/usage/training/resource/tier/read | Obtient l’utilisation de la ressource de formation pour l’utilisateur Azure |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/usage/training/user/read | Obtient l’utilisation de la ressource de formation pour l’utilisateur Oxford |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/action | *NotDefined* |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/delete | *NotDefined* |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/state/write | Met à jour l’état utilisateur |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/tier/write | *NotDefined* |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/users/read | *NotDefined* |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/whitelist/delete | Supprime un utilisateur figurant sur la liste verte et doté de capacités spécifiques |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/whitelist/read | Obtient une liste d’utilisateurs figurant sur la liste verte et dotés de capacités spécifiques |
> | DataAction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/whitelist/write | Met à jour ou crée un utilisateur dans la liste verte avec des capacités spécifiques |
> | Action | Microsoft.CognitiveServices/accounts/delete | Supprime les comptes d’API |
> | DataAction | Microsoft.CognitiveServices/accounts/EntitySearch/search/action | Obtient les entités et résultats de lieux d'une requête donnée. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/detect/action | Détectez les visages humains dans une image, retournez des rectangles de visage et éventuellement des faceId, des points de repère et des attributs. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/delete | Supprime une liste de visages spécifiée. Les images de visage connexes dans la liste de visages seront supprimées aussi. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | Supprime un visage d’une liste de visages selon les valeurs persisitedFaceId et faceListId spécifiées. L’image de visage connexe est également supprimée. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | Ajoutez un visage à une liste de visages spécifiée, jusqu'à 1 000 visages. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Récupérez les propriétés faceListId, name, userData et face dans la liste de visages. Répertorie les propriétés faceListId, name et userData de la liste de visages. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Crée une liste de visages vide avec les valeurs faceListId, name et éventuellement userData spécifiées par l’utilisateur. Jusqu'à 64 listes de visages sont autorisées. Met à jour les informations d’une liste de visages, notamment name et userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | En fonction du faceId de la requête, recherche les visages similaires à partir d’un tableau de faceId, d’une liste de visages ou d’une grande liste de visages. faceId |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/group/action | Divise les visages des candidats en groupes en fonction de la similarité du visage. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/identify/action | L’identification de 1 à plusieurs pour rechercher les correspondances les plus proches pour le visage de la personne spécifiée dans la requête à partir d’un groupe de personnes ou d’un grand groupe de personnes. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | Supprime une grande liste de visages spécifiée. Les images de visage connexes dans la grande liste de visages seront supprimées aussi. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | Supprime un visage d’une grande liste de visages selon les valeurs persisitedFaceId et largeFaceListId spécifiées. L’image de visage connexe est également supprimée. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | Récupère le visage persistant dans la grande liste de visages en fonction des valeurs largeFaceListId et persistedFaceId. Répertorie les valeurs persistedFaceId et userData dans une grande liste de visages spécifiée. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | Ajoutez un visage à une grande liste de visages spécifiée, jusqu'à 1 000 000 visages. Met à jour de champ userData d’un visage spécifié dans une grande liste de visages en fonction du persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | Récupère les valeurs largeFaceListId name et userData d’une grande liste de visages. Répertorie les valeurs largeFaceListId, name et userData pour une grande liste de visages. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | Envoie une tâche d’apprentissage de grande liste de visages. L’apprentissage est une étape essentielle que seule une grande liste de visages entraînée peut utiliser. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | Pour vérifier l’état d’apprentissage d’une grande liste de visages, en cours ou terminé. L’apprentissage d’une grande liste de visages est une opération asynchrone |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | Crée une grande liste de visages vide avec les valeurs largeFaceListId, name et éventuellement userData spécifiées par l’utilisateur. Met à jour les informations d’une grande liste de visages, notamment name et userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | Supprime un grand groupe de personnes avec le personGroupId spécifié. Les données persistantes dans ce grand groupe de personnes seront supprimées. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | Crée une nouvelle personne dans un grand groupe de personnes spécifié. Pour ajouter un visage à cette personne, appelez |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | Supprime une personne existante d’un grand groupe de personnes. Toutes les données de personne et images de visage stockées dans l’entrée de la personne seront supprimées. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Supprime le visage d’une personne dans un grand groupe de personnes. L’image et les données de visage liées à cette entrée de visage sont également supprimées. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | Récupère les informations de visage de la personne. Le visage de la personne persistante est spécifié par ses valeurs largePersonGroupId, personId et persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | Ajoute une image de visage à une personne dans un grand groupe de personnes pour l’identification ou la vérification des visages. Pour traiter l’image. Met à jour le champ userData de visage persistant d’une personne. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | Récupère les champs name et userData d’une personne ainsi que les faceIds persistants qui représentent l’image du visage de la personne inscrite. Répertorie les informations de toutes les personnes dans le grand groupe de personnes spécifié, notamment les champs personId, name, userData et persistedFaceIds. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | Met à jour le champ name ou userData d’une personne. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | Récupère les informations d’un grand groupe de personnes, notamment les champs name et userData. Cette API retourne les informations d’un grand groupe de personnes. Répertorie les tous champs largePesonGroupId name et userData d’un grand groupe de personnes existant. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | Envoie une tâche d’apprentissage de grand groupe de personnes. L’apprentissage est une étape essentielle que seul un grand groupe de personnes entraîné peut utiliser. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | Pour vérifier l’état d’apprentissage d’un grand groupe de personnes, en cours ou terminé. L’apprentissage d’un grand groupe de personnes est une opération asynchrone |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | Crée un nouveau grand groupe de personnes avec les valeurs largePersonGroupId, name et éventuellement userData spécifiées par l’utilisateur. Met à jour les champs name et userData d’un grand groupe de personnes existant. Les propriétés restent inchangées si elles ne sont pas dans le corps de la demande. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | Supprime un groupe de personnes avec le personGroupId spécifié. Les données persistantes dans ce groupe de personnes seront supprimées. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | Crée une nouvelle personne dans un groupe de personnes spécifié. Pour ajouter un visage à cette personne, appelez |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | Supprime une personne existante d’un groupe de personnes. Toutes les données de personne et images de visage stockées dans l’entrée de la personne seront supprimées. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | Supprime le visage d’une personne dans un groupe de personnes. L’image et les données de visage liées à cette entrée de visage sont également supprimées. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | Récupère les informations de visage de la personne. Le visage de la personne persistante est spécifié par ses valeurs personGroupId, personId et persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | Ajoute une image de visage à une personne dans un groupe de personnes pour l’identification ou la vérification des visages. Pour traiter plusieurs images. Met à jour le champ userData de visage persistant d’une personne. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | Récupère les champs name et userData d’une personne ainsi que les faceIds persistants qui représentent l’image du visage de la personne inscrite. Répertorie les informations de toutes les personnes dans le groupe de personnes spécifié, notamment les champs personId, name, userData et persistedFaceIds. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | Met à jour le champ name ou userData d’une personne. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/read | Récupère les champs name et userData d’un groupe de personne. Pour obtenir des informations sur les personnes de ce groupe de personnes, répertoriez les valeurs name, pesonGroupId, nom et userData du groupe de personnes. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | Envoie une tâche d’apprentissage de groupe de personnes. L’apprentissage est une étape essentielle que seule un groupe de personnes entraîné peut utiliser. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | Pour vérifier l’état d’apprentissage d’un groupe de personnes, en cours ou terminé. L’apprentissage d’un groupe de personnes est une opération asynchrone déclenchée |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/write | Crée un nouveau groupe de personnes avec les valeurs personGroupId, name et userData fournies par l’utilisateur. Met à jour les champs name et userData d’un groupe de personnes existant. Les propriétés restent inchangées si elles ne sont pas dans le corps de la demande. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/snapshot/apply/action | Applique une capture instantanée, en fournissant un ID d'objet spécifié par l'utilisateur. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/snapshot/delete | Supprime une capture instantanée. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/snapshot/read | Permet d'obtenir l'état d'une opération de capture instantanée. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/snapshot/take/action | Effectue une capture instantanée d'un objet. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/snapshot/write | Met à jour les propriétés d'une capture instantanée. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/snapshots/read | Répertorie toutes les captures instantanées accessibles à l'utilisateur avec des informations.* |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/verify/action | Vérifiez si deux visages appartiennent à une même personne, ou si un visage appartient à une personne. |
> | DataAction | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/analyze/action | Extrait les paires clé-valeur d’un document donné. Le document d’entrée doit appartenir à l’un des types de contenu pris en charge : « application/pdf », « image/jpeg » ou « image/png ». Une réponse correcte est retournée au format JSON. |
> | DataAction | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/delete | Supprime les artefacts de modèle. |
> | DataAction | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/keys/read | Récupère les clés du modèle. |
> | DataAction | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/read | Obtient des informations sur un modèle. Obtient des informations sur tous les modèles personnalisés formés* |
> | DataAction | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/train/action | Crée un modèle personnalisé et en effectue l’apprentissage.<br>La demande de formation doit inclure un paramètre source qui est soit un URI de conteneur de blobs Stockage Azure accessible en externe (de préférence un URI de signature d’accès partagé), soit un chemin d’accès valide vers un dossier de données dans un lecteur monté localement.<br>Quand des chemins d’accès locaux sont spécifiés, ils doivent suivre le format de chemin d’accès Linux/Unix et être un chemin d’accès absolu enraciné dans la configuration de montage d’entrée. |
> | DataAction | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/receipt/asyncbatchanalyze/action | Extrait le texte de champ et les valeurs sémantiques d’un document de reçu donné. Le document d’image d’entrée doit appartenir à l’un des types de contenu pris en charge (JPEG, PNG, BMP, PDF ou TIFF). Une réponse réussie est un fichier JSON contenant un champ appelé « Operation-Location », qui contient l’URL de l’opération Obtenir le résultat du reçu afin de récupérer les résultats de façon asynchrone. |
> | DataAction | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/receipt/operations/action | Interroge l’état et récupère le résultat d’une opération Analyser le reçu. L’URL vers cette interface peut être obtenue à partir de l’en-tête « Operation-Location » dans la réponse de Analyser le reçu. |
> | DataAction | Microsoft.CognitiveServices/accounts/ImageSearch/details/action | Renvoie des informations à propos d’une image, comme les pages web où figure l’image. |
> | DataAction | Microsoft.CognitiveServices/accounts/ImageSearch/search/action | Obtient les images pertinentes pour une requête donnée. |
> | DataAction | Microsoft.CognitiveServices/accounts/ImageSearch/trending/action | Obtient les images actuellement tendance. |
> | DataAction | Microsoft.CognitiveServices/accounts/ImmersiveReader/getcontentmodelforreader/action | Crée une session de lecteur immersif |
> | DataAction | Microsoft.CognitiveServices/accounts/InkRecognizer/recognize/action | À partir d’un ensemble de données de trait, analyse le contenu et génère une liste d’entités reconnues, y compris le texte reconnu. |
> | Action | Microsoft.CognitiveServices/accounts/listKeys/action | Afficher la liste des clés |
> | DataAction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Obtient la prédiction du point de terminaison publié pour la requête donnée. |
> | DataAction | Microsoft.CognitiveServices/accounts/NewsSearch/categorysearch/action | Renvoie les actualités d'une catégorie fournie. |
> | DataAction | Microsoft.CognitiveServices/accounts/NewsSearch/search/action | Obtient des articles d'actualités pertinents pour une requête donnée. |
> | DataAction | Microsoft.CognitiveServices/accounts/NewsSearch/trendingtopics/action | Obtient les rubriques tendance identifiées par Bing. Ces rubriques sont les mêmes que celles indiquées dans la bannière en bas de la page d’accueil de Bing. |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read | Télécharge les modifications à partir du runtime. |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/alterations/write | Remplace les données de modification. |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read | Permet d'obtenir les clés d'un point de terminaison |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/refreshkeys/action | Recrée une clé de point de terminaison. |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read | Permet d'obtenir les paramètres d'un point de terminaison |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/write | Met à jour les paramètres d'un point de terminaison. |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/create/write | Opération asynchrone permettant de créer une base de connaissances. |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/delete | Supprime la base de connaissances et toutes ses données. |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read | Télécharge la base de connaissances. |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action | Appelle GenerateAnswer pour interroger la base de connaissances. |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/publish/action | Publie toutes les modifications de l'index de test d'une base de connaissances dans son index de prod. |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read | Permet d'obtenir la liste des bases de connaissances ou les détails d'une base de connaissances spécifique. |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/train/action | Former un appel pour ajouter des suggestions à la base de connaissances. |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/write | Opération asynchrone pour modifier une base de connaissances ou remplacer le contenu de la base de connaissances. |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/operations/read | Obtient les détails d’une opération durable spécifique. |
> | DataAction | Microsoft.CognitiveServices/accounts/QnAMaker/root/action | QnA Maker |
> | Action | Microsoft.CognitiveServices/accounts/read | Lit les comptes d’API. |
> | Action | Microsoft.CognitiveServices/accounts/regenerateKey/action | Régénérer la clé |
> | Action | Microsoft.CognitiveServices/accounts/skus/read | Lit les références disponibles pour une ressource existante. |
> | DataAction | Microsoft.CognitiveServices/accounts/SpellCheck/spellcheck/action | Obtient le résultat d'une requête de vérification orthographique par le biais de GET ou POST. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | L’API retourne une liste des entités connues et des entités nommées générales (\"Personne\", \"Emplacement\", \"Organisation\", etc.) dans un document donné. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | L’API renvoie une liste de chaînes indiquant les principaux propos suggérés dans le texte en entrée. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | L’API renvoie la langue détectée et un score numérique compris entre 0 et 1. Un score proche de 1 indique une certitude à 100 % que la langue identifiée est la bonne. Au total, 120 langues sont prises en charge. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | L’API retourne un score numérique compris entre 0 et 1.<br>Les valeurs de notation proches de 1 indiquent un sentiment positif, tandis que les valeurs proches de 0 signalent un sentiment négatif.<br>Un score de 0,5 indique l’absence de sentiment (par ex.<br>une expression factuelle). |
> | Action | Microsoft.CognitiveServices/accounts/usages/read | Obtenir l’utilisation du quota pour une ressource existante. |
> | DataAction | Microsoft.CognitiveServices/accounts/VideoSearch/details/action | Obtenez des insights sur une vidéo, par exemple des vidéos connexes. |
> | DataAction | Microsoft.CognitiveServices/accounts/VideoSearch/search/action | Obtient des vidéos pertinentes pour une requête donnée. |
> | DataAction | Microsoft.CognitiveServices/accounts/VideoSearch/trending/action | Obtient les vidéos actuellement tendance. |
> | DataAction | Microsoft.CognitiveServices/accounts/VisualSearch/search/action | Renvoie une liste de balises correspondant à l’image fournie |
> | DataAction | Microsoft.CognitiveServices/accounts/WebSearch/search/action | Obtient des résultats web, d'images, d'actualités et de vidéos pour une requête donnée. |
> | Action | Microsoft.CognitiveServices/accounts/write | Écrit les comptes d’API. |
> | Action | Microsoft.CognitiveServices/checkDomainAvailability/action | Lit les références SKU disponibles pour un abonnement. |
> | Action | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Lit les références SKU disponibles pour un abonnement. |
> | Action | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Lit les références SKU disponibles pour un abonnement. |
> | Action | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Notification de la part de Microsoft.Network de suppression de VirtualNetworks ou de sous-réseaux. |
> | Action | Microsoft.CognitiveServices/locations/operationresults/read | Lit l’état d’une opération asynchrone |
> | Action | Microsoft.CognitiveServices/Operations/read | Répertorie toutes les opérations disponibles |
> | Action | Microsoft.CognitiveServices/register/action | Enregistre l’abonnement à Cognitive Services |
> | Action | Microsoft.CognitiveServices/register/action | Enregistre l’abonnement à Cognitive Services |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Commerce/RateCard/read | Retourne les données, les métadonnées de ressource/compteur et les taux d’offre de l’abonnement concerné. |
> | Action | Microsoft.Commerce/register/action | Inscrit un abonnement à Microsoft Commerce UsageAggregate |
> | Action | Microsoft.Commerce/unregister/action | Désinscrit un abonnement à Microsoft Commerce UsageAggregate |
> | Action | Microsoft.Commerce/UsageAggregates/read | Récupère l’utilisation de Microsoft Azure par un abonnement. Le résultat contient des données d’utilisation, des informations sur l’abonnement et la ressource agrégées, sur un intervalle de temps donné. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Compute/availabilitySets/delete | Supprimer le groupe à haute disponibilité |
> | Action | Microsoft.Compute/availabilitySets/read | Obtenir les propriétés d’un groupe à haute disponibilité |
> | Action | Microsoft.Compute/availabilitySets/vmSizes/read | Répertorier les tailles disponibles pour la création ou la mise à jour d’une machine virtuelle dans un groupe à haute disponibilité |
> | Action | Microsoft.Compute/availabilitySets/write | Créer ou mettre à jour un groupe à haute disponibilité |
> | Action | Microsoft.Compute/diskEncryptionSets/delete | Supprimer un jeu de chiffrement de disque |
> | Action | Microsoft.Compute/diskEncryptionSets/read | Obtenir les propriétés d’un jeu de chiffrement de disque |
> | Action | Microsoft.Compute/diskEncryptionSets/write | Crée un jeu de chiffrement de disque ou en mettre un existant à jour |
> | Action | Microsoft.Compute/disks/beginGetAccess/action | Obtenir l’URI SAP du disque pour l’accès aux objets blob |
> | Action | Microsoft.Compute/disks/delete | Supprimer le disque |
> | Action | Microsoft.Compute/disks/endGetAccess/action | Révoquer l’URI SAP du disque |
> | Action | Microsoft.Compute/disks/read | Obtenir les propriétés d’un disque |
> | Action | Microsoft.Compute/disks/write | Créer ou mettre à jour un disque |
> | Action | Microsoft.Compute/galleries/applications/delete | Supprime l’application Gallery |
> | Action | Microsoft.Compute/galleries/applications/read | Obtient les propriétés de l’application Gallery |
> | Action | Microsoft.Compute/galleries/applications/versions/delete | Supprime la version de l’application Gallery |
> | Action | Microsoft.Compute/galleries/applications/versions/read | Obtient les propriétés de la version de l’application Gallery |
> | Action | Microsoft.Compute/galleries/applications/versions/write | Crée ou met à jour une version de l’application Gallery |
> | Action | Microsoft.Compute/galleries/applications/write | Crée ou met à jour une application Gallery |
> | Action | Microsoft.Compute/galleries/delete | Supprimer la galerie |
> | Action | Microsoft.Compute/galleries/images/delete | Supprimer l'image de galerie |
> | Action | Microsoft.Compute/galleries/images/read | Obtenir les propriétés de l'image de galerie |
> | Action | Microsoft.Compute/galleries/images/versions/delete | Supprimer la version de l'image de galerie |
> | Action | Microsoft.Compute/galleries/images/versions/read | Obtenir les propriétés de la version de l'image de galerie |
> | Action | Microsoft.Compute/galleries/images/versions/write | Créer ou mettre à jour une version d'image de galerie |
> | Action | Microsoft.Compute/galleries/images/write | Créer ou mettre à jour une image de galerie |
> | Action | Microsoft.Compute/galleries/read | Obtenir les propriétés de la galerie |
> | Action | Microsoft.Compute/galleries/write | Créer ou mettre à jour une galerie |
> | Action | Microsoft.Compute/hostGroups/delete | Supprime le groupe hôte |
> | Action | Microsoft.Compute/hostGroups/hosts/delete | Supprime l’hôte |
> | Action | Microsoft.Compute/hostGroups/hosts/read | Obtenir les propriétés d’un hôte |
> | Action | Microsoft.Compute/hostGroups/hosts/write | Créer ou mettre à jour un hôte |
> | Action | Microsoft.Compute/hostGroups/read | Obtenir les propriétés d’un groupe hôte |
> | Action | Microsoft.Compute/hostGroups/write | Crée un nouveau groupe hôte ou met à jour un groupe hôte existant |
> | Action | Microsoft.Compute/images/delete | Supprimer l’image |
> | Action | Microsoft.Compute/images/read | Obtenir les propriétés de l’image |
> | Action | Microsoft.Compute/images/write | Créer ou mettre à jour une image |
> | Action | Microsoft.Compute/locations/capsOperations/read | Obtient l’état d’une opération de plafonnement asynchrone |
> | Action | Microsoft.Compute/locations/diskOperations/read | Obtient l’état d’une opération de disque asynchrone |
> | Action | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Créer des journaux d’activité pour afficher le nombre total de requêtes par intervalle de temps pour participer aux diagnostics de limitation |
> | Action | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Créer des journaux d’activité pour afficher des agrégats de requêtes limitées regroupées par ResourceName, OperationName ou selon la stratégie de limitation appliquée. |
> | Action | Microsoft.Compute/locations/operations/read | Afficher l’état d’une opération asynchrone |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Obtient les propriétés d’une offre d’image de plateforme |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Obtient les propriétés d’une référence (SKU) d’image de plateforme |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Obtient les propriétés d’une version d’image de plateforme |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Obtient les propriétés d’un type d’extension de machine virtuelle |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Obtient les propriétés d’une version d’extension de machine virtuelle |
> | Action | Microsoft.Compute/locations/publishers/read | Obtient les propriétés d’un éditeur |
> | Action | Microsoft.Compute/locations/runCommands/read | Répertorie les commandes d’exécution disponibles dans un emplacement |
> | Action | Microsoft.Compute/locations/usages/read | Afficher les limites du service et les quantités en cours d’utilisation pour les ressources de calcul de l’abonnement dans un emplacement |
> | Action | Microsoft.Compute/locations/vmSizes/read | Répertorier les tailles de machines virtuelles disponibles dans un emplacement |
> | Action | Microsoft.Compute/locations/vsmOperations/read | Obtient l’état d’une opération asynchrone pour un groupe de machines virtuelles identiques avec l’extension du service de runtime de machine virtuelle |
> | Action | Microsoft.Compute/operations/read | Répertorier les opérations disponibles dans le fournisseur de ressources Microsoft.Compute |
> | Action | Microsoft.Compute/proximityPlacementGroups/delete | Supprime le groupe de placements de proximité |
> | Action | Microsoft.Compute/proximityPlacementGroups/read | Obtient les propriétés d’un groupe de placements de proximité |
> | Action | Microsoft.Compute/proximityPlacementGroups/write | Crée un nouveau groupe de placements de proximité ou le met à jour |
> | Action | Microsoft.Compute/register/action | Inscrit l’abonnement auprès du fournisseur de ressources Microsoft.Compute |
> | Action | Microsoft.Compute/restorePointCollections/delete | Supprimer la collection de points de restauration et les points de restauration contenus |
> | Action | Microsoft.Compute/restorePointCollections/read | Obtenir les propriétés d’une collection de points de restauration |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/delete | Supprimer le point de restauration |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/read | Obtenir les propriétés d’un point de restauration |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Obtenir les propriétés d’un point de restauration, ainsi que les URI SAP des objets blob |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/write | Créer un nouveau point de restauration |
> | Action | Microsoft.Compute/restorePointCollections/write | Crée une nouvelle collection de points de restauration ou met à jour une collection existante |
> | Action | Microsoft.Compute/sharedVMImages/delete | Supprime l’image de machine virtuelle partagée |
> | Action | Microsoft.Compute/sharedVMImages/read | Obtient les propriétés d’une image de machine virtuelle partagée |
> | Action | Microsoft.Compute/sharedVMImages/versions/delete | Supprime une version d’image de machine virtuelle partagée |
> | Action | Microsoft.Compute/sharedVMImages/versions/read | Obtient les propriétés d’une version image de machine virtuelle partagée |
> | Action | Microsoft.Compute/sharedVMImages/versions/replicate/action | Réplique une version d’image de machine virtuelle partagée dans des régions cibles |
> | Action | Microsoft.Compute/sharedVMImages/versions/write | Crée ou met à jour une version d’image de machine virtuelle partagée |
> | Action | Microsoft.Compute/sharedVMImages/write | Crée ou met à jour une image de machine virtuelle partagée |
> | Action | Microsoft.Compute/skus/read | Obtient la liste des références (SKU) Microsoft.Compute disponibles pour votre abonnement |
> | Action | Microsoft.Compute/snapshots/beginGetAccess/action | Obtient l’URI SAP de la capture instantanée pour l’accès aux objets blob |
> | Action | Microsoft.Compute/snapshots/delete | Supprimer une capture instantanée |
> | Action | Microsoft.Compute/snapshots/endGetAccess/action | Révoque l’URI SAP de la capture instantanée |
> | Action | Microsoft.Compute/snapshots/read | Obtenir les propriétés d’une capture instantanée |
> | Action | Microsoft.Compute/snapshots/write | Créer ou mettre à jour une capture instantanée |
> | Action | Microsoft.Compute/unregister/action | Désinscrit l’abonnement auprès du fournisseur de ressources Microsoft.Compute |
> | Action | Microsoft.Compute/virtualMachines/assessPatches/action | Évalue la machine virtuelle et recherche la liste des correctifs de mise à jour de système d’exploitation disponibles. |
> | Action | Microsoft.Compute/virtualMachines/cancelPatchInstallation/action | Annule l’opération d’installation de correctif de mise à jour de système d’exploitation en cours sur la machine virtuelle. |
> | Action | Microsoft.Compute/virtualMachines/capture/action | Capturer la machine virtuelle en copiant les disques durs virtuels et générer un modèle qui peut être utilisé pour créer des machines virtuelles similaires |
> | Action | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Convertir les disques basés sur des objets blob de la machine virtuelle en disques managés |
> | Action | Microsoft.Compute/virtualMachines/deallocate/action | Mettre hors tension la machine virtuelle et libérer les ressources de calcul |
> | Action | Microsoft.Compute/virtualMachines/delete | Supprimer la machine virtuelle |
> | Action | Microsoft.Compute/virtualMachines/extensions/delete | Supprimer l’extension de machine virtuelle |
> | Action | Microsoft.Compute/virtualMachines/extensions/read | Afficher les propriétés d’une extension de machine virtuelle |
> | Action | Microsoft.Compute/virtualMachines/extensions/write | Créer ou mettre à jour une extension de machine virtuelle |
> | Action | Microsoft.Compute/virtualMachines/generalize/action | Définir l’état de la machine virtuelle sur Généralisée et préparer la machine virtuelle pour la capture |
> | Action | Microsoft.Compute/virtualMachines/installPatches/action | Installe les correctifs de mise à jour de système d’exploitation disponibles sur la machine virtuelle en fonction des paramètres fournis par l’utilisateur. Les résultats de l’évaluation contenant la liste des correctifs disponibles seront également actualisés dans le cadre de cette opération. |
> | Action | Microsoft.Compute/virtualMachines/instanceView/read | Afficher l’état d’exécution détaillé de la machine virtuelle et de ses ressources |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Se connecter à la machine virtuelle comme utilisateur normal |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Se connecter à une machine virtuelle avec des privilèges d’administrateur Windows ou d’utilisateur racine Linux |
> | Action | Microsoft.Compute/virtualMachines/performMaintenance/action | Performs Maintenance Operation on the VM. |
> | Action | Microsoft.Compute/virtualMachines/powerOff/action | Mettre hors tension la machine virtuelle. Notez que la machine virtuelle sera toujours facturée. |
> | Action | Microsoft.Compute/virtualMachines/read | Obtenir les propriétés d’une machine virtuelle |
> | Action | Microsoft.Compute/virtualMachines/redeploy/action | Redéployer la machine virtuelle |
> | Action | Microsoft.Compute/virtualMachines/reimage/action | Réimage une machine virtuelle utilisant un disque de différenciation |
> | Action | Microsoft.Compute/virtualMachines/restart/action | Redémarrer la machine virtuelle |
> | Action | Microsoft.Compute/virtualMachines/runCommand/action | Exécute un script prédéfini sur la machine virtuelle |
> | Action | Microsoft.Compute/virtualMachines/start/action | Démarrer la machine virtuelle |
> | Action | Microsoft.Compute/virtualMachines/vmSizes/read | Répertorier les tailles disponibles pour la mise à jour de la machine virtuelle |
> | Action | Microsoft.Compute/virtualMachines/write | Créer ou mettre à jour une machine virtuelle |
> | Action | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Met hors tension et libère les ressources de calcul pour les instances du groupe de machines virtuelles identiques  |
> | Action | Microsoft.Compute/virtualMachineScaleSets/delete | Supprime le groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/delete/action | Supprime les instances du groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Supprime l’extension de groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Obtient les propriétés d’une extension de groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/roles/read | Obtient les propriétés d’un rôle dans un groupe de machines virtuelles identiques avec l’extension du service de runtime de machine virtuelle |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/roles/write | Met à jour les propriétés d’un rôle existant dans un groupe de machines virtuelles identiques avec l’extension du service de runtime de machine virtuelle |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Crée ou met à jour une extension de groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Nécessite de parcourir manuellement les domaines de mise à jour de plateforme d’un groupe de machines virtuelles identiques Service Fabric pour achever une mise à jour en attente bloquée |
> | Action | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Obtient la vue d’instance du groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Nécessite de mettre à jour manuellement les instances avec le dernier modèle du groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Obtient les propriétés de toutes les interfaces réseau d’un groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Démarre une mise à niveau propagée pour déplacer toutes les instances de groupe de machines virtuelles identiques vers la dernière version de système d’exploitation d’image de plateforme disponible. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Obtient l’historique des mises à niveau du système d’exploitation pour un groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Effectue une maintenance planifiée sur les instances du groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Met hors tension les instances du groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Obtient les propriétés de toutes les adresses IP publiques d’un groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/read | Obtient les propriétés d’un groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Redéploie les instances du groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Réimage les instances du groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Réimager tous les disques (disque de système d'exploitation et disques de données) des instances d'un groupe de machines virtuelles identiques  |
> | Action | Microsoft.Compute/virtualMachineScaleSets/restart/action | Redémarre les instances du groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/action | Annule la mise à niveau propagée d’un groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Obtient l’état de la dernière mise à niveau propagée d’un groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/scale/action | Vérifie si un groupe de machines virtuelles identiques existant peut être mis à l’échelle vers un nombre d’instances spécifié |
> | Action | Microsoft.Compute/virtualMachineScaleSets/skus/read | Répertorie les références SKU valides pour un groupe de machines virtuelles identiques existant |
> | Action | Microsoft.Compute/virtualMachineScaleSets/start/action | Démarre les instances du groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Mettre hors tension et libérer les ressources de calcul d’une machine virtuelle d’un groupe de machines virtuelles identiques. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Supprimer une machine virtuelle spécifique d’un groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Récupérer la vue d’instance d’une machine virtuelle dans un groupe de machines virtuelles identiques. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Obtient les propriétés de l’adresse IP publique créée à l’aide d’un groupe de machines virtuelles identiques. Un groupe de machines virtuelles identiques peut créer au maximum une adresse IP par configuration IP (adresse IP privée) |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Obtient les propriétés d’une ou de la totalité des configurations IP d’une interface réseau créée à l’aide d’un groupe de machines virtuelles identiques. Les configurations IP représentent des adresses IP privées |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Obtient les propriétés d’une ou de la totalité des interfaces réseau d’une machine virtuelle créée à l’aide d’un groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Effectue une maintenance planifiée sur une instance de machine virtuelle dans un groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Mettre hors tension une instance de machine virtuelle dans un groupe de machines virtuelles identiques. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Récupérer les propriétés d’une machine virtuelle dans un groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Redéploie une instance de machine virtuelle dans un groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Réimage une instance de machine virtuelle dans un groupe de machines virtuelles identiques. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Réimager tous les disques (disque de système d'exploitation et disques de données) d’une instance de machine virtuelle dans un groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Redémarrer une instance de machine virtuelle dans un groupe de machines virtuelles identiques. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Exécuter un script prédéfini sur une instance de machine virtuelle dans un groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Démarrer une instance de machine virtuelle dans un groupe de machines virtuelles identiques. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Met à jour les propriétés d’une machine virtuelle dans un groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | Répertorier les tailles disponibles pour la création ou la mise à jour d’une machine virtuelle dans le groupe de machines virtuelles identiques |
> | Action | Microsoft.Compute/virtualMachineScaleSets/write | Crée ou met à jour un groupe de machines virtuelles identiques |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Consumption/aggregatedcost/read | Répertorie AggregatedCost pour le groupe d’administration. |
> | Action | Microsoft.Consumption/balances/read | Affiche le récapitulatif d’utilisation d’une période de facturation pour un groupe d’administration. |
> | Action | Microsoft.Consumption/budgets/delete | Supprime les budgets par abonnement ou groupe d’administration. |
> | Action | Microsoft.Consumption/budgets/read | Affiche les budgets par abonnement ou un groupe d’administration. |
> | Action | Microsoft.Consumption/budgets/write | Crée et met à jour les budgets par abonnement ou groupe d’administration. |
> | Action | Microsoft.Consumption/charges/read | Lister les frais |
> | Action | Microsoft.Consumption/credits/read | Lister les crédits |
> | Action | Microsoft.Consumption/events/read | Lister les événements |
> | Action | Microsoft.Consumption/externalBillingAccounts/tags/read | Lister les balises pour les accords Entreprise (EA) et les abonnements. |
> | Action | Microsoft.Consumption/externalSubscriptions/tags/read | Lister les balises pour les accords Entreprise (EA) et les abonnements. |
> | Action | Microsoft.Consumption/forecasts/read | Lister les prévisions |
> | Action | Microsoft.Consumption/lots/read | Lister les lots |
> | Action | Microsoft.Consumption/marketplaces/read | Affiche les détails d’utilisation des ressources de la place de marché pour une étendue en lien avec des abonnements EA et WebDirect. |
> | Action | Microsoft.Consumption/operationresults/read | Lister les résultats d’opération |
> | Action | Microsoft.Consumption/operations/read | Répertorie toutes les opérations prises en charge par un fournisseur de ressources Microsoft.Consumption. |
> | Action | Microsoft.Consumption/operationstatus/read | Lister les états d’opération |
> | Action | Microsoft.Consumption/pricesheets/read | Affiche les données de grille tarifaire pour un abonnement ou un groupe d’administration. |
> | Action | Microsoft.Consumption/register/action | S’inscrire auprès d’un fournisseur de ressources de consommation |
> | Action | Microsoft.Consumption/reservationDetails/read | Affiche les détails d’utilisation d’instances réservées par ordre de réservation ou groupe d’administration. Les données sont détaillées par instance par jour. |
> | Action | Microsoft.Consumption/reservationRecommendations/read | Répertorie les recommandations uniques ou partagées pour les instances réservées concernant un abonnement. |
> | Action | Microsoft.Consumption/reservationSummaries/read | Affiche le récapitulatif d’utilisation d’instances réservées par ordre de réservation ou groupe d’administration. Les données récapitulatives sont affichées au niveau mensuel ou quotidien. |
> | Action | Microsoft.Consumption/reservationTransactions/read | Affiche l’historique des transactions pour les instances réservées par groupe d’administration. |
> | Action | Microsoft.Consumption/tags/read | Lister les balises pour les accords Entreprise (EA) et les abonnements. |
> | Action | Microsoft.Consumption/tenants/read | Répertorier les locataires |
> | Action | Microsoft.Consumption/tenants/register/action | Inscrire une action pour l’étendue de Microsoft.Consumption par un client |
> | Action | Microsoft.Consumption/terms/read | Affiche les termes d’un abonnement ou d’un groupe d’administration. |
> | Action | Microsoft.Consumption/usageDetails/read | Affiche les détails d’utilisation pour une étendue en lien avec des abonnements EA et WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.ContainerInstance/containerGroups/containers/buildlogs/read | Obtient les journaux de génération pour un conteneur spécifique. |
> | Action | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Exécuter dans un conteneur spécifique. |
> | Action | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Obtient les journaux d’activité pour un conteneur spécifique. |
> | Action | Microsoft.ContainerInstance/containerGroups/delete | Supprime le groupe de conteneurs spécifique. |
> | Action | Microsoft.ContainerInstance/containerGroups/operationResults/read | Obtenir le résultat d’une opération asynchrone |
> | Action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Obtient le paramètre de diagnostic pour le groupe de conteneurs. |
> | Action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour le groupe de conteneurs. |
> | Action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Obtient les métriques disponibles pour le groupe de conteneurs. |
> | Action | Microsoft.ContainerInstance/containerGroups/read | Obtenir tous les groupes de conteneurs. |
> | Action | Microsoft.ContainerInstance/containerGroups/restart/action | Redémarre un groupe de conteneurs spécifique. |
> | Action | Microsoft.ContainerInstance/containerGroups/start/action | Démarre un groupe de conteneurs spécifique. |
> | Action | Microsoft.ContainerInstance/containerGroups/stop/action | Arrête un groupe de conteneurs spécifique. Les ressources de calcul sont désallouées et la facturation est arrêtée. |
> | Action | Microsoft.ContainerInstance/containerGroups/write | Crée ou met à jour un groupe de conteneurs spécifique. |
> | Action | Microsoft.ContainerInstance/locations/cachedImages/read | Obtient les images mises en cache pour l’abonnement dans une région. |
> | Action | Microsoft.ContainerInstance/locations/capabilities/read | Obtenir les capacités d’une région. |
> | Action | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Notifie Microsoft.ContainerInstance de la suppression du réseau virtuel ou du sous-réseau. |
> | Action | Microsoft.ContainerInstance/locations/operations/read | Répertorie les opérations pour le service Azure Container Instance. |
> | Action | Microsoft.ContainerInstance/locations/usages/read | Obtenir l’utilisation pour une région spécifique. |
> | Action | Microsoft.ContainerInstance/operations/read | Répertorie les opérations pour le service Azure Container Instance. |
> | Action | Microsoft.ContainerInstance/register/action | Inscrire l'abonnement pour le fournisseur de ressources de l'instance de conteneur, et activer la création de groupes de conteneurs |
> | Action | Microsoft.ContainerInstance/serviceassociationlinks/delete | Supprime le lien d’association de service créé par le fournisseur de ressources Azure Container Instance sur un sous-réseau. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.ContainerRegistry/checkNameAvailability/read | Vérifie si le nom de registre de conteneurs est disponible pour utilisation. |
> | Action | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Informer Microsoft.ContainerRegistry que le réseau ou sous-réseau virtuel est en cours de suppression. |
> | Action | Microsoft.ContainerRegistry/locations/operationResults/read | Obtient un résultat d’opération asynchrone |
> | Action | Microsoft.ContainerRegistry/operations/read | Répertorie toutes les opérations disponibles de l’API REST Azure Container Registry |
> | Action | Microsoft.ContainerRegistry/register/action | Inscrit l’abonnement pour le fournisseur de ressources du registre de conteneurs et active la création de registres de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/artifacts/delete | Supprimer l’artefact dans un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/builds/cancel/action | Annule une build existante. |
> | Action | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Obtient un lien pour télécharger les journaux d’activité de build. |
> | Action | Microsoft.ContainerRegistry/registries/builds/read | Obtient les propriétés de la build spécifiée ou affiche toutes les builds pour le registre de conteneurs spécifié. |
> | Action | Microsoft.ContainerRegistry/registries/builds/write | Met à jour une build pour un registre de conteneurs avec les paramètres spécifiés. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/delete | Supprime une tâche de génération d’un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Répertorie les propriétés du contrôle de code source pour une tâche de génération. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/read | Obtient les propriétés de la tâche de génération spécifiée ou affiche toutes les tâches de génération pour le registre de conteneurs spécifié. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Supprime une étape de génération dans une tâche de génération. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Répertorie les arguments de build pour une étape de génération, y compris les arguments de secret. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Obtient les propriétés de l’étape de génération spécifiée ou affiche toutes les étapes de génération de la tâche de génération spécifiée. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Crée ou met à jour une étape de génération dans une tâche de génération avec les paramètres spécifiés. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/write | Crée ou met à jour une tâche de génération pour un registre de conteneurs avec les paramètres spécifiés. |
> | Action | Microsoft.ContainerRegistry/registries/delete | Supprime un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Supprime un filtre Event Grid d’un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Obtient les propriétés du filtre Event Grid spécifié ou répertorie tous les filtres Event Grid pour le registre de conteneurs spécifié. |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Crée ou met à jour un filtre Event Grid pour un registre de conteneurs avec les paramètres spécifiés. |
> | Action | Microsoft.ContainerRegistry/registries/generateCredentials/action | Génère des clés pour le jeton d’un registre de conteneurs spécifié. |
> | Action | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Obtient l’emplacement de chargement pour que l’utilisateur puisse charger la source. |
> | Action | Microsoft.ContainerRegistry/registries/importImage/action | Importe une image vers un registre de conteneurs avec les paramètres spécifiés. |
> | Action | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Obtenir l’emplacement de l’url de chargement de la source pour un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/listCredentials/action | Répertorie les informations d’identification pour le registre de conteneurs spécifié. |
> | Action | Microsoft.ContainerRegistry/registries/listPolicies/read | Afficher les stratégies pour le registre de conteneurs spécifié |
> | Action | Microsoft.ContainerRegistry/registries/listUsages/read | Affiche les utilisations du quota pour le registre de conteneurs spécifié. |
> | Action | Microsoft.ContainerRegistry/registries/metadata/read | Obtient les métadonnées d’un référentiel spécifique pour un registre de conteneurs |
> | Action | Microsoft.ContainerRegistry/registries/metadata/write | Met à jour les métadonnées d’un référentiel pour un registre de conteneurs |
> | Action | Microsoft.ContainerRegistry/registries/operationStatuses/read | Obtient un état d’opération asynchrone du registre |
> | Action | Microsoft.ContainerRegistry/registries/privateEndpointConnectionProxies/delete | Supprime le proxy de connexion de point de terminaison privé (NPR uniquement) |
> | Action | Microsoft.ContainerRegistry/registries/privateEndpointConnectionProxies/operationStatuses/read | Obtient l’état de l’opération asynchrone du proxy de connexion de point de terminaison privé |
> | Action | Microsoft.ContainerRegistry/registries/privateEndpointConnectionProxies/read | Obtient le proxy de connexion de point de terminaison privé (NRP uniquement) |
> | Action | Microsoft.ContainerRegistry/registries/privateEndpointConnectionProxies/validate/action | Valide le proxy de connexion de point de terminaison privé (NRP uniquement) |
> | Action | Microsoft.ContainerRegistry/registries/privateEndpointConnectionProxies/write | Crée le proxy de connexion de point de terminaison privé (NRP uniquement) |
> | Action | Microsoft.ContainerRegistry/registries/pull/read | Tire (pull) ou obtient des images à partir d’un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/push/write | Envoie (push) ou écrit des images dans un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/quarantine/read | Tire (pull) ou obtient des images en quarantaine à partir du registre de conteneurs |
> | Action | Microsoft.ContainerRegistry/registries/quarantine/write | Écrit ou modifie l’état des images en quarantaine |
> | Action | Microsoft.ContainerRegistry/registries/queueBuild/action | Crée une nouvelle build basée sur les paramètres de requête, et l’ajoute à la file d’attente de builds. |
> | Action | Microsoft.ContainerRegistry/registries/read | Obtient les propriétés du registre de conteneurs spécifié ou affiche tous les registres de conteneurs sous le groupe de ressources ou l’abonnement spécifiés. |
> | Action | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Régénère l’une des informations d’identification pour le registre de conteneurs spécifié. |
> | Action | Microsoft.ContainerRegistry/registries/replications/delete | Supprime une réplication d’un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Obtient un état d’opération asynchrone de réplication |
> | Action | Microsoft.ContainerRegistry/registries/replications/read | Obtient les propriétés de la réplication spécifiée ou affiche toutes les réplications pour le registre de conteneurs spécifié. |
> | Action | Microsoft.ContainerRegistry/registries/replications/write | Crée ou met à jour une réplication pour un registre de conteneurs avec les paramètres spécifiés. |
> | Action | Microsoft.ContainerRegistry/registries/runs/cancel/action | Annule une exécution existante. |
> | Action | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Obtient l’URL SAP des journaux pour une exécution. |
> | Action | Microsoft.ContainerRegistry/registries/runs/read | Obtient les propriétés d’une exécution par rapport à un registre de conteneurs, ou répertorie les exécutions. |
> | Action | Microsoft.ContainerRegistry/registries/runs/write | Met à jour une exécution. |
> | Action | Microsoft.ContainerRegistry/registries/scheduleRun/action | Planifie une exécution par rapport à un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/scopeMaps/delete | Supprime un mappage d’étendue d’un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/scopeMaps/operationStatuses/read | Obtient un état de l’opération asynchrone du mappage d’étendue. |
> | Action | Microsoft.ContainerRegistry/registries/scopeMaps/read | Obtient les propriétés du mappage d’étendue spécifié ou répertorie tous les mappages d’étendue pour le registre de conteneurs spécifié. |
> | Action | Microsoft.ContainerRegistry/registries/scopeMaps/write | Crée ou met à jour un mappage d’étendue pour un registre de conteneurs avec les paramètres spécifiés. |
> | Action | Microsoft.ContainerRegistry/registries/sign/write | Envoie ou tire des métadonnées d’approbation du contenu pour un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/delete | Supprime une tâche pour un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Répertorie tous les détails d’une tâche pour un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/read | Obtient une tâche pour un registre de conteneurs, ou répertorie toutes les tâches. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/write | Crée ou met à jour une tâche pour un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/tokens/delete | Supprime un jeton d’un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/tokens/operationStatuses/read | Obtient un état de l’opération asynchrone du jeton. |
> | Action | Microsoft.ContainerRegistry/registries/tokens/read | Obtient les propriétés du jeton spécifié ou répertorie tous les jetons pour le registre de conteneurs spécifié. |
> | Action | Microsoft.ContainerRegistry/registries/tokens/write | Crée ou met à jour un jeton pour un registre de conteneurs avec les paramètres spécifiés. |
> | Action | Microsoft.ContainerRegistry/registries/updatePolicies/write | Mettre à jour les stratégies pour le registre de conteneur spécifié |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/delete | Supprime un webhook d’un registre de conteneurs. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Obtient la configuration de l’URI de service et les en-têtes personnalisés pour le webhook. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Affiche les événements récents pour le webhook spécifié. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Obtient un état de l’opération asynchrone de webhook |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Déclenche un événement de test ping à envoyer au webhook. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/read | Obtient les propriétés du webhook spécifié ou affiche tous les webhooks pour le registre de conteneurs spécifié. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/write | Crée ou met à jour un webhook pour un registre de conteneurs avec les paramètres spécifiés. |
> | Action | Microsoft.ContainerRegistry/registries/write | Crée ou met à jour un registre de conteneurs avec les paramètres spécifiés. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.ContainerService/containerServices/delete | Supprime un service de conteneur |
> | Action | Microsoft.ContainerService/containerServices/read | Obtient un service de conteneur |
> | Action | Microsoft.ContainerService/containerServices/write | Crée ou met à jour un service de conteneur |
> | Action | Microsoft.ContainerService/locations/operationresults/read | Obtenir l’état du résultat d’une opération asynchrone |
> | Action | Microsoft.ContainerService/locations/operations/read | Afficher l’état d’une opération asynchrone |
> | Action | Microsoft.ContainerService/locations/orchestrators/read | Répertorier les orchestrateurs pris en charge |
> | Action | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Obtient un profil d’accès au cluster géré en fonction du nom de rôle à l’aide des informations d’identification de la liste |
> | Action | Microsoft.ContainerService/managedClusters/accessProfiles/read | Obtient un profil d’accès au cluster géré en fonction du nom de rôle |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/delete | Supprime un pool d’agents |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/read | Obtient un pool d’agents |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles/read | Obtient le profil de mise à niveau du pool d'agents |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/write | Créer ou mettre à jour un pool d'agents |
> | Action | Microsoft.ContainerService/managedClusters/availableAgentPoolVersions/read | Obtient les versions de pool d’agents disponibles du cluster |
> | Action | Microsoft.ContainerService/managedClusters/delete | Supprime un cluster géré |
> | Action | Microsoft.ContainerService/managedClusters/detectors/read | Obtenir le détecteur de cluster managé |
> | Action | Microsoft.ContainerService/managedClusters/diagnosticsState/read | Obtient l’état des diagnostics du cluster |
> | Action | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Répertorier les informations d’identification clusterAdmin d’un cluster géré |
> | Action | Microsoft.ContainerService/managedClusters/listClusterMonitoringUserCredential/action | Répertorie les informations d’identification clusterMonitoringUser d’un cluster managé |
> | Action | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Répertorier les informations d’identification clusterAdmin d’un cluster géré |
> | Action | Microsoft.ContainerService/managedClusters/privateEndpointConnectionsApproval/action | Détermine si l’utilisateur est autorisé à approuver une connexion de point de terminaison privé |
> | Action | Microsoft.ContainerService/managedClusters/read | Obtient un cluster géré |
> | Action | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Réinitialiser le profil AAD d’un cluster managé |
> | Action | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Réinitialiser le profil du principal de service d’un cluster managé |
> | Action | Microsoft.ContainerService/managedClusters/rotateClusterCertificates/action | Fait pivoter les certificats d’un cluster managé |
> | Action | Microsoft.ContainerService/managedClusters/upgradeProfiles/read | Obtenir le profil de mise à niveau du cluster |
> | Action | Microsoft.ContainerService/managedClusters/write | Crée ou met à jour un cluster géré |
> | Action | Microsoft.ContainerService/openShiftClusters/delete | Supprimer un cluster Open Shift |
> | Action | Microsoft.ContainerService/openShiftClusters/read | Obtenir un cluster Open Shift |
> | Action | Microsoft.ContainerService/openShiftClusters/write | Crée ou met à jour un cluster Open Shift |
> | Action | Microsoft.ContainerService/openShiftManagedClusters/delete | Supprimer un cluster managé Open Shift |
> | Action | Microsoft.ContainerService/openShiftManagedClusters/read | Obtenir un cluster managé Open Shift |
> | Action | Microsoft.ContainerService/openShiftManagedClusters/write | Crée ou met à jour un cluster managé |
> | Action | Microsoft.ContainerService/operations/read | Répertorier les opérations disponibles sur le fournisseur de ressources Microsoft.ContainerService |
> | Action | Microsoft.ContainerService/register/action | Inscrire l’abonnement auprès du fournisseur de ressources Microsoft.ContainerService |
> | Action | Microsoft.ContainerService/unregister/action | Désinscrit l’abonnement auprès du fournisseur de ressources Microsoft.ContainerService |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.CostManagement/alerts/write | Mettre à jour des alertes. |
> | Action | Microsoft.CostManagement/cloudConnectors/delete | Supprime le cloudConnector spécifié. |
> | Action | Microsoft.CostManagement/cloudConnectors/read | Répertorie les cloudConnectors pour l’utilisateur authentifié. |
> | Action | Microsoft.CostManagement/cloudConnectors/write | Crée ou met à jour le cloudConnector spécifié. |
> | Action | Microsoft.CostManagement/dimensions/read | Répertorier toutes les dimensions prises en charge par une étendue |
> | Action | Microsoft.CostManagement/exports/action | Exécuter l’exportation spécifiée. |
> | Action | Microsoft.CostManagement/exports/delete | Supprimer l’exportation spécifiée. |
> | Action | Microsoft.CostManagement/exports/read | Lister les exportations par étendue. |
> | Action | Microsoft.CostManagement/exports/run/action | Exécute les exportations. |
> | Action | Microsoft.CostManagement/exports/write | Créer ou mettre à jour l’exportation spécifiée. |
> | Action | Microsoft.CostManagement/externalBillingAccounts/dimensions/read | Répertorie toutes les dimensions prises en charge pour les BillingAccounts externes. |
> | Action | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Répertorie les externalSubscriptions au sein d’un externalBillingAccount pour l’utilisateur authentifié. |
> | Action | Microsoft.CostManagement/externalBillingAccounts/forecast/action | Prévoit les données d’utilisation pour les BillingAccounts externes. |
> | Action | Microsoft.CostManagement/externalBillingAccounts/forecast/read | Prévoit les données d’utilisation pour les BillingAccounts externes. |
> | Action | Microsoft.CostManagement/externalBillingAccounts/query/action | Interroge les données d’utilisation pour les BillingAccounts externes. |
> | Action | Microsoft.CostManagement/externalBillingAccounts/query/read | Interroge les données d’utilisation pour les BillingAccounts externes. |
> | Action | Microsoft.CostManagement/externalBillingAccounts/read | Répertorie les externalBillingAccounts pour l’utilisateur authentifié. |
> | Action | Microsoft.CostManagement/externalSubscriptions/dimensions/read | Répertorie toutes les dimensions prises en charge pour l’abonnement externe. |
> | Action | Microsoft.CostManagement/externalSubscriptions/forecast/action | Prévoit les données d’utilisation pour les BillingAccounts externes. |
> | Action | Microsoft.CostManagement/externalSubscriptions/forecast/read | Prévoit les données d’utilisation pour les BillingAccounts externes. |
> | Action | Microsoft.CostManagement/externalSubscriptions/query/action | Interroge les données d’utilisation de l’abonnement externe. |
> | Action | Microsoft.CostManagement/externalSubscriptions/query/read | Interroge les données d’utilisation de l’abonnement externe. |
> | Action | Microsoft.CostManagement/externalSubscriptions/read | Répertorie les externalSubscriptions pour l’utilisateur authentifié. |
> | Action | Microsoft.CostManagement/externalSubscriptions/write | Met à jour le groupe d’administration associé d’externalSubscription |
> | Action | Microsoft.CostManagement/forecast/action | Prévoit les données d’utilisation par une étendue. |
> | Action | Microsoft.CostManagement/forecast/read | Prévoit les données d’utilisation par une étendue. |
> | Action | Microsoft.CostManagement/operations/read | Répertorie toutes les opérations prises en charge par le fournisseur de ressources Microsoft.CostManagement. |
> | Action | Microsoft.CostManagement/query/action | Interroger les données d’utilisation par étendue |
> | Action | Microsoft.CostManagement/query/read | Interroger les données d’utilisation par étendue |
> | Action | Microsoft.CostManagement/register/action | Inscrit une action pour l’étendue de Microsoft.CostManagement par un abonnement. |
> | Action | Microsoft.CostManagement/reports/action | Planifier des rapports sur les données d’utilisation par étendue |
> | Action | Microsoft.CostManagement/reports/read | Planifier des rapports sur les données d’utilisation par étendue |
> | Action | Microsoft.CostManagement/tenants/register/action | Inscrire une action pour l’étendue de Microsoft.CostManagement par un client. |
> | Action | Microsoft.CostManagement/views/action | Crée un affichage. |
> | Action | Microsoft.CostManagement/views/delete | Supprime les affichages enregistrés. |
> | Action | Microsoft.CostManagement/views/read | Répertorie tous les affichages enregistrés. |
> | Action | Microsoft.CostManagement/views/write | Met à jour un affichage. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Autoriser la planification de l'enlèvement pour les retours |
> | Action | Microsoft.DataBox/jobs/cancel/action | Annuler une commande en cours |
> | Action | Microsoft.DataBox/jobs/delete | Supprimer les commandes |
> | Action | Microsoft.DataBox/jobs/listCredentials/action | Répertorie les informations d’identification non chiffrées liées à la commande |
> | Action | Microsoft.DataBox/jobs/read | Répertorier ou obtenir les commandes |
> | Action | Microsoft.DataBox/jobs/write | Créer ou mettre à jour les commandes |
> | Action | Microsoft.DataBox/locations/availableSkus/action | Retourner la liste des références (SKU) disponibles |
> | Action | Microsoft.DataBox/locations/availableSkus/read | Répertorier ou récupérer les SKU disponibles |
> | Action | Microsoft.DataBox/locations/operationResults/read | Lister ou obtenir les résultats de l’opération |
> | Action | Microsoft.DataBox/locations/regionConfiguration/action | Cette méthode retourne les configurations pour la région. |
> | Action | Microsoft.DataBox/locations/validateAddress/action | Valider l'adresse de livraison et fournir d'autres adresses s’il en est |
> | Action | Microsoft.DataBox/locations/validateInputs/action | Cette méthode effectue tous les types de validations. |
> | Action | Microsoft.DataBox/operations/read | Lister ou obtenir les opérations |
> | Action | Microsoft.DataBox/register/action | Inscrire le fournisseur Microsoft.Databox |
> | Action | Microsoft.DataBox/subscriptions/resourceGroups/moveResources/action | Cette méthode effectue le déplacement des ressources. |
> | Action | Microsoft.DataBox/subscriptions/resourceGroups/validateMoveResources/action | Cette méthode vérifie si le déplacement des ressources est autorisé ou non. |
> | Action | Microsoft.DataBox/unregister/action | Désinscrire le fournisseur Microsoft.Databox |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Répertorie ou obtient les alertes |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Supprime les planifications de bande passante |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Répertorie ou obtient les résultats de l’opération |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Répertorie ou obtient les planifications de bande passante |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Répertorie ou obtient les planifications de bande passante |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Répertorie ou met à jour les planifications de bande passante |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Supprime les appareils Data Box Edge |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Télécharger les mises à jour dans l’appareil |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Récupère les informations de ressource étendues |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Installe les mises à jour sur l’appareil |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Répertorie ou obtient les travaux |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Répertorie ou obtient les paramètres réseau de l’appareil |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/nodes/read | Liste ou obtient les nœuds |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Répertorie ou obtient les résultats de l’opération |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | Répertorie ou obtient l’état de l’opération |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Supprime les commandes |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Répertorie ou obtient les résultats de l’opération |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Répertorie ou obtient les commandes |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Répertorie ou obtient les commandes |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Crée ou met à jour les commandes |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Répertorie ou obtient les appareils Data Box Edge |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Répertorie ou obtient les appareils Data Box Edge |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Répertorie ou obtient les appareils Data Box Edge |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Supprime les rôles |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Répertorie ou obtient les résultats de l’opération |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Répertorie ou obtient les rôles |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Répertorie ou obtient les rôles |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Crée ou met à jour les rôles |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Rechercher les mises à jour |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Répertorie ou obtient les résultats de l’opération |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Mettre à jour les paramètres de sécurité |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Supprime les partages |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Répertorie ou obtient les résultats de l’opération |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Répertorie ou obtient les partages |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Répertorie ou obtient les partages |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Actualise les métadonnées de partage avec les données du cloud |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Crée ou met à jour les partages |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Supprime les informations d’identification du compte de stockage |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Répertorie ou obtient les résultats de l’opération |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Répertorie ou obtient les informations d’identification du compte de stockage |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Répertorie ou obtient les informations d’identification du compte de stockage |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Crée ou met à jour les informations d’identification du compte de stockage |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/delete | Supprime les conteneurs |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/operationResults/read | Répertorie ou obtient les résultats de l’opération |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/read | Permet de répertorier ou d'obtenir les conteneurs |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/refresh/action | Actualise les métadonnées de conteneur avec les données du cloud |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/write | Crée ou met à jour les conteneurs |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/delete | Supprime les comptes de stockage |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/operationResults/read | Répertorie ou obtient les résultats de l’opération |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/read | Permet de répertorier ou d'obtenir les comptes de stockage |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/write | Crée ou met à jour les comptes de stockage |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Supprime les déclencheurs |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Répertorie ou obtient les résultats de l’opération |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Répertorie ou récupère les déclencheurs |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Répertorie ou récupère les déclencheurs |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Crée ou met à jour les déclencheurs |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Répertorie ou obtient le résumé de la mise à jour |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Télécharger le certificat pour l’inscription de l’appareil |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Supprime les utilisateurs du partage |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Répertorie ou obtient les résultats de l’opération |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Répertorie ou obtient les utilisateurs du partage |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Répertorie ou obtient les utilisateurs du partage |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Crée ou met à jour les utilisateurs du partage |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Crée ou met à jour les appareils Data Box Edge |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Crée ou met à jour les appareils Data Box Edge |
> | Action | Microsoft.DataBoxEdge/skus/read | Répertorie ou obtient les références SKU |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Databricks/locations/getNetworkPolicies/action | Obtenir des stratégies d’intention de réseau pour un sous-réseau en fonction de l’emplacement utilisé par NRP |
> | Action | Microsoft.Databricks/locations/operationstatuses/read | Lit l’état de l’opération pour la ressource. |
> | Action | Microsoft.Databricks/operations/read | Obtient la liste des opérations. |
> | Action | Microsoft.Databricks/register/action | S’inscrit à Databricks. |
> | Action | Microsoft.Databricks/workspaces/delete | Supprime un espace de travail Databricks. |
> | Action | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Définit les paramètres de diagnostics disponibles pour l’espace de travail Databricks |
> | Action | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Ajoutez ou modifiez des paramètres de diagnostics. |
> | Action | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Obtient les définitions de journal disponibles pour l’espace de travail Databricks |
> | Action | Microsoft.Databricks/workspaces/read | Récupère une liste d’espaces de travail Databricks. |
> | Action | Microsoft.Databricks/workspaces/refreshPermissions/action | Actualise les autorisations pour un espace de travail |
> | Action | Microsoft.Databricks/workspaces/storageEncryption/delete | Désactive le chiffrement des clés gérées par le client sur le compte de stockage géré de l'espace de travail Databricks |
> | Action | Microsoft.Databricks/workspaces/storageEncryption/write | Active le chiffrement des clés gérées par le client sur le compte de stockage géré de l'espace de travail Databricks |
> | Action | Microsoft.Databricks/workspaces/updateDenyAssignment/action | Met à jour l’affectation de refus et non les actions pour le groupe de ressources managé d’un espace de travail |
> | Action | Microsoft.Databricks/workspaces/virtualNetworkPeerings/delete | Supprime un peering de réseau virtuel. |
> | Action | Microsoft.Databricks/workspaces/virtualNetworkPeerings/read | Obtenir le peering de réseau virtuel |
> | Action | Microsoft.Databricks/workspaces/virtualNetworkPeerings/write | Ajoute ou modifie un appairage de réseaux virtuels |
> | Action | Microsoft.Databricks/workspaces/write | Crée un espace de travail Databricks. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.DataCatalog/catalogs/delete | Supprime les ressources de catalogue pour le fournisseur de ressources de Data Catalog. |
> | Action | Microsoft.DataCatalog/catalogs/read | Lit les ressources de catalogue pour le fournisseur de ressources de Data Catalog. |
> | Action | Microsoft.DataCatalog/catalogs/write | Écrit les ressources de catalogue pour le fournisseur de ressources de Data Catalog. |
> | Action | Microsoft.DataCatalog/checkNameAvailability/read | Vérifie la disponibilité du nom de catalogue pour le fournisseur de ressources de Data Catalog. |
> | Action | Microsoft.DataCatalog/datacatalogs/delete | Supprime la ressource DataCatalog pour le fournisseur de ressources de Data Catalog. |
> | Action | Microsoft.DataCatalog/datacatalogs/read | Lit la ressource DataCatalog pour le fournisseur de ressources de Data Catalog. |
> | Action | Microsoft.DataCatalog/datacatalogs/write | Écrit la ressource DataCatalog pour le fournisseur de ressources de Data Catalog. |
> | Action | Microsoft.DataCatalog/operations/read | Lit toutes les opérations disponibles dans le fournisseur de ressources de Data Catalog. |
> | Action | Microsoft.DataCatalog/register/action | Inscrit l’abonnement pour le fournisseur de ressources de Data Catalog |
> | Action | Microsoft.DataCatalog/unregister/action | Désinscrit l’abonnement pour le fournisseur de ressources de Data Catalog |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Vérifie si le nom de la fabrique de données est disponible pour utilisation. |
> | Action | Microsoft.DataFactory/datafactories/activitywindows/read | Affiche les fenêtres d’activité dans la fabrique de données avec les paramètres spécifiés. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Affiche les fenêtres d’activité dans l’activité du pipeline avec les paramètres spécifiés. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Lit les fenêtres d’activité pour le pipeline avec les paramètres spécifiés. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/delete | Supprime tous les pipelines. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Interrompt tous les pipelines. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/read | Affiche tous les pipelines. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Rétablit tous les pipelines. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/update/action | Met à jour tous les pipelines. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/write | Crée ou met à jour tous les pipelines. |
> | Action | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Affiche les fenêtres d’activité pour le jeu de données avec les paramètres spécifiés. |
> | Action | Microsoft.DataFactory/datafactories/datasets/delete | Supprime tous les jeux de données. |
> | Action | Microsoft.DataFactory/datafactories/datasets/read | Affiche tous les jeux de données. |
> | Action | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Affiche l’exécution de la tranche de données pour le jeu de données indiqué avec l’heure de début donnée. |
> | Action | Microsoft.DataFactory/datafactories/datasets/slices/read | Obtient les tranches de données dans la période indiquée. |
> | Action | Microsoft.DataFactory/datafactories/datasets/slices/write | Met à jour l’état de la tranche de données. |
> | Action | Microsoft.DataFactory/datafactories/datasets/write | Crée ou met à jour tous les jeux de données. |
> | Action | Microsoft.DataFactory/datafactories/delete | Supprime la fabrique de données. |
> | Action | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Affiche les informations de connexion pour toutes les passerelles. |
> | Action | Microsoft.DataFactory/datafactories/gateways/delete | Supprime toutes les passerelles. |
> | Action | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Répertorie les clés d’authentification de toutes les passerelles. |
> | Action | Microsoft.DataFactory/datafactories/gateways/read | Affiche toutes les passerelles. |
> | Action | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Régénère les clés d’authentification de toutes les passerelles. |
> | Action | Microsoft.DataFactory/datafactories/gateways/write | Crée ou met à jour toutes les passerelles. |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/delete | Supprime tous les services liés. |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/read | Affiche tous les services liés. |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/write | Crée ou met à jour tous les services liés. |
> | Action | Microsoft.DataFactory/datafactories/read | Affiche la fabrique de données. |
> | Action | Microsoft.DataFactory/datafactories/runs/loginfo/read | Affiche un URI SAS vers un conteneur blob comportant les journaux d’activité. |
> | Action | Microsoft.DataFactory/datafactories/tables/delete | Supprime tous les jeux de données. |
> | Action | Microsoft.DataFactory/datafactories/tables/read | Affiche tous les jeux de données. |
> | Action | Microsoft.DataFactory/datafactories/tables/write | Crée ou met à jour tous les jeux de données. |
> | Action | Microsoft.DataFactory/datafactories/write | Crée ou met à jour la fabrique de données. |
> | Action | Microsoft.DataFactory/factories/addDataFlowToDebugSession/action | Ajoute Data Flow à la session de débogage pour la préversion. |
> | Action | Microsoft.DataFactory/factories/cancelpipelinerun/action | Annule l’exécution du pipeline spécifiée par l’ID d’exécution. |
> | Action | Microsoft.DataFactory/factories/cancelSandboxPipelineRun/action | Annule une exécution de débogage pour le pipeline. |
> | Action | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Crée une session de débogage Data Flow. |
> | Action | Microsoft.DataFactory/factories/dataflows/delete | Supprime un flux de données. |
> | Action | Microsoft.DataFactory/factories/dataflows/read | Lit un flux de données. |
> | Action | Microsoft.DataFactory/factories/dataflows/write | Créer ou mettre à jour un flux de données |
> | Action | Microsoft.DataFactory/factories/datasets/delete | Supprime tous les jeux de données. |
> | Action | Microsoft.DataFactory/factories/datasets/read | Affiche tous les jeux de données. |
> | Action | Microsoft.DataFactory/factories/datasets/write | Crée ou met à jour tous les jeux de données. |
> | Action | Microsoft.DataFactory/factories/debugpipelineruns/cancel/action | Annule une exécution de débogage pour le pipeline. |
> | Action | Microsoft.DataFactory/factories/delete | Supprime la fabrique de données. |
> | Action | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | Supprime une session de débogage de flux de données. |
> | Action | Microsoft.DataFactory/factories/executeDataFlowDebugCommand/action | Exécute la commande de débogage de Data Flow. |
> | Action | Microsoft.DataFactory/factories/getDataPlaneAccess/action | Obtient l’accès au service ADF DataPlane. |
> | Action | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Lit l’accès au service ADF DataPlane. |
> | Action | Microsoft.DataFactory/factories/getFeatureValue/action | Obtenir la valeur de la fonctionnalité de contrôle d’exposition pour l’emplacement spécifique. |
> | Action | Microsoft.DataFactory/factories/getFeatureValue/read | Lit la valeur de la fonctionnalité de contrôle d’exposition pour l’emplacement spécifique. |
> | Action | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Obtient le jeton d’accès à GitHub. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/delete | Supprime tous les runtimes d’intégration. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Affiche les informations de connexion du runtime d’intégration. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Obtenir les métadonnées SSIS Integration Runtime pour le runtime d’intégration spécifié. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Affiche l’état du runtime d’intégration. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Créer une référence Integration Runtime liée sur le runtime d’intégration partagé spécifié. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Répertorie les clés d’authentification de tous les runtimes d’intégration. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Obtient les données de surveillance pour tous les runtimes d’intégration. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Supprime le nœud pour le runtime d’intégration spécifié. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Retourne l’adresse IP du nœud spécifié du runtime d’intégration. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Lit le nœud pour le runtime d’intégration spécifié. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Met à jour un runtime d’intégration auto-hébergé. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/read | Affiche tous les runtimes d’intégration. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Actualiser les métadonnées SSIS Integration Runtime pour le runtime d’intégration spécifié. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Régénère les clés d’authentification pour le runtime d’intégration spécifié. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Supprime les références Integration Runtime liées du runtime d’intégration spécifié. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/start/action | Lance tous les runtimes d’intégration. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Interrompt tous les runtimes d’intégration. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Synchronise les informations d’identification pour le runtime d’intégration spécifié. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Met à niveau le runtime d’intégration spécifié. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/write | Crée ou met à jour tous les runtimes d’intégration. |
> | Action | Microsoft.DataFactory/factories/linkedServices/delete | Supprime le service lié. |
> | Action | Microsoft.DataFactory/factories/linkedServices/read | Affiche le service lié. |
> | Action | Microsoft.DataFactory/factories/linkedServices/write | Crée ou met à jour un service lié |
> | Action | Microsoft.DataFactory/factories/operationResults/read | Obtient les résultats de l’opération. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Affiche les exécutions d’activité pour l’ID d’exécution du pipeline spécifié. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Annule l’exécution du pipeline spécifiée par l’ID d’exécution. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | Interroge les exécutions d’activité pour l’ID d’exécution du pipeline spécifié. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Lit les résultats des exécutions d’activités de requête pour l’ID d’exécution du pipeline spécifié. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/read | Affiche les exécutions du pipeline. |
> | Action | Microsoft.DataFactory/factories/pipelines/createrun/action | Crée une exécution pour le pipeline. |
> | Action | Microsoft.DataFactory/factories/pipelines/delete | Supprime le pipeline. |
> | Action | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Obtient la progression des exécutions d’activités. |
> | Action | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Lit l’exécution du pipeline. |
> | Action | Microsoft.DataFactory/factories/pipelines/read | Affiche le pipeline. |
> | Action | Microsoft.DataFactory/factories/pipelines/sandbox/action | Crée un environnement d’exécution de débogage pour le pipeline. |
> | Action | Microsoft.DataFactory/factories/pipelines/sandbox/create/action | Crée un environnement d’exécution de débogage pour le pipeline. |
> | Action | Microsoft.DataFactory/factories/pipelines/sandbox/run/action | Crée une exécution de débogage pour le pipeline. |
> | Action | Microsoft.DataFactory/factories/pipelines/write | Crée ou met à jour un pipeline. |
> | Action | Microsoft.DataFactory/factories/querydataflowdebugsessions/action | Interroge une session de débogage de Data Flow. |
> | Action | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Interroge les exécutions du pipeline de débogage. |
> | Action | Microsoft.DataFactory/factories/querypipelineruns/action | Interroge les exécutions du pipeline. |
> | Action | Microsoft.DataFactory/factories/querypipelineruns/read | Lit les résultats des exécutions de pipeline de requête. |
> | Action | Microsoft.DataFactory/factories/querytriggerruns/action | Interroge les exécutions du déclencheur. |
> | Action | Microsoft.DataFactory/factories/querytriggerruns/read | Lit les résultats des exécutions du déclencheur. |
> | Action | Microsoft.DataFactory/factories/querytriggers/action | Interroge les déclencheurs. |
> | Action | Microsoft.DataFactory/factories/read | Affiche la fabrique de données. |
> | Action | Microsoft.DataFactory/factories/sandboxpipelineruns/action | Interroge les exécutions du pipeline de débogage. |
> | Action | Microsoft.DataFactory/factories/sandboxpipelineruns/read | Obtient les informations d’exécution de débogage pour le pipeline. |
> | Action | Microsoft.DataFactory/factories/sandboxpipelineruns/sandboxActivityRuns/read | Obtient les informations d’exécution de débogage pour l’activité. |
> | Action | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Démarre une session de débogage de flux de données. |
> | Action | Microsoft.DataFactory/factories/triggerruns/read | Affiche les exécutions du déclencheur. |
> | Action | Microsoft.DataFactory/factories/triggers/delete | Supprime tous les déclencheurs. |
> | Action | Microsoft.DataFactory/factories/triggers/geteventsubscriptionstatus/action | État de l’abonnement aux événements. |
> | Action | Microsoft.DataFactory/factories/triggers/read | Affiche tous les déclencheurs. |
> | Action | Microsoft.DataFactory/factories/triggers/start/action | Lance tous les déclencheurs. |
> | Action | Microsoft.DataFactory/factories/triggers/stop/action | Interrompt tous les déclencheurs. |
> | Action | Microsoft.DataFactory/factories/triggers/subscribetoevents/action | S’abonne aux événements. |
> | Action | Microsoft.DataFactory/factories/triggers/triggerruns/read | Affiche les exécutions du déclencheur. |
> | Action | Microsoft.DataFactory/factories/triggers/unsubscribefromevents/action | Se désabonne des événements. |
> | Action | Microsoft.DataFactory/factories/triggers/write | Crée ou met à jour tous les déclencheurs. |
> | Action | Microsoft.DataFactory/factories/write | Crée ou met à jour la fabrique de données. |
> | Action | Microsoft.DataFactory/locations/configureFactoryRepo/action | Configure le dépôt pour la fabrique. |
> | Action | Microsoft.DataFactory/locations/getFeatureValue/action | Obtenir la valeur de la fonctionnalité de contrôle d’exposition pour l’emplacement spécifique. |
> | Action | Microsoft.DataFactory/locations/getFeatureValue/read | Lit la valeur de la fonctionnalité de contrôle d’exposition pour l’emplacement spécifique. |
> | Action | Microsoft.DataFactory/operations/read | Lit toutes les opérations du fournisseur Microsoft Data Factory. |
> | Action | Microsoft.DataFactory/register/action | Enregistre l’abonnement pour le fournisseur de ressources de fabrique de données. |
> | Action | Microsoft.DataFactory/unregister/action | Annule l’enregistrement de l’abonnement pour le fournisseur de ressources de fabrique de données. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Supprime une stratégie de calcul. |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Obtient des informations sur une stratégie de calcul. |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Crée ou met à jour une stratégie de calcul. |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Dissocie un compte Data Lake Store d’un compte Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Obtient des informations sur un compte Data Lake Store lié d’un compte Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Crée ou met à jour un compte Data Lake Store lié d’un compte Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/delete | Supprime un compte Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Supprimer une règle de pare-feu. |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Obtenir des informations sur une règle de pare-feu. |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Créer ou mettre à jour une règle de pare-feu. |
> | Action | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Obtient le résultat d’une opération de compte Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/read | Obtient des informations sur un compte Data Lake Analytics existant. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Affiche les jetons SAP pour des conteneurs de stockage d’un compte de stockage lié d’un compte Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Obtient les conteneurs d’un compte de stockage lié d’un compte Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Dissocie un compte de stockage d’un compte Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Obtient des informations sur un compte de stockage lié d’un compte Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Crée ou met à jour un compte de stockage lié d’un compte Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Accorde des autorisations pour annuler des travaux soumis par d’autres utilisateurs. |
> | Action | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Transférer SystemMaxAnalyticsUnits aux comptes DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Supprimer une règle de réseau virtuel. |
> | Action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Obtenir des informations sur une règle de réseau virtuel. |
> | Action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Créer ou mettre à jour une règle de réseau virtuel. |
> | Action | Microsoft.DataLakeAnalytics/accounts/write | Crée ou met à jour un compte Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/locations/capability/read | Obtient les informations de fonctionnalité d’un abonnement concernant l’utilisation de Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Vérifie la disponibilité d’un nom de compte Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/locations/operationResults/read | Obtient le résultat d’une opération de compte Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/locations/usages/read | Obtenir les informations sur les utilisations du quota d’un abonnement concernant l’utilisation de DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/operations/read | Obtient les opérations disponibles de Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/register/action | Inscrit un abonnement à Data Lake Analytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.DataLakeStore/accounts/delete | Supprime un compte Data Lake Store. |
> | Action | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Active le coffre de clés pour un compte Data Lake Store. |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Supprimer un filtre EventGrid |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Obtenir un filtre EventGrid |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Créer ou mettre à jour un filtre EventGrid |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/delete | Supprimer une règle de pare-feu. |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/read | Obtenir des informations sur une règle de pare-feu. |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/write | Créer ou mettre à jour une règle de pare-feu. |
> | Action | Microsoft.DataLakeStore/accounts/operationResults/read | Obtient le résultat d’une opération de compte Data Lake Store. |
> | Action | Microsoft.DataLakeStore/accounts/read | Obtient des informations sur un compte Data Lake Store existant. |
> | Action | Microsoft.DataLakeStore/accounts/shares/delete | Supprimer un partage. |
> | Action | Microsoft.DataLakeStore/accounts/shares/read | Obtenir des informations sur un partage. |
> | Action | Microsoft.DataLakeStore/accounts/shares/write | Créer ou mettre à jour un partage. |
> | Action | Microsoft.DataLakeStore/accounts/Superuser/action | Accorde des privilèges de superutilisateur sur Data Lake Store quand accordés avec Microsoft.Authorization/roleAssignments/write. |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Supprimer un fournisseur d’identité approuvé. |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Obtenir des informations sur un fournisseur d’identité approuvé. |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Créer ou mettre à jour un fournisseur d’identité approuvé. |
> | Action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Supprimer une règle de réseau virtuel. |
> | Action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Obtenir des informations sur une règle de réseau virtuel. |
> | Action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Créer ou mettre à jour une règle de réseau virtuel. |
> | Action | Microsoft.DataLakeStore/accounts/write | Crée ou met à jour un compte Data Lake Store. |
> | Action | Microsoft.DataLakeStore/locations/capability/read | Obtient les informations de fonctionnalité d’un abonnement concernant l’utilisation de Data Lake Store. |
> | Action | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Vérifie la disponibilité d’un nom de compte Data Lake Store. |
> | Action | Microsoft.DataLakeStore/locations/operationResults/read | Obtient le résultat d’une opération de compte Data Lake Store. |
> | Action | Microsoft.DataLakeStore/locations/usages/read | Obtenir les informations sur les utilisations du quota d’un abonnement concernant l’utilisation de DataLakeStore. |
> | Action | Microsoft.DataLakeStore/operations/read | Obtient les opérations disponibles de Data Lake Store. |
> | Action | Microsoft.DataLakeStore/register/action | Inscrit un abonnement à Data Lake Store. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.DataMigration/locations/operationResults/read | Obtenir l’état d’une opération longue liée à une réponse 202 Accepté |
> | Action | Microsoft.DataMigration/locations/operationStatuses/read | Obtenir l’état d’une opération longue liée à une réponse 202 Accepté |
> | Action | Microsoft.DataMigration/register/action | Inscrire l’abonnement auprès du fournisseur Azure Database Migration Service |
> | Action | Microsoft.DataMigration/services/addWorker/action | Ajoute un worker DMS pour les workers disponibles du service |
> | Action | Microsoft.DataMigration/services/checkStatus/action | Vérifier si le service est déployé et en cours d’exécution |
> | Action | Microsoft.DataMigration/services/configureWorker/action | Configure un worker DMS pour les workers disponibles du service |
> | Action | Microsoft.DataMigration/services/delete | Supprimer une ressource et tous ses enfants |
> | Action | Microsoft.DataMigration/services/getHybridDownloadLink/action | Permet d'obtenir un lien de téléchargement pour le package du worker DMS à partir du stockage blob RP. |
> | Action | Microsoft.DataMigration/services/projects/accessArtifacts/action | Générer une URL pouvant utilisable pour des opérations GET ou PUT sur des artefacts de projet |
> | Action | Microsoft.DataMigration/services/projects/delete | Supprimer une ressource et tous ses enfants |
> | Action | Microsoft.DataMigration/services/projects/files/delete | Supprimer une ressource et tous ses enfants |
> | Action | Microsoft.DataMigration/services/projects/files/read | Lire les informations sur les ressources |
> | Action | Microsoft.DataMigration/services/projects/files/read/action | Obtenir une URL pouvant être utilisée pour lire le contenu du fichier |
> | Action | Microsoft.DataMigration/services/projects/files/readWrite/action | Obtenir une URL pouvant être utilisée pour lire ou écrire le contenu du fichier |
> | Action | Microsoft.DataMigration/services/projects/files/write | Créer ou mettre à jour des ressources et leurs propriétés |
> | Action | Microsoft.DataMigration/services/projects/read | Lire les informations sur les ressources |
> | Action | Microsoft.DataMigration/services/projects/tasks/cancel/action | Annuler la tâche si elle est en cours d’exécution |
> | Action | Microsoft.DataMigration/services/projects/tasks/delete | Supprimer une ressource et tous ses enfants |
> | Action | Microsoft.DataMigration/services/projects/tasks/read | Lire les informations sur les ressources |
> | Action | Microsoft.DataMigration/services/projects/tasks/write | Exécuter des tâches Azure Database Migration Service |
> | Action | Microsoft.DataMigration/services/projects/write | Exécuter des tâches Azure Database Migration Service |
> | Action | Microsoft.DataMigration/services/read | Lire les informations sur les ressources |
> | Action | Microsoft.DataMigration/services/removeWorker/action | Supprime un worker DMS pour les workers disponibles du service |
> | Action | Microsoft.DataMigration/services/serviceTasks/cancel/action | Annuler la tâche si elle est en cours d’exécution |
> | Action | Microsoft.DataMigration/services/serviceTasks/delete | Supprimer une ressource et tous ses enfants |
> | Action | Microsoft.DataMigration/services/serviceTasks/read | Lire les informations sur les ressources |
> | Action | Microsoft.DataMigration/services/serviceTasks/write | Exécuter des tâches Azure Database Migration Service |
> | Action | Microsoft.DataMigration/services/slots/delete | Supprimer une ressource et tous ses enfants |
> | Action | Microsoft.DataMigration/services/slots/read | Lire les informations sur les ressources |
> | Action | Microsoft.DataMigration/services/slots/write | Créer ou mettre à jour des ressources et leurs propriétés |
> | Action | Microsoft.DataMigration/services/start/action | Démarrer le service DMS pour lui permettre de retraiter des migrations |
> | Action | Microsoft.DataMigration/services/stop/action | Arrêter le service DMS pour réduire son coût |
> | Action | Microsoft.DataMigration/services/updateAgentConfig/action | Met à jour la configuration de l’agent DMS avec les valeurs fournies. |
> | Action | Microsoft.DataMigration/services/write | Créer ou mettre à jour des ressources et leurs propriétés |
> | Action | Microsoft.DataMigration/skus/read | Obtenir la liste des références (SKU) prises en charge par les ressources DMS |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.DBforMariaDB/checkNameAvailability/action | Vérifie si le nom du serveur spécifié est disponible pour l’approvisionnement dans le monde entier pour un abonnement donné. |
> | Action | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | Retourne les résultats de l’opération serveur MariaDB |
> | Action | Microsoft.DBforMariaDB/locations/operationResults/read | Retourne le groupe de ressources en fonction des résultats des opérations serveur MariaDB |
> | Action | Microsoft.DBforMariaDB/locations/operationResults/read | Retourne les résultats de l’opération serveur MariaDB |
> | Action | Microsoft.DBforMariaDB/locations/performanceTiers/read | Retourne la liste des niveaux de performances disponibles. |
> | Action | Microsoft.DBforMariaDB/locations/privateEndpointConnectionAzureAsyncOperation/read | Obtient le résultat d’une opération de connexion de point de terminaison privé |
> | Action | Microsoft.DBforMariaDB/locations/privateEndpointConnectionOperationResults/read | Obtient le résultat d’une opération de connexion de point de terminaison privé |
> | Action | Microsoft.DBforMariaDB/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Obtient le résultat d’une opération proxy de connexion de point de terminaison privé |
> | Action | Microsoft.DBforMariaDB/locations/privateEndpointConnectionProxyOperationResults/read | Obtient le résultat d’une opération proxy de connexion de point de terminaison privé |
> | Action | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | Retourne la liste du résultat de l’opération de détection des menaces contre le serveur. |
> | Action | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Retourne la liste du résultat de l’opération de détection des menaces contre le serveur. |
> | Action | Microsoft.DBforMariaDB/locations/serverKeyAzureAsyncOperation/read | Obtient les opérations en cours d’exécution sur les clés de serveur de chiffrement transparent des données |
> | Action | Microsoft.DBforMariaDB/locations/serverKeyOperationResults/read | Obtient les opérations en cours d’exécution sur les clés de serveur de chiffrement transparent des données |
> | Action | Microsoft.DBforMariaDB/operations/read | Retourne la liste des opérations MariaDB. |
> | Action | Microsoft.DBforMariaDB/performanceTiers/read | Retourne la liste des niveaux de performances disponibles. |
> | Action | Microsoft.DBforMariaDB/register/action | Inscrit le fournisseur de ressources MariaDB |
> | Action | Microsoft.DBforMariaDB/servers/administrators/delete | Supprime un administrateur existant du serveur MariaDB. |
> | Action | Microsoft.DBforMariaDB/servers/administrators/read | Obtient une liste des administrateurs serveur MariaDB. |
> | Action | Microsoft.DBforMariaDB/servers/administrators/write | Crée ou met à jour l’administrateur de serveur MariaDB avec les paramètres spécifiés. |
> | Action | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | Crée une nouvelle session d’action de recommandation |
> | Action | Microsoft.DBforMariaDB/servers/advisors/read | Retourne la liste des conseillers |
> | Action | Microsoft.DBforMariaDB/servers/advisors/read | Retourne un Advisor |
> | Action | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Retourne la liste des actions recommandées |
> | Action | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Retourne la liste des actions recommandées |
> | Action | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Retourne une action recommandée |
> | Action | Microsoft.DBforMariaDB/servers/configurations/read | Retourner la liste des configurations pour un serveur ou obtenir les propriétés de la configuration spécifiée. |
> | Action | Microsoft.DBforMariaDB/servers/configurations/write | Mettre à jour la valeur de la configuration spécifiée |
> | Action | Microsoft.DBforMariaDB/servers/databases/delete | Supprime une base de données MariaDB existante. |
> | Action | Microsoft.DBforMariaDB/servers/databases/read | Retourner la liste des bases de données MariaDB ou obtenir les propriétés pour la base de données spécifiée. |
> | Action | Microsoft.DBforMariaDB/servers/databases/write | Crée une base de données MariaDB avec les paramètres spécifiés ou met à jour les propriétés de la base de données spécifiée. |
> | Action | Microsoft.DBforMariaDB/servers/delete | Supprime un serveur existant. |
> | Action | Microsoft.DBforMariaDB/servers/firewallRules/delete | Supprime une règle de pare-feu existante. |
> | Action | Microsoft.DBforMariaDB/servers/firewallRules/read | Retourne la liste des règles de pare-feu pour un serveur ou obtient les propriétés de la règle de pare-feu spécifiée. |
> | Action | Microsoft.DBforMariaDB/servers/firewallRules/write | Crée une règle de pare-feu avec les paramètres spécifiés ou met à jour une règle existante. |
> | Action | Microsoft.DBforMariaDB/servers/keys/delete | Supprime une clé de serveur existante. |
> | Action | Microsoft.DBforMariaDB/servers/keys/read | Retourne la liste des clés de serveur ou obtient les propriétés de la clé de serveur spécifiée. |
> | Action | Microsoft.DBforMariaDB/servers/keys/write | Crée une clé avec les paramètres spécifiés ou met à jour les propriétés ou balises pour la clé de serveur spécifiée. |
> | Action | Microsoft.DBforMariaDB/servers/logFiles/read | Retourne la liste des opérations LogFiles. |
> | Action | Microsoft.DBforMariaDB/servers/privateEndpointConnectionProxies/delete | Supprime un proxy de connexion de point de terminaison privé existant |
> | Action | Microsoft.DBforMariaDB/servers/privateEndpointConnectionProxies/read | Retourne la liste de proxys de connexion de point de terminaison privé ou obtient les propriétés pour du proxy de connexion de point de terminaison privé spécifié. |
> | Action | Microsoft.DBforMariaDB/servers/privateEndpointConnectionProxies/validate/action | Valide un appel de création de connexion de point de terminaison privé du côté NRP |
> | Action | Microsoft.DBforMariaDB/servers/privateEndpointConnectionProxies/write | Crée un proxy de connexion de point de terminaison privé avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le proxy de connexion de point de terminaison privé spécifié. |
> | Action | Microsoft.DBforMariaDB/servers/privateEndpointConnections/delete | Supprime une connexion de point de terminaison privé existante |
> | Action | Microsoft.DBforMariaDB/servers/privateEndpointConnections/read | Retourne la liste de connexions de point de terminaison privé ou obtient les propriétés pour de la connexion de point de terminaison privé spécifiée. |
> | Action | Microsoft.DBforMariaDB/servers/privateEndpointConnections/write | Approuve ou rejette une connexion de point de terminaison privé existante |
> | Action | Microsoft.DBforMariaDB/servers/privateLinkResources/read | Obtient les ressources de liaison privée pour le serveur MariaDB correspondant |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obtient le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Obtient les journaux d’activité disponibles pour les serveurs MariaDB |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Renvoie les types de mesures disponibles pour les bases de données |
> | Action | Microsoft.DBforMariaDB/servers/queryTexts/action | Retourner les textes pour une liste de requêtes |
> | Action | Microsoft.DBforMariaDB/servers/queryTexts/action | Retourner le texte d’une requête |
> | Action | Microsoft.DBforMariaDB/servers/read | Retourner la liste des serveurs ou obtenir les propriétés pour le serveur spécifié. |
> | Action | Microsoft.DBforMariaDB/servers/recoverableServers/read | Retourne les informations du serveur MariaDB récupérables |
> | Action | Microsoft.DBforMariaDB/servers/replicas/read | Obtient les réplicas en lecture d’un serveur MariaDB |
> | Action | Microsoft.DBforMariaDB/servers/restart/action | Redémarre un serveur spécifique. |
> | Action | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Récupère les détails de la stratégie de détection des menaces configurée sur un serveur donné. |
> | Action | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Modifie la stratégie de détection des menaces de serveur pour un serveur donné |
> | Action | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Retourner la liste des statistiques de requête pour les premières requêtes. |
> | Action | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Retourne une statistique de requête |
> | Action | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Mettre à jour des configurations pour le serveur spécifié |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Supprime une règle de réseau virtuel existante |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Retourne la liste des règles de réseau virtuel ou obtient les propriétés de la règle de réseau virtuel spécifiée. |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Crée une règle de réseau virtuel avec les paramètres spécifiés ou met à jour les propriétés ou balises de la règle de réseau virtuel spécifiée. |
> | Action | Microsoft.DBforMariaDB/servers/waitStatistics/read | Retourner des statistiques d’attente pour une instance |
> | Action | Microsoft.DBforMariaDB/servers/waitStatistics/read | Retourne une statistique d’attente |
> | Action | Microsoft.DBforMariaDB/servers/write | Crée un serveur avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le serveur spécifié. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.DBforMySQL/checkNameAvailability/action | Vérifie si le nom du serveur spécifié est disponible pour l’approvisionnement dans le monde entier pour un abonnement donné. |
> | Action | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | Retourne les résultats de l’opération serveur MySQL |
> | Action | Microsoft.DBforMySQL/locations/operationResults/read | Retourne le groupe de ressources en fonction des résultats des opérations serveur MySQL |
> | Action | Microsoft.DBforMySQL/locations/operationResults/read | Retourne les résultats de l’opération serveur MySQL |
> | Action | Microsoft.DBforMySQL/locations/performanceTiers/read | Retourne la liste des niveaux de performances disponibles. |
> | Action | Microsoft.DBforMySQL/locations/privateEndpointConnectionAzureAsyncOperation/read | Obtient le résultat d’une opération de connexion de point de terminaison privé |
> | Action | Microsoft.DBforMySQL/locations/privateEndpointConnectionOperationResults/read | Obtient le résultat d’une opération de connexion de point de terminaison privé |
> | Action | Microsoft.DBforMySQL/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Obtient le résultat d’une opération proxy de connexion de point de terminaison privé |
> | Action | Microsoft.DBforMySQL/locations/privateEndpointConnectionProxyOperationResults/read | Obtient le résultat d’une opération proxy de connexion de point de terminaison privé |
> | Action | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Retourne la liste du résultat de l’opération de détection des menaces contre le serveur. |
> | Action | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | Retourne la liste du résultat de l’opération de détection des menaces contre le serveur. |
> | Action | Microsoft.DBforMySQL/locations/serverKeyAzureAsyncOperation/read | Obtient les opérations en cours d’exécution sur les clés de serveur de chiffrement transparent des données |
> | Action | Microsoft.DBforMySQL/locations/serverKeyOperationResults/read | Obtient les opérations en cours d’exécution sur les clés de serveur de chiffrement transparent des données |
> | Action | Microsoft.DBforMySQL/operations/read | Retourne la liste des opérations MySQL. |
> | Action | Microsoft.DBforMySQL/performanceTiers/read | Retourne la liste des niveaux de performances disponibles. |
> | Action | Microsoft.DBforMySQL/register/action | Inscrire le fournisseur de ressources MySQL |
> | Action | Microsoft.DBforMySQL/servers/administrators/delete | Supprime un administrateur existant du serveur MySQL. |
> | Action | Microsoft.DBforMySQL/servers/administrators/read | Obtient une liste des administrateurs serveur MySQL. |
> | Action | Microsoft.DBforMySQL/servers/administrators/write | Crée ou met à jour l’administrateur de serveur MySQL avec les paramètres spécifiés. |
> | Action | Microsoft.DBforMySQL/servers/advisors/createRecommendedActionSession/action | Crée une nouvelle session d’action de recommandation |
> | Action | Microsoft.DBforMySQL/servers/advisors/read | Retourne la liste des conseillers |
> | Action | Microsoft.DBforMySQL/servers/advisors/read | Retourne un Advisor |
> | Action | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Retourne la liste des actions recommandées |
> | Action | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Retourne la liste des actions recommandées |
> | Action | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Retourne une action recommandée |
> | Action | Microsoft.DBforMySQL/servers/configurations/read | Retourner la liste des configurations pour un serveur ou obtenir les propriétés de la configuration spécifiée. |
> | Action | Microsoft.DBforMySQL/servers/configurations/write | Mettre à jour la valeur de la configuration spécifiée |
> | Action | Microsoft.DBforMySQL/servers/databases/delete | Supprime une base de données MySQL existante. |
> | Action | Microsoft.DBforMySQL/servers/databases/read | Retourner la liste des bases de données MySQL ou obtenir les propriétés pour la base de données spécifiée. |
> | Action | Microsoft.DBforMySQL/servers/databases/write | Crée une base de données MySQL avec les paramètres spécifiés ou met à jour les propriétés de la base de données spécifiée. |
> | Action | Microsoft.DBforMySQL/servers/delete | Supprime un serveur existant. |
> | Action | Microsoft.DBforMySQL/servers/firewallRules/delete | Supprime une règle de pare-feu existante. |
> | Action | Microsoft.DBforMySQL/servers/firewallRules/read | Retourne la liste des règles de pare-feu pour un serveur ou obtient les propriétés de la règle de pare-feu spécifiée. |
> | Action | Microsoft.DBforMySQL/servers/firewallRules/write | Crée une règle de pare-feu avec les paramètres spécifiés ou met à jour une règle existante. |
> | Action | Microsoft.DBforMySQL/servers/keys/delete | Supprime une clé de serveur existante. |
> | Action | Microsoft.DBforMySQL/servers/keys/read | Retourne la liste des clés de serveur ou obtient les propriétés de la clé de serveur spécifiée. |
> | Action | Microsoft.DBforMySQL/servers/keys/write | Crée une clé avec les paramètres spécifiés ou met à jour les propriétés ou balises pour la clé de serveur spécifiée. |
> | Action | Microsoft.DBforMySQL/servers/logFiles/read | Retourne la liste des LogFiles MySQL. |
> | Action | Microsoft.DBforMySQL/servers/privateEndpointConnectionProxies/delete | Supprime un proxy de connexion de point de terminaison privé existant |
> | Action | Microsoft.DBforMySQL/servers/privateEndpointConnectionProxies/read | Retourne la liste de proxys de connexion de point de terminaison privé ou obtient les propriétés pour du proxy de connexion de point de terminaison privé spécifié. |
> | Action | Microsoft.DBforMySQL/servers/privateEndpointConnectionProxies/validate/action | Valide un appel de création de connexion de point de terminaison privé du côté NRP |
> | Action | Microsoft.DBforMySQL/servers/privateEndpointConnectionProxies/write | Crée un proxy de connexion de point de terminaison privé avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le proxy de connexion de point de terminaison privé spécifié. |
> | Action | Microsoft.DBforMySQL/servers/privateEndpointConnections/delete | Supprime une connexion de point de terminaison privé existante |
> | Action | Microsoft.DBforMySQL/servers/privateEndpointConnections/read | Retourne la liste de connexions de point de terminaison privé ou obtient les propriétés pour de la connexion de point de terminaison privé spécifiée. |
> | Action | Microsoft.DBforMySQL/servers/privateEndpointConnections/write | Approuve ou rejette une connexion de point de terminaison privé existante |
> | Action | Microsoft.DBforMySQL/servers/privateLinkResources/read | Obtient les ressources de liaison privée pour le serveur MySQL correspondant |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obtient le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Obtient les journaux d’activité disponibles pour les serveurs MySQL |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Renvoie les types de mesures disponibles pour les bases de données |
> | Action | Microsoft.DBforMySQL/servers/queryTexts/action | Retourner les textes pour une liste de requêtes |
> | Action | Microsoft.DBforMySQL/servers/queryTexts/action | Retourner le texte d’une requête |
> | Action | Microsoft.DBforMySQL/servers/read | Retourner la liste des serveurs ou obtenir les propriétés pour le serveur spécifié. |
> | Action | Microsoft.DBforMySQL/servers/recoverableServers/read | Retourne les informations du serveur MySQL récupérables |
> | Action | Microsoft.DBforMySQL/servers/replicas/read | Obtient les réplicas en lecture d’un serveur MySQL |
> | Action | Microsoft.DBforMySQL/servers/restart/action | Redémarre un serveur spécifique. |
> | Action | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Récupère les détails de la stratégie de détection des menaces configurée sur un serveur donné. |
> | Action | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Modifie la stratégie de détection des menaces de serveur pour un serveur donné |
> | Action | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Retourner la liste des statistiques de requête pour les premières requêtes. |
> | Action | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Retourne une statistique de requête |
> | Action | Microsoft.DBforMySQL/servers/updateConfigurations/action | Mettre à jour des configurations pour le serveur spécifié |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Supprime une règle de réseau virtuel existante |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Retourne la liste des règles de réseau virtuel ou obtient les propriétés de la règle de réseau virtuel spécifiée. |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Crée une règle de réseau virtuel avec les paramètres spécifiés ou met à jour les propriétés ou balises de la règle de réseau virtuel spécifiée. |
> | Action | Microsoft.DBforMySQL/servers/waitStatistics/read | Retourner des statistiques d’attente pour une instance |
> | Action | Microsoft.DBforMySQL/servers/waitStatistics/read | Retourne une statistique d’attente |
> | Action | Microsoft.DBforMySQL/servers/write | Crée un serveur avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le serveur spécifié. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.DBforPostgreSQL/checkNameAvailability/action | Vérifie si le nom du serveur spécifié est disponible pour l’approvisionnement dans le monde entier pour un abonnement donné. |
> | Action | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Retourne les résultats de l’opération serveur PostgreSQL |
> | Action | Microsoft.DBforPostgreSQL/locations/operationResults/read | Retourne le groupe de ressources en fonction des résultats des opérations serveur PostgreSQL |
> | Action | Microsoft.DBforPostgreSQL/locations/operationResults/read | Retourne les résultats de l’opération serveur PostgreSQL |
> | Action | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Retourne la liste des niveaux de performances disponibles. |
> | Action | Microsoft.DBforPostgreSQL/locations/privateEndpointConnectionAzureAsyncOperation/read | Obtient le résultat d’une opération de connexion de point de terminaison privé |
> | Action | Microsoft.DBforPostgreSQL/locations/privateEndpointConnectionOperationResults/read | Obtient le résultat d’une opération de connexion de point de terminaison privé |
> | Action | Microsoft.DBforPostgreSQL/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Obtient le résultat d’une opération proxy de connexion de point de terminaison privé |
> | Action | Microsoft.DBforPostgreSQL/locations/privateEndpointConnectionProxyOperationResults/read | Obtient le résultat d’une opération proxy de connexion de point de terminaison privé |
> | Action | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Retourne la liste du résultat de l’opération de détection des menaces contre le serveur. |
> | Action | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Retourne la liste du résultat de l’opération de détection des menaces contre le serveur. |
> | Action | Microsoft.DBforPostgreSQL/locations/serverKeyAzureAsyncOperation/read | Obtient les opérations en cours d’exécution sur les clés de serveur de chiffrement transparent des données |
> | Action | Microsoft.DBforPostgreSQL/locations/serverKeyOperationResults/read | Obtient les opérations en cours d’exécution sur les clés de serveur de chiffrement transparent des données |
> | Action | Microsoft.DBforPostgreSQL/operations/read | Retourne la liste d’opérations de PostgreSQL. |
> | Action | Microsoft.DBforPostgreSQL/performanceTiers/read | Retourne la liste des niveaux de performances disponibles. |
> | Action | Microsoft.DBforPostgreSQL/register/action | Inscrire le fournisseur de ressources PostgreSQL |
> | Action | Microsoft.DBforPostgreSQL/servers/administrators/delete | Supprime un administrateur existant du serveur PostgreSQL. |
> | Action | Microsoft.DBforPostgreSQL/servers/administrators/read | Obtient une liste des administrateurs serveur PostgreSQL. |
> | Action | Microsoft.DBforPostgreSQL/servers/administrators/write | Crée ou met à jour l’administrateur de serveur PostgreSQL avec les paramètres spécifiés. |
> | Action | Microsoft.DBforPostgreSQL/servers/advisors/read | Retourne la liste des conseillers |
> | Action | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Retourne la liste des actions recommandées |
> | Action | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Effectuer des recommandations |
> | Action | Microsoft.DBforPostgreSQL/servers/configurations/read | Retourner la liste des configurations pour un serveur ou obtenir les propriétés de la configuration spécifiée. |
> | Action | Microsoft.DBforPostgreSQL/servers/configurations/write | Mettre à jour la valeur de la configuration spécifiée |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/delete | Supprime une base de données PostgreSQL existante. |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/read | Retourner la liste des bases de données PostgreSQL ou obtenir les propriétés pour la base de données spécifiée. |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/write | Crée une base de données PostgreSQL avec les paramètres spécifiés ou met à jour les propriétés de la base de données spécifiée. |
> | Action | Microsoft.DBforPostgreSQL/servers/delete | Supprime un serveur existant. |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Supprime une règle de pare-feu existante. |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Retourne la liste des règles de pare-feu pour un serveur ou obtient les propriétés de la règle de pare-feu spécifiée. |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Crée une règle de pare-feu avec les paramètres spécifiés ou met à jour une règle existante. |
> | Action | Microsoft.DBforPostgreSQL/servers/keys/delete | Supprime une clé de serveur existante. |
> | Action | Microsoft.DBforPostgreSQL/servers/keys/read | Retourne la liste des clés de serveur ou obtient les propriétés de la clé de serveur spécifiée. |
> | Action | Microsoft.DBforPostgreSQL/servers/keys/write | Crée une clé avec les paramètres spécifiés ou met à jour les propriétés ou balises pour la clé de serveur spécifiée. |
> | Action | Microsoft.DBforPostgreSQL/servers/logFiles/read | Retourne la liste de LogFiles de PostgreSQL. |
> | Action | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/delete | Supprime un proxy de connexion de point de terminaison privé existant |
> | Action | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/read | Retourne la liste de proxys de connexion de point de terminaison privé ou obtient les propriétés pour du proxy de connexion de point de terminaison privé spécifié. |
> | Action | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/validate/action | Valide un appel de création de connexion de point de terminaison privé du côté NRP |
> | Action | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/write | Crée un proxy de connexion de point de terminaison privé avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le proxy de connexion de point de terminaison privé spécifié. |
> | Action | Microsoft.DBforPostgreSQL/servers/privateEndpointConnections/delete | Supprime une connexion de point de terminaison privé existante |
> | Action | Microsoft.DBforPostgreSQL/servers/privateEndpointConnections/read | Retourne la liste de connexions de point de terminaison privé ou obtient les propriétés pour de la connexion de point de terminaison privé spécifiée. |
> | Action | Microsoft.DBforPostgreSQL/servers/privateEndpointConnections/write | Approuve ou rejette une connexion de point de terminaison privé existante |
> | Action | Microsoft.DBforPostgreSQL/servers/privateLinkResources/read | Obtenir les ressources de liaison privée pour le serveur PostgreSQL correspondant |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obtient le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Obtient les journaux d’activité disponibles pour les serveurs Postgres |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Renvoie les types de mesures disponibles pour les bases de données |
> | Action | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Retourner le texte d’une requête |
> | Action | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Retourner les textes pour une liste de requêtes |
> | Action | Microsoft.DBforPostgreSQL/servers/read | Retourner la liste des serveurs ou obtenir les propriétés pour le serveur spécifié. |
> | Action | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Retourne les informations du serveur PostgreSQL récupérables |
> | Action | Microsoft.DBforPostgreSQL/servers/replicas/read | Obtient les réplicas en lecture d’un serveur PostgreSQL |
> | Action | Microsoft.DBforPostgreSQL/servers/restart/action | Redémarre un serveur spécifique. |
> | Action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Récupère les détails de la stratégie de détection des menaces configurée sur un serveur donné. |
> | Action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Modifie la stratégie de détection des menaces de serveur pour un serveur donné |
> | Action | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Retourner la liste des statistiques de requête pour les premières requêtes. |
> | Action | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Mettre à jour des configurations pour le serveur spécifié |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Supprime une règle de réseau virtuel existante |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Retourne la liste des règles de réseau virtuel ou obtient les propriétés de la règle de réseau virtuel spécifiée. |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Crée une règle de réseau virtuel avec les paramètres spécifiés ou met à jour les propriétés ou balises de la règle de réseau virtuel spécifiée. |
> | Action | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Retourner des statistiques d’attente pour une instance |
> | Action | Microsoft.DBforPostgreSQL/servers/write | Crée un serveur avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le serveur spécifié. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Retourner la liste des configurations pour un serveur ou obtenir les propriétés de la configuration spécifiée. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Mettre à jour la valeur de la configuration spécifiée |
> | Action | Microsoft.DBforPostgreSQL/serversv2/delete | Supprime un serveur existant. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Supprime une règle de pare-feu existante. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Retourne la liste des règles de pare-feu pour un serveur ou obtient les propriétés de la règle de pare-feu spécifiée. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Crée une règle de pare-feu avec les paramètres spécifiés ou met à jour une règle existante. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Obtient le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Obtient les journaux d’activité disponibles pour les serveurs Postgres |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Renvoie les types de mesures disponibles pour les bases de données |
> | Action | Microsoft.DBforPostgreSQL/serversv2/read | Retourner la liste des serveurs ou obtenir les propriétés pour le serveur spécifié. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Mettre à jour des configurations pour le serveur spécifié |
> | Action | Microsoft.DBforPostgreSQL/serversv2/write | Crée un serveur avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le serveur spécifié. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Devices/Account/diagnosticSettings/read | Récupère le paramètre de diagnostic pour la ressource. |
> | Action | Microsoft.Devices/Account/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.Devices/Account/logDefinitions/read | Obtient les définitions de journal disponibles pour le service IotHub |
> | Action | Microsoft.Devices/Account/metricDefinitions/read | Obtenir les mesures disponibles pour le service IotHub |
> | Action | Microsoft.Devices/checkNameAvailability/Action | Vérifier que le nom IotHub est disponible |
> | Action | Microsoft.Devices/checkNameAvailability/Action | Vérifier que le nom IotHub est disponible |
> | Action | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Vérifier si le nom du service de provisionnement est disponible |
> | Action | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Vérifier si le nom du service de provisionnement est disponible |
> | Action | Microsoft.Devices/digitalTwins/Delete | Supprime un compte Digital Twins et de tous ses enfants |
> | Action | Microsoft.Devices/digitalTwins/operationresults/Read | Obtient l’état d’une opération sur un compte Digital Twins |
> | Action | Microsoft.Devices/digitalTwins/Read | Obtient la liste des comptes Digital Twins associés à un abonnement |
> | Action | Microsoft.Devices/digitalTwins/skus/Read | Obtient la liste des références SKU valides pour les comptes Digital Twins |
> | Action | Microsoft.Devices/digitalTwins/Write | Crée un compte Digital Twins |
> | Action | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Récupère le paramètre de diagnostic pour la ressource. |
> | Action | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.Devices/elasticPools/eventGridFilters/Delete | Supprime le filtre Event Grid de Pool élastique |
> | Action | Microsoft.Devices/elasticPools/eventGridFilters/Read | Obtient le filtre Event Grid de Pool élastique |
> | Action | Microsoft.Devices/elasticPools/eventGridFilters/Write | Crée ou met à jour un filtre Event Grid de Pool élastique existant |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Supprime un certificat |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Génère le code de vérification |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Obtient le certificat |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Vérifie la ressource de certificat |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Crée ou met à jour un certificat |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Supprime la ressource du locataire IotHub |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Récupère le paramètre de diagnostic pour la ressource. |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Supprimer le groupe de consommateurs EventHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Afficher les groupes de consommateurs EventHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Créer un groupe de consommateurs EventHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Exporter des appareils |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Obtient la ressource de statistiques du locataire IotHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importer des appareils |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Obtient la clé du locataire IotHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Obtenir des informations sur les travaux soumis à un concentrateur IotHub donné |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Obtient les clés du locataire IotHub |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Obtient les définitions de journal disponibles pour le service IotHub |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Obtenir les mesures disponibles pour le service IotHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Obtenir les mesures de quota |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Read | Obtient la ressource du locataire IotHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Tester un message sur l’ensemble des itinéraires existants |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Tester un message sur un itinéraire test fourni |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Afficher l’état de tous les points de terminaison de routage pour un concentrateur IotHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/securitySettings/operationResults/Read | Obtient le résultat de l’opération Placer de façon asynchrone pour le locataire IoT Hub SecuritySettings |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/securitySettings/Read | Obtient les paramètres Azure Security Center sur le locataire IoT Hub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/securitySettings/Write | Met à jour les paramètres Azure Security Center sur le locataire IoT Hub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Write | Crée ou met à jour la ressource du locataire IotHub |
> | Action | Microsoft.Devices/ElasticPools/metricDefinitions/read | Obtenir les mesures disponibles pour le service IotHub |
> | Action | Microsoft.Devices/iotHubs/certificates/Delete | Supprime un certificat |
> | Action | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Génère le code de vérification |
> | Action | Microsoft.Devices/iotHubs/certificates/Read | Obtient le certificat |
> | Action | Microsoft.Devices/iotHubs/certificates/verify/Action | Vérifie la ressource de certificat |
> | Action | Microsoft.Devices/iotHubs/certificates/Write | Crée ou met à jour un certificat |
> | Action | Microsoft.Devices/iotHubs/Delete | Supprimer une ressource IotHub |
> | Action | Microsoft.Devices/IotHubs/diagnosticSettings/read | Récupère le paramètre de diagnostic pour la ressource. |
> | Action | Microsoft.Devices/IotHubs/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Supprime le filtre Event Grid |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Read | Obtient le filtre Event Grid |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Write | Crée ou met à jour un filtre Event Grid existant |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Supprimer le groupe de consommateurs EventHub |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Afficher les groupes de consommateurs EventHub |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Créer un groupe de consommateurs EventHub |
> | Action | Microsoft.Devices/iotHubs/exportDevices/Action | Exporter des appareils |
> | Action | Microsoft.Devices/iotHubs/importDevices/Action | Importer des appareils |
> | Action | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Obtenir la clé IotHub pour le nom spécifié |
> | Action | Microsoft.Devices/iotHubs/iotHubStats/Read | Afficher les statistiques IotHub |
> | Action | Microsoft.Devices/iotHubs/jobs/Read | Obtenir des informations sur les travaux soumis à un concentrateur IotHub donné |
> | Action | Microsoft.Devices/iotHubs/listkeys/Action | Obtenir toutes les clés IotHub |
> | Action | Microsoft.Devices/IotHubs/logDefinitions/read | Obtient les définitions de journal disponibles pour le service IotHub |
> | Action | Microsoft.Devices/IotHubs/metricDefinitions/read | Obtenir les mesures disponibles pour le service IotHub |
> | Action | Microsoft.Devices/iotHubs/operationresults/Read | Obtenir le résultat d’une opération (API obsolète) |
> | Action | Microsoft.Devices/iotHubs/quotaMetrics/Read | Obtenir les mesures de quota |
> | Action | Microsoft.Devices/iotHubs/Read | Afficher les ressources IotHub |
> | Action | Microsoft.Devices/iotHubs/routing/$testall/Action | Tester un message sur l’ensemble des itinéraires existants |
> | Action | Microsoft.Devices/iotHubs/routing/$testnew/Action | Tester un message sur un itinéraire test fourni |
> | Action | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Afficher l’état de tous les points de terminaison de routage pour un concentrateur IotHub |
> | Action | Microsoft.Devices/iotHubs/securitySettings/operationResults/Read | Obtient le résultat de l’opération Placer de façon asynchrone pour IoT Hub SecuritySettings |
> | Action | Microsoft.Devices/iotHubs/securitySettings/Read | Obtient les paramètres Azure Security Center sur IoT Hub |
> | Action | Microsoft.Devices/iotHubs/securitySettings/Write | Met à jour les paramètres Azure Security Center sur IoT Hub |
> | Action | Microsoft.Devices/iotHubs/skus/Read | Afficher les références IotHub valides |
> | Action | Microsoft.Devices/iotHubs/Write | Créer ou mettre à jour une ressource IotHub |
> | Action | Microsoft.Devices/locations/operationresults/Read | Obtenir le résultat de l’opération basée sur l’emplacement |
> | Action | Microsoft.Devices/operationresults/Read | Obtenir le résultat d’une opération |
> | Action | Microsoft.Devices/operations/Read | Afficher toutes les opérations du fournisseur de ressources |
> | Action | Microsoft.Devices/provisioningServices/certificates/Delete | Supprime un certificat |
> | Action | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Génère le code de vérification |
> | Action | Microsoft.Devices/provisioningServices/certificates/Read | Obtient le certificat |
> | Action | Microsoft.Devices/provisioningServices/certificates/verify/Action | Vérifie la ressource de certificat |
> | Action | Microsoft.Devices/provisioningServices/certificates/Write | Crée ou met à jour un certificat |
> | Action | Microsoft.Devices/provisioningServices/Delete | Supprime la ressource IotDps |
> | Action | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Récupère le paramètre de diagnostic pour la ressource. |
> | Action | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Obtient les clés IotDps pour le nom de clé |
> | Action | Microsoft.Devices/provisioningServices/listkeys/Action | Obtient toutes les clés IotDps |
> | Action | Microsoft.Devices/provisioningServices/logDefinitions/read | Obtient les définitions de journal disponibles pour le service d’approvisionnement |
> | Action | Microsoft.Devices/provisioningServices/metricDefinitions/read | Obtient les métriques disponibles pour le service d’approvisionnement |
> | Action | Microsoft.Devices/provisioningServices/operationresults/Read | Obtenir le résultat d’une opération DPS |
> | Action | Microsoft.Devices/provisioningServices/Read | Obtient la ressource IotDps |
> | Action | Microsoft.Devices/provisioningServices/skus/Read | Obtient les références (SKU) IotDps valides |
> | Action | Microsoft.Devices/provisioningServices/Write | Crée une ressource IotDps |
> | Action | Microsoft.Devices/register/action | Inscrire l’abonnement pour le fournisseur de ressources IotHub et activer la création de ressources IotHub |
> | Action | Microsoft.Devices/usages/Read | Obtenir des informations sur l’utilisation de l’abonnement pour ce fournisseur |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.DevSpaces/controllers/delete | Supprimer les services du contrôleur Azure Dev Spaces et de dataplane |
> | Action | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Lister des détails de connexion pour l’infrastructure du contrôleur Azure Dev Spaces |
> | Action | Microsoft.DevSpaces/controllers/read | Lire les propriétés du contrôleur Azure Dev Spaces |
> | Action | Microsoft.DevSpaces/controllers/write | Créer ou mettre à jour des propriétés du contrôleur Azure Dev Spaces |
> | Action | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Vérifie le mappage de contrôleur existant pour un hôte de conteneur |
> | Action | Microsoft.DevSpaces/locations/checkContainerHostMapping/read | Vérifie le mappage de contrôleur existant pour un hôte de conteneur |
> | Action | Microsoft.DevSpaces/locations/operationresults/read | Lit l’état d’une opération asynchrone |
> | Action | Microsoft.DevSpaces/register/action | Inscrire le fournisseur de ressources Microsoft Dev Spaces à l’aide d’un abonnement |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.DevTestLab/labCenters/delete | Supprime les centres lab. |
> | Action | Microsoft.DevTestLab/labCenters/read | Lit les centres lab. |
> | Action | Microsoft.DevTestLab/labCenters/write | Ajoute ou modifie les centres lab. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Lire les modèles Azure Resource Manager. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Génère un modèle Azure Resource Manager pour l’artefact donné, télécharge les fichiers requis vers un compte de stockage et valide l’artefact généré. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Lire des artefacts. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/delete | Supprimer des sources d’artefact. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/read | Lire des sources d’artefact. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/write | Ajouter ou modifier des sources d’artefact. |
> | Action | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Revendiquer une machine virtuelle exigible aléatoire dans le laboratoire. |
> | Action | Microsoft.DevTestLab/labs/costs/read | Lire les coûts. |
> | Action | Microsoft.DevTestLab/labs/costs/write | Ajouter ou modifier les coûts. |
> | Action | Microsoft.DevTestLab/labs/CreateEnvironment/action | Créer des machines virtuelles dans un laboratoire. |
> | Action | Microsoft.DevTestLab/labs/customImages/delete | Supprimer des images personnalisées. |
> | Action | Microsoft.DevTestLab/labs/customImages/read | Lire des images personnalisées. |
> | Action | Microsoft.DevTestLab/labs/customImages/write | Ajouter ou modifier des images personnalisées. |
> | Action | Microsoft.DevTestLab/labs/delete | Supprimer des laboratoires. |
> | Action | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Vérifiez que l’utilisateur actuel dispose d’un profil valide dans le labo. |
> | Action | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exporter les données d’utilisation des ressources du laboratoire vers un compte de stockage. |
> | Action | Microsoft.DevTestLab/labs/formulas/delete | Supprimer des formules. |
> | Action | Microsoft.DevTestLab/labs/formulas/read | Lire des formules. |
> | Action | Microsoft.DevTestLab/labs/formulas/write | Ajouter ou modifier des formules. |
> | Action | Microsoft.DevTestLab/labs/galleryImages/read | Lire des images de la galerie. |
> | Action | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Générer un URI pour charger des images de disque personnalisées dans un laboratoire. |
> | Action | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importe une machine virtuelle dans un autre laboratoire. |
> | Action | Microsoft.DevTestLab/labs/ListVhds/action | Répertorier les images de disque disponibles pour la création d’images personnalisées. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/delete | Supprimer les canaux de notification. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Envoyer une notification au canal fourni. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/read | Lire les canaux de notification. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/write | Ajouter ou modifier des canaux de notification. |
> | Action | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Évaluer la stratégie de laboratoire. |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/delete | Supprimer des stratégies. |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/read | Lire les stratégies. |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/write | Ajouter ou modifier des stratégies. |
> | Action | Microsoft.DevTestLab/labs/policySets/read | Lire des jeux de stratégies. |
> | Action | Microsoft.DevTestLab/labs/read | Afficher des laboratoires. |
> | Action | Microsoft.DevTestLab/labs/schedules/delete | Supprimer des planifications. |
> | Action | Microsoft.DevTestLab/labs/schedules/Execute/action | Exécuter une planification. |
> | Action | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Répertorier toutes les planifications applicables. |
> | Action | Microsoft.DevTestLab/labs/schedules/read | Lire les planifications. |
> | Action | Microsoft.DevTestLab/labs/schedules/write | Ajouter ou modifier des planifications. |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/delete | Supprimer des exécuteurs de service. |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/read | Lire des exécuteurs de service. |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/write | Ajouter ou modifier des exécuteurs de service. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/delete | Supprime les galeries partagées. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/read | Lit les galeries partagées. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Supprime les images partagées. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Lire les images partagées. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Ajouter ou modifier des images partagées. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/write | Ajouter ou modifier des galeries partagées. |
> | Action | Microsoft.DevTestLab/labs/users/delete | Supprimer des profils utilisateur. |
> | Action | Microsoft.DevTestLab/labs/users/disks/Attach/action | Créer le bail du disque et l’associer à la machine virtuelle. |
> | Action | Microsoft.DevTestLab/labs/users/disks/delete | Supprimer des disques. |
> | Action | Microsoft.DevTestLab/labs/users/disks/Detach/action | Casser et dissocier le bail du disque associé à la machine virtuelle. |
> | Action | Microsoft.DevTestLab/labs/users/disks/read | Afficher les disques. |
> | Action | Microsoft.DevTestLab/labs/users/disks/write | Ajouter ou modifier des disques. |
> | Action | Microsoft.DevTestLab/labs/users/environments/delete | Supprimer des environnements. |
> | Action | Microsoft.DevTestLab/labs/users/environments/read | Afficher les environnements. |
> | Action | Microsoft.DevTestLab/labs/users/environments/write | Ajouter ou modifier des environnements. |
> | Action | Microsoft.DevTestLab/labs/users/read | Afficher les profils utilisateur. |
> | Action | Microsoft.DevTestLab/labs/users/secrets/delete | Supprimer des clés secrètes. |
> | Action | Microsoft.DevTestLab/labs/users/secrets/read | Afficher les clés secrètes. |
> | Action | Microsoft.DevTestLab/labs/users/secrets/write | Ajouter ou modifier des clés secrètes. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Supprime les structures Service Fabric. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Répertorie les planifications de démarrage/arrêt applicables, le cas échéant. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Lit les structures Service Fabric. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Supprimer des planifications. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Exécuter une planification. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Lire les planifications. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Ajouter ou modifier des planifications. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Démarre une structure Service Fabric. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Arrête une structure Service Fabric. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Ajoute ou modifie des structures Service Fabric. |
> | Action | Microsoft.DevTestLab/labs/users/write | Ajouter ou modifier des profils utilisateur. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Attacher un nouveau disque de données ou un disque de données existant à la machine virtuelle. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Appliquer des artefacts à une machine virtuelle. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Prendre possession d’une machine virtuelle existante. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/ClearArtifactResults/action | Efface les résultats d’artefact de la machine virtuelle. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/delete | Supprimer des machines virtuelles. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Détacher le disque spécifié de la machine virtuelle. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Obtenir une chaîne qui représente le contenu du fichier RDP pour la machine virtuelle |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Répertorie les planifications de démarrage/arrêt applicables, le cas échéant. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/read | Lire les machines virtuelles. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Redéploie une machine virtuelle |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Redimensionne la machine virtuelle. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Redémarre une machine virtuelle. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Supprimer des planifications. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Exécuter une planification. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Lire les planifications. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Ajouter ou modifier des planifications. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Démarrer une machine virtuelle. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Arrêt d’une machine virtuelle |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Transfère tous les disques de données attachés à la machine virtuelle dont l’utilisateur actuel prendra possession. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Cesse d’être propriétaire d’une machine virtuelle existante |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/write | Ajouter ou modifier des machines virtuelles. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/bastionHosts/delete | Supprimer des hôtes bastion. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/bastionHosts/read | Lire des hôtes bastion. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/bastionHosts/write | Ajouter ou modifier des hôtes bastion. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/delete | Supprimer des réseaux virtuels. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/read | Lire les réseaux virtuels. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/write | Ajouter ou modifier des réseaux virtuels. |
> | Action | Microsoft.DevTestLab/labs/vmPools/delete | Supprime les pools de machines virtuelles. |
> | Action | Microsoft.DevTestLab/labs/vmPools/read | Affiche les pools de machines virtuelles. |
> | Action | Microsoft.DevTestLab/labs/vmPools/write | Ajoute ou modifie des pools de machines virtuelles. |
> | Action | Microsoft.DevTestLab/labs/write | Ajouter ou modifier des laboratoires. |
> | Action | Microsoft.DevTestLab/locations/operations/read | Opérations de lecture. |
> | Action | Microsoft.DevTestLab/register/action | Enregistrer l’abonnement. |
> | Action | Microsoft.DevTestLab/schedules/delete | Supprimer des planifications. |
> | Action | Microsoft.DevTestLab/schedules/Execute/action | Exécuter une planification. |
> | Action | Microsoft.DevTestLab/schedules/read | Lire les planifications. |
> | Action | Microsoft.DevTestLab/schedules/Retarget/action | Mettre à jour l’ID de ressource cible d’une planification. |
> | Action | Microsoft.DevTestLab/schedules/write | Ajouter ou modifier des planifications. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.DocumentDB/databaseAccountNames/read | Vérifier la disponibilité du nom. |
> | Action | Microsoft.DocumentDB/databaseAccounts/backup/action | Soumettre une demande pour configurer la sauvegarde |
> | Action | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/delete | Supprime un espace de clés Cassandra. |
> | Action | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/read | Lit un espace de clés Cassandra ou répertorie tous les espaces de clés Cassandra. |
> | Action | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/delete | Supprime une table Cassandra. |
> | Action | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/read | Lit une table Cassandra ou répertorie toutes les tables Cassandra. |
> | Action | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/read | Lit le débit d'une table Cassandra. |
> | Action | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/write | Met à jour le débit d'une table Cassandra. |
> | Action | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/write | Crée ou met à jour une table Cassandra. |
> | Action | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/read | Lit le débit d'un espace de clés Cassandra. |
> | Action | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/write | Met à jour le débit d'un espace de clés Cassandra. |
> | Action | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/write | Crée un espace de clés Cassandra. |
> | Action | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Modifier le groupe de ressources d’un compte de base de données. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Lire les définitions de mesures de la collection. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Lire les mesures de la collection. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Lit les métriques de niveau de clé de partition de compte de base de données |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Lit les métriques de niveau de partition de compte de base de données |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Lit les partitions de compte de base de données dans une collection |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Lit les utilisations de niveau de partition de compte de base de données |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Lire les données d’utilisation de la collection. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Lire les définitions de mesures de la base de données. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Lire les mesures de la base de données. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Lire les données d’utilisation de la base de données. |
> | Action | Microsoft.DocumentDB/databaseAccounts/delete | Supprimer les comptes de base de données. |
> | Action | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Modifier les priorités de basculement des régions pour un compte de base de données. Cela permet d’effectuer un basculement manuel. |
> | Action | Microsoft.DocumentDB/databaseAccounts/getBackupPolicy/action | Obtenir la stratégie de sauvegarde du compte de base de données |
> | Action | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/delete | Supprime une base de données Gremlin. |
> | Action | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/delete | Supprime un graphique Gremlin. |
> | Action | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/read | Lit un graphique Gremlin ou répertorie tous les graphiques Gremlin. |
> | Action | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/read | Lit le débit d'un graphique Gremlin. |
> | Action | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/write | Met à jour le débit d'un graphique Gremlin. |
> | Action | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/write | Crée ou met à jour un graphique Gremlin. |
> | Action | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/read | Lit une base de données Gremlin ou répertorie toutes les bases de données Gremlin. |
> | Action | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/read | Lit le débit d'une base de données Gremlin. |
> | Action | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/write | Met à jour le débit d'une base de données Gremlin. |
> | Action | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/write | Crée une base de données Gremlin. |
> | Action | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Obtenir les chaînes de connexion d’un compte de base de données. |
> | Action | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Répertorier les clés d’un compte de base de données. |
> | Action | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Lire les définitions de mesures du compte de base de données. |
> | Action | Microsoft.DocumentDB/databaseAccounts/metrics/read | Lire les mesures du compte de base de données. |
> | Action | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/delete | Supprime une collection MongoDB. |
> | Action | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/read | Lit une collection MongoDB ou répertorie toutes les collections MongoDB. |
> | Action | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/read | Lit le débit d'une collection MongoDB. |
> | Action | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/write | Met à jour le débit d'une collection MongoDB. |
> | Action | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/write | Crée ou met à jour une collection MongoDB. |
> | Action | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/delete | Supprime une base de données MongoDB. |
> | Action | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/read | Lit une base de données MongoDB ou répertorie toutes les bases de données MongoDB. |
> | Action | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/read | Lit le débit d'une base de données MongoDB. |
> | Action | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/write | Met à jour le débit d'une base de données MongoDB. |
> | Action | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/write | Crée une base de données MongoDB. |
> | Action | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/delete | Supprime un espace de travail de notebook |
> | Action | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/listConnectionInfo/action | Répertorie les informations de connexion pour un espace de travail de notebook |
> | Action | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/operationResults/read | Lit l’état d’une opération asynchrone sur des espaces de travail de notebook |
> | Action | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/read | Lit un espace de travail de notebook |
> | Action | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/write | Crée ou met à jour un espace de travail de notebook |
> | Action | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Mettre hors connexion une région d’un compte de base de données  |
> | Action | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Mettre en ligne une région d’un compte de base de données |
> | Action | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Lit l’état de l’opération asynchrone |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Lit les métriques de latence |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/read | Affiche les centiles des latences de réplication |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Lit les métriques de latence pour une source et une région cible spécifiques |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Lit les métriques de latence pour une région cible spécifique |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Supprime un proxy de connexion de point de terminaison privé du compte de base de données |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/read | Lit l’état de l’opération asynchrone du proxy de connexion de point de terminaison privé |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Lit un proxy de connexion de point de terminaison privé du compte de base de données |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Valide un proxy de connexion de point de terminaison privé du compte de base de données |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Crée ou met à jour un proxy de connexion de point de terminaison privé du compte de base de données |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/delete | Supprime une connexion de point de terminaison privé d’un compte de base de données |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/operationResults/read | Lit l’état de l’opération asynchrone privateEndpointConnections |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/read | Lit une connexion de point de terminaison privé ou répertorie toutes les connexions de point de terminaison privé d’un compte de base de données |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/write | Crée ou met à jour une connexion de point de terminaison privé d’un compte de base de données |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateLinkResources/read | Lit une ressource de liaison privée ou répertorie toutes les ressources de liaison privée d’un compte de base de données |
> | Action | Microsoft.DocumentDB/databaseAccounts/read | Lire un compte de base de données. |
> | Action | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Lire les clés en lecture seule du compte de base de données. |
> | Action | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Lire les clés en lecture seule du compte de base de données. |
> | Action | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Régénérer les clés d’un compte de base de données. |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Lit les métriques de collection régionales. |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Lit les métriques régionales de niveau de clé de partition de compte de base de données |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Lit les métriques régionales de niveau de partition de compte de base de données |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Affiche les partitions de compte de base de données régionales dans une collection |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Lit les métriques de compte de région et de base de données. |
> | Action | Microsoft.DocumentDB/databaseAccounts/restore/action | Soumettre une demande de restauration |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/delete | Supprime un conteneur SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/read | Lit un conteneur SQL ou répertorie tous les conteneurs SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/storedProcedures/delete | Supprime une procédure stockée SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/storedProcedures/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/storedProcedures/read | Lit une procédure stockée SQL ou répertorie toutes les procédures stockées SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/storedProcedures/write | Crée ou met à jour une procédure stockée SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/read | Lit le débit d'un conteneur SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/write | Met à jour le débit d'un conteneur SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/triggers/delete | Supprime un déclencheur SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/triggers/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/triggers/read | Lit un déclencheur SQL ou répertorie tous les déclencheurs SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/triggers/write | Crée ou met à jour un déclencheur SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/userDefinedFunctions/delete | Supprime une fonction SQL définie par l'utilisateur. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/userDefinedFunctions/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/userDefinedFunctions/read | Lit une fonction SQL définie par l'utilisateur ou répertorie toutes les fonctions SQL définies par l'utilisateur. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/userDefinedFunctions/write | Crée ou met à jour une fonction SQL définie par l'utilisateur. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/write | Crée ou met à jour un conteneur SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/delete | Supprime une base de données SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/read | Lit une base de données SQL ou répertorie toutes les bases de données SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/read | Lit le débit d'une base de données SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/write | Met à jour le débit d'une base de données SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/write | Crée une base de données SQL. |
> | Action | Microsoft.DocumentDB/databaseAccounts/tables/delete | Supprimer une table. |
> | Action | Microsoft.DocumentDB/databaseAccounts/tables/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/tables/read | Lit une table ou répertorie toutes les tables. |
> | Action | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/operationResults/read | Lit l’état de l’opération asynchrone. |
> | Action | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/read | Lit un débit de table. |
> | Action | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/write | Met à jour un débit de table. |
> | Action | Microsoft.DocumentDB/databaseAccounts/tables/write | Créer ou mettre à jour une table. |
> | Action | Microsoft.DocumentDB/databaseAccounts/usages/read | Lire les données d’utilisation du compte de base de données. |
> | Action | Microsoft.DocumentDB/databaseAccounts/write | Mettre à jour les comptes de base de données. |
> | Action | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Informe Microsoft.DocumentDB qu’un sous-réseau ou réseau virtuel est en cours de suppression |
> | Action | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Affiche l’état de l’opération asynchrone deleteVirtualNetworkOrSubnets |
> | Action | Microsoft.DocumentDB/locations/operationsStatus/read | Lit l’état des opérations asynchrones |
> | Action | Microsoft.DocumentDB/operationResults/read | Lit l’état de l’opération asynchrone |
> | Action | Microsoft.DocumentDB/operations/read | Lit les opérations disponibles pour Microsoft DocumentDB  |
> | Action | Microsoft.DocumentDB/register/action |  Inscrit le fournisseur de ressources Microsoft DocumentDB pour l’abonnement |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.DomainRegistration/checkDomainAvailability/Action | Vérifier si un domaine est disponible à l’achat. |
> | Action | Microsoft.DomainRegistration/domains/Delete | Supprimer un domaine existant. |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Supprime l’identificateur de propriété |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Affiche les identificateurs de propriété |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Obtient l’identificateur de propriété |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Crée ou met à jour l’identificateur |
> | Action | Microsoft.DomainRegistration/domains/operationresults/Read | Obtenir une opération de domaine. |
> | Action | Microsoft.DomainRegistration/domains/Read | Obtenir la liste des domaines. |
> | Action | Microsoft.DomainRegistration/domains/Read | Obtient le domaine |
> | Action | Microsoft.DomainRegistration/domains/renew/Action | Renouvelle un domaine existant. |
> | Action | Microsoft.DomainRegistration/domains/Write | Ajouter ou mettre à jour un domaine. |
> | Action | Microsoft.DomainRegistration/generateSsoRequest/Action | Générer une demande de connexion au centre de contrôle du domaine. |
> | Action | Microsoft.DomainRegistration/listDomainRecommendations/Action | Récupérer les recommandations de domaine en fonction de mots clés. |
> | Action | Microsoft.DomainRegistration/operations/Read | Affiche toutes les opérations de l’inscription de domaine de service d’application |
> | Action | Microsoft.DomainRegistration/register/action | Inscrire le fournisseur de ressources de domaines Microsoft pour l’abonnement. |
> | Action | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Affiche l’action d’accord |
> | Action | Microsoft.DomainRegistration/topLevelDomains/Read | Obtient les domaines de niveau supérieur |
> | Action | Microsoft.DomainRegistration/topLevelDomains/Read | Obtient le domaine de niveau supérieur |
> | Action | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Valider l’objet d’achat de domaine sans le soumettre. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.EventGrid/domains/delete | Supprime un domaine |
> | Action | Microsoft.EventGrid/domains/listKeys/action | Répertorie les clés d’un domaine |
> | Action | Microsoft.EventGrid/domains/providers/Microsoft.Insights/logDefinitions/read | Autorise l’accès aux journaux de diagnostic |
> | Action | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | Obtient les métriques disponibles pour les domaines |
> | Action | Microsoft.EventGrid/domains/read | Lit un domaine |
> | Action | Microsoft.EventGrid/domains/regenerateKey/action | Régénère une clé pour un domaine |
> | Action | Microsoft.EventGrid/domains/topics/delete | Supprimer une rubrique de domaine |
> | Action | Microsoft.EventGrid/domains/topics/read | Lire une rubrique de domaine |
> | Action | Microsoft.EventGrid/domains/topics/write | Créer ou mettre à jour une rubrique de domaine |
> | Action | Microsoft.EventGrid/domains/write | Crée ou met à jour un domaine |
> | Action | Microsoft.EventGrid/eventSubscriptions/delete | Supprimer un abonnement à un événement |
> | Action | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Obtient l’URL complète de l’abonnement à l’événement |
> | Action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Obtient le paramètre de diagnostic pour les abonnements aux événements |
> | Action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour les abonnements aux événements |
> | Action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Obtient les métriques disponibles pour les abonnements aux événements |
> | Action | Microsoft.EventGrid/eventSubscriptions/read | Lit un abonnement à un événement |
> | Action | Microsoft.EventGrid/eventSubscriptions/write | Créer ou mettre à jour un abonnement à un événement |
> | Action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Obtient le paramètre de diagnostic pour des rubriques |
> | Action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour des rubriques |
> | Action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Obtient les métriques disponibles pour les rubriques |
> | Action | Microsoft.EventGrid/extensionTopics/read | Lire un extensionTopic. |
> | Action | Microsoft.EventGrid/locations/eventSubscriptions/read | Lister les abonnements à des événements régionaux |
> | Action | Microsoft.EventGrid/locations/operationResults/read | Lit le résultat d’une opération régionale |
> | Action | Microsoft.EventGrid/locations/operationsStatus/read | Lit l’état d’une opération régionale |
> | Action | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Lister des abonnements à des événements régionaux par type de rubrique |
> | Action | Microsoft.EventGrid/operationResults/read | Lit le résultat d’une opération |
> | Action | Microsoft.EventGrid/operations/read | Lister les opérations EventGrid. |
> | Action | Microsoft.EventGrid/operationsStatus/read | Lit l’état d’une opération |
> | Action | Microsoft.EventGrid/register/action | Enregistre l’abonnement pour le fournisseur de ressources EventGrid. |
> | Action | Microsoft.EventGrid/topics/delete | Supprimer une rubrique |
> | Action | Microsoft.EventGrid/topics/listKeys/action | Répertorie les clés d’une rubrique |
> | Action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Obtient le paramètre de diagnostic pour des rubriques |
> | Action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour des rubriques |
> | Action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/logDefinitions/read | Autorise l’accès aux journaux de diagnostic |
> | Action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Obtient les métriques disponibles pour les rubriques |
> | Action | Microsoft.EventGrid/topics/read | Lit une rubrique |
> | Action | Microsoft.EventGrid/topics/regenerateKey/action | Régénère une clé pour une rubrique |
> | Action | Microsoft.EventGrid/topics/write | Crée ou met à jour une rubrique |
> | Action | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Lister les abonnements à des événements globaux par type de rubrique |
> | Action | Microsoft.EventGrid/topictypes/eventtypes/read | Lit les types d’événements pris en charge par un type de rubrique |
> | Action | Microsoft.EventGrid/topictypes/read | Lit un type de rubrique |
> | Action | Microsoft.EventGrid/unregister/action | Annule l’inscription de l’abonnement pour le fournisseur de ressources EventGrid. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.EventHub/availableClusterRegions/read | Opération de lecture pour afficher la liste des clusters préconfigurés disponibles par région Azure. |
> | Action | Microsoft.EventHub/checkNameAvailability/action | Vérifie la disponibilité d’un espace de noms sous un abonnement donné. |
> | Action | Microsoft.EventHub/checkNamespaceAvailability/action | Vérifie la disponibilité d’un espace de noms sous un abonnement donné. Cette API étant dépréciée, utilisez CheckNameAvailability à la place. |
> | Action | Microsoft.EventHub/clusters/delete | Supprime une ressource de cluster existante. |
> | Action | Microsoft.EventHub/clusters/namespaces/read | Répertorie les ID Azure Resource Manager d’espace de noms pour les espaces de noms au sein d’un cluster. |
> | Action | Microsoft.EventHub/clusters/operationresults/read | Obtient l’état d’une opération de cluster asynchrone. |
> | Action | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obtient la liste des descriptions de ressources de métriques Cluster |
> | Action | Microsoft.EventHub/clusters/read | Obtient la description de la ressource Cluster |
> | Action | Microsoft.EventHub/clusters/write | Crée ou modifie une ressource de cluster existante. |
> | Action | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Supprime les règles de réseau virtuel dans le fournisseur de ressources EventHub pour le réseau virtuel spécifié |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/action | Met à jour une règle d’autorisation d’espace de noms. Cette API est déconseillée. Utilisez un appel PUT à la place pour mettre à jour la règle d’autorisation d’espace de noms... Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/delete | Supprimer une règle d’autorisation de ressource Namespace. La règle d’autorisation de ressource Namespace par défaut ne peut pas être supprimée.  |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Obtenir la chaîne de connexion à la ressource Namespace. |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/read | Obtenir la liste des descriptions des règles d’autorisation des ressources Namespaces. |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Régénère la clé primaire ou secondaire pour la ressource. |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/write | Créer des règles d’autorisation au niveau d’une ressource Namespace et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour. |
> | Action | Microsoft.EventHub/namespaces/Delete | Supprimer une ressource Namespace |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obtient les clés de règles d’autorisation pour l’espace de noms principal de récupération d’urgence |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obtient les règles d’autorisation de l’espace de noms principal de récupération d’urgence |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Désactive la récupération d’urgence et arrête la réplication des changements de l’espace de noms principal sur l’espace de noms secondaire. |
> | Action | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Vérifie la disponibilité d’un alias d’espace de noms sous un abonnement donné. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Supprime la configuration de récupération d’urgence associée à l’espace de noms. Cette opération peut être appelée uniquement via l’espace de noms principal. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Appelle un basculement de géorécupération d’urgence et reconfigure l’alias d’espace de noms pour qu’il pointe vers l’espace de noms secondaire. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Obtient la configuration de récupération d’urgence associée à l’espace de noms. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Crée ou met à jour la configuration de récupération d’urgence associée à l’espace de noms. |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Met à jour EventHub. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. Règles d’autorisation. Utilisez un appel PUT pour mettre à jour la règle d’autorisation. |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Supprimer des règles d’autorisation EventHub |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Obtenir la chaîne de connexion à EventHub |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Obtenir la liste des règles d’autorisation EventHub |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Régénère la clé primaire ou secondaire pour la ressource. |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Créer des règles d’autorisation EventHub et mettre à jour leurs propriétés. Les droits d’accès des règles d’autorisation peuvent être mis à jour. |
> | Action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Supprimer des ressources du groupe de consommateurs |
> | Action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Obtenir la liste des descriptions de ressources du groupe de consommateurs |
> | Action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Créer ou mettre à jour les propriétés d’un groupe de consommateurs |
> | Action | Microsoft.EventHub/namespaces/eventhubs/Delete | Supprimer des ressources EventHub |
> | Action | Microsoft.EventHub/namespaces/eventhubs/read | Obtenir la liste des descriptions de ressources EventHub |
> | Action | Microsoft.EventHub/namespaces/eventhubs/write | Créer ou mettre à jour des propriétés EventHub |
> | Action | Microsoft.EventHub/namespaces/ipFilterRules/delete | Supprimer une ressource de filtre IP |
> | Action | Microsoft.EventHub/namespaces/ipFilterRules/read | Obtenir une ressource de filtre IP |
> | Action | Microsoft.EventHub/namespaces/ipFilterRules/write | Créer une ressource de filtre IP |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | Recevoir des messages |
> | DataAction | Microsoft.EventHub/namespaces/messages/send/action | Envoyer des messages |
> | Action | Microsoft.EventHub/namespaces/messagingPlan/read | Obtient le plan de messagerie pour un espace de noms.<br>Cette API est déconseillée.<br>Les propriétés exposées via la ressource MessagingPlan sont déplacées vers la ressource d’espace de noms (parente) dans les versions ultérieures de l’API.<br>Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. |
> | Action | Microsoft.EventHub/namespaces/messagingPlan/write | Met à jour le plan de messagerie pour un espace de noms.<br>Cette API est déconseillée.<br>Les propriétés exposées via la ressource MessagingPlan sont déplacées vers la ressource d’espace de noms (parente) dans les versions ultérieures de l’API.<br>Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. |
> | Action | Microsoft.EventHub/namespaces/networkruleset/delete | Supprimer la ressource de règle de réseau virtuel |
> | Action | Microsoft.EventHub/namespaces/networkruleset/read | Obtient la ressource NetworkRuleSet |
> | Action | Microsoft.EventHub/namespaces/networkruleset/write | Créer une ressource de règle de réseau virtuel |
> | Action | Microsoft.EventHub/namespaces/networkrulesets/delete | Supprimer la ressource de règle de réseau virtuel |
> | Action | Microsoft.EventHub/namespaces/networkrulesets/read | Obtient la ressource NetworkRuleSet |
> | Action | Microsoft.EventHub/namespaces/networkrulesets/write | Créer une ressource de règle de réseau virtuel |
> | Action | Microsoft.EventHub/namespaces/operationresults/read | Obtient l’état de l’opération d’espace de noms |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obtenir la liste des descriptions des ressources des paramètres de diagnostics Namespace. |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obtenir la liste des descriptions des ressources des paramètres de diagnostics Namespace. |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obtenir la liste des descriptions des ressources des journaux d’activité Namespace |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obtenir la liste des descriptions de mesures des ressources Namespace |
> | Action | Microsoft.EventHub/namespaces/read | Obtenir la liste des descriptions des ressources Namespace. |
> | Action | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Supprime l’espace de noms ACS |
> | Action | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Supprimer la ressource de règle de réseau virtuel |
> | Action | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Obtient la ressource de règle de réseau virtuel |
> | Action | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Créer une ressource de règle de réseau virtuel |
> | Action | Microsoft.EventHub/namespaces/write | Créer une ressource Namespace et mettre à jour ses propriétés. Les balises et la capacité de l’espace de noms sont les propriétés qui peuvent être mises à jour. |
> | Action | Microsoft.EventHub/operations/read | Obtient les opérations |
> | Action | Microsoft.EventHub/register/action | Inscrire l’abonnement pour le fournisseur de ressources EventHub et activer la création de ressources EventHub |
> | Action | Microsoft.EventHub/sku/read | Obtient la liste des descriptions des ressources de référence (SKU). |
> | Action | Microsoft.EventHub/sku/regions/read | Obtient la liste des descriptions de ressources SkuRegions |
> | Action | Microsoft.EventHub/unregister/action | Enregistre le fournisseur de ressources EventHub |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Features/features/read | Afficher les fonctionnalités d’un abonnement |
> | Action | Microsoft.Features/operations/read | Obtient la liste des opérations. |
> | Action | Microsoft.Features/providers/features/read | Afficher les fonctionnalités d’un abonnement pour un fournisseur de ressources donné |
> | Action | Microsoft.Features/providers/features/register/action | Enregistrer les fonctionnalités d’un abonnement pour un fournisseur de ressources donné |
> | Action | Microsoft.Features/providers/features/unregister/action | Désinscrit la fonctionnalité pour un abonnement dans un fournisseur de ressources donné. |
> | Action | Microsoft.Features/register/action | Enregistre la fonctionnalité d’un abonnement. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | Supprimer l’attribution de la configuration des invités. |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Obtient l’attribution de la configuration des invités. |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Obtient le rapport d’affectation de la configuration invité. |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Crée une nouvelle attribution de configuration des invités. |
> | Action | Microsoft.GuestConfiguration/operations/read | Obtient les opérations pour le fournisseur de ressources Microsoft.GuestConfiguration |
> | Action | Microsoft.GuestConfiguration/register/action | Inscrit l’abonnement pour le fournisseur de ressources Microsoft.GuestConfiguration. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.HDInsight/clusters/applications/delete | Supprimer l’application pour le cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/applications/read | Obtenir l’application pour le cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/applications/write | Créer ou mettre à jour l’application pour le cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/changerdpsetting/action | Modifier le paramètre RDP du cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/configurations/action | Afficher les configurations du cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/configurations/read | Afficher les configurations du cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/delete | Supprimer un cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/extensions/delete | Supprimer l’extension de cluster pour le cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/extensions/read | Obtenir l’extension de cluster pour le cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/extensions/write | Créer l’extension de cluster pour le cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/getGatewaySettings/action | Obtenir les paramètres de passerelle pour un HDInsight Cluster |
> | Action | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Obtient le paramètre de diagnostic pour le cluster HDInsight de ressource |
> | Action | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour le cluster HDInsight de ressource |
> | Action | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obtient les métriques disponibles pour le cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/read | Obtenir des informations sur un cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/roles/resize/action | Redimensionner un cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Mettre à jour les paramètres de passerelle pour un HDInsight Cluster |
> | Action | Microsoft.HDInsight/clusters/write | Créer ou mettre à jour un cluster HDInsight |
> | Action | Microsoft.HDInsight/locations/capabilities/read | Afficher les fonctionnalités de l’abonnement |
> | Action | Microsoft.HDInsight/locations/checkNameAvailability/read | Vérifier la disponibilité du nom |
> | Action | Microsoft.HDInsight/register/action | Inscrire le fournisseur de ressources HDInsight pour l’abonnement |
> | Action | Microsoft.HDInsight/unregister/action | Désinscrire le fournisseur de ressources HDInsight pour l’abonnement |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.HybridCompute/locations/operationresults/read | Lit l’état d’une opération sur le fournisseur de ressources Microsoft.HybridCompute |
> | Action | Microsoft.HybridCompute/machines/delete | Supprime toutes les machines Azure Arc |
> | Action | Microsoft.HybridCompute/machines/extensions/delete | Supprime toutes les extensions Azure Arc |
> | Action | Microsoft.HybridCompute/machines/extensions/read | Lit toutes les extensions Azure Arc |
> | Action | Microsoft.HybridCompute/machines/extensions/write | Installe ou met à jour toutes les extensions Azure Arc |
> | Action | Microsoft.HybridCompute/machines/read | Lit des machines Azure Arc |
> | Action | Microsoft.HybridCompute/machines/reconnect/action | Reconnecte toutes les machines Azure Arc |
> | Action | Microsoft.HybridCompute/machines/write | Écrit toutes les machines Azure Arc |
> | Action | Microsoft.HybridCompute/operations/read | Lire toutes les opérations pour Azure Arc pour les serveurs |
> | Action | Microsoft.HybridCompute/register/action | Inscrit l’abonnement pour le fournisseur de ressources Microsoft.HybridCompute |
> | Action | Microsoft.HybridCompute/unregister/action | Désinscrit l’abonnement pour le fournisseur de ressources Microsoft.HybridCompute |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.ImportExport/jobs/delete | Supprime un travail existant. |
> | Action | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Génère les clés BitLocker pour le travail spécifié. |
> | Action | Microsoft.ImportExport/jobs/read | Affiche les propriétés du travail spécifié ou retourne la liste des travaux. |
> | Action | Microsoft.ImportExport/jobs/write | Crée un travail avec les paramètres spécifiés ou met à jour les propriétés ou les balises pour le travail spécifié. |
> | Action | Microsoft.ImportExport/locations/read | Affiche les propriétés de l’emplacement spécifié ou retourne la liste des emplacements. |
> | Action | Microsoft.ImportExport/operations/read | Obtient les opérations prises en charge par le fournisseur de ressources. |
> | Action | Microsoft.ImportExport/register/action | Registers the subscription for the import/export resource provider and enables the creation of import/export jobs. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Insights/ActionGroups/Delete | Supprimer un groupe d'actions |
> | Action | Microsoft.Insights/ActionGroups/Read | Lire un groupe d'actions |
> | Action | Microsoft.Insights/ActionGroups/Write | Créer ou mettre à jour un groupe d'actions |
> | Action | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Activer les alertes du journal d'activité |
> | Action | Microsoft.Insights/ActivityLogAlerts/Delete | Supprimer une alerte du journal d’activité |
> | Action | Microsoft.Insights/ActivityLogAlerts/Read | Lire une alerte du journal d'activité |
> | Action | Microsoft.Insights/ActivityLogAlerts/Write | Créer ou mettre à jour une alerte du journal d'activité |
> | Action | Microsoft.Insights/AlertRules/Activated/Action | Activer les alertes de métrique classique |
> | Action | Microsoft.Insights/AlertRules/Delete | Supprimer une alerte de métrique classique |
> | Action | Microsoft.Insights/AlertRules/Incidents/Read | Lire un incident d'alerte de métrique classique |
> | Action | Microsoft.Insights/AlertRules/Read | Lire une alerte de métrique classique |
> | Action | Microsoft.Insights/AlertRules/Resolved/Action | Résoudre une alerte de métrique classique |
> | Action | Microsoft.Insights/AlertRules/Throttled/Action | Limiter une règle d'alerte de métrique classique |
> | Action | Microsoft.Insights/AlertRules/Write | Créer ou mettre à jour une alerte de métrique classique |
> | Action | Microsoft.Insights/AutoscaleSettings/Delete | Supprimer un paramètre de mise à l'échelle automatique |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Lire un paramètre de diagnostic de ressource |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Créer ou mettre à jour un paramètre de diagnostic de ressource |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Lire les définitions de journal |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Lire les définitions des mesures |
> | Action | Microsoft.Insights/AutoscaleSettings/Read | Lire un paramètre de mise à l'échelle automatique |
> | Action | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Descente en puissance entamée |
> | Action | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Descente en puissance accomplie |
> | Action | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Montée en puissance entamée |
> | Action | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Montée en puissance accomplie |
> | Action | Microsoft.Insights/AutoscaleSettings/Write | Créer ou mettre à jour un paramètre de mise à l'échelle automatique |
> | Action | Microsoft.Insights/Baseline/Read | Lit une ligne de base métrique (préversion) |
> | Action | Microsoft.Insights/CalculateBaseline/Read | Calcule la ligne de base pour les valeurs de métrique (préversion) |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Delete | Supprime un élément analytique Application Insights |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Read | Lit un élément analytique Application Insights |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Write | Écrit un élément analytique Application Insights |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Action | Action de table analytique Application Insights |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Delete | Supprime un schéma de table analytique Application Insights |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Read | Lit un schéma de table analytique Application Insights |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Write | Écrit un schéma de table analytique Application Insights |
> | Action | Microsoft.Insights/Components/Annotations/Delete | Supprime une annotation Application Insights |
> | Action | Microsoft.Insights/Components/Annotations/Read | Lit une annotation Application Insights |
> | Action | Microsoft.Insights/Components/Annotations/Write | Écrit une annotation Application Insights |
> | Action | Microsoft.Insights/Components/Api/Read | Lit l’API Données de composant Application Insights |
> | Action | Microsoft.Insights/Components/ApiKeys/Action | Génère une clé d’API Application Insights |
> | Action | Microsoft.Insights/Components/ApiKeys/Delete | Supprime une clé d’API Application Insights |
> | Action | Microsoft.Insights/Components/ApiKeys/Read | Lit une clé d’API Application Insights |
> | Action | Microsoft.Insights/Components/BillingPlanForComponent/Read | Lit un plan de facturation pour le composant Application Insights |
> | Action | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Lit les fonctionnalités de facturation actuelles pour le composant Application Insights |
> | Action | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Écrit les fonctionnalités de facturation actuelles pour le composant Application Insights |
> | Action | Microsoft.Insights/Components/DailyCapReached/Action | Limite quotidienne atteinte pour le composant Application Insights |
> | Action | Microsoft.Insights/Components/DailyCapWarningThresholdReached/Action | Seuil d’avertissement de la limite quotidienne du composant Application Insights atteint |
> | Action | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Lit une configuration d’intégration ALM Application Insights par défaut |
> | Action | Microsoft.Insights/Components/Delete | Supprime une configuration du composant Application Insights |
> | Action | Microsoft.Insights/Components/Events/Read | Obtenir les journaux d’activité d’Application Insights en utilisant un format de requête OData |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Action | Action de paramètres d’exportation Application Insights |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Delete | Supprime les paramètres d’exportation Application Insights |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Read | Lit les paramètres d’exportation Application Insights |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Write | Écrit les paramètres d’exportation Application Insights |
> | Action | Microsoft.Insights/Components/ExtendQueries/Read | Lit les résultats de requête étendue du composant Application Insights |
> | Action | Microsoft.Insights/Components/Favorites/Delete | Supprime un favori Application Insights |
> | Action | Microsoft.Insights/Components/Favorites/Read | Lit un favori Application Insights |
> | Action | Microsoft.Insights/Components/Favorites/Write | Écrit un favori Application Insights |
> | Action | Microsoft.Insights/Components/FeatureCapabilities/Read | Lit les capacités de fonctionnalité du composant Application Insights |
> | Action | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Lit les fonctionnalités de facturation disponibles du composant Application Insights |
> | Action | Microsoft.Insights/Components/GetToken/Read | Lit un jeton du composant Application Insights |
> | Action | Microsoft.Insights/Components/MetricDefinitions/Read | Lit les définitions de métrique du composant Application Insights |
> | Action | Microsoft.Insights/Components/Metrics/Read | Lit les métriques du composant Application Insights |
> | Action | Microsoft.Insights/Components/Move/Action | Déplace un composant Application Insights vers un autre groupe de ressources ou abonnement |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Supprime un élément analytique personnel Application Insights |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Read | Lit un élément analytique personnel Application Insights |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Write | Écrit un élément analytique personnel Application Insights |
> | Action | Microsoft.Insights/Components/MyFavorites/Read | Lit un favori personnel Application Insights |
> | Action | Microsoft.Insights/Components/Operations/Read | Obtenir l’état des opérations longues dans Application Insights |
> | Action | Microsoft.Insights/Components/PricingPlans/Read | Lit un plan de tarification du composant Application Insights |
> | Action | Microsoft.Insights/Components/PricingPlans/Write | Écrit un plan de tarification du composant Application Insights |
> | Action | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Lit une configuration de détection proactive Application Insights |
> | Action | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Écrit une configuration de détection proactive Application Insights |
> | Action | Microsoft.Insights/Components/providers/Microsoft.Insights/diagnosticSettings/Read | Lire un paramètre de diagnostic de ressource |
> | Action | Microsoft.Insights/Components/providers/Microsoft.Insights/diagnosticSettings/Write | Créer ou mettre à jour un paramètre de diagnostic de ressource |
> | Action | Microsoft.Insights/Components/providers/Microsoft.Insights/logDefinitions/Read | Lire les définitions de journal |
> | Action | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Lire les définitions des mesures |
> | Action | Microsoft.Insights/Components/Purge/Action | Vider des données d’Application Insights |
> | Action | Microsoft.Insights/Components/Query/Read | Exécuter des requêtes sur les journaux d’activité d’Application Insights |
> | Action | Microsoft.Insights/Components/QuotaStatus/Read | Lit l’état de quota du composant Application Insights |
> | Action | Microsoft.Insights/Components/Read | Lit une configuration du composant Application Insights |
> | Action | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Lit les emplacements de test web Application Insights |
> | Action | Microsoft.Insights/Components/Webtests/Read | Lit une configuration de test web |
> | Action | Microsoft.Insights/Components/WorkItemConfigs/Delete | Supprime une configuration d’intégration ALM Application Insights |
> | Action | Microsoft.Insights/Components/WorkItemConfigs/Read | Lit une configuration d’intégration ALM Application Insights |
> | Action | Microsoft.Insights/Components/WorkItemConfigs/Write | Écrit une configuration d’intégration ALM Application Insights |
> | Action | Microsoft.Insights/Components/Write | Écrit dans une configuration du composant Application Insights |
> | Action | Microsoft.Insights/DataCollectionRuleAssociations/Delete | Supprime l’association d’une ressource à une règle de collecte de données |
> | Action | Microsoft.Insights/DataCollectionRuleAssociations/Read | Lit l’association d’une ressource à une règle de collecte de données |
> | Action | Microsoft.Insights/DataCollectionRuleAssociations/Write | Crée ou met à jour l’association d’une ressource à une règle de collecte de données |
> | DataAction | Microsoft.Insights/DataCollectionRules/Data/Write | Envoie des données une règle de collecte de données |
> | Action | Microsoft.Insights/DataCollectionRules/Delete | Supprime une règle de collecte de données |
> | Action | Microsoft.Insights/DataCollectionRules/Read | Lit une règle de collecte de données |
> | Action | Microsoft.Insights/DataCollectionRules/Write | Crée ou met à jour une règle de collecte de données |
> | Action | Microsoft.Insights/DiagnosticSettings/Delete | Supprimer un paramètre de diagnostic de ressource |
> | Action | Microsoft.Insights/DiagnosticSettings/Read | Lire un paramètre de diagnostic de ressource |
> | Action | Microsoft.Insights/DiagnosticSettings/Write | Créer ou mettre à jour un paramètre de diagnostic de ressource |
> | Action | Microsoft.Insights/EventCategories/Read | Lire les catégories d'événement de journal d'activité disponibles |
> | Action | Microsoft.Insights/eventtypes/digestevents/Read | Lire le résumé des types d’événement de gestion |
> | Action | Microsoft.Insights/eventtypes/values/Read | Lire les événements du journal d'activité |
> | Action | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Supprimer un paramètre de diagnostic du journal de flux réseau |
> | Action | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Lire un paramètre de diagnostic du journal de flux réseau |
> | Action | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Créer ou mettre à jour un paramètre de diagnostic du journal de flux réseau |
> | Action | Microsoft.Insights/ListMigrationDate/Action | Récupère la date de migration de l’abonnement |
> | Action | Microsoft.Insights/ListMigrationDate/Read | Récupère la date de migration de l’abonnement |
> | Action | Microsoft.Insights/LogDefinitions/Read | Lire les définitions de journal |
> | Action | Microsoft.Insights/LogProfiles/Delete | Supprimer un profil de journal d'activité |
> | Action | Microsoft.Insights/LogProfiles/Read | Lire un profil de journal d'activité |
> | Action | Microsoft.Insights/LogProfiles/Write | Créer ou mettre à jour un profil de journal d'activité |
> | Action | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Lire les données de la table ADAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/ADReplicationResult/Read | Lire les données de la table ADReplicationResult |
> | Action | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Lire les données de la table ADSecurityAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/Alert/Read | Lire les données de la table Alert |
> | Action | Microsoft.Insights/Logs/AlertHistory/Read | Lire les données de la table AlertHistory |
> | Action | Microsoft.Insights/Logs/ApplicationInsights/Read | Lire les données de la table ApplicationInsights |
> | Action | Microsoft.Insights/Logs/AzureActivity/Read | Lire les données de la table AzureActivity |
> | Action | Microsoft.Insights/Logs/AzureMetrics/Read | Lire les données de la table AzureMetrics |
> | Action | Microsoft.Insights/Logs/BoundPort/Read | Lire les données de la table BoundPort |
> | Action | Microsoft.Insights/Logs/CommonSecurityLog/Read | Lire les données de la table CommonSecurityLog |
> | Action | Microsoft.Insights/Logs/ComputerGroup/Read | Lire les données de la table ComputerGroup |
> | Action | Microsoft.Insights/Logs/ConfigurationChange/Read | Lire les données de la table ConfigurationChange |
> | Action | Microsoft.Insights/Logs/ConfigurationData/Read | Lire les données de la table ConfigurationData |
> | Action | Microsoft.Insights/Logs/ContainerImageInventory/Read | Lire les données de la table ContainerImageInventory |
> | Action | Microsoft.Insights/Logs/ContainerInventory/Read | Lire les données de la table ContainerInventory |
> | Action | Microsoft.Insights/Logs/ContainerLog/Read | Lire les données de la table ContainerLog |
> | Action | Microsoft.Insights/Logs/ContainerServiceLog/Read | Lire les données de la table ContainerServiceLog |
> | Action | Microsoft.Insights/Logs/DeviceAppCrash/Read | Lire les données de la table DeviceAppCrash |
> | Action | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Lire les données de la table DeviceAppLaunch |
> | Action | Microsoft.Insights/Logs/DeviceCalendar/Read | Lire les données de la table DeviceCalendar |
> | Action | Microsoft.Insights/Logs/DeviceCleanup/Read | Lire les données de la table DeviceCleanup |
> | Action | Microsoft.Insights/Logs/DeviceConnectSession/Read | Lire les données de la table DeviceConnectSession |
> | Action | Microsoft.Insights/Logs/DeviceEtw/Read | Lire les données de la table DeviceEtw |
> | Action | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Lire les données de la table DeviceHardwareHealth |
> | Action | Microsoft.Insights/Logs/DeviceHealth/Read | Lire les données de la table DeviceHealth |
> | Action | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Lire les données de la table DeviceHeartbeat |
> | Action | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Lire les données de la table DeviceSkypeHeartbeat |
> | Action | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Lire les données de la table DeviceSkypeSignIn |
> | Action | Microsoft.Insights/Logs/DeviceSleepState/Read | Lire les données de la table DeviceSleepState |
> | Action | Microsoft.Insights/Logs/DHAppFailure/Read | Lire les données de la table DHAppFailure |
> | Action | Microsoft.Insights/Logs/DHAppReliability/Read | Lire les données de la table DHAppReliability |
> | Action | Microsoft.Insights/Logs/DHDriverReliability/Read | Lire les données de la table DHDriverReliability |
> | Action | Microsoft.Insights/Logs/DHLogonFailures/Read | Lire les données de la table DHLogonFailures |
> | Action | Microsoft.Insights/Logs/DHLogonMetrics/Read | Lire les données de la table DHLogonMetrics |
> | Action | Microsoft.Insights/Logs/DHOSCrashData/Read | Lire les données de la table DHOSCrashData |
> | Action | Microsoft.Insights/Logs/DHOSReliability/Read | Lire les données de la table DHOSReliability |
> | Action | Microsoft.Insights/Logs/DHWipAppLearning/Read | Lire les données de la table DHWipAppLearning |
> | Action | Microsoft.Insights/Logs/DnsEvents/Read | Lire les données de la table DnsEvents |
> | Action | Microsoft.Insights/Logs/DnsInventory/Read | Lire les données de la table DnsInventory |
> | Action | Microsoft.Insights/Logs/ETWEvent/Read | Lire les données de la table ETWEvent |
> | Action | Microsoft.Insights/Logs/Event/Read | Lire les données de la table Event |
> | Action | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Lire les données de la table ExchangeAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Lire les données de la table ExchangeOnlineAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/Heartbeat/Read | Lire les données de la table Heartbeat |
> | Action | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Lire les données de la table IISAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/InboundConnection/Read | Lire les données de la table InboundConnection |
> | Action | Microsoft.Insights/Logs/KubeNodeInventory/Read | Lire les données de la table KubeNodeInventory |
> | Action | Microsoft.Insights/Logs/KubePodInventory/Read | Lire les données de la table KubePodInventory |
> | Action | Microsoft.Insights/Logs/LinuxAuditLog/Read | Lire les données de la table LinuxAuditLog |
> | Action | Microsoft.Insights/Logs/MAApplication/Read | Lire les données de la table MAApplication |
> | Action | Microsoft.Insights/Logs/MAApplicationHealth/Read | Lire les données de la table MAApplicationHealth |
> | Action | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Lire les données de la table MAApplicationHealthAlternativeVersions |
> | Action | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Lire les données de la table MAApplicationHealthIssues |
> | Action | Microsoft.Insights/Logs/MAApplicationInstance/Read | Lire les données de la table MAApplicationInstance |
> | Action | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Lire les données de la table MAApplicationInstanceReadiness |
> | Action | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Lire les données de la table MAApplicationReadiness |
> | Action | Microsoft.Insights/Logs/MADeploymentPlan/Read | Lire les données de la table MADeploymentPlan |
> | Action | Microsoft.Insights/Logs/MADevice/Read | Lire les données de la table MADevice |
> | Action | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Lire les données de la table MADevicePnPHealth |
> | Action | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Lire les données de la table MADevicePnPHealthAlternativeVersions |
> | Action | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Lire les données de la table MADevicePnPHealthIssues |
> | Action | Microsoft.Insights/Logs/MADeviceReadiness/Read | Lire les données de la table MADeviceReadiness |
> | Action | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Lire les données de la table MADriverInstanceReadiness |
> | Action | Microsoft.Insights/Logs/MADriverReadiness/Read | Lire les données de la table MADriverReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeAddin/Read | Lire les données de la table MAOfficeAddin |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Lire les données de la table MAOfficeAddinHealth |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Lire les données de la table MAOfficeAddinHealthIssues |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | Lire les données de la table MAOfficeAddinInstance |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Lire les données de la table MAOfficeAddinInstanceReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Lire les données de la table MAOfficeAddinReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeApp/Read | Lire les données de la table MAOfficeApp |
> | Action | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Lire les données de la table MAOfficeAppHealth |
> | Action | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Lire les données de la table MAOfficeAppInstance |
> | Action | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Lire les données de la table MAOfficeAppReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Lire les données de la table MAOfficeBuildInfo |
> | Action | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | Lire les données de la table MAOfficeCurrencyAssessment |
> | Action | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Lire les données de la table MAOfficeCurrencyAssessmentDailyCounts |
> | Action | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Lire les données de la table MAOfficeDeploymentStatus |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Lire les données de la table MAOfficeMacroHealth |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Lire les données de la table MAOfficeMacroHealthIssues |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Lire les données de la table MAOfficeMacroIssueInstanceReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Lire les données de la table MAOfficeMacroIssueReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | Lire les données de la table MAOfficeMacroSummary |
> | Action | Microsoft.Insights/Logs/MAOfficeSuite/Read | Lire les données de la table MAOfficeSuite |
> | Action | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Lire les données de la table MAOfficeSuiteInstance |
> | Action | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Lire les données de la table MAProposedPilotDevices |
> | Action | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Lire les données de la table MAWindowsBuildInfo |
> | Action | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Lire les données de la table MAWindowsCurrencyAssessment |
> | Action | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Lire les données de la table MAWindowsCurrencyAssessmentDailyCounts |
> | Action | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Lire les données de la table MAWindowsDeploymentStatus |
> | Action | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Lire les données de la table MAWindowsSysReqInstanceReadiness |
> | Action | Microsoft.Insights/Logs/NetworkMonitoring/Read | Lire les données de la table NetworkMonitoring |
> | Action | Microsoft.Insights/Logs/OfficeActivity/Read | Lire les données de la table OfficeActivity |
> | Action | Microsoft.Insights/Logs/Operation/Read | Lire les données de la table de Operation |
> | Action | Microsoft.Insights/Logs/OutboundConnection/Read | Lire les données de la table OutboundConnection |
> | Action | Microsoft.Insights/Logs/Perf/Read | Lire les données de la table Perf |
> | Action | Microsoft.Insights/Logs/ProtectionStatus/Read | Lire les données de la table ProtectionStatus |
> | Action | Microsoft.Insights/Logs/Read | Lire les données de tous vos journaux d’activité |
> | Action | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Lire les données de la table ReservedAzureCommonFields |
> | Action | Microsoft.Insights/Logs/ReservedCommonFields/Read | Lire les données de la table ReservedCommonFields |
> | Action | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Lire les données de la table SCCMAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Lire les données de la table SCOMAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SecurityAlert/Read | Lire les données de la table SecurityAlert |
> | Action | Microsoft.Insights/Logs/SecurityBaseline/Read | Lire les données de la table SecurityBaseline |
> | Action | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Lire les données de la table SecurityBaselineSummary |
> | Action | Microsoft.Insights/Logs/SecurityDetection/Read | Lire les données de la table SecurityDetection |
> | Action | Microsoft.Insights/Logs/SecurityEvent/Read | Lire les données de la table SecurityEvent |
> | Action | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Lire les données de la table ServiceFabricOperationalEvent |
> | Action | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Lire les données de la table ServiceFabricReliableActorEvent |
> | Action | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Lire les données de la table ServiceFabricReliableServiceEvent |
> | Action | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Lire les données de la table SfBAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Lire les données de la table SfBOnlineAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Lire les données de la table SharePointOnlineAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Lire les données de la table SPAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Lire les données de la table SQLAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Lire les données de la table SQLQueryPerformance |
> | Action | Microsoft.Insights/Logs/Syslog/Read | Lire les données de la table de Syslog |
> | Action | Microsoft.Insights/Logs/SysmonEvent/Read | Lire les données de la table SysmonEvent |
> | Action | Microsoft.Insights/Logs/Tables.Custom/Read | Lire les données de n’importe quel journal personnalisé |
> | Action | Microsoft.Insights/Logs/UAApp/Read | Lire les données de la table UAApp |
> | Action | Microsoft.Insights/Logs/UAComputer/Read | Lire les données de la table UAComputer |
> | Action | Microsoft.Insights/Logs/UAComputerRank/Read | Lire les données de la table UAComputerRank |
> | Action | Microsoft.Insights/Logs/UADriver/Read | Lire les données de la table UADriver |
> | Action | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Lire les données de la table UADriverProblemCodes |
> | Action | Microsoft.Insights/Logs/UAFeedback/Read | Lire les données de la table UAFeedback |
> | Action | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Lire les données de la table UAHardwareSecurity |
> | Action | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Lire les données de la table UAIESiteDiscovery |
> | Action | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Lire les données de la table UAOfficeAddIn |
> | Action | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Lire les données de la table UAProposedActionPlan |
> | Action | Microsoft.Insights/Logs/UASysReqIssue/Read | Lire les données de la table UASysReqIssue |
> | Action | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Lire les données de la table UAUpgradedComputer |
> | Action | Microsoft.Insights/Logs/Update/Read | Lire les données de la table Update |
> | Action | Microsoft.Insights/Logs/UpdateRunProgress/Read | Lire les données de la table UpdateRunProgress |
> | Action | Microsoft.Insights/Logs/UpdateSummary/Read | Lire les données de la table UpdateSummary |
> | Action | Microsoft.Insights/Logs/Usage/Read | Lire les données de la table Usage |
> | Action | Microsoft.Insights/Logs/W3CIISLog/Read | Lire les données de la table W3CIISLog |
> | Action | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Lire les données de la table WaaSDeploymentStatus |
> | Action | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Lire les données de la table WaaSInsiderStatus |
> | Action | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Lire les données de la table WaaSUpdateStatus |
> | Action | Microsoft.Insights/Logs/WDAVStatus/Read | Lire les données de la table WDAVStatus |
> | Action | Microsoft.Insights/Logs/WDAVThreat/Read | Lire les données de la table WDAVThreat |
> | Action | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Lire les données de la table WindowsClientAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/WindowsFirewall/Read | Lire les données de la table WindowsFirewall |
> | Action | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Lire les données de la table WindowsServerAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/WireData/Read | Lire les données de la table WireData |
> | Action | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Lire les données de la table WUDOAggregatedStatus |
> | Action | Microsoft.Insights/Logs/WUDOStatus/Read | Lire les données de la table WUDOStatus |
> | Action | Microsoft.Insights/MetricAlerts/Delete | Supprimer une alerte de métrique |
> | Action | Microsoft.Insights/MetricAlerts/Read | Lire une alerte de métrique |
> | Action | Microsoft.Insights/MetricAlerts/Status/Read | Lire l'état d'une alerte de métrique |
> | Action | Microsoft.Insights/MetricAlerts/Write | Créer ou mettre à jour une alerte de métrique |
> | Action | Microsoft.Insights/MetricBaselines/Read | Lit les lignes de base des métriques |
> | Action | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Lire les définitions des mesures |
> | Action | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Lire les définitions des mesures |
> | Action | Microsoft.Insights/MetricDefinitions/Read | Lire les définitions des mesures |
> | Action | Microsoft.Insights/Metricnamespaces/Read | Lit les espaces de noms des métriques |
> | Action | Microsoft.Insights/Metrics/Action | Action de métrique |
> | Action | Microsoft.Insights/Metrics/Microsoft.Insights/Read | Lire des mesures |
> | Action | Microsoft.Insights/Metrics/providers/Metrics/Read | Lire des mesures |
> | Action | Microsoft.Insights/Metrics/Read | Lire des mesures |
> | DataAction | Microsoft.Insights/Metrics/Write | Écrit des métriques |
> | Action | Microsoft.Insights/MigrateToNewpricingModel/Action | Migre un abonnement vers un nouveau modèle de tarification |
> | Action | Microsoft.Insights/myWorkbooks/Read | Lit un classeur privé |
> | Action | Microsoft.Insights/myWorkbooks/Write | Crée ou met à jour un classeur privé |
> | Action | Microsoft.Insights/Operations/Read | Lire les opérations |
> | Action | Microsoft.Insights/PrivateLinkScopes/Delete | Supprime une étendue de liaison privée |
> | Action | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Delete | Supprime un proxy de connexion de point de terminaison privé |
> | Action | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Read | Crée un proxy de connexion de point de terminaison privé |
> | Action | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Validate/Action | Valide un proxy de connexion de point de terminaison privé |
> | Action | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Write | Crée ou met à jour un proxy de connexion de point de terminaison privé |
> | Action | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnections/Delete | Supprime une connexion de point de terminaison privé |
> | Action | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnections/Read | Lit une connexion de point de terminaison privé |
> | Action | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnections/Write | Crée ou met à jour une connexion de point de terminaison privé |
> | Action | Microsoft.Insights/PrivateLinkScopes/PrivateLinkResources/Read | Lire une ressource de liaison privée |
> | Action | Microsoft.Insights/PrivateLinkScopes/Read | Lit une étendue de liaison privée |
> | Action | Microsoft.Insights/PrivateLinkScopes/ScopedResources/Delete | Supprime une ressource limitée à une liaison privée |
> | Action | Microsoft.Insights/PrivateLinkScopes/ScopedResources/Read | Lit une ressource limitée à une liaison privée |
> | Action | Microsoft.Insights/PrivateLinkScopes/ScopedResources/Write | Crée ou met à jour une ressource limitée à une liaison privée |
> | Action | Microsoft.Insights/PrivateLinkScopes/Write | Crée ou met à jour une étendue de liaison privée |
> | Action | Microsoft.Insights/Register/Action | Inscrire le fournisseur Microsoft Insights |
> | Action | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Restaure un abonnement au modèle de tarification hérité |
> | Action | Microsoft.Insights/ScheduledQueryRules/Delete | Supprime une règle de requête planifiée |
> | Action | Microsoft.Insights/ScheduledQueryRules/Read | Affiche une règle de requête planifiée |
> | Action | Microsoft.Insights/ScheduledQueryRules/Write | Écrit une règle de requête planifiée |
> | Action | Microsoft.Insights/Tenants/Register/Action | Initialiser le fournisseur Microsoft Insights |
> | Action | Microsoft.Insights/Unregister/Action | Inscrire le fournisseur Microsoft Insights |
> | Action | Microsoft.Insights/Webtests/Delete | Supprime une configuration de test web |
> | Action | Microsoft.Insights/Webtests/GetToken/Read | Lit un jeton de test web |
> | Action | Microsoft.Insights/Webtests/MetricDefinitions/Read | Lit des définitions de métrique de test web |
> | Action | Microsoft.Insights/Webtests/Metrics/Read | Lit des métriques de test web |
> | Action | Microsoft.Insights/Webtests/Read | Lit une configuration de test web |
> | Action | Microsoft.Insights/Webtests/Write | Écrit dans une configuration de test web |
> | Action | Microsoft.Insights/Workbooks/Delete | Supprimer un classeur |
> | Action | Microsoft.Insights/Workbooks/Read | Lire un classeur |
> | Action | Microsoft.Insights/Workbooks/Write | Créer ou mettre à jour un classeur |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Intune/diagnosticsettings/delete | Désactive un paramètre de diagnostic |
> | Action | Microsoft.Intune/diagnosticsettings/read | Lit un paramètre de diagnostic |
> | Action | Microsoft.Intune/diagnosticsettings/write | Écrit un paramètre de diagnostic |
> | Action | Microsoft.Intune/diagnosticsettingscategories/read | Lit les catégories d’un paramètre de diagnostic |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.IoTCentral/appTemplates/action | Obtient tous les modèles d’application disponibles sur Azure IoT Central |
> | Action | Microsoft.IoTCentral/checkNameAvailability/action | Vérifie si un nom d’application IoT Central est disponible |
> | Action | Microsoft.IoTCentral/checkSubdomainAvailability/action | Vérifie si un sous-domaine d’application IoT Central est disponible |
> | Action | Microsoft.IoTCentral/IoTApps/delete | Supprime des applications IoT Central |
> | Action | Microsoft.IoTCentral/IoTApps/read | Obtient une seule application IoT Central |
> | Action | Microsoft.IoTCentral/IoTApps/write | Crée ou met à jour des applications IoT Central |
> | Action | Microsoft.IoTCentral/operations/read | Obtient toutes les opérations disponibles sur les Applications IoT Central |
> | Action | Microsoft.IoTCentral/register/action | Inscrire l’abonnement pour le fournisseur de ressources Azure IoT Central |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.IoTSpaces/Graph/delete | Supprime la ressource graphique Microsoft.IoTSpaces |
> | Action | Microsoft.IoTSpaces/Graph/read | Obtient la/les ressource(s) graphique(s) Microsoft.IoTSpaces |
> | Action | Microsoft.IoTSpaces/Graph/write | Crée la ressource graphique Microsoft.IoTSpaces |
> | Action | Microsoft.IoTSpaces/register/action | Inscrire l’abonnement pour le fournisseur de ressources graphiques Microsoft.IoTSpaces afin d’activer la création de ressources |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.KeyVault/checkNameAvailability/read | Vérifier que le nom du coffre Key Vault est valide et qu’il n’est pas déjà utilisé |
> | Action | Microsoft.KeyVault/deletedVaults/read | Afficher les propriétés de coffres Key Vault supprimés de manière réversible |
> | Action | Microsoft.KeyVault/hsmPools/delete | Supprime un pool HSM |
> | Action | Microsoft.KeyVault/hsmPools/joinVault/action | Joint un coffre de clés à un pool HSM |
> | Action | Microsoft.KeyVault/hsmPools/read | Affiche les propriétés d’un pool HSM |
> | Action | Microsoft.KeyVault/hsmPools/write | Crée un pool HSM pour mettre à jour les propriétés d’un pool HSM existant |
> | Action | Microsoft.KeyVault/locations/deletedVaults/purge/action | Vider un coffre Key Vault supprimé de manière réversible |
> | Action | Microsoft.KeyVault/locations/deletedVaults/read | Afficher les propriétés d’un coffre Key Vault supprimé de manière réversible |
> | Action | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Avertit Microsoft.KeyVault de la suppression d’un réseau virtuel ou d’un sous-réseau |
> | Action | Microsoft.KeyVault/locations/operationResults/read | Vérifier le résultat d’une opération longue |
> | Action | Microsoft.KeyVault/operations/read | Répertorie les opérations disponibles sur le fournisseur de ressources Microsoft.KeyVault |
> | Action | Microsoft.KeyVault/register/action | Enregistrer un abonnement |
> | Action | Microsoft.KeyVault/unregister/action | Désinscrit un abonnement |
> | Action | Microsoft.KeyVault/vaults/accessPolicies/write | Mettre à jour une stratégie d’accès existante via une fusion ou un remplacement, ou ajouter une nouvelle stratégie d’accès à un coffre |
> | DataAction | Microsoft.KeyVault/vaults/certificatecas/delete | Supprime l'émetteur du certificat |
> | DataAction | Microsoft.KeyVault/vaults/certificatecas/read | Lit l'émetteur du certificat |
> | DataAction | Microsoft.KeyVault/vaults/certificatecas/write | Écrit l'émetteur du certificat |
> | DataAction | Microsoft.KeyVault/vaults/certificatecontacts/write | Gère le contact du certificat |
> | DataAction | Microsoft.KeyVault/vaults/certificates/backup/action | Crée le fichier de sauvegarde d'un certificat. Le fichier peut être utilisé pour restaurer le certificat dans un coffre de clés Key Vault du même abonnement. Des restrictions peuvent s'appliquer. |
> | DataAction | Microsoft.KeyVault/vaults/certificates/create/action | Crée un certificat. |
> | DataAction | Microsoft.KeyVault/vaults/certificates/delete | Supprime un certificat. |
> | DataAction | Microsoft.KeyVault/vaults/certificates/import/action | Importe un certificat valide existant, contenant une clé privée, dans Azure Key Vault. Le certificat à importer peut être au format PFX ou PEM. |
> | DataAction | Microsoft.KeyVault/vaults/certificates/purge/action | Vide un certificat, ce qui le rend irrécupérable. |
> | DataAction | Microsoft.KeyVault/vaults/certificates/read | Répertorie les certificats d'un coffre de clés spécifié, ou permet d'obtenir des informations sur un certificat. |
> | DataAction | Microsoft.KeyVault/vaults/certificates/recover/action | Permet de récupérer le certificat supprimé. L'opération annule l'opération de suppression. L'opération est applicable dans les coffres activés pour la suppression réversible et doit être émise pendant l'intervalle de conservation. |
> | DataAction | Microsoft.KeyVault/vaults/certificates/restore/action | Restaure un certificat sauvegardé et toutes ses versions dans un coffre. |
> | DataAction | Microsoft.KeyVault/vaults/certificates/update/action | Met à jour les attributs spécifiés associés au certificat donné. |
> | Action | Microsoft.KeyVault/vaults/delete | Supprimer un coffre Key Vault |
> | Action | Microsoft.KeyVault/vaults/deploy/action | Permettre l’accès aux clés secrètes dans un coffre Key Vault lors du déploiement de ressources Azure |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/delete | Avertit Microsoft.KeyVault de la suppression d’un abonnement EventGrid pour Key Vault |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/read | Avertit Microsoft.KeyVault de la visualisation d’un abonnement EventGrid pour Key Vault |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/write | Avertit Microsoft.KeyVault de la création d’un nouvel abonnement EventGrid pour Key Vault |
> | DataAction | Microsoft.KeyVault/vaults/keys/backup/action | Crée le fichier de sauvegarde d'une clé. Le fichier peut être utilisé pour restaurer la clé dans un coffre de clés Key Vault du même abonnement. Des restrictions peuvent s'appliquer. |
> | DataAction | Microsoft.KeyVault/vaults/keys/create/action | Crée une clé. |
> | DataAction | Microsoft.KeyVault/vaults/keys/decrypt/action | Déchiffre le texte chiffré avec une clé. |
> | DataAction | Microsoft.KeyVault/vaults/keys/delete | Supprime une clé. |
> | DataAction | Microsoft.KeyVault/vaults/keys/encrypt/action | Chiffre le texte en clair avec une clé. Notez que si la clé est asymétrique, cette opération peut être effectuée par des principaux avec accès en lecture. |
> | DataAction | Microsoft.KeyVault/vaults/keys/import/action | Importe une clé créée en externe, l'enregistre, et renvoie les paramètres et attributs de la clé au client. |
> | DataAction | Microsoft.KeyVault/vaults/keys/purge/action | Vide une clé, ce qui la rend irrécupérable. |
> | DataAction | Microsoft.KeyVault/vaults/keys/read | Répertorie les clés dans le coffre spécifié, ou lit les propriétés et le matériel public d'une clé.<br>Pour les clés asymétriques, cette opération expose la clé publique et permet d'exécuter des algorithmes de clé publique, comme le chiffrement et la vérification de la signature.<br>Les clés privées et les clés symétriques ne sont jamais exposées. |
> | DataAction | Microsoft.KeyVault/vaults/keys/recover/action | Permet de récupérer la clé supprimée. L'opération annule l'opération de suppression. L'opération est applicable dans les coffres activés pour la suppression réversible et doit être émise pendant l'intervalle de conservation. |
> | DataAction | Microsoft.KeyVault/vaults/keys/restore/action | Restaure une clé sauvegardée et toutes ses versions dans un coffre. |
> | DataAction | Microsoft.KeyVault/vaults/keys/sign/action | Signe un code de hachage avec une clé. |
> | DataAction | Microsoft.KeyVault/vaults/keys/unwrap/action | Renvoie une clé symétrique avec une clé Key Vault. |
> | DataAction | Microsoft.KeyVault/vaults/keys/update/action | Met à jour les attributs spécifiés associés à la clé donnée. |
> | DataAction | Microsoft.KeyVault/vaults/keys/verify/action | Vérifie un code de hachage. Notez que si la clé est asymétrique, cette opération peut être effectuée par des principaux avec accès en lecture. |
> | DataAction | Microsoft.KeyVault/vaults/keys/wrap/action | Enveloppe une clé symétrique avec une clé Key Vault. Notez que si la clé Key Vault est asymétrique, cette opération peut être effectuée avec accès en lecture. |
> | Action | Microsoft.KeyVault/vaults/read | Afficher les propriétés d’un coffre Key Vault |
> | DataAction | Microsoft.KeyVault/vaults/secrets/backup/action | Crée le fichier de sauvegarde d'un secret. Le fichier peut être utilisé pour restaurer le secret dans un coffre de clés Key Vault du même abonnement. Des restrictions peuvent s'appliquer. |
> | DataAction | Microsoft.KeyVault/vaults/secrets/delete | Supprime un secret. |
> | DataAction | Microsoft.KeyVault/vaults/secrets/getSecret/action | Permet d'obtenir la valeur d'un secret. |
> | DataAction | Microsoft.KeyVault/vaults/secrets/purge/action | Vide un secret, ce le rend irrécupérable. |
> | Action | Microsoft.KeyVault/vaults/secrets/read | Afficher les propriétés d’une clé secrète, mais pas sa valeur. |
> | DataAction | Microsoft.KeyVault/vaults/secrets/readMetadata/action | Permet de répertorier ou d'affiche les propriétés d'un secret, mais pas sa valeur. |
> | DataAction | Microsoft.KeyVault/vaults/secrets/recover/action | Permet de récupérer le secret supprimé. L'opération annule l'opération de suppression. L'opération est applicable dans les coffres activés pour la suppression réversible et doit être émise pendant l'intervalle de conservation. |
> | DataAction | Microsoft.KeyVault/vaults/secrets/restore/action | Restaure un secret sauvegardé et toutes ses versions dans un coffre. |
> | DataAction | Microsoft.KeyVault/vaults/secrets/setSecret/action | Crée un secret. |
> | DataAction | Microsoft.KeyVault/vaults/secrets/update/action | Met à jour les attributs spécifiés associés au secret donné. |
> | Action | Microsoft.KeyVault/vaults/secrets/write | Créer une nouvelle clé sécrète ou mettre à jour la valeur d’une clé secrète existante. |
> | Action | Microsoft.KeyVault/vaults/write | Créer un coffre Key Vault ou mettre à jour les propriétés d’un coffre Key Vault existant |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Kusto/Clusters/Activate/action | Démarre le cluster. |
> | Action | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | Supprime une ressource de configuration de base de données attachée. |
> | Action | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | Lit une ressource de configuration de base de données attachée. |
> | Action | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | Écrit une ressource de configuration de base de données attachée. |
> | Action | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Vérifie la disponibilité du nom du cluster. |
> | Action | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Ajoute des principaux de base de données. |
> | Action | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | Vérifie la disponibilité du nom pour un type donné. |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Supprime une ressource de connexions de données. |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Lit une ressource de connexions de données. |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Écrit une ressource de connexions de données. |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Valide la connexion de données de la base de données. |
> | Action | Microsoft.Kusto/Clusters/Databases/delete | Supprime une ressource de base de données. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Supprime une ressource de connexions Event Hub. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Lit une ressource de connexions Event Hub. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Écrit une ressource de connexions Event Hub. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Valide la connexion de à Event Hub de la base de données. |
> | Action | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Répertorie les principaux de base de données. |
> | Action | Microsoft.Kusto/Clusters/Databases/PrincipalAssignments/delete | Supprime une ressource d'affectations de principal de base de données. |
> | Action | Microsoft.Kusto/Clusters/Databases/PrincipalAssignments/read | Lit une ressource d'affectations de principal de base de données. |
> | Action | Microsoft.Kusto/Clusters/Databases/PrincipalAssignments/write | Écrit une ressource d'affectations de principal de base de données. |
> | Action | Microsoft.Kusto/Clusters/Databases/read | Lit une ressource de base de données. |
> | Action | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Supprime les principaux de base de données. |
> | Action | Microsoft.Kusto/Clusters/Databases/write | Écrit une ressource de base de données. |
> | Action | Microsoft.Kusto/Clusters/DataConnections/delete | Supprime une ressource de connexions de données d’un cluster. |
> | Action | Microsoft.Kusto/Clusters/DataConnections/read | Lit une ressource de connexions de données d’un cluster. |
> | Action | Microsoft.Kusto/Clusters/DataConnections/write | Écrit une ressource de connexions de données d’un cluster. |
> | Action | Microsoft.Kusto/Clusters/Deactivate/action | Arrête le cluster. |
> | Action | Microsoft.Kusto/Clusters/delete | Supprime une ressource de cluster. |
> | Action | Microsoft.Kusto/Clusters/DetachFollowerDatabases/action | Détache les bases de données du suiveur. |
> | Action | Microsoft.Kusto/Clusters/DiagnoseVirtualNetwork/action | Diagnostique l'état de la connectivité réseau des ressources externes dont dépend le service. |
> | Action | Microsoft.Kusto/Clusters/ListFollowerDatabases/action | Liste les bases de données du suiveur. |
> | Action | Microsoft.Kusto/Clusters/PrincipalAssignments/delete | Supprime une ressource d'affectations de principal de cluster. |
> | Action | Microsoft.Kusto/Clusters/PrincipalAssignments/read | Lit une ressource d'affectations de principal de cluster. |
> | Action | Microsoft.Kusto/Clusters/PrincipalAssignments/write | Écrit une ressource d'affectations de principal de cluster. |
> | Action | Microsoft.Kusto/Clusters/read | Lit une ressource de cluster. |
> | Action | Microsoft.Kusto/Clusters/SKUs/read | Lit une ressource de référence SKU du cluster. |
> | Action | Microsoft.Kusto/Clusters/Start/action | Démarre le cluster. |
> | Action | Microsoft.Kusto/Clusters/Stop/action | Arrête le cluster. |
> | Action | Microsoft.Kusto/Clusters/write | Écrit une ressource de cluster. |
> | Action | Microsoft.Kusto/Locations/CheckNameAvailability/action | Vérifie la disponibilité du nom de ressource. |
> | Action | Microsoft.Kusto/Locations/GetNetworkPolicies/action | Obtient les stratégies d’intention de réseau |
> | Action | Microsoft.Kusto/locations/operationresults/read | Lit les ressources d’opérations |
> | Action | Microsoft.Kusto/Operations/read | Lit les ressources d’opérations |
> | Action | Microsoft.Kusto/register/action | Action d’inscription de l’abonnement |
> | Action | Microsoft.Kusto/Register/action | Inscrit l’abonnement sur le fournisseur de ressources Kusto. |
> | Action | Microsoft.Kusto/SKUs/read | Lit une ressource de référence SKU. |
> | Action | Microsoft.Kusto/Unregister/action | Désinscrit l’abonnement sur le fournisseur de ressources Kusto. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.LabServices/labAccounts/CreateLab/action | Crée un lab dans un compte lab. |
> | Action | Microsoft.LabServices/labAccounts/delete | Supprime des comptes de laboratoire. |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/delete | Supprimer des images de galerie |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/read | Lire des images de la galerie. |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/write | Ajouter ou modifier des images de la galerie. |
> | Action | Microsoft.LabServices/labAccounts/GetPricingAndAvailability/action | Permet d'obtenir le prix et la disponibilité de combinaisons de tailles, de zones géographiques et de systèmes d'exploitation pour le compte Lab. |
> | Action | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Obtenir des informations de disponibilité régionale pour chaque catégorie de taille configurée sous un compte Lab |
> | Action | Microsoft.LabServices/labAccounts/GetRestrictionsAndUsage/action | Obtient les informations principales sur les restrictions et l’utilisation de cet abonnement |
> | Action | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Ajouter des utilisateurs à un laboratoire |
> | Action | Microsoft.LabServices/labAccounts/labs/delete | Supprimer des laboratoires. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Supprime un paramètre d’environnement. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Supprimer des environnements. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Afficher les environnements. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Réinitialise le mot de passe utilisateur dans un environnement |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Lance un environnement en lançant toutes les ressources au sein de l’environnement. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Interrompt un environnement en interrompant toutes les ressources au sein de l’environnement |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Approvisionne/déprovisionne des ressources requises pour un paramètre d’environnement en fonction de l’état actuel du paramètre de laboratoire/d’environnement. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Lit le paramètre environnement. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | Réinitialise le mot de passe sur la machine virtuelle du modèle. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Enregistre l’image de modèle actuelle dans la galerie partagée du compte lab |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Supprimer des planifications. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Lire les planifications. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Ajouter ou modifier des planifications. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Lance un modèle en lançant toutes les ressources au sein du modèle. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Arrête un modèle en arrêtant toutes les ressources qu’il contient. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Ajoute ou modifie un paramètre d’environnement. |
> | Action | Microsoft.LabServices/labAccounts/labs/GetLabPricingAndAvailability/action | Permet d'obtenir le prix par unité Lab pour ce labo ainsi que la disponibilité, afin de déterminer si ce labo peut monter en puissance. |
> | Action | Microsoft.LabServices/labAccounts/labs/read | Afficher des laboratoires. |
> | Action | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Envoyer un e-mail avec un lien d’inscription au labo |
> | Action | Microsoft.LabServices/labAccounts/labs/users/delete | Supprime des utilisateurs. |
> | Action | Microsoft.LabServices/labAccounts/labs/users/read | Lit des utilisateurs. |
> | Action | Microsoft.LabServices/labAccounts/labs/users/write | Ajoute ou modifie des utilisateurs. |
> | Action | Microsoft.LabServices/labAccounts/labs/write | Ajouter ou modifier des laboratoires. |
> | Action | Microsoft.LabServices/labAccounts/read | Lit les comptes de laboratoire. |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Supprimer les galeries partagées. |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/read | Lire les galeries partagées. |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/write | Ajouter ou modifier des galeries partagées. |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/delete | Supprimer des images partagées. |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/read | Lire des images partagées. |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/write | Ajouter ou modifier des images partagées. |
> | Action | Microsoft.LabServices/labAccounts/write | Ajoute ou modifie les comptes de laboratoire. |
> | Action | Microsoft.LabServices/locations/operations/read | Opérations de lecture. |
> | Action | Microsoft.LabServices/register/action | Enregistrer l’abonnement. |
> | Action | Microsoft.LabServices/users/ListAllEnvironments/action | Répertorier tous les environnements de l’utilisateur |
> | Action | Microsoft.LabServices/users/Register/action | Enregistre un utilisateur dans un laboratoire géré |
> | Action | Microsoft.LabServices/users/ResetPassword/action | Réinitialise le mot de passe utilisateur dans un environnement |
> | Action | Microsoft.LabServices/users/StartEnvironment/action | Lance un environnement en lançant toutes les ressources au sein de l’environnement. |
> | Action | Microsoft.LabServices/users/StopEnvironment/action | Interrompt un environnement en interrompant toutes les ressources au sein de l’environnement |
> | Action | Microsoft.LabServices/users/UserSettings/action | Met à jour et retourne les paramètres utilisateur personnels. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Logic/integrationAccounts/agreements/delete | Supprime l’accord dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Obtient l’URL de rappel pour le contenu de l’accord dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/agreements/read | Lit l’accord dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/agreements/write | Crée ou met à jour l’accord dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/delete | Supprime l’assembly dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Obtient l’URL de rappel pour le contenu d’assembly dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/read | Lit l’assembly dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/write | Crée ou met à jour l’assembly dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Supprime la configuration du lot dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Lit la configuration du lot dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Crée ou met à jour la configuration du lot dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/certificates/delete | Supprime le certificat dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/certificates/read | Lit le certificat dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/certificates/write | Crée ou met à jour le certificat dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/delete | Supprime le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/groups/delete | Supprime le groupe du compte d'intégration. |
> | Action | Microsoft.Logic/integrationAccounts/groups/read | Lit le groupe sur le compte d'intégration. |
> | Action | Microsoft.Logic/integrationAccounts/groups/write | Crée ou met à jour le groupe sur le compte d'intégration. |
> | Action | Microsoft.Logic/integrationAccounts/join/action | Joint le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Obtient l’URL de rappel pour le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Obtient les clés dans le coffre de clés. |
> | Action | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Journalise les événements de suivi dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/maps/delete | Supprime le mappage dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Obtient l’URL de rappel pour le mappage de contenu dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/maps/read | Lit le mappage dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/maps/write | Crée ou met à jour le mappage dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/partners/delete | Supprime le partenaire dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Obtient l’URL de rappel pour le contenu partenaire dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/partners/read | Lit le partenaire dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/partners/write | Crée ou met à jour le partenaire dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Lit les définitions de journal du compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/read | Lit le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Régénère les secrets de clé d’accès. |
> | Action | Microsoft.Logic/integrationAccounts/rosettaNetProcessConfigurations/delete | Supprime la configuration de processus RosettaNet du compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/rosettaNetProcessConfigurations/read | Lit la configuration de processus RosettaNet du compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/rosettaNetProcessConfigurations/write | Crée ou met à jour la configuration de processus RosettaNet dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/schedules/delete | Supprime la planification dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/schedules/read | Lit la planification dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/schedules/write | Crée ou met à jour la planification dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/delete | Supprime le schéma dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Obtient l’URL de rappel pour le contenu de schéma dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/read | Lit le schéma dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/write | Crée ou met à jour le schéma dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/sessions/delete | Supprime la session dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/sessions/read | Lit la session sur le compte d'intégration. |
> | Action | Microsoft.Logic/integrationAccounts/sessions/write | Crée ou met à jour la session dans le compte d’intégration. |
> | Action | Microsoft.Logic/integrationAccounts/write | Crée ou met à jour le compte d’intégration. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/availableManagedApis/read | Lit les API managées disponibles dans l’environnement de service d’intégration. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/delete | Supprimer l'environnement de service d'intégration |
> | Action | Microsoft.Logic/integrationServiceEnvironments/join/action | Joindre l'environnement de service d'intégration |
> | Action | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Lit l’opération d’API managée de l’environnement de service d’intégration |
> | Action | Microsoft.Logic/integrationServiceEnvironments/managedApis/join/action | Joint l’API managée de l’environnement de service d’intégration. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/managedApis/operationStatuses/read | Lit les états d’opération des API managées de l’environnement de service d’intégration. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Lit l’API managée de l’environnement de service d’intégration |
> | Action | Microsoft.Logic/integrationServiceEnvironments/managedApis/write | Crée ou met à jour l’API managée de l’environnement de service d’intégration. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Lit les états des opérations de l’environnement de service d’intégration. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Lit les définitions de métrique de l’environnement de service d’intégration |
> | Action | Microsoft.Logic/integrationServiceEnvironments/read | Lire l'environnement de service d'intégration |
> | Action | Microsoft.Logic/integrationServiceEnvironments/write | Créer ou mettre à jour l’environnement de service d’intégration |
> | Action | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Obtient les groupes d’opération recommandés du workflow |
> | Action | Microsoft.Logic/locations/workflows/validate/action | Valide le workflow. |
> | Action | Microsoft.Logic/operations/read | Obtient l’opération. |
> | Action | Microsoft.Logic/register/action | Inscrit le fournisseur de ressources Microsoft.Logic pour un abonnement donné. |
> | Action | Microsoft.Logic/workflows/accessKeys/delete | Supprime la clé d’accès. |
> | Action | Microsoft.Logic/workflows/accessKeys/list/action | Répertorie les secrets de clé d’accès. |
> | Action | Microsoft.Logic/workflows/accessKeys/read | Lit la clé d’accès. |
> | Action | Microsoft.Logic/workflows/accessKeys/regenerate/action | Régénère les secrets de clé d’accès. |
> | Action | Microsoft.Logic/workflows/accessKeys/write | Crée ou met à jour la clé d’accès. |
> | Action | Microsoft.Logic/workflows/delete | Supprime le workflow. |
> | Action | Microsoft.Logic/workflows/detectors/read | Lit le détecteur de workflow. |
> | Action | Microsoft.Logic/workflows/disable/action | Désactive le workflow. |
> | Action | Microsoft.Logic/workflows/enable/action | Active le workflow. |
> | Action | Microsoft.Logic/workflows/listCallbackUrl/action | Obtient l’URL de rappel pour le workflow. |
> | Action | Microsoft.Logic/workflows/listSwagger/action | Obtient les définitions Swagger du workflow. |
> | Action | Microsoft.Logic/workflows/move/action | Déplace le workflow vers un ID d’abonnement, un groupe de ressources et/ou un nom différent. |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Lit les paramètres de diagnostic de flux de travail. |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic de flux de travail. |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Lit les définitions de journal de flux de travail. |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Lit les définitions de métrique de flux de travail. |
> | Action | Microsoft.Logic/workflows/read | Lit le workflow. |
> | Action | Microsoft.Logic/workflows/regenerateAccessKey/action | Régénère les secrets de clé d’accès. |
> | Action | Microsoft.Logic/workflows/run/action | Démarre l’exécution du workflow. |
> | Action | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Obtient les traces d’expression d’action d’exécution de flux de travail. |
> | Action | Microsoft.Logic/workflows/runs/actions/read | Lit l’action d’exécution d’un workflow. |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Obtient les traces d’expression de répétition d’action d’exécution de flux de travail. |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/read | Lit la répétition d’action d’exécution de flux de travail. |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Lire l’historique des demandes d’action de réexécution de flux de travail |
> | Action | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Lire l’historique des demandes d’action d’exécution de flux de travail |
> | Action | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Lit la répétition de l’étendue de l’action d’exécution de flux de travail. |
> | Action | Microsoft.Logic/workflows/runs/cancel/action | Annule l’exécution d’un workflow. |
> | Action | Microsoft.Logic/workflows/runs/delete | Supprime une exécution de workflow. |
> | Action | Microsoft.Logic/workflows/runs/operations/read | Lit l’état d’exécution d’un workflow. |
> | Action | Microsoft.Logic/workflows/runs/read | Lit l’exécution d’un workflow. |
> | Action | Microsoft.Logic/workflows/suspend/action | Suspend le flux de travail. |
> | Action | Microsoft.Logic/workflows/triggers/histories/read | Lit l’historique du déclencheur. |
> | Action | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Soumet à nouveau le déclencheur du workflow. |
> | Action | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Obtient l’URL de rappel pour le déclencheur. |
> | Action | Microsoft.Logic/workflows/triggers/read | Lit le déclencheur. |
> | Action | Microsoft.Logic/workflows/triggers/reset/action | Réinitialise le déclencheur. |
> | Action | Microsoft.Logic/workflows/triggers/run/action | Exécute le déclencheur. |
> | Action | Microsoft.Logic/workflows/triggers/setState/action | Définit l’état du déclencheur. |
> | Action | Microsoft.Logic/workflows/validate/action | Valide le workflow. |
> | Action | Microsoft.Logic/workflows/versions/read | Lit la version du workflow. |
> | Action | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Obtient l’URL de rappel pour le déclencheur. |
> | Action | Microsoft.Logic/workflows/write | Crée ou met à jour le workflow. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Déplacer une association de plans d’engagement Machine Learning |
> | Action | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Lire une association de plans d’engagement Machine Learning |
> | Action | Microsoft.MachineLearning/commitmentPlans/delete | Supprimer un plan d’engagement Machine Learning |
> | Action | Microsoft.MachineLearning/commitmentPlans/join/action | Rejoindre un plan d’engagement Machine Learning |
> | Action | Microsoft.MachineLearning/commitmentPlans/read | Lire un plan d’engagement Machine Learning |
> | Action | Microsoft.MachineLearning/commitmentPlans/write | Créer ou mettre à jour un plan d’engagement Machine Learning |
> | Action | Microsoft.MachineLearning/locations/operationresults/read | Obtient le résultat d’une opération de Machine Learning |
> | Action | Microsoft.MachineLearning/locations/operationsstatus/read | Obtient l’état d’une opération de Machine Learning en cours |
> | Action | Microsoft.MachineLearning/operations/read | Obtient les opérations de Machine Learning |
> | Action | Microsoft.MachineLearning/register/action | Enregistrer l’abonnement pour le fournisseur de ressources du service web Machine Learning et activer la création de services web |
> | Action | Microsoft.MachineLearning/skus/read | Obtient les références (SKU) de plan d’engagement de Machine Learning |
> | Action | Microsoft.MachineLearning/webServices/action | Créer des propriétés régionales de service web pour les régions prises en charge |
> | Action | Microsoft.MachineLearning/webServices/delete | Supprimer un service web Machine Learning |
> | Action | Microsoft.MachineLearning/webServices/listkeys/read | Obtient les clés pour un service web Machine Learning |
> | Action | Microsoft.MachineLearning/webServices/read | Afficher un service web Machine Learning |
> | Action | Microsoft.MachineLearning/webServices/write | Créer ou mettre à jour un service web Machine Learning |
> | Action | Microsoft.MachineLearning/Workspaces/delete | Supprimer un espace de travail Machine Learning |
> | Action | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Répertorier les clés d’un espace de travail Machine Learning |
> | Action | Microsoft.MachineLearning/Workspaces/read | Afficher un espace de travail Machine Learning |
> | Action | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Resynchroniser les clés d’un compte de stockage configuré pour un espace de travail Machine Learning |
> | Action | Microsoft.MachineLearning/Workspaces/write | Créer ou mettre à jour un espace de travail Machine Learning |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Obtient l’état d’une opération de calcul particulière |
> | Action | Microsoft.MachineLearningServices/locations/quotas/read | Obtient les quotas d’espace de travail actuellement attribués en fonction de VMFamily. |
> | Action | Microsoft.MachineLearningServices/locations/updateQuotas/action | Met à jour le quota pour chaque famille de machines virtuelles dans l’espace de travail. |
> | Action | Microsoft.MachineLearningServices/locations/usages/read | Rapport d’utilisation pour les ressources de calcul AML dans un abonnement |
> | Action | Microsoft.MachineLearningServices/locations/vmsizes/read | Obtenir les tailles de machine virtuelle prises en charge |
> | Action | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Obtient l’état d’une opération d’espace de travail particulière |
> | Action | Microsoft.MachineLearningServices/register/action | Enregistre l’abonnement pour le fournisseur de ressources Machine Learning Services |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/delete | Supprime les ressources de calcul dans le ou les espaces de travail Machine Learning Services |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Répertorie les secrets des ressources de calcul dans l’espace de travail Machine Learning Services |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Lister les nœuds de la ressource de calcul dans l’espace de travail Machine Learning Services |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/read | Obtient les ressources de calcul dans le ou les espaces de travail Machine Learning Services |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/restart/action | Redémarre la ressource de calcul dans l’espace de travail Machine Learning Services |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/start/action | Démarre la ressource de calcul dans l’espace de travail Machine Learning Services |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/stop/action | Arrête la ressource de calcul dans l’espace de travail Machine Learning Services |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/write | Crée ou met à jour les ressources de calcul dans le ou les espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datadriftdetectors/read | Obtient des détecteurs de dérive de données dans les espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datadriftdetectors/write | Crée ou met à jour des détecteurs de dérive de données dans les espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datasets/registered/delete | Supprime des jeux de données inscrits dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read | Obtient l’aperçu du jeu de données pour les jeux de données inscrits dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read | Obtient les profils de jeu de données pour les jeux de données inscrits dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/write | Crée ou met à jour les profils des jeux de données inscrits dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datasets/registered/read | Obtient les jeux de données inscrits dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read | Permet d'obtenir le schéma des jeux de données inscrits dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datasets/registered/write | Crée ou met à jour des jeux de données inscrits dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete | Supprime des jeux de données non inscrits dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read | Obtient l’aperçu du jeu de données pour les jeux de données non inscrits dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read | Obtient les profils de jeu de données pour les jeux de données non inscrits dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/write | Crée ou met à jour les profils des jeux de données non inscrits dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/read | Obtient les jeux de données non inscrits dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read | Permet d'obtenir le schéma des jeux de données non inscrits dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write | Crée ou met à jour des jeux de données non inscrits dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datastores/delete | Supprime des magasins de données dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datastores/read | Obtient les magasins de données dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datastores/write | Crée ou met à jour des magasins de données dans les espaces de travail Machine Learning Services |
> | Action | Microsoft.MachineLearningServices/workspaces/delete | Supprime le ou les espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read | Obtient les pipelines publiés et les points de terminaison de pipeline dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write | Crée ou met à jour des pipelines publiés et des points de terminaison de pipeline dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/environments/build/action | Génère des environnements dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/environments/read | Obtient des environnements dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/environments/readSecrets/action | Obtient des environnements avec des secrets dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/environments/write | Crée ou met à jour des environnements dans des espaces de travail Machine Learning Services |
> | Action | Microsoft.MachineLearningServices/workspaces/eventGridFilters/read | Obtient un filtre Event Grid pour un espace de travail particulier |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/delete | Supprime des expériences dans un ou plusieurs espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/read | Obtient des expériences dans les espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/runs/read | Obtient les exécutions dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/runs/scriptRun/submit/action | Crée ou met à jour les exécutions de script dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/runs/write | Crée ou met à jour des exécutions dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/write | Crée ou met à jour des expériences dans les espaces de travail Machine Learning Services |
> | Action | Microsoft.MachineLearningServices/workspaces/features/read | Obtient toutes les fonctionnalités activées pour un espace de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/labeling/export/action | Exporte des étiquettes de projets d’étiquetage dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/labeling/labels/read | Obtient des étiquettes de projets d’étiquetage dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/labeling/labels/reject/action | Refuse des étiquettes de projets d’étiquetage dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/labeling/labels/write | Crée des étiquettes de projets d’étiquetage dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/labeling/projects/delete | Supprime un projet d’étiquetage dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/labeling/projects/read | Obtient un projet d’étiquetage dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read | Obtient un résumé de projet d’étiquetage dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/labeling/projects/write | Crée ou met à jour un projet d’étiquetage dans des espaces de travail Machine Learning Services |
> | Action | Microsoft.MachineLearningServices/workspaces/listKeys/action | Répertorie les secrets d’un espace de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/metadata/artifacts/delete | Supprime des artefacts dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read | Obtient les artefacts dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write | Crée ou met à jour des artefacts dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/metadata/secrets/delete | Supprime les secrets dans les espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/metadata/secrets/read | Permet d'obtenir les secrets des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/metadata/secrets/write | Crée ou met à jour des secrets dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/metadata/snapshots/delete | Supprime des instantanés dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read | Obtient des instantanés dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write | Crée ou met à jour des instantanés dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/models/delete | Supprime des modèles dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/models/package/action | Met en package des modèles dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/models/read | Obtient des modèles dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/models/write | Crée ou met à jour des modèles dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/modules/read | Obtient des modules dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/modules/write | Crée ou met à jour des modules dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/pipelinedrafts/delete | Supprime des brouillons de pipeline dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/pipelinedrafts/read | Obtient des brouillons de pipeline dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write | Crée ou met à jour des brouillons de pipeline dans des espaces de travail Machine Learning Services |
> | Action | Microsoft.MachineLearningServices/workspaces/privateEndpointConnectionProxies/delete | Supprime un proxy de connexion à une ressource Private Endpoint du fournisseur Microsoft.Network |
> | Action | Microsoft.MachineLearningServices/workspaces/privateEndpointConnectionProxies/read | Affiche l'état d'un proxy de connexion à une ressource Private Endpoint du fournisseur Microsoft.Network |
> | Action | Microsoft.MachineLearningServices/workspaces/privateEndpointConnectionProxies/validate/action | Valide un proxy de connexion à une ressource Private Endpoint du fournisseur Microsoft.Network |
> | Action | Microsoft.MachineLearningServices/workspaces/privateEndpointConnectionProxies/write | Modifie l'état d'un proxy de connexion à une ressource Private Endpoint du fournisseur Microsoft.Network |
> | Action | Microsoft.MachineLearningServices/workspaces/privateEndpointConnections/delete | Supprime une connexion à une ressource Private Endpoint du fournisseur Microsoft.Network |
> | Action | Microsoft.MachineLearningServices/workspaces/privateEndpointConnections/read | Affiche l'état d'une connexion à une ressource Private Endpoint du fournisseur Microsoft.Network |
> | Action | Microsoft.MachineLearningServices/workspaces/privateEndpointConnections/write | Change l'état d'une connexion à une ressource Private Endpoint du fournisseur Microsoft.Network |
> | Action | Microsoft.MachineLearningServices/workspaces/PrivateEndpointConnectionsApproval/action | Approuve ou rejette une connexion à une ressource Private Endpoint du fournisseur Microsoft.Network |
> | Action | Microsoft.MachineLearningServices/workspaces/privateLinkResources/read | Permet d'obtenir les ressources de lien privé disponibles pour l'instance spécifiée des espaces de travail Machine Learning Services |
> | Action | Microsoft.MachineLearningServices/workspaces/read | Obtient le ou les espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aci/delete | Supprime des services ACI dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aci/listkeys/action | Répertorie les clés des services ACI dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aci/write | Crée ou met à jour des services ACI dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/delete | Supprime les services AKS devtest dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/listkeys/action | Répertorie les clés des services AKS devtest dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/score/action | Attribue un score aux services AKS devtest dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/write | Crée ou met à jour des services AKS devtest dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/delete | Supprime les services AKS prod dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/listkeys/action | Répertorie les clés des services AKS prod dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/score/action | Attribue un score aux services AKS prod dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/write | Crée ou met à jour des services AKS prod dans des espaces de travail Machine Learning Services |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/read | Obtient des services dans des espaces de travail Machine Learning Services |
> | Action | Microsoft.MachineLearningServices/workspaces/write | Crée ou met à jour le ou les espaces de travail Machine Learning Services |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.ManagedIdentity/identities/read | Obtient l’identité assignée d’un système existant |
> | Action | Microsoft.ManagedIdentity/operations/read | Répertorie les opérations disponibles sur le fournisseur de ressources Microsoft.ManagedIdentity |
> | Action | Microsoft.ManagedIdentity/register/action | Inscrit l’abonnement pour le fournisseur de ressources d’identité managée |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Action RBAC pour l’affectation de l’identité assignée d’un utilisateur existant à une ressource |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Supprime l’identité assignée d’un utilisateur existant |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/read | Obtient l’identité assignée d’un utilisateur existant |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/write | Crée une identité assignée d’utilisateur ou met à jour les balises associées à l’identité assignée d’un utilisateur existant |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.ManagedServices/marketplaceRegistrationDefinitions/read | Récupère la liste des définitions d'inscription des services managés. |
> | Action | Microsoft.ManagedServices/operations/read | Récupère la liste des opérations des services managés. |
> | Action | Microsoft.ManagedServices/operationStatuses/read | Lit l’état de l’opération pour la ressource. |
> | Action | Microsoft.ManagedServices/register/action | Enregistre dans les services managés. |
> | Action | Microsoft.ManagedServices/registrationAssignments/delete | Supprime l'affectation d'inscription des services managés. |
> | Action | Microsoft.ManagedServices/registrationAssignments/read | Récupère la liste des affectations d'inscription des services managés. |
> | Action | Microsoft.ManagedServices/registrationAssignments/write | Ajoute ou modifie l'affectation d'inscription des services managés. |
> | Action | Microsoft.ManagedServices/registrationDefinitions/delete | Supprime la définition d'inscription des services managés. |
> | Action | Microsoft.ManagedServices/registrationDefinitions/read | Récupère la liste des définitions d'inscription des services managés. |
> | Action | Microsoft.ManagedServices/registrationDefinitions/write | Ajoute ou modifie la définition d'inscription des services managés. |
> | Action | Microsoft.ManagedServices/unregister/action | Annule l’inscription des services managés. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Management/checkNameAvailability/action | Vérifie si le nom de groupe d’administration spécifié est valide et unique. |
> | Action | Microsoft.Management/getEntities/action | Affiche toutes les entités (groupes d’administration, abonnements, etc.) pour l’utilisateur authentifié. |
> | Action | Microsoft.Management/managementGroups/delete | Supprime un groupe d’administration. |
> | Action | Microsoft.Management/managementGroups/descendants/read | Obtient tous les descendants (groupes d’administration, abonnements) d’un groupe d’administration. |
> | Action | Microsoft.Management/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | Action | Microsoft.Management/managementGroups/settings/delete | Supprime les paramètres de hiérarchie de groupe d’administration. |
> | Action | Microsoft.Management/managementGroups/settings/read | Répertorie les paramètres de hiérarchie de groupe d’administration existant. |
> | Action | Microsoft.Management/managementGroups/settings/write | Crée ou met à jour les paramètres de hiérarchie de groupe d’administration. |
> | Action | Microsoft.Management/managementGroups/subscriptions/delete | Dissocie un abonnement du groupe d’administration. |
> | Action | Microsoft.Management/managementGroups/subscriptions/write | Associe un abonnement existant au groupe d’administration. |
> | Action | Microsoft.Management/managementGroups/write | Crée ou met à jour un groupe d’administration. |
> | Action | Microsoft.Management/register/action | Enregistre l’abonnement spécifié avec Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Accorde à un compte Maps l’accès en lecture aux données. |
> | Action | Microsoft.Maps/accounts/delete | Supprime un compte Maps. |
> | Action | Microsoft.Maps/accounts/eventGridFilters/delete | Supprime un filtre Event Grid |
> | Action | Microsoft.Maps/accounts/eventGridFilters/read | Obtient un filtre Event Grid |
> | Action | Microsoft.Maps/accounts/eventGridFilters/write | Crée ou met à jour un filtre Event Grid |
> | Action | Microsoft.Maps/accounts/listKeys/action | Répertorie les clés de compte Maps |
> | Action | Microsoft.Maps/accounts/read | Obtient un compte Maps. |
> | Action | Microsoft.Maps/accounts/regenerateKey/action | Génère une clé primaire ou secondaire de compte Maps |
> | Action | Microsoft.Maps/accounts/write | Crée ou met à jour un compte Maps. |
> | Action | Microsoft.Maps/operations/read | Lire les opérations du fournisseur |
> | Action | Microsoft.Maps/register/action | Inscrit le fournisseur |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Renvoie un accord. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Accepte un accord signé. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importe une image vers l’ACR de l’utilisateur final. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Retourne une configuration. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Enregistre une configuration. |
> | Action | Microsoft.Marketplace/register/action | Inscrit le fournisseur de ressources Microsoft.Marketplace dans l’abonnement. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/delete | Effectue une opération DELETE sur une ressource de service de développement classique. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Obtient les clés de gestion des ressources d’un service de développement classique. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Obtient l’URL d’authentification unique pour un service de développement classique. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/read | Effectue une opération GET sur un service de développement classique. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Génère les clés de gestion des ressources d’un service de développement classique. |
> | Action | Microsoft.MarketplaceApps/Operations/read | Lit les opérations pour tous les types de ressources. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Annuler un contrat pour un élément de place de marché donné |
> | Action | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Renvoyer un contrat pour un élément de place de marché donné |
> | Action | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Signer un contrat pour un élément de place de marché donné |
> | Action | Microsoft.MarketplaceOrdering/agreements/read | Retourne tous les accords sous un abonnement spécifié |
> | Action | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Obtient un accord pour un élément spécifié de machine virtuelle de la place de marché |
> | Action | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Signe ou annule un accord pour un élément spécifié de machine virtuelle de la place de marché |
> | Action | Microsoft.MarketplaceOrdering/operations/read | Répertorie toutes les opérations possibles dans l’API |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Media/checknameavailability/action | Vérifie si un nom de compte Media Services est disponible |
> | Action | Microsoft.Media/mediaservices/accountfilters/delete | Supprimer tous les filtres de compte |
> | Action | Microsoft.Media/mediaservices/accountfilters/read | Lire tous les filtres de compte |
> | Action | Microsoft.Media/mediaservices/accountfilters/write | Créer ou mettre à jour tous les filtres de compte |
> | Action | Microsoft.Media/mediaservices/assets/assetfilters/delete | Supprimer tous les filtres d’actif multimédia |
> | Action | Microsoft.Media/mediaservices/assets/assetfilters/read | Lire tous les filtres d’actif multimédia |
> | Action | Microsoft.Media/mediaservices/assets/assetfilters/write | Créer ou mettre à jour tous les filtres d’actif multimédia |
> | Action | Microsoft.Media/mediaservices/assets/delete | Supprime toutes les ressources |
> | Action | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Obtient la clé de chiffrement de la ressource |
> | Action | Microsoft.Media/mediaservices/assets/listContainerSas/action | Répertorie les URL SAS de conteneur de ressources |
> | Action | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | Lister les localisateurs de streaming pour l’actif multimédia |
> | Action | Microsoft.Media/mediaservices/assets/read | Affiche toutes les ressources |
> | Action | Microsoft.Media/mediaservices/assets/write | Crée ou met à jour toutes les ressources |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Supprime toutes les stratégies de clé de contenu |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Obtient les propriétés de stratégie avec les secrets |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/read | Affiche toutes les stratégies de clé de contenu |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/write | Crée ou met à jour toutes les stratégies de clé de contenu |
> | Action | Microsoft.Media/mediaservices/delete | Supprime un compte Media Services |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/delete | Supprime tous les filtres Event Grid |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/read | Affiche tous les filtres Event Grid |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/write | Crée ou met à jour tous les filtres Event Grid |
> | Action | Microsoft.Media/mediaservices/liveEventOperations/read | Affiche toutes les opérations d’événement en direct |
> | Action | Microsoft.Media/mediaservices/liveEvents/delete | Supprime tous les événements en direct |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Supprime toutes les sorties en direct |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Affiche toutes les sorties en direct |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Crée ou met à jour toutes les sorties en direct |
> | Action | Microsoft.Media/mediaservices/liveEvents/read | Affiche tous les événements en direct |
> | Action | Microsoft.Media/mediaservices/liveEvents/reset/action | Réinitialise toutes les opérations d’événement en direct |
> | Action | Microsoft.Media/mediaservices/liveEvents/start/action | Lance toutes les opérations d’événement en direct |
> | Action | Microsoft.Media/mediaservices/liveEvents/stop/action | Interrompt toutes les opérations d’événement en direct |
> | Action | Microsoft.Media/mediaservices/liveEvents/write | Crée ou met à jour tous les événements en direct |
> | Action | Microsoft.Media/mediaservices/liveOutputOperations/read | Affiche toutes les opérations de sortie en direct |
> | Action | Microsoft.Media/mediaservices/read | Lit un compte Media Services |
> | Action | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Affiche toutes les opérations de point de terminaison de streaming |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/delete | Supprime tous les points de terminaison de streaming |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/read | Affiche tous les points de terminaison de streaming |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Met à l’échelle toutes les opérations de point de terminaison de streaming |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Lance toutes les opérations de point de terminaison de streaming |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Interrompt toutes les opérations de point de terminaison de streaming |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/write | Crée ou met à jour tous les points de terminaison de streaming |
> | Action | Microsoft.Media/mediaservices/streamingLocators/delete | Supprime tous les localisateurs de streaming |
> | Action | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Répertorie les clés de contenu |
> | Action | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Répertorie les chemins d’accès |
> | Action | Microsoft.Media/mediaservices/streamingLocators/read | Affiche tous les localisateurs de streaming |
> | Action | Microsoft.Media/mediaservices/streamingLocators/write | Crée ou met à jour tous les localisateurs de streaming |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/delete | Supprime toutes les stratégies de streaming |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/read | Affiche toutes les stratégies de streaming |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/write | Crée ou met à jour toutes les stratégies de streaming |
> | Action | Microsoft.Media/mediaservices/syncStorageKeys/action | Synchronise les clés de stockage pour un compte de stockage Azure attaché |
> | Action | Microsoft.Media/mediaservices/transforms/delete | Supprime toutes les transformations |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Annuler un travail. |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/delete | Supprime tous les travaux |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/read | Affiche tous les travaux |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/write | Crée ou met à jour tous les travaux |
> | Action | Microsoft.Media/mediaservices/transforms/read | Affiche toutes les transformations |
> | Action | Microsoft.Media/mediaservices/transforms/write | Crée ou met à jour toutes les transformations |
> | Action | Microsoft.Media/mediaservices/write | Crée ou met à jour un compte Media Services |
> | Action | Microsoft.Media/operations/read | Obtenir les opérations disponibles. |
> | Action | Microsoft.Media/register/action | Inscrit l’abonnement pour le fournisseur de ressources Media Services et active la création de comptes Media Services |
> | Action | Microsoft.Media/unregister/action | Annule l’enregistrement de l’abonnement pour le fournisseur de ressources Media Services |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Migrate/assessmentprojects/assessmentOptions/read | Obtient les options d’évaluation qui sont disponibles à l’emplacement donné. |
> | Action | Microsoft.Migrate/assessmentprojects/assessments/read | Répertorier les évaluations au sein d’un projet |
> | Action | Microsoft.Migrate/assessmentprojects/delete | Supprime le projet d’évaluation |
> | Action | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | Obtenir les propriétés d’une machine évaluée. |
> | Action | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | Supprime une évaluation. |
> | Action | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | Télécharger l'URL d’un rapport d’évaluation |
> | Action | Microsoft.Migrate/assessmentprojects/groups/assessments/read | Obtenir les propriétés d'une évaluation |
> | Action | Microsoft.Migrate/assessmentprojects/groups/assessments/write | Crée une évaluation ou met à jour une évaluation existante. |
> | Action | Microsoft.Migrate/assessmentprojects/groups/delete | Supprime un groupe. |
> | Action | Microsoft.Migrate/assessmentprojects/groups/read | Obtenir les propriétés d’un groupe. |
> | Action | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | Met à jour un groupe en ajoutant ou supprimant des machines |
> | Action | Microsoft.Migrate/assessmentprojects/groups/write | Crée un groupe ou met à jour un groupe existant. |
> | Action | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | Supprime le collecteur HyperV |
> | Action | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | Obtient les propriétés du collecteur HyperV |
> | Action | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | Crée un nouveau collecteur HyperV ou met à jour un collecteur HyperV existant |
> | Action | Microsoft.Migrate/assessmentprojects/importcollectors/delete | Supprime le collecteur d’importation |
> | Action | Microsoft.Migrate/assessmentprojects/importcollectors/read | Obtient les propriétés du collecteur d’importation |
> | Action | Microsoft.Migrate/assessmentprojects/importcollectors/write | Crée ou met à jour un collecteur d’importation |
> | Action | Microsoft.Migrate/assessmentprojects/machines/read | Obtient les propriétés d’une machine. |
> | Action | Microsoft.Migrate/assessmentprojects/read | Obtenir les propriétés d'un projet d’évaluation |
> | Action | Microsoft.Migrate/assessmentprojects/servercollectors/read | Obtient les propriétés du collecteur de serveurs |
> | Action | Microsoft.Migrate/assessmentprojects/servercollectors/write | Crée ou met à jour un collecteur de serveurs |
> | Action | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | Supprime le collecteur VMware |
> | Action | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | Obtient les propriétés du collecteur VMware |
> | Action | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | Crée un nouveau collecteur VMware ou met à jour un collecteur VMware existant |
> | Action | Microsoft.Migrate/assessmentprojects/write | Crée un projet d’évaluation ou met à jour un projet d’évaluation existant |
> | Action | Microsoft.Migrate/locations/assessmentOptions/read | Obtient les options d’évaluation qui sont disponibles à l’emplacement donné. |
> | Action | Microsoft.Migrate/locations/checknameavailability/action | Vérifie la disponibilité du nom de ressource de l’abonnement donné dans l’emplacement spécifié. |
> | Action | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Obtient les propriétés d’une instance de base de données |
> | Action | Microsoft.Migrate/migrateprojects/Databases/read | Obtient les propriétés d’une base de données |
> | Action | Microsoft.Migrate/migrateprojects/delete | Supprime un projet de migration |
> | Action | Microsoft.Migrate/migrateprojects/machines/read | Obtient les propriétés d’une machine. |
> | Action | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | Supprime un événement de migration |
> | Action | Microsoft.Migrate/migrateprojects/MigrateEvents/read | Obtient les propriétés d’un événement de migration. |
> | Action | Microsoft.Migrate/migrateprojects/read | Obtient les propriétés d’un projet de migration |
> | Action | Microsoft.Migrate/migrateprojects/RefreshSummary/action | Actualise le résumé du projet de migration |
> | Action | Microsoft.Migrate/migrateprojects/registerTool/action | Inscrit l’outil dans un projet de migration |
> | Action | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | Nettoie les données de solution de projet de migration |
> | Action | Microsoft.Migrate/migrateprojects/solutions/Delete | Supprime une solution de projet de migration |
> | Action | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Obtient la configuration de la solution de projet de migration |
> | Action | Microsoft.Migrate/migrateprojects/solutions/read | Obtient les propriétés de la solution de projet de migration |
> | Action | Microsoft.Migrate/migrateprojects/solutions/write | Crée une nouvelle solution de projet de migration ou met à jour une solution de projet de migration existante |
> | Action | Microsoft.Migrate/migrateprojects/write | Crée un projet de migration ou met à jour un projet de migration existant |
> | Action | Microsoft.Migrate/Operations/read | Répertorie les opérations disponibles sur le fournisseur de ressources Microsoft.Migrate. |
> | Action | Microsoft.Migrate/projects/assessments/read | Répertorier les évaluations au sein d’un projet |
> | Action | Microsoft.Migrate/projects/delete | Supprime le projet. |
> | Action | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Obtenir les propriétés d’une machine évaluée. |
> | Action | Microsoft.Migrate/projects/groups/assessments/delete | Supprime une évaluation. |
> | Action | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Télécharger l'URL d’un rapport d’évaluation |
> | Action | Microsoft.Migrate/projects/groups/assessments/read | Obtenir les propriétés d'une évaluation |
> | Action | Microsoft.Migrate/projects/groups/assessments/write | Crée une évaluation ou met à jour une évaluation existante. |
> | Action | Microsoft.Migrate/projects/groups/delete | Supprime un groupe. |
> | Action | Microsoft.Migrate/projects/groups/read | Obtenir les propriétés d’un groupe. |
> | Action | Microsoft.Migrate/projects/groups/write | Crée un groupe ou met à jour un groupe existant. |
> | Action | Microsoft.Migrate/projects/keys/action | Obtient les clés partagées du projet. |
> | Action | Microsoft.Migrate/projects/machines/read | Obtient les propriétés d’une machine. |
> | Action | Microsoft.Migrate/projects/read | Obtient les propriétés d’un projet. |
> | Action | Microsoft.Migrate/projects/write | Crée un projet ou met à jour un projet existant. |
> | Action | Microsoft.Migrate/register/action | Inscrit un abonnement auprès du fournisseur de ressources Microsoft.Migrate. |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.MixedReality/register/action | Inscrit un abonnement pour le fournisseur de ressources Mixed Reality. |
> | DataAction | Microsoft.MixedReality/RemoteRenderingAccounts/convert/action | Lance une conversion de ressources |
> | DataAction | Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete | Met fin à une conversion de ressources |
> | DataAction | Microsoft.MixedReality/RemoteRenderingAccounts/convert/read | Permet d'obtenir les propriétés de la conversion de ressources |
> | DataAction | Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read | Permet de se connecter à l'inspecteur Remote Rendering |
> | DataAction | Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action | Lance des sessions |
> | DataAction | Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete | Met fin à des sessions |
> | DataAction | Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read | Définit les propriétés de session |
> | Action | Microsoft.MixedReality/remoteRenderingAccounts/providers/Microsoft.Insights/metricDefinitions/read | Obtient les métriques disponibles pour Microsoft.MixedReality/remoteRenderingAccounts |
> | DataAction | Microsoft.MixedReality/RemoteRenderingAccounts/render/read | Permet de se connecter à une session |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Créer des ancres spatiales |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Supprimer des ancres spatiales |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Détecter des ancres spatiales proches |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obtenir les propriétés d’ancres spatiales |
> | Action | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Obtient le paramètre de diagnostic pour Microsoft.MixedReality/spatialAnchorsAccounts |
> | Action | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour Microsoft.MixedReality/spatialAnchorsAccounts |
> | Action | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/metricDefinitions/read | Obtient les métriques disponibles pour Microsoft.MixedReality/spatialAnchorsAccounts |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localiser des ancres spatiales |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Envoyer des données de diagnostic pour aider à améliorer la qualité du service Azure Spatial Anchors |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Mettre à jour les propriétés d’ancres spatiales |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.NetApp/locations/checkfilepathavailability/action | Vérifier si le chemin d’accès au fichier est disponible |
> | Action | Microsoft.NetApp/locations/checknameavailability/action | Vérifier si le nom de la ressource est disponible |
> | Action | Microsoft.NetApp/locations/operationresults/read | Lire une ressource de résultat d’opération |
> | Action | Microsoft.NetApp/locations/read | Lire une ressource de vérification de disponibilité. |
> | Action | Microsoft.NetApp/netAppAccounts/accountBackups/delete | Supprime une ressource de sauvegarde de compte. |
> | Action | Microsoft.NetApp/netAppAccounts/accountBackups/read | Lit une ressource de sauvegarde de compte. |
> | Action | Microsoft.NetApp/netAppAccounts/accountBackups/write | Écrit une ressource de sauvegarde de compte. |
> | Action | Microsoft.NetApp/netAppAccounts/backupPolicies/delete | Supprime une ressource de stratégie de sauvegarde. |
> | Action | Microsoft.NetApp/netAppAccounts/backupPolicies/read | Lit une ressource de stratégie de sauvegarde. |
> | Action | Microsoft.NetApp/netAppAccounts/backupPolicies/write | Écrit une ressource de stratégie de sauvegarde. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Supprimer une ressource de pool |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/read | Lire une ressource de pool |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/AuthorizeReplication/action | Autorise la réplication du volume source |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/delete | Supprime une ressource de sauvegarde. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/read | Lit une ressource de sauvegarde. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/write | Écrit une ressource de sauvegarde. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/break/action | Arrête les relations de réplication des volumes |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Supprimer une ressource de volume |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/DeleteReplication/action | Supprime la réplication sur le volume de destination |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Lire une ressource de volume |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/ReplicationStatus/action | Lit les états de la réplication de volume. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/ResyncReplication/action | Resynchronise la réplication sur le volume de destination |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/revert/action | Restaure le volume à une capture instantanée spécifique |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Supprimer une ressource de capture instantanée |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Lire une ressource de capture instantanée |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Écrire une ressource de capture instantanée |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Écrire une ressource de volume |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/write | Écrire une ressource de pool |
> | Action | Microsoft.NetApp/netAppAccounts/delete | Supprime une ressource de compte. |
> | Action | Microsoft.NetApp/netAppAccounts/read | Lire une ressource de compte |
> | Action | Microsoft.NetApp/netAppAccounts/snapshotPolicies/delete | Supprime une ressource de stratégie de capture instantanée. |
> | Action | Microsoft.NetApp/netAppAccounts/snapshotPolicies/ListVolumes/action | Répertorie les volumes connectés à une stratégie de capture instantanée |
> | Action | Microsoft.NetApp/netAppAccounts/snapshotPolicies/read | Lit une ressource de stratégie de capture instantanée. |
> | Action | Microsoft.NetApp/netAppAccounts/snapshotPolicies/write | Écrit une ressource de stratégie de capture instantanée. |
> | Action | Microsoft.NetApp/netAppAccounts/vaults/read | Lit une ressource de coffre. |
> | Action | Microsoft.NetApp/netAppAccounts/write | Écrire une ressource de compte |
> | Action | Microsoft.NetApp/Operations/read | Lire une ressource d’opération |
> | Action | Microsoft.NetApp/register/action | Action d’inscription de l’abonnement |
> | Action | Microsoft.NetApp/unregister/action | Désinscrit un abonnement auprès du fournisseur de ressources Microsoft.NetApp |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Obtenir les en-têtes de requête disponibles pour Application Gateway |
> | Action | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Obtenir l’en-tête de réponse disponible pour Application Gateway |
> | Action | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Obtenir les variables de serveur disponibles pour Application Gateway |
> | Action | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Stratégie SSL prédéfinie d’Application Gateway |
> | Action | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Options SSL disponibles d’Application Gateway |
> | Action | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Obtient les ensembles de règles WAF disponibles pour Application Gateway. |
> | Action | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Joint un pool d’adresses principales de passerelle d’application. Impossible à alerter. |
> | Action | Microsoft.Network/applicationGateways/backendhealth/action | Obtient l’intégrité du serveur principal d’une passerelle d’application. |
> | Action | Microsoft.Network/applicationGateways/delete | Supprime une passerelle d’application. |
> | Action | Microsoft.Network/applicationGateways/getBackendHealthOnDemand/action | Obtient l’intégrité du serveur principal d’une passerelle d’application à la demande pour le pool principal et la configuration http donnés |
> | Action | Microsoft.Network/applicationGateways/read | Obtient une passerelle d’application. |
> | Action | Microsoft.Network/applicationGateways/start/action | Démarre une passerelle d’application. |
> | Action | Microsoft.Network/applicationGateways/stop/action | Arrête une passerelle d’application. |
> | Action | Microsoft.Network/applicationGateways/write | Crée une passerelle d’application ou met à jour une passerelle d’application. |
> | Action | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Supprime une stratégie WAF d’Application Gateway |
> | Action | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Obtient une stratégie WAF d’Application Gateway |
> | Action | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Crée une stratégie WAF de passerelle d’application ou met à jour une stratégie WAF de passerelle d’application |
> | Action | Microsoft.Network/applicationSecurityGroups/delete | Supprime un groupe de sécurité d’application |
> | Action | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Joint une configuration IP aux groupes de sécurité d’application. Impossible à alerter. |
> | Action | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Joint une règle de sécurité aux groupes de sécurité d’application. Impossible à alerter. |
> | Action | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Liste les configurations d’adresses IP dans le groupe de sécurité d’application (ApplicationSecurityGroup) |
> | Action | Microsoft.Network/applicationSecurityGroups/read | Obtient un ID de groupe de sécurité d’application. |
> | Action | Microsoft.Network/applicationSecurityGroups/write | Crée un groupe de sécurité d’application ou met à jour un groupe de sécurité d’application existant. |
> | Action | Microsoft.Network/azureFirewallFqdnTags/read | Obtient les balises FQDN du Pare-feu Azure |
> | Action | Microsoft.Network/azurefirewalls/delete | Supprimer le Pare-feu Azure |
> | Action | Microsoft.Network/azurefirewalls/read | Obtenir le Pare-feu Azure |
> | Action | Microsoft.Network/azurefirewalls/write | Créer ou mettre à jour un Pare-feu Azure |
> | Action | Microsoft.Network/bastionHosts/createShareableLinks/action | Crée des URL partageables pour les machines virtuelles sous un hôte bastion et retourne les URL |
> | Action | Microsoft.Network/bastionHosts/delete | Supprime un hôte Bastion |
> | Action | Microsoft.Network/bastionHosts/deleteShareableLinks/action | Supprime les URL partageables pour les machines virtuelles fournies sous un hôte bastion |
> | Action | Microsoft.Network/bastionHosts/disconnectactivesessions/action | Déconnecte les sessions actives données dans l’hôte bastion |
> | Action | Microsoft.Network/bastionHosts/getactivesessions/action | Obtient les sessions actives sur l’hôte bastion |
> | Action | Microsoft.Network/bastionHosts/getShareableLinks/action | Retourne les URL partageables pour les machines virtuelles spécifiées dans un sous-réseau Bastion, à condition que leurs URL soient créées |
> | Action | Microsoft.Network/bastionHosts/read | Obtient un hôte Bastion |
> | Action | Microsoft.Network/bastionHosts/write | Crée ou met à jour un hôte Bastion |
> | Action | Microsoft.Network/bgpServiceCommunities/read | Obtenir les communautés de services BGP. |
> | Action | Microsoft.Network/checkFrontDoorNameAvailability/action | Vérifie si un nom de la porte d’entrée est disponible |
> | Action | Microsoft.Network/checkTrafficManagerNameAvailability/action | Vérifier la disponibilité d’un nom DNS relatif dans Traffic Manager. |
> | Action | Microsoft.Network/connections/delete | Supprime une VirtualNetworkGatewayConnection. |
> | Action | Microsoft.Network/connections/read | Obtient une VirtualNetworkGatewayConnection. |
> | Action | Microsoft.Network/connections/revoke/action | Marque l’état d’une connexion Express Route comme Révoqué |
> | Action | Microsoft.Network/connections/sharedkey/action | Obtient une clé partagée de connexion de passerelle de réseau virtuel |
> | Action | Microsoft.Network/connections/sharedKey/read | Obtient une SharedKey VirtualNetworkGatewayConnection. |
> | Action | Microsoft.Network/connections/sharedKey/write | Crée une SharedKey VirtualNetworkGatewayConnection ou met à jour une SharedKey VirtualNetworkGatewayConnection existante. |
> | Action | Microsoft.Network/connections/startpacketcapture/action | Démarre une capture des paquets de connexion à la passerelle de réseau virtuel. |
> | Action | Microsoft.Network/connections/stoppacketcapture/action | Arrête une capture des paquets de connexion à la passerelle de réseau virtuel. |
> | Action | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Obtient la configuration de l’appareil VPN d’une connexion de passerelle de réseau virtuel |
> | Action | Microsoft.Network/connections/write | Crée une VirtualNetworkGatewayConnection ou met à jour une VirtualNetworkGatewayConnection existante. |
> | Action | Microsoft.Network/ddosCustomPolicies/delete | Supprime une stratégie personnalisée DDoS |
> | Action | Microsoft.Network/ddosCustomPolicies/read | Obtient une définition de stratégie personnalisée DDoS |
> | Action | Microsoft.Network/ddosCustomPolicies/write | Crée ou met à jour une stratégie personnalisée DDoS |
> | Action | Microsoft.Network/ddosProtectionPlans/delete | Supprime un plan de protection DDoS |
> | Action | Microsoft.Network/ddosProtectionPlans/join/action | Joint un plan de protection DDoS. Impossible à alerter. |
> | Action | Microsoft.Network/ddosProtectionPlans/read | Obtient un plan de protection DDoS |
> | Action | Microsoft.Network/ddosProtectionPlans/write | Crée ou met à jour un plan de protection DDoS  |
> | Action | Microsoft.Network/dnsoperationresults/read | Obtient les résultats d’une opération DNS. |
> | Action | Microsoft.Network/dnsoperationstatuses/read | Obtient l’état d’une opération DNS.  |
> | Action | Microsoft.Network/dnszones/A/delete | Supprimer le jeu d’enregistrements d’un nom donné et de type « A » d’une zone DNS. |
> | Action | Microsoft.Network/dnszones/A/read | Obtenir le jeu d’enregistrements de type « A » au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag. |
> | Action | Microsoft.Network/dnszones/A/write | Créer ou mettre à jour un jeu d’enregistrements de type « A » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements. |
> | Action | Microsoft.Network/dnszones/AAAA/delete | Supprimer le jeu d’enregistrements d’un nom donné et de type « AAAA » d’une zone DNS. |
> | Action | Microsoft.Network/dnszones/AAAA/read | Obtenir le jeu d’enregistrements de type « AAAA » au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag. |
> | Action | Microsoft.Network/dnszones/AAAA/write | Créer ou mettre à jour un jeu d’enregistrements de type « AAAA » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements. |
> | Action | Microsoft.Network/dnszones/all/read | Obtient les jeux d’enregistrements DNS parmi les types. |
> | Action | Microsoft.Network/dnszones/CAA/delete | Supprime le jeu d’enregistrements d’un nom donné et le type CAA d’une zone DNS. |
> | Action | Microsoft.Network/dnszones/CAA/read | Obtient le jeu d’enregistrements de type CAA au format JSON. Le jeu d’enregistrements contient la durée de vie, les balises et l’etag. |
> | Action | Microsoft.Network/dnszones/CAA/write | Crée ou met à jour un jeu d’enregistrements de type CAA à l’intérieur d’une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements. |
> | Action | Microsoft.Network/dnszones/CNAME/delete | Supprimer le jeu d’enregistrements d’un nom donné et de type « CNAME » d’une zone DNS. |
> | Action | Microsoft.Network/dnszones/CNAME/read | Obtenir le jeu d’enregistrements de type « CNAME » au format JSON. Le jeu d’enregistrements contient la durée de vie, les balises et l’etag. |
> | Action | Microsoft.Network/dnszones/CNAME/write | Créer ou mettre à jour un jeu d’enregistrements de type « CNAME » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements. |
> | Action | Microsoft.Network/dnszones/delete | Supprimer la zone DNS au format JSON. Exemples de propriétés de la zone : balises, etag, numberOfRecordSets et maxNumberOfRecordSets. |
> | Action | Microsoft.Network/dnszones/MX/delete | Supprimer le jeu d’enregistrements d’un nom donné et de type « MX » d’une zone DNS. |
> | Action | Microsoft.Network/dnszones/MX/read | Obtenir le jeu d’enregistrements de type « MX » au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag. |
> | Action | Microsoft.Network/dnszones/MX/write | Créer ou mettre à jour un jeu d’enregistrements de type « MX » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements. |
> | Action | Microsoft.Network/dnszones/NS/delete | Supprimer le jeu d’enregistrements DNS de type NS |
> | Action | Microsoft.Network/dnszones/NS/read | Obtenir le jeu d’enregistrements DNS de type NS |
> | Action | Microsoft.Network/dnszones/NS/write | Créer ou mettre à jour un jeu d’enregistrements DNS de type NS |
> | Action | Microsoft.Network/dnszones/PTR/delete | Supprimer le jeu d’enregistrements d’un nom donné et de type « PTR » d’une zone DNS. |
> | Action | Microsoft.Network/dnszones/PTR/read | Obtenir le jeu d’enregistrements de type « PTR » au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag. |
> | Action | Microsoft.Network/dnszones/PTR/write | Créer ou mettre à jour un jeu d’enregistrements de type « PTR » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements. |
> | Action | Microsoft.Network/dnszones/read | Obtenir la zone DNS au format JSON. Exemples de propriétés de la zone : balises, etag, numberOfRecordSets et maxNumberOfRecordSets. Notez que cette commande ne récupère pas les jeux d’enregistrements contenus dans la zone. |
> | Action | Microsoft.Network/dnszones/recordsets/read | Obtient les jeux d’enregistrements DNS parmi les types. |
> | Action | Microsoft.Network/dnszones/SOA/read | Obtenir le jeu d’enregistrements DNS de type SOA |
> | Action | Microsoft.Network/dnszones/SOA/write | Créer ou mettre à jour un jeu d’enregistrements DNS de type SOA |
> | Action | Microsoft.Network/dnszones/SRV/delete | Supprimer le jeu d’enregistrements d’un nom donné et de type « SRV » d’une zone DNS. |
> | Action | Microsoft.Network/dnszones/SRV/read | Obtenir le jeu d’enregistrements de type « SRV » au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag. |
> | Action | Microsoft.Network/dnszones/SRV/write | Créer ou mettre à jour un jeu d’enregistrements de type SRV |
> | Action | Microsoft.Network/dnszones/TXT/delete | Supprimer le jeu d’enregistrements d’un nom donné et de type « TXT » d’une zone DNS. |
> | Action | Microsoft.Network/dnszones/TXT/read | Obtenir le jeu d’enregistrements de type « TXT », au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag. |
> | Action | Microsoft.Network/dnszones/TXT/write | Créer ou mettre à jour un jeu d’enregistrements de type « TXT » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements. |
> | Action | Microsoft.Network/dnszones/write | Créer ou mettre à jour une zone DNS dans un groupe de ressources.  Permet de mettre à jour les balises dans une ressource de la zone DNS. Notez que cette commande ne peut pas être utilisée pour créer ou mettre à jour des jeux d’enregistrements au sein de la zone. |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/delete | Supprime une autorisation ExpressRouteCircuit. |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/read | Obtient une autorisation ExpressRouteCircuit. |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/write | Crée une autorisation ExpressRouteCircuit ou met à jour une autorisation ExpressRouteCircuit existante. |
> | Action | Microsoft.Network/expressRouteCircuits/delete | Supprime un ExpressRouteCircuit. |
> | Action | Microsoft.Network/expressRouteCircuits/join/action | Joint un circuit Express Route. Impossible à alerter. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/arpTables/read | Obtient une ArpTable de peering ExpressRouteCircuit. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Supprime une connexion ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Obtient une connexion ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Crée ou met à jour une ressource de connexion ExpressRouteCircuit existante |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/delete | Supprime un peering ExpressRouteCircuit. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Obtient une connexion appairée au circuit Express Route |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/read | Obtient un peering ExpressRouteCircuit. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/routeTables/read | Obtient une RouteTable de peering ExpressRouteCircuit. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/read | Obtient un résumé RouteTable de peering ExpressRouteCircuit. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Obtient les statistiques de peering d’un ExpressRouteCircuit. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/write | Crée un peering ExpressRouteCircuit ou met à jour un peering ExpressRouteCircuit existant. |
> | Action | Microsoft.Network/expressRouteCircuits/read | Obtenir un ExpressRouteCircuit. |
> | Action | Microsoft.Network/expressRouteCircuits/stats/read | Obtient les statistiques d’un ExpressRouteCircuit. |
> | Action | Microsoft.Network/expressRouteCircuits/write | Crée un ExpressRouteCircuit ou met à jour un ExpressRouteCircuit existant. |
> | Action | Microsoft.Network/expressRouteCrossConnections/join/action | Joint une connexion croisée Express Route. Impossible à alerter. |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/read | Obtient une table ARP de peering de connexion croisée ExpressRoute |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Supprime un peering de connexion croisée ExpressRoute |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/read | Obtient un peering de connexion croisée ExpressRoute |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/read | Obtient une table de routage de peering de connexion croisée ExpressRoute |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/read | Obtient un résumé de table de routage de peering de connexion croisée ExpressRoute |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/write | Crée un peering de connexion croisée ExpressRoute ou met à jour un peering de connexion croisée ExpressRoute existant |
> | Action | Microsoft.Network/expressRouteCrossConnections/read | Obtient une connexion croisée ExpressRoute |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Supprime une connexion Express Route |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Obtient une connexion Express Route |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Crée une connexion Express Route ou met à jour une connexion Express Route existante |
> | Action | Microsoft.Network/expressRouteGateways/join/action | Joint une passerelle Express Route. Impossible à alerter. |
> | Action | Microsoft.Network/expressRouteGateways/read | Obtenir une passerelle Express Route |
> | Action | Microsoft.Network/expressRoutePorts/delete | Supprime les ports ExpressRoute. |
> | Action | Microsoft.Network/expressRoutePorts/join/action | Joint des ports Express Route. Impossible à alerter. |
> | Action | Microsoft.Network/expressRoutePorts/links/read | Obtenir ExpressRouteLink |
> | Action | Microsoft.Network/expressRoutePorts/read | Obtient les ports ExpressRoute. |
> | Action | Microsoft.Network/expressRoutePorts/write | Crée ou met à jour les ports ExpressRoute. |
> | Action | Microsoft.Network/expressRoutePortsLocations/read | Obtenir les emplacements de ports ExpressRoute |
> | Action | Microsoft.Network/expressRouteServiceProviders/read | Obtient les fournisseurs de services ExpressRoute. |
> | Action | Microsoft.Network/firewallPolicies/delete | Supprime une stratégie de pare-feu |
> | Action | Microsoft.Network/firewallPolicies/join/action | Joint une stratégie de pare-feu. Impossible à alerter. |
> | Action | Microsoft.Network/firewallPolicies/read | Obtient une stratégie de pare-feu |
> | Action | Microsoft.Network/firewallPolicies/ruleGroups/delete | Supprime un groupe de règles de la stratégie de pare-feu |
> | Action | Microsoft.Network/firewallPolicies/ruleGroups/read | Obtient un groupe de règles de la stratégie de pare-feu |
> | Action | Microsoft.Network/firewallPolicies/ruleGroups/write | Crée un groupe de règles de stratégie de pare-feu ou met à jour un groupe de règles de stratégie de pare-feu existant |
> | Action | Microsoft.Network/firewallPolicies/write | Crée une stratégie de pare-feu ou met à jour une stratégie de pare-feu existante |
> | Action | Microsoft.Network/frontDoors/backendPools/delete | Supprime un pool back-end |
> | Action | Microsoft.Network/frontDoors/backendPools/read | Obtient un pool back-end |
> | Action | Microsoft.Network/frontDoors/backendPools/write | Crée ou met à jour un pool back-end |
> | Action | Microsoft.Network/frontDoors/delete | Supprime une porte d’entrée |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/delete | Supprime un point de terminaison front-end |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Désactive le protocole HTTPS sur un point de terminaison front-end |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Active le protocole HTTPS sur un point de terminaison front-end |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/read | Obtient un point de terminaison front-end |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/write | Crée ou met à jour un point de terminaison front-end |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/delete | Supprime des paramètres de sonde d’intégrité |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/read | Obtient des paramètres de sonde d’intégrité |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/write | Crée ou met à jour des paramètres de sonde d’intégrité |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Crée ou met à jour les paramètres d’équilibrage de charge |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/read | Obtient des paramètres d’équilibrage de charge |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/write | Crée ou met à jour les paramètres d’équilibrage de charge |
> | Action | Microsoft.Network/frontDoors/purge/action | Vide le contenu mis en cache à partir d’une porte d’entrée |
> | Action | Microsoft.Network/frontDoors/read | Obtient une porte d’entrée |
> | Action | Microsoft.Network/frontDoors/routingRules/delete | Supprime une règle d’acheminement |
> | Action | Microsoft.Network/frontDoors/routingRules/read | Obtient une règle d’acheminement |
> | Action | Microsoft.Network/frontDoors/routingRules/write | Crée ou met à jour une règle d’acheminement |
> | Action | Microsoft.Network/frontDoors/validateCustomDomain/action | Valide un point de terminaison front-end pour une porte d’entrée |
> | Action | Microsoft.Network/frontDoors/write | Crée ou met à jour une porte d’entrée |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Obtient les ensembles de règles gérées du pare-feu d’applications web |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Supprime une stratégie de pare-feu d’applications web |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/join/action | Joint une stratégie de pare-feu d’applications web. Impossible à alerter. |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Obtient la stratégie de pare-feu d’applications web |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Crée ou met à jour une stratégie de pare-feu d’applications web |
> | Action | Microsoft.Network/ipAllocations/delete | Supprime un IpAllocation |
> | Action | Microsoft.Network/ipAllocations/read | Obtient l’IpAllocation |
> | Action | Microsoft.Network/ipAllocations/write | Crée un IpAllocation ou met à jour un IpAllocation existant |
> | Action | Microsoft.Network/ipGroups/delete | Supprime un IpGroup |
> | Action | Microsoft.Network/ipGroups/join/action | Joint un IpGroup. Impossible à alerter. |
> | Action | Microsoft.Network/ipGroups/read | Obtient un IpGroup |
> | Action | Microsoft.Network/ipGroups/updateReferences/action | Met à jour les références dans un IpGroup |
> | Action | Microsoft.Network/ipGroups/validate/action | Valide un IpGroup |
> | Action | Microsoft.Network/ipGroups/write | Crée ou met à jour un IpGroup |
> | Action | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Joint un pool d’adresses principales d’équilibrage de charge. Impossible à alerter. |
> | Action | Microsoft.Network/loadBalancers/backendAddressPools/read | Obtient une définition de pool d’adresses principales d’équilibrage de charge. |
> | Action | Microsoft.Network/loadBalancers/delete | Supprime un équilibrage de charge. |
> | Action | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Joint une configuration d’adresse IP frontale d’équilibrage de charge Impossible à alerter. |
> | Action | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Obtient une définition de configuration d’adresse IP frontale d’équilibrage de charge. |
> | Action | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Joint un pool NAT entrant d’équilibrage de charge. Impossible à alerter. |
> | Action | Microsoft.Network/loadBalancers/inboundNatPools/read | Obtient une définition de pool NAT entrant d’équilibrage de charge. |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/delete | Supprime une règle NAT entrante d’équilibrage de charge. |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joint une règle nat de trafic entrant d’équilibrage de charge. Impossible à alerter. |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/read | Obtient une définition de règle NAT entrante d’équilibrage de charge. |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/write | Crée une règle NAT entrante d’équilibrage de charge ou met à jour une règle NAT entrante d’équilibrage de charge existante. |
> | Action | Microsoft.Network/loadBalancers/loadBalancingRules/read | Obtient une définition de règle d’équilibrage de charge. |
> | Action | Microsoft.Network/loadBalancers/networkInterfaces/read | Obtient les références à toutes les interfaces réseau sous un équilibrage de charge. |
> | Action | Microsoft.Network/loadBalancers/outboundRules/read | Obtient une définition de règle sortante d’équilibrage de charge. |
> | Action | Microsoft.Network/loadBalancers/probes/join/action | Autorise l’utilisation des sondes d’un équilibreur de charge. Par exemple, avec cette autorisation, la propriété healthProbe du groupe de machines virtuelles identiques peut faire référence à la sonde. Impossible à alerter. |
> | Action | Microsoft.Network/loadBalancers/probes/read | Obtient une sonde d’équilibrage de charge. |
> | Action | Microsoft.Network/loadBalancers/read | Obtient une définition d’équilibrage de charge. |
> | Action | Microsoft.Network/loadBalancers/virtualMachines/read | Obtient les références à toutes les machines virtuelles sous un équilibrage de charge. |
> | Action | Microsoft.Network/loadBalancers/write | Crée un équilibrage de charge ou met à jour un équilibrage de charge existant. |
> | Action | Microsoft.Network/localnetworkgateways/delete | Supprime une LocalNetworkGateway. |
> | Action | Microsoft.Network/localnetworkgateways/read | Obtient une LocalNetworkGateway. |
> | Action | Microsoft.Network/localnetworkgateways/write | Crée une LocalNetworkGateway ou met à jour une LocalNetworkGateway existante. |
> | Action | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Obtient les services de liaison privée à approbation automatique |
> | Action | Microsoft.Network/locations/availableDelegations/read | Obtient les délégations disponibles |
> | Action | Microsoft.Network/locations/availablePrivateEndpointTypes/read | Obtient les ressources de point de terminaison privé disponibles |
> | Action | Microsoft.Network/locations/availableServiceAliases/read | Obtient les alias de service disponibles |
> | Action | Microsoft.Network/locations/bareMetalTenants/action | Alloue ou valide un locataire Bare Metal |
> | Action | Microsoft.Network/locations/batchNotifyPrivateEndpointsForResourceMove/action | Avertit le point de terminaison privé par lots pour un déplacement des ressources. |
> | Action | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Vérifie la prise en charge de la mise en réseau accélérée |
> | Action | Microsoft.Network/locations/checkDnsNameAvailability/read | Vérifie si le nom DNS est disponible à l’emplacement spécifié. |
> | Action | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Vérifie la visibilité du service de liaison privée |
> | Action | Microsoft.Network/locations/operationResults/read | Obtient le résultat d’une opération DELETE ou POST asynchrone. |
> | Action | Microsoft.Network/locations/operations/read | Obtient la ressource d’opération qui représente l’état d’une opération asynchrone. |
> | Action | Microsoft.Network/locations/serviceTags/read | Obtenir les balises de service |
> | Action | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Obtient la taille des machines virtuelles prises en charge |
> | Action | Microsoft.Network/locations/usages/read | Obtient les mesures d’utilisation des ressources. |
> | Action | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Obtient la liste des services de point de terminaison de réseau virtuel disponibles |
> | Action | Microsoft.Network/networkIntentPolicies/delete | Supprime une stratégie d’intention de réseau. |
> | Action | Microsoft.Network/networkIntentPolicies/read | Obtient une description de la stratégie d’intention de réseau. |
> | Action | Microsoft.Network/networkIntentPolicies/write | Crée une stratégie d’intention de réseau ou met à jour une stratégie d’intention de réseau existante. |
> | Action | Microsoft.Network/networkInterfaces/delete | Supprime une interface réseau. |
> | Action | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Obtenir les groupes de sécurité réseau configurés sur l’interface réseau de la machine virtuelle. |
> | Action | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Obtenir la table de routage configurée sur l’interface réseau de la machine virtuelle. |
> | Action | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Joint une configuration de l’adresse IP de l’interface réseau Impossible à alerter. |
> | Action | Microsoft.Network/networkInterfaces/ipconfigurations/read | Obtient une définition de la configuration de l’adresse IP de l’interface réseau.  |
> | Action | Microsoft.Network/networkInterfaces/join/action | Joint une machine virtuelle à une interface réseau. Impossible à alerter. |
> | Action | Microsoft.Network/networkInterfaces/loadBalancers/read | Obtient tous les équilibrages de charge auxquels l’interface réseau appartient. |
> | Action | Microsoft.Network/networkInterfaces/read | Obtient une définition d’interface réseau.  |
> | Action | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Supprime une configuration d’appui de l’interface réseau |
> | Action | Microsoft.Network/networkInterfaces/tapConfigurations/read | Obtient une configuration d’appui de l’interface réseau |
> | Action | Microsoft.Network/networkInterfaces/tapConfigurations/write | Crée une configuration d’appui de l’interface réseau ou met à jour une configuration d’appui de l’interface réseau existante |
> | Action | Microsoft.Network/networkInterfaces/UpdateParentNicAttachmentOnElasticNic/action | Met à jour la carte réseau parente associée à la carte réseau élastique |
> | Action | Microsoft.Network/networkInterfaces/write | Crée une interface réseau ou met à jour une interface réseau existante.  |
> | Action | Microsoft.Network/networkProfiles/delete | Supprime un profil réseau. |
> | Action | Microsoft.Network/networkProfiles/read | Obtient un profil réseau. |
> | Action | Microsoft.Network/networkProfiles/removeContainers/action | Supprime des conteneurs. |
> | Action | Microsoft.Network/networkProfiles/setContainers/action | Définit des conteneurs. |
> | Action | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Définit des interfaces réseau de conteneur. |
> | Action | Microsoft.Network/networkProfiles/write | Crée ou met à jour un profil réseau. |
> | Action | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Obtient une définition de règle de sécurité par défaut. |
> | Action | Microsoft.Network/networkSecurityGroups/delete | Supprime un groupe de sécurité réseau. |
> | Action | Microsoft.Network/networkSecurityGroups/join/action | Joint un groupe de sécurité réseau. Impossible à alerter. |
> | Action | Microsoft.Network/networkSecurityGroups/read | Obtient une définition de groupe de sécurité réseau. |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/delete | Supprime une règle de sécurité. |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/read | Obtient une définition de règle de sécurité. |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/write | Crée une règle de sécurité ou met à jour une règle de sécurité existante. |
> | Action | Microsoft.Network/networkSecurityGroups/write | Crée un groupe de sécurité réseau ou met à jour un groupe de sécurité réseau existant. |
> | Action | Microsoft.Network/networkWatchers/availableProvidersList/action | Retourne tous les fournisseurs de services Internet disponibles pour une région Azure spécifiée. |
> | Action | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Retourne le score de la latence relatif pour les fournisseurs de services Internet d’un emplacement spécifié vers les régions Azure. |
> | Action | Microsoft.Network/networkWatchers/configureFlowLog/action | Configure la journalisation des flux pour une ressource cible. |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/delete | Supprime un moniteur de connexion |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Interroge la connectivité de surveillance entre des points de terminaison spécifiés |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/read | Obtient les détails du moniteur de connexion |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Démarre la surveillance de la connectivité entre des points de terminaison spécifiés |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Arrête/suspend la surveillance de la connectivité entre des points de terminaison spécifiés |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/write | Crée un moniteur de connexion |
> | Action | Microsoft.Network/networkWatchers/connectivityCheck/action | Vérifie la possibilité d’établir une connexion TCP directe à partir d’une machine virtuelle à un point de terminaison spécifié, notamment une autre machine virtuelle ou un serveur distant arbitraire. |
> | Action | Microsoft.Network/networkWatchers/delete | Supprime un observateur de réseau. |
> | Action | Microsoft.Network/networkWatchers/flowLogs/delete | Supprime un journal de flux |
> | Action | Microsoft.Network/networkWatchers/flowLogs/read | Obtient les détails du journal de flux |
> | Action | Microsoft.Network/networkWatchers/flowLogs/write | Crée un journal de flux |
> | Action | Microsoft.Network/networkWatchers/ipFlowVerify/action | Indique si le paquet est autorisé ou refusé vers ou depuis une destination particulière. |
> | Action | Microsoft.Network/networkWatchers/lenses/delete | Supprime un filtre |
> | Action | Microsoft.Network/networkWatchers/lenses/query/action | Interroge le trafic réseau de surveillance sur un point de terminaison spécifié |
> | Action | Microsoft.Network/networkWatchers/lenses/read | Obtient les détails du filtre |
> | Action | Microsoft.Network/networkWatchers/lenses/start/action | Démarre la surveillance du trafic réseau sur un point de terminaison spécifié |
> | Action | Microsoft.Network/networkWatchers/lenses/stop/action | Arrête/suspend la surveillance du trafic réseau sur un point de terminaison spécifié |
> | Action | Microsoft.Network/networkWatchers/lenses/write | Crée un filtre |
> | Action | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnostic de la configuration réseau. |
> | Action | Microsoft.Network/networkWatchers/nextHop/action | Pour une adresse IP de destination et une cible spécifiées, indique le type de saut suivant et l’adresse IP de saut suivant. |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/delete | Supprimer une capture de paquets. |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Obtient des informations sur les propriétés et l’état d’une ressource de capture de paquets. |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/read | Obtenir la définition de la capture de paquets. |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Arrêter la session de capture de paquets en cours d’exécution. |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/write | Crée une capture de paquets. |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/delete | Supprime un PingMesh |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/read | Obtenir les détails de PingMesh |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/start/action | Démarrer PingMesh entre les machines virtuelles spécifiées |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Arrêter PingMesh entre les machines virtuelles spécifiées |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/write | Crée un PingMesh |
> | Action | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Regrouper la connectivité de surveillance des requêtes entre des points de terminaison spécifiés |
> | Action | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Obtient l’état de la journalisation des flux sur une ressource. |
> | Action | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Obtient le résultat de la résolution des problèmes à partir de l’opération de résolution des problèmes en cours d’exécution ou exécutée précédemment. |
> | Action | Microsoft.Network/networkWatchers/read | Obtient la définition d’un observateur de réseau. |
> | Action | Microsoft.Network/networkWatchers/securityGroupView/action | Afficher les règles de groupe de sécurité réseau configurées et en vigueur appliquées à une machine virtuelle. |
> | Action | Microsoft.Network/networkWatchers/topology/action | Obtient une vue au niveau du réseau des ressources et de leurs relations dans un groupe de ressources. |
> | Action | Microsoft.Network/networkWatchers/troubleshoot/action | Démarre la résolution des problèmes sur une ressource réseau dans Azure. |
> | Action | Microsoft.Network/networkWatchers/write | Crée un observateur de réseau ou met à jour un observateur de réseau existant. |
> | Action | Microsoft.Network/operations/read | Obtenir les opérations disponibles. |
> | Action | Microsoft.Network/p2sVpnGateways/delete | Supprime une passerelle P2SVpnGateway |
> | Action | Microsoft.Network/p2sVpnGateways/disconnectp2svpnconnections/action | Déconnecte les connexions VPN P2S |
> | Action | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Générer un profil VPN pour la passerelle P2SVpnGateway |
> | Action | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Obtient l’intégrité d’une connexion VPN P2S pour P2SVpnGateway |
> | Action | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/action | Obtient l’intégrité d’une connexion détaillée VPN P2S pour P2SVpnGateway |
> | Action | Microsoft.Network/p2sVpnGateways/read | Obtient une passerelle P2SVpnGateway |
> | Action | Microsoft.Network/p2sVpnGateways/write | Génère une passerelle P2SVpnGateway |
> | Action | Microsoft.Network/privateDnsOperationResults/read | Obtient les résultats d’une opération de DNS privé |
> | Action | Microsoft.Network/privateDnsOperationStatuses/read | Obtient l’état d’une opération de DNS privé |
> | Action | Microsoft.Network/privateDnsZones/A/delete | Supprimer le jeu d’enregistrements d’un nom donné et de type « A » d’une zone DNS privée. |
> | Action | Microsoft.Network/privateDnsZones/A/read | Obtient le jeu d’enregistrements de type « A » au sein d’une zone DNS privée, au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag. |
> | Action | Microsoft.Network/privateDnsZones/A/write | Créer ou mettre à jour un jeu d’enregistrements de type « A » dans une zone DNS privée. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements. |
> | Action | Microsoft.Network/privateDnsZones/AAAA/delete | Supprimer le jeu d’enregistrements d’un nom donné et de type « AAAA » d’une zone DNS privée. |
> | Action | Microsoft.Network/privateDnsZones/AAAA/read | Obtient le jeu d’enregistrements de type « AAAA » au sein d’une zone DNS privée, au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag. |
> | Action | Microsoft.Network/privateDnsZones/AAAA/write | Créer ou mettre à jour un jeu d’enregistrements de type « AAAA » dans une zone DNS. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements. |
> | Action | Microsoft.Network/privateDnsZones/ALL/read | Obtient les jeux d’enregistrements DNS privés parmi les types |
> | Action | Microsoft.Network/privateDnsZones/CNAME/delete | Supprimer le jeu d’enregistrements d’un nom donné et de type « CNAME » d’une zone DNS privée. |
> | Action | Microsoft.Network/privateDnsZones/CNAME/read | Obtient le jeu d’enregistrements de type « CNAME » au sein d’une zone DNS privée, au format JSON. |
> | Action | Microsoft.Network/privateDnsZones/CNAME/write | Créer ou mettre à jour un jeu d’enregistrements de type « CNAME » dans une zone DNS privée. |
> | Action | Microsoft.Network/privateDnsZones/delete | Supprime une zone DNS privée. |
> | Action | Microsoft.Network/privateDnsZones/join/action | Joint une zone DNS privée |
> | Action | Microsoft.Network/privateDnsZones/MX/delete | Supprimer le jeu d’enregistrements d’un nom donné et de type « MX » d’une zone DNS privée. |
> | Action | Microsoft.Network/privateDnsZones/MX/read | Obtient le jeu d’enregistrements de type « MX » au sein d’une zone DNS privée, au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag. |
> | Action | Microsoft.Network/privateDnsZones/MX/write | Créer ou mettre à jour un jeu d’enregistrements de type « MX » dans une zone DNS privée. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements. |
> | Action | Microsoft.Network/privateDnsZones/PTR/delete | Supprimer le jeu d’enregistrements d’un nom donné et de type « PTR » d’une zone DNS privée. |
> | Action | Microsoft.Network/privateDnsZones/PTR/read | Obtient le jeu d’enregistrements de type « PTR » au sein d’une zone DNS privée, au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag. |
> | Action | Microsoft.Network/privateDnsZones/PTR/write | Créer ou mettre à jour un jeu d’enregistrements de type « PTR » dans une zone DNS privée. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements. |
> | Action | Microsoft.Network/privateDnsZones/read | Obtient les propriétés de la zone DNS privée au format JSON. Notez que cette commande ne récupère pas les réseaux virtuels auxquels la zone DNS privée est liée ni les jeux d’enregistrements contenus dans la zone. |
> | Action | Microsoft.Network/privateDnsZones/recordsets/read | Obtient les jeux d’enregistrements DNS privés parmi les types |
> | Action | Microsoft.Network/privateDnsZones/SOA/read | Obtient le jeu d’enregistrements de type « SOA » au sein d’une zone DNS privée, au format JSON. |
> | Action | Microsoft.Network/privateDnsZones/SOA/write | Met à jour un jeu d’enregistrements de type « SOA » dans une zone DNS privée. |
> | Action | Microsoft.Network/privateDnsZones/SRV/delete | Supprimer le jeu d’enregistrements d’un nom donné et de type « SRV » d’une zone DNS privée. |
> | Action | Microsoft.Network/privateDnsZones/SRV/read | Obtient le jeu d’enregistrements de type « SRV » au sein d’une zone DNS privée, au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag. |
> | Action | Microsoft.Network/privateDnsZones/SRV/write | Créer ou mettre à jour un jeu d’enregistrements de type « SRV » dans une zone DNS privée. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements. |
> | Action | Microsoft.Network/privateDnsZones/TXT/delete | Supprimer le jeu d’enregistrements d’un nom donné et de type « TXT » d’une zone DNS privée. |
> | Action | Microsoft.Network/privateDnsZones/TXT/read | Obtient le jeu d’enregistrements de type « TXT » au sein d’une zone DNS privée, au format JSON. Le jeu d’enregistrements contient une liste d’enregistrements, ainsi que la durée de vie, les balises et l’etag. |
> | Action | Microsoft.Network/privateDnsZones/TXT/write | Créer ou mettre à jour un jeu d’enregistrements de type « TXT » dans une zone DNS privée. Les enregistrements spécifiés remplaceront les enregistrements actuels dans le jeu d’enregistrements. |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Supprime un lien de zone DNS privée du réseau virtuel. |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Obtient le lien de DNS privé vers les propriétés du réseau virtuel au format JSON. |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Crée ou met à jour un lien de zone DNS privée du réseau virtuel. |
> | Action | Microsoft.Network/privateDnsZones/write | Créer ou mettre à jour une zone DNS privée dans un groupe de ressources. Notez que cette commande ne peut pas être utilisée pour créer ou mettre à jour des liens de réseau virtuel ou jeux d’enregistrements au sein de la zone. |
> | Action | Microsoft.Network/privateEndpointRedirectMaps/read | Obtient un point de terminaison privé RedirectMap |
> | Action | Microsoft.Network/privateEndpointRedirectMaps/write | Crée un point de terminaison privé RedirectMap ou met à jour à jour un point de terminaison privé RedirectMap existant |
> | Action | Microsoft.Network/privateEndpoints/delete | Supprime une ressource de point de terminaison privé. |
> | Action | Microsoft.Network/privateEndpoints/privateDnsZoneConfigs/write | Place une configuration de zone DNS privée |
> | Action | Microsoft.Network/privateEndpoints/read | Obtient une ressource de point de terminaison privé. |
> | Action | Microsoft.Network/privateEndpoints/write | Crée un nouveau point de terminaison privé, ou met à jour un point de terminaison privé existant. |
> | Action | Microsoft.Network/privateLinkServices/delete | Supprime une ressource de service de liaison privée |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Supprime une connexion de point de terminaison privé. |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Obtient une définition de connexion du point de terminaison privé. |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Crée une nouvelle connexion du point de terminaison privé ou met à jour une connexion du point de terminaison privé existante. |
> | Action | Microsoft.Network/privateLinkServices/read | Obtient une ressource de service de liaison privée. |
> | Action | Microsoft.Network/privateLinkServices/write | Crée un nouveau service de liaison privée, ou met à jour un service de liaison privée existant |
> | Action | Microsoft.Network/publicIPAddresses/delete | Supprime une adresse IP publique. |
> | Action | Microsoft.Network/publicIPAddresses/join/action | Joint une adresse IP publique. Impossible à alerter. |
> | Action | Microsoft.Network/publicIPAddresses/read | Obtient une définition de l’adresse IP publique. |
> | Action | Microsoft.Network/publicIPAddresses/write | Crée une adresse IP publique ou met à jour une adresse IP publique existante.  |
> | Action | Microsoft.Network/publicIPPrefixes/delete | Supprimer un préfixe d’adresse IP publique |
> | Action | Microsoft.Network/publicIPPrefixes/join/action | Joint un préfixe d’adresse IP publique. Impossible à alerter. |
> | Action | Microsoft.Network/publicIPPrefixes/read | Obtenir une définition de préfixe d’adresse IP publique |
> | Action | Microsoft.Network/publicIPPrefixes/write | Créer ou mettre à jour un préfixe d’adresse IP publique |
> | Action | Microsoft.Network/register/action | Enregistrer l’abonnement. |
> | Action | Microsoft.Network/routeFilters/delete | Supprime une définition de filtre de routage. |
> | Action | Microsoft.Network/routeFilters/join/action | Joint un filtre de routage. Impossible à alerter. |
> | Action | Microsoft.Network/routeFilters/read | Obtient une définition de filtre de routage. |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/delete | Supprime une définition de règle de filtre de routage. |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/read | Obtient une définition de règle de filtre de routage. |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/write | Crée une règle de filtre de routage ou met à jour une règle de filtre de routage existante. |
> | Action | Microsoft.Network/routeFilters/write | Crée un filtre de routes ou met à jour un filtre de routes existant |
> | Action | Microsoft.Network/routeTables/delete | Supprime une définition de table de routage. |
> | Action | Microsoft.Network/routeTables/join/action | Joint une table de routage. Impossible à alerter. |
> | Action | Microsoft.Network/routeTables/read | Obtient une définition de table de routage. |
> | Action | Microsoft.Network/routeTables/routes/delete | Supprime une définition de routage. |
> | Action | Microsoft.Network/routeTables/routes/read | Obtient une définition de routage. |
> | Action | Microsoft.Network/routeTables/routes/write | Crée un routage ou met à jour un routage existant. |
> | Action | Microsoft.Network/routeTables/write | Crée une table de routage ou met à jour une table de routage existante |
> | Action | Microsoft.Network/serviceEndpointPolicies/delete | Supprime une stratégie de point de terminaison de service |
> | Action | Microsoft.Network/serviceEndpointPolicies/join/action | Joint une stratégie de point de terminaison de service. Impossible à alerter. |
> | Action | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Joint un sous-réseau aux stratégies de point de terminaison de service. Impossible à alerter. |
> | Action | Microsoft.Network/serviceEndpointPolicies/read | Obtient une description de stratégie de point de terminaison de service |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Supprime une définition de stratégie de point de terminaison de service |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Obtient une description de définition de stratégie de point de terminaison de service |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Crée une définition de stratégie de point de terminaison de service ou met à jour une définition de stratégie de point de terminaison de service existante |
> | Action | Microsoft.Network/serviceEndpointPolicies/write | Crée une stratégie de point de terminaison de service ou met à jour une stratégie de point de terminaison de service existante |
> | Action | Microsoft.Network/trafficManagerGeographicHierarchies/read | Obtient la hiérarchie géographique Traffic Manager contenant des régions qui peuvent être utilisées dans la méthode de routage du trafic géographique. |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Supprime un point de terminaison Azure d’un profil Traffic Manager existant. Traffic Manager cesse de router le trafic vers le point de terminaison Azure supprimé. |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Obtient un point de terminaison Azure qui appartient à un profil Traffic Manager, incluant toutes les propriétés de ce point de terminaison Azure. |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Ajoute un nouveau point de terminaison Azure dans un profil Traffic Manager existant ou met à jour les propriétés d’un point de terminaison Azure existant dans ce profil Traffic Manager. |
> | Action | Microsoft.Network/trafficManagerProfiles/delete | Supprimer le profil Traffic Manager. Tous les paramètres associés au profil Traffic Manager seront perdus et le profil ne pourra plus être utilisé pour acheminer le trafic. |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Supprime un point de terminaison externe d’un profil Traffic Manager existant. Traffic Manager cesse de router le trafic vers le point de terminaison externe supprimé. |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Obtient un point de terminaison externe qui appartient à un profil Traffic Manager, incluant toutes les propriétés de ce point de terminaison externe. |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Ajoute un nouveau point de terminaison externe dans un profil Traffic Manager existant ou met à jour les propriétés d’un point de terminaison externe existant dans ce profil Traffic Manager. |
> | Action | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Obtient la carte thermique Traffic Manager pour le profil Traffic Manager spécifié qui contient les données de nombre et de latence des requêtes par emplacement et IP source. |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Supprime un point de terminaison imbriqué d’un profil Traffic Manager existant. Traffic Manager cesse de router le trafic vers le point de terminaison imbriqué supprimé. |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Obtient un point de terminaison imbriqué qui appartient à un profil Traffic Manager, incluant toutes les propriétés de ce point de terminaison imbriqué. |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Ajoute un nouveau point de terminaison imbriqué dans un profil Traffic Manager existant ou met à jour les propriétés d’un point de terminaison imbriqué existant dans ce profil Traffic Manager. |
> | Action | Microsoft.Network/trafficManagerProfiles/read | Obtient la configuration du profil Traffic Manager. Cela inclut les paramètres DNS, les paramètres de routage du trafic, les paramètres de surveillance des points de terminaison et la liste des points de terminaison acheminés par ce profil Traffic Manager. |
> | Action | Microsoft.Network/trafficManagerProfiles/write | Créer un profil Traffic Manager ou modifier la configuration d’un profil Traffic Manager existant.<br>Cela inclut l’activation ou la désactivation d’un profil et la modification des paramètres DNS, des paramètres de routage du trafic ou des paramètres de surveillance des points de terminaison.<br>Les points de terminaison acheminés par le profil Traffic Manager peuvent être ajoutés, supprimés, activés ou désactivés. |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Supprime la clé de niveau d’abonnement utilisée pour la collecte de métriques utilisateur en temps réel. |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/read | Obtient la clé de niveau d’abonnement utilisée pour la collecte de métriques utilisateur en temps réel. |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/write | Crée une nouvelle clé de niveau d’abonnement à utiliser pour la collecte de métriques utilisateur en temps réel. |
> | Action | Microsoft.Network/unregister/action | Désenregistrer l’abonnement. |
> | Action | Microsoft.Network/virtualHubs/delete | Supprime un hub virtuel |
> | Action | Microsoft.Network/virtualHubs/effectiveRoutes/action | Obtient l’itinéraire effectif configuré sur le hub virtuel |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Supprime une connexion de réseau virtuel hub |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Obtient une connexion de réseau virtuel hub |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Crée ou met à jour une connexion de réseau virtuel hub |
> | Action | Microsoft.Network/virtualHubs/read | Obtient un hub virtuel |
> | Action | Microsoft.Network/virtualHubs/routeTables/delete | Supprime une instance VirtualHubRouteTableV2 |
> | Action | Microsoft.Network/virtualHubs/routeTables/read | Obtient une instance VirtualHubRouteTableV2 |
> | Action | Microsoft.Network/virtualHubs/routeTables/write | Crée ou met à jour une instance VirtualHubRouteTableV2 |
> | Action | Microsoft.Network/virtualHubs/write | Crée ou met à jour un hub virtuel |
> | Action | microsoft.network/virtualnetworkgateways/connections/read | Obtient une connexion de passerelle de réseau virtuel |
> | Action | Microsoft.Network/virtualNetworkGateways/delete | Supprime une passerelle de réseau virtuel |
> | Action | microsoft.network/virtualnetworkgateways/disconnectvirtualnetworkgatewayvpnconnections/action | Déconnecte des connexions VPN à la passerelle de réseau virtuel |
> | Action | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Génère le package du client VPN pour la passerelle de réseau virtuel |
> | Action | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Génère le package du profil VPN pour la passerelle de réseau virtuel |
> | Action | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Obtient les itinéraires annoncés de la passerelle de réseau virtuel |
> | Action | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Obtient l’état d’homologue BGP de la passerelle de réseau virtuel |
> | Action | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Obtient les itinéraires appris de la passerelle de réseau virtuel |
> | Action | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Obtenir le contrôle d’intégrité par connexion client VPN pour VirtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Obtient les paramètres IPsec de client VPN pour le client P2S de la passerelle de réseau virtuel. |
> | Action | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Obtient l’URL d’un package de profil de client VPN prégénéré |
> | Action | Microsoft.Network/virtualNetworkGateways/read | Obtient une passerelle de réseau virtuel |
> | Action | microsoft.network/virtualnetworkgateways/reset/action | Réinitialise une passerelle de réseau virtuel |
> | Action | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Réinitialiser la clé partagée de client VPN pour le client P2S de la passerelle de réseau virtuel |
> | Action | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Définit les paramètres IPsec de client VPN pour le client P2S de la passerelle de réseau virtuel. |
> | Action | microsoft.network/virtualnetworkgateways/startpacketcapture/action | Démarre une capture des paquets de la passerelle de réseau virtuel. |
> | Action | microsoft.network/virtualnetworkgateways/stoppacketcapture/action | Arrête une capture des paquets de la passerelle de réseau virtuel. |
> | Action | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Affiche les appareils VPN pris en charge |
> | Action | Microsoft.Network/virtualNetworkGateways/write | Crée ou met à jour une passerelle de réseau virtuel |
> | Action | Microsoft.Network/virtualNetworks/BastionHosts/action | Obtient des références d’hôte bastion dans un réseau virtuel. |
> | Action | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Obtient des références d’hôte bastion dans un réseau virtuel. |
> | Action | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Vérifier si l’adresse IP est disponible sur le réseau virtuel spécifié. |
> | Action | Microsoft.Network/virtualNetworks/delete | Supprime un réseau virtuel. |
> | Action | Microsoft.Network/virtualNetworks/join/action | Joint un réseau virtuel. Impossible à alerter. |
> | Action | Microsoft.Network/virtualNetworks/peer/action | Appaire un réseau virtuel avec un autre réseau virtuel. |
> | Action | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Action | Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/delete | Supprime une stratégie de point de terminaison de service contextuel |
> | Action | Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/read | Obtient des stratégie de point de terminaison de service contextuel |
> | Action | Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/write | Crée une stratégie de point de terminaison de service contextuel ou met à jour une stratégie de point de terminaison de service contextuel existante |
> | Action | Microsoft.Network/virtualNetworks/subnets/delete | Supprime un sous-réseau de réseau virtuel. |
> | Action | Microsoft.Network/virtualNetworks/subnets/join/action | Joint un réseau virtuel. Impossible à alerter. |
> | Action | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau. Impossible à alerter. |
> | Action | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Prépare un sous-réseau en appliquant les stratégies réseau nécessaires |
> | Action | Microsoft.Network/virtualNetworks/subnets/read | Obtient une définition de sous-réseau de réseau virtuel. |
> | Action | Microsoft.Network/virtualNetworks/subnets/unprepareNetworkPolicies/action | Annule la préparation d'un sous-réseau en supprimant les stratégies réseau appliquées |
> | Action | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Obtient les références à toutes les machines virtuelles dans un sous-réseau de réseau virtuel. |
> | Action | Microsoft.Network/virtualNetworks/subnets/write | Crée un sous-réseau de réseau virtuel ou met à jour un sous-réseau de réseau virtuel existant. |
> | Action | Microsoft.Network/virtualNetworks/usages/read | Obtient les utilisations d’IP pour chaque sous-réseau du réseau virtuel |
> | Action | Microsoft.Network/virtualNetworks/virtualMachines/read | Obtient les références à toutes les machines virtuelles dans un réseau virtuel. |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Supprime un peering de réseau virtuel. |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Obtient une définition de peering de réseau virtuel. |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Crée un peering de réseau virtuel ou met à jour un peering de réseau virtuel existant. |
> | Action | Microsoft.Network/virtualNetworks/write | Crée un réseau virtuel ou met à jour un réseau virtuel existant. |
> | Action | Microsoft.Network/virtualNetworkTaps/delete | Supprime un TAP de réseau virtuel |
> | Action | Microsoft.Network/virtualNetworkTaps/join/action | Joint un TAP de réseau virtuel. Impossible à alerter. |
> | Action | Microsoft.Network/virtualNetworkTaps/read | Obtient un TAP de réseau virtuel |
> | Action | Microsoft.Network/virtualNetworkTaps/write | Crée ou met à jour un TAP de réseau virtuel |
> | Action | Microsoft.Network/virtualRouters/delete | Supprime une instance VirtualRouter |
> | Action | Microsoft.Network/virtualRouters/join/action | Joint une instance VirtualRouter. Impossible à alerter. |
> | Action | Microsoft.Network/virtualRouters/peerings/delete | Supprime une instance VirtualRouterPeering |
> | Action | Microsoft.Network/virtualRouters/peerings/read | Obtient une instance VirtualRouterPeering |
> | Action | Microsoft.Network/virtualRouters/peerings/write | Crée une instance VirtualRouterPeering ou met à jour une instance VirtualRouterPeering existante |
> | Action | Microsoft.Network/virtualRouters/read | Obtient une instance VirtualRouter |
> | Action | Microsoft.Network/virtualRouters/write | Crée une instance VirtualRouter ou met à jour une instance VirtualRouter existante |
> | Action | Microsoft.Network/virtualWans/delete | Supprime un WAN virtuel |
> | Action | Microsoft.Network/virtualwans/generateVpnProfile/action | Génère VirtualWanVpnServerConfiguration sur VpnProfile |
> | Action | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Supprime une configuration P2SVpnServerConfiguration du WAN virtuel |
> | Action | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Obtient une configuration P2SVpnServerConfiguration du WAN virtuel |
> | Action | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Crée une configuration P2SVpnServerConfiguration du WAN virtuel ou met à jour une configuration P2SVpnServerConfiguration du WAN virtuel existante |
> | Action | Microsoft.Network/virtualWans/read | Obtient un WAN virtuel |
> | Action | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Obtient les fournisseurs de sécurité VirtualWan pris en charge |
> | Action | Microsoft.Network/virtualWans/virtualHubs/read | Obtient tous les hubs virtuels qui référencent un WAN virtuel. |
> | Action | Microsoft.Network/virtualwans/vpnconfiguration/action | Obtient une configuration VPN |
> | Action | Microsoft.Network/virtualwans/vpnServerConfigurations/action | Obtenir VirtualWanVpnServerConfigurations |
> | Action | Microsoft.Network/virtualWans/vpnSites/read | Obtient tous les sites VPN qui référencent un WAN virtuel. |
> | Action | Microsoft.Network/virtualWans/write | Crée ou met à jour un WAN virtuel |
> | Action | Microsoft.Network/vpnGateways/delete | Supprime une passerelle VPN. |
> | Action | microsoft.network/vpngateways/listvpnconnectionshealth/action | Obtient le contrôle d’intégrité de la connexion pour l’ensemble des connexions sur une passerelle VPN, ou seulement un sous-ensemble |
> | Action | Microsoft.Network/vpnGateways/read | Obtient une passerelle VPN. |
> | Action | microsoft.network/vpngateways/reset/action | Réinitialise une passerelle VPN |
> | Action | microsoft.network/vpnGateways/vpnConnections/delete | Supprime une connexion VPN |
> | Action | microsoft.network/vpnGateways/vpnConnections/read | Obtient une connexion VPN. |
> | Action | microsoft.network/vpnGateways/vpnConnections/vpnLinkConnections/read | Obtient une connexion de liaison VPN |
> | Action | microsoft.network/vpnGateways/vpnConnections/write | Place une connexion VPN. |
> | Action | Microsoft.Network/vpnGateways/write | Place une passerelle VPN. |
> | Action | Microsoft.Network/vpnServerConfigurations/delete | Supprime VpnServerConfiguration |
> | Action | Microsoft.Network/vpnServerConfigurations/read | Obtient VpnServerConfiguration |
> | Action | Microsoft.Network/vpnServerConfigurations/write | Crée ou met à jour VpnServerConfiguration |
> | Action | Microsoft.Network/vpnsites/delete | Supprime une ressource de site VPN. |
> | Action | Microsoft.Network/vpnsites/read | Obtient une ressource de site VPN. |
> | Action | microsoft.network/vpnSites/vpnSiteLinks/read | Obtient un lien de site VPN |
> | Action | Microsoft.Network/vpnsites/write | Crée ou met à jour une ressource de site VPN. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Vérifie si un nom de ressource Namespace donné est disponible dans le service NotificationHub. |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Obtenir la liste des descriptions des règles d’autorisation des ressources Namespaces. |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Supprimer une règle d’autorisation de ressource Namespace. La règle d’autorisation de ressource Namespace par défaut ne peut pas être supprimée.  |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Obtenir la chaîne de connexion à la ressource Namespace. |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Obtenir la liste des descriptions des règles d’autorisation des ressources Namespaces. |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Règle d’autorisation de ressource Namespace pour régénérer les clés primaires et secondaires ; spécifier la clé qui doit être régénérée. |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Créer des règles d’autorisation au niveau d’une ressource Namespace et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour. |
> | Action | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Vérifie si un nom de NotificationHub donné est disponible à l’intérieur d’une ressource Namespace. |
> | Action | Microsoft.NotificationHubs/Namespaces/Delete | Supprimer une ressource Namespace |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Obtenir la liste des règles d’autorisation de concentrateur de notification. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Supprimer les règles d’autorisation de concentrateur de notification. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Obtenir la chaîne de connexion au concentrateur de notification. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Obtenir la liste des règles d’autorisation de concentrateur de notification. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Règle d’autorisation de concentrateur de notification pour régénérer les clés primaires et secondaires ; spécifier la clé qui doit être régénérée. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Créer des règles d’autorisation de concentrateur de notification et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Envoyer une notification Push de test. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Supprimer une ressource de concentrateur de notification. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Obtenir la liste des descriptions de mesures des ressources Namespace |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Obtenir toutes les informations d’identification PNS du concentrateur de notification. Cela inclut les informations d’identification WNS, MPNS, APNS, GCM et BAIDU. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Obtenir la liste des descriptions des ressources de concentrateur de notification. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Créer un concentrateur de notification et mettre à jour ses propriétés. Ses propriétés incluent principalement des informations d’identification PNS. Durée de vie et règles d’autorisation |
> | Action | Microsoft.NotificationHubs/Namespaces/read | Obtenir la liste des descriptions des ressources Namespace. |
> | Action | Microsoft.NotificationHubs/Namespaces/write | Créer une ressource Namespace et mettre à jour ses propriétés. Les balises et la capacité de l’espace de noms sont les propriétés qui peuvent être mises à jour. |
> | Action | Microsoft.NotificationHubs/operationResults/read | Retourne les résultats des opérations pour le fournisseur Notification Hubs. |
> | Action | Microsoft.NotificationHubs/operations/read | Retourne la liste des opérations prises en charge pour le fournisseur Notification Hubs. |
> | Action | Microsoft.NotificationHubs/register/action | Inscrit l’abonnement pour le fournisseur de ressources NotificationHubs et active la création de ressources Namespaces et NotificationHubs |
> | Action | Microsoft.NotificationHubs/unregister/action | Annule l’inscription de l’abonnement pour le fournisseur de ressources NotificationHubs et active la création de ressources Namespaces et NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.OffAzure/HyperVSites/clusters/read | Obtient les propriétés d’un cluster Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/clusters/write | Crée ou met à jour le cluster Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/delete | Supprime le site Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/healthsummary/read | Obtient le résumé d’intégrité pour la ressource Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/read | Obtient les propriétés d’un hôte Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/write | Crée ou met à jour l’hôte Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/jobs/read | Obtient les propriétés de travaux Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/machines/read | Obtient les propriétés de machines Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Obtient les propriétés d’un état d’opération Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/read | Obtient les propriétés d’un site Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/refresh/action | Actualise les objets au sein d’un site Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Obtient les propriétés de comptes d’identification Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/summary/read | Obtient le résumé d’un site Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/usage/read | Obtient les utilisations d’un site Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/write | Crée ou met à jour le site Hyper-V |
> | Action | Microsoft.OffAzure/ImportSites/delete | Supprime le site d'importation |
> | Action | Microsoft.OffAzure/ImportSites/exporturi/action | Obtient l’URI SAS pour exporter le fichier CSV des machines. |
> | Action | Microsoft.OffAzure/ImportSites/importuri/action | Permet d'obtenir l'URI SAS pour importer le fichier CSV des machines. |
> | Action | Microsoft.OffAzure/ImportSites/jobs/read | Permet d'obtenir les propriétés des travaux d'importation |
> | Action | Microsoft.OffAzure/ImportSites/machines/delete | Supprime le machine d'importation |
> | Action | Microsoft.OffAzure/ImportSites/machines/read | Permet d'obtenir les propriétés des machines d'importation |
> | Action | Microsoft.OffAzure/ImportSites/read | Permet d'obtenir les propriétés d'un site d'importation |
> | Action | Microsoft.OffAzure/ImportSites/write | Crée ou met à jour le site d'importation |
> | Action | Microsoft.OffAzure/Operations/read | Lit les opérations exposées |
> | Action | Microsoft.OffAzure/register/action | Inscrit un abonnement auprès du fournisseur de ressources Microsoft.OffAzure |
> | Action | Microsoft.OffAzure/ServerSites/delete | Supprime le site du serveur. |
> | Action | Microsoft.OffAzure/ServerSites/jobs/read | Obtient les propriétés des travaux d’un serveur |
> | Action | Microsoft.OffAzure/ServerSites/machines/read | Obtient les propriétés des machines d’un serveur |
> | Action | Microsoft.OffAzure/ServerSites/machines/write | Écrit les propriétés des machines d’un serveur |
> | Action | Microsoft.OffAzure/ServerSites/operationsstatus/read | Obtient les propriétés d’un état d’opération de serveur |
> | Action | Microsoft.OffAzure/ServerSites/read | Obtient les propriétés d’un site de serveur |
> | Action | Microsoft.OffAzure/ServerSites/refresh/action | Actualise les objets au sein d’un site de serveur |
> | Action | Microsoft.OffAzure/ServerSites/runasaccounts/read | Obtient les propriétés de comptes d’identification d’un serveur |
> | Action | Microsoft.OffAzure/ServerSites/summary/read | Obtient le résumé d’un site de serveur |
> | Action | Microsoft.OffAzure/ServerSites/usage/read | Obtient les utilisations d’un site de serveur |
> | Action | Microsoft.OffAzure/ServerSites/write | Crée ou met à jour le site du serveur |
> | Action | Microsoft.OffAzure/VMwareSites/clientGroupMembers/action | Répertorie les membres du groupe de clients sélectionné. |
> | Action | Microsoft.OffAzure/VMwareSites/delete | Supprime le site VMware |
> | Action | Microsoft.OffAzure/VMwareSites/exportapplications/action | Exporte les données des applications et des rôles VMware au format xls |
> | Action | Microsoft.OffAzure/VMwareSites/generateCoarseMap/action | Génère une carte grossière pour la liste des machines |
> | Action | Microsoft.OffAzure/VMwareSites/generateDetailedMap/action | Génère la carte VMware détaillée |
> | Action | Microsoft.OffAzure/VMwareSites/getApplications/action | Permet d'obtenir la liste des informations relatives aux applications pour les machines sélectionnées |
> | Action | Microsoft.OffAzure/VMwareSites/healthsummary/read | Obtient le résumé d’intégrité pour la ressource VMware |
> | Action | Microsoft.OffAzure/VMwareSites/jobs/read | Obtient les propriétés de travaux VMware |
> | Action | Microsoft.OffAzure/VMwareSites/machines/applications/read | Permet d'obtenir les propriétés des applications des machines VMware |
> | Action | Microsoft.OffAzure/VMwareSites/machines/read | Obtient les propriétés de machines VMware |
> | Action | Microsoft.OffAzure/VMwareSites/machines/start/action | Démarrer les machines VMware |
> | Action | Microsoft.OffAzure/VMwareSites/machines/stop/action | Arrête les machines VMware |
> | Action | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Obtient les propriétés d’un état d’opération VMware |
> | Action | Microsoft.OffAzure/VMwareSites/read | Obtient les propriétés d’un site VMware |
> | Action | Microsoft.OffAzure/VMwareSites/refresh/action | Actualise les objets au sein d’un site VMware |
> | Action | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Obtient les propriétés de comptes d’identification VMware |
> | Action | Microsoft.OffAzure/VMwareSites/serverGroupMembers/action | Répertorie les membres du groupe de serveurs sélectionné. |
> | Action | Microsoft.OffAzure/VMwareSites/summary/read | Obtient le résumé d’un site VMware |
> | Action | Microsoft.OffAzure/VMwareSites/updateProperties/action | Met à jour les propriétés des machines d'un site |
> | Action | Microsoft.OffAzure/VMwareSites/usage/read | Obtient les utilisations d’un site VMware |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/read | Obtient les propriétés d’un vCenter VMware |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/write | Crée ou met à jour le vCenter VMware |
> | Action | Microsoft.OffAzure/VMwareSites/write | Crée ou met à jour le site VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | microsoft.operationalinsights/availableservicetiers/read | Obtient les niveaux de service disponibles. |
> | Action | Microsoft.OperationalInsights/linkTargets/read | Répertorie les comptes existants qui ne sont pas associés à un abonnement Azure. Pour lier cet abonnement Azure à un espace de travail, utilisez un ID de client renvoyé par cette opération dans la propriété d’ID de client de l’opération de création d’un espace de travail. |
> | Action | microsoft.operationalinsights/locations/operationStatuses/read | Obtient l’état de l’opération asynchrone Azure d’analytique des journaux d’activité. |
> | Action | microsoft.operationalinsights/operations/read | Répertorie toutes les opérations de l’API Rest OperationalInsights disponibles. |
> | Action | microsoft.operationalinsights/register/action | Enregistre l’abonnement. |
> | Action | Microsoft.OperationalInsights/register/action | Inscrire un abonnement à un fournisseur de ressources. |
> | Action | microsoft.operationalinsights/unregister/action | Désenregistre l’abonnement. |
> | Action | Microsoft.OperationalInsights/workspaces/analytics/query/action | Effectue les recherches à l’aide d’un nouveau moteur. |
> | Action | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Obtient le schéma de recherche V2. |
> | Action | Microsoft.OperationalInsights/workspaces/api/query/action | Effectue les recherches à l’aide d’un nouveau moteur. |
> | Action | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Obtient le schéma de recherche V2. |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Supprimer l’étendue de la configuration. |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Obtenir l’étendue de la configuration. |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Définir l’étendue de la configuration. |
> | Action | microsoft.operationalinsights/workspaces/dataexport/delete | Supprime une exportation de données spécifique. |
> | Action | microsoft.operationalinsights/workspaces/dataexport/read | Obtient une exportation de données spécifique. |
> | Action | microsoft.operationalinsights/workspaces/dataexport/write | Crée ou met à jour une exportation de données. |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/delete | Supprimer des sources de données sous un espace de travail. |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/read | Obtenir des sources de données sous un espace de travail. |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/write | Créer/mettre à jour des sources de données sous un espace de travail. |
> | Action | Microsoft.OperationalInsights/workspaces/delete | Supprime un espace de travail. Si l’espace de travail a été lié à un espace de travail existant au moment de la création, l’espace de travail auquel il a été lié n’est pas supprimé. |
> | Action | Microsoft.OperationalInsights/workspaces/gateways/delete | Supprime une passerelle configurée pour l’espace de travail. |
> | Action | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Génère le certificat d’inscription de l’espace de travail. Ce certificat est utilisé pour connecter Microsoft System Center Operation Manager à l’espace de travail. |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Désactive un Intelligence Pack pour un espace de travail donné. |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Active un Intelligence Pack pour un espace de travail donné. |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Répertorie tous les Intelligence Packs visibles pour un espace de travail donné et répertorie également si le pack est activé ou désactivé pour cet espace de travail. |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Supprime les services liés sous un espace de travail spécifié. |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/read | Obtient les services liés sous un espace de travail spécifié. |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/write | Crée/met à jour les services liés sous un espace de travail spécifié. |
> | Action | Microsoft.OperationalInsights/workspaces/listKeys/action | Récupère les clés de liste pour l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail. |
> | Action | Microsoft.OperationalInsights/workspaces/listKeys/read | Récupère les clés de liste pour l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail. |
> | Action | Microsoft.OperationalInsights/workspaces/managementGroups/read | Obtient les noms et les métadonnées des groupes d’administration System Center Operations Manager connectés à cet espace de travail. |
> | Action | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Obtient les définitions de métrique sous un espace de travail |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Supprime les paramètres de notification de l’utilisateur pour l’espace de travail. |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Obtient les paramètres de notification de l’utilisateur pour l’espace de travail. |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Définit les paramètres de notification de l’utilisateur pour l’espace de travail. |
> | Action | microsoft.operationalinsights/workspaces/operations/read | Obtient l’état d’une opération d’espace de travail OperationalInsights. |
> | Action | microsoft.operationalinsights/workspaces/privateEndpointConnectionProxies/delete | Supprime un proxy de connexion de point de terminaison privé. |
> | Action | microsoft.operationalinsights/workspaces/privateEndpointConnectionProxies/read | Obtient un proxy de connexion de point de terminaison privé. |
> | Action | microsoft.operationalinsights/workspaces/privateEndpointConnectionProxies/validate/action | Valide un proxy de connexion de point de terminaison privé. |
> | Action | microsoft.operationalinsights/workspaces/privateEndpointConnectionProxies/write | Place un proxy de connexion de point de terminaison privé. |
> | Action | microsoft.operationalinsights/workspaces/privateEndpointConnections/delete | Supprime un proxy de connexion de point de terminaison privé. |
> | Action | microsoft.operationalinsights/workspaces/privateEndpointConnections/read | Obtient un proxy de connexion de point de terminaison privé. |
> | Action | microsoft.operationalinsights/workspaces/privateEndpointConnections/write | Place un proxy de connexion de point de terminaison privé. |
> | Action | microsoft.operationalinsights/workspaces/privateLinkResources/read | Obtient des ressources de liaison privée de Log Analytics. |
> | Action | Microsoft.OperationalInsights/workspaces/purge/action | Supprime les données spécifiées de l’espace de travail |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | Lit les données de la table AADDomainServicesAccountLogon |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | Lit les données de la table AADDomainServicesAccountManagement |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Lit les données de la table AADDomainServicesDirectoryServiceAccess |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Lit les données de la table AADDomainServicesLogonLogoff |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | Lit les données de la table AADDomainServicesPolicyChange |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Lit les données de la table AADDomainServicesPrivilegeUse |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Lit les données de la table AADDomainServicesSystemSecurity |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Lire les données de la table ADAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupAlerts/read | Lire les données de la table AddonAzureBackupAlerts |
> | Action | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupJobs/read | Lire les données de la table AddonAzureBackupJobs |
> | Action | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupPolicy/read | Lire les données de la table AddonAzureBackupPolicy |
> | Action | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupProtectedInstance/read | Lire les données de la table AddonAzureBackupProtectedInstance |
> | Action | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupStorage/read | Lire les données de la table AddonAzureBackupStorage |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Lit les données de la table ADFActivityRun |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Lit les données de la table ADFPipelineRun |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Lit les données de la table ADFTriggerRun |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Lire les données de la table ADReplicationResult |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Lire les données de la table ADSecurityAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/AegDeliveryFailureLogs/read | Lit les données de la table AegDeliveryFailureLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/AegPublishFailureLogs/read | Lit les données de la table AegPublishFailureLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/Alert/read | Lire les données de la table Alert |
> | Action | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Lire les données de la table AlertHistory |
> | Action | Microsoft.OperationalInsights/workspaces/query/AmlComputeClusterEvent/read | Lit les données de la table AmlComputeClusterEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/AmlComputeClusterNodeEvent/read | Lit les données de la table AmlComputeClusterNodeEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/AmlComputeJobEvent/read | Lit les données de la table AmlComputeJobEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/ApiManagementGatewayLogs/read | Lire les données de la table ApiManagementGatewayLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Lire les données à partir de la table AppCenterError |
> | Action | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Lire les données de la table ApplicationInsights |
> | Action | Microsoft.OperationalInsights/workspaces/query/AppPlatformLogsforSpring/read | Lit les données de la table AppPlatformLogsforSpring |
> | Action | Microsoft.OperationalInsights/workspaces/query/AppPlatformSystemLogs/read | Lit les données de la table AppPlatformSystemLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/AppServiceAppLogs/read | Lit les données de la table AppServiceAppLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/AppServiceAuditLogs/read | Lit les données de la table AppServiceAuditLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/AppServiceConsoleLogs/read | Lit les données de la table AppServiceConsoleLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/AppServiceEnvironmentPlatformLogs/read | Lire les données de la table AppServiceEnvironmentPlatformLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/AppServiceFileAuditLogs/read | Lit les données de la table AppServiceFileAuditLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/AppServiceHTTPLogs/read | Lit les données de la table AppServiceHTTPLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Lire les données de la table AuditLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Lit les données de la table AutoscaleEvaluationsLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Lit les données de la table AutoscaleScaleActionsLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/availabilityResults/read | Lit des données de la table availabilityResults |
> | Action | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Lit les données de la table AWSCloudTrail |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Lire les données de la table AzureActivity |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Lire les données de la table AzureAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Lire les données de la table AzureMetrics |
> | Action | Microsoft.OperationalInsights/workspaces/query/BaiClusterEvent/read | Lit les données de la table BaiClusterEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/BaiClusterNodeEvent/read | Lit les données de la table BaiClusterNodeEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/BaiJobEvent/read | Lit les données de la table BaiJobEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/BlockchainApplicationLog/read | Lit les données de la table BlockchainApplicationLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/BlockchainProxyLog/read | Lit les données de la table BlockchainProxyLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Lire les données de la table BoundPort |
> | Action | Microsoft.OperationalInsights/workspaces/query/browserTimings/read | Lit des données de la table browserTimings |
> | Action | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Lire les données de la table CommonSecurityLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Lire les données de la table ComputerGroup |
> | Action | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Lire les données de la table ConfigurationChange |
> | Action | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Lire les données de la table ConfigurationData |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Lire les données de la table ContainerImageInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Lire les données de la table ContainerInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Lire les données de la table ContainerLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Lire les données de la table ContainerNodeInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerRegistryLoginEvents/read | Lire les données de la table ContainerRegistryLoginEvents |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerRegistryRepositoryEvents/read | Lire les données de la table ContainerRegistryRepositoryEvents |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Lire les données de la table ContainerServiceLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/CoreAzureBackup/read | Lire les données de la table CoreAzureBackup |
> | Action | Microsoft.OperationalInsights/workspaces/query/customEvents/read | Lit des données de la table customEvents |
> | Action | Microsoft.OperationalInsights/workspaces/query/customMetrics/read | Lire des données de la table customMetrics |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Lit les données de la table DatabricksAccounts |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | Lit les données de la table DatabricksClusters |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | Lit les données de la table DatabricksDBFS |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksInstancePools/read | Lire les données de la table DatabricksInstancePools |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Lit les données de la table DatabricksJobs |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | Lit les données de la table DatabricksNotebook |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Lit les données de la table DatabricksSecrets |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Lit les données de la table DatabricksSQLPermissions |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | Lit les données de la table DatabricksSSH |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | Lit les données de la table DatabricksTables |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Lit les données de la table DatabricksWorkspace |
> | Action | Microsoft.OperationalInsights/workspaces/query/dependencies/read | Lit les données de la table des dépendances |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Lire les données de la table DeviceAppCrash |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Lire les données de la table DeviceAppLaunch |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Lire les données de la table DeviceCalendar |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Lire les données de la table DeviceCleanup |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Lire les données de la table DeviceConnectSession |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Lire les données de la table DeviceEtw |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Lire les données de la table DeviceHardwareHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Lire les données de la table DeviceHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Lire les données de la table DeviceHeartbeat |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Lire les données de la table DeviceSkypeHeartbeat |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Lire les données de la table DeviceSkypeSignIn |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Lire les données de la table DeviceSleepState |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Lire les données de la table DHAppFailure |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Lire les données de la table DHAppReliability |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHCPActivity/read | Lit les données de la table DHCPActivity |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Lire les données de la table DHDriverReliability |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Lire les données de la table DHLogonFailures |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Lire les données de la table DHLogonMetrics |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Lire les données de la table DHOSCrashData |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Lire les données de la table DHOSReliability |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Lire les données de la table DHWipAppLearning |
> | Action | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Lire les données de la table DnsEvents |
> | Action | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Lire les données de la table DnsInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Lire les données de la table ETWEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/Event/read | Lire les données de la table Event |
> | Action | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Lire les données de la table ExchangeAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Lire les données de la table ExchangeOnlineAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/FailedIngestion/read | Lire les données de la table FailedIngestion |
> | Action | Microsoft.OperationalInsights/workspaces/query/FunctionAppLogs/read | Lit les données de la table FunctionAppLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Lire les données de la table Heartbeat |
> | Action | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Lit les données de la table HuntingBookmark |
> | Action | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Lire les données de la table IISAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Lire les données de la table InboundConnection |
> | Action | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Lit les données de la table InsightsMetrics |
> | Action | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Lire des données à partir de la table IntuneAuditLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/IntuneDeviceComplianceOrg/read | Lit les données de la table IntuneDeviceComplianceOrg |
> | Action | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Lire les données à partir de la table IntuneOperationalLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/IoTHubDistributedTracing/read | Lit les données de la table IoTHubDistributedTracing |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Lit les données de la table KubeEvents |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubeHealth/read | Lire les données de la table KubeHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubeMonAgentEvents/read | Lit les données de la table KubeMonAgentEvents |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Lire les données de la table KubeNodeInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Lire les données de la table KubePodInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Lit les données de la table KubeServices |
> | Action | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Lire les données de la table LinuxAuditLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Lire les données de la table MAApplication |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Lire les données de la table MAApplicationHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Lire les données de la table MAApplicationHealthAlternativeVersions |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Lire les données de la table MAApplicationHealthIssues |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Lire les données de la table MAApplicationInstance |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Lire les données de la table MAApplicationInstanceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Lire les données de la table MAApplicationReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Lire les données de la table MADeploymentPlan |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Lire les données de la table MADevice |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Lire les données à partir de la table MADeviceNotEnrolled |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Lire les données de la table MADeviceNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Lire les données de la table MADevicePnPHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Lire les données de la table MADevicePnPHealthAlternativeVersions |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Lire les données de la table MADevicePnPHealthIssues |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Lire les données de la table MADeviceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Lire les données de la table MADriverInstanceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Lire les données de la table MADriverReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Lire les données de la table MAOfficeAddin |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Lire les données de la table MAOfficeAddinEntityHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Lire les données de la table MAOfficeAddinHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Lit les données de la table MAOfficeAddinHealthEventNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Lire les données de la table MAOfficeAddinHealthIssues |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Lire les données de la table MAOfficeAddinInstance |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Lire les données de la table MAOfficeAddinInstanceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Lire les données de la table MAOfficeAddinReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Lire les données de la table MAOfficeApp |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Lit les données de la table MAOfficeAppCrashesNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Lire les données de la table MAOfficeAppHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Lire les données de la table MAOfficeAppInstance |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Lire les données de la table MAOfficeAppInstanceHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Lire les données de la table MAOfficeAppReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Lit les données de la table MAOfficeAppSessionsNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Lire les données de la table MAOfficeBuildInfo |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Lire les données de la table MAOfficeCurrencyAssessment |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Lire les données de la table MAOfficeCurrencyAssessmentDailyCounts |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Lire les données de la table MAOfficeDeploymentStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Lire les données de la table MAOfficeDeploymentStatusNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Lit les données de la table MAOfficeMacroErrorNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | Lire les données de la table MAOfficeMacroGlobalHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Lire les données de la table MAOfficeMacroHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Lire les données de la table MAOfficeMacroHealthIssues |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Lire les données de la table MAOfficeMacroIssueInstanceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Lire les données de la table MAOfficeMacroIssueReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Lire les données de la table MAOfficeMacroSummary |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Lire les données de la table MAOfficeSuite |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Lire les données de la table MAOfficeSuiteInstance |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Lire les données de la table MAProposedPilotDevices |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Lire les données de la table MAWindowsBuildInfo |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Lire les données de la table MAWindowsCurrencyAssessment |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Lire les données de la table MAWindowsCurrencyAssessmentDailyCounts |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Lire les données de la table MAWindowsDeploymentStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Lire les données de la table MAWindowsDeploymentStatusNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Lire les données de la table MAWindowsSysReqInstanceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/McasShadowItReporting/read | Lire les données de la table McasShadowItReporting |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftAzureBastionAuditLogs/read | Lit les données de la table MicrosoftAzureBastionAuditLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftDataShareReceivedSnapshotLog/read | Lit les données de la table MicrosoftDataShareReceivedSnapshotLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftDataShareSentSnapshotLog/read | Lit les données de la table MicrosoftDataShareSentSnapshotLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftDataShareShareLog/read | Lire les données de la table MicrosoftDataShareShareLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftDynamicsTelemetryPerformanceLogs/read | Lit les données de la table MicrosoftDynamicsTelemetryPerformanceLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftDynamicsTelemetrySystemMetricsLogs/read | Lit les données de la table MicrosoftDynamicsTelemetrySystemMetricsLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftHealthcareApisAuditLogs/read | Lire les données de la table MicrosoftHealthcareApisAuditLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Lire les données de la table NetworkMonitoring |
> | Action | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Lire les données de la table OfficeActivity |
> | Action | Microsoft.OperationalInsights/workspaces/query/Operation/read | Lire les données de la table de Operation |
> | Action | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Lire les données de la table OutboundConnection |
> | Action | Microsoft.OperationalInsights/workspaces/query/pageViews/read | Lire des données de la table pageViews |
> | Action | Microsoft.OperationalInsights/workspaces/query/Perf/read | Lire les données de la table Perf |
> | Action | Microsoft.OperationalInsights/workspaces/query/performanceCounters/read | Lit les données de la table performanceCounters |
> | Action | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Lire les données de la table ProtectionStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/read | Exécuter des requêtes sur les données dans l’espace de travail |
> | Action | Microsoft.OperationalInsights/workspaces/query/requests/read | Lit les données de la table des requêtes |
> | Action | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Lire les données de la table ReservedCommonFields |
> | Action | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Lire les données de la table SCCMAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Lire les données de la table SCOMAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Lire les données de la table SecurityAlert |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Lire les données de la table SecurityBaseline |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Lire les données de la table SecurityBaselineSummary |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Lire les données de la table SecurityDetection |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Lire les données de la table SecurityEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Lit les données de la table SecurityIoTRawEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Lit les données de la table SecurityRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Lire les données de la table ServiceFabricOperationalEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Lire les données de la table ServiceFabricReliableActorEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Lire les données de la table ServiceFabricReliableServiceEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Lire les données de la table SfBAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Lire les données de la table SfBOnlineAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Lire les données de la table SharePointOnlineAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SignalRServiceDiagnosticLogs/read | Lit les données de la table SignalRServiceDiagnosticLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Lire les données de la table SigninLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Lire les données de la table SPAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Lire les données de la table SQLAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Lire les données de la table SQLQueryPerformance |
> | Action | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Lit les données de la table SqlThreatProtectionLoginAudits |
> | Action | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Lit les données de la table SqlVulnerabilityAssessmentResult |
> | Action | Microsoft.OperationalInsights/workspaces/query/StorageBlobLogs/read | Lit les données de la table StorageBlobLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/StorageFileLogs/read | Lit les données de la table StorageFileLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/StorageQueueLogs/read | Lit les données de la table StorageQueueLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/StorageTableLogs/read | Lit les données de la table StorageTableLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/SucceededIngestion/read | Lire les données de la table SucceededIngestion |
> | Action | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Lire les données de la table de Syslog |
> | Action | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Lire les données de la table SysmonEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/systemEvents/read | Lit des données de la table systemEvents |
> | Action | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Lire les données de n’importe quel journal personnalisé |
> | Action | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Lit les données de la table ThreatIntelligenceIndicator |
> | Action | Microsoft.OperationalInsights/workspaces/query/traces/read | Lit les données de la table des traces |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Lire les données de la table UAApp |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Lire les données de la table UAComputer |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Lire les données de la table UAComputerRank |
> | Action | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Lire les données de la table UADriver |
> | Action | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Lire les données de la table UADriverProblemCodes |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Lire les données de la table UAFeedback |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Lire les données de la table UAHardwareSecurity |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Lire les données de la table UAIESiteDiscovery |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Lire les données de la table UAOfficeAddIn |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Lire les données de la table UAProposedActionPlan |
> | Action | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Lire les données de la table UASysReqIssue |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Lire les données de la table UAUpgradedComputer |
> | Action | Microsoft.OperationalInsights/workspaces/query/Update/read | Lire les données de la table Update |
> | Action | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Lire les données de la table UpdateRunProgress |
> | Action | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Lire les données de la table UpdateSummary |
> | Action | Microsoft.OperationalInsights/workspaces/query/Usage/read | Lire les données de la table Usage |
> | Action | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Lire les données de la table VMBoundPort |
> | Action | Microsoft.OperationalInsights/workspaces/query/VMComputer/read | Lit les données de la table VMComputer |
> | Action | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Lire les données de la table VMConnection |
> | Action | Microsoft.OperationalInsights/workspaces/query/VMProcess/read | Lit les données de la table VMProcess |
> | Action | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Lire les données de la table W3CIISLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Lire les données de la table WaaSDeploymentStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Lire les données de la table WaaSInsiderStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Lire les données de la table WaaSUpdateStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Lire les données de la table WDAVStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Lire les données de la table WDAVThreat |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Lire les données de la table WindowsClientAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Lire les données de la table WindowsEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Lire les données de la table WindowsFirewall |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Lire les données de la table WindowsServerAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/WireData/read | Lire les données de la table WireData |
> | Action | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Lit les données de la table WorkloadMonitoringPerf |
> | Action | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Lire les données de la table WUDOAggregatedStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Lire les données de la table WUDOStatus |
> | Action | Microsoft.OperationalInsights/workspaces/read | Obtient un espace de travail existant. |
> | Action | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Régénère la clé partagée de l’espace de travail spécifié |
> | Action | microsoft.operationalinsights/workspaces/rules/read | Obtient toutes les règles d’alerte. |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Supprime une requête de recherche enregistrée. |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/read | Obtient une requête de recherche enregistrée. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/results/read | Obtient les résultats des recherches enregistrées. Déprécié |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Supprimer les actions de recherches planifiées. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Obtenir des actions de recherches planifiées. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Créer ou mettre à jour des actions de recherches planifiées. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Supprimer les recherches planifiées. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Obtenir des recherches planifiées. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Créer ou mettre à jour des recherches planifiées. |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/write | Crée une requête de recherche enregistrée. |
> | Action | Microsoft.OperationalInsights/workspaces/schema/read | Obtient le schéma de recherche de l’espace de travail.  Le schéma de recherche inclut les champs exposés et leurs types. |
> | Action | microsoft.operationalinsights/workspaces/scopedPrivateLinkProxies/delete | Supprime le proxy de liaison privée limitée. |
> | Action | microsoft.operationalinsights/workspaces/scopedPrivateLinkProxies/read | Obtient le proxy de liaison privée limitée. |
> | Action | microsoft.operationalinsights/workspaces/scopedPrivateLinkProxies/write | Place le proxy de liaison privée limitée. |
> | Action | Microsoft.OperationalInsights/workspaces/search/action | Exécute une requête de recherche. |
> | Action | microsoft.operationalinsights/workspaces/search/read | Obtient les résultats de la recherche. Action déconseillée. |
> | Action | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Récupère les clés partagées de l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail. |
> | Action | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Récupère les clés partagées de l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail. |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Supprime une configuration de stockage. Cela empêchera Microsoft Operational Insights de lire des données du compte de stockage. |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Obtient une configuration de stockage. |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Crée une configuration de stockage. Ces configurations sont utilisées pour extraire des données à partir d’un emplacement dans un compte de stockage existant. |
> | Action | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Obtient le journal Search Upgrade Translation Failure de l’espace de travail |
> | Action | Microsoft.OperationalInsights/workspaces/usages/read | Obtient les données d’utilisation d’un espace de travail, y compris la quantité de données lues par l’espace de travail. |
> | Action | Microsoft.OperationalInsights/workspaces/write | Crée un espace de travail ou lie un espace de travail existant en fournissant l’ID de client à partir de l’espace de travail existant. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.OperationsManagement/managementAssociations/delete | Supprime une association de gestion existante |
> | Action | Microsoft.OperationsManagement/managementAssociations/read | Obtient une association de gestion existante |
> | Action | Microsoft.OperationsManagement/managementAssociations/write | Crée une association de gestion |
> | Action | Microsoft.OperationsManagement/managementConfigurations/delete | Supprimer une configuration de gestion existante |
> | Action | Microsoft.OperationsManagement/managementConfigurations/read | Obtient une configuration de gestion existante |
> | Action | Microsoft.OperationsManagement/managementConfigurations/write | Crée une configuration de gestion |
> | Action | Microsoft.OperationsManagement/register/action | Inscrire un abonnement à un fournisseur de ressources. |
> | Action | Microsoft.OperationsManagement/solutions/delete | Supprimer la solution OMS existante. |
> | Action | Microsoft.OperationsManagement/solutions/read | Obtenir la solution OMS existante. |
> | Action | Microsoft.OperationsManagement/solutions/write | Créer une solution OMS. |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.PolicyInsights/asyncOperationResults/read | Obtenir le résultat de l'opération asynchrone |
> | DataAction | Microsoft.PolicyInsights/checkDataPolicyCompliance/action | Vérifie l’état de conformité d’un composant donné par rapport aux stratégies de données. |
> | Action | Microsoft.PolicyInsights/operations/read | Obtient les opérations prises en charge sur l’espace de noms Microsoft.PolicyInsights |
> | DataAction | Microsoft.PolicyInsights/policyEvents/logDataEvents/action | Journalise les événements de stratégie de composant de ressource. |
> | Action | Microsoft.PolicyInsights/policyEvents/queryResults/action | Demande des informations sur les événements de stratégie. |
> | Action | Microsoft.PolicyInsights/policyEvents/queryResults/read | Demande des informations sur les événements de stratégie. |
> | Action | Microsoft.PolicyInsights/policyMetadata/read | Obtient les ressources de métadonnées de stratégie. |
> | Action | Microsoft.PolicyInsights/policyStates/queryResults/action | Demande des informations sur les états de stratégie. |
> | Action | Microsoft.PolicyInsights/policyStates/queryResults/read | Demande des informations sur les états de stratégie. |
> | Action | Microsoft.PolicyInsights/policyStates/summarize/action | Demande des informations récapitulatives sur les états de stratégie. |
> | Action | Microsoft.PolicyInsights/policyStates/summarize/read | Demande des informations récapitulatives sur les états de stratégie. |
> | Action | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Déclencher une nouvelle évaluation de conformité pour l'étendue sélectionnée |
> | Action | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Interroger les informations sur les ressources requises par les stratégies DeployIfNotExists |
> | Action | Microsoft.PolicyInsights/register/action | Inscrit le fournisseur de ressources Microsoft Policy Insights et autorise des actions sur celui-ci. |
> | Action | Microsoft.PolicyInsights/remediations/cancel/action | Annule les corrections de Microsoft Policy en cours. |
> | Action | Microsoft.PolicyInsights/remediations/delete | Supprimer les corrections de stratégie |
> | Action | Microsoft.PolicyInsights/remediations/listDeployments/read | Répertorie les déploiements requis par une correction de stratégie |
> | Action | Microsoft.PolicyInsights/remediations/read | Obtenir les corrections de stratégie |
> | Action | Microsoft.PolicyInsights/remediations/write | Crée ou met à jour les corrections de Microsoft Policy. |
> | Action | Microsoft.PolicyInsights/unregister/action | Annule l’inscription du fournisseur de ressources Microsoft Policy Insights. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Portal/consoles/delete | Supprime l’instance Cloud Shell. |
> | Action | Microsoft.Portal/consoles/read | Lit l’instance Cloud Shell. |
> | Action | Microsoft.Portal/consoles/write | Créer ou mettre à jour une instance Cloud Shell. |
> | Action | Microsoft.Portal/dashboards/delete | Supprime le tableau de bord de l’abonnement. |
> | Action | Microsoft.Portal/dashboards/read | Lit les tableaux de bord pour l’abonnement. |
> | Action | Microsoft.Portal/dashboards/write | Ajoute ou modifie un tableau de bord à un abonnement. |
> | Action | Microsoft.Portal/register/action | S’inscrire au portail |
> | Action | Microsoft.Portal/usersettings/delete | Supprime les paramètres utilisateur Cloud Shell. |
> | Action | Microsoft.Portal/usersettings/read | Lit les paramètres utilisateur Cloud Shell. |
> | Action | Microsoft.Portal/usersettings/write | Créer ou mettre à jour le paramètre utilisateur Cloud Shell. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.PowerBIDedicated/capacities/delete | Supprime la capacité dédiée Power BI. |
> | Action | Microsoft.PowerBIDedicated/capacities/read | Récupère les informations de la capacité dédiée Power BI spécifiée. |
> | Action | Microsoft.PowerBIDedicated/capacities/resume/action | Reprend la capacité. |
> | Action | Microsoft.PowerBIDedicated/capacities/skus/read | Récupère les informations de référence (SKU) disponibles pour la capacité |
> | Action | Microsoft.PowerBIDedicated/capacities/suspend/action | Suspend la capacité. |
> | Action | Microsoft.PowerBIDedicated/capacities/write | Crée ou met à jour la capacité dédiée Power BI spécifiée. |
> | Action | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Vérifie que le nom de la capacité dédiée Power BI spécifié est valide et non utilisé. |
> | Action | Microsoft.PowerBIDedicated/locations/operationresults/read | Récupère les informations du résultat de l’opération spécifiée |
> | Action | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Récupère les informations de l’état de l’opération spécifiée |
> | Action | Microsoft.PowerBIDedicated/operations/read | Récupère les informations d’opérations |
> | Action | Microsoft.PowerBIDedicated/register/action | Enregistre le fournisseur de ressources dédié Power BI. |
> | Action | Microsoft.PowerBIDedicated/skus/read | Récupère les informations de références (SKU) |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
> | Action | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp est une opération interne utilisée par le service. |
> | Action | microsoft.recoveryservices/Locations/backupPreValidateProtection/action |  |
> | Action | microsoft.recoveryservices/Locations/backupProtectedItem/write | Créer un élément protégé de sauvegarde. |
> | Action | microsoft.recoveryservices/Locations/backupProtectedItems/read | Renvoie la liste de tous les éléments protégés. |
> | Action | microsoft.recoveryservices/Locations/backupStatus/action | Vérifie l’état de la sauvegarde pour les coffres Recovery Services |
> | Action | microsoft.recoveryservices/Locations/backupValidateFeatures/action | Valide des fonctionnalités |
> | Action | Microsoft.RecoveryServices/locations/checkNameAvailability/action | L’action Vérifier la disponibilité du nom de ressource est une API permettant de vérifier si le nom de ressource est disponible |
> | Action | Microsoft.RecoveryServices/locations/operationStatus/read | Obtient l’état de l’opération pour une opération donnée. |
> | Action | Microsoft.RecoveryServices/operations/read | Retourne la liste d’opérations pour un fournisseur de ressources |
> | Action | Microsoft.RecoveryServices/register/action | Inscrit un abonnement pour le fournisseur de ressources spécifié |
> | Action | microsoft.recoveryservices/Vaults/backupconfig/read | Renvoie la configuration pour le coffre Recovery Services. |
> | Action | microsoft.recoveryservices/Vaults/backupconfig/write | Met à jour la configuration pour le coffre Recovery Services. |
> | Action | microsoft.recoveryservices/Vaults/backupEncryptionConfigs/read | Obtient la configuration de chiffrement de la ressource de sauvegarde. |
> | Action | microsoft.recoveryservices/Vaults/backupEncryptionConfigs/write | Met à jour la configuration de chiffrement de la ressource de sauvegarde |
> | Action | microsoft.recoveryservices/Vaults/backupEngines/read | Retourne tous les serveurs d’administration de sauvegarde inscrits auprès du coffre. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/delete | Créer une intention de protection de sauvegarde |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/read | Créer une intention de protection de sauvegarde |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/write | Crée une intention de protection de sauvegarde. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/operationResults/read | Renvoie l’état de l’opération. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/operationsStatus/read | Renvoie l’état de l’opération. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectableContainers/read | Obtient tous les conteneurs protégeables |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/delete | Supprime le conteneur enregistré |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/inquire/action | Recherche les charges de travail dans un conteneur |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/items/read | Obtient tous les éléments figurant dans un conteneur |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtient les résultats de l’opération effectuée sur le conteneur de protection. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/operationsStatus/read | Obtient l’état de l’opération effectuée sur le conteneur de protection. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Effectue la sauvegarde d’un élément protégé. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Supprime un élément protégé. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtient les résultats de l’opération effectuée sur les éléments protégés. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Renvoie l’état de l’opération effectuée sur les éléments protégés. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Renvoie des détails d’objet de l’élément protégé. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Approvisionner la récupération d’éléments instantanée pour l’élément protégé. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obtenir les points de récupération des éléments protégés. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurer les points de récupération des éléments protégés. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Révoquer la récupération d’éléments instantanée pour l’élément protégé. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Créer un élément protégé de sauvegarde. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/read | Renvoie tous les conteneurs inscrits. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/write | Crée un conteneur inscrit |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/refreshContainers/action | Actualise la liste de conteneurs. |
> | Action | microsoft.recoveryservices/Vaults/backupJobs/cancel/action | Annuler le travail. |
> | Action | microsoft.recoveryservices/Vaults/backupJobs/operationResults/read | Renvoie le résultat de l’opération de travail. |
> | Action | microsoft.recoveryservices/Vaults/backupJobs/operationsStatus/read | Retourne l’état de l’opération de travail. |
> | Action | microsoft.recoveryservices/Vaults/backupJobs/read | Renvoie tous les objets de travail. |
> | Action | microsoft.recoveryservices/Vaults/backupJobsExport/action | Travaux d’exportation |
> | Action | microsoft.recoveryservices/Vaults/backupOperationResults/read | Renvoie le résultat de l’opération de sauvegarde pour le coffre Recovery Services. |
> | Action | microsoft.recoveryservices/Vaults/backupOperations/read | Renvoie l’état de l’opération de sauvegarde pour le coffre Recovery Services. |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/delete | Supprimer une stratégie de protection. |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/operationResults/read | Obtenir les résultats de l’opération de stratégie. |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/operations/read | Obtenir l’état de l’opération de stratégie. |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/read | Renvoie toutes les stratégies de protection. |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/write | Crée une stratégie de protection. |
> | Action | microsoft.recoveryservices/Vaults/backupProtectableItems/read | Renvoie la liste de tous les éléments pouvant être protégés. |
> | Action | microsoft.recoveryservices/Vaults/backupProtectedItems/read | Renvoie la liste de tous les éléments protégés. |
> | Action | microsoft.recoveryservices/Vaults/backupProtectionContainers/read | Renvoie tous les conteneurs appartenant à l’abonnement. |
> | Action | microsoft.recoveryservices/Vaults/backupProtectionIntents/read | Répertorier tous les intentions de protection de sauvegarde |
> | Action | microsoft.recoveryservices/Vaults/backupSecurityPIN/action | Renvoie les informations relatives au code PIN de sécurité pour le coffre Recovery Services. |
> | Action | microsoft.recoveryservices/Vaults/backupstorageconfig/read | Renvoie la configuration de stockage pour le coffre Recovery Services. |
> | Action | microsoft.recoveryservices/Vaults/backupstorageconfig/write | Met à jour la configuration de stockage pour le coffre Recovery Services. |
> | Action | microsoft.recoveryservices/Vaults/backupUsageSummaries/read | Renvoie des résumés pour les éléments protégés et les serveurs protégés d’un coffre Recovery Services. |
> | Action | microsoft.recoveryservices/Vaults/backupValidateOperation/action | Valider l’opération sur l’élément protégé. |
> | Action | Microsoft.RecoveryServices/Vaults/certificates/write | L’opération de mise à jour de certificat de ressource met à jour le certificat d’identification du coffre/de la ressource. |
> | Action | Microsoft.RecoveryServices/Vaults/delete | L’opération de suppression de coffre supprime la ressource Azure de type « coffre » spécifiée. |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/read | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/write | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Résout l’alerte. |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Obtient la configuration de notification du coffre Recovery Services. |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Configure les notifications par e-mail au coffre Recovery Services. |
> | Action | Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | L’opération d’annulation de l’inscription d’un conteneur peut être utilisée pour annuler l’inscription d’un conteneur. |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | L’opération d’obtention des résultats d’une opération peut être utilisée pour obtenir l’état de l’opération et le résultat de l’opération envoyée de manière asynchrone. |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | L’opération d’obtention de conteneurs peut être utilisée pour obtenir les conteneurs inscrits pour une ressource. |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | L’opération d’inscription d’un conteneur de service peut être utilisée pour inscrire un conteneur avec Recovery Services. |
> | Action | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lire des paramètres d’alertes. |
> | Action | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Créer ou mettre à jour des paramètres d’alertes. |
> | Action | Microsoft.RecoveryServices/vaults/replicationEvents/read | Lire des événements. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Vérifie la cohérence de la structure. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Supprimer des structures. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Déployer une image de serveur de traitement. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrer la structure fabric vers AAD |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/operationresults/read | Suivre les résultats d’une opération asynchrone sur la ressource Structures |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Lire des structures. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Réassocier une passerelle. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Supprimer une structure. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renouveler le certificat pour Fabric |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Configurer des réseaux logiques. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Lire des réseaux. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Supprimer des mappages réseau. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Lire des mappages réseau. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Créer ou mettre à jour des mappages réseau. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Découvrir un élément pouvant être protégé. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/operationresults/read | Suivre les résultats d’une opération asynchrone sur la ressource Conteneurs de protection |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Lire des conteneurs de protection. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Supprimer un conteneur de protection. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Supprimer tous les éléments de migration |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migrer l’élément |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Lire tous les points de récupération de la migration |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/read | Suivre les résultats d’une opération asynchrone sur la ressource Éléments de la migration |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Lire tous les éléments de la migration |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/resync/action | Resynchronise |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Migration de test |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Nettoyage de la migration de test |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Créer ou mettre à jour tous les éléments de migration |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Lire des éléments pouvant être protégés. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Ajoute des disques |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Appliquer un point de récupération. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Supprimer des éléments protégés. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Validation du basculement. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/read | Suivre les résultats d’une opération asynchrone sur la ressource Éléments protégés |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Basculement planifié. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Lire des éléments protégés. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Lire des points de récupération de réplication. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Supprimer un élément protégé. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Supprimer des disques |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Réparer la réplication. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Reprotéger l’élément protégé. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Envoyer des commentaires. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Lire toutes les tailles de calcul cible |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test Failover |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Nettoyage de basculement test. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Basculement |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Mettre à jour le service Mobilité. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Créer ou mettre à jour des éléments protégés. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Supprimer des mappages de conteneurs de protection. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/read | Suivre les résultats d’une opération asynchrone sur la ressource Mappages de conteneurs de protection |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Lire des mappages de conteneurs de protection. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Supprimer un mappage de conteneurs de protection. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Créer ou mettre à jour des mappages de conteneurs de protection. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Basculer un conteneur de protection. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Créer ou mettre à jour des conteneurs de protection. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Supprimer des fournisseurs Recovery Services. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/operationresults/read | Suivre les résultats d’une opération asynchrone sur la ressource Fournisseurs de services de récupération |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Lire des fournisseurs Recovery Services. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Actualiser un fournisseur. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Supprimer un fournisseur Recovery Services. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Créer ou mettre à jour des fournisseurs Recovery Services. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Lire des classifications de stockage. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Supprimer des mappages de classifications de stockage. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/read | Suivre les résultats d’une opération asynchrone sur la ressource Mappages de classifications de stockage |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Lire des mappages de classifications de stockage. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Créer ou mettre à jour des mappages de classifications de stockage. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Supprimer des serveurs vCenter. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/operationresults/read | Suivre les résultats d’une opération asynchrone sur la ressource vCenters |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Lire des serveurs vCenter. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Créer ou mettre à jour des serveurs vCenter. |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Créer ou mettre à jour des structures. |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Annuler un travail. |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/operationresults/read | Suivre les résultats d’une opération asynchrone sur la ressource Travaux |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/read | Lire des travaux. |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Redémarrer un travail. |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Reprendre un travail. |
> | Action | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Lire tous les éléments de la migration |
> | Action | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Lire des mappages réseau. |
> | Action | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Lire des réseaux. |
> | Action | Microsoft.RecoveryServices/vaults/replicationOperationStatus/read | Lit tout état de l’opération de réplication du coffre |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Supprimer des stratégies. |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/operationresults/read | Suivre les résultats d’une opération asynchrone sur la ressource Stratégies |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Lire des stratégies. |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Créer ou mettre à jour des stratégies. |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Lire des éléments protégés. |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Lire des mappages de conteneurs de protection. |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Lire des conteneurs de protection. |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Supprimer des plans de récupération. |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plan de récupération de validation de basculement. |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/operationresults/read | Suivre les résultats d’une opération asynchrone sur la ressource Plans de récupération |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plan de récupération de basculement planifié. |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Lire des plans de récupération. |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Reprotéger le plan de récupération. |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plan de récupération de basculement test. |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plan de récupération de nettoyage de basculement test. |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan de récupération de basculement. |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Créer ou mettre à jour des plans de récupération. |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Lire des fournisseurs Recovery Services. |
> | Action | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Lire des mappages de classifications de stockage. |
> | Action | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Lire des classifications de stockage. |
> | Action | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Lire tout  |
> | Action | Microsoft.RecoveryServices/vaults/replicationUsages/read | Lire des utilisations de la réplication du coffre. |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultHealth/operationresults/read | Suivre les résultats d’une opération asynchrone sur la ressource Intégrité de la réplication du coffre |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Lire l’intégrité de la réplication du coffre. |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Actualiser l’intégrité du coffre. |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Lire tout  |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Crée ou mettre à jour tout  |
> | Action | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Lire des serveurs vCenter. |
> | Action | microsoft.recoveryservices/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | Action | Microsoft.RecoveryServices/vaults/usages/read | Lire des utilisations de coffre. |
> | Action | Microsoft.RecoveryServices/Vaults/vaultTokens/read | L’opération de jeton de coffre peut être utilisée pour obtenir un jeton de coffre pour les opérations de serveur principal au niveau du coffre. |
> | Action | Microsoft.RecoveryServices/Vaults/write | L’opération de création de coffre entraîne la création d’une ressource Azure de type « coffre ». |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Relay/checkNameAvailability/action | Vérifie la disponibilité d’un espace de noms sous un abonnement donné. |
> | Action | Microsoft.Relay/checkNamespaceAvailability/action | Vérifie la disponibilité d’un espace de noms sous un abonnement donné. Cette API étant dépréciée, utilisez CheckNameAvailability à la place. |
> | Action | Microsoft.Relay/namespaces/authorizationRules/action | Met à jour une règle d’autorisation d’espace de noms. Cette API est déconseillée. Utilisez un appel PUT à la place pour mettre à jour la règle d’autorisation d’espace de noms... Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. |
> | Action | Microsoft.Relay/namespaces/authorizationRules/delete | Supprimer une règle d’autorisation de ressource Namespace. La règle d’autorisation de ressource Namespace par défaut ne peut pas être supprimée.  |
> | Action | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Obtenir la chaîne de connexion à la ressource Namespace. |
> | Action | Microsoft.Relay/namespaces/authorizationRules/read | Obtenir la liste des descriptions des règles d’autorisation des ressources Namespaces. |
> | Action | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Régénère la clé primaire ou secondaire pour la ressource. |
> | Action | Microsoft.Relay/namespaces/authorizationRules/write | Créer des règles d’autorisation au niveau d’une ressource Namespace et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour. |
> | Action | Microsoft.Relay/namespaces/Delete | Supprimer une ressource Namespace |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obtient les clés de règles d’autorisation pour l’espace de noms principal de récupération d’urgence |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obtient les règles d’autorisation de l’espace de noms principal de récupération d’urgence |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Désactive la récupération d’urgence et arrête la réplication des changements de l’espace de noms principal sur l’espace de noms secondaire. |
> | Action | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Vérifie la disponibilité d’un alias d’espace de noms sous un abonnement donné. |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Supprime la configuration de récupération d’urgence associée à l’espace de noms. Cette opération peut être appelée uniquement via l’espace de noms principal. |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Appelle un basculement de géorécupération d’urgence et reconfigure l’alias d’espace de noms pour qu’il pointe vers l’espace de noms secondaire. |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Obtient la configuration de récupération d’urgence associée à l’espace de noms. |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Crée ou met à jour la configuration de récupération d’urgence associée à l’espace de noms. |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Met à jour HybridConnection. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. Règles d’autorisation. Utilisez un appel PUT pour mettre à jour la règle d’autorisation. |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Opération de suppression des règles d’autorisation HybridConnection. |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Obtenir la chaîne de connexion à HybridConnection. |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Obtient la liste des règles d’autorisation de HybridConnection |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Régénère la clé primaire ou secondaire pour la ressource. |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Créer des règles d’autorisation HybridConnection et mettre à jour leurs propriétés. Les droits d’accès des règles d’autorisation peuvent être mis à jour. |
> | Action | Microsoft.Relay/namespaces/HybridConnections/Delete | Opération de suppression des ressources HybridConnection. |
> | Action | Microsoft.Relay/namespaces/HybridConnections/read | Obtenir la liste des descriptions des ressources HybridConnection. |
> | Action | Microsoft.Relay/namespaces/HybridConnections/write | Créer ou mettre à jour des propriétés HybridConnection. |
> | Action | Microsoft.Relay/namespaces/messagingPlan/read | Obtient le plan de messagerie pour un espace de noms.<br>Cette API est déconseillée.<br>Les propriétés exposées via la ressource MessagingPlan sont déplacées vers la ressource d’espace de noms (parente) dans les versions ultérieures de l’API.<br>Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. |
> | Action | Microsoft.Relay/namespaces/messagingPlan/write | Met à jour le plan de messagerie pour un espace de noms.<br>Cette API est déconseillée.<br>Les propriétés exposées via la ressource MessagingPlan sont déplacées vers la ressource d’espace de noms (parente) dans les versions ultérieures de l’API.<br>Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. |
> | Action | Microsoft.Relay/namespaces/networkrulesets/delete | Supprimer la ressource de règle de réseau virtuel |
> | Action | Microsoft.Relay/namespaces/networkrulesets/read | Obtient la ressource NetworkRuleSet |
> | Action | Microsoft.Relay/namespaces/networkrulesets/write | Créer une ressource de règle de réseau virtuel |
> | Action | Microsoft.Relay/namespaces/operationresults/read | Obtient l’état de l’opération d’espace de noms |
> | Action | Microsoft.Relay/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obtenir la liste des descriptions des ressources des paramètres de diagnostics Namespace. |
> | Action | Microsoft.Relay/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obtenir la liste des descriptions des ressources des paramètres de diagnostics Namespace. |
> | Action | Microsoft.Relay/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obtenir la liste des descriptions des ressources des journaux d’activité Namespace |
> | Action | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obtenir la liste des descriptions de mesures des ressources Namespace |
> | Action | Microsoft.Relay/namespaces/read | Obtenir la liste des descriptions des ressources Namespace. |
> | Action | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Supprime l’espace de noms ACS |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Met à jour WcfRelay. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. Règles d’autorisation. Utilisez un appel PUT pour mettre à jour la règle d’autorisation. |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Opération de suppression des règles d’autorisation WcfRelay. |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Obtenir la chaîne de connexion à WcfRelay. |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Obtient la liste des règles d’autorisation de WcfRelay |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Régénère la clé primaire ou secondaire pour la ressource. |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Créer des règles d’autorisation WcfRelay et mettre à jour leurs propriétés. Les droits d’accès des règles d’autorisation peuvent être mis à jour. |
> | Action | Microsoft.Relay/namespaces/WcfRelays/Delete | Opération de suppression des ressources WcfRelay. |
> | Action | Microsoft.Relay/namespaces/WcfRelays/read | Obtenir la liste des descriptions des ressources WcfRelay. |
> | Action | Microsoft.Relay/namespaces/WcfRelays/write | Créer ou mettre à jour des propriétés WcfRelay. |
> | Action | Microsoft.Relay/namespaces/write | Créer une ressource Namespace et mettre à jour ses propriétés. Les balises et la capacité de l’espace de noms sont les propriétés qui peuvent être mises à jour. |
> | Action | Microsoft.Relay/operations/read | Obtient les opérations |
> | Action | Microsoft.Relay/register/action | Inscrit l’abonnement pour le fournisseur de ressources Relay et active la création de ressources Relay. |
> | Action | Microsoft.Relay/unregister/action | Inscrit l’abonnement pour le fournisseur de ressources Relay et active la création de ressources Relay. |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Obtient l’état de disponibilité de la ressource spécifiée. |
> | Action | Microsoft.ResourceHealth/AvailabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Action | Microsoft.ResourceHealth/events/read | Obtient les événements Service Health de l’abonnement donné |
> | Action | Microsoft.Resourcehealth/healthevent/action | Indique le changement d’état d’intégrité de la ressource spécifiée |
> | Action | Microsoft.Resourcehealth/healthevent/Activated/action | Indique le changement d’état d’intégrité de la ressource spécifiée |
> | Action | Microsoft.Resourcehealth/healthevent/InProgress/action | Indique le changement d’état d’intégrité de la ressource spécifiée |
> | Action | Microsoft.Resourcehealth/healthevent/Pending/action | Indique le changement d’état d’intégrité de la ressource spécifiée |
> | Action | Microsoft.Resourcehealth/healthevent/Resolved/action | Indique le changement d’état d’intégrité de la ressource spécifiée |
> | Action | Microsoft.Resourcehealth/healthevent/Updated/action | Indique le changement d’état d’intégrité de la ressource spécifiée |
> | Action | Microsoft.ResourceHealth/impactedResources/read | Obtient les ressources impactées de l’abonnement donné |
> | Action | Microsoft.ResourceHealth/metadata/read | Obtient les métadonnées |
> | Action | Microsoft.ResourceHealth/Notifications/read | Reçoit les notifications Azure Resource Manager |
> | Action | Microsoft.ResourceHealth/Operations/read | Obtient les opérations disponibles pour Microsoft ResourceHealth |
> | Action | Microsoft.ResourceHealth/register/action | Inscrit l’abonnement à Microsoft ResourceHealth |
> | Action | Microsoft.ResourceHealth/unregister/action | Annule l’inscription de l’abonnement à Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Resources/calculateTemplateHash/action | Calcule le code de hachage du modèle fourni. |
> | Action | Microsoft.Resources/checkPolicyCompliance/read | Vérifie l’état de conformité d’une ressource donnée par rapport aux stratégies de ressource. |
> | Action | Microsoft.Resources/checkResourceName/action | Vérifier la validité du nom de la ressource. |
> | Action | Microsoft.Resources/deployments/cancel/action | Annule un déploiement. |
> | Action | Microsoft.Resources/deployments/delete | Supprime un déploiement. |
> | Action | Microsoft.Resources/deployments/exportTemplate/action | Exporte un modèle pour un déploiement |
> | Action | Microsoft.Resources/deployments/operations/read | Obtient ou répertorie les opérations de déploiement. |
> | Action | Microsoft.Resources/deployments/operationstatuses/read | Obtient ou répertorie les états de l’opération de déploiement. |
> | Action | Microsoft.Resources/deployments/read | Obtient ou répertorie les déploiements. |
> | Action | Microsoft.Resources/deployments/validate/action | Valide un déploiement. |
> | Action | Microsoft.Resources/deployments/whatIf/action | Prédit les changements des modèles de déploiement. |
> | Action | Microsoft.Resources/deployments/write | Crée ou met à jour un déploiement. |
> | Action | Microsoft.Resources/deploymentScripts/delete | Supprime un script de déploiement |
> | Action | Microsoft.Resources/deploymentScripts/logs/read | Obtient ou répertorie les journaux de scripts de déploiement |
> | Action | Microsoft.Resources/deploymentScripts/read | Obtient ou répertorie les scripts de déploiement |
> | Action | Microsoft.Resources/deploymentScripts/write | Crée ou met à jour un script de déploiement |
> | Action | Microsoft.Resources/links/delete | Supprime un lien de ressource. |
> | Action | Microsoft.Resources/links/read | Obtient ou répertorie les liens des ressources. |
> | Action | Microsoft.Resources/links/write | Crée ou met à jour un lien de ressource. |
> | Action | Microsoft.Resources/marketplace/purchase/action | Achète une ressource sur la place de marché. |
> | Action | Microsoft.Resources/providers/read | Obtient la liste des fournisseurs. |
> | Action | Microsoft.Resources/resources/read | Obtenir la liste des ressources en fonction des filtres. |
> | Action | Microsoft.Resources/subscriptions/locations/read | Obtient la liste des emplacements pris en charge. |
> | Action | Microsoft.Resources/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | Action | Microsoft.Resources/subscriptions/providers/read | Obtient ou répertorie les fournisseurs de ressources. |
> | Action | Microsoft.Resources/subscriptions/read | Obtient la liste des abonnements. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/delete | Supprime un groupe de ressources, ainsi que toutes ses ressources. |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Obtient ou répertorie les opérations de déploiement. |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Obtient ou répertorie les états de l’opération de déploiement. |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Obtient ou répertorie les déploiements. |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Crée ou met à jour un déploiement. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Déplace les ressources d’un groupe de ressources à un autre. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Obtient les ressources du groupe de ressources. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Valide le déplacement des ressources d’un groupe de ressources à un autre. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/write | Crée ou met à jour un groupe de ressources. |
> | Action | Microsoft.Resources/subscriptions/resources/read | Obtient les ressources d’un abonnement. |
> | Action | Microsoft.Resources/subscriptions/tagNames/delete | Supprime une balise d’abonnement. |
> | Action | Microsoft.Resources/subscriptions/tagNames/read | Obtient ou répertorie les balises d’abonnement. |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Supprime une valeur de balise d’abonnement. |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Obtient ou répertorie les valeurs des balises d’abonnement. |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Ajoute une valeur de balise d’abonnement. |
> | Action | Microsoft.Resources/subscriptions/tagNames/write | Ajoute une balise d’abonnement. |
> | Action | Microsoft.Resources/tags/delete | Supprime toutes les balises sur une ressource. |
> | Action | Microsoft.Resources/tags/read | Obtient toutes les balises sur une ressource. |
> | Action | Microsoft.Resources/tags/write | Met à jour les balises sur une ressource en remplaçant ou fusionnant les balises existantes avec un nouvel ensemble de balises ou en supprimant les balises existantes. |
> | Action | Microsoft.Resources/tenants/read | Obtient la liste des clients. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Scheduler/jobcollections/delete | Supprime une collection de travaux. |
> | Action | Microsoft.Scheduler/jobcollections/disable/action | Désactive une collection de travaux. |
> | Action | Microsoft.Scheduler/jobcollections/enable/action | Active une collection de travaux. |
> | Action | Microsoft.Scheduler/jobcollections/jobs/delete | Supprime un travail. |
> | Action | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Génère une définition d’application logique basée sur un travail de Scheduler. |
> | Action | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Affiche l’historique des travaux. |
> | Action | Microsoft.Scheduler/jobcollections/jobs/read | Obtient un travail. |
> | Action | Microsoft.Scheduler/jobcollections/jobs/run/action | Exécute un travail. |
> | Action | Microsoft.Scheduler/jobcollections/jobs/write | Crée ou met à jour un travail. |
> | Action | Microsoft.Scheduler/jobcollections/read | Obtenir une collection de travaux. |
> | Action | Microsoft.Scheduler/jobcollections/write | Crée ou met à jour une collection de travaux. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Search/checkNameAvailability/action | Vérifie la disponibilité du nom du service. |
> | Action | Microsoft.Search/operations/read | Répertorie toutes les opérations disponibles du fournisseur Microsoft.Search. |
> | Action | Microsoft.Search/register/action | Inscrit l’abonnement pour le fournisseur de ressources de recherche et active la création de services de recherche. |
> | Action | Microsoft.Search/searchServices/createQueryKey/action | Crée la clé de requête. |
> | Action | Microsoft.Search/searchServices/delete | Supprime le service de recherche. |
> | Action | Microsoft.Search/searchServices/deleteQueryKey/delete | Supprime la clé de requête. |
> | Action | Microsoft.Search/searchServices/listAdminKeys/action | Lit les clés d’administration. |
> | Action | Microsoft.Search/searchServices/listQueryKeys/action | Retourne la liste des clés d’API de requête pour le service Recherche Azure donné. |
> | Action | Microsoft.Search/searchServices/privateEndpointConnectionProxies/delete | Supprime un proxy de connexion de point de terminaison privé existant |
> | Action | Microsoft.Search/searchServices/privateEndpointConnectionProxies/read | Retourne la liste de proxys de connexion de point de terminaison privé ou obtient les propriétés pour du proxy de connexion de point de terminaison privé spécifié |
> | Action | Microsoft.Search/searchServices/privateEndpointConnectionProxies/validate/action | Valide un appel de création de connexion de point de terminaison privé du côté NRP |
> | Action | Microsoft.Search/searchServices/privateEndpointConnectionProxies/write | Crée un proxy de connexion de point de terminaison privé avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le proxy de connexion de point de terminaison privé spécifié |
> | Action | Microsoft.Search/searchServices/read | Lit le service de recherche. |
> | Action | Microsoft.Search/searchServices/regenerateAdminKey/action | Régénère la clé d’administration. |
> | Action | Microsoft.Search/searchServices/start/action | Démarre le service de recherche. |
> | Action | Microsoft.Search/searchServices/stop/action | Arrête le service de recherche. |
> | Action | Microsoft.Search/searchServices/write | Crée ou met à jour le service de recherche. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Applique les règles de renforcement du trafic en créant les règles de sécurité correspondantes sur le ou les groupes de sécurité réseau donnés |
> | Action | Microsoft.Security/adaptiveNetworkHardenings/read | Obtient les recommandations de renforcement de réseau adaptatif d’une ressource Azure protégée |
> | Action | Microsoft.Security/advancedThreatProtectionSettings/read | Obtient les paramètres de Protection avancée contre les menaces pour la ressource |
> | Action | Microsoft.Security/advancedThreatProtectionSettings/write | Met à jour les paramètres de Protection avancée contre les menaces pour la ressource |
> | Action | Microsoft.Security/alerts/read | Obtient toutes les alertes de sécurité disponibles |
> | Action | Microsoft.Security/applicationWhitelistings/read | Obtient les listes blanches des applications. |
> | Action | Microsoft.Security/applicationWhitelistings/write | Crée une liste verte des applications ou met à jour une liste verte des applications existante. |
> | Action | Microsoft.Security/assessmentMetadata/read | Obtenir les métadonnées d’évaluation de la sécurité disponibles sur votre abonnement |
> | Action | Microsoft.Security/assessmentMetadata/write | Créer ou mettre à jour des métadonnées d’évaluation de la sécurité |
> | Action | Microsoft.Security/assessments/read | Obtenir des évaluations de la sécurité de votre abonnement |
> | Action | Microsoft.Security/assessments/write | Créer ou mettre à jour des évaluations de la sécurité de votre abonnement |
> | Action | Microsoft.Security/complianceResults/read | Obtient les résultats de conformité pour la ressource |
> | Action | Microsoft.Security/informationProtectionPolicies/read | Obtient les stratégies de protection des informations pour la ressource |
> | Action | Microsoft.Security/informationProtectionPolicies/write | Met à jour les stratégies de protection des informations pour la ressource |
> | Action | Microsoft.Security/locations/alerts/activate/action | Active une alerte de sécurité |
> | Action | Microsoft.Security/locations/alerts/dismiss/action | Ignore une alerte de sécurité. |
> | Action | Microsoft.Security/locations/alerts/read | Obtient toutes les alertes de sécurité disponibles |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Supprimer la stratégie d’accès réseau juste-à-temps |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Initier une demande de stratégie d’accès réseau juste-à-temps |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Obtient les stratégies d’accès réseau immédiat. |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Crée une stratégie d’accès réseau immédiat ou met à jour une stratégie d’accès réseau immédiat existante. |
> | Action | Microsoft.Security/locations/read | Obtient l’emplacement des données de sécurité |
> | Action | Microsoft.Security/locations/tasks/activate/action | Activer une recommandation de sécurité. |
> | Action | Microsoft.Security/locations/tasks/dismiss/action | Ignorer une recommandation de sécurité. |
> | Action | Microsoft.Security/locations/tasks/read | Obtient toutes les recommandations de sécurité disponibles. |
> | Action | Microsoft.Security/locations/tasks/resolve/action | Résout une recommandation de sécurité |
> | Action | Microsoft.Security/locations/tasks/start/action | Démarre une recommandation de sécurité |
> | Action | Microsoft.Security/policies/read | Obtient la stratégie de sécurité. |
> | Action | Microsoft.Security/policies/write | Met à jour la stratégie de sécurité. |
> | Action | Microsoft.Security/pricings/delete | Supprime les paramètres de tarification pour l’étendue |
> | Action | Microsoft.Security/pricings/read | Obtient les paramètres de tarification pour l’étendue |
> | Action | Microsoft.Security/pricings/write | Met à jour les paramètres de tarification pour l’étendue |
> | Action | Microsoft.Security/register/action | Enregistre l’abonnement pour Azure Security Center |
> | Action | Microsoft.Security/securityContacts/delete | Supprime le contact de sécurité |
> | Action | Microsoft.Security/securityContacts/read | Obtient le contact de sécurité |
> | Action | Microsoft.Security/securityContacts/write | Met à jour le contact de sécurité |
> | Action | Microsoft.Security/securitySolutions/delete | Supprime une solution de sécurité. |
> | Action | Microsoft.Security/securitySolutions/read | Obtient les solutions de sécurité. |
> | Action | Microsoft.Security/securitySolutions/write | Crée une solution de sécurité ou met à jour une solution de sécurité existante. |
> | Action | Microsoft.Security/securitySolutionsReferenceData/read | Obtient les données de référence des solutions de sécurité. |
> | Action | Microsoft.Security/securityStatuses/read | Obtient les états d’intégrité de la sécurité des ressources Azure. |
> | Action | Microsoft.Security/securityStatusesSummaries/read | Obtient les résumés d’état de sécurité pour l’étendue |
> | Action | Microsoft.Security/settings/read | Obtient les paramètres pour l’étendue |
> | Action | Microsoft.Security/settings/write | Met à jour les paramètres pour l’étendue |
> | Action | Microsoft.Security/tasks/read | Obtient toutes les recommandations de sécurité disponibles. |
> | Action | Microsoft.Security/unregister/action | Annule l’inscription de l’abonnement dans Azure Security Center |
> | Action | Microsoft.Security/webApplicationFirewalls/delete | Supprime un pare-feu d’applications web. |
> | Action | Microsoft.Security/webApplicationFirewalls/read | Obtient le pare-feu d’applications web. |
> | Action | Microsoft.Security/webApplicationFirewalls/write | Crée un pare-feu d’applications web nouvelles ou met à jour un pare-feu d’applications web existant. |
> | Action | Microsoft.Security/workspaceSettings/connect/action | Modifie les paramètres de reconnexion de l’espace de travail |
> | Action | Microsoft.Security/workspaceSettings/delete | Supprime les paramètres de l’espace de travail |
> | Action | Microsoft.Security/workspaceSettings/read | Obtient les paramètres de l’espace de travail |
> | Action | Microsoft.Security/workspaceSettings/write | Met à jour les paramètres de l’espace de travail |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/delete | Désactive un paramètre de diagnostic |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/read | Lit un paramètre de diagnostic |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/write | Écrit un paramètre de diagnostic |
> | Action | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Lit les catégories d’un paramètre de diagnostic |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.SecurityInsights/Aggregations/read | Obtient des informations agrégées |
> | Action | Microsoft.SecurityInsights/alertRules/actions/delete | Supprime les actions de réponse d’une règle d’alerte |
> | Action | Microsoft.SecurityInsights/alertRules/actions/read | Obtient les actions de réponse d’une règle d’alerte |
> | Action | Microsoft.SecurityInsights/alertRules/actions/write | Met à jour les actions de réponse d’une règle d’alerte |
> | Action | Microsoft.SecurityInsights/alertRules/delete | Supprime des règles d’alerte |
> | Action | Microsoft.SecurityInsights/alertRules/read | Obtient les règles d’alerte |
> | Action | Microsoft.SecurityInsights/alertRules/write | Met à jour des règles d’alerte |
> | Action | Microsoft.SecurityInsights/Bookmarks/delete | Supprime des signets |
> | Action | Microsoft.SecurityInsights/Bookmarks/expand/action | Obtient les entités associées d’une entité par une expansion spécifique |
> | Action | Microsoft.SecurityInsights/Bookmarks/read | Obtient les signets |
> | Action | Microsoft.SecurityInsights/Bookmarks/write | Met à jour les signets |
> | Action | Microsoft.SecurityInsights/cases/comments/read | Obtient les commentaires de cas |
> | Action | Microsoft.SecurityInsights/cases/comments/write | Crée les commentaires de cas |
> | Action | Microsoft.SecurityInsights/cases/delete | Supprime un cas |
> | Action | Microsoft.SecurityInsights/cases/investigations/read | Obtient les investigations de cas |
> | Action | Microsoft.SecurityInsights/cases/investigations/write | Met à jour les métadonnées d’un cas |
> | Action | Microsoft.SecurityInsights/cases/read | Obtient un cas |
> | Action | Microsoft.SecurityInsights/cases/write | Met à jour un cas |
> | Action | Microsoft.SecurityInsights/dataConnectors/delete | Supprime un connecteur de données |
> | Action | Microsoft.SecurityInsights/dataConnectors/read | Obtient les connecteurs de données |
> | Action | Microsoft.SecurityInsights/dataConnectors/write | Met à jour un connecteur de données |
> | Action | Microsoft.SecurityInsights/incidents/comments/read | Obtient les commentaires de l’incident |
> | Action | Microsoft.SecurityInsights/incidents/comments/write | Crée un commentaire sur l’incident |
> | Action | Microsoft.SecurityInsights/incidents/delete | Supprime un incident |
> | Action | Microsoft.SecurityInsights/incidents/read | Obtient un incident |
> | Action | Microsoft.SecurityInsights/incidents/relations/delete | Supprime une relation entre l’incident et des ressources associées |
> | Action | Microsoft.SecurityInsights/incidents/relations/read | Obtient une relation entre l’incident et des ressources associées |
> | Action | Microsoft.SecurityInsights/incidents/relations/write | Met à jour une relation entre l’incident et des ressources associées |
> | Action | Microsoft.SecurityInsights/incidents/write | Met à jour un incident |
> | Action | Microsoft.SecurityInsights/register/action | Inscrit l’abonnement dans Azure Sentinel |
> | Action | Microsoft.SecurityInsights/settings/read | Obtient les paramètres |
> | Action | Microsoft.SecurityInsights/settings/write | Met à jour les paramètres |
> | Action | Microsoft.SecurityInsights/unregister/action | Annule l’inscription de l’abonnement dans Azure Sentinel |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.ServiceBus/checkNameAvailability/action | Vérifie la disponibilité d’un espace de noms sous un abonnement donné. |
> | Action | Microsoft.ServiceBus/checkNamespaceAvailability/action | Vérifie la disponibilité d’un espace de noms sous un abonnement donné. Cette API étant dépréciée, utilisez CheckNameAvailability à la place. |
> | Action | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Supprime les règles de réseau virtuel dans le fournisseur de ressources ServiceBus pour le réseau virtuel spécifié |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/action | Met à jour une règle d’autorisation d’espace de noms. Cette API est déconseillée. Utilisez un appel PUT à la place pour mettre à jour la règle d’autorisation d’espace de noms... Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Supprimer une règle d’autorisation de ressource Namespace. La règle d’autorisation de ressource Namespace par défaut ne peut pas être supprimée.  |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Obtenir la chaîne de connexion à la ressource Namespace. |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/read | Obtenir la liste des descriptions des règles d’autorisation des ressources Namespaces. |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Régénère la clé primaire ou secondaire pour la ressource. |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/write | Créer des règles d’autorisation au niveau d’une ressource Namespace et mettre à jour leurs propriétés. Les droits d’accès aux règles d’autorisation et les clés primaires et secondaires peuvent être mis à jour. |
> | Action | Microsoft.ServiceBus/namespaces/Delete | Supprimer une ressource Namespace |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obtient les clés de règles d’autorisation pour l’espace de noms principal de récupération d’urgence |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obtient les règles d’autorisation de l’espace de noms principal de récupération d’urgence |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Désactive la récupération d’urgence et arrête la réplication des changements de l’espace de noms principal sur l’espace de noms secondaire. |
> | Action | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Vérifie la disponibilité d’un alias d’espace de noms sous un abonnement donné. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Supprime la configuration de récupération d’urgence associée à l’espace de noms. Cette opération peut être appelée uniquement via l’espace de noms principal. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Appelle un basculement de géorécupération d’urgence et reconfigure l’alias d’espace de noms pour qu’il pointe vers l’espace de noms secondaire. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Obtient la configuration de récupération d’urgence associée à l’espace de noms. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Crée ou met à jour la configuration de récupération d’urgence associée à l’espace de noms. |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Supprime le filtre Event Grid associé à l’espace de noms. |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Obtient le filtre Event Grid associé à l’espace de noms. |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Crée ou met à jour le filtre Event Grid associé à l’espace de noms. |
> | Action | Microsoft.ServiceBus/namespaces/eventhubs/read | Obtenir la liste des descriptions de ressources EventHub |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Supprimer une ressource de filtre IP |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Obtenir une ressource de filtre IP |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Créer une ressource de filtre IP |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | Recevoir des messages |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | Envoyer des messages |
> | Action | Microsoft.ServiceBus/namespaces/messagingPlan/read | Obtient le plan de messagerie pour un espace de noms.<br>Cette API est déconseillée.<br>Les propriétés exposées via la ressource MessagingPlan sont déplacées vers la ressource d’espace de noms (parente) dans les versions ultérieures de l’API.<br>Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. |
> | Action | Microsoft.ServiceBus/namespaces/messagingPlan/write | Met à jour le plan de messagerie pour un espace de noms.<br>Cette API est déconseillée.<br>Les propriétés exposées via la ressource MessagingPlan sont déplacées vers la ressource d’espace de noms (parente) dans les versions ultérieures de l’API.<br>Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. |
> | Action | Microsoft.ServiceBus/namespaces/migrate/action | Migre une opération d’espace de noms |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Supprimer la configuration de migration. |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Obtient la configuration de migration qui indique l’état de la migration et des opérations de réplication en attente. |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Annule la migration de l’espace de noms Standard vers l’espace de noms Premium. |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Attribue à l’espace de noms Premium le DNS associé à l’espace de noms Standard, ce qui lance la migration et arrête la synchronisation des ressources de l’espace de noms Standard vers l’espace de noms Premium. |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Crée ou met à jour la configuration de migration. Cette opération démarre la synchronisation des ressources de l’espace de noms Standard vers l’espace de noms Premium. |
> | Action | Microsoft.ServiceBus/namespaces/networkruleset/delete | Supprimer la ressource de règle de réseau virtuel |
> | Action | Microsoft.ServiceBus/namespaces/networkruleset/read | Obtient la ressource NetworkRuleSet |
> | Action | Microsoft.ServiceBus/namespaces/networkruleset/write | Créer une ressource de règle de réseau virtuel |
> | Action | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Supprimer la ressource de règle de réseau virtuel |
> | Action | Microsoft.ServiceBus/namespaces/networkrulesets/read | Obtient la ressource NetworkRuleSet |
> | Action | Microsoft.ServiceBus/namespaces/networkrulesets/write | Créer une ressource de règle de réseau virtuel |
> | Action | Microsoft.ServiceBus/namespaces/operationresults/read | Obtient l’état de l’opération d’espace de noms |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obtenir la liste des descriptions des ressources des paramètres de diagnostics Namespace. |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obtenir la liste des descriptions des ressources des paramètres de diagnostics Namespace. |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obtenir la liste des descriptions des ressources des journaux d’activité Namespace |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obtenir la liste des descriptions de mesures des ressources Namespace |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Met à jour la file d’attente. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. Règles d’autorisation. Utilisez un appel PUT pour mettre à jour la règle d’autorisation. |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Opération de suppression des règles d’autorisation de file d’attente. |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Obtenir la chaîne de connexion à la file d’attente. |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Obtenir la liste des règles d’autorisation de file d’attente. |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Régénère la clé primaire ou secondaire pour la ressource. |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Créer des règles d’autorisation de file d’attente et mettre à jour leurs propriétés. Les droits d’accès des règles d’autorisation peuvent être mis à jour. |
> | Action | Microsoft.ServiceBus/namespaces/queues/Delete | Opération de suppression des ressources de file d’attente. |
> | Action | Microsoft.ServiceBus/namespaces/queues/read | Obtenir la liste des descriptions des ressources de file d’attente. |
> | Action | Microsoft.ServiceBus/namespaces/queues/write | Créer ou mettre à jour des propriétés de file d’attente. |
> | Action | Microsoft.ServiceBus/namespaces/read | Obtenir la liste des descriptions des ressources Namespace. |
> | Action | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Supprime l’espace de noms ACS |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Met à jour la rubrique. Cette opération n’est pas prise en charge sur la version d’API 2017-04-01. Règles d’autorisation. Utilisez un appel PUT pour mettre à jour la règle d’autorisation. |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Opération de suppression des règles d’autorisation de rubrique |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Récupère la chaîne de connexion à la rubrique. |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Récupère la liste des règles d’autorisation de rubrique. |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Régénère la clé primaire ou secondaire pour la ressource. |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Crée des règles d’autorisation de rubrique et met à jour leurs propriétés. Les droits d’accès des règles d’autorisation peuvent être mis à jour. |
> | Action | Microsoft.ServiceBus/namespaces/topics/Delete | Opération de suppression des ressources de rubrique. |
> | Action | Microsoft.ServiceBus/namespaces/topics/read | Obtenir la liste des descriptions des ressources de rubrique. |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Opération de suppression d’une ressource TopicSubscription |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Récupère la liste des descriptions des ressources TopicSubscription. |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Opération de suppression d’une ressource de règle. |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Récupère la liste des descriptions des ressources de règle. |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Crée ou met à jour les propriétés de règle. |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Crée ou met à jour les propriétés TopicSubscription. |
> | Action | Microsoft.ServiceBus/namespaces/topics/write | Créer ou mettre à jour des propriétés de rubrique. |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Supprimer la ressource de règle de réseau virtuel |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Obtient la ressource de règle de réseau virtuel |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Créer une ressource de règle de réseau virtuel |
> | Action | Microsoft.ServiceBus/namespaces/write | Créer une ressource Namespace et mettre à jour ses propriétés. Les balises et la capacité de l’espace de noms sont les propriétés qui peuvent être mises à jour. |
> | Action | Microsoft.ServiceBus/operations/read | Obtient les opérations |
> | Action | Microsoft.ServiceBus/register/action | Inscrit l’abonnement pour le fournisseur de ressources ServiceBus et active la création de ressources ServiceBus. |
> | Action | Microsoft.ServiceBus/sku/read | Obtient la liste des descriptions des ressources de référence (SKU). |
> | Action | Microsoft.ServiceBus/sku/regions/read | Obtient la liste des descriptions de ressources SkuRegions |
> | Action | Microsoft.ServiceBus/unregister/action | Inscrit l’abonnement pour le fournisseur de ressources ServiceBus et active la création de ressources ServiceBus. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.ServiceFabric/clusters/applications/delete | Supprime une application |
> | Action | Microsoft.ServiceFabric/clusters/applications/read | Lit une application |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/delete | Supprime un service |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Lit une partition |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Lit un réplica |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/read | Lit un service |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Lit un état de service |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/write | Crée ou met à jour un service |
> | Action | Microsoft.ServiceFabric/clusters/applications/write | Crée ou met à jour une application |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Supprime un type d’application |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/read | Lit un type d’application |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Supprime une version de type d’application |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Lit une version de type d’application |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Crée ou met à jour une version de type d’application |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/write | Crée ou met à jour un type d’application |
> | Action | Microsoft.ServiceFabric/clusters/delete | Supprime un cluster |
> | Action | Microsoft.ServiceFabric/clusters/nodes/read | Lit un nœud |
> | Action | Microsoft.ServiceFabric/clusters/read | Lit un cluster |
> | Action | Microsoft.ServiceFabric/clusters/statuses/read | Lit un état de cluster |
> | Action | Microsoft.ServiceFabric/clusters/write | Crée ou met à jour un cluster |
> | Action | Microsoft.ServiceFabric/locations/clusterVersions/read | Lit une version de cluster |
> | Action | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Lit une version de cluster pour un environnement spécifique |
> | Action | Microsoft.ServiceFabric/locations/operationresults/read | Lit les résultats d’une opération |
> | Action | Microsoft.ServiceFabric/locations/operations/read | Lit des opérations par emplacement |
> | Action | Microsoft.ServiceFabric/operations/read | Lit des opérations disponibles |
> | Action | Microsoft.ServiceFabric/register/action | Inscrit une action |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.SignalRService/locations/checknameavailability/action | Vérifie si un nom peut être utilisé avec un nouveau service SignalIR |
> | Action | Microsoft.SignalRService/locations/operationresults/signalr/read | Interroge le résultat d’une opération asynchrone basée sur un emplacement |
> | Action | Microsoft.SignalRService/locations/operationStatuses/operationId/read | Interroge l’état d’une opération asynchrone basée sur un emplacement |
> | Action | Microsoft.SignalRService/locations/usages/read | Obtenir les utilisations du quota pour le service Azure SignalR |
> | Action | Microsoft.SignalRService/operationresults/read | Interroge le résultat d’une opération asynchrone au niveau d’un fournisseur |
> | Action | Microsoft.SignalRService/operations/read | Liste les opérations pour le service Azure Signalr. |
> | Action | Microsoft.SignalRService/operationstatus/read | Interroge l’état d’une opération asynchrone au niveau d’un fournisseur |
> | Action | Microsoft.SignalRService/register/action | Enregistre le fournisseur de ressources Microsoft.SignalRService sur un abonnement |
> | Action | Microsoft.SignalRService/SignalR/delete | Supprimer l’intégralité du service SignalR |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Supprime un filtre Event Grid d’un SignalR. |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/read | Obtient les propriétés du filtre Event Grid spécifié ou répertorie tous les filtres Event Grid pour le SignalR spécifié. |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/write | Crée ou met à jour un filtre Event Grid pour un SignalR avec les paramètres spécifiés. |
> | Action | Microsoft.SignalRService/SignalR/listkeys/action | Afficher la valeur des clés d’accès SignalR dans le portail de gestion ou par le biais d’une API |
> | Action | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/delete | Supprime un proxy de connexion de point de terminaison privé |
> | Action | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/read | Lit un proxy de connexion de point de terminaison privé |
> | Action | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/validate/action | Valide un proxy de connexion de point de terminaison privé |
> | Action | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/write | Crée un proxy de connexion de point de terminaison privé |
> | Action | Microsoft.SignalRService/SignalR/privateEndpointConnections/read | Lit une connexion de point de terminaison privé |
> | Action | Microsoft.SignalRService/SignalR/privateEndpointConnections/write | Approuve ou rejette une connexion de point de terminaison privé |
> | Action | Microsoft.SignalRService/SignalR/privateLinkResources/read | Répertorie toutes les ressources de liaison privée SignalR |
> | Action | Microsoft.SignalRService/SignalR/read | Affiche les paramètres et les configurations de SignalR dans le portail de gestion ou par le biais de l’API |
> | Action | Microsoft.SignalRService/SignalR/regeneratekey/action | Changer la valeur des clés d’accès SignalR dans le portail de gestion ou par le biais d’une API |
> | Action | Microsoft.SignalRService/SignalR/restart/action | Redémarrer un service Azure SignalR dans le portail de gestion ou par le biais d’une API. Il y aura un certain temps d’arrêt. |
> | Action | Microsoft.SignalRService/SignalR/write | Modifie les paramètres et les configurations de SignalR dans le portail de gestion ou par le biais de l’API |
> | Action | Microsoft.SignalRService/unregister/action | Annule l’enregistrement du fournisseur de ressources Microsoft.SignalRService sur un abonnement |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Répertorie les artefacts d’application de la définition d’application. |
> | Action | Microsoft.Solutions/applicationDefinitions/delete | Supprime une définition d’application. |
> | Action | Microsoft.Solutions/applicationDefinitions/read | Récupère une liste de définitions d’application. |
> | Action | Microsoft.Solutions/applicationDefinitions/write | Ajoute ou modifie une définition d’application. |
> | Action | Microsoft.Solutions/applications/applicationArtifacts/read | Répertorie les artefacts d’application. |
> | Action | Microsoft.Solutions/applications/delete | Supprime une application. |
> | Action | Microsoft.Solutions/applications/read | Récupère une liste d’applications. |
> | Action | Microsoft.Solutions/applications/refreshPermissions/action | Actualise les autorisations d’application. |
> | Action | Microsoft.Solutions/applications/updateAccess/action | Met à jour l’accès à l’application. |
> | Action | Microsoft.Solutions/applications/write | Crée une application. |
> | Action | Microsoft.Solutions/jitRequests/delete | Supprimer une demande JIT |
> | Action | Microsoft.Solutions/jitRequests/read | Récupère une liste de demandes JIT |
> | Action | Microsoft.Solutions/jitRequests/write | Crée une demande JIT |
> | Action | Microsoft.Solutions/locations/operationStatuses/read | Lit l’état de l’opération pour la ressource. |
> | Action | Microsoft.Solutions/operations/read | Obtient la liste des opérations. |
> | Action | Microsoft.Solutions/register/action | Inscrit à Solutions. |
> | Action | Microsoft.Solutions/unregister/action | Annule l’inscription de Solutions. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Sql/checkNameAvailability/action | Vérifie si le nom du serveur spécifié est disponible pour l’approvisionnement dans le monde entier pour un abonnement donné. |
> | Action | Microsoft.Sql/instancePools/delete | Supprime un pool d’instances |
> | Action | Microsoft.Sql/instancePools/read | Obtient un pool d’instances |
> | Action | Microsoft.Sql/instancePools/usages/read | Obtient les informations d’utilisation d’un pool d’instances |
> | Action | Microsoft.Sql/instancePools/write | Crée ou met à jour un pool d’instances |
> | Action | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Récupère le résultat de l’opération de configuration de la stratégie d’audit des objets blob de serveur. |
> | Action | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Récupérer le résultat l’opération de configuration de la stratégie d’audit des objets blob de serveur |
> | Action | Microsoft.Sql/locations/capabilities/read | Obtient les capacités pour cet abonnement dans un emplacement donné |
> | Action | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Obtient l’état d’une opération de base de données. |
> | Action | Microsoft.Sql/locations/databaseOperationResults/read | Obtient l’état d’une opération de base de données. |
> | Action | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Obtient les opérations en cours d’exécution sur le serveur supprimé |
> | Action | Microsoft.Sql/locations/deletedServerOperationResults/read | Obtient les opérations en cours d’exécution sur le serveur supprimé |
> | Action | Microsoft.Sql/locations/deletedServers/read | Retourne la liste des serveurs supprimés ou obtient les propriétés du serveur supprimé spécifié. |
> | Action | Microsoft.Sql/locations/deletedServers/recover/action | Récupère un serveur supprimé |
> | Action | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Obtient l’opération asynchrone Azure pour une opération asynchrone de pool élastique |
> | Action | Microsoft.Sql/locations/elasticPoolOperationResults/read | Obtient le résultat d’une opération de pool élastique. |
> | Action | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Obtient les opérations en cours d’exécution sur le protecteur de chiffrement transparent des données |
> | Action | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Obtient les opérations en cours d’exécution sur le protecteur de chiffrement transparent des données |
> | Action | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Récupère le résultat de l’opération de configuration de la stratégie d’audit des objets blob de serveur. |
> | Action | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Récupère le résultat de l’opération de configuration de la stratégie d’audit des objets blob de serveur. |
> | Action | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Obtient l’état d’une opération de règle de pare-feu. |
> | Action | Microsoft.Sql/locations/firewallRulesOperationResults/read | Obtient l’état d’une opération de règle de pare-feu. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/delete | Supprime un groupe de basculement d’instance. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Exécute un basculement planifié dans un groupe de basculement d’instance existant. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Exécute un basculement forcé dans un groupe de basculement d’instance existant. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/read | Retourne la liste des groupes de basculement d’instance ou obtient les propriétés du groupe de basculement d’instance spécifié. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/write | Crée un groupe de basculement d’instance avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le groupe de basculement d’instance spécifié. |
> | Action | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Obtient l’état d’une opération de pool d’instances |
> | Action | Microsoft.Sql/locations/instancePoolOperationResults/read | Obtient le résultat d’une opération de pool d’instances |
> | Action | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Retourne les détails d’une opération asynchrone Azure de point de terminaison d’interface spécifique |
> | Action | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Retourne les détails d’une opération de profil de point de terminaison d’interface spécifique |
> | Action | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Obtient l’état d’une opération d’un agent de travail. |
> | Action | Microsoft.Sql/locations/jobAgentOperationResults/read | Obtient le résultat d’une opération d’un agent de travail. |
> | Action | Microsoft.Sql/locations/longTermRetentionBackups/read | Répertorier les sauvegardes de rétention à long terme pour chaque base de données sur chaque serveur dans un emplacement |
> | Action | Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read | Retourne une liste de sauvegardes LTR d’instance gérée pour un emplacement spécifique  |
> | Action | Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete | Supprime une sauvegarde LTR pour une base de données d’instance gérée |
> | Action | Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read | Retourne une liste de sauvegardes LTR pour une base de données d’instance gérée |
> | Action | Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read | Retourne une liste de sauvegardes LTR d’instance gérée pour une instance gérée spécifique |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Répertorier les sauvegardes de rétention à long terme pour chaque base de données sur un serveur |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Supprimer une sauvegarde de rétention à long terme |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Répertorier les sauvegardes de rétention à long terme pour une base de données |
> | Action | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Achève l’opération de restauration de base de données managée |
> | Action | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Obtient les opérations en cours d’exécution sur le protecteur de chiffrement de l’instance de chiffrement transparent des données |
> | Action | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Obtient les opérations en cours d’exécution sur le protecteur de chiffrement de l’instance de chiffrement transparent des données |
> | Action | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Obtient les opérations en cours d’exécution sur les clés d’instance de chiffrement transparent des données |
> | Action | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Obtient les opérations en cours d’exécution sur les clés d’instance de chiffrement transparent des données |
> | Action | Microsoft.Sql/locations/managedInstanceLongTermRetentionPolicyAzureAsyncOperation/read | Obtient l’état d’une opération de stratégie de rétention à long terme pour une base de données managée |
> | Action | Microsoft.Sql/locations/managedInstanceLongTermRetentionPolicyOperationResults/read | Obtient l’état d’une opération de stratégie de rétention à long terme pour une base de données managée |
> | Action | Microsoft.Sql/locations/managedShortTermRetentionPolicyOperationResults/read | Obtient l’état d’une opération de stratégie de rétention à court terme |
> | Action | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Obtient les opérations en cours d’exécution sur le chiffrement transparent des données de base de données managée |
> | Action | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Obtient les opérations en cours d’exécution sur le chiffrement transparent des données de base de données managée |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Obtient le résultat d’une opération de connexion de point de terminaison privé |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Obtient le résultat d’une opération de connexion de point de terminaison privé |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Obtient le résultat d’une opération proxy de connexion de point de terminaison privé |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Obtient le résultat d’une opération proxy de connexion de point de terminaison privé |
> | Action | Microsoft.Sql/locations/read | Obtient les emplacements disponibles pour un abonnement spécifié |
> | Action | Microsoft.Sql/locations/serverAdministratorAzureAsyncOperation/read | Résultats de l'opération administrateur asynchrone Server Azure Active Directory |
> | Action | Microsoft.Sql/locations/serverAdministratorOperationResults/read | Résultats de l'opération administrateur Server Azure Active Directory |
> | Action | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Obtient les opérations en cours d’exécution sur les clés de serveur de chiffrement transparent des données |
> | Action | Microsoft.Sql/locations/serverKeyOperationResults/read | Obtient les opérations en cours d’exécution sur les clés de serveur de chiffrement transparent des données |
> | Action | Microsoft.Sql/locations/shortTermRetentionPolicyOperationResults/read | Obtient l’état d’une opération de stratégie de rétention à court terme |
> | Action | Microsoft.Sql/locations/syncAgentOperationResults/read | Récupère le résultat de l’opération de ressource de l’agent de synchronisation |
> | Action | Microsoft.Sql/locations/syncDatabaseIds/read | Récupère les ID de base de données de synchronisation pour une région et un abonnement particuliers |
> | Action | Microsoft.Sql/locations/syncGroupOperationResults/read | Récupère le résultat de l’opération de ressource de groupe de synchronisation |
> | Action | Microsoft.Sql/locations/syncMemberOperationResults/read | Récupère le résultat de l’opération de ressource de membre de synchronisation |
> | Action | Microsoft.Sql/locations/transparentDataEncryptionAzureAsyncOperation/read | Obtient les opérations en cours d’exécution sur le chiffrement transparent des données de base de données logique |
> | Action | Microsoft.Sql/locations/transparentDataEncryptionOperationResults/read | Obtient les opérations en cours d’exécution sur le chiffrement transparent des données de base de données logique |
> | Action | Microsoft.Sql/locations/usages/read | Obtient une collection de métriques d’utilisation pour cet abonnement dans un emplacement |
> | Action | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Retourne les résultats de l’opération asynchrone Azure effectuée selon les règles de réseau virtuel spécifiées  |
> | Action | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Retourne les détails de l’opération effectuée selon les règles de réseau virtuel spécifiées  |
> | Action | Microsoft.Sql/managedInstances/administrators/delete | Supprime un administrateur existant de l’instance gérée. |
> | Action | Microsoft.Sql/managedInstances/administrators/read | Obtient la liste des administrateurs de l’instance gérée. |
> | Action | Microsoft.Sql/managedInstances/administrators/write | Crée ou met à jour l’administrateur d’instance gérée avec les paramètres spécifiés. |
> | Action | Microsoft.Sql/managedInstances/databases/backupLongTermRetentionPolicies/read | Obtient une stratégie de rétention à long terme pour une base de données managée |
> | Action | Microsoft.Sql/managedInstances/databases/backupLongTermRetentionPolicies/write | Met à jour une stratégie de rétention à long terme pour une base de données managée |
> | Action | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Obtient une stratégie de rétention à court terme pour une base de données managée |
> | Action | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Met à jour une stratégie de rétention à court terme pour une base de données managée |
> | Action | Microsoft.Sql/managedInstances/databases/columns/read | Retourne une liste de colonnes pour une base de données managée |
> | Action | Microsoft.Sql/managedInstances/databases/completeRestore/action | Achève l’opération de restauration de base de données managée |
> | Action | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Affiche les étiquettes de sensibilité d’une base de données spécifiée |
> | Action | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | Met à jour les étiquettes de sensibilité par lot |
> | Action | Microsoft.Sql/managedInstances/databases/delete | Supprime une base de données managée existante |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Récupère le paramètre de diagnostic pour la ressource. |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Obtient les journaux disponibles pour les bases de données d’une instance managée |
> | Action | Microsoft.Sql/managedInstances/databases/read | Obtient une base de données managée existante |
> | Action | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Affiche les étiquettes de sensibilité d’une base de données spécifiée |
> | Action | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/write | Met à jour les étiquettes de sensibilité recommandées par lot |
> | Action | Microsoft.Sql/managedInstances/databases/restoreDetails/read | Retourne les détails de la restauration de base de données managée pendant que la restauration est en cours. |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/read | Obtient un schéma de base de données managée. (schéma uniquement) |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Obtient une colonne de base de données managée (schéma uniquement) |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Supprime l’étiquette de sensibilité d’une colonne donnée |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Désactive les recommandations de sensibilité sur une colonne donnée |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Active les recommandations de sensibilité sur une colonne donnée |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Obtient l’étiquette de sensibilité d’une colonne donnée |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Crée ou met à jour l’étiquette de sensibilité d’une colonne donnée |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Obtient une table de base de données managée (schéma uniquement) |
> | Action | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Récupère la liste des stratégies de détection des menaces de base de données managée configurées pour un serveur donné |
> | Action | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Modifie la stratégie de détection des menaces pour une base de données managée spécifiée |
> | Action | Microsoft.Sql/managedInstances/databases/securityEvents/read | Récupère les événements de sécurité de base de données managée |
> | Action | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Affiche les étiquettes de sensibilité d’une base de données spécifiée |
> | Action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Récupère les détails de chiffrement transparent des données sur une base de données managée spécifiée |
> | Action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Modifie le chiffrement transparent des données pour une base de données managée spécifiée |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Supprime l’évaluation des vulnérabilités d’une base de données spécifiée |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Récupérer les stratégies d’évaluation de vulnérabilité sur une base de données spécifiée |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Supprime la ligne de base de la règle d’évaluation des vulnérabilités pour une base de données spécifiée |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Obtient la ligne de base de la règle d’évaluation des vulnérabilités pour une base de données spécifiée |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Modifie la ligne de base de la règle d’évaluation des vulnérabilités pour une base de données spécifiée |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Convertit un résultat d’analyse existant en un format lisible. S’il existe déjà, rien ne se passe |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Exécuter l’analyse de la base de données d’évaluation de la vulnérabilité. |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Retourne la liste des enregistrements d’analyse d’évaluation des vulnérabilités de la base de données ou obtient l’enregistrement d’analyse pour l’ID d’analyse spécifié. |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Modifie l’évaluation des vulnérabilités pour une base de données |
> | Action | Microsoft.Sql/managedInstances/databases/write | Crée une base de données ou met à jour une base de données existante. |
> | Action | Microsoft.Sql/managedInstances/delete | Supprime une instance gérée existante. |
> | Action | Microsoft.Sql/managedInstances/encryptionProtector/read | Retourne la liste des protecteurs de chiffrement de serveur ou obtient les propriétés pour le protecteur du chiffrement du serveur spécifié. |
> | Action | Microsoft.Sql/managedInstances/encryptionProtector/revalidate/action | Met à jour les propriétés pour le protecteur du chiffrement du serveur spécifié. |
> | Action | Microsoft.Sql/managedInstances/encryptionProtector/write | Met à jour les propriétés pour le protecteur du chiffrement du serveur spécifié. |
> | Action | Microsoft.Sql/managedInstances/inaccessibleManagedDatabases/read | Obtient une liste des bases de données managées inaccessibles dans une instance gérée |
> | Action | Microsoft.Sql/managedInstances/keys/delete | Supprime une clé Azure SQL Managed Instance existante. |
> | Action | Microsoft.Sql/managedInstances/keys/read | Retourne la liste des clés d’instance gérée ou obtient les propriétés de la clé d’instance gérée spécifiée. |
> | Action | Microsoft.Sql/managedInstances/keys/write | Crée une clé avec les paramètres spécifiés ou met à jour les propriétés ou balises pour la clé d’instance gérée spécifiée. |
> | Action | Microsoft.Sql/managedInstances/metricDefinitions/read | Obtient les définitions de métrique de l’instance gérée |
> | Action | Microsoft.Sql/managedInstances/metrics/read | Obtient les métriques de l’instance gérée |
> | Action | Microsoft.Sql/managedInstances/operations/cancel/action | Annule une opération asynchrone d’instance gérée Azure SQL qui n’est pas encore terminée. |
> | Action | Microsoft.Sql/managedInstances/operations/read | Obtient les opérations de l’instance gérée |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Récupère le paramètre de diagnostic pour la ressource. |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Obtient les journaux d’activité disponibles pour les instances gérées |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Retourne les types de métriques disponibles pour les instances gérées |
> | Action | Microsoft.Sql/managedInstances/read | Retourne la liste des instances gérées ou obtient les propriétés de l’instance gérée spécifiée. |
> | Action | Microsoft.Sql/managedInstances/recoverableDatabases/read | Retourne une liste de bases de données managées récupérables |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Obtient une stratégie de rétention à court terme pour une base de données managée supprimée |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Met à jour une stratégie de rétention à court terme pour une base de données managée supprimée |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Retourne une liste de bases de données gérées supprimées pouvant être restaurées |
> | Action | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Récupère la liste des stratégies de détection des menaces de serveur managé configurées pour un serveur donné |
> | Action | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Modifie la stratégie de détection des menaces pour un serveur géré spécifié |
> | Action | Microsoft.Sql/managedInstances/tdeCertificates/action | Créer/mettre à jour le certificat TDE. |
> | Action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Supprimer l’évaluation des vulnérabilités d’une instance gérée spécifiée |
> | Action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Récupérer les stratégies d’évaluation de vulnérabilité sur une instance gérée spécifiée |
> | Action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Modifier l’évaluation des vulnérabilités d’une instance gérée spécifiée |
> | Action | Microsoft.Sql/managedInstances/write | Crée une instance gérée avec les paramètres spécifiés ou met à jour les propriétés ou balises pour l’instance gérée spécifiée. |
> | Action | Microsoft.Sql/operations/read | Obtient les opérations REST disponibles |
> | Action | Microsoft.Sql/privateEndpointConnectionsApproval/action | Détermine si l’utilisateur est autorisé à approuver une connexion de point de terminaison privé |
> | Action | Microsoft.Sql/register/action | Inscrit l’abonnement pour le fournisseur de ressources Microsoft SQL Database et permet la création de bases de données Microsoft SQL. |
> | Action | Microsoft.Sql/servers/administratorOperationResults/read | Obtient les opérations en cours sur les administrateurs du serveur |
> | Action | Microsoft.Sql/servers/administrators/delete | Supprime un objet d’administrateur Azure Active Directory spécifique |
> | Action | Microsoft.Sql/servers/administrators/read | Obtient un objet d’administrateur Azure Active Directory spécifique |
> | Action | Microsoft.Sql/servers/administrators/write | Ajoute ou met à jour un objet d’administrateur Azure Active Directory spécifique |
> | Action | Microsoft.Sql/servers/advisors/read | Renvoie la liste des conseillers disponibles pour le serveur |
> | Action | Microsoft.Sql/servers/advisors/recommendedActions/read | Renvoie la liste des actions recommandées du conseiller spécifié pour le serveur |
> | Action | Microsoft.Sql/servers/advisors/recommendedActions/write | Appliquer l’action recommandée sur le serveur |
> | Action | Microsoft.Sql/servers/advisors/write | Met à jour l’état d’exécution automatique d’un conseiller au niveau du serveur. |
> | Action | Microsoft.Sql/servers/auditingPolicies/read | Récupérer les détails de la stratégie d’audit de table du serveur par défaut configurée sur un serveur donné |
> | Action | Microsoft.Sql/servers/auditingPolicies/write | Modifier l’audit de table du serveur par défaut pour un serveur donné |
> | Action | Microsoft.Sql/servers/auditingSettings/operationResults/read | Récupérer le résultat l’opération de configuration de la stratégie d’audit des objets blob de serveur |
> | Action | Microsoft.Sql/servers/auditingSettings/read | Récupérer les détails de la stratégie d’audit des objets blob de serveur configurée sur un serveur donné |
> | Action | Microsoft.Sql/servers/auditingSettings/write | Modifier l’audit des objets blob de serveur pour un serveur donné |
> | Action | Microsoft.Sql/servers/automaticTuning/read | Retourne les paramètres de réglage automatique pour le serveur |
> | Action | Microsoft.Sql/servers/automaticTuning/write | Met à jour les paramètres de réglage automatique pour le serveur et retourne les paramètres mis à jour |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Supprime un coffre d’archivage de sauvegarde existant. |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Cette opération est utilisée pour obtenir un coffre de rétention de sauvegarde à long terme. Retourne des informations sur le coffre inscrit sur ce serveur |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Cette opération permet d’inscrire un coffre de rétention de sauvegarde à long terme auprès d’un serveur |
> | Action | Microsoft.Sql/servers/communicationLinks/delete | Supprime un lien de communication de serveur existant. |
> | Action | Microsoft.Sql/servers/communicationLinks/read | Retourne la liste des liens de communication d’un serveur spécifié. |
> | Action | Microsoft.Sql/servers/communicationLinks/write | Crée ou met à jour un lien de communication du serveur. |
> | Action | Microsoft.Sql/servers/connectionPolicies/read | Retourne la liste des stratégies de connexion d’un serveur spécifié. |
> | Action | Microsoft.Sql/servers/connectionPolicies/write | Crée ou met à jour une stratégie de connexion du serveur. |
> | Action | Microsoft.Sql/servers/databases/advisors/read | Retourne la liste des conseillers disponibles pour la base de données. |
> | Action | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Retourne la liste des actions recommandées du conseiller spécifié pour la base de données. |
> | Action | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Applique l’action recommandée sur la base de données. |
> | Action | Microsoft.Sql/servers/databases/advisors/write | Met à jour l’état d’exécution automatique d’un conseiller au niveau de la base de données. |
> | Action | Microsoft.Sql/servers/databases/auditingPolicies/read | Récupère les détails de la stratégie d’audit des tables configurée dans une base de données spécifique. |
> | Action | Microsoft.Sql/servers/databases/auditingPolicies/write | Modifie la stratégie d’audit des tables pour une base de données spécifique. |
> | Action | Microsoft.Sql/servers/databases/auditingSettings/read | Récupère les détails de la stratégie d’audit d’objets blob configurée dans une base de données spécifique. |
> | Action | Microsoft.Sql/servers/databases/auditingSettings/write | Modifie la stratégie d’audit d’objets blob pour une base de données spécifique. |
> | Action | Microsoft.Sql/servers/databases/auditRecords/read | Récupère les enregistrements d’audit d’objet blob de base de données. |
> | Action | Microsoft.Sql/servers/databases/automaticTuning/read | Retourne les paramètres de réglage automatique pour une base de données |
> | Action | Microsoft.Sql/servers/databases/automaticTuning/write | Met à jour les paramètres de réglage automatique pour une base de données et retourne les paramètres mis à jour |
> | Action | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Obtient l’état d’une opération de base de données. |
> | Action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Retourne la liste des stratégies d’archivage de sauvegarde d’une base de données spécifiée. |
> | Action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Crée ou met à jour une stratégie d’archivage de sauvegarde de base de données. |
> | Action | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Obtient une stratégie de rétention à court terme pour une base de données managée |
> | Action | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Met à jour une stratégie de rétention à court terme pour une base de données |
> | Action | Microsoft.Sql/servers/databases/columns/read | Retourne une liste de colonnes pour une base de données |
> | Action | Microsoft.Sql/servers/databases/connectionPolicies/read | Récupère les détails de la stratégie de connexion configurée d’une base de données spécifique. |
> | Action | Microsoft.Sql/servers/databases/connectionPolicies/write | Modifie la stratégie de connexion d’une base de données spécifique. |
> | Action | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Affiche les étiquettes de sensibilité d’une base de données spécifiée |
> | Action | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | Met à jour les étiquettes de sensibilité par lot |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Retourne la liste des stratégies de masquage des données de base de données. |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Supprime une règle de stratégie de masquage de données pour une base de données spécifique |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Récupérer les détails de la règle de stratégie de masquage des données configurée sur une base de données spécifique |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Modifier la règle de stratégie de masquage des données pour une base de données spécifique |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Modifier la stratégie de masquage des données pour une base de données spécifique |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Retourne les informations d’étape de requête distribuée de la requête de l’entrepôt de données pour l’ID d’étape sélectionné. |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Retourne les informations de requête de distribution d’entrepôt de données pour l’ID de requête sélectionné. |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Récupère les activités d’utilisateur d’une instance SQL Data Warehouse qui inclut les requêtes en cours d’exécution et suspendues |
> | Action | Microsoft.Sql/servers/databases/delete | Supprime une base de données existante. |
> | Action | Microsoft.Sql/servers/databases/export/action | Exporte Azure SQL Database |
> | Action | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Récupère les détails de la stratégie étendue d’audit d’objets blob configurée dans une base de données spécifique |
> | Action | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Modifie la stratégie étendue d’audit d’objets blob pour une base de données spécifique |
> | Action | Microsoft.Sql/servers/databases/extensions/importExtensionOperationResults/read | Permet d'obtenir les opérations d'importation en cours |
> | Action | Microsoft.Sql/servers/databases/extensions/read | Obtient une collection d’extensions pour la base de données. |
> | Action | Microsoft.Sql/servers/databases/extensions/write | Modifie l’extension d’une base de données spécifiée |
> | Action | Microsoft.Sql/servers/databases/failover/action | Bascule la base de données initiée par le client. |
> | Action | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Récupère les stratégies de géosauvegarde pour une base de données spécifiée |
> | Action | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Crée ou met à jour une stratégie de géosauvegarde de base de données |
> | Action | Microsoft.Sql/servers/databases/importExportOperationResults/read | Obtient les opérations d’importation/exportation en cours |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Obtient une liste des fenêtres de maintenance disponibles pour une base de données sélectionnée. |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindows/read | Obtient les paramètres des fenêtres de maintenance pour une base de données sélectionnée. |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindows/write | Définit les paramètres des fenêtres de maintenance pour une base de données sélectionnée. |
> | Action | Microsoft.Sql/servers/databases/metricDefinitions/read | Renvoie les types de mesures disponibles pour les bases de données |
> | Action | Microsoft.Sql/servers/databases/metrics/read | Retourne les métriques pour les bases de données |
> | Action | Microsoft.Sql/servers/databases/move/action | Modifie le nom d’une base de données existante. |
> | Action | Microsoft.Sql/servers/databases/operationResults/read | Obtient l’état d’une opération de base de données. |
> | Action | Microsoft.Sql/servers/databases/operations/cancel/action | Annule une opération asynchrone de base de données Azure SQL qui n’est pas encore terminée. |
> | Action | Microsoft.Sql/servers/databases/operations/read | Retourne la liste des opérations effectuées sur la base de données |
> | Action | Microsoft.Sql/servers/databases/pause/action | Suspend la base de données de l’entrepôt de données Azure SQL |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Récupère le paramètre de diagnostic pour la ressource. |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Obtient les journaux d’activité disponibles pour les bases de données |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Renvoie les types de mesures disponibles pour les bases de données |
> | Action | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Retourne la collection de textes de requêtes qui correspondent aux paramètres spécifiés. |
> | Action | Microsoft.Sql/servers/databases/queryStore/read | Retourne les valeurs actuelles des paramètres du magasin des requêtes pour la base de données. |
> | Action | Microsoft.Sql/servers/databases/queryStore/write | Met à jour les paramètres de magasin de requêtes pour la base de données. |
> | Action | Microsoft.Sql/servers/databases/read | Retourner la liste des bases de données ou obtenir les propriétés pour la base de données spécifiée. |
> | Action | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Affiche les étiquettes de sensibilité d’une base de données spécifiée |
> | Action | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/write | Met à jour les étiquettes de sensibilité recommandées par lot |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/delete | Mettre fin à la relation de réplication volontairement avec une perte de données potentielle |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Bascule après synchronisation toutes les modifications de la base de données principale, transformant celle-ci en base de données principale dans la relation de réplication, et la base de données principale distante en base de données secondaire |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Bascule immédiatement avec perte de données potentielle, transformant cette base de données en base de données principale dans la relation de réplication, et la base de données principale distante en base de données secondaire |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/read | Retourner la liste des liens de réplication ou obtenir les propriétés pour les liens de réplication spécifiés. |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Mettre fin à la relation de réplication volontairement ou après la synchronisation avec le partenaire |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Mettre à jour le mode de réplication pour le lien vers un mode synchrone ou asynchrone |
> | Action | Microsoft.Sql/servers/databases/restorePoints/action | Créer un nouveau point de restauration |
> | Action | Microsoft.Sql/servers/databases/restorePoints/delete | Supprime un point de restauration pour la base de données. |
> | Action | Microsoft.Sql/servers/databases/restorePoints/read | Retourne les points de restauration pour la base de données. |
> | Action | Microsoft.Sql/servers/databases/resume/action | Reprend la base de données de l’entrepôt de données Azure SQL |
> | Action | Microsoft.Sql/servers/databases/schemas/read | Obtient un schéma de base de données (schéma uniquement). |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Obtient une colonne de base de données (schéma uniquement). |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Supprime l’étiquette de sensibilité d’une colonne donnée |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Désactive les recommandations de sensibilité sur une colonne donnée |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Active les recommandations de sensibilité sur une colonne donnée |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Obtient l’étiquette de sensibilité d’une colonne donnée |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Crée ou met à jour l’étiquette de sensibilité d’une colonne donnée |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/read | Obtient un tableau de base de données (schéma uniquement). |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Récupère la liste des recommandations d’index d’une base de données. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Applique une recommandation d’index. |
> | Action | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Récupère la liste des stratégies de détection des menaces de base de données configurées pour un serveur donné |
> | Action | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Modifie la stratégie de détection des menaces pour une base de données spécifiée |
> | Action | Microsoft.Sql/servers/databases/securityMetrics/read | Obtient une collection de métriques de sécurité de base de données |
> | Action | Microsoft.Sql/servers/databases/sensitivityLabels/read | Affiche les étiquettes de sensibilité d’une base de données spécifiée |
> | Action | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Retourne une suggestion sur la montée ou la descente en puissance d’une base de données en fonction des statistiques d’exécution de requête afin d’améliorer les performances ou de réduire les coûts. |
> | Action | Microsoft.Sql/servers/databases/skus/read | Obtient une collection de références (SKU) disponibles pour une base de données |
> | Action | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Annule la synchronisation du groupe de synchronisation |
> | Action | Microsoft.Sql/servers/databases/syncGroups/delete | Supprime un groupe de synchronisation existant. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Retourne la liste des schémas de base de données du hub de synchronisation |
> | Action | Microsoft.Sql/servers/databases/syncGroups/logs/read | Retourne la liste des journaux d’activité du groupe de synchronisation |
> | Action | Microsoft.Sql/servers/databases/syncGroups/read | Retourne la liste des groupes de synchronisation ou obtient les propriétés du groupe de synchronisation spécifié. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Actualise le schéma de base de données du hub de synchronisation |
> | Action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Récupère le résultat de l’opération d’actualisation du schéma du hub de synchronisation |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Supprime un membre de synchronisation existant. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Retourne la liste des membres de synchronisation ou obtient les propriétés du membre de synchronisation spécifié. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Actualise le schéma du membre de synchronisation |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Récupère le résultat de l’opération d’actualisation du schéma du membre de synchronisation |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Retourne la liste des schémas de base de données du membre de synchronisation |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Crée un membre de synchronisation avec les paramètres spécifiés ou met à jour les propriétés du membre de synchronisation spécifié. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Déclenche la synchronisation du groupe de synchronisation |
> | Action | Microsoft.Sql/servers/databases/syncGroups/write | Crée un groupe de synchronisation avec les paramètres spécifiés ou met à jour les propriétés du groupe de synchronisation spécifié. |
> | Action | Microsoft.Sql/servers/databases/topQueries/queryText/action | Retourne le texte Transact-SQL pour l’ID de requête sélectionné |
> | Action | Microsoft.Sql/servers/databases/topQueries/read | Renvoie les statistiques d’exécution agrégées de la requête sélectionnée dans la période choisie |
> | Action | Microsoft.Sql/servers/databases/topQueries/statistics/read | Renvoie les statistiques d’exécution agrégées de la requête sélectionnée dans la période choisie |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Obtient les opérations en cours d’exécution sur le chiffrement transparent des données |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Récupère les détails du Transparent Data Encryption de base de données logique sur une base de données managée spécifiée |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Modifie le Transparent Data Encryption de base de données pour une base de données logique spécifiée |
> | Action | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Met à niveau la base de données de l’entrepôt de données Azure SQL |
> | Action | Microsoft.Sql/servers/databases/usages/read | Obtient les informations d’utilisation d’Azure SQL Database |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Supprime l’évaluation des vulnérabilités d’une base de données spécifiée |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Récupérer les stratégies d’évaluation de vulnérabilité sur une base de données spécifiée |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Supprime la ligne de base de la règle d’évaluation des vulnérabilités pour une base de données spécifiée |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Obtient la ligne de base de la règle d’évaluation des vulnérabilités pour une base de données spécifiée |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Modifie la ligne de base de la règle d’évaluation des vulnérabilités pour une base de données spécifiée |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Convertit un résultat d’analyse existant en un format lisible. S’il existe déjà, rien ne se passe |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Exécuter l’analyse de la base de données d’évaluation de la vulnérabilité. |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Retourne la liste des enregistrements d’analyse d’évaluation des vulnérabilités de la base de données ou obtient l’enregistrement d’analyse pour l’ID d’analyse spécifié. |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Modifie l’évaluation des vulnérabilités pour une base de données |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Exécuter l’analyse de la base de données d’évaluation de la vulnérabilité. |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Récupère le résultat de l’opération d’analyse d’évaluation des vulnérabilités de la base de données |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Récupère les détails de l’évaluation des vulnérabilités configurée sur une base de données spécifique |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Modifie l’évaluation des vulnérabilités pour une base de données |
> | Action | Microsoft.Sql/servers/databases/workloadGroups/delete | Supprime un groupe de charge de travail spécifique. |
> | Action | Microsoft.Sql/servers/databases/workloadGroups/read | Répertorie les groupes de charge de travail d’une base de données sélectionnée. |
> | Action | Microsoft.Sql/servers/databases/workloadGroups/workloadClassifiers/delete | Supprime un classifieur de charge de travail spécifique. |
> | Action | Microsoft.Sql/servers/databases/workloadGroups/workloadClassifiers/read | Répertorie les classifieurs de charge de travail d’une base de données sélectionnée. |
> | Action | Microsoft.Sql/servers/databases/workloadGroups/workloadClassifiers/write | Définit les propriétés d’un classifieur de charge de travail spécifique. |
> | Action | Microsoft.Sql/servers/databases/workloadGroups/write | Définit les propriétés d’un groupe de charge de travail spécifique. |
> | Action | Microsoft.Sql/servers/databases/write | Crée une base de données avec les paramètres spécifiés ou met à jour les propriétés ou balises de la base de données spécifiée. |
> | Action | Microsoft.Sql/servers/delete | Supprime un serveur existant. |
> | Action | Microsoft.Sql/servers/disableAzureADOnlyAuthentication/action | Désactive l’authentification Azure Active Directory uniquement sur un serveur logique |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Supprime une configuration de récupération d’urgence existante pour un serveur spécifié |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Bascule une configuration de récupération d’urgence |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Force le basculement d’une configuration de récupération d’urgence |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Obtient une collection de configurations de récupération d’urgence qui incluent ce serveur |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Modifie la configuration de récupération d’urgence du serveur |
> | Action | Microsoft.Sql/servers/elasticPoolEstimates/read | Renvoie la liste des estimations du pool élastique déjà créée pour ce serveur |
> | Action | Microsoft.Sql/servers/elasticPoolEstimates/write | Crée la nouvelle estimation du pool élastique pour la liste des bases de données fournie |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/read | Retourne la liste des conseillers disponibles pour le pool élastique. |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Retourne la liste des actions recommandées du conseiller spécifié pour le pool élastique. |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Appliquer l’action recommandée sur le pool élastique |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/write | Met à jour l’état d’exécution automatique d’un conseiller au niveau du pool élastique. |
> | Action | Microsoft.Sql/servers/elasticPools/databases/read | Obtient une liste de bases de données pour un pool élastique |
> | Action | Microsoft.Sql/servers/elasticPools/delete | Supprime un pool élastique existant |
> | Action | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Récupérer les activités et les détails sur un pool de bases de données élastique donné |
> | Action | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Récupère les activités et les détails d’une base de données spécifique faisant partie d’un pool de bases de données élastique. |
> | Action | Microsoft.Sql/servers/elasticPools/failover/action | Bascule le pool élastique initié par le client. |
> | Action | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Retourne les types de mesure disponibles pour les pools de bases de données élastiques. |
> | Action | Microsoft.Sql/servers/elasticPools/metrics/read | Retourne des métriques pour des pools de bases de données élastiques |
> | Action | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Annule une opération asynchrone de pool élastique SQL qui n’est pas encore terminée. |
> | Action | Microsoft.Sql/servers/elasticPools/operations/read | Retourne la liste des opérations effectuées sur le pool élastique |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Récupère le paramètre de diagnostic pour la ressource. |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Crée ou met à jour le paramètre de diagnostic pour la ressource |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Retourne les types de mesure disponibles pour les pools de bases de données élastiques. |
> | Action | Microsoft.Sql/servers/elasticPools/read | Récupère les détails d’un pool élastique sur un serveur spécifié |
> | Action | Microsoft.Sql/servers/elasticPools/skus/read | Obtient une collection de références (SKU) disponibles pour un pool élastique |
> | Action | Microsoft.Sql/servers/elasticPools/write | Crée ou modifie les propriétés d’un pool élastique existant |
> | Action | Microsoft.Sql/servers/encryptionProtector/read | Retourne la liste des protecteurs de chiffrement de serveur ou obtient les propriétés pour le protecteur du chiffrement du serveur spécifié. |
> | Action | Microsoft.Sql/servers/encryptionProtector/revalidate/action | Met à jour les propriétés pour le protecteur du chiffrement du serveur spécifié. |
> | Action | Microsoft.Sql/servers/encryptionProtector/write | Met à jour les propriétés pour le protecteur du chiffrement du serveur spécifié. |
> | Action | Microsoft.Sql/servers/extendedAuditingSettings/read | Récupère les détails de la stratégie étendue d’audit des objets blob de serveur configurée sur un serveur spécifié |
> | Action | Microsoft.Sql/servers/extendedAuditingSettings/write | Modifie l’audit étendu des objets blob de serveur pour un serveur spécifié |
> | Action | Microsoft.Sql/servers/failoverGroups/delete | Supprime un groupe de basculement existant. |
> | Action | Microsoft.Sql/servers/failoverGroups/failover/action | Exécute un basculement planifié dans un groupe de basculement existant. |
> | Action | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Exécute un basculement forcé dans un groupe de basculement existant. |
> | Action | Microsoft.Sql/servers/failoverGroups/read | Retourne la liste des groupes de basculement ou obtient les propriétés du groupe de basculement spécifié. |
> | Action | Microsoft.Sql/servers/failoverGroups/write | Crée un groupe de basculement avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le groupe de basculement spécifié. |
> | Action | Microsoft.Sql/servers/firewallRules/delete | Supprime une règle de pare-feu de serveur existante. |
> | Action | Microsoft.Sql/servers/firewallRules/read | Retourne la liste des règles de pare-feu de serveur ou obtient les propriétés pour la règle de pare-feu du serveur spécifié. |
> | Action | Microsoft.Sql/servers/firewallRules/write | Crée une règle de pare-feu de serveur avec les paramètres spécifiés, met à jour les propriétés de la règle spécifiée, ou remplace toutes les règles existantes par des règles de pare-feu de serveur plus récentes. |
> | Action | Microsoft.Sql/servers/import/action | Crée une base de données sur le serveur et déploie le schéma et les données à partir d’un package DacPac. |
> | Action | Microsoft.Sql/servers/importExportOperationResults/read | Obtient les opérations d’importation/exportation en cours |
> | Action | Microsoft.Sql/servers/inaccessibleDatabases/read | Retourne une liste des bases de données inaccessibles dans un serveur logique. |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Supprime le profil de point de terminaison d’interface spécifié |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Retourne les propriétés pour le profil de point de terminaison d’interface spécifié |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Crée un profil de point de terminaison d’interface avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le point de terminaison d’interface spécifié |
> | Action | Microsoft.Sql/servers/jobAgents/delete | Supprime un agent de travail Azure SQL Database |
> | Action | Microsoft.Sql/servers/jobAgents/read | Obtient un agent de travail Azure SQL Database |
> | Action | Microsoft.Sql/servers/jobAgents/write | Crée ou met à jour un agent de travail Azure SQL Database |
> | Action | Microsoft.Sql/servers/keys/delete | Supprime une clé de serveur existante. |
> | Action | Microsoft.Sql/servers/keys/read | Retourne la liste des clés de serveur ou obtient les propriétés de la clé de serveur spécifiée. |
> | Action | Microsoft.Sql/servers/keys/write | Crée une clé avec les paramètres spécifiés ou met à jour les propriétés ou balises pour la clé de serveur spécifiée. |
> | Action | Microsoft.Sql/servers/operationResults/read | Obtient les opérations du serveur en cours |
> | Action | Microsoft.Sql/servers/operations/read | Retourne la liste des opérations effectuées sur le serveur |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Supprime un proxy de connexion de point de terminaison privé existant |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Retourne la liste de proxys de connexion de point de terminaison privé ou obtient les propriétés pour du proxy de connexion de point de terminaison privé spécifié. |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Valide un appel de création de connexion de point de terminaison privé du côté NRP |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Crée un proxy de connexion de point de terminaison privé avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le proxy de connexion de point de terminaison privé spécifié. |
> | Action | Microsoft.Sql/servers/privateEndpointConnections/delete | Supprime une connexion de point de terminaison privé existante |
> | Action | Microsoft.Sql/servers/privateEndpointConnections/read | Retourne la liste de connexions de point de terminaison privé ou obtient les propriétés pour de la connexion de point de terminaison privé spécifiée. |
> | Action | Microsoft.Sql/servers/privateEndpointConnections/write | Approuve ou rejette une connexion de point de terminaison privé existante |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionsApproval/action | Détermine si l’utilisateur est autorisé à approuver une connexion de point de terminaison privé |
> | Action | Microsoft.Sql/servers/privateLinkResources/read | Obtient les ressources de liaison privée pour le serveur SQL correspondant |
> | Action | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Retourne les types de métriques disponibles pour les serveurs |
> | Action | Microsoft.Sql/servers/read | Retourner la liste des serveurs ou obtenir les propriétés pour le serveur spécifié. |
> | Action | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Récupère les mesures pour les pools de bases de données élastiques recommandés pour un serveur donné. |
> | Action | Microsoft.Sql/servers/recommendedElasticPools/read | Récupérer une recommandation pour les pools de bases de données élastiques afin de réduire les coûts ou d’améliorer les performances en fonction de l’utilisation des ressources d’historique |
> | Action | Microsoft.Sql/servers/recoverableDatabases/read | Cette opération est utilisée pour la récupération d’urgence de base de données active pour restaurer la base de données au point de sauvegarde correct connu. Elle retourne des informations sur la dernière sauvegarde correcte, mais ne restaure pas réellement la base de données. |
> | Action | Microsoft.Sql/servers/replicationLinks/read | Retourner la liste des liens de réplication ou obtenir les propriétés pour les liens de réplication spécifiés. |
> | Action | Microsoft.Sql/servers/restorableDroppedDatabases/read | Récupère la liste des bases de données supprimées d’un serveur donné, qui figurent toujours dans la stratégie de rétention. |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Récupère les résultats de l’opération d’écriture de la stratégie de détection des menaces de serveur |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/read | Récupère la liste des stratégies de détection des menaces de serveur configurées pour un serveur donné |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/write | Modifie la stratégie de détection des menaces de serveur pour un serveur donné |
> | Action | Microsoft.Sql/servers/serviceObjectives/read | Récupère la liste des objectifs de niveau de service (également appelés niveaux de performance) disponibles sur un serveur donné. |
> | Action | Microsoft.Sql/servers/syncAgents/delete | Supprime un agent de synchronisation existant. |
> | Action | Microsoft.Sql/servers/syncAgents/generateKey/action | Générer la clé d’inscription de l’agent de synchronisation |
> | Action | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Retourne la liste des bases de données liées à l’agent de synchronisation |
> | Action | Microsoft.Sql/servers/syncAgents/read | Retourne la liste des agents de synchronisation ou obtient les propriétés de l’agent de synchronisation spécifié. |
> | Action | Microsoft.Sql/servers/syncAgents/write | Crée un agent de synchronisation avec les paramètres spécifiés ou met à jour les propriétés de l’agent de synchronisation spécifié. |
> | Action | Microsoft.Sql/servers/tdeCertificates/action | Créer/mettre à jour le certificat TDE. |
> | Action | Microsoft.Sql/servers/usages/read | Renvoyer le quota DTU du serveur et la consommation DTU actuelle par toutes les bases de données au sein du serveur |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/delete | Supprime une règle de réseau virtuel existante |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/read | Retourne la liste des règles de réseau virtuel ou obtient les propriétés de la règle de réseau virtuel spécifiée. |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/write | Crée une règle de réseau virtuel avec les paramètres spécifiés ou met à jour les propriétés ou balises de la règle de réseau virtuel spécifiée. |
> | Action | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Supprimer l’évaluation des vulnérabilités pour un serveur spécifié |
> | Action | Microsoft.Sql/servers/vulnerabilityAssessments/read | Récupérer les stratégies d’évaluation de vulnérabilité sur un serveur spécifié |
> | Action | Microsoft.Sql/servers/vulnerabilityAssessments/write | Modifier l’évaluation des vulnérabilités pour un serveur spécifié |
> | Action | Microsoft.Sql/servers/write | Crée un serveur avec les paramètres spécifiés ou met à jour les propriétés ou balises pour le serveur spécifié. |
> | Action | Microsoft.Sql/unregister/action | Désinscrit l’abonnement pour le fournisseur de ressources Microsoft SQL Database et permet la création de bases de données Microsoft SQL. |
> | Action | Microsoft.Sql/virtualClusters/delete | Supprime un cluster virtuel existant. |
> | Action | Microsoft.Sql/virtualClusters/read | Retourne la liste des clusters virtuels ou obtient les propriétés du serveur virtuel spécifié. |
> | Action | Microsoft.Sql/virtualClusters/write | Met à jour des balises de cluster virtuel. |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.SqlVirtualMachine/locations/availabilityGroupListenerOperationResults/read | Obtient le résultat d’une opération d’écouteur de groupe de disponibilité |
> | Action | Microsoft.SqlVirtualMachine/locations/sqlVirtualMachineGroupOperationResults/read | Obtient le résultat d’une opération de groupe de machines virtuelles SQL |
> | Action | Microsoft.SqlVirtualMachine/locations/sqlVirtualMachineOperationResults/read | Obtient le résultat d’une opération de machine virtuelle SQL |
> | Action | Microsoft.SqlVirtualMachine/operations/read |  |
> | Action | Microsoft.SqlVirtualMachine/register/action | Inscrit un abonnement auprès du fournisseur de ressources Microsoft.SqlVirtualMachine |
> | Action | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/delete | Supprime un écouteur de groupe de disponibilité existant |
> | Action | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/read | Récupère les détails de l’écouteur de groupe de disponibilité SQL sur un groupe de machines virtuelles SQL donné |
> | Action | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/write | Crée ou modifie les propriétés de l’écouteur de groupe de disponibilité SQL existant |
> | Action | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/delete | Supprime le groupe de machines virtuelles SQL existant |
> | Action | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/read | Récupère les détails du groupe de machines virtuelles SQL |
> | Action | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/sqlVirtualMachines/read | Répertorie les machines virtuelles SQL d’un groupe de machines virtuelles SQL particulier |
> | Action | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/write | Crée ou modifie les propriétés d’un groupe de machines virtuelles SQL existant |
> | Action | Microsoft.SqlVirtualMachine/sqlVirtualMachines/delete | Supprimer une machine virtuelle SQL existante |
> | Action | Microsoft.SqlVirtualMachine/sqlVirtualMachines/read | Récupérer les détails de la machine virtuelle SQL |
> | Action | Microsoft.SqlVirtualMachine/sqlVirtualMachines/write | Crée ou modifie les propriétés d’une machine virtuelle SQL existante |
> | Action | Microsoft.SqlVirtualMachine/unregister/action | Désinscrit l’abonnement auprès du fournisseur de ressources Microsoft.SqlVirtualMachine |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Storage/checknameavailability/read | Vérifie que le nom du valide est valide et qu’il n’est pas déjà utilisé. |
> | Action | Microsoft.Storage/locations/checknameavailability/read | Vérifie que le nom du valide est valide et qu’il n’est pas déjà utilisé. |
> | Action | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Informe Microsoft.Storage que le sous-réseau ou le réseau virtuel est en cours de suppression. |
> | Action | Microsoft.Storage/locations/usages/read | Retourne la limite et le nombre actuel d’utilisations des ressources dans l’abonnement spécifié. |
> | Action | Microsoft.Storage/operations/read | Interroge l’état d’une opération asynchrone. |
> | Action | Microsoft.Storage/register/action | Inscrit l’abonnement pour le fournisseur de ressources de stockage et permet la création de comptes de stockage. |
> | Action | Microsoft.Storage/skus/read | Répertorie les références prises en charge par Microsoft.Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Retourner le résultat de l’ajout de contenu d’objet blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Retourner le résultat de la suppression d’un objet blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action | Retourne le résultat de la suppression d’une version blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | Retourne la liste d’objets blob sous un compte avec le filtre de balises correspondantes |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/manageOwnership/action | Modifie la propriété de l'objet blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/modifyPermissions/action | Modifie les autorisations de l'objet blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action | Déplace l'objet blob d'un chemin à un autre |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retourne un objet blob ou une liste d'objets blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | Retourne le résultat de la commande blob. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | Retourne le résultat de la lecture des balises d’objet blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | Retourne le résultat de l’écriture des balises d’objet blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Retourner le résultat de l’écriture d’un objet blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Efface la conservation légale du conteneur d’objets blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Retourne le résultat de la suppression d’un conteneur |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Supprime la stratégie d’immuabilité du conteneur d’objets blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Étend la stratégie d’immuabilité du conteneur d’objets blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Verrouille la stratégie d’immuabilité du conteneur d’objets blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Obtient la stratégie d’immuabilité du conteneur d’objets blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Place la stratégie d’immuabilité du conteneur d’objets blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Retourne le résultat de la location du conteneur d’objets blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retourne un conteneur |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retourne la liste des conteneurs |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Définit la conservation légale du conteneur d’objets blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retourne le résultat du correctif de conteneur d’objets blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retourne le résultat du conteneur put blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourne une clé de délégation d’utilisateur pour le service BLOB. |
> | Action | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | Action | Microsoft.Storage/storageAccounts/blobServices/read | Retourne les propriétés ou statistiques du service blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/write | Retourne le résultat des propriétés du service blob de placement |
> | Action | Microsoft.Storage/storageAccounts/dataSharePolicies/delete |  |
> | Action | Microsoft.Storage/storageAccounts/dataSharePolicies/read |  |
> | Action | Microsoft.Storage/storageAccounts/dataSharePolicies/read |  |
> | Action | Microsoft.Storage/storageAccounts/dataSharePolicies/write |  |
> | Action | Microsoft.Storage/storageAccounts/delete | Supprime un compte de stockage existant. |
> | Action | Microsoft.Storage/storageAccounts/encryptionScopes/read |  |
> | Action | Microsoft.Storage/storageAccounts/encryptionScopes/read |  |
> | Action | Microsoft.Storage/storageAccounts/encryptionScopes/write |  |
> | Action | Microsoft.Storage/storageAccounts/encryptionScopes/write |  |
> | Action | Microsoft.Storage/storageAccounts/failover/action | Le client est en mesure de contrôler le basculement en cas de problèmes de disponibilité |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action | Obtient des privilèges d'administration de fichiers |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Retourne le résultat de la suppression d’un fichier/dossier |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Retourne le résultat de la modification de l’autorisation sur un fichier/dossier |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Renvoie un fichier/dossier ou une liste de fichiers/dossiers |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Renvoie le résultat de l’écriture d’un fichier ou de la création d’un dossier |
> | Action | Microsoft.Storage/storageAccounts/fileServices/read |  |
> | Action | Microsoft.Storage/storageAccounts/fileServices/read | Obtenir les propriétés du service de fichiers |
> | Action | Microsoft.Storage/storageAccounts/fileServices/shares/action |  |
> | Action | Microsoft.Storage/storageAccounts/fileServices/shares/delete |  |
> | Action | Microsoft.Storage/storageAccounts/fileServices/shares/read |  |
> | Action | Microsoft.Storage/storageAccounts/fileServices/shares/read |  |
> | Action | Microsoft.Storage/storageAccounts/fileServices/shares/write |  |
> | Action | Microsoft.Storage/storageAccounts/fileServices/write |  |
> | Action | Microsoft.Storage/storageAccounts/listAccountSas/action | Retourne le jeton SAS du compte de stockage spécifié. |
> | Action | Microsoft.Storage/storageAccounts/listkeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | Action | Microsoft.Storage/storageAccounts/listServiceSas/action | Retourne le jeton SAS de service pour le compte de stockage spécifié. |
> | Action | Microsoft.Storage/storageAccounts/managementPolicies/delete | Supprime des stratégies de gestion de compte de stockage |
> | Action | Microsoft.Storage/storageAccounts/managementPolicies/read | Obtient des stratégies de compte de gestion de stockage |
> | Action | Microsoft.Storage/storageAccounts/managementPolicies/write | Placer les stratégies de gestion du compte de stockage |
> | Action | Microsoft.Storage/storageAccounts/objectReplicationPolicies/delete |  |
> | Action | Microsoft.Storage/storageAccounts/objectReplicationPolicies/read |  |
> | Action | Microsoft.Storage/storageAccounts/objectReplicationPolicies/read |  |
> | Action | Microsoft.Storage/storageAccounts/objectReplicationPolicies/write |  |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | Supprime des proxys de connexion de point de terminaison privé |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/read | Obtenir un proxy de connexion de point de terminaison privé |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | Écrit des proxys de connexion de point de terminaison privé |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | Supprime une connexion de point de terminaison privé |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | Obtient une connexion de point de terminaison privé |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Écrit une connexion de point de terminaison privé |
> | Action | Microsoft.Storage/storageAccounts/PrivateEndpointConnectionsApproval/action | Approuve les connexions de point de terminaison privé |
> | Action | Microsoft.Storage/storageAccounts/privateLinkResources/read | Obtient les groupID du compte de stockage |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Retourne le résultat de la suppression d’une file d’attente |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Retourner le résultat de l’ajout d’un message |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Retourner le résultat de la suppression d’un message |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Retourner le résultat du traitement d’un message |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Retourne un message |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Retourner le résultat de l’écriture d’un message |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/read | Retourne une file d’attente ou une liste de files d’attente. |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/write | Retourne le résultat de l’écriture d’une file d’attente |
> | Action | Microsoft.Storage/storageAccounts/queueServices/read | Obtenir les propriétés du service de File d’attente |
> | Action | Microsoft.Storage/storageAccounts/queueServices/read | Retourne les propriétés ou les statistiques du service de file d’attente. |
> | Action | Microsoft.Storage/storageAccounts/queueServices/write | Retourne le résultat de la définition des propriétés du service de file d’attente |
> | Action | Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | Action | Microsoft.Storage/storageAccounts/regeneratekey/action | Régénère les clés d’accès au compte de stockage spécifié. |
> | Action | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | Restaure les plages d’objets blob à l’état de l’heure spécifiée |
> | Action | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Révoque toutes les clés de délégation de l’utilisateur pour le compte de stockage spécifié |
> | Action | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Crée/met à jour les paramètres de diagnostic de compte de stockage. |
> | Action | Microsoft.Storage/storageAccounts/tableServices/read | Obtenir les propriétés du service de Table |
> | Action | Microsoft.Storage/storageAccounts/write | Crée un compte de stockage avec les paramètres spécifiés ou met à jour les propriétés ou les balises, ou ajoute un domaine personnalisé pour le compte de stockage spécifié. |
> | Action | Microsoft.Storage/usages/read | Retourne la limite et le nombre actuel d’utilisations des ressources dans l’abonnement spécifié. |

## <a name="microsoftstoragesync"></a>microsoft.storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | microsoft.storagesync/locations/checkNameAvailability/action | Vérifie que le nom du service de synchronisation du stockage est valide et qu’il n’est pas déjà utilisé. |
> | Action | microsoft.storagesync/locations/workflows/operations/read | Afficher l’état d’une opération asynchrone |
> | Action | microsoft.storagesync/operations/read | Obtient une liste des opérations prises en charge |
> | Action | microsoft.storagesync/register/action | Enregistre l’abonnement pour le fournisseur de synchronisation de stockage |
> | Action | microsoft.storagesync/storageSyncServices/delete | Supprime des services de synchronisation de stockage |
> | Action | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Obtient les métriques disponibles pour les services de synchronisation de stockage |
> | Action | microsoft.storagesync/storageSyncServices/read | Lit des services de synchronisation de stockage |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/delete | Supprime un serveur inscrit |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Obtient les métriques disponibles pour le serveur inscrit |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/read | Lit un serveur inscrit |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/write | Crée ou met à jour un serveur inscrit |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Supprime des points de terminaison cloud |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Obtient l’état d’une opération de sauvegarde/restauration asynchrone |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Appelle cette action après une sauvegarde |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Appelle cette action après une restauration |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Appelle cette action avant une sauvegarde |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Appelle cette action avant une restauration |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Lit des points de terminaison cloud |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Restaure la pulsation |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/triggerChangeDetection/action | Appelle cette action pour déclencher la détection de modifications sur le partage de fichiers d’un point de terminaison cloud |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Crée ou met à jour des points de terminaison cloud |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/delete | Supprime des groupes de synchronisation |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Obtient les métriques disponibles pour les groupes de synchronisation |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/read | Lit des groupes de synchronisation |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Supprime des points de terminaison de serveur |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Obtient les métriques disponibles pour les points de terminaison de serveur |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Lit des points de terminaison de serveur |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Appelle cette action pour rappeler des fichiers sur un serveur |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Crée ou met à jour des points de terminaison de serveur |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/write | Crée ou met à jour des groupes de synchronisation |
> | Action | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Afficher l’état d’une opération asynchrone |
> | Action | microsoft.storagesync/storageSyncServices/workflows/operations/read | Afficher l’état d’une opération asynchrone |
> | Action | microsoft.storagesync/storageSyncServices/workflows/read | Lire les flux de travail |
> | Action | microsoft.storagesync/storageSyncServices/write | Crée ou met à jour des services de synchronisation de stockage |
> | Action | microsoft.storagesync/unregister/action | Désenregistre l’abonnement pour le fournisseur de synchronisation de stockage |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/delete | Supprime les enregistrements de contrôle d’accès. |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/read | Répertorie ou récupère les enregistrements de contrôle d’accès. |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/write | Crée ou met à jour les enregistrements de contrôle d’accès. |
> | Action | Microsoft.StorSimple/managers/alerts/read | Répertorie ou récupère les alertes. |
> | Action | Microsoft.StorSimple/managers/backups/read | Répertorie ou récupère le jeu de sauvegarde. |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/delete | Supprime des paramètres de bande passante existants (série 8000 uniquement). |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Répertorier les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/read | Répertorie les paramètres de bande passante (série 8000 uniquement). |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/write | Crée ou met à jour les paramètres de bande passante (série 8000 uniquement). |
> | Action | Microsoft.StorSimple/managers/certificates/write | Créer ou mettre à jour les certificats |
> | Action | Microsoft.StorSimple/Managers/certificates/write | L’opération de mise à jour de certificat de ressource met à jour le certificat d’identification du coffre/de la ressource. |
> | Action | Microsoft.StorSimple/managers/clearAlerts/action | Efface toutes les alertes associées au gestionnaire d’appareils. |
> | Action | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Répertorie les configurations prises en charge d’appliance cloud. |
> | Action | Microsoft.StorSimple/managers/configureDevice/action | Configure un appareil. |
> | Action | Microsoft.StorSimple/managers/delete | Supprime les gestionnaires d’appareils. |
> | Action | Microsoft.StorSimple/Managers/delete | L’opération de suppression de coffre supprime la ressource Azure de type « coffre » spécifiée. |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/read | Répertorie ou récupère les paramètres d’alerte. |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/write | Crée ou met à jour les paramètres d’alerte. |
> | Action | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Autoriser la substitution de clé de chiffrement de service d’appareils |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Exécute une sauvegarde manuelle pour créer une sauvegarde à la demande de tous les volumes protégés par la stratégie. |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Supprime des stratégies de sauvegarde existantes (série 8000 uniquement). |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Répertorier les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/read | Répertorie les stratégies de sauvegarde (série 8000 uniquement). |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Supprime une planification existante. |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Répertorier les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Répertorie les planifications. |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Crée une planification ou met à jour des planifications existantes. |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/write | Crée ou met à jour des stratégies de sauvegarde (série 8000 uniquement). |
> | Action | Microsoft.StorSimple/managers/devices/backups/delete | Supprime le jeu de sauvegarde. |
> | Action | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Clone un partage ou un volume à l’aide d’un élément de sauvegarde. |
> | Action | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/backups/read | Répertorie ou récupère le jeu de sauvegarde. |
> | Action | Microsoft.StorSimple/managers/devices/backups/restore/action | Restaure tous les volumes d’un jeu de sauvegarde. |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Supprime les groupes de planification de sauvegarde. |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Répertorie ou récupère les groupes de planification de sauvegarde. |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Crée ou met à jour les groupes de planification de sauvegarde. |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/delete | Supprime les paramètres CHAP. |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/read | Répertorie ou récupère les paramètres CHAP. |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/write | Crée ou met à jour les paramètres CHAP. |
> | Action | Microsoft.StorSimple/managers/devices/deactivate/action | Désactive un appareil. |
> | Action | Microsoft.StorSimple/managers/devices/delete | Supprime les appareils. |
> | Action | Microsoft.StorSimple/managers/devices/disks/read | Répertorie ou récupère les disques. |
> | Action | Microsoft.StorSimple/managers/devices/download/action | Télécharge les mises à jour d’un appareil. |
> | Action | Microsoft.StorSimple/managers/devices/failover/action | Basculement de l’appareil. |
> | Action | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/failoverTargets/read | Répertorier ou obtenir les cibles de basculement des appareils |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Exécute la sauvegarde d’un serveur de fichiers. |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/delete | Supprime les serveurs de fichiers. |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Répertorie ou récupère les mesures. |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Répertorie ou récupère les définitions de mesures. |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/read | Répertorie ou récupère les serveurs de fichiers. |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Supprime les partages. |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Répertorie ou récupère les mesures. |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Répertorie ou récupère les définitions de mesures. |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Répertorie ou récupère les partages. |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Crée ou met à jour les partages. |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/write | Crée ou met à jour les serveurs de fichiers. |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Modifie l’état d’alimentation du contrôleur des groupes de composants matériels. |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Répertorier les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Répertorie les groupes de composants matériels. |
> | Action | Microsoft.StorSimple/managers/devices/install/action | Installe des mises à jour sur un appareil. |
> | Action | Microsoft.StorSimple/managers/devices/installUpdates/action | Installer les mises à jour sur les appareils (série 8000 uniquement) |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Exécute la sauvegarde d’un serveur iSCSI. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Supprime les serveurs iSCSI. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Supprime les disques. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Répertorie ou récupère les mesures. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Répertorie ou récupère les définitions de mesures. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Répertorie ou récupère les disques. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Crée ou met à jour les disques. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Répertorie ou récupère les mesures. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Répertorie ou récupère les définitions de mesures. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/read | Répertorie ou récupère les serveurs iSCSI. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/write | Crée ou met à jour les serveurs iSCSI. |
> | Action | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Annule un travail en cours d’exécution. |
> | Action | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Répertorier les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/jobs/read | Répertorie ou récupère les travaux. |
> | Action | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Répertorier les ensembles de basculement d’un appareil (série 8000 uniquement) |
> | Action | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Répertorier les cibles de basculement des appareils (série 8000 uniquement) |
> | Action | Microsoft.StorSimple/managers/devices/metrics/read | Répertorie ou récupère les mesures. |
> | Action | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Répertorie ou récupère les définitions de mesures. |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Vérifie et valide une migration réussie. |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Répertorier le statut de confirmation de la migration |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Extrait l’état de confirmation de la migration. |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Extrait l’état du travail d’estimation de la migration. |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Extrait l’état de la migration. |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importe les configurations de source pour la migration. |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Répertorier l’estimation de la migration |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Répertorier le statut de la migration |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Répertorier les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Démarre la migration à l’aide des configurations de source. |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Démarre un travail pour estimer la durée du processus de migration. |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Répertorier les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/read | Répertorie ou récupère les paramètres réseau. |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/write | Crée ou met à jour les paramètres réseau. |
> | Action | Microsoft.StorSimple/managers/devices/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Répertorie les clés de chiffrement publiques d’un gestionnaire d’appareils. |
> | Action | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publier le package de support pour un appareil existant. Un package de support StorSimple est un mécanisme facile à utiliser qui collecte tous les journaux d’activité appropriés afin d’aider le support Microsoft à résoudre tout problème lié à votre appareil StorSimple. |
> | Action | Microsoft.StorSimple/managers/devices/read | Répertorie ou récupère les appareils. |
> | Action | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Recherche des mises à jour sur un appareil. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/read | Répertorie les paramètres de sécurité. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Synchronise le certificat de gestion à distance d’un appareil. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Met à jour les paramètres de sécurité. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/write | Crée ou met à jour les paramètres de sécurité. |
> | Action | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Envoie un test d’alerte par e-mail aux destinataires de courrier électronique configurés. |
> | Action | Microsoft.StorSimple/managers/devices/shares/read | Répertorie ou récupère les partages. |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Répertorier les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/read | Répertorie ou récupère les paramètres horaires. |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/write | Crée ou met à jour les paramètres horaires. |
> | Action | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/updateSummary/read | Répertorie ou récupère le résumé de la mise à jour. |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Supprime des conteneurs de volumes existants (série 8000 uniquement). |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Répertorie les mesures. |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Répertorie les définitions de mesures. |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Répertorier les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/read | Répertorie les conteneurs de volumes (série 8000 uniquement). |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Supprime un volume existant. |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Répertorie les mesures. |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Répertorie les définitions de mesures. |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Répertorier les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Répertorie les volumes. |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Crée un volume ou met à jour des volumes existants. |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/write | Crée ou met à jour des conteneurs de volumes (série 8000 uniquement). |
> | Action | Microsoft.StorSimple/managers/devices/volumes/read | Répertorie les volumes. |
> | Action | Microsoft.StorSimple/managers/devices/write | Crée ou met à jour les appareils. |
> | Action | Microsoft.StorSimple/managers/encryptionSettings/read | Répertorie ou récupère les paramètres de chiffrement. |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/delete | Supprimer les informations étendues du coffre |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/delete | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/read | Répertorier ou récupérer les informations étendues du coffre |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/read | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/write | Créer ou mettre à jour les informations étendues du coffre |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/write | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Action | Microsoft.StorSimple/managers/features/read | Répertorier les fonctionnalités |
> | Action | Microsoft.StorSimple/managers/fileservers/read | Répertorie ou récupère les serveurs de fichiers. |
> | Action | Microsoft.StorSimple/managers/getEncryptionKey/action | Récupère la clé de chiffrement du gestionnaire d’appareils. |
> | Action | Microsoft.StorSimple/managers/iscsiservers/read | Répertorie ou récupère les serveurs iSCSI. |
> | Action | Microsoft.StorSimple/managers/jobs/read | Répertorie ou récupère les travaux. |
> | Action | Microsoft.StorSimple/managers/listActivationKey/action | Récupère la clé d’activation du gestionnaire StorSimple Device Manager. |
> | Action | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Répertorie les clés de chiffrement publiques d’un gestionnaire StorSimple Device Manager. |
> | Action | Microsoft.StorSimple/managers/metrics/read | Répertorie ou récupère les mesures. |
> | Action | Microsoft.StorSimple/managers/metricsDefinitions/read | Répertorie ou récupère les définitions de mesures. |
> | Action | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Migrer depuis des déploiements classiques vers des déploiements Resource Manager |
> | Action | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Répertorier les configurations de source de migration (série 8000 uniquement) |
> | Action | Microsoft.StorSimple/managers/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Crée une appliance cloud. |
> | Action | Microsoft.StorSimple/managers/read | Répertorie ou récupère les gestionnaires d’appareils. |
> | Action | Microsoft.StorSimple/Managers/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Action | Microsoft.StorSimple/managers/regenerateActivationKey/action | Regénérer la clé d’activation pour un gestionnaire d’appareil StorSimple existant. |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Supprime les informations d’identification du compte de stockage. |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/read | Répertorie ou récupère les informations d’identification du compte de stockage. |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/write | Crée ou met à jour les informations d’identification du compte de stockage. |
> | Action | Microsoft.StorSimple/managers/storageDomains/delete | Supprime les domaines de stockage. |
> | Action | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Répertorier ou obtenir les résultats de l’opération |
> | Action | Microsoft.StorSimple/managers/storageDomains/read | Répertorie ou récupère les domaines de stockage. |
> | Action | Microsoft.StorSimple/managers/storageDomains/write | Crée ou met à jour les domaines de stockage. |
> | Action | Microsoft.StorSimple/managers/write | Crée ou met à jour les gestionnaires d’appareils. |
> | Action | Microsoft.StorSimple/Managers/write | L’opération de création de coffre entraîne la création d’une ressource Azure de type « coffre ». |
> | Action | Microsoft.StorSimple/operations/read | Répertorier ou obtenir les opérations |
> | Action | Microsoft.StorSimple/register/action | Inscrire le fournisseur Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.StreamAnalytics/locations/quotas/Read | Lit le quota d’abonnement Stream Analytics |
> | Action | Microsoft.StreamAnalytics/operations/Read | Lit les opérations Stream Analytics |
> | Action | Microsoft.StreamAnalytics/Register/action | Inscrit l’abonnement auprès du fournisseur de ressources Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Delete | Supprime une tâche Stream Analytics. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Supprime la fonction de tâche Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Lit les résultats d’opération pour la fonction de tâche Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Lit la fonction de tâche Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Récupère la définition par défaut d’une fonction de tâche Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Teste la fonction de tâche Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Écrit la fonction de tâche Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Supprime une entrée de tâche Stream Analytics. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Lit les résultats d’opération pour l’entrée de la tâche Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Lit une entrée de tâche Stream Analytics. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Échantillonne une entrée de tâche Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Teste une entrée de tâche Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Écrit une entrée de tâche Stream Analytics. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Lit les définitions des métriques |
> | Action | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Lit les résultats d’opération pour la tâche Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Supprime une sortie de tâche Stream Analytics. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Lit les résultats d’opération pour la sortie de tâche Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Lit une sortie de tâche Stream Analytics. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Teste une sortie de tâche Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Écrit une sortie de tâche Stream Analytics. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Lit le paramètre de diagnostic. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Écrit un paramètre de diagnostic. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Récupère les journaux d’activité disponibles pour les tâches de diffusion de contenu. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Récupère les mesures disponibles pour les tâches de diffusion de contenu. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/PublishEdgePackage/action | Publie le package Edge pour le travail Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Read | Lit une tâche Stream Analytics. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Scale/action | Met à l’échelle un travail Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Start/action | Démarre une tâche Stream Analytics. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Arrête une tâche Stream Analytics. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Supprime une transformation de tâche Stream Analytics. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Lit une transformation de tâche Stream Analytics. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Écrit une transformation de tâche Stream Analytics. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Write | Écrit une tâche Stream Analytics. |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Subscription/cancel/action | Annule l’abonnement |
> | Action | Microsoft.Subscription/CreateSubscription/action | Crée un abonnement Azure |
> | Action | Microsoft.Subscription/register/action | Inscrit un abonnement auprès du fournisseur de ressources Microsoft.Subscription |
> | Action | Microsoft.Subscription/rename/action | Renomme l'abonnement |
> | Action | Microsoft.Subscription/SubscriptionDefinitions/read | Obtient une définition d’abonnement Azure dans un groupe d’administration. |
> | Action | Microsoft.Subscription/SubscriptionDefinitions/write | Crée une définition d’abonnement Azure |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.Support/checkNameAvailability/action | Vérifie que le nom est valide et qu'il n'est pas utilisé pour le type de ressource |
> | Action | Microsoft.Support/operationresults/read | Obtenir le résultat d’une opération |
> | Action | Microsoft.Support/operations/read | Répertorie les opérations disponibles sur le fournisseur de ressources Microsoft.Support |
> | Action | Microsoft.Support/operationsstatus/read | Obtenir l’état d’une opération Get |
> | Action | Microsoft.Support/register/action | Inscrit le fournisseur de ressources de support |
> | Action | Microsoft.Support/services/problemClassifications/read | Permet d'obtenir la liste de classification des problèmes disponible pour un service Azure |
> | Action | Microsoft.Support/services/read | Permet d'obtenir la liste des services Azure pour lesquels un support est disponible |
> | Action | Microsoft.Support/supportTickets/communications/read | Permet d'obtenir la liste des communications relatives aux tickets de support |
> | Action | Microsoft.Support/supportTickets/communications/write | Crée des communications relatives aux tickets de support |
> | Action | Microsoft.Support/supportTickets/read | Permet d'obtenir la liste des tickets de support. |
> | Action | Microsoft.Support/supportTickets/write | Crée le ticket de support de façon asynchrone ou le met à jour. Vous pouvez créer un ticket de support pour les problèmes techniques ou les problèmes liés à la facturation, aux quotas ou à la gestion des abonnements. Vous pouvez mettre à jour la gravité des problèmes et les détails du contact pour les tickets de support existants. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Supprime la stratégie d’accès. |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Obtient les propriétés d’une stratégie d’accès. |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Crée une stratégie d’accès ou met à jour une stratégie d’accès existante pour un environnement. |
> | Action | Microsoft.TimeSeriesInsights/environments/delete | Supprime l’environnement. |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Supprime la source de l’événement. |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/read | Obtient les propriétés d’une source de l’événement. |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/write | Crée une source d’événement ou met à jour une source d’événement existante pour un environnement. |
> | Action | Microsoft.TimeSeriesInsights/environments/read | Obtient les propriétés d’un environnement. |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Supprime le jeu de données de référence. |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Obtient les propriétés d’un jeu de données de référence. |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Crée un jeu de données de référence ou met à jour un jeu de données de référence existant pour un environnement. |
> | Action | Microsoft.TimeSeriesInsights/environments/status/read | Obtient l’état de l’environnement, l’état de ses opérations associées comme l’entrée. |
> | Action | Microsoft.TimeSeriesInsights/environments/write | Crée un environnement ou met à jour un environnement existant. |
> | Action | Microsoft.TimeSeriesInsights/register/action | Inscrit l’abonnement pour le fournisseur de ressources Time Series Insights et permet la création d’environnements Time Series Insights. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.VisualStudio/Account/Delete | Supprime le compte |
> | Action | Microsoft.VisualStudio/Account/Extension/Read | Lire le compte/l’extension |
> | Action | Microsoft.VisualStudio/Account/Project/Read | Lire le compte/projet |
> | Action | Microsoft.VisualStudio/Account/Project/Write | Définir le compte/projet |
> | Action | Microsoft.VisualStudio/Account/Read | Affiche le compte |
> | Action | Microsoft.VisualStudio/Account/Write | Définit le compte |
> | Action | Microsoft.VisualStudio/Extension/Delete | Supprime l’extension |
> | Action | Microsoft.VisualStudio/Extension/Read | Affiche l’extension |
> | Action | Microsoft.VisualStudio/Extension/Write | Définit l’extension |
> | Action | Microsoft.VisualStudio/Project/Delete | Supprime le projet |
> | Action | Microsoft.VisualStudio/Project/Read | Affiche le projet |
> | Action | Microsoft.VisualStudio/Project/Write | Définit le projet |
> | Action | Microsoft.VisualStudio/Register/Action | Inscrire l’abonnement Azure auprès du fournisseur Microsoft.VisualStudio |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | microsoft.web/apimanagementaccounts/apiacls/read | Obtient des Apiacl de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Supprime des Apiacl de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/read | Obtient des Apiacl d’API de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/write | Met à jour des Apiacl d’API de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Obtient des Connectionacl d’API de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Vérifie les connexions d’API des comptes de gestion des API de code de confirmation. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Supprime des Connectionacl de connexions d’API de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Obtient des Connectionacl de connexions d’API de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Met à jour des Connectionacl de connexions d’API de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/delete | Supprime des connexions d’API de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Obtient des liens de confirmation pour les connexions d’API des comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Affiche les connexions d’API des comptes de gestion des API des clés de connexion. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Affiche les connexions d’API des comptes de gestion des API des secrets. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/read | Obtient des connexions d’API de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/write | Met à jour des connexions d’API de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/delete | Supprime des API de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Supprime des définitions localisées d’API de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Obtient des définitions localisées d’API de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Met à jour des définitions localisées d’API de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/read | Obtient des API de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/apis/write | Met à jour des API de comptes de gestion des API. |
> | Action | microsoft.web/apimanagementaccounts/connectionacls/read | Obtient des Connectionacl de comptes de gestion des API. |
> | Action | microsoft.web/availablestacks/read | Récupère les piles disponibles. |
> | Action | Microsoft.Web/certificates/Delete | Supprime un certificat existant. |
> | Action | Microsoft.Web/certificates/Read | Récupère la liste des certificats. |
> | Action | Microsoft.Web/certificates/Write | Ajoute un nouveau certificat ou en met un à jour. |
> | Action | microsoft.web/checknameavailability/read | Vérifie si le nom de la ressource est disponible. |
> | Action | microsoft.web/classicmobileservices/read | Récupère Classic Mobile Services. |
> | Action | Microsoft.Web/connectionGateways/Delete | Supprime une passerelle de connexion. |
> | Action | Microsoft.Web/connectionGateways/Join/Action | Joint une passerelle de connexion. |
> | Action | Microsoft.Web/connectionGateways/ListStatus/Action | Affiche l’état d’une passerelle de connexion. |
> | Action | Microsoft.Web/connectionGateways/Move/Action | Déplace une passerelle de connexion. |
> | Action | Microsoft.Web/connectionGateways/Read | Récupère la liste des passerelles de connexions. |
> | Action | Microsoft.Web/connectionGateways/Write | Crée ou met à jour une passerelle de connexion. |
> | Action | microsoft.web/connections/confirmconsentcode/action | Vérifie le code de consentement des connexions. |
> | Action | Microsoft.Web/connections/Delete | Supprime une connexion. |
> | Action | Microsoft.Web/connections/Join/Action | Joint une connexion. |
> | Action | microsoft.web/connections/listconsentlinks/action | Répertorie les liens de consentement pour les connexions. |
> | Action | Microsoft.Web/connections/Move/Action | Déplace une connexion. |
> | Action | Microsoft.Web/connections/Read | Récupère la liste des connexions. |
> | Action | Microsoft.Web/connections/Write | Crée ou met à jour une connexion. |
> | Action | Microsoft.Web/customApis/Delete | Supprime une API personnalisée. |
> | Action | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extrait une définition d’API de WSDL. |
> | Action | Microsoft.Web/customApis/Join/Action | Joint une API personnalisée. |
> | Action | Microsoft.Web/customApis/listWsdlInterfaces/Action | Affiche les interfaces WSDL pour une API personnalisée. |
> | Action | Microsoft.Web/customApis/Move/Action | Déplace une API personnalisée. |
> | Action | Microsoft.Web/customApis/Read | Obtient la liste des API personnalisées. |
> | Action | Microsoft.Web/customApis/Write | Crée ou met à jour une API personnalisée. |
> | Action | Microsoft.Web/deletedSites/Read | Obtenir les propriétés d’une application web supprimée |
> | Action | microsoft.web/deploymentlocations/read | Récupère les emplacements de déploiement. |
> | Action | Microsoft.Web/geoRegions/Read | Récupère la liste des zones géographiques. |
> | Action | microsoft.web/hostingenvironments/capacities/read | Récupère les fonctionnalités des environnements d’hébergement. |
> | Action | Microsoft.Web/hostingEnvironments/Delete | Supprime un environnement App Service. |
> | Action | Microsoft.Web/hostingenvironments/detectors/Read | Obtenir les détecteurs d’environnements d’hébergement. |
> | Action | microsoft.web/hostingenvironments/diagnostics/read | Récupère les diagnostics des environnements d’hébergement. |
> | Action | Microsoft.Web/hostingEnvironments/eventGridFilters/delete | Supprime le filtre Event Grid de l'environnement d'hébergement. |
> | Action | Microsoft.Web/hostingEnvironments/eventGridFilters/read | Permet d'obtenir le filtre Event Grid dans l'environnement d'hébergement. |
> | Action | Microsoft.Web/hostingEnvironments/eventGridFilters/write | Place le filtre Event Grid dans l'environnement d'hébergement. |
> | Action | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Obtient les points de terminaison réseau de toutes les dépendances entrantes. |
> | Action | Microsoft.Web/hostingEnvironments/Join/Action | Joint un environnement App Service Environment |
> | Action | microsoft.web/hostingenvironments/metricdefinitions/read | Récupère les définitions métriques des environnements d’hébergement. |
> | Action | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Récupère les définitions métriques des pools multirôles des environnements d’hébergement. |
> | Action | microsoft.web/hostingenvironments/multirolepools/metrics/read | Récupère les mesures des pools multirôles des environnements d’hébergement. |
> | Action | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Récupère les propriétés d’un pool frontal dans un environnement App Service. |
> | Action | microsoft.web/hostingenvironments/multirolepools/skus/read | Récupère les références des pools multirôles des environnements d’hébergement. |
> | Action | microsoft.web/hostingenvironments/multirolepools/usages/read | Récupère les utilisations des pools multirôles des environnements d’hébergement. |
> | Action | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Crée un pool frontal dans un environnement App Service ou en met un à jour. |
> | Action | microsoft.web/hostingenvironments/operations/read | Récupère les opérations des environnements d’hébergement. |
> | Action | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Obtient les points de terminaison réseau de toutes les dépendances sortantes. |
> | Action | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Approuve les connexions de point de terminaison privé |
> | Action | Microsoft.Web/hostingEnvironments/Read | Récupère les propriétés d’un environnement App Service. |
> | Action | Microsoft.Web/hostingEnvironments/reboot/Action | Redémarre tous les ordinateurs dans un environnement App Service. |
> | Action | microsoft.web/hostingenvironments/resume/action | Reprend les environnements d’hébergement. |
> | Action | microsoft.web/hostingenvironments/serverfarms/read | Récupère les plans App Service des environnements d’hébergement. |
> | Action | microsoft.web/hostingenvironments/sites/read | Récupère les applications web des environnements d’hébergement. |
> | Action | microsoft.web/hostingenvironments/suspend/action | Interrompt les environnements d’hébergement. |
> | Action | microsoft.web/hostingenvironments/usages/read | Récupère les utilisations des environnements d’hébergement. |
> | Action | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Récupère les définitions métriques des pools de workers des environnements d’hébergement. |
> | Action | microsoft.web/hostingenvironments/workerpools/metrics/read | Récupère les mesures des pools de workers des environnements d’hébergement. |
> | Action | Microsoft.Web/hostingEnvironments/workerPools/Read | Récupère les propriétés d’un pool de workers dans un environnement App Service. |
> | Action | microsoft.web/hostingenvironments/workerpools/skus/read | Récupère les références des pools de workers des environnements d’hébergement. |
> | Action | microsoft.web/hostingenvironments/workerpools/usages/read | Récupère les utilisations des pools de workers des environnements d’hébergement. |
> | Action | Microsoft.Web/hostingEnvironments/workerPools/Write | Crée un pool de workers dans un environnement App Service ou en met un à jour. |
> | Action | Microsoft.Web/hostingEnvironments/Write | Crée un environnement App Service ou en met un à jour. |
> | Action | microsoft.web/ishostingenvironmentnameavailable/read | Récupère des éléments si le nom de l’environnement d’hébergement est disponible. |
> | Action | microsoft.web/ishostnameavailable/read | Vérifie si le nom d’hôte est disponible. |
> | Action | microsoft.web/isusernameavailable/read | Vérifie si le nom d’utilisateur est disponible. |
> | Action | Microsoft.Web/listSitesAssignedToHostName/Read | Récupère les noms de sites affectés à un nom d’hôte. |
> | Action | microsoft.web/locations/apioperations/read | Récupère les opérations API d’emplacements. |
> | Action | microsoft.web/locations/connectiongatewayinstallations/read | Récupère les installations de la passerelle de connexion des emplacements. |
> | Action | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Notification de suppression de réseau virtuel ou de sous-réseau pour les emplacements. |
> | Action | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extrait la définition d’API de WSDL pour les emplacements. |
> | Action | microsoft.web/locations/listwsdlinterfaces/action | Affiche les interfaces WSDL pour les emplacements. |
> | Action | microsoft.web/locations/managedapis/apioperations/read | Obtient les opérations API managée d’emplacements. |
> | Action | Microsoft.Web/locations/managedapis/Join/Action | Joint une API managée. |
> | Action | microsoft.web/locations/managedapis/read | Récupère les API managées des emplacements. |
> | Action | microsoft.web/locations/operationResults/read | Récupère les opérations. |
> | Action | microsoft.web/locations/operations/read | Récupère les opérations. |
> | Action | microsoft.web/operations/read | Récupère les opérations. |
> | Action | microsoft.web/publishingusers/read | Récupère la publication des utilisateurs. |
> | Action | microsoft.web/publishingusers/write | Met à jour la publication des utilisateurs. |
> | Action | Microsoft.Web/recommendations/Read | Récupère la liste des recommandations pour les abonnements. |
> | Action | microsoft.web/register/action | Inscrit le fournisseur de ressources Microsoft.Web pour l’abonnement. |
> | Action | microsoft.web/resourcehealthmetadata/read | Obtient les métadonnées de Resource Health. |
> | Action | microsoft.web/serverfarms/capabilities/read | Récupère les fonctionnalités des plans App Service. |
> | Action | Microsoft.Web/serverfarms/Delete | Supprimer un plan App Service existant |
> | Action | Microsoft.Web/serverfarms/eventGridFilters/delete | Supprime un filtre Event Grid dans la batterie de serveurs. |
> | Action | Microsoft.Web/serverfarms/eventGridFilters/read | Obtient un filtre Event Grid dans la batterie de serveurs. |
> | Action | Microsoft.Web/serverfarms/eventGridFilters/write | Place un filtre Event Grid dans la batterie de serveurs. |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/delete | Supprime les paramètres des applications principales des plans App Service. |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/read | Récupère les paramètres des applications principales des plans App Service. |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/write | Met à jour les paramètres des applications principales des plans App Service. |
> | Action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Supprime les relais d’espace de noms de connexion hybride de plans App Service. |
> | Action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Obtient les relais d’espace de noms de connexion hybride de plans App Service. |
> | Action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Récupère les applications web des relais d’espace de noms de connexion hybride des plans App Service. |
> | Action | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Récupère les limites de plan de connexion hybride des plans App Service. |
> | Action | microsoft.web/serverfarms/hybridconnectionrelays/read | Récupère les relais de connexion hybride des plans App Service. |
> | Action | microsoft.web/serverfarms/metricdefinitions/read | Récupère les définitions métriques des plans App Service. |
> | Action | microsoft.web/serverfarms/metrics/read | Récupère les mesures des plans App Service. |
> | Action | microsoft.web/serverfarms/operationresults/read | Récupère les résultats de l’opération des plans App Service. |
> | Action | Microsoft.Web/serverfarms/Read | Récupère les propriétés d’un plan App Service. |
> | Action | Microsoft.Web/serverfarms/restartSites/Action | Redémarre toutes les applications web d’un plan App Service. |
> | Action | microsoft.web/serverfarms/sites/read | Récupère les applications web des plans App Service. |
> | Action | microsoft.web/serverfarms/skus/read | Récupère les références des plans App Service. |
> | Action | microsoft.web/serverfarms/usages/read | Récupère les usages des plans App Service. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Met à jour les passerelles de connexions de réseau virtuel des plans App Service. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/read | Récupère les connexions de réseau virtuel des plans App Service. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Supprime les itinéraires des connexions de réseau virtuel des plans App Service. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Récupère les itinéraires des connexions de réseau virtuel des plans App Service. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Met à jour les itinéraires des connexions de réseau virtuel des plans App Service. |
> | Action | microsoft.web/serverfarms/workers/reboot/action | Redémarre les Workers des plans App Service. |
> | Action | Microsoft.Web/serverfarms/Write | Crée un plan App Service ou en met un à jour. |
> | Action | microsoft.web/sites/analyzecustomhostname/read | Analyse le nom d’hôte personnalisé. |
> | Action | Microsoft.Web/sites/applySlotConfig/Action | Applique la configuration d’emplacement d’application web de l’emplacement cible à l’application web actuelle. |
> | Action | Microsoft.Web/sites/backup/Action | Crée une sauvegarde d’application web. |
> | Action | microsoft.web/sites/backup/read | Récupère la sauvegarde Web Apps. |
> | Action | microsoft.web/sites/backup/write | Met à jour la sauvegarde Web Apps. |
> | Action | Microsoft.Web/sites/Backups/action | Détecter une sauvegarde d’application existante qui peut être restaurée à partir d’un objet blob dans un stockage Azure |
> | Action | microsoft.web/sites/backups/delete | Supprime les sauvegardes Web Apps. |
> | Action | microsoft.web/sites/backups/list/action | Répertorie les sauvegardes Web Apps. |
> | Action | Microsoft.Web/sites/backups/Read | Récupère les propriétés de sauvegarde d’une application web. |
> | Action | microsoft.web/sites/backups/restore/action | Restaure les sauvegardes Web Apps. |
> | Action | Microsoft.Web/sites/Backups/Write | Mettre à jour des sauvegardes d’applications web |
> | Action | microsoft.web/sites/config/delete | Supprime la configuration de Web Apps. |
> | Action | Microsoft.Web/sites/config/list/Action | Répertorie les paramètres sensibles de sécurité d’application web, tels que les informations d’identification de publication, les paramètres d’application et les chaînes de connexion. |
> | Action | Microsoft.Web/sites/config/Read | Récupère les paramètres de configuration des applications web. |
> | Action | microsoft.web/sites/config/snapshots/read | Obtient les captures instantanées de la configuration Web Apps. |
> | Action | Microsoft.Web/sites/config/Write | Met à jour les paramètres de configuration d’application web. |
> | Action | Microsoft.Web/sites/containerlogs/action | Obtenir des journaux d’activité compressés de conteneur pour une application web |
> | Action | microsoft.web/sites/containerlogs/download/action | Télécharger les journaux d’activité de conteneur Web Apps. |
> | Action | microsoft.web/sites/continuouswebjobs/delete | Supprime des tâches web continues Web Apps. |
> | Action | microsoft.web/sites/continuouswebjobs/read | Récupère des tâches web continues Web Apps. |
> | Action | microsoft.web/sites/continuouswebjobs/start/action | Démarre des tâches web continues Web Apps. |
> | Action | microsoft.web/sites/continuouswebjobs/stop/action | Arrête des tâches web continues Web Apps. |
> | Action | Microsoft.Web/sites/Delete | Supprimer une application web existante |
> | Action | microsoft.web/sites/deployments/delete | Supprime les déploiements de Web Apps. |
> | Action | microsoft.web/sites/deployments/log/read | Récupère le journal des déploiements de Web Apps. |
> | Action | microsoft.web/sites/deployments/read | Récupère les déploiements de Web Apps. |
> | Action | microsoft.web/sites/deployments/write | Met à jour les déploiements de Web Apps. |
> | Action | Microsoft.Web/sites/detectors/Read | Obtenir des détecteurs d’applications web |
> | Action | microsoft.web/sites/diagnostics/analyses/execute/Action | Exécute l’analyse de diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/analyses/read | Obtient l’analyse des diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/aspnetcore/read | Obtient des diagnostics Web Apps pour une application ASP.NET Core. |
> | Action | microsoft.web/sites/diagnostics/autoheal/read | Obtient l’Autoheal des diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/deployment/read | Obtient le déploiement des diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/deployments/read | Obtient des déploiements des diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/detectors/execute/Action | Exécute le détecteur de diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/detectors/read | Obtient le détecteur des diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Obtient les échecs de requêtes de diagnostics Web Apps par Uri. |
> | Action | microsoft.web/sites/diagnostics/frebanalysis/read | Récupère l’analyse FREB dans les diagnostics de Web Apps. |
> | Action | microsoft.web/sites/diagnostics/loganalyzer/read | Obtient l’analyseur de journal des diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/read | Obtient les catégories de diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/runtimeavailability/read | Récupère la disponibilité d’exécution des diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/servicehealth/read | Récupère l’état du service dans les diagnostics de Web Apps. |
> | Action | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Obtient l’analyse d’UC du site des diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/sitecrashes/read | Obtient les pannes du site des diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/sitelatency/read | Obtient la latence du site des diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Obtient l’analyse de mémoire du site des diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Obtient la mise à jour du paramètre de redémarrage du site des diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Obtient le redémarrage du site des diagnostics Web Apps que l’utilisateur a déclenché. |
> | Action | microsoft.web/sites/diagnostics/siteswap/read | Obtient l’échange de sites des diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/threadcount/read | Obtient le nombre de threads des diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/workeravailability/read | Récupère la disponibilité Worker des diagnostics Web Apps. |
> | Action | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Récupère le recyclage du processus Worker des diagnostics Web Apps. |
> | Action | microsoft.web/sites/domainownershipidentifiers/read | Récupère l’identificateur de propriété de domaine Web Apps. |
> | Action | microsoft.web/sites/domainownershipidentifiers/write | Met à jour l’identificateur de propriété de domaine Web Apps. |
> | Action | Microsoft.Web/sites/eventGridFilters/delete | Supprime un filtre Event Grid dans l’application web. |
> | Action | Microsoft.Web/sites/eventGridFilters/read | Obtient un filtre Event Grid dans l’application web. |
> | Action | Microsoft.Web/sites/eventGridFilters/write | Place un filtre Event Grid dans l’application web. |
> | Action | microsoft.web/sites/extensions/delete | Supprime les extensions de site Web Apps. |
> | Action | microsoft.web/sites/extensions/read | Obtient les extensions de site Web Apps. |
> | Action | microsoft.web/sites/extensions/write | Met à jour les extensions de site Web Apps. |
> | Action | microsoft.web/sites/functions/action | Fonctions Web Apps. |
> | Action | microsoft.web/sites/functions/delete | Supprime les fonctions Web Apps. |
> | Action | microsoft.web/sites/functions/keys/delete | Supprime les clés de fonction. |
> | Action | microsoft.web/sites/functions/keys/write | Met à jour les clés de fonction. |
> | Action | microsoft.web/sites/functions/listkeys/action | Liste les clés de fonction. |
> | Action | microsoft.web/sites/functions/listsecrets/action | Liste les secrets de fonction. |
> | Action | microsoft.web/sites/functions/masterkey/read | Obtient la clé principale des fonctions Web Apps. |
> | Action | microsoft.web/sites/functions/properties/read | Lit les propriétés de fonctions Web Apps. |
> | Action | microsoft.web/sites/functions/properties/write | Met à jour les propriétés de fonctions Web Apps. |
> | Action | microsoft.web/sites/functions/read | Récupère les fonctions Web Apps. |
> | Action | microsoft.web/sites/functions/token/read | Obtient le jeton des fonctions Web Apps. |
> | Action | microsoft.web/sites/functions/write | Met à jour les fonctions Web Apps. |
> | Action | microsoft.web/sites/host/functionkeys/delete | Supprime les clés de fonction d’hôte Functions. |
> | Action | microsoft.web/sites/host/functionkeys/write | Met à jour les clés de fonction d’hôte Functions. |
> | Action | microsoft.web/sites/host/listkeys/action | Liste les clés d’hôte Functions. |
> | Action | microsoft.web/sites/host/listsyncstatus/action | Liste l’état des déclencheurs de fonction de synchronisation. |
> | Action | microsoft.web/sites/host/properties/read | Lit les propriétés d’hôte de fonctions Web Apps. |
> | Action | microsoft.web/sites/host/sync/action | Synchronise les déclencheurs de fonction. |
> | Action | microsoft.web/sites/host/systemkeys/delete | Supprime les clés système d’hôte Functions. |
> | Action | microsoft.web/sites/host/systemkeys/write | Met à jour les clés système d’hôte Functions. |
> | Action | microsoft.web/sites/hostnamebindings/delete | Supprime les liaisons de nom d’hôte Web Apps. |
> | Action | microsoft.web/sites/hostnamebindings/read | Récupère les liaisons de nom d’hôte Web Apps. |
> | Action | microsoft.web/sites/hostnamebindings/write | Met à jour les liaisons de nom d’hôte Web Apps. |
> | Action | microsoft.web/sites/hostruntime/functions/keys/read | Obtenir les clés de fonctions Hostruntime Web Apps. |
> | Action | Microsoft.Web/sites/hostruntime/host/_master/read | Obtient la clé principale de Function App pour les opérations d’administration |
> | Action | Microsoft.Web/sites/hostruntime/host/action | Effectue une action de runtime Function App, comme le déclenchement de la synchronisation, l’ajout de fonctions, l’appel de fonctions, la suppression de fonctions, etc. |
> | Action | microsoft.web/sites/hostruntime/host/read | Obtenir l’hôte Hostruntime Web Apps. |
> | Action | microsoft.web/sites/hybridconnection/delete | Supprime une connexion hybride Web Apps. |
> | Action | microsoft.web/sites/hybridconnection/read | Récupère une connexion hybride Web Apps. |
> | Action | microsoft.web/sites/hybridconnection/write | Met à jour une connexion hybride Web Apps. |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Supprime les relais d’espaces de noms de connexion hybride Web Apps. |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Affiche les relais d’espaces de noms de connexion hybride Web Apps de clés. |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Obtient les relais d’espaces de noms de connexion hybride Web Apps. |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Met à jour les relais d’espaces de noms de connexion hybride Web Apps. |
> | Action | microsoft.web/sites/hybridconnectionrelays/read | Récupère les relais de connexions hybrides Web Apps. |
> | Action | microsoft.web/sites/instances/deployments/delete | Supprime les déploiements d’instances de Web Apps. |
> | Action | microsoft.web/sites/instances/deployments/read | Récupère les déploiements des instances Web Apps. |
> | Action | microsoft.web/sites/instances/extensions/log/read | Obtient le journal des extensions des instances Web Apps. |
> | Action | microsoft.web/sites/instances/extensions/processes/read | Obtenir les processus des extensions des instances Web Apps. |
> | Action | microsoft.web/sites/instances/extensions/read | Obtient les extensions des instances Web Apps. |
> | Action | microsoft.web/sites/instances/processes/delete | Supprime les processus des instances Web Apps. |
> | Action | microsoft.web/sites/instances/processes/modules/read | Obtenir les modules des processus des instances Web Apps. |
> | Action | microsoft.web/sites/instances/processes/read | Récupère les processus des instances Web Apps. |
> | Action | microsoft.web/sites/instances/processes/threads/read | Obtient les threads de processus des instances Web Apps. |
> | Action | microsoft.web/sites/instances/read | Récupère des instances Web Apps. |
> | Action | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Liste l’état de déclencheur de fonction de synchronisation. |
> | Action | microsoft.web/sites/metricdefinitions/read | Récupère les définitions métriques Web Apps. |
> | Action | microsoft.web/sites/metrics/read | Récupère les mesures Web Apps. |
> | Action | microsoft.web/sites/metricsdefinitions/read | Obtient les définitions de métrique de Web Apps. |
> | Action | microsoft.web/sites/migratemysql/action | Migre MySql Web Apps. |
> | Action | microsoft.web/sites/migratemysql/read | Obtient MySql de migration Web Apps. |
> | Action | microsoft.web/sites/networktrace/action | Trace réseau Web Apps. |
> | Action | microsoft.web/sites/networktraces/operationresults/read | Obtient les résultats de l’opération Trace réseau de Web Apps. |
> | Action | microsoft.web/sites/newpassword/action | Newpassword Web Apps. |
> | Action | microsoft.web/sites/operationresults/read | Récupère les résultats de l’opération Web Apps. |
> | Action | microsoft.web/sites/operations/read | Obtient les opérations Web Apps. |
> | Action | microsoft.web/sites/perfcounters/read | Récupère les compteurs de performances Web Apps. |
> | Action | microsoft.web/sites/premieraddons/delete | Supprime les modules complémentaires Premier Web Apps. |
> | Action | microsoft.web/sites/premieraddons/read | Récupère les modules complémentaires Premier Web Apps. |
> | Action | microsoft.web/sites/premieraddons/write | Met à jour les modules complémentaires Premier Web Apps. |
> | Action | microsoft.web/sites/privateaccess/read | Obtient des données sur l’activation de l’accès privé aux sites et sur les réseaux virtuels autorisés qui peuvent y accéder. |
> | Action | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | Approuve les connexions de point de terminaison privé |
> | Action | microsoft.web/sites/processes/modules/read | Obtenir les modules des processus d’applications web. |
> | Action | microsoft.web/sites/processes/read | Obtient les processus Web Apps. |
> | Action | microsoft.web/sites/processes/threads/read | Obtenir les threads des processus d’applications web. |
> | Action | microsoft.web/sites/publiccertificates/delete | Supprime les certificats publics Web Apps. |
> | Action | microsoft.web/sites/publiccertificates/read | Obtient les certificats publics Web Apps. |
> | Action | microsoft.web/sites/publiccertificates/write | Met à jour les certificats publics Web Apps. |
> | Action | Microsoft.Web/sites/publish/Action | Publie une application web. |
> | Action | Microsoft.Web/sites/publishxml/Action | Récupère le profil de publication xml pour une application web. |
> | Action | microsoft.web/sites/publishxml/read | Récupère publication XML Web Apps. |
> | Action | Microsoft.Web/sites/Read | Récupère les propriétés d’une application web. |
> | Action | microsoft.web/sites/recommendationhistory/read | Récupère un historique des recommandations Web Apps. |
> | Action | microsoft.web/sites/recommendations/disable/action | Désactive les recommandations des applications web. |
> | Action | Microsoft.Web/sites/recommendations/Read | Récupère la liste des recommandations pour l’application web. |
> | Action | microsoft.web/sites/recover/action | Récupère Web Apps. |
> | Action | Microsoft.Web/sites/resetSlotConfig/Action | Réinitialise la configuration d’application web. |
> | Action | microsoft.web/sites/resourcehealthmetadata/read | Obtient les métadonnées de Resource Health Web Apps. |
> | Action | Microsoft.Web/sites/restart/Action | Redémarre une application web. |
> | Action | microsoft.web/sites/restore/read | Récupère la restauration Web Apps. |
> | Action | microsoft.web/sites/restore/write | Restaure Web Apps. |
> | Action | microsoft.web/sites/restorefrombackupblob/action | Restaure une application web à partir d’un objet Blob de sauvegarde. |
> | Action | microsoft.web/sites/restorefromdeletedapp/action | Restaurer des applications web à partir d’une application supprimée |
> | Action | microsoft.web/sites/restoresnapshot/action | Restaure les captures instantanées de Web Apps. |
> | Action | microsoft.web/sites/siteextensions/delete | Supprime les extensions de site Web Apps. |
> | Action | microsoft.web/sites/siteextensions/read | Obtient les extensions de site Web Apps. |
> | Action | microsoft.web/sites/siteextensions/write | Met à jour les extensions de site Web Apps. |
> | Action | microsoft.web/sites/slots/analyzecustomhostname/read | Récupère le nom d’hôte personnalisé d’analyse des emplacements Web Apps. |
> | Action | Microsoft.Web/sites/slots/applySlotConfig/Action | Applique la configuration d’emplacement d’application web de l’emplacement cible à l’emplacement actuel. |
> | Action | Microsoft.Web/sites/slots/backup/Action | Crée une sauvegarde d’emplacement d’application web. |
> | Action | microsoft.web/sites/slots/backup/read | Obtient la sauvegarde des emplacements de Web Apps. |
> | Action | microsoft.web/sites/slots/backup/write | Met à jour la sauvegarde des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/backups/action | Découvre les sauvegardes des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/backups/delete | Supprime les sauvegardes des emplacements de Web Apps. |
> | Action | microsoft.web/sites/slots/backups/list/action | Répertorie les sauvegardes des emplacements Web Apps. |
> | Action | Microsoft.Web/sites/slots/backups/Read | Récupère les propriétés de sauvegarde des emplacements d’une application web. |
> | Action | microsoft.web/sites/slots/backups/restore/action | Restaure les sauvegardes des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/config/delete | Supprime la configuration des emplacements Web Apps. |
> | Action | Microsoft.Web/sites/slots/config/list/Action | Répertorie les paramètres sensibles de sécurité de l’emplacement d’application web, tels que les informations d’identification de publication, les paramètres d’application et les chaînes de connexion. |
> | Action | Microsoft.Web/sites/slots/config/Read | Récupère les paramètres de configuration de l’emplacement d’application web. |
> | Action | Microsoft.Web/sites/slots/config/Write | Met à jour les paramètres de configuration de l’emplacement d’application web. |
> | Action | microsoft.web/sites/slots/containerlogs/action | Obtient des journaux d’activité compressés de conteneur pour un emplacement Web Apps. |
> | Action | microsoft.web/sites/slots/containerlogs/download/action | Télécharger les journaux d’activité de conteneur des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/continuouswebjobs/delete | Supprime des tâches web continues dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/continuouswebjobs/read | Récupère des tâches web continues dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/continuouswebjobs/start/action | Démarre des tâches web continues dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/continuouswebjobs/stop/action | Arrête des tâches web continues dans les emplacements Web Apps. |
> | Action | Microsoft.Web/sites/slots/Delete | Supprime un emplacement d’application web existant. |
> | Action | microsoft.web/sites/slots/deployments/delete | Supprime les déploiements des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/deployments/log/read | Récupère le journal des déploiements des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/deployments/read | Récupère les déploiements des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/deployments/write | Met à jour les déploiements des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/detectors/read | Obtient les détecteurs d’emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Exécuter l’analyse des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/analyses/read | Obtient l’analyse des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Obtient les diagnostics des emplacements Web Apps pour l’application ASP.NET Core. |
> | Action | microsoft.web/sites/slots/diagnostics/autoheal/read | Obtient l’Autoheal des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/deployment/read | Obtient le déploiement des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/deployments/read | Obtient les déploiements des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Exécute le détecteur des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/detectors/read | Obtient le détecteur des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Obtient l’analyse FREB des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Obtient l’analyseur de journal des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/read | Obtient les diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Obtient la disponibilité du runtime des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/servicehealth/read | Obtient l’état du service des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Obtient l’analyse d’UC du site des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Obtient les pannes du site des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/sitelatency/read | Obtient la latence du site des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Obtient l’analyse de mémoire du site des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Obtient la mise à jour du paramètre de redémarrage du site des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Obtient le redémarrage du site des diagnostics des emplacements Web Apps que l’utilisateur a déclenché. |
> | Action | microsoft.web/sites/slots/diagnostics/siteswap/read | Obtient l’échange de sites des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/threadcount/read | Obtient le nombre de threads des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/workeravailability/read | Obtient la disponibilité du processus de travail des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Obtient le recyclage du processus de travail des diagnostics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/domainownershipidentifiers/read | Obtient les identificateurs de propriété de domaine des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/extensions/read | Obtient des extensions d’emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/extensions/write | Met à jour des extensions d’emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/functions/listsecrets/action | Lister les fonctions des emplacements Web Apps des secrets. |
> | Action | microsoft.web/sites/slots/functions/read | Obtient les fonctions des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/hostnamebindings/delete | Supprime les liaisons de nom d’hôte dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/hostnamebindings/read | Récupère les liaisons de nom d’hôte des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/hostnamebindings/write | Met à jour les liaisons de nom d’hôte des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/hybridconnection/delete | Supprime une connexion hybride dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/hybridconnection/read | Récupère une connexion hybride dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/hybridconnection/write | Met à jour une connexion hybride dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Supprime les relais d’espaces de noms de connexion hybride des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Met à jour les relais d’espaces de noms de connexion hybride des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/hybridconnectionrelays/read | Obtient les relais de connexion hybride des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/instances/deployments/read | Récupère les déploiements des instances des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/instances/processes/delete | Supprime les processus d’instances des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/instances/processes/read | Récupère les processus des instances des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/instances/read | Récupère les instances des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/metricdefinitions/read | Récupère les définitions métriques des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/metrics/read | Récupère les mesures des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/migratemysql/read | Obtient MySql de migration des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/networktrace/action | Obtient la trace réseau des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/networktraces/operationresults/read | Obtient les résultats de l’opération Trace réseau des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/newpassword/action | Emplacements Newpassword Web Apps. |
> | Action | microsoft.web/sites/slots/operationresults/read | Récupère les résultats de l’opération des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/operations/read | Obtient les opérations des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/perfcounters/read | Obtient les compteurs de performances des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/phplogging/read | Récupère Phplogging des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/premieraddons/delete | Supprime les modules complémentaires Premier dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/premieraddons/read | Récupère les modules complémentaires Premier dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/premieraddons/write | Met à jour les modules complémentaires Premier dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/processes/read | Obtenir les processus des emplacements Web Apps. |
> | Action | Microsoft.Web/sites/slots/publiccertificates/Delete | Supprimer des certificats publics d’emplacements d’applications web |
> | Action | microsoft.web/sites/slots/publiccertificates/read | Obtient les certificats publics des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/publiccertificates/write | Crée ou met à jour les certificats publics des emplacements Web Apps. |
> | Action | Microsoft.Web/sites/slots/publish/Action | Publie un emplacement d’application web. |
> | Action | Microsoft.Web/sites/slots/publishxml/Action | Récupère le profil de publication xml pour l’emplacement d’application web. |
> | Action | Microsoft.Web/sites/slots/Read | Récupère les propriétés d’un emplacement de déploiement d’une application web. |
> | Action | microsoft.web/sites/slots/recover/action | Récupère les emplacements Web Apps. |
> | Action | Microsoft.Web/sites/slots/resetSlotConfig/Action | Réinitialise la configuration d’emplacement de l’application web. |
> | Action | microsoft.web/sites/slots/resourcehealthmetadata/read | Obtient les métadonnées de Resource Health des emplacements Web Apps. |
> | Action | Microsoft.Web/sites/slots/restart/Action | Redémarre un emplacement d’application web. |
> | Action | microsoft.web/sites/slots/restore/read | Récupère la restauration des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/restore/write | Restaure les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/restorefrombackupblob/action | Restaure un emplacement Web Apps à partir d’un objet Blob de sauvegarde. |
> | Action | microsoft.web/sites/slots/restorefromdeletedapp/action | Restaurer des emplacements d’application web à partir d’une application supprimée |
> | Action | microsoft.web/sites/slots/restoresnapshot/action | Restaure les captures instantanées des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/siteextensions/delete | Supprime les extensions de site des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/siteextensions/read | Obtient les extensions de site des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/siteextensions/write | Met à jour les extensions de site des emplacements Web Apps. |
> | Action | Microsoft.Web/sites/slots/slotsdiffs/Action | Récupère les différences de configuration entre l’application web et les emplacements. |
> | Action | Microsoft.Web/sites/slots/slotsswap/Action | Permute des emplacements de déploiement d’application web. |
> | Action | microsoft.web/sites/slots/snapshots/read | Obtient les captures instantanées des emplacements Web Apps. |
> | Action | Microsoft.Web/sites/slots/sourcecontrols/Delete | Supprime les paramètres de configuration du contrôle de code source de l’emplacement d’application web. |
> | Action | Microsoft.Web/sites/slots/sourcecontrols/Read | Récupère les paramètres de configuration du contrôle de code source de l’emplacement d’application Web. |
> | Action | Microsoft.Web/sites/slots/sourcecontrols/Write | Met à jour les paramètres de configuration du contrôle de code source de l’emplacement d’application web. |
> | Action | Microsoft.Web/sites/slots/start/Action | Démarre un emplacement d’application web. |
> | Action | Microsoft.Web/sites/slots/stop/Action | Arrête un emplacement d’application web. |
> | Action | microsoft.web/sites/slots/sync/action | Synchronise les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/delete | Supprime les tâches web déclenchées dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/read | Récupère les tâches web déclenchées dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/run/action | Exécute les tâches web déclenchées dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/usages/read | Récupère les utilisations des emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/delete | Supprime les connexions de réseau virtuel dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Met à jour les passerelles de connexions de réseau virtuel dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/read | Récupère les connexions de réseau virtuel dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/write | Met à jour les connexions de réseau virtuel dans les emplacements Web Apps. |
> | Action | microsoft.web/sites/slots/webjobs/read | Récupère WebJobs des emplacements Web Apps. |
> | Action | Microsoft.Web/sites/slots/Write | Crée un emplacement d’application web ou en met un à jour. |
> | Action | Microsoft.Web/sites/slotsdiffs/Action | Récupère les différences de configuration entre l’application web et les emplacements. |
> | Action | Microsoft.Web/sites/slotsswap/Action | Permute des emplacements de déploiement d’application web. |
> | Action | microsoft.web/sites/snapshots/read | Récupère les captures instantanées de Web Apps. |
> | Action | Microsoft.Web/sites/sourcecontrols/Delete | Supprime les paramètres de configuration du contrôle de code source d’application web. |
> | Action | Microsoft.Web/sites/sourcecontrols/Read | Récupère les paramètres de configuration du contrôle de code source d’application web. |
> | Action | Microsoft.Web/sites/sourcecontrols/Write | Met à jour les paramètres de configuration du contrôle de code source d’application web. |
> | Action | Microsoft.Web/sites/start/Action | Démarre une application web. |
> | Action | Microsoft.Web/sites/stop/Action | Arrête une application web. |
> | Action | microsoft.web/sites/sync/action | Synchronise Web Apps. |
> | Action | microsoft.web/sites/syncfunctiontriggers/action | Synchronise les déclencheurs de fonction. |
> | Action | microsoft.web/sites/triggeredwebjobs/delete | Supprime les tâches web déclenchées dans Web Apps. |
> | Action | microsoft.web/sites/triggeredwebjobs/history/read | Obtient l’historique des tâches web déclenchées de Web Apps. |
> | Action | microsoft.web/sites/triggeredwebjobs/read | Récupère les tâches web déclenchées dans Web Apps. |
> | Action | microsoft.web/sites/triggeredwebjobs/run/action | Exécute les tâches web déclenchées dans Web Apps. |
> | Action | microsoft.web/sites/usages/read | Récupère les utilisations Web Apps. |
> | Action | microsoft.web/sites/virtualnetworkconnections/delete | Supprime les connexions de réseau virtuel Web Apps. |
> | Action | microsoft.web/sites/virtualnetworkconnections/gateways/read | Récupère les passerelles de connexions de réseau virtuel Web Apps. |
> | Action | microsoft.web/sites/virtualnetworkconnections/gateways/write | Met à jour les passerelles de connexions de réseau virtuel Web Apps. |
> | Action | microsoft.web/sites/virtualnetworkconnections/read | Récupère les connexions de réseau virtuel Web Apps. |
> | Action | microsoft.web/sites/virtualnetworkconnections/write | Met à jour les connexions de réseau virtuel Web Apps. |
> | Action | microsoft.web/sites/webjobs/read | Récupère WebJobs de Web Apps. |
> | Action | Microsoft.Web/sites/Write | Crée une application web ou en met une à jour. |
> | Action | microsoft.web/skus/read | Récupère des références. |
> | Action | microsoft.web/sourcecontrols/read | Récupère les contrôles de code source. |
> | Action | microsoft.web/sourcecontrols/write | Met à jour les contrôles de code source. |
> | Action | microsoft.web/unregister/action | Désinscrit le fournisseur de ressources Microsoft.Web pour l’abonnement. |
> | Action | microsoft.web/validate/action | Valide. |
> | Action | microsoft.web/verifyhostingenvironmentvnet/action | Vérifie le réseau virtuel de l’environnement d’hébergement. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Type d’action | Opération | Description |
> | --- | --- | --- |
> | Action | Microsoft.WorkloadMonitor/components/read | Obtient les composants de la ressource |
> | Action | Microsoft.WorkloadMonitor/componentsSummary/read | Obtient le récapitulatif des composants |
> | Action | Microsoft.WorkloadMonitor/monitorInstances/read | Obtient les instances des moniteurs de la ressource |
> | Action | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Obtient le récapitulatif des instances de moniteurs |
> | Action | Microsoft.WorkloadMonitor/monitors/read | Obtient les moniteurs de la ressource |
> | Action | Microsoft.WorkloadMonitor/monitors/write | Configure le moniteur de la ressource |
> | Action | Microsoft.WorkloadMonitor/notificationSettings/read | Obtient les paramètres de notification de la ressource |
> | Action | Microsoft.WorkloadMonitor/notificationSettings/write | Configure les paramètres de notification de la ressource |
> | Action | Microsoft.WorkloadMonitor/operations/read | Obtient les opérations prises en charge |

## <a name="next-steps"></a>Étapes suivantes

- [Fournisseur de ressources correspondant au service](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Rôles intégrés pour les ressources Azure](custom-roles.md)
- [Rôles intégrés pour les ressources Azure](built-in-roles.md)
