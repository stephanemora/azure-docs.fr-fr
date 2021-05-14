---
title: Configurer la stratégie d’exportation pour les volumes NFS ou à deux protocoles d’Azure NetApp Files – Azure NetApp Files
description: Décrit comment configurer une stratégie d’exportation pour contrôler l’accès à un volume NFS à l’aide d’Azure NetApp Files.
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: b71d3cc0636e5edeb78e1d88be1df2d2d61f239b
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107988457"
---
# <a name="configure-export-policy-for-nfs-or-dual-protocol-volumes"></a>Configurer la stratégie d’exportation pour les volumes NFS ou à deux protocoles

Vous pouvez configurer la stratégie d’exportation pour contrôler l’accès à un volume Azure NetApp Files qui utilise le protocole NFS (NFSv3 et NFSv4.1) ou le double protocole (NFSv3 et SMB). 

Vous pouvez créer jusqu’à cinq règles de stratégie d’exportation.

## <a name="configure-the-policy"></a>Configurer la stratégie 

1.  Dans la page **Volumes**, sélectionnez le volume pour lequel vous souhaitez configurer la stratégie d’exportation, puis sélectionnez **Exporter la stratégie**. Vous pouvez également configurer la stratégie d’exportation lors de la création du volume.

2.  Pour créer une règle de stratégie d’exportation, spécifiez les informations suivantes :   
    * **Index** : Spécifiez le numéro d’index pour la règle.  
      
      Une stratégie d’exportation peut contenir jusqu’à cinq règles. Les règles sont évaluées en fonction de leur ordre dans la liste des numéros d’index. Les règles avec des numéros d’index inférieurs sont évaluées en premier. Par exemple, la règle portant le numéro d’index 1 est évaluée avant la règle portant le numéro d’index 2. 

    * **Clients autorisés** : Spécifiez la valeur dans l’un des formats suivants :  
      * Adresse IPv4. Exemple : `10.1.12.24`
      * Adresse IPv4 avec un masque de sous-réseau exprimé sous la forme d’un nombre de bits. Exemple : `10.1.12.10/4`
      * Adresses IP séparées par des virgules. Vous pouvez entrer plusieurs adresses IP hôtes dans une seule règle en les séparant par des virgules. Exemple : `10.1.12.25,10.1.12.28,10.1.12.29`

    * **Accès** : Sélectionnez l’un des types d’accès suivants :  
      * Aucun accès 
      * Lecture et écriture
      * Lecture seule

    * **Lecture seule** et **Lecture/écriture** : Si vous utilisez le chiffrement Kerberos avec NFSv4.1, suivez les instructions de la section [Configurer le chiffrement Kerberos NFSv4.1](configure-kerberos-encryption.md).  Pour connaitre l’impact de Kerberos sur les performances, consultez [Impact de Kerberos sur les performances sur les volumes NFSv4.1](performance-impact-kerberos.md). 

      ![Options de sécurité Kerberos](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Accès racine** : Spécifiez si le compte `root` peut accéder au volume.  Par défaut, l’accès racine est défini sur **Activé** et le compte `root` a accès au volume.

      ![Exporter la stratégie](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 

## <a name="next-steps"></a>Étapes suivantes 
* [Monter ou démonter un volume](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gérer les instantanés](azure-netapp-files-manage-snapshots.md)
