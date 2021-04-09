---
title: Avantages de l’utilisation de Azure NetApp Files pour le déploiement de SQL Server | Microsoft Docs
description: Affiche les avantages d’une analyse détaillée des coûts/performances liés à l’utilisation de Azure NetApp Files pour le déploiement SQL Server.
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
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: 46fe7570b7b9ea9446918d407dbe87596b8d0496
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863902"
---
#  <a name="benefits-of-using-azure-netapp-files-for-sql-server-deployment"></a>Avantages de l’utilisation de Azure NetApp Files pour le déploiement de SQL Server

Azure NetApp Files réduit le coût total de possession (TCO) de SQL Server par rapport aux solutions de stockage en mode bloc.  Avec le stockage en mode bloc, les machines virtuelles ont des limites imposées sur les E/S et la bande passante pour les opérations sur disque. Seules les limites de bande passante réseau sont appliquées à Azure NetApp Files.  En d’autres termes, aucune limite d’E/S au niveau de la machine virtuelle n’est appliquée à Azure NetApp Files. Sans ces limites d’E/S, les instances SQL Server s’exécutant sur des machines virtuelles de plus petite taille connectées à Azure NetApp Files peuvent fonctionner aussi bien que les instances SQL Server s’exécutant sur des machines virtuelles d’une taille bien plus grande. Le dimensionnement des instances à la baisse réduit le coût du calcul de 25 % par rapport à l’ancien tarif.  *Vous pouvez réduire les coûts de calcul avec Azure NetApp Files.*  

Toutefois, les coûts de calcul sont peu élevés par rapport aux coûts de licence SQL Server.  Les [licences](https://download.microsoft.com/download/B/C/0/BC0B2EA7-D99D-42FB-9439-2C56880CAFF4/SQL_Server_2017_Licensing_Datasheet.pdf) Microsoft SQL Server sont liées au nombre de cœurs physiques. Par conséquent, la réduction de la taille de l’instance introduit une économie encore plus importante pour les licences logicielles. *Vous pouvez réduire les coûts liés aux licences logicielles avec Azure NetApp Files.*

Le coût du stockage lui-même varie en fonction de la taille réelle de la base de données. Quel que soit le stockage sélectionné, la capacité a un coût, qu’il s’agisse d’un disque managé ou d’un partage de fichiers.  À mesure que la taille des bases de données augmente et que le coût du stockage augmente, le stockage contribue à l’augmentation du coût TCO, affectant le coût global.  Par conséquent, l’assertion est ajustée comme suit : *Vous pouvez réduire les coûts de déploiement de SQL Server avec Azure NetApp Files.* 

Cet article affiche les avantages d’une analyse détaillée des coûts/performances liés à l’utilisation de Azure NetApp Files pour le déploiement SQL Server. Non seulement les plus petites instances ont suffisamment d’UC pour permettre l’exécution de tâches de base de données uniquement en mode bloc sur des instances plus grandes, mais *dans de nombreux cas, les plus petites instances sont même encore plus performantes que leurs équivalents de plus grande taille, basés sur disque, grâce à Azure NetApp Files.* 

## <a name="detailed-cost-analysis"></a>Analyse du coût détaillée 

Les deux ensembles de graphiques de cette section illustrent l’exemple de coût total de possession.  Le nombre et le type de disques managés, le niveau de service Azure NetApp Files et la capacité pour chaque scénario ont été sélectionnés pour obtenir le meilleur rapport prix-capacité-performance.  Chaque graphique est constitué de machines groupées (D16 avec Azure NetApp Files, par rapport à D64 avec disque managé par exemple), et les prix sont répartis pour chaque type de machine.  

Le premier ensemble de graphiques montre le coût global de la solution à l’aide d’une taille de base de données de 1 Tio, en comparant l’instance D16s_v3 à l’instance D64, l’instance D8 à l’instance D32 et l’instance D4 à l’instance D16. Les E/S par seconde prévues pour chaque configuration sont indiquées par une ligne verte ou jaune et correspondent à l’axe Y du côté droit.

[ ![Graphique illustrant le coût global de la solution à l’aide d’une taille de base de données de 1 Tio. ](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png#lightbox)


Le deuxième ensemble de graphiques montre le coût global avec une base de données de 50 Tio. Sinon, les comparaisons restent inchangées : comparaison de l’instance D16 avec Azure NetApp Files par rapport à l’instance D64 en mode bloc, par exemple. 

[ ![Graphique illustrant le coût global avec une taille de base de données de 50 Tio.](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png) ](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png#lightbox)
 
## <a name="performance-and-lots-of-it"></a>Nombreuses performances  

Pour fournir une assertion de réduction des coûts significative nécessitant des performances élevées, les plus grandes instances de l’inventaire Azure général prennent en charge 80 000 E/S par seconde de disque par exemple. Un volume Azure NetApp Files unique peut atteindre 80 000 E/S par seconde sur base de données, et des instances telles que D16 ont une consommation identique. L’instance D16, affichant habituellement 25 600 E/S par seconde sur disque a une taille équivalant à 25 % de celle du D64.  L’instance D64s_v3 a une capacité de 80 000 E/S par seconde sur disque et, par conséquent, présente un excellent point de comparaison de niveau supérieur.

L’instance D16s_v3 peut piloter un volume Azure NetApp Files pouvant atteindre 80 000 E/S par seconde sur base de données. Comme l’a prouvé l’outil de référence SQL Storage Benchmark (SSB), l’instance D16 a atteint une charge de travail 125 % supérieure à celle réalisable sur disque avec l’instance D64.  Pour plus d’informations sur l’outil, consultez la section [outil de test SSB](#ssb-testing-tool).

À l’aide d’une taille de jeu de travail de 1 Tio et d’une charge de travail SQL Server répartie à 80 % en lecture et 20 % en mise à jour, les capacités de performances de la plupart des instances de la classe d’instance D ont été mesurées, pas toutes, mais la plupart, car les instances D2 et D64 sont elles-mêmes exclues des tests. La première a été omise, car elle ne prend pas en charge les performances réseau accélérées et la seconde, car il s’agit du point de comparaison. Consultez le graphique suivant pour comprendre les limites des instances D4s_v3, D8s_v3, D16s_v3 et D32s_v3, respectivement.  Les tests de stockage sur disque managé ne sont pas illustrés dans le graphique. Les valeurs de comparaison sont extraites directement à partir de la [table de limites de machines virtuelles Azure](../virtual-machines/dv3-dsv3-series.md) pour le type d’instance de classe D.

Avec Azure NetApp Files, chacune des instances de la classe D peut atteindre ou dépasser les capacités de performances sur disque des instances deux fois plus grandes.  *Vous pouvez réduire considérablement les coûts liés aux licences logicielles avec Azure NetApp Files.*  

* L’utilisation de l’UC à 75 % de l’instance D4 correspond aux capacités sur disque de l’instance D16.  
    * La vitesse sur l’instance D16 est limitée à 25 600 E/S par seconde sur disque.  
* L’utilisation de l’UC à 75 % de l’instance D8 correspond aux capacités sur disque de l’instance D32.  
    * La vitesse sur l’instance D32 est limitée à 51 200 E/S par seconde sur disque.  
* L’utilisation de l’UC à 55 % de l’instance D16 correspond aux capacités sur disque de l’instance D64.  
    * La vitesse sur l’instance D64 est limitée à 80 000 E/S par seconde sur disque.  
* L’utilisation de l’UC à 15 % de l’instance D32 correspond aux capacités sur disque de l’instance D64 également.  
    * Comme indiqué ci-dessus, la vitesse sur l’instance D64 est limitée à 80 000 E/S par seconde sur disque.  

### <a name="s3b-cpu-limits-test--performance-versus-processing-power"></a>Test de limites du processeur S3B - Performances et puissance de traitement

Le diagramme suivant résume le test de limites de l’UC S3B :

![Diagramme qui affiche le pourcentage moyen d’utilisation de l’UC pour SQL Server à instance unique sur Azure NetApp Files.](../media/azure-netapp-files/solution-sql-server-single-instance-average-cpu.png)

La scalabilité n’est pas le seul facteur. La latence est également un facteur important.  C’est une chose pour les machines virtuelles plus petites de pouvoir augmenter considérablement les taux d’E/S, c’en est une autre de le faire avec des faibles latences à un chiffre, comme illustré ci-dessous.  

* L’instance D4 a affiché 26 000 E/S par seconde sur Azure NetApp Files avec une latence de 2,3 ms.  
* L’instance D8 a affiché 51 000 E/S par seconde sur Azure NetApp Files avec une latence de 2 ms.  
* L’instance D16 a affiché 88 000 E/S par seconde sur Azure NetApp Files avec une latence de 2,8 ms.
* L’instance D32 a affiché 80 000 E/S par seconde sur Azure NetApp Files avec une latence de 2,4 ms.  

### <a name="s3b-per-instance-type-latency-results"></a>Résultats de la latence par type d’instance sur S3B

Le diagramme suivant illustre la latence pour les instances SQL Server à instance unique sur Azure NetApp Files :

![Diagramme illustrant la latence pour les instance SQL Server à instance unique sur Azure NetApp Files.](../media/azure-netapp-files/solution-sql-server-single-instance-latency.png)

## <a name="ssb-testing-tool"></a>Outil de test SSB 
 
L’outil de référence [TPC-E](http://www.tpc.org/tpce/), par conception, insiste sur l’aspect *calcul* plutôt que sur l’aspect *stockage*. Les résultats des tests présentés dans cette section sont basés sur un outil de test de contrainte nommé SQL Storage benchmark (SSB).  Le test SQL Server Storage Benchmark peut exécuter du code SQL à grande échelle sur une base de données SQL Server pour simuler une charge de travail OLTP, similaire à l'[outil de référence Oracle SLOB2](https://kevinclosson.net/slob/). 

L’outil SSB génère une charge de travail SELECT et UPDATE qui envoie les instructions directement à la base de données SQL Server s’exécutant sur la machine virtuelle Azure.  Pour ce projet, les charges de travail SSB sont passées de 1 à 100 utilisateurs SQL Server, avec 10 ou 12 points intermédiaires à 15 minutes par nombre d’utilisateurs.  Toutes les mesures de performance ont été exécutées du point de vue de Perfmon. À des fins de répétabilité, le test SSB a été exécuté trois fois par scénario. 

Les tests eux-mêmes ont été configurés à 80 % avec des instructions SELECT et à 20 % avec des instructions UPDATE, donc 90 % de lectures aléatoires.  La base de données elle-même créée par SSB a une taille de 1 000 Go. Elle comprend 15 tables utilisateur et 9 millions lignes par table utilisateur et 8 192 octets par ligne. 

L’outil de référence SSB est un outil open source.  Il est disponible gratuitement sur la [page GitHub SQL Storage Benchmark](https://github.com/NetApp/SQL_Storage_Benchmark.git).  


## <a name="in-summary"></a>En résumé  

Avec Azure NetApp Files, vous pouvez augmenter considérablement les performances de SQL Server tout en réduisant considérablement le coût total de possession. 

## <a name="next-steps"></a>Étapes suivantes

* [Créer un volume SMB pour Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Architectures de solution avec Azure NetApp Files - SQL Server](azure-netapp-files-solution-architectures.md#sql-server) 

