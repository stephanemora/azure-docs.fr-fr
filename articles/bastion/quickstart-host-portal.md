---
title: 'Démarrage rapide : Créer un hôte Bastion à partir d’une machine virtuelle et se connecter via une adresse IP privée'
titleSuffix: Azure Bastion
description: Cet article de démarrage rapide explique comment créer un hôte Azure Bastion à partir d’une machine virtuelle, et se connecter de manière sécurisée à l’aide d’une adresse IP privée.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 6f451e7b115c00bc7b2cf350e00b9f704ab1d29f
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019050"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Démarrage rapide : Se connecter à une machine virtuelle à l’aide d’une adresse IP privée et d’Azure Bastion

Cet article de démarrage rapide vous explique comment vous connecter à une machine virtuelle via votre navigateur à l’aide d’Azure Bastion et du portail Azure. Dans le portail Azure, à partir de votre machine virtuelle Azure, vous pouvez déployer Bastion sur votre réseau virtuel. Après avoir déployé Bastion, vous pouvez vous connecter à la machine virtuelle via son adresse IP privée à l’aide du portail Azure. Votre machine virtuelle n’a pas besoin d’adresse IP publique ou d’un logiciel spécial. L’un des avantages de la création d’un hôte Bastion pour votre réseau virtuel directement à partir de votre machine virtuelle est que la plupart des paramètres sont préremplis pour vous.

Une fois le service approvisionné, l’expérience RDP/SSH est disponible pour toutes les machines virtuelles du même réseau virtuel. Pour plus d’informations sur Azure Bastion, consultez [Présentation d’Azure Bastion](bastion-overview.md).

## <a name="prerequisites"></a><a name="prereq"></a>Configuration requise

* Un réseau virtuel.
* Une machine virtuelle Windows se trouvant sur le réseau virtuel.
* Les rôles requis suivants :
  * Rôle de lecteur sur la machine virtuelle.
  * Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle.

* Ports : Pour que vous puissiez vous connecter à la machine virtuelle, les ports suivants doivent être ouverts sur celle-ci :
  * Ports d’entrée : RDP (3389)

### <a name="example-values"></a>Exemples de valeurs

|**Nom** | **Valeur** |
| --- | --- |
| Nom |  TestVNet1-bastion |
| Réseau virtuel |  TestVNet1 (en fonction de la machine virtuelle) |
| + Nom du sous-réseau | AzureBastionSubnet |
| Adresses AzureBastionSubnet |  10.1.254.0/27 |
| Adresse IP publique |  Création |
| Nom de l’adresse IP publique | VNet1BastionPIP  |
| Référence SKU d’adresse IP publique |  standard  |
| Affectation  | statique |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Créer un hôte Bastion

Quand vous créez un hôte bastion dans le portail Azure à l’aide d’une machine virtuelle existante, différents paramètres sont automatiquement définis par défaut en fonction de votre machine virtuelle et/ou réseau virtuel.

1. Ouvrez le [portail Azure](https://portal.azure.com). Accédez à votre machine virtuelle, puis sélectionnez **Se connecter**.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="Paramètres de machine virtuelle" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. Dans la liste déroulante, sélectionnez **Bastion**.
1. Dans la page **TestVM | Se connecter**, sélectionnez **Utiliser Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="Paramètres de machine virtuelle" border="false":::

1. Dans la page **Bastion**, renseignez les champs suivants :

   * **Nom** : nommez l’hôte bastion.
   * **Sous-réseau** : Sous-réseau au sein de votre réseau virtuel dans lequel la ressource Bastion est déployée. Le sous-réseau doit être créé avec le nom **AzureBastionSubnet**. Le nom permet à Azure de savoir dans quel sous-réseau déployer la ressource Bastion. C’est différent d’un sous-réseau de passerelle. Utilisez un sous-réseau de /27 ou plus (/27, /26, /25, etc.).
   
      * Sélectionnez **Gérer la configuration du sous-réseau**.
      * Sélectionnez **AzureBastionSubnet**.
      * Si nécessaire, ajustez la plage d’adresses en notation CIDR. Par exemple, 10.1.254.0/27.
      * N’ajustez pas les autres paramètres. Sélectionnez **OK** pour accepter et enregistrer les modifications apportées au sous-réseau, ou sélectionnez **x** en haut de la page si vous ne souhaitez pas apporter de modifications.
1. Cliquez sur le bouton Précédent de votre navigateur pour revenir à la page **Bastion** et continuer à spécifier des valeurs.
   * **Nom de l’adresse IP publique** : Nom de la ressource de l’adresse IP publique.
   * **Adresse IP publique** : Adresse IP publique de la ressource Bastion où RDP/SSH sera accessible (sur le port 443). Créez une adresse IP publique.
1. Sélectionnez **Créer** pour créer l’hôte bastion. Azure valide vos paramètres, puis crée l’hôte. La création et le déploiement de l’hôte et de ses ressources prennent environ 5 minutes.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="Paramètres de machine virtuelle":::

## <a name="connect"></a><a name="connect"></a>Connexion

Une fois Bastion déployé sur le réseau virtuel, l’écran devient la page de connexion.

1. Entrez le nom d’utilisateur et le mot de passe pour votre machine virtuelle. puis **Connecter**.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="Paramètres de machine virtuelle":::
1. La connexion RDP à cette machine virtuelle avec Bastion s’ouvrira directement dans le portail Azure (en HTML5) via le port 443 et le service Bastion.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="Paramètres de machine virtuelle":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous avez fini d’utiliser le réseau virtuel et les machines virtuelles, supprimez le groupe de ressources et toutes les ressources qu’il contient :

1. Entrez le nom de votre groupe de ressources dans la zone **Rechercher** en haut du portail et sélectionnez-le dans les résultats de la recherche.

1. Sélectionnez **Supprimer le groupe de ressources**.

1. Entrez votre groupe de ressources dans **TAPER LE NOM DU GROUPE DE RESSOURCES**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un hôte Bastion pour votre réseau virtuel, puis vous vous êtes connecté à une machine virtuelle en toute sécurité via l’hôte Bastion. Vous pouvez à présent passer à l’étape suivante si vous souhaitez vous connecter à un groupe de machines virtuelles identiques.

> [!div class="nextstepaction"]
> [Se connecter à un groupe de machines virtuelles identiques à l’aide d’Azure Bastion](bastion-connect-vm-scale-set.md)
