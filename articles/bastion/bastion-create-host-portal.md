---
title: 'Création d’un hôte Azure Bastion : portail'
description: Dans cet article, découvrez comment créer un hôte Azure Bastion avec le portail.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 04/24/2020
ms.author: cherylmc
ms.openlocfilehash: d18d520419e77a225431d9c2a395f62411656537
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84744270"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Création d’un hôte Azure Bastion avec le portail

Dans cet article, découvrez comment créer un hôte Azure Bastion à l’aide du portail Azure. Une fois que vous avez provisionné le service Azure Bastion dans votre réseau virtuel, l’expérience fluide RDP/SSH est disponible pour toutes les machines virtuelles du même réseau virtuel. Le déploiement Azure Bastion est effectué par réseau virtuel et non par abonnement/compte ou machine virtuelle.

Vous pouvez créer une ressource d’hôte bastion dans le portail en spécifiant tous les paramètres manuellement ou en utilisant les paramètres correspondant à une machine virtuelle existante. Pour créer un hôte bastion à l’aide des paramètres de machine virtuelle, consultez l’article de [démarrage rapide](quickstart-host-portal.md). Si vous le souhaitez, vous pouvez utiliser [Azure PowerShell](bastion-create-host-powershell.md) pour créer un hôte Azure Bastion.

## <a name="before-you-begin"></a>Avant de commencer

Azure Bastion est disponible dans les régions publiques Azure suivantes :

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Créer un hôte Bastion

Cette section vous permet de créer une ressource Azure Bastion à partir du portail Azure.

1. Dans le menu du [Portail Azure](https://portal.azure.com) ou dans la page **Accueil**, sélectionnez **Créer une ressource**.

1. Dans la page **Nouveau**, dans le champ *Rechercher dans la Place de marché*, tapez **Bastion**, puis cliquez sur **Entrée** pour obtenir les résultats de recherche.

1. Dans les résultats, cliquez sur **Bastion**. Assurez-vous que l’éditeur est *Microsoft* et la catégorie *Mise en réseau*.

1. Dans la page **Bastion** , cliquez sur **Créer** pour ouvrir la page **Créer un bastion**.

1. Dans la page **Create a bastion (Créer un bastion)** , configurez une nouvelle ressource Bastion. Spécifiez les paramètres de configuration de votre ressource Bastion.

    ![Créer un bastion](./media/bastion-create-host-portal/settings.png)

    * **Abonnement**: L’abonnement Azure que vous souhaitez utiliser pour créer une ressource Bastion.
    * **Groupe de ressources** : Le groupe de ressources Azure dans lequel la nouvelle ressource Bastion est créée. Si vous n’avez pas de groupe de ressources, vous pouvez en créer un.
    * **Name** : Nom de la nouvelle ressource Bastion
    * **Région** : Région publique Azure dans laquelle est créée la ressource.
    * **Réseau virtuel** : Réseau virtuel dans lequel la ressource Bastion est créée. Vous pouvez créer un réseau virtuel dans le portail au cours de ce processus ou utiliser un réseau virtuel existant. Si vous utilisez un réseau virtuel existant, vérifiez que ce dernier a suffisamment d’espace d’adresse disponible pour prendre en compte les exigences du sous-réseau Bastion.
    * **Sous-réseau** : Sous-réseau dans votre réseau virtuel sur lequel le nouvel hôte bastion sera déployé. Le sous-réseau sera dédié à l’hôte bastion et doit être nommé **AzureBastionSubnet**. Ce sous-réseau doit être au moins égal à /27 ou supérieur.
    
       **AzureBastionSubnet** ne prend pas en charge les [itinéraires définis par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md#custom-routes), mais prend en charge les [groupes de sécurité réseau](bastion-nsg.md).
    * **Adresse IP publique** : Adresse IP publique de la ressource Bastion où RDP/SSH est accessible (sur le port 443). Créez une adresse IP publique ou utilisez-en une existante. L’adresse IP publique doit être située dans la même région que la ressource Bastion que vous créez.
    * **Nom de l’adresse IP publique** : Nom de la ressource de l’adresse IP publique.
    * **Référence (SKU) d'adresse IP publique** : Par défaut, ce paramètre est prérempli avec la valeur **Standard**. Azure Bastion utilise/prend en charge uniquement la référence (SKU) d’adresse IP publique standard.
    * **Attribution** : Par défaut, ce paramètre est prérempli avec la valeur **Statique**.

1. Lorsque vous avez terminé de spécifier les paramètres, cliquez sur **Review + Create (Vérifier + créer)** . Cela valide les valeurs. Une fois que la validation a réussi, vous pouvez commencer le processus de création.
1. Dans la page **Créer un bastion**, cliquez sur **Créer**.
1. Un message vous informe que votre déploiement est en cours. L’état s’affiche sur cette page à mesure que les ressources sont créées. Il faut environ 5 minutes pour que la ressource Bastion soit créée et déployée.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations, lisez les [questions fréquentes (FAQ) sur Bastion](bastion-faq.md).

* Pour utiliser des groupes de sécurité réseau avec le sous-réseau Azure Bastion, consultez [Utiliser des groupes de sécurité réseau](bastion-nsg.md).
