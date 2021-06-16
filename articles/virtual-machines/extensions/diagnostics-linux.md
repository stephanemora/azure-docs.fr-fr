---
title: Azure Compute- Extension de diagnostic Linux 4.0
description: Guide pratique pour configurer l’extension de diagnostic Linux Azure 4.0 pour collecter des métriques et journaliser les événements provenant de machines virtuelles Linux qui s’exécutent dans Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/05/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aae7cc0d9289e4121f0cb58b719ff791e4880bb4
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950006"
---
# <a name="use-the-linux-diagnostic-extension-40-to-monitor-metrics-and-logs"></a>Utiliser l’extension de diagnostic Linux 4.0 pour superviser les métriques et les journaux

Ce document décrit les dernières versions de l’extension de diagnostic Linux.

> [!IMPORTANT]
> Pour plus d’informations sur la version 3.x, consultez [Utiliser l’extension de diagnostic Linux 3.0 pour superviser les métriques et les journaux](./diagnostics-linux-v3.md).
> Pour plus d’informations sur la version 2.3 et sur les versions antérieures, consultez [Superviser les données de performances et de diagnostic d’une machine virtuelle Linux](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2).

## <a name="introduction"></a>Introduction

L’extension de diagnostic Linux aide l’utilisateur à superviser l’intégrité d’une machine virtuelle Linux s’exécutant sur Microsoft Azure. Elle possède la collection et les capacités suivantes :

| Source de données | Options de personnalisation | Destinations obligatoires | Destinations facultatives |
| ----------- | --------------------- | -------------------- | --------------------- |
| Mesures     | [Compteur, Agrégation, Échantillonnage, Spécificateurs](#performancecounters) | Stockage de table Azure | EventHub, Stockage Blob Azure (format JSON), Azure Monitor<sup>1</sup> |
| syslog      | [Fonction, niveau de gravité](#syslogevents) | Stockage de table Azure | EventHub, Stockage Blob Azure (format JSON)
| Fichiers       | [Chemin d’accès du journal, Table de destination](#filelogs) | Stockage de table Azure | EventHub, Stockage Blob Azure (format JSON)

<sup>1</sup> Nouveau dans l’extension de diagnostic Linux 4.0

Cette extension fonctionne avec les deux modèles de déploiement Azure (Azure Resource Manager et classique).

## <a name="install-the-extension"></a>Installer l’extension

Vous pouvez activer cette extension pour votre machine virtuelle et votre groupe de machines virtuelles identiques en utilisant les cmdlets Azure PowerShell, des scripts Azure CLI, des modèles Azure Resource Manager (modèles ARM) ou le portail Azure. Pour plus d’informations, consultez [Extensions et fonctionnalités](features-linux.md).

>[!NOTE]
>Certains composants de l’extension de machine virtuelle de diagnostic Linux sont également fournis avec l’[extension de machine virtuelle Log Analytics](./oms-linux.md). En raison de cette architecture, des conflits peuvent survenir si les deux extensions sont instanciées dans le même modèle ARM.
>
>Pour éviter ces conflits au moment de l’installation, utilisez la [directive `dependsOn`](../../azure-resource-manager/templates/resource-dependency.md#dependson) pour installer les extensions de façon séquentielle. Les extensions peuvent être installées dans n’importe quel ordre.

Utilisez ces instructions d’installation et un [exemple de configuration téléchargeable](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) pour configurer l’extension de diagnostic Linux 4.0 pour :

* Capturez et stockez les mêmes métriques que celles fournies par l’extension de diagnostic Linux versions 2.3 et 3.x.
* Envoyez des métriques au récepteur Azure Monitor avec le récepteur habituel vers Stockage Azure. Cette fonctionnalité est nouvelle dans l’extension de diagnostic Linux 4.0.
* Capturez un ensemble pratique de métriques du système de fichiers, comme dans l’extension de diagnostic Linux 3.0.
* Capturez la collection Syslog par défaut activée par l’extension de diagnostic Linux 2.3.
* Activez l’expérience du portail Azure pour les graphiques et les alertes sur des métriques de machine virtuelle.

La configuration téléchargeable n’est qu’un exemple. Modifiez-la pour l’adapter à vos besoins.

### <a name="supported-linux-distributions"></a>Distributions de Linux prises en charge

L’extension de diagnostic Linux prend en charge un grand nombre de distributions et de versions. La liste des distributions et des versions suivante s’applique seulement aux images du fournisseur Linux approuvées par Azure. L’extension ne prend généralement pas en charge les images BYOL et BYOS de tiers, comme les appliances.

Une distribution répertoriant uniquement les versions principales, telles que Debian 7, est également prise en charge pour toutes les versions mineures. Si une version mineure spécifique est spécifiée, seule cette version est prise en charge. Si un signe plus (+) est présent, les versions mineures égales ou ultérieures à la version spécifiée sont prises en charge.

Distributions et versions prises en charge :

- Ubuntu 18.04, 16.04, 14.04
- CentOS 7, 6.5+
- Oracle Linux 7, 6.4
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- Red Hat Enterprise Linux (RHEL) 7, 6.7+

### <a name="prerequisites"></a>Prérequis

* **Agent Linux Azure version 2.2.0 ou ultérieure**. La plupart des images de la galerie Linux de machines virtuelles Azure incluent la version 2.2.7 ou ultérieure. Exécutez `/usr/sbin/waagent -version` pour vérifier la version installée sur la machine virtuelle. Si la machine virtuelle exécute une version antérieure de l’agent invité, [mettez à jour l’agent Linux](./update-linux-agent.md).
* **Azure CLI**. [Configurez l’environnement Azure CLI](/cli/azure/install-azure-cli) sur votre machine.
* **La commande `wget`** . Si vous ne l’avez pas encore, exécutez `sudo apt-get install wget`.
* **Un abonnement Azure et un compte de stockage universel** pour stocker les données.  Les comptes de stockage universels prennent en charge le stockage Table, qui est nécessaire.  Un compte de stockage d’objets blob ne va pas fonctionner.
* **Python 2**.

### <a name="python-requirement"></a>Exigence relative à Python

L’extension de diagnostic Linux nécessite Python 2. Si votre machine virtuelle utilise une distribution qui n’inclut pas Python 2 par défaut, installez-le.

Les exemples de commandes suivants installent Python 2 sur différentes distributions :

- Red Hat, CentOS, Oracle : `yum install -y python2`
- Ubuntu, Debian : `apt-get install -y python2`
- SUSE : `zypper install -y python2`

Le fichier exécutable `python2` doit avoir comme alias *python*. Voici une façon d’y parvenir :

1. Exécutez la commande suivante pour supprimer tous les alias existants.

    ```
    sudo update-alternatives --remove-all python
    ```

2. Pour créer l’alias, exécutez la commande suivante.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="installation"></a>Installation

Vous pouvez installer et configurer l’extension de diagnostic Linux 4.0 dans Azure CLI ou dans PowerShell.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Si vos paramètres protégés sont dans le fichier *ProtectedSettings.json* et que vos informations de configuration publique sont dans *PublicSettings.json*, exécutez cette commande :

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

La commande suppose que vous utilisez le mode Azure Resource Manager d’Azure CLI. Pour configurer l’extension de diagnostic Linux pour des machines virtuelles du modèle de déploiement classique, passez au mode Management des services (`azure config mode asm`) et omettez le nom du groupe de ressources dans la commande.

Pour plus d’informations, consultez la [documentation relative à l’interface de ligne de commande multiplateforme](/cli/azure/authenticate-azure-cli).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Si vos paramètres protégés sont dans la variable `$protectedSettings` et que vos informations de configuration publique sont dans la variable `$publicSettings`, exécutez cette commande :

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0
```

---

### <a name="sample-installation"></a>Exemple d’installation

> [!NOTE]
> Pour les exemples suivants, renseignez les valeurs appropriées pour les variables de la première section avant d’exécuter le code.

Dans ces exemples, l’exemple de configuration collecte un ensemble de données standard et les envoie au Stockage Table. L’URL de l’exemple de configuration et son contenu peut changer.

Dans la plupart des cas, vous devez télécharger une copie du fichier JSON des paramètres du portail et la personnaliser en fonction de vos besoins. Utilisez ensuite des modèles ou votre propre automatisation pour utiliser une version personnalisée du fichier de configuration au lieu de le télécharger à chaque fois depuis l’URL.

> [!NOTE]
> Quand vous activez le nouveau récepteur Azure Monitor, les machines virtuelles doivent avoir une identité affectée par le système activée pour générer des jetons d’authentification Managed Service Identity (MSI). Vous pouvez ajouter ces paramètres pendant ou après la création de la machine virtuelle.
>
> Pour obtenir des instructions pour le portail Azure, Azure CLI, PowerShell et Azure Resource Manager, consultez [Configurer des identités managées](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

#### <a name="installation-sample---azure-cli"></a>Exemple d’installation – Azure CLI

```azurecli
# Set your Azure VM diagnostic variables.
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Enable system-assigned identity on the existing VM.
az vm identity assign -g $my_resource_group -n $my_linux_vm

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
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

#### <a name="installation-sample---powershell"></a>Exemple d’installation – PowerShell

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Enable system-assigned identity on an existing VM
Update-AzVM -ResourceGroupName $VMresourceGroup -VM $vm -IdentityType SystemAssigned

# Get the public settings template from GitHub and update the templated values for the storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0
```

---

#### <a name="installation-sample-for-virtual-machine-scale-sets---azure-cli"></a>Exemple d’installation pour les groupes de machines virtuelles identiques – Azure CLI

```azurecli
# Set your Azure virtual machine scale set diagnostic variables.
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Enable system-assigned identity on the existing virtual machine scale set.
az vmss identity assign -g $my_resource_group -n $my_linux_vmss

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
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

### <a name="update-the-extension-settings"></a>Mettre à jour les paramètres de l’extension

Une fois que vous avez modifié vos paramètres protégés ou publics, déployez-les sur la machine virtuelle en exécutant la même commande. Si des paramètres ont été modifiés, les mises à jour sont envoyées à l’extension. L’extension de diagnostic Linux recharge la configuration et redémarre automatiquement.

### <a name="migrate-from-previous-versions-of-the-extension"></a>Migrer depuis des versions antérieures de l’extension

La version la plus récente de l’extension est la version 4.0, *qui est actuellement disponible en préversion publique*. Les versions 3.x antérieures sont toujours prises en charge. Les versions 2.x ont cependant été dépréciées le 31 juillet 2018.

> [!IMPORTANT]
> Pour migrer de 3.x vers la version la plus récente de l’extension, désinstallez l’ancienne extension. Installez ensuite la version 4, qui comprend la configuration mise à jour pour l’identité affectée par le système et les récepteurs pour envoyer des métriques au récepteur Azure Monitor.

Quand vous installez la nouvelle extension, activez les mises à niveau automatiques des versions mineures :
* Sur les machines virtuelles du modèle de déploiement Azure Resource Manager, incluez `"autoUpgradeMinorVersion": true` dans le modèle de déploiement des machines virtuelles.
* Sur les machines virtuelles du modèle de déploiement classique, spécifiez la version `4.*` si vous installez l’extension via Azure CLI ou PowerShell.

Vous pouvez utiliser le même compte de stockage que celui utilisé pour l’extension de diagnostic Linux 3.x.

## <a name="protected-settings"></a>Paramètres protégés

Cet ensemble d’informations de configuration contient des informations sensibles qui doivent être protégées d’une exposition publique. Il contient par exemple les informations d’identification du stockage. Ces paramètres sont transmis à l’extension et stockés par celle-ci sous une forme chiffrée.

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
storageAccountName | Nom du compte de stockage où les données sont écrites par l’extension.
storageAccountEndPoint | (Facultatif) Point de terminaison identifiant le cloud où se trouve le compte de stockage. Si ce paramètre est absent, par défaut, l’extension de diagnostic Linux utilise le cloud public Azure, `https://core.windows.net`. Pour utiliser un compte de stockage dans Azure Allemagne, Azure Government ou Azure Chine 21Vianet, définissez cette valeur comme nécessaire.
storageAccountSasToken | Un [jeton SAS de compte](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) pour les services Blob et Table (`ss='bt'`). Ce jeton s’applique aux conteneurs et aux objets (`srt='co'`). Il accorde les autorisations d’ajouter, de créer, de lister, de mettre à jour et d’écrire (`sp='acluw'`). *N’incluez pas* le point d’interrogation ( ?) du début.
mdsdHttpProxy | (Facultatif) Informations du proxy HTTP nécessaires pour permettre à l’extension de se connecter au compte de stockage et au point de terminaison spécifiés.
sinksConfig | (Facultatif) Détails des destinations alternatives auxquelles les métriques et les événements peuvent être délivrés. Les sections suivantes fournissent des détails sur chaque récepteur de données pris en charge par l’extension.

Pour obtenir un jeton SAS dans un modèle ARM, utilisez la fonction `listAccountSas`. Pour obtenir un exemple de modèle, consultez [Exemple de fonction de liste](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Vous pouvez construire le jeton SAP nécessaire par le biais du portail Azure :

1. Sélectionnez le compte de stockage universel sur lequel vous voulez que l’extension écrive.
1. Dans le menu de gauche, sous **Paramètres**, sélectionnez **Signature d’accès partagé**.
1. Effectuez les sélections comme décrit précédemment.
1. Sélectionnez **Générer la SAP**.

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Capture d’écran montrant la page Signature d’accès partagé avec le bouton Générer la SAP.":::

Copiez la signature d’accès partagé générée dans le champ `storageAccountSasToken`. Supprimez le point d’interrogation (?) figurant au début.

### <a name="sinksconfig"></a>sinksConfig

> [!NOTE]
> Les paramètres publics et protégés ont une section `sinksConfig` facultative. La section `sinksConfig` dans les paramètres *protégés* contient uniquement les configurations des récepteurs `EventHub` et `JsonBlob`, en raison de l’inclusion de secrets comme `sasURL`. Les configurations du récepteur `AzMonSink` **ne peuvent pas** être incluses dans vos paramètres protégés.

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

La section facultative `sinksConfig` définit des destinations supplémentaires auxquelles l’extension enverra les informations collectées. Le tableau `"sink"` contient un objet pour chaque récepteur de données supplémentaire. L’attribut `"type"` détermine les autres attributs de l’objet.

Élément | Valeur
------- | -----
name | Chaîne utilisée pour référencer ce récepteur ailleurs dans la configuration de l’extension.
type | Type du récepteur défini. Détermine les autres valeurs (le cas échéant) dans les instances de ce type.

L’extension de diagnostic Linux 4.0 prend en charge deux types de récepteur protégé : `EventHub` et `JsonBlob`.

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

L’entrée `"sasURL"` contient l’URL complète, y compris le jeton SAP, de l’Event Hub sur lequel les données doivent être publiées. L’extension de diagnostic Linux nécessite une signature SAS pour nommer une stratégie qui active la revendication d’envoi. Voici un exemple :

* Créez un espace de noms Event Hubs appelé `contosohub`.
* Créez un hub d’événements dans l’espace de noms appelé `syslogmsgs`.
* Créez une stratégie d’accès partagé sur le hub d’événements nommé `writer` qui active la revendication d’envoi.

Si vous avez créé une signature d’accès partagé valide jusqu’à minuit UTC le 1er janvier 2018, la valeur de `sasURL` peut être similaire à l’exemple suivant.

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Pour plus d’informations sur la génération et l’extraction d’informations sur les jetons SAS pour Event Hubs, consultez [Générer un jeton SAS](/rest/api/eventhub/generate-sas-token#powershell).

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

Les blobs sont stockés dans un conteneur du même nom que le récepteur. Les règles du Stockage Azure pour les noms de conteneur d’objets blob s’appliquent aux noms des récepteurs `JsonBlob`. Autrement dit, les noms doivent comporter entre 3 et 63 caractères ASCII alphanumériques en minuscules ou des tirets.

## <a name="public-settings"></a>Paramètres publics

Cette structure de paramètres publics contient différents blocs de paramètres qui contrôlent les informations collectées par l’extension. Chaque paramètre, excepté `ladCfg`, est facultatif. Si vous spécifiez la collection de métriques ou syslog dans `ladCfg`, vous devez également spécifier `StorageAccount`. Vous devez spécifier l’élément `sinksConfig` pour activer le récepteur Azure Monitor pour les métriques de l’extension de diagnostic Linux 4.0.

```json
{
    "ladCfg":  { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "sinksConfig": { ... },
    "mdsdHttpProxy" : ""
}
```

Élément | Valeur
------- | -----
StorageAccount | Nom du compte de stockage où les données sont écrites par l’extension. Il doit s’agir du nom spécifié dans les [paramètres protégés](#protected-settings).
mdsdHttpProxy | (Facultatif) Le proxy spécifié dans les [paramètres protégés](#protected-settings). Si la valeur privée est définie, elle remplace la valeur publique. Placez les paramètres de proxy qui contiennent un secret, comme un mot de passe, dans les [paramètres protégés](#protected-settings).

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

La structure `ladCfg` contrôle la collecte des métriques et des journaux pour les délivrer au service des métriques Azure Monitor et à d’autres récepteurs. Spécifiez `performanceCounters` ou `syslogEvents`, ou les deux. Spécifiez aussi la structure `metrics`.

Si vous ne voulez pas activer Syslog ou la collecte de métriques, spécifiez une structure vide pour l’élément `ladCfg`, comme ceci :

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {}
}
```

Élément | Valeur
------- | -----
eventVolume | (facultatif) Contrôle le nombre de partitions créées dans la table de stockage. La valeur doit être `"Large"`, `"Medium"` ou `"Small"`. Si la valeur n’est pas spécifiée, la valeur par défaut est `"Medium"`.
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
resourceId | L’ID de ressource Azure Resource Manager de la machine virtuelle ou du groupe de machines virtuelles identiques auquel la machine virtuelle appartient. Spécifiez aussi ce paramètre si la configuration utilise un récepteur `JsonBlob`.
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
sinks | (Facultatif) Liste, séparée par des virgules, des noms des récepteurs auxquels l’extension de diagnostic Linux envoie les résultats des métriques agrégées. Toutes les métriques agrégées sont publiées sur chaque récepteur répertorié. Exemple : `"MyEventHubSink, MyJsonSink, MyAzMonSink"`. Pour plus d’informations, consultez [`sinksConfig` (paramètres protégés)](#sinksconfig) et [`sinksConfig` (paramètres publics)](#sinksconfig-1).
type | Identifie le fournisseur réel de la mesure.
class | Avec `"counter"`, identifie la métrique spécifique dans l’espace de noms du fournisseur.
counter | Avec `"class"`, identifie la métrique spécifique dans l’espace de noms du fournisseur. Consultez la liste des compteurs disponibles [ci-dessous](#metrics-supported-by-the-builtin-provider).
counterSpecifier | Identifie la métrique spécifique dans l’espace de noms Azure Monitor Metrics.
condition | (Facultatif) Sélectionne une instance de l’objet auquel la métrique s’applique, ou sélectionne l’agrégation sur toutes les instances de cet objet.
sampleRate | Intervalle ISO 8601 qui définit la fréquence à laquelle des échantillons bruts sont collectés pour cette métrique. Si la valeur n’est pas définie, l’intervalle de collecte est défini par la valeur de [`sampleRateInSeconds`](#ladcfg). L’échantillonnage le plus court pris en charge est de 15 secondes (PT15S).
unité | Définit l’unité pour la métrique. Doit être une des chaînes suivantes : `"Count"`, `"Bytes"`, `"Seconds"`, `"Percent"`, `"CountPerSecond"`, `"BytesPerSecond"`, `"Millisecond"`. Les consommateurs des données collectées attendent des valeurs de données collectées correspondant à cette unité. L’extension de diagnostic Linux ignore ce champ.
displayName | Étiquette à attacher aux données dans Azure Monitor Metrics lors de l’affichage dans l’espace de noms de métriques `Guest (classic)`. Cette étiquette est dans la langue spécifiée par les paramètres régionaux associés. L’extension de diagnostic Linux ignore ce champ.<br/>**Remarque** : Si vous affichez la même métrique dans l’espace de noms de métriques `azure.vm.linux.guestmetrics` (disponible si `AzMonSink` est configuré), le nom d’affichage dépend entièrement du compteur. Consultez les [tableaux ci-dessous](#metrics-supported-by-the-builtin-provider) pour rechercher la correspondance entre les compteurs et les noms.

`counterSpecifier` est un identificateur arbitraire. Les consommateurs de métriques, comme les fonctionnalités de graphiques et d’alertes du portail Azure, utilisent `counterSpecifier` comme « clé » qui identifie une métrique ou l’instance d’une métrique.

Pour les métriques `builtin`, nous recommandons des valeurs de `counterSpecifier` commençant par `/builtin/`. Si vous collectez une instance spécifique d’une métrique, attachez l’identificateur de l’instance à la valeur de `counterSpecifier`. Voici quelques exemples :

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
facilityName | Nom de la fonction Syslog, comme `"LOG\_USER"` ou `"LOG\_LOCAL0"`. Pour plus d’informations, consultez la section « facility » de la [page man syslog](http://man7.org/linux/man-pages/man3/syslog.3.html).
minSeverity | Niveau de gravité Syslog, comme `"LOG\_ERR"` ou `"LOG\_INFO"`. Pour plus d’informations, consultez la section « level » de la [page man syslog](http://man7.org/linux/man-pages/man3/syslog.3.html). L’extension capture les événements envoyés à la fonction à un niveau supérieur ou égal au niveau spécifié.

Quand vous spécifiez `syslogEvents`, l’extension de diagnostic Linux écrit toujours les données dans une table de Stockage Azure. Les mêmes données peuvent être écrites dans des blobs JSON ou des Event Hubs, ou les deux. Vous ne pouvez cependant pas désactiver le stockage des données dans une table.

Le comportement de partitionnement pour cette table est identique à celui décrit pour `performanceCounters`. Le nom de la table est la concaténation des chaînes suivantes :

* `LinuxSyslog`
* Une date, sous la forme « AAAAMMJJ », qui change tous les 10 jours

Exemples : `LinuxSyslog20170410` et `LinuxSyslog20170609`.

### <a name="sinksconfig"></a>sinksConfig

La section `sinksConfig` publique facultative permet d’envoyer des métriques au récepteur Azure Monitor en plus du compte de stockage et du panneau Métriques d’invité par défaut.

> [!NOTE]
> Les paramètres publics et protégés ont une section `sinksConfig` facultative. La section `sinksConfig` dans les paramètres *publics* contient uniquement la configuration du récepteur `AzMonSink`. Les configurations des récepteurs `EventHub` et `JsonBlob` **ne peuvent pas** être incluses dans vos paramètres publics.

> [!NOTE]
> La section `sinksConfig` nécessite l’activation de l’identité affectée par le système sur les machines virtuelles ou sur le groupe de machines virtuelles identiques.
> Vous pouvez activer l’identité affectée par le système via le portail Azure, l’interface CLI, PowerShell ou Azure Resource Manager. Suivez les [instructions détaillées](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) ou consultez les exemples d’installations précédents dans cet article.

```json
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
```

### <a name="filelogs"></a>fileLogs

La section `fileLogs` contrôle la capture des fichiers journaux. L’extension de diagnostic Linux capture les nouvelles lignes de texte à mesure qu’elles sont écrites dans le fichier. Elle les écrit dans les lignes de la table et/ou dans les récepteurs spécifiés, comme `JsonBlob` et `EventHub`.

> [!NOTE]
> Les `fileLogs` sont capturés par un sous-composant de l’extension de diagnostic Linux appelé `omsagent`. Pour collecter les `fileLogs`, vérifiez que l’utilisateur `omsagent` dispose des autorisations de lecture sur les fichiers que vous spécifiez. Il doit également disposer d’autorisations d’exécution sur tous les répertoires dans le chemin vers ce fichier. Une fois l’extension de diagnostic Linux installée, vous pouvez vérifier les autorisations en exécutant `sudo su omsagent -c 'cat /path/to/file'`.

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
fichier | Nom du chemin complet du fichier journal à observer et à capturer. Le nom du chemin correspond à un seul fichier. Il ne peut pas nommer un répertoire ni contenir des caractères génériques. Le compte d’utilisateur `omsagent` doit avoir un accès en lecture sur le chemin du fichier.
table | (Facultatif) Table de Stockage Azure dans laquelle les nouvelles lignes de la « fin » du fichier sont écrites. La table doit se trouver dans le compte de stockage désigné, comme spécifié dans la configuration protégée.
sinks | (Facultatif) Une liste séparée par des virgules des noms des récepteurs supplémentaires auxquels les lignes des journaux sont envoyées.

Vous devez spécifier `"table"`, `"sinks"` ou les deux.

## <a name="metrics-supported-by-the-builtin-provider"></a>Métriques prises en charge par le fournisseur intégré

> [!NOTE]
> Les métriques par défaut prises en charge par l’extension de diagnostic Linux sont agrégées pour l’ensemble des systèmes de fichiers, des disques ou des noms. Pour les métriques non agrégées, reportez-vous à la prise en charge des métriques plus récentes du récepteur Azure Monitor.

> [!NOTE]
> Les noms d’affichage de chaque métrique varient en fonction de l’espace de noms de métriques auquel elle appartient :
> * `Guest (classic)` (renseigné à partir de votre compte de stockage) : le `displayName` spécifié dans la section `performanceCounters`, ou le nom d’affichage par défaut tel qu’il apparaît dans le portail Azure (Machine virtuelle > Paramètres de diagnostic > Métriques > Personnalisé).
> * `azure.vm.linux.guestmetrics` (renseigné à partir de `AzMonSink` si configuré) : le « nom d’affichage `azure.vm.linux.guestmetrics` » spécifié dans les tableaux ci-dessous.
>
> En raison de détails d’implémentation, les valeurs métriques entre les versions `Guest (classic)` et `azure.vm.linux.guestmetrics` seront différentes. Alors que certaines agrégations étaient appliquées aux métriques classiques dans l’agent, les nouvelles métriques sont des compteurs non agrégés, ce qui donne aux clients la flexibilité nécessaire pour les agréger au moment de l’affichage/alerte.

Le fournisseur de métriques `builtin` est une source de métriques parmi les plus intéressantes pour un large éventail d’utilisateurs. Ces métriques se répartissent en cinq classes principales :

* Processeur
* Mémoire
* Réseau
* Système de fichiers
* Disque

### <a name="builtin-metrics-for-the-processor-class"></a>métriques intégrées pour la classe Processeur

La classe de métriques Processeur fournit des informations sur l’utilisation du processeur dans la machine virtuelle. Quand des pourcentages sont agrégés, le résultat est la moyenne pour tous les UC.

Dans une machine virtuelle à 2 processeurs virtuels, si un processeur virtuel est occupé à 100 % et que l’autre est inactif à 100 %, le `PercentIdleTime` signalé est de 50. Si chaque processeur virtuel est occupé à 50 % pendant la même période, le résultat signalé est également de 50. Dans une machine virtuelle à 4 processeurs virtuels, si un processeur virtuel est occupé à 100 % et que les autres sont inactifs, le `PercentIdleTime` signalé est de 75.

Compteur | Nom d’affichage `azure.vm.linux.guestmetrics` | Signification
--------- | ---------------------------------- | -------
`PercentIdleTime` | `cpu/usage_idle` | Pourcentage de temps de la fenêtre d’agrégation pendant lequel les UC ont exécuté la boucle d’inactivité du noyau
`PercentProcessorTime` |  `cpu/usage_active` | Pourcentage de temps passé à exécuter un thread actif
`PercentIOWaitTime` |  `cpu/usage_iowait` | Pourcentage de temps passé à attendre la fin d’opérations d’E/S
`PercentInterruptTime` |  `cpu/usage_irq` | Pourcentage de temps passé à exécuter des interruptions matérielles ou logicielles et des appels DPC (appels de procédure différés)
`PercentUserTime` |  `cpu/usage_user` | Relativement au temps d’activité de la fenêtre d’agrégation, pourcentage de temps passé en mode utilisateur à une priorité normale
`PercentNiceTime` |  `cpu/usage_nice` | Pourcentage de temps passé à une priorité abaissée (commande nice), relativement au temps d’activité
`PercentPrivilegedTime` |  `cpu/usage_system` | Pourcentage de temps passé en mode privilégié (noyau), relativement au temps d’activité

La somme des 4 premiers compteurs doit être de 100 %. La somme des 3 premiers compteurs est également de 100 %. Ces trois compteurs subdivisent la somme de `PercentProcessorTime`, `PercentIOWaitTime` et `PercentInterruptTime`.

### <a name="builtin-metrics-for-the-memory-class"></a>métriques intégrées pour la classe Mémoire

La classe de métriques Mémoire fournit des informations sur l’utilisation, la pagination et les échanges de la mémoire.

Compteur | Nom d’affichage `azure.vm.linux.guestmetrics` | Signification
--------- | ---------------------------------- | -------
`AvailableMemory` | `mem/available` | Mémoire physique disponible en Mio
`PercentAvailableMemory` | `mem/available_percent` | Mémoire physique disponible sous forme de pourcentage de la mémoire totale
`UsedMemory` | `mem/used` | Mémoire physique utilisée (Mio)
`PercentUsedMemory` | `mem/used_percent` | Mémoire physique utilisée sous forme de pourcentage de la mémoire totale
`PagesPerSec` | `kernel_vmstat/total_pages` | Pagination totale (lecture/écriture)
`PagesReadPerSec` | `kernel_vmstat/pgpgin` | Pages lues à partir du magasin de stockage, comme le fichier d’échange, les fichiers programme et les fichiers mappés
`PagesWrittenPerSec` | `kernel_vmstat/pgpgout` | Pages écrites dans le magasin de stockage, comme le fichier d’échange et les fichiers mappés
`AvailableSwap` | `swap/free` | Espace d’échange non utilisé (Mio)
`PercentAvailableSwap` | `swap/free_percent` | Espace d’échange non utilisé sous forme de pourcentage de l’espace d’échange total
`UsedSwap` | `swap/used` | Espace d’échange utilisé (Mio)
`PercentUsedSwap` | `swap/used_percent` | Espace d’échange utilisé sous forme de pourcentage de l’espace d’échange total

Cette classe de métriques n’a qu’une seule instance. L’attribut `"condition"` n’a pas de paramètres utiles et doit être omis.

### <a name="builtin-metrics-for-the-network-class"></a>métriques intégrées pour la classe Réseau

La classe de métriques Réseau fournit des informations sur l’activité réseau sur une interface réseau individuelle depuis le démarrage.

L’extension de diagnostic Linux n’expose pas les métriques de la bande passante. Vous pouvez obtenir ces métriques à partir des métriques de l’hôte.

Compteur | Nom d’affichage `azure.vm.linux.guestmetrics` | Signification
--------- | ---------------------------------- | -------
`BytesTransmitted` | `net/bytes_sent` | Nombre total d’octets envoyés depuis le démarrage
`BytesReceived` | `net/bytes_recv` | Nombre total d’octets reçus depuis le démarrage
`BytesTotal` | `net/bytes_total` | Nombre total d’octets envoyés ou reçus depuis le démarrage
`PacketsTransmitted` | `net/packets_sent` | Nombre total de paquets envoyés depuis le démarrage
`PacketsReceived` | `net/packets_recv` | Nombre total de paquets reçus depuis le démarrage
`TotalRxErrors` | `net/err_in` | Nombre d’erreurs de réception depuis le démarrage
`TotalTxErrors` | `net/err_out` | Nombre d’erreurs de transmission depuis le démarrage
`TotalCollisions` | `net/drop_total` | Nombre de collisions signalées par les ports réseau depuis le démarrage

### <a name="builtin-metrics-for-the-file-system-class"></a>Métriques intégrées pour la classe Système de fichiers

La classe de métriques Système de fichiers fournit des informations sur l’utilisation du système de fichiers. Les valeurs absolues et en pourcentage sont indiquées comme elles sont affichées pour un utilisateur ordinaire (non racine).

Compteur | Nom d’affichage `azure.vm.linux.guestmetrics` | Signification
--------- | ---------------------------------- | -------
`FreeSpace` | `disk/free` | Espace disque disponible en octets
`UsedSpace` | `disk/used` | Espace disque utilisé en octets
`PercentFreeSpace` | `disk/free_percent` | Pourcentage d’espace libre
`PercentUsedSpace` | `disk/used_percent` | Pourcentage d’espace utilisé
`PercentFreeInodes` | `disk/inodes_free_percent` | Pourcentage de nœuds d’index non utilisés (inodes)
`PercentUsedInodes` | `disk/inodes_used_percent` | Pourcentage total d’inodes alloués (utilisés) pour tous les systèmes de fichiers
`BytesReadPerSecond` | `diskio/read_bytes_filesystem` | Octets lus par seconde
`BytesWrittenPerSecond` | `diskio/write_bytes_filesystem` | Octets écrits par seconde
`BytesPerSecond` | `diskio/total_bytes_filesystem` | Octets lus ou écrits par seconde
`ReadsPerSecond` | `diskio/reads_filesystem` | Opérations de lecture par seconde
`WritesPerSecond` | `diskio/writes_filesystem` | Opérations d’écriture par seconde
`TransfersPerSecond` | `diskio/total_transfers_filesystem` | Opérations de lecture ou d’écriture par seconde

### <a name="builtin-metrics-for-the-disk-class"></a>métriques intégrées pour la classe Disque

La classe de métriques Disque fournit des informations sur l’utilisation du disque. Ces statistiques s’appliquent à la totalité du lecteur.

Quand un périphérique a plusieurs systèmes de fichiers, les compteurs pour ce périphérique sont agrégés pour tous les systèmes de fichiers.

Compteur | Nom d’affichage `azure.vm.linux.guestmetrics` | Signification
--------- | ---------------------------------- | -------
`ReadsPerSecond` | `diskio/reads` | Opérations de lecture par seconde
`WritesPerSecond` | `diskio/writes` | Opérations d’écriture par seconde
`TransfersPerSecond` | `diskio/total_transfers` | Nombre total d’opérations par seconde
`AverageReadTime` | `diskio/read_time` | Nombre moyen de secondes par opération de lecture
`AverageWriteTime` | `diskio/write_time` | Nombre moyen de secondes par opération d’écriture
`AverageTransferTime` | `diskio/io_time` | Nombre moyen de secondes par opération
`AverageDiskQueueLength` | `diskio/iops_in_progress` | Nombre moyen d’opérations disque en file d’attente
`ReadBytesPerSecond` | `diskio/read_bytes` | Nombre d’octets lus par seconde
`WriteBytesPerSecond` | `diskio/write_bytes` | Nombre d’octets écrits par seconde
`BytesPerSecond` | `diskio/total_bytes` | Nombre d’octets lus ou écrits par seconde

## <a name="example-lad-40-configuration"></a>Exemple de configuration de l’extension de diagnostic Linux 4.0

À partir des définitions précédentes, cette section fournit un exemple de configuration de l’extension de diagnostic Linux 4.0 et quelques explications. Pour appliquer cet exemple à votre cas, vous devez utiliser le nom de votre compte de stockage, le jeton SAS de votre compte et vos jetons SAS pour Event Hubs.

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

* Charge les métriques de pourcentage de temps processeur et d’espace disque utilisé dans la table `WADMetrics*`.
* Charge les messages depuis la fonction Syslog `"user"` et le niveau de gravité `"info"` dans la table `LinuxSyslog*`.
* Charge les lignes ajoutées au fichier `/var/log/myladtestlog` dans la table `MyLadTestLog`.

Dans chaque cas, les données sont également chargées dans :

* Stockage Blob Azure. Le nom du conteneur est défini dans le récepteur `JsonBlob`.
* Un point de terminaison Event Hubs, tel que spécifié dans le récepteur `EventHub`.

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
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
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

## <a name="view-your-data"></a>Affichage de vos données

Utilisez le portail Azure pour afficher les données de performances ou pour définir des alertes :

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Capture d’écran montrant le portail Azure. L’espace disque utilisé sur la métrique est sélectionné. Le graphique résultant est montré.":::

Les données de `performanceCounters` sont toujours stockées dans une table de Stockage Azure. Les API Stockage Azure sont disponibles pour de nombreux langages et de nombreuses plateformes.

Les données envoyées aux récepteurs `JsonBlob` sont stockées dans des objets blob dans le compte de stockage nommé dans les [paramètres protégés](#protected-settings). Vous pouvez consommer les données d’objet blob dans n’importe quelle API de Stockage Blob Azure.

Vous pouvez aussi utiliser ces outils d’interface utilisateur pour accéder aux données de Stockage Azure :

* Explorateur de serveurs Visual Studio.
* [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)

La capture d’écran suivante d’une session de l’Explorateur Stockage Azure montre les tables et les conteneurs Stockage Azure générés à partir d’une extension de diagnostic Linux 4.0 correctement configurée sur une machine virtuelle de test. L’image ne correspond pas exactement à [l’exemple de configuration de l’extension de diagnostic Linux 4.0](#example-lad-40-configuration).

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Capture d’écran montrant l’Explorateur Stockage Azure.":::

Pour plus d’informations sur la façon de consommer des messages publiés sur un point de terminaison Event Hubs, consultez la [documentation correspondante d’Event Hubs](../../event-hubs/event-hubs-about.md).

## <a name="next-steps"></a>Étapes suivantes

* Dans [Azure Monitor](../../azure-monitor/alerts/alerts-classic-portal.md), créez des alertes pour les mesures que vous collectez.
* [Créez des graphiques de supervision](../../azure-monitor/data-platform.md) pour vos métriques.
* [Créez un groupe de machines virtuelles identiques](../linux/tutorial-create-vmss.md) en utilisant vos métriques pour contrôler la mise à l’échelle automatique.