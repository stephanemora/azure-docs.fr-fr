---
title: Références SKU pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Références SKU pour SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/03/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d5ed903eefea3a8c13bb9e43f5ef71063b453d93
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806641"
---
# <a name="available-skus-for-hli"></a>Références SKU disponibles pour HLI

Le service SAP HANA sur Azure (Grandes instances) basé sur les tampons Révision 3 est disponible dans plusieurs configurations dans les régions suivantes :

- USA Ouest
- USA Est
- Australie Est
- Sud-Australie Est
- Europe Ouest
- Europe Nord
- Japon Est
- OuJapon Est

Le service SAP HANA sur Azure (Grandes instances) basé sur des tampons Révision 4 est disponible dans plusieurs configurations dans les régions suivantes :

- USA Ouest 2
- USA Est
- États-Unis - partie centrale méridionale
- Europe Ouest
- Europe Nord



Liste des [Références SKU certifiées SAP HANA des grandes instances HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) telles que :

| Solution SAP | UC | Mémoire | Stockage | Disponibilité |
| --- | --- | --- | --- | --- |
| Optimisé pour OLAP : SAP BW, BW/4HANA<br /> ou SAP HANA pour les charges de travail OLAP génériques | SAP HANA sur Azure S72<br /> \- 2 x processeurs Intel® Xeon® E7-8890 v3<br /> 36 cœurs et 72 threads d’UC |  768 Go |  3 To | Plus proposé |
| --- | SAP HANA sur Azure S144<br /> \- 4 x processeurs Intel® Xeon® E7-8890 v3<br /> 72 cœurs et 144 threads d’UC |  1,5 To |  6 To | Plus proposé |
| --- | SAP HANA sur Azure S192<br /> \- 4 x processeurs Intel® Xeon® E7-8890 v4<br /> 96 cœurs et 192 threads d’UC |  2 TO |  8 To | Plus proposé |
| --- | SAP HANA sur Azure S224<br /> – Processeur 4x Intel® Xeon® Platinum 8276 (également appelé Cascade Lake)<br /> 112 cœurs et 224 threads de processeur |  3 TO |  6,3 To | Disponible dans les tampons des révisions 3 et 4  |
| --- | SAP HANA sur Azure S384<br /> \- 8 x processeurs Intel® Xeon® E7-8890 v4<br /> 192 cœurs et 384 threads d’UC |  4 TO |  16 TO | Disponible dans les tampons de révision 4 |
| Optimisé pour OLTP : SAP Business Suite<br /> sur SAP HANA ou S/4HANA (OLTP),<br /> OLTP générique | SAP HANA sur Azure S72m<br /> \- 2 x processeurs Intel® Xeon® E7-8890 v3<br /> 36 cœurs et 72 threads d’UC |  1,5 To |  6 To | Plus proposé |
|---| SAP HANA sur Azure S144m<br /> \- 4 x processeurs Intel® Xeon® E7-8890 v3<br /> 72 cœurs et 144 threads d’UC |  3 TO |  12 To | Plus proposé |
|---| SAP HANA sur Azure S192m<br /> \- 4 x processeurs Intel® Xeon® E7-8890 v4<br /> 96 cœurs et 192 threads d’UC  |  4 TO |  16 TO | Plus proposé |
|---| SAP HANA sur Azure S384m<br /> \- 8 x processeurs Intel® Xeon® E7-8890 v4<br /> 192 cœurs et 384 threads d’UC |  6,0 To |  18 To | Disponible dans les tampons de révision 4|
|---| SAP HANA sur Azure S384xm<br /> \- 8 x processeurs Intel® Xeon® E7-8890 v4<br /> 192 cœurs et 384 threads d’UC |  8,0 To |  22 To |  Disponible dans les tampons de révision 4 |
|---| SAP HANA sur Azure S576m<br /> \- 12 x processeurs Intel® Xeon® E7-8890 v4<br /> 288 cœurs et 576 threads d’UC |  12,0 To |  28 To | Disponible dans les tampons de révision 4|
|---| SAP HANA sur Azure S768m<br /> \- 16 x processeurs Intel® Xeon® E7-8890 v4<br /> 384 cœurs et 768 threads d’UC |  16,0 To |  36 To | Disponible dans les tampons de révision 4|
|---| SAP HANA sur Azure S960m<br /> \- 20 x processeurs Intel® Xeon® E7-8890 v4<br /> 480 cœurs et 960 threads d’UC |  20,0 To |  46 To | Disponible dans les tampons de révision 4|


Sous SAP HANA TDIv5, SAP permet un dimensionnement personnalisé et des projets propres au client qui peuvent déboucher sur des configurations de serveur qui ne sont pas répertoriées comme certifiées dans :

- [Appliances certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

Dans bien des cas, ces configurations de serveur propres au client comportent plus de mémoire que les unités de serveur certifiées pour SAP. Lorsqu’ils travaillent avec SAP, les clients ont bien la possibilité de recourir au support technique SAP pour faire certifier leurs configurations de serveur aux tailles spécifiques du client. Dans Azure, les références SKU standard des grandes instances HANA suivantes sont disponibles, et affichées dans les tarifs Microsoft pour de tels projets TDIv5 de dimensionnement propre au client.

| SKU|UC | Mémoire | Stockage | Disponibilité |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA sur Azure S96<br /> \- 2 x processeurs Intel® Xeon® E7-8890 v4<br /> 48 cœurs et 96 threads d’UC |  768 Go |  3 To | Disponible dans les tampons des révisions 3 et 4|


| Référence SKU d’origine qui peut être <br /> étendue dans la mémoire | UC | Mémoire | Stockage | Disponibilité |
| --- | --- | --- | --- | --- |
| S192m peut être étendue à | SAP HANA sur Azure S192xm<br /> \- 4 x processeurs Intel® Xeon® E7-8890 v4<br /> 96 cœurs et 192 threads d’UC |  6,0 To |  16 TO | Plus proposé |
| S384xm peut être étendue à | SAP HANA sur Azure S384xxm<br /> \- 8 x processeurs Intel® Xeon® E7-8890 v4<br /> 192 cœurs et 384 threads d’UC |  12,0 To |  28 To | Disponible dans les tampons de révision 4 |
| S576m peut être étendue à | SAP HANA sur Azure S576xm<br /> \- 12 x processeurs Intel® Xeon® E7-8890 v4<br /> 288 cœurs et 576 threads d’UC |  18,0 To |  41 To | Disponible dans les tampons de révision 4|
| S768m peut être étendue à | SAP HANA sur Azure S768xm<br /> \- 16 x processeurs Intel® Xeon® E7-8890 v4<br /> 384 cœurs et 768 threads d’UC |  24,0 To |  56 To | Disponible dans les tampons de révision 4 |

- Cœurs d’unité centrale = somme des cœurs d’UC non multithreads dérivée de la somme des processeurs de l’unité de serveur.
- Threads d’UC = somme des threads de calcul fournis par les cœurs d’UC multithreads dérivée de la somme des processeurs de l’unité de serveur. La plupart des unités sont configurées par défaut pour utiliser la technologie Hyper-Threading.
- D’après les recommandations du fournisseur, S768xm et S960m ne sont pas configurées en vue d’utiliser la technologie Hyper-Threading pour l’exécution de SAP HANA.


Les configurations spécifiques choisies dépendent de la charge de travail, des ressources d’UC et de la mémoire souhaitée. La charge de travail OLTP est en mesure d’utiliser des références SKU optimisées pour les charges de travail OLAP. 

La base matérielle des offres, à l’exception des unités pour les projets de redimensionnement spécifique au client, est certifiée SAP HANA TDI. Deux classes de matériel différentes divisent les références SKU comme suit :

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm et S224, nommées références SKU de « classe de type I ».
- S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm et S960m, nommées références SKU de « classe de type II ».
- Si vous vous intéressez à d’autres offres de références SKU S224 de 4,5 à 9 To avec Optane, contactez l’équipe des comptes Microsoft pour obtenir plus d’informations. 


Un tampon de grande instance HANA complet n’est pas exclusivement alloué à l’utilisation d’un client unique. Cela s’applique également aux racks de ressources de calcul et de stockage connectés par le biais d’une structure réseau déployée dans Azure. L’infrastructure de grande instance HANA, par exemple Azure, déploie plusieurs &quot;abonnés&quot; client isolés les uns des autres selon les trois niveaux suivants :

- **Réseau** : isolation par le biais des réseaux virtuels dans le tampon de grande instance HANA.
- **Stockage** : isolation par le biais de machines virtuelles auxquelles des volumes de stockage sont assignés et qui isolent les volumes de stockage entre les abonnés.
- **Calcul** : assignation dédiée des unités de serveur à un seul abonné. Aucun partitionnement matériel ou logiciel des unités de serveur. Aucun partage d’une unité de serveur ou hôte unique entre les locataires. 

Les déploiements d’unités de grande instance HANA entre les différents abonnés ne sont pas visibles entre eux. Les unités de grande instance HANA déployées dans différents abonnés ne peuvent pas communiquer directement entre elles au niveau du tampon de grande instance HANA. Seules les unités de grande instance HANA d’un abonné unique peuvent communiquer entre elles au niveau du tampon de grande instance HANA.

Un abonné déployé dans le tampon de grande instance est assigné à un abonnement Azure dans le cadre de la facturation. En termes de réseau, il est accessible à partir des réseaux virtuels d’autres abonnements Azure dans la même inscription Azure. Si vous effectuez un déploiement avec un autre abonnement Azure dans la même région Azure, vous pouvez également demander à obtenir un locataire de grande instance HANA distinct.

Il existe des différences importantes entre l’exécution de SAP HANA sur la grande instance HANA et l’exécution de SAP HANA sur des machines virtuelles déployées dans Azure :

- Il n’existe aucune couche Virtualisation pour SAP HANA sur Azure (grandes instances). Vous bénéficiez des performances du matériel nu sous-jacent.
- Contrairement à Azure, le serveur SAP HANA sur Azure (grandes instances) est dédié à un client spécifique. Il est impossible qu’une unité de serveur ou un hôte fasse l’objet d’un partitionnement matériel ou logiciel. Par conséquent, une unité de grande instance HANA est assignée dans son intégralité à un abonné, et donc à vous en tant que client. Un redémarrage ou un arrêt du serveur n’entraîne pas automatiquement le déploiement du système d’exploitation et de SAP HANA sur un autre serveur. (Pour les références SKU de classe de type I, la seule exception est lorsqu’un serveur rencontre des problèmes et qu’un redéploiement doit être effectué sur un autre serveur.)
- Contrairement à Azure, où les types de processeurs hôtes sont sélectionnés en fonction du meilleur rapport prix/performances, les types de processeurs choisis pour SAP HANA sur Azure (grandes instances) sont les plus performants de la gamme de processeurs Intel E7v3 et E7v4.

**Étapes suivantes**
- Voir [Dimensionnement de HLI](hana-sizing.md)
