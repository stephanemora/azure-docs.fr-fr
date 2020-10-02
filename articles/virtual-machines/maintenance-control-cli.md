---
title: Contrôle de maintenance des machines virtuelles Azure avec l’interface CLI
description: Apprenez à contrôler le moment où la maintenance est appliquée à vos machines virtuelles Azure à l’aide du contrôle de maintenance et de l’interface CLI.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/20/2020
ms.author: cynthn
ms.openlocfilehash: 67e33732574d2a6c173675d5adf0a7d1c2050688
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90528174"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>Contrôler les mises à jour avec le contrôle de maintenance et Azure CLI

Le contrôle de maintenance vous permet de décider du moment où appliquer les mises à jour sur vos machines virtuelles isolées et hôtes dédiés Azure. Cette rubrique traite les options de l’interface Azure CLI pour le contrôle de maintenance. Pour plus d’informations sur les avantages de l’utilisation du contrôle de maintenance, ses limitations et d’autres options de gestion, consultez [Gestion des mises à jour de plateformes avec le contrôle de maintenance](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Créer une configuration de maintenance

Utilisez `az maintenance configuration create` pour créer une configuration de maintenance. Cet exemple crée une configuration de maintenance nommée *myConfig*, étendue à l’hôte. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location eastus
```

Copiez l’ID de configuration à partir de la sortie pour l’utiliser ultérieurement.

L’utilisation de `--maintenanceScope host` garantit l’utilisation de la configuration de maintenance pour contrôler les mises à jour de l’hôte.

Si vous essayez de créer une configuration portant le même nom mais dans un autre emplacement, vous obtenez une erreur. Les noms de configuration doivent être uniques dans votre groupe de ressources.

Vous pouvez rechercher par requête les configurations de maintenance disponibles à l’aide de `az maintenance configuration list`.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>Affecter la configuration

Utilisez `az maintenance assignment create` pour affecter la configuration à votre machine virtuelle isolée ou à votre hôte dédié Azure.

### <a name="isolated-vm"></a>Machine virtuelle isolée

Appliquez la configuration à une machine virtuelle à l’aide de l’ID de la configuration. Spécifiez `--resource-type virtualMachines` et fournissez le nom de la machine virtuelle pour `--resource-name`, le groupe de ressources de la machine virtuelle pour `--resource-group` et l’emplacement de la machine virtuelle pour `--location`. 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Hôte dédié

Pour appliquer une configuration à un hôte dédié, vous devez inclure `--resource-type hosts`, `--resource-parent-name` avec le nom du groupe hôte et `--resource-parent-type hostGroups`. 

Le paramètre `--resource-id` est l’ID de l’hôte. Vous pouvez utiliser [az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view) pour obtenir l’ID de votre hôte dédié.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Vérifier la configuration

Vous pouvez vérifier que la configuration a été appliquée correctement ou vérifier quelle configuration est actuellement appliquée à l’aide de `az maintenance assignment list`.

### <a name="isolated-vm"></a>Machine virtuelle isolée

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Hôte dédié 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Rechercher les mises à jour en attente

Utilisez `az maintenance update list` pour voir s’il existe des mises à jour en attente. Mettez à jour --subscription comme l’ID de l’abonnement contenant la machine virtuelle.

S’il n’y a aucune mise à jour, la commande retourne un message d’erreur qui contient le texte suivant : `Resource not found...StatusCode: 404`.

S’il existe des mises à jour, une seule est retournée, même si plusieurs mises à jour sont en attente. Les données de cette mise à jour seront retournées dans un objet :

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>Machine virtuelle isolée

Recherchez les mises à jour en attente pour une machine virtuelle isolée. Dans cet exemple, la sortie est mise en forme en tant que tableau pour faciliter la lecture.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Hôte dédié

Pour rechercher les mises à jour en attente pour un hôte dédié. Dans cet exemple, la sortie est mise en forme en tant que tableau pour faciliter la lecture. Remplacez les valeurs des ressources par les vôtres.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Appliquer des mises à jour

Utilisez `az maintenance apply update` pour appliquer les mises à jour en attente. En cas de réussite, cette commande renverra un fichier JSON contenant les détails de la mise à jour.

### <a name="isolated-vm"></a>Machine virtuelle isolée

Créez une demande d’application des mises à jour à une machine virtuelle isolée.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Hôte dédié

Appliquez les mises à jour à un hôte dédié.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Vérifier l’état de l’application des mises à jour 

Vous pouvez vérifier la progression des mises à jour à l’aide de `az maintenance applyupdate get`. 

Vous pouvez utiliser `default` comme nom de mise à jour pour afficher les résultats de la dernière mise à jour ou remplacer `myUpdateName` par le nom de la mise à jour qui a été retournée lors de l’exécution de `az maintenance applyupdate create`.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime correspond à l’heure à laquelle la mise à jour est terminée, soit à votre initiative, soit à celle de la plateforme au cas où la fenêtre d’automaintenance n’aurait pas été utilisée. Si aucune mise à jour n’a jamais été appliquée par le biais du contrôle de maintenance, la valeur par défaut s’affiche.

### <a name="isolated-vm"></a>Machine virtuelle isolée

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Hôte dédié

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Supprimer une configuration de maintenance

Utilisez `az maintenance configuration delete` pour supprimer une configuration de maintenance. La suppression de la configuration supprime le contrôle de maintenance des ressources associées.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez [Maintenance et mises à jour](maintenance-and-updates.md).
