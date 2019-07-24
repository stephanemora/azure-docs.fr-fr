---
title: Attributs de sécurité pour Azure App Service
description: Liste de contrôle des attributs de sécurité pour l’évaluation d’Azure App Service
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: e38e1479c1e094f691e8f22f0a48f9342cae5cf3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475082"
---
# <a name="security-attributes-for-azure-app-service"></a>Attributs de sécurité pour Azure App Service

Cet article documente les attributs de sécurité courants intégrés dans Azure App Service.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | OUI | Le contenu des fichiers du site web est stocké dans le Stockage Azure, qui chiffre automatiquement le contenu au repos. Pour en savoir plus, consultez [Chiffrement du Stockage Azure pour les données au repos](../storage/common/storage-service-encryption.md).<br><br>Les secrets fournis par le client sont chiffrées au repos. Les secrets sont chiffrés au repos lorsqu’ils sont stockés dans les bases de données de configuration d’App Service.<br><br>Les disques joints localement peuvent optionnellement être utilisés comme stockage temporaire par les sites web (D:\local et %TMP%). Les disques joints localement ne sont pas chiffrés au repos. |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel)| OUI | Les clients peuvent configurer les sites web pour exiger et utiliser le HTTPS pour le trafic entrant. Consultez le blog [Comment limiter une instance d’Azure App Service au HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| OUI | Les clients peuvent choisir de stocker les secrets d’application dans Key Vault et de les récupérer au moment de l’exécution. Pour en savoir plus, veuillez consulter [Utiliser des références Key Vault pour App Service et Azure Functions (préversion)](app-service-key-vault-references.md).|
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Les appels de gestion pour configurer App Service se font via des appels [Azure Resource Manager](../azure-resource-manager/index.yml) par le biais du protocole HTTPS. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI | Actuellement disponible en préversion pour App Service. Pour en savoir plus, veuillez consulter [Restrictions d’accès dans Azure App Service](app-service-ip-restrictions.md). |
| Prise en charge de l’injection de réseau virtuel| OUI | Les environnements de services applicatifs sont des implémentations privées d’App Service dédiées à un client unique injecté dans le réseau virtuel d’un client. Pour en savoir plus, veuillez consulter [Présentation des environnements App Service](environment/intro.md). |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Pour la variante publique multi-locataires d’App Service, les clients peuvent configurer les listes de contrôle d’accès (Restrictions d’IP) du réseau pour verrouiller le trafic entrant autorisé.  Pour en savoir plus, veuillez consulter [Restrictions d’accès dans Azure App Service](app-service-ip-restrictions.md).  Les environnements App Service sont déployés directement dans des réseaux virtuels et peuvent donc être sécurisés avec les groupes de sécurité réseau. |
| Prise en charge du tunneling forcé| OUI | Les environnements App Service peuvent être déployés sur le réseau virtuel d’un client où le tunneling forcé est configuré. Les clients doivent suivre les instructions de la section [Configurer votre instance d’App Service Environment avec le tunneling forcé](environment/forced-tunnel-support.md). |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | App Service s’intègre avec Application Insights pour les langages qui prennent en charge Application Insights (.NET Framework complet, .NET Core, Java et Node.JS).  Pour en savoir plus, veuillez consulter [Analyser les performances d’Azure App Service](../azure-monitor/app/azure-web-apps.md). App Service envoie également les mesures de l’application dans Azure Monitor. Pour en savoir plus, veuillez consulter [Superviser les applications dans Azure App Service](web-sites-monitor.md). |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Les clients peuvent créer des applications sur App Service qui s’intègrent automatiquement avec [Azure Active Directory (Azure AD)](../active-directory/index.yml) ainsi que d’autres fournisseurs d’identité compatibles avec OAuth. Pour en savoir plus, veuillez consulter [Authentification et autorisation dans Azure App Service](overview-authentication-authorization.md). Pour la gestion de l’accès aux ressources App Service, tous les accès sont contrôlés par une combinaison de principaux authentifiés par Azure AD et de rôles RBAC d’Azure Resource Manager. |
| Authorization| OUI | Pour la gestion de l’accès aux ressources App Service, tous les accès sont contrôlés par une combinaison de principaux authentifiés par Azure AD et de rôles RBAC d’Azure Resource Manager.  |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Toutes les opérations de gestion effectuées sur les objets App Service se font via [Azure Resource Manager](../azure-resource-manager/index.yml). Les journaux historiques de ces opérations sont disponibles à la fois dans le portail et via l’interface de ligne de commande. Pour en savoir plus, veuillez consulter [Opérations du fournisseur de ressources Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb) et [az monitor activity-log](/cli/azure/monitor/activity-log). |
| Journalisation et audit du plan de données | Non | Le plan de données pour App Service est un partage de fichiers distant contenant le contenu du site web déployé par un client.  Il n’y a pas d’audit du partage de fichiers distant. |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Pour les opérations de gestion, l’état d’une configuration d’App Service peut être exporté sous forme de modèle Azure Resource Manager et géré avec le temps. Pour les opérations d’exécution, les clients peuvent gérer plusieurs versions en direct d’une application à l’aide des emplacements de déploiement App Service. | 

