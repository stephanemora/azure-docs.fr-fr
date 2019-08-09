---
title: Attributs de sécurité des services Azure
description: Check-list des attributs de sécurité pour l’évaluation des services Azure
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 43eb7e5c4cab722eb97f9e2fe819c9c79bae45d9
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828772"
---
# <a name="security-attributes-for-azure-services"></a>Attributs de sécurité des services Azure

Cet article regroupe les attributs de sécurité courants de certains services Azure. Un attribut de sécurité est une qualité ou une fonctionnalité d’un service Azure. Il contribue à la capacité du service à empêcher, détecter et répondre aux failles de sécurité.

Les attributs de sécurité sont classés de la façon suivante :
* Préventifs
* Segmentation du réseau
* Détection
* Prise en charge de la gestion des identités et des accès
* Piste d'audit
* Contrôles d'accès (le cas échéant)
* Gestion de la configuration (le cas échéant)

Dans chaque catégorie, nous mentionnons « Oui » ou « Non » pour indiquer si un attribut est utilisé. Pour certains services, nous mentionnons « N/A » pour un attribut qui n’est pas applicable. Nous spécifions éventuellement aussi une note ou un lien vers d’autres informations relatives à un attribut.

## <a name="api-managementazureapi-managementapi-management-security-attributes"></a>[Gestion des API](/azure/api-management/api-management-security-attributes)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | Les données sensibles telles que les certificats, les clés et les valeurs secrètes sont chiffrées à l'aide de clés d'instance de service gérées par le service. |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel) | OUI | Le chiffrement [Express Route](/azure/expressroute/index) et VNet est fourni par la [mise en réseau Azure](/azure/virtual-network/index). |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Non | Toutes les clés de chiffrement sont propres à l'instance de service et gérées par ce dernier. |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Les appels de plan de gestion se font par le biais d'[Azure Resource Manager](/azure/azure-resource-manager/index) sur TLS. Un jeton web JSON valide (JWT) est nécessaire.  Les appels de plan de données peuvent être sécurisés via TLS et un des mécanismes d’authentification pris en charge (certificat client ou JWT, par exemple).
 |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non | |
| Prise en charge de l’injection de réseau virtuel| OUI | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | À l’aide de groupes de sécurité réseau (NSG) et Azure Application Gateway (ou une autre appliance logicielle) respectivement. |
| Prise en charge du tunneling forcé| OUI | La gestion réseau Azure fournit le tunneling forcé. |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | |
| Authorization| OUI | |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | [Journaux d’activité Azure Monitor](/azure/azure-monitor/platform/activity-logs-overview) |
| Journalisation et audit du plan de données| OUI | [Journaux de diagnostic Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) et (éventuellement) [Azure Application Insights](/azure/azure-monitor/app/app-insights-overview).|

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | À l’aide du [Kit de ressources DevOps Gestion des API Azure](https://aka.ms/apimdevops) |

### <a name="vulnerability-scans-false-positives"></a>Faux-positifs des analyses des vulnérabilités

Cette section traite des vulnérabilités courantes, qui n’affectent pas Gestion des API Azure.

| Vulnérabilité               | Description                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed constitue une vulnérabilité en matière d'implémentation de l'extension TLS SessionTicket disponible dans certains produits F5. Elle permet la fuite (« bleeding ») d'un maximum de 31 octets de données à partir de la mémoire non initialisée. Ce problème est dû au remplissage de la pile TLS d'un ID de session, transmis par le client, avec des données à hauteur de 32 octets. |


## <a name="app-serviceazureapp-serviceapp-service-security-attributes"></a>[App Service](/azure/app-service/app-service-security-attributes)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | OUI | Le contenu des fichiers du site web est stocké dans le Stockage Azure, qui chiffre automatiquement le contenu au repos. Pour en savoir plus, consultez [Chiffrement du Stockage Azure pour les données au repos](/azure/storage/common/storage-service-encryption).<br><br>Les secrets fournis par le client sont chiffrées au repos. Les secrets sont chiffrés au repos lorsqu’ils sont stockés dans les bases de données de configuration d’App Service.<br><br>Les disques joints localement peuvent optionnellement être utilisés comme stockage temporaire par les sites web (D:\local et %TMP%). Les disques joints localement ne sont pas chiffrés au repos. |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel) | OUI | Les clients peuvent configurer les sites web pour exiger et utiliser le HTTPS pour le trafic entrant. Consultez le blog [Comment limiter une instance d’Azure App Service au HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| OUI | Les clients peuvent choisir de stocker les secrets d’application dans Key Vault et de les récupérer au moment de l’exécution. Pour en savoir plus, veuillez consulter [Utiliser des références Key Vault pour App Service et Azure Functions (préversion)](/azure/app-service/app-service-key-vault-references).|
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Les appels de gestion pour configurer App Service se font via des appels [Azure Resource Manager](/azure/azure-resource-manager/index) par le biais du protocole HTTPS. |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI | Actuellement disponible en préversion pour App Service. Pour en savoir plus, veuillez consulter [Restrictions d’accès dans Azure App Service](/azure/app-service/app-service-ip-restrictions). |
| Prise en charge de l’injection de réseau virtuel| OUI | Les environnements de services applicatifs sont des implémentations privées d’App Service dédiées à un client unique injecté dans le réseau virtuel d’un client. Pour en savoir plus, veuillez consulter [Présentation des environnements App Service](/azure/app-service/environment/intro). |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Pour la variante publique multi-locataires d’App Service, les clients peuvent configurer les listes de contrôle d’accès (Restrictions d’IP) du réseau pour verrouiller le trafic entrant autorisé.  Pour en savoir plus, veuillez consulter [Restrictions d’accès dans Azure App Service](/azure/app-service/app-service-ip-restrictions).  Les environnements App Service sont déployés directement dans des réseaux virtuels et peuvent donc être sécurisés avec les groupes de sécurité réseau. |
| Prise en charge du tunneling forcé| OUI | Les environnements App Service peuvent être déployés sur le réseau virtuel d’un client où le tunneling forcé est configuré. Les clients doivent suivre les instructions de la section [Configurer votre instance d’App Service Environment avec le tunneling forcé](/azure/app-service/environment/forced-tunnel-support). |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | App Service s’intègre avec Application Insights pour les langages qui prennent en charge Application Insights (.NET Framework complet, .NET Core, Java et Node.JS).  Pour en savoir plus, veuillez consulter [Analyser les performances d’Azure App Service](/azure/azure-monitor/app/azure-web-apps). App Service envoie également les mesures de l’application dans Azure Monitor. Pour en savoir plus, veuillez consulter [Superviser les applications dans Azure App Service](/azure/app-service/web-sites-monitor). |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Les clients peuvent créer des applications sur App Service qui s’intègrent automatiquement avec [Azure Active Directory (Azure AD)](/azure/active-directory/index) ainsi que d’autres fournisseurs d’identité compatibles avec OAuth. Pour en savoir plus, veuillez consulter [Authentification et autorisation dans Azure App Service](/azure/app-service/overview-authentication-authorization). Pour la gestion de l’accès aux ressources App Service, tous les accès sont contrôlés par une combinaison de principaux authentifiés par Azure AD et de rôles RBAC d’Azure Resource Manager. |
| Authorization| OUI | Pour la gestion de l’accès aux ressources App Service, tous les accès sont contrôlés par une combinaison de principaux authentifiés par Azure AD et de rôles RBAC d’Azure Resource Manager.  |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Toutes les opérations de gestion effectuées sur les objets App Service se font via [Azure Resource Manager](/azure/azure-resource-manager/index). Les journaux historiques de ces opérations sont disponibles à la fois dans le portail et via l’interface de ligne de commande. Pour en savoir plus, veuillez consulter [Opérations du fournisseur de ressources Azure Resource Manager](/azure/role-based-access-control/resource-provider-operations#microsoftweb) et [az monitor activity-log](/cli/azure/monitor/activity-log). |
| Journalisation et audit du plan de données | Non | Le plan de données pour App Service est un partage de fichiers distant contenant le contenu du site web déployé par un client.  Il n’y a pas d’audit du partage de fichiers distant. |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Pour les opérations de gestion, l’état d’une configuration d’App Service peut être exporté sous forme de modèle Azure Resource Manager et géré avec le temps. Pour les opérations d’exécution, les clients peuvent gérer plusieurs versions en direct d’une application à l’aide des emplacements de déploiement App Service. | 



## <a name="azure-resource-managerazureazure-resource-managerazure-resource-manager-security-attributes"></a>[Azure Resource Manager](/azure/azure-resource-manager/azure-resource-manager-security-attributes)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | OUI |  |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel) | OUI | HTTPS/TLS. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| N/A | Azure Resource Manager ne stocke aucun contenu client, uniquement des données de contrôle. |
| Chiffrement au niveau des colonnes (Azure Data Services)| OUI | |
| Appels d’API chiffrés| OUI | |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non | |
| Prise en charge de l’injection de réseau virtuel| OUI | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Non |  |
| Prise en charge du tunneling forcé| Non |  |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| Non | |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Basée sur [Azure Active Directory](/azure/active-directory).|
| Authorization| OUI | |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Les journaux d’activité exposent toutes les opérations d’écriture (PUT, POST, DELETE) effectuées sur vos ressources ; consultez [Afficher les journaux d’activité pour surveiller les actions sur les ressources](/azure/azure-resource-manager/resource-group-audit). |
| Journalisation et audit du plan de données| N/A | |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI |  |


## <a name="azure-backupazurebackupbackup-security-attributes"></a>[Sauvegarde Azure](/azure/backup/backup-security-attributes)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement)| OUI | Utilisation d’un chiffrement du service de stockage pour les comptes de stockage. |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel) | Non | Utilisation du protocole HTTPS. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Non |  |
| Chiffrement au niveau des colonnes (Azure Data Services)| Non |  |
| Appels d’API chiffrés| OUI |  |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non |  |
| Prise en charge de l’injection de réseau virtuel| Non |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Le tunneling forcé est pris en charge pour la sauvegarde de machine virtuelle. Le tunneling forcé n’est pas pris en charge pour les charges de travail s’exécutant à l’intérieur de machines virtuelles. |
| Prise en charge du tunneling forcé| Non |  |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Log Analytics est pris en charge via des journaux de diagnostic. Pour plus d’informations, consultez [Superviser les charges de travail protégées de Sauvegarde Azure à l’aide de Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | L’authentification est effectuée via Azure Active Directory. |
| Authorization| OUI | Des rôles RBAC intégrés et gérés par le client sont utilisés. Pour plus d’informations, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer les points de récupération de Sauvegarde Azure](/azure/backup/backup-rbac-rs-vault). |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Toutes les actions déclenchées par le client à partir du portail Azure sont consignées dans des journaux d’activité. |
| Journalisation et audit du plan de données| Non | Le plan de données de Sauvegarde Azure n’est pas accessible directement.  |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI|  |

## <a name="cosmos-dbazurecosmos-dbcosmos-db-security-attributes"></a>[Cosmos DB](/azure/cosmos-db/cosmos-db-security-attributes)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | OUI | Par défaut, toutes les bases de données et sauvegardes Cosmos DB sont chiffrées (voir [Chiffrement des données dans Azure Cosmos DB](/azure/cosmos-db/database-encryption-at-rest)). Le chiffrement côté serveur avec des clés gérées par le client n’est pas pris en charge. |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel)| OUI | Toutes les données Azure Cosmos DB sont chiffrées en transit. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Non |  |
| Chiffrement au niveau des colonnes (Azure Data Services)| OUI | Uniquement dans l’API Table Premium. Certaines API ne prennent pas en charge cette fonctionnalité. Voir [Présentation d’Azure Cosmos DB : API Table](/azure/cosmos-db/table-introduction). |
| Appels d’API chiffrés| OUI | Toutes les connexions à Azure Cosmos DB prennent en charge le protocole HTTPS. Azure Cosmos DB prend également en charge les connexions utilisant le protocole TLS 1.2, mais celui-ci n’est pas encore appliqué. Si des clients désactivent une protocole TLS de niveau inférieur de leur côté, ils peuvent s’assurer de se connecter à Cosmos DB.  |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI |  |
| Prise en charge de l’injection de réseau virtuel| OUI | Avec un point de terminaison de service de réseau virtuel, vous pouvez configurer un compte Azure Cosmos DB pour n’autoriser l’accès qu’à partir d’un sous-réseau spécifique d’un réseau virtuel. Vous pouvez également combiner un accès à partir d’un réseau virtuel avec des règles de pare-feu.  Voir [Accéder à Azure Cosmos DB à partir de réseaux virtuels](/azure/cosmos-db/vnet-service-endpoint). |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Avec la prise en charge du pare-feu, vous pouvez configurer votre compte Azure Cosmos pour autoriser l’accès uniquement à partir d’un ensemble approuvé d’adresses IP, d’une plage d’adresses IP ou de services cloud. Voir [Configurer un pare-feu IP dans Azure Cosmos DB](/azure/cosmos-db/how-to-configure-firewall).|
| Prise en charge du tunneling forcé| OUI | Peut être configuré côté client sur le réseau virtuel où se trouvent les machines virtuelles.   |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Toutes les demandes envoyées à Azure Cosmos DB sont journalisées. Les journalisations d’[Azure Monitoring](/azure/azure-monitor/overview), de Métriques Azure et d’Audit Azure sont prises en charge.  Vous pouvez journaliser des informations correspondant à des demandes de plan de données, des statistiques d’exécution de requête, du texte de requête et des demandes MongoDB. Vous pouvez également configurer des alertes. |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Oui au niveau du compte de base de données. Au niveau du plan de données, Cosmos DB utilise un accès à l’aide de jetons de ressource et de clés. |
| Authorization| OUI | Prise en charge au niveau du compte Azure Cosmos avec des clés principales (primaire et secondaire) et des jetons de ressource. Vous pouvez obtenir un accès en lecture/écriture ou en lecture seule aux données avec des clés principales. Les jetons de ressource autorisent un accès temporaire à des ressources telles que des documents et des conteneurs. |

### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Journal d’activité Azure pour des opérations au niveau d’un compte : pare-feu, réseaux virtuels, accès aux clés et IAM. |
| Journalisation et audit du plan de données | OUI | Journalisation de la surveillance des diagnostics pour les opérations au niveau conteneur. Par exemple, création de conteneur, approvisionnement de débit, stratégies d’indexation et opérations CRUD sur des documents. |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| Non  | | 

### <a name="additional-security-attributes-for-cosmos-db"></a>Attributs de sécurité supplémentaires pour Cosmos DB

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Partage des ressources cross-origin (CORS) | OUI | Voir [Configurer le partage des ressources cross-origin (CORS)](/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing). |


## <a name="event-hubsazureevent-hubsevent-hubs-security-attributes"></a>[Hubs d'événements](/azure/event-hubs/event-hubs-security-attributes)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) |  OUI | |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel) | OUI | |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Non |  |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI |  |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI |  |
| Prise en charge de l’injection de réseau virtuel| Non | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI |  |
| Prise en charge du tunneling forcé| Non |  |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | |
| Authorization|  OUI | |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI |  |
| Journalisation et audit du plan de données| OUI |   |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | |


## <a name="expressrouteazureexpressrouteexpressroute-security-attributes"></a>[ExpressRoute](/azure/expressroute/expressroute-security-attributes)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) |  N/A | ExpressRoute ne stocke pas les données client. |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel) | Non | |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| N/A |  |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Via [Azure Resource Manager](/azure/azure-resource-manager/index) et HTTPS. |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| N/A |  |
| Prise en charge de l’injection de réseau virtuel| N/A | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Chaque client est contenu dans son propre domaine de routage et acheminé vers son propre réseau virtuel |
| Prise en charge du tunneling forcé| N/A | Via le protocole de passerelle frontière (BGP). |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Consultez [Supervision, métriques et alertes ExpressRoute](/azure/expressroute/expressroute-monitoring-metrics-alerts).|

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Compte de service de passerelle pour Microsoft (GWM) (contrôleur) ; Accès JIT (juste-à-temps) pour le développement et l’exploitation. |
| Authorization|  OUI |Compte de service de passerelle pour Microsoft (GWM) (contrôleur) ; Accès JIT (juste-à-temps) pour le développement et l’exploitation. |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes| 
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI |  |
| Journalisation et audit du plan de données| Non |   |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Via le fournisseur de ressources réseau (NRP). |


## <a name="key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Key Vault](../../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | OUI | Tous les objets sont chiffrés. |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel) | OUI | Toutes les communications se font par le biais d’appels d’API chiffrés |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| OUI | Le client contrôle toutes les clés de son Key Vault. Lorsque des clés sauvegardées avec HSM (Hardware Security Module) sont spécifiées, un module HSM Niveau 2 FIPS protège la clé, le certificat ou le secret. |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A |  |
| Appels d’API chiffrés| OUI | Utilisation du protocole HTTPS. |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI | Utilisation de points de terminaison de service de réseau virtuel. |
| Prise en charge de l’injection de réseau virtuel| Non |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Utilisation de règles de pare-feu de réseau virtuel. |
| Prise en charge du tunneling forcé| Non |  |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Utilisation de Log Analytics. |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | L’authentification est effectuée via Azure Active Directory. |
| Authorization| OUI | Utilisation d’une stratégie d’accès à Key Vault. |

### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion/contrôle| OUI | Utilisation de Log Analytics. |
| Journalisation et audit du plan de données| OUI | Utilisation de Log Analytics. |

### <a name="access-controls"></a>Contrôles d’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Contrôles d’accès au plan de gestion/contrôle | OUI | Contrôle d’accès en fonction du rôle (RBAC) Azure Resource Manager |
| Contrôles d’accès au plan de données (à chaque niveau de service) | OUI | Stratégie d’accès à Key Vault |

## <a name="load-balancerload-balancerload-balancer-security-attributesmd"></a>[Équilibreur de charge](../../load-balancer/load-balancer-security-attributes.md)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | N/A | |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel)| N/A | |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| N/A | |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Via [Azure Resource Manager](/azure/azure-resource-manager/index). |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| N/A | |
| Prise en charge de l’injection de réseau virtuel| N/A | . |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| N/A |  |
| Prise en charge du tunneling forcé| N/A | |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Consultez les [Journaux Azure Monitor pour une instance publique de Basic Load Balancer](/azure/load-balancer/load-balancer-monitor-log). |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| N/A |  |
| Authorization| N/A |  |

### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Consultez les [Journaux Azure Monitor pour une instance publique de Basic Load Balancer](/azure/load-balancer/load-balancer-monitor-log). |
| Journalisation et audit du plan de données | N/A |  |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| N/A |  | 

## <a name="service-bus-messagingazureservice-bus-messagingservice-bus-messaging-security-attributes"></a>[Messagerie Service Bus](/azure/service-bus-messaging/service-bus-messaging-security-attributes)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) |  Oui pour le chiffrement côté serveur au repos par défaut. | Les clés gérées par le client et du BYOK ne sont pas encore prises en charge. Le chiffrement côté client est sous la responsabilité du client |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel) | OUI | Prend en charge le mécanisme HTTPS/TLS standard. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Non |   |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Les appels d’API sont effectués via [Azure Resource Manager](/azure/azure-resource-manager/index) et HTTPS. |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui (niveau Premium uniquement) | Les points de terminaison de service de réseau virtuel sont uniquement pris en charge pour le [niveau Service Bus Premium](/azure/service-bus-messaging/service-bus-premium-messaging). |
| Prise en charge de l’injection de réseau virtuel| Non | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Oui (niveau Premium uniquement) |  |
| Prise en charge du tunneling forcé| Non |  |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Prise en charge par le biais de [Azure Monitor and Alerts](/azure/service-bus-messaging/service-bus-metrics-azure-monitor). |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Géré par le biais de [l’identité de service managé Azure Active Directory ](/azure/service-bus-messaging/service-bus-managed-service-identity); consultez [Authentification et autorisation Service Bus](/azure/service-bus-messaging/service-bus-authentication-and-authorization).|
| Authorization| OUI | Prend en charge l’autorisation via [RBAC](/azure/service-bus-messaging/service-bus-role-based-access-control) (préversion) et jeton SAS ; consultez [Authentification et autorisation Service Bus](/azure/service-bus-messaging/service-bus-authentication-and-authorization). |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Les journaux des opérations sont disponibles ; consultez [Journaux de diagnostic Service Bus](/azure/service-bus-messaging/service-bus-diagnostic-logs).  |
| Journalisation et audit du plan de données| Non |  |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Prend en charge le contrôle de version du fournisseur de ressources via l’[API Azure Resource Manager](/rest/api/resources/).|


## <a name="service-bus-relayazureservice-bus-relayservice-bus-relay-security-attributes"></a>[Service Bus Relay](/azure/service-bus-relay/service-bus-relay-security-attributes)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) |  N/A | Le relais est un socket web et ne persiste pas dans les données. |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel) | OUI | Le service nécessite TLS. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Non | Utilise uniquement les certificats Microsoft TLS.  |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | HTTPS. |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Non |  |
| Prise en charge du tunneling forcé| N/A | Le relais est le tunnel TLS  |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Via SAS. |
| Authorization|  OUI | Via SAS. |

### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Via [Azure Resource Manager](/azure/azure-resource-manager/index). |
| Journalisation et audit du plan de données| OUI | La réussite, l’échec et les erreurs de la connexion sont journalisés.  |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Via [Azure Resource Manager](/azure/azure-resource-manager/index).|

## <a name="service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Service Fabric](../../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | OUI | Le cluster et le groupe de machines virtuelles identiques sur lequel le cluster est basé appartiennent au client. Le chiffrement de disque Azure peut être activé sur le groupe de machines virtuelles identiques. |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel) | OUI | Le cluster et le groupe de machines virtuelles identiques sur lequel le cluster est basé appartiennent au client. Le chiffrement de disque Azure peut être activé sur le groupe de machines virtuelles identiques. |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A |  |
| Appels d’API chiffrés| OUI | Les appels d’API Service Fabric sont effectués via Azure Resource Manager. Un jeton web JSON valide (JWT) est nécessaire. |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI |  |
| Prise en charge de l’injection de réseau virtuel| OUI |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Utilisation de groupes de sécurité réseau (NSG). |
| Prise en charge du tunneling forcé| OUI | La gestion réseau Azure fournit le tunneling forcé. |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Prise en charge de l’utilisation de la surveillance Azure et prise en charge des tiers. |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | L’authentification est effectuée via Azure Active Directory. |
| Authorization| OUI | Gestion des identités et des accès (IAM) pour les appels via SFRP. Les appels effectués directement au point de terminaison du cluster prennent en charge deux rôles : utilisateur et administrateur. Le client peut mapper les API sur n’importe lequel des deux rôles. |

### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Toutes les opérations de plan de contrôle sont exécutées par le biais de processus pour l’audit et les approbations. |
| Journalisation et audit du plan de données| N/A | Le cluster appartient au client.  |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | |

## <a name="sql-databasesql-databasesql-database-security-attributesmd"></a>[Base de données SQL](../../sql-database/sql-database-security-attributes.md)

SQL Database inclut une [base de données unique](/azure/sql-database/sql-database-single-index) et une [instance gérée](/azure/sql-database/sql-database-managed-instance). Les entrées suivantes s’appliquent aux deux offres, sauf mention contraire.

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | OUI | Appelé « chiffrement en cours d’utilisation », comme décrit dans l’article [Always Encrypted](/azure/sql-database/sql-database-always-encrypted). Le chiffrement côté serveur utilise le [chiffrement transparent des données](/azure/sql-database/transparent-data-encryption-azure-sql).|
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel) | OUI | Utilisation du protocole HTTPS. |
| Gestion des clés de chiffrement, comme CMK ou BYOK| OUI | Une gestion de clé par le service et une gestion de clé par le client sont proposées. Cette dernière est proposée par [Azure Key Vault](/azure/key-vault/index). |
| Chiffrement au niveau des colonnes fourni par les services de données Azure| OUI | Via [Always Encrypted](/azure/sql-database/sql-database-always-encrypted). |
| Appels d’API chiffrés| OUI | À l’aide de HTTPS/SSL. |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI | S’applique exclusivement à une [base de données unique](/azure/sql-database/sql-database-single-index). |
| Prise en charge de l’injection par Azure Virtual Network| OUI | S’applique exclusivement à une [instance gérée](/azure/sql-database/sql-database-managed-instance). |
| Prise en charge de l’isolement et du pare-feu réseau| OUI | Pare-feu au niveau de la base de données et du serveur. L’isolement réseau est réservé à une [instance gérée](/azure/sql-database/sql-database-managed-instance). |
| Prise en charge du tunneling forcé| OUI | [Instance gérée](/azure/sql-database/sql-database-managed-instance) via un VPN [ExpressRoute](/azure/expressroute/index). |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure, comme Log Analytics ou Application Insights| OUI | SecureSphere, la solution SIEM d’Imperva, est également prise en charge à travers l’intégration d’[Azure Event Hubs](/azure/event-hubs/index) via l’[audit SQL](/azure/sql-database/sql-database-auditing). |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Azure Active Directory (Azure AD) |
| Authorization| OUI | Aucun |

### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de contrôle et de gestion| OUI | Oui, seulement pour certains événements |
| Journalisation et audit du plan de données | OUI | Via l’[audit SQL](/azure/sql-database/sql-database-auditing) |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion des configurations, comme la gestion des versions des configurations| Non  | Aucun |

### <a name="additional-security-attributes-for-sql-database"></a>Attributs de sécurité supplémentaires pour SQL Database

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Préventif : évaluation des vulnérabilités | OUI | Voir [Le service d’évaluation des vulnérabilités SQL vous permet d’identifier des vulnérabilités de base de données](/azure/sql-database/sql-vulnerability-assessment). |
| Préventif : découverte et classification des données  | OUI | Voir [Découverte et classification des données pour Azure SQL Database et SQL Data Warehouse](/azure/sql-database/sql-database-data-discovery-and-classification) |
| Détection : détection des menaces | OUI | Voir [Advanced Threat Protection pour Azure SQL Database](/azure/sql-database/sql-database-threat-detection-overview). |


## <a name="storageazurestoragecommonstorage-security-attributes"></a>[Stockage](/azure/storage/common/storage-security-attributes)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | OUI |  |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel) | OUI | Prend en charge les mécanismes HTTPS/TLS standards.  Les utilisateurs peuvent également chiffrer les données avant leur transmission au service. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| OUI | Consultez [Chiffrement du service de stockage à l’aide de clés gérées par le client dans Azure Key Vault](/azure/storage/common/storage-service-encryption-customer-managed-keys?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A |  |
| Appels d’API chiffrés| OUI |  |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI |  |
| Prise en charge de l’injection de réseau virtuel| N/A |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | |
| Prise en charge du tunneling forcé| N/A |  |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Indicateurs de performance Azure Monitor disponible à présent, début de la préversion des journaux d’activité |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Azure Active Directory, clé partagée, jeton d’accès partagé. |
| Authorization| OUI | Autorisation de support via le contrôle d’accès en fonction du rôle (RBAC), les listes de contrôle d'accès POSIX et les jetons SAS |

### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle | OUI | Journal d’activité Azure Resource Manager |
| Journalisation et audit du plan de données| OUI | Journaux de diagnostic de service et début de la préversion de la journalisation Azure Monitor  |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Prise en charge du contrôle de version du fournisseur de ressources via les API Azure Resource Manager |

## <a name="virtual-machines-and-virtual-machine-scale-sets"></a>Machines virtuelles et groupes de machines virtuelles identiques

[Machines virtuelles Linux](/azure/virtual-machines/windows/virtual-machines-windows-security-attributes) | [Machines virtuelles Windows](/azure/virtual-machines/windows/virtual-machines-windows-security-attributes) | [Groupe de machines virtuelles identiques](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-security-attributes)


### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | OUI | Consultez [Comment chiffrer une machine virtuelle Linux dans Azure](/azure/virtual-machines/linux/encrypt-disks) et [Chiffrer des disques virtuels sur une machine virtuelle Windows](/azure/virtual-machines/windows/encrypt-disks). |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel)| OUI | Le service Machines virtuelles Azure prend en charge [ExpressRoute](/azure/expressroute) et le chiffrement de réseau virtuel. Consultez [Chiffrement en transit sur des machines virtuelles](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| OUI | Les clés gérées par le client sont un scénario de chiffrement Azure pris en charge ; consultez [Vue d’ensemble du chiffrement Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Par le biais de HTTPS et de SSL. |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI | |
| Prise en charge de l’injection de réseau virtuel| OUI | . |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI |  |
| Prise en charge du tunneling forcé| OUI | Consultez [Configuration du tunneling forcé à l’aide du modèle de déploiement Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Consultez [Superviser et mettre à jour une machine virtuelle Linux dans Azure](/azure/virtual-machines/linux/tutorial-monitoring) et [Superviser et mettre à jour une machine virtuelle Windows dans Azure](/azure/virtual-machines/windows/tutorial-monitoring). |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI |  |
| Authorization| OUI |  |


### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI |  |
| Journalisation et audit du plan de données | Non |  |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI |  | 


## <a name="vpn-gatewayazurevpn-gatewayvpn-gateway-security-attributes"></a>[Passerelle VPN](/azure/vpn-gateway/vpn-gateway-security-attributes)

### <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | N/A | Données client en transit dans la passerelle VPN, ne stocke PAS les données client |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel)| OUI | Les passerelles VPN chiffrent les paquets client entre les passerelles VPN Azure et les périphériques VPN locaux du client (S2S) ou les clients VPN (P2S). Les passerelles VPN prennent également en charge le chiffrement entre réseaux virtuels. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Non | Les clés prépartagées spécifiées par le client sont chiffrées au repos, mais pas encore intégrées à CMK. |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Via [Azure Resource Manager](/azure/azure-resource-manager/index) et HTTPS  |

### <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| N/A | |
| Prise en charge de l’injection de réseau virtuel| N/A | . |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Les passerelles VPN sont des instances de machine virtuelle dédiées pour chaque réseau virtuel client  |
| Prise en charge du tunneling forcé| OUI |  |

### <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Consultez [Journaux de diagnostic Azure Monitor/alertes](/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log) & [Métriques Azure Monitor/alertes](/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric).  |

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) pour gérer le service et configurer la passerelle VPN Azure. |
| Authorization| OUI | Prise en charge de l'autorisation via [RBAC](/azure/role-based-access-control/overview). |

### <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Journal d’activité Azure Resource Manager. |
| Journalisation et audit du plan de données | OUI | [Journaux de diagnostic Azure Monitor](/azure/azure-resource-manager/resource-group-audit) pour la journalisation et l’audit de la connectivité VPN. |

### <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Pour les opérations de gestion, l’état d’une configuration de passerelle VN Azure peut être exporté sous forme de modèle Azure Resource Manager et géré au fil du temps. | 

