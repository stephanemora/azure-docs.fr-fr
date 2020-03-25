---
title: Journalisation d’Azure Key Vault | Microsoft Docs
description: Utilisez ce didacticiel pour vous aider à vous familiariser avec la journalisation du coffre de clés.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 8915970cd4c70228fad3b49921f4c81d6d90aa72
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78195326"
---
# <a name="azure-key-vault-logging"></a>Journalisation d’Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Une fois que vous avez créé un ou plusieurs coffres de clés, vous voulez probablement contrôler qui accède à ces derniers, par quel moyen et quand. Pour ce faire, vous pouvez activer la journalisation d’Azure Key Vault, ce qui enregistre les informations dans un compte de stockage Azure que vous fournissez. Un conteneur nommé **insights-logs-auditevent** est automatiquement créé pour le compte de stockage spécifié. Vous pouvez utiliser ce même compte de stockage pour collecter les journaux de plusieurs coffres de clés.

Vous pouvez accéder aux informations de journalisation 10 minutes (au maximum) après l’opération sur le coffre de clés. Dans la plupart des cas, ce sera plus rapide.  C’est à vous de gérer vos journaux d’activité dans votre compte de stockage :

* Utilisez les méthodes de contrôle d’accès Azure standard pour assurer la sécurité de vos journaux d’activité en limitant l’accès à ces derniers.
* Supprimez les journaux d’activité que vous ne souhaitez plus conserver dans votre compte de stockage.

Utilisez ce didacticiel pour vous aider à vous familiariser avec la journalisation du coffre de clés. Vous allez créer un compte de stockage, activer la journalisation et interpréter les informations de journal collectées.  

> [!NOTE]
> Ce didacticiel n’inclut pas d’instructions pour créer des coffres de clés, des clés ou des clés secrètes. Pour en savoir plus, consultez [Présentation d'Azure Key Vault](key-vault-overview.md). Ou, si vous souhaitez obtenir des instructions applicables à l’interface de ligne de commande (Azure CLI) multiplateforme, consultez [ce didacticiel équivalent](key-vault-manage-with-cli2.md).
>
> Cet article fournit des instructions Azure PowerShell pour la mise à jour de la journalisation des diagnostics. Vous pouvez également mettre à jour la journalisation des diagnostics à l’aide d’Azure Monitor dans la section **Journaux de diagnostic** du Portail Azure. 
>

Pour obtenir des informations générales sur Key Vault, consultez l’article [Qu’est-ce qu’Azure Key Vault ?](key-vault-overview.md). Pour plus d’informations sur les endroits où Key Vault est disponible, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce tutoriel, vous devez disposer des éléments suivants :

* Un coffre de clés existant que vous utilisez déjà.  
* Azure PowerShell, version 1.0.0 ou ultérieure. Pour installer Azure PowerShell et l’associer à votre abonnement Azure, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview). Si vous avez déjà installé Azure PowerShell et que vous ne connaissez pas la version que vous utilisez, à partir de la console Azure PowerShell, entrez `$PSVersionTable.PSVersion`.  
* Espace de stockage suffisant sur Azure pour vos journaux d’activité de coffre de clés.

## <a name="connect-to-your-key-vault-subscription"></a><a id="connect"></a>Se connecter à l’abonnement du coffre de clés

La première étape de la procédure de configuration de la journalisation d’un coffre de clés consiste à faire pointer Azure PowerShell vers le coffre de clés à journaliser.

Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure en utilisant la commande suivante :  

```powershell
Connect-AzAccount
```

Dans la fenêtre contextuelle de votre navigateur, entrez votre nom d’utilisateur et votre mot de passe Azure. Azure PowerShell obtient alors tous les abonnements associés à ce compte. Par défaut, PowerShell utilise le premier abonnement.

Vous devrez peut-être spécifier l’abonnement que vous avez utilisé pour créer votre coffre de clés. Entrez la commande ci-après pour afficher les abonnements de votre compte :

```powershell
Get-AzSubscription
```

Ensuite, pour spécifier l’abonnement associé au coffre de clés que vous allez journaliser, entrez la commande suivante :

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

L’opération qui consiste à faire pointer PowerShell vers l’abonnement adéquat constitue une étape importante, en particulier si plusieurs abonnements sont associés à votre compte. Pour plus d’informations sur la configuration d’Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

## <a name="create-a-storage-account-for-your-logs"></a><a id="storage"></a>Créer un compte de stockage pour vos journaux

Bien que vous puissiez utiliser un compte de stockage existant pour vos journaux, nous allons en créer un qui sera dédié aux journaux Key Vault. Pour plus de commodité, en prévision du moment où vous devrez le spécifier par la suite, nous allons stocker les détails correspondants dans une variable nommée **sa**.

Pour simplifier davantage la gestion, nous allons utiliser le groupe de ressources qui contient le coffre de clés. Dans le [tutoriel de prise en main](key-vault-get-started.md), ce groupe de ressources se nomme **ContosoResourceGroup**, et nous allons continuer d’utiliser l’emplacement Asie Est. Remplacez ces valeurs par les vôtres, comme il convient :

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Si vous décidez d’utiliser un compte de stockage existant, ce dernier doit utiliser le même abonnement que votre coffre de clés, ainsi que le modèle de déploiement Azure Resource Manager plutôt que le modèle de déploiement Azure Classic.
>
>

## <a name="identify-the-key-vault-for-your-logs"></a><a id="identify"></a>Identification du coffre de clés pour vos journaux d’activité

Dans le [tutoriel de prise en main](key-vault-get-started.md), le coffre de clés porte le nom **ContosoKeyVault**. Nous allons continuer à utiliser ce nom et stocker les détails correspondants dans une variable nommée **kv** :

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a name="enable-logging"></a><a id="enable"></a>Activation de la journalisation

Pour activer la journalisation de Key Vault, nous allons utiliser la cmdlet **Set-AzDiagnosticSetting**, ainsi que les variables que nous avons créées pour le nouveau compte de stockage et le coffre de clés. Nous allons également définir l’indicateur **-Enabled** sur **$true** et la catégorie sur **AuditEvent** (la seule catégorie pour la journalisation de Key Vault) :

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

Cette sortie confirme que la journalisation est à présent activée pour votre coffre de clés, et qu’elle enregistrera les informations dans votre compte de stockage.

Si vous le souhaitez, vous pouvez définir une stratégie de rétention pour vos journaux, par exemple la suppression automatique des anciens journaux. Par exemple, définissez une stratégie de rétention en paramétrant l’indicateur **-RetentionEnabled** sur la valeur **$true** et le paramètre **-RetentionInDays** sur la valeur **90** afin que les journaux antérieurs à 90 jours soient automatiquement supprimés.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Éléments consignés :

* Toutes les requêtes d’API REST authentifiées, ce qui inclut les requêtes ayant échoué suite à des autorisations d’accès, des erreurs système ou des requêtes incorrectes.
* Les opérations sur le coffre de clés proprement dit, notamment la création, la suppression, la définition des stratégies d’accès au coffre de clés et la mise à jour des attributs de coffre de clés (par exemple, les balises).
* Les opérations sur les clés et secrets dans le coffre de clés, à savoir :
  * Création, modification ou suppression de ces clés ou secrets.
  * Signature, vérification, chiffrement, déchiffrement, inclusion dans un wrapper et retrait d’un wrapper de clés, obtention des secrets, et liste de clés et secrets (et leurs versions).
* les requêtes non authentifiées qui génèrent une réponse 401. Il s’agit notamment des requêtes dépourvues de jeton du porteur, dont le format est incorrect, qui ont expiré ou qui comportent un jeton non valide.  

## <a name="access-your-logs"></a><a id="access"></a>Accéder à vos journaux d’activité

Les journaux Key Vault sont stockés dans le conteneur **insights-logs-auditevent** du compte de stockage que vous avez fourni. Pour visualiser les journaux, vous devez télécharger des objets blob.

Commencez par créer une variable pour le nom du conteneur. Vous utiliserez cette variable dans le reste de cette procédure pas à pas.

```powershell
$container = 'insights-logs-auditevent'
```

Pour répertorier tous les objets blob présents dans ce conteneur, entrez la commande suivante :

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Le résultat ressemble à ce qui suit :

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Comme vous pouvez le voir dans cette sortie, les objets blob suivent une convention d’affectation de noms : `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Les valeurs de date et d’heure utilisent UTC.

Étant donné que vous pouvez utiliser le même compte de stockage pour collecter les journaux de plusieurs ressources, l’ID de ressource complet dans le nom de l’objet blob est utile si vous voulez uniquement accéder aux objets blob dont vous avez besoin ou télécharger ces derniers. Mais avant cela, nous aborderons le téléchargement de tous les objets blob.

Créez un dossier pour y télécharger les objets blob. Par exemple :

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Procurez-vous la liste de tous les objets blob :  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Redirigez cette liste par le biais de **Get-AzStorageBlobContent** pour télécharger les objets blob dans le dossier de destination :

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Lorsque vous exécutez cette seconde commande, le délimiteur **/** présent dans les noms d’objet blob crée une structure de dossiers complète sous le dossier de destination. Vous allez utiliser cette structure pour télécharger et stocker les objets blob sous forme de fichiers.

Pour télécharger les objets blob de façon sélective, utilisez des caractères génériques. Par exemple :

* Si vous disposez de plusieurs coffres de clés et souhaitez télécharger les journaux d’activité d’un seul d’entre eux nommé CONTOSOKEYVAULT3 :

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Si vous disposez de plusieurs groupes de ressources et souhaitez télécharger les journaux d’activité d’un seul d’entre eux, utilisez `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Si vous souhaitez télécharger tous les journaux du mois de janvier 2019, utilisez `-Blob '*/year=2019/m=01/*'` :

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Vous êtes maintenant prêt à commencer les recherches dans le contenu des journaux d’activité. Mais avant de procéder à cette opération, vous devez connaître deux autres commandes :

* Pour interroger l’état des paramètres de diagnostic de votre ressource de coffre de clés : `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Pour désactiver la journalisation de votre ressource de coffre de clés : `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a name="interpret-your-key-vault-logs"></a><a id="interpret"></a>Interpréter vos journaux d’activité Key Vault

Les objets blob individuels sont stockés sous forme de texte en tant qu’objet blob JSON. Examinons un exemple d’entrée du journal. Exécutez cette commande :

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Elle renvoie une entrée du journal semblable à celle-ci :

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
| **category** |Type de résultat. Pour les journaux Key Vault, **AuditEvent** est la seule valeur disponible. |
| **resultType** |Résultat de la requête d’API REST. |
| **resultSignature** |État HTTP |
| **resultDescription** |Description supplémentaire du résultat le cas échéant. |
| **durationMs** |Délai nécessaire pour répondre à la demande API REST, en millisecondes. La latence du réseau n’est pas incluse dans ce chiffre et donc, le temps mesuré côté client peut ne pas correspondre à cette durée. |
| **callerIpAddress** |Adresse IP du client qui a effectué la requête. |
| **correlationId** |GUID facultatif que le client peut transférer pour mettre en corrélation les journaux d’activité côté client avec les journaux d’activité côté service (Key Vault). |
| **identity** |Identité issue du jeton qui a été présenté dans la requête d’API REST. Il s’agit généralement d’un « utilisateur », d’un « principal de service » ou de la « utilisateur + appId », comme dans le cas d’une requête résultant d’une cmdlet Azure PowerShell. |
| **properties** |Informations variables en fonction de l’opération (**operationName**). Dans la plupart des cas, ce champ contient des informations sur le client (chaîne d’agent utilisateur transmise par le client), l’URI de requête d’API REST exacte et le code d’état HTTP. En outre, lorsqu’un objet est renvoyé suite à une requête (par exemple, **KeyCreate** ou **VaultGet**), ce champ contient également l’URI de clé (sous la forme « id »), l’URI de coffre ou l’URI de secret. |

Les valeurs du champ **operationName** sont indiquées au format *ObjectVerb*. Par exemple :

* Toutes les opérations de coffre de clés présentent le format `Vault<action>`, comme `VaultGet` et `VaultCreate`.
* Toutes les opérations de clé présentent le format `Key<action>`, comme `KeySign` et `KeyList`.
* Toutes les opérations de secret présentent le format `Secret<action>`, comme `SecretGet` et `SecretListVersions`.

Le tableau ci-après répertorie les valeurs **operationName** et les commandes API REST correspondantes :

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

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Utiliser les journaux Azure Monitor

Vous pouvez utiliser la solution Key Vault dans les journaux Azure Monitor pour consulter les journaux **AuditEvent** Key Vault. Dans les journaux Azure Monitor, vous utilisez des requêtes de journaux pour analyser les données et obtenir les informations dont vous avez besoin. 

Pour plus d’informations, notamment sur la procédure de configuration correspondante, consultez l’article [Azure Key Vault solution in Azure Monitor logs](../azure-monitor/insights/azure-key-vault.md) (Solution Azure Key Vault dans les journaux Azure Monitor). Cet article contient également les instructions adéquates si vous devez effectuer une migration à partir de l’ancienne solution Key Vault qui était disponible dans la préversion des journaux Azure Monitor. Lorsque vous utilisiez cette dernière, vous commenciez par acheminer vos journaux vers un compte de stockage Azure avant de configurer les journaux Azure Monitor pour la lecture des données à cet emplacement.

## <a name="next-steps"></a><a id="next"></a>Étapes suivantes

Pour accéder à un tutoriel utilisant Azure Key Vault dans une application web .NET, consultez l’article [Utilisation d’Azure Key Vault à partir d’une application web](tutorial-net-create-vault-azure-web-app.md).

Pour les références de programmation, consultez le [guide du développeur de coffre de clés Azure](key-vault-developers-guide.md).

Pour obtenir la liste des cmdlets Azure PowerShell 1.0 concernant Azure Key Vault, consultez l’article [Azure Key Vault Cmdlets](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault) (Cmdlets Azure Key Vault).

Pour accéder à un tutoriel sur la rotation des clés et les journaux d’audit avec Azure Key Vault, consultez l’article [Configurer Key Vault avec une rotation des clés et un audit de bout en bout](key-vault-key-rotation-log-monitoring.md).
