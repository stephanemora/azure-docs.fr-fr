---
title: Configurer les fonctionnalités réseau d’un volume Azure NetApp Files | Microsoft Docs
description: Décrit les options des fonctionnalités réseau et la configuration de l’option Fonctionnalités réseau pour un volume.
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
ms.topic: how-to
ms.date: 10/04/2021
ms.custom: references_regions
ms.author: b-juche
ms.openlocfilehash: 6350084ef916132d7b4d77da00853836daeabccd
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129533578"
---
# <a name="configure-network-features-for-an-azure-netapp-files-volume"></a>Configurer les fonctionnalités réseau d’un volume Azure NetApp Files

La fonctionnalité **Fonctionnalités réseau** est disponible en préversion publique.  Cette fonctionnalité vous permet d’indiquer si vous souhaitez utiliser les fonctionnalités du réseau virtuel pour un volume Azure NetApp Files. Grâce à cette fonctionnalité, vous pouvez définir l’option sur **_Standard_ *_ ou _* _De base_**. Vous pouvez spécifier le paramètre lorsque vous créez un nouveau volume NFS, SMB ou à double protocole. Consultez [Consignes pour planifier un réseau Azure NetApp Files](azure-netapp-files-network-topologies.md) pour plus de détails sur les fonctionnalités réseau.

Cet article vous aide à comprendre les différentes options et vous montre comment configurer les fonctionnalités réseau.

## <a name="options-for-network-features"></a>Options des fonctionnalités réseau 

Deux paramètres sont disponibles pour les fonctionnalités réseau : 

* ***Standard***  
    Ce paramètre active des fonctionnalités de réseau virtuel pour le volume.  

    Si vous avez besoin de limites d’adresse IP plus élevées et de fonctionnalités de réseau virtuel telles que des [groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md), des [itinéraires définis par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md#user-defined) ou des modèles de connectivité supplémentaires, vous devez définir **Fonctionnalités réseau** sur *Standard*.

* ***De base***  
    Ce paramètre offre des limites d’adresse IP réduites (<1000) et aucune autre fonctionnalité de réseau virtuel pour les volumes.

    Si vous n’avez pas besoin de fonctionnalités de réseau virtuel, vous devez définir **Fonctionnalités réseau** sur *De base*.  

## <a name="supported-regions"></a>Régions prises en charge 

Actuellement, la capacité Fonctionnalités réseau est prise en charge dans les régions suivantes : 

* Centre-Nord des États-Unis 

## <a name="considerations"></a>Considérations

* Quelle que soit l’option Fonctionnalités réseau que vous définissez (*Standard* ou *De base*), un réseau virtuel Azure ne peut avoir qu’un seul sous-réseau délégué à Azure NetApp Files. Consultez [Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md#considerations). 
 
* Actuellement, vous pouvez spécifier le paramètre des fonctionnalités réseau uniquement pendant le processus de création d’un nouveau volume. Vous ne pouvez pas modifier le paramètre sur des volumes existants. 

* Vous pouvez créer des volumes avec les fonctionnalités réseau Standard uniquement si [la région Azure correspondante prend en charge la capacité de volume Standard](#supported-regions). 
    * Si la région prend en charge la capacité de volume Standard, la valeur par défaut du champ Fonctionnalités réseau de la page Créer un volume est *Standard*. Vous ne pouvez pas modifier ce paramètre en *De base*. 
    * Si la capacité de volume Standard n’est pas disponible pour la région, la valeur par défaut du champ Fonctionnalités réseau de la page Créer un volume est *De base*, et vous ne pouvez pas modifier ce paramètre.

* La possibilité de localiser un stockage compatible avec le type souhaité de fonctionnalités réseau dépend du réseau virtuel spécifié.  Si vous ne pouvez pas créer de volume en raison de ressources insuffisantes, vous pouvez essayer un autre réseau virtuel pour lequel un stockage compatible est disponible.
  
## <a name="register-the-feature"></a>Inscrire la fonctionnalité 

La capacité des fonctionnalités réseau est actuellement en préversion publique. Si vous utilisez cette fonctionnalité pour la première fois, vous devez d’abord l’inscrire.

1.  Pour inscrire la fonctionnalité, exécutez les commandes suivantes :

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSDNAppliance

    Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowPoliciesOnBareMetal
    ```

2. Vérifiez l’état d’inscription de la fonctionnalité : 

    > [!NOTE]
    > **RegistrationState** peut rester à l’état `Registering` jusqu’à 60 minutes avant de passer à l’état `Registered`. Avant de continuer, attendez que l’état soit `Registered`.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSDNAppliance

    Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowPoliciesOnBareMetal
    ```

Vous pouvez également utiliser les [commandes Azure CLI](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` et `az feature show` pour inscrire la fonctionnalité et afficher l’état de l’inscription. 

## <a name="set-the-network-features-option"></a>Définir l’option Fonctionnalités réseau

Cette section vous montre comment définir l’option Fonctionnalités réseau. 

1. Au cours du processus de création d’un nouveau volume [NFS](azure-netapp-files-create-volumes.md), [SMB](azure-netapp-files-create-volumes-smb.md) ou [à double protocole](create-volumes-dual-protocol.md), vous pouvez définir l’option **Fonctionnalités réseau** sur **De base** ou **Standard** sous l’onglet Informations de base de l’écran Créer un volume.

    La capture d’écran suivante montre un exemple de création de volume pour une région qui prend en charge les capacités de fonctionnalités réseau Standard : 

    ![Capture d’écran montrant la création d’un volume pour les fonctionnalités réseau Standard.](../media/azure-netapp-files/network-features-create-standard.png)

    La capture d’écran suivante montre un exemple de création de volume pour une région qui *ne prend pas* en charge les capacités de fonctionnalités réseau Standard : 

    ![Capture d’écran montrant la création d’un volume pour les fonctionnalités réseau De base.](../media/azure-netapp-files/network-features-create-basic.png)

2. Avant de terminer le processus de création du volume, vous pouvez afficher le paramètre des fonctionnalités réseau spécifié dans l’onglet **Vérifier + créer** de l’écran Créer un volume. Cliquez sur **Créer** pour terminer la création du volume.

    ![Capture d’écran montrant l’onglet Vérifier et créer de la création d’un volume.](../media/azure-netapp-files/network-features-review-create-tab.png)

3. Vous pouvez cliquer sur **Volumes** pour afficher le paramètre des fonctionnalités réseau pour chaque volume :

    [![Capture d’écran montrant la page Volumes affichant le paramètre des fonctionnalités réseau.](../media/azure-netapp-files/network-features-volume-list.png)](../media/azure-netapp-files/network-features-volume-list.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes  

* [Consignes pour planifier un réseau Azure NetApp Files](azure-netapp-files-network-topologies.md)
* [Créer un volume NFS pour Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Créer un volume SMB pour Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Créer un volume double protocole pour Azure NetApp Files](create-volumes-dual-protocol.md) 