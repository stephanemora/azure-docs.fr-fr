---
title: Attributs de sécurité pour les services Azure
description: Check-list des attributs de sécurité couramment utilisés pour l'évaluation d'Azure Service Fabric
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 64accb70561d4c0282b3ee45935d955dba1c67c4
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474536"
---
# <a name="security-attributes-for-azure-services"></a>Attributs de sécurité pour les services Azure

Cet article rassemble les attributs de sécurité courants pour les services Azure sélectionnés. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="azure-api-managementapi-managementapi-management-security-attributesmd"></a>[Gestion des API Azure](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui (chiffrement côté service uniquement) | Données sensibles telles que les certificats, les clés et les valeurs nommées de secret sont chiffrées avec géré par le service, toutes les clés d’instance de service. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | [Express Route](../expressroute/index.yml) et chiffrement du réseau virtuel est fourni par [mise en réseau Azure](../virtual-network/index.yml). |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Non | Toutes les clés de chiffrement sont par instance de service et service managé. |
| Chiffrement au niveau colonne (Services de données Azure)| N/A | |
| Appels d’API chiffrés| Oui | Appels de plan de gestion sont effectuées via [Azure Resource Manager](../azure-resource-manager/index.yml) sur TLS. Un jeton web JSON valide (JWT) est nécessaire.  Appels de plan de données peuvent être sécurisées avec TLS et de mécanismes d’authentification pris en charge (par exemple, le certificat client ou le JWT).
 |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non | |
| Prise en charge l’injection de réseau virtuel| Oui | |
| Prise en charge des pare-feu et l’isolement réseau| Oui | À l’aide de groupes de sécurité réseau (NSG) et Azure Application Gateway (ou autre appliance logicielle) respectivement. |
| Prise en charge de tunneling de forcé| Oui | La gestion réseau Azure fournit le tunneling forcé. |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| Oui | |
| Authorization| Oui | |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | [Journaux d’activité Azure Monitor](../azure-monitor/platform/activity-logs-overview.md) |
| Audit et consignation de plan de données| Oui | [Journaux de diagnostic Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md) et (éventuellement) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui | À l’aide de la [Kit de ressources de DevOps de gestion des API Azure](https://aka.ms/apimdevops) |


## <a name="azure-app-serviceapp-serviceapp-service-security-attributesmd"></a>[Azure App Service](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (par exemple, le chiffrement côté serveur, le chiffrement côté serveur avec des clés gérées par le client et d’autres fonctionnalités de chiffrement) | Oui | Contenu de fichier de site Web est stocké dans stockage Azure, qui chiffre automatiquement le contenu au repos. Consultez [chiffrement de stockage Azure pour les données au repos](../storage/common/storage-service-encryption.md).<br><br>Les secrets fournis par le client sont chiffrées au repos. Les clés secrètes sont chiffrées au repos, tandis que stockées dans les bases de données de configuration App Service.<br><br>Disques attachés localement peuvent éventuellement être utilisés comme stockage temporaire par les sites Web (D:\local et % %TMP%). Disques attachés localement ne sont pas chiffrées au repos. |
| Chiffrement en transit (telles que le chiffrement de ExpressRoute, de cryptage de réseau virtuel et le cryptage de réseau)| Oui | Les clients peuvent configurer des sites web pour exiger et utiliser le protocole HTTPS pour le trafic entrant. Consultez le blog [comment rendre un Azure App Service HTTPS uniquement](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Oui | Les clients peuvent choisir stocker des secrets d’application dans Key Vault et les récupérer lors de l’exécution. Consultez [fait référence à utiliser Key Vault pour App Service et Azure Functions (version préliminaire)](../app-service/app-service-key-vault-references.md).|
| Chiffrement au niveau colonne (Services de données Azure)| N/A | |
| Appels d’API chiffrés| Oui | Les appels de gestion pour configurer App Service se produisent [Azure Resource Manager](../azure-resource-manager/index.yml) appels via le protocole HTTPS. |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui | Actuellement disponible en version préliminaire pour App Service. Consultez [Restrictions d’accès Azure App Service](../app-service/app-service-ip-restrictions.md). |
| Prise en charge l’injection de réseau virtuel| Oui | Environnements App Service sont des implémentations privées d’App Service dédié à un client unique injecté dans le réseau virtuel d’un client. Consultez [Introduction aux environnements App Service](../app-service/environment/intro.md). |
| Prise en charge de l’isolement réseau et Firewalling| Oui | Pour la version publique d’architecture mutualisée d’App Service, les clients peuvent configurer réseau ACL (Restrictions d’adresse IP) pour verrouiller le trafic entrant autorisé.  Consultez [Restrictions d’accès Azure App Service](../app-service/app-service-ip-restrictions.md).  Les environnements App Service sont déployés directement dans les réseaux virtuels et par conséquent, peuvent être sécurisés avec des groupes de sécurité réseau. |
| Prise en charge de tunneling de forcé| Oui | Les environnements App Service peuvent être déployés dans le réseau virtuel d’un client où le tunneling forcé est configuré. Les clients doivent suivre les instructions de [configurer votre environnement App Service avec le tunneling forcé](../app-service/environment/forced-tunnel-support.md). |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | App Service s’intègre à Application Insights pour les langages qui prennent en charge d’Application Insights (.NET Framework, .NET Core, Java et Node.JS).  Consultez [performances d’analyse Azure App Service](../azure-monitor/app/azure-web-apps.md). App Service envoie également des mesures d’application dans Azure Monitor. Consultez [surveiller des applications dans Azure App Service](../app-service/web-sites-monitor.md). |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| Oui | Les clients peuvent créer des applications App Service qui s’intègrent automatiquement [Azure Active Directory (Azure AD)](../active-directory/index.yml) ainsi que d’autres fournisseurs d’identité compatible OAuth ; consultez [authentification et autorisation dans Azure App Service](../app-service/overview-authentication-authorization.md). Pour l’accès de gestion pour les ressources des services d’application, tout accès sont contrôlé par une combinaison de principal Azure AD authentifié et les rôles Azure RBAC Resource Manager. |
| Authorization| Oui | Pour l’accès de gestion pour les ressources des services d’application, tout accès sont contrôlé par une combinaison de principal Azure AD authentifié et les rôles Azure RBAC Resource Manager.  |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | Toutes les opérations de gestion effectuées sur les objets de Service d’application se produisent [Azure Resource Manager](../azure-resource-manager/index.yml). Historique des journaux de ces opérations sont disponibles à la fois dans le portail et via l’interface CLI ; consultez [opérations de fournisseur de ressources Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb) et [az moniteur-journal d’activité](/cli/azure/monitor/activity-log). |
| Audit et consignation de plan de données | Non | Le plan de données pour App Service est un partage de fichiers distant qui contient un client s déployés contenu de site web.  Il n’existe pas d’audit de partage de fichiers distant. |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui | Pour les opérations de gestion, l’état d’une configuration d’App Service peut être exportée en tant qu’un modèle Azure Resource Manager et avec contrôle de version au fil du temps. Pour les opérations d’exécution, les clients peuvent conserver plusieurs versions de live différents d’une application à l’aide de la fonctionnalité d’emplacements de déploiement App Service. | 


## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure Resource Manager](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui |  |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | HTTPS/TLS. |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| N/A | Azure Resource Manager ne stocke aucun contenu client, seules les données de contrôle. |
| Chiffrement au niveau colonne (Services de données Azure)| Oui | |
| Appels d’API chiffrés| Oui | |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non | |
| Prise en charge l’injection de réseau virtuel| Oui | |
| Prise en charge des pare-feu et l’isolement réseau| Non |  |
| Prise en charge de tunneling de forcé| Non |  |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Non | |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| Oui | [Azure Active Directory](/azure/active-directory) en fonction.|
| Authorization| Oui | |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | Journaux d’activité exposent toutes les opérations d’écriture (PUT, POST, DELETE) effectuées sur vos ressources ; consultez [afficher les journaux d’activité pour auditer les actions sur les ressources](../azure-resource-manager/resource-group-audit.md). |
| Audit et consignation de plan de données| N/A | |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Sauvegarde Azure](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui | Utilisation d’un chiffrement du service de stockage pour les comptes de stockage. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Non | Utilisation du protocole HTTPS. |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Non |  |
| Chiffrement au niveau colonne (Services de données Azure)| Non |  |
| Appels d’API chiffrés| Oui |  |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non |  |
| Prise en charge l’injection de réseau virtuel| Non |  |
| Prise en charge des pare-feu et l’isolement réseau| Oui | Le tunneling forcé est pris en charge pour la sauvegarde de machine virtuelle. Le tunneling forcé n’est pas pris en charge pour les charges de travail s’exécutant à l’intérieur de machines virtuelles. |
| Prise en charge de tunneling de forcé| Non |  |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | Log Analytics est pris en charge via des journaux de diagnostic. Voir Superviser les charges de travail protégées de Sauvegarde Azure à l’aide de Log Analytics (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) pour plus d’informations. |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| Oui | L’authentification est effectuée via Azure Active Directory. |
| Authorization| Oui | Des rôles RBAC intégrés et gérés par le client sont utilisés. Consultez utiliser le contrôle d’accès pour gérer les points de récupération Azure Backup (/ / / sauvegarde-rbac-rs-coffre de sauvegarde azure) pour plus d’informations. |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | Toutes les actions déclenchées par le client à partir du portail Azure sont consignées dans des journaux d’activité. |
| Audit et consignation de plan de données| Non | Le plan de données de Sauvegarde Azure n’est pas accessible directement.  |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui|  |


## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui | Tous les objets sont chiffrés. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | Toutes les communications se font par le biais d’appels d’API chiffrés |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Oui | Le client contrôle toutes les clés de son Key Vault. Lorsque les clés de module (HSM) soutenu de sécurité matériel sont spécifiées, un module de sécurité 2 de niveau FIPS protège la clé, le certificat ou la clé secrète. |
| Chiffrement au niveau colonne (Services de données Azure)| N/A |  |
| Appels d’API chiffrés| Oui | Utilisation du protocole HTTPS. |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui | À l’aide de points de terminaison de service réseau virtuel (VNet). |
| Prise en charge l’injection de réseau virtuel| Non |  |
| Prise en charge des pare-feu et l’isolement réseau| Oui | À l’aide de règles de pare-feu de réseau virtuel. |
| Prise en charge de tunneling de forcé| Non |  |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | Utilisation de Log Analytics. |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| Oui | L’authentification est effectuée via Azure Active Directory. |
| Authorization| Oui | Utilisation d’une stratégie d’accès à Key Vault. |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion/contrôle| Oui | Utilisation de Log Analytics. |
| Audit et consignation de plan de données| Oui | Utilisation de Log Analytics. |

### <a name="access-controls"></a>Contrôles d’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Contrôles d’accès au plan de gestion/contrôle | Oui | Contrôle d’accès en fonction du rôle (RBAC) Azure Resource Manager |
| Contrôles d’accès au plan de données (à chaque niveau de service) | Oui | Stratégie d’accès à Key Vault |

## <a name="azure-service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Azure Service Bus Messaging](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>|  Oui pour le chiffrement au repos côté serveur par défaut. | Les clés gérées par le client et du BYOK ne sont pas prises en charge encore. Chiffrement côté client est la responsabilité du client |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | Prend en charge du mécanisme HTTPS/TLS standard. |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Non |   |
| Chiffrement au niveau colonne (Services de données Azure)| N/A | |
| Appels d’API chiffrés| Oui | Appels d’API sont effectuées via [Azure Resource Manager](../azure-resource-manager/index.yml) et HTTPS. |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui (niveau Premium uniquement) | Points de terminaison de service réseau virtuel sont pris en charge pour [niveau de Service Bus Premium](../service-bus-messaging/service-bus-premium-messaging.md) uniquement. |
| Prise en charge l’injection de réseau virtuel| Non | |
| Prise en charge des pare-feu et l’isolement réseau| Oui (niveau Premium uniquement) |  |
| Prise en charge de tunneling de forcé| Non |  |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | Prise en charge par le biais de [Azure Monitor et alertes](../service-bus-messaging/service-bus-metrics-azure-monitor.md). |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| Oui | Géré par le biais [Azure Active Directory Managed Service Identity](../service-bus-messaging/service-bus-managed-service-identity.md); consultez [authentification et autorisation Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md).|
| Authorization| Oui | Prend en charge d’autorisation via [RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) (version préliminaire) et SAS token ; consultez [authentification et autorisation Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md). |

### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | Journaux des opérations sont disponibles ; consultez [les journaux de diagnostic de Service Bus](../service-bus-messaging/service-bus-diagnostic-logs.md).  |
| Audit et consignation de plan de données| Non |  |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui | Prend en charge le contrôle de version du fournisseur de ressources via les [API Azure Resource Manager](/rest/api/resources/).|


## <a name="azure-service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Azure Service Bus Relay](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>|  N/A | Le relais est un socket web et ne conserve pas les données. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | Le service nécessite TLS. |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Non | Utilise uniquement les certificats de Microsoft TLS.  |
| Chiffrement au niveau colonne (Services de données Azure)| N/A | |
| Appels d’API chiffrés| Oui | HTTPS. |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non |  |
| Prise en charge des pare-feu et l’isolement réseau| Non |  |
| Prise en charge de tunneling de forcé| N/A | Le relais est le tunnel TLS  |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| Oui | Via SAS. |
| Authorization|  Oui | Via SAS. |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Audit et consignation de plan de données| Oui | Réussite de la connexion / échec et les erreurs et connecté.  |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|


## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui | Le cluster et le groupe de machines virtuelles identiques sur lequel le cluster est basé appartiennent au client. Chiffrement de disque Azure peut être activé sur les machines virtuelles identiques. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui |  |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Oui | Le cluster et le groupe de machines virtuelles identiques sur lequel le cluster est basé appartiennent au client. Chiffrement de disque Azure peut être activé sur les machines virtuelles identiques. |
| Chiffrement au niveau colonne (Services de données Azure)| N/A |  |
| Appels d’API chiffrés| Oui | Les appels d’API Service Fabric sont effectués via Azure Resource Manager. Un jeton web JSON valide (JWT) est nécessaire. |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui |  |
| Prise en charge l’injection de réseau virtuel| Oui |  |
| Prise en charge des pare-feu et l’isolement réseau| Oui | Utilisation de groupes de sécurité réseau (NSG). |
| Prise en charge de tunneling de forcé| Oui | La gestion réseau Azure fournit le tunneling forcé. |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | À l’aide de la prise en charge et la prise en charge par des tiers de surveillance Azure. |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| Oui | L’authentification est effectuée via Azure Active Directory. |
| Authorization| Oui | Gestion des identités et des accès (IAM) pour les appels via SFRP. Les appels effectués directement au point de terminaison du cluster prennent en charge deux rôles : utilisateur et administrateur. Le client peut mapper les API sur n’importe lequel des deux rôles. |

### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | Toutes les opérations de plan de contrôle sont exécutées par le biais de processus pour l’audit et les approbations. |
| Audit et consignation de plan de données| N/A | Le cluster appartient au client.  |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui | La configuration du service est déployée à l’aide du déploiement Azure, qui gère également sa version. La version du code (application et runtime) est gérée à l’aide de la build Azure.
 |

## <a name="azure-sql-databasesql-databasesql-database-security-attributesmd"></a>[Base de données SQL Azure](../sql-database/sql-database-security-attributes.md)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui | Appelée « chiffrement en cours d’utilisation », comme décrit dans l’article [Always Encrypted](../sql-database/sql-database-always-encrypted.md). Utilise le chiffrement côté service [chiffrement transparent des données](../sql-database/transparent-data-encryption-azure-sql.md) (TDE).|
| Chiffrement en transit :<ul><li>Chiffrement d’ExpressRoute</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | Utilisation du protocole HTTPS. |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Oui | Gestion de clé à la fois géré par le service et gérée par le client sont proposées (ce dernier via [Azure Key Vault](../key-vault/index.yml). |
| Chiffrement au niveau colonne (Services de données Azure)| Oui | Via [Always Encrypted](../sql-database/sql-database-always-encrypted.md). |
| Appels d’API chiffrés| Oui | À l’aide de HTTPS/SSL. |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui | S’applique aux [base de données unique](../sql-database/sql-database-single-index.yml) uniquement. |
| Prise en charge l’injection de réseau virtuel| Oui | S’applique aux [instance managée](../sql-database/sql-database-managed-instance.md) uniquement. |
| Prise en charge des pare-feu et l’isolement réseau| Oui | Pare-feu sur les deux de base de données - et au niveau du serveur ; isolement de réseau [instance managée](../sql-database/sql-database-managed-instance.md) uniquement |
| Prise en charge de tunneling de forcé| Oui | [instance managée](../sql-database/sql-database-managed-instance.md) via [Azure ExpressRoute](../expressroute/index.yml) VPN |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | La solution SIEM tiers proposées par Imperva (securesphere et) est également pris en charge, via [Azure Event Hubs](../event-hubs/index.yml) intégration via [SQL audit](../sql-database/sql-database-auditing.md). |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| Oui | Azure Active Directory |
| Authorization| Oui |  |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | Oui pour certains événements uniquement. |
| Audit et consignation de plan de données | Oui | Via [SQL audit](../sql-database/sql-database-auditing.md). |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Non  | | 

### <a name="additional-security-attributes-for-sql-database"></a>Attributs de sécurité supplémentaires pour la base de données SQL

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Préventives : évaluation des vulnérabilités | Oui | Consultez [évaluation des vulnérabilités SQL service vous aide à identifier les vulnérabilités de base de données](../sql-database/sql-vulnerability-assessment.md). |
| Préventives : découverte de données et la classification  | Oui | Consultez [découverte de données Azure SQL Database et SQL Data Warehouse et classification](../sql-database/sql-database-data-discovery-and-classification.md). |
| Détection : détection des menaces | Oui | Consultez [Advanced Threat Protection pour Azure SQL Database](../sql-database/sql-database-threat-detection-overview.md). |

## <a name="azure-storagestoragecommonstorage-security-attributesmd"></a>[Azure Storage](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui |  |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | Prend en charge les mécanismes HTTPS/TLS standard.  Les utilisateurs peuvent également chiffrer les données avant leur transmission au service. |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Oui | Consultez [Storage Service Encryption à l’aide de clés gérées par le client dans Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Chiffrement au niveau colonne (Services de données Azure)| N/A |  |
| Appels d’API chiffrés| Oui |  |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui |  |
| Prise en charge l’injection de réseau virtuel| N/A |  |
| Prise en charge des pare-feu et l’isolement réseau| Oui | |
| Prise en charge de tunneling de forcé| N/A |  |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | Mesures Azure Monitor disponible consigne désormais, version préliminaire de départ |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| Oui | Azure Active Directory, clé partagée, le jeton d’accès partagé. |
| Authorization| Oui | Autorisation de prise en charge par le biais de RBAC, les ACL POSIX et les jetons SAS |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion | Oui | Journal d’activité Azure Resource Manager |
| Audit et consignation de plan de données| Oui | Journaux de Diagnostic de service et l’aperçu de départ journalisation d’Azure Monitor  |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui | Prend en charge la gestion des versions du fournisseur de ressources via les API Azure Resource Manager |