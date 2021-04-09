---
title: Configurer un point de terminaison d’équilibreur de charge interne (ILB)
titleSuffix: Azure Application Gateway
description: Cet article fournit des informations sur la configuration d’Application Gateway avec une adresse IP frontale privée.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: 224cbe1e34e5915a7fa5fc1cf415c35f86c3abe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711652"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Configurer une passerelle Application Gateway avec un point de terminaison d’équilibreur de charge interne (ILB)

Vous pouvez configurer Azure Application Gateway avec une adresse IP virtuelle accessible via Internet ou avec un point de terminaison interne non exposé à Internet. Un point de terminaison interne utilise une adresse IP privée pour le serveur frontal, également appelée *point de terminaison d’équilibreur de charge interne*.

Il est utile de configurer la passerelle à l’aide d’une adresse IP privée frontale pour des applications métier internes non exposées à Internet. Il est également utile pour les services et les niveaux d’une application à plusieurs niveaux, qui se trouvent dans une zone de sécurité non exposée à Internet mais qui :

- nécessitent toujours une distribution de charge par tourniquet (Round Robin) ;
- nécessitent une adhérence de session
- ou une terminaison Transport Layer Security (TLS) , anciennement appelée Secure Sockets Layer (SSL).

Cet article vous guide dans les étapes de configuration d’une passerelle Application Gateway avec une adresse IP privée frontale à l’aide du portail Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

Azure a besoin d’un réseau virtuel pour communiquer avec les différentes ressources que vous créez. Créez un réseau virtuel ou utilisez un réseau virtuel existant. 

Dans cet exemple, vous allez créer un réseau virtuel. Vous pouvez créer un réseau virtuel en même temps que la passerelle d’application. Les instances Application Gateway sont créées dans des sous-réseaux séparés. Cet exemple comporte deux sous-réseaux : un pour la passerelle d’application et un autre pour les serveurs back-end.

1. Développez le menu du portail, puis sélectionnez **Créer une ressource**.
2. Sélectionnez **Mise en réseau**, puis sélectionnez **Application Gateway** dans la liste de suggestions.
3. Entrez *myAppGateway* pour le nom de la passerelle d’application et *myResourceGroupAG* pour le nouveau groupe de ressources.
4. Pour **Région**, sélectionnez **USA Centre**.
5. Pour **Niveau**, sélectionnez **Standard**.
6. Sous **Configurer un réseau virtuel**, sélectionnez **Créer**, puis entrez les valeurs suivantes pour le réseau virtuel :
   - *myVNet* : pour le nom du réseau virtuel.
   - *10.0.0.0/16* : pour l’espace d’adressage du réseau virtuel.
   - *myAGSubnet* : pour le nom du sous-réseau.
   - *10.0.0.0/24* : pour l’espace d’adressage du sous-réseau.
   - *myBackendSubnet* : pour le nom de sous-réseau principal.
   - *10.0.1.0/24* : pour l’espace d’adressage de sous-réseau principal.

    ![Création d’un réseau virtuel](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Sélectionnez **OK** pour créer le réseau virtuel et les sous-réseaux.
7. Sélectionnez **Suivant : Serveurs frontaux**.
8. Pour **Type d’adresse IP de serveur frontal**, sélectionnez **Privée**.

   Par défaut, il s’agit d’une affectation d’adresse IP dynamique. La première adresse disponible du sous-réseau configuré est affectée en tant qu’adresse IP du serveur frontal.
   > [!NOTE]
   > Une fois alloué, le type d’adresse IP (statique ou dynamique) ne peut pas être modifié ultérieurement.
9. Sélectionnez **Suivant : Serveurs principaux**.
10. Sélectionnez **Ajouter un pool principal**.
11. Pour **Nom**, tapez *appGatewayBackendPool*.
12. Pour **Ajouter un pool principal sans cible**, sélectionnez **Oui**. Vous ajouterez les cibles ultérieurement.
13. Sélectionnez **Ajouter**.
14. Sélectionnez **Suivant : Configuration**.
15. Sous **Règles de routage**, sélectionnez **Ajouter une règle de routage**.
16. Pour **Nom de la règle**, tapez *Rule-01*.
17. Pour **Nom de l’écouteur**, tapez *Listener-01*.
18. Pour **Adresse IP du front-end**, sélectionnez **Privée**.
19. Acceptez les valeurs par défaut restantes, puis sélectionnez l’onglet **Cibles de back-end**.
20. Pour **Type de cible**, sélectionnez **Pool principal**, puis **appGatewayBackendPool**.
21. Pour **Paramètres HTTP**, sélectionnez **Ajouter nouveau**.
22. Pour **Nom du paramètre HTTP**, tapez *http-Setting-01*.
23. Pour **Protocole principal**, sélectionnez **HTTP**.
24. Pour **Port principal**, tapez *80*.
25. Acceptez les valeurs par défaut restantes, puis sélectionnez **Ajouter**.
26. Dans la page **Ajouter une règle de routage**, sélectionnez **Ajouter**.
27. Sélectionnez **Suivant : Balises**.
28. Sélectionnez **Suivant : Vérifier + créer**.
29. Passez en revue les paramètres sur la page de résumé, puis sélectionnez **Créer** pour créer les ressources réseau et la passerelle d’application. La création de la passerelle d’application peut prendre plusieurs minutes. Patientez jusqu’à ce que le déploiement soit terminé avant de passer à la section suivante.

## <a name="add-backend-pool"></a>Ajouter le pool principal

Le pool de back-ends est utilisé pour router les demandes vers les serveurs back-end qui les traitent. Le pool principal peut se composer de cartes d’interface réseau, de groupes de machines virtuelles identiques, d’adresses IP publiques, d’adresses IP internes, de noms de domaine complets (FQSN) et de services principaux mutualisés comme Azure App Service. Dans cet exemple, vous allez utilisez des machines virtuelles comme back-end cible. Vous pouvez utiliser des machines virtuelles existantes ou en créer de nouvelles. Dans cet exemple, vous créez deux machines virtuelles qu’Azure va utiliser comme serveurs back-end pour la passerelle d’application.

Pour ce faire, procédez comme suit :

1. Créez deux machines virtuelles, *myVM* et *myVM2*, utilisées en tant que serveurs principaux.
2. Installer IIS sur les machines virtuelles pour vérifier que la passerelle d’application a bien été créée.
3. Ajoutez les serveurs principaux au pool principal.

### <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle


1. Sélectionnez **Créer une ressource**.
2. Sélectionnez **Compute**, puis **Machine virtuelle**.
4. Entrez ces valeurs pour la machine virtuelle :
   - Sélectionnez votre abonnement.
   - Sélectionnez *myResourceGroupAG* pour **Groupe de ressources**.
   - Tapez *myVM* pour **Nom de la machine virtuelle**.
   - Sélectionnez **Windows Server 2019 Datacenter** pour **Image**.
   - Entrez un **nom d'utilisateur** valide.
   - Entrez un **mot de passe** valide.
1. Acceptez les valeurs par défaut restantes, puis sélectionnez **Suivant : Disques**.
1. Acceptez les valeurs par défaut, puis sélectionnez **Suivant : Mise en réseau**.
1. Assurez-vous que **myVNet** est sélectionné pour le réseau virtuel et que le sous-réseau est **myBackendSubnet**.
1. Acceptez les valeurs par défaut restantes, puis sélectionnez **Suivant : Gestion**.
1. Sélectionnez **Désactiver** pour désactiver les diagnostics de démarrage.
1. Sélectionnez **Revoir + créer**.
1. Vérifiez les paramètres sur la page de récapitulatif, puis sélectionnez **Créer**. La création de la machine virtuelle peut nécessiter plusieurs minutes. Patientez jusqu’à ce que le déploiement soit terminé avant de passer à la section suivante.

### <a name="install-iis"></a>Installer IIS

1. Ouvrez le service Cloud Shell et assurez-vous qu’il est défini sur **PowerShell**.
    ![Capture d’écran montrant une fenêtre de console ouverte Azure Cloud Shell qui utilise PowerShell.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Exécutez la commande suivante pour installer IIS sur la machine virtuelle :

   ```azurepowershell
   Set-AzVMExtension `
        -ResourceGroupName myResourceGroupAG `
        -ExtensionName IIS `
        -VMName myVM `
        -Publisher Microsoft.Compute `
        -ExtensionType CustomScriptExtension `
        -TypeHandlerVersion 1.4 `
        -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
         -Location CentralUS 

   ```

3. Créez une deuxième machine virtuelle et installez IIS à l’aide de la procédure que vous venez de terminer. Utilisez myVM2 comme nom de machine virtuelle, et pour `VMName` dans `Set-AzVMExtension`.

### <a name="add-backend-servers-to-backend-pool"></a>Ajouter des serveurs principaux pour le pool principal

1. Sélectionnez **Toutes les ressources**, puis **myAppGateway**.
2. Sélectionnez **Pools principaux**, puis **appGatewayBackendPool**.
3. Sous **Type de cible**, sélectionnez **Machine virtuelle**, puis, sous **Cible**, sélectionnez la carte réseau virtuelle associée à myVM.
4. Répétez l’opération pour ajouter MyVM2.
   ![Volet Modifier le pool principal avec les Types de cible et les Cibles mis en surbrillance.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. Sélectionnez **Enregistrer**.

## <a name="create-a-client-virtual-machine"></a>Créer une machine virtuelle cliente

La machine virtuelle cliente sert à se connecter au pool principal de la passerelle d’application.

- Créez une troisième machine virtuelle à l’aide des étapes précédentes. Utilisez myVM3 comme nom de la machine virtuelle.

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

1. Sur la page myAppGateway, sélectionnez **Configurations d’adresses IP frontales** pour noter l’adresse IP privée du serveur frontal.
    ![Volet Configurations d’adresses IP frontales avec le type Privé mis en surbrillance.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copiez l’adresse IP privée, puis collez-la dans la barre d’adresse du navigateur sur myVM3 pour accéder au pool principal de la passerelle d’application.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez surveiller l’intégrité de votre pool principal, voir [Intégrité du serveur principal et journaux de diagnostic pour la passerelle Application Gateway](application-gateway-diagnostics.md).
