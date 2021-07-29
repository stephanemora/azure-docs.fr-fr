---
title: 'Configurer des clients VPN pour les connexions P2S de protocole OpenVPN : authentification Azure AD : macOS : Préversion'
description: 'Préversion : Découvrez comment configurer un client VPN macOS pour qu’il se connecte à un réseau virtuel en utilisant un VPN point à site de passerelle et l’authentification Azure Active Directory.'
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/13/2021
ms.author: cherylmc
ms.openlocfilehash: defcc3c35cab51e9606151f11c37397dc8f4a06e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110104061"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections---macos---preview"></a>Authentification Azure Active Directory : Configurer un client VPN pour les connexions P2S de protocole OpenVPN - macOS - Préversion

Cet article vous aide à configurer un client VPN pour un ordinateur exécutant macOS 10.15 ou version ultérieure pour qu’il se connecte à un réseau virtuel à l’aide d’un VPN point à site et de l’authentification Azure Active Directory. Avant de pouvoir vous connecter et vous authentifier à l’aide d’Azure AD, vous devez d’abord configurer votre locataire Azure AD. Pour plus d’informations, consultez [Configurer un locataire Azure AD](openvpn-azure-ad-tenant.md). Pour plus d’informations sur les connexions point à site, consultez [À propos des connexions point à site](point-to-site-about.md).

> [!NOTE]
> * Cette préversion est actuellement disponible dans la plupart des pays (mais pas tous), en raison des exigences réglementaires locales.
> * L’authentification Azure AD est prise en charge uniquement pour les connexions de protocole OpenVPN® et nécessite le client VPN Azure.
>

Pour chaque ordinateur que vous souhaitez connecter à un réseau virtuel à l’aide d’une connexion VPN de point à site, vous devez effectuer les opérations suivantes :
 
* Téléchargez Azure VPN Client sur l’ordinateur.
* Configurez un profil client qui contient les paramètres VPN. 

Si vous souhaitez configurer plusieurs ordinateurs, vous pouvez créer un profil client sur un ordinateur, l’exporter, puis l’importer sur les autres ordinateurs.

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir vous connecter et vous authentifier à l’aide d’Azure AD, vous devez d’abord configurer votre locataire Azure AD. Pour plus d’informations, consultez [Configurer un locataire Azure AD](openvpn-azure-ad-tenant.md).

## <a name="to-download-the-azure-vpn-client"></a><a name="download"></a>Pour télécharger le client VPN Azure

1. Téléchargez [Azure VPN Client](https://apps.apple.com/us/app/azure-vpn-client/id1553936137) à partir de l’App Store.
1. Installez le client sur votre ordinateur.

## <a name="to-import-a-connection-profile"></a><a name="import"></a>Pour importer un profil de connexion

1. Téléchargez et extrayez les fichiers de profil. Pour connaître les étapes à suivre, consultez [Utilisation des fichiers de profil client VPN](about-vpn-profile-download.md).
1. Sur la page Azure VPN Client, sélectionnez **Importer**.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-1.png" alt-text="Capture d’écran de la sélection de l’importation Azure VPN Client.":::
1. Accédez au fichier de profil que vous souhaitez importer, sélectionnez-le, puis cliquez sur **Ouvrir**.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-2.png" alt-text="Capture d’écran de l’ouverture de l’importation Azure VPN Client.":::
1. Affichez les informations de profil de connexion, puis cliquez sur **Enregistrer**.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-3.png" alt-text="Capture d’écran d’Azure VPN Client enregistrant les paramètres du profil importé.":::
1. Dans le volet Connexions VPN, sélectionnez le profil de connexion que vous avez enregistré. Ensuite, cliquez sur **Se connecter**.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-4.png" alt-text="Capture d’écran d’Azure VPN Client après avoir cliqué sur Se connecter.":::
1. Après connexion, l’état passe à **Connecté**. Pour vous déconnecter de la session, cliquez sur **Se déconnecter**.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-5.png" alt-text="Capture d’écran de l’état Connecté d’Azure VPN Client et du bouton Se déconnecter.":::

## <a name="to-create-a-connection-manually"></a><a name="manual"></a>Pour créer une connexion manuellement

1. Ouvrez Azure VPN Client. Sélectionnez **Ajouter** pour créer une nouvelle connexion.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-1.png" alt-text="Capture d’écran d’Azure VPN Client lors de la sélection du bouton Ajouter.":::

1. Sur la page **Azure VPN Client**, vous pouvez configurer les paramètres de profil.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-2.png" alt-text="Capture d’écran des paramètres de profil d’Azure VPN Client.":::

   Configurez les paramètres suivants :

   * **Nom de la connexion** : nom par lequel vous souhaitez faire référence à ce profil de connexion.
   * **Serveur VPN :** Ce nom est le nom que vous voulez utiliser pour faire référence au serveur. Le nom que vous choisissez ici n’a pas besoin d’être le nom formel d’un serveur.
   * **Validation du serveur**
     * **Informations sur le certificat :** Autorité de certification de certificat.
     * **Secret du serveur :** Clé secrète du serveur.
   * **Authentification du client**
     * **Type d’authentification :** Azure Active Directory
     * **Client :** Nom du locataire.
     * **Émetteur :** Nom de l’émetteur.
1. Après avoir renseigné les champs, cliquez sur **Enregistrer**.
1. Dans le volet Connexions VPN, sélectionnez le profil de connexion que vous avez configuré. Ensuite, cliquez sur **Se connecter**.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-3.png" alt-text="Capture d’écran de la connexion d’Azure VPN Client.":::
1. À l’aide de vos informations d’identification, connectez-vous.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-4.png" alt-text="Capture d’écran de la connexion à Azure VPN Client.":::
1. Après connexion, vous verrez l’état **Connecté**. Lorsque vous souhaitez vous déconnecter, cliquez sur **Se déconnecter** pour couper la connexion.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-5.png" alt-text="Capture d’écran de l’état Connecté et du bouton Se déconnecter d’Azure VPN Client.":::

## <a name="to-remove-a-connection-profile"></a><a name="remove"></a>Pour supprimer un profil de connexion

Vous pouvez supprimer le profil de connexion VPN de votre ordinateur. 

1. Accédez à Azure VPN Client.
1. Sélectionnez la connexion VPN que vous souhaitez supprimer, cliquez sur la liste déroulante, puis sélectionnez **Supprimer**.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/remove-1.png" alt-text="Capture d’écran de suppression.":::
1. Dans la zone **Supprimer la connexion VPN ?** , cliquez sur **Supprimer**.
   :::image type="content" source="media/openvpn-azure-ad-client-mac/remove-2.png" alt-text="Capture d’écran de la suppression.":::

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Créer un locataire Azure Active Directory qui utilise l’authentification Azure AD pour les connexions Open VPN P2S](openvpn-azure-ad-tenant.md).