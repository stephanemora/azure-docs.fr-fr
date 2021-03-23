---
title: Se connecter à une machine virtuelle Linux à l’aide d’Azure Bastion
description: Dans cet article, découvrez comment vous connecter à une machine virtuelle Linux à l’aide d’Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: a5cda5d4d447bc04f853ea4a9abd15be75e7e177
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588716"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Connexion SSH à une machine virtuelle Linux à l'aide d'Azure Bastion

Cet article vous montre comment vous connecter en toute sécurité et en toute transparence avec le protocole SSH à vos machines virtuelles Linux dans un réseau virtuel Azure. Vous pouvez vous connecter à une machine virtuelle directement à partir du portail Azure. Lorsque vous utilisez Azure Bastion, les machines virtuelles n’ont pas besoin de client, d’agent ni de logiciel supplémentaire. Pour plus d’informations sur Azure Bastion, consultez la [vue d’ensemble](bastion-overview.md).

Vous pouvez utiliser Azure Bastion pour vous connecter au travers du protocole SSH à une machine virtuelle Linux. Vous pouvez utiliser le nom d’utilisateur/mot de passe et les clés SSH pour l’authentification. Vous pouvez vous connecter à votre machine virtuelle avec des clés SSH en utilisant :

* Une clé privée que vous entrez manuellement
* Un fichier qui contient les informations relatives à la clé privée

La clé privée SSH doit être dans un format qui commence par `"-----BEGIN RSA PRIVATE KEY-----"` et se termine par `"-----END RSA PRIVATE KEY-----"`.

## <a name="prerequisites"></a>Prérequis

Assurez-vous que vous avez configuré un hôte Azure Bastion pour le réseau virtuel dans lequel réside la machine virtuelle. Pour plus d’informations, consultez [Créer un hôte Azure Bastion](./tutorial-create-host-portal.md). Une fois que le service Bastion est approvisionné et déployé dans votre réseau virtuel, vous pouvez l’utiliser pour vous connecter à toutes les machines virtuelles dans ce réseau virtuel. 

Lorsque vous utilisez Bastion pour vous connecter, ce dernier part du principe que vous utilisez le protocole RDP pour vous connecter à une machine virtuelle Windows, et le protocole SSH pour vous connecter à vos machines virtuelles Linux. Pour plus d’informations sur la connexion à une machine virtuelle Windows, consultez [Se connecter à une machine virtuelle Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Rôles nécessaires

Pour établir une connexion, les rôles suivants sont nécessaires :

* Rôle de lecteur sur la machine virtuelle
* Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle
* Rôle de lecteur sur la ressource Azure Bastion

### <a name="ports"></a>Ports

Pour vous connecter à la machine virtuelle Linux par le biais du protocole SSH, les ports suivants doivent être ouverts sur votre machine virtuelle :

* Port d’entrée : SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Connexion : Utilisation du nom d’utilisateur et du mot de passe

1. Ouvrez le [portail Azure](https://portal.azure.com). Accédez à la machine virtuelle à laquelle vous souhaitez vous connecter, puis cliquez sur **Se connecter** et sélectionnez **Bastion** dans la liste déroulante.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Capture d’écran montrant la vue d’ensemble d’une machine virtuelle dans le Portail Azure avec l’option Se connecter sélectionnée":::
1. Après avoir sélectionné Bastion, cliquez sur **Utiliser Bastion**. Si vous n’avez pas configuré Bastion pour le réseau virtuel, consultez [Configure Bastion (Configurer Bastion)](./quickstart-host-portal.md).
1. Dans la page **Se connecter à l’aide d’Azure Bastion**, entrez le **Nom d’utilisateur** et le **Mot de passe**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/password.png" alt-text="Authentification par mot de passe":::
1. Sélectionnez **Se connecter** pour vous connecter à la machine virtuelle.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Connexion : Entrez manuellement une clé privée

1. Ouvrez le [portail Azure](https://portal.azure.com). Accédez à la machine virtuelle à laquelle vous souhaitez vous connecter, puis cliquez sur **Se connecter** et sélectionnez **Bastion** dans la liste déroulante.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Capture d’écran montrant la vue d’ensemble d’une machine virtuelle dans le Portail Azure avec l’option Se connecter sélectionnée":::
1. Après avoir sélectionné Bastion, cliquez sur **Utiliser Bastion**. Si vous n’avez pas configuré Bastion pour le réseau virtuel, consultez [Configure Bastion (Configurer Bastion)](./quickstart-host-portal.md).
1. Dans la page **Se connecter à l’aide d’Azure Bastion**, entrez le **Nom d’utilisateur** et la **Clé privée SSH**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-private-key.png" alt-text="Authentification par clé privée SSH":::
1. Entrez votre clé privée dans la zone de texte **Clé privée SSH** (ou collez-la directement).
1. Sélectionnez **Se connecter** pour vous connecter à la machine virtuelle.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Connexion : Utilisation d’un fichier de clé privée

1. Ouvrez le [portail Azure](https://portal.azure.com). Accédez à la machine virtuelle à laquelle vous souhaitez vous connecter, puis cliquez sur **Se connecter** et sélectionnez **Bastion** dans la liste déroulante.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Capture d’écran montrant la vue d’ensemble d’une machine virtuelle dans le Portail Azure avec l’option Se connecter sélectionnée":::
1. Après avoir sélectionné Bastion, cliquez sur **Utiliser Bastion**. Si vous n’avez pas configuré Bastion pour le réseau virtuel, consultez [Configure Bastion (Configurer Bastion)](./quickstart-host-portal.md).
1. Dans la page **Se connecter à l’aide d’Azure Bastion**, entrez le **Nom d’utilisateur** et la **Clé privée SSH du fichier local**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/private-key-file.png" alt-text="Fichier de clé privée SSH":::

1. Recherchez le fichier, puis sélectionnez **Ouvrir**.
1. Sélectionnez **Se connecter** pour vous connecter à la machine virtuelle. Une fois que vous avez cliqué sur Se connecter, le protocole SSH vers cette machine virtuelle s’ouvre directement dans le portail Azure. La connexion s’effectue au moyen du protocole HTML5 sur le port 443 du service Bastion, sur l’adresse IP privée de votre machine virtuelle.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>Connexion : utilisation d’une clé privée stockée dans Azure Key Vault

>[!NOTE]
>La mise à jour du portail pour cette fonctionnalité est en cours de déploiement dans les régions.
>

1. Ouvrez le [portail Azure](https://portal.azure.com). Accédez à la machine virtuelle à laquelle vous souhaitez vous connecter, puis cliquez sur **Se connecter** et sélectionnez **Bastion** dans la liste déroulante.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Capture d’écran montrant la vue d’ensemble d’une machine virtuelle dans le Portail Azure avec l’option Se connecter sélectionnée":::
1. Après avoir sélectionné Bastion, cliquez sur **Utiliser Bastion**. Si vous n’avez pas configuré Bastion pour le réseau virtuel, consultez [Configure Bastion (Configurer Bastion)](./quickstart-host-portal.md).
1. Dans la page **Se connecter à l’aide d’Azure Bastion**, entrez le **Nom d’utilisateur** et sélectionnez **Clé privée SSH d’Azure Key Vault**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-key-vault.png" alt-text="Clé privée SSH d’Azure Key Vault":::
1. Sélectionnez la liste déroulante **Azure Key Vault**, puis la ressource dans laquelle vous avez stocké votre clé privée SSH. Si vous n’avez pas configuré de ressource Azure Key Vault, consultez [Création d’un coffre de clés](../key-vault/general/quick-create-portal.md), puis stockez votre clé privée SSH comme valeur d’un nouveau secret Key Vault.

   :::image type="content" source="./media/bastion-connect-vm-ssh/key-vault.png" alt-text="Azure Key Vault":::

   Assurez-vous que vous disposez des accès **List** et **Get** aux secrets stockés dans la ressource Key Vault. Pour affecter et modifier des stratégies d’accès pour votre ressource Key Vault, consultez [Attribuer une stratégie d’accès Key Vault](../key-vault/general/assign-access-policy-portal.md).
1. Sélectionnez la liste déroulante **Secret Azure Key Vault**, puis le secret Key Vault contenant la valeur de votre clé privée SSH.
1. Sélectionnez **Se connecter** pour vous connecter à la machine virtuelle. Une fois que vous avez cliqué sur **Se connecter**, le protocole SSH vers cette machine virtuelle s’ouvre directement dans le portail Azure. La connexion s’effectue au moyen du protocole HTML5 sur le port 443 du service Bastion, sur l’adresse IP privée de votre machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Bastion, consultez les [Questions fréquentes (FAQ) sur Bastion](bastion-faq.md). 
