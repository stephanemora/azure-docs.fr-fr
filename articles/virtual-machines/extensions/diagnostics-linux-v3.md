---
title: Azure Compute – Extension de diagnostic Linux 3.0
description: Comment configurer l’extension de diagnostic Linux Azure 3.0 pour collecter des métriques et consigner les événements provenant de machines virtuelles Linux qui s’exécutent dans Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 12/13/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 20be29d428fe6eaf9e7e64b4536c5014641c5416
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111962526"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>Utiliser l’extension de diagnostic Linux 3.0 pour superviser les métriques et les journaux

Ce document décrit la version 3.0 et les versions ultérieures de l’extension de diagnostic Linux.

> [!IMPORTANT]
> Pour plus d’informations sur la version 2.3 et sur les versions antérieures, consultez [Superviser les données de performances et de diagnostic d’une machine virtuelle Linux](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2).

## <a name="introduction"></a>Introduction

L’extension de diagnostic Linux aide l’utilisateur à surveiller l’intégrité d’une machine virtuelle Linux qui s’exécute sur Microsoft Azure. Elle présente les fonctionnalités suivantes :

* Elle collecte des métriques de performances du système auprès de la machine virtuelle et les stocke dans une table spécifique d’un compte de stockage désigné.
* Elle récupère les journaux d’événements Syslog et les stocke dans une table spécifique du compte de stockage désigné.
* Elle permet aux utilisateurs de personnaliser les métriques des données qui sont collectées et chargées.
* Elle permet aux utilisateurs de personnaliser les fonctions Syslog et les niveaux de gravité des événements qui sont collectés et chargés.
* Elle permet aux utilisateurs de télécharger les fichiers journaux spécifiés dans la table de stockage désignée.
* Elle prend en charge l’envoi des métriques et des événements des journaux à des points de terminaison Azure Event Hubs arbitraires et à des blobs au format JSON dans le compte de stockage désigné.

Cette extension fonctionne avec les deux modèles de déploiement d’Azure.

## <a name="install-the-extension-on-a-vm"></a>Installer l’extension sur une machine virtuelle

Vous pouvez activer l’extension en utilisant des cmdlets Azure PowerShell, des scripts Azure CLI, des modèles Azure Resource Manager (modèles ARM) ou le portail Azure. Pour plus d’informations, consultez [Fonctionnalités des extensions](features-linux.md).

>[!NOTE]
>Certains composants de l’extension de machine virtuelle de diagnostic Linux sont également fournis avec l’[extension de machine virtuelle Log Analytics](./oms-linux.md). En raison de cette architecture, des conflits peuvent survenir si les deux extensions sont instanciées dans le même modèle ARM. 
>
>Pour éviter les conflits au moment de l’installation, utilisez la [directive `dependsOn`](../../azure-resource-manager/templates/resource-dependency.md#dependson) pour vous assurer que les extensions sont installées de manière séquentielle. Les extensions peuvent être installées dans n’importe quel ordre.

Ces instructions d’installation et un [exemple de configuration téléchargeable](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) configurent l’extension de diagnostic Linux 3.0 pour :

* Capturer et stocker les mêmes métriques que dans l’extension de diagnostic Linux 2.3.
* Capturer un ensemble utile de métriques du système de fichiers. Cette fonctionnalité est nouvelle dans l’extension de diagnostic Linux 3.0.
* Capturer la collection Syslog par défaut que l’extension de diagnostic Linux 2.3 a activée.
* Permettre l’utilisation du portail Azure pour les graphes et les alertes sur des métriques de machine virtuelle.

La configuration téléchargeable n’est qu’un exemple. Modifiez-la pour l’adapter à vos besoins.

### <a name="supported-linux-distributions"></a>Distributions de Linux prises en charge

L’extension de diagnostic Linux prend en charge les distributions et versions suivantes. La liste des distributions et versions s’applique uniquement aux images du fournisseur Linux approuvées par Azure. L’extension ne prend généralement pas en charge les images BYOL et BYOS de tiers, comme les appliances.

Une distribution répertoriant uniquement les versions principales, telles que Debian 7, est également prise en charge pour toutes les versions mineures. Si une version mineure est spécifiée, seule cette version est prise en charge. Si un signe plus (+) est ajouté, les versions mineures égales ou ultérieures à la version spécifiée sont prises en charge.

Distributions et versions prises en charge :

- Ubuntu 18.04, 16.04, 14.04
- CentOS 7, 6.5+
- Oracle Linux 7, 6.4
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- Red Hat Enterprise Linux (RHEL) 7, 6.7+

### <a name="prerequisites"></a>Prérequis

* **Agent Azure Linux version 2.2.0 ou ultérieure**. La plupart des images de la galerie Linux de machines virtuelles Azure incluent la version 2.2.7 ou ultérieure. Exécutez `/usr/sbin/waagent -version` pour vérifier la version installée sur la machine virtuelle. Si la machine virtuelle exécute une version antérieure, [mettez à jour l’agent invité](./update-linux-agent.md).
* **Interface de ligne de commande Azure**. Le cas échéant, [configurez l’environnement Azure CLI](/cli/azure/install-azure-cli) sur votre machine.
* **Commande wget**. Si vous ne l’avez pas encore, exécutez `sudo apt-get install wget`.
* **Abonnement Azure**. 
* **Compte de stockage universel** pour stocker les données. Les comptes de stockage universels doivent prendre en charge le stockage de tables. Un compte de stockage Blob ne fonctionnera pas.
* **Python 2**.

### <a name="python-requirement"></a>Exigence relative à Python

L’extension de diagnostic Linux nécessite Python 2. Si votre machine virtuelle utilise une distribution qui n’inclut pas Python 2 par défaut, vous devez l’installer. Les exemples de commandes suivants installent Python 2 sur différentes distributions :   

- Red Hat, CentOS, Oracle : `yum install -y python2`
- Ubuntu, Debian : `apt-get install -y python2`
- SUSE : `zypper install -y python2`

Le fichier exécutable `python2` doit avoir *python* comme alias. Voici une méthode pour définir cet alias :

1. Exécutez la commande suivante pour supprimer tous les alias existants.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Pour créer l’alias, exécutez la commande suivante.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Exemple d’installation

L’exemple de configuration téléchargé dans les exemples suivants collecte un ensemble de données standard et les envoie au stockage de tables. L’URL de l’exemple de configuration et son contenu peut changer. 

Dans la plupart des cas, vous devez télécharger une copie du fichier JSON des paramètres du portail et la personnaliser en fonction de vos besoins. Utilisez ensuite des modèles ou votre propre automatisation pour utiliser une version personnalisée du fichier config au lieu de le télécharger à chaque fois depuis l’URL.

> [!NOTE]
> Pour les exemples suivants, renseignez les valeurs correctes des variables de la première section avant d’exécuter le code. 
#### <a name="azure-cli-sample"></a>Exemple Azure CLI

```azurecli
# Set your Azure VM diagnostic variables.
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace the storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-to-install-lad-30-on-the-virtual-machine-scale-set-instance"></a>Exemple Azure CLI pour installer l’extension de diagnostic Linux 3.0 sur l’instance de groupe de machines virtuelles identiques

```azurecli
#Set your Azure Virtual Machine Scale Sets diagnostic variables.
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the virtual machine scale set resource ID. Replace the storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>Exemple de code PowerShell

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="update-the-extension-settings"></a>Mettre à jour les paramètres de l’extension

Une fois que vous avez modifié vos paramètres protégés ou publics, déployez-les sur la machine virtuelle en exécutant la même commande. Si les paramètres ont été modifiés, les mises à jour sont envoyées à l’extension. L’extension de diagnostic Linux recharge la configuration et redémarre automatiquement.

### <a name="migrate-from-previous-versions-of-the-extension"></a>Migrer depuis des versions antérieures de l’extension

La version la plus récente de l’extension est la version *4.0*. 

> [!IMPORTANT]
> Cette extension introduit des changements cassants à la configuration. L’un de ces changements a amélioré la sécurité de l’extension, de sorte que la compatibilité descendante avec la version 2.x n’a pas pu être maintenue. En outre, l’éditeur de cette extension diffère de celui des versions 2.x.
>
> Pour migrer de la version 2.x vers la nouvelle version, vous devez d’abord désinstaller l’ancienne extension (sous l’ancien nom de l’éditeur). Installez ensuite la version 3.

Recommandations :

* Installez l’extension avec la mise à niveau automatique de version mineure activée.
  * Sur les machines virtuelles du modèle de déploiement classique, spécifiez la version `3.*` si vous installez l’extension via Azure XPLAT CLI ou PowerShell.
  * Sur les machines virtuelles du modèle de déploiement Azure Resource Manager, incluez `"autoUpgradeMinorVersion": true` dans le modèle de déploiement des machines virtuelles.
* Utilisez un compte de stockage nouveau ou différent pour l’extension de diagnostic Linux 3.0. Les versions 2.3 et 3.0 de l’extension de diagnostic Linux présentent plusieurs petites incompatibilités qui rendent problématique le partage d’un compte :
  * L’extension de diagnostic Linux 3.0 stocke les événements Syslog dans une table qui a un nom différent.
  * Les chaînes `counterSpecifier` pour les métriques `builtin` sont différentes dans l’extension de diagnostic Linux 3.0.

## <a name="protected-settings"></a>Paramètres protégés

Cet ensemble d’informations de configuration contient des informations sensibles qui doivent être protégées d’une exposition publique. Il contient par exemple les informations d’identification du stockage. Ces paramètres sont transmis et stockés par l’extension sous une forme chiffrée.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Nom | Valeur
---- | -----
storageAccountName | Nom du compte de stockage dans lequel les données sont écrites par l’extension.
storageAccountEndPoint | (Facultatif) Point de terminaison identifiant le cloud où se trouve le compte de stockage. Si ce paramètre est absent, l’extension de diagnostic Linux utilise par défaut le cloud public Azure, `https://core.windows.net`. Pour utiliser un compte de stockage dans Azure Allemagne, Azure Government ou Azure Chine 21Vianet, définissez cette valeur comme requis.
storageAccountSasToken | [Jeton SAP de compte](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) pour les services Blob et Table (`ss='bt'`). Il s’applique aux conteneurs et aux objets (`srt='co'`). Il accorde les autorisations d’ajout, de création, de liste, de mise à jour et d’écriture (`sp='acluw'`). *N’incluez pas* le point d’interrogation ( ?) du début.
mdsdHttpProxy | (Facultatif) Informations du proxy HTTP dont l’extension a besoin pour se connecter au compte de stockage et au point de terminaison spécifiés.
sinksConfig | (Facultatif) Détails des destinations alternatives auxquelles les métriques et les événements peuvent être livrés. Les sections suivantes traitent des détails sur chaque récepteur de données pris en charge par l’extension.

Pour obtenir un jeton SAP dans un modèle ARM, utilisez la fonction `listAccountSas`. Pour obtenir un exemple de modèle, consultez [Exemple de fonction de liste](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Vous pouvez construire le jeton SAP nécessaire par le biais du portail Azure :

1. Sélectionnez le compte de stockage universel sur lequel vous voulez que l’extension écrive.
1. Dans le menu de gauche, sous **Paramètres**, sélectionnez **Signature d’accès partagé**.
1. Effectuez les sélections comme décrit précédemment.
1. Sélectionnez **Générer la SAP**.

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Capture d’écran montrant la page Signature d’accès partagé avec le bouton Générer la SAP.":::

Copiez la signature d’accès partagé générée dans le champ `storageAccountSasToken`. Supprimez le point d’interrogation (?) figurant au début.

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

La section facultative `sinksConfig` définit d’autres destinations vers lesquelles l’extension envoie les informations qu’elle collecte. Le tableau `sink` contient un objet pour chaque récepteur de données supplémentaire. L’attribut `type` détermine les autres attributs de l’objet.

Élément | Valeur
------- | -----
name | Chaîne qui fait référence à ce récepteur ailleurs dans la configuration de l’extension.
type | Type du récepteur défini. Détermine les autres valeurs (le cas échéant) dans les instances de ce type.

La version 3.0 de l’extension de diagnostic Linux prend en charge deux types de récepteurs : `EventHub` et `JsonBlob`.

#### <a name="eventhub-sink"></a>Récepteur EventHub

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

L’entrée `"sasURL"` contient l’URL complète, y compris le jeton SAP, de l’Event Hub sur lequel les données doivent être publiées. L’extension de diagnostic Linux nécessite une signature d’accès partagé pour nommer une stratégie qui active la revendication d’envoi. 

Par exemple :

* Créez un espace de noms Azure Event Hubs appelé `contosohub`.
* Créez un Event Hub dans l’espace de noms appelé `syslogmsgs`.
* Créez une stratégie d’accès partagé sur l’Event Hub qui active la réclamation d’envoi. Nommez la stratégie `writer`.

Si votre SAP est valable jusqu’à minuit (UTC) le 1er janvier 2018, la valeur sasURL peut être similaire à l’exemple suivant :

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Pour plus d’informations sur la génération et l’extraction d’informations sur les jetons SAP pour Event Hubs, consultez [Générer un jeton SAP](/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="jsonblob-sink"></a>Récepteur JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Les données dirigées vers un récepteur `JsonBlob` sont stockées dans des blobs dans Stockage Azure. Chaque instance de l’extension de diagnostic Linux crée un objet blob toutes les heures pour chaque nom de récepteur. Chaque blob contient toujours un tableau syntaxiquement valide d’objets JSON. Les nouvelles entrées sont ajoutées au tableau de manière atomique. 

Les blobs sont stockés dans un conteneur du même nom que le récepteur. Les règles de Stockage Azure pour les noms de conteneur de blobs s’appliquent aux noms des récepteurs `JsonBlob`. Le nom doit comporter entre 3 et 63 caractères ASCII alphanumériques en minuscules ou des tirets.

## <a name="public-settings"></a>Paramètres publics

La structure des paramètres publics contient différents blocs de paramètres qui contrôlent les informations collectées par l’extension. Chaque paramètre est facultatif. Si vous spécifiez `ladCfg`, vous devez aussi spécifier `StorageAccount`.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Élément | Valeur
------- | -----
StorageAccount | Nom du compte de stockage dans lequel les données sont écrites par l’extension. Il doit s’agir du nom spécifié dans les [paramètres protégés](#protected-settings).
mdsdHttpProxy | (Facultatif) Comme dans les [paramètres protégés](#protected-settings). La valeur publique est remplacée par la valeur privée si celle-ci est définie. Placez les paramètres de proxy qui contiennent un secret, comme un mot de passe, dans les [paramètres protégés](#protected-settings).

Les sections suivantes fournissent des détails sur les éléments restants.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

La structure `ladCfg` est facultative. Elle contrôle la collecte des métriques et des journaux qui sont livrés au service Azure Monitor Metrics et à d’autres récepteurs de données. Vous devez spécifier :

* Soit `performanceCounters`, soit `syslogEvents`, soit les deux. 
* La structure `metrics`.

Élément | Valeur
------- | -----
eventVolume | (Facultatif) Contrôle le nombre de partitions créées dans la table de stockage. La valeur doit être `"Large"`, `"Medium"` ou `"Small"`. Si la valeur n’est pas spécifiée, la valeur par défaut est `"Medium"`.
sampleRateInSeconds | (Facultatif) Intervalle par défaut entre les collectes des métriques brutes (non agrégées). L’échantillonnage le plus petit pris en charge de 15 secondes. Si la valeur n’est pas spécifiée, la valeur par défaut est `15`.

#### <a name="metrics"></a>Mesures

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Élément | Valeur
------- | -----
resourceId | ID de ressource Azure Resource Manager de la machine virtuelle ou du groupe identique auquel la machine virtuelle appartient. Ce paramètre doit également être spécifié si un récepteur `JsonBlob` est utilisé dans la configuration.
scheduledTransferPeriod | Fréquence à laquelle les métriques agrégées doivent être calculées et transférées à Azure Monitor Metrics. La fréquence est exprimée sous la forme d’un intervalle de temps ISO 8601. La périodicité de transfert la plus petite est de 60 secondes, c’est-à-dire PT1M. Spécifiez au moins une fréquence `scheduledTransferPeriod`.

Des échantillons des métriques spécifiées dans la section `performanceCounters` sont collectés toutes les 15 secondes ou selon l’échantillonnage explicitement défini pour le compteur. Si plusieurs fréquences `scheduledTransferPeriod` apparaissent, comme dans l’exemple, chaque agrégation est calculée indépendamment.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

La section facultative `performanceCounters` contrôle la collecte des métriques. Les échantillons bruts sont agrégés pour chaque [`scheduledTransferPeriod`](#metrics) pour produire ces valeurs :

* Moyenne
* Minimum
* Maximale
* Dernière valeur collectée
* Nombre d’échantillons bruts utilisés pour calculer l’agrégation

Élément | Valeur
------- | -----
sinks | (Facultatif) Liste, séparée par des virgules, des noms des récepteurs auxquels l’extension de diagnostic Linux envoie les résultats des métriques agrégées. Toutes les métriques agrégées sont publiées sur chaque récepteur répertorié. Exemple : `"EHsink1, myjsonsink"`. Pour plus d’informations, consultez [`sinksConfig`](#sinksconfig).
type | Identifie le fournisseur réel de la mesure.
class | Avec `"counter"`, identifie la métrique spécifique dans l’espace de noms du fournisseur.
counter | Avec `"class"`, identifie la métrique spécifique dans l’espace de noms du fournisseur.
counterSpecifier | Identifie la métrique spécifique dans l’espace de noms Azure Monitor Metrics.
condition | (Facultatif) Sélectionne une instance spécifique de l’objet à laquelle la métrique s’applique ou sélectionne l’agrégation sur toutes les instances de cet objet. 
sampleRate | Intervalle ISO 8601 qui définit la fréquence à laquelle des échantillons bruts sont collectés pour cette métrique. Si la valeur n’est pas définie, l’intervalle de collecte est défini par la valeur de [`sampleRateInSeconds`](#ladcfg). L’échantillonnage le plus court pris en charge est de 15 secondes (PT15S).
unité | Définit l’unité pour la métrique. Doit être une des chaînes suivantes : `"Count"`, `"Bytes"`, `"Seconds"`, `"Percent"`, `"CountPerSecond"`, `"BytesPerSecond"`, `"Millisecond"`. Les consommateurs des données collectées attendent des valeurs de données collectées correspondant à cette unité. L’extension de diagnostic Linux ignore ce champ.
displayName | Étiquette à attacher aux données dans Azure Monitor Metrics. Cette étiquette est dans la langue spécifiée par les paramètres régionaux associés. L’extension de diagnostic Linux ignore ce champ.

`counterSpecifier` est un identificateur arbitraire. Les consommateurs de métriques, comme les fonctionnalités de graphiques et d’alertes du portail Azure, utilisent `counterSpecifier` comme « clé » qui identifie une métrique ou l’instance d’une métrique. 

Pour les métriques `builtin`, nous recommandons des valeurs de `counterSpecifier` commençant par `/builtin/`. Si vous collectez une instance spécifique d’une métrique, nous vous recommandons de joindre l’identificateur de l’instance à la valeur de `counterSpecifier`. 

Voici quelques exemples :

* `/builtin/Processor/PercentIdleTime` : temps d’inactivité moyen pour tous les processeurs virtuels
* `/builtin/Disk/FreeSpace(/mnt)` : espace libre pour le système de fichiers `/mnt`
* `/builtin/Disk/FreeSpace` : espace libre moyen pour tous les systèmes de fichiers montés

L’extension de diagnostic Linux et le portail Azure ne s’attendent pas à ce que la valeur de `counterSpecifier` corresponde à un modèle particulier. Soyez cohérent dans la façon dont vous construisez les valeurs de `counterSpecifier`.

Quand vous spécifiez `performanceCounters`, l’extension de diagnostic Linux écrit toujours les données dans une table de Stockage Azure. Les mêmes données peuvent être écrites dans des blobs JSON ou des Event Hubs, ou les deux. Vous ne pouvez cependant pas désactiver le stockage des données dans une table. 

Toutes les instances de l’extension de diagnostic Linux qui utilisent le même nom et le même point de terminaison de compte de stockage ajoutent leurs métriques et leurs journaux à la même table. Si un trop grand nombre de machines virtuelles écrivent dans la même partition de table, Azure peut limiter les écritures sur cette partition. 

Le paramètre `eventVolume` permet de répartir les entrées entre 1 (Small), 10 (Medium) ou 100 (Large) partitions. En règle générale, 10 partitions (Medium) sont suffisantes pour éviter la limitation du trafic. 

La fonctionnalité Azure Monitor Metrics du portail Azure utilise les données de cette table pour produire des graphes ou déclencher des alertes. Le nom de la table est la concaténation des chaînes suivantes :

* `WADMetrics`
* `"scheduledTransferPeriod"` pour les valeurs agrégées stockées dans la table
* `P10DV2S`
* Une date, sous la forme « AAAAMMJJ », qui change tous les 10 jours

Exemples : `WADMetricsPT1HP10DV2S20170410` et `WADMetricsPT1MP10DV2S20170609`.

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

La section facultative `syslogEvents` contrôle la collecte des événements des journaux auprès de Syslog. Si la section est omise, les événements Syslog ne sont pas capturés du tout.

La collection `syslogEventConfiguration` a une entrée pour chaque fonction Syslog qui présente un intérêt. Si `minSeverity` est défini sur `"NONE"` pour une fonction donnée ou si cette fonction n’apparaît pas du tout dans l’élément, aucun événement de cette fonction n’est capturé.

Élément | Valeur
------- | -----
sinks | Une liste séparée par des virgules de noms de récepteurs sur lesquels des événements de journaux sont publiés. Tous les événements de journaux correspondant aux restrictions de `syslogEventConfiguration` sont publiés sur chaque récepteur listé. Exemple : `"EHforsyslog"`
facilityName | Nom de la fonction Syslog, comme `"LOG_USER"` ou `"LOG\LOCAL0"`. Pour plus d’informations, consultez la section « Facility » de la [page man de Syslog](http://man7.org/linux/man-pages/man3/syslog.3.html).
minSeverity | Niveau de gravité Syslog, comme `"LOG_ERR"` ou `"LOG_INFO"`. Pour plus d’informations, consultez la section « Level » de la [page man de Syslog](http://man7.org/linux/man-pages/man3/syslog.3.html). L’extension capture les événements envoyés à la fonction à un niveau supérieur ou égal au niveau spécifié.

Quand vous spécifiez `syslogEvents`, l’extension de diagnostic Linux écrit toujours les données dans une table de Stockage Azure. Les mêmes données peuvent être écrites dans des blobs JSON ou des Event Hubs, ou les deux. Vous ne pouvez cependant pas désactiver le stockage des données dans une table. 

Le comportement de partitionnement pour la table est identique à celui décrit pour `performanceCounters`. Le nom de la table est la concaténation des chaînes suivantes :

* `LinuxSyslog`
* Une date, sous la forme « AAAAMMJJ », qui change tous les 10 jours

Exemples : `LinuxSyslog20170410` et `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

La section `perfCfg` est facultative. Elle contrôle l’exécution de requêtes [Open Management Infrastructure (OMI)](https://github.com/Microsoft/omi) arbitraires.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Élément | Valeur
------- | -----
espace de noms | (Facultatif) Espace de noms OMI dans lequel la requête doit être exécutée. Si la valeur n’est pas spécifiée, la valeur par défaut est `"root/scx"`. Elle est implémentée par les [fournisseurs multiplateformes de System Center](https://github.com/Microsoft/SCXcore).
query | Requête OMI à exécuter.
table | (Facultatif) Table Stockage Azure, dans le compte de stockage désigné. Pour plus d’informations, consultez [Paramètres protégés](#protected-settings).
frequency | (Facultatif) Nombre de secondes entre les exécutions de requête. La valeur par défaut est 300 (5 minutes). La valeur minimale est de 15 secondes.
sinks | (Facultatif) Liste, séparée par des virgules, des noms d’autres récepteurs sur lesquels les résultats des échantillons bruts des métriques doivent être publiés. Aucune agrégation de ces échantillons bruts n’est calculée par l’extension ou par Azure Monitor Metrics.

Vous devez spécifier `"table"`, `"sinks"` ou les deux.

### <a name="filelogs"></a>fileLogs

La section `fileLogs` contrôle la capture des fichiers journaux. L’extension de diagnostic Linux capture les nouvelles lignes de texte à mesure qu’elles sont écrites dans le fichier. Elle les écrit dans les lignes de la table et/ou dans les récepteurs spécifiés (`JsonBlob` ou `EventHub`).

> [!NOTE]
> Les `fileLogs` sont capturés par un sous-composant de l’extension de diagnostic Linux appelé `omsagent`. Pour collecter les `fileLogs`, vérifiez que l’utilisateur `omsagent` dispose des autorisations de lecture sur les fichiers que vous spécifiez. L’utilisateur doit également disposer d’autorisations d’exécution sur tous les répertoires dans le chemin d’accès à ce fichier. Une fois l’extension de diagnostic Linux installée, vous pouvez vérifier les autorisations en exécutant `sudo su omsagent -c 'cat /path/to/file'`.

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Élément | Valeur
------- | -----
fichier | Nom du chemin complet du fichier journal à observer et à capturer. Le nom du chemin d’accès doit nommer un seul fichier. Il ne peut pas nommer un répertoire ni contenir des caractères génériques. Le compte d’utilisateur `omsagent` doit avoir un accès en lecture sur le chemin du fichier.
table | (Facultatif) Table de Stockage Azure dans laquelle les nouvelles lignes de la « fin » du fichier sont écrites. La table doit se trouver dans le compte de stockage désigné, comme spécifié dans la configuration protégée. 
sinks | (Facultatif) Liste, séparée par des virgules, des noms d’autres récepteurs auxquels les lignes des journaux sont envoyées.

Vous devez spécifier `"table"`, `"sinks"` ou les deux.

## <a name="metrics-supported-by-the-builtin-provider"></a>Métriques prises en charge par le fournisseur intégré

Le fournisseur de métriques `builtin` est une source de métriques parmi les plus intéressantes pour un large éventail d’utilisateurs. Ces métriques se répartissent en cinq classes principales :

* Processeur
* Mémoire
* Réseau
* Système de fichiers
* Disque

### <a name="builtin-metrics-for-the-processor-class"></a>métriques intégrées pour la classe Processeur

La classe de métriques Processeur fournit des informations sur l’utilisation du processeur dans la machine virtuelle. Quand des pourcentages sont agrégés, le résultat est la moyenne pour tous les UC. 

Dans une machine virtuelle à 2 processeurs virtuels, si un processeur virtuel est occupé à 100 % et que l’autre est inactif à 100 %, le `PercentIdleTime` signalé est de 50. Si chaque processeur virtuel est occupé à 50 % pendant la même période, le résultat signalé est également de 50. Dans une machine virtuelle à 4 processeurs virtuels, si un processeur virtuel est occupé à 100 % et que les autres sont inactifs, le `PercentIdleTime` signalé est de 75.

Compteur | Signification
------- | -------
PercentIdleTime | Pourcentage de temps de la fenêtre d’agrégation pendant lequel les UC ont exécuté la boucle d’inactivité du noyau
percentProcessorTime | Pourcentage de temps passé à exécuter un thread actif
PercentIOWaitTime | Pourcentage de temps passé à attendre la fin d’opérations d’E/S
PercentInterruptTime | Pourcentage de temps passé à exécuter des interruptions matérielles ou logicielles et des appels DPC (appels de procédure différés)
PercentUserTime | Relativement au temps d’activité de la fenêtre d’agrégation, pourcentage de temps passé en mode utilisateur à une priorité normale
PercentNiceTime | Pourcentage de temps passé à une priorité abaissée (commande nice), relativement au temps d’activité
PercentPrivilegedTime | Pourcentage de temps passé en mode privilégié (noyau), relativement au temps d’activité

La somme des 4 premiers compteurs doit être de 100 %. La somme des 3 premiers compteurs est également de 100 %. Ces trois compteurs subdivisent la somme de `PercentProcessorTime`, `PercentIOWaitTime` et `PercentInterruptTime`.

Pour agréger une seule métrique sur tous les UC, définissez `"condition": "IsAggregate=TRUE"`. Pour obtenir une métrique pour un processeur spécifique, par exemple le deuxième processeur logique d’une machine virtuelle à quatre processeurs virtuels, définissez `"condition": "Name=\\"1\\""`. Les numéros des processeurs logiques sont dans la plage `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>métriques intégrées pour la classe Mémoire

La classe de métriques Mémoire fournit des informations sur l’utilisation, la pagination et les échanges de la mémoire.

Compteur | Signification
------- | -------
AvailableMemory | Mémoire physique disponible en Mio
PercentAvailableMemory | Mémoire physique disponible sous forme de pourcentage de la mémoire totale
UsedMemory | Mémoire physique utilisée (Mio)
PercentUsedMemory | Mémoire physique utilisée sous forme de pourcentage de la mémoire totale
PagesPerSec | Pagination totale (lecture/écriture)
PagesReadPerSec | Pages lues à partir du magasin de stockage, comme le fichier d’échange, les fichiers programme et les fichiers mappés
PagesWrittenPerSec | Pages écrites dans le magasin de stockage, comme le fichier d’échange et les fichiers mappés
AvailableSwap | Espace d’échange non utilisé (Mio)
PercentAvailableSwap | Espace d’échange non utilisé sous forme de pourcentage de l’espace d’échange total
UsedSwap | Espace d’échange utilisé (Mio)
PercentUsedSwap | Espace d’échange utilisé sous forme de pourcentage de l’espace d’échange total

Cette classe de métriques n’a qu’une seule instance. L’attribut `"condition"` n’a pas de paramètres utiles et doit être omis.

### <a name="builtin-metrics-for-the-network-class"></a>métriques intégrées pour la classe Réseau

La classe de métriques Réseau fournit des informations sur l’activité réseau sur une interface réseau individuelle depuis le démarrage. 

L’extension de diagnostic Linux n’expose pas les métriques de la bande passante. Vous pouvez obtenir ces métriques à partir des métriques de l’hôte.

Compteur | Signification
------- | -------
BytesTransmitted | Nombre total d’octets envoyés depuis le démarrage
BytesReceived | Nombre total d’octets reçus depuis le démarrage
BytesTotal | Nombre total d’octets envoyés ou reçus depuis le démarrage
PacketsTransmitted | Nombre total de paquets envoyés depuis le démarrage
PacketsReceived | Nombre total de paquets reçus depuis le démarrage
TotalRxErrors | Nombre d’erreurs de réception depuis le démarrage
TotalTxErrors | Nombre d’erreurs de transmission depuis le démarrage
TotalCollisions | Nombre de collisions signalées par les ports réseau depuis le démarrage

Bien que la classe Réseau soit instanciée, l’extension de diagnostic Linux ne prend pas en charge la capture des métriques Réseau agrégées pour tous les périphériques réseau. Pour obtenir les métriques pour une interface spécifique, comme eth0, définissez `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-file-system-class"></a>Métriques builtin pour la classe Système de fichiers

La classe de métriques Système de fichiers fournit des informations sur l’utilisation du système de fichiers. Les valeurs absolues et en pourcentage sont indiquées comme elles sont affichées pour un utilisateur ordinaire (non racine).

Compteur | Signification
------- | -------
FreeSpace | Espace disque disponible en octets
UsedSpace | Espace disque utilisé en octets
PercentFreeSpace | Pourcentage d’espace libre
PercentUsedSpace | Pourcentage d’espace utilisé
PercentFreeInodes | Pourcentage de nœuds d’index non utilisés (inodes)
PercentUsedInodes | Pourcentage total d’inodes alloués (utilisés) pour tous les systèmes de fichiers
BytesReadPerSecond | Octets lus par seconde
BytesWrittenPerSecond | Octets écrits par seconde
BytesPerSecond | Octets lus ou écrits par seconde
ReadsPerSecond | Opérations de lecture par seconde
WritesPerSecond | Opérations d’écriture par seconde
TransfersPerSecond | Opérations de lecture ou d’écriture par seconde

Vous pouvez obtenir des valeurs agrégées sur tous les systèmes de fichiers en définissant `"condition": "IsAggregate=True"`. Obtenez des valeurs pour un système de fichiers monté spécifique, comme `"/mnt"`, en définissant `"condition": 'Name="/mnt"'`. 

> [!NOTE]
> Si vous travaillez dans le portail Azure au lieu de JSON, la forme de champ de condition correcte est `Name='/mnt'`.

### <a name="builtin-metrics-for-the-disk-class"></a>métriques intégrées pour la classe Disque

La classe de métriques Disque fournit des informations sur l’utilisation du disque. Ces statistiques s’appliquent à la totalité du lecteur. 

Quand un périphérique a plusieurs systèmes de fichiers, les compteurs pour ce périphérique sont agrégés pour tous les systèmes de fichiers.

Compteur | Signification
------- | -------
ReadsPerSecond | Opérations de lecture par seconde
WritesPerSecond | Opérations d’écriture par seconde
TransfersPerSecond | Nombre total d’opérations par seconde
AverageReadTime | Nombre moyen de secondes par opération de lecture
AverageWriteTime | Nombre moyen de secondes par opération d’écriture
AverageTransferTime | Nombre moyen de secondes par opération
AverageDiskQueueLength | Nombre moyen d’opérations disque en file d’attente
ReadBytesPerSecond | Nombre d’octets lus par seconde
WriteBytesPerSecond | Nombre d’octets écrits par seconde
BytesPerSecond | Nombre d’octets lus ou écrits par seconde

Vous pouvez obtenir des valeurs agrégées sur tous les disques en définissant `"condition": "IsAggregate=True"`. Pour obtenir des informations pour un périphérique spécifique (par exemple `/dev/sdf1`), définissez `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="install-and-configure-lad-30"></a>Installer et configurer l’extension de diagnostic Linux 3.0

### <a name="azure-cli"></a>Azure CLI

Si vos paramètres protégés sont dans le fichier *ProtectedSettings.json* et que vos informations de configuration publique sont *PublicSettings.json*, exécutez la commande suivante.

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

La commande suppose que vous utilisez le mode Azure Resource Manager d’Azure CLI. Pour configurer l’extension de diagnostic Linux pour des machines virtuelles du modèle de déploiement classique, passez au mode « asm » (`azure config mode asm`) et omettez le nom du groupe de ressources dans la commande. 

Pour plus d’informations, consultez la [documentation relative à l’interface de ligne de commande multiplateforme](/cli/azure/authenticate-azure-cli).

### <a name="powershell"></a>PowerShell

Si vos paramètres protégés sont dans la variable `$protectedSettings` et que vos informations de configuration publique sont dans la variable `$publicSettings`, exécutez cette commande :

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="example-lad-30-configuration"></a>Exemple de configuration de l’extension de diagnostic Linux 3.0

À partir des définitions précédentes, cette section fournit un exemple de configuration de l’extension de diagnostic Linux 3.0 et quelques explications. Pour appliquer cet exemple à votre cas, vous devez utiliser le nom de votre compte de stockage, le jeton SAP de votre compte et vos jetons SAP pour Event Hubs.

> [!NOTE]
> Selon que vous utilisez Azure CLI ou PowerShell pour installer l’extension de diagnostic Linux, la méthode pour fournir des paramètres publics et protégés diffère : 
>
> * Si vous utilisez Azure CLI, enregistrez les paramètres suivants dans *ProtectedSettings.json* et *PublicSettings.json* pour utiliser l’exemple de commande précédent. 
> * Si vous utilisez PowerShell, enregistrez les paramètres suivants dans `$protectedSettings` et `$publicSettings` en exécutant `$protectedSettings = '{ ... }'`.

### <a name="protected-settings"></a>Paramètres protégés

Les paramètres protégés configurent :

* Un compte de stockage.
* Un jeton SAP du compte correspondant.
* Plusieurs récepteurs (`JsonBlob` ou `EventHub` avec des jetons SAP).

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>Paramètres publics

Les paramètres publics font que l’extension de diagnostic Linux :

* Charge les métriques percent-processor-time et used-disk-space metrics dans la table `WADMetrics*`.
* Charge les messages de la fonction Syslog `"user"` et du niveau de gravité `"info"` dans la table `LinuxSyslog*`.
* Charge les résultats bruts des requêtes OMI (`PercentProcessorTime` et `PercentIdleTime`) dans la table `LinuxCPU` nommée.
* Charge les lignes ajoutées au fichier `/var/log/myladtestlog` dans la table `MyLadTestLog`.

Dans chaque cas, les données sont également chargées dans :

* Stockage Blob Azure. Le nom du conteneur est défini dans le récepteur `JsonBlob`.
* Le point de terminaison Event Hubs, tel que spécifié dans le récepteur `EventHub`.

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

Le `resourceId` dans la configuration doit correspondre à celui de la machine virtuelle ou du groupe de machines virtuelles identiques.

* Les graphiques et les alertes des métriques de la plateforme Azure connaissent le `resourceId` de la machine virtuelle sur laquelle vous travaillez. Ils s’attendent à trouver les données de votre machine virtuelle en utilisant le `resourceId` comme clé de recherche.
* Si vous utilisez la mise à l’échelle automatique d’Azure, le `resourceId` dans la configuration de la mise à l’échelle automatique doit correspondre au `resourceId` utilisé par l’extension de diagnostic Linux.
* Le `resourceId` est intégré aux noms des blobs JSON écrits par l’extension de diagnostic Linux.

## <a name="view-your-data"></a>Afficher vos données

Utilisez le portail Azure pour afficher les données de performances ou pour définir des alertes :

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Capture d’écran montrant le portail Azure. La métrique « Espace disque utilisé sur » est sélectionnée. Le graphique résultant est affiché.":::

Les données de `performanceCounters` sont toujours stockées dans une table de Stockage Azure. Les API Stockage Azure sont disponibles pour de nombreux langages et de nombreuses plateformes.

Les données envoyées aux récepteurs `JsonBlob` sont stockées dans des blobs dans le compte de stockage nommé dans les [paramètres protégés](#protected-settings). Vous pouvez consommer les données des blobs en utilisant n’importe quelle API de Stockage Blob Azure.

Vous pouvez aussi utiliser ces outils d’interface utilisateur pour accéder aux données dans Stockage Azure :

* Explorateur de serveurs Visual Studio.
* [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)

La capture d’écran suivante d’une session d’Explorateur Stockage Azure montre les tables et les conteneurs Stockage Azure générés à partir d’une extension de diagnostic Linux 3.0 correctement configurée sur une machine virtuelle de test. L’image ne correspond pas exactement à [l’exemple de configuration de l’extension de diagnostic Linux 3.0](#example-lad-30-configuration).

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Capture d’écran montrant Explorateur Stockage Azure.":::


Pour plus d’informations sur la façon de consommer des messages publiés sur un point de terminaison Event Hubs, consultez la [documentation correspondante d’Event Hubs](../../event-hubs/event-hubs-about.md).

## <a name="next-steps"></a>Étapes suivantes

* Dans [Azure Monitor](../../azure-monitor/alerts/alerts-classic-portal.md), créez des alertes pour les métriques que vous collectez.
* Créez [des graphiques de surveillance](../../azure-monitor/data-platform.md) pour vos métriques.
* [Créez un groupe de machines virtuelles identiques](../linux/tutorial-create-vmss.md) en utilisant vos métriques pour contrôler la mise à l’échelle automatique.