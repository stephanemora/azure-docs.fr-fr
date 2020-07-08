---
title: Configurer une stratégie d’exportation pour un volume NFS – Azure NetApp Files
description: Décrit comment configurer une stratégie d’exportation pour contrôler l’accès à un volume NFS à l’aide d’Azure NetApp Files.
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 10/18/2019
ms.openlocfilehash: e59648ee76b6715029c690329cbf8f4f1eee7243
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483650"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurer une stratégie d’exportation pour un volume NFS

Si vous le souhaitez, vous pouvez configurer une stratégie d’exportation pour contrôler l’accès à un volume Azure NetApp Files. La stratégie d’exportation d’Azure NetApp Files prend en charge les volumes NFS uniquement.  Les systèmes NFSv3 et NFSv4 sont pris en charge. 

## <a name="steps"></a>Étapes 

1.  Cliquez sur **Exporter la stratégie** dans le volet de navigation d’Azure NetApp Files. 

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

    ![Exporter la stratégie](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Étapes suivantes 
* [Gérer les volumes](azure-netapp-files-manage-volumes.md)
* [Monter ou démonter un volume pour les machines virtuelles](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gérer les instantanés](azure-netapp-files-manage-snapshots.md)
