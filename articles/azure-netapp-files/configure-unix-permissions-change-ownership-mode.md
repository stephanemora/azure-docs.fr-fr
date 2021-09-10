---
title: Configurer des autorisations UNIX et modifier le mode de propriété pour les volumes NFS Azure NetApp Files et à double protocole | Microsoft Docs
description: Décrit la façon de définir les autorisations UNIX et les options de modification du mode de propriété pour les volumes NFS Azure NetApp Files et à double protocole.
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
ms.date: 08/06/2021
ms.author: b-juche
ms.openlocfilehash: bfb794b1344dbc4723ca699f572b487b54d60c77
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563299"
---
# <a name="configure-unix-permissions-and-change-ownership-mode-for-nfs-and-dual-protocol-volumes"></a>Configurer des autorisations UNIX et modifier le mode de propriété pour les volumes NFS et à double protocole

Pour les volumes NFS Azure NetApp Files ou les volumes à double protocole avec le style de sécurité `Unix`, vous avez la possibilité de définir les options d’**autorisations Unix** et de **mode de modification de propriété**  ( **`Chown Mode`** ). Vous pouvez spécifier ces paramètres lors de la création du volume ou après. 

## <a name="unix-permissions"></a>Autorisations Unix   

La fonctionnalité **Autorisations UNIX** d’Azure NetApp Files vous permet de spécifier des autorisations de modification pour le chemin d’accès de montage. Ce paramètre ne s’applique pas aux fichiers sous le chemin d’accès de montage.   

Le paramètre d’autorisations UNIX est défini sur `0770` par défaut. Ce paramètre par défaut accorde des autorisations de lecture, d’écriture et d’exécution au propriétaire et au groupe, mais aucune autorisation n’est accordée aux autres utilisateurs. 

 Vous pouvez spécifier une valeur d’autorisation Unix personnalisée (par exemple, `0755`) pour donner l’autorisation souhaitée au propriétaire, au groupe ou à d’autres utilisateurs.  

## <a name="change-ownership-mode"></a>Modifier le mode de propriété   

La fonctionnalité de modification du mode de propriété ( **`Chown Mode`** ) vous permet de définir les fonctionnalités de gestion des propriétés des fichiers et des répertoires.  Vous pouvez spécifier ou modifier le paramètre dans la stratégie d’exportation d’un volume. Deux options sont disponibles pour **`Chown Mode`** .   

* `Restricted` (par défaut) - Seul l’utilisateur racine peut modifier la propriété des fichiers et des répertoires.
* `Unrestricted` - Les utilisateurs non-racine peuvent uniquement modifier la propriété des fichiers et des répertoires dont ils sont propriétaires.

## <a name="considerations"></a>Considérations  

* Les autorisations UNIX que vous spécifiez s’appliquent uniquement au point de montage du volume (répertoire racine).  
* Vous ne pouvez pas modifier les autorisations UNIX sur les volumes source ou de destination qui se trouvent dans une configuration de réplication entre régions. 

## <a name="steps"></a>Étapes

1. Les fonctionnalités d’autorisations UNIX et de modification du mode de propriété sont actuellement en préversion. Avant d’utiliser ces fonctionnalités pour la première fois, vous devez commencer par les inscrire :   

    1. Inscrivez la fonctionnalité **Autorisations Unix** :   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFUnixPermissions
        ```

    2.  Inscrivez la fonctionnalité **modifier le mode de propriété** :    

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFChownMode
        ```

    3. Vérifiez l’état d’inscription de la fonctionnalité :    

        > [!NOTE]
        > **RegistrationState** peut rester à l’état `Registering` jusqu’à 60 minutes avant de passer à l’état `Registered`. Avant de continuer, attendez que l’état soit `Registered`.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFUnixPermissions  
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFChownMode
        ```
        
    Vous pouvez également utiliser les [commandes Azure CLI](/cli/azure/feature) `az feature register` et `az feature show` pour inscrire la fonctionnalité et afficher l’état de l’inscription. 

2. Vous pouvez spécifier les **autorisations Unix** et modifier les paramètres du mode de propriété ( **`Chown Mode`** ) sous l’onglet **Protocole** lorsque vous [créez un volume NFS](azure-netapp-files-create-volumes.md) ou un [volume à double protocole](create-volumes-dual-protocol.md). 

    L’exemple suivant montre l’écran Créer un volume pour un volume NFS. 

    ![Captures d’écran montrant l’écran Créer un volume pour NFS.](../media/azure-netapp-files/unix-permissions-create-nfs-volume.png)

3. Pour les volumes NFS ou à double protocole existants, vous pouvez définir ou modifier les **autorisations Unix** et **modifier le mode de propriété** comme suit :  

    1. Pour modifier les autorisations UNIX, cliquez avec le bouton droit sur le **volume**, puis sélectionnez **Modifier**. Dans la fenêtre Modifier qui s’affiche, spécifiez une valeur pour les **autorisations Unix**.  
        ![Captures d’écran montrant l’écran de modification pour les autorisations Unix.](../media/azure-netapp-files/unix-permissions-edit.png)

    2. Pour modifier le mode de modification de la propriété, cliquez sur le **volume**, cliquez sur **Exporter la stratégie**, puis modifiez le paramètre **`Chown Mode`** .  
        ![Captures d’écran montrant l’écran Exporter la stratégie.](../media/azure-netapp-files/chown-mode-edit.png)

## <a name="next-steps"></a>Étapes suivantes  

* [Créer un volume NFS pour Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Créer un volume double protocole pour Azure NetApp Files](create-volumes-dual-protocol.md) 
* [Configurer la stratégie d’exportation](azure-netapp-files-configure-export-policy.md)
