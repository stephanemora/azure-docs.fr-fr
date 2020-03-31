---
title: Se connecter à une machine virtuelle Windows avec Azure Bastion
description: Dans cet article, découvrez comment vous connecter à une machine virtuelle Azure exécutant Windows à l’aide d’Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 6ec60dc313c8a4374637adf38ea0e5a7d4ed964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597340"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Connexion à une machine virtuelle Windows à l’aide d’Azure Bastion

En utilisant Azure Bastion, vous pouvez vous connecter de manière sécurisée et fluide à vos machines virtuelles via SSL directement dans le portail Azure. Quand vous utilisez Azure Bastion, vos machines virtuelles n’ont pas besoin de client, d’agent ni de logiciel supplémentaire. Cet article vous explique comment vous connecter à vos machines virtuelles Windows. Pour plus d’informations sur la connexion à une machine virtuelle Linux, consultez [Se connecter à une machine virtuelle à l’aide d’Azure Bastion - Linux](bastion-connect-vm-ssh.md).

Azure Bastion fournit une connectivité sécurisée à toutes les machines virtuelles du réseau virtuel dans lequel il est provisionné. Azure Bastion protège vos machines virtuelles contre l’exposition des ports RDP/SSH au monde extérieur, tout en fournissant un accès sécurisé à l’aide de RDP/SSH. Pour plus d'informations, consultez la [Vue d’ensemble](bastion-overview.md).

## <a name="before-you-begin"></a>Avant de commencer

Assurez-vous d’avoir configuré un hôte Azure Bastion pour le réseau virtuel dans lequel se trouve la machine virtuelle. Une fois que le service Bastion est provisionné et déployé dans votre réseau virtuel, vous pouvez l’utiliser pour vous connecter à toutes les machines virtuelles dans le réseau virtuel. Pour configurer un hôte Azure Bastion, consultez [Créer un hôte Azure Bastion](bastion-create-host-portal.md).

### <a name="required-roles"></a>Rôles nécessaires

Pour établir une connexion, les rôles suivants sont nécessaires :

* Rôle de lecteur sur la machine virtuelle
* Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle
* Rôle de lecteur sur la ressource Azure Bastion

### <a name="ports"></a>Ports

Pour vous connecter à la machine virtuelle Windows, les ports suivants doivent être ouverts sur votre machine virtuelle Windows :

* Ports d’entrée : RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Connexion

1. Ouvrez le [portail Azure](https://portal.azure.com). Accédez à la machine virtuelle à laquelle vous souhaitez vous connecter, puis cliquez sur **Se connecter** et sélectionnez **Bastion** dans la liste déroulante.

   ![Connexion à une machine virtuelle](./media/bastion-connect-vm-rdp/connect.png)
1. Quand vous cliquez sur Bastion, une barre latérale s’affiche avec trois onglets : RDP, SSH et Bastion. Si le service Bastion a été approvisionné pour le réseau virtuel, l’onglet Bastion est actif par défaut. Si vous n’avez pas approvisionné Bastion pour le réseau virtuel, vous pouvez cliquer sur le lien permettant de configurer Bastion. Pour en savoir plus sur la procédure de configuration, consultez [Configurer Bastion](bastion-create-host-portal.md).

   ![Onglet Bastion](./media/bastion-connect-vm-rdp/bastion.png)
1. Sous l’onglet Bastion, entrez le nom d’utilisateur et le mot de passe pour votre machine virtuelle, puis cliquez sur **Se connecter**. La connexion RDP à cette machine virtuelle avec Bastion s’ouvrira directement dans le portail Azure (en HTML5) via le port 443 et le service Bastion.

   ![Connexion RDP](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Étapes suivantes

Lisez les [questions fréquentes sur Bastion](bastion-faq.md).
