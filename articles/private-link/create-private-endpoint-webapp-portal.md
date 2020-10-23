---
title: Se connecter en privé à une application web avec Azure Private Endpoint
description: Cet article explique comment se connecter en privé à une application web à l’aide d’Azure Private Endpoint.
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 10/07/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 3e0f05240aba9b5c92689315e409aaabe793b3f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91772836"
---
# <a name="connect-privately-to-a-web-app-by-using-azure-private-endpoint"></a>Se connecter en privé à une application web avec Azure Private Endpoint

Le point de terminaison privé Azure est le composant fondamental d’Azure Private Link. En utilisant Private Endpoint, vous pouvez vous connecter en privé à votre application web. Dans cet article, vous allez apprendre à déployer une application web à l’aide de Private Endpoint, puis à vous y connecter à partir d’une machine virtuelle.

Pour plus d’informations, consultez [Utiliser des points de terminaison privés pour une application web Azure][privateendpointwebapp].

> [!Note]
> Private Endpoint est disponible dans les régions publiques pour les applications web Windows de niveau Premium V2, Premium V3, les applications Linux et le plan Azure Functions Premium (parfois appelé plan Élastique Premium). 

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Avant de commencer, connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-virtual-network-and-virtual-machine"></a>Créer un réseau virtuel et une machine virtuelle

Dans cette section, vous créez un réseau virtuel et un sous-réseau pour héberger une machine virtuelle que vous allez utiliser pour accéder à une application web par le biais d’un point de terminaison privé.

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Pour créer le réseau virtuel et le sous-réseau, effectuez les étapes suivantes :

1. Dans le volet gauche, sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.

1. Dans le volet **Créer un réseau virtuel**, sélectionnez l’onglet **Informations de base**, puis entrez les informations indiquées ici :

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran du volet « Créer un réseau virtuel » dans le portail Azure.][1]

1. Sélectionnez l’onglet **Adresses IP**, puis entrez les informations indiquées ici :

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de l’onglet « Adresses IP » dans le volet Créer un réseau virtuel.][2]

1. Dans la section **Sous-réseau**, sélectionnez **Ajouter un sous-réseau**, entrez les informations indiquées ici, puis sélectionnez **Ajouter**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran du volet « Ajouter un sous-réseau ».][3]

1. Sélectionnez **Revoir + créer**.

1. Après la validation, sélectionnez **Créer**.

### <a name="create-the-virtual-machine"></a>Créer la machine virtuelle

Pour créer la machine virtuelle, effectuez les étapes suivantes :

1. Dans le portail Azure, dans le volet gauche, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**.

1. Dans le volet **Créer une machine virtuelle - Informations de base**, entrez les informations indiquées ici :

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran du volet « Créer une machine virtuelle ».][4]

1. Sélectionnez **Suivant : Disques**.

1. Dans le volet **Disques**, conservez les paramètres par défaut, puis sélectionnez **Suivant : Mise en réseau**.

1. Dans le volet **Mise en réseau**, entrez les informations indiquées ici :

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de l’onglet « Mise en réseau » du volet « Créer une machine virtuelle ».][5]

1. Sélectionnez **Revoir + créer**.

1. Après la validation, sélectionnez **Créer**.

## <a name="create-a-web-app-and-a-private-endpoint"></a>Créer une application web et un point de terminaison privé

Dans cette section, vous créez une application web privée qui utilise un point de terminaison privé.

> [!Note]
> La fonctionnalité Private Endpoint (Point de terminaison privé) est disponible uniquement pour les niveaux Premium V2 et Premium V3.

### <a name="create-the-web-app"></a>Créer l’application web

1. Dans le portail Azure, dans le volet gauche, sélectionnez **Créer une ressource** > **Web** > **Application web**.

1. Dans le volet **Application web**, sélectionnez l’onglet **Informations de base**, puis entrez les informations indiquées ici :

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de l’onglet « Informations de base » dans le volet « Application web ».][6]

1. Sélectionnez **Revoir + créer**.

1. Après la validation, sélectionnez **Créer**.

### <a name="create-the-private-endpoint"></a>Créer le point de terminaison privé

1. Dans les propriétés de l’application web, sous **Paramètres**, sélectionnez **Mise en réseau**, puis, sous **Connexions des points de terminaison privés**, sélectionnez **Configurer vos connexions de point de terminaison privé**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran du lien « Configurer vos connexions de point de terminaison privé » dans le volet Mise en réseau de l’application web.][7]

1. Dans l’Assistant **Connexions des points de terminaison privés**, sélectionnez **Ajouter**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran du bouton Ajouter dans l’Assistant « Connexions des points de terminaison privés ».][8]

1. Sélectionnez les informations appropriées dans les listes déroulantes **Abonnement**, **Réseau virtuel** et **Sous-réseau**, puis cliquez sur **OK**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran du volet « Ajouter un point de terminaison privé ».][9]

1. Supervisez la progression de la création du point de terminaison privé.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de la progression de l’ajout du point de terminaison privé.][10]
   > ![Capture d’écran du point de terminaison privé nouvellement créé.][11]

## <a name="connect-to-the-vm-from-the-internet"></a>Se connecter à la machine virtuelle à partir d’Internet

1. Dans la zone **Rechercher** du portail Azure, entrez **myVm**.
1. Sélectionnez **Connecter**, puis **RDP**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran du bouton « RDP » dans le volet « myVM ».][12]

1. Dans le volet **Se connecter avec RDP**, sélectionnez **Télécharger le fichier RDP**.  

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran du bouton « Télécharger le fichier RDP » dans le volet « Se connecter avec RDP ».][13]

   Azure crée un fichier RDP (Remote Desktop Protocol) et le télécharge sur votre ordinateur.   

1. Ouvrez le fichier .rdp téléchargé.

   a. À l’invite, sélectionnez **Connecter**.  
   b. Entrez le nom d’utilisateur et le mot de passe spécifiés quand vous avez créé la machine virtuelle.

     > [!Note]
     > Pour utiliser ces informations d’identification, vous devrez peut-être sélectionner **Autres choix** > **Utiliser un compte différent**.

1. Sélectionnez **OK**.

   > [!Note]
   > Si vous recevez un avertissement lié au certificat pendant le processus de connexion, sélectionnez **Oui** ou **Continuer**.

1. Quand la fenêtre du bureau de la machine virtuelle s’affiche, réduisez-la pour revenir à votre bureau local.

## <a name="access-the-web-app-privately-from-the-vm"></a>Accéder à l’application web en privé à partir de la machine virtuelle

Dans cette section, vous allez vous connecter en privé à l’application web à l’aide du point de terminaison privé.

1. Pour obtenir l’adresse IP privée de votre point de terminaison privé, dans la zone **Rechercher**, tapez **liaison privée**, puis, dans la liste des résultats, sélectionnez **Liaison privée**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran du lien « Liaison privée » dans la liste des résultats de la recherche.][14]

1. Dans le volet gauche du Centre de liaisons privées, sélectionnez **Points de terminaison privés** pour afficher vos points de terminaison privés.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de la liste des points de terminaison privés dans le Centre de liaisons privées.][15]

1. Sélectionnez le point de terminaison privé lié à votre application web et votre sous-réseau.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran du volet des propriétés d’un point de terminaison privé.][16]

1. Copiez l’adresse IP privée de votre point de terminaison privé et le nom de domaine complet (FQDN) de votre application web. Dans l’exemple précédent, l’ID privé est *`webappdemope.azurewebsites.net 10.10.2.4`* .

1. Dans le volet **myVM**, vérifiez que l’application web n’est pas accessible par le biais de l’adresse IP publique. Pour cela, ouvrez un navigateur et collez le nom de l’application web. La page doit afficher un message « Erreur 403 - Interdit ».

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran d’une page d’erreur « Erreur 403 - Interdit ».][17]

   Pour le DNS, effectuez l’une des opérations suivantes :
 
   - Utilisez le service de zone privée Azure DNS.  

     a. Créez une zone privée DNS nommée *`privatelink.azurewebsites.net`* , puis liez-la au réseau virtuel.  
     b. Créez les deux enregistrements A (autrement dit, le nom de l’application et le nom du gestionnaire de contrôle des services [GCL]) avec l’adresse IP de votre point de terminaison privé.  
     > [!div class="mx-imgBorder"]
     > ![Capture d’écran des enregistrements de la zone privée DNS.][21]  

   - Utilisez le fichier *hosts* de la machine virtuelle.  

     a. Créez l’entrée hosts, ouvrez l’explorateur de fichiers et recherchez le fichier *hosts*.  
     > [!div class="mx-imgBorder"]
     > ![Capture d’écran montrant le fichier hosts dans l’explorateur de fichiers.][18]  
     b. Ajoutez une entrée qui contient l’adresse IP privée et le nom public de votre application web en modifiant le fichier *hosts* dans un éditeur de texte.  
     > [!div class="mx-imgBorder"]
     > ![Capture d’écran du texte du fichier hosts.][19]  
     c. Enregistrez le fichier.

1. Dans un navigateur, tapez l’URL de votre application web.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran d’un navigateur affichant une application web.][20]

Vous accédez maintenant à votre application web par le biais du point de terminaison privé.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous avez fini d’utiliser le point de terminaison privé, l’application web et la machine virtuelle, supprimez le groupe de ressources et toutes les ressources qu’il contient.

1. Dans le portail Azure, dans la zone **Rechercher**, entrez **ready-rg**, puis sélectionnez **ready-rg** dans la liste des résultats.

1. Sélectionnez **Supprimer le groupe de ressources**.

1. Sous **Tapez le nom du groupe de ressources**, entrez **ready-rg**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé une machine virtuelle sur un réseau virtuel ainsi qu’une application web et un point de terminaison privé. Vous vous êtes connecté à une machine virtuelle à partir d’Internet et avez communiqué de façon sécurisée avec l’application web par le biais d’Azure Private Link. 

Pour plus d’informations sur les points de terminaison privés, consultez [Qu’est-ce qu’Azure Private Endpoint ?][privateendpoint].

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtual-machine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png
[21]: ./media/create-private-endpoint-webapp-portal/dns-private-zone-records.png


<!--Links-->
[privateendpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
