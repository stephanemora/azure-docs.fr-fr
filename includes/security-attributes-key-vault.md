---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: aba09012bf2e9d2741f598280add8b599a6f6d1a
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55807197"
---
## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui | Tous les objets sont chiffrés. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Chiffrement dans le réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | Toutes les communications se font par le biais d’appels d’API chiffrés |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Oui | Le client contrôle toutes les clés de son Key Vault. Lorsque des clés sauvegardées avec HSM (Hardware Security Module) sont spécifiées, un module HSM Niveau 2 FIPS protège la clé, le certificat ou le secret. |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A |  |
| Appels d’API chiffrés| Oui | Utilisation du protocole HTTPS. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui | Utilisation de points de terminaison de service de réseau virtuel. |
| Prise en charge de l’injection de réseau virtuel| Non  |  |
| Prise en charge de l’isolement réseau/l’installation de pare-feu| Oui | Utilisation de règles de pare-feu de réseau virtuel. |
| Prise en charge du tunneling forcé | Non  |  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la Surveillance Azure (Log Analytics, App Insights, etc.)| Oui | Utilisation de Log Analytics. |

## <a name="iam-support"></a>Prise en charge de la gestion des identités et des accès (IAM)

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Gestion de l’accès - Authentification| Oui | L’authentification s’effectue par le biais d’Azure Active Directory. |
| Gestion de l’accès - Autorisation| Oui | Utilisation d’une stratégie d’accès à Key Vault. |


## <a name="audit-trail"></a>Piste d’audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion/contrôle| Oui | Utilisation de Log Analytics. |
| Journalisation et audit du plan de données| Oui | Utilisation de Log Analytics. |

## <a name="access-controls"></a>Contrôles d’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Contrôles d’accès au plan de gestion/contrôle | Oui | Contrôle d’accès en fonction du rôle (RBAC) Azure Resource Manager |
| Contrôles d’accès au plan de données (à chaque niveau de service) | Oui | Stratégie d’accès à Key Vault |
