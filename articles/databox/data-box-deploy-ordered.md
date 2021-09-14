---
title: Tutoriel - Commander Azure Data Box | Microsoft Docs
description: Ce tutoriel présente Azure Data Box, une solution hybride qui vous permet d’importer des données locales dans Azure, et explique comment commander Azure Data Box.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 489182db34366a910a26ad358d9ef60ae4244530
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123468561"
---
# <a name="tutorial-order-azure-data-box"></a>Tutoriel : Commander Azure Data Box

Azure Data Box est une solution hybride qui vous permet d’importer vos données locales dans Azure de manière rapide, simple et fiable. Vous transférez vos données vers un dispositif de stockage de 80 To (capacité utilisable) fourni par Microsoft, puis réexpédiez le dispositif. Ces données sont ensuite chargées dans Azure.

Ce tutoriel explique comment commander une Azure Data Box. Ce tutoriel vous fournira des informations sur :  

> [!div class="checklist"]
>
> * Conditions préalables au déploiement de la Data Box
> * Commander une Data Box
> * Suivre la commande
> * Annuler la commande

> [!NOTE]
> Pour obtenir des réponses aux questions fréquemment posées sur les commandes et les livraisons de Data Box, consultez [Questions fréquentes (FAQ) sur Data Box](data-box-faq.yml).

## <a name="prerequisites"></a>Prérequis

# <a name="portal"></a>[Portail](#tab/portal)

Respectez les prérequis de configuration suivants pour l’appareil et le service Data Box avant de déployer l’appareil :

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

Vous pouvez vous connecter à Azure et exécuter des commandes Azure CLI de l’une des deux façons suivantes :

* Vous pouvez installer l’interface CLI et exécuter des commandes CLI localement.
* Vous pouvez exécuter des commandes CLI à partir du Portail Azure, dans Azure Cloud Shell.

Nous utilisons Azure CLI par le biais de Windows PowerShell pour le tutoriel, mais vous êtes libre de choisir l’une ou l’autre option.

### <a name="for-azure-cli"></a>Pour Azure CLI

Avant de commencer, assurez-vous que :

#### <a name="install-the-cli-locally"></a>Installer la CLI localement

* Installez [Azure CLI](/cli/azure/install-azure-cli) version 2.0.67 ou ultérieure. Vous pouvez également effectuer l’[installation à l’aide de MSI](https://aka.ms/installazurecliwindows).

**Se connecter à Azure**

Ouvrez une fenêtre de commande Windows PowerShell, puis connectez-vous à Azure avec la commande [az login](/cli/azure/reference-index#az_login) :

```azurecli
PS C:\Windows> az login
```

Voici la sortie d’une connexion réussie :

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

**Installer l’extension CLI Azure Data Box**

Avant de pouvoir utiliser les commandes CLI Azure Data Box, vous devez installer l’extension. Les extensions Azure CLI vous donnent accès à des commandes expérimentales et en préversion qui ne sont pas encore offertes par l’interface CLI principale. Pour plus d’informations sur les extensions, consultez [Utiliser des extensions avec Azure CLI](/cli/azure/azure-cli-extensions-overview).

Pour installer l’extension pour Azure Data Box, exécutez la commande suivante : `az extension add --name databox` :

```azurecli

    PS C:\Windows> az extension add --name databox
```

Si l’extension est correctement installée, la sortie suivante s’affiche :

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

#### <a name="use-azure-cloud-shell"></a>Utiliser Azure Cloud Shell

Vous pouvez utiliser [Azure Cloud Shell](https://shell.azure.com/), un environnement d’interpréteur de commandes interactif hébergé Azure, par le biais de votre navigateur pour exécuter des commandes CLI. Azure Cloud Shell prend en charge Bash ou Windows PowerShell avec les services Azure. L’interface Azure CLI est préinstallée et configurée pour être utilisée avec votre compte. Sélectionnez le bouton Cloud Shell du menu situé dans la section supérieure droite du portail Azure :

![Sélection du menu Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

Ce bouton lance un interpréteur de commandes interactif que vous pouvez utiliser pour exécuter les étapes décrites dans cet article de guide pratique.

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>Pour Azure PowerShell

Avant de commencer, vous devez :

* Installer Windows PowerShell version 6.2.4 ou supérieure
* Installer le module Azure PowerShell (AZ)
* Installer le module Azure Data Box (Az.DataBox)
* Connectez-vous à Azure.

#### <a name="install-azure-powershell-and-modules-locally"></a>Installer Azure PowerShell et les modules localement

**Installer ou mettre à niveau Windows PowerShell**

Windows PowerShell version 6.2.4 ou supérieure doit être installé. Pour connaître la version de PowerShell installée, exécutez : `$PSVersionTable`.

La sortie suivante s’affiche :

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

Si votre version de Windows PowerShell est inférieure à la version 6.2.4, vous devez la mettre à niveau. Pour installer la dernière version de Windows PowerShell, consultez [Installation d’Azure PowerShell](/powershell/scripting/install/installing-powershell).

**Installer Azure PowerShell et les modules Data Box**

Vous devez installer les modules Azure PowerShell afin d’utiliser Azure PowerShell pour commander un service Azure Data Box. Pour installer les modules Azure PowerShell :

1. Installez le [module Azure PowerShell Az](/powershell/azure/new-azureps-module-az).
2. Installez ensuite Az.DataBox à l’aide de la commande `Install-Module -Name Az.DataBox`.

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Connexion à Azure

Ouvrez une fenêtre de commande Windows PowerShell, puis connectez-vous à Azure avec la commande [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) :

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

Voici la sortie d’une connexion réussie :

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Pour plus d’informations sur la connexion à Azure à l’aide de Windows PowerShell, consultez [Se connecter avec Azure PowerShell](/powershell/azure/authenticate-azureps).

---

## <a name="order-data-box"></a>Commander une Data Box

# <a name="portal"></a>[Portail](#tab/portal)

[!INCLUDE [order-data-box-via-portal](../../includes/data-box-order-portal.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Effectuez les étapes suivantes l’aide d’Azure CLI pour commander un appareil :

1. Notez vos paramètres pour votre commande Data Box. Ces paramètres incluent vos informations personnelles/professionnelles, le nom de l’abonnement, les informations sur l’appareil et les informations d’expédition. Vous aurez besoin d’utiliser ces paramètres comme paramètres lors de l’exécution de la commande CLI pour créer la commande Data Box. Le tableau suivant indique la configuration des paramètres utilisés pour `az databox job create` :

   | Configuration (paramètre) | Description |  Exemple de valeur |
   |---|---|---|
   |resource-group| Créez-en un nouveau ou utilisez un groupe existant. Un groupe de ressources est un conteneur logique pour les ressources qui peuvent être gérées ou déployées ensemble. | « myresourcegroup »|
   |name| Nom de la commande que vous créez. | « mydataboxorder »|
   |contact-name| Nom associé à l’adresse de livraison. | « Gus Poland »|
   |phone| Numéro de téléphone de la personne ou de l’entreprise qui recevra la commande.| « 14255551234 »
   |location| Région Azure la plus proche de vous qui expédiera votre appareil.| « USA Ouest »|
   |sku| Appareil Data Box spécifique que vous commandez. Les valeurs autorisées sont : « DataBox », « DataBoxDisk » et « DataBoxHeavy »| « DataBox » |
   |email-list| Adresses e-mail associées à la commande.| "gusp@contoso.com" |
   |street-address1| Adresse postale de l’endroit où la commande sera expédiée. | « 15700 NE 39th St » |
   |street-address2| Informations sur l’adresse secondaire, comme le numéro de l’appartement ou le numéro du bâtiment. | « Bâtiment 123 » |
   |city| Ville dans laquelle l’appareil sera expédié. | « Redmond » |
   |state-or-province| État dans lequel l’appareil sera expédié.| « WA » |
   |country| Pays dans lequel l’appareil sera expédié. | « États-Unis » |
   |postal-code| Code postal associé à l’adresse d’expédition.| « 98052 »|
   |company-name| Nom de l’entreprise pour laquelle vous travaillez.| « Contoso, LTD » |
   |compte de stockage| Compte de Stockage Azure à partir duquel vous voulez importer des données.| « mystorageaccount »|
   |debug| Inclure les informations de débogage à la journalisation détaillée  | --debug |
   |help| Afficher les informations d’aide pour cette commande. | --help -h |
   |only-show-errors| Afficher uniquement les erreurs, en supprimant les avertissements. | --only-show-errors |
   |output -o| Définit le format de sortie.  Valeurs autorisées : json, jsonc, none, table, tsv, yaml, yamlc. La valeur par défaut est json. | --output "json" |
   |query| Chaîne de requêtes JMESPath. Pour plus d’informations, consultez [JMESPath](http://jmespath.org/). | --query &lt;chaîne&gt;|
   |verbose| Inclure la journalisation détaillée. | --verbose |

2. Dans l’invite de commandes de votre choix ou dans votre terminal, exécutez [az data box job create](/cli/azure/databox/job#az_databox_job_create) pour créer votre commande Azure Data Box.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Voici un exemple d’utilisation de la commande :

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   Voici la sortie de l’exécution de la commande :

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Toutes les commandes Azure CLI utilisent JSON comme format de sortie par défaut, sauf si vous le changez. Vous pouvez changer le format de sortie à l’aide du paramètre global `--output <output-format>`. Le fait de remplacer le format par « table » améliore la lisibilité de la sortie.

   Voici la même commande que celle que nous venons d’exécuter avec un petit ajustement pour changer la mise en forme :

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Voici la sortie de l’exécution de la commande :

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

Effectuez les étapes suivantes l’aide d’Azure PowerShell pour commander un appareil :

1. Avant de créer l’ordre d’importation, vous devez obtenir votre compte de stockage et enregistrer l’objet de compte de stockage dans une variable.

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Notez vos paramètres pour votre commande Data Box. Ces paramètres incluent vos informations personnelles/professionnelles, le nom de l’abonnement, les informations sur l’appareil et les informations d’expédition. Vous devrez les utiliser comme paramètres quand vous exécuterez la commande PowerShell pour créer la commande Data Box. Le tableau suivant indique la configuration des paramètres utilisés pour [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob).

    | Configuration (paramètre) | Description |  Exemple de valeur |
    |---|---|---|
    |ResourceGroupName [Obligatoire]| Utilisez un groupe de ressources existant. Un groupe de ressources est un conteneur logique pour les ressources qui peuvent être gérées ou déployées ensemble. | « myresourcegroup »|
    |Name [Obligatoire]| Nom de la commande que vous créez. | « mydataboxorder »|
    |ContactName [Obligatoire]| Nom associé à l’adresse de livraison. | « Gus Poland »|
    |PhoneNumber [Obligatoire]| Numéro de téléphone de la personne ou de l’entreprise qui recevra la commande.| « 14255551234 »
    |Location [Obligatoire]| Région Azure la plus proche de vous qui expédiera votre appareil.| "WestUS"|
    |DataBoxType [Obligatoire]| Appareil Data Box spécifique que vous commandez. Les valeurs autorisées sont : « DataBox », « DataBoxDisk » et « DataBoxHeavy »| « DataBox » |
    |EmailId [Obligatoire]| Adresses e-mail associées à la commande.| "gusp@contoso.com" |
    |StreetAddress1 [Obligatoire]| Adresse postale de l’endroit où la commande sera expédiée. | « 15700 NE 39th St » |
    |StreetAddress2| Informations sur l’adresse secondaire, comme le numéro de l’appartement ou le numéro du bâtiment. | « Bâtiment 123 » |
    |StreetAddress3| Troisième information d’adresse. | |
    |City [Obligatoire]| Ville dans laquelle l’appareil sera expédié. | « Redmond » |
    |StateOrProvinceCode [Obligatoire]| État dans lequel l’appareil sera expédié.| « WA » |
    |CountryCode [Obligatoire]| Pays dans lequel l’appareil sera expédié. | « États-Unis » |
    |PostalCode [Obligatoire]| Code postal associé à l’adresse d’expédition.| « 98052 »|
    |CompanyName| Nom de l’entreprise pour laquelle vous travaillez.| « Contoso, LTD » |
    |StorageAccountResourceId [Obligatoire]| ID du compte de stockage Azure à partir duquel vous voulez importer des données.| &lt;AzstorageAccount&gt;.id |

3. Dans l’invite de commandes de votre choix ou dans votre terminal, utilisez la commande [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob) pour créer votre commande Azure Data Box.

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   Voici la sortie de l’exécution de la commande :

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
   ```

---

## <a name="track-the-order"></a>Suivre la commande

# <a name="portal"></a>[Portail](#tab/portal)

Une fois la commande passée, vous pouvez suivre son état à partir du portail Azure. Accédez à votre commande Data Box, puis à **Vue d’ensemble** pour voir l’état. Le portail affiche la commande avec l’état **Commandé**.

Si l’appareil n’est pas disponible, vous recevez une notification. Si l’appareil est disponible, Microsoft identifie l’appareil à expédier et le prépare. Pendant la préparation de l’appareil, les actions suivantes se produisent :

* Des partages SMB sont créés pour chaque compte de stockage associé à l’appareil.
* Pour chaque partage, des informations d’identification d’accès (nom d’utilisateur et mot de passe) sont générées.
* Un mot de passe qui permet de déverrouiller l’appareil est également généré.
* La Data Box est verrouillée pour empêcher tout accès non autorisé à l’appareil à tout moment.

Une fois l’appareil préparé, le portail affiche la commande avec l’état **Traité**.

![Commande Data Box qui a été traitée](media/data-box-overview/data-box-order-status-processed.png)

Ensuite, Microsoft prépare et achemine l’appareil via un transporteur régional. Vous recevez un numéro de suivi une fois l’appareil expédié. Le portail affiche la commande dont l’état est **Distribué**.

![Commande Data Box qui a été expédié](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>Suivre une commande unique

Pour obtenir des informations de suivi sur une commande Azure Data Box unique existante, exécutez [`az databox job show`](/cli/azure/databox/job#az_databox_job_show). La commande affiche des informations sur la commande, notamment (liste non exhaustive) : nom, groupe de ressources, informations de suivi, ID d’abonnement, informations de contact, type d’expédition et référence SKU de l’appareil.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   Le tableau suivant indique les informations des paramètres pour `az databox job show` :

   | Paramètre | Description |  Exemple de valeur |
   |---|---|---|
   |resource-group [Obligatoire]| Nom du groupe de ressources associé à la commande. Un groupe de ressources est un conteneur logique pour les ressources qui peuvent être gérées ou déployées ensemble. | « myresourcegroup »|
   |name [Obligatoire]| Nom de la commande à afficher. | « mydataboxorder »|
   |debug| Inclure les informations de débogage à la journalisation détaillée | --debug |
   |help| Afficher les informations d’aide pour cette commande. | --help -h |
   |only-show-errors| Afficher uniquement les erreurs, en supprimant les avertissements. | --only-show-errors |
   |output -o| Définit le format de sortie.  Valeurs autorisées : json, jsonc, none, table, tsv, yaml, yamlc. La valeur par défaut est json. | --output "json" |
   |query| Chaîne de requêtes JMESPath. Pour plus d’informations, consultez [JMESPath](http://jmespath.org/). | --query &lt;chaîne&gt;|
   |verbose| Inclure la journalisation détaillée. | --verbose |

   Voici un exemple de la commande avec le format de sortie défini sur « table » :

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Voici la sortie de l’exécution de la commande :

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> L’ordre de liste peut être pris en charge au niveau de l’abonnement et fait du groupe de ressources un paramètre facultatif (au lieu d’un paramètre obligatoire).

### <a name="list-all-orders"></a>Lister toutes les commandes

Si vous avez commandé plusieurs dispositifs, vous pouvez exécuter [`az databox job list`](/cli/azure/databox/job#az_databox_job_list) pour voir toutes vos commandes Azure Data Box. La commande liste toutes les commandes qui appartiennent à un groupe de ressources spécifique. Également affichés dans la sortie : nom de la commande, état de l’expédition, région Azure, type de livraison, état de la commande. Les commandes annulées sont également incluses dans la liste.
La commande affiche également les horodatages de chaque commande.

```azurecli
az databox job list --resource-group <resource-group>
```

Le tableau suivant indique les informations des paramètres pour `az databox job list` :

   | Paramètre | Description |  Exemple de valeur |
   |---|---|---|
   |resource-group [Obligatoire]| Nom du groupe de ressources qui contient les commandes. Un groupe de ressources est un conteneur logique pour les ressources qui peuvent être gérées ou déployées ensemble. | « myresourcegroup »|
   |debug| Inclure les informations de débogage à la journalisation détaillée | --debug |
   |help| Afficher les informations d’aide pour cette commande. | --help -h |
   |only-show-errors| Afficher uniquement les erreurs, en supprimant les avertissements. | --only-show-errors |
   |output -o| Définit le format de sortie.  Valeurs autorisées : json, jsonc, none, table, tsv, yaml, yamlc. La valeur par défaut est json. | --output "json" |
   |query| Chaîne de requêtes JMESPath. Pour plus d’informations, consultez [JMESPath](http://jmespath.org/). | --query &lt;chaîne&gt;|
   |verbose| Inclure la journalisation détaillée. | --verbose |

   Voici un exemple de la commande avec le format de sortie défini sur « table » :

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Voici la sortie de l’exécution de la commande :

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>Suivre une commande unique

Pour obtenir des informations de suivi sur une commande Azure Data Box unique existante, exécutez [Get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob). La commande affiche des informations sur la commande, notamment (liste non exhaustive) : nom, groupe de ressources, informations de suivi, ID d’abonnement, informations de contact, type d’expédition et référence SKU de l’appareil.

> [!NOTE]
> `Get-AzDataBoxJob` permet d’afficher les commandes uniques et multiples. Pour les commandes uniques, cependant, vous devez spécifier le nom de la commande.

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   Le tableau suivant indique les informations des paramètres pour `Get-AzDataBoxJob` :

   | Paramètre | Description |  Exemple de valeur |
   |---|---|---|
   |ResourceGroup [Obligatoire]| Nom du groupe de ressources associé à la commande. Un groupe de ressources est un conteneur logique pour les ressources qui peuvent être gérées ou déployées ensemble. | « myresourcegroup »|
   |Name [Obligatoire]| Nom de la commande sur laquelle obtenir des informations. | « mydataboxorder »|
   |ResourceId| ID de la ressource associée à la commande. |  |

   Voici un exemple de la commande avec la sortie :

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   Voici la sortie de l’exécution de la commande :

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>Lister toutes les commandes

Si vous avez commandé plusieurs dispositifs, vous pouvez exécuter [`Get-AzDataBoxJob`](/powershell/module/az.databox/Get-AzDataBoxJob) pour voir toutes vos commandes Azure Data Box. La commande liste toutes les commandes qui appartiennent à un groupe de ressources spécifique. Également affichés dans la sortie : nom de la commande, état de l’expédition, région Azure, type de livraison, état de la commande. Les commandes annulées sont également incluses dans la liste.
La commande affiche également les horodatages de chaque commande.

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

Voici un exemple de la commande :

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

Voici la sortie de l’exécution de la commande :

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
PS C:\WINDOWS\system32>
```

---

## <a name="cancel-the-order"></a>Annuler la commande

# <a name="portal"></a>[Portail](#tab/portal)

Pour annuler cette commande, dans le portail Azure, accédez à **Vue d’ensemble**, puis sélectionnez **Annuler** dans la barre de commandes.

Après avoir passé commande, vous pouvez l’annuler à tout moment tant qu’elle n’a pas été traitée.

Pour supprimer une commande annulée, accédez à **Vue d’ensemble**, puis sélectionnez **Supprimer** dans la barre de commandes.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>Annuler une commande

Pour annuler une commande Azure Data Box, exécutez [`az databox job cancel`](/cli/azure/databox/job#az_databox_job_cancel). Vous devez spécifier la raison de l’annulation de la commande.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   Le tableau suivant indique les informations des paramètres pour `az databox job cancel` :

   | Paramètre | Description |  Exemple de valeur |
   |---|---|---|
   |resource-group [Obligatoire]| Nom du groupe de ressources associé à la commande à supprimer. Un groupe de ressources est un conteneur logique pour les ressources qui peuvent être gérées ou déployées ensemble. | « myresourcegroup »|
   |name [Obligatoire]| Nom de la commande à supprimer. | « mydataboxorder »|
   |reason [Obligatoire]| Motif de l’annulation de la commande. | « J’ai entré des informations erronées et j’ai dû annuler la commande. » |
   |Oui| Ne pas demander de confirmation. | --yes (-y)| 
   |debug| Inclure les informations de débogage à la journalisation détaillée | --debug |
   |help| Afficher les informations d’aide pour cette commande. | --help -h |
   |only-show-errors| Afficher uniquement les erreurs, en supprimant les avertissements. | --only-show-errors |
   |output -o| Définit le format de sortie.  Valeurs autorisées : json, jsonc, none, table, tsv, yaml, yamlc. La valeur par défaut est json. | --output "json" |
   |query| Chaîne de requêtes JMESPath. Pour plus d’informations, consultez [JMESPath](http://jmespath.org/). | --query &lt;chaîne&gt;|
   |verbose| Inclure la journalisation détaillée. | --verbose |

   Voici un exemple de la commande avec la sortie :

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Voici la sortie de l’exécution de la commande :

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Supprimer une commande

Si vous avez annulé une commande Azure Data Box, vous pouvez exécuter [`az databox job delete`](/cli/azure/databox/job#az_databox_job_delete) pour la supprimer.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   Le tableau suivant indique les informations des paramètres pour `az databox job delete` :

   | Paramètre | Description |  Exemple de valeur |
   |---|---|---|
   |resource-group [Obligatoire]| Nom du groupe de ressources associé à la commande à supprimer. Un groupe de ressources est un conteneur logique pour les ressources qui peuvent être gérées ou déployées ensemble. | « myresourcegroup »|
   |name [Obligatoire]| Nom de la commande à supprimer. | « mydataboxorder »|
   |subscription| Nom ou ID (GUID) de votre abonnement Azure. | « xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx » |
   |Oui| Ne pas demander de confirmation. | --yes (-y)|
   |debug| Inclure les informations de débogage à la journalisation détaillée | --debug |
   |help| Afficher les informations d’aide pour cette commande. | --help -h |
   |only-show-errors| Afficher uniquement les erreurs, en supprimant les avertissements. | --only-show-errors |
   |output -o| Définit le format de sortie.  Valeurs autorisées : json, jsonc, none, table, tsv, yaml, yamlc. La valeur par défaut est json. | --output "json" |
   |query| Chaîne de requêtes JMESPath. Pour plus d’informations, consultez [JMESPath](http://jmespath.org/). | --query &lt;chaîne&gt;|
   |verbose| Inclure la journalisation détaillée. | --verbose |

Voici un exemple de la commande avec la sortie :

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Voici la sortie de l’exécution de la commande :

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>Annuler une commande

Pour annuler une commande Azure Data Box, exécutez [Stop-AzDataBoxJob](/powershell/module/az.databox/stop-azdataboxjob). Vous devez spécifier la raison de l’annulation de la commande.

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

Le tableau suivant indique les informations des paramètres pour `Stop-AzDataBoxJob` :

| Paramètre | Description |  Exemple de valeur |
|---|---|---|
|ResourceGroup [Obligatoire]| Nom du groupe de ressources associé à la commande à annuler. Un groupe de ressources est un conteneur logique pour les ressources qui peuvent être gérées ou déployées ensemble. | « myresourcegroup »|
|Name [Obligatoire]| Nom de la commande à supprimer. | « mydataboxorder »|
|Reason [Obligatoire]| Motif de l’annulation de la commande. | « J’ai entré des informations erronées et j’ai dû annuler la commande. » |
|Force | Force l’exécution de la cmdlet sans confirmation de l’utilisateur. | -Force |

Voici un exemple de la commande avec la sortie :

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

Voici la sortie de l’exécution de la commande :

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>Supprimer une commande

Si vous avez annulé une commande Azure Data Box, vous pouvez exécuter [`Remove-AzDataBoxJob`](/powershell/module/az.databox/remove-azdataboxjob) pour la supprimer.

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

Le tableau suivant indique les informations des paramètres pour `Remove-AzDataBoxJob` :

| Paramètre | Description |  Exemple de valeur |
|---|---|---|
|ResourceGroup [Obligatoire]| Nom du groupe de ressources associé à la commande à supprimer. Un groupe de ressources est un conteneur logique pour les ressources qui peuvent être gérées ou déployées ensemble. | « myresourcegroup »|
|Name [Obligatoire]| Nom de la commande à supprimer. | « mydataboxorder »|
|Force | Force l’exécution de la cmdlet sans confirmation de l’utilisateur. | -Force |

Voici un exemple de la commande avec la sortie :

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

Voici la sortie de l’exécution de la commande :

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert des articles Azure Data Box comme les suivants :

> [!div class="checklist"]
>
> * Conditions préalables au déploiement de la Data Box
> * Commander une Data Box
> * Suivre la commande
> * Annuler la commande

Passez au tutoriel suivant pour apprendre à configurer votre Data Box.

> [!div class="nextstepaction"]
> [Configurer votre Azure Data Box](./data-box-deploy-set-up.md)
