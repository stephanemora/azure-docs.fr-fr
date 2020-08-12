---
title: Configurer une stratégie d’exportation pour un volume NFS – Azure NetApp Files
description: Décrit comment configurer une stratégie d’exportation pour contrôler l’accès à un volume NFS à l’aide d’Azure NetApp Files.
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 4a20a223932f82c80ad5831ef3a02bad803e26e6
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533198"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurer une stratégie d’exportation pour un volume NFS

Vous pouvez configurer une stratégie d’exportation pour contrôler l’accès à un volume Azure NetApp Files. La stratégie d’exportation d’Azure NetApp Files prend en charge les volumes qui utilisent le protocole NFS (NFSv3 et NFSv4.1) et le protocole double (NFSv3 et SMB). 

Vous pouvez créer jusqu’à cinq règles de stratégie d’exportation.

## <a name="steps"></a>Étapes 

1.  Dans la page Volumes, sélectionnez le volume pour lequel vous souhaitez configurer la stratégie d’exportation, puis cliquez sur **Stratégie d’exportation**. 

    Vous pouvez également configurer la stratégie d’exportation lors de la création du volume.

2.  Renseignez les champs suivants pour créer une règle de stratégie d’exportation :   
    *  **Index**   
        Spécifiez le numéro d’index pour la règle.  
        Une stratégie d’exportation peut contenir jusqu’à cinq règles. Les règles sont évaluées en fonction de leur ordre dans la liste des numéros d’index. Les règles avec des numéros d’index inférieurs sont évaluées en premier. Par exemple, la règle portant le numéro d’index 1 est évaluée avant la règle portant le numéro d’index 2. 

    * **Clients autorisés**   
        Spécifiez la valeur dans l’un des formats suivants :  
        * Adresse IPv4, par exemple, `10.1.12.24` 
        * Adresse IPv4 avec un masque de sous-réseau exprimé sous la forme d’un nombre de bits, par exemple, `10.1.12.10/4`

    * **y accéder**  
        Sélectionnez l’un des types d’accès suivants :  
        * Aucun accès 
        * Lecture et écriture
        * Lecture seule

    * **Lecture seule** et **Lecture/écriture**  
        Si vous utilisez le chiffrement Kerberos avec NFSv4.1, suivez les instructions de la section [Configurer le chiffrement Kerberos NFSv4.1](configure-kerberos-encryption.md).  Pour connaitre l’impact de Kerberos sur le niveau de performance, consultez [Impact de Kerberos sur les performances de NFSv4.1](configure-kerberos-encryption.md#kerberos_performance). 

        ![Options de sécurité Kerberos](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Accès racine**  
        Spécifiez si le compte `root` peut accéder au volume.  Par défaut, l’accès racine est défini sur **Activé**et le compte `root` a accès au volume.

![Exporter la stratégie](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>Étapes suivantes 
* [Monter ou démonter un volume pour les machines virtuelles](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gérer les instantanés](azure-netapp-files-manage-snapshots.md)
