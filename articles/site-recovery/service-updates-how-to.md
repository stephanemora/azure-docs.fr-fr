---
title: Mises à jour Azure Site Recovery | Microsoft Docs
description: Fournit une vue d’ensemble des mises à jour de service et explique comment mettre à niveau les composants utilisés dans Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/05/2019
ms.author: rajanaki
ms.openlocfilehash: 36d0a976b73fe5b6f89755ce790b919468744179
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091645"
---
# <a name="service-updates-in-azure-site-recovery"></a>Mises à jour de service dans Azure Site Recovery
En tant qu’organisation, vous devez déterminer comment vous allez protéger vos données ainsi que les applications/charges de travail en cours d’exécution lors d’interruptions planifiées ou non. Azure Site Recovery contribue à la mise en œuvre de votre stratégie BCDR (continuité d’activité et reprise d’activité). En effet, en cas de panne du site, il maintient les applications en cours d’exécution sur des machines virtuelles et des serveurs physiques disponibles. Site Recovery réplique les charges de travail en cours d’exécution sur des machines virtuelles et des serveurs physiques, afin que ces charges restent disponibles à un second emplacement si jamais l’emplacement principal n’est pas disponible. Ce service récupère les charges de travail sur le site principal lorsqu’il fonctionne à nouveau.

Site Recovery peut gérer la réplication pour :

- [Réplication de machines virtuelles Azure entre régions Azure](azure-to-azure-tutorial-dr-drill.md).
- Les machines virtuelles et les serveurs physiques locaux qui répliquent des données sur Azure ou sur un site secondaire.
Pour en savoir plus, consultez [cette documentation](https://docs.microsoft.com/azure/site-recovery).

Azure Site Recovery publie régulièrement des mises à jour de service pour ajouter de nouvelles fonctionnalités, améliorer la matrice de support, corriger d’éventuels bogues, etc. Pour rester à jour et profiter des toutes dernières fonctionnalités, améliorations et corrections de bogues, le cas échéant, nous conseillons aux utilisateurs de toujours mettre à jour les composants Azure Site Recovery vers les dernières versions. 
 
## <a name="support-statement-for-azure-site-recovery"></a>Déclaration de support pour Azure Site Recovery 

> [!IMPORTANT]
> **À chaque publication d’une nouvelle version « N » d’un composant Azure Site Recovery, toutes les versions inférieures à « N-4 » sont considérées comme non couvertes par le support**. Il est donc toujours recommandé d’effectuer la mise à niveau vers les dernières versions disponibles.

> [!IMPORTANT]
> Le support officiel des mises à niveau couvre les versions allant de N-4 à N (N étant la dernière version). Si vous utilisez la version N-6, vous devez d’abord la mettre à niveau vers la version N-4, puis vers la version N.

### <a name="upgrading-when-the-difference-between-current-version-and-latest-released-version-is-greater-than-4"></a>Mise à niveau quand la différence entre la version actuelle et la dernière version publiée est supérieure à 4

1. Dans un premier temps, mettez à niveau le composant actuellement installé, par exemple de la version N vers N+4, puis passez à la version compatible suivante. Supposons que la version actuelle soit 9.24 et que vous soyez à 9.16. Effectuez d’abord la mise à niveau vers 9.20, puis vers 9.24.
2. Suivez le même processus pour tous les composants en fonction du scénario.

### <a name="support-for-latest-oskernel-versions"></a>Prise en charge des dernières versions du système d’exploitation/noyau

> [!NOTE]
> Si vous avez une fenêtre de maintenance planifiée et qu’un redémarrage en fait partie, nous vous recommandons tout d’abord de mettre à niveau les composants de Site Recovery et de procéder au reste des activités planifiées.

1. Avant de mettre à niveau les versions de votre noyau/système d’exploitation, vérifiez d’abord si la version cible est prise en charge par Azure Site Recovery. Vous trouverez des informations dans notre documentation sur les machines virtuelles Azure, [VMware](vmware-physical-azure-support-matrix.md) et Hyper-V dans
2. Consultez nos [mises à jour de service](https://azure.microsoft.com/updates/?product=site-recovery) pour connaître, pour chaque composant Site Recovery, la version qui prend en charge la version spécifique à laquelle vous souhaitez passer.
3. Commencez par effectuer la mise à niveau vers la dernière version de Site Recovery.
4. À présent, mettez à niveau le système d’exploitation/noyau vers les versions désirées.
5. Effectuez un redémarrage.
6. Cela garantit que le système d’exploitation/noyau de vos machines est mis à niveau vers la dernière version et que les dernières modifications de Site Recovery nécessaires pour prendre en charge la nouvelle version sont également chargées sur la machine source.



## <a name="azure-vm-disaster-recovery-to-azure"></a>Récupération d’urgence de machine virtuelle Azure vers Azure
Dans ce scénario, nous vous recommandons vivement d’[activer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-autoupdate) les mises à jour automatiques. Vous pouvez choisir d’autoriser Site Recovery à gérer les mises à jour comme suit :

- Dans le cadre de l’étape d’activation de la réplication
- Activer/désactiver les paramètres de mise à jour de l’extension dans le coffre

Si vous avez choisi de gérer manuellement les mises à jour, effectuez les étapes suivantes :

1. Accédez au portail Azure, puis accédez à votre coffre Recovery Services.
2. Accédez au volet Éléments répliqués dans le portail Azure pour le coffre Recovery services.
3. Cliquez sur la notification suivante en haut de l’écran :
    
    *Une nouvelle mise à jour de l’agent de réplication Site Recovery est disponible.*
    
    *Cliquez pour installer ->*

4. Sélectionnez les machines virtuelles auxquelles vous souhaitez appliquer la mise à jour, puis cliquez sur **OK**.

## <a name="between-two-on-premises-vmm-sites"></a>Entre deux sites VMM locaux
1. Téléchargez le dernier correctif cumulatif pour le fournisseur Microsoft Azure Site Recovery.
2. Installez d’abord le correctif cumulatif sur le serveur VMM local qui gère le site de récupération.
3. Une fois le site de récupération mis à jour, installez le correctif cumulatif sur le serveur VMM qui gère le site principal.

> [!NOTE]
> Si le VMM est hautement disponible (VMM en cluster), veillez à installer la mise à niveau sur tous les nœuds du cluster où le service VMM est installé.

## <a name="between-an-on-premises-vmm-site-and-azure"></a>Entre un site VMM local et Azure
1. Téléchargez le correctif cumulatif pour le fournisseur Microsoft Azure Site Recovery.
2. Installez le correctif cumulatif sur le serveur VMM local.
3. Installez le dernier agent MARS sur tous les hôtes Hyper-V.

> [!NOTE]
> Si votre VMM est hautement disponible (VMM en cluster), veillez à installer la mise à niveau sur tous les nœuds du cluster où le service VMM est installé.

## <a name="between-an-on-premises-hyper-v-site-and-azure"></a>Entre un site Hyper-V local et Azure

1. Téléchargez le correctif cumulatif pour le fournisseur Microsoft Azure Site Recovery.
2. Installez le fournisseur sur chacun des nœuds des serveurs Hyper-V que vous avez inscrits dans Azure Site Recovery.

> [!NOTE]
> Si votre Hyper-V est un serveur Hyper-V en cluster hôte, veillez à installer la mise à niveau sur tous les nœuds du cluster.

## <a name="between-an-on-premises-vmware-or-physical-site-to-azure"></a>Entre un VMware local ou un site physique et Azure

Avant de procéder aux mises à jour, reportez-vous aux [Informations de prise en charge de Site Recovery](#support-statement-for-azure-site-recovery) pour connaître le chemin de mise à niveau.

1. En fonction de votre version actuelle et des informations de prise en charge mentionnées ci-dessus, installez d'abord la mise à jour sur votre serveur de gestion local en suivant les instructions fournies [ici](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). Il s'agit du serveur détenant les rôles Serveur de configuration et Serveur de processus.
2. Si vous disposez de serveurs de processus avec scale-out, mettez-les à jour en suivant les instructions fournies [ici](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Ensuite, pour mettre à jour l'agent de mobilité sur chaque élément protégé, accédez au portail Azure, puis à la page **Éléments protégés** > **Éléments répliqués**. Sélectionnez une machine virtuelle dans cette page. Sélectionnez le bouton **Mettre à jour l’agent** qui apparaît en bas de la page pour chaque machine virtuelle. L’agent du service Mobilité est alors mis à jour sur toutes les machines virtuelles protégées.

### <a name="reboot-of-source-machine-after-mobility-agent-upgrade"></a>Redémarrage de la machine source après la mise à niveau de l'agent de mobilité

Un redémarrage est recommandé après chaque mise à niveau de l’agent Mobilité pour garantir le chargement de toutes les dernières modifications sur la machine source. Toutefois, il **n’est pas obligatoire**. Si la différence entre la version de l’agent durant le dernier redémarrage et la version actuelle est supérieure à 4, un redémarrage est obligatoire. Pour obtenir des explications détaillées, consultez le tableau suivant :

|**Version de l’agent durant le dernier redémarrage** | **Mise à niveau vers** | **Redémarrage obligatoire ?**|
|---------|---------|---------|
|9.16 |  9.18 | Facultatif|
|9.16 | 9.19 | Facultatif|
| 9.16 | 9.20 | Facultatif
 | 9.16 | 9.21 | Oui, effectuez d’abord la mise à niveau vers 9.20, puis redémarrez avant de procéder à la mise à niveau vers 9.21 car la différence entre les versions (9.16 quand le dernier redémarrage a été effectué et la version cible 9.21) est supérieure à 4.

## <a name="links-to-currently-supported-update-rollups"></a>Liens vers les correctifs cumulatifs actuellement pris en charge

|Correctif cumulatif  |Fournisseur  |Installation unifiée| OVF  |MARS|
|---------|---------|---------|---------|--------|
|[Correctif cumulatif 34](https://support.microsoft.com/en-us/help/4490016/update-rollup-34-for-azure-site-recovery) – Correctif logiciel     |   5.1.3950.0  |  9.22.5142.1   |  5.1.3950.0  | 2.0.9155.0
|[Correctif cumulatif 33](https://support.microsoft.com/en-us/help/4489582/update-rollup-33-for-azure-site-recovery)     |   5.1.3900.0  |  9.22.5109.1   |  5.1.3900.0  | 2.0.9155.0
|[Correctif cumulatif 32](https://support.microsoft.com/en-us/help/4485985/update-rollup-32-for-azure-site-recovery)     |   5.1.3800.0  |  9.21.5091.1   |  5.1.3800.0  |2.0.9144.0
|[Correctif cumulatif 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)     |     5.1.3700.0      |   9.20.5051.1      |     5.1.3700.0    |2.0.9144.0
|[Correctif cumulatif 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)     |    5.1.3650.0   |   9.19.5007.1    |     5.1.3650.0    |2.0.9139.0
|[Correctif cumulatif 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)     |   5.1.3650.0      |   9.19.4973.1     |     5.1.3700.0    |2.0.9131.0
|[Mise à jour cumulative 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)     |  5.1.3600.0      |    9.19.4973.1     |  5.1.3600.0       |2.0.9131.0
| [Correctif cumulatif 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)       |   5.1.3550.0      |    9.18.4946.1     | 5.1.3550.0         |2.0.9125.0


## <a name="previous-update-rollups"></a>Correctifs cumulatifs précédents
- [Correctif cumulatif 26](https://support.microsoft.com/help/4344054/update-rollup-26-for-azure-site-recovery)  
- [Correctif cumulatif 25](https://support.microsoft.com/help/4278275/update-rollup-25-for-azure-site-recovery) 
- [Correctif cumulatif 23](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 
- [Correctif cumulatif 22](https://support.microsoft.com/help/4072852/update-rollup-22-for-azure-site-recovery) 
- [Correctif cumulatif 21](https://support.microsoft.com/help/4051380/update-rollup-21-for-azure-site-recovery) 
- [Correctif cumulatif 20](https://support.microsoft.com/help/4041105/update-rollup-20-for-azure-site-recovery) 
- [Correctif cumulatif 19](https://support.microsoft.com/help/4034599/update-rollup-19-for-azure-site-recovery) 
