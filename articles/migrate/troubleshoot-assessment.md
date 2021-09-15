---
title: Résoudre les problèmes des évaluations dans Azure Migrate
description: Obtenez de l’aide sur l’évaluation dans Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/28/2021
ms.openlocfilehash: d701767f6e8f22b1577b3fb745d01dea00557e8b
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123306818"
---
# <a name="troubleshoot-assessment"></a>Résoudre les problèmes d’évaluation

Cet article vous aide à résoudre des problèmes liés à l’évaluation et à la visualisation des dépendances grâce à [Azure Migrate : découverte et évaluation](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).

## <a name="assessment-readiness-issues"></a>Problèmes de préparation à l’évaluation

Ce tableau indique l’aide disponible pour résoudre différents problèmes de préparation à l’évaluation.

**Problème** | **Correctif**
--- | ---
Type de démarrage non pris en charge | Azure ne prend pas en charge les machines virtuelles associées au type de démarrage EFI. Convertissez le type de démarrage en BIOS avant d’exécuter une migration. <br/><br/>Vous pouvez utiliser Azure Migrate Server Migration pour gérer la migration de ces machines virtuelles. Celui-ci permet de convertir le type de démarrage de la machine virtuelle en BIOS au cours de la migration.
Système d’exploitation Windows pris en charge de manière conditionnelle | Le système d’exploitation n’est plus pris en charge et a besoin d’un programme Custom Support Agreement pour bénéficier d’une [prise en charge dans Azure](/troubleshoot/azure/virtual-machines/server-software-support). Envisagez de mettre à niveau avant d’effectuer la migration vers Azure. Consultez les informations relatives à la [préparation des serveurs exécutant Windows Server 2003](prepare-windows-server-2003-migration.md) pour la migration vers Azure.
Système d’exploitation Windows non pris en charge | Azure ne prend en charge que [certaines versions du système d’exploitation Windows](/troubleshoot/azure/virtual-machines/server-software-support). Envisagez de mettre à niveau le serveur avant d’opérer la migration vers Azure.
Systèmes d’exploitation Linux approuvés sous condition | Azure n’approuve que [certaines versions du système d’exploitation Linux](../virtual-machines/linux/endorsed-distros.md). Envisagez de mettre à niveau le serveur avant d’opérer la migration vers Azure. Pour plus d’informations, consultez [ce site web](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment).
Systèmes d’exploitation Linux non approuvés | Le serveur peut démarrer dans Azure, mais Azure ne fournit aucun support pour le système d’exploitation. Envisagez de mettre à niveau vers une [version approuvée de Linux](../virtual-machines/linux/endorsed-distros.md) avant d’effectuer la migration vers Azure.
Système d’exploitation inconnu | Le système d’exploitation de la machine virtuelle a été spécifié comme étant du type « Autre » dans vCenter Server. Ce comportement empêche Azure Migrate de vérifier la préparation de la machine virtuelle à Azure. Vérifiez que le système d’exploitation est [pris en charge](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements) par Azure avant d’opérer la migration du serveur.
Version de bit non prise en charge | Les machines virtuelles dotées d’un système d’exploitation 32 bits peuvent être démarrées dans Azure. Toutefois, il est recommandé d’effectuer une mise à niveau vers la version 64 bits avant de procéder à la migration vers Azure.
Nécessite un abonnement Microsoft Visual Studio. | Le serveur exécute un système d’exploitation client Windows qui est pris en charge uniquement via un abonnement Visual Studio.
Nous n’avons pas trouvé de machine virtuelle correspondant au niveau de performance de stockage nécessaire | Les performances de stockage (IOPS et débit) requises pour le serveur dépassent le niveau de prise en charge de machine virtuelle Azure. Réduisez les besoins de stockage du serveur avant la migration.
Nous n’avons pas trouvé de machine virtuelle correspondant au niveau de performance réseau nécessaire | Les performances réseau (entrée/sortie) requises pour le serveur dépassent le niveau de prise en charge de machine virtuelle Azure. Réduisez les exigences de mise en réseau du serveur.
Nous n’avons pas trouvé de machine virtuelle à l’emplacement spécifié | Utilisez un emplacement cible différent avant la migration.
Un ou plusieurs disques ne sont pas adaptés | Un ou plusieurs disques attachés à la machine virtuelle ne répondent pas aux exigences Azure.<br/><br/> Azure Migrate : Découverte et évaluation évalue les disques en fonction des limites appliquées aux disques Ultra (64 To).<br/><br/> Pour chaque disque attaché à la machine virtuelle, assurez-vous que la taille du disque est inférieure à 64 To (pris en charge par les disques SSD Ultra).<br/><br/> Si ce n’est pas le cas, réduisez la taille du disque avant d’effectuer la migration vers Azure, ou utilisez plusieurs disques dans Azure et [associez-les](../virtual-machines/premium-storage-performance.md#disk-striping) pour bénéficier de limites de stockage supérieures. Vérifiez que les performances (IOPS et débit) nécessaires à chaque disque sont prises en charge par les [disques de la machine virtuelle managée](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) Azure.
Une ou plusieurs cartes réseau ne sont pas adaptées | Supprimez les cartes réseau non utilisées du sereveur avant la migration.
Le nombre de disques dépasse la limite autorisée | Supprimez les disques non utilisés du serveur avant la migration.
La taille du disque dépasse la limite autorisée | Azure Migrate : Découverte et évaluation prend en charge les disques d’une taille allant jusqu’à 64 To (disques Ultra). Réduisez la taille des disques pour passer en dessous des 64 To avant la migration, ou utilisez plusieurs disques Azure et [associez-les](../virtual-machines/premium-storage-performance.md#disk-striping) pour bénéficier de limites de stockage plus élevées.
Disque indisponible dans l'emplacement spécifié | Assurez-vous que le disque se trouve dans votre emplacement cible avant la migration.
Disque indisponible pour la redondance spécifiée | Le disque doit utiliser le type de stockage de redondance défini dans les paramètres d'évaluation (LRS par défaut).
Impossible de déterminer l’adéquation du disque en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Nous n'avons pas trouvé de machine virtuelle avec les cœurs et la mémoire requis | Azure n’a pas trouvé de type de machine virtuelle adapté. Réduisez la mémoire et le nombre de cœurs du serveur local avant d’opérer la migration.
Impossible de déterminer l’adéquation de la machine virtuelle en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Impossible de déterminer l’adéquation d’un ou plusieurs disques en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Impossible de déterminer l’adéquation d’une ou plusieurs cartes réseau en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Aucune taille de machine virtuelle trouvée pour l’instance réservée de devise de l’offre | Le serveur a été marqué comme ne convenant pas parce que la taille de la machine virtuelle est introuvable pour la combinaison sélectionnée d’instance réservée, d’offre et de devise. Modifiez les propriétés d’évaluation et choisissez les combinaisons valides, puis recalculez l’évaluation. 
Préparé pour le protocole Internet sous conditions | Applicable uniquement aux évaluations Azure VMware Solution. Azure VMware Solution ne prend pas en charge les adresses Internet IPv6. Si votre serveur est détecté avec IPv6, contactez l’équipe Azure VMware Solution pour obtenir des conseils de correction.

## <a name="suggested-migration-tool-in-an-import-based-azure-vmware-solution-assessment-is-unknown"></a>L’outil de migration suggéré dans une évaluation Azure VMware Solution basée sur l’importation est inconnu

Pour des serveurs importés via un fichier CSV, l’outil de migration par défaut dans une évaluation Azure VMware Solution est inconnu. Pour des serveurs dans un environnement VMware, utilisez la solution VMware Hybrid Cloud Extension (HCX). [Plus d’informations](../azure-vmware/configure-vmware-hcx.md)

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Les machines virtuelles Linux sont « préparées sous conditions » dans une évaluation de machine virtuelle Azure

En ce qui concerne les machines virtuelles Hyper-V et VMware, une évaluation de machine virtuelle Azure marque les machines virtuelles Linux comme « préparées sous conditions » en raison d’un écart connu. 

- L’écart l’empêche de détecter la version mineure du système d’exploitation Linux installé sur les machines virtuelles locales.
- Par exemple, pour RHEL 6.10, une évaluation de machine virtuelle Azure ne détecte actuellement que RHEL 6 comme version du système d’exploitation. Ce comportement survient parce que vCenter Server ainsi que l’hôte Hyper-V ne fournissent pas de version de noyau pour les systèmes d’exploitation de la machine virtuelle Linux.
- Dans la mesure où Azure approuve uniquement certaines versions de Linux, les machines virtuelles Linux sont actuellement marquées comme « Prêtes sous condition » dans l’évaluation de machine virtuelle Azure.
- Vous pouvez déterminer si le système d’exploitation Linux exécuté sur la machine virtuelle locale est approuvé dans Azure en consultant la [prise en charge de Linux par Azure](../virtual-machines/linux/endorsed-distros.md).
- Une fois que vous avez vérifié la distribution approuvée, vous pouvez ignorer cet avertissement.

Cet écart peut être résolu en activant la [découverte des applications](./how-to-discover-applications.md) sur les machines virtuelles VMware. Une évaluation de machine virtuelle Azure utilise le système d’exploitation détecté de la machine virtuelle à l’aide des informations d’identification d’invité fournies. Les données du système d'exploitation identifient les informations correctes sur le système d'exploitation pour les machines virtuelles Windows et Linux.

## <a name="operating-system-version-not-available"></a>Version de système d'exploitation non disponible

Pour les serveurs physiques, les informations sur la version mineure du système d’exploitation doivent être disponibles. Dans le cas contraire, contactez le support Microsoft. Pour des serveurs dans un environnement VMware, Azure Migrate utilise les informations du système d’exploitation spécifiées pour la machine virtuelle dans vCenter Server. Toutefois, vCenter Server ne fournit pas la version mineure pour les systèmes d’exploitation. Pour découvrir la version mineure, configurez la [découverte d’application](./how-to-discover-applications.md). Pour les machines virtuelles Hyper-V, la détection des versions mineures du système d’exploitation n’est pas prise en charge. 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Les références SKU Azure sont plus volumineuses qu’en local dans une évaluation de machine virtuelle Azure

Une évaluation de machine virtuelle Azure peut recommander des références SKU de machine virtuelle Azure avec davantage de cœurs et de mémoire que l’allocation locale actuelle en fonction du type d’évaluation :

- La recommandation en matière de référence SKU de machine virtuelle varie en fonction des propriétés d’évaluation.
- Elle est affectée par le type d’évaluation que vous effectuez dans une évaluation de machine virtuelle Azure. Il existe deux types d’évaluation : **En fonction des performances** et **Localement**.
- Pour les évaluations en fonction des performances, l’évaluation de machine virtuelle Azure prend en compte les données d’utilisation des machines virtuelles locales (utilisation de l’UC, de la mémoire, des disques et du réseau) afin de déterminer la référence SKU de machine virtuelle cible appropriée pour vos machines virtuelles locales. Il ajoute également un facteur de confort pour déterminer l’utilisation effective.
- Pour le dimensionnement local, les données de performances ne sont pas prises en compte et la référence SKU cible est recommandée en fonction de la répartition locale.

Examinons un exemple de recommandation :

Nous disposons d’une machine virtuelle locale dotée de quatre cœurs et de 8 Go de mémoire, avec une utilisation de l’UC de 50 % et une utilisation de la mémoire de 50 %, ainsi qu’un facteur de confort spécifié de 1,3.

- Si l’évaluation est définie sur **Localement**, il est recommandé d’utiliser une référence SKU de machine virtuelle Azure avec 4 cœurs et 8 Go de mémoire.
- Si l’évaluation est **en fonction des performances**, d’après l’utilisation effective de l’UC et de la mémoire (50 % de 4 cœurs * 1,3 = 2,6 cœurs et 50 % de 8 Go de mémoire * 1,3 = 5,3 Go de mémoire), la référence SKU de machine virtuelle la moins coûteuse avec quatre cœurs (nombre de cœurs pris en charge le plus proche) et 8 Go de mémoire (taille de mémoire prise en charge la plus proche) est celle qui est recommandée.
- [En savoir](concepts-assessment-calculation.md#types-of-assessments) plus sur le dimensionnement de l’évaluation.

## <a name="why-is-the-recommended-azure-disk-sku-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Pourquoi la référence SKU de disque Azure recommandée à la suite d’une évaluation de machine virtuelle Azure est-elle plus volumineuse qu’en local ?

L’évaluation de machine virtuelle Azure peut recommander un disque plus volumineux en fonction du type d’évaluation :

- Le dimensionnement de disque dépend de deux propriétés d’évaluation : le critère de dimensionnement et le type de stockage.
- Si le critère de dimensionnement est **En fonction des performances** et que le type de stockage est défini sur **Automatique**, les valeurs de débit et d’IOPS du disque sont prises en compte lors de l’identification du type de disque cible (HDD Standard, SSD Standard, Premium ou Disque Ultra). Une référence SKU de disque parmi celles du type approprié est ensuite recommandée conformément aux exigences de taille du disque local.
- Si le critère de dimensionnement est **En fonction des performances** et le type de stockage est **Premium**, une référence SKU de disque Premium dans Azure est recommandée conformément aux exigences de débit, d’IOPS et de taille du disque local. La même logique est utilisée pour effectuer le dimensionnement du disque quand le critère de dimensionnement est défini sur **Localement** et que le type de stockage est **HDD Standard**, **SSD Standard**, **Premium** ou **Disque Ultra**.

Par exemple, supposons que vous ayez un disque local avec 32 Go de mémoire, mais que la valeur d’IOPS en lecture et écriture agrégée pour le disque est de 800 IOPS. L’évaluation de machines virtuelles Azure recommande un type de disque Premium en raison des exigences d’IOPS plus élevées. Il recommande également une référence SKU de disque capable de prendre en charge la taille et les IOPS nécessaires. La correspondance la plus proche dans cet exemple serait P15 (256 Go, 1100 IOPS). Par conséquent, même si la taille nécessaire au disque local était de 32 Go, l’évaluation de machine virtuelle Azure a recommandé un disque avec une plus grande taille en raison de l’exigence élevée du disque local concernant les IOPS.

## <a name="why-is-performance-data-missing-for-some-or-all-vms-in-my-assessment-report"></a>Pourquoi les données de performances sont-elles manquantes pour une partie ou la totalité des machines virtuelles dans mon rapport d’évaluation ?

Pour l’évaluation **En fonction des performances**, le rapport d’évaluation signale « PercentageOfCoresUtilizedMissing » ou « PercentageOfMemoryUtilizedMissing » quand l’appliance Azure Migrate ne peut pas collecter les données de performances pour les machines virtuelles locales. Veillez à vérifier les points suivants :

- Si les machines virtuelles sont sous tension pendant que vous créez l’évaluation.
- S’il manque uniquement des compteurs de mémoire et si vous tentez d’évaluer des machines virtuelles Hyper-V, vérifiez si une mémoire dynamique est activée sur celles-ci. Un problème connu empêche l’appliance Azure Migrate de collecter les données d’utilisation de la mémoire pour les machines virtuelles de ce type.
- En l'absence de tous les compteurs de performances, assurez-vous que les exigences d’accès aux ports à des fins d'évaluation sont respectées. Apprenez-en davantage sur les conditions d’accès aux ports à des fins d’évaluation de [VMware](./migrate-support-matrix-vmware.md#port-access-requirements), de [Hyper-V](./migrate-support-matrix-hyper-v.md#port-access) et du [serveur physique](./migrate-support-matrix-physical.md#port-access).
Si l’un des compteurs de performances est manquant, Azure Migrate : découverte et évaluation se base sur les cœurs et la mémoire alloués localement pour recommander une taille de machine virtuelle appropriée.

## <a name="why-is-performance-data-missing-for-some-or-all-servers-in-my-azure-vm-or-azure-vmware-solution-assessment-report"></a>Pourquoi les données de performances sont-elles manquantes pour certains ou tous les serveurs dans mon rapport d’évaluation de machine virtuelle Azure ou Azure VMware Solution ?

Pour l’évaluation **En fonction des performances**, le rapport d’évaluation signale « PercentageOfCoresUtilizedMissing » ou « PercentageOfMemoryUtilizedMissing » quand l’appliance Azure Migrate ne peut pas collecter les données de performances pour les serveurs locaux. Veillez à vérifier les points suivants :

- Si les serveurs sont sous tension pendant que vous créez l’évaluation.
- Si seuls les compteurs de mémoire manquent alors que vous essayez d’évaluer des serveurs dans un environnement Hyper-V. Dans ce scénario, activez la mémoire dynamique sur les serveurs et recalculez l’évaluation pour tenir compte des dernières modifications. L’appliance peut collecter des valeurs d’utilisation de la mémoire pour des serveurs dans un environnement Hyper-V uniquement lorsque la mémoire dynamique est activée sur le serveur.
- Si tous les compteurs de performances sont manquants, assurez-vous que les connexions sortantes sur le port 443 (HTTPS) sont autorisées.

    > [!Note]
    > Si l’un des compteurs de performances est manquant, Azure Migrate : découverte et évaluation se base sur les cœurs et la mémoire alloués localement pour recommander une taille de machine virtuelle appropriée.

## <a name="why-is-performance-data-missing-for-some-or-all-sql-instances-or-databases-in-my-azure-sql-assessment"></a>Pourquoi des données de performances sont-elles manquantes pour certaines ou la totalité des instances ou bases de données SQL dans mon évaluation Azure SQL ?

Pour vous assurer que les données de performances soient collectées, veillez à vérifier les points suivants :

- Les serveurs SQL Server sont sous tension pendant que vous créez l’évaluation.
- Si l’état de connexion de l’agent SQL dans Azure Migrate est **Connecté**. Contrôlez également la dernière pulsation. 
- Si l’état de connexion d’Azure Migrate pour toutes les instances SQL est **Connecté** dans le volet des instances SQL découvertes.
- Si tous les compteurs de performances sont manquants, assurez-vous que les connexions sortantes sur le port 443 (HTTPS) sont autorisées.

Si des compteurs de performances sont manquants, l’évaluation SQL Azure recommande la plus petite configuration d’Azure SQL pour cette instance ou base de données.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Pourquoi la note de confiance de mon évaluation est faible ?

Le calcul de la note de confiance pour les évaluations **basées sur les performances** repose sur le pourcentage de [points de données disponibles](./concepts-assessment-calculation.md#ratings) nécessaires pour calculer l’évaluation. Une évaluation peut obtenir une note de confiance faible pour les raisons suivantes :

- Vous n’avez pas profilé votre environnement pendant la durée pour laquelle vous créez l’évaluation. Par exemple, si vous créez une évaluation avec une durée des performances définie sur une semaine, vous devez attendre au moins une semaine après le démarrage de la découverte pour que tous les points de données soient recueillis. Si vous ne pouvez pas attendre pendant ce temps, définissez la durée des performances sur une période plus courte et recalculez l’évaluation.
- L’évaluation ne parvient pas à collecter les données de performances d’une partie ou de la totalité des serveurs pendant la période d’évaluation. Pour obtenir une note de confiance élevée, vérifiez les points suivants : 
    - Les serveurs sont sous tension pendant toute la durée de l’évaluation.
    - Les connexions sortantes sont autorisées sur le port 443.
    - Pour les serveurs Hyper-V, la mémoire dynamique est activée.
    - L’état de connexion des agents dans Azure Migrate est « Connecté ». Contrôlez également la dernière pulsation.
    - Pour les évaluations Azure SQL, l’état de connexion d’Azure Migrate pour toutes les instances SQL est « Connecté » dans le volet des instances SQL découvertes.

    Recalculez l’évaluation pour qu’elle reflète l’évolution récente de la note de confiance.

- Pour les évaluations de machine virtuelle Azure et d’Azure VMware Solution, peu de serveurs ont été créés après le démarrage de la découverte. Par exemple, supposons que vous créez une évaluation de l’historique des performances du mois dernier, mais que la création de quelques serveurs dans l’environnement ne remonte qu’à une semaine. Dans ce cas, les données de performances pour les nouveaux serveurs ne seront pas disponibles pendant toute la durée et la note de confiance sera faible. [Plus d’informations](./concepts-assessment-calculation.md#confidence-ratings-performance-based)
- Pour les évaluations Azure SQL, peu d’instances et de bases de données SQL ont été créées après le démarrage de la découverte. Par exemple, supposons que vous créez une évaluation de l’historique des performances du mois dernier, mais que la création de quelques instances ou bases de données SQL dans l’environnement ne remonte qu’à une semaine. Dans ce cas, les données de performances pour les nouveaux serveurs ne seront pas disponibles pendant toute la durée et la note de confiance sera faible. [Plus d’informations](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>La licence du système d'exploitation est-elle incluse dans une évaluation de machine virtuelle Azure ?

Une évaluation de machine virtuelle Azure prend en considération le coût de la licence du système d’exploitation uniquement pour les serveurs Windows, Les coûts de licence ne sont pas pris en considération pour des serveurs Linux.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Comment le dimensionnement basé sur les performances fonctionne-t-il dans une évaluation de machine virtuelle Azure ?

Une évaluation de machine virtuelle Azure recueille les données de performances des serveurs locaux de manière continue, et les utilise pour recommander la référence SKU de machine virtuelle et la référence SKU de disque dans Azure. [Découvrez comment](concepts-assessment-calculation.md#calculate-sizing-performance-based) les données basées sur les performances sont collectées.

## <a name="can-i-migrate-my-disks-to-an-ultra-disk-by-using-azure-migrate"></a>Puis-je migrer mes disques vers un disque Ultra à l’aide d’Azure Migrate ?

Non. Actuellement, Azure Migrate et Azure Site Recovery ne prennent pas en charge la migration vers des disques Ultra. Les étapes de déploiement d’un disque Ultra sont décrites sur [ce site web](https://docs.microsoft.com/azure/virtual-machines/disks-enable-ultra-ssd?tabs=azure-portal#deploy-an-ultra-disk).

## <a name="why-are-the-provisioned-iops-and-throughput-in-my-ultra-disk-more-than-my-on-premises-iops-and-throughput"></a>Pourquoi les IOPS et le débit provisionnés dans mon disque Ultra sont-ils supérieurs à mon débit et mes IOPS locaux ?

Conformément à la [page de tarification officielle](https://azure.microsoft.com/pricing/details/managed-disks/), le disque Ultra est facturé en fonction de la taille provisionnée, des IOPS provisionnées et du débit provisionné. Par exemple, si vous avez provisionné un disque Ultra de 200 Gio avec 20 000 IOPS et 1 000 Mo/s, et que vous l’avez supprimé au bout de 20 heures, il sera mappé à l’offre de taille de disque de 256 Gio. Vous serez facturé pour les 256 Gio, les 20 000 IOPS et les 1 000 Mo/s sur 20 heures.

IOPS à provisionner = (débit détecté) * 1024/256

## <a name="does-the-ultra-disk-recommendation-consider-latency"></a>La recommandation de disque Ultra relative prend-elle en compte la latence ?

Non, actuellement, seule la taille de disque, le débit total et le nombre total d’IOPS sont utilisés pour le dimensionnement et le calcul des coûts.

## <a name="i-can-see-m-series-supports-ultra-disk-but-in-my-assessment-where-ultra-disk-was-recommended-it-says-no-vm-found-for-this-location"></a>Je peux voir que la série M prend en charge les disques Ultra, mais dans mon évaluation où un disque Ultra était recommandé, elle indique « Aucune machine virtuelle n’a été trouvée pour cet emplacement ».

Ce résultat est possible car toutes les tailles de machine virtuelle prenant en charge les disques Ultra ne sont pas présentes dans les régions où les disques Ultra sont pris en charge. Changez la région d’évaluation cible pour obtenir la taille de machine virtuelle pour ce serveur.

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>Pourquoi mon évaluation affiche-t-elle un avertissement indiquant qu’elle a été créée à l’aide d’une combinaison non valide d’instances réservées, de durée de bon fonctionnement de la machine virtuelle et de remise (%) ?

Lorsque vous sélectionnez **Instances réservées**, les propriétés **Remise (%)** et **Durée de fonctionnement de la machine virtuelle** ne sont pas applicables. Lorsque votre évaluation a été créée avec une combinaison non valide de ces propriétés, les boutons **Modifier** et **Recalculer** sont désactivés. Créez une évaluation. [Plus d’informations](./concepts-assessment-calculation.md#whats-an-assessment)

## <a name="i-dont-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Je ne vois pas les données de performances pour certaines cartes réseau sur mes serveurs physiques

Ce problème peut se produire si la virtualisation Hyper-V est activée sur le serveur physique. En raison d’un écart, Azure Migrate détecte actuellement les cartes réseau physiques et virtuelles sur ces serveurs. Le débit du réseau est capturé uniquement sur les cartes réseau virtuelles détectées.

## <a name="the-recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>La référence SKU de machine virtuelle Azure recommandée pour mon serveur physique est surdimensionnée

Ce problème peut se produire si la virtualisation Hyper-V est activée sur le serveur physique. Azure Migrate détecte actuellement les cartes réseau physiques et virtuelles sur ces serveurs. Par conséquent, le nombre de cartes réseau détectées est supérieur au nombre réel. Étant donné que l’évaluation de machine virtuelle Azure choisit une machine virtuelle Azure capable de prendre en charge le nombre requis de cartes réseau, cela peut potentiellement entraîner le surdimensionnement d’une machine virtuelle. [Apprenez-en davantage](./concepts-assessment-calculation.md#calculating-sizing) sur l’impact du nombre de cartes réseau sur le dimensionnement. À l’avenir, cet écart sera corrigé.

## <a name="the-readiness-category-is-marked-not-ready-for-my-physical-server"></a>La catégorie de disponibilité « Non prêt » est indiquée pour mon serveur physique

La catégorie de disponibilité n’est peut-être pas correctement marquée comme « Non prêt » lorsque la virtualisation Hyper-V est activée sur un serveur physique. En raison d’un écart, Azure Migrate détecte actuellement les cartes réseau physiques et virtuelles sur ces serveurs. Par conséquent, le nombre de cartes réseau détectées est supérieur au nombre réel. Dans l’évaluation **locale** aussi bien que dans l’évaluation **basée sur les performances**, l’évaluation de machine virtuelle Azure choisit une machine virtuelle Azure capable de prendre en charge le nombre requis de cartes réseau. Si le nombre de cartes réseau détectées est supérieur à 32 (nombre maximal de cartes réseau prises en charge sur les machines virtuelles Azure), le serveur est marqué comme « Non prêt ». [Apprenez-en davantage](./concepts-assessment-calculation.md#calculating-sizing) sur l’impact du nombre de cartes réseau sur le dimensionnement.

## <a name="the-number-of-discovered-nics-is-higher-than-actual-for-physical-servers"></a>Le nombre de cartes réseau détectées est supérieur à la valeur réelle pour les serveurs physiques

Ce problème peut se produire si la virtualisation Hyper-V est activée sur le serveur physique. Azure Migrate détecte actuellement les cartes réseau physiques et virtuelles sur ces serveurs. Par conséquent, le nombre de cartes réseau détectées est supérieur au nombre réel.

## <a name="capture-network-traffic"></a>Capturez le trafic

Pour collecter les journaux du trafic réseau :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez F12 pour démarrer les outils de développement. Si nécessaire, désactivez le paramètre **Effacer les entrées lors de la navigation**.
1. Sélectionnez l’onglet **Réseau**, puis commencez la capture du trafic réseau :
   - Dans Chrome, sélectionnez **Conserver le journal**. L'enregistrement devrait commencer automatiquement. Un cercle rouge indique que le trafic est en cours de capture. Si le cercle rouge ne s’affiche pas, sélectionnez le cercle noir pour démarrer la capture.
   - Dans Microsoft Edge et Internet Explorer, l’enregistrement doit commencer automatiquement. Si ce n’est pas le cas, sélectionnez le bouton de lecture vert.
1. Essayez de reproduire l'erreur.
1. Après avoir rencontré l’erreur pendant l’enregistrement, arrêtez l’enregistrement et enregistrez une copie de l’activité enregistrée :
   - Dans Chrome, cliquez avec le bouton droit et sélectionnez **Enregistrer comme HAR avec du contenu**. Cette action compresse et exporte les journaux sous la forme d’un fichier .har.
   - Dans Microsoft Edge ou Internet Explorer, sélectionnez l’option **Exporter le trafic capturé**. Cette action compresse et exporte le journal.
1. Sélectionnez l’onglet **Console** pour vérifier la présence d’avertissements ou d’erreurs. Pour enregistrer le journal de la console :
   - Dans Chrome, cliquez avec le bouton droit n'importe où dans le journal de la console. Sélectionnez **Enregistrer sous** pour exporter et compresser le journal.
   - Dans Microsoft Edge ou Internet Explorer, cliquez avec le bouton droit sur les erreurs, puis sélectionnez **Tout copier**.
1. Fermez les outils de développement.

## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>Où les données du système d’exploitation de mon évaluation sont-elles découvertes ?

- Pour les machines virtuelles VMware, par défaut, il s’agit des données du système d’exploitation fournies par vCenter Server.
   - Pour les machines virtuelles Linux VMware, si la découverte des applications est activée, les détails du système d’exploitation sont récupérés à partir de la machine virtuelle invitée. Pour vérifier les détails du système d’exploitation dans l’évaluation, accédez à l’affichage **Serveurs détectés** et pointez avec la souris sur la valeur dans la colonne **Système d’exploitation**. Dans le texte qui s’affiche, vous pouvez voir si les données du système d’exploitation que vous voyez sont collectées à partir de vCenter Server ou de la machine virtuelle invitée à l’aide des informations d’identification de la machine virtuelle.
   - Pour les machines virtuelles Windows, les détails du système d’exploitation sont toujours extraits du serveur vCenter.
- Pour les machines virtuelles Hyper-V, les données du système d’exploitation sont collectées depuis l’hôte Hyper-V.
- Pour les serveurs physiques, elles sont extraites depuis le serveur.

## <a name="common-web-apps-discovery-errors"></a>Erreurs courantes de découverte des applications web

Azure Migrate offre la possibilité d’évaluer les applications web ASP.NET découvertes pour la migration vers Azure App Service, en utilisant l’outil Azure Migrate : Découverte et évaluation. Reportez-vous au tutoriel [Évaluation](tutorial-assess-webapps.md) pour commencer.

Les erreurs d’évaluation courantes d’App Service sont récapitulées dans le tableau.

| **Error** | **Cause** | **Action recommandée** |
|--|--|--|
|**Vérification du pool d’applications**|Le site IIS utilise les pools d’applications suivants : {0}.|Azure App Service ne prend pas en charge plusieurs configurations de pool d’applications par application App Service. Déplacez les charges de travail vers un même pool d’applications et supprimez les autres pools d’applications.|
|**Vérification de l’identité du pool d’applications**|Le pool d’applications du site s’exécute avec un type d’identité d’utilisateur non pris en charge : {0}.|App Service ne prend pas en charge l’utilisation des types d’identité de pool d’applications LocalSystem ou SpecificUser. Définissez le pool d’applications pour qu’il s’exécute en tant que ApplicationPoolIdentity.|
|**Vérification des autorisations**|Les types d’authentification non pris en charge suivants ont été trouvés : {0}.|Les types d’authentification et la configuration pris en charge par App Service sont différents de ceux d’IIS local. Désactivez les types d’authentification non pris en charge sur le site. Une fois la migration terminée, il est possible de configurer le site en utilisant un des types d’authentification App Service pris en charge.|
|**Vérification d’autorisation inconnue**|Impossible de déterminer les types d’authentification activés pour l’ensemble de la configuration du site.|Impossible de déterminer les types d’authentification. Corrigez toutes les erreurs de configuration et vérifiez que tous les emplacements de contenu du site sont accessibles au groupe d’administrateurs.|
|**Vérification des erreurs de configuration**|Les erreurs de configuration suivantes ont été trouvées : {0}.|La préparation à la migration ne peut pas être déterminée sans lire toutes les configurations applicables. Résolvez toutes les erreurs de configuration. Vérifiez que la configuration est valide et accessible.|
|**Vérification de la taille du contenu**|Le contenu du site semble être supérieur au maximum autorisé de 2 Go pour la migration.|Pour que la migration réussisse, le contenu du site doit être inférieur à 2 Go. Évaluez si le site peut passer à l’utilisation des options de stockage non basées sur un système de fichiers pour le contenu statique (par exemple, Stockage Azure).|
|**Vérification de la taille du contenu inconnue**|La taille du contenu des fichiers n’a pas pu être déterminée, ce qui indique généralement un problème d’accès.|Le contenu doit être accessible pour migrer le site. Vérifiez que le site n’utilise pas de partages UNC pour le contenu et que tous les emplacements des contenus du site sont accessibles au groupe d’administrateurs.|
|**Vérification des modules globaux**|Les modules globaux non pris en charge suivants ont été détectés : {0}.|App Service prend en charge des modules globaux limités. Supprimez les modules non pris en charge de la section GlobalModules, ainsi que toute la configuration associée.|
|**Vérification des filtres ISAPI**|Les filtres ISAPI non pris en charge suivants ont été détectés : {0}.|La configuration automatique de filtres ISAPI personnalisés n’est pas prise en charge. Supprimez les filtres ISAPI non pris en charge.|
|**Vérification des filtres ISAPI inconnus**|Impossible de déterminer les filtres ISAPI présents pour l’ensemble de la configuration du site.|La configuration automatique de filtres ISAPI personnalisés n’est pas prise en charge. Corrigez toutes les erreurs de configuration et vérifiez que tous les emplacements de contenu du site sont accessibles au groupe d’administrateurs.|
|**Vérification des étiquettes d’emplacement**|Les chemins d’emplacement suivants ont été trouvés dans le fichier applicationHost.config : {0}.|La méthode de migration ne prend pas en charge le déplacement de la configuration des chemins d’emplacement dans applicationHost.config. Déplacez la configuration des chemins d’emplacement dans le fichier web.config à la racine du site ou dans un fichier web.config associé à l’application spécifique à laquelle il s’applique.|
|**Vérification des protocoles**|Des liaisons ont été trouvées en utilisant les protocoles non pris en charge suivants : {0}.|App Service prend seulement en charge les protocoles HTTP et HTTPS. Supprimez les liaisons avec des protocoles autres que HTTP ou HTTPS.|
|**Vérification des répertoires virtuels**|Les répertoires virtuels suivants sont hébergés sur des partages UNC : {0}.|La migration ne prend pas en charge le contenu de site hébergé sur des partages UNC. Déplacez le contenu dans un chemin de fichier local ou passez à une option de stockage non basée sur un système de fichiers, comme le Stockage Azure. Si vous utilisez une configuration partagée, désactivez la configuration partagée pour le serveur avant de modifier les chemins du contenu.|
|**Vérification des liaisons HTTPS**|L’application utilise HTTPS.|D’autres étapes manuelles sont nécessaires pour la configuration de HTTPS dans App Service. D’autres étapes post-migration sont nécessaires pour associer des certificats au site App Service.|
|**Vérification des ports TCP**|Des liaisons ont été trouvées sur les ports non pris en charge suivants : {0}.|App Service prend seulement en charge les ports 80 et 443. Les clients qui font des demandes au site doivent mettre à jour le port dans leurs demandes et utiliser le port 80 ou 443.|
|**Vérification des frameworks**|Les frameworks non-.NET ou les versions du .NET Framework non pris en charge suivants ont été détectés comme étant possiblement utilisés par ce site : {0}.|La migration ne vérifie pas le framework pour les sites non-.NET. App Service prend en charge plusieurs frameworks, mais ceux-ci ont différentes options de migration. Vérifiez que les frameworks non-.NET ne sont pas utilisés par le site ou bien envisagez d’utiliser une autre option de migration.|

## <a name="next-steps"></a>Étapes suivantes

[Créer](how-to-create-assessment.md) ou [personnaliser](how-to-modify-assessment.md) une évaluation.
