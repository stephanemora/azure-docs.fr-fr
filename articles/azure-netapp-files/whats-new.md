---
title: Nouveautés d’Azure NetApp Files | Microsoft Docs
description: Fournit un résumé des dernières fonctionnalités et améliorations d’Azure NetApp Files.
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
ms.topic: overview
ms.date: 04/30/2021
ms.author: b-juche
ms.openlocfilehash: 46e84814e27562097a4c5dc4e3daa1e5b36669f7
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108287824"
---
# <a name="whats-new-in-azure-netapp-files"></a>Nouveautés d’Azure NetApp Files

Azure NetApp Files est régulièrement mis à jour. Cet article récapitule les dernières fonctionnalités et améliorations. 

## <a name="april-2021"></a>Avril 2021

* [Gestion manuelle des volumes et des pools de capacités](volume-quota-introduction.md) (quota strict) 

    Le comportement du provisionnement des volumes et des pools de capacité Azure NetApp Files a changé pour un mécanisme manuel et contrôlable. La capacité de stockage d’un volume est limitée à la taille définie (quota) du volume. Quand la consommation du volume atteint la limite maximale, ni le volume ni le pool de capacité sous-jacent n’augmentent automatiquement. Au lieu de cela, le volume reçoit une condition « espace insuffisant ». Toutefois, vous pouvez [redimensionner le pool de capacité ou un volume](azure-netapp-files-resize-capacity-pools-or-volumes.md) en fonction des besoins. Vous devez activement [superviser la capacité d’un volume](monitor-volume-capacity.md) et le pool de capacité sous-jacent.

    Ce changement de comportement résulte des demandes clés suivantes indiquées par de nombreux utilisateurs :

    * Avant, les clients de machines virtuelles voyaient la capacité provisionnée dynamiquement (100 Tio) d’un volume donné quand ils utilisaient l’espace du système d’exploitation ou des outils de supervision de la capacité.  Cette situation pouvait entraîner une visibilité inexacte de la capacité côté client ou application. Ce comportement a été corrigé.  
    * Le comportement de croissance automatique précédent des pools de capacité ne donnait aux propriétaires d’applications aucun contrôle sur l’espace du pool de capacité provisionnée (et sur le coût associé). Ce comportement était particulièrement fastidieux dans les environnements où les « pertes de contrôle de processus » pouvaient rapidement remplir et augmenter la capacité provisionnée. Ce comportement a été corrigé.  
    * Les utilisateurs veulent voir et maintenir une corrélation directe entre la taille du volume (quota) et le niveau de performance. Le comportement précédent autorisait un sur-abonnement (implicite) d’un volume (capacité) et une croissance automatique d’un pool de capacité. Ainsi, les utilisateurs ne pouvaient pas établir de corrélation directe tant que le quota de volume n’avait pas été défini ou réinitialisé activement. Ce comportement a été corrigé.

    Les utilisateurs ont demandé un contrôle direct sur la capacité provisionnée. Les utilisateurs souhaitent contrôler et équilibrer la capacité et l’utilisation du stockage. Ils souhaitent également contrôler les coûts et disposer de la visibilité, côté application et côté client, de la capacité disponible, utilisée et provisionnée et du niveau de performance de leurs volumes d’application. Avec ce nouveau comportement, toute cette fonctionnalité est désormais activée.

* [Prise en charge des partages à disponibilité continue SMB pour les conteneurs de profil utilisateur FSLogix](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (préversion)  

    [FSLogix](/fslogix/overview) est un ensemble de solutions qui améliorent, activent et simplifient les environnements informatiques Windows non persistants. Les solutions FSLogix sont adaptées aux environnements virtuels dans les clouds publics et privés. Les solutions FSLogix peuvent également être utilisées pour créer davantage de sessions de calcul portables quand vous utilisez des appareils physiques. FSLogix peut être utilisé pour fournir un accès dynamique aux conteneurs de profil utilisateur persistants stockés sur un stockage en réseau partagé SMB, y compris Azure NetApp Files. Pour améliorer la résilience FSLogix aux événements de maintenance du service de stockage, la prise en charge du basculement transparent SMB par Azure NetApp Files a été étendue avec les [partages à disponibilité continue SMB sur Azure NetApp Files](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) pour les conteneurs de profil utilisateur. Pour plus d’informations, consultez la section sur les [solutions Windows Virtual Desktop](azure-netapp-files-solution-architectures.md#windows-virtual-desktop) avec Azure NetApp Files.  

* [Chiffrement du protocole SMB3](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (Préversion) 

    Vous pouvez désormais activer le chiffrement du protocole SMB3 sur les volumes SMB Azure NetApp Files et à double protocole. Cette fonctionnalité permet le chiffrement des données SMB3 en transit, en utilisant les connexions de l’[algorithme AES-CCM sur SMB 3.0 et de l’algorithme AES-GCM sur SMB 3.1.1](/windows-server/storage/file-server/file-server-smb-overview#features-added-in-smb-311-with-windows-server-2016-and-windows-10-version-1607). Les clients SMB n’utilisant pas le chiffrement SMB3 ne seront pas en mesure d’accéder à ce volume. Les données au repos sont chiffrées, indépendamment de ce paramètre. Le chiffrement SMB renforce la sécurité. Pour autant, il peut y avoir un impact sur le client (surcharge du processeur pour le chiffrement et le déchiffrement des messages). L’utilisation des ressources de stockage (réductions du débit) peut s’en trouver également impactée. Vous devez tester l’incidence des performances du chiffrement sur vos applications avant de déployer des charges de travail en production.

* [Mappage d'utilisateurs LDAP Active Directory Domain Services (AD DS) avec des groupes étendus NFS](configure-ldap-extended-groups.md) (préversion)   

    Par défaut, Azure NetApp Files prend en charge jusqu'à 16 ID de groupe lors du traitement des informations d'identification des utilisateurs NFS, comme défini dans les normes [RFC 5531](https://tools.ietf.org/html/rfc5531). Grâce à cette nouvelle fonctionnalité, ce maximum passe désormais à 1 024 si vous avez des utilisateurs qui sont membres d'un nombre de groupes supérieur au nombre par défaut. Pour prendre en charge cette fonctionnalité, les volumes NFS peuvent désormais être ajoutés à LDAP AD DS, ce qui permet aux utilisateurs LDAP Active Directory dotés d'entrées de groupes étendus (jusqu'à 1 024 groupes) d'accéder au volume. 

## <a name="march-2021"></a>Mars 2021
 
* [Partages d’autorité de certification SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (préversion)  

    Le basculement transparent SMB permet d’effectuer des opérations de maintenance sur le service Azure NetApp Files sans interrompre la connectivité aux applications du serveur qui stockent et accèdent aux données sur les volumes SMB. Pour prendre en charge le basculement transparent SMB, Azure NetApp Files prend désormais en charge l’option de partages d’autorité de certification SMB pour une utilisation avec des applications SQL Server, plutôt que SMB exécuté sur des machines virtuelles Azure. Cette fonctionnalité est actuellement prise en charge sur Windows SQL Server. Linux SQL Server n’est pas pris en charge actuellement. L’activation de cette fonctionnalité fournit des améliorations SQL Server significatives en matière de performances, de mise à l’échelle et d’avantages de coût pour [Instance unique, Instance de cluster de basculement toujours active et les déploiements de groupes de disponibilité toujours actifs](azure-netapp-files-solution-architectures.md#sql-server). Consultez[Avantages de l’utilisation d’Azure NetApp Files pour le déploiement de SQL Server](solutions-benefits-azure-netapp-files-sql-server.md).

* [Redimensionnement automatique d’un volume de destination de réplication inter-régions](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)

    Dans une relation de réplication inter-régions, un volume de destination est automatiquement redimensionné en fonction de la taille du volume source. Par conséquent, vous n’avez pas besoin de redimensionner le volume de destination séparément. Ce comportement de redimensionnement automatique est applicable lorsque les volumes sont dans une relation de réplication active ou lorsque le peering de réplication est interrompu avec l'opération de resynchronisation. Pour que cette fonctionnalité fonctionne, vous devez garantir une marge suffisante dans les pools de capacité pour les volumes source et de destination.

## <a name="december-2020"></a>Décembre 2020

* [Outil Azure Application Consistent Snapshot](azacsnap-introduction.md)(préversion)    

    L’outil Azure Application Consistent Snapshot Tool (AzAcSnap) est un outil en ligne de commande qui vous permet de simplifier la protection des données pour les bases de données tierces (SAP HANA) dans les environnements Linux (par exemple, SUSE et RHEL).   

    AzAcSnap tire parti des fonctionnalités d’instantané de volume et de réplication dans Azure NetApp Files et les grandes instances Azure. Elle vous permet de bénéficier des avantages suivants :

    * Protection des données cohérente avec les applications 
    * Gestion du catalogue de la base de données 
    * Protection de volume *ad hoc* 
    * Clonage des volumes de stockage 
    * Prise en charge de la reprise d’activité 

## <a name="november-2020"></a>Novembre 2020

* [Restauration d’instantané](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

    La fonctionnalité de restauration d’instantané vous permet de restaurer rapidement un volume à l’état dans lequel il se trouvait lors de la prise d’un instantané particulier. Dans la plupart des cas, le rétablissement d’un volume est beaucoup plus rapide que la restauration de fichiers individuels, d’un instantané vers le système de fichiers actif. La gestion de l’espace est également mieux optimisée comparé à la restauration d’un instantané sur un nouveau volume.

## <a name="september-2020"></a>Septembre 2020

* [Réplication inter-régions Azure NetApp Files](cross-region-replication-introduction.md) (préversion)

  Azure NetApp Files prend désormais en charge la réplication inter-région. Grâce à cette nouvelle fonctionnalité de récupération d’urgence, vous pouvez répliquer vos volumes Azure NetApp Files d’une région Azure vers une autre de façon rapide et économique, ce qui protège vos données contre les défaillances régionales imprévisibles. La réplication inter-région Azure NetApp Files tire parti de la technologie NetApp SnapMirror®. Seuls les blocs modifiés sont envoyés sur le réseau dans un format compressé. Cette technologie propriétaire limite le volume de données nécessaire à la réplication dans les différentes régions, réduisant ainsi le coût de transfert des données. Elle réduit également le temps de réplication, ce qui vous permet d’obtenir un objectif de point de restauration (RPO) inférieur.

* [Pool de capacités QoS manuel](manual-qos-capacity-pool-introduction.md) (préversion)  

    Au sein d’un pool de capacités QoS manuel, vous pouvez affecter la capacité et le débit d’un volume de manière indépendante. Le débit total de tous les volumes créés avec un pool de capacités de QoS manuel est limité par le débit total du pool. Il est déterminé par la combinaison de la taille du pool et du débit au niveau du service. Autre possibilité, le [type QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) d’un pool de capacités peut être automatique, ce qui correspond au paramètre par défaut. Dans un pool de capacités QoS automatique, le débit est affecté automatiquement aux volumes du pool, proportionnelementl au quota de taille alloué aux volumes.

* [Signature LDAP](azure-netapp-files-create-volumes-smb.md) (préversion)   

    Azure NetApp Files prend désormais en charge la signature LDAP pour sécuriser les recherches LDAP entre le service Azure NetApp Files et les contrôleurs de domaine Active Directory Domain Services spécifiés par l’utilisateur. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.

* [Chiffrement AES pour l’authentification AD](azure-netapp-files-create-volumes-smb.md) (préversion)

    Azure NetApp Files prend désormais en charge le chiffrement AES sur une connexion LDAP à DC afin d’activer le chiffrement AES pour un volume SMB. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. 

* Nouvelles [métriques](azure-netapp-files-metrics.md) :   

    * Nouvelles métriques de volume : 
        * *Taille allouée aux volumes* : Taille provisionnée d’un volume
    * Nouvelles métriques de pool : 
        * *Taille allouée au pool* : Taille approvisionnée du pool 
        * *Taille totale des instantanés du pool* : Somme de la taille des instantanés de tous les volumes du pool

## <a name="july-2020"></a>Juillet 2020

* [Volume à deux protocoles (NFSv3 et SMB)](create-volumes-dual-protocol.md)

    Vous pouvez désormais créer un volume Azure NetApp Files autorisant un accès simultané à deux protocoles (NFS v3 et SMB) avec prise en charge du mappage d’utilisateur LDAP. Cette fonctionnalité permet les cas d’usage avec charge de travail Linux générant et stockant des données dans un volume Azure NetApp Files. Dans le même temps, votre personnel doit utiliser des clients et des logiciels Windows pour analyser les données nouvellement générées à partir du même volume Azure NetApp Files. La fonctionnalité d’accès simultané à deux protocoles évite de devoir copier les données générées par la charge de travail sur un volume distinct à l’aide d’un protocole différent pour la post-analyse, l’enregistrement des coûts de stockage et le temps de fonctionnement. Cette fonctionnalité est gratuite (le [coût de stockage Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) normal s’applique) et mise à la disposition générale. Pour en savoir plus, consultez la [documentation relative à l’accès simultané à deux protocoles](create-volumes-dual-protocol.MD).

* [Chiffrement NFS v4.1 Kerberos en transit](configure-kerberos-encryption.MD)

    Azure NetApp Files prend désormais en charge le chiffrement client NFS dans les modes Kerberos (krb5, krb5i, et krb5p) avec le chiffrement AES-256, ce qui renforce la sécurité des données. Cette fonctionnalité est gratuite (le [coût de stockage Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) normal s’applique) et mise à la disposition générale. Pour en savoir plus, consultez la [documentation relative au chiffrement NFS v 4.1 Kerberos](configure-kerberos-encryption.MD).

* [Modification du niveau de service d’un volume dynamique](dynamic-change-volume-service-level.MD)

    Le cloud permet la flexibilité des dépenses informatiques. Vous pouvez désormais modifier le niveau de service d’un volume Azure NetApp Files existant en déplaçant le volume vers un autre pool de capacité qui utilise le niveau de service souhaité pour le volume. Cette modification sur place du niveau du service pour le volume ne nécessite pas de migration des données. Elle n’affecte pas non plus l’accès de plan de données au volume. Vous pouvez modifier un volume existant pour utiliser un niveau de service supérieur afin d’améliorer les performances, ou pour utiliser un niveau de service inférieur afin d’optimiser les coûts. Actuellement en préversion publique, cette fonctionnalité est gratuite (le [coût de stockage Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) normal s’applique). Vous pouvez vous inscrire à la préversion de la fonctionnalité en suivant la [documentation sur la modification du niveau de service d’un volume dynamique](dynamic-change-volume-service-level.md).

* [Stratégie d’instantané de volume](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) (préversion) 

    Azure NetApp Files vous permet de créer des instantanés de vos volumes à un point dans le temps. Vous pouvez désormais créer une stratégie d’instantané pour permettre à Azure NetApp Files de créer automatiquement des instantanés de volume à la fréquence de votre choix. Vous avez la possibilité de planifier les instantanés en cycles horaires, quotidiens, hebdomadaires ou mensuels. Vous pouvez également spécifier le nombre maximal d’instantanés à conserver dans le cadre de la stratégie d’instantané. Actuellement en préversion, cette fonctionnalité est gratuite (le [coût de stockage Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) normal s’applique). Vous pouvez vous inscrire à la préversion de la fonctionnalité en suivant la [documentation sur la stratégie d’instantané de volume](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

* [Stratégie d’exportation de l’accès à la racine NFS](azure-netapp-files-configure-export-policy.md)

    Azure NetApp Files vous permet désormais de spécifier si le compte racine peut accéder au volume. 

* [Masquer le chemin d'instantané](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    Azure NetApp Files vous permet désormais de spécifier si un utilisateur peut voir et accéder au `.snapshot` répertoire (clients NFS) ou `~snapshot` dossier (clients SMB) sur un volume monté.

## <a name="may-2020"></a>Mai 2020

* [Utilisateurs de stratégie de sauvegarde](create-active-directory-connections.md) (préversion)

    Azure NetApp Files vous permet d’inclure des comptes supplémentaires qui requièrent des privilèges élevés sur le compte d’ordinateur créé pour une utilisation avec Azure NetApp Files. Les comptes spécifiés seront autorisés à modifier les autorisations NTFS au niveau du fichier ou du dossier. Par exemple, vous pouvez spécifier un compte de service non privilégié utilisé pour la migration des données vers un partage de fichiers SMB dans Azure NetApp Files. La fonctionnalité Utilisateurs de stratégie de sauvegarde est actuellement en préversion.

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure NetApp Files](azure-netapp-files-introduction.md)
* [Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) 
