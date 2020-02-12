---
title: Azure VMware Solutions (AVS) - Sécuriser un cloud privé AVS
description: Indique comment sécuriser un cloud privé AVS (Azure VMware Solutions)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b053f1493c2380153711176a4748ebf90b479a6c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020078"
---
# <a name="how-to-secure-your-avs-private-cloud-environment"></a>Sécurisation de votre environnement de cloud privé AVS

Définissez le contrôle d’accès en fonction du rôle (RBAC) pour le service, le portail et le cloud privé AVS à partir d’Azure. Les utilisateurs, les groupes et les rôles pour l’accès au serveur vCenter du cloud privé AVS sont indiqués à l’aide de l’authentification unique VMware. 

## <a name="rbac-for-avs-service"></a>RBAC pour le service AVS

La création du service AVS requiert un rôle **Propriétaire** ou **Contributeur** sur l’abonnement Azure. Par défaut, tous les propriétaires et contributeurs peuvent créer un service AVS et accéder au portail AVS pour créer et gérer des clouds privés AVS. Un seul service AVS peut être créé par région. Pour restreindre l’accès à des administrateurs précis, suivez la procédure ci-dessous.

1. Créez un service AVS dans un nouveau **groupe de ressources** dans le portail Azure
2. Indiquez le RBAC de ce groupe de ressources.
3. Achetez des nœuds et utilisez le même groupe de ressources que celui du service AVS

Seuls les utilisateurs disposant de privilèges **Propriétaire** ou **Contributeur** sur le groupe de ressources peuvent voir le service AVS et lancer le portail AVS.

Pour plus d’informations sur le RBAC, consultez la page [Qu’est-ce que le contrôle d’accès en fonction du rôle (RBAC) pour les ressources Azure ?](../role-based-access-control/overview.md)

## <a name="rbac-for-avs-private-cloud-vcenter"></a>RBAC pour le serveur vCenter de cloud privé AVS

Un utilisateur par défaut `CloudOwner@AVS.local` est créé dans le domaine SSO vCenter lors de la création d’un cloud privé AVS. L’utilisateur CloudOwner dispose des privilèges lui permettant de gérer vCenter. Des sources d’identité supplémentaires sont ajoutées au SSO vCenter pour donner accès à différents utilisateurs. Les groupes et les rôles prédéfinis sont configurés sur le vCenter qui peut être utilisé pour ajouter des utilisateurs supplémentaires.

### <a name="add-new-users-to-vcenter"></a>Ajouter de nouveaux utilisateurs dans VCenter

1. [Réaffectez les privilèges](escalate-private-cloud-privileges.md) pour l’utilisateur **CloudOwner@AVS.local** sur le cloud privé AVS.
2. Connectez-vous à vCenter à l’aide de **CloudOwner@AVS.local**
3. [Ajouter des utilisateurs avec authentification unique sur vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Ajoutez des utilisateurs pour les [groupes avec authentification unique sur vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Pour plus d’informations sur les groupes et les rôles prédéfinis, consultez l’article [Modèle d’autorisation de cloud privé AVS de VMware vCenter](learn-private-cloud-permissions.md).

### <a name="add-new-identity-sources"></a>Ajouter de nouvelles sources d’identité

Vous pouvez ajouter des fournisseurs d’identité supplémentaires pour le domaine SSO vCenter de votre cloud privé AVS. Les fournisseurs d’identité fournissent une authentification et les groupes SSO vCenter accordent une autorisation aux utilisateurs.

* [Utilisez Active Directory en tant que fournisseur d’identité](set-vcenter-identity.md) sur le serveur vCenter de cloud privé AVS.
* [Utilisez Azure AD en tant que fournisseur d’identité](azure-ad.md) sur le serveur vCenter de cloud privé AVS

1. [Réaffectez les privilèges](escalate-private-cloud-privileges.md) pour l’utilisateur **CloudOwner@AVS.local** sur le cloud privé AVS.
2. Connectez-vous à vCenter à l’aide de **CloudOwner@AVS.local**
3. Ajoutez des utilisateurs à partir du fournisseur d’identité à [des groupes avec authentification unique sur vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-avs-private-cloud-environment"></a>Sécuriser le réseau de votre environnement de cloud privé AVS

La sécurité réseau de l’environnement de cloud privé AVS est assurée par la sécurisation de l’accès réseau et le contrôle du trafic réseau entre les ressources.

### <a name="access-to-avs-private-cloud-resources"></a>Accéder aux ressources de cloud privé AVS

Vous pouvez accéder aux ressources et au serveur vCenter de cloud privé AVS grâce à une connexion réseau sécurisée :

* **[Connexion ExpressRoute](on-premises-connection.md)** . ExpressRoute fournit une connexion sécurisée, à bande passante élevée et à faible latence à partir de votre environnement local. L’utilisation de la connexion permet à vos services, réseaux et utilisateurs locaux d’accéder à votre serveur vCenter de cloud privé AVS.
* **[Passerelle VPN de site à site](vpn-gateway.md)** . Le VPN de site à site permet d’accéder à vos ressources de cloud privé AVS à partir d’un emplacement local grâce à un tunnel sécurisé. Vous indiquez les réseaux locaux qui peuvent envoyer et recevoir le trafic réseau sur votre cloud privé AVS.
* **[Passerelle VPN de point à site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** . Utilisez une connexion VPN de point à site pour un accès à distance rapide à votre serveur vCenter de cloud privé AVS.

### <a name="control-network-traffic-in-avs-private-cloud"></a>Contrôler le trafic réseau dans le cloud privé AVS

Les tables et les règles de pare-feu contrôlent le trafic réseau dans le cloud privé AVS. La table de pare-feu vous permet de contrôler le trafic réseau entre un réseau source ou une adresse IP et un réseau ou une adresse IP de destination en fonction de la combinaison des règles définies dans la table.

1. Créez une [table de pare-feu](firewall.md#add-a-new-firewall-table).
2. [Ajoutez des règles](firewall.md#create-a-firewall-rule) à la table de pare-feu.
3. [Joindre une table de pare-feu à un réseau local virtuel/sous-réseau](firewall.md#attach-vlanssubnet.
