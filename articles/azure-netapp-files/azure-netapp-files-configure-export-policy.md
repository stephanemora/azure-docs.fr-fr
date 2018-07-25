---
title: Configurer une stratégie d’exportation pour un volume Azure NetApp Files | Microsoft Docs
description: Décrit comment configurer une stratégie d’exportation pour contrôler l’accès à un volume Azure NetApp Files.
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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 08de7e2ca8cd993a0931f5b16cb9d6c9a04e53dc
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010954"
---
# <a name="configure-export-policy-for-a-volume-optional"></a>Configurer une stratégie d’exportation pour un volume (facultatif)

Si vous le souhaitez, vous pouvez configurer une stratégie d’exportation pour contrôler l’accès à un volume Azure NetApp Files. 

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

    * **Access**  
        Sélectionnez l’un des types d’accès suivants :  
        * Aucun accès 
        * Lecture et écriture
        * Lecture seule

    * **Protocoles**   
        Spécifiez le protocole à utiliser pour la stratégie d’exportation.   
        Actuellement, Azure NetApp Files prend uniquement en charge le protocole NFSv3.

    ![Exporter la stratégie](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Étapes suivantes 
* [Gérer les volumes](azure-netapp-files-manage-volumes.md)
* [Monter ou démonter un volume pour les machines virtuelles](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gérer les instantanés](azure-netapp-files-manage-snapshots.md)
