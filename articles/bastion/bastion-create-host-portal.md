---
title: Créer un hôte Azure Bastion | Microsoft Docs
description: Dans cet article, découvrez comment créer un hôte Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: cherylmc
ms.openlocfilehash: 881d89170c82731fca3f6977da486eac5be772f8
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466991"
---
# <a name="create-an-azure-bastion-host-preview"></a>Créer un hôte Azure Bastion (préversion)

Dans cet article, découvrez comment créer un hôte Azure Bastion. Une fois que vous avez provisionné le service Azure Bastion dans votre réseau virtuel, l’expérience fluide RDP/SSH est disponible pour toutes vos machines virtuelles sur le même réseau virtuel. Ce déploiement est effectué par réseau virtuel et non par abonnement/compte ou machine virtuelle.

Il existe deux méthodes pour créer une ressource d’hôte Bastion :

* Créez une ressource Bastion à l’aide du portail Azure.
* Créez une ressource Bastion dans le portail Azure à l’aide des paramètres de machine virtuelle existants.

> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Avant de commencer

La préversion publique est limitée aux régions publiques Azure suivantes :

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Créer un hôte Bastion

Cette section vous permet de créer une ressource Azure Bastion à partir du portail Azure.

1. À partir de la page d’accueil de la [préversion du portail Azure - Bastion](https://aka.ms/BastionHost), cliquez sur **+ Créer une ressource**. Vérifiez que vous utilisez le lien fourni pour accéder au portail pour cette préversion, pas le portail Azure classique.

1. Dans la page **Nouveau**, dans le champ *Rechercher dans la Place de marché*, tapez **Bastion**, puis cliquez sur **Entrée** pour obtenir les résultats de recherche.

1. À partir des résultats, cliquez sur **Bastion (préversion)** . Assurez-vous que l’éditeur est *Microsoft* et la catégorie *Mise en réseau*.

1. Dans la page **Bastion (préversion)** , cliquez sur **Créer** pour ouvrir la page **Create a bastion (Créer un bastion)** .

1. Dans la page **Create a bastion (Créer un bastion)** , configurez une nouvelle ressource Bastion. Spécifiez les paramètres de configuration de votre ressource Bastion.

    ![Créer un bastion](./media/bastion-create-host-portal/settings.png)

    * **Abonnement**: L’abonnement Azure que vous souhaitez utiliser pour créer une ressource Bastion.
    * **Groupe de ressources** : Le groupe de ressources Azure dans lequel la nouvelle ressource Bastion est créée. Si vous n’avez pas de groupe de ressources, vous pouvez en créer un.
    * **Nom** : Nom de la nouvelle ressource Bastion
    * **Région** : Région publique Azure dans laquelle est créée la ressource.
    * **Réseau virtuel** : Réseau virtuel dans lequel la ressource Bastion est créée. Vous pouvez créer un réseau virtuel dans le portail pendant ce processus, au cas où vous n’avez pas ou ne souhaitez pas utiliser un réseau virtuel existant. Si vous utilisez un réseau virtuel existant, vérifiez que ce dernier a suffisamment d’espace d’adresse disponible pour prendre en compte les exigences du sous-réseau Bastion.
    * **Sous-réseau** : Sous-réseau dans votre réseau virtuel auquel la nouvelle ressource d’hôte Bastion est déployée. Vous devez créer un sous-réseau à l’aide de la valeur de nom **AzureBastionSubnet**. Cette valeur permet à Azure de savoir dans quel sous-réseau déployer les ressources Bastion. Ceci est différent d’un sous-réseau de passerelle. Nous vous recommandons vivement d’utiliser au moins/27 ou un sous-réseau plus vaste (/27, /26 et ainsi de suite). Créez le sous-réseau **AzureBastionSubnet** sans tables de routage ou délégations. Lorsque vous utilisez des groupes de sécurité réseau sur le sous-réseau **AzureBastionSubnet**, consultez la rubrique [Travailler avec des groupes de sécurité réseau](bastion-nsg.md).
    * **Adresse IP publique** : Adresse IP publique de la ressource Bastion où RDP/SSH est accessible (sur le port 443). Créez une adresse IP publique ou utilisez-en une existante. L’adresse IP publique doit être située dans la même région que la ressource Bastion que vous créez.
    * **Nom de l’adresse IP publique** : Nom de la ressource de l’adresse IP publique.
    * **Référence (SKU) d'adresse IP publique** : Défini par défaut sur **Standard**. Azure Bastion utilise/prend en charge uniquement la référence (SKU) d’adresse IP publique standard.
    * **Attribution** : Défini par défaut sur **Statique**.

1. Lorsque vous avez terminé de spécifier les paramètres, cliquez sur **Review + Create (Vérifier + créer)** . Cela valide les valeurs. Une fois que la validation a réussi, vous pouvez commencer le processus de création.
1. Dans la page Créer un bastion, cliquez sur **Créer**.
1. Un message vous informe que votre déploiement est en cours. L’état s’affiche sur cette page à mesure que les ressources sont créées. Il faut environ 5 minutes pour que la ressource Bastion soit créée et déployée.

## <a name="createvmset"></a>Créer un hôte bastion à l’aide des paramètres de machine virtuelle

Si vous créez un hôte bastion dans le portail à l’aide d’une machine virtuelle existante, différents paramètres sont automatiquement définis par défaut sur votre machine virtuelle et/ou réseau virtuel.

1. Dans le [portail Azure - préversion de Bastion](https://aka.ms/BastionHost), accédez à votre machine virtuelle, puis cliquez sur **Connect (Connecter)** .

    ![Connexion à une machine virtuelle](./media/bastion-create-host-portal/vmsettings.png)

1. Dans le volet droit, cliquez sur **Bastion**, puis **Use Bastion (Utiliser Bastion)** .

    ![Bastion](./media/bastion-create-host-portal/vmbastion.png)

1. Dans la page Bastion, renseignez les champs suivants :

    * **Nom** : Nom de l’hôte bastion que vous souhaitez créer.
    * **Sous-réseau** : Sous-réseau au sein de votre réseau virtuel dans lequel la ressource Bastion est déployée. Le sous-réseau doit être créé avec le nom **AzureBastionSubnet**. Cela permet à Azure de savoir dans quel sous-réseau déployer la ressource Bastion. Ceci est différent d’un sous-réseau de passerelle. Cliquez sur **Gérer la configuration du sous-réseau** pour créer le sous-réseau Azure Bastion. Nous vous recommandons vivement d’utiliser au moins/27 ou un sous-réseau plus vaste (/27, /26 et ainsi de suite). Créez le sous-réseau **AzureBastionSubnet** sans groupes de sécurité réseau, tables de routage ni délégations. Cliquez sur **Créer** pour créer le sous-réseau, puis continuez avec les paramètres suivants.

      ![Bastion](./media/bastion-create-host-portal/subnet.png)
      
    * **Adresse IP publique** : Adresse IP publique de la ressource Bastion où RDP/SSH est accessible (sur le port 443). Créez une adresse IP publique ou utilisez-en une existante. L’adresse IP publique doit être située dans la même région que la ressource Bastion que vous créez.
    * **Nom de l’adresse IP publique** : Nom de la ressource de l’adresse IP publique.
1. Dans l’écran de validation, cliquez sur **Créer**. Il faut environ 5 minutes pour que la ressource Bastion soit créée et déployée.

## <a name="next-steps"></a>Étapes suivantes

Lisez les [questions fréquentes sur Bastion](bastion-faq.md).
