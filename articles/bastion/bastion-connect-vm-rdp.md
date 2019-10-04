---
title: Connexion à une machine virtuelle Windows à l’aide d’Azure Bastion | Microsoft Docs
description: Dans cet article, découvrez comment vous connecter à une machine virtuelle Azure exécutant Windows à l’aide d’Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 376b7042a513dd50647dc8f88bf1de70f65bb21c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478398"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion-preview"></a>Connexion à une machine virtuelle Windows à l’aide d’Azure Bastion (préversion)

Cet article vous montre comment connecter via RDP de manière fluide et sécurisée les machines virtuelles Windows d’un réseau virtuel à l’aide d’Azure Bastion. Vous pouvez vous connecter à une machine virtuelle directement à partir du portail Azure. Lorsque vous utilisez Azure Bastion, les machines virtuelles n’ont pas besoin de client, d’agent ni de logiciel supplémentaire. Pour plus d’informations sur Azure Bastion, consultez la [vue d’ensemble](bastion-overview.md).

> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Avant de commencer

Assurez-vous que vous avez configuré un hôte Azure Bastion pour le réseau virtuel dans lequel réside la machine virtuelle. Pour plus d’informations, consultez [Créer un hôte Azure Bastion](bastion-create-host-portal.md). Une fois que le service Bastion est approvisionné et déployé dans votre réseau virtuel, vous pouvez l’utiliser pour vous connecter à toutes les machines virtuelles dans ce réseau virtuel. Dans cette préversion, Bastion part du principe que vous utilisez le protocole RDP pour vous connecter à une machine virtuelle Windows et le protocole SSH pour vous connecter à vos machines virtuelles Linux. Pour plus d’informations sur la connexion à une machine virtuelle Linux, consultez [Connexion à une machine virtuelle - Linux](bastion-connect-vm-ssh.md).

Pour établir une connexion, les rôles suivants sont nécessaires :

* Rôle de lecteur sur la machine virtuelle
* Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle
* Rôle de lecteur sur la ressource Azure Bastion

## <a name="rdp"></a>Connexion RDP

1. Utilisez [ce lien](https://aka.ms/BastionHost) afin d’ouvrir la page du portail en préversion pour Azure Bastion. Accédez à la machine virtuelle à laquelle vous souhaitez vous connecter, puis cliquez sur **Se connecter**. La machine virtuelle doit être une machine virtuelle Windows en cas de connexion RDP.

    ![Connexion à une machine virtuelle](./media/bastion-connect-vm-rdp/connect.png)

1. Lorsque vous cliquez sur Se connecter, une barre latérale s’affiche avec trois onglets : RDP, SSH et Bastion. Si le service Bastion a été approvisionné pour le réseau virtuel, l’onglet Bastion est actif par défaut. Si vous n’avez pas approvisionné Bastion pour le réseau virtuel, vous pouvez cliquer sur le lien permettant de configurer Bastion. Pour en savoir plus sur la procédure de configuration, consultez [Configurer Bastion](bastion-create-host-portal.md). Si vous ne voyez pas **Bastion** dans la liste, c’est que vous n’avez pas ouvert le portail en préversion. Ouvrez le portail en utilisant ce [lien vers la préversion](https://aka.ms/BastionHost).

    ![Connexion à une machine virtuelle](./media/bastion-connect-vm-rdp/bastion.png)

1. Dans l’onglet Bastion, entrez le nom d’utilisateur et le mot de passe pour votre machine virtuelle, puis cliquez sur **Se connecter**. La connexion RDP à cette machine virtuelle avec Bastion s’ouvrira directement dans le portail Azure (en HTML5) via le port 443 et le service Bastion.

    ![Connexion à une machine virtuelle](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Étapes suivantes

Lisez les [questions fréquentes sur Bastion](bastion-faq.md).
