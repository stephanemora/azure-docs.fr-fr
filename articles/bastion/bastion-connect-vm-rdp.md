---
title: Connexion à une machine virtuelle Windows à l’aide d’Azure Bastion | Microsoft Docs
description: Dans cet article, découvrez comment vous connecter à une machine virtuelle Azure exécutant Windows à l’aide d’Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7dad6a517341f83f693e1e7e1f7d27e899e00f7e
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990485"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Connexion à une machine virtuelle Windows à l’aide d’Azure Bastion

Cet article vous montre comment connecter via RDP de manière fluide et sécurisée les machines virtuelles Windows d’un réseau virtuel à l’aide d’Azure Bastion. Vous pouvez vous connecter à une machine virtuelle directement à partir du portail Azure. Lorsque vous utilisez Azure Bastion, les machines virtuelles n’ont pas besoin de client, d’agent ni de logiciel supplémentaire. Pour plus d’informations sur Azure Bastion, consultez la [vue d’ensemble](bastion-overview.md).

## <a name="before-you-begin"></a>Avant de commencer

Assurez-vous que vous avez configuré un hôte Azure Bastion pour le réseau virtuel dans lequel réside la machine virtuelle. Pour plus d’informations, consultez [Créer un hôte Azure Bastion](bastion-create-host-portal.md). Une fois que le service Bastion est approvisionné et déployé dans votre réseau virtuel, vous pouvez l’utiliser pour vous connecter à toutes les machines virtuelles dans ce réseau virtuel.

Bastion part du principe que vous utilisez le protocole RDP pour vous connecter à une machine virtuelle Windows et le protocole SSH pour vous connecter à vos machines virtuelles Linux. Pour plus d’informations sur la connexion à une machine virtuelle Linux, consultez [Connexion à une machine virtuelle - Linux](bastion-connect-vm-ssh.md).

### <a name="required-roles"></a>Rôles nécessaires
Pour établir une connexion, les rôles suivants sont nécessaires :

* Rôle de lecteur sur la machine virtuelle
* Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle
* Rôle de lecteur sur la ressource Azure Bastion

### <a name="ports"></a>Ports

Pour vous connecter à la machine virtuelle Windows via le protocole RDP, les ports suivants doivent être ouverts sur votre machine virtuelle Windows :

* Ports d’entrée : RDP (3389)

## <a name="rdp"></a>Connexion RDP

1. Ouvrez le [portail Azure](https://portal.azure.com). Accédez à la machine virtuelle à laquelle vous souhaitez vous connecter, puis cliquez sur **Se connecter**. La machine virtuelle doit être une machine virtuelle Windows en cas de connexion RDP.

   ![Connexion à une machine virtuelle](./media/bastion-connect-vm-rdp/connect.png)
1. Lorsque vous cliquez sur Se connecter, une barre latérale s’affiche avec trois onglets : RDP, SSH et Bastion. Si le service Bastion a été approvisionné pour le réseau virtuel, l’onglet Bastion est actif par défaut. Si vous n’avez pas approvisionné Bastion pour le réseau virtuel, vous pouvez cliquer sur le lien permettant de configurer Bastion. Pour en savoir plus sur la procédure de configuration, consultez [Configurer Bastion](bastion-create-host-portal.md).

   ![Connexion à une machine virtuelle](./media/bastion-connect-vm-rdp/bastion.png)
1. Dans l’onglet Bastion, entrez le nom d’utilisateur et le mot de passe pour votre machine virtuelle, puis cliquez sur **Se connecter**. La connexion RDP à cette machine virtuelle avec Bastion s’ouvrira directement dans le portail Azure (en HTML5) via le port 443 et le service Bastion.

   ![Connexion à une machine virtuelle](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Étapes suivantes

Lisez les [questions fréquentes sur Bastion](bastion-faq.md).
