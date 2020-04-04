---
title: Connexion privée à une application web avec Azure Private Endpoint
description: Connexion privée à une application web avec Azure Private Endpoint
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 2f10c7378ae7681b14df6e96b6a6f1adac832d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287813"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Connexion privée à une application web avec Azure Private Endpoint (préversion)

Azure Private Endpoint est le composant fondamental de Private Link dans Azure. Il vous permet de vous connecter en privé à votre application web.
Dans ce guide de démarrage rapide, vous allez apprendre à déployer une application web avec Private Endpoint et à vous connecter à cette application web à partir d’une machine virtuelle.

Pour plus d’informations, consultez [Utilisation de points de terminaison privés pour application web Azure][privatenedpointwebapp].

> [!Note]
>La préversion est disponible dans les régions USA Est et USA Ouest 2 pour toutes les éditions PremiumV2 Windows et Applications web Linux. 

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Réseau virtuel et machine virtuelle

Dans cette section, vous allez créer le réseau virtuel et le sous-réseau pour héberger la machine virtuelle qui est utilisée pour accéder à votre application web via le point de terminaison privé.

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel et un sous-réseau.

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel** ou recherchez **Réseau virtuel** dans la zone de recherche.

1. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet Général :

   > [!div class="mx-imgBorder"]
   > ![Créer un réseau virtuel][1]

1. Cliquez sur **Suivant : Adresses IP**, puis entrez ou sélectionnez les informations suivantes :

   > [!div class="mx-imgBorder"]
   >![Configurer les adresses IP][2]

1. Dans la section du sous-réseau, cliquez sur **+ Ajouter un sous-réseau** et entrez les informations suivantes, puis cliquez sur **Ajouter**.

   > [!div class="mx-imgBorder"]
   >![Ajouter un sous-réseau][3]

1. Cliquez sur **Vérifier + créer**.

1. Une fois la validation effectuée, cliquez sur **Créer**.

### <a name="create-virtual-machine"></a>Créer une machine virtuelle

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**.

1. Dans Créer une machine virtuelle - Notions de base, entrez ou sélectionnez ces informations :

   > [!div class="mx-imgBorder"]
   >![Paramètres de base de la machine virtuelle ][4]

1. Sélectionnez **Suivant : Disques**.

   Conservez les paramètres par défaut.

1. Sélectionnez **Suivant : Mise en réseau**, puis les informations suivantes :

   > [!div class="mx-imgBorder"]
   >![Mise en réseau ][5]

1. Cliquez sur **Vérifier + créer**.

1. Quand apparaît le message indiquant que la validation a été effectuée, cliquez sur **Créer**.

## <a name="create-your-web-app-and-private-endpoint"></a>Créer votre application web et votre point de terminaison privé

Dans cette section, vous allez créer une application web privée en y associant un point de terminaison privé.

> [!Note]
>La fonctionnalité de point de terminaison privé est disponible uniquement pour la référence SKU Premium v2.

### <a name="web-app"></a>Application web

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Web** > **Application web**.

1. Dans Créer une application web - Concepts de base, entrez ou sélectionnez les informations suivantes :

   > [!div class="mx-imgBorder"]
   >![Paramètres de base de l’application web ][6]

1. Sélectionnez **Vérifier + créer**.

1. Quand apparaît le message indiquant que la validation a été effectuée, cliquez sur **Créer**.

### <a name="create-the-private-endpoint"></a>Créer le point de terminaison privé

1. Dans les propriétés Application web, sélectionnez **Paramètres** > **Mise en réseau**, puis cliquez sur **Configurer vos connexions de point de terminaison privé**.

   > [!div class="mx-imgBorder"]
   >![Mise en réseau de l’application web][7]

1. Dans l’Assistant, cliquez sur **Ajouter**.

   > [!div class="mx-imgBorder"]
   >![Point de terminaison privé de l’application web][8]

1. Remplissez les informations relatives à l’abonnement, au réseau virtuel et au sous-réseau, puis cliquez sur **OK**

   > [!div class="mx-imgBorder"]
   >![Mise en réseau de l’application web][9]

1. Examinez la création du point de terminaison privé.

   > [!div class="mx-imgBorder"]
   >![Examen][10]
   >![Vue finale du point de terminaison privé][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Se connecter à une machine virtuelle à partir d’Internet

1. Dans la barre de recherche du portail, entrez **myVm**.
1. Sélectionnez le bouton **Connexion**. Après avoir sélectionné le bouton Connexion, Se connecter à la machine virtuelle s’ouvre ; sélectionnez **RDP**.

   > [!div class="mx-imgBorder"]
   >![Bouton RDP][12]

1. Azure crée un fichier de protocole RDP (Remote Desktop Protocol) (.rdp) et le télécharge sur votre ordinateur une fois que vous avez cliqué sur **Télécharger le fichier RDP**.

   > [!div class="mx-imgBorder"]
   >![Télécharger le fichier RDP][13]

1. Ouvrez le fichier downloaded.rdp.

- Si vous y êtes invité, sélectionnez Connexion.
- Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle.

> [!Note]
> Vous devrez peut-être sélectionner Plus de choix > Utiliser un autre compte pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle.

- Sélectionnez OK.

1. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez un avertissement de certificat, sélectionnez Oui ou Continuer.

1. Une fois que le bureau de la machine virtuelle s’affiche, réduisez-le pour revenir à votre poste de travail local.

## <a name="access-web-app-privately-from-the-vm"></a>Accéder à l’application web en privé à partir de la machine virtuelle

Dans cette section, vous allez vous connecter en privé à l’application web à l’aide du point de terminaison privé.

1. Récupérez l’adresse IP privée de votre point de terminaison privé, dans la barre de recherche, tapez **Liaison privée**, puis sélectionnez Liaison privée.

   > [!div class="mx-imgBorder"]
   >![Liaison privée][14]

1. Dans le Centre de liaisons privées, sélectionnez **Points de terminaison privés** pour lister tous vos points de terminaison privés.

   > [!div class="mx-imgBorder"]
   >![Centre de liaisons privées][15]

1. Sélectionnez le lien de point de terminaison privé vers votre application web et votre sous-réseau.

   > [!div class="mx-imgBorder"]
   >![Propriétés du point de terminaison privé][16]

1. Copiez l’adresse IP privée de votre point de terminaison privé et le nom de domaine complet de votre application web, en l’occurrence webappdemope.azurewebsites.net 10.10.2.4.

1. Sur la machine virtuelle myVM, vérifiez que l’application web n’est pas accessible par le biais de l’adresse IP publique. Ouvrez un navigateur et collez le nom de l’application web. Vous devez avoir une page d’erreur 403 – Interdit

   > [!div class="mx-imgBorder"]
   >![Interdit][17]

> [!Important]
> Cette fonctionnalité étant en préversion, vous devez gérer manuellement l’entrée DNS.

1. Créez l’entrée de l’hôte, ouvrez l’Explorateur de fichiers et recherchez le fichier hosts.

   > [!div class="mx-imgBorder"]
   >![Fichier hosts][18]

1. Ajoutez une entrée avec l’adresse IP privée et le nom public de votre application web en modifiant le fichier hosts à l’aide du Bloc-notes.

   > [!div class="mx-imgBorder"]
   >![Contenu du fichier hosts][19]

1. Enregistrez le fichier.

1. Ouvrez un navigateur et tapez l’URL de votre application web.

   > [!div class="mx-imgBorder"]
   >![Site web avec point de terminaison privé][20]

1. Vous accédez à votre application web via le point de terminaison privé.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous avez fini d’utiliser le point de terminaison privé, l’application web et la machine virtuelle, supprimez le groupe de ressources et toutes les ressources qu’il contient :

1. Entrez ready-rg dans la zone Recherche en haut du portail, puis sélectionnez ready-rg dans les résultats de la recherche.
1. Sélectionnez Supprimer le groupe de ressources.
1. Entrez ready-rg dans TAPER NOM DU GROUPE DE RESSOURCES, puis sélectionnez Supprimer.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une machine virtuelle sur un réseau virtuel ainsi qu’une application web et un point de terminaison privé. Vous vous êtes connecté à une machine virtuelle à partir d’Internet et avez communiqué de façon sécurisée avec l’application web via Azure Private Link. Pour plus d’informations sur les points de terminaison privés, consultez [Qu’est-ce qu’Azure Private Endpoint ?][privateendpoint].

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtualmachine.png
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

<!--Links-->
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
