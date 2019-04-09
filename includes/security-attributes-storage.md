---
author: msmbaldwin
ms.service: storage
ms.topic: include
ms.date: 03/15/2019
ms.author: mbaldwin
ms.openlocfilehash: b242bda524c747b28453061c797afde02cf6f455
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007561"
---
## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui |  |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Chiffrement dans le réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | Prend en charge les mécanismes HTTPS/TLS standard.  Les utilisateurs peuvent également chiffrer les données avant leur transmission au service. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Oui | Consultez [Storage Service Encryption à l’aide de clés gérées par le client dans Azure Key Vault](../articles/storage/common/storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Chiffrement au niveau des colonnes (Azure Data Services)| S.O. |  |
| Appels d’API chiffrés| Oui |  |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui |  |
| Prise en charge de l’injection de réseau virtuel| S.O. |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Oui | |
| Prise en charge du tunneling forcé | S.O. |  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | Mesures Azure Monitor disponible consigne désormais, version préliminaire de départ |

## <a name="iam-support"></a>Prise en charge d’IAM

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Gestion des accès – Authentification| Oui | Azure Active Directory, clé partagée, le jeton d’accès partagé. |
| Gestion des accès – Autorisation| Oui | Autorisation de prise en charge par le biais de RBAC, les ACL POSIX et les jetons SAS |


## <a name="audit-trail"></a>Piste d’audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion/contrôle | Oui | Journal d’activité Azure Resource Manager |
| Journalisation et audit du plan de données| Oui | Journaux de Diagnostic de service et l’aperçu de départ journalisation d’Azure Monitor  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui | Prend en charge la gestion des versions du fournisseur de ressources via les API Azure Resource Manager |