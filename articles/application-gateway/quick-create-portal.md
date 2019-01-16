---
title: Démarrage rapide - Diriger le trafic web avec Azure Application Gateway - Portail Azure | Microsoft Docs
description: Découvrez comment utiliser le portail Azure pour créer une passerelle d’application Azure qui dirige le trafic web vers les machines virtuelles d’un pool backend.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 16e23f77509d2402f765981b39a30e08a2309f68
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156525"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Démarrage rapide : Diriger le trafic web avec Azure Application Gateway - Portail Azure

Ce guide de démarrage rapide vous montre comment utiliser le portail Azure pour créer rapidement la passerelle d’application avec deux machines virtuelles dans son pool backend. Vous la testez ensuite pour vérifier qu’elle fonctionne correctement. Avec Azure Application Gateway, vous dirigez le trafic web de votre application vers des ressources spécifiques en affectant des écouteurs à des ports, en créant des règles et en ajoutant des ressources à un pool de back-ends.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte Azure.

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

Azure a besoin d’un réseau virtuel pour communiquer avec les différentes ressources que vous créez. Vous créez deux sous-réseaux dans cet exemple : un pour la passerelle d’application et un autre pour les serveurs back-end. Vous pouvez créer un réseau virtuel en même temps que la passerelle d’application.

1. Sélectionnez **Créer une ressource** dans le menu de gauche du portail Azure. La fenêtre **Nouvelle** apparaît.

2. Sélectionnez **Mise en réseau**, puis sélectionnez **Application Gateway** dans la liste **Sélection**.

### <a name="basics-page"></a>Page De base

1. Dans la page **De base**, entrez ces valeurs pour les paramètres de passerelle d’application suivants :

    - **Nom** : entrez *myAppGateway* comme nom de passerelle d’application.
    - **Groupe de ressources** : sélectionnez **myResourceGroupAG** comme nom de groupe de ressources. Si ce groupe n’existe pas encore, sélectionnez **Créer** pour le créer.

    ![Créer une nouvelle passerelle d’application](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. Acceptez les valeurs par défaut des autres paramètres, puis sélectionnez **OK**.

### <a name="settings-page"></a>Page Paramètres

1. Dans la page **Paramètres**, sous **Configuration du sous-réseau**, sélectionnez **Choisir un réseau virtuel**.

2. Dans la page **Choisir un réseau virtuel**, sélectionnez **Créer**, puis entrez les valeurs des paramètres de réseau virtuel suivants :

    - **Nom** : entrez *myVNet* comme nom de réseau virtuel.

    - **Espace d’adressage** : entrez *10.0.0.0/16* pour l’espace d’adressage du réseau virtuel.

    - **Nom du sous-réseau** : entrez *myAGSubnet* comme nom de sous-réseau.<br>Le sous-réseau de passerelle d’application peut contenir uniquement des passerelles d’application. Aucune autre ressource n’est autorisée.

    - **Plage d’adresses de sous-réseau** : entrez *10.0.0.0/24* pour la plage d’adresses du sous-réseau.

    ![Création d’un réseau virtuel](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

3. Sélectionnez **OK** pour revenir à la page **Paramètres**.

4. Sous **Configuration d’adresse IP frontale**, vérifiez que **Type d’adresse IP** a la valeur **Public**. Sous **Adresse IP publique**, vérifiez que l’option **Créer** est sélectionnée. 

5. Entrez *myAGPublicIPAddress* comme nom d’adresse IP publique. 

6. Acceptez les valeurs par défaut des autres paramètres, puis sélectionnez **OK**.

### <a name="summary-page"></a>Page de résumé

Passez en revue les paramètres dans la page **Résumé**, puis sélectionnez **OK** pour créer le réseau virtuel, l’adresse IP publique et la passerelle d’application. La création de la passerelle d’application par Azure peut prendre plusieurs minutes. Patientez jusqu’à ce que le déploiement soit terminé avant de passer à la section suivante.

## <a name="add-a-subnet"></a>Ajouter un sous-réseau

Ajoutez un sous-réseau au réseau virtuel que vous avez créé en suivant ces étapes :

1. Sélectionnez **Toutes les ressources** dans le menu de gauche du portail Azure, entrez *myVNet* dans la zone de recherche, puis sélectionnez **myVNet** dans les résultats de la recherche.

2. Sélectionnez **Sous-réseaux** dans le menu de gauche, puis sélectionnez **+ Sous-réseau**. 

    ![Créer un sous-réseau](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Dans la page **Ajouter un sous-réseau**, entrez *myBackendSubnet* comme **Nom** de sous-réseau, puis sélectionnez **OK**.

## <a name="create-backend-servers"></a>Créer des serveurs back-end

Dans cet exemple, vous créez deux machines virtuelles qu’Azure va utiliser comme serveurs back-end pour la passerelle d’application. Vous installez également IIS sur les machines virtuelles pour vérifier qu’Azure a bien créé la passerelle d’application.

### <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

1. Dans le portail Azure, sélectionnez **Créer une ressource**. La fenêtre **Nouvelle** apparaît.

2. Sélectionnez **Compute**, puis sélectionnez **Windows Server 2016 Datacenter** dans la liste **Sélection**. La page **Créer une machine virtuelle** s’affiche.

3. Sous l’onglet **De base**, entrez ces valeurs pour les paramètres de machine virtuelle suivants :

    - **Groupe de ressources** : sélectionnez **myResourceGroupAG** comme nom de groupe de ressources.
    - **Nom de la machine virtuelle** : entrez *myVM* comme nom de machine virtuelle.
    - **Nom d’utilisateur** : entrez *azureuser* comme nom d’utilisateur administrateur.
    - **Mot de passe** : entrez *Azure123456!* comme mot de passe d’administrateur.

4. Acceptez les autres valeurs par défaut, puis sélectionnez **Suivant : Disques**.  

5. Acceptez les valeurs par défaut sous l’onglet **Disques**, puis sélectionnez **Suivant : Mise en réseau**.

6. Sous l’onglet **Mise en réseau**, vérifiez que **myVNet** est sélectionné comme **Réseau virtuel** et que **Sous-réseau** est défini sur  **myBackendSubnet**. Acceptez les autres valeurs par défaut, puis sélectionnez **Suivant : Gestion**.

8. Sous l’onglet **Gestion**, définissez **Diagnostics de démarrage** sur **Désactivé**. Acceptez les autres valeurs par défaut, puis sélectionnez **Vérifier + créer**.

9. Sous l’onglet **Vérifier + créer**, passez en revue les paramètres, corrigez les éventuelles erreurs de validation et sélectionnez **Créer**.

10. Attendez la fin de la création de la machine virtuelle avant de continuer.

### <a name="install-iis"></a>Installer IIS

1. Ouvrez [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Pour ce faire, sélectionnez **Cloud Shell** dans la barre de navigation supérieure du portail Azure, puis sélectionnez **PowerShell** dans la liste déroulante. 

    ![Installer l’extension personnalisée](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Exécutez la commande suivante pour installer IIS sur la machine virtuelle : 

    ```azurepowershell-interactive
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Créez une deuxième machine virtuelle et installez IIS en suivant les mêmes étapes que vous avez effectuées précédemment. Utilisez *myVM2* comme nom de machine virtuelle et comme paramètre **VMName** de l’applet de commande **Set-AzureRmVMExtension**.

### <a name="add-backend-servers"></a>Ajouter des serveurs principaux

1. Sélectionnez **Toutes les ressources**, puis **myAppGateway**.

2. Sélectionnez **Pools principaux** dans le menu de gauche. Azure a automatiquement créé un pool par défaut, appelé **appGatewayBackendPool**, quand vous avez créé la passerelle d’application. 

3. Sélectionnez **appGatewayBackendPool**.

4. Sous **Cibles**, sélectionnez **Machine virtuelle** dans la liste déroulante.

5. Sous **MACHINE VIRTUELLE** et **INTERFACES RÉSEAU**, sélectionnez les machines virtuelles **myVM** et **myVM2** ainsi que leurs interfaces réseau associées dans les listes déroulantes.

    ![Ajouter des serveurs principaux](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Sélectionnez **Enregistrer**.

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

1. Recherchez l’adresse IP publique de la passerelle d’application dans la page **Vue d’ensemble** correspondante.

    ![Enregistrer l’adresse IP publique de la passerelle d’application](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

    Vous pouvez aussi sélectionner **Toutes les ressources**, entrer *myAGPublicIPAddress* dans la zone de recherche, puis sélectionner l’adresse IP publique dans les résultats de la recherche. Azure affiche l’adresse IP publique dans la page **Vue d’ensemble**.

2. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur.

    ![Tester la passerelle d’application](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin des ressources que vous avez créées avec la passerelle d’application, supprimez le groupe de ressources. En supprimant le groupe de ressources, vous supprimez aussi la passerelle d’application et toutes ses ressources associées. 

Pour supprimer le groupe de ressources :
1. Dans le menu de gauche du portail Azure, sélectionnez **Groupes de ressources**.
2. Dans la page **Groupes de ressources**, recherchez **myResourceGroupAG** dans la liste, puis sélectionnez ce groupe de ressources.
3. Dans la page **Groupe de ressources**, sélectionnez **Supprimer le groupe de ressources**.
4. Entrez *myResourceGroupAG* dans **TAPER LE NOM DU GROUPE DE RESSOURCES**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer le trafic web avec une passerelle d’application à l’aide d’Azure CLI](./tutorial-manage-web-traffic-cli.md)
