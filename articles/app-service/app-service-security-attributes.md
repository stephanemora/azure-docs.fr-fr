---
title: Attributs de sécurité pour Azure App Service
description: Une liste de vérification des attributs de sécurité pour l’évaluation d’Azure App Service
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 1d7ab8008e8fbdb5f851f158d14f62bdea803f11
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001697"
---
# <a name="security-attributes-for-azure-app-service"></a>Attributs de sécurité pour Azure App Service

Cet article décrit les attributs de sécurité courantes intégrées à Azure App Service.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/non | Notes |
|---|---|--|
| Chiffrement au repos (par exemple, le chiffrement côté serveur, le chiffrement côté serveur avec des clés gérées par le client et d’autres fonctionnalités de chiffrement) | Oui | Contenu de fichier de site Web est stocké dans stockage Azure, qui chiffre automatiquement le contenu au repos. Consultez [chiffrement de stockage Azure pour les données au repos](../storage/common/storage-service-encryption.md).<br><br>Les secrets fournis par le client sont chiffrées au repos. Les clés secrètes sont chiffrées au repos, tandis que stockées dans les bases de données de configuration App Service.<br><br>Disques attachés localement peuvent éventuellement être utilisés comme stockage temporaire par les sites Web (D:\local et % %TMP%). Disques attachés localement ne sont pas chiffrées au repos. |
| Chiffrement en transit (telles que le chiffrement de ExpressRoute, de cryptage de réseau virtuel et le cryptage de réseau)| Oui | Les clients peuvent configurer des sites web pour exiger et utiliser le protocole HTTPS pour le trafic entrant. Consultez le blog [comment rendre un Azure App Service HTTPS uniquement](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Oui | Les clients peuvent choisir stocker des secrets d’application dans Key Vault et les récupérer lors de l’exécution. Consultez [fait référence à utiliser Key Vault pour App Service et Azure Functions (version préliminaire)](app-service-key-vault-references.md).|
| Chiffrement au niveau colonne (Services de données Azure)| N/A | |
| Appels d’API chiffrés| Oui | Les appels de gestion pour configurer App Service se produisent [Azure Resource Manager](../azure-resource-manager/index.yml) appels via le protocole HTTPS. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui | Actuellement disponible en version préliminaire pour App Service. Consultez [Restrictions d’accès Azure App Service](app-service-ip-restrictions.md). |
| Prise en charge l’injection de réseau virtuel| Oui | Environnements App Service sont des implémentations privées d’App Service dédié à un client unique injecté dans le réseau virtuel d’un client. Consultez [Introduction aux environnements App Service](environment/intro.md). |
| Prise en charge de l’isolement réseau et Firewalling| Oui | Pour la version publique d’architecture mutualisée d’App Service, les clients peuvent configurer réseau ACL (Restrictions d’adresse IP) pour verrouiller le trafic entrant autorisé.  Consultez [Restrictions d’accès Azure App Service](app-service-ip-restrictions.md).  Les environnements App Service sont déployés directement dans les réseaux virtuels et par conséquent, peuvent être sécurisés avec des groupes de sécurité réseau. |
| Prise en charge de tunneling de forcé| Oui | Les environnements App Service peuvent être déployés dans le réseau virtuel d’un client où le tunneling forcé est configuré. Les clients doivent suivre les instructions de [configurer votre environnement App Service avec le tunneling forcé](environment/forced-tunnel-support.md). |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | App Service s’intègre à Application Insights pour les langages qui prennent en charge d’Application Insights (.NET Framework, .NET Core, Java et Node.JS).  Consultez [performances d’analyse Azure App Service](../azure-monitor/app/azure-web-apps.md). App Service envoie également des mesures d’application dans Azure Monitor. Consultez [surveiller des applications dans Azure App Service](web-sites-monitor.md). |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Authentication| Oui | Les clients peuvent créer des applications App Service qui s’intègrent automatiquement [Azure Active Directory (Azure AD)](../active-directory/index.md) ainsi que d’autres fournisseurs d’identité compatible OAuth ; consultez [authentification et autorisation dans Azure App Service](overview-authentication-authorization.md). Pour l’accès de gestion pour les ressources des services d’application, tout accès sont contrôlé par une combinaison de principal Azure AD authentifié et les rôles Azure RBAC Resource Manager. |
| Authorization| Oui | Pour l’accès de gestion pour les ressources des services d’application, tout accès sont contrôlé par une combinaison de principal Azure AD authentifié et les rôles Azure RBAC Resource Manager.  |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | Toutes les opérations de gestion effectuées sur les objets de Service d’application se produisent [Azure Resource Manager](../azure-resource-manager/index.yml). Historique des journaux de ces opérations sont disponibles à la fois dans le portail et via l’interface CLI ; consultez [opérations de fournisseur de ressources Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb) et [az moniteur-journal d’activité](/cli/azure/monitor/activity-log). |
| Audit et consignation de plan de données | Non | Le plan de données pour App Service est un partage de fichiers distant qui contient le contenu du site web déployé d’un client.  Il n’existe pas d’audit de partage de fichiers distant. |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui | Pour les opérations de gestion, l’état d’une configuration d’App Service peut être exportée en tant qu’un modèle Azure Resource Manager et avec contrôle de version au fil du temps. Pour les opérations d’exécution, les clients peuvent conserver plusieurs versions de live différents d’une application à l’aide de la fonctionnalité d’emplacements de déploiement App Service. | 

