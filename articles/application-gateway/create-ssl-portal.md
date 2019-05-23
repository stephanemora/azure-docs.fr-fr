---
title: Tutoriel – Configurer une passerelle d’application avec un arrêt SSL – Portail Azure
description: Dans ce tutoriel, vous allez découvrir comment configurer une passerelle d’application et ajouter un certificat pour un arrêt SSL à partir du portail Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/17/2019
ms.author: victorh
ms.openlocfilehash: f3ba3eb12dc85a72c4e49c374e62209b83400d33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66134532"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Didacticiel : Configurer une passerelle d’application avec un arrêt SSL à l’aide du portail Azure

Vous pouvez utiliser le portail Azure afin de configurer une [passerelle d’application](overview.md) avec un certificat pour un arrêt SSL qui utilise des machines virtuelles avec des serveurs backend.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un certificat auto-signé
> * Créer une passerelle d’application avec le certificat
> * Créer les machines virtuelles utilisées comme serveurs principaux
> * Tester la passerelle d’application

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-self-signed-certificate"></a>Créer un certificat auto-signé

Dans cette section, vous allez utiliser [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) pour créer un certificat auto-signé. Vous chargerez ensuite ce certificat sur le portail Azure au moment de créer l’écouteur pour la passerelle d’application.

Sur votre ordinateur local, ouvrez une fenêtre Windows PowerShell en tant qu’administrateur. Exécutez la commande suivante pour créer le certificat :

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

Une réponse comme l’exemple suivant devrait s’afficher :

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) with the Thumbprint that was returned to export a pfx file from the certificate:
```

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate \
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 \
  -FilePath c:\appgwcert.pfx \
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

Un réseau virtuel est nécessaire pour la communication entre les ressources que vous créez. Deux sous-réseaux sont créés dans cet exemple : une pour la passerelle d’application et l’autre pour les serveurs principaux. Vous pouvez créer un réseau virtuel en même temps que la passerelle d’application.

1. Sélectionnez **Nouveau** dans le coin supérieur gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis sélectionnez **Application Gateway** dans la liste de suggestions.
3. Entrez *myAppGateway* pour le nom de la passerelle d’application et *myResourceGroupAG* pour le nouveau groupe de ressources.
4. Acceptez les valeurs par défaut des autres paramètres, puis sélectionnez **OK**.
5. Sélectionnez **Choisir un réseau virtuel**, **Créer nouveau**, puis entrez ces valeurs pour le réseau virtuel :

   - *myVNet* : pour le nom du réseau virtuel.
   - *10.0.0.0/16* : pour l’espace d’adressage du réseau virtuel.
   - *myAGSubnet* : pour le nom du sous-réseau.
   - *10.0.0.0/24* : pour l’espace d’adressage du sous-réseau.

     ![Création d’un réseau virtuel](./media/create-ssl-portal/application-gateway-vnet.png)

6. Sélectionnez **OK** pour créer le réseau virtuel et le sous-réseau.
7. Sélectionnez **Choisir une adresse IP publique**, **Créer nouveau**, puis entrez le nom de l’adresse IP publique. Dans cet exemple, l’adresse IP publique est nommée *myAGPublicIPAddress*. Acceptez les valeurs par défaut des autres paramètres, puis sélectionnez **OK**.
8. Sélectionnez **HTTPS** pour le protocole de l’écouteur et vérifiez que le port est défini sur **443**.
9. Sélectionnez l’icône de dossier et recherchez le certificat *appgwcert.pfx* que vous avez créé précédemment pour le charger.
10. Entrez *mycert1* pour le nom du certificat et *Azure123456!* pour le mot de passe, puis sélectionnez **OK**.

    ![Créer une nouvelle passerelle d’application](./media/create-ssl-portal/application-gateway-create.png)

11. Passez en revue les paramètres sur la page de résumé, puis cliquez sur **OK** pour créer les ressources réseau et la passerelle d’application. La création de la passerelle d’application peut prendre plusieurs minutes. Patientez jusqu’à ce que le déploiement soit terminé avant de passer à la section suivante.

### <a name="add-a-subnet"></a>Ajouter un sous-réseau

1. Sélectionnez **Toutes les ressources** dans le menu de gauche, puis cliquez sur **myVNet** dans la liste des ressources.
2. Sélectionnez **Sous-réseaux**, puis **Sous-réseau**.

    ![Créer un sous-réseau](./media/create-ssl-portal/application-gateway-subnet.png)

3. Entrez *myBackendSubnet* pour le nom du sous-réseau, puis sélectionnez **OK**.

## <a name="create-backend-servers"></a>Créer des serveurs principaux

Dans cet exemple, vous allez créer deux machines virtuelles à utiliser comme serveurs back-end pour la passerelle d’application. Vous allez aussi installer IIS sur les machines virtuelles pour vérifier que la passerelle d’application a bien été créée.

### <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

1. Sélectionnez **Nouveau**.
2. Cliquez sur **Compute**, puis sélectionnez **Windows Server 2016 Datacenter** dans la liste de suggestions.
3. Entrez ces valeurs pour la machine virtuelle :

    - *myVM* : pour le nom de la machine virtuelle.
    - *azureuser* : pour le nom d’utilisateur administrateur.
    - *Azure123456!* pour le mot de passe.
    - Sélectionnez **Utiliser l’existant**, puis *myResourceGroupAG*.

4. Sélectionnez **OK**.
5. Sélectionnez **DS1_V2** pour la taille de la machine virtuelle, puis **Sélectionner**.
6. Assurez-vous que **myVNet** est sélectionné pour le réseau virtuel et que le sous-réseau est **myBackendSubnet**. 
7. Sélectionnez **Désactivé** pour désactiver les diagnostics de démarrage.
8. Sélectionnez **OK**, vérifiez les paramètres sur la page de résumé, puis sélectionnez **Créer**.

### <a name="install-iis"></a>Installer IIS

1. Ouvrez l’interpréteur de commandes interactif et assurez-vous qu’il est défini sur **PowerShell**.

    ![Installer l’extension personnalisée](./media/create-ssl-portal/application-gateway-extension.png)

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

3. Créez une deuxième machine virtuelle et installez IIS à l’aide de la procédure que vous venez de terminer. Entrez *myVM2* pour le nom et VMName dans Set-AzureRmVMExtension.

### <a name="add-backend-servers"></a>Ajouter des serveurs principaux

1. Sélectionnez **Toutes les ressources**, puis **myAppGateway**.
1. Sélectionnez **Pools principaux**. Un pool par défaut a été automatiquement créé avec la passerelle d’application. Sélectionnez **appGatewayBackendPool**.
1. Sélectionnez **Ajouter une cible** pour ajouter chaque machine virtuelle que vous avez créée au pool back-end.

    ![Ajouter des serveurs principaux](./media/create-ssl-portal/application-gateway-backend.png)

1. Sélectionnez **Enregistrer**.

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

1. Sélectionnez **Toutes les ressources**, puis **myAGPublicIPAddress**.

    ![Enregistrer l’adresse IP publique de la passerelle d’application](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur. Pour accepter l’avertissement de sécurité si vous avez utilisé un certificat auto-signé, sélectionnez Détails, puis Atteindre la page web :

    ![Avertissement de sécurité](./media/create-ssl-portal/application-gateway-secure.png)

    Votre site IIS sécurisé apparaît maintenant comme dans l’exemple suivant :

    ![Tester l’URL de base dans la passerelle d’application](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur ce que vous pouvez faire avec Azure Application Gateway](application-gateway-introduction.md)
