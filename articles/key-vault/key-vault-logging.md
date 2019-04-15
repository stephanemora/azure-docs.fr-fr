---
title: Journalisation d’Azure Key Vault | Microsoft Docs
description: Utilisez ce didacticiel pour vous aider à vous familiariser avec la journalisation du coffre de clés.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 25ebd72c512eb92c5d9a464a4b4d74f9e41ae389
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484106"
---
# <a name="azure-key-vault-logging"></a>Journalisation d’Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Après avoir créé un ou plusieurs coffres de clés, vous souhaiterez probablement surveiller comment et quand vos coffres de clés sont utilisées et par qui. Vous pouvez le faire en activant la journalisation pour Azure Key Vault, qui enregistre les informations dans un compte de stockage Azure que vous fournissez. Un conteneur nommé **insights-logs-auditevent** est automatiquement créé pour votre compte de stockage spécifié. Vous pouvez utiliser ce même compte de stockage pour recueillir les journaux de plusieurs coffres de clés.

Vous pouvez accéder à vos informations de journalisation 10 minutes (au plus) après l’opération de coffre de clés. Dans la plupart des cas, ce sera plus rapide.  C’est à vous de gérer vos journaux d’activité dans votre compte de stockage :

* Utilisez les méthodes de contrôle d’accès Azure standard pour assurer la sécurité de vos journaux d’activité en limitant l’accès à ces derniers.
* Supprimez les journaux d’activité que vous ne souhaitez plus conserver dans votre compte de stockage.

Utilisez ce didacticiel pour vous aider à vous familiariser avec la journalisation du coffre de clés. Vous allez créer un compte de stockage, activez la journalisation et interpréter les informations de journal collecté.  

> [!NOTE]
> Ce didacticiel n’inclut pas d’instructions pour créer des coffres de clés, des clés ou des clés secrètes. Pour en savoir plus, consultez [Présentation d'Azure Key Vault](key-vault-overview.md). Ou, pour obtenir des instructions de la CLI Azure multiplateforme, consultez [ce didacticiel équivalent](key-vault-manage-with-cli2.md).
>
> Cet article fournit des instructions Azure PowerShell pour mettre à jour de la journalisation des Diagnostics. Vous pouvez également modifier la journalisation des diagnostics à l’aide d’Azure Monitor dans le **journaux de Diagnostic** section du portail Azure. 
>

Pour plus d’informations sur Key Vault, consultez [qu’est Azure Key Vault ?](key-vault-whatis.md). Pour savoir où Key Vault est disponible, consultez le [page de tarification](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Conditions préalables

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un coffre de clés existant que vous utilisez déjà.  
* Azure PowerShell, version minimale de 1.0.0. Pour installer Azure PowerShell et l’associer à votre abonnement Azure, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview). Si vous avez déjà installé Azure PowerShell et que vous ne connaissez pas la version, à partir de la console Azure PowerShell, entrez `$PSVersionTable.PSVersion`.  
* Espace de stockage suffisant sur Azure pour vos journaux d’activité de coffre de clés.

## <a id="connect"></a>Connectez-vous à votre abonnement de coffre de clés

La première étape dans la configuration d’enregistrement de frappe est point Azure PowerShell, le coffre de clés que vous souhaitez journaliser.

Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure en utilisant la commande suivante :  

```powershell
Connect-AzAccount
```

Dans la fenêtre contextuelle de votre navigateur, entrez votre nom d’utilisateur et votre mot de passe Azure. Azure PowerShell obtient alors tous les abonnements associés à ce compte. Par défaut, PowerShell utilise la première.

Vous devrez peut-être spécifier l’abonnement que vous avez utilisé pour créer votre coffre de clés. Entrez la commande suivante pour afficher les abonnements de votre compte :

```powershell
Get-AzSubscription
```

Ensuite, pour spécifier l’abonnement associé au coffre de clés que vous allez vous connecter, entrez :

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Pointant vers PowerShell pour l’abonnement approprié d’est une étape importante, surtout si vous avez plusieurs abonnements associés à votre compte. Pour plus d’informations sur la configuration d’Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

## <a id="storage"></a>Créer un compte de stockage pour vos journaux

Bien que vous pouvez utiliser un compte de stockage existant pour vos journaux, nous allons créer un compte de stockage qui sera dédié aux journaux de coffre de clés. Pour plus de commodité pour le moment où vous devrez spécifier par la suite, nous allons enregistrer les détails dans une variable nommée **sa**.

Pour faciliter encore de gestion, nous utiliserons également le même groupe de ressources que celui qui contient le coffre de clés. À partir de la [didacticiel mise en route](key-vault-get-started.md), ce groupe de ressources est nommé **ContosoResourceGroup**, et nous allons continuer à utiliser l’emplacement Asie de l’est. Remplacez ces valeurs par les vôtres, selon le cas :

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Si vous décidez d’utiliser un compte de stockage existant, il doit utiliser le même abonnement que votre coffre de clés. Et il doit utiliser le modèle de déploiement Azure Resource Manager, plutôt que le modèle de déploiement classique.
>
>

## <a id="identify"></a>Identification du coffre de clés pour vos journaux

Dans le [didacticiel mise en route](key-vault-get-started.md), le nom de coffre de clés a été **ContosoKeyVault**. Nous allons continuer à utiliser ce nom et de stocker les détails dans une variable nommée **kv**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Activation de la journalisation

Pour activer la journalisation pour Key Vault, nous allons utiliser le **Set-AzDiagnosticSetting** applet de commande, ainsi que les variables que nous avons créé pour le nouveau compte de stockage et le coffre de clés. Nous allons également définir le **-activé** indicateur **$true** et la catégorie **AuditEvent** (la seule catégorie pour la journalisation de Key Vault) :

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Le résultat se présente ainsi :

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Cette sortie confirme que la journalisation est maintenant activée pour votre coffre de clés, et il enregistrera des informations à votre compte de stockage.

Si vous le souhaitez, vous pouvez définir une stratégie de rétention pour vos journaux telles que les anciens journaux sont automatiquement supprimés. Par exemple, définir la stratégie de rétention en définissant le **- RetentionEnabled** indicateur **$true**et définissez le **- RetentionInDays** paramètre **90**afin que les journaux de plus de 90 jours sont automatiquement supprimés.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Éléments consignés :

* Toutes les authentifiées requêtes d’API REST, y compris les demandes ayant échoués suite à des demandes incorrectes, des erreurs système ou des autorisations d’accès.
* Opérations sur la clé de coffre lui-même, y compris la création, suppression, stratégies d’accès de coffre de clés de paramètre et la mise à jour des attributs de coffre de clés tels que les balises.
* Opérations sur les clés et secrets dans le coffre de clés, y compris :
  * Création, modification ou la suppression de ces clés ou ces secrets.
  * Signature, vérification, chiffrement, déchiffrement, encapsulant et désencapsuler les clés, l’obtention des secrets et liste de clés et secrets (et leurs versions).
* les requêtes non authentifiées qui génèrent une réponse 401. Exemples sont des requêtes qui n’ont pas un jeton du porteur, qui sont mal formé ou a expiré ou qui ont un jeton non valide.  

## <a id="access"></a>Accéder à vos journaux

Journaux de coffre de clés sont stockés dans le **insights-logs-auditevent** conteneur du compte de stockage que vous avez fourni. Pour afficher les journaux, vous devez télécharger des objets BLOB.

Commencez par créer une variable pour le nom du conteneur. Vous utiliserez cette variable dans le reste de la procédure pas à pas.

```powershell
$container = 'insights-logs-auditevent'
```

Pour répertorier tous les objets BLOB dans ce conteneur, entrez :

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Le résultat ressemble à ce qui suit :

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

- - -
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Comme vous pouvez le voir dans cette sortie, les objets blob suivent une convention d’affectation de noms : `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Les valeurs de date et d’heure utilisent UTC.

Étant donné que vous pouvez utiliser le même compte de stockage pour collecter les journaux de plusieurs ressources, l’ID de ressource complet dans le nom de l’objet blob est utile pour accéder à ou télécharger uniquement les objets BLOB dont vous avez besoin. Mais avant cela, nous aborderons le téléchargement de tous les objets blob.

Créez un dossier pour télécharger les objets BLOB. Par exemple : 

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Procurez-vous la liste de tous les objets blob :  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Redirigez cette liste via **Get-AzStorageBlobContent** pour télécharger les objets BLOB dans le dossier de destination :

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Lorsque vous exécutez cette seconde commande, le délimiteur **/** présent dans les noms d’objet blob crée une structure de dossiers complète sous le dossier de destination. Vous allez utiliser cette structure pour télécharger et stocker les objets BLOB en tant que fichiers.

Pour télécharger les objets blob de façon sélective, utilisez des caractères génériques. Par exemple : 

* Si vous disposez de plusieurs coffres de clés et souhaitez télécharger les journaux d’activité d’un seul d’entre eux nommé CONTOSOKEYVAULT3 :

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Si vous disposez de plusieurs groupes de ressources et souhaitez télécharger les journaux d’activité d’un seul d’entre eux, utilisez `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Si vous souhaitez télécharger tous les journaux pour le mois de janvier 2019, utilisez `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Vous êtes maintenant prêt à commencer les recherches dans le contenu des journaux d’activité. Mais avant de passer à cela, vous devez connaître deux autres commandes :

* Pour interroger l’état des paramètres de diagnostic de votre ressource de coffre de clés : `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Pour désactiver la journalisation de votre ressource de coffre de clés : `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Interpréter vos journaux Key Vault

Les objets blob individuels sont stockés sous forme de texte en tant qu’objet blob JSON. Examinons un exemple d’entrée de journal. Exécutez cette commande :

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Elle retourne une entrée de journal semblable à celui-ci :

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

Le tableau suivant répertorie les noms de champs et les descriptions :

| Nom du champ | Description |
| --- | --- |
| **time** |Date et heure UTC. |
| **resourceId** |ID de ressource Azure Resource Manager. Pour les journaux d’activité de coffre de clés, il s’agit toujours de l’ID de ressource du coffre de clés. |
| **operationName** |Nom de l’opération, comme indiqué dans le tableau suivant. |
| **operationVersion** |Version de l’API REST demandée par le client. |
| **category** |Type de résultat. Pour les journaux de Key Vault, **AuditEvent** est la seule valeur disponible. |
| **resultType** |Résultat de la demande de l’API REST. |
| **resultSignature** |État HTTP |
| **resultDescription** |Description supplémentaire du résultat le cas échéant. |
| **durationMs** |Délai nécessaire pour répondre à la demande API REST, en millisecondes. La latence du réseau n’est pas incluse dans ce chiffre et donc, le temps mesuré côté client peut ne pas correspondre à cette durée. |
| **callerIpAddress** |Adresse IP du client qui a effectué la demande. |
| **correlationId** |GUID facultatif que le client peut transférer pour mettre en corrélation les journaux d’activité côté client avec les journaux d’activité côté service (Key Vault). |
| **identity** |Identité du jeton qui a été présenté dans la demande de l’API REST. Il s’agit généralement d’un « utilisateur », « principal de service », ou la combinaison « utilisateur + appId, » comme dans le cas d’une demande qui résulte d’une applet de commande Azure PowerShell. |
| **properties** |Informations varient en fonction de l’opération (**NomOpération**). Dans la plupart des cas, ce champ contient des informations sur le client (chaîne d’agent utilisateur transmise par le client), l’URI de la demande d’API REST exacte et le code d’état HTTP. En outre, quand un objet est retourné suite à une demande (par exemple, **KeyCreate** ou **VaultGet**), elle contient également la clé URI ou URI du secret du coffre URI (sous la forme « id »). |

Le **NomOpération** sont des valeurs de champ dans *ObjectVerb* format. Par exemple : 

* Toutes les opérations de coffre de clés ont la `Vault<action>` mettre en forme, telles que `VaultGet` et `VaultCreate`.
* Toutes les opérations de clé sont les `Key<action>` mettre en forme, telles que `KeySign` et `KeyList`.
* Toutes les opérations secrètes ont la `Secret<action>` mettre en forme, telles que `SecretGet` et `SecretListVersions`.

Le tableau suivant répertorie les **NomOpération** valeurs et les commandes correspondantes de l’API REST :

| operationName | Commande de l’API REST |
| --- | --- |
| **Authentification** |S’authentifier via un point de terminaison Azure Active Directory |
| **VaultGet** |[Obtention des informations sur un coffre de clés](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Création ou mise à jour d’un coffre de clés](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Suppression d’un coffre de clés](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Mise à jour d’un coffre de clés](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Liste de l’ensemble des coffres de clés dans un groupe de ressources](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Création d’une clé](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Obtention des informations sur une clé](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importation d’une clé dans un coffre](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Sauvegarder une clé](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
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
| **SecretGet** |[Obtenir une clé secrète](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Mise à jour d’une clé secrète](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Suppression d’une clé secrète](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Liste des clés secrètes d’un coffre](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Liste des versions d’une clé secrète](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Utiliser les journaux d’Azure Monitor

Vous pouvez utiliser la solution Key Vault dans les journaux Azure Monitor pour passer en revue le coffre de clés **AuditEvent** journaux. Dans les journaux Azure Monitor, vous utilisez des requêtes de journal pour analyser les données et obtenir les informations que vous avez besoin. 

Pour plus d’informations, y compris comment le configurer, consultez [solution Azure Key Vault dans Azure Monitor journaux](../azure-monitor/insights/azure-key-vault.md). Cet article contient également des instructions si vous devez migrer à partir de l’ancienne solution Key Vault qui proposée dans les journaux Azure Monitor, afficher un aperçu, où vous tout d’abord acheminé vos journaux vers un compte de stockage Azure et les journaux d’analyse Azure configuré pour lire à partir de là.

## <a id="next"></a>Étapes suivantes

Pour obtenir un didacticiel qui utilise Azure Key Vault dans une application web de .NET, consultez [utiliser Azure Key Vault à partir d’une application web](tutorial-net-create-vault-azure-web-app.md).

Pour les références de programmation, consultez le [guide du développeur de coffre de clés Azure](key-vault-developers-guide.md).

Pour obtenir la liste des applets de commande Azure PowerShell 1.0 pour Azure Key Vault, consultez [applets de commande Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

Pour obtenir un didacticiel sur la rotation des clés et les journaux d’audit avec Azure Key Vault, consultez [configurer Key Vault end-to-end rotation des clés et l’audit](key-vault-key-rotation-log-monitoring.md).
