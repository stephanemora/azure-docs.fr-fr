---
title: 'Tutoriel : Créer un hôte Azure Bastion : Machine virtuelle Windows : portail'
description: Découvrez comment créer un hôte Azure Bastion et se connecter à une machine virtuelle Windows.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: 2479b736809bbbe0dc2810b168dada6536ab8f91
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110540485"
---
# <a name="tutorial-configure-bastion-and-connect-to-a-windows-vm-through-a-browser"></a>Tutoriel : Configurer Bastion et se connecter à une machine virtuelle Windows à partir d’un navigateur

Ce tutoriel vous explique comment vous connecter à une machine virtuelle via votre navigateur à l’aide d’Azure Bastion et du portail Azure. Dans le portail Azure, vous déployez Bastion sur votre réseau virtuel. Après avoir déployé Bastion, vous vous connectez à une machine virtuelle via son adresse IP privée à l’aide du portail Azure. Votre machine virtuelle n’a pas besoin d’adresse IP publique ni d’un logiciel spécial. Une fois le service approvisionné, l’expérience RDP/SSH est disponible pour toutes les machines virtuelles du même réseau virtuel. Pour plus d’informations sur Azure Bastion, consultez [Présentation d’Azure Bastion](bastion-overview.md).

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un hôte bastion pour votre réseau virtuel.
> * Supprimer l’adresse IP publique d’une machine virtuelle.
> * Se connecter à une machine virtuelle Windows.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Un réseau virtuel.
* Une machine virtuelle Windows hébergée sur le réseau virtuel.
* Les rôles requis suivants :
  * Rôle de lecteur sur la machine virtuelle.
  * Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle.
  * Rôle de lecteur sur la ressource Azure Bastion.

* Ports : Pour vous connecter à la machine virtuelle Windows, les ports suivants doivent être ouverts sur votre machine virtuelle Windows :
  * Ports d’entrée : RDP (3389)

 >[!NOTE]
 >L'utilisation d'Azure Bastion avec Azure DNS Private Zones n'est pas prise en charge pour le moment. Avant de commencer, assurez-vous que le réseau virtuel sur lequel vous prévoyez de déployer votre ressource Bastion n'est pas lié à une zone DNS privée.
 >

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Créer un hôte Bastion

Cette section vous aide à créer l’objet bastion dans votre réseau virtuel. Cela est nécessaire pour créer une connexion sécurisée à une machine virtuelle dans le réseau virtuel.

1. Dans la page d’**accueil**, sélectionnez **+ Créer une ressource**.
1. Dans la page **Nouveau**, dans la zone de recherche, tapez **Bastion**, puis sélectionnez **Entrée** pour obtenir les résultats de la recherche. Dans le résultat pour **Bastion**, vérifiez que l’éditeur est Microsoft.
1. Sélectionnez **Create** (Créer).
1. Dans la page **Create a bastion** (Créer un bastion), configurez une nouvelle ressource Bastion.

   :::image type="content" source="./media/tutorial-create-host-portal/create.png" alt-text="Capture d’écran de la page Créer un portail Bastion." lightbox="./media/tutorial-create-host-portal/create-expand.png":::

    * **Abonnement**: L’abonnement Azure que vous souhaitez utiliser pour créer une ressource Bastion.
    * **Groupe de ressources** : Le groupe de ressources Azure dans lequel la nouvelle ressource Bastion est créée. Si vous n’avez pas de groupe de ressources, vous pouvez en créer un.
    * **Nom** : Nom de la nouvelle ressource Bastion.
    * **Région** : Région publique Azure dans laquelle est créée la ressource.
    * **Réseau virtuel** : Réseau virtuel dans lequel la ressource Bastion est créée. Vous pouvez créer un réseau virtuel dans le portail au cours de ce processus ou utiliser un réseau virtuel existant. Si vous utilisez un réseau virtuel existant, vérifiez que ce dernier a suffisamment d’espace d’adresse disponible pour prendre en compte les exigences du sous-réseau Bastion. Si vous ne voyez pas votre réseau virtuel dans la liste déroulante, vérifiez que vous avez sélectionné le groupe de ressources approprié.
    * **Sous-réseau** : Une fois que vous avez créé ou sélectionné un réseau virtuel, le champ sous-réseau s’affiche. Sous-réseau dans votre réseau virtuel sur lequel le nouvel hôte bastion sera déployé. Le sous-réseau sera dédié à l’hôte Bastion. Sélectionnez **Gérer la configuration du sous-réseau** et créez le sous-réseau Azure Bastion. Sélectionnez **+ Sous-réseau** et créez un sous-réseau à l’aide des instructions suivantes :

         * Le sous-réseau doit être nommé **AzureBastionSubnet**.
         * Le sous-réseau doit être au moins égal à /27 ou supérieur.

      Vous n’avez pas besoin de renseigner des champs supplémentaires. Sélectionnez **OK** puis, en haut de la page, sélectionnez **Create a Bastion** (Créer un bastion) pour revenir à la page de configuration du bastion.
    * **Adresse IP publique** : adresse IP publique de la ressource Bastion sur laquelle accéder à RDP/SSH (sur le port 443). Créez une adresse IP publique. L’adresse IP publique doit être située dans la même région que la ressource Bastion que vous créez. Cette adresse IP n’a rien à voir avec les machines virtuelles auxquelles vous voulez vous connecter. Il s’agit de l’adresse IP publique de la ressource d’hôte bastion.
    * **Nom de l’adresse IP publique** : Nom de la ressource de l’adresse IP publique. Pour ce tutoriel, vous pouvez laisser la valeur par défaut.
    * **Référence (SKU) d'adresse IP publique** : Par défaut, ce paramètre est prérempli avec la valeur **Standard**. Azure Bastion utilise/prend en charge uniquement la référence SKU d’IP publique Standard.
    * **Attribution** : Par défaut, ce paramètre est prérempli avec la valeur **Statique**.

1. Lorsque vous avez terminé de spécifier les paramètres, sélectionnez **Vérifier + créer**. Cela valide les valeurs. Une fois la validation réussie, vous pouvez créer la ressource Bastion.

   :::image type="content" source="./media/tutorial-create-host-portal/validation.png" alt-text="Capture d’écran de la page de validation.":::
1. Passez vos paramètres en revue. Ensuite, en bas de la page, sélectionnez **Créer**.
1. Un message vous informe que votre déploiement est en cours. L’état s’affiche sur cette page à mesure que les ressources sont créées. Il faut environ 5 minutes pour que la ressource Bastion soit créée et déployée.

## <a name="remove-a-vm-public-ip-address"></a>Supprimer une adresse IP publique VM

[!INCLUDE [Remove a public IP address from a VM](../../includes/bastion-remove-ip.md)]

## <a name="connect-to-a-vm"></a>Se connecter à une machine virtuelle

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin d’utiliser cette application, supprimez vos ressources en effectuant les étapes suivantes :

1. Entrez le nom de votre groupe de ressources dans la zone **Recherche** en haut du portail. Quand vous voyez votre groupe de ressources dans les résultats de la recherche, sélectionnez-le.
1. Sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources dans **TAPER LE NOM DU GROUPE DE RESSOURCES :** , puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un hôte bastion et l’avez associé à un réseau virtuel. Vous avez ensuite supprimé l’adresse IP publique d’une machine virtuelle et vous y êtes connecté. Vous pouvez choisir d’utiliser des groupes de sécurité réseau avec votre sous-réseau Azure Bastion. Pour cela, consultez :

> [!div class="nextstepaction"]
> [Utiliser des groupes de sécurité réseau](bastion-nsg.md)
