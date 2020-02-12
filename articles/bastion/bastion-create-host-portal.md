---
title: Créer un hôte Azure Bastion | Microsoft Docs
description: Dans cet article, découvrez comment créer un hôte Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f907dcb4427fd07a2c212e5de91ccce5e8198960
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990417"
---
# <a name="create-an-azure-bastion-host"></a>Créer un hôte Azure Bastion

Dans cet article, découvrez comment créer un hôte Azure Bastion à l’aide du portail Azure. Une fois que vous avez provisionné le service Azure Bastion dans votre réseau virtuel, l’expérience fluide RDP/SSH est disponible pour toutes les machines virtuelles du même réseau virtuel. Le déploiement Azure Bastion est effectué par réseau virtuel et non par abonnement/compte ou machine virtuelle.

Vous pouvez créer une ressource d’hôte bastion dans le portail en spécifiant tous les paramètres manuellement ou en utilisant les paramètres correspondant à une machine virtuelle existante. Éventuellement, vous pouvez utiliser [Azure PowerShell](bastion-create-host-powershell.md) pour créer un hôte Azure Bastion.

## <a name="before-you-begin"></a>Avant de commencer

Azure Bastion est disponible dans les régions publiques Azure suivantes :

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Créer un hôte bastion en spécifiant des paramètres

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
    * **Sous-réseau** : Sous-réseau dans votre réseau virtuel auquel la nouvelle ressource d’hôte Bastion est déployée. Vous devez créer un sous-réseau à l’aide de la valeur de nom **AzureBastionSubnet**. Cette valeur permet à Azure de savoir dans quel sous-réseau déployer les ressources Bastion. Ceci est différent d’un sous-réseau de passerelle. Vous devez utiliser un sous-réseau de /27 ou plus (/27, /26, etc.).
    
       Créez le sous-réseau **AzureBastionSubnet** sans tables de routage ou délégations. Si vous utilisez des groupes de sécurité réseau sur le sous-réseau **AzureBastionSubnet**, reportez-vous à l’article [Utiliser des groupes de sécurité réseau](bastion-nsg.md).
    * **Adresse IP publique** : Adresse IP publique de la ressource Bastion où RDP/SSH est accessible (sur le port 443). Créez une adresse IP publique ou utilisez-en une existante. L’adresse IP publique doit être située dans la même région que la ressource Bastion que vous créez.
    * **Nom de l’adresse IP publique** : Nom de la ressource de l’adresse IP publique.
    * **Référence (SKU) d'adresse IP publique** : Par défaut, ce paramètre est prérempli avec la valeur **Standard**. Azure Bastion utilise/prend en charge uniquement la référence (SKU) d’adresse IP publique standard.
    * **Attribution** : Par défaut, ce paramètre est prérempli avec la valeur **Statique**.

1. Lorsque vous avez terminé de spécifier les paramètres, cliquez sur **Review + Create (Vérifier + créer)** . Cela valide les valeurs. Une fois que la validation a réussi, vous pouvez commencer le processus de création.
1. Dans la page **Créer un bastion**, cliquez sur **Créer**.
1. Un message vous informe que votre déploiement est en cours. L’état s’affiche sur cette page à mesure que les ressources sont créées. Il faut environ 5 minutes pour que la ressource Bastion soit créée et déployée.

## <a name="createvmset"></a>Créer un hôte bastion en utilisant des paramètres de machine virtuelle

Si vous créez un hôte bastion dans le portail à l’aide d’une machine virtuelle existante, différents paramètres sont automatiquement définis par défaut de sorte à correspondre à votre machine virtuelle et/ou réseau virtuel.

1. Ouvrez le [portail Azure](https://portal.azure.com). Accédez à votre machine virtuelle, puis cliquez sur **Se connecter**.

   ![Connexion à une machine virtuelle](./media/bastion-create-host-portal/vmsettings.png)
1. Dans le volet droit, cliquez sur **Bastion**, puis **Use Bastion (Utiliser Bastion)** .

   ![Bastion](./media/bastion-create-host-portal/vmbastion.png)
1. Dans la page Bastion, renseignez les champs suivants :

   * **Name** : Nom de l’hôte bastion que vous souhaitez créer.
   * **Sous-réseau** : Sous-réseau au sein de votre réseau virtuel dans lequel la ressource Bastion est déployée. Le sous-réseau doit être créé avec le nom **AzureBastionSubnet**. Cela permet à Azure de savoir dans quel sous-réseau déployer la ressource Bastion. Ceci est différent d’un sous-réseau de passerelle. Vous devez utiliser un sous-réseau de /27 ou plus (/27, /26, etc.). Créez le sous-réseau sans groupes de sécurité réseau, tables de routage ni délégations. Si vous choisissez plus tard d’utiliser des groupes de sécurité réseau sur **AzureBastionSubnet**, consultez [Utiliser des groupes de sécurité réseau](bastion-nsg.md).
   
     Cliquez sur **Gérer la configuration du sous-réseau** pour créer le sous-réseau **AzureBastionSubnet**.  Cliquez sur **Créer** pour créer le sous-réseau, puis continuez avec les paramètres suivants.
   * **Adresse IP publique** : Adresse IP publique de la ressource Bastion où RDP/SSH est accessible (sur le port 443). Créez une adresse IP publique ou utilisez-en une existante. L’adresse IP publique doit être située dans la même région que la ressource Bastion que vous créez.
   * **Nom de l’adresse IP publique** : Nom de la ressource de l’adresse IP publique.
1. Dans l’écran de validation, cliquez sur **Créer**. Patientez pendant environ 5 minutes pour que la ressource Bastion soit créée et déployée.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations, lisez les [questions fréquentes (FAQ) sur Bastion](bastion-faq.md).

* Pour utiliser des groupes de sécurité réseau avec le sous-réseau Azure Bastion, consultez [Utiliser des groupes de sécurité réseau](bastion-nsg.md).