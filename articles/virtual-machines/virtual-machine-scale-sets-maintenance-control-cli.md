---
title: Contrôle de maintenance pour les mises à niveau d’images de système d’exploitation sur les groupes de machines virtuelles identiques Azure à l’aide d’Azure CLI
description: Découvrez comment contrôler le moment où les mises à niveau automatiques des images de système d’exploitation sont déployées sur vos groupes de machines virtuelles identiques Azure à l’aide du contrôle de maintenance et d’Azure CLI.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e00341b38cca73ccd351b4e16ce71317a8d0c751
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073045"
---
# <a name="maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-azure-cli"></a>Contrôle de maintenance pour les mises à niveau d’images de système d’exploitation sur les groupes de machines virtuelles identiques Azure à l’aide d’Azure CLI

Le contrôle de maintenance vous permet de choisir quand appliquer automatiquement les mises à niveau d’images de système d’exploitation invité à vos groupes de machines virtuelles identiques. Cette rubrique traite les options de l’interface Azure CLI pour le contrôle de maintenance. Pour plus d’informations sur l’utilisation du contrôle de maintenance, consultez [Contrôle maintenance pour les groupes de machines virtuelles identiques Azure](virtual-machine-scale-sets-maintenance-control.md).


## <a name="create-a-maintenance-configuration"></a>Créer une configuration de maintenance

Utilisez `az maintenance configuration create` pour créer une configuration de maintenance. Cet exemple crée une configuration de maintenance nommée *myConfig*, étendue à l’image de système d’exploitation. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope osimage\
   --location eastus
```

Copiez l’ID de configuration à partir de la sortie pour l’utiliser ultérieurement.

L’utilisation de `--maintenance-scope osimage` garantit l’utilisation de la configuration de maintenance pour contrôler les mises à jour du système d’exploitation invité.

Si vous essayez de créer une configuration portant le même nom mais dans un autre emplacement, vous obtenez une erreur. Les noms de configuration doivent être uniques dans votre groupe de ressources.

Vous pouvez rechercher par requête les configurations de maintenance disponibles à l’aide de `az maintenance configuration list`.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-a-scheduled-window"></a>Créer une configuration de maintenance avec une fenêtre planifiée
Vous pouvez également déclarer une fenêtre planifiée quand Azure applique les mises à jour sur vos ressources. Cet exemple crée une configuration de maintenance nommée *myConfig* avec une fenêtre planifiée de 5 heures, le quatrième lundi de chaque mois. Une fois que vous aurez créé une fenêtre planifiée, vous n’aurez plus besoin d’appliquer les mises à jour manuellement.

> [!IMPORTANT]
> La **durée** de maintenance doit être de *5 heures* ou plus. La **périodicité** de la maintenance doit être définie sur *Jour*.

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope osimage \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Day" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

## <a name="assign-the-configuration"></a>Affecter la configuration

Utilisez `az maintenance assignment create` pour affecter la configuration à votre groupe de machines virtuelles identiques.


## <a name="enable-automatic-os-upgrade"></a>Activez les mises à niveau automatiques du système d’exploitation

Vous pouvez activer les mises à niveau automatiques du système d’exploitation pour chaque groupe de machines virtuelles identiques qui utilisera le contrôle de maintenance. Pour obtenir des mises à niveau automatiques du système d’exploitation sur votre groupe de machines virtuelles identiques, consultez [Mises à niveau automatiques d’images de système d’exploitation de groupes de machines virtuelles identiques Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrir la maintenance et les mises à jour pour les machines virtuelles s’exécutant dans Azure](maintenance-and-updates.md)
