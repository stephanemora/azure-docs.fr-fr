---
title: Calcul de macroordinateur de déplacement pour les Machines virtuelles
description: Comparaison de ressources favorablement à la capacité de macroordinateur de calcul Azure afin de pouvoir migrer et moderniser des applications z14 IBM.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 8aea4a74ba84855f011dada70ea75ec0d5fb64fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485641"
---
# <a name="move-mainframe-compute-to-azure"></a>Déplacer le calcul de macroordinateur vers Azure

Macroordinateurs une réputation pour une haute fiabilité et la disponibilité et continuent à être la dorsale principale approuvée de nombreuses entreprises. Ils sont souvent considérées pour avoir une extensibilité presque illimitée et la puissance de calcul ainsi. Toutefois, certaines entreprises ont dépassé la capacité des plus grande macroordinateurs disponibles. Si cela vous ressemble, Azure offre des économies d’agilité, l’infrastructure et la portée.

Pour exécuter des charges de travail mainframe sur Microsoft Azure, vous devez savoir comment macroordinateur calcul comparaison des fonctionnalités dans Azure. Basé sur un macroordinateur de z14 IBM (le modèle plus récent à ce jour), cet article vous indique comment obtenir des résultats comparables sur Azure.

Pour commencer, prendre en compte les environnements côte à côte. La figure suivante compare un environnement de macroordinateur pour exécuter des applications vers un environnement d’hébergement Azure.

![Services et Azure offre d’environnements émulation comparables prennent en charge et simplifie la migration](media/mainframe-compute-azure.png)

La puissance des grands systèmes est souvent utilisée pour les transactions en ligne (OLTP) les systèmes qui gèrent des millions de mises à jour pour des milliers d’utilisateurs de traitement. Ces applications utilisent souvent des logiciels pour le traitement des transactions, la gestion de l’écran et entrée de formulaire. Ils peuvent utiliser un client contrôle CICS (Information System), système IMS (Information Management) ou Interface Package TIP (Transaction).

Comme dans l’illustration, un émulateur de module de plateforme sécurisée sur Azure peut gérer des charges de travail CICS et IMS. Un émulateur de système de traitement par lots sur Azure joue le rôle de langage JCL (JCL). Données mainframe sont migrées vers des bases de données Azure, comme base de données SQL Azure. Services Azure ou autres logiciels hébergés dans des Machines virtuelles Azure peut être utilisé pour la gestion de système.

## <a name="mainframe-compute-at-a-glance"></a>Calcul de macroordinateur en un clin de œil

Dans le macroordinateur z14, les processeurs sont organisés en quatre *tiroirs*. Un *tiroir* est simplement un cluster de processeurs et de puces. Chaque tiroir peut avoir six chips processeur central active (CP), et chaque CP a 10 puces de contrôleur (SC) système. Dans la terminologie Intel x86, il existe six sockets par quatre bacs tiroir et 10 cœurs par socket. Cette architecture fournit l’équivalent approximatif de 24 sockets et 240 cœurs, maximum, pour un z14.

La rapidité de z14 CP a une vitesse d’horloge 5.2 GHz. En règle générale, un z14 est fourni avec la déclaration CPs dans la zone. Elles ont été activées en fonction des besoins. Un client est couramment facturé pour au moins quatre heures de calcul par mois en dépit de l’utilisation réelle.

Un processeur de macroordinateur peut être configuré comme l’un des types suivants :

- Processeur d’objectif (GP) générales
- Système z intégré d’informations processeur (zIIP)
- Fonctionnalité intégrée pour le processeur de Linux (IFL)
- Processeur du système de Assist (SAP)
- Processeur de couplage Facility (ICF) intégrée

## <a name="scaling-mainframe-compute-up-and-out"></a>Mise à l’échelle de calcul de macroordinateur et

Gros ordinateurs IBM offrent la possibilité de mettre à l’échelle jusqu'à 240 cœurs (la taille z14 actuelle pour un seul système). En outre, les gros ordinateurs IBM peuvent monter en charge via une fonctionnalité appelée la fonctionnalité de couplage (CF). La CF permet à plusieurs systèmes mainframe accéder aux mêmes données simultanément. À l’aide de la cloud Foundry, les processeurs de macroordinateur mainframe Parallel Sysplex technologie groupes dans les clusters. Lorsque ce guide a été écrit, la fonctionnalité Parallel Sysplex pris en charge 32 regroupements de 64 processeurs. Jusqu'à 2 048 processeurs peut être regroupée de cette manière pour faire évoluer la capacité de calcul.

Un CF permet de partager des données avec un accès direct, les clusters de calcul. Il est utilisé pour le verrouillage de plus d’informations, les informations de cache et la liste des ressources de données partagée. Un Sysplex parallèle à l’aide d’un ou plusieurs CFs peut être considéré comme un « partagé tout » cluster de calcul de la montée en puissance. Pour plus d’informations sur ces fonctionnalités, consultez [Parallel Sysplex sur IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) sur le site Web d’IBM.

Applications peuvent utiliser ces fonctionnalités pour fournir des performances de montée en puissance et haute disponibilité. Pour plus d’informations sur l’utilisation CICS Parallel Sysplex avec CF, téléchargez le [IBM CICS et la fonctionnalité de couplage : Pour approfondir](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) rouge.

## <a name="azure-compute-at-a-glance"></a>Calcul Azure en un clin de œil

Certaines personnes pensent par erreur que les serveurs basés sur Intel ne sont pas aussi puissants que les grands systèmes. Cependant, les nouveaux systèmes dense-core Intel et en possèdent bien le calcul comme les grands systèmes. Cette section décrit les options de (IaaS) Azure infrastructure-as-a-service pour le calcul et de stockage. Azure offre platform-as-a-service (PaaS) options également, mais cet article se concentre sur les choix de IaaS qui fournissent une capacité mainframe comparable.

Machines virtuelles Azure fournissent la puissance de calcul dans une plage de tailles et types. Dans Azure, un processeur virtuel (processeurs virtuels) équivaut à peu près à un cœur sur un macroordinateur.

Actuellement, les tailles de plage de Machine virtuelle Azure fournit à partir de 1 à 128 processeurs virtuels. Types de machines virtuelles (VM) sont optimisés pour les charges de travail particulières. Par exemple, la liste suivante présente les types de machine virtuelle (en cours à ce jour) et de leurs utilisations recommandées :

| Taille     | Type et description                                                                 |
|----------|--------------------------------------------------------------------------------------|
| Série D | Usage général avec 64 processeurs virtuels et jusqu'à 3.5 GHz d’horloge                           |
| Série E | Mémoire optimisée avec jusqu'à 64 processeurs virtuels                                                 |
| Série F | Calcul optimisé avec jusqu'à 64 processeurs virtuels et une vitesse d’horloge 3..7 GHz                       |
| Série H | Optimisé pour les applications haute performance computing (HPC)                          |
| Série L | Stockage optimisé pour les applications à haut débit soutenues par des bases de données comme NoSQL |
| Série M | Mémoire et calcul plus grande optimisé des machines virtuelles avec jusqu'à 128 processeurs virtuels                        |

Pour plus d’informations sur les machines virtuelles disponibles, consultez [série de machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Un macroordinateur z14 peut avoir jusqu'à 240 cœurs. Toutefois, les macroordinateurs z14 utilisent presque jamais tous les cœurs pour une application unique ou de la charge de travail. Au lieu de cela, un macroordinateur isole les autres charges de travail en partitions logiques (partitions logiques), et les partitions logiques ont des évaluations, MIPS (Millions d’Instructions par seconde) ou MSU (Million Service Unit). Lors de la détermination de la taille de machine virtuelle comparable nécessaire pour exécuter une charge de travail mainframe sur Azure, évaluation facteur MIPS (ou MSU).

Les éléments suivants sont des estimations générales :

-   150 MIPS par processeur virtuel

-   MIPS 1 000 par processeur

Pour déterminer la taille correcte de la machine virtuelle pour une charge de travail donnée dans une partition logique, commencer par optimiser la machine virtuelle pour la charge de travail. Déterminez ensuite le nombre de processeurs virtuels nécessité. Pessimiste est 150 MIPS par processeur virtuel. Selon cette estimation, par exemple, une machine virtuelle de série F avec 16 processeurs virtuels peut facilement prendre en charge une charge de travail IBM Db2 provenant d’une partition logique avec MIPS 2 400.

## <a name="azure-compute-scale-up"></a>Montée en charge de calcul Azure

Les machines virtuelles de série M peuvent évoluer jusqu'à 128 processeurs virtuels (au moment de la que rédaction de cet article). À l’aide d’une estimation prudente de 150 MIPS par processeur virtuel, la machine virtuelle de série M équivaut à environ 19 000 MIPS. La règle générale pour l’estimation MIPS pour un macroordinateur est MIPS 1 000 par processeur. Un macroordinateur z14 peut avoir jusqu'à 24 processeurs et fournir d’environ 24 000 MIPS pour un système unique de macroordinateur.

Le macroordinateur z14 unique plus grand a environ 5 000 MIPS plus que la machine virtuelle plus disponible dans Azure. Encore, il est important de comparer la façon dont les charges de travail sont déployées. Si le système mainframe est une application et une base de données relationnelle, ils sont généralement déployés sur le macroordinateur physique même, chacun dans sa propre partition logique. La même solution sur Azure est souvent déployée à l’aide d’une machine virtuelle pour l’application et un ordinateur distinct, correctement dimensionné virtuel pour la base de données.

Par exemple, si un système de processeur virtuel M64 prend en charge l’application, et un processeur virtuel M96 est utilisé pour la base de données, environ 150 processeurs virtuels sont nécessaires, ou environ 24 000 MIPS comme le montre l’illustration suivante.

![Comparaison des déploiements de charge de travail de 24 000 MIPS](media/mainframe-compute-mips.png)

L’approche consiste à migrer des partitions logiques vers des machines virtuelles individuelles. Puis Azure facilement monte à la taille nécessaire pour la plupart des applications qui sont déployées sur un système unique de macroordinateur.

## <a name="azure-compute-scale-out"></a>Montée de calcul Azure

Un des avantages d’une solution basée sur Azure est la possibilité de monter en charge. Capacité disponible pour une application de calcul de mise à l’échelle rend presque illimitée. Azure prend en charge plusieurs méthodes de montée en puissance de calcul :

- **Équilibrage de charge sur un cluster.** Dans ce scénario, une application peut utiliser un [équilibreur de charge](/azure/load-balancer/load-balancer-overview) ou resource manager afin de répartir la charge de travail entre plusieurs machines virtuelles dans un cluster. Si davantage de capacité de calcul, machines virtuelles supplémentaires sont ajoutés au cluster.

- **Machines virtuelles identiques.** Dans ce scénario, une application peut mettre à l’échelle à supplémentaire [des ressources de calcul](/azure/virtual-machine-scale-sets/overview) selon l’utilisation de machine virtuelle. Lorsque la demande baisse, le nombre de machines virtuelles dans un groupe identique peut également passer vers le bas, en garantissant une utilisation efficace de puissance de calcul.

- **PaaS mise à l’échelle.** Ressources de calcul de mise à l’échelle les offres PaaS Azure. Par exemple, [Azure Service Fabric](/azure/service-fabric/service-fabric-overview) alloue des ressources de calcul pour faire face aux augmentations dans le volume de requêtes.

- **Clusters Kubernetes.** Les applications sur Azure peuvent utiliser [clusters Kubernetes](/azure/aks/concepts-clusters-workloads) des services de calcul pour les ressources spécifiées. Azure Kubernetes Service (AKS) est un service géré qui orchestre les nœuds Kubernetes, les pools et les clusters sur Azure.

Pour choisir la méthode appropriée pour la mise à l’échelle les ressources de calcul, il est important de comprendre la différence entre Azure et les macroordinateurs. La clé est comment, ou si, les données sont partagées par les ressources de calcul. Dans Azure, les données (par défaut) ne sont pas généralement partagées par plusieurs machines virtuelles. Si le partage de données est requise par cluster de calcul de plusieurs machines virtuelles dans une montée en puissance, les données partagées doivent résider dans une ressource qui prend en charge cette fonctionnalité. Sur Azure, partage de données implique le stockage comme l’explique la section suivante.

## <a name="azure-compute-optimization"></a>Optimisation de calcul Azure

Vous pouvez optimiser chaque niveau de traitement dans une architecture Azure. Utilisez le type plus approprié de machines virtuelles et des fonctionnalités pour chaque environnement. La figure suivante illustre un modèle potentiel pour le déploiement de machines virtuelles dans Azure pour prendre en charge d’une application CICS qui utilise Db2. Dans le site principal, les machines virtuelles de test, de préproduction et de production sont déployées avec une haute disponibilité. Le site secondaire est utilisé pour la sauvegarde et pour la reprise d’activité après sinistre.

Chaque niveau peut également fournir urgence approprié des services de récupération. Par exemple, les machines virtuelles de base de données et de production peuvent nécessiter une reprise d’activité à chaud, alors que les machines virtuelles de développement et de test prennent en charge une reprise d’activité à froid.

![Déploiement à haute disponibilité qui prend en charge la récupération d’urgence](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Étapes suivantes

- [Migration mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe réhébergement sur des Machines virtuelles Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Déplacer le stockage de macroordinateur à Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Ressources d’IBM

- [Parallel Sysplex sur IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS et la fonctionnalité de couplage faible : Notions avancées](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Création des utilisateurs requis pour l’installation d’une fonctionnalité DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [DB2icrt - Commande Créer une instance](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [DB2 pureScale Solution de base de données de cluster](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBMData Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government cloud pour applications mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft et FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Plus de ressources de migration

- [Platform Modernization Alliance : IBM Db2 sur Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Azure Virtual Datacenter: Lift and Shift Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [iSCSI GlusterFS](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
