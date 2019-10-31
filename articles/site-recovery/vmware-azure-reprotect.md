---
title: Reprotéger des machines virtuelles à partir d’Azure vers un site local lors de la récupération d’urgence de machines virtuelles VMware et de serveurs physiques | Microsoft Docs
description: Après un basculement vers Azure lors d’une récupération d’urgence de machines virtuelles VMware et de serveurs physiques, découvrez comment effectuer une reprise automatique à partir d’Azure vers le site local.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: mayg
ms.openlocfilehash: cf1ccdf953781ca9b9bd17152f2cf32677997d12
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791799"
---
# <a name="reprotect-and-fail-back-machines-to-an-on-premises-site-after-failover-to-azure"></a>Reprotéger et effectuer une reprise automatique des machines vers un site local après le basculement vers Azure

Après un [basculement](site-recovery-failover.md) de machines virtuelles VMware ou de serveurs physiques locaux vers Azure, la première étape de restauration automatique vers votre site local implique de reprotéger les machines virtuelles Azure qui ont été créées pendant le basculement. Cet article décrit ce processus de reprotection. 

Pour une vue d’ensemble, regardez la vidéo suivante sur la procédure de basculement d’Azure vers un site local.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Avant de commencer

Si vous avez utilisé un modèle pour créer vos machines virtuelles, assurez-vous que chaque machine virtuelle possède son UUID pour les disques. Si l’UUID de la machine virtuelle locale est en conflit avec l’UUID du serveur cible maître (car ceux-ci ont été créés à partir du même modèle), la reprotection échoue. Déployez un autre serveur cible maître qui n’a pas été créé à partir du même modèle. Notez les informations suivantes :
- Si vous tentez d’effectuer une restauration automatique vers un autre serveur vCenter, vérifiez que le nouveau serveur vCenter et le serveur cible maître sont détectés. Un symptôme courant est que les banques de données ne sont pas accessibles/visibles dans la boîte de dialogue **Reprotéger**.
- Pour effectuer une réplication en retour vers votre site local, vous avez besoin d’une stratégie de restauration automatique. Cette stratégie est automatiquement créée lorsque vous créez une stratégie dans le sens initial. Notez les informations suivantes :
    - Cette stratégie est automatiquement associée au serveur de configuration lors de la création.
    - Cette stratégie n’est pas modifiable.
    - Les valeurs définies de la stratégie sont (Seuil d’objectif de point de récupération = 15 minutes, Rétention de point de récupération = 24 heures, Fréquence des captures instantanées de cohérence d’application = 60 minutes).
- Le serveur de configuration local doit être en cours d’exécution et connecté pendant la reprotection et la restauration automatique.
- Si un serveur vCenter gère les machines virtuelles vers lesquelles vous allez effectuer une restauration automatique, veillez à disposer des [autorisations nécessaires](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) pour la détection des machines virtuelles sur les serveurs vCenter.
- Supprimez les captures instantanées sur le serveur cible maître avant la reprotection. Si des captures instantanées sont présentes sur le serveur cible maître local ou sur la machine virtuelle, la reprotection échoue. Les captures instantanées sur la machine virtuelle sont fusionnées automatiquement lors d’un travail de reprotection.
- Toutes les machines virtuelles d’un groupe de réplication doivent avoir le même type de système d’exploitation (toutes Windows ou toutes Linux). Actuellement, les groupes de réplication avec des systèmes d’exploitation mixtes ne sont pas pris en charge pour la reprotection et la restauration automatique vers un site local. C’est parce que le serveur cible maître doit avoir le même système d’exploitation que la machine virtuelle. Toutes les machines virtuelles d’un groupe de réplication doivent avoir le même serveur cible maître. 
- La cible principale doit avoir la même version de système d’exploitation que les éléments répliqués ou une version supérieure.
- Un serveur de configuration est requis en local lorsque vous effectuez une restauration automatique. Lors de la restauration automatique, la machine virtuelle doit exister dans la base de données du serveur de configuration. Sinon, la restauration automatique échoue. Veillez à effectuer des sauvegardes régulières de votre serveur de configuration. En cas d’incident, restaurez le serveur avec la même adresse IP pour que la restauration automatique réussisse. 
- La reprotection et la restauration nécessitent un VPN de site à site (S2S) ou le peering privé ExpressRoute pour répliquer des données. Spécifiez le réseau de sorte que les machines virtuelles basculées dans Azure puissent atteindre (avec une requête ping) le serveur de configuration local. Vous devez déployer un serveur de processus dans le réseau Azure de la ou des machines virtuelles basculées. Ce serveur de processus doit également pouvoir communiquer avec le serveur de configuration local et le serveur cible maître.
- Si les adresses IP des éléments répliqués ont été conservées lors du basculement, la connectivité S2S ou ExpressRoute doit être établie entre les machines virtuelles Azure et la carte réseau de restauration automatique du serveur de configuration. Notez que la conservation des adresses IP nécessite que le serveur de configuration dispose de deux cartes réseau : une pour la connectivité des machines source et une pour la connectivité de la restauration automatique Azure. Cela permet d’éviter le chevauchement des plages d’adresses de sous-réseau des machines virtuelles source et basculées.
- Veillez à ouvrir les ports suivants pour le basculement et la restauration automatique :

    ![Ports pour le basculement et la restauration automatique](./media/vmware-azure-reprotect/failover-failback.png)

- Lisez tous les [prérequis pour les ports et la mise sur liste verte des URL](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="deploy-a-process-server-in-azure"></a>Déployer un serveur de processus dans Azure

Vous aurez besoin d’un serveur de processus dans Azure avant de restaurer automatiquement votre site local :

- Le serveur de processus reçoit les données de la ou des machines virtuelles protégées dans Azure et envoie ensuite les données au site local.
- Un réseau à faible latence est requis entre le serveur de processus et la machine virtuelle protégée. Par conséquent, il est recommandé de déployer un serveur de processus dans Azure. Les performances de réplication sont plus élevées si le serveur de processus est plus proche de la machine virtuelle de réplication (machine basculée dans Azure). 
- Pour une preuve de concept, vous pouvez utiliser le serveur de processus local et ExpressRoute avec un peering privé.

Pour déployer un serveur de processus dans Azure :

1. Si vous avez besoin de déployer un serveur de processus dans Azure, consultez [Configurer un serveur de processus dans Azure pour la restauration automatique](vmware-azure-set-up-process-server-azure.md).
2. Les machines virtuelles Azure envoient les données de réplication au serveur de processus. Configurez les réseaux de sorte que les machines virtuelles Azure puissent atteindre le serveur de processus.
3. N’oubliez pas que la réplication depuis Azure vers un site local peut se produire uniquement via le VPN S2S ou le peering privé de votre réseau ExpressRoute. Assurez-vous de disposer de suffisamment de bande passante sur ce réseau.

## <a name="deploy-a-separate-master-target-server"></a>Déployer un serveur cible maître distinct

Le serveur cible maître reçoit les données de la restauration automatique. Par défaut, le serveur cible maître s’exécute sur le serveur de configuration local. Toutefois, en fonction du volume de trafic restauré automatiquement, vous devrez peut-être créer un serveur cible maître distinct pour procéder à la restauration automatique. Voici comment en créer un :

* [Créez un serveur cible maître Linux](vmware-azure-install-linux-master-target.md) pour la restauration automatique des machines virtuelles Linux. Cela est nécessaire. Notez qu’un serveur cible maître sur une machine virtuelle Linux n’est pas pris en charge.
* Vous pouvez éventuellement créer un serveur cible maître distinct pour la restauration automatique de la machine virtuelle Windows. Pour ce faire, réexécutez l’installation unifiée et sélectionnez pour créer un serveur cible maître. [Plus d’informations](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) 

Après avoir créé un serveur cible maître, effectuez les tâches suivantes :

- Si la machine virtuelle est présente localement sur le serveur vCenter, le serveur cible maître a besoin d’accéder au fichier VMDK (Virtual Machine Disk) de la machine virtuelle locale. L’accès est obligatoire pour écrire les données répliquées sur les disques de la machine virtuelle. Assurez-vous que le magasin de données de la machine virtuelle locale est monté sur l’hôte du serveur cible maître avec accès en lecture/écriture.
- Si la machine virtuelle n’est pas présente en local sur le serveur vCenter, le service Site Recovery doit créer une nouvelle machine virtuelle pendant la reprotection. Cette machine virtuelle est créée sur l’hôte ESX sur lequel vous créez le serveur cible maître. Sélectionnez bien l’hôte ESX afin que la machine virtuelle de la restauration automatique soit créée sur l’hôte de votre choix.
- Vous ne pouvez pas utiliser Storage vMotion pour le serveur cible maître. L’utilisation de Storage vMotion pour le serveur cible maître risque d’entraîner l’échec de la restauration automatique. La machine virtuelle ne peut pas démarrer, car les disques ne sont pas disponibles. Pour éviter que cela n’arrive, excluez les serveurs cibles maîtres de votre liste vMotion.
- Si un serveur cible maître subit une tâche Storage vMotion après la reprotection, les disques de machine virtuelle protégés qui sont attachés au serveur cible maître migrent vers la cible de la tâche vMotion. Si vous essayez d’effectuer une restauration automatique après cela, le détachement des disques échoue car les disques sont introuvables. Cela devient difficile de trouver les disques dans vos comptes de stockage. Vous devez les rechercher manuellement et les attacher à la machine virtuelle. Après quoi, vous pouvez démarrer la machine virtuelle locale.
- Ajoutez un lecteur de rétention à votre serveur cible maître Windows existant. Ajoutez un disque et formatez le lecteur. Le lecteur de rétention est utilisé pour arrêter les points dans le temps où la machine virtuelle est répliquée vers le site local. Vous trouverez ci-après quelques critères d’un lecteur de rétention. Si ces critères ne sont pas remplis, le lecteur n’est pas listé pour le serveur cible maître :
    - Le volume n’est pas utilisé à d’autres fins, par exemple une cible de réplication.
    - Le volume n’est pas en mode verrouillage.
    - Le volume n’est pas un volume de cache. L’installation du serveur cible maître ne peut pas exister sur ce volume. Le volume d’une installation personnalisée du serveur de processus et du serveur maître cible n’est pas éligible pour un volume de rétention. Lorsque le serveur de processus et le serveur cible maître sont installés sur un volume, ce volume est un volume de cache du serveur cible maître.
    - Le type de système de fichiers du volume n’est pas FAT ni FAT32.
    - La capacité du volume est différente de zéro.
    - Le volume de rétention par défaut pour Windows est le volume R.
    - Le volume de rétention par défaut pour Linux est /mnt/retention.
- Vous devez ajouter un nouveau lecteur si vous utilisez une machine de serveur configuration/serveur de processus existante, ou une échelle ou une machine de serveur de processus/serveur cible maître. Le nouveau lecteur doit respecter les critères précédents. Si le lecteur de rétention n’est pas présent, il n’apparaît pas dans la liste déroulante de sélection du portail. Une fois que vous avez ajouté un lecteur au serveur cible maître local, 15 minutes sont nécessaires pour que le lecteur apparaisse dans la sélection sur le portail. Vous pouvez également actualiser le serveur de configuration si le lecteur n’apparaît pas après 15 minutes.
- Installez les outils VMware ou open-vm-tools sur le serveur maître cible. Sans les outils, les banques de données sur l’hôte ESXi du serveur cible maître ne peuvent pas être détectées.
- Définissez le paramètre `disk.EnableUUID=true` dans les paramètres de configuration de la machine virtuelle cible maître dans VMware. Si cette ligne n’existe pas, ajoutez-la. Ce paramètre est obligatoire pour fournir un UUID cohérent au disque VMDK afin que celui-ci monte correctement.
- L’hôte ESX sur lequel est créé le serveur cible maître doit avoir au moins une banque de données VMFS (Virtual Machine File System) attachée. Si aucune banque de données VMFS n’est attachée, l’entrée de la **banque de données** dans la page de reprotection est vide et vous ne pouvez pas continuer.
- Le serveur cible maître ne peut pas avoir de captures instantanées sur les disques. S’il existe des captures instantanées, l’opération de reprotection et de restauration automatique échoue.
- Le serveur cible maître ne peut pas avoir de contrôleur Paravirtual SCSI. Il peut uniquement s’agir d’un contrôleur LSI Logic. Sans contrôleur LSI Logic, la reprotection échoue.
- Le serveur cible maître peut avoir au maximum 60 disques attachés pour chaque instance. Si le nombre de machines virtuelles en cours de reprotection sur le serveur cible maître local dépasse le nombre total de 60 disques, la reprotection sur le serveur cible maître commence à échouer. Veillez à avoir suffisamment d’emplacements de disque sur le serveur cible maître, ou déployez des serveurs cibles maîtres supplémentaires.
    

## <a name="enable-reprotection"></a>Activer la reprotection

Après le démarrage d’une machine virtuelle dans Azure, il faut un certain temps à l’agent pour se réinscrire auprès du serveur de configuration (jusqu’à 15 minutes). Pendant ce temps, vous ne pouvez pas reprotéger, et un message d’erreur indique que l’agent n’est pas installé. Si cela se produit, attendez quelques minutes, puis recommencez la reprotection :


1. Sélectionnez **Coffre** > **Éléments répliqués**. Cliquez avec le bouton droit sur la machine virtuelle qui a basculé et sélectionnez **Reprotéger**. Ou, à partir des boutons de commande, sélectionnez la machine et **Reprotéger**.
2. Vérifiez que le sens de protection **D’Azure à local** est activé.
3. Dans les champs **Serveur cible maître** et **Serveur de processus**, sélectionnez le serveur cible maître local et le serveur de processus.  
4. Pour **Banque de données**, sélectionnez la banque de données dans laquelle vous souhaitez récupérer les disques locaux. Cette option est utilisée lorsque la machine virtuelle locale est supprimée et que vous devez créer de nouveaux disques. Cette option est ignorée si les disques existent déjà. Vous devez quand même spécifier une valeur.
5. Sélectionnez le lecteur de rétention.
6. La stratégie de restauration automatique est sélectionnée automatiquement.
7. Sélectionnez **OK** pour commencer la reprotection. Un travail est lancé pour répliquer la machine virtuelle à partir d’Azure vers le site local. Vous pouvez en suivre la progression sous l’onglet **Tâches** . Lorsque la reprotection réussit, la machine virtuelle entre dans un état protégé.

Notez les informations suivantes :
- Si vous souhaitez procéder à la récupération vers un autre emplacement (si la machine virtuelle locale est supprimée), sélectionnez le lecteur de rétention et la banque de données configurés pour le serveur cible maître. Si vous effectuez la restauration automatique vers le site local, les machines virtuelles VMware du plan de protection de la restauration automatique utilisent la même banque de données que le serveur cible maître. Une nouvelle machine virtuelle est créée dans vCenter.
- Si vous souhaitez récupérer la machine virtuelle Azure sur une machine virtuelle locale existante, montez les banques de données de la machine virtuelle locale avec un accès en lecture/écriture sur l’hôte ESXi du serveur cible maître.

    ![Reprotéger la boîte de dialogue](./media/vmware-azure-reprotect/reprotectinputs.png)

- Vous pouvez également effectuer la reprotection au niveau du plan de récupération. Un groupe de réplication peut uniquement être reprotégé via un plan de récupération. Lorsque vous reprotégez en utilisant un plan de récupération, vous devez fournir les valeurs pour chaque machine protégée.
- Utilisez le même serveur cible maître pour reprotéger un groupe de réplication. Si vous utilisez un autre serveur cible maître pour reprotéger un groupe de réplication, le serveur ne peut pas fournir un point dans le temps commun.
- La machine virtuelle locale est désactivée au cours de la reprotection. Cela permet de garantir la cohérence des données pendant la réplication. N’allumez pas la machine virtuelle une fois la reprotection terminée.



## <a name="common-issues"></a>Problèmes courants

- Si vous effectuez une découverte utilisateur vCenter en mode lecture seule et protégez des machines virtuelles, la protection réussit et le basculement fonctionne. Lors de la reprotection, le basculement échoue parce que les banques de données ne peuvent pas être découvertes. L’un des symptômes est que les banques de données ne sont pas répertoriées lors de la reprotection. Pour résoudre ce problème, vous pouvez mettre à jour les informations d’identification vCenter avec un compte approprié qui dispose des autorisations, puis réessayez le travail. 
- Lorsque vous restaurez automatiquement une machine virtuelle Linux et l’exécutez en local, vous pouvez constater que le package du Gestionnaire de réseau est désinstallé de la machine. Cette désinstallation se produit parce que le package du Gestionnaire de réseau est supprimé quand la machine virtuelle est restaurée dans Azure.
- Lorsqu’une machine virtuelle Linux est configurée avec une adresse IP statique puis restaurée automatiquement vers Azure, l’adresse IP est obtenue à partir de DHCP. Lorsque vous basculez en local, la machine virtuelle continue d’utiliser DHCP pour obtenir l’adresse IP. Connectez-vous manuellement à la machine et redéfinissez l’adresse IP avec une adresse statique, si nécessaire. Une machine virtuelle Windows peut reprendre son adresse IP statique.
- Si vous utilisez l’édition gratuite ESXi 5.5 ou l’édition gratuite de l’hyperviseur vSphere 6, le basculement réussit, mais pas la restauration automatique. Vous devrez effectuer une mise à niveau vers une licence d’évaluation d’un des programmes pour activer la restauration automatique.
- Si vous ne pouvez pas atteindre le serveur de configuration depuis le serveur de processus, utilisez Telnet pour vérifier la connectivité au serveur de configuration sur le port 443. Vous pouvez également essayer d’exécuter un test ping sur le serveur de configuration à partir du serveur de processus. Un serveur de processus doit également avoir une pulsation lorsqu’il est connecté au serveur de configuration.
- Un serveur Windows Server 2008 R2 SP1 qui est protégé en tant que serveur physique local ne peut pas être restauré à partir d’Azure sur un site local.
- Vous ne pouvez pas effectuer de restauration automatique dans les circonstances suivantes :
    - Vous avez effectué une migration des machines vers Azure. [Plus d’informations](migrate-overview.md#what-do-we-mean-by-migration)
    - Vous avez déplacé une machine virtuelle vers un autre groupe de ressources.
    - Vous avez supprimé la machine virtuelle Azure.
    - Vous avez désactivé la protection de la machine virtuelle.
    - Vous avez créé la machine virtuelle manuellement dans Azure. La machine doit avoir été initialement protégée localement et basculée vers Azure avant la reprotection.
    - Vous pouvez basculer uniquement vers un hôte ESXi. Vous ne pouvez pas restaurer automatiquement des machines virtuelles VMware ou des serveurs physiques vers des hôtes Hyper-V, des ordinateurs physiques ou des stations de travail VMware.


## <a name="next-steps"></a>Étapes suivantes

Une fois que la machine virtuelle est passée à l’état protégé, vous pouvez [commencer une restauration automatique](vmware-azure-failback.md). La restauration arrête la machine virtuelle dans Azure et démarre la machine virtuelle en local. Prévoyez un temps d’arrêt de l’application. Effectuez la restauration automatique lorsque l’application peut tolérer un temps d’arrêt.


