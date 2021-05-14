---
title: Limites des ressources pour Azure NetApp Files | Microsoft Docs
description: Décrit les limites des ressources Azure NetApp Files et la manière de demander une augmentation de ces limites.
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
ms.date: 04/22/2021
ms.author: b-juche
ms.openlocfilehash: b5abb26a5a96b73f06f25661c62061f664069ee3
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107903485"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limites des ressources pour Azure NetApp Files

Comprendre les limites des ressources pour Azure NetApp Files vous permet de gérer vos volumes.

## <a name="resource-limits"></a>Limites des ressources

Le tableau suivant décrit les limites des ressources pour Azure NetApp Files :

|  Ressource  |  Limite par défaut  |  Ajustable via une demande de support  |
|----------------|---------------------|--------------------------------------|
|  Nombre de comptes NetApp par région Azure par abonnement  |  10    |  Oui   |
|  Nombre de pools de capacités par compte NetApp   |    25     |   Oui   |
|  Nombre de volumes par abonnement   |    500     |   Oui   |
|  Nombre de volumes par pool de capacités     |    500   |    Oui     |
|  Nombre d’instantanés par volume       |    255     |    Non        |
|  Nombre de sous-réseaux délégués à Azure NetApp Files (Microsoft.NetApp/volumes) par réseau virtuel Azure    |   1   |    Non    |
|  Nombre d’adresses IP utilisées dans un réseau virtuel (notamment les réseaux virtuels immédiatement appairés) avec Azure NetApp Files   |    1 000   |    Non   |
|  Taille minimale d’un pool de capacités   |  4 Tio     |    Non  |
|  Taille maximale d’un pool de capacités    |  500 Tio   |   Non   |
|  Taille minimale d’un volume    |    100 Gio    |    Non    |
|  Taille maximale d’un volume     |    100 Tio    |    Non    |
|  Taille maximale d’un fichier unique     |    16 Tio    |    Non    |    
|  Taille maximale des métadonnées de répertoire dans un répertoire unique      |    320 Mo    |    Non    |    
|  Nombre maximal de fichiers dans un seul répertoire  | *Environ* 4 millions. <br> Voir [Déterminer si un répertoire approche de la taille limite](#directory-limit).  |    Non    |   
|  Nombre maximal de fichiers ([maxfiles](#maxfiles)) par volume     |    100 millions    |    Oui    |    
|  Nombre maximal de règles de stratégie d’exportation par volume     |    5  |    Non    | 
|  Débit minimum attribué pour un volume avec Qualité de service manuelle     |    1 Mio/s   |    Non    |    
|  Débit maximum attribué pour un volume avec Qualité de service manuelle     |    4 500 Mio/s    |    Non    |    
|  Nombre de volumes de protection des données de réplication inter-région (volumes de destination)     |    5    |    Oui    |     

Pour plus d’informations, consultez [Questions fréquentes (FAQ) sur la gestion de la capacité](azure-netapp-files-faqs.md#capacity-management-faqs).

## <a name="determine-if-a-directory-is-approaching-the-limit-size"></a>Déterminer si un répertoire approche de la taille limite<a name="directory-limit"></a>  

Vous pouvez utiliser la commande `stat` à partir d’un client pour voir si la taille d’un annuaire approche de la limite maximale pour des métadonnées d’annuaire (320 Mo).   

Pour un annuaire de 320 Mo, les blocs sont au nombre de 655 360, d’une taille 512 octets chacun  (soit, 320 x 1 024 x 1 024/512). Ce nombre se traduit par environ 4 millions de fichiers au maximum pour un annuaire de 320 Mo. Toutefois, le nombre de fichiers maximal réel peut être inférieur, en fonction de facteurs tels que le nombre de fichiers contenant des caractères non-ASCII dans l’annuaire. Par conséquent, vous devez utiliser la commande `stat` comme suit pour déterminer si votre annuaire approche de sa limite.  

Exemples :

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```

## <a name="maxfiles-limits"></a>Limites maxfiles <a name="maxfiles"></a> 

Les volumes Azure NetApp Files ont une limite appelée *maxfiles*. La limite maxfiles correspond au nombre de fichiers qu’un volume peut contenir. Les systèmes de fichiers Linux se réfèrent à la limite en tant qu’*inodes*. La limite maxfiles d’un volume Azure NetApp Files est indexée en fonction de la taille (du quota) du volume. La limite maxfiles d’un volume augmente ou diminue au rythme de 20 millions de fichiers par Tio de taille de volume provisionnée. 

Le service ajuste dynamiquement la limite maxfiles d’un volume en fonction de sa taille provisionnée. Par exemple, un volume configuré initialement avec une taille de 1 Tio a une limite maxfiles de 20 millions. Les modifications ultérieures apportées à la taille du volume entraînent une réajustement automatique de la limite maxfiles selon les règles suivantes : 

|    Taille du volume (quota)     |  Réajustement automatique de la limite maxfiles    |
|----------------------------|-------------------|
|    <= 1 Tio                |    20 millions     |
|    > 1 Tio mais <= 2 Tio    |    40 millions     |
|    > 2 Tio mais <= 3 Tio    |    60 millions     |
|    > 3 Tio mais <= 4 Tio    |    80 millions     |
|    > 4 Tio                 |    100 millions    |

Si vous avez déjà alloué au moins 4 Tio de quota pour un volume, vous pouvez initier une [demande de support](#limit_increase) afin d’augmenter la limite maxfiles (inodes) au-delà de 100 millions. Tous les 100 millions de fichiers que vous augmentez (ou une fraction de ceux-ci), vous devez augmenter le quota de volume correspondant de 4 Tio.  Par exemple, si vous augmentez la limite maxfiles de 100 millions de fichiers à 200 millions de fichiers (ou n’importe quel nombre dans cette plage), vous devez augmenter le quota de volume de 4 Tio à 8 Tio.

Vous pouvez augmenter la limite maxfiles à 500 millions si votre quota de volume est d’au moins 20 Tio. <!-- ANF-11854 --> 

## <a name="request-limit-increase"></a>Demander une augmentation de limite <a name="limit_increase"></a> 

Vous pouvez créer une demande de support Azure pour augmenter les limites ajustables à partir du tableau ci-dessus. 

À partir du volet de navigation du portail Azure : 

1. Cliquez sur **Aide + support**.
2. Cliquez sur **Nouvelle demande de support**.
3. Sous l’onglet De base, fournissez les informations suivantes : 
    1. Type de problème : Sélectionnez **Limites du service et de l’abonnement (quotas)** .
    2. Abonnements : Sélectionnez l’abonnement pour la ressource dont vous voulez augmenter le quota.
    3. Type de quota : Sélectionnez **Stockage : limites d’Azure NetApp Files**.
    4. Cliquez sur **Suivant : Solutions**.
4. Sous l’onglet Détails :
    1. Dans la zone Description, fournissez les informations suivantes pour le type de ressource correspondant :

        |  Ressource  |    Ressources parentes      |    Nouvelles limites demandées     |    Raison de l’augmentation du quota       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Compte |  *Identifiant d’abonnement*   |  *Nouveau **nombre** maximal demandé*    |  *Quel scénario ou cas d’utilisation a motivé la demande ?*  |
        |  pool    |  *ID d’abonnement, URI de compte NetApp*  |  *Nouveau nombre maximal de **pools** demandé*   |  *Quel scénario ou cas d’utilisation a motivé la demande ?*  |
        |  Volume  |  *ID d’abonnement, URI de compte NetApp, URI de pool de capacité*   |  *Nouveau **volume** maximal demandé*     |  *Quel scénario ou cas d’utilisation a motivé la demande ?*  |
        |  Maxfiles  |  *ID d’abonnement, URI de compte NetApp, URI de pool de capacité, URI de volume*   |  *Nouveau nombre maximal **maxfiles** demandé*     |  *Quel scénario ou cas d’utilisation a motivé la demande ?*  |    
        |  Volumes de protection des données de réplication inter-région  |  *ID d’abonnement, URI de compte NetApp de destination, URI de pool de capacité de destination, URI de compte NetApp source, URI de pool de capacité source, URI de volume source*   |  *Nouveau nombre maximal de **volumes de protection des données de réplication inter-région (volumes de destination)** _     |  _Quel scénario ou cas d’utilisation a motivé la demande ?*  |    

    2. Spécifiez la méthode de support appropriée et fournissez les informations relatives à votre contrat.

    3. Cliquez sur **Suivant : Vérifier + créer** pour créer la demande. 


## <a name="next-steps"></a>Étapes suivantes  

- [Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modèle de coût pour Azure NetApp Files](azure-netapp-files-cost-model.md)
