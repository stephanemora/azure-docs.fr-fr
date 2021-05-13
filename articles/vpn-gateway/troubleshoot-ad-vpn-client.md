---
title: Résolution des problèmes de clients VPN de point à site – Authentification Azure AD
titleSuffix: Azure VPN Gateway
description: Découvrez comment résoudre les problèmes des clients de point à site de passerelle VPN qui utilisent l’authentification Azure AD.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 04/29/2021
ms.author: cherylmc
ms.openlocfilehash: d307524fffd9b129044cd0f4e4b20d833db5ba83
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108292195"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Résoudre les problèmes d’un client VPN avec l’authentification Azure AD

Cet article vous aide à résoudre les problèmes rencontrés par un client VPN pour se connecter à un réseau virtuel à l’aide d’un VPN point à site et de l’authentification Azure Active Directory.

## <a name="view-status-log"></a><a name="status"></a>Afficher le journal d’état

Affichez le journal d’état pour examiner les messages d’erreur.

![logs](./media/troubleshoot-ad-vpn-client/1.png)

1. Cliquez sur l’icône des flèches en bas à droite dans la fenêtre du client pour afficher les **journaux d’état**.
2. Recherchez dans les journaux des erreurs susceptibles d’indiquer le problème.
3. Les messages d’erreur sont affichés en rouge.

## <a name="clear-sign-in-information"></a><a name="clear"></a>Effacer les informations de connexion

Effacez les informations de connexion.

![se connecter](./media/troubleshoot-ad-vpn-client/2.png)

1. Sélectionnez « … » à côté du profil qui pose problème. Sélectionnez **Configure -> Clear Saved Account** (Configurer -> Effacer le compte enregistré).
2. Sélectionnez **Enregistrer**.
3. Essayez de vous connecter.
4. Si la connexion échoue encore, passez à la section suivante.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>Exécuter les diagnostics

Exécutez les diagnostics sur le client VPN.

![diagnostics](./media/troubleshoot-ad-vpn-client/3.png)

1. Cliquez sur **…** à côté du profil sur lequel vous voulez exécuter les diagnostics. Sélectionnez **Diagnose -> Run Diagnosis** (Diagnostiquer -> Exécuter les diagnostics).
2. Le client exécute alors une série de tests et affiche les résultats.

   * Accès à Internet : vérifie si le client dispose d’une connectivité Internet
   * Informations d’identification du client : vérifie si le point de terminaison de l’authentification Azure Active Directory est accessible
   * Server Resolvable (Résolution par le serveur) : contacte le serveur DNS pour résoudre l’adresse IP du serveur VPN configuré
   * Serveur accessible : vérifie si le serveur VPN répond
3. Si l’un de ces tests échoue, contactez votre administrateur réseau pour résoudre le problème.
4. La section suivante vous montre comment collecter les journaux, si vous en avez besoin.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>Collecter les fichiers journaux du client

Collectez les fichiers journaux du client. Vous pouvez ensuite envoyer les fichiers journaux au support technique ou à votre administrateur par la méthode de votre choix (par exemple, par e-mail).

1. Cliquez sur « ... » à côté du profil sur lequel vous voulez exécuter les diagnostics. Sélectionnez **Diagnose -> Show Logs Directory** (Diagnostiquer -> Afficher le répertoire des journaux).

   ![afficher les journaux](./media/troubleshoot-ad-vpn-client/4.png)
2. L’Explorateur Windows ouvre le dossier qui contient les fichiers journaux.

   ![afficher le fichier](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Créer un locataire Azure Active Directory qui utilise l’authentification Azure AD pour les connexions Open VPN P2S](openvpn-azure-ad-tenant.md).