---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: ecf70bbbeae8fd68309f3343615f021038fb10b6
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66111435"
---
La famille de machines virtuelles de série B vous permet de choisir la taille de machine virtuelle vous offrant les performances de base nécessaires à votre charge de travail, avec la possibilité d’étendre jusqu’à 100 % les performances d’un processeur virtuel Intel® Broadwell E5-2673 v4 2.3 GHz ou Intel® Haswell 2.4 GHz E5-2673 v3.

Les machines virtuelles de la série B sont idéales pour les charges de travail ne nécessitant pas en permanence les performances complètes du processeur, comme les serveurs web, les preuves de concept, les petites bases de données et les environnements de build pour le développement. Ces charges de travail ont généralement des exigences modulables en termes de performances. La série B vous offre la possibilité d’acheter une taille de machine virtuelle aux performances de base. Ainsi, l’instance de machine virtuelle génère des crédits lorsqu’elle n’utilise pas la totalité de ses performances. Dès que la machine virtuelle a cumulé des crédits, elle peut étendre ses performances en utilisant jusqu’à 100 % du processeur virtuel lorsque l’application requiert des performances de processeur plus élevées.

La série B est disponible dans les six tailles de machines virtuelles suivantes :

| Taille             | Processeurs virtuels  | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Perf. du processeur de base de machine virtuelle | Perf. du processeur max. de machine virtuelle | Crédits initiales | Crédits cumulés/heure | Crédits cumulés max. | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / Mbit/s  | Débit du disque non mis en cache max. : IOPS / Mbit/s  | Nombre max de cartes réseau |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5 %                   | 100 %                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100 %                   | 30                   | 6.                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100 %                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60 %                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |

<sup>1</sup> B1ls est pris en charge uniquement sur Linux

## <a name="q--a"></a>Questions et réponses 

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>Q : Comment obtenir 135 % les performances de base à partir d’une machine virtuelle ?
**R** : Le % 135 est partagé entre les 8 processeurs virtuels qui composent la taille de machine virtuelle. Par exemple, si votre application utilise 4 des 8 cœurs travaillant au traitement par lots et que chacun de ces 4 processeurs virtuels est utilisé à 30 %, la quantité totale des performances du processeur de la machine virtuelle serait égale à 120 %.  Ce qui signifie que votre machine virtuelle générerait un crédit temps basé sur le delta de 15 % à partir de vos performances de base.  Cela signifie également que lorsque vous disposez de crédits, cette même machine virtuelle peut utiliser la totalité des 8 processeurs virtuels pour obtenir une performance de processeur maximale de 800 %.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>Q : Comment puis-je surveiller mon solde de crédit et la consommation
**R** : Nous allons présenter 2 nouvelles mesures dans les prochaines semaines, le **crédit** vous permettra d’afficher les crédits combien votre machine virtuelle a cumulés et les **ConsumedCredit** métrique affichera les crédits de processeur a de votre machine virtuelle consommés à partir de la banque.    Ces mesures figurent sur le volet des mesures du portail ou sont visibles par programme via les API Azure Monitor.

Pour en savoir plus sur l’accès aux données de mesure pour Azure, consultez [Vue d’ensemble des mesures dans Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>Q : Comment les crédits sont-ils cumulés ?
**R** : Les tarifs de cumul et des machines virtuelles sont configurés pour une machine virtuelle en cours d’exécution à son niveau de performances de base exactement aura ni un cumul net ou la consommation de crédit.  Une machine virtuelle connaît une augmentation nette de ses crédits chaque fois qu’elle s’exécute sous son niveau de performances de base, et une diminution nette de ses crédits chaque fois qu’elle utilise le processeur à un niveau plus élevé de performances.

**Exemple**:  Déployer une machine virtuelle à l’aide de la taille B1ms pour mon petit laps de temps et l’application de base de données de présence. Cette taille permet à mon application d’utiliser jusqu’à 20 % d’un processeur virtuel qui est considéré comme étant ma base, soit 0,2 crédit par minute utilisable ou cumulable. 

Mon application est occupée en début et fin de journée de travail de mes employés, soit entre 7 h 00 et 9 h 00 et 16 h 00 et 18 h 00. Pendant les 20 heures restantes de la journée, mon application est en général en veille et n’utilise que 10 % du processeur virtuel. Pendant les heures creuses, je cumule 0,2 crédit par minute et utilise uniquement 0,1 crédit par minute. Ainsi, ma machine virtuelle cumule 0,1 x 60, soit 6 crédits par heure.  Pendant les 20 heures creuses, je cumule 120 crédits.  

Pendant les heures de pointe, mon application utilise en moyenne 60 % du processeur virtuel. Je cumule toujours 0,2 crédit par minute, mais j’utilise 0,6 crédit par minute pour un coût net de 0,4 crédit par minute ou 0,4 x 60 = 24 crédits par heure. Je dispose de 4 heures d’utilisation de pointe par jour, ce qui coûte 4 x 24 = 96 crédits.

Si je prends les 120 crédits cumulés lors des heures creuses et que je soustrais les 96 crédits utilisés pendant mes heures de pointe, je cumule 24 crédits supplémentaires par jour utilisables pour d’autres pics d’activité.


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>Q : La série B prend-elle en charge les disques de données de stockage Premium ?
**R** : Oui, toutes les tailles de série B prend en charge les disques de données de stockage Premium.   
    
### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>Q : Pourquoi mon crédit restant est défini à 0 après un redéploiement ou de démarrage/arrêt ?
**A** : Quand une machine virtuelle est « REDPLOYED » et la machine virtuelle est déplacée vers un autre nœud, le crédit cumulé est perdue. Si la machine virtuelle est arrêtée/démarrée, mais reste sur le même nœud, la machine virtuelle conserve le crédit cumulé. Lorsque la machine virtuelle redémarre de zéro sur un nœud, elle obtient un crédit initial, de 240 minutes pour  Standard_B8ms.
    
### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>Q : Que se passe-t-il si je déploie une image de système d’exploitation non pris en charge sur B1ls ?
**A** : B1ls prend uniquement en charge les images Linux et si vous déployez les autre image de système d’exploitation vous ne pouvez pas obtenir la meilleure expérience utilisateur.
    
### <a name="q-why-is-there-no-pricing-information-for-b1ls-windows"></a>Q : Pourquoi n’existe-t-il aucune information tarifaire pour B1ls windows ?
**A** : B1ls prend uniquement en charge les images Linux et si vous déployez toute une autre image de système d’exploitation que vous ne pouvez pas obtenir la meilleure expérience utilisateur mais vous êtes facturé.


    

    
