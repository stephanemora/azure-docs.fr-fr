---
author: msmbaldwin
ms.service: service-fabric
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: a20311a0285bf8fda5498241a60b85093039ad19
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513254"
---
## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui | Le cluster et le groupe de machines virtuelles identiques sur lequel le cluster est basé appartiennent au client. Le chiffrement de disque Azure peut être activé sur le groupe de machines virtuelles identiques. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Chiffrement dans le réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui |  |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Oui | Le cluster et le groupe de machines virtuelles identiques sur lequel le cluster est basé appartiennent au client. Le chiffrement de disque Azure peut être activé sur le groupe de machines virtuelles identiques. |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A |  |
| Appels d’API chiffrés| Oui | Les appels d’API Service Fabric sont effectués via Azure Resource Manager. Un jeton web JSON valide (JWT) est nécessaire. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui |  |
| Prise en charge de l’injection de réseau virtuel| Oui |  |
| Prise en charge de l’isolement réseau/l’installation de pare-feu| Oui | Utilisation de groupes de sécurité réseau (NSG). |
| Prise en charge du tunneling forcé | Oui | La gestion réseau Azure fournit le tunneling forcé. |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la Surveillance Azure (Log Analytics, App Insights, etc.)| Oui | Prise en charge de l’utilisation de la surveillance Azure et prise en charge des tiers. |

## <a name="iam-support"></a>Prise en charge IAM

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Gestion de l’accès - Authentification| Oui | L’authentification s’effectue par le biais d’Azure Active Directory. |
| Gestion de l’accès - Autorisation| Oui | Gestion des identités et des accès (IAM) pour les appels via SFRP. Les appels effectués directement au point de terminaison du cluster prennent en charge deux rôles : utilisateur et administrateur. Le client peut mapper les API sur n’importe lequel des deux rôles. |


## <a name="audit-trail"></a>Piste d’audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion/contrôle| Oui | Toutes les opérations de plan de contrôle sont exécutées par le biais de processus pour l’audit et les approbations. |
| Journalisation et audit du plan de données| N/A | Le cluster appartient au client.  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| Oui | La configuration du service est déployée à l’aide du déploiement Azure, qui gère également sa version. La version du code (application et runtime) est gérée à l’aide de la build Azure.
 |