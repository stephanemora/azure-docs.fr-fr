---
title: 'Passerelle VPN Azure : À propos des profils clients VPN P2S'
description: Cela vous aide à utiliser le fichier de profil client.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 5386cace7191be60534f0d2fbf4a85b592d1ecdd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151621"
---
# <a name="about-p2s-vpn-client-profiles"></a>À propos des profils clients VPN P2S

Le fichier de profil téléchargé contient les informations nécessaires à la configuration d’une connexion VPN. Cet article vous aide à obtenir et à comprendre les informations nécessaires pour un profil client VPN.

## <a name="1-download-the-file"></a>1. Télécharger le fichier

Exécutez les commandes suivantes : Copiez l’URL du résultat dans votre navigateur pour télécharger le fichier zip du profil.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Extrayez le fichier zip.

Extrayez le fichier zip. Le fichier contient les dossiers suivants :

* AzureVPN
* Générique
* OpenVPN (si vous avez activé les paramètres d’authentification OpenVPN et Azure AD sur la passerelle. Voir [Créer un locataire](openvpn-azure-ad-tenant.md).)

## <a name="3-retrieve-information"></a>3. Récupérer des informations

Dans le dossier **AzureVPN**, accédez au fichier ***azurevpnconfig.xml*** et ouvrez-le avec le Bloc-notes. Prenez note du texte entre les balises suivantes.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Détails du profil

Lorsque vous ajoutez une connexion, utilisez les informations que vous avez collectées à l’étape précédente pour la page Détails du profil. Les champs correspondent aux informations suivantes :

   * **Audience** : identifie la ressource de destination du jeton.
   * **Émetteur** : identifie le service d’émission de jeton de sécurité (STS) qui a émis le jeton ainsi que le locataire Azure AD
   * **Locataire :** contient un identificateur unique non modifiable du client du répertoire qui a émis le jeton.
   * **FQDN :** nom de domaine complet (FQDN) sur la passerelle VPN Azure
   * **ServerSecret :** clé prépartagée de la passerelle VPN

## <a name="folder-contents"></a>Contenu du dossier

* Le dossier **OpenVPN** contient le profil *ovpn* qui doit être modifié pour inclure la clé et le certificat. Pour plus d’informations, consultez [Configurer des clients OpenVPN pour la passerelle VPN Azure](vpn-gateway-howto-openvpn-clients.md#windows).

* Le **dossier générique** contient le certificat de serveur public et le fichier VpnSettings.xml. Le fichier VpnSettings.xml contient les informations nécessaires à la configuration d’un client générique.

* Le fichier zip téléchargé peut également contenir des dossiers **WindowsAmd64** et **WindowsX86**. Ces dossiers contiennent le programme d’installation de SSTP et IKEv2 pour les clients Windows. Vous avez besoin de droits d’administrateur sur le client pour les installer.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la fonctionnalité point à site, consultez l’article [À propos de la fonctionnalité point à site](point-to-site-about.md).
