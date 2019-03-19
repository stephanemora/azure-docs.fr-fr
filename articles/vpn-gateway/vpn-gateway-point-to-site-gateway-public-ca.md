---
title: Remplacement des certificats des passerelles P2S par des certificats publics | Passerelle VPN Azure | Microsoft Docs
description: Cet article explique comment installer les nouveaux certificats d'autorité de certification publics des passerelles P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: cherylmc
ms.openlocfilehash: 24956dd51ef4c2544ce28005fa3bff31113e5959
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848923"
---
# <a name="transition-to-a-public-ca-gateway-certificate-for-p2s"></a>Passer à un certificat de passerelle d’autorité de certification public pour P2S

La Passerelle VPN Azure n'émet plus de certificats auto-signés de niveau Azure vers ses passerelles pour les connexions P2S. Les certificats délivrés sont maintenant signés par une autorité de certification publique. Toutefois, certaines des passerelles les plus anciennes peuvent encore utiliser des certificats auto-signés. Ces certificats auto-signés approchent de leur date d'expiration et doivent être remplacés par des certificats d'autorité de certification publics.

>[!NOTE]
> * Les certificats auto-signés utilisés pour l'authentification des clients P2S ne sont pas concernés par ce changement de certificats de niveau Azure. Vous pouvez continuer à émettre et à utiliser des certificats auto-signés normalement.
>

Dans ce contexte, les certificats constituent des certificats supplémentaires de niveau Azure. Il ne s'agit pas des chaînes de certificats que vous utilisez lors de la génération de vos propres certificats racine auto-signés et des certificats clients pour l'authentification. Ces certificats ne sont pas concernés ; ils expireront aux dates que vous avez définies.

Auparavant, un certificat auto-signé destiné à la passerelle (émis en arrière-plan par Azure) devait être mis à jour tous les 18 mois. Les fichiers de configuration des clients VPN devaient ensuite être générés et redéployés sur tous les clients P2S. Les certificats d'autorité de certification publics mettent fin à cette limitation. En plus du remplacement des certificats, ce changement apporte également des améliorations à la plateforme, de meilleures métriques et une stabilité accrue.

Seules les passerelles les plus anciennes sont concernées par ce changement. Si votre certificat de passerelle doit être remplacé, vous recevrez un message ou une notification toast sur le portail Azure. Pour savoir si votre passerelle est concernée, suivez les étapes décrites dans cet article.

> [!IMPORTANT]
> La mise à jour est prévue pour le 12 mars 2019 à partir de 18:00 UTC. Vous pouvez créer un cas de support si vous préférez choisir un autre créneau horaire. Formulez et finalisez votre demande au moins 24 heures à l'avance.  Vous pouvez demander l'un des créneaux horaire suivants :
>
> * 06:00 UTC le 25 février
> * 18:00 UTC le 25 février
> * 06:00 UTC le 1er mars
> * 18:00 UTC le 1er mars
>
> **Pour toutes les autres passerelles, la mise à jour aura lieu le 12 mars 2019 à partir de 18:00 UTC**.
>
> Les clients recevront un e-mail au terme du processus de remplacement de leur passerelle.
> 

## <a name="1-verify-your-certificate"></a>1. Vérifiez votre certificat

### <a name="resource-manager"></a>Gestionnaire de ressources

1. Vérifiez si vous êtes concerné par cette mise à jour. Téléchargez la configuration de votre client VPN actuel en suivant les étapes décrites dans [cet article](point-to-site-vpn-client-configuration-azure-cert.md).

2. Ouvrez ou extrayez le fichier zip, puis accédez au dossier « Generic ». Dans le dossier Generic, vous verrez deux fichiers, dont *VPNSettings.xml*.
3. Ouvrez le fichier *VPNSettings.xml* dans la visionneuse ou l'éditeur de votre choix. Dans le fichier xml, recherchez les champs suivants :

   * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
   * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. Si la mention « DigiCert Global Root CA » apparaît pour les champs *ServerCertRotCn* et *ServerCertIssuerCn*, cela signifie que vous n'êtes pas concerné par cette mise à jour et que vous n'avez pas besoin de suivre les étapes décrites dans cet article. Si une autre mention apparaît, en revanche, le certificat de votre passerelle est concerné par la mise à jour et sera remplacé.

### <a name="classic"></a>Classique

1. Sur un ordinateur client, accédez à l'emplacement %appdata%/Microsoft/Network/Connections/Cm/<gatewayID>. Dans le dossier Gateway ID, vous pouvez afficher le certificat.
2. Dans l'onglet Général du certificat, vérifiez que l'autorité émettrice est « DigiCert Global Root CA ». Si vous avez autre chose que cette autorité émettrice, cela signifie que le certificat de votre passerelle est concerné par la mise à jour et qu'il va faire l'objet d'un remplacement.

## <a name="2-check-certificate-transition-schedule"></a>2. Consultez le calendrier de mise à jour des certificats

Si votre certificat est concerné par la mise à jour, le certificat de votre passerelle sera remplacé. Reportez-vous à la note **Important** pour connaître les heures de mise à jour. Après la mise à jour, les clients P2S ne pourront plus se connecter avec leur ancien profil. Vous devrez générer de nouveaux profils VPN et les installer sur les clients.

## <a name="3-generate-vpn-client-configuration-profile"></a>3. Générez le profil de configuration du client VPN

Une fois le certificat mis à jour, téléchargez le nouveau profil VPN (fichiers de configuration du client VPN) à partir du portail Azure. Pour connaître les étapes à suivre, consultez [Créer et installer les fichiers de configuration du client VPN](point-to-site-vpn-client-configuration-azure-cert.md). Vous n'avez pas besoin de générer de nouveaux certificats clients.

## <a name="4-deploy-vpn-client-profile"></a>4. Déployez le profil du client VPN

Déployez le nouveau profil sur tous les clients VPN de point-to-site. Les clients VPN seront déconnectés jusqu'à ce que le nouveau profil VPN des connexions point à site soit téléchargé et déployé sur les appareils clients. Il n'est pas nécessaire de réémettre les certificats clients déjà installés sur les ordinateurs des clients VPN.

## <a name="5-connect-the-vpn-client"></a>5. Connectez le client VPN

Connectez-vous normalement à Azure à partir du client VPN.
