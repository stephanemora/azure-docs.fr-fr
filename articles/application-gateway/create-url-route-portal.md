---
title: Tutoriel - Créer une passerelle d’application avec des règles d’acheminement par chemin d’accès URL - Portail Azure
description: Dans ce tutoriel, découvrez comment créer des règles d’acheminement par chemin d’accès URL pour une passerelle d’application et un groupe de machines virtuelles identiques avec le portail Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: victorh
ms.openlocfilehash: 63a1faa79374e72eabfbee4ece454728c3b4cc05
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597599"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Didacticiel : Créer une passerelle d’application avec des règles d’acheminement par chemin d’accès à l’aide du portail Azure

Vous pouvez utiliser le portail Azure pour configurer des [règles d’acheminement par chemin d’accès URL](url-route-overview.md) lors de la création d’une [passerelle d’application](overview.md). Ce didacticiel montre comment créer des pools principaux à l’aide de machines virtuelles. Il permet ensuite de définir des règles d’acheminement qui font en sorte que le trafic web arrive sur les serveurs voulus dans les pools.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une passerelle Application Gateway
> * Créer des machines virtuelles pour les serveurs principaux
> * Créer des pools principaux avec les serveurs principaux
> * Créer un écouteur principal
> * Créer une règle d’acheminement par chemin d’accès

![Exemple d’acheminement d’URL](./media/create-url-route-portal/scenario.png)

Si vous préférez, vous pouvez suivre ce tutoriel en utilisant [Azure CLI](tutorial-url-route-cli.md) ou [Azure PowerShell](tutorial-url-route-powershell.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

1. Sélectionnez **Créer une ressource** dans le menu de gauche du portail Azure. La fenêtre **Nouvelle** apparaît.

2. Sélectionnez **Mise en réseau**, puis sélectionnez **Application Gateway** dans la liste **Sélection**.

### <a name="basics-tab"></a>Onglet Informations de base

1. Sous l’onglet **Informations de base**, entrez ces valeurs pour les paramètres de passerelle d’application suivants :

   - **Groupe de ressources** : sélectionnez **myResourceGroupAG** comme nom de groupe de ressources. Si ce groupe n’existe pas encore, sélectionnez **Créer** pour le créer.
   - **Nom de passerelle d’application** : entrez *myAppGateway* comme nom de passerelle d’application.

     ![Créer une passerelle d’application : Concepts de base](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Azure a besoin d’un réseau virtuel pour communiquer avec les différentes ressources que vous créez. Vous pouvez créer un réseau virtuel ou en utiliser un. Dans cet exemple, vous allez créer un réseau virtuel en même temps que la passerelle d’application. Les instances Application Gateway sont créées dans des sous-réseaux séparés. Vous créez deux sous-réseaux dans cet exemple : un pour la passerelle d’application et un autre pour les serveurs back-end.

    Sous **Configurer le réseau virtuel**, sélectionnez **Créer** pour créer un réseau virtuel. Dans la fenêtre **Créer un réseau virtuel** qui s’ouvre, entrez les valeurs suivantes pour créer le réseau virtuel et deux sous-réseaux :

    - **Nom** : entrez *myVNet* comme nom de réseau virtuel.

    - **Nom de sous-réseau** (sous-réseau Application Gateway) : La grille **Sous-réseaux** affiche un sous-réseau nommé *Par défaut*. Remplacez le nom de ce sous-réseau par *myAGSubnet*.

      Le sous-réseau de passerelle d’application peut contenir uniquement des passerelles d’application. Aucune autre ressource n’est autorisée.

    - **Nom de sous-réseau** (sous-réseau de serveur principal) : Dans la deuxième ligne de la grille **Sous-réseaux**, entrez *myBackendSubnet* dans la colonne **Nom de sous-réseau**.

    - **Plage d’adresses** (sous-réseau de serveur principal) : Dans la deuxième ligne de la grille **Sous-réseaux**, entrez une plage d’adresses qui ne chevauche pas la plage d’adresses de *myAGSubnet*. Par exemple, si la plage d’adresses de *myAGSubnet* est 10.0.0.0/24, entrez *10.0.1.0/24* pour la plage d’adresses de *myBackendSubnet*.

    Sélectionnez **OK** pour fermer la fenêtre **Créer un réseau virtuel** et enregistrez les paramètres du réseau virtuel.

     ![Créer une passerelle d’application : réseau virtuel](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Sous l’onglet **Informations de base**, acceptez les valeurs par défaut des autres paramètres, puis sélectionnez **Suivant : Serveurs frontaux**.

### <a name="frontends-tab"></a>Onglet Front-ends

1. Sous l’onglet **Front-ends**, vérifiez que **Type d’adresse IP de front-end** est défini sur **Publique**. <br>Vous pouvez l’adresse IP frontale pour qu’elle soit publique ou privée conformément à votre cas d’utilisation. Dans cet exemple, vous allez choisir une adresse IP front-end publique.
   > [!NOTE]
   > Pour la référence SKU Application Gateway v2, vous pouvez uniquement choisir la configuration d’une adresse IP front-end **publique**. La configuration d’une adresse IP de front-end privée n’est pas activée pour la référence SKU v2.

2. Choisissez **Créer nouveau** pour **Adresse IP publique**, puis entrez *myAGPublicIPAddress* comme nom d’adresse IP publique, puis sélectionnez **OK**. 

     ![Créer une passerelle d’application : front-ends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Sélectionnez **Suivant : Back-ends**.

### <a name="backends-tab"></a>Onglet Back-ends

Le pool de back-ends est utilisé pour router les demandes vers les serveurs back-end qui les traitent. Les pools de back-ends peuvent être composés de cartes d’interface réseau, de groupes de machines virtuelles identiques, d’adresses IP publiques, d’adresses IP internes, de noms de domaine complets et de back-ends multilocataires comme Azure App Service. Dans cet exemple, vous allez créer un pool de back-ends vide avec votre passerelle d’application, puis ajouter des cibles de back-end au pool.

1. Sous l’onglet **Backends**, sélectionnez **+Ajouter un pool de back-ends**.

2. Dans la fenêtre **Ajouter un pool de back-ends** qui s’ouvre, entrez les valeurs suivantes pour créer un pool de back-ends vide :

    - **Nom** : Entrez *appGatewayBackendPool* comme nom du pool de back-ends.
    - **Ajouter un pool backend sans cible** : Sélectionnez **Oui** pour créer un pool de back-ends sans cible. Vous ajouterez des cibles de back-end après avoir créé la passerelle d’application.

3. Dans la fenêtre **Ajouter un pool de back-ends**, sélectionnez  **Ajouter** pour enregistrer la configuration du pool de back-ends et revenir à l’onglet **Back-ends**.
4. À présent, ajoutez deux pools de back-ends nommés *imagesBackendPool* et *videoBackendPool*.

     ![Créer une passerelle d’application : back-ends](./media/create-url-route-portal/backends.png)

4. Sous l’onglet **Back-ends**, sélectionnez **Suivant : Configuration**.

### <a name="configuration-tab"></a>Onglet Configuration

Sous l’onglet **Configuration**, vous allez connecter les pools de front-ends et de back-ends que vous avez créés à l’aide de règles d’acheminement.

1. Sélectionnez **Ajouter une règle** dans la colonne **Règles de routage**.
2. Dans la fenêtre **Ajouter une règle d’acheminement** qui s’ouvre, entrez *Rule1* comme **Nom de la règle**.
3. Une règle de routage requiert un écouteur. Sous l’onglet **Écouteur** dans la fenêtre **Ajouter une règle de routage**, entrez les valeurs suivantes pour l’écouteur :

    - **Nom de l’écouteur** : Entrez *DefaultListener* comme nom pour l’écouteur.
    - **Adresse IP du front-end** : Sélectionnez **Publique** pour choisir l’adresse IP publique que vous avez créée pour le front-end.

   Acceptez les valeurs par défaut pour les autres paramètres sous l’onglet **Écouteur**, puis sélectionnez l’onglet **Cibles de back-end** pour configurer le reste de la règle de routage.
4. Sous l’onglet **Cibles de back-end**, sélectionnez **appGatewayBackendPool** pour la **cible de back-end**.

5. Pour **Paramètre HTTP**, sélectionnez **Créer nouveau** pour créer un nouveau paramètre HTTP. Le paramètre HTTP détermine le comportement de la règle de routage. Dans la fenêtre **Ajouter un paramètre HTTP** qui s’ouvre, entrez *myHTTPSetting* pour le **Nom du paramètre HTTP**. Acceptez les valeurs par défaut pour les autres paramètres de la fenêtre **Ajouter un paramètre HTTP**, puis sélectionnez **Ajouter** pour revenir à la fenêtre **Ajouter une règle de routage**. 

6. Dans la fenêtre **Ajouter une règle de routage**, sélectionnez **Ajouter** pour enregistrer la règle de routage et revenir à l’onglet **Configuration**.



1. Sélectionnez **Ajouter une règle** dans la colonne **Règles de routage**.

2. Dans la fenêtre **Ajouter une règle de routage** qui s’ouvre, entrez *Rule2* comme **Nom de la règle**.

3. Une règle de routage requiert un écouteur. Sous l’onglet **Écouteur** dans la fenêtre **Ajouter une règle de routage**, entrez les valeurs suivantes pour l’écouteur :

    - **Nom de l’écouteur** : Entrez *myBackendListener* comme nom pour l’écouteur.
    - **Adresse IP du front-end** : Sélectionnez **Publique** pour choisir l’adresse IP publique que vous avez créée pour le front-end.
    - **Port** : 8080

   Sous **Paramètres supplémentaires** :
   - **Type d’écouteur** : De base

   Acceptez les valeurs par défaut pour les autres paramètres sous l’onglet **Écouteur**, puis sélectionnez l’onglet **Cibles de back-end** pour configurer le reste de la règle de routage.

4. Sous l’onglet **Cibles de back-end**, sélectionnez **appGatewayBackendPool** pour la **cible de back-end**.

5. Pour le **paramètre HTTP**, sélectionnez *myHTTPSetting*. Acceptez les valeurs par défaut pour les autres paramètres de la fenêtre **Ajouter un paramètre HTTP**, puis sélectionnez **Ajouter** pour revenir à la fenêtre **Ajouter une règle de routage**. 

1. Sous **Routage basé sur le chemin**, sélectionnez **Ajouter plusieurs cibles pour créer une règle basée sur le chemin**.
2. Dans la fenêtre **Ajouter une règle de chemin**, entrez les valeurs suivantes pour la règle de chemin :

   - **Chemin** : */images/\**
   - **Nom de la règle de chemin** : *Images*
   - **Paramètre HTTP** : sélectionnez *myHTTPSetting*
   - **Cible de back-end** : *imagesBackendPool*
9. Sélectionnez **Ajouter**.
10. Ajoutez une autre règle de chemin nommée *Vidéo* avec un chemin */video/\** et *videoBackendPool*.
11. Sélectionnez **Enregistrer les modifications et revenir aux règles de routage**.

    ![Ajouter une règle de routage](media/create-url-route-portal/add-routing-rule.png)

12. Sélectionnez **Ajouter**.

7. Sélectionnez **Suivant : Balises**, puis sur **Suivant : Vérifier + créer**.

### <a name="review--create-tab"></a>Onglet Vérifier + créer

Examinez les paramètres sous l’onglet **Vérifier + créer**, puis sélectionnez **Créer** pour créer le réseau virtuel, l’adresse IP publique et la passerelle d’application. La création de la passerelle d’application par Azure peut prendre plusieurs minutes.

Patientez jusqu’à ce que le déploiement soit terminé avant de passer à la section suivante.


## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Dans cet exemple, vous créez trois machines virtuelles à utiliser en tant que serveurs principaux pour la passerelle d’application. Vous allez aussi installer IIS sur les machines virtuelles pour vérifier que la passerelle d’application a bien été créée.

1. Dans le portail Azure, sélectionnez **Créer une ressource**. La fenêtre **Nouvelle** apparaît.
2. Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter** dans la liste **Populaire**. La page **Créer une machine virtuelle** s’affiche.

   Application Gateway peut acheminer le trafic vers n’importe quel type de machine virtuelle utilisée dans son pool principal. Dans cet exemple, vous utilisez un serveur Windows Server 2016 Datacenter.
1. Sous l’onglet **De base**, entrez ces valeurs pour les paramètres de machine virtuelle suivants :

    - **Groupe de ressources** : sélectionnez **myResourceGroupAG** comme nom de groupe de ressources.
    - **Nom de la machine virtuelle** : Entrez *myVM1* pour le nom de la machine virtuelle.
    - **Nom d’utilisateur** : entrez *azureuser* comme nom d’utilisateur administrateur.
    - **Mot de passe** : entrez *Azure123456!* comme mot de passe d’administrateur.
4. Acceptez les autres valeurs par défaut, puis sélectionnez **Suivant : Disques**.  
5. Acceptez les valeurs par défaut sous l’onglet **Disques**, puis sélectionnez **Suivant : Mise en réseau**.
6. Sous l’onglet **Mise en réseau**, vérifiez que **myVNet** est sélectionné comme **Réseau virtuel** et que **Sous-réseau** est défini sur  **myBackendSubnet**. Acceptez les autres valeurs par défaut, puis sélectionnez **Suivant : Gestion**.

   Application Gateway peut communiquer avec des instances en dehors du réseau virtuel dans lequel il réside, mais vous devez vérifier qu’il existe une connectivité IP.
1. Sous l’onglet **Gestion**, définissez **Diagnostics de démarrage** sur **Désactivé**. Acceptez les autres valeurs par défaut, puis sélectionnez **Vérifier + créer**.
2. Sous l’onglet **Vérifier + créer**, passez en revue les paramètres, corrigez les éventuelles erreurs de validation et sélectionnez **Créer**.
3. Attendez la fin de la création de la machine virtuelle avant de continuer.

### <a name="install-iis"></a>Installer IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Ouvrez l’interpréteur de commandes interactif et assurez-vous qu’il est défini sur **PowerShell**.

    ![Installer l’extension personnalisée](./media/create-url-route-portal/application-gateway-extension.png)

2. Exécutez la commande suivante pour installer IIS sur la machine virtuelle : 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Créez deux autres machines virtuelles et installez IIS à l’aide de la procédure que vous venez de terminer. Utilisez *myVM2* et *myVM3* pour les noms des machines virtuelles et pour les valeurs **VMName** dans Set-AzVMExtension.

## <a name="add-backend-servers-to-backend-pools"></a>Ajouter des serveurs back-end au pool de back-ends

1. Sélectionnez **Toutes les ressources**, puis **myAppGateway**.

2. Sélectionnez **Pools principaux** dans le menu de gauche.

3. Sélectionnez **appGatewayBackendPool**.

4. Sous **Cibles**, sélectionnez **Machine virtuelle** dans la liste déroulante.

5. Sous **MACHINE VIRTUELLE** et **INTERFACES RÉSEAU**, sélectionnez la machine virtuelle **myVM1** et son interface réseau associée dans les listes déroulantes.

    ![Ajouter des serveurs principaux](./media/create-url-route-portal/backend-pool.png)

6. Sélectionnez **Enregistrer**.
7. Répétez l’opération pour ajouter *myVM2* et une interface à *imagesBackendPool*, puis *myVM3* et une interface au *videoBackendPool*.

Attendez que le déploiement se termine avant de passer à l’étape suivante.

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

1. Sélectionnez **Toutes les ressources**, puis **myAGPublicIPAddress**.

    ![Enregistrer l’adresse IP publique de la passerelle d’application](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur. Par exemple, http://40.121.222.19.

    ![Tester l’URL de base dans la passerelle d’application](./media/create-url-route-portal/application-gateway-iistest.png)

3. Modifiez l’URL http://&lt;ip-address&gt;:8080/images/test.htm, en remplaçant &lt;ip-address&gt; avec votre adresse IP. Vous devriez voir quelque chose ressemblant à ceci :

    ![Tester l’URL images dans la passerelle d’application](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Modifiez l’URL http://&lt;ip-address&gt;:8080/video/test.htm, en remplaçant &lt;ip-address&gt; avec votre adresse IP. Vous devriez voir quelque chose ressemblant à ceci :

    ![Tester l’URL vidéo dans la passerelle d’application](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin des ressources que vous avez créées avec la passerelle d’application, supprimez le groupe de ressources. En supprimant le groupe de ressources, vous supprimez aussi la passerelle d’application et toutes ses ressources associées. 

Pour supprimer le groupe de ressources :

1. Dans le menu de gauche du portail Azure, sélectionnez **Groupes de ressources**.
2. Dans la page **Groupes de ressources**, recherchez **myResourceGroupAG** dans la liste, puis sélectionnez ce groupe de ressources.
3. Dans la page **Groupe de ressources**, sélectionnez **Supprimer le groupe de ressources**.
4. Entrez *myResourceGroupAG* dans **TYPE THE RESOURCE GROUP NAME**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur ce que vous pouvez faire avec Azure Application Gateway](application-gateway-introduction.md)
