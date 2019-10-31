---
title: Configurer une stratégie d’exportation pour un volume NFS à l’aide d’Azure NetApp Files | Microsoft Docs
description: Décrit comment configurer une stratégie d’exportation pour contrôler l’accès à un volume NFS à l’aide d’Azure NetApp Files.
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
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: d323bd0b9684cfe4930d8c779a6728fcfd3836fb
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2019
ms.locfileid: "72674930"
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
