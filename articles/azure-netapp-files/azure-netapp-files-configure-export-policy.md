---
title: Configurer la stratégie d’exportation pour un volume NFS à l’aide de fichiers NetApp de Azure | Microsoft Docs
description: Décrit comment configurer la stratégie d’exportation pour contrôler l’accès à un volume NFS à l’aide de fichiers NetApp de Azure
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
ms.date: 03/20/2019
ms.author: b-juche
ms.openlocfilehash: 8cda5921a1aec86d28beabbd9cea5b07a203a0e8
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401480"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurer la stratégie d’exportation pour un volume NFS

Si vous le souhaitez, vous pouvez configurer une stratégie d’exportation pour contrôler l’accès à un volume Azure NetApp Files. Stratégie d’exportation est prise en charge uniquement pour les volumes NFS. 

## <a name="steps"></a>Étapes 

1.  Cliquez sur le panneau **Create Export Policy** (Créer une stratégie d’exportation) à partir du panneau de gestion de volumes. 

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

    * **Protocoles**   
        Spécifiez le protocole à utiliser pour la stratégie d’exportation.   
        Actuellement, stratégie d’exportation de fichiers NetApp de Azure prend en charge uniquement NFSv3.

    ![Exporter la stratégie](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Étapes suivantes 
* [Gérer les volumes](azure-netapp-files-manage-volumes.md)
* [Monter ou démonter un volume pour les machines virtuelles](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gérer les instantanés](azure-netapp-files-manage-snapshots.md)
