---
title: 'Configurer un client VPN pour les connexions VPN P2S : Authentification Azure AD | Microsoft Docs'
description: Vous pouvez utiliser un VPN P2S pour vous connecter à votre réseau virtuel à l'aide de l'authentification Azure AD
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: cherylmc
ms.openlocfilehash: 704dcd6335766a6058de4e520b8dcbca0d304c9d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721475"
---
# <a name="configure-a-vpn-client-for-p2s-vpn-connections-azure-ad-authentication-preview"></a>Configurer un client VPN pour les connexions VPN P2S : Authentification Azure AD (préversion)

Cet article vous aide à configurer un client VPN pour qu’il se connecte à un réseau virtuel à l’aide d’un VPN point à site et de l’authentification Azure Active Directory. Avant de pouvoir vous connecter et vous authentifier à l’aide d’Azure AD, vous devez d’abord configurer votre locataire Azure AD. Pour plus d’informations, consultez [Configurer un locataire Azure AD](openvpn-azure-ad-tenant.md).

## <a name="profile"></a>Utilisation de profils clients

Pour vous connecter, vous devez [télécharger](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) le client Azure VPN et configurer un profil de client VPN sur chaque ordinateur qui veut se connecter au réseau virtuel. Vous pouvez créer un profil client sur un ordinateur, l’exporter, puis l’importer sur des ordinateurs supplémentaires.

### <a name="cert"></a>Pour créer un profil client basé sur un certificat

Lorsque vous utilisez un profil basé sur un certificat, assurez-vous que les certificats appropriés sont installés sur l’ordinateur client. Pour plus d’informations sur les certificats, consultez [Installer un certificat client](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="radius"></a>Pour créer un profil client RADIUS

  ![radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)

### <a name="export"></a>Pour exporter et distribuer un profil client

Une fois que vous disposez d’un profil fonctionnel et que vous devez le distribuer à d’autres utilisateurs, vous pouvez l’exporter en procédant comme suit :

1. Mettez en surbrillance le profil client VPN que vous souhaitez exporter, sélectionnez les points de sélection ( **...** ), puis sélectionnez **Exporter**.

    ![export](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Sélectionnez l’emplacement dans lequel vous souhaitez enregistrer ce profil, laissez le nom de fichier tel quel, puis sélectionnez **Enregistrer** pour enregistrer le fichier XML.

    ![export](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="import"></a>Pour importer un profil client

1. Dans la page, sélectionnez **Importer**.

    ![importation](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Accédez au fichier XML de profil et sélectionnez-le. Ensuite, sélectionnez **Ouvrir**.

    ![importation](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Spécifiez le nom du profil et sélectionnez **Enregistrer**.

    ![importation](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Sélectionnez **Se connecter** pour vous connecter au VPN.

    ![importation](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Une fois que vous êtes connecté, l’icône devient verte et indique **Connecté**.

    ![importation](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="delete"></a>Pour supprimer un profil client

1. Sélectionnez les points de suspension en regard du profil client que vous souhaitez supprimer. Sélectionnez ensuite **Supprimer**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Sélectionnez **Supprimer** pour effectuer la suppression.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="connection"></a>Créer une connexion

1. Sur la page, sélectionnez **+** , puis **+ Ajouter**.

    ![connection](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Remplissez les informations de connexion. Si vous ne connaissez pas les valeurs, contactez votre administrateur. Quand vous avez rempli tous les champs, sélectionnez **Enregistrer**.

    ![connection](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Sélectionnez **Se connecter** pour vous connecter au VPN.

    ![connection](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Sélectionnez les informations d’identification appropriées, puis sélectionnez **Continuer**.

    ![connection](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Une fois que vous êtes connecté, l’icône devient verte et indique **Connecté**.

    ![connection](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="autoconnect"></a>Pour se connecter automatiquement

Ces étapes vous aident à configurer votre connexion pour qu’elle se connecte automatiquement avec Always-on.

1. Sur la page d’accueil de votre client VPN, sélectionnez **Paramètres VPN**.

    ![auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Sélectionnez **Oui** dans la boîte de dialogue de commutateur d’applications.

    ![auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Vérifiez que la connexion que vous souhaitez définir n’est pas déjà établie, puis mettez en surbrillance le profil et activez la case à cocher **Se connecter automatiquement**.

    ![auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Sélectionnez **Se connecter** pour initier la connexion VPN.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose"></a>Diagnostiquer les problèmes de connexion

1. Pour diagnostiquer les problèmes de connexion, vous pouvez utiliser l’outil **Diagnostiquer**. Sélectionnez les points de suspension ( **...** ) en regard de la connexion VPN que vous souhaitez diagnostiquer pour afficher le menu. Sélectionnez ensuite **Diagnostiquer**.

    ![diagnose](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Sur la page **Propriétés de connexion**, sélectionnez **Exécuter le diagnostic**.

    ![diagnose](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Connectez-vous avec vos informations d’identification.

    ![diagnose](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Affichez les résultats du diagnostic.

    ![diagnose](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Créer un locataire Azure Active Directory qui utilise l’authentification Azure AD pour les connexions Open VPN P2S](openvpn-azure-ad-tenant.md).
