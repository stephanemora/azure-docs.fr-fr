---
title: Accès à la solution Azure VMware de CloudSimple à partir d’un emplacement local
titleSuffix: Azure VMware Solution by CloudSimple
description: Accès à votre solution Azure VMware de CloudSimple à partir de votre réseau local via un pare-feu
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df4c51953c6f50e30ba61b993cdb35856fcb8e25
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452405"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>Accès à votre environnement de cloud privé CloudSimple et à vos applications à partir d’un emplacement local

Une connexion peut être configurée à partir d’un réseau local vers CloudSimple à l’aide d’Azure ExpressRoute ou d’un VPN de site à site.  Accédez à votre vCenter de cloud privé CloudSimple et à toutes les charges de travail que vous exécutez sur le cloud privé à l’aide de la connexion.  Vous pouvez contrôler les ports ouverts sur la connexion à l’aide d’un pare-feu sur votre réseau local.  Cet article présente quelques-unes des exigences classiques des ports d’applications.  Pour toute autre application, reportez-vous à la documentation de l’application pour les exigences de ports.

## <a name="ports-required-for-accessing-vcenter"></a>Ports requis pour accéder à vCenter

Pour accéder à votre vCenter de cloud privé et au gestionnaire NSX-T, les ports définis dans le tableau ci-dessous doivent être ouverts sur le pare-feu local.  

| Port       | Source                           | Destination                      | Objectif                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Serveurs DNS locaux          | Serveurs DNS de cloud privé        | Requis pour le transfert de la recherche DNS de *az.cloudsimple.io* vers des serveurs DNS de cloud privé à partir d’un réseau local.       |
| 53 (UDP)   | Serveurs DNS de cloud privé        | Serveurs DNS locaux          | Requis pour le transfert de la recherche DNS de noms de domaines locaux de vCenter de cloud privé vers des serveurs DNS locaux. |
| 80 (TCP)   | Réseau local              | Réseau de gestion de cloud privé | Requis pour la redirection de l’URL de vCenter de *http* vers *https*.                                                           |
| 443 (TCP)  | Réseau local              | Réseau de gestion de cloud privé | Requis pour l’accès à vCenter et au gestionnaire NSX-T à partir d’un réseau local.                                             |
| 8000 (TCP) | Réseau local              | Réseau de gestion de cloud privé | Requis pour vMotion de machines virtuelles locales d’un emplacement local vers un cloud privé.                                            |
| 8000 (TCP) | Réseau de gestion de cloud privé | Réseau local              | Requis pour vMotion de machines virtuelles d’un cloud privé vers un emplacement local.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Ports requis pour l’utilisation d’un Active Directory local en tant que source d’identité

Pour configurer un Active Directory local en tant que source d’identité sur un vCenter de cloud privé, les ports définis dans le tableau doivent être ouverts.  Pour connaître les étapes de configuration, consultez [Use Azure AD as an identity provider for vCenter on CloudSimple Private Cloud](https://docs.azure.cloudsimple.com/azure-ad/) (Utiliser Azure AD comme fournisseur d’identité pour vCenter sur un cloud privé CloudSimple).

| Port         | Source                           | Destination                                         | Objectif                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Serveurs DNS de cloud privé        | Serveurs DNS locaux                             | Requis pour le transfert de la recherche DNS de noms de domaines Active Directory locaux de vCenter de cloud privé vers des serveurs DNS locaux.          |
| 389 (TCP/UDP) | Réseau de gestion de cloud privé | Contrôleurs de domaine Active Directory locaux     | Requis pour la communication LDAP entre le serveur vCenter de cloud privé et les contrôleurs de domaine Active Directory pour l’authentification utilisateur.                |
| 636 (TCP)     | Réseau de gestion de cloud privé | Contrôleurs de domaine Active Directory locaux     | Requis pour la communication LDAP sécurisé (LDAPS) entre le serveur vCenter de cloud privé et les contrôleurs de domaine Active Directory pour l’authentification utilisateur. |
| 3268 (TCP)    | Réseau de gestion de cloud privé | Serveurs de catalogue global Active Directory locaux | Requis pour la communication LDAP dans les déploiements de contrôleurs à plusieurs domaines.                                                                        |
| 3269 (TCP)    | Réseau de gestion de cloud privé | Serveurs de catalogue global Active Directory locaux | Requis pour la communication LDAPS dans les déploiements de contrôleurs à plusieurs domaines.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Ports communs requis pour l’accès aux machines virtuelles de charge de travail

L’accès aux machines virtuelles de charge de travail exécutées sur un cloud privé requiert l’ouverture de ports sur votre pare-feu local.  Le tableau ci-dessous présente certains des ports communs requis et leur fonction.  Pour les exigences de ports spécifiques à toute autre application, reportez-vous à la documentation de l’application.

| Port         | Source                         | Destination                          | Objectif                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Réseau local            | Réseau de charge de travail de cloud privé       | Accès d’interpréteur de commandes sécurisé aux machines virtuelles Linux s’exécutant sur un cloud privé.              |
| 3389 (TCP)    | Réseau local            | Réseau de charge de travail de cloud privé       | Bureau à distance sur des machines virtuelles Windows s’exécutant sur un cloud privé.                 |
| 80 (TCP)      | Réseau local            | Réseau de charge de travail de cloud privé       | Accès à tous les serveurs web déployés sur des machines virtuelles s’exécutant sur un cloud privé.        |
| 443 (TCP)     | Réseau local            | Réseau de charge de travail de cloud privé       | Accès à tous les serveurs web sécurisés déployés sur des machines virtuelles s’exécutant sur un cloud privé. |
| 389 (TCP/UDP) | Réseau de charge de travail de cloud privé | Réseau Active Directory local | Joignez des machines virtuelles de charge de travail Windows à un domaine Active Directory local.       |
| 53 (UDP)      | Réseau de charge de travail de cloud privé | Réseau local                  | Accès au service DNS de machines virtuelles de charge de travail sur des serveurs DNS locaux.         |

## <a name="next-steps"></a>Étapes suivantes

* [Créer et gérer des réseaux locaux virtuels et des sous-réseaux](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Se connecter à un réseau local à l’aide d’Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Configurer un VPN de site à site en local](https://docs.azure.cloudsimple.com/vpn-gateway/)
