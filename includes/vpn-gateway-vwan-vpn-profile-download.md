---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/13/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1d14ee849c89e6c3807636d0a728157abd9de97a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650665"
---
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
* OpenVPN (si vous avez activé les paramètres d’authentification OpenVPN et Azure AD sur la passerelle). Pour la passerelle VPN, consultez [Créer un locataire](../articles/vpn-gateway/openvpn-azure-ad-tenant.md). Pour Virtual WAN, consultez [Créer un locataire - VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

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

* Le **dossier générique** contient le certificat de serveur public et le fichier VpnSettings.xml. Le fichier VpnSettings.xml contient les informations nécessaires à la configuration d’un client générique.

* Le fichier zip téléchargé peut également contenir des dossiers **WindowsAmd64** et **WindowsX86**. Ces dossiers contiennent le programme d’installation de SSTP et IKEv2 pour les clients Windows. Vous avez besoin de droits d’administrateur sur le client pour les installer.
