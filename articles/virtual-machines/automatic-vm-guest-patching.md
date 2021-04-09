---
title: Mise à jour corrective automatique des systèmes invités des machines virtuelles Azure
description: Découvrez comment appliquer automatiquement des patchs aux machines virtuelles dans Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 276762bc2b8624f687cbb77e1af771478791a57b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101678424"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>Préversion : Mise à jour corrective automatique des systèmes invités des machines virtuelles Azure

L’activation de la mise à jour corrective automatique des systèmes invités des machines virtuelles Azure facilite la gestion des mises à jour en appliquant les patchs sur les machines virtuelles de façon automatique et sécurisée afin de maintenir la conformité en termes de sécurité.

La mise à jour corrective automatique de l’invité de machine virtuelle présente les caractéristiques suivantes :
- Les patchs classés comme *Critique* ou *Sécurité* sont automatiquement téléchargés et appliqués sur la machine virtuelle.
- Les patchs sont appliqués pendant les heures creuses dans le fuseau horaire de la machine virtuelle.
- L’orchestration des patchs est gérée par Azure et les patchs sont appliqués selon les [principes de première disponibilité](#availability-first-patching).
- L’intégrité des machines virtuelles, telle que déterminée par les signaux d’intégrité de la plateforme, est surveillée pour détecter les échecs de mise à jour corrective.
- Elle fonctionne pour toutes les tailles de machine virtuelle.

> [!IMPORTANT]
> La mise à jour corrective automatique de l’invité de machine virtuelle est actuellement disponible en Préversion publique. Une procédure de consentement est requise pour utiliser la fonctionnalité en préversion publique décrite ci-dessous.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Fonctionnement de la mise à jour corrective automatique de l’invité de machine virtuelle

Si la mise à jour corrective automatique de l’invité de machine virtuelle est activée sur une machine virtuelle, les patchs *Critiques* et *Sécurité* sont téléchargés et appliqués automatiquement sur la machine virtuelle. Ce processus démarre automatiquement chaque mois quand de nouveaux patchs sont publiés. L’évaluation et l’installation des patchs sont automatiques, et le processus comprend le redémarrage de la machine virtuelle le cas échéant.

La machine virtuelle est évaluée régulièrement et plusieurs fois sur une période de 30 jours pour déterminer les correctifs applicables pour cette machine virtuelle. Les patchs peuvent être installés n’importe quel jour sur la machine virtuelle, pendant les heures creuses de la machine virtuelle. Cette évaluation automatique garantit que tous les patchs manquants sont découverts dès que possible.

Les patchs sont installés dans les 30 jours suivant la publication de patchs mensuelle, qui suit l’orchestration de première disponibilité décrite ci-dessous. Les patchs sont installés uniquement pendant les heures creuses de la machine virtuelle, en fonction de son fuseau horaire. La machine virtuelle doit fonctionner pendant les heures creuses pour que les patchs soient installés automatiquement. Si une machine virtuelle est mise hors tension pendant une évaluation périodique, elle est automatiquement évaluée et les patchs applicables sont installés automatiquement lors de l’évaluation périodique suivante (habituellement quelques jours plus tard) où la machine virtuelle est sous tension.

Les mises à jour de définitions et d’autres patchs non classifiés comme *critiques* ou de *sécurité* ne seront pas installés par le biais de la mise à jour corrective automatique des systèmes invités des machines virtuelles. Pour installer des patchs avec d’autres classifications de patchs ou pour planifier l’installation des patchs dans votre propre fenêtre de maintenance personnalisée, vous pouvez utiliser [Update Management](./windows/tutorial-config-management.md#manage-windows-updates).

### <a name="availability-first-patching"></a>Mise à jour corrective selon la première disponibilité

Le processus d’installation des patchs est orchestré mondialement par Azure pour toutes les machines virtuelles sur lesquelles la mise à jour corrective automatique de l’invité de machine virtuelle est activée. Cette orchestration suit les principes de première disponibilité sur les différents niveaux de disponibilité fournis par Azure.

Pour un groupe de machines virtuelles en cours de mise à jour, la plateforme Azure orchestre les mises à jour :

**Entre les régions :**
- Une mise à jour mensuelle est orchestrée mondialement sur l’ensemble du service Azure et par phases afin d’éviter les échecs de déploiement au niveau mondial.
- Une phase peut avoir une ou plusieurs régions, et une mise à jour passe aux phases suivantes uniquement si les machines virtuelles éligibles d’une phase sont correctement mises à jour.
- Les régions associées géographiquement ne sont pas mises à jour simultanément et ne peuvent pas dépendre de la même phase régionale.
- La réussite d’une mise à jour est mesurée par le suivi de l’intégrité de la machine virtuelle après sa mise à jour. L’intégrité de la machine virtuelle est suivie via les indicateurs d’intégrité de la plateforme pour la machine virtuelle.

**Dans une région :**
- Les machines virtuelles de différentes Zones de disponibilité ne sont pas mises à jour simultanément.
- Les machines virtuelles ne faisant pas partie d’un groupe à haute disponibilité sont traitées par lot dans la mesure du possible afin d’éviter les mises à jour simultanées pour toutes les machines virtuelles d’un abonnement.

**Dans un groupe à haute disponibilité :**
- Toutes les machines virtuelles d’un même groupe à haute disponibilité ne sont pas mises à jour simultanément.
-   Les machines virtuelles d’un même groupe à haute disponibilité sont mises à jour dans les limites du domaine de mise à jour, et les machines virtuelles de différents domaines de mise à jour ne sont pas mises à jour simultanément.

La date d’installation des patchs pour une machine virtuelle donnée peut varier d’un mois à l’autre, car une machine virtuelle spécifique peut être choisie dans un autre lot entre les cycles de mise à jour corrective mensuelle.

### <a name="which-patches-are-installed"></a>Quels sont les patchs installés ?
Les patchs installés dépendent de la phase de lancement pour la machine virtuelle. Chaque mois, un nouveau déploiement mondial est lancé. Tous les patchs de sécurité et critiques évalués pour une machine virtuelle individuelle sont alors installés pour cette machine virtuelle. Le déploiement est orchestré dans toutes les régions Azure par lots (consultez la description dans la section plus haut, relative à la mise à jour corrective selon la première disponibilité).

L’ensemble exact de patchs à installer varie en fonction de la configuration de la machine virtuelle, notamment du type de système d’exploitation et du moment auquel a lieu l’évaluation. Des patchs différents peuvent être installés sur deux machines virtuelles identiques de régions différentes si le nombre de patchs disponibles est supérieur ou inférieur quand l’orchestration des patchs atteint les différentes régions à différents moments. De même, des machines virtuelles situées dans la même région mais évaluées à des moments différents (en raison de différents lots de zones de disponibilité ou de groupes à haute disponibilité) peuvent recevoir des patchs différents. Ceci se produit cependant moins fréquemment.

Étant donné que la mise à jour corrective automatique des systèmes invités des machines virtuelles ne configure pas la source de patchs, il se peut également que les ensembles de patchs installés sur deux machines virtuelles similaires configurées avec des sources de patchs différentes (référentiel public et référentiel privé, par exemple) ne soient pas exactement les mêmes.

Pour les types de système d’exploitation qui publient des patchs à une cadence fixe, les machines virtuelles configurées sur le référentiel public pour le système d’exploitation recevront probablement le même ensemble de patchs au cours des différentes phases de déploiement du mois. Il peut s’agir, par exemple, des machines virtuelles Windows configurées sur le référentiel Windows Update public.

Étant donné qu’un nouveau déploiement est déclenché chaque mois, une machine virtuelle reçoit au moins un déploiement de patchs chaque mois si elle est mise sous tension pendant les heures creuses. Ceci garantit que les derniers patchs de sécurité et critiques disponibles sont appliqués à la machine virtuelle sur une base mensuelle. Pour garantir la cohérence de l’ensemble de patchs installés, vous pouvez configurer vos machines virtuelles pour évaluer et télécharger des patchs à partir de vos propres référentiels privés.

## <a name="supported-os-images"></a>Images de système d’exploitation prises en charge
Seules les machines virtuelles créées à partir de certaines images de plateforme de système d’exploitation sont actuellement prises en charge dans la préversion. Les images personnalisées ne sont actuellement pas prises en charge dans la préversion.

Les références SKU de plateforme suivantes sont prises en charge (et d’autres sont régulièrement ajoutées) :

| Serveur de publication               | Offre de système d’exploitation      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18.04-LTS |
| Redhat  | RHEL | 7.x |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-centre-de-données    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |

## <a name="patch-orchestration-modes"></a>Modes d’orchestration des patchs
Les machines virtuelles sur Azure prennent maintenant en charge les modes d’orchestration des patchs suivants :

**AutomaticByPlatform :**
- Ce mode est pris en charge pour les machines virtuelles Linux et Windows.
- Ce mode active la mise à jour corrective automatique des systèmes invités des machines virtuelles pour la machine virtuelle, et l’installation ultérieure des patchs est orchestrée par Azure.
- Ce mode est obligatoire pour la mise à jour corrective selon la première disponibilité.
- Ce mode est pris en charge uniquement pour les machines virtuelles créées à l’aide des images de plateforme de système d’exploitation prises en charge ci-dessus.
- Pour les machines virtuelles Windows, le réglage de ce mode désactive également les mises à jour automatiques natives sur la machine virtuelle Windows pour éviter les doublons.
- Pour utiliser ce mode sur des machines virtuelles Linux, définissez la propriété `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` dans le modèle de machine virtuelle.
- Pour utiliser ce mode sur des machines virtuelles Windows, définissez la propriété `osProfile.windowsConfiguration.enableAutomaticUpdates=true` et la propriété `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` dans le modèle de machine virtuelle.

**AutomaticByOS :**
- Ce mode est pris en charge uniquement pour les machines virtuelles Windows.
- Ce mode active les mises à jour automatiques sur la machine virtuelle Windows, et les patchs sont installés sur la machine virtuelle par le biais des mises à jour automatiques.
- Ce mode ne prend pas en charge la mise à jour corrective selon la première disponibilité.
- Ce mode est défini par défaut si aucun autre mode correctif n’est spécifié pour une machine virtuelle Windows.
- Pour utiliser ce mode sur des machines virtuelles Windows, définissez la propriété `osProfile.windowsConfiguration.enableAutomaticUpdates=true` et la propriété `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` dans le modèle de machine virtuelle.

**Manuel :**
- Ce mode est pris en charge uniquement pour les machines virtuelles Windows.
- Ce mode désactive les mises à jour automatiques sur la machine virtuelle Windows.
- Ce mode ne prend pas en charge la mise à jour corrective selon la première disponibilité.
- Ce mode doit être défini lors de l’utilisation de solutions personnalisées de mise à jour corrective.
- Pour utiliser ce mode sur des machines virtuelles Windows, définissez la propriété `osProfile.windowsConfiguration.enableAutomaticUpdates=false` et la propriété `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` dans le modèle de machine virtuelle.

**ImageDefault :**
- Ce mode est pris en charge uniquement pour les machines virtuelles Linux.
- Ce mode ne prend pas en charge la mise à jour corrective selon la première disponibilité.
- Ce mode respecte la configuration de mise à jour corrective par défaut dans l’image utilisée pour créer la machine virtuelle.
- Ce mode est défini par défaut si aucun autre mode correctif n’est spécifié pour une machine virtuelle Linux.
- Pour utiliser ce mode sur des machines virtuelles Linux, définissez la propriété `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` dans le modèle de machine virtuelle.

> [!NOTE]
>Pour les machines virtuelles Windows, la propriété `osProfile.windowsConfiguration.enableAutomaticUpdates` ne peut actuellement être définie qu’au moment où la machine virtuelle est créée pour la première fois. Le passage du mode manuel à un mode automatique ou d’un mode automatique au mode manuel n’est pas pris en charge pour le moment. Le passage du mode AutomaticByOS au mode AutomaticByPlatfom est pris en charge.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Conditions requises pour l’activation de la mise à jour corrective automatique de l’invité de machine virtuelle

- L’agent de machine virtuelle Azure pour [Windows](./extensions/agent-windows.md) ou [Linux](./extensions/agent-linux.md) doit être installé sur la machine virtuelle.
- Pour les machines virtuelles Linux, l’agent Linux Azure doit être en version 2.2.53.1 ou supérieure. [Mettez à jour l’agent Linux](./extensions/update-linux-agent.md) si la version actuelle est inférieure à la version requise.
- Pour les machines virtuelles Windows, le service Windows Update doit fonctionner sur la machine virtuelle.
- La machine virtuelle doit être en mesure d’accéder aux points de terminaison de mise à jour configurés. Si votre machine virtuelle est configurée pour utiliser des référentiels privés pour Linux ou Windows Server Update Services (WSUS) pour les machines virtuelles Windows, les points de terminaison de mise à jour appropriés doivent être accessibles.
- Utilisez la version 2020-12-01 ou une version supérieure de l’API Compute. La version 2020-06-01 de l’API Compute peut être utilisée pour les machines virtuelles Windows avec des fonctionnalités limitées.

L’activation de la fonctionnalité en préversion nécessite une inscription unique aux fonctionnalités **InGuestAutoPatchVMPreview** et **InGuestPatchVMPreview** par abonnement comme indiqué dans la section suivante.

### <a name="rest-api"></a>API REST
L’exemple suivant décrit comment activer la préversion pour votre abonnement :

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```
```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

L’inscription de la fonctionnalité peut prendre jusqu’à 15 minutes. Pour vérifier l’état de l’inscription :

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```
```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```
Une fois la fonctionnalité enregistrée pour votre abonnement, effectuez le processus d’inscription en propageant la modification dans le fournisseur de ressources de calcul.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Utilisez l’applet de commande [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) pour activer la préversion pour votre abonnement.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

L’inscription de la fonctionnalité peut prendre jusqu’à 15 minutes. Pour vérifier l’état de l’inscription :

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Une fois la fonctionnalité enregistrée pour votre abonnement, effectuez le processus d’inscription en propageant la modification dans le fournisseur de ressources de calcul.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Utilisez [az feature register](/cli/azure/feature#az-feature-register) pour activer la préversion pour votre abonnement.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

L’inscription de la fonctionnalité peut prendre jusqu’à 15 minutes. Pour vérifier l’état de l’inscription :

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Une fois la fonctionnalité enregistrée pour votre abonnement, effectuez le processus d’inscription en propageant la modification dans le fournisseur de ressources de calcul.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>Activer la mise à jour corrective automatique de l’invité de machine virtuelle
Pour activer la mise à jour corrective automatique des systèmes invités des machines virtuelles sur une machine virtuelle Windows, assurez-vous que la propriété *osProfile.windowsConfiguration.enableAutomaticUpdates* est définie sur *true* dans la définition du modèle de machine virtuelle. Cette propriété peut uniquement être définie lors de la création de la machine virtuelle. Cette propriété supplémentaire n’est pas applicable aux machines virtuelles Linux.

### <a name="rest-api-for-linux-vms"></a>API REST pour machines virtuelles Linux
L’exemple suivant décrit comment activer la mise à jour corrective automatique de l’invité de machine virtuelle :

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="rest-api-for-windows-vms"></a>API REST pour machines virtuelles Windows
L’exemple suivant décrit comment activer la mise à jour corrective automatique de l’invité de machine virtuelle :

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell-for-windows-vms"></a>Azure PowerShell pour machines virtuelles Windows
Utilisez la cmdlet [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) pour activer la mise à jour corrective automatique de l’invité de machine virtuelle lors de la création ou de la mise à jour d’une machine virtuelle.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Azure CLI pour machines virtuelles Windows
Utilisez [az vm create](/cli/azure/vm#az-vm-create) pour activer la mise à jour corrective automatique de l’invité de machine virtuelle lors de la création d’une machine virtuelle. L’exemple suivant configure la mise à jour corrective automatique de l’invité de machine virtuelle pour la machine virtuelle nommée *myVM* dans le groupe de ressources appelé *myResourceGroup* :

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Pour modifier une machine virtuelle existante, utilisez [az vm update](/cli/azure/vm#az-vm-update).

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Activation et évaluation

> [!NOTE]
>L’activation des mises à jour automatiques de l’invité de machine virtuelle peut prendre plus de trois heures, car elle est effectuée pendant les heures creuses de la machine virtuelle. Comme l’évaluation et l’installation des patchs se produisent uniquement pendant les heures creuses, votre machine virtuelle doit également fonctionner pendant les heures creuses pour appliquer les patchs.

Quand la mise à jour corrective automatique des systèmes invités des machines virtuelles est activée pour une machine virtuelle, une extension de machine virtuelle est installée : de type `Microsoft.CPlat.Core.LinuxPatchExtension` sur une machine virtuelle Linux et de type `Microsoft.CPlat.Core.WindowsPatchExtension` sur une machine virtuelle Windows. Il n’est pas nécessaire d’installer ou de mettre à jour cette extension manuellement, car elle est gérée par la plateforme Azure dans le cadre du processus de mise à jour corrective automatique de l’invité de machine virtuelle.

L’activation des mises à jour automatiques de l’invité de machine virtuelle peut prendre plus de trois heures, car elle est effectuée pendant les heures creuses de la machine virtuelle. L’extension est également installée et mise à jour pendant les heures creuses de la machine virtuelle. Si les heures creuses de la machine virtuelle se terminent avant la fin de l’activation, le processus d’activation reprendra pendant les heures creuses disponibles suivantes.

Les mises à jour automatiques sont désactivées dans la plupart des scénarios, et l’installation des patchs s’effectue désormais par le biais de l’extension. Les conditions suivantes s’appliquent.
- Si les mises à jour automatiques Windows Update étaient activées sur une machine virtuelle Windows par le biais du mode correctif AutomaticByOS, elles sont désactivées pour la machine virtuelle quand l’extension est installée.
- Pour les machines virtuelles Ubuntu, les mises à jour automatiques par défaut sont désactivées automatiquement à l’issue du processus d’activation de la mise à jour corrective automatique des systèmes invités des machines virtuelles.
- Pour RHEL, les mises à jour automatiques doivent être désactivées manuellement (il s’agit d’une limitation de la préversion). Exécutez :

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

Pour vérifier si la mise à jour corrective automatique de l’invité de machine virtuelle est terminée et si l’extension de mise à jour corrective est installée sur la machine virtuelle, vous pouvez consulter la vue d’instance de la machine virtuelle. Si le processus d’activation est terminé, l’extension sera installée et les résultats de l’évaluation de la machine virtuelle seront disponibles sous `patchStatus`. La vue d’instance de la machine virtuelle est accessible de plusieurs façons, comme décrit ci-dessous.

### <a name="rest-api"></a>API REST

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Utilisez la cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) avec le paramètre `-Status` pour accéder à la vue d’instance de votre machine virtuelle.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

Actuellement, PowerShell fournit uniquement des informations sur l’extension de mise à jour corrective. Des informations sur `patchStatus` seront également bientôt disponibles par le biais de PowerShell.

### <a name="azure-cli"></a>Azure CLI
Utilisez [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) pour accéder à la vue d’instance de votre machine virtuelle.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>Compréhension de l’état des patchs pour votre machine virtuelle

La section `patchStatus` de la réponse de la vue d’instance fournit des détails sur l’évaluation la plus récente et la dernière installation de patch pour votre machine virtuelle.

Les résultats de l’évaluation de votre machine virtuelle peuvent être consultés dans la section `availablePatchSummary`. Une évaluation est effectuée régulièrement pour une machine virtuelle pour laquelle la mise à jour corrective automatique de l’invité de machine virtuelle est activée. Le nombre de patchs disponibles après une évaluation est fourni sous les résultats `criticalAndSecurityPatchCount` et `otherPatchCount`. La mise à jour corrective automatique de l’invité de machine virtuelle installera tous les patchs évalués sous les classifications de patchs *Critique* et *Sécurité*. Tout autre patch évalué est ignoré.

Les résultats de l’installation des patchs pour votre machine virtuelle peuvent être consultés dans la section `lastPatchInstallationSummary`. Cette section fournit des informations sur la dernière tentative d’installation de patchs sur la machine virtuelle, notamment le nombre de patchs installés, en attente, en échec ou ignorés. Les patchs sont installés uniquement pendant la fenêtre de maintenance des heures creuses pour la machine virtuelle. Une nouvelle tentative est effectuée automatiquement pour les patchs en attente et en échec pendant la fenêtre de maintenance des heures creuses.

## <a name="on-demand-patch-assessment"></a>Évaluation des patchs à la demande
Si la mise à jour corrective automatique de l’invité de machine virtuelle est déjà activée pour votre machine virtuelle, une évaluation périodique des patchs est effectuée sur la machine virtuelle pendant ses heures creuses. Ce processus est automatique et les résultats de l’évaluation la plus récente peuvent être examinés par le biais de la vue d’instance de la machine virtuelle, comme décrit précédemment dans ce document. Vous pouvez également déclencher une évaluation des patchs à la demande pour votre machine virtuelle à tout moment. L’évaluation des patchs peut prendre quelques minutes et l’état de l’évaluation la plus récente est mis à jour dans la vue d’instance de la machine virtuelle.

L’activation de la fonctionnalité en préversion requiert une inscription unique à la fonctionnalité **InGuestPatchVMPreview** par abonnement. Cette préversion de la fonctionnalité est différente de l’inscription automatique de la fonctionnalité de mise à jour corrective invité de machine virtuelle effectuée précédemment pour **InGuestAutoPatchVMPreview**. L’activation de l’aperçu des fonctionnalités supplémentaires est une exigence distincte et supplémentaire. La préversion des fonctionnalités pour l’évaluation des correctifs à la demande peut être activée à la suite du [processus d’activation de la préversion](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) décrit précédemment pour la mise à jour corrective de l’invité de machine virtuelle.

> [!NOTE]
>L’évaluation des correctifs à la demande ne déclenche pas automatiquement l’installation des correctifs. Si vous avez activé la mise à jour corrective automatique de l’invité de machine virtuelle, les correctifs appliqués et applicables pour la machine virtuelle sont installés pendant les heures creuses de la machine virtuelle, en suivant le processus de mise à jour corrective et de disponibilité décrit plus haut dans ce document.

### <a name="rest-api"></a>API REST
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Utilisez la cmdlet [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) pour évaluer les patchs disponibles pour votre machine virtuelle.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Azure CLI
Utilisez [az vm assess-patches](/cli/azure/vm#az-vm-assess-patches) pour évaluer les patchs disponibles pour votre machine virtuelle.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [En savoir plus sur la création et la gestion de machines virtuelles Windows](./windows/tutorial-manage-vm.md)
