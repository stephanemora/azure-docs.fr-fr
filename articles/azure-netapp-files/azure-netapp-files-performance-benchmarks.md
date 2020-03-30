---
title: Résultats des tests de performances pour Azure NetApp Files | Microsoft Docs
description: Décrit les résultats des tests d’évaluation des performances pour Azure NetApp Files au niveau du volume.
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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 1d6b43110046f26d8c8070b19587366588eee7b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68881751"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Résultats des tests de performances pour Azure NetApp Files

Cet article décrit les résultats des tests d’évaluation des performances pour Azure NetApp Files au niveau du volume. 

## <a name="sample-application-used-for-the-tests"></a>Exemple d’application utilisée pour les tests

Les tests de performances ont été exécutés avec un exemple d’application utilisant Azure NetApp Files. L’application présente les caractéristiques suivantes : 

* Application Linux conçue pour le cloud.
* Application capable d’adapter son échelle linéairement au nombre de machines virtuelles ajoutées afin d’augmenter la puissance de calcul si nécessaire.
* Nécessite un accès rapide au lac de données.
* Intègre des modèles d’E/S qui sont parfois aléatoires, parfois séquentiels. 
    * Un modèle aléatoire nécessite une faible latence pour de grandes quantités d’E/S. 
    * Un modèle séquentiel nécessite une bande passante conséquente. 

## <a name="about-the-workload-generator"></a>À propos du générateur de charge de travail

Les résultats sont extraits de fichiers de synthèse Vdbench. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) est un utilitaire de ligne de commande qui génère des charges de travail d’E/S de disque pour la validation des performances de stockage. La configuration client-serveur utilisée est évolutive.  Elle comprend un ordinateur maître/client mixte et 14 machines virtuelles clients dédiées.

## <a name="about-the-tests"></a>À propos des tests

Les tests ont été conçus pour identifier les limites potentielles de l’exemple d’application et la courbe de temps de réponse jusqu’aux limites.  

Les tests exécutés sont les suivants : 

* Lecture aléatoire de 8 Kio 100 %
* Écriture aléatoire de 8 Kio 100 %
* Lecture séquentielle de 64 Kio 100 %
* Écriture séquentielle de 64 Kio 100 %
* Lecture séquentielle de 64 Kio 50 %, écriture séquentielle de 64 Kio 50 %
* Lecture aléatoire de 8 Kio 50 %, écriture aléatoire de 8 Kio 50 %

## <a name="bandwidth"></a>Bande passante

Azure NetApp Files propose plusieurs [niveaux de service](azure-netapp-files-service-levels.md). Chaque niveau de service offre une quantité distincte de bande passante par Tio de capacité approvisionnée (quota de volume). La limite de bande passante pour un volume est approvisionnée en fonction de la combinaison du niveau de service et du quota de volume. La limite de bande passante est le seul facteur déterminant de la quantité réelle du débit réalisé.  

Actuellement, le 4 500 Mio est le débit le plus élevé atteint par une charge de travail sur un seul volume dans le cadre d’un test.  Avec le niveau de service Premium, un quota de volume de 70,31 Tio approvisionnera une bande passante suffisante pour réaliser ce débit en vertu du calcul ci-dessous : 

![Formule de bande passante](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Niveau de quota et de service](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Charges de travail intensives en débit

Le test de débit a utilisé Vdbench et une combinaison de machines virtuelles de stockage 12xD32s V3. L’exemple de volume dans le test a obtenu les chiffres de débit suivants :

![Test de débit](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Charges de travail intensives en E/S

Le test d’E/S a utilisé Vdbench et une combinaison de machines virtuelles de stockage 12xD32s V3. L’exemple de volume dans le test a obtenu les chiffres d’E/S suivants :

![Test d’E/S](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latence

La distance entre les machines virtuelles de test et le volume Azure NetApp Files a une incidence sur les performances d’E/S.  Le graphique ci-dessous compare les IOPS aux courbes de réponse de latence pour deux groupes de machines virtuelles.  Un groupe de machines virtuelles est proche d’Azure NetApp Files et l’autre en est plus éloigné.  La latence accrue pour le groupe éloigné de machines virtuelles a une incidence sur la quantité d’IOPS obtenue à un niveau donné de parallélisme.  Malgré tout, les lectures sur un volume peuvent dépasser 300 000 E/S, comme illustré ci-dessous : 

![Étude de latence](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Résumé

Les charges de travail sensibles à la latence (bases de données) peuvent avoir un temps de réponse d’une milliseconde. Les performances transactionnelle peuvent dépasser 300 000 IOPS pour un seul volume.

Les applications sensibles au débit (pour la diffusion en continu et l’acquisition d’images) peuvent atteindre un débit de 4,5 Gio/s.

## <a name="example-scripts"></a>Exemples de scripts

Les exemples de scripts suivants servent uniquement de démonstration.  Ils ne doivent ne pas être utilisés pour les activités de production.  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
