---
title: Solutions Oracle sur des machines virtuelles Azure | Microsoft Docs
description: En savoir plus sur les configurations prises en charge et les limitations des images de machine virtuelle Oracle sur Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: b62b35320ba1f4473e9b3a039d181d6a2fb58257
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743619"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Images de machine virtuelle Oracle et leur déploiement sur Microsoft Azure

Cet article traite des informations sur les solutions Oracle basées sur des images de machine virtuelle publiées par Oracle sur Azure Marketplace. Si vous êtes intéressé par les solutions d’applications de cross-cloud avec l’Infrastructure de Cloud Oracle, consultez [solutions d’applications Oracle l’intégration de Microsoft Azure et l’Infrastructure de Cloud Oracle](oracle-oci-overview.md).

Pour obtenir la liste des images actuellement disponibles, exécutez la commande suivante :

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Depuis mai 2019 les images suivantes sont disponibles :

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Ces images sont considérées comme de type BYOL (apportez votre propre licence). Par conséquent, vous serez facturé uniquement pour les frais de calcul, de stockage et de mise en réseau induits par l’exécution d’une machine virtuelle.  Vous êtes supposé disposer des licences appropriées pour utiliser les logiciels Oracle, ainsi que d’un contrat de support avec Oracle. Oracle a garanti la mobilité des licence locale vers Azure. Pour plus d’informations sur la mobilité des licences, voir la note publiée [Oracle et Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

Les utilisateurs peuvent également choisir de baser leurs solutions sur une image personnalisée qu’ils créent de toutes pièces dans Azure, ou de charger des images personnalisées à partir de leur environnement local.

## <a name="support-for-jd-edwards"></a>Prise en charge de JD Edwards
Selon la note de prise en charge Oracle [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), JD Edwards EnterpriseOne versions 9.2 et ultérieures sont pris en charge sur **offre de cloud publics** qui répond à leurs propres `Minimum Technical Requirements` (MTR).  Vous devez créer des images personnalisées répondant à leurs spécifications MTR pour la compatibilité des applications logicielles et des systèmes d’exploitation. 

## <a name="oracle-database-vm-images"></a>Images de machine virtuelle de base de données Oracle
Oracle prend en charge l’exécution des éditions Standard d’Oracle DB 12.1 dans Azure sur des images de machine virtuelle basées sur Oracle Linux.  Pour optimiser les performances des charges de travail de production des bases de données Oracle sur Azure, veillez à dimensionner correctement l’image de machine virtuelle et à utiliser des disques managés (fonctionnalité Disques managés) sauvegardés par Stockage Premium. Pour obtenir des instructions sur la façon d’obtenir rapidement une base de données Oracle opérationnelle dans Azure à l’aide de l’image de machine virtuelle publiée d’Oracle, [essayez de suivre la procédure de démarrage rapide de base de données Oracle](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Options de configuration des disques connectés

Les disques attachés s’appuient sur le service de stockage d’objets blob Microsoft Azure. En théorie, chaque disque standard peut proposer un maximum de 500 entrées/sorties par seconde (IOPS). Notre offre de disque Premium est conseillée pour les charges de travail de base de données hautes performances et peut atteindre jusqu’à 5 000 IOPS par disque. Vous pouvez utiliser un seul disque si cela correspond à vos besoins en performances. Toutefois, vous pouvez améliorer les performances d’IOPS effectives si vous utilisez plusieurs disques attachés, répartissez les données de la base de données sur et ensuite utilisez Oracle Automatic Storage Management (ASM). Pour plus d’informations spécifiques d’Oracle ASM, voir [vue d’ensemble du stockage automatique Oracle](https://www.oracle.com/technetwork/database/index-100339.html). Pour obtenir un exemple montrant comment installer et configurer Oracle ASM sur une machine virtuelle Linux Azure, consultez le [installation et configuration d’Oracle Automated Storage Management](configure-oracle-asm.md) didacticiel.

## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle RAC est conçu pour atténuer les conséquences de la défaillance d’un nœud dans une configuration locale de cluster à plusieurs nœuds. Il s’appuie sur deux technologies locales qui ne sont pas natives d’environnements de cloud public hyperscale : réseau multidiffusion et disque partagé. Si votre solution de base de données nécessite Oracle RAC dans Azure, vous devez tiers logiciel tiers pour activer ces technologies. Pour plus d’informations sur Oracle RAC, voir la [FlashGrid SkyCluster page](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Remarques relatives à la haute disponibilité et à la récupération d’urgence
Lorsque vous utilisez des bases de données Oracle dans Azure, vous êtes chargé d’implémenter une solution de récupération d’urgence et disponibilité élevée pour éviter tout temps d’arrêt. 

Haute disponibilité et récupération d’urgence pour Oracle Database Enterprise Edition (sans reposer sur RAC) peut être obtenue sur Azure à l’aide [Data Guard, Active Data Guard](https://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), ou [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate), avec deux bases de données sur deux machines virtuelles distinctes. Ces deux machines doivent se trouver dans le même [réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/), afin de pouvoir être accessibles l’une à l’autre via l’adresse IP privée persistante.  En outre, nous vous recommandons de placer les machines virtuelles au sein du même groupe à haute disponibilité, afin de permettre à Azure de les placer dans des domaines d’erreur et de mise à niveau distincts. Si vous souhaitez disposer de la géo-redondance, configurer les deux bases de données à répliquer entre deux régions différentes et connecter les deux instances avec une passerelle VPN.

Le didacticiel [implémenter Oracle Data Guard sur Azure](configure-oracle-dataguard.md) vous guide dans la procédure de configuration de base sur Azure.  

Grâce à Oracle Data Guard, vous pouvez assurer la haute disponibilité du système en plaçant la base de données primaire sur une machine virtuelle et une base de données secondaire (de veille) sur une autre, et en configurant une réplication monodirectionnelle entre ces bases de données. Vous bénéficiez ainsi d’un accès en lecture à la copie de la base de données. Oracle GoldenGate vous permet de configurer une réplication bidirectionnelle entre les deux bases de données. Pour savoir comment configurer une solution de haute disponibilité pour vos bases de données en utilisant ces outils, voir la documentation relative à [Active Data Guard](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) et [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) sur le site web d’Oracle. Si vous avez besoin d’un accès en lecture et en écriture à la copie de la base de données, vous pouvez utiliser [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Le didacticiel [implémenter Oracle GoldenGate sur Azure](configure-oracle-golden-gate.md) vous guide dans la procédure de configuration de base sur Azure.

En plus d’avoir une solution de haute disponibilité et récupération d’urgence soit architecturée dans Azure, vous devez avoir une stratégie de sauvegarde en place pour restaurer votre base de données. Le didacticiel [sauvegarde et restauration d’une base de données Oracle](oracle-backup-recovery.md) vous guide tout au long de la procédure de base pour l’établissement d’une sauvegarde cohérente.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Images de machines virtuelles Oracle WebLogic Server

* **Le clustering est pris en charge sur la version Enterprise Edition uniquement.** Vous disposez d’une licence pour utiliser le clustering WebLogic uniquement lorsque vous utilisez l’édition Enterprise de WebLogic Server. N’utilisez pas le clustering avec WebLogic Server Standard Edition.
* **La multidiffusion UDP n’est pas gérée.** Azure prend en charge la monodiffusion UDP, mais ni la diffusion, ni la multidiffusion. WebLogic Server est capable de s’appuyer sur les fonctionnalités de monodiffusion UDP d’Azure. Pour de meilleurs résultats monodiffusion UDP, nous recommandons que la taille de cluster WebLogic reste statique ou conservés avec pas plus de 10 serveurs gérés.
* **WebLogic Server attend des ports publics et privés identiques pour l’accès T3 (par exemple, lorsque vous utilisez Enterprise JavaBeans).** Imaginez un scénario multiniveau où une application de couche (EJB) de service est en cours d’exécution sur un cluster WebLogic Server composé de deux ou plusieurs machines virtuelles dans un réseau virtuel nommé *SLWLS*. Le niveau client se trouve dans un sous-réseau différent sur le même réseau virtuel, un programme Java simple s’efforçant d’appeler Enterprise Javabeans dans la couche de service en cours d’exécution. Comme il est nécessaire d’équilibrer la couche de service, un point de terminaison public avec équilibrage de charge doit être créé pour les machines virtuelles du cluster WebLogic Server. Si le port privé que vous spécifiez est différent du port public (par exemple, 7006:7008), une erreur semblable à la suivante se produit :

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   En effet, WebLogic Server attend un port d’équilibreur de charge et un port de serveur géré WebLogic identiques pour tout accès T3 distant. Dans le cas précédent, le client accède au port 7006 (port d’équilibreur de charge) et le serveur managé est à l’écoute sur le port 7008 (port privé). Cette restriction porte uniquement sur l’accès T3, et non sur le protocole HTTP.

   Pour éviter ce problème, utilisez l’une des solutions de contournement suivantes :

  * Utilisez les mêmes numéros de port public et privé pour les points de terminaison avec équilibrage de charge dédiés à l’accès T3.
  * Incluez le paramètre JVM suivant lors du démarrage de WebLogic Server :

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Pour plus d’informations, consultez l’article de la Base de connaissances **860340.1** à l’adresse <https://support.oracle.com>.

* **Limitations relatives à l’équilibrage de charge et au clustering dynamique.** Partons du principe que vous souhaitez utiliser un cluster dynamique dans WebLogic Server et l’exposer en tant que point unique de terminaison public avec équilibrage de charge. Cela est possible tant que vous utilisez un numéro de port fixe pour chacun des serveurs gérés (pas dynamiquement attribués à partir d’une plage) et que vous ne démarrez pas plus de serveurs gérés qu’il ne sont a machines surveillées par l’administrateur. Autrement dit, il n’est pas plus d’un serveur géré par machine virtuelle). Si votre configuration plus de serveurs WebLogic démarrés qu’il n’y a machines virtuelles (autrement dit, si plusieurs instances de WebLogic Server partagent la même machine virtuelle), il n’est pas possible pour plusieurs de ces instances de serveurs WebLogic Pour lier à un numéro de port donné. Les autres sur cette machine virtuelle échouent.

   Si vous configurez le serveur d’administration de façon à affecter automatiquement des numéros de port uniques à ses serveurs managés, aucun équilibrage de charge n’est possible, car Microsoft Azure ne prend pas en charge le mappage d’un port public unique à plusieurs ports privés (opération alors requise pour cette configuration).
* **Plusieurs instances WebLogic Server sur une machine virtuelle.** En fonction des besoins de votre déploiement, vous pouvez envisager d’exécuter plusieurs instances de WebLogic Server sur la même machine virtuelle, si la machine virtuelle est suffisante. Ainsi, sur une machine virtuelle de taille moyenne contenant deux cœurs, vous pouvez opter pour l’exécution de deux instances WebLogic Server. Toutefois, nous recommandons toujours d’éviter d’introduire des points de défaillance uniques dans votre architecture, ce qui serait le cas si vous avez utilisé qu’une seule machine virtuelle qui exécute plusieurs instances de WebLogic Server. À l’aide d’au moins deux machines virtuelles peut être une meilleure approche, et chaque machine virtuelle peut alors exécuter plusieurs instances de WebLogic Server. Chaque instance de WebLogic Server pourrait être toujours partie du même cluster. Toutefois, il n’est actuellement pas possible d’utiliser Azure pour les points de terminaison de l’équilibrage de charge qui sont exposés par de tels déploiements WebLogic Server au sein de la même machine virtuelle, car l’équilibreur de charge Azure nécessite les serveurs avec équilibrage de charge soient répartis entre unique machines virtuelles.

## <a name="oracle-jdk-virtual-machine-images"></a>Images de machine virtuelle JDK Oracle
* **Dernières mises à jour de JDK 6 et 7.** Nous recommandons d’utiliser la dernière version publique de Java prise en charge (il s’agit actuellement de la version 8). Cependant, Azure propose également des images de JDK 6 et 7. Ces images sont destinées aux applications héritées qui ne peuvent pas encore être mises à niveau vers la version 8 de JDK. Il se peut que les mises à jour des images JDK précédentes ne soient plus mises à la disposition du public. Toutefois, suite au partenariat entre Microsoft et Oracle, les images de JDK 6 et 7 proposées par Microsoft Azure ont pour objectif de contenir une mise à jour plus récente, non destinée au public, qui est généralement proposée par Oracle à certains clients spécifiques bénéficiant d’un support Oracle. De nouvelles versions des images de JDK seront mises à disposition au fur et à mesure, en parallèle avec les versions de JDK 6 et 7 mises à jour.

   Le JDK disponible dans le JDK 6 et 7 images et les machines virtuelles et les images dérivées, peuvent uniquement être utilisées dans Azure.
* **JDK 64 bits.** Les images de machine virtuelle Oracle WebLogic Server et les images de machine virtuelle Oracle JDK fournies par Microsoft Azure contiennent les versions 64 bits de Windows Server et de JDK.

## <a name="next-steps"></a>Étapes suivantes
Vous disposez maintenant d’une vue d’ensemble des solutions Oracle actuelles basées sur des images de machine virtuelle dans Microsoft Azure. L’étape suivante consiste à déployer votre première base de données Oracle sur Azure.

> [!div class="nextstepaction"]
> [Créer une base de données Oracle sur Azure](oracle-database-quick-create.md)
