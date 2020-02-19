---
title: Accès à Azure VMware Solutions (AVS) à partir d’un emplacement local
description: Accès à Azure VMware Solutions (AVS) à partir de votre réseau local via un pare-feu
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a4a9760b5c7a70c58a1afe1b14b781a35f2b9b18
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77082972"
---
# <a name="accessing-your-avs-private-cloud-environment-and-applications-from-on-premises"></a>Accès à votre environnement de cloud privé AVS et à vos applications à partir d’un emplacement local

Une connexion peut être configurée à partir d’un réseau local vers AVS à l’aide d’Azure ExpressRoute ou d’un VPN de site à site. Accédez à votre vCenter de cloud privé AVS et à toutes les charges de travail que vous exécutez sur le cloud privé AVS à l’aide de la connexion. Vous pouvez contrôler les ports ouverts sur la connexion à l’aide d’un pare-feu sur votre réseau local. Cet article présente quelques-unes des exigences classiques des ports d’applications. Pour toute autre application, reportez-vous à la documentation de l’application pour les exigences de ports.

## <a name="ports-required-for-accessing-vcenter"></a>Ports requis pour accéder à vCenter

Pour accéder à votre vCenter de cloud privé AVS et au gestionnaire NSX-T, les ports définis dans le tableau ci-dessous doivent être ouverts sur le pare-feu local. 

| Port       | Source                           | Destination                      | Objectif                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Serveurs DNS locaux          | Serveurs DNS de cloud privé AVS        | Requis pour le transfert de la recherche DNS de *az.cloudsimple.io* vers des serveurs DNS de cloud privé AVS à partir d’un réseau local.     |
| 53 (UDP)   | Serveurs DNS de cloud privé AVS        | Serveurs DNS locaux          | Requis pour le transfert de la recherche DNS de noms de domaines locaux de vCenter de cloud privé AVS vers des serveurs DNS locaux. |
| 80 (TCP)   | Réseau local              | Réseau de gestion de cloud privé AVS | Requis pour la redirection de l’URL de vCenter de *http* vers *https*.                                                         |
| 443 (TCP)  | Réseau local              | Réseau de gestion de cloud privé AVS | Requis pour l’accès à vCenter et au gestionnaire NSX-T à partir d’un réseau local.                                           |
| 8000 (TCP) | Réseau local              | Réseau de gestion de cloud privé AVS | Requis pour vMotion de machines virtuelles locales d’un emplacement local vers un cloud privé AVS.                                          |
| 8000 (TCP) | Réseau de gestion de cloud privé AVS | Réseau local              | Requis pour vMotion de machines virtuelles d’un cloud privé AVS vers un emplacement local.                                          |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Ports requis pour l’utilisation d’un Active Directory local en tant que source d’identité

Pour configurer un Active Directory local en tant que source d’identité sur un vCenter de cloud privé AVS, les ports définis dans le tableau doivent être ouverts. Pour connaître les étapes de configuration, consultez [Utiliser Azure AD comme fournisseur d’identité pour vCenter sur un cloud privé AVS](https://docs.azure.cloudsimple.com/azure-ad/).

| Port         | Source                           | Destination                                         | Objectif                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Serveurs DNS de cloud privé AVS        | Serveurs DNS locaux                             | Requis pour le transfert de la recherche DNS de noms de domaines Active Directory locaux de vCenter de cloud privé AVS vers des serveurs DNS locaux.        |
| 389 (TCP/UDP) | Réseau de gestion de cloud privé AVS | Contrôleurs de domaine Active Directory locaux     | Requis pour la communication LDAP entre le serveur vCenter de cloud privé AVS et les contrôleurs de domaine Active Directory pour l’authentification utilisateur.              |
| 636 (TCP)     | Réseau de gestion de cloud privé AVS | Contrôleurs de domaine Active Directory locaux     | Requis pour la communication LDAP sécurisé (LDAPS) entre le serveur vCenter de cloud privé AVS et les contrôleurs de domaine Active Directory pour l’authentification utilisateur. |
| 3268 (TCP)    | Réseau de gestion de cloud privé AVS | Serveurs de catalogue global Active Directory locaux | Requis pour la communication LDAP dans les déploiements de contrôleurs à plusieurs domaines.                                                                      |
| 3269 (TCP)    | Réseau de gestion de cloud privé AVS | Serveurs de catalogue global Active Directory locaux | Requis pour la communication LDAPS dans les déploiements de contrôleurs à plusieurs domaines.                                                                     |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Ports communs requis pour l’accès aux machines virtuelles de charge de travail

L’accès aux machines virtuelles de charge de travail exécutées sur un cloud privé AVS requiert l’ouverture de ports sur votre pare-feu local. Le tableau ci-dessous présente certains des ports communs requis et leur fonction. Pour les exigences de ports spécifiques à toute autre application, reportez-vous à la documentation de l’application.

| Port         | Source                         | Destination                          | Objectif                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Réseau local            | Réseau de charge de travail de cloud privé AVS       | Accès d’interpréteur de commandes sécurisé aux machines virtuelles Linux s’exécutant sur un cloud privé AVS.            |
| 3389 (TCP)    | Réseau local            | Réseau de charge de travail de cloud privé AVS       | Bureau à distance sur des machines virtuelles Windows s’exécutant sur un cloud privé AVS.               |
| 80 (TCP)      | Réseau local            | Réseau de charge de travail de cloud privé AVS       | Accès à tous les serveurs web déployés sur des machines virtuelles s’exécutant sur un cloud privé AVS.      |
| 443 (TCP)     | Réseau local            | Réseau de charge de travail de cloud privé AVS       | Accès à tous les serveurs web sécurisés déployés sur des machines virtuelles s’exécutant sur un cloud privé AVS. |
| 389 (TCP/UDP) | Réseau de charge de travail de cloud privé AVS | Réseau Active Directory local | Joignez des machines virtuelles de charge de travail Windows à un domaine Active Directory local.     |
| 53 (UDP)      | Réseau de charge de travail de cloud privé AVS | Réseau local                  | Accès au service DNS de machines virtuelles de charge de travail sur des serveurs DNS locaux.       |

## <a name="next-steps"></a>Étapes suivantes

* [Créer et gérer des réseaux locaux virtuels et des sous-réseaux](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Se connecter à un réseau local à l’aide d’Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Configurer un VPN de site à site en local](https://docs.azure.cloudsimple.com/vpn-gateway/)
