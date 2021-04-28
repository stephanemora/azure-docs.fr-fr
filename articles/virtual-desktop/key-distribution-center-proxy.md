---
title: Configurer le proxy de centre de distribution de clés Kerberos pour Windows Virtual Desktop – Azure
description: Comment configurer un pool d’hôtes Windows Virtual Desktop pour utiliser un proxy de centre de distribution de clés Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 79ae61d21693ae56dff280402c7a3d114822be7f
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108132356"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Configurer un proxy de centre de distribution de clés Kerberos (préversion)

> [!IMPORTANT]
> Cette fonctionnalité est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service, c’est pourquoi nous déconseillons son utilisation pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les clients soucieux de la sécurité, tels que les organisations financières ou gouvernementales, se connectent souvent à l’aide de cartes à puces. Les cartes à puces permettent de sécuriser les déploiements en exigeant une authentification multifacteur (MFA). Toutefois, pour la partie RDP d’une session de Windows Virtual Desktop, les cartes à puces nécessitent une connexion directe, ou « ligne de vue », avec un contrôleur de domaine Active Directory (AD) pour l’authentification Kerberos. Sans cette connexion directe, les utilisateurs ne peuvent pas se connecter automatiquement au réseau de l’organisation à partir de connexions distantes. Les utilisateurs dans un déploiement de Windows Virtual Desktop peuvent utiliser le service proxy KDC pour acheminer ce trafic d’authentification et se connecter à distance. Le proxy KDC permet l’authentification pour le protocole Bureau à distance d’une session de Windows Virtual Desktop, permettant à l’utilisateur de se connecter en toute sécurité. Il est ainsi beaucoup plus facile de travailler à domicile, et certains scénarios de récupération d’urgence peuvent se dérouler plus facilement.

Toutefois, la configuration du proxy KDC implique généralement l’attribution du rôle Passerelle Windows Server dans Windows Server 2016 ou une version ultérieure. Comment utiliser un rôle Services Bureau à distance pour vous connecter à Windows Virtual Desktop ? Pour répondre à cette question, examinons rapidement les composants.

Le service Windows Virtual Desktop doit être authentifié sur deux composants :

- Le flux du client Windows Virtual Desktop qui donne aux utilisateurs une liste des ordinateurs de bureau ou applications disponibles auxquels ils ont accès. Ce processus d’authentification se déroule dans Azure Active Directory, ce qui signifie que ce composant n’est pas l’objet de cet article.
- La session RDP qui résulte de la sélection par un utilisateur de l’une de ces ressources disponibles. Ce composant utilise l’authentification Kerberos et requiert un proxy KDC pour les utilisateurs distants.

Cet article vous montre comment configurer le flux dans le client Windows Virtual Desktop dans le portail Azure. Si vous souhaitez savoir comment configurer le rôle Passerelle des services Bureau à distance, consultez [Déployer le rôle Passerelle des services Bureau à distance](/windows-server/remote/remote-desktop-services/remote-desktop-gateway-role).

## <a name="requirements"></a>Configuration requise

Pour configurer un hôte de session Windows Virtual Desktop avec un proxy KDC, vous avez besoin des éléments suivants :

- Un accès au portail Azure et à un compte Administrateur Azure.
- Les ordinateurs clients distants doivent fonctionner sous Windows 10 ou Windows 7 et avoir le [client Windows Desktop](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) installé. Actuellement, le client Web n’est pas pris en charge.
- Un proxy KDC doit déjà être installé sur votre ordinateur. Pour savoir comment procéder, consultez [Configurer le rôle Passerelle des services Bureau à distance pour Windows Virtual Desktop](/windows-server/remote/remote-desktop-services/remote-desktop-gateway-role).
- Le système d’exploitation de l’ordinateur doit être Windows Server 2016 ou une version ultérieure.

Une fois que vous avez vérifié que vous répondez à ces exigences, vous êtes prêt à commencer.

## <a name="how-to-configure-the-kdc-proxy"></a>Comment configurer le proxy de KDC

Pour configurer le proxy de KDC :

1. Connectez-vous au portail Azure en tant qu’administrateur.

2. Accédez à la page Windows Virtual Desktop.

3. Sélectionnez le pool d’hôtes pour lequel vous souhaitez activer le proxy de KDC, puis sélectionnez **Propriétés RDP**.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la page du portail Azure montrant un utilisateur sélectionnant des pools d’hôtes, le nom de l’exemple de pool d’hôtes, puis les propriétés RDP.](media/rdp-properties.png)

4. Sélectionnez l’onglet **Avancé**, puis entrez une valeur au format suivant sans espaces :

    
    > kdcproxyname:s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran montrant l’onglet Avancé sélectionné, avec la valeur entrée comme décrit à l’étape 4.](media/advanced-tab-selected.png)

5. Sélectionnez **Enregistrer**.

6. Le pool d’hôtes sélectionné doit maintenant commencer à émettre des fichiers de connexion RDP qui incluent la valeur kdcproxyname que vous avez entrée à l’étape 4.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment gérer le côté Services Bureau à distance du proxy KDC et attribuer le rôle Passerelle des services Bureau à distance, consultez [Déployer le rôle Passerelle des services Bureau à distance](/windows-server/remote/remote-desktop-services/remote-desktop-gateway-role).

Si vous êtes intéressé par la mise à l’échelle de vos serveurs proxy KDC, découvrez comment configurer la haute disponibilité pour le proxy KDC dans [Ajouter la haute disponibilité au serveur frontal d’accès web et de passerelle des services Bureau à distance](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).