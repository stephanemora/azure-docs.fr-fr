---
title: Journalisation d’Azure Key Vault | Microsoft Docs
description: Découvrez comment superviser l’accès à vos coffres de clés en activant la journalisation pour Azure Key Vault, ce qui enregistre les informations dans un compte de stockage Azure que vous fournissez.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: a51e9a628f67269357d42bd1d3af10c1d86f301a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91739780"
---
# <a name="azure-key-vault-logging"></a>Journalisation d’Azure Key Vault

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Une fois que vous avez créé un ou plusieurs coffres de clés, vous voulez probablement contrôler qui accède à ces derniers, par quel moyen et quand. Pour ce faire, vous pouvez activer la journalisation d’Azure Key Vault, ce qui enregistre les informations dans un compte de stockage Azure que vous fournissez. Un conteneur nommé **insights-logs-auditevent** est automatiquement créé pour le compte de stockage spécifié. Vous pouvez utiliser ce même compte de stockage pour collecter les journaux de plusieurs coffres de clés.

Vous pouvez accéder aux informations de journalisation 10 minutes (au maximum) après l’opération sur le coffre de clés. Dans la plupart des cas, ce sera plus rapide.  C’est à vous de gérer vos journaux d’activité dans votre compte de stockage :

* Utilisez les méthodes de contrôle d’accès Azure standard pour assurer la sécurité de vos journaux d’activité en limitant l’accès à ces derniers.
* Supprimez les journaux d’activité que vous ne souhaitez plus conserver dans votre compte de stockage.

Pour obtenir des informations générales sur Key Vault, consultez [Qu’est-ce qu’Azure Key Vault ?](overview.md) Pour plus d’informations sur les endroits où Key Vault est disponible, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/key-vault/). Découvrez plus d’informations sur l’utilisation d’[Azure Monitor pour Key Vault](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview).

## <a name="interpret-your-key-vault-logs"></a>Interpréter vos journaux d’activité Key Vault

Les objets blob individuels sont stockés sous forme de texte en tant qu’objet blob JSON. Examinons un exemple d’entrée du journal. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

Le tableau ci-après répertorie les noms de champ et leurs descriptions :

| Nom du champ | Description |
| --- | --- |
| **time** |Date et heure en temps universel coordonné (UTC). |
| **resourceId** |ID de ressource Azure Resource Manager. Pour les journaux d’activité de coffre de clés, il s’agit toujours de l’ID de ressource du coffre de clés. |
| **operationName** |Nom de l’opération, comme indiqué dans le tableau suivant. |
| **operationVersion** |Version d’API REST demandée par le client. |
| **category** |Type de résultat. Pour les journaux Key Vault, `AuditEvent` est la seule valeur disponible. |
| **resultType** |Résultat de la requête d’API REST. |
| **resultSignature** |État HTTP |
| **resultDescription** |Description supplémentaire du résultat le cas échéant. |
| **durationMs** |Délai nécessaire pour répondre à la demande API REST, en millisecondes. La latence du réseau n’est pas incluse dans ce chiffre et donc, le temps mesuré côté client peut ne pas correspondre à cette durée. |
| **callerIpAddress** |Adresse IP du client qui a effectué la requête. |
| **correlationId** |GUID facultatif que le client peut transférer pour mettre en corrélation les journaux d’activité côté client avec les journaux d’activité côté service (Key Vault). |
| **identity** |Identité issue du jeton qui a été présenté dans la requête d’API REST. Il s’agit généralement d’un « utilisateur », d’un « principal de service » ou de la « utilisateur + appId », comme dans le cas d’une requête résultant d’une cmdlet Azure PowerShell. |
| **properties** |Informations variables en fonction de l’opération (**operationName**). Dans la plupart des cas, ce champ contient des informations sur le client (chaîne d’agent utilisateur transmise par le client), l’URI de requête d’API REST exacte et le code d’état HTTP. En outre, quand un objet est retourné suite à une requête (par exemple, **KeyCreate** ou **VaultGet**), ce champ contient également l’URI de la clé (sous la forme `id`), l’URI du coffre ou l’URI du secret. |

Les valeurs du champ **operationName** sont indiquées au format *ObjectVerb*. Par exemple :

* Toutes les opérations de coffre de clés présentent le format `Vault<action>`, comme `VaultGet` et `VaultCreate`.
* Toutes les opérations de clé présentent le format `Key<action>`, comme `KeySign` et `KeyList`.
* Toutes les opérations de secret présentent le format `Secret<action>`, comme `SecretGet` et `SecretListVersions`.

Le tableau ci-après répertorie les valeurs **operationName** et les commandes API REST correspondantes :

### <a name="operation-names-table"></a>Table des noms d’opération

| operationName | Commande API REST |
| --- | --- |
| **Authentification** |Authentification par le biais du point de terminaison Azure Active Directory |
| **VaultGet** |[Obtention des informations sur un coffre de clés](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Création ou mise à jour d’un coffre de clés](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Suppression d’un coffre de clés](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Mise à jour d’un coffre de clés](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Liste de l’ensemble des coffres de clés dans un groupe de ressources](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Création d’une clé](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Obtention des informations sur une clé](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importation d’une clé dans un coffre](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Sauvegarde d’une clé](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Suppression d’une clé](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Restauration d’une clé](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Signature avec une clé](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Vérification à l’aide d’une clé](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Encapsulage d’une clé](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Désencapsulage d’une clé](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Chiffrement avec une clé](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Déchiffrement avec une clé](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Mise à jour d’une clé](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Liste des clés dans un coffre](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Liste des versions d’une clé](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Création d’une clé secrète](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Obtention d’un secret](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Mise à jour d’une clé secrète](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Suppression d’une clé secrète](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Liste des clés secrètes d’un coffre](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Liste des versions d’une clé secrète](https://msdn.microsoft.com/library/azure/dn986824.aspx) |
| **VaultAccessPolicyChangedEventGridNotification** | Événement lié à la modification de la stratégie d’accès au coffre publié |
| **SecretNearExpiryEventGridNotification** |Événement lié à un secret proche de l’expiration publié |
| **SecretExpiredEventGridNotification** |Événement lié à un secret expiré publié |
| **KeyNearExpiryEventGridNotification** |Événement lié à une clé proche de l’expiration publié |
| **KeyExpiredEventGridNotification** |Événement lié à une clé expirée publié |
| **CertificateNearExpiryEventGridNotification** |Événement lié à un certificat proche de l’expiration publié |
| **CertificateExpiredEventGridNotification** |Événement lié à un certificat expiré publié |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Utiliser les journaux Azure Monitor

Vous pouvez utiliser la solution Key Vault dans les journaux Azure Monitor pour consulter les journaux `AuditEvent` de Key Vault. Dans les journaux Azure Monitor, vous utilisez des requêtes de journaux pour analyser les données et obtenir les informations dont vous avez besoin. 

Pour plus d’informations, notamment sur la procédure de configuration correspondante, consultez [Azure Key Vault dans Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a><a id="next"></a>Étapes suivantes

Pour accéder à un tutoriel utilisant Azure Key Vault dans une application web .NET, consultez l’article [Utilisation d’Azure Key Vault à partir d’une application web](tutorial-net-create-vault-azure-web-app.md).

Pour les références de programmation, consultez le [guide du développeur de coffre de clés Azure](developers-guide.md).

Pour obtenir la liste des cmdlets Azure PowerShell 1.0 concernant Azure Key Vault, consultez l’article [Azure Key Vault Cmdlets](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault) (Cmdlets Azure Key Vault).
