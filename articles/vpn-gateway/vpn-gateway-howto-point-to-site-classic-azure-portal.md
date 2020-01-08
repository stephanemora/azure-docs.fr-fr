---
title: "Connecter un ordinateur à un réseau virtuel à l'aide d'une connexion point à site et d'une authentification par certificat : Portail Azure classique | Microsoft Docs"
description: Créez une connexion de passerelle VPN point à site classique par le biais du portail Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 01327d24aebee02c3b14594c2b0b2f2f175211fd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450794"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Configurer une connexion point à site à l'aide d'une authentification par certificat (classique)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Cet article explique comment créer un réseau virtuel avec une connexion point à site. Pour créer ce réseau virtuel, vous devez utiliser le modèle de déploiement classique et le portail Azure. Cette configuration utilise des certificats pour authentifier le client qui se connecte, qu’ils soient auto-signés ou délivrés par une autorité de certification. Vous pouvez également créer cette configuration avec un outil ou un modèle de déploiement différent en utilisant les options décrites dans les articles suivants :

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portail Azure (classique)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Vous utilisez une passerelle VPN point à site (P2S) pour créer une connexion sécurisée à votre réseau virtuel à partir d'un ordinateur client individuel. Les connexions VPN point à site vous permettent de vous connecter à votre réseau virtuel à partir d'un emplacement distant. Lorsqu'un nombre restreint de clients doit se connecter à un réseau virtuel, l'utilisation d'un VPN P2S est une solution utile qui constitue une alternative au VPN site à site. Une connexion VPN P2S est établie en étant démarrée à partir de l’ordinateur du client.

> [!IMPORTANT]
> Le modèle de déploiement classique prend en charge les clients VPN Windows uniquement et utilise le protocole de tunneling Secure Socket (SSTP), un protocole VPN basé sur SSL. Pour prendre en charge des clients VPN non Windows, vous devez créer votre réseau virtuel à l'aide du modèle de déploiement Resource Manager. Le modèle de déploiement Azure Resource Manager prend en charge les VPN IKEv2, en plus de SSTP. Pour plus d'informations, consultez [À propos des connexions P2S](point-to-site-about.md).
>
>

![Diagramme point à site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Conditions préalables requises

Les connexions d'authentification par certificat point à site requièrent les éléments suivants :

* Une passerelle VPN dynamique.
* La clé publique (fichier .cer) d’un certificat racine, chargée sur Azure. Cette clé est considérée comme un certificat approuvé et est utilisée pour l'authentification.
* Un certificat client généré à partir du certificat racine et installé sur chaque ordinateur client qui se connecte. Ce certificat est utilisé pour l’authentification du client.
* Un package de configuration du client VPN doit être généré et installé sur chaque ordinateur client qui se connecte. Le package de configuration du client configure le client VPN natif qui se trouve déjà sur le système d'exploitation en lui fournissant les informations nécessaires à la connexion au réseau virtuel.

Les connexions point à site ne nécessitent pas de périphérique VPN ou d'adresse IP publique locale. La connexion VPN est créée sur le protocole SSTP (Secure Socket Tunneling Protocol). Côté serveur, nous prenons en charge SSTP, versions 1.0, 1.1 et 1.2. Le client détermine la version à utiliser. Pour Windows 8.1 et supérieur, SSTP utilise la version 1.2 par défaut. 

Pour plus d'informations sur les connexions point à site, consultez le [Forum Aux Questions sur les connexions point à site](#point-to-site-faq).

### <a name="example-settings"></a>Exemples de paramètres

Utilisez les valeurs suivantes pour créer un environnement de test, ou reportez-vous à celles-ci pour mieux comprendre les exemples fournis dans cet article :

- **Créer des paramètres de réseau virtuel (classique)**
   - **Name** : entrez *VNet1*.
   - **Espace d’adressage** : entrez *192.168.0.0/16*. Pour cet exemple, nous n’utilisons qu’un seul espace d’adressage. Vous pouvez avoir plusieurs espaces d’adressage pour votre réseau virtuel, comme indiqué sur le diagramme.
   - **Nom du sous-réseau** : Entrez *FrontEnd*.
   - **Plage d’adresses de sous-réseau** : entrez *192.168.1.0/24*.
   - **Abonnement**: Sélectionnez un abonnement dans la liste des abonnements disponibles.
   - **Groupe de ressources** : entrez *TestRG*. Sélectionnez **Créer** si le groupe de ressources n'existe pas.
   - **Emplacement** : sélectionnez **USA Est** dans la liste.

  - **Paramètres de connexion VPN**
    - **Type de connexion** : sélectionnez **Point à site**.
    - **Espace d'adressage du client** : entrez *172.16.201.0/24*. Les clients VPN qui se connectent au réseau virtuel à l'aide de cette connexion point à site reçoivent une adresse IP de ce pool.

- **Paramètres de sous-réseau de configuration de passerelle**
   - **Name** : automatiquement renseigné avec le nom *GatewaySubnet*.
   - **Plage d’adresses** : entrez *192.168.200.0/24*. 

- **Paramètres de configuration de la passerelle** :
   - **Size** : sélectionnez la référence SKU de la passerelle que vous souhaitez utiliser.
   - **Type de routage** : sélectionnez **Dynamique**.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>créer un réseau virtuel et une passerelle VPN ;

Avant de commencer, vérifiez que vous disposez d'un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Première partie : Créez un réseau virtuel

Si vous n'avez pas de réseau virtuel, créez-en un. Les captures d’écran sont fournies à titre d’exemple. Assurez-vous de remplacer ces valeurs par les vôtres. Pour créer un réseau virtuel à l’aide du portail Azure, procédez comme suit :

1. Dans le menu du [Portail Azure](https://portal.azure.com) ou dans la page **Accueil**, sélectionnez **Créer une ressource**. La page **Nouveau** s’ouvre.

2. Dans le champ **Rechercher dans la Place de marché**, entrez *réseau virtuel* et sélectionnez **Réseau virtuel** dans la liste retournée. La page **Réseau virtuel** s’ouvre.

3. Dans la liste **Sélectionner un modèle de déploiement**, sélectionnez **Classique**, puis **Créer**. La page **Créer un réseau virtuel** s’ouvre.

4. Sur la page **Créer un réseau virtuel**, configurez les paramètres du réseau virtuel. Sur cette page, vous ajoutez votre premier espace d’adressage et une plage d’adresses de sous-réseau unique. Après avoir créé le réseau virtuel, vous pouvez revenir en arrière et ajouter des espaces d’adressage et des sous-réseaux supplémentaires.

   ![Créer une page Réseau virtuel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. Sélectionnez l'**abonnement**  que vous souhaitez utiliser dans la liste déroulante.

6. Sélectionnez un **groupe de ressources** existant. Ou créez un groupe de ressources en sélectionnant **Créer** et en entrant un nom. Si vous créez un groupe de ressources, nommez-le en fonction de vos valeurs de configuration planifiées. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/management/overview.md#resource-groups).

7. Sélectionnez l'**emplacement** de votre réseau virtuel. Ce paramètre détermine l'emplacement géographique des ressources que vous déployez sur ce réseau virtuel.

8. Sélectionnez **Créer** pour créer le réseau virtuel. Le message **Déploiement en cours** s'affiche sur la page **Notifications**.

8. Une fois votre réseau virtuel créé, le message **Déploiement réussi** s'affiche sur la page **Notifications**. Sélectionnez **Épingler au tableau de bord** pour avoir facilement accès à votre réseau virtuel sur le tableau de bord. 

10. Ajoutez un serveur DNS (facultatif). Après avoir créé votre réseau virtuel, vous pouvez ajouter l’adresse IP d’un serveur DNS pour la résolution de noms. L’adresse IP du serveur DNS que vous spécifiez doit être celle d’un serveur DNS capable de résoudre les noms des ressources de votre réseau virtuel.

    Pour ajouter un serveur DNS, sélectionnez **Serveurs DNS** sur la page de votre réseau virtuel. Puis entrez l'adresse IP du serveur DNS que vous souhaitez utiliser et sélectionnez **Enregistrer**.

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>Deuxième partie : création d'un sous-réseau de passerelle et d'une passerelle de routage dynamique

Vous allez maintenant créer un sous-réseau de passerelle et une passerelle de routage dynamique. Sur le portail Azure, pour le modèle de déploiement classique, la création du sous-réseau de passerelle et de la passerelle s'effectue sur les mêmes pages de configuration. Utilisez le sous-réseau de passerelle pour les services de passerelle uniquement. Ne déployez jamais rien directement sur le sous-réseau de passerelle (comme des machines virtuelles ou d’autres services).

1. Sur le portail Azure, accédez au réseau virtuel pour lequel vous souhaitez créer une passerelle.

2. Sur la page de votre réseau virtuel, sélectionnez **Vue d'ensemble**, puis dans la section **Connexions VPN**, sélectionnez **Passerelle**.

   ![Sélectionner pour créer une passerelle](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Sur la page **Nouvelle connexion VPN**, sélectionnez **Point à site**.

   ![Type de connexion de point à site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Sous **Espace d'adressage client**, ajoutez la plage d'adresses IP à partir de laquelle les clients VPN reçoivent une adresse IP lorsqu'ils se connectent. Utilisez une plage d'adresses IP privées qui ne chevauche ni l'emplacement local à partir duquel vous vous connectez ni le réseau virtuel auquel vous vous connectez. Vous pouvez remplacer la plage renseignée automatiquement avec la plage d'adresses IP privées que vous souhaitez utiliser. Cet exemple illustre la plage renseignée automatiquement. 

   ![Espace d’adressage du client](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Sélectionnez **Créer une passerelle immédiatement**, puis **Configuration de passerelle facultative** pour ouvrir la page **Configuration de la passerelle**.

   ![Sélectionnez Configuration de passerelle facultative](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. Sur la page **Configuration de la passerelle**, sélectionnez **Sous-réseau** pour ajouter le sous-réseau de passerelle. Il est possible de créer un sous-réseau de passerelle aussi petit que/29. Néanmoins, nous vous recommandons de créer un sous-réseau plus vaste qui inclut un plus grand nombre d'adresses en sélectionnant au moins /28 ou /27. Cela permettra à un nombre suffisant d'adresses de s'adapter aux éventuelles configurations supplémentaires que vous pourriez souhaiter ajouter par la suite. Lorsque vous travaillez avec des sous-réseaux de passerelle, évitez d’associer un groupe de sécurité réseau (NSG) au sous-réseau de passerelle. Si vous associez un groupe de sécurité réseau à ce sous-réseau, votre passerelle VPN cessera peut-être de fonctionner normalement. Sélectionnez **OK** pour enregistrer ce paramètre.

   ![Ajouter le sous-réseau de passerelle](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Sélectionnez la **taille** de la passerelle. La taille correspond à la référence SKU de votre passerelle de réseau virtuel. Sur le portail Azure, la référence SKU par défaut est **Par défaut**. Pour plus d'informations sur les références SKU de passerelle, consultez [À propos des paramètres de la passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

   ![Taille de la passerelle](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Sélectionnez le **type de routage** pour votre passerelle. Les configurations P2S nécessitent un type de routage **dynamique**. Sélectionnez **OK** lorsque vous avez terminé la configuration de cette page.

   ![Configurer le type de routage](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. Sur la page **Nouvelle connexion VPN**, sélectionnez **OK** en bas de la page pour commencer à créer votre passerelle de réseau virtuel. L'achèvement d'une passerelle VPN peut prendre jusqu'à 45 minutes en fonction de la référence SKU de passerelle que vous sélectionnez.
 
## <a name="generatecerts"></a>Créer des certificats

Azure utilise des certificats afin d'authentifier les clients VPN pour les VPN point à site. Vous chargez les informations de la clé publique du certificat racine vers Azure. La clé publique est alors considérée comme *approuvée*. Les certificats clients doivent être générés à partir du certificat racine approuvé, puis installés sur chaque ordinateur client dans le magasin de certificats Certificates-Current User\Personal\Certificates. Le certificat permet d'authentifier le client lorsqu'il se connecte au réseau virtuel. 

Si vous utilisez des certificats auto-signés, ceux-ci doivent être créés à l'aide de paramètres spécifiques. Vous pouvez créer un certificat auto-signé en suivant les instructions relatives à [PowerShell et Windows 10](vpn-gateway-certificates-point-to-site.md) ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Il est important de suivre les étapes décrites dans ces instructions lorsque vous utilisez des certificats racines auto-signés et que vous générez des certificats clients à partir du certificat racine auto-signé. Dans le cas contraire, les certificats que vous créez ne seront pas compatibles avec les connexions P2S, ce qui entraînera une erreur de connexion.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Acquérir la clé publique (.cer) pour le certificat racine

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Générer un certificat client

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Charger le fichier .cer de certificat racine

Une fois la passerelle créée, chargez le fichier .cer (qui contient les informations de clé publique) d'un certificat racine approuvé sur le serveur Azure. Ne chargez pas la clé privée du certificat racine. Une fois le certificat chargé, Azure l'utilise pour authentifier les clients qui ont installé un certificat client généré à partir du certificat racine approuvé. Si nécessaire, vous pourrez ultérieurement charger d'autres fichiers de certificat racine approuvé (20 maximum).  

1. Dans la section **Connexions VPN** de la page de votre réseau virtuel, sélectionnez le graphique des clients pour ouvrir la page **Connexion VPN point à site**.

   ![Clients](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Sur la page **Connexion VPN de point à site**, sélectionnez **Gérer le certificat** pour ouvrir la page **Certificats**.

   ![Page Certificats](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. Sur la page **Certificats**, sélectionnez **Charger** pour ouvrir la page **Charger un certificat**.

    ![Page Télécharger des certificats](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Sélectionnez le graphique du dossier pour rechercher le fichier .cer. Sélectionnez le fichier, puis **OK**. Le certificat chargé apparaît sur la page **Certificats**.

   ![Téléchargement d’un certificat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>Configurer le client

Pour se connecter à un réseau virtuel à l'aide d'un VPN point à site, chaque client doit installer un package afin de configurer le client VPN Windows natif. Le package de configuration configure le client VPN Windows natif avec les paramètres nécessaires pour se connecter au réseau virtuel.

Vous pouvez utiliser le même package de configuration du client VPN sur chaque ordinateur client, tant que la version correspond à l’architecture du client. Pour obtenir la liste des systèmes d'exploitation clients pris en charge, consultez la section [Forum Aux Questions sur les connexions point à site](#point-to-site-faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Générer et installer le package de configuration du client VPN

1. Sur le portail Azure, accédez à la page **Vue d'ensemble** de votre réseau virtuel, puis dans **Connexions VPN**, sélectionnez le graphique de client pour ouvrir la page **Connexion VPN point à site**.

2. Sur la page **Connexion VPN point à site**, sélectionnez le package de téléchargement correspondant au système d'exploitation client sur lequel il sera installé :

   * Pour les clients 64 bits, sélectionnez **Client VPN (64 bits)** .
   * Pour les clients 32 bits, sélectionnez **Client VPN (32 bits)** .

   ![Charger le package de configuration du client VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. Une fois le package généré, téléchargez-le et installez-le sur votre ordinateur client. Si une fenêtre contextuelle SmartScreen s'affiche, sélectionnez **Plus d'infos**, puis **Exécuter quand même**. Vous pouvez également enregistrer le package pour l’installer sur d’autres ordinateurs clients.

### <a name="install-a-client-certificate"></a>Installer un certificat client

Pour créer une connexion P2S à partir d'un ordinateur client autre que celui utilisé pour générer les certificats clients, installez un certificat client. Pour installer un certificat client, vous devez disposer du mot de passe créé lors de l'exportation du certificat client. En règle générale, il vous suffit de double-cliquer sur le certificat pour l'installer. Pour plus d’informations, consultez la rubrique [Installer un certificat client exporté](vpn-gateway-certificates-point-to-site.md#install).


## <a name="connect-to-your-vnet"></a>Se connecter à votre réseau virtuel

>[!NOTE]
>Vous devez disposer de droits d’administrateur sur l’ordinateur client à partir duquel vous vous connectez.
>
>

1. Pour vous connecter à votre réseau virtuel, sur l'ordinateur client, accédez aux **connexions VPN** sur le Portail Azure et recherchez celle que vous avez créée. La connexion VPN porte le même nom que votre réseau virtuel. Sélectionnez **Connecter**. Si un message contextuel relatif au certificat s'affiche, sélectionnez **Continuer** pour utiliser des privilèges élevés.

2. Sur la page d'état **Connexion**, sélectionnez **Connecter** pour établir la connexion. Si l'écran **Sélectionner un certificat** apparaît, vérifiez que le certificat client affiché est le bon. Si ce n'est pas le cas, sélectionnez le bon certificat dans la liste déroulante, puis cliquez sur **OK**.

3. Si votre connexion aboutit, la notification **Connecté** apparaît.


### <a name="troubleshooting-p2s-connections"></a>Résolution des problèmes liés aux connexions P2S

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Vérifier la connexion VPN

1. Vérifiez que votre connexion VPN est active. Ouvrez une invite de commandes avec élévation de privilèges sur votre ordinateur client et exécutez **ipconfig/all**.
2. Affichez les résultats. Notez que l’adresse IP que vous avez reçue est l’une des adresses de la plage d’adresses de connectivité point à site que vous avez spécifiée quand vous avez créé votre réseau virtuel. Les résultats devraient être semblables à cet exemple :

   ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
   ```

## <a name="connect-to-a-virtual-machine"></a>Connexion à une machine virtuelle

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add-or-remove-trusted-root-certificates"></a>Ajout ou suppression de certificats racine approuvés

Vous pouvez ajouter et supprimer des certificats racines approuvés à partir d'Azure. Lorsque vous supprimez un certificat racine, les clients qui possèdent un certificat généré à partir de cette racine ne sont plus en mesure de s'authentifier et de se connecter. Pour permettre à ces clients de s'authentifier et de se connecter, vous devez installer un nouveau certificat client généré à partir d'un certificat racine approuvé par Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Ajout d’un certificat racine approuvé

Vous pouvez ajouter jusqu’à 20 fichiers .cer de certificat racine approuvés dans Azure. Pour obtenir des instructions, consultez Charger le fichier de certificat racine .cer.

### <a name="to-remove-a-trusted-root-certificate"></a>Suppression d’un certificat racine approuvé

1. Dans la section **Connexions VPN** de la page de votre réseau virtuel, sélectionnez le graphique des clients pour ouvrir la page **Connexion VPN point à site**.

   ![Clients](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Sur la page **Connexion VPN de point à site**, sélectionnez **Gérer le certificat** pour ouvrir la page **Certificats**.

   ![Page Certificats](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. Sur la page **Certificats**, sélectionnez le bouton de sélection correspondant au certificat que vous souhaitez supprimer, puis sélectionnez **Supprimer**.

   ![Suppression d'un certificat racine](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Révocation d'un certificat client

Si nécessaire, vous pouvez révoquer un certificat client. La liste de révocation de certificat vous permet de refuser sélectivement la connexion point à site en fonction des certificats clients individuels. Cette méthode est différente de la suppression d'un certificat racine approuvé. Si vous supprimez un fichier .cer de certificat racine approuvé d’Azure, vous révoquez l’accès pour tous les certificats clients générés/signés par le certificat racine révoqué. Révoquer un certificat client plutôt que le certificat racine permet de continuer à utiliser les autres certificats générés à partir du certificat racine pour l’authentification de la connexion Point à site.

La pratique courante consiste à utiliser le certificat racine pour gérer l'accès au niveaux de l'équipe ou de l'organisation, tout en utilisant des certificats clients révoqués pour le contrôle d'accès précis des utilisateurs individuels.

### <a name="to-revoke-a-client-certificate"></a>Révocation d'un certificat client

Vous pouvez révoquer un certificat client en ajoutant son empreinte à la liste de révocation.

1. Récupérez l’empreinte du certificat client. Pour plus d’informations, consultez [Procédure : Récupérer l'empreinte d'un certificat](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copiez les informations dans un éditeur de texte et supprimez les espaces afin d'obtenir une chaîne continue.
3. Accédez au réseau virtuel classique. Sélectionnez **Connexion VPN de point à site**, puis **Gérer le certificat** pour ouvrir la page **Certificats**.
4. Sélectionnez **Liste de révocation** pour ouvrir la page **Liste de révocation**. 
5. Sélectionnez **Ajouter un certificat** pour ouvrir la page **Ajouter un certificat à la liste de révocation**.
6. Sous **Empreinte**, collez l'empreinte numérique du certificat sous la forme d'une seule ligne continue de texte, sans espaces. Sélectionnez **OK** pour terminer.

Une fois la mise à jour terminée, le certificat ne peut plus être utilisé pour se connecter. Les clients qui tentent de se connecter à l'aide de ce certificat reçoivent un message indiquant que le certificat n'est plus valide.

## <a name="point-to-site-faq"></a>Forum Aux Questions sur les connexions point à site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Étapes suivantes

- Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez [Machines virtuelles](https://docs.microsoft.com/azure/). 

- Pour plus d'informations sur la mise en réseau et les machines virtuelles Linux, consultez [Vue d'ensemble du réseau de machines virtuelles Azure et Linux](../virtual-machines/linux/network-overview.md).

- Pour plus d’informations sur la résolution des problèmes liés aux connexions point à site, consultez l’article [Résolution des problèmes de connexion de point à site Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
