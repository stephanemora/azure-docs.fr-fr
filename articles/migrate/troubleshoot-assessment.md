---
title: Détecter un problème d’évaluation et de visualisation des dépendances dans Azure Migrate
description: Obtenez de l’aide pour détecter un problème d’évaluation et de visualisation des dépendances dans Azure Migrate.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 3098e85fd21b6185defc4bbcf0a71d412846ab25
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722130"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Résoudre les problèmes de l’évaluation/la visualisation des dépendances

Cet article vous aide à détecter un problème lié à l’évaluation et à la visualisation des dépendances grâce à [Azure Migrate : Évaluation de serveur](migrate-services-overview.md#azure-migrate-server-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problèmes de préparation à l’évaluation

Corrigez les problèmes de préparation à l’évaluation comme suit :

**Problème** | **Correctif**
--- | ---
Type de démarrage non pris en charge | Azure ne prend pas en charge les machines virtuelles associées au type de démarrage EFI. Nous vous recommandons de convertir le type de démarrage en BIOS avant d’effectuer une migration. <br/><br/>Vous pouvez utiliser Azure Migrate Server Migration pour gérer la migration de ces machines virtuelles. Celui-ci permet de convertir le type de démarrage de la machine virtuelle en BIOS au cours de la migration.
Système d’exploitation Windows pris en charge de manière conditionnelle | Le système d’exploitation n’est plus pris en charge et a besoin d’un programme Custom Support Agreement (CSA) pour bénéficier d’une [prise en charge dans Azure](https://aka.ms/WSosstatement). Envisagez de mettre à niveau avant d’effectuer la migration vers Azure.
Système d’exploitation Windows non pris en charge | Azure ne prend en charge que [certaines versions du système d’exploitation Windows](https://aka.ms/WSosstatement). Envisagez de mettre à niveau la machine avant d’effectuer la migration vers Azure.
Systèmes d’exploitation Linux approuvés sous condition | Azure n’approuve que [certaines versions du système d’exploitation Linux](../virtual-machines/linux/endorsed-distros.md). Envisagez de mettre à niveau la machine avant d’effectuer la migration vers Azure.
Systèmes d’exploitation Linux non approuvés | La machine peut démarrer dans Azure, mais Azure ne fournit aucune prise en charge du système d’exploitation. Envisagez de mettre à niveau vers une [version approuvée de Linux](../virtual-machines/linux/endorsed-distros.md) avant d’effectuer la migration vers Azure.
Système d’exploitation inconnu | Le système d’exploitation de la machine virtuelle a été spécifié comme étant du type « Autre » dans vCenter Server. Ce comportement empêche Azure Migrate de vérifier la préparation de la machine virtuelle à Azure. Vérifiez que le système d’exploitation est [pris en charge](https://aka.ms/azureoslist) par Azure avant de procéder à la migration.
Version de bit non prise en charge | Les machines virtuelles dotées d’un système d’exploitation 32 bits peuvent être démarrées dans Azure. Toutefois, il est recommandé d’effectuer une mise à niveau vers la version 64 bits avant de procéder à la migration vers Azure.
Nécessite un abonnement Microsoft Visual Studio. | La machine exécute un système d’exploitation client Windows qui est pris en charge uniquement dans un abonnement Visual Studio.
Nous n’avons pas trouvé de machine virtuelle correspondant au niveau de performance de stockage nécessaire | Les performances de stockage (IOPS et débit) nécessaires à la machine dépassent le niveau de prise en charge Azure. Réduisez les besoins de stockage de la machine avant la migration.
Nous n’avons pas trouvé de machine virtuelle correspondant au niveau de performance réseau nécessaire | Les performances réseau (entrée/sortie) requises pour la machine dépassent la prise en charge des machines virtuelles Azure. Réduisez les exigences de mise en réseau de la machine.
Nous n’avons pas trouvé de machine virtuelle à l’emplacement spécifié | Utilisez un emplacement cible différent avant la migration.
Un ou plusieurs disques ne sont pas adaptés | Un ou plusieurs disques attachés à la machine virtuelle ne répondent pas aux exigences Azure.A<br/><br/> Azure Migrate : Server Assessment ne prend pas en charge les disques SSD Ultra et évalue les disques en fonction des limites appliquées aux disques managés Premium (32 To).<br/><br/> Pour chaque disque attaché à la machine virtuelle, assurez-vous que la taille du disque est < 64 To (pris en charge par les disques SSD Ultra).<br/><br/> Si ce n’est pas le cas, réduisez la taille du disque avant d’effectuer la migration vers Azure, ou utilisez plusieurs disques dans Azure et [associez-les](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) pour bénéficier de limites de stockage supérieures. Vérifiez que les performances (IOPS et débit) nécessaires à chaque disque sont prises en charge par les [disques de la machine virtuelle managée](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits) Azure.
Un ou plusieurs adaptateurs réseau ne sont pas adaptés | Supprimez les cartes réseau non utilisées de la machine avant la migration.
Le nombre de disques dépasse la limite autorisée | Supprimez les disques non utilisés de la machine avant la migration.
La taille du disque dépasse la limite autorisée | Azure Migrate : Server Assessment ne prend pas en charge les disques SSD Ultra, et évalue les disques en fonction des limites appliquées aux disques Premium (32 To).<br/><br/> Toutefois, Azure prend en charge les disques d’une taille maximale de 64 To (prise en charge par les disques SSD Ultra). Réduisez la taille des disques pour passer en dessous des 64 To avant la migration, ou utilisez plusieurs disques Azure et [associez-les](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) pour bénéficier de limites de stockage plus élevées.
Disque indisponible dans l'emplacement spécifié | Assurez-vous que le disque se trouve dans votre emplacement cible avant la migration.
Disque indisponible pour la redondance spécifiée | Le disque doit utiliser le type de stockage de redondance défini dans les paramètres d'évaluation (LRS par défaut).
Impossible de déterminer l’adéquation du disque en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Nous n'avons pas trouvé de machine virtuelle avec les cœurs et la mémoire requis | Azure n’a pas trouvé de type de machine virtuelle adapté. Réduisez la mémoire et le nombre de cœurs de la machine sur site avant de migrer.
Impossible de déterminer l’adéquation de la machine virtuelle en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Impossible de déterminer l’adéquation d’un ou plusieurs disques en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Impossible de déterminer l’adéquation d’une ou plusieurs cartes réseau en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.

## <a name="linux-vms-are-conditionally-ready"></a>Les machines virtuelles Linux sont « préparées sous condition »

Server Assessment marque les machines virtuelles Linux comme « préparées sous condition » en raison d’un écart connu dans Server Assessment.

- L’écart l’empêche de détecter la version mineure du système d’exploitation Linux installé sur les machines virtuelles locales.
- Par exemple, pour RHEL 6.10, Server Assessment ne détecte actuellement que RHEL 6 comme version du système d’exploitation.
-  Dans la mesure où Azure approuve uniquement certaines versions de Linux, les machines virtuelles Linux sont actuellement marquées comme « Prêtes sous condition » dans Server Assessment.
- Vous pouvez déterminer si le système d’exploitation Linux exécuté sur la machine virtuelle locale est approuvé dans Azure en consultant la [prise en charge de Linux par Azure](https://aka.ms/migrate/selfhost/azureendorseddistros).
-  Une fois que vous avez vérifié la distribution approuvée, vous pouvez ignorer cet avertissement.

## <a name="azure-skus-bigger-than-on-premises"></a>Références SKU Azure supérieures aux locales

Azure Migrate Server Assessment peut recommander des références SKU de machine virtuelle Azure avec davantage de cœurs et de mémoire que la répartition locale actuelle en fonction du type d’évaluation :


- La recommandation en matière de référence SKU de machine virtuelle varie en fonction des propriétés d’évaluation.
- Cela est affecté par le type d’évaluation que vous effectuez dans Server Assessment : une évaluation effectuée *En fonction des performances* et une évaluation effectuée *Localement*.
- Pour les évaluations en fonction des performances, Server Assessment prend en compte les données d’utilisation des machines virtuelles locales (processeur, mémoire, utilisation des disques et du réseau) afin de déterminer la référence SKU de machine virtuelle cible appropriée pour vos machines virtuelles locales. Il ajoute également un facteur de confort pour déterminer l’utilisation effective.
- Pour le dimensionnement local, les données de performances ne sont pas prises en compte et la référence SKU cible est recommandée en fonction de la répartition locale.

Pour illustrer la façon dont cela peut affecter les recommandations, prenons un exemple :

Nous disposons d’une machine virtuelle locale avec quatre cœurs et huit Go de mémoire, avec une utilisation de l’UC de 50 % et une utilisation de la mémoire de 50 %, ainsi qu’un facteur de confort spécifié de 1,3.

-  Si l’évaluation est définie sur **Localement**, il est recommandé d’utiliser une référence SKU de machine virtuelle Azure avec 4 cœurs et 8 Go de mémoire.
- Si l’évaluation est basée sur les performancess, d’après l’utilisation effective de l’UC et de la mémoire (50 % de 4 cœurs * 1,3 = 2,6 cœurs et 50 % de 8 Go de mémoire * 1,3 = 5,3 Go de mémoire), la référence SKU de machine virtuelle la moins coûteuse avec quatre cœurs (nombre de cœurs pris en charge le plus proche) et huit Go de mémoire (taille de mémoire prise en charge la plus proche) est celle qui est recommandée.
- [En savoir](concepts-assessment-calculation.md#assessments-in-server-assessment) plus sur le dimensionnement de l’évaluation.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Références SKU de disque Azure supérieures aux locales

Azure Migrate Server Assessment peut recommander un disque plus volumineux en fonction du type d’évaluation.
- Dans Server Assessment, le dimensionnement du disque dépend de deux propriétés d’évaluation : le critère de dimensionnement et le type de stockage.
- Si le critère de dimensionnement est **En fonction des performances** et que le type de stockage est défini sur **Automatique**, les valeurs de débit et d’IOPS du disque sont prises en compte lors de l’identification du type de disque cible (HDD Standard, SSD Standard ou Premium). Une référence SKU de disque parmi celles du type approprié est ensuite recommandée conformément aux exigences de taille du disque local.
- Si le critère de dimensionnement est **En fonction des performances** et si le type de stockage est **Premium**, une référence SKU de disque Premium dans Azure est recommandée conformément aux exigences de débit, d’IOPS et de taille du disque local. La même logique est utilisée pour effectuer le dimensionnement du disque quand le critère de dimensionnement est défini sur **Localement** et que le type de stockage est **HDD Standard**, **SSD Standard** ou **Premium**.

Par exemple, si vous avez un disque local avec 32 Go de mémoire, mais que la valeur d’IOPS en lecture et écriture agrégée pour le disque est de 800 IOPS, Server Assessment recommande un disque Premium (en raison des exigences d’IOPS plus élevées), puis recommande une référence SKU de disque capable de prendre en charge la taille et les IOPS nécessaires. La correspondance la plus proche dans cet exemple serait P15 (256 Go, 1100 IOPS). Par conséquent, même si la taille nécessaire au disque local était de 32 Go, Server Assessment a recommandé un disque avec une plus grande taille en raison de l’exigence élevée du disque local concernant les IOPS.

## <a name="utilized-corememory-percentage-missing"></a>Pourcentage de noyau/mémoire utilisé manquant

Server Assessment signale « PercentageOfCoresUtilizedMissing » ou « PercentageOfMemoryUtilizedMissing » lorsque l’appliance Azure Migrate ne peut pas collecter les données de performances pour les machines virtuelles locales appropriées.

- Cela peut se produire si les machines virtuelles sont désactivées pendant la durée de l’évaluation. L’appliance ne peut pas collecter les données de performances d’une machine virtuelle lorsque celle-ci est désactivée.
- S’il manque uniquement les compteurs de mémoire et si vous tentez d’évaluer des machines virtuelles Hyper-V, vérifiez si la mémoire dynamique est activée sur ces machines. Il existe un problème connu pour les machines virtuelles Hyper-V qui empêche l’appliance Azure Migrate de collecter les données d’utilisation de la mémoire pour les machines virtuelles sur lesquelles la mémoire dynamique n’est pas activée.
- Si l’un des compteurs de performances est manquant, Azure Migrate Server Assessment a recours aux cœurs et à la mémoire alloués, et recommande une taille de machine virtuelle adaptée.

## <a name="is-the-operating-system-license-included"></a>La licence du système d’exploitation est-elle incluse ?

Azure Migrate Server Assessment estime le coût de la licence du système d’exploitation pour les ordinateurs Windows, mais pas pour les ordinateurs Linux.

## <a name="how-does-performance-based-sizing-work"></a>Comment le dimensionnement en fonction des performances fonctionne-t-il ?

Server Assessment recueille les données de performances des machines locales de manière continue, et les utilise pour recommander la référence SKU de machine virtuelle et la référence SKU de disque dans Azure. [Découvrez comment](concepts-assessment-calculation.md#calculate-sizing-performance-based) les données basées sur les performances sont collectées.


## <a name="dependency-visualization-in-azure-government"></a>Visualisation des dépendances dans Azure Government

Azure Migrate dépend de Service Map pour la fonctionnalité de visualisation des dépendances. Étant donné que Service Map n’est pas disponible pour Azure Government, cette fonctionnalité de visualisation n’est pas disponible dans Azure Government.

## <a name="dependencies-dont-show-after-agent-install"></a>Les dépendances ne sont pas affichées après l’installation des agents

Une fois que vous avez installé les agents de visualisation des dépendances sur les machines virtuelles locales, Azure Migrate prend généralement 15 à 30 minutes pour afficher les dépendances dans le portail. Si vous avez attendu plus de 30 minutes, assurez-vous que le Microsoft Monitoring Agent (MMA) peut se connecter à l’espace de travail Log Analytics.

Pour les machines virtuelles Windows :
1. Dans le panneau de configuration, démarrez MMA.
2. Dans les **propriétés Microsoft Monitoring Agent** > **Azure Log Analytics (OMS)** , assurez-vous que l’**état** de l’espace de travail est vert.
3. Si l’état n’est pas vert, essayez de supprimer l’espace de travail et de le rajouter à MMA.

    ![État MMA](./media/troubleshoot-assessment/mma-properties.png)

Pour les machines virtuelles Linux, vérifiez que tout s’est bien passé pour les commandes d’installation MMA et Dependency Agent.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

- **Agent MMS** : Passez en revue les systèmes d’exploitation Windows et [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) pris en charge.
- **Agent des dépendances** : les systèmes d’exploitation [Windows et Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) pris en charge.

## <a name="visualize-dependencies-for--hour"></a>Visualiser les dépendances pendant > heure

Même si Azure Migrate vous permet de revenir à une date du mois passé, la durée maximale pendant laquelle vous pouvez visualiser les dépendances est fixée à 1 heure.

Par exemple, vous pouvez utiliser la fonctionnalité de durée dans la carte des dépendances pour voir les dépendances d’hier, mais uniquement sur une période d’une heure.

Vous pouvez aussi utiliser les journaux d’activité Azure Monitor pour [interroger les données de dépendance](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) sur une plus longue durée.

## <a name="visualized-dependencies-for--10-machines"></a>Dépendances visualisées pour > 10 machines

Dans Azure Migrate Server Assessment, vous pouvez [visualiser les dépendances pour des groupes](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) contenant jusqu’à 10 machines virtuelles. Pour les groupes plus grands, nous vous recommandons de fractionner les machines virtuelles en groupes plus petits pour visualiser les dépendances.

## <a name="machines-show-install-agent"></a>Les machines affichent « Installer l’agent »

Après la migration des machines avec la visualisation des dépendances activée vers Azure, les ordinateurs peuvent afficher l’action « Installer l’agent » au lieu de « Afficher les dépendances » en raison du comportement suivant :


- Après la migration vers Azure, les machines locales sont désactivées et les machines virtuelles équivalentes sont lancées dans Azure. Ces machines acquièrent une adresse MAC différente.
- Les ordinateurs peuvent également avoir une adresse IP différente, selon que vous avez ou non conservé l’adresse IP locale.
- Si les adresses MAC et IP sont différentes des adresses locales, Azure Migrate n’associe pas les machines locales aux données de dépendance Service Map. Dans ce cas, l’option d’installation de l’agent s’affiche au lieu des dépendances.
- Après une migration de test vers Azure, les machines locales restent allumées comme prévu. Les machines équivalentes lancées dans Azure acquièrent une adresse MAC différente et peuvent acquérir une adresse IP différente. À moins que vous ne bloquiez le trafic sortant des journaux Azure Monitor provenant de ces machines, Azure Migrate n’associera pas les machines locales aux données de dépendances Service Map, et affichera donc l’option permettant d’installer des agents au lieu de celle permettant d’afficher les dépendances.


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

## <a name="next-steps"></a>Étapes suivantes

[Créer](how-to-create-assessment.md) ou [personnaliser](how-to-modify-assessment.md) une évaluation.
