---
title: Ouvrir une session sur une machine virtuelle Azure Classic | Microsoft Docs
description: Utilisez le Portail Azure pour vous connecter à une machine virtuelle Windows créée avec le modèle de déploiement classique.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 87ecc65d2d4802ae826f3260b66b26e0bbe414e6
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012357"
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Ouvrir une session sur une machine virtuelle Windows à l’aide du portail Azure
Sur le portail Azure, vous utilisez le bouton **Connecter** pour démarrer une session Bureau à distance et ouvrir une session sur une machine virtuelle Windows.

Vous souhaitez vous connecter à une machine virtuelle Linux ? Consultez [Connexion à une machine virtuelle sous Linux](../../linux/mac-create-ssh-keys.md).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour plus d’informations sur la connexion à une machine virtuelle avec le modèle Resource Manager, suivez [ce lien](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>Connectez-vous à la machine virtuelle.
1. Connectez-vous au portail Azure.
2. Cliquez sur la machine virtuelle à laquelle vous souhaitez accéder. Le nom est répertorié dans le volet **Toutes les ressources**.

    ![Virtual-machine-locations](./media/connect-logon/azureportaldashboard.png)

1. Cliquez sur le bouton **Se connecter** sur la page de propriétés de la machine virtuelle. 
2. Sur la page **Se connecter à la machine virtuelle**, laissez les options appropriées sélectionnées, puis cliquez sur **Télécharger le fichier RDP**.
2. Ouvrez le fichier RDP téléchargé et, à l’invite, cliquez sur **Se connecter**. 
2. Un message vous avertit que le fichier `.rdp` provient d’un éditeur inconnu. C’est normal. Dans la fenêtre Bureau à distance, cliquez sur **Connecter** pour continuer.
   
    ![Capture d’écran d’avertissement relatif à un éditeur inconnu.](./media/connect-logon/rdp-warn.png)
3. Dans la fenêtre **Sécurité Windows**, sélectionnez **Plus de choix**, puis **Utiliser un autre compte**. Tapez les informations d’identification d’un compte sur la machine virtuelle, puis cliquez sur **OK**.
   
     **Compte local** : il s’agit généralement du nom d’utilisateur et du mot de passe du compte local que vous avez spécifiés quand vous avez créé la machine virtuelle. Le domaine correspond alors au nom de la machine virtuelle et vous devez l’entrer sous la forme *nom_machine_virtuelle*&#92;*nom_utilisateur*.  
   
    **Machine virtuelle jointe à un domaine** : si la machine virtuelle appartient à un domaine, entrez le nom d’utilisateur au format *Domaine*&amp;#92;*Nom d’utilisateur*. Le compte doit également être membre du groupe Administrateurs ou bénéficier de privilèges d’accès à distance à la machine virtuelle.
   
    **Contrôleur de domaine** : si la machine virtuelle est un contrôleur de domaine, tapez le nom d’utilisateur et le mot de passe d’un compte d’administrateur de domaine pour ce domaine.
4. Cliquez sur **Oui** pour vérifier l’identité de la machine virtuelle et terminer la connexion.
   
   ![Capture d'écran montrant un message relatif à la vérification de l'identité de la machine virtuelle.](./media/connect-logon/cert-warning.png)

## <a name="next-steps"></a>Étapes suivantes
* Si le bouton **Connexion** est inactif ou que vous rencontrez d’autres problèmes avec la connexion Bureau à distance, essayez de réinitialiser la configuration. Dans le tableau de bord de la machine virtuelle, cliquez sur **Réinitialiser la configuration à distance**.

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* Si votre mot de passe pose problème, essayez de le réinitialiser. Cliquez sur **Réinitialiser le mot de passe** le long du bord gauche de du tableau de bord de la machine virtuelle, sous **Support + dépannage**.

    ![Reset-password](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Si ces conseils ne donnent aucun résultat ou ne vous sont pas utiles, consultez [Résolution des problèmes de connexion du Bureau à distance à une machine virtuelle Azure sous Windows](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Cet article vous guide tout au long des opérations de diagnostic et de résolution des problèmes courants.
