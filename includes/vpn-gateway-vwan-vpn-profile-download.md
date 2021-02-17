---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/08/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f3eb2d9469ab3a3d2c1d09e4adc3ee2cb1f86e6e
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979065"
---
## <a name="extract-the-zip-file"></a>Extrayez le fichier zip.

Extrayez le fichier zip. Le fichier contient les dossiers suivants :

* AzureVPN
* Générique
* OpenVPN (si vous avez activé OpenVPN avec les paramètres **Certificat Azure** ou **Authentification RADIUS** sur la passerelle). Sélectionnez l’article approprié correspondant à votre configuration pour créer un locataire.

  * [Passerelle VPN – Créer un locataire](../articles/vpn-gateway/openvpn-azure-ad-tenant.md).
  * [Virtual WAN – Créer un locataire](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="retrieve-information"></a>Récupérer des informations

Dans le dossier **AzureVPN**, accédez au fichier **_azurevpnconfig.xml_** et ouvrez-le avec le Bloc-notes. Prenez note du texte entre les balises suivantes.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Détails du profil

Lorsque vous ajoutez une connexion, utilisez les informations que vous avez collectées à l’étape précédente pour la page Détails du profil. Les champs correspondent aux informations suivantes :

* **Audience** : Identifie la ressource de destination du jeton.
* **Émetteur** : identifie le service d’émission de jeton de sécurité (STS) qui a émis le jeton ainsi que le locataire Azure AD.
* **Locataire :** Contient un identificateur unique non modifiable du client de l’annuaire qui a émis le jeton.
* **FQDN :** nom de domaine complet (FQDN) sur la passerelle VPN Azure.
* **ServerSecret :** clé prépartagée de la passerelle VPN.

## <a name="folder-contents"></a>Contenu du dossier

* Le **dossier générique** contient le certificat de serveur public et le fichier VpnSettings.xml. Le fichier VpnSettings.xml contient les informations nécessaires à la configuration d’un client générique.

* Le fichier zip téléchargé peut également contenir des dossiers **WindowsAmd64** et **WindowsX86**. Ces dossiers contiennent le programme d’installation de SSTP et IKEv2 pour les clients Windows. Vous avez besoin de droits d’administrateur sur le client pour les installer.
