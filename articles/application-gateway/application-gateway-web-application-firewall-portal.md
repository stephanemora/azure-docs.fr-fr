---
title: 'Didacticiel : créer une passerelle d’application avec un pare-feu d’applications web - portail Azure | Microsoft Docs'
description: Apprenez à créer une passerelle d’application avec un pare-feu d’applications web à l’aide du portail Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/25/2019
ms.author: victorh
ms.openlocfilehash: 1284ddec4cd9cea3ea53c20d437550405dd614d9
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905866"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Créer une passerelle d’application avec un pare-feu d’applications web à l’aide du portail Azure

> [!div class="op_single_selector"]
>
> - [Portail Azure](application-gateway-web-application-firewall-portal.md)
> - [PowerShell](tutorial-restrict-web-traffic-powershell.md)
> - [Azure CLI](tutorial-restrict-web-traffic-cli.md)
>
> 

Ce didacticiel vous montre comment utiliser le portail Azure pour créer un [passerelle d’application](application-gateway-introduction.md) avec un [pare-feu d’applications web](application-gateway-web-application-firewall-overview.md) (WAF). Le WAF utilise des règles [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) pour protéger votre application. Ces règles incluent la protection contre les attaques telles que l’injection de code SQL, les attaques de script entre sites et les détournements de session. Après avoir créé la passerelle d’application, vous la testez pour vous assurer qu’elle fonctionne correctement. Avec Azure Application Gateway, vous dirigez le trafic web de votre application vers des ressources spécifiques en affectant des écouteurs à des ports, en créant des règles et en ajoutant des ressources à un pool de back-ends. Par souci de simplicité, cet article utilise une configuration simple avec une adresse IP frontale publique, un écouteur de base pour héberger un site unique sur cette instance Application Gateway, deux machines virtuelles utilisées pour le pool principal et une règle d’acheminement de requête simple.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer une passerelle d’application avec WAF activé
> * Créer les machines virtuelles utilisées comme serveurs principaux
> * Créer un compte de stockage et configurer des diagnostics

![Exemple de pare-feu d’applications web](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

Azure a besoin d’un réseau virtuel pour communiquer avec les différentes ressources que vous créez. Vous pouvez créer un réseau virtuel ou en utiliser un. Pour les besoins de cet exemple, nous allons créer un réseau virtuel. Vous pouvez créer un réseau virtuel en même temps que la passerelle d’application. Les instances Application Gateway sont créées dans des sous-réseaux séparés. Vous créez deux sous-réseaux dans cet exemple : un pour la passerelle d’application et un autre pour les serveurs back-end.

Sélectionnez **Créer une ressource** dans le menu de gauche du portail Azure. La fenêtre **Nouvelle** apparaît.

Sélectionnez **Mise en réseau**, puis sélectionnez **Application Gateway** dans la liste **Sélection**.

### <a name="basics-page"></a>Page De base

1. Dans la page **De base**, entrez ces valeurs pour les paramètres de passerelle d’application suivants :
   - **Nom** : entrez *myAppGateway* comme nom de passerelle d’application.
   - **Groupe de ressources** : sélectionnez **myResourceGroupAG** comme nom de groupe de ressources. Si ce groupe n’existe pas encore, sélectionnez **Créer** pour le créer.
   - Sélectionnez *WAF* pour le niveau de la passerelle d’application.![ Créer la nouvelle passerelle d’application](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

Acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK**.

### <a name="settings-page"></a>Page Paramètres

1. Dans la page **Paramètres**, sous **Configuration du sous-réseau**, sélectionnez **Choisir un réseau virtuel**. <br>
2. Dans la page **Choisir un réseau virtuel**, sélectionnez **Créer**, puis entrez les valeurs des paramètres de réseau virtuel suivants :
   - **Nom** : entrez *myVNet* comme nom de réseau virtuel.
   - **Espace d’adressage** : entrez *10.0.0.0/16* pour l’espace d’adressage du réseau virtuel.
   - **Nom du sous-réseau** : entrez *myAGSubnet* comme nom de sous-réseau.<br>Le sous-réseau de passerelle d’application peut contenir uniquement des passerelles d’application. Aucune autre ressource n’est autorisée.
   - **Plage d’adresses de sous-réseau** : Entrez *10.0.0.0/24* pour la plage d’adresses de sous-réseau.![ Créer le réseau virtuel](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)
3. Cliquez sur **OK** pour créer le réseau virtuel et le sous-réseau.
4. Choisissez la **configuration IP frontale**. Sous **Configuration d’adresse IP frontale**, vérifiez que **Type d’adresse IP** a la valeur **Public**. Sous **Adresse IP publique**, vérifiez que l’option **Créer** est sélectionnée. <br>Vous pouvez l’adresse IP frontale pour qu’elle soit publique ou privée conformément à votre cas d’utilisation. Dans cet exemple, nous allons choisir une adresse IP frontale publique. 
5. Entrez *myAGPublicIPAddress* comme nom d’adresse IP publique. 
6. Acceptez les valeurs par défaut des autres paramètres, puis sélectionnez **OK**.<br>Nous allons les valeurs par défaut dans cet article pour plus de simplicité, mais vous pouvez configurer des valeurs personnalisées pour les autres paramètres en fonction de votre cas d’usage. 

### <a name="summary-page"></a>Page de résumé

Passez en revue les paramètres dans la page **Résumé**, puis sélectionnez **OK** pour créer le réseau virtuel, l’adresse IP publique et la passerelle d’application. La création de la passerelle d’application par Azure peut prendre plusieurs minutes. Patientez jusqu’à ce que le déploiement soit terminé avant de passer à la section suivante.

## <a name="add-backend-pool"></a>Ajouter le pool principal

Le pool principal est utilisé pour acheminer les requêtes vers les serveurs principaux qui les traiteront. Les pools back-end peuvent être composés de cartes d’interface réseau, de groupes de machines virtuelles identiques, d’adresses IP publiques, d’adresses IP internes, de noms de domaine complets et de serveurs back-end multi-locataires comme Azure App Service. Vous devez ajouter vos cibles principales à un pool principal.

Dans cet exemple, nous utiliserons des machines virtuelles comme cibles principales. Nous pouvons utiliser des machines virtuelles existantes ou en créer de nouvelles. Dans cet exemple, nous créons deux machines virtuelles qu’Azure va utiliser comme serveurs back-end pour la passerelle d’application. Pour ce faire, nous allons :

1. Créez un sous-réseau, *myBackendSubnet*, dans lequel les machines virtuelles seront créées. 
2. Créez deux machines virtuelles, *myVM* et *myVM2*, à utiliser comme serveurs principaux.
3. Installer IIS sur les machines virtuelles pour vérifier que la passerelle d’application a bien été créée.
4. Ajoutez les serveurs principaux au pool principal.

### <a name="add-a-subnet"></a>Ajouter un sous-réseau

Ajoutez un sous-réseau au réseau virtuel que vous avez créé en suivant ces étapes :

1. Sélectionnez **Toutes les ressources** dans le menu de gauche du portail Azure, entrez *myVNet* dans la zone de recherche, puis sélectionnez **myVNet** dans les résultats de la recherche.

2. Sélectionnez **Sous-réseaux** dans le menu de gauche, puis sélectionnez **+ Sous-réseau**. 

   ![Créer un sous-réseau](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Dans la page **Ajouter un sous-réseau**, entrez *myBackendSubnet* comme **Nom** de sous-réseau, puis sélectionnez **OK**.

### <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

1. Dans le portail Azure, sélectionnez **Créer une ressource**. La fenêtre **Nouvelle** apparaît.
2. Sélectionnez **Compute**, puis sélectionnez **Windows Server 2016 Datacenter** dans la liste **Sélection**. La page **Créer une machine virtuelle** s’affiche.<br>Application Gateway peut acheminer le trafic vers n’importe quel type de machine virtuelle utilisée dans son pool principal. Dans cet exemple, vous utilisez un serveur Windows Server 2016 Datacenter.
3. Sous l’onglet **De base**, entrez ces valeurs pour les paramètres de machine virtuelle suivants :
   - **Groupe de ressources** : sélectionnez **myResourceGroupAG** comme nom de groupe de ressources.
   - **Nom de la machine virtuelle** : entrez *myVM* comme nom de machine virtuelle.
   - **Nom d’utilisateur** : entrez *azureuser* comme nom d’utilisateur administrateur.
   - **Mot de passe** : entrez *Azure123456!* comme mot de passe d’administrateur.
4. Acceptez les autres valeurs par défaut, puis sélectionnez **Suivant : Disques**.  
5. Acceptez les valeurs par défaut sous l’onglet **Disques**, puis sélectionnez **Suivant : Mise en réseau**.
6. Sous l’onglet **Mise en réseau**, vérifiez que **myVNet** est sélectionné comme **Réseau virtuel** et que **Sous-réseau** est défini sur  **myBackendSubnet**. Acceptez les autres valeurs par défaut, puis sélectionnez **Suivant : Gestion**.<br>Application Gateway peut communiquer avec des instances en dehors du réseau virtuel dans lequel il réside, mais nous devons nous assurer qu’il existe une connectivité IP. 
7. Sous l’onglet **Gestion**, définissez **Diagnostics de démarrage** sur **Désactivé**. Acceptez les autres valeurs par défaut, puis sélectionnez **Vérifier + créer**.
8. Sous l’onglet **Vérifier + créer**, passez en revue les paramètres, corrigez les éventuelles erreurs de validation et sélectionnez **Créer**.
9. Attendez la fin de la création de la machine virtuelle avant de continuer.

### <a name="install-iis-for-testing"></a>Installer IIS pour les tests

Dans cet exemple, nous installons IIS sur les machines virtuelles uniquement dans le but de vérifier si Azure a créé la passerelle d’application avec succès. 

1. Ouvrez [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Pour ce faire, sélectionnez **Cloud Shell** dans la barre de navigation supérieure du portail Azure, puis sélectionnez **PowerShell** dans la liste déroulante. 

   ![Installer l’extension personnalisée](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Exécutez la commande suivante pour installer IIS sur la machine virtuelle : 

   ```azurepowershell-interactive
   Set-AzVMExtension `
     -ResourceGroupName myResourceGroupAG `
     -ExtensionName IIS `
     -VMName myVM `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
   ```

3. Créez une deuxième machine virtuelle et installez IIS en suivant les mêmes étapes que vous avez effectuées précédemment. Utilisez *myVM2* pour le nom de la machine virtuelle et pour le **VMName** définition de la **Set-AzVMExtension** applet de commande.

### <a name="add-backend-servers-to-backend-pool"></a>Ajouter des serveurs principaux pour le pool principal

1. Sélectionnez **Toutes les ressources**, puis **myAppGateway**.

2. Sélectionnez **Pools principaux** dans le menu de gauche. Azure a automatiquement créé un pool par défaut, appelé **appGatewayBackendPool**, quand vous avez créé la passerelle d’application. 

3. Sélectionnez **appGatewayBackendPool**.

4. Sous **Cibles**, sélectionnez **Machine virtuelle** dans la liste déroulante.

5. Sous **MACHINE VIRTUELLE** et **INTERFACES RÉSEAU**, sélectionnez les machines virtuelles **myVM** et **myVM2** ainsi que leurs interfaces réseau associées dans les listes déroulantes.

   ![Ajouter des serveurs principaux](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Sélectionnez **Enregistrer**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Créer un compte de stockage et configurer des diagnostics

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Dans ce didacticiel, la passerelle d’application utilise un compte de stockage pour stocker des données à des fins de détection et de prévention. Vous pouvez également utiliser les journaux Azure Monitor ou Event Hub pour enregistrer des données.

1. Cliquez sur **Nouveau** dans le coin supérieur gauche du portail Azure.
2. Sélectionnez **Stockage**, puis **Compte de stockage - blob, fichier, table, file d’attente**.
3. Entrez le nom du compte de stockage, sélectionnez **Utiliser l’existant** pour le groupe de ressources, puis sélectionnez **myResourceGroupAG**. Dans cet exemple, le nom du compte de stockage est *myagstore1*. Acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **Créer**.

### <a name="configure-diagnostics"></a>Configuration de la collecte des diagnostics

Configurez des diagnostics pour enregistrer des données dans les journaux ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog et ApplicationGatewayFirewallLog.

1. Dans le menu de gauche, cliquez sur **Toutes les ressources**, puis sélectionnez *myAppGateway*.
2. Sous Surveillance, cliquez sur **Journaux de diagnostic**.
3. Cliquez sur **Ajouter les paramètres de diagnostic**.
4. Entrez *myDiagnosticsSettings* pour le nom des paramètres de diagnostic.
5. Sélectionnez **Archive to a storage account** (Archiver dans un compte de stockage), puis cliquez sur **Configurer** pour sélectionner le compte de stockage *myagstore1* que vous avez créé précédemment.
6. Sélectionnez les journaux de passerelle d’application à collecter et conserver.
7. Cliquez sur **Enregistrer**.

    ![Configuration de la collecte des diagnostics](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

IIS n’est pas nécessaire pour créer la passerelle d’application, mais vous l’avez installé dans ce guide de démarrage rapide pour vérifier qu’Azure avait bien créé la passerelle d’application. Utilisez IIS pour tester la passerelle d’application :

1. Trouvez l’adresse IP publique de la passerelle d’application sur la page **Vue d’ensemble** correspondante. ![Enregistrez l’adresse IP publique de la passerelle d’application](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png).Vous pouvez aussi sélectionner **Toutes les ressources**, entrer *myAGPublicIPAddress* dans la zone de recherche, puis sélectionner l’adresse IP publique dans les résultats de la recherche. Azure affiche l’adresse IP publique dans la page **Vue d’ensemble**.
2. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur.
3. Vérifiez la réponse. Une réponse valide vérifie que la passerelle d’application a été créée avec succès et qu’elle est capable de se connecter avec succès au serveur principal.![Tester la passerelle d’application](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin des ressources que vous avez créées avec la passerelle d’application, supprimez le groupe de ressources. En supprimant le groupe de ressources, vous supprimez aussi la passerelle d’application et toutes ses ressources associées. 

Pour supprimer le groupe de ressources :

1. Dans le menu de gauche du portail Azure, sélectionnez **Groupes de ressources**.
2. Dans la page **Groupes de ressources**, recherchez **myResourceGroupAG** dans la liste, puis sélectionnez ce groupe de ressources.
3. Dans la page **Groupe de ressources**, sélectionnez **Supprimer le groupe de ressources**.
4. Entrez *myResourceGroupAG* dans **TAPER LE NOM DU GROUPE DE RESSOURCES**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une passerelle d’application avec WAF activé
> * Créer les machines virtuelles utilisées comme serveurs principaux
> * Créer un compte de stockage et configurer des diagnostics

Pour en savoir plus sur les passerelles d’application et leurs ressources associées, consultez les articles de procédures.
