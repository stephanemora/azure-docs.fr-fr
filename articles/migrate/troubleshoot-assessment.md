---
title: Détecter un problème d’évaluation et de visualisation des dépendances dans Azure Migrate
description: Obtenez de l’aide pour l’évaluation et la visualisation des dépendances dans Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 78e54543c94cd6e8434023b61516242c2491f353
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863596"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Résoudre les problèmes de l’évaluation/la visualisation des dépendances

Cet article vous aide à résoudre des problèmes liés à l’évaluation et à la visualisation des dépendances grâce à [Azure Migrate : découverte et évaluation](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problèmes de préparation à l’évaluation

Corrigez les problèmes de préparation à l’évaluation comme suit :

**Problème** | **Correctif**
--- | ---
Type de démarrage non pris en charge | Azure ne prend pas en charge les machines virtuelles associées au type de démarrage EFI. Nous vous recommandons de convertir le type de démarrage en BIOS avant d’effectuer une migration. <br/><br/>Vous pouvez utiliser Azure Migrate Server Migration pour gérer la migration de ces machines virtuelles. Celui-ci permet de convertir le type de démarrage de la machine virtuelle en BIOS au cours de la migration.
Système d’exploitation Windows pris en charge de manière conditionnelle | Le système d’exploitation n’est plus pris en charge et a besoin d’un programme Custom Support Agreement (CSA) pour bénéficier d’une [prise en charge dans Azure](/troubleshoot/azure/virtual-machines/server-software-support). Envisagez de mettre à niveau avant d’effectuer la migration vers Azure. Consultez les informations relatives à la [préparation des serveurs exécutant Windows Server 2003](prepare-windows-server-2003-migration.md) pour la migration vers Azure.
Système d’exploitation Windows non pris en charge | Azure ne prend en charge que [certaines versions du système d’exploitation Windows](/troubleshoot/azure/virtual-machines/server-software-support). Envisagez de mettre à niveau le serveur avant d’opérer la migration vers Azure.
Systèmes d’exploitation Linux approuvés sous condition | Azure n’approuve que [certaines versions du système d’exploitation Linux](../virtual-machines/linux/endorsed-distros.md). Envisagez de mettre à niveau le serveur avant d’opérer la migration vers Azure. Pour plus d’informations, voyez également [ici](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment).
Systèmes d’exploitation Linux non approuvés | Le serveur peut démarrer dans Azure, mais Azure ne fournit aucun support pour le système d’exploitation. Envisagez de mettre à niveau vers une [version approuvée de Linux](../virtual-machines/linux/endorsed-distros.md) avant d’effectuer la migration vers Azure.
Système d’exploitation inconnu | Le système d’exploitation de la machine virtuelle a été spécifié comme étant du type « Autre » dans vCenter Server. Ce comportement empêche Azure Migrate de vérifier la préparation de la machine virtuelle à Azure. Vérifiez que le système d’exploitation est [pris en charge](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements) par Azure avant d’opérer la migration du serveur.
Version de bit non prise en charge | Les machines virtuelles dotées d’un système d’exploitation 32 bits peuvent être démarrées dans Azure. Toutefois, il est recommandé d’effectuer une mise à niveau vers la version 64 bits avant de procéder à la migration vers Azure.
Nécessite un abonnement Microsoft Visual Studio. | Le serveur exécute un système d’exploitation client Windows qui est pris en charge uniquement via un abonnement Visual Studio.
Nous n’avons pas trouvé de machine virtuelle correspondant au niveau de performance de stockage nécessaire | Les performances de stockage (IOPS et débit) requises pour le serveur dépassent le niveau de prise en charge de machine virtuelle Azure. Réduisez les besoins de stockage du serveur avant la migration.
Nous n’avons pas trouvé de machine virtuelle correspondant au niveau de performance réseau nécessaire | Les performances réseau (entrée/sortie) requises pour le serveur dépassent le niveau de prise en charge de machine virtuelle Azure. Réduisez les exigences de mise en réseau du serveur.
Nous n’avons pas trouvé de machine virtuelle à l’emplacement spécifié | Utilisez un emplacement cible différent avant la migration.
Un ou plusieurs disques ne sont pas adaptés | Un ou plusieurs disques attachés à la machine virtuelle ne répondent pas aux exigences Azure.A<br/><br/> Azure Migrate : découverte et évaluation ne prend pas en charge les disques SSD Ultra et évalue les disques en fonction des limites appliquées aux disques managés Premium (32 To).<br/><br/> Pour chaque disque attaché à la machine virtuelle, assurez-vous que la taille du disque est < 64 To (pris en charge par les disques SSD Ultra).<br/><br/> Si ce n’est pas le cas, réduisez la taille du disque avant d’effectuer la migration vers Azure, ou utilisez plusieurs disques dans Azure et [associez-les](../virtual-machines/premium-storage-performance.md#disk-striping) pour bénéficier de limites de stockage supérieures. Vérifiez que les performances (IOPS et débit) nécessaires à chaque disque sont prises en charge par les [disques de la machine virtuelle managée](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) Azure.
Un ou plusieurs adaptateurs réseau ne sont pas adaptés | Supprimez les cartes réseau non utilisées du sereveur avant la migration.
Le nombre de disques dépasse la limite autorisée | Supprimez les disques non utilisés du serveur avant la migration.
La taille du disque dépasse la limite autorisée | Azure Migrate : découverte et évaluation ne prend pas en charge les disques SSD Ultra, et évalue les disques en fonction des limites appliquées aux disques Premium (32 To).<br/><br/> Toutefois, Azure prend en charge les disques d’une taille maximale de 64 To (prise en charge par les disques SSD Ultra). Réduisez la taille des disques pour passer en dessous des 64 To avant la migration, ou utilisez plusieurs disques Azure et [associez-les](../virtual-machines/premium-storage-performance.md#disk-striping) pour bénéficier de limites de stockage plus élevées.
Disque indisponible dans l'emplacement spécifié | Assurez-vous que le disque se trouve dans votre emplacement cible avant la migration.
Disque indisponible pour la redondance spécifiée | Le disque doit utiliser le type de stockage de redondance défini dans les paramètres d'évaluation (LRS par défaut).
Impossible de déterminer l’adéquation du disque en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Nous n'avons pas trouvé de machine virtuelle avec les cœurs et la mémoire requis | Azure n’a pas trouvé de type de machine virtuelle adapté. Réduisez la mémoire et le nombre de cœurs du serveur local avant d’opérer la migration.
Impossible de déterminer l’adéquation de la machine virtuelle en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Impossible de déterminer l’adéquation d’un ou plusieurs disques en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Impossible de déterminer l’adéquation d’une ou plusieurs cartes réseau en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Aucune taille de machine virtuelle trouvée pour l’instance réservée de devise de l’offre | Le serveur a été marqué comme ne convenant pas parce que la taille de la machine virtuelle est introuvable pour la combinaison sélectionnée de RI, d’offre et de devise. Modifiez les propriétés d’évaluation et choisissez les combinaisons valides, puis recalculez l’évaluation. 
Préparé pour le protocole Internet sous conditions | Applicable uniquement aux évaluations Azure VMware Solution (AVS). AVS ne prend actuellement pas en charge le facteur de l’adresse Internet IPv6. Si votre serveur est détecté avec IPv6, contactez l’équipe AVS pour obtenir des conseils de correction .

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>L’outil de migration suggéré dans l’évaluation AVS basée sur l’importation est inconnu

Pour des serveurs importés via un fichier CSV, l’outil de migration par défaut dans l’évaluation AVS est inconnu. Pour des serveurs dans un environnement VMware, il est toutefois recommandé d’utiliser la solution VMware Hybrid Cloud Extension (HCX). [En savoir plus](../azure-vmware/tutorial-deploy-vmware-hcx.md)

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Les machines virtuelles Linux sont « préparées sous conditions » dans une évaluation de machine virtuelle Azure

En ce qui concerne les machines virtuelles Hyper-V et VMware, l’évaluation de machine virtuelle Azure marque les machines virtuelles Linux comme « préparées sous conditions » en raison d’un écart connu. 

- L’écart l’empêche de détecter la version mineure du système d’exploitation Linux installé sur les machines virtuelles locales.
- Par exemple, pour RHEL 6.10, l’évaluation de machine virtuelle Azure ne détecte actuellement que RHEL 6 comme version du système d’exploitation. Cela est dû au fait que le serveur vCenter ainsi que l’hôte Hyper-V ne fournissent pas de version de noyau pour les systèmes d’exploitation de la machine virtuelle Linux.
-  Dans la mesure où Azure approuve uniquement certaines versions de Linux, les machines virtuelles Linux sont actuellement marquées comme « Prêtes sous condition » dans l’évaluation de machine virtuelle Azure.
- Vous pouvez déterminer si le système d’exploitation Linux exécuté sur la machine virtuelle locale est approuvé dans Azure en consultant la [prise en charge de Linux par Azure](../virtual-machines/linux/endorsed-distros.md).
-  Une fois que vous avez vérifié la distribution approuvée, vous pouvez ignorer cet avertissement.

Cet écart peut être résolu en activant la [découverte des applications](./how-to-discover-applications.md) sur les machines virtuelles VMware. L’évaluation de machine virtuelle Azure utilise le système d’exploitation détecté de la machine virtuelle à l’aide des informations d’identification d’invité fournies. Les données du système d'exploitation identifient les informations correctes sur le système d'exploitation pour les machines virtuelles Windows et Linux.

## <a name="operating-system-version-not-available"></a>Version de système d'exploitation non disponible

Pour les serveurs physiques, les informations sur la version mineure du système d’exploitation doivent être disponibles. Dans le cas contraire, contactez le support Microsoft. Pour des serveurs dans un environnement VMware, Azure Migrate utilise les informations du système d’exploitation spécifiées pour la machine virtuelle dans vCenter Server. Toutefois, vCenter Server ne fournit pas la version mineure pour les systèmes d’exploitation. Pour découvrir la version mineure, vous devez configurer la [découverte d’application](./how-to-discover-applications.md). Pour les machines virtuelles Hyper-V, la détection des versions mineures du système d’exploitation n’est pas prise en charge. 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Les références SKU Azure sont plus volumineuses qu’en local dans une évaluation de machine virtuelle Azure

L’évaluation de machine virtuelle Azure peut recommander des références SKU de machine virtuelle Azure avec davantage de cœurs et de mémoire que l’allocation locale actuelle en fonction du type d’évaluation :

- La recommandation en matière de référence SKU de machine virtuelle varie en fonction des propriétés d’évaluation.
- Cela est affecté par le type d’évaluation que vous effectuez dans l’évaluation de machine virtuelle Azure, selon que celle-ci est effectuée *En fonction des performances* ou *Localement*.
- Pour les évaluations en fonction des performances, l’évaluation de machine virtuelle Azure prend en compte les données d’utilisation des machines virtuelles locales (processeur, mémoire, utilisation des disques et du réseau) afin de déterminer la référence SKU de machine virtuelle cible appropriée pour vos machines virtuelles locales. Il ajoute également un facteur de confort pour déterminer l’utilisation effective.
- Pour le dimensionnement local, les données de performances ne sont pas prises en compte et la référence SKU cible est recommandée en fonction de la répartition locale.

Pour illustrer la façon dont cela peut affecter les recommandations, prenons un exemple :

Nous disposons d’une machine virtuelle locale avec quatre cœurs et huit Go de mémoire, avec une utilisation de l’UC de 50 % et une utilisation de la mémoire de 50 %, ainsi qu’un facteur de confort spécifié de 1,3.

-  Si l’évaluation est définie sur **Localement**, il est recommandé d’utiliser une référence SKU de machine virtuelle Azure avec 4 cœurs et 8 Go de mémoire.
- Si l’évaluation est basée sur les performancess, d’après l’utilisation effective de l’UC et de la mémoire (50 % de 4 cœurs * 1,3 = 2,6 cœurs et 50 % de 8 Go de mémoire * 1,3 = 5,3 Go de mémoire), la référence SKU de machine virtuelle la moins coûteuse avec quatre cœurs (nombre de cœurs pris en charge le plus proche) et huit Go de mémoire (taille de mémoire prise en charge la plus proche) est celle qui est recommandée.
- [En savoir](concepts-assessment-calculation.md#types-of-assessments) plus sur le dimensionnement de l’évaluation.

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Pourquoi les références (SKU) de disque Azure recommandées à la suite d’une évaluation de machine virtuelle Azure sont-elles plus volumineuses qu’en local ?

L’évaluation de machine virtuelle Azure peut recommander un disque plus volumineux en fonction du type d’évaluation.
- Le dimensionnement de disque dépend de deux propriétés d’évaluation : le critère de dimensionnement et le type de stockage.
- Si le critère de dimensionnement est **En fonction des performances** et que le type de stockage est défini sur **Automatique**, les valeurs de débit et d’IOPS du disque sont prises en compte lors de l’identification du type de disque cible (HDD Standard, SSD Standard ou Premium). Une référence SKU de disque parmi celles du type approprié est ensuite recommandée conformément aux exigences de taille du disque local.
- Si le critère de dimensionnement est **En fonction des performances** et si le type de stockage est **Premium**, une référence SKU de disque Premium dans Azure est recommandée conformément aux exigences de débit, d’IOPS et de taille du disque local. La même logique est utilisée pour effectuer le dimensionnement du disque quand le critère de dimensionnement est défini sur **Localement** et que le type de stockage est **HDD Standard**, **SSD Standard** ou **Premium**.

Par exemple, si vous avez un disque local avec 32 Go de mémoire, mais que la valeur d’IOPS en lecture et écriture agrégée pour le disque est de 800 IOPS, l’évaluation de machine virtuelle Azure recommande un disque Premium (en raison des exigences d’IOPS plus élevées), puis recommande une référence SKU de disque capable de prendre en charge la taille et les IOPS nécessaires. La correspondance la plus proche dans cet exemple serait P15 (256 Go, 1100 IOPS). Par conséquent, même si la taille nécessaire au disque local était de 32 Go, l’évaluation de machine virtuelle Azure a recommandé un disque avec une plus grande taille en raison de l’exigence élevée du disque local concernant les IOPS.

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>Pourquoi les données de performances sont-elles manquantes pour une partie ou la totalité des machines virtuelles dans mon rapport d’évaluation ?

Pour l’évaluation « En fonction des performances », le rapport d’évaluation signale « PercentageOfCoresUtilizedMissing » ou « PercentageOfMemoryUtilizedMissing » quand l’appliance Azure Migrate ne peut pas collecter les données de performances pour les machines virtuelles locales. Vérifiez :

- Si les machines virtuelles sont sous tension pendant que vous créez l’évaluation.
- S’il manque uniquement les compteurs de mémoire et si vous tentez d’évaluer des machines virtuelles Hyper-V, vérifiez si la mémoire dynamique est activée sur ces dernières. Il existe un problème connu qui empêche l’appliance Azure Migrate de collecter les données d’utilisation de la mémoire pour les machines virtuelles de ce type.
- En l'absence de tous les compteurs de performances, assurez-vous que les exigences d’accès aux ports à des fins d'évaluation sont respectées. Apprenez-en plus sur les conditions d’accès aux ports à des fins d’évaluation de [VMware](./migrate-support-matrix-vmware.md#port-access-requirements), de [Hyper-V](./migrate-support-matrix-hyper-v.md#port-access) et du [serveur physique](./migrate-support-matrix-physical.md#port-access).
Remarque : si l’un des compteurs de performances est manquant, Azure Migrate : découverte et évaluation se base sur les cœurs et la mémoire alloués localement pour recommander une taille de machine virtuelle appropriée.

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Pourquoi des données de performances sont-elles manquantes pour certains ou la totalité des serveurs dans ma machine virtuelle Azure et/ou mon rapport d’évaluation AVS ?

Pour l’évaluation « En fonction des performances », le rapport d’évaluation signale « PercentageOfCoresUtilizedMissing » ou « PercentageOfMemoryUtilizedMissing » quand l’appliance Azure Migrate ne peut pas collecter les données de performances pour les serveurs locaux. Vérifiez :

- Si les serveurs sont sous tension pendant que vous créez l’évaluation.
- Si seuls les compteurs de mémoire manquent et si vous essayez d’évaluer des serveurs dans un environnement Hyper-V. Dans ce scénario, activez la mémoire dynamique sur les serveurs et recalculez l’évaluation pour tenir compte des dernières modifications. L’appliance peut collecter des valeurs d’utilisation de la mémoire pour des serveurs dans un environnement Hyper-V uniquement lorsque la mémoire dynamique est activée sur le serveur.

- Si tous les compteurs de performances sont manquants, assurez-vous que les connexions sortantes sur le port 443 (HTTPS) sont autorisées.

    > [!Note]
    > Si l’un des compteurs de performances est manquant, Azure Migrate : découverte et évaluation se base sur les cœurs et la mémoire alloués localement pour recommander une taille de machine virtuelle appropriée.


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Pourquoi des données de performances sont-elles manquantes pour certaines ou la totalité des instances/bases de données SQL dans mon évaluation Azure SQL ?

Pour vous assurer que les données de performances soient collectées, vérifiez les éléments suivants :

- Les serveurs SQL Server sont sous tension pendant que vous créez l’évaluation.
- L’état de connexion de l’agent SQL dans Azure Migrate est « Connecté » (contrôlez la dernière pulsation). 
- L’état de connexion d’Azure Migrate pour toutes les instances SQL est « Connecté » dans le panneau des instances SQL découvertes.
- Si tous les compteurs de performances sont manquants, assurez-vous que les connexions sortantes sur le port 443 (HTTPS) sont autorisées

Si des compteurs de performances sont manquant, l’évaluation SQL Azure recommande la plus petite configuration d’Azure SQL pour cette instance/cette base de données.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Pourquoi la note de confiance de mon évaluation est faible ?

La note de confiance pour les évaluations « En fonction des performances » dépend du pourcentage de [points de données disponibles](./concepts-assessment-calculation.md#ratings) nécessaires pour calculer l’évaluation. Voici les raisons pour lesquelles une évaluation pourrait obtenir une note de confiance faible :

- Vous n’avez pas profilé votre environnement pour la durée pour laquelle vous créez l’évaluation. Par exemple, si vous créez une évaluation avec une durée des performances définie sur une semaine, vous devez attendre au moins une semaine après le démarrage de la découverte pour que tous les points de données soient recueillis. Si vous ne pouvez pas attendre pendant cette durée, définissez la durée des performances sur une période plus courte, puis **Recalculez** l’évaluation.
 
- L’évaluation ne parvient pas à collecter les données de performances d’une partie ou de la totalité des serveurs pendant la période d’évaluation. Pour obtenir un niveau de confiance élevé, vérifiez les éléments suivants : 
    - Les serveurs sont sous tension pendant toute la durée de l’évaluation.
    - Les connexions sortantes sont autorisées sur le port 443.
    - Pour les serveurs Hyper-V, la mémoire dynamique est activée. 
    - L’état de connexion des agents dans Azure Migrate est « Connecté » (et contrôlez la dernière pulsation).
    - Pour les évaluations d’Azure SQL, l’état de connexion d’Azure Migrate pour toutes les instances SQL est « Connecté » dans le panneau des instances SQL découvertes.

    **Recalculez** l’évaluation pour qu’elle reflète les dernières modifications du niveau de confiance.

- Pour les évaluations de machines virtuelles Azure et d’AVS, peu de serveurs ont été créés après le démarrage de la découverte. Par exemple, si vous créez une évaluation de l’historique des performances du mois dernier, mais si la création de quelques serveurs dans l’environnement ne remonte qu’à une semaine. Dans ce cas, les données de performances pour les nouveaux serveurs ne seront pas disponibles pendant toute la durée et le classement de confiance sera faible. [En savoir plus](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- Pour les évaluations Azure SQL, peu d’instances et de bases de données SQL ont été créées après le démarrage de la découverte. Par exemple, si vous créez une évaluation de l’historique des performances du mois dernier, mais si la création de quelques instances ou bases de données SQL dans l’environnement ne remonte qu’à une semaine. Dans ce cas, les données de performances pour les nouveaux serveurs ne seront pas disponibles pendant toute la durée et le classement de confiance sera faible. [En savoir plus](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>La licence du système d'exploitation est-elle incluse dans une évaluation de machine virtuelle Azure ?

L’évaluation de machine virtuelle Azure prend en considération le coût de la licence du système d’exploitation uniquement pour les serveurs Windows, Les coûts de licence ne sont pas pris en considération pour des serveurs Linux.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Comment le dimensionnement basé sur les performances fonctionne-t-il dans une évaluation de machine virtuelle Azure ?

L’évaluation de machine virtuelle Azure recueille les données de performances des serveurs locaux de manière continue, et les utilise pour recommander la référence SKU de machine virtuelle et la référence SKU de disque dans Azure. [Découvrez comment](concepts-assessment-calculation.md#calculate-sizing-performance-based) les données basées sur les performances sont collectées.

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>Pourquoi mon évaluation affiche-t-elle un avertissement indiquant qu’elle a été créée à l’aide d’une combinaison non valide d’instances réservées, de durée de bon fonctionnement de la machine virtuelle et de remise (%) ?
Lorsque vous sélectionnez « Instances réservées », les propriétés « Remise (%) » et « Durée de fonctionnement de la machine virtuelle » ne sont pas applicables. Lorsque votre évaluation a été créée avec une combinaison non valide de ces propriétés, les boutons de modification et de recalcul sont désactivés. Créez une stratégie. [Plus d’informations](./concepts-assessment-calculation.md#whats-an-assessment)

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Je ne vois pas les données de performances pour certaines cartes réseau sur mes serveurs physiques

Cela peut se produire si la virtualisation Hyper-V est activée sur le serveur physique. En raison d’un écart, Azure Migrate détecte actuellement les cartes réseau physiques et virtuelles sur ces serveurs. Le débit du réseau est capturé uniquement sur les cartes réseau virtuelles détectées.

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>La référence SKU de machine virtuelle Azure recommandée pour mon serveur physique est surdimensionnée

Cela peut se produire si la virtualisation Hyper-V est activée sur le serveur physique. Azure Migrate détecte actuellement les cartes réseau physiques et virtuelles sur ces serveurs. D’où le fait que le nombre de cartes réseau détectées soit supérieur au nombre réel. Étant donné que l’évaluation de machine virtuelle Azure choisit une machine virtuelle Azure capable de prendre en charge le nombre requis de cartes réseau, cela peut potentiellement entraîner le surdimensionnement d’une machine virtuelle. [En savoir plus](./concepts-assessment-calculation.md#calculating-sizing) sur l’impact du nombre de cartes réseau sur le dimensionnement. Cet écart sera traité plus tard.

## <a name="readiness-category-not-ready-for-my-physical-server"></a>Catégorie de disponibilité « Non prêt » pour mon serveur physique

La catégorie de disponibilité n’est peut-être pas correctement marquée comme « Non prêt » lorsque la virtualisation Hyper-V est activée sur un serveur physique. En raison d’un écart, Azure Migrate détecte actuellement les cartes réseau physiques et virtuelles sur ces serveurs. D’où le fait que le nombre de cartes réseau détectées soit supérieur au nombre réel. Dans l’évaluation locale aussi bien que dans l’évaluation basée sur les performances, l’évaluation de machine virtuelle Azure choisit une machine virtuelle Azure capable de prendre en charge le nombre requis de cartes réseau. Si le nombre de cartes réseau détecté est supérieur à 32, le nombre maximal de cartes réseau prises en charge sur les machines virtuelles Azure, le serveur sera marqué « Non prêt ».  [En savoir plus](./concepts-assessment-calculation.md#calculating-sizing) sur l’impact du nombre de cartes réseau sur le dimensionnement.


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>Nombre de cartes réseau détectées supérieur à la valeur réelle pour les serveurs physiques

Cela peut se produire si la virtualisation Hyper-V est activée sur le serveur physique. Azure Migrate détecte actuellement les cartes réseau physiques et virtuelles sur ces serveurs. D’où le fait que le nombre de cartes réseau détectées soit supérieur au nombre réel.

## <a name="dependency-visualization-in-azure-government"></a>Visualisation des dépendances dans Azure Government

L’analyse des dépendances basée sur les agents n’est pas prise en charge dans Azure Government. Utilisez l’analyse des dépendances sans agent.


## <a name="dependencies-dont-show-after-agent-install"></a>Les dépendances ne sont pas affichées après l’installation des agents

Une fois que vous avez installé les agents de visualisation des dépendances sur les machines virtuelles locales, Azure Migrate prend généralement 15 à 30 minutes pour afficher les dépendances dans le portail. Si vous avez attendu plus de 30 minutes, assurez-vous que le Microsoft Monitoring Agent (MMA) peut se connecter à l’espace de travail Log Analytics.

Pour les machines virtuelles Windows :
1. Dans le panneau de configuration, démarrez MMA.
2. Dans les **propriétés Microsoft Monitoring Agent** > **Azure Log Analytics (OMS)** , assurez-vous que l’**état** de l’espace de travail est vert.
3. Si l’état n’est pas vert, essayez de supprimer l’espace de travail et de le rajouter à MMA.

    ![État MMA](./media/troubleshoot-assessment/mma-properties.png)

Pour les machines virtuelles Linux, vérifiez que tout s’est bien passé pour les commandes d’installation MMA et Dependency Agent. Reportez-vous à cet autre [guide de résolution des problèmes](../azure-monitor/vm/service-map.md#post-installation-issues).

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

- **Agent MMS** : Passez en revue les systèmes d’exploitation [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems) et [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems) pris en charge.
- **Agent des dépendances** : les systèmes d’exploitation [Windows et Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) pris en charge.

## <a name="visualize-dependencies-for--1-hour"></a>Visualiser les dépendances pendant > 1 heure

Avec l’analyse des dépendances sans agent, vous pouvez visualiser les dépendances ou les exporter dans une carte pour une durée allant jusqu’à 30 jours.

Avec l’analyse des dépendances sans agent, même si Azure Migrate vous permet de revenir à une date du mois passé, la durée maximale pendant laquelle vous pouvez visualiser les dépendances est fixée à 1 heure. Par exemple, vous pouvez utiliser la fonctionnalité de durée dans la carte des dépendances pour voir les dépendances d’hier, mais uniquement sur une période d’une heure. Vous pouvez aussi utiliser les journaux d’activité Azure Monitor pour [interroger les données de dépendance](./how-to-create-group-machine-dependencies.md) sur une plus longue durée.

## <a name="visualized-dependencies-for--10-servers"></a>Dépendances visualisées pour > 10 serveurs

Dans Azure Migrate, avec l’analyse des dépendances sans agent, vous pouvez [visualiser les dépendances pour des groupes](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) contenant jusqu’à 10 machines virtuelles. Pour les groupes plus grands, nous vous recommandons de fractionner les machines virtuelles en groupes plus petits pour visualiser les dépendances.


## <a name="servers-show-install-agent"></a>Les serveurs affichent « Installer l’agent »

Après la migration des serveurs avec la visualisation des dépendances activée vers Azure, les serveurs peuvent afficher l’action « Installer l’agent » au lieu de « Afficher les dépendances » en raison du comportement suivant :

- Après la migration vers Azure, les serveurs locaux sont désactivés et les machines virtuelles équivalentes sont lancées dans Azure. Ces serveurs acquièrent une adresse MAC différente.
- Les serveurs peuvent également avoir une adresse IP différente, selon que vous avez ou non conservé l’adresse IP locale.
- Si les adresses MAC et IP sont différentes des adresses locales, Azure Migrate n’associe pas les serveurs locaux aux données de dépendance Service Map. Dans ce cas, l’option d’installation de l’agent s’affiche au lieu des dépendances.
- Après une migration de test vers Azure, les serveurs locaux restent allumés comme prévu. Les serveurs équivalents lancés dans Azure acquièrent une adresse MAC différente et peuvent acquérir une adresse IP différente. À moins que vous ne bloquiez le trafic sortant des journaux Azure Monitor provenant de ces serveurs, Azure Migrate n’associera pas les serveurs locaux aux données de dépendances Service Map, et affichera donc l’option permettant d’installer des agents au lieu de celle permettant d’afficher les dépendances.

## <a name="dependencies-export-csv-shows-unknown-process"></a>Exportation des dépendances au format CSV affiche « Processus inconnu »
Dans l’analyse des dépendances sans agent, les noms des processus sont capturés au mieux. Dans certains scénarios, bien que les noms des serveurs source et de destination et le port de destination soient capturés, il est impossible de déterminer les noms des processus aux deux extrémités de la dépendance. Dans ce cas, le processus est marqué comme « Processus inconnu ».

## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-azure-migrate"></a>Mon espace de travail Log Analytics n’est pas listé lorsque je tente de configurer l’espace de travail dans Azure Migrate
Azure Migrate prend actuellement en charge la création d’un espace de travail OMS dans les régions USA Est, Asie Sud-Est et Europe Ouest. Si l’espace de travail est créé en dehors d’Azure Migrate dans une autre région, actuellement il ne peut pas être associé à un projet.


## <a name="capture-network-traffic"></a>Capturez le trafic

Collectez les journaux du trafic comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Appuyez sur F12 pour démarrer les outils de développement. Si nécessaire, désactivez le paramètre **Effacer les entrées lors de la navigation**.
3. Sélectionnez l’onglet **Réseau**, puis commencez la capture du trafic réseau :
   - Dans Chrome, sélectionnez **Conserver le journal**. L'enregistrement devrait commencer automatiquement. Un cercle rouge indique que le trafic est en cours de capture. Si le cercle rouge ne s’affiche pas, sélectionnez le cercle noir pour démarrer la capture.
   - Dans Microsoft Edge et Internet Explorer, l’enregistrement doit commencer automatiquement. Si ce n’est pas le cas, sélectionnez le bouton de lecture vert.
4. Essayez de reproduire l'erreur.
5. Après avoir rencontré l'erreur pendant l'enregistrement, arrêtez l'enregistrement et enregistrez une copie de l'activité enregistrée :
   - Dans Chrome, cliquez avec le bouton droit et sélectionnez **Enregistrer comme HAR avec du contenu**. Cette action compresse et exporte les journaux sous la forme d’un fichier .har.
   - Dans Microsoft Edge ou Internet Explorer, sélectionnez l’option **Exporter le trafic capturé**. Cette action compresse et exporte le journal.
6. Sélectionnez l’onglet **Console** pour vérifier la présence d’avertissements ou d’erreurs. Pour enregistrer le journal de la console :
   - Dans Chrome, cliquez avec le bouton droit n'importe où dans le journal de la console. Sélectionnez **Enregistrer sous** pour exporter et compresser le journal.
   - Dans Microsoft Edge ou Internet Explorer, cliquez avec le bouton droit sur les erreurs, puis sélectionnez **Tout copier**.
7. Fermez les outils de développement.


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>Où les données du système d’exploitation de mon évaluation sont-elles découvertes ?

- Pour les machines virtuelles VMware, par défaut, il s’agit des données du système d’exploitation fournies par vCenter. 
   - Pour les machines virtuelles Linux VMware, si la découverte des applications est activée, les détails du système d’exploitation sont récupérés à partir de la machine virtuelle invitée. Pour vérifier les détails du système d’exploitation dans l’évaluation, accédez à l’affichage des serveurs détectés et pointez avec la souris sur la valeur dans la colonne « Système d’exploitation ». Dans le texte qui s’affiche, vous pouvez voir si les données du système d’exploitation que vous voyez sont collectées à partir du serveur vCenter ou de la machine virtuelle invitée à l’aide des informations d’identification de la machine virtuelle. 
   - Pour les machines virtuelles Windows, les détails du système d’exploitation sont toujours extraits du serveur vCenter.
- Pour les machines virtuelles Hyper-V, les données du système d’exploitation sont collectées depuis l’hôte Hyper-V
- Pour les serveurs physiques, elles sont extraites depuis le serveur.

## <a name="next-steps"></a>Étapes suivantes

[Créer](how-to-create-assessment.md) ou [personnaliser](how-to-modify-assessment.md) une évaluation.