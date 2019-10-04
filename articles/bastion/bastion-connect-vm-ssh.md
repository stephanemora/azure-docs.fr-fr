---
title: Connexion à une machine virtuelle Linux à l’aide d’Azure Bastion | Microsoft Docs
description: Dans cet article, découvrez comment vous connecter à une machine virtuelle Linux à l’aide d’Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 69548541d16db95f633400808f72aebaf59cff08
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477776"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion-preview"></a>Connectez-vous au travers de SSH à une machine virtuelle Linux avec Azure Bastion (préversion)

Cet article vous montre comment vous connecter en toute sécurité et en toute transparence avec le protocole SSH à vos machines virtuelles Linux dans un réseau virtuel Azure. Vous pouvez vous connecter à une machine virtuelle directement à partir du Portail Azure. Lorsque vous utilisez Azure Bastion, les machines virtuelles n’ont pas besoin de client, d’agent ni de logiciel supplémentaire. Pour plus d’informations sur Azure Bastion, consultez la [vue d’ensemble](bastion-overview.md).

Vous pouvez utiliser Azure Bastion pour vous connecter au travers du protocole SSH à une machine virtuelle Linux. Vous pouvez utiliser le nom d’utilisateur/mot de passe et les clés SSH pour l’authentification. Vous pouvez vous connecter à votre machine virtuelle avec des clés SSH en utilisant :

* Une clé privée que vous entrez manuellement
* Un fichier qui contient les informations relatives à la clé privée

La clé privée SSH doit être dans un format qui commence par `"-----BEGIN RSA PRIVATE KEY-----"` et se termine par `"-----END RSA PRIVATE KEY-----"`.

> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Avant de commencer

Assurez-vous que vous avez configuré un hôte Azure Bastion pour le réseau virtuel dans lequel réside la machine virtuelle. Pour plus d’informations, consultez [Créer un hôte Azure Bastion](bastion-create-host-portal.md). Une fois que le service Bastion est configuré et déployé dans votre réseau virtuel, vous pouvez l’utiliser pour vous connecter à toutes les machines virtuelles dans ce réseau virtuel. Dans cette préversion, lorsque vous utilisez Bastion pour vous connecter, ce dernier part du principe que vous utilisez le protocole RDP pour vous connecter à une machine virtuelle Windows, et le protocole SSH pour vous connecter à vos machines virtuelles Linux.

Pour établir une connexion, les rôles suivants sont nécessaires :

* Rôle de lecteur sur la machine virtuelle
* Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle
* Rôle de lecteur sur la ressource Azure Bastion

## <a name="username"></a>Connexion : Utilisation du nom d’utilisateur et du mot de passe


1.  Utilisez [ce lien](https://aka.ms/BastionHost) afin d’ouvrir la page du portail en préversion pour Azure Bastion. Accédez à la machine virtuelle à laquelle vous souhaitez vous connecter, puis cliquez sur **Se connecter**. La machine virtuelle doit être une machine virtuelle Linux en cas de connexion SSH.
1. Lorsque vous cliquez sur Se connecter, une barre latérale s’affiche avec trois onglets : RDP, SSH et Bastion. Si le service Bastion a été configuré pour le réseau virtuel, l’onglet Bastion est actif par défaut. Si vous n’avez pas configuré Bastion pour le réseau virtuel, consultez [Configure Bastion (Configurer Bastion)](bastion-create-host-portal.md). Si vous ne voyez pas **Bastion** dans la liste, c’est que vous n’avez pas ouvert le portail en préversion. Ouvrez le portail en utilisant [ce lien](https://aka.ms/BastionHost).

      ![Connexion à une machine virtuelle](./media/bastion-connect-vm-ssh/bastion.png)

1. Entrez le nom d’utilisateur et le mot de passe pour le protocole SSH vers votre machine virtuelle.
1. Cliquez sur le bouton **Se connecter** après avoir entré la clé.

## <a name="privatekey"></a>Connexion : Entrez manuellement une clé privée

1.  Utilisez [ce lien](https://aka.ms/BastionHost) afin d’ouvrir la page du portail en préversion pour Azure Bastion. Accédez à la machine virtuelle à laquelle vous souhaitez vous connecter, puis cliquez sur **Se connecter**. La machine virtuelle doit être une machine virtuelle Linux en cas de connexion SSH.
1. Lorsque vous cliquez sur Se connecter, une barre latérale s’affiche avec trois onglets : RDP, SSH et Bastion. Si le service Bastion a été configuré pour le réseau virtuel, l’onglet Bastion est actif par défaut. Si vous n’avez pas configuré Bastion pour le réseau virtuel, consultez [Configure Bastion (Configurer Bastion)](bastion-create-host-portal.md). Si vous ne voyez pas **Bastion** dans la liste, c’est que vous n’avez pas ouvert le portail en préversion. Ouvrez le portail en utilisant [ce lien](https://aka.ms/BastionHost).

      ![Connexion à une machine virtuelle](./media/bastion-connect-vm-ssh/bastion.png)

1. Entrez le nom d’utilisateur, puis sélectionnez **Clé privée SSH**.
1. Entrez votre clé privée dans la zone de texte **Clé privée SSH** (ou collez-la directement).
1. Cliquez sur le bouton **Se connecter** après avoir entré la clé.

## <a name="ssh"></a>Connexion : Utilisation d’un fichier de clé privée

1.  Utilisez [ce lien](https://aka.ms/BastionHost) afin d’ouvrir la page du portail en préversion pour Azure Bastion. Accédez à la machine virtuelle à laquelle vous souhaitez vous connecter, puis cliquez sur **Se connecter**. La machine virtuelle doit être une machine virtuelle Linux en cas de connexion SSH.

    ![Connexion à une machine virtuelle](./media/bastion-connect-vm-ssh/connect.png)

1. Lorsque vous cliquez sur Se connecter, une barre latérale s’affiche avec trois onglets : RDP, SSH et Bastion. Si le service Bastion a été configuré pour le réseau virtuel, l’onglet Bastion est actif par défaut. Si vous n’avez pas configuré Bastion pour le réseau virtuel, consultez [Configure Bastion (Configurer Bastion)](bastion-create-host-portal.md). Si vous ne voyez pas **Bastion** dans la liste, c’est que vous n’avez pas ouvert le portail en préversion. Ouvrez le portail en utilisant [ce lien](https://aka.ms/BastionHost).

    ![Connexion à une machine virtuelle](./media/bastion-connect-vm-ssh/bastion.png)

1. Entrez le nom d’utilisateur, puis sélectionnez **SSH Private Key from Local File (Clé privée SSH provenant du fichier local)** .
1. Cliquez sur le bouton **Parcourir** (icône de dossier dans le fichier local).
1. Recherchez le fichier, puis cliquez sur **Ouvrir**.
1. Cliquez sur **Se connecter** pour vous connecter à la machine virtuelle. Une fois que vous avez cliqué sur Se connecter, le protocole SSH vers cette machine virtuelle s’ouvre directement dans le portail Azure. La connexion s’effectue au moyen du protocole HTML5 sur le port 443 du service Bastion, sur l’adresse IP privée de votre machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

Lire le [FAQ Bastion](bastion-faq.md)
