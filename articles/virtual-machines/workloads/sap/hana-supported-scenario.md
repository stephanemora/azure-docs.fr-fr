---
title: Scénarios pris en charge pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Scénarios pris en charge avec les détails de leur architecture pour SAP HANA sur Azure (grandes instances)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7ed63f5caa6b1f1c0072a92f6a60ad43c5431af0
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538375"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Scénario pris en charge pour des grandes instances HANA
Ce document décrit les scénarios pris en charge, avec les détails de leur architecture pour les grandes instances HANA.

>[!NOTE]
>Si le scénario voulu n’est pas mentionné ici, contactez l’équipe de gestion des services Microsoft pour évaluer vos besoins.
Avant de procéder au provisionnement de l’unité HLI (Hana Large Instances), validez la conception avec SAP ou avec votre partenaire d’implémentation du service.

## <a name="terms-and-definitions"></a>Termes et définitions
Découvrons les termes et définitions utilisés dans ce document.

- SID : Identificateur système du système HANA.
- HLI (Hana Large Instances) : Grandes instances Hana.
- Reprise d’activité après sinistre : Site de reprise après sinistre.
- Reprise d’activité après sinistre normale : Configuration système utilisant des ressources dédiées à des fins de reprise après sinistre uniquement.
- Récupération d’urgence polyvalente : Système sur le site de reprise après sinistre, configuré pour utiliser un environnement hors production en même temps qu’une instance de production configurée pour une utilisation sur un événement de reprise après sinistre. 
- SID unique :  Système doté d’une instance installée.
- Multi SID : Système doté de plusieurs instances configurées. Également appelé environnement MCOS.
- HSR : Réplication du système SAP HANA.

## <a name="overview"></a>Vue d'ensemble
Les grandes instances HANA prennent en charge un large éventail d’architectures pour répondre aux besoins de votre entreprise. La liste suivante présente les scénarios ainsi que les détails de leur configuration. 

La conception de l’architecture dérivée s’appuie exclusivement sur la perspective de l’infrastructure, et vous devez consulter SAP ou vos partenaires d’implémentation pour le déploiement HANA. Si vos scénarios ne sont pas répertoriés, contactez l’équipe des comptes Microsoft pour examiner l’architecture et trouver une solution qui vous convienne.

**Ces architectures sont entièrement conformes à la conception TDI (Tailored Data Integration) et sont prises en charge par SAP.**

Ce document détaille les deux composants dans chaque architecture prise en charge :

- Ethernet
- Stockage

### <a name="ethernet"></a>Ethernet

Chaque serveur provisionné est préconfiguré avec les jeux d’interfaces Ethernet. Voici les détails des interfaces Ethernet configurées sur chaque unité HLI.

- **R** : Interface utilisée pour/par l’accès client.
- **B** : Interface utilisée pour la communication nœud à nœud. Interface configurée sur tous les serveurs (quelle que soit la topologie demandée), mais n’est utilisée que pour les 
- scénarios de scale-out.
- **C** : Interface utilisée pour la connectivité nœud au stockage.
- **D** : Interface utilisée pour la connexion nœud à appareil ISCSI dans l’installation STONITH. Configurée uniquement lorsque l’installation de HSR est demandée.  

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | Nœud à nœud |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | STONITH |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Nœud à nœud |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

Vous utilisez les interfaces en fonction de la topologie configurée sur l’unité HLI. Par exemple, l’interface « B » est configurée pour la communication de nœud à nœud, ce qui est utile lorsque vous avez une topologie de montée en charge configurée. Dans le cas d’une configuration de montée en puissance sur un nœud unique, cette interface n’est pas utilisée. Étudiez les scénarios dont vous avez besoin (plus loin dans ce document) pour obtenir plus d’informations sur l’utilisation de l’interface. 

Si nécessaire, vous pouvez définir vous-même des cartes réseau supplémentaires. Par contre, la configuration des cartes réseau existantes NE peut PAS être modifiée.

>[!NOTE]
>Vous pouvez toujours trouver des interfaces supplémentaires qui sont des interfaces physiques ou de liaison. Tenez plutôt compte des interfaces mentionnées ci-dessus pour votre cas d’utilisation, les autres peuvent être ignorées/ou elles ne doivent pas être touchées.

Pour les unités avec deux adresses IP affectées, voici à quoi devrait ressembler la distribution :

- L’adresse IP affectée à « Ethernet A » doit se trouver hors de la plage du pool d’adresses IP du serveur envoyé à Microsoft. Cette adresse IP doit être utilisée pour maintenir le fichier /etc/hosts du système d’exploitation à jour.

- L’adresse IP affectée à « Ethernet C » doit être utilisée pour la communication à NFS. Par conséquent, ces adresses **N’ONT PAS BESOIN** d’être mises à jour dans le fichier etc/hosts pour autoriser un trafic interinstance au sein du locataire.

Une configuration à deux adresses IP ne convient pas aux déploiements de réplication système HANA ou aux montées en charge HANA. Si seules deux adresses IP sont affectées et si vous souhaitez déployer une telle configuration, contactez SAP HANA sur Azure Service Management pour obtenir une troisième adresse IP dans un troisième VLAN. Pour les unités de grande instance HANA avec trois adresses IP affectées à trois ports de carte réseau, les règles d’utilisation suivantes s’appliquent :

- L’adresse IP affectée à « Ethernet A » doit se trouver hors de la plage du pool d’adresses IP du serveur envoyé à Microsoft. Par conséquent, cette adresse IP ne doit pas être utilisée pour maintenir le fichier /etc/hosts du système d’exploitation à jour.

- Ethernet « B » doit être utilisé exclusivement dans le cadre de la mise à jour du fichier etc/hosts pour la communication entre les différentes instances. Ces adresses doivent également être mises à jour dans les configurations HANA de montée en charge : ce sont les adresses IP que HANA utilise pour la communication entre les nœuds.

- L’adresse IP affectée à « Ethernet C » doit être utilisée pour la communication avec le stockage NFS. Ce type d’adresse ne doit donc pas être mis à jour dans le fichier etc/hosts.

- Ethernet « D » doit être utilisé exclusivement dans le cadre de l’accès à l’appareil STONITH pour Pacemaker. Cette interface est nécessaire quand vous configurez la réplication de système HANA (HSR) et que vous voulez obtenir le basculement automatique au niveau du système d’exploitation à l’aide d’un appareil SBD.


### <a name="storage"></a>Stockage
Le stockage est préconfiguré en fonction de la topologie demandée. Les tailles de volume et le point de montage varient en fonction du nombre de serveurs, des références SKU et de la topologie configurée. Pour plus d’informations, étudiez les scénarios dont vous avez besoin (plus loin dans ce document). Si un stockage supplémentaire est nécessaire, vous pouvez l’acheter par incrément d’un To.

>[!NOTE]
>Le point de montage /usr/sap/\<SI> est un lien symbolique vers le point de montage/hana/shared.


## <a name="supported-scenarios"></a>Scénarios pris en charge

Dans les diagrammes d’architecture, les notations suivantes sont utilisées pour les graphiques :

[ ![Legends.PNG](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

La liste suivante présente les scénarios pris en charge :

1. Nœud unique avec un SID
2. Nœud unique avec MCOS
3. Nœud unique avec récupération d’urgence (Normale)
4. Nœud unique avec reprise d’activité après sinistre (Polyvalente)
5. HSR avec STONITH
6. HSR avec reprise d’activité après sinistre (Normale / Polyvalente) 
7. Basculement automatique avec hôte (1+1) 
8. Scale-out avec nœud de secours
9. Scale-out sans nœud de secours
10. Scale-out avec reprise d’activité après sinistre



## <a name="1-single-node-with-one-sid"></a>1. Nœud unique avec un SID

Cette topologie prend en charge un seul nœud dans une configuration de montée en charge avec un SID.

### <a name="architecture-diagram"></a>Diagramme de l’architecture  

![Single-node-with-one-SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|/Hana/Shared/SID | Installation HANA | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux | 
|/hana/logbackups/SID | Journaux d’activité de rétablissement |

### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.

## <a name="2-single-node-mcos"></a>2. Nœud unique avec MCOS

Cette topologie prend en charge un seul nœud dans une configuration de montée en charge avec plusieurs SID.

### <a name="architecture-diagram"></a>Diagramme de l’architecture  

![single-node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|/hana/shared/SID1 | Installation HANA pour SID1 | 
|/hana/data/SID1/mnt00001 | Installation des fichiers de données pour SID1 | 
|/hana/log/SID1/mnt00001 | Installation des fichiers journaux pour SID1 | 
|/hana/logbackups/SID1 | Journaux d’activité de rétablissement pour SID1 |
|/hana/shared/SID2 | Installation HANA pour SID2 | 
|/hana/data/SID2/mnt00001 | Installation des fichiers de données pour SID2 | 
|/hana/log/SID2/mnt00001 | Installation des fichiers journaux pour SID2 | 
|/hana/logbackups/SID2 | Journaux d’activité de rétablissement pour SID2 |

### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- La répartition de la taille des volumes est basée sur la taille de la base de données en mémoire. Consultez la section [Vue d’ensemble et architecture](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) pour découvrir quelles tailles de base de données en mémoire sont prises en charge avec l’environnement multi SID.

## <a name="3-single-node-with-dr-using-storage-replication"></a>3. Nœud unique avec reprise d’activité à l’aide de la réplication de stockage
 
Cette topologie prend en charge un seul nœud dans une configuration de montée en charge à un ou plusieurs SID, avec la réplication du stockage sur le site de reprise après sinistre pour un SID principal. Dans le diagramme, un seul SID est représenté sur le site principal, mais l’environnement multi SID (MCOS) est également pris en charge.

### <a name="architecture-diagram"></a>Diagramme de l’architecture  

![Single-node-with-dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|/Hana/Shared/SID | Installation HANA pour SID | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID | 
|/hana/logbackups/SID | Journaux d’activité de rétablissement pour SID |


### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Pour MCOS : La répartition de la taille des volumes est basée sur la taille de la base de données en mémoire. Consultez la section [Vue d’ensemble et architecture](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) pour découvrir quelles tailles de base de données en mémoire sont prises en charge avec l’environnement multi SID.
- Sur le site de reprise après sinistre : Les volumes et les points de montage sont configurés, donc marqués comme « Required for HANA installation » (nécessaires pour l’installation HANA), en vue de l’installation de l’instance HANA de production sur l’unité HLI de reprise après sinistre. 
- Sur le site de reprise après sinistre : Les volumes partagés, de sauvegarde de fichiers journaux, et de données, marqués comme « Storage Replication » (Réplication de stockage), sont répliqués par le biais de la capture instantanée depuis le site de production. Ces volumes sont montés uniquement pendant la durée de basculement. Pour plus d’informations, lisez le document [Procédure de basculement en cas de récupération d’urgence](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery).
- Le volume de démarrage pour la **classe de type I des références SKU** est répliqué sur le nœud de reprise après sinistre.


## <a name="4-single-node-with-dr-multipurpose-using-storage-replication"></a>4. Nœud unique avec reprise d’activité (polyvalente) à l’aide de la réplication de stockage
 
Cette topologie prend en charge un seul nœud dans une configuration de montée en charge à un ou plusieurs SID, avec la réplication du stockage sur le site de reprise après sinistre pour un SID principal. Dans le diagramme, un seul SID est représenté sur le site principal, mais l’environnement multi SID (MCOS) est également pris en charge. Sur le site de reprise après sinistre, l’unité HLI est utilisée pour l’instance AQ pendant l’exécution des opérations de production à partir du site principal. Au moment du basculement de la reprise après sinistre (ou du test de basculement), l’instance AQ sur le site de reprise après sinistre est arrêté.

### <a name="architecture-diagram"></a>Diagramme de l’architecture  

![single-node-with-dr-multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le site principal**|
|/Hana/Shared/SID | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le site de reprise après sinistre**|
|/Hana/Shared/SID | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/shared/QA-SID | Installation HANA pour SID AQ | 
|/hana/data/QA-SID/mnt00001 | Installation des fichiers de données pour SID AQ | 
|/hana/log/QA-SID/mnt00001 | Installation des fichiers journaux pour SID AQ |
|/hana/logbackups/QA-SID | Journaux d’activité de rétablissement pour SID AQ |

### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Pour MCOS : La répartition de la taille des volumes est basée sur la taille de la base de données en mémoire. Consultez la section [Vue d’ensemble et architecture](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) pour découvrir quelles tailles de base de données en mémoire sont prises en charge avec l’environnement multi SID.
- Sur le site de reprise après sinistre : Les volumes et les points de montage sont configurés, donc marqués comme « Required for HANA installation » (nécessaires pour l’installation HANA), en vue de l’installation de l’instance HANA de production sur l’unité HLI de reprise après sinistre. 
- Sur le site de reprise après sinistre : Les volumes partagés, de sauvegarde de fichiers journaux, et de données, marqués comme « Storage Replication » (Réplication de stockage), sont répliqués par le biais de la capture instantanée depuis le site de production. Ces volumes sont montés uniquement pendant la durée de basculement. Pour plus d’informations, lisez le document [Procédure de basculement en cas de récupération d’urgence](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- Sur le site de reprise après sinistre : Les volumes partagés de données, de fichiers journaux et de sauvegardes de fichiers journaux pour l’AQ, marqués comme « QA Instance installation » (Installation de l’instance AQ), sont configurés pour l’installation de l’instance AQ.
- Le volume de démarrage pour la **classe de type I des références SKU** est répliqué sur le nœud de reprise après sinistre.

## <a name="5-hsr-with-stonith-for-high-availability"></a>5. HSR avec STONITH pour une haute disponibilité
 
Cette topologie prend en charge deux nœuds pour la configuration HSR (HANA System Replication). Cette configuration est uniquement prise en charge pour les instances HANA uniques sur un nœud. Cela signifie que les scénarios MCOS ne sont pas pris en charge.

**À partir de maintenant, cette architecture est uniquement prise en charge pour le système d’exploitation de SUSE.**


### <a name="architecture-diagram"></a>Diagramme de l’architecture  

![HSR-with-STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Utilisé pour STONITH |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Utilisé pour STONITH |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le nœud principal**|
|/Hana/Shared/SID | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le nœud secondaire**|
|/Hana/Shared/SID | Installation HANA pour SID secondaire | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID secondaire | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID secondaire | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID secondaire |

### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Pour MCOS : La répartition de la taille des volumes est basée sur la taille de la base de données en mémoire. Consultez la section [Vue d’ensemble et architecture](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) pour découvrir quelles tailles de base de données en mémoire sont prises en charge avec l’environnement multi SID.
- STONITH : Un SBD est configuré pour l’installation STONITH. L’utilisation de STONITH est néanmoins facultative.


## <a name="6-high-availability-with-hsr-and-dr-with-storage-replication"></a>6. Haute disponibilité avec HSR et reprise d’activité avec réplication de stockage
 
Cette topologie prend en charge deux nœuds pour la configuration HSR (HANA System Replication). Les deux possibilités de reprise après sinistre, normale et polyvalente, sont prises en charge. Ces configurations sont uniquement prises en charge pour les instances HANA uniques sur un nœud. Cela signifie que les scénarios MCOS NE sont PAS pris en charge avec ces configurations.

Dans le diagramme, le scénario polyvalent est décrit où, sur le site de reprise après sinistre, l’unité HLI est utilisée pour l’instance AQ pendant l’exécution des opérations de production à partir du site principal. Au moment du basculement de la reprise après sinistre (ou du test de basculement), l’instance AQ sur le site de reprise après sinistre est arrêté. 



### <a name="architecture-diagram"></a>Diagramme de l’architecture  

![HSR-with-DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Utilisé pour STONITH |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Utilisé pour STONITH |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le nœud principal du site principal**|
|/Hana/Shared/SID | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le nœud secondaire du site principal**|
|/Hana/Shared/SID | Installation HANA pour SID secondaire | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID secondaire | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID secondaire | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID secondaire |
|**Sur le site de reprise après sinistre**|
|/Hana/Shared/SID | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/shared/QA-SID | Installation HANA pour SID AQ | 
|/hana/data/QA-SID/mnt00001 | Installation des fichiers de données pour SID AQ | 
|/hana/log/QA-SID/mnt00001 | Installation des fichiers journaux pour SID AQ |
|/hana/logbackups/QA-SID | Journaux d’activité de rétablissement pour SID AQ |

### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Pour MCOS : La répartition de la taille des volumes est basée sur la taille de la base de données en mémoire. Consultez la section [Vue d’ensemble et architecture](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) pour découvrir quelles tailles de base de données en mémoire sont prises en charge avec l’environnement multi SID.
- STONITH : Un SBD est configuré pour l’installation STONITH. L’utilisation de STONITH est néanmoins facultative.
- Sur le site de reprise après sinistre : **Deux jeux de volumes de stockage sont nécessaires** pour la réplication du nœud principal et du nœud secondaire.
- Sur le site de reprise après sinistre : Les volumes et les points de montage sont configurés, donc marqués comme « Required for HANA installation » (nécessaires pour l’installation HANA), en vue de l’installation de l’instance HANA de production sur l’unité HLI de reprise après sinistre. 
- Sur le site de reprise après sinistre : Les volumes partagés, de sauvegarde de fichiers journaux, et de données, marqués comme « Storage Replication » (Réplication de stockage), sont répliqués par le biais de la capture instantanée depuis le site de production. Ces volumes sont montés uniquement pendant la durée de basculement. Pour plus d’informations, lisez le document [Procédure de basculement en cas de récupération d’urgence](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- Sur le site de reprise après sinistre : Les volumes partagés de données, de fichiers journaux et de sauvegardes de fichiers journaux pour l’AQ, marqués comme « QA Instance installation » (Installation de l’instance AQ), sont configurés pour l’installation de l’instance AQ.
- Le volume de démarrage pour la **classe de type I des références SKU** est répliqué sur le nœud de reprise après sinistre.


## <a name="7-host-auto-failover-11"></a>7. Basculement automatique avec hôte (1+1)
 
Cette topologie prend en charge deux nœuds dans une configuration de basculement automatique avec hôte. Un nœud dispose du rôle principal/de travail, l’autre étant un nœud de secours. **SAP prend en charge ce scénario uniquement pour S/4 HANA.** Reportez-vous à la note OSS « [2408419 - SAP S/4HANA - Multi-Node Support](https://launchpad.support.sap.com/#/notes/2408419) » (2408419 - SAP S/4HANA - Prise en charge de plusieurs nœuds) pour plus d’informations.



### <a name="architecture-diagram"></a>Diagramme de l’architecture  

![sca](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | Communication nœud à nœud |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Communication nœud à nœud |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le nœud principal et le nœud de secours**|
|/hana/shared | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |



### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Sur le nœud de secours : Les volumes et les points de montage sont configurés, donc marqués comme « Required for HANA installation » (nécessaires pour l’installation HANA), en vue de l’installation de l’instance HANA sur l’unité de secours.
 

## <a name="8-scale-out-with-standby"></a>8. Scale-out avec nœud de secours
 
Cette topologie prend en charge plusieurs nœuds dans une configuration de scale-out. Elle est composée d’un nœud doté du rôle principal, d’un ou de plusieurs nœuds dotés du rôle de travail et d’un ou plusieurs nœuds de secours. Toutefois, il ne peut jamais exister qu’un seul nœud principal.


### <a name="architecture-diagram"></a>Diagramme de l’architecture  

![scaleout-nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | Communication nœud à nœud |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Communication nœud à nœud |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le nœud principal, les nœuds de travail et les nœuds de secours**|
|/hana/shared | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |


## <a name="9-scale-out-without-standby"></a>9. Scale-out sans nœud de secours
 
Cette topologie prend en charge plusieurs nœuds dans une configuration de scale-out. Elle est composée d’un nœud doté du rôle principal et d’un ou de plusieurs nœuds dotés du rôle de travail. Toutefois, il ne peut jamais exister qu’un seul nœud principal.


### <a name="architecture-diagram"></a>Diagramme de l’architecture  

![scaleout-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | Communication nœud à nœud |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Communication nœud à nœud |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le nœud principal et les nœuds de travail**|
|/hana/shared | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |


### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.

## <a name="10-scale-out-with-dr-using-storage-replication"></a>10. Scale-out avec reprise d’activité à l’aide de la réplication de stockage
 
Cette topologie prend en charge plusieurs nœuds dans une configuration de scale-out avec reprise d’activité après sinistre. Les deux possibilités de reprise après sinistre, normale et polyvalente, sont prises en charge. Dans le diagramme, seul le site de reprise après sinistre à objectif unique est décrit. Vous pouvez demander cette topologie avec ou sans nœud de secours.


### <a name="architecture-diagram"></a>Diagramme de l’architecture  

![scaleout-with-dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | Communication nœud à nœud |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Communication nœud à nœud |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le nœud principal**|
|/hana/shared | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le nœud du site de reprise après sinistre**|
|/hana/shared | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 


### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
-  Sur le site de reprise après sinistre : Les volumes et les points de montage sont configurés, donc marqués comme « Required for HANA installation » (nécessaires pour l’installation HANA), en vue de l’installation de l’instance HANA de production sur l’unité HLI de reprise après sinistre. 
- Sur le site de reprise après sinistre : Les volumes partagés, de sauvegarde de fichiers journaux, et de données, marqués comme « Storage Replication » (Réplication de stockage), sont répliqués par le biais de la capture instantanée depuis le site de production. Ces volumes sont montés uniquement pendant la durée de basculement. Pour plus d’informations, lisez le document [Procédure de basculement en cas de récupération d’urgence](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- Le volume de démarrage pour la **classe de type I des références SKU** est répliqué sur le nœud de reprise après sinistre.


## <a name="11-single-node-with-dr-using-hsr"></a>11. Nœud unique avec reprise d’activité à l’aide de HSR
 
Cette topologie prend en charge un seul nœud dans une configuration de scale-up avec un seul SID, avec la réplication du système HANA sur le site de reprise d’activité pour un SID principal. Dans le diagramme, un seul SID est représenté sur le site principal, mais l’environnement multi SID (MCOS) est également pris en charge.

### <a name="architecture-diagram"></a>Diagramme de l’architecture  

![single-node-hsr-dr-111.png](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI/HSR |
| b | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI/HSR |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés sur les deux unités HLI (principale et de reprise d’activité) :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|/Hana/Shared/SID | Installation HANA pour SID | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID | 
|/hana/logbackups/SID | Journaux d’activité de rétablissement pour SID |


### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Pour MCOS : La répartition de la taille des volumes est basée sur la taille de la base de données en mémoire. Consultez la section [Vue d’ensemble et architecture](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) pour découvrir quelles tailles de base de données en mémoire sont prises en charge avec l’environnement multi SID.
- Le nœud principal est synchronisé sur le nœud de récupération d’urgence via la réplication du système HANA. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) permet d’associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux régionaux.



## <a name="12-single-node-hsr-to-dr-cost-optimized"></a>12. HSR à nœud unique vers site de récupération d’urgence (optimisation des coûts) 
 
 Cette topologie prend en charge un seul nœud dans une configuration de scale-up avec un seul SID, avec la réplication du système HANA sur le site de reprise d’activité pour un SID principal. Dans le diagramme, un seul SID est représenté sur le site principal, mais l’environnement multi SID (MCOS) est également pris en charge. Sur le site de reprise après sinistre, l’unité HLI est utilisée pour l’instance AQ pendant l’exécution des opérations de production à partir du site principal. Au moment du basculement de la reprise après sinistre (ou du test de basculement), l’instance AQ sur le site de reprise après sinistre est arrêté.

### <a name="architecture-diagram"></a>Diagramme de l’architecture  

![single-node-hsr-dr-cost-optimized-121.png](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI/HSR |
| b | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI/HSR |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le site principal**|
|/Hana/Shared/SID | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le site de reprise après sinistre**|
|/Hana/Shared/SID | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|/hana/shared/QA-SID | Installation HANA pour SID AQ | 
|/hana/data/QA-SID/mnt00001 | Installation des fichiers de données pour SID AQ | 
|/hana/log/QA-SID/mnt00001 | Installation des fichiers journaux pour SID AQ |
|/hana/logbackups/QA-SID | Journaux d’activité de rétablissement pour SID AQ |

### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Pour MCOS : La répartition de la taille des volumes est basée sur la taille de la base de données en mémoire. Consultez la section [Vue d’ensemble et architecture](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) pour découvrir quelles tailles de base de données en mémoire sont prises en charge avec l’environnement multi SID.
- Sur le site de reprise après sinistre : Les volumes et les points de montage sont configurés, donc marqués comme « PROD Instance at DR site » (Instance PROD sur le site de reprise d’activité), en vue de l’installation de l’instance HANA de production sur l’unité HLI de reprise d’activité après sinistre. 
- Sur le site de reprise après sinistre : Les volumes partagés de données, de fichiers journaux et de sauvegardes de fichiers journaux pour l’AQ, marqués comme « QA Instance installation » (Installation de l’instance AQ), sont configurés pour l’installation de l’instance AQ.
- Le nœud principal est synchronisé sur le nœud de récupération d’urgence via la réplication du système HANA. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) permet d’associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux régionaux.

## <a name="13-high-availability-and-disaster-recovery-with-hsr"></a>13. Haute disponibilité et reprise d’activité avec HSR 
 
 Cette topologie prend en charge deux nœuds pour la configuration de la réplication du système HANA (HSR) pour la haute disponibilité des régions locales. Pour la reprise d’activité, le troisième nœud de la région de reprise d’activité est synchronisé à partir du site principal via HSR (mode asynchrone). 

### <a name="architecture-diagram"></a>Diagramme de l’architecture  

![hana-system-replication-dr-131.png](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI/HSR |
| b | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI/HSR |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le site principal**|
|/Hana/Shared/SID | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le site de reprise après sinistre**|
|/Hana/Shared/SID | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |


### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Sur le site de reprise après sinistre : Les volumes et les points de montage sont configurés, donc marqués comme « PROD DR Instance » (Instance de reprise d’activité PROD), en vue de l’installation de l’instance HANA de production sur l’unité HLI de reprise d’activité. 
- Le nœud du site principal est synchronisé sur le nœud de reprise d’activité via la réplication du système HANA. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) permet d’associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux régionaux.

## <a name="14-high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>14. Haute disponibilité et reprise d’activité avec HSR (optimisation des coûts)
 
 Cette topologie prend en charge deux nœuds pour la configuration de la réplication du système HANA (HSR) pour la haute disponibilité des régions locales. Pour la reprise d’activité après sinistre, le troisième nœud de la région de reprise d’activité est synchronisé à partir du site principal via HSR (mode asynchrone), alors qu’une autre instance (p. ex. QA) est déjà en cours d’exécution à partir du nœud de reprise d’activité. 

### <a name="architecture-diagram"></a>Diagramme de l’architecture  

![hana-system-replication-dr-cost-optimized-141.png](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI/HSR |
| b | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI/HSR |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le site principal**|
|/Hana/Shared/SID | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le site de reprise après sinistre**|
|/Hana/Shared/SID | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|/hana/shared/QA-SID | Installation HANA pour SID AQ | 
|/hana/data/QA-SID/mnt00001 | Installation des fichiers de données pour SID AQ | 
|/hana/log/QA-SID/mnt00001 | Installation des fichiers journaux pour SID AQ |
|/hana/logbackups/QA-SID | Journaux d’activité de rétablissement pour SID AQ |

### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Sur le site de reprise après sinistre : Les volumes et les points de montage sont configurés, donc marqués comme « PROD DR Instance » (Instance de reprise d’activité PROD), en vue de l’installation de l’instance HANA de production sur l’unité HLI de reprise d’activité. 
- Sur le site de reprise après sinistre : Les volumes partagés de données, de fichiers journaux et de sauvegardes de fichiers journaux pour l’AQ, marqués comme « QA Instance installation » (Installation de l’instance AQ), sont configurés pour l’installation de l’instance AQ.
- Le nœud du site principal est synchronisé sur le nœud de reprise d’activité via la réplication du système HANA. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) permet d’associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux régionaux.

## <a name="15-scale-out-with-dr-using-hsr"></a>15. Scale-out avec reprise d’activité à l’aide de HSR
 
Cette topologie prend en charge plusieurs nœuds dans une configuration de scale-out avec reprise d’activité après sinistre. Vous pouvez demander cette topologie avec ou sans nœud de secours. Les nœuds du site principal sont synchronisés vers les nœuds du site de reprise d’activité via la réplication du système HANA (mode asynchrone).


### <a name="architecture-diagram"></a>Diagramme de l’architecture  

[ ![scale-out-dr-hsr-151.png](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| INTERFACES LOGIQUES DE CARTE RÉSEAU | TYPE DE RÉFÉRENCE (SKU) | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client à HLI/HSR |
| b | TYPE I | eth2.tenant | eno3.tenant | Communication nœud à nœud |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI/HSR |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Communication nœud à nœud |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le nœud principal**|
|/hana/shared | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le nœud du site de reprise après sinistre**|
|/hana/shared | Installation HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |


### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Sur le site de reprise après sinistre : Les volumes et les points de montage sont configurés en vue de l’installation de l’instance HANA de production sur l’unité HLI de reprise d’activité. 
- Les nœuds du site principal sont synchronisés vers les nœuds de récupération d’urgence via la réplication du système HANA. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) permet d’associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux régionaux.


## <a name="next-steps"></a>Étapes suivantes
- Reportez-vous à [Infrastructure et connectivité](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) pour HLI.
- Reportez-vous à [Haute disponibilité et reprise après sinistre](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) pour HLI.
