---
title: Activer la disponibilité continue sur les volumes SMB Azure NetApp Files existants | Microsoft Docs
description: Décrit comment activer la disponibilité continue SMB sur un volume SMB Azure NetApp Files existant.
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
ms.date: 10/15/2021
ms.author: b-juche
ms.openlocfilehash: 8dace313ed9296dd514fb059e8ed681a6276af0f
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074529"
---
# <a name="enable-continuous-availability-on-existing-smb-volumes"></a>Activer la disponibilité continue sur des volumes SMB existants

Vous pouvez activer la fonctionnalité de disponibilité continue SMB lors de la [création d’un volume SMB](azure-netapp-files-create-volumes-smb.md#continuous-availability). Vous pouvez également activer la disponibilité continue SMB sur un volume SMB existant. Cet article vous explique comment procéder.

## <a name="considerations"></a>Considérations

* L’option [**Masquer le chemin d’accès de la capture instantanée**](snapshots-edit-hide-path.md) n’a actuellement aucun effet pour les volumes SMB avec disponibilité continue.  

* Le répertoire `~snapshot` (qui peut être utilisé pour se déplacer dans d’autres volumes SMB) n’est pas visible pour les volumes SMB avec disponibilité continue. Vous pouvez entrer manuellement `~snapshot\<snapshotName>` pour accéder à la capture instantanée.

## <a name="steps"></a>Étapes

1. Assurez-vous que vous êtes [inscrit à la fonctionnalité de partages de disponibilité continue SMB](https://aka.ms/anfsmbcasharespreviewsignup) .  

    Vous devez activer la disponibilité continue uniquement pour les [conteneurs de profil utilisateur FSLogix](../virtual-desktop/create-fslogix-profile-container.md) et SQL Server. L’utilisation de partages de disponibilité continue SMB pour des charges de travail autres que les conteneurs de profil utilisateur FSLogix et SQL Server n’est *pas* prise en charge. Cette fonctionnalité est actuellement prise en charge sur Windows SQL Server. Linux SQL Server n’est pas pris en charge actuellement. Si vous utilisez un compte non-administrateur (domaine) pour installer SQL Server, assurez-vous que le compte dispose du privilège de sécurité requis. Si le compte de domaine ne dispose pas du privilège de sécurité requis (`SeSecurityPrivilege`) et que le privilège ne peut pas être défini au niveau du domaine, vous pouvez octroyer le privilège au compte à l’aide du champ **Utilisateurs avec privilège de sécurité** des connexions Active Directory. Consultez [Créer une connexion Active Directory](create-active-directory-connections.md#create-an-active-directory-connection).
            
3. Cliquez sur le volume SMB pour lequel vous souhaitez activer la disponibilité continue. Puis cliquez sur **Modifier**.  
4. Dans la fenêtre de modification qui apparaît, cochez la case **Activer la disponibilité continue**.   
    ![Capture instantanée affichant l’option Activer la disponibilité continue.](../media/azure-netapp-files/enable-continuous-availability.png)

4. Redémarrez les systèmes de Windows qui se connectent au partage SMB existant.   

    > [!NOTE]
    > La seule sélection de l’option **Activer la disponibilité continue** n’entraîne pas automatiquement la disponibilité continue des sessions SMB existantes. Après avoir sélectionné l’option, veillez à redémarrer le serveur pour que la modification prenne effet.  

5. Utilisez la commande suivante pour vérifier que la disponibilité continue est activée et utilisée sur le système qui monte le volume :

    ```powershell-interactive
    get-smbconnection | select -Property servername,ContinuouslyAvailable
    ```
 
    Vous devrez peut-être installer une version plus récente de PowerShell. 

    Si vous connaissez le nom du serveur, vous pouvez utiliser le paramètre `-ServerName` avec la commande. Consultez les détails de la commande PowerShell [Get-SmbConnection](/powershell/module/smbshare/get-smbconnection?view=windowsserver2019-ps&preserve-view=true).

## <a name="next-steps"></a>Étapes suivantes  

* [Créer un volume SMB pour Azure NetApp Files](azure-netapp-files-create-volumes-smb.md)
