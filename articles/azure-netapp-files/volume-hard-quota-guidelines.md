---
title: Ce que signifie le passage au quota inconditionnel de volume pour votre service Azure NetApp Files | Microsoft Docs
description: Décrit le passage au quota inconditionnel de volume, la planification de la modification et la surveillance et la gestion des capacités.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: b173342c1c384213e88f216334b5e03cd8b7bea7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374488"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Ce que signifie le passage au quota inconditionnel de volume pour votre service Azure NetApp Files

Depuis le début du service, Azure NetApp Files utilise un mécanisme d’approvisionnement et de croissance automatique des pools de capacité. Les volumes Azure NetApp Files sont approvisionnés de façon dynamique sur un pool de capacité sous-jacent, approvisionné par le client, d’un niveau et d’une taille sélectionnés. Les tailles de volume (quotas) sont utilisées pour fournir le niveau de performance et la capacité, et les quotas peuvent être ajustés à la volée à tout moment. Ce comportement signifie que, actuellement, le quota de volume est un levier du niveau de performance utilisé pour contrôler la bande passante sur le volume. Actuellement, les pools de capacité sous-jacents augmentent automatiquement lorsque la capacité est pleine.   

> [!IMPORTANT] 
> Le comportement d’Azure NetApp Files en matière d’approvisionnement de volumes et de pools de capacité va changer pour un mécanisme *manuel* et *contrôlable*. **À partir du 1er avril 2021, les tailles de volume (quota) géreront le niveau de performance de la bande passante ainsi que la capacité approvisionnée, et les pools de capacité sous-jacents n’augmenteront plus automatiquement.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>Raisons du passage au quota inconditionnel de volume

De nombreux clients ont indiqué que le comportement *initial* posait trois grands inconvénients :
* Les clients de machines virtuelles voient la capacité approvisionnée dynamiquement (100 Tio) d’un volume donné lorsqu’ils utilisent l’espace du système d’exploitation ou des outils de surveillance de la capacité, ce qui donne une visibilité inexacte de la capacité côté client ou application.
* Les propriétaires d’applications n’ont aucun contrôle sur l’espace de pools de capacité approvisionné (et le coût associé) en raison du comportement de croissance automatique des pools de capacité. Cette situation est fastidieuse dans les environnements où les « pertes de contrôle de processus » peuvent rapidement remplir et augmenter la capacité approvisionnée et le coût.
* Les clients veulent voir et maintenir une corrélation directe entre la taille du volume (quota) et le niveau de performance. Avec le comportement actuel de surabonnement (implicite) d’un volume (en matière de capacité) et de croissance automatique des pools, les clients n’ont pas de corrélation directe tant que le quota de volume n’a pas été défini ou réinitialisé activement. 

De nombreux clients ont demandé un contrôle direct sur la capacité approvisionnée. Ils souhaitent contrôler et équilibrer la capacité et l’utilisation du stockage. Ils souhaitent également contrôler les coûts et disposer d’une visibilité, côté application et côté client, de la capacité disponible, utilisée et approvisionnée et du niveau de performance de leurs volumes d’application. 

## <a name="what-is-the-volume-hard-quota-change"></a>Présentation du passage au quota inconditionnel de volume   

Lorsque vous passez au quota inconditionnel de volume, les volumes Azure NetApp Files ne seront plus soumis à l’approvisionnement dynamique de 100 Tio (maximum). Les volumes seront approvisionnés à la taille réelle configurée (quota). En outre, les pools de capacité sous-jacents ne seront plus automatiquement augmentés lorsque la consommation à pleine capacité sera atteinte. Ce changement reflète le comportement des disques managés Azure, qui sont également approvisionnés tels quels, sans augmentation automatique de la capacité.

Prenons l’exemple d’un volume Azure NetApp Files configuré à une taille de 1 Tio (quota) sur un pool de capacité de niveau de service Ultra de 4 Tio. Une application écrit continuellement des données sur le volume.

Le comportement *initial* :  
* Bande passante attendue : 128 Mio/s
* Capacité totale utilisable (et visible par le client) : 100 Tio   
    Vous ne serez pas en mesure d’écrire plus de données sur le volume au-delà de cette taille.
* Pool de capacité : Augmente automatiquement par incréments de 1 Tio lorsqu’il est plein.
* Modification du quota de volume : Modifie uniquement le niveau de performance (bande passante) du volume. Cela ne modifie pas la capacité visible ou utilisable par le client.

Le comportement *modifié* :  
* Bande passante attendue : 128 Mio/s
* Capacité totale utilisable (et visible par le client) : 1 Tio. Vous ne serez pas en mesure d’écrire plus de données sur le volume au-delà de cette taille.
* Pool de capacité : La taille reste fixée à 4 Tio et n’augmente pas automatiquement. 
* Modification du quota de volume : Modifie le niveau de performance (bande passante) et la capacité visible ou utilisable par le client du volume.

Vous devez surveiller de manière proactive l’utilisation des volumes Azure NetApp Files et des pools de capacité. Pour une consommation proche de la pleine capacité, vous devez modifier intentionnellement l’utilisation des volumes et des pools. Azure NetApp Files continuera à permettre des [opérations de redimensionnement à la volée des volumes et des pools de capacité](azure-netapp-files-resize-capacity-pools-or-volumes.md).

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>Mise en œuvre du passage au quota inconditionnel de volume

Cette section fournit une aide sur la manière de rendre opérationnel le passage au quota inconditionnel de volume pour une transition en douceur. Elle donne également des indications sur la manière de gérer les volumes et les pools de capacité actuellement approvisionnés, le suivi continu et les options d’alerte et de gestion de la capacité.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>Volumes et pools de capacité actuellement approvisionnés

En raison du passage au quota inconditionnel de volume, vous devez modifier votre modèle de fonctionnement. Les volumes et les pools de capacité approvisionnés nécessitent une gestion continue de la capacité.  Comme le changement de comportement se fera instantanément, l’équipe d’Azure NetApp Files recommande une série de mesures correctives ponctuelles pour les volumes et les pools de capacité existants, précédemment approvisionnés, comme décrit dans cette section.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Recommandations de mesures correctives ou préventives ponctuelles  

Le passage au quota inconditionnel de volume entraîne des modifications de la capacité approvisionnée et disponible pour les volumes et pools précédemment approvisionnés. Par conséquent, certains problèmes d’allocation de capacité peuvent se produire. Pour éviter aux clients des situations de dépassement de capacité à court terme, l’équipe d’Azure NetApp Files recommande les mesures correctives/préventives ponctuelles suivantes : 

* **Tailles de volume approvisionnées** :   
    Redimensionnez chaque volume approvisionné pour disposer d’une mémoire tampon appropriée en fonction du taux de changement et des alertes ou redimensionnez le délai d’exécution (par exemple, 20 % en fonction des considérations de charge de travail typiques), avec un maximum de 100 Tio (ce qui correspond à la [limite de taille des volumes](azure-netapp-files-resource-limits.md#resource-limits)). Cette nouvelle taille de volume, y compris la capacité de la mémoire tampon, doit reposer sur les facteurs suivants :
    * Capacité de volume **approvisionnée**, dans le cas où la capacité utilisée est inférieure au quota de volume approvisionné.
    * Capacité de volume **utilisée**, dans le cas où la capacité utilisée est supérieure au quota de volume approvisionné.  
    Il n’y a pas de frais supplémentaires pour l’augmentation de la capacité au niveau du volume si le pool de capacité sous-jacent n’a pas besoin d’être augmenté. À la suite de ce changement, vous pourriez observer une *augmentation* de la limite de bande passante pour le volume (dans le cas où le [type de pool de capacité Qualité de service automatique](azure-netapp-files-understand-storage-hierarchy.md#qos_types) est utilisé).

* **Tailles des pools de capacité approvisionnées** :   
    Une fois les tailles de volume ajustées, si la somme des tailles de volumes devient supérieure à la taille du pool de capacité d’hébergement, le pool de capacité devra être augmenté à une taille égale ou supérieure à la somme des volumes, avec un maximum de 500 Tio (ce qui correspond à la [limite de taille des pools de capacité](azure-netapp-files-resource-limits.md#resource-limits)). La capacité supplémentaire du pool de capacité est soumise à des frais ACR comme d’habitude.

Si vous avez besoin d’aide pour configurer la surveillance ou les alertes comme décrit dans les sections ci-dessous, collaborez avec vos spécialistes Azure NetApp Files pour valider votre environnement.

### <a name="ongoing-capacity-management"></a>Gestion continue des capacités  

Après avoir appliqué les mesures correctives ponctuelles, vous devez mettre en place des processus permanents pour surveiller et gérer les capacités. Les sections suivantes présentent des suggestions et des alternatives concernant le suivi et la gestion des capacités.

### <a name="monitor-capacity-utilization"></a>Surveiller l’utilisation des capacités

Vous pouvez surveiller l’utilisation des capacités à différents niveaux. 

#### <a name="vm-level-monitoring"></a>Surveillance au niveau de la machine virtuelle 

Le niveau le plus élevé de surveillance (le plus proche de l’application) se fait à partir de la machine virtuelle de l’application. Vous observerez un changement de comportement dans les rapports de capacité depuis le système d’exploitation client de la machine virtuelle.

Dans les deux scénarios suivants, prenons l’exemple d’un volume Azure NetApp Files configuré à une taille de 1 Tio (quota) sur un pool de capacité de niveau de service Ultra de 4 Tio. 

##### <a name="windows"></a>Windows

Les clients Windows peuvent vérifier la capacité utilisée et disponible d’un volume à l’aide des propriétés du lecteur mappé. Vous pouvez utiliser l’option **Explorateur de fichiers** -> **Lecteur** -> **Propriétés**.  

Les exemples suivants illustrent les rapports relatifs à la capacité de volume dans Windows *avant* le changement de comportement :

![Captures d’écran montrant un exemple de capacité de stockage d’un volume avant le changement de comportement.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

Vous pouvez également utiliser la commande `dir` dans l’invite de commandes, comme indiqué ci-dessous :

![Capture d’écran montrant l’utilisation d’une commande pour afficher la capacité de stockage d’un volume avant le changement de comportement.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

Les exemples suivants illustrent les rapports relatifs à la capacité de volume dans Windows *après* le changement de comportement :

![Captures d’écran montrant un exemple de capacité de stockage d’un volume après le changement de comportement.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

L’exemple suivant montre la sortie de la commande `dir` :  

![Capture d’écran montrant l’utilisation d’une commande pour afficher la capacité de stockage d’un volume après le changement de comportement.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Les clients Linux peuvent vérifier la capacité utilisée et disponible d’un volume à l’aide de la [commande `df`](https://linux.die.net/man/1/df). L’option `-h` indiquera la taille, l’espace utilisé et l’espace disponible dans un format lisible, à l’aide de les tailles d’unités M, G et T.

L’exemple suivant illustre les rapports relatifs à la capacité de volume dans Linux *avant* le changement de comportement :  

![Capture d’écran montrant l’utilisation de Linux pour afficher la capacité de stockage d’un volume avant le changement de comportement.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

L’exemple suivant illustre les rapports relatifs à la capacité de volume dans Linux *après* le changement de comportement :  

![Capture d’écran montrant l’utilisation de Linux pour afficher la capacité de stockage d’un volume après le changement de comportement.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>Configurer des alertes à l’aide d’ANFCapacityManager

Vous pouvez utiliser l’outil ANFCapacityManager de Logic Apps, pris en charge par la communauté, pour surveiller la capacité d’Azure NetApp Files et recevoir des alertes personnalisées. L’outil ANFCapacityManager est disponible sur la [page de GitHub relative à ANFCapacityManager](https://github.com/ANFTechTeam/ANFCapacityManager).

ANFCapacityManager est une application logique Azure qui gère les règles d’alerte basées sur la capacité. Elle augmente automatiquement la taille des volumes pour éviter que vos volumes Azure NetApp Files ne manquent d’espace. Elle est facile à déployer et offre les capacités Alert Management suivantes :

* Lorsqu’un pool de capacité ou un volume Azure NetApp Files est créé, ANFCapacityManager crée une règle d’alerte métrique en fonction du seuil spécifié du pourcentage de consommation.
* Lorsqu’un pool de capacité ou un volume Azure NetApp Files est redimensionné, ANFCapacityManager modifie la règle d’alerte métrique en fonction du seuil spécifié du pourcentage de capacité consommée. Si la règle d’alerte n’existe pas, elle est créée.
* Lorsqu’un pool de capacité ou un volume Azure NetApp Files est supprimé, la règle d’alerte métrique correspondante est supprimée.

Vous pouvez configurer les principaux paramètres d’alerte suivants :  

* **Seuil de pleine capacité en pourcentage du pool de capacité** : Ce paramètre détermine le seuil de consommation qui déclenche une alerte pour les pools de capacité. Une valeur de 90 déclenche une alerte lorsque le pool de capacité atteint une consommation de 90 %.
* **Seuil de pleine capacité en pourcentage du volume** : Ce paramètre détermine le seuil de consommation qui déclenche une alerte pour les volumes. Une valeur de 80 déclenche une alerte lorsque le volume atteint une consommation de 80 %.
* **Groupe d’actions existant pour les notifications de capacité** : Ce paramètre est le groupe d’actions qui sera déclenché pour les alertes basées sur la capacité. Vous devez créer ce paramètre au préalable. Le groupe d’actions peut envoyer des e-mails, des SMS ou d’autres formats de notification.

L’illustration suivante montre la configuration des alertes :  

![Illustration montrant la configuration des alertes à l’aide d’ANFCapacityManager.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

Après avoir installé ANFCapacityManager, vous pouvez vous attendre au comportement suivant : lorsqu’un pool de capacité ou un volume Azure NetApp Files est créé, modifié ou supprimé, l’application logique crée, modifie ou supprime automatiquement une règle d’alerte métrique basée sur la capacité et portant le nom `ANF_Pool_poolname` ou `ANF_Volume_poolname_volname`. 

### <a name="manage-capacity"></a>Gérer la capacité

En plus de la surveillance et des alertes, vous devez également intégrer une pratique de gestion de la capacité des applications pour gérer la consommation de capacité (accrue) d’Azure NetApp Files. Quand un pool de capacité ou un volume Azure NetApp Files se remplit, [une capacité supplémentaire peut être fournie à la volée sans interruption des applications](azure-netapp-files-resize-capacity-pools-or-volumes.md). Cette section décrit les différentes méthodes manuelles et automatisées permettant d’augmenter l’espace des volumes et des pools de capacité en fonction des besoins.
 
#### <a name="manual"></a>Manuel 

Vous pouvez utiliser le portail ou l’interface CLI pour augmenter manuellement la taille des pools de capacité ou des volumes. 

##### <a name="portal"></a>Portail 

Vous pouvez [changer la taille d’un volume](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) selon les besoins. La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool.

1. Dans le panneau Gérer le compte NetApp, cliquez sur **Volumes**.  
2. Cliquez avec le bouton droit sur le nom du volume que vous souhaitez redimensionner ou cliquez sur l’icône `…` à la fin de la ligne correspondant au volume pour afficher le menu contextuel. 
3. Utilisez les options du menu contextuel pour redimensionner ou pour supprimer le volume.   

   ![Capture d’écran montrant les options de menu contextuel pour un volume.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![Capture d’écran montrant la fenêtre Mettre à jour le quota de volume.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

Dans certains cas, le pool de capacité d’hébergement ne dispose pas d’une capacité suffisante pour redimensionner les volumes. Toutefois, vous pouvez [modifier la taille du pool de capacité](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) par incréments ou décréments de 1 Tio. La taille du pool de capacité ne peut pas être inférieure à 4 Tio. *Le redimensionnement du pool de capacités change la capacité Azure NetApp Files achetée.*

1. Dans le panneau Gérer le compte NetApp, cliquez sur le pool de capacités que vous voulez redimensionner.
2. Cliquez avec le bouton droit sur le nom du pool de capacité ou cliquez sur l’icône `…` à la fin de la ligne correspondant au pool de capacité pour afficher le menu contextuel.
3. Utilisez les options du menu contextuel pour redimensionner ou pour supprimer le pool de capacités.    

   ![Capture d’écran montrant les options de menu contextuel pour un pool de capacité.](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![Capture d’écran montrant la fenêtre Redimensionnement de pool.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI ou PowerShell

Vous pouvez utiliser les [outils CLI d’Azure NetApp Files](azure-netapp-files-sdk-cli.md#cli-tools), y compris Azure CLI et Azure PowerShell, pour modifier manuellement la taille des volumes ou des pools de capacité.  Les deux commandes suivantes peuvent être utilisées pour gérer les ressources de pool et de volume Azure NetApp Files :  

* [`az netappfiles pool`](https://docs.microsoft.com/cli/azure/netappfiles/pool?view=azure-cli-latest&preserve-view=true)
* [`az netappfiles volume`](https://docs.microsoft.com/cli/azure/netappfiles/volume?view=azure-cli-latest&preserve-view=true)

Pour gérer les ressources Azure NetApp Files à l’aide d’Azure CLI, vous pouvez ouvrir le portail Azure et sélectionner le lien **Azure Cloud Shell** en haut de la barre de menu : 

[ ![Capture d’écran montrant comment accéder au lien Cloud Shell.](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png) ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

Cette action ouvre Azure Cloud Shell :

[ ![Capture d’écran montrant la fenêtre Cloud Shell.](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png) ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

Les exemples suivants utilisent les commandes permettant d’[afficher](https://docs.microsoft.com/cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show&preserve-view=true) et de [mettre à jour](https://docs.microsoft.com/cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-update&preserve-view=true) la taille d’un volume :
 
[ ![Capture d’écran montrant l’utilisation de PowerShell pour afficher la taille du volume.](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png) ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[ ![Capture d’écran montrant l’utilisation de PowerShell pour mettre à jour la taille du volume.](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png) ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

Les exemples suivants utilisent les commandes permettant d’[afficher](https://docs.microsoft.com/cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-show&preserve-view=true) et de [mettre à jour](https://docs.microsoft.com/cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-update&preserve-view=true) la taille d’un pool de capacité :

[ ![Capture d’écran montrant l’utilisation de PowerShell pour afficher la taille du pool de capacité.](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png) ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[ ![Capture d’écran montrant l’utilisation de PowerShell pour mettre à jour la taille du pool de capacité.](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png) ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Automatisé  

Vous pouvez créer un processus automatisé pour gérer le changement de comportement.

##### <a name="rest-api"></a>API REST   

L’API REST pour le service Azure NetApp Files définit des opérations HTTP sur des ressources telles que le compte NetApp, le pool de capacité, les volumes et les captures instantanées. La spécification de l’API REST pour Azure NetApp Files est publiée via [la page Gestionnaire des ressources Azure NetApp Files de GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager). Vous trouverez un [exemple de code à utiliser avec les API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) dans GitHub.

Voir [Développer pour Azure NetApp Files avec l’API REST](azure-netapp-files-develop-with-rest-api.md). 

##### <a name="rest-api-using-powershell"></a>API REST avec PowerShell  

L’API REST pour le service Azure NetApp Files définit des opérations HTTP sur des ressources telles que le compte NetApp, le pool de capacité, les volumes et les captures instantanées. La [spécification de l’API REST pour Azure NetApp Files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) est publiée via GitHub.

Voir [Développer pour Azure NetApp Files avec l’API REST à l’aide de PowerShell](develop-rest-api-powershell.md).

##### <a name="capacity-management-using-anfcapacitymanager"></a>Gestion des capacités à l’aide d’ANFCapacityManager

ANFCapacityManager est une application logique Azure qui gère les règles d’alerte basées sur la capacité. Elle augmente automatiquement la taille des volumes pour éviter que vos volumes Azure NetApp Files ne manquent d’espace. Outre l’envoi d’alertes, elle peut permettre l’augmentation automatique de la taille des volumes et des pools de capacité pour éviter que vos volumes Azure NetApp Files ne manquent d’espace : 

* Facultativement, lorsqu’un volume Azure NetApp Files atteint le seuil spécifié du pourcentage de consommation, le quota de volume (taille) sera augmenté du pourcentage spécifié entre 10 et 100.  
* Si l’augmentation de la taille du volume dépasse la capacité du pool de capacité contenant, la taille du pool de capacité est également augmentée pour s’adapter à la nouvelle taille du volume.

Vous pouvez configurer le paramètre clé de gestion des capacités suivant :  

* **Augmentation en pourcentage de la croissance automatique** : Pourcentage de la taille de volume existante pour augmenter automatiquement un volume si celui-ci atteint le **seuil de pleine capacité en pourcentage** spécifié. La valeur 0 (zéro) désactive la fonctionnalité de croissance automatique. Une valeur comprise entre 10 et 100 est recommandée.

    ![Capture d’écran montrant la fenêtre Définir le pourcentage de croissance automatique du volume.](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>Questions fréquentes (FAQ) 

Cette section répond à quelques questions sur le passage au quota inconditionnel de volume. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>L’espace de capture instantanée est-il pris en compte dans la capacité utilisable ou approvisionnée d’un volume ?

Oui, la taille consommée de capture instantanée est comptabilisée dans l’espace approvisionné du volume. Si le volume est plein, deux options de correction peuvent être envisagées :

* Redimensionnez le volume comme décrit dans cet article.
* Supprimez les captures instantanées plus anciennes pour libérer de l’espace dans le volume d’hébergement.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Ce changement signifie-t-il que le comportement de croissance automatique du volume disparaîtra d’Azure NetApp Files ?

Une idée fausse, mais courante, est que la taille des *volumes* Azure NetApp Files augmente automatiquement lorsqu’ils arrivent à saturation. Les volumes étaient approvisionnés de manière dynamique avec une taille de 100 Tio, indépendamment du quota réel défini, tandis que la taille du *pool de capacité* sous-jacent augmentait automatiquement par incréments de 1 Tio. Ce changement portera sur la taille du *volume* (visible et utilisable) par rapport au quota défini, et la taille des *pools de capacité* n’augmenta plus automatiquement. Ce changement permet d’obtenir les rapports précis recherchés quant à l’espace et à la capacité côté client. Il permet d’éviter une consommation de capacité « incontrôlée ».

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Ce changement a-t-il un effet sur les volumes répliqués avec la réplication interrégion (préversion) ? 

Le quota inconditionnel de volume n’est pas appliqué sur les volumes de destination de la réplication.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Ce changement a-t-il un effet sur les métriques actuellement disponibles dans Azure Monitor ?

Les métriques du portail et les statistiques d’Azure Monitor refléteront avec précision le nouveau modèle d’allocation et d’utilisation.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Ce changement a-t-il un effet sur les limites de ressources pour Azure NetApp Files ?

Il n’y a aucune modification des limites de ressources pour Azure NetApp Files au-delà des modifications de quota décrites dans cet article.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Existe-t-il un exemple de workflow ANFCapacityManager ?  

Oui. Consultez la [page Exemple de workflow de croissance automatique de volume de GitHub](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md).

### <a name="is-anfcapacitymanager-microsoft-supported"></a>ANFCapacityManager est-elle prise en charge par Microsoft ?  

[L’application logique ANFCapacityManager est fournie telle quelle et n’est pas prise en charge par NetApp ni Microsoft](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer). Vous êtes encouragé à la modifier en fonction de votre environnement ou de vos besoins spécifiques. Vous devez tester la fonctionnalité avant de la déployer dans un environnement vital pour l’entreprise ou dans un environnement de production.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Comment signaler un bogue ou envoyer une demande de fonctionnalité pour ANFCapacityManger ?
Vous pouvez envoyer des bogues et des demandes de fonctionnalité en cliquant sur **New Issue** (Nouveau problème) dans la [page ANFCapacityManager de GitHub](https://github.com/ANFTechTeam/ANFCapacityManager/issues).

## <a name="next-steps"></a>Étapes suivantes
* [Redimensionner un pool de capacités ou un volume](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Métriques pour Azure NetApp Files](azure-netapp-files-metrics.md) 
