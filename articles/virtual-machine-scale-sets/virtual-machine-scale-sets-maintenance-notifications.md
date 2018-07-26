---
title: Gestion des notifications de maintenance pour les groupes de machines virtuelles identiques dans Azure | Microsoft Docs
description: Affichez les notifications de maintenance pour les groupes de machines virtuelles identiques dans Azure et démarrez la maintenance en libre-service.
services: virtual-machine-scale-sets
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: shants
ms.openlocfilehash: 4ce984686c2bb320d5d32771d31b81cdec1153af
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38506256"
---
# <a name="handling-planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Gestion des notifications de maintenance planifiées pour les groupes de machines virtuelles identiques

Azure exécute régulièrement des mises à jour afin d’améliorer la fiabilité, les performances et la sécurité de l’infrastructure hôte des machines virtuelles. Les mises à jour sont des modifications telles que la mise à jour corrective de l’environnement d’hébergement ou la mise à niveau et la désactivation de matériel. Une majorité de ces mises à jour ont lieu sans affecter les machines virtuelles hébergées. Cependant, il existe des cas où les mises à jour ont un impact :

- Lorsque la maintenance ne nécessite pas de redémarrage, Azure utilise une migration sur place pour mettre en pause la machine virtuelle pendant la mise à jour de l’hôte. Ces opérations de maintenance sans redémarrage sont appliquées domaine d’erreur par domaine d’erreur et sont arrêtées si des signaux d’avertissement sont reçus.

- Si la maintenance nécessite un redémarrage, une notification vous dira pour quand est prévue la maintenance. Dans ces cas, vous disposez d’une période pour commencer la maintenance vous-même, au moment qui vous convient.


La maintenance planifiée nécessitant un redémarrage s’effectue par vagues. Chaque vague a une portée différente (régions).

- Une vague commence par une notification aux clients. Par défaut, la notification est envoyée aux propriétaire et copropriétaires de l’abonnement. Vous pouvez ajouter plusieurs destinataires et options de messagerie, telles que les e-mails, les SMS et les Webhooks, aux notifications à l’aide des [alertes de journal d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) Azure.  
- Au moment de la notification, une *fenêtre de libre-service* est disponible. Pendant cet intervalle, vous pouvez rechercher quelles machines virtuelles sont incluses dans cette vague et démarrer la maintenance de manière proactive en fonction de vos besoins de planification.
- Après la fenêtre de libre-service, une *fenêtre de maintenance planifiée* apparaît. Au cours de cette fenêtre, Azure planifie et applique la maintenance requise à votre machine virtuelle. 

L’objectif de ces deux fenêtres est de vous donner suffisamment de temps pour commencer la maintenance et redémarrer votre machine virtuelle tout en sachant à quel moment Azure démarrera automatiquement la maintenance.


Vous pouvez utiliser le portail Azure, PowerShell, les API REST et l’interface de ligne de commande pour demander les fenêtres de maintenance pour vos machines virtuelles de groupes de machines virtuelles identiques et démarrer la maintenance en libre-service.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Devez-vous démarrer la maintenance pendant la fenêtre de libre-service ?  

Les instructions suivantes doivent vous aider à décider si vous devez utiliser cette fonctionnalité et démarrer la maintenance à votre rythme.

> [!NOTE] 
> La maintenance de libre-service n’est peut-être pas disponible pour toutes vos machines virtuelles. Pour déterminer si le redéploiement proactif est disponible pour votre machine virtuelle, recherchez **Démarrer maintenant** dans l’état de maintenance. La maintenance de libre-service n’est pas disponible pour les Services cloud (rôle de travail/web) et Service Fabric.


La maintenance de libre-service n’est pas recommandée pour les déploiements qui utilisent des **groupes à haute disponibilité**, car il s’agit de configurations hautement disponibles, dans lesquelles seulement un domaine de mise à jour est affecté à un moment donné. 

- Laissez Azure déclencher la maintenance. Dans le cas d’une maintenance qui nécessite un redémarrage, gardez à l’esprit qu’elle est effectuée domaine de mise à jour par domaine de mise à jour, que les domaines de mise à jour ne la reçoivent pas nécessairement de manière séquentielle et qu’une pause de 30 minutes sépare les domaines de mise à jour.
- Si une perte temporaire d’une partie de votre capacité (1/nombre de domaines de mise à jour) pose problème, elle peut facilement être compensée par l’allocation d’instances additionnelles au cours de la période de maintenance.
- Dans le cas d’une maintenance qui ne nécessite pas un redémarrage, les mises à jour sont appliquées au niveau du domaine d’erreur. 
    
**Ne pas** utiliser la maintenance de libre-service dans les scénarios suivants : 

- Si vous arrêtez vos machines virtuelles fréquemment, soit manuellement, à l’aide de DevTest Labs, en utilisant l’arrêt automatique ou en suivant une planification, cela peut rétablir l’état de maintenance et donc entraîner un temps d’arrêt supplémentaire.
- Sur les machines virtuelles à durée de vie limitée dont vous savez qu’elles seront supprimées avant la fin de la vague de maintenance. 
- Pour les charges de travail avec un état volumineux stockées dans le disque local (éphémère) qui doivent être maintenues lors de la mise à jour. 
- Dans les cas où vous redimensionnez souvent votre machine virtuelle, comme cela peut rétablir l’état de maintenance. 
- Si vous avez adopté des événements planifiés qui permettent un basculement proactif ou l’arrêt approprié de votre charge de travail, 15 minutes avant le début de l’arrêt de la maintenance.

**Utilisez** la maintenance de libre-service, si vous envisagez d’exécuter votre machine virtuelle sans interruption pendant la phase de maintenance planifiée et qu’aucune des contre-indications mentionnées ci-dessus n’est applicable. 

Il est conseillé d’utiliser la maintenance de libre-service dans les cas suivants :

- Vous devez communiquer une fenêtre de maintenance exacte à votre administration ou à votre client final. 
- Vous devez réaliser la maintenance pour une date donnée. 
- Vous devez contrôler la séquence de maintenance, par exemple, les applications multiniveaux pour garantir la récupération sans échec.
- Vous avez besoin de plus de 30 minutes de temps de récupération de machine virtuelle entre deux domaines de mise à jour (UD). Pour contrôler le délai entre les domaines de mise à jour, vous devez déclencher la maintenance sur vos machines virtuelles, un domaine de mise à jour (UD) à la fois.

 
## <a name="view-virtual-machine-scale-sets-impacted-by-maintenance-in-the-portal"></a>Afficher les groupes de machines virtuelles identiques affectés par la maintenance dans le portail

Lorsqu’une vague d’opérations de maintenance est planifiée, voue pouvez afficher la liste des groupes de machines virtuelles identiques impactés par la maintenance à venir à l’aide du portail Azure. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** sur le volet de navigation gauche et choisissez **Groupes de machines virtuelles identiques**.
3. Dans la page **Groupes de machines virtuelles identiques**, choisissez l’option **Modifier les colonnes** en haut pour ouvrir la liste des colonnes disponibles.
4. Dans la **section des colonnes disponibles**, sélectionnez l’élément **Maintenance en libre-service** et utilisez les boutons fléchés pour le déplacer dans la liste **Colonnes sélectionnées**. Vous pouvez basculer la liste déroulante dans la section **Colonnes disponibles** de **Toutes** à **Propriétés** pour rendre l’élément **Maintenance en libre-service** plus facile à rechercher. Une fois que vous avez l’élément **Maintenance en libre-service** dans la section **Colonnes sélectionnées**, sélectionnez le bouton **Appliquer** en bas de la page. 

Après avoir suivi les étapes ci-dessus, la colonne **Maintenance en libre-service** s’affiche dans la liste des groupes de machines virtuelles identiques. Chaque groupe de machines virtuelles identiques peut avoir une des valeurs suivantes pour la colonne de maintenance en libre-service :

| Valeur | Description |
|-------|-------------|
| OUI | Au moins une machine virtuelle dans votre groupe de machines virtuelles identiques est dans une fenêtre de libre-service. Vous pouvez démarrer la maintenance à tout moment pendant cette fenêtre de libre-service. | 
| Non  | Il n’y a aucune machine virtuelle définie dans une fenêtre de libre-service dans le groupe de machines virtuelles identiques affecté. | 
| - | Vos groupes de machines virtuelles identiques ne font pas partie d’un cycle de maintenance planifiée.| 

## <a name="notification-and-alerts-in-the-portal"></a>Notification et alertes dans le portail

Azure communique une planification de maintenance planifiée en envoyant un e-mail au propriétaire et au groupe de copropriétaires de l’abonnement. Vous pouvez ajouter des destinataires et des chaînes à cette communication en créant des alertes de journal d’activité Azure. Pour plus d’informations, consultez [Surveiller l’activité d’abonnement avec le journal d’activité Azure] (../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Surveiller**. 
3. Dans le volet **Surveiller - Alertes (classique)**, cliquez sur **+ Ajouter une alerte de journal d’activité**.
4. Renseignez les informations dans la page **Ajouter une alerte de journal d’activité** et vérifiez que vous définissez les éléments suivants dans **Critères** :
   - **Catégorie d’événement** : intégrité du service
   - **Services** : groupes de machines virtuelles identiques et machines virtuelles
   - **Type** : maintenance planifiée 
    
Pour découvrir plus en détail comment configurer des alertes de journal d’activité, consultez [Créer des alertes de journal d’activité](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md).
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Démarrer la maintenance sur votre groupe de machines virtuelles identiques à partir du portail

La présentation des groupes de machines virtuelles identiques contient davantage d’informations relatives à la maintenance. Si au moins une machine virtuelle dans le groupe de machines virtuelles identiques est incluse dans le cycle de maintenance planifiée, un nouveau ruban de notification sera ajouté au sommet de la page. Vous pouvez cliquer sur le ruban de notification pour accéder à la page **Maintenance** pour voir quelle instance de machine virtuelle est affectée par la maintenance planifiée. 

À partir de là, vous pourrez démarrer la maintenance en cochant la case correspondant à la machine virtuelle concernée et puis en cliquant sur l’option **Démarrer la maintenance**.

Lorsque vous démarrez la maintenance, les machines virtuelles affectées dans votre groupe de machines virtuelles identiques seront soumises à une maintenance et temporairement indisponibles. Si vous avez raté la fenêtre de libre-service, vous pourrez toujours afficher la fenêtre quand votre groupe de machines virtuelles identiques sera soumis à une maintenance par Azure.
 
## <a name="check-maintenance-status-using-powershell"></a>Vérifier l’état de maintenance à l’aide de PowerShell

Vous pouvez utiliser Azure Powershell pour voir quand les machines virtuelles dans vos groupes de machines virtuelles identiques sont planifiées pour la maintenance. Les informations de maintenance planifiée sont disponibles à partir de la cmdlet [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) lorsque vous utilisez le paramètre `-InstanceView`.
 
Les informations de maintenance sont retournées uniquement en cas de maintenance planifiée. Si aucune maintenance planifiée n’impacte l’instance de machine virtuelle, la cmdlet ne retourne pas d’informations de maintenance. 

```powershell
Get-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

Les propriétés suivantes sont retournées sous MaintenanceRedeployStatus : 
| Valeur | Description   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indique si vous pouvez démarrer la maintenance sur la machine virtuelle maintenant ||
| PreMaintenanceWindowStartTime         | Début de la fenêtre de maintenance en libre-service lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| PreMaintenanceWindowEndTime           | Fin de la fenêtre de maintenance en libre-service lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| MaintenanceWindowStartTime            | Début de la maintenance planifiée pendant laquelle Azure lance la maintenance sur votre machine virtuelle ||
| MaintenanceWindowEndTime              | Fin de la fenêtre de maintenance planifiée pendant laquelle Azure lance la maintenance sur votre machine virtuelle ||
| LastOperationResultCode               | Résultat de la dernière tentative de lancement de la maintenance sur la machine virtuelle ||



### <a name="start-maintenance-on-your-vm-instance-using-powershell"></a>Démarrer la maintenance sur votre instance de machine virtuelle à l’aide de PowerShell

Vous pouvez démarrer la maintenance sur une machine virtuelle si **IsCustomerInitiatedMaintenanceAllowed** est défini sur true à l’aide de la cmdlet [Set-AzureRmVmss](/powershell/module/azurerm.compute/set-azurermvmss) avec le paramètre `-PerformMaintenance`.

```powershell
Set-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-using-cli"></a>Vérifier l’état de maintenance à l’aide de l’interface CLI

Les informations de maintenance planifiée peuvent être consultées à l’aide de [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Les informations de maintenance sont retournées uniquement en cas de maintenance planifiée. S’il n’existe aucune maintenance planifiée qui affecte l’instance de machine virtuelle, la commande ne retourne pas d’informations de maintenance. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Les propriétés suivantes sont retournées sous MaintenanceRedeployStatus pour chaque instance de machine virtuelle : 
| Valeur | Description   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indique si vous pouvez démarrer la maintenance sur la machine virtuelle maintenant ||
| PreMaintenanceWindowStartTime         | Début de la fenêtre de maintenance en libre-service lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| PreMaintenanceWindowEndTime           | Fin de la fenêtre de maintenance en libre-service lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| MaintenanceWindowStartTime            | Début de la maintenance planifiée pendant laquelle Azure lance la maintenance sur votre machine virtuelle ||
| MaintenanceWindowEndTime              | Fin de la fenêtre de maintenance planifiée pendant laquelle Azure lance la maintenance sur votre machine virtuelle ||
| LastOperationResultCode               | Résultat de la dernière tentative de lancement de la maintenance sur la machine virtuelle ||


### <a name="start-maintenance-on-your-vm-instance-using-cli"></a>Démarrer la maintenance sur votre instance de machine virtuelle à l’aide de l’interface CLI

L’appel suivant lance la maintenance sur une instance de machine virtuelle si `IsCustomerInitiatedMaintenanceAllowed` est défini sur true.

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Forum Aux Questions


**Q : Pourquoi devez-vous redémarrer mes machines virtuelles maintenant ?**

**R :** Alors que la majorité des mises à jour et des mises à niveau vers la plateforme Azure n’affectent pas la disponibilité des machines virtuelles, il existe des cas où nous ne pouvons pas éviter le redémarrage des machines virtuelles hébergées dans Azure. Nous avons accumulé plusieurs modifications qui nous obligent à redémarrer nos serveurs, ce qui entraînera le redémarrage des machines virtuelles.

**Q : Si je suis vos recommandations concernant la haute disponibilité en utilisant un groupe à haute disponibilité, suis-je sécurisé ?**

**R :** Les machines virtuelles déployées dans un groupe à haute disponibilité ou dans des groupes de machines virtuelles identiques incluent la notion de domaines de mise à jour (UD). Lors de la réalisation de la maintenance, Azure respecte la contrainte de domaine de mise à jour et ne redémarre pas les machines virtuelles à partir de différents domaines de mise à jour (dans le même groupe à haute disponibilité).  Azure attend également au moins 30 minutes avant de passer au groupe de machines virtuelles suivant. 

Pour plus d’informations sur la haute disponibilité, consultez [Régions et disponibilité des machines virtuelles dans Azure](../virtual-machines/windows/regions-and-availability.md).

**Q : Comment être averti d’une maintenance planifiée ?**

**R :** Une vague d’opérations de maintenance planifiées commence par une planification sur une ou plusieurs régions Azure. Peu après, une notification par e-mail est envoyée aux propriétaires d’abonnement (un e-mail par abonnement). Il est possible de configurer des canaux et des destinataires supplémentaires pour cette notification à l’aide de la fonctionnalité Alertes de journal d’activité. Dans le cas où vous déployez une machine virtuelle dans une région où la planification de la maintenance est déjà effectuée, vous ne recevez pas la notification. Au lieu de cela, vous devez vérifier l’état de maintenance de la machine virtuelle.

**Q : Je ne vois aucune indication de maintenance planifiée dans le portail, dans PowerShell ou dans l’interface CLI. D’où vient le problème ?**

**R :** Les informations relatives à la maintenance planifiée ne sont disponibles pendant une vague d’opérations de maintenance planifiées que pour les machines virtuelles qu’elle impactera. En d’autres termes, si vous ne voyez pas de données, il est possible que la vague d’opérations de maintenance soit déjà terminée (ou qu’elle n’ait pas démarré), ou bien que votre machine virtuelle soit déjà hébergée sur un serveur mis à jour.

**Q : Est-il possible de savoir exactement quand ma machine virtuelle sera affectée ?**

**R :** Lors de la définition de la planification, nous définissons une fenêtre de temps de plusieurs jours. Toutefois, le séquencement exact des serveurs (et des machines virtuelles) au sein de cette fenêtre est inconnu. Les clients qui souhaitent connaître l’heure exacte pour leurs machines virtuelles peuvent utiliser des [événements planifiés](../virtual-machines/windows/scheduled-events.md), faire une requête à partir de la machine virtuelle et recevoir une notification 15 minutes avant le redémarrage d’une machine virtuelle.

**Q : Combien de temps vous faudra-t-il pour redémarrer ma machine virtuelle ?**

**R :** En fonction de la taille de votre machine virtuelle, le redémarrage peut prendre plusieurs minutes pendant la fenêtre de maintenance libre-service. Tout redémarrage lancé par Azure pendant la fenêtre de maintenance planifiée dure habituellement 25 minutes environ. Notez que dans le cas où vous utilisez des services cloud (rôle de travail/web), Virtual Machine Scale Sets ou des groupes à haute disponibilité, vous disposez de 30 minutes entre chaque groupe de machines virtuelles (UD) pendant la fenêtre de maintenance planifiée. 

**Q : Je ne vois aucune information de maintenance sur mes machines virtuelles. Quelle est la cause du problème ?**

**R :** Plusieurs raisons peuvent expliquer pourquoi vous ne voyez aucune information de maintenance sur vos machines virtuelles :
   - Vous utilisez un abonnement marqué comme interne à Microsoft.
   - Vos machines virtuelles ne sont pas planifiées pour la maintenance. Il est possible que la vague d’opérations de maintenance soit terminée, annulée ou modifiée, de sorte que celle-ci n’impacte plus vos machines virtuelles.
   - La colonne **Maintenance** n’a pas été ajoutée à l’affichage de liste de votre machine virtuelle. Alors que nous avons ajouté cette colonne à la vue par défaut, les clients qui ont effectué une configuration pour afficher des colonnes non définies par défaut doivent ajouter manuellement la colonne **Maintenance** à la vue liste de leurs machines virtuelles.

**Q : La maintenance de ma machine virtuelle est planifiée pour la deuxième fois. Pourquoi ?**

**R :** Il existe plusieurs cas d’utilisation où vous verrez votre machine virtuelle planifiée pour la maintenance après avoir déjà effectué votre maintenance-redéploiement :
   - Nous avons annulé la vague d’opérations de maintenance et l’avons redémarrée avec une charge utile différente. Il est possible que nous ayons détecté une charge utile ayant généré une erreur et nous voulons simplement déployer une charge utile supplémentaire.
   - Votre machine virtuelle a été *réparée par service* sur un autre nœud en raison d’une panne matérielle.
   - Vous avez choisi d’arrêter (de libérer) et de redémarrer la machine virtuelle.
   - Vous avez activé **Arrêt automatique** pour la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment vous pouvez vous inscrire aux événements de maintenance à partir de la machine virtuelle à l’aide de [Événements planifiés](../virtual-machines/windows/scheduled-events.md).
