---
title: Planifier la capacité et la mise à l’échelle de la récupération après sinistre VMware vers Azure avec Azure Site Recovery | Microsoft Docs
description: Utilisez cet article pour planifier la capacité et la mise à l’échelle pendant la récupération après sinistre des machines virtuelles VMware vers Azure avec Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 6f644416a9e56009aadd0f8e1b217402d625af84
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788733"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Planifier la capacité et la mise à l’échelle pour la récupération d’urgence VMware vers Azure

Utilisez cet article pour déterminer la planification de la capacité et de la mise à l’échelle pendant la réplication de serveurs physiques et de machines virtuelles VMware locales vers Azure avec [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Comment dois-je commencer la planification de la capacité ?

Pour savoir quelles sont les exigences pour l’infrastructure Azure Site Recovery, collectez des informations sur votre environnement de réplication en exécutant l’outil [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) pour la réplication VMware. [En savoir plus](site-recovery-deployment-planner.md) sur cet outil. Cet outil fournit un rapport avec des informations complètes sur les machines virtuelles compatibles et non compatibles, sur les disques par machine virtuelle et sur l’activité des données par disque. L’outil récapitule également les exigences en bande passante du réseau pour satisfaire à l’objectif de point de récupération cible, et l’infrastructure Azure nécessaire pour la réussite de la réplication et du test de basculement.

## <a name="capacity-considerations"></a>Considérations relatives à la capacité

**Composant** | **Détails** |
--- | --- | ---
**Réplication** | **Taux de modification quotidien maximal** : une machine protégée ne peut utiliser qu’un serveur de processus, et un seul serveur de processus peut gérer un taux de modification de 2 To par jour au maximum. Par conséquent, 2 To est le taux de modification quotidien maximal de données pris en charge pour une machine protégée.<br/><br/> **Débit maximal** : un ordinateur répliqué peut appartenir à un compte de stockage dans Azure. Un compte de stockage standard peut gérer un maximum de 20 000 requêtes par seconde, et nous vous recommandons de conserver le nombre d’opérations d’entrée/sortie par seconde (IOPS) sur un ordinateur source à 20 000. Par exemple, si vous disposez d’une machine source à 5 disques et si chaque disque génère 120 IOPS (taille de 8 Ko) sur la machine source, la limite de 500 IOPS sur le disque Azure est respectée. (Le nombre de comptes de stockage requis est égal au nombre d’E/S par seconde total de la machine source divisé par 20 000).
**Serveur de configuration** | Le serveur de configuration doit être en mesure de gérer le taux de modification quotidien pour toutes les charges de travail en cours d’exécution sur des machines protégées, et il a besoin d’une bande passante suffisante pour permettre la réplication continue des données sur Stockage Azure.<br/><br/> À titre de meilleure pratique, placez le serveur de configuration sur le même réseau et le même segment de réseau local que les machines à protéger. Il peut se trouver sur un réseau différent, mais les ordinateurs à protéger doivent avoir une visibilité de réseau de couche 3.<br/><br/> Les recommandations en matière de taille du serveur de configuration sont résumées dans le tableau de la section suivante.
**Serveur de traitement** | Le premier serveur de processus est installé par défaut sur le serveur de configuration. Vous pouvez déployer des serveurs de processus supplémentaires pour étendre votre environnement. <br/><br/> le serveur de processus reçoit les données de réplication des machines protégées et les optimise grâce à la mise en cache, la compression et le chiffrement avant de les transmettre à Azure. Les données sont ensuite envoyées vers Azure. Le serveur de processus doit disposer de ressources suffisantes pour effectuer ces tâches.<br/><br/> Le serveur de processus utilise un cache disque. Utilisez un disque de cache distinct de 600 Go ou plus pour gérer les modifications apportées aux données stockées en cas de goulot d’étranglement ou de panne.

## <a name="size-recommendations-for-the-configuration-serverin-built-process-server"></a>Recommandations de taille pour le serveur de configuration/serveur de processus intégré

Chaque serveur de configuration déployé via le [modèle OVF](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) a un serveur de processus intégré. Les ressources du serveur de configuration, comme le processeur, la mémoire et l’espace libre, sont utilisées dans une autre proportion quand le serveur de processus intégré est utilisé pour protéger des machines virtuelles. Ainsi, les exigences varient quand le serveur de processus intégré est utilisé.
Un serveur de configuration où le serveur de processus intégré est utilisé pour protéger des charges de travail peut gérer jusqu’à 200 machines virtuelles en fonction des configurations suivantes :

**UC** | **Mémoire** | **Taille du disque cache** | **Taux de modification des données** | **Machines protégées**
--- | --- | --- | --- | ---
8 processeurs virtuels (2 sockets * 4 cœurs \@ 2,5 gigahertz [GHz]) | 16 Go | 300 Go | 500 Go ou moins | Répliquez moins de 100 machines.
12 processeurs virtuels (2 sockets * 6 cœurs \@ 2,5 GHz) | 18 Go | 600 Go | 500 Go à 1 To | Répliquez entre 100 et 150 machines.
16 processeurs virtuels (2 sockets * 8 cœurs \@ 2,5 GHz) | 32 Go | 1 To | 1 To à 2 To | Répliquez entre 150 et 200 machines.
Déployer un autre serveur de configuration via le [modèle OVF](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) | | | | Déployez un nouveau serveur de configuration si vous répliquez plus de 200 machines.
Déployer un autre [serveur de processus](vmware-azure-set-up-process-server-scale.md#download-installation-file) | | | > 2 To| Déployez un nouveau serveur de processus de scale-out si la quantité globale des modifications apportées quotidiennement aux données dépasse 2 To.

Où :

* Chaque ordinateur source est configuré avec 3 disques de 100 Go chacune.
* Nous avons utilisé le stockage de référence de 8 disques SAP de 10 K tr/min avec RAID 10 pour les mesures de disque cache.

## <a name="size-recommendations-for-the-configuration-server"></a>Recommandations de taille pour le serveur de configuration

Quand vous ne prévoyez pas d’utiliser le serveur de configuration comme serveur de processus, appliquez la configuration ci-dessous pour gérer jusqu’à 650 machines virtuelles.

**UC** | **RAM** | **Taille du disque de système d’exploitation** | **Taux de modification des données** | **Machines protégées**
--- | --- | --- | --- | ---
24 processeurs virtuels (2 sockets * 12 cœurs \@ 2,5 gigahertz [GHz])| 32 Go | 80 Go | Non applicable | Jusqu’à 650 machines virtuelles

Où chaque machine source est configurée avec 3 disques de 100 Go chacun.

Comme la fonctionnalité de serveur de processus n’est pas utilisée, la quantité de modifications des données n’est pas applicable. Pour conserver une capacité supérieure, vous pouvez basculer votre charge de travail depuis le serveur de processus intégré vers un autre processus de scale-out en suivant les instructions données [ici](vmware-azure-manage-process-server.md#balance-the-load-on-process-server).

## <a name="size-recommendations-for-the-process-server"></a>Recommandations de taille pour le serveur de processus

Le serveur de processus est le composant qui gère le processus de réplication des données dans Azure Site Recovery. Si la quantité de modifications quotidiennes est supérieure à 2 To, vous devez ajouter des serveurs de processus de scale-out pour gérer la charge de réplication. Pour augmenter la taille des instances, vous pouvez :

* Augmenter le nombre de serveurs de configuration en déployant via le [modèle OVF](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). Par exemple, vous pouvez protéger jusqu’à 400 machines avec deux serveurs de configuration.
* Ajouter des [serveurs de processus de scale-out](vmware-azure-set-up-process-server-scale.md#download-installation-file) et les utiliser pour gérer le trafic de réplication à la place (ou en plus) du serveur de configuration.

Le tableau suivant décrit un scénario dans lequel :

* Vous avez configuré un serveur de processus de scale-out.
* Vous avez configuré des machines virtuelles protégées pour qu’elles utilisent le serveur de processus de scale-out.
* Chaque ordinateur source protégé est configuré avec trois disques de 100 Go chacun.

**Serveur de traitement supplémentaire** | **Taille du disque cache** | **Taux de modification des données** | **Machines protégées**
--- | --- | --- | ---
4 processeurs virtuels (2 sockets * 2 cœurs \@ 2,5 GHz), 8 Go de mémoire | 300 Go | 250 Go ou moins | Répliquez 85 machines ou moins.
8 processeurs virtuels (2 sockets * 4 cœurs \@ 2,5 GHz), 12 Go de mémoire | 600 Go | 250 Go à 1 To | Répliquez entre 85 et 150 machines.
12 processeurs virtuels (2 sockets * 6 cœurs \@ 2,5 GHz), 24 Go de mémoire | 1 To | 1 To à 2 To | Répliquez entre 150 et 225 machines.

La façon dont vous allez mettre à niveau vos serveurs dépend de votre préférence pour un modèle de type Scale up ou Scale out.  Vous pouvez aboutir à une montée en puissance avec le développement de serveurs de configuration et de processus haut de gamme, ou à une montée en charge avec le déploiement d’un nombre de serveurs supérieur avec moins de ressources. Par exemple, si vous avez besoin de protéger 200 machines avec une quantité de modifications de données quotidienne globale de 1,5 To, vous pouvez effectuer l’une ou l’autre des actions suivantes :

* Configurer un seul serveur de processus avec 16 processeurs virtuels et 24 Go de RAM.
* Configurer deux serveurs de processus (2 x 8 processeurs virtuels et 2 * 12 Go de RAM).

## <a name="control-network-bandwidth"></a>Contrôler la bande passante réseau

Après avoir utilisé [l’outil Deployment Planner](site-recovery-deployment-planner.md) pour calculer la bande passante nécessaire à la réplication (réplication initiale et delta), vous pouvez contrôler la quantité de bande passante utilisée pour la réplication à l’aide de ces deux options :

* **Limiter la bande passante** : le trafic VMware qui est répliqué sur Azure passe par un serveur de processus spécifique. Vous pouvez limiter la bande passante sur les machines qui s’exécutent en tant que serveurs de processus.
* **Influer sur la bande passante** : vous pouvez influer sur la bande passante utilisée pour la réplication à l’aide de quelques clés de Registre :
  * La valeur de Registre **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** détermine le nombre de threads utilisés pour le transfert des données (réplication initiale ou différentielle) sur un disque. Une valeur plus élevée permet d’augmenter la bande passante réseau utilisée pour la réplication.
  * La valeur de Registre **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** détermine le nombre de threads utilisés pour le transfert des données pendant la restauration automatique.

### <a name="throttle-bandwidth"></a>Limiter la bande passante

1. Ouvrez le composant logiciel enfichable MMC Azure Backup sur la machine qui fait office de serveur de processus. Par défaut, un raccourci vers Backup est disponible sur le bureau ou dans le dossier suivant : C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.

    ![Capture d’écran de l’option enfichable MMC d’Azure Backup pour modifier les propriétés](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Sous l’onglet **Limitation**, sélectionnez la case **Activer la limitation de la bande passante sur Internet pour les opérations de sauvegarde**. Définissez les limites pour les heures ouvrées et non ouvrées. Les plages valides vont de 512 Kbits/s à 1023 Mbits/s.

    ![Capture d’écran de la boîte de dialogue Propriétés d’Azure Backup](./media/site-recovery-vmware-to-azure/throttle2.png)

Vous pouvez également utiliser l’applet de commande [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) pour définir la limitation. Voici un exemple :

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indique qu’aucune limitation n’est requise.

### <a name="influence-network-bandwidth-for-a-vm"></a>Influer sur la bande passante réseau d’une machine virtuelle

1. Dans le registre de machine virtuelle, accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Pour influer sur le trafic de la bande passante sur un disque de réplication, modifiez la valeur du paramètre **UploadThreadsPerVM**, ou créez la clé si elle n’existe pas.
   * Pour influer sur la bande passante utilisée pour le trafic lié à la restauration automatique à partir d’Azure, modifiez la valeur du paramètre **DownloadThreadsPerVM**.
2. La valeur par défaut est 4. Dans un réseau « surutilisé », ces clés de Registre doivent être modifiées par rapport aux valeurs par défaut. La valeur maximale est de 32. Surveillez le trafic pour optimiser la valeur.

## <a name="setup-azure-site-recovery-infrastructure-to-protect-more-than-500-virtual-machines"></a>Configurer l’infrastructure Azure Site Recovery pour protéger plus de 500 machines virtuelles

Avant de configurer l’infrastructure Azure Site Recovery, vous devez accéder à l’environnement pour mesurer les facteurs suivants : machines virtuelles compatibles, quantité quotidienne de modifications des données, bande passante réseau nécessaire pour l’objectif de point de récupération souhaité, nombre de composants Azure Site Recovery nécessaires, temps nécessaire pour effectuer la réplication initiale, etc.

1. Pour mesurer ces paramètres, veillez à exécuter le planificateur de déploiement sur votre environnement à l’aide de directives partagées [ici](site-recovery-deployment-planner.md).
2. Déployez un serveur de configuration avec les exigences mentionnées [ici](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server). Si votre charge de travail de production dépasse 650 machines virtuelles, déployez un autre serveur de configuration.
3. Selon la quantité de modifications des données quotidiennes mesurée, déployez [des serveurs de processus de scale-out](vmware-azure-set-up-process-server-scale.md#download-installation-file) en utilisant les directives concernant la taille spécifiées [ici](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Si vous pensez que le débit lié aux modifications des données pour un disque de machine virtuelle dépassera 2 Mbits/s, veillez à [configurer un compte de stockage Premium](tutorial-prepare-azure.md#create-a-storage-account). Comme le planificateur de déploiement est exécuté pour une période de temps spécifique, les pics des quantités de modifications des données pour d’autres périodes peuvent ne pas être capturés dans le rapport.
5. En fonction de l’objectif de point de récupération souhaité, [définissez la bande passante réseau](site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
6. Après la configuration de l’infrastructure, suivez les instructions publiées sous la [section des procédures](vmware-azure-set-up-source.md) pour activer la reprise d’activité sur votre charge de travail.

## <a name="deploy-additional-process-servers"></a>Déployer d’autres serveurs de traitement

Si vous devez monter en charge votre déploiement au-delà de 200 machines source ou que votre taux d’évaluation quotidien total dépasse 2 To, vous avez besoin de serveurs de traitement supplémentaires pour gérer le volume de trafic. Pour configurer le serveur de processus, suivez les instructions de [cet article](vmware-azure-set-up-process-server-scale.md). Après avoir configuré le serveur, vous pouvez migrer les machines source pour les utiliser.

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrez les machines pour utiliser le nouveau serveur de traitement

1. Dans **Paramètres** > **Serveurs Site Recovery**, cliquez sur le serveur de configuration, puis développez **Serveurs de processus**.

    ![Capture d’écran de la boîte de dialogue du serveur de traitement](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Cliquez avec le bouton droit sur le serveur de processus en cours d’utilisation et sélectionnez **Basculer**.

    ![Capture d’écran de la boîte de dialogue Serveur de configuration](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. Dans **Sélectionner un serveur de traitement cible**, sélectionnez le nouveau serveur de processus à utiliser, puis les machines virtuelles que le serveur va gérer. Cliquez sur l’icône d’information pour obtenir des informations sur le serveur. Pour vous aider à prendre des décisions quant à la charge, l’espace moyen nécessaire à la réplication de chaque machine virtuelle sélectionnée vers le nouveau serveur de processus s’affiche. Cliquez sur la coche pour commencer la réplication vers le nouveau serveur de traitement.

## <a name="deploy-additional-master-target-servers"></a>Déployer des serveurs maîtres cibles supplémentaires

Vous aurez besoin d’un serveur maître cible supplémentaire au cours des scénarios suivants

1. Si vous essayez de protéger une machine virtuelle Linux.
2. Si le serveur cible maître disponible sur le serveur de configuration n’a pas accès au magasin de données de la machine virtuelle.
3. Si le nombre total de disques sur le serveur maître cible (nbre de disques locaux sur le serveur + disques à protéger) dépasse 60.

Pour ajouter un nouveau serveur maître cible pour une **machine virtuelle Linux**, [cliquez ici](vmware-azure-install-linux-master-target.md).

Pour la **machine virtuelle Windows**, suivez les instructions ci-dessous.

1. Accédez à **coffre Recovery Services** > **Infrastructure Site Recovery** > **Serveurs de Configuration**.
2. Cliquez sur le serveur de configuration requis > **+ Serveur maître cible**.![ add-master-target-server.png](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Téléchargez la configuration unifiée et exécutez-la sur la machine virtuelle pour configurer le serveur maître cible.
4. Choisissez **Installer le serveur maître cible** > **Suivant**. ![choose-MT.PNG](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Choisissez l’emplacement d’installation par défaut > cliquez sur **Installer**. ![MT-installation](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Cliquez sur **Procéder à la Configuration** pour enregistrer le serveur maître avec le serveur de configuration. ![MT-proceed-configuration.PNG](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Entrez l’adresse IP du serveur de configuration et la phrase secrète. [Cliquez ici](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) pour savoir comment générer une phrase secrète.![cs-ip-passphrase](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Cliquez sur **Enregistrer** et, une fois l’enregistrement effectué, cliquez sur **Terminer**.
9. Une fois l’enregistrement réussi, ce serveur est répertorié dans le portail sous **coffre Recovery Services** > **Infrastructure Site Recovery** > **Serveurs de configuration** > Serveurs maîtres cibles du serveur de configuration approprié.

 >[!NOTE]
 >Vous pouvez également télécharger la dernière version de la configuration unifiée du serveur maître cible pour Windows [ici](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Étapes suivantes

Télécharger et exécuter l’outil [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).
