---
title: Configurer un point de terminaison d’équilibreur de charge interne (ILB)
titleSuffix: Azure Application Gateway
description: Cet article fournit des informations sur la configuration d’Application Gateway avec une adresse IP frontale privée.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: a9e3150a5382e4d690ddf66c43bbe51e125509d3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075214"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Configurer une passerelle Application Gateway avec un point de terminaison d’équilibreur de charge interne (ILB)

Vous pouvez configurer une passerelle Azure Application Gateway avec une adresse IP virtuelle côté Internet ou avec un point de terminaison interne non exposé à Internet (à l’aide d’une adresse IP privée comme adresse IP frontale), également appelé point de terminaison d’équilibreur de charge interne (ILB). Il est utile de configurer la passerelle à l’aide d’une adresse IP privée frontale, car les applications cœur de métier internes non sont pas exposées à Internet. Elle est également utile pour les services et niveaux au sein d’une application multiniveau qui se trouve dans une limite de sécurité non exposée à Internet, mais qui requiert tout de même une distribution de charge par tourniquet, une adhérence de session ou une terminaison SSL (Secure Sockets Layer).

Cet article vous guidera au cours des étapes de configuration d’une passerelle Application Gateway avec une adresse IP privée frontale à partir du portail Azure.

Dans cet article, vous allez apprendre à :

- Créer une configuration d’adresse IP frontale privée pour une passerelle Application Gateway
- Créer une passerelle Application Gateway avec une configuration d’adresse IP frontale privée


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure à l’adresse suivante : <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

Azure a besoin d’un réseau virtuel pour communiquer avec les différentes ressources que vous créez. Vous pouvez créer un réseau virtuel ou en utiliser un. Pour les besoins de cet exemple, nous allons créer un réseau virtuel. Vous pouvez créer un réseau virtuel en même temps que la passerelle d’application. Les instances Application Gateway sont créées dans des sous-réseaux séparés. Vous créez deux sous-réseaux dans cet exemple : un pour la passerelle d’application et un autre pour les serveurs back-end.

1. Cliquez sur **Nouveau** dans le coin supérieur gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis sélectionnez **Application Gateway** dans la liste de suggestions.
3. Entrez *myAppGateway* pour le nom de la passerelle d’application et *myResourceGroupAG* pour le nouveau groupe de ressources.
4. Acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK**.
5. Cliquez sur **Choisir un réseau virtuel**, cliquez sur **Créer nouveau**, puis entrez ces valeurs pour le réseau virtuel :
   - myVNet* : pour le nom du réseau virtuel.
   - 10.0.0.0/16* : pour l’espace d’adressage du réseau virtuel.
   - *myAGSubnet* : pour le nom du sous-réseau.
   - *10.0.0.0/24* : pour l’espace d’adressage du sous-réseau.  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. Cliquez sur **OK** pour créer le réseau virtuel et le sous-réseau.
7. Choisissez la configuration d’adresse IP frontale et définissez-la sur Privé et par défaut. Il s’agit d’une attribution d’adresse IP dynamique. La première adresse disponible du sous-réseau choisi sera attribuée en tant qu’adresse IP frontale.
8. Si vous souhaitez choisir une adresse IP privée à partir de la plage d’adresses du sous-réseau (allocation statique), cliquez sur la zone **Choisir une adresse IP privée spécifique** et spécifiez l’adresse IP.
   > [!NOTE]
   > Une fois alloué, le type d’adresse IP (statique ou dynamique) ne peut pas être modifié ultérieurement.
9. Choisissez votre configuration d’écouteur pour le protocole et le port ainsi que votre configuration de pare-feu d’applications web (si nécessaire), puis cliquez sur OK.
    ![private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Passez en revue les paramètres sur la page de résumé, puis cliquez sur **OK** pour créer les ressources réseau et la passerelle d’application. La création de la passerelle d’application peut prendre plusieurs minutes. Patientez jusqu’à ce que le déploiement soit terminé avant de passer à la section suivante.

## <a name="add-backend-pool"></a>Ajouter le pool principal

Le pool principal est utilisé pour acheminer les requêtes vers les serveurs principaux qui les traiteront. La partie principale peut se composer de cartes d’interface réseau, de groupes de machines virtuelles identiques, d’adresses IP publiques, d’adresses IP internes, de noms de domaine complets et de serveurs back-end multi-locataires comme Azure App Service. Dans cet exemple, nous utiliserons des machines virtuelles comme cibles principales. Nous pouvons utiliser des machines virtuelles existantes ou en créer de nouvelles. Dans cet exemple, nous créons deux machines virtuelles qu’Azure va utiliser comme serveurs back-end pour la passerelle d’application. Pour ce faire, nous allons :

1. Créez deux machines virtuelles, *myVM* et *myVM2*, à utiliser comme serveurs principaux.
2. Installer IIS sur les machines virtuelles pour vérifier que la passerelle d’application a bien été créée.
3. Ajoutez les serveurs principaux au pool principal.

### <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

1. Cliquez sur **Nouveau**.
2. Cliquez sur **Compute**, puis sélectionnez **Windows Server 2016 Datacenter** dans la liste de suggestions.
3. Entrez ces valeurs pour la machine virtuelle :
   - *myVM* : pour le nom de la machine virtuelle.
   - *azureuser* : pour le nom d’utilisateur administrateur.
   - *Azure123456!* pour le mot de passe.
   - Sélectionnez **Utiliser l’existant**, puis *myResourceGroupAG*.
4. Cliquez sur **OK**.
5. Sélectionnez **DS1_V2** pour la taille de la machine virtuelle, puis cliquez sur **Sélectionner**.
6. Assurez-vous que **myVNet** est sélectionné pour le réseau virtuel et que le sous-réseau est **myBackendSubnet**.
7. Cliquez sur **Désactivé** pour désactiver les diagnostics de démarrage.
8. Cliquez sur **OK**, vérifiez les paramètres sur la page de résumé, puis cliquez sur **Créer**.

### <a name="install-iis"></a>Installer IIS

1. Ouvrez l’interpréteur de commandes interactif et assurez-vous qu’il est défini sur **PowerShell**.
    ![private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Exécutez la commande suivante pour installer IIS sur la machine virtuelle :

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
