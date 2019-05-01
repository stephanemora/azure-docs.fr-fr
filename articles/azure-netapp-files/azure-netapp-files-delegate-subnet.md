---
title: Déléguer un sous-réseau à Azure NetApp Files | Microsoft Docs
description: Décrit la procédure de délégation d’un sous-réseau à Azure NetApp Files.
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
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: fd8e380ad68b86b9ffd0f1e40efde8bdadfb19c5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711820"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Déléguer un sous-réseau à Azure NetApp Files 

Vous devez déléguer un sous-réseau à Azure NetApp Files.   Lorsque vous créez un volume, vous devez spécifier le sous-réseau délégué.

## <a name="considerations"></a>Considérations
* L’Assistant de création d’un sous-réseau applique par défaut un masque réseau /24, qui fournit 251 adresses IP disponibles. L’utilisation d’un masque réseau /28, qui fournit 16 adresses IP utilisables, est suffisante pour le service.
* Dans chaque réseau virtuel Azure, un seul sous-réseau peut être délégué à Azure NetApp Files.
* Vous ne pouvez pas désigner un groupe de sécurité ou un point de terminaison de service réseau dans le sous-réseau délégué. Cette opération fait échouer la délégation du sous-réseau.
* Accès à un volume à partir d’un réseau virtuel homologué dans le monde entier n’est pas pris en charge.
* Création [itinéraires personnalisés définis par l’utilisateur](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) sur les sous-réseaux de machine virtuelle avec adresse préfixe (destination) à un sous-réseau délégué à Azure NetApp fichiers non pris en charge. Cela affectera la connectivité de machine virtuelle.

## <a name="steps"></a>Étapes 
1.  Accédez au panneau **Réseaux virtuels** dans le Portail Azure, puis sélectionnez le réseau virtuel que vous souhaitez utiliser pour Azure NetApp Files.    

1. Sélectionnez **Sous-réseaux** dans le panneau Réseau virtuel et cliquez sur le bouton **+Sous-réseau**. 

1. Créez un sous-réseau à utiliser pour Azure NetApp Files en renseignant les champs obligatoires suivants dans la page Ajouter un sous-réseau :
    * **Nom** : spécifiez le nom du sous-réseau.
    * **Plage d’adresses** : spécifiez la plage d’adresses IP.
    * **Délégation de sous-réseau** : sélectionnez **Microsoft.NetApp/volumes**. 

      ![Délégation de sous-réseau](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Vous pouvez également créer et déléguer un sous-réseau lorsque vous [créez un volume pour Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Étapes suivantes  
* [Créer un volume pour Azure NetApp Files](azure-netapp-files-create-volumes.md)
* En savoir plus sur [l’intégration d’un réseau virtuel pour les services Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


