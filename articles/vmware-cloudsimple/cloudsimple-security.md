---
title: Sécurité des Services de CloudSimple
description: Décrit les modèles de responsabilité partagée pour la sécurité des services de CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/27/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f5f3fe32e03a9a2bb0186854a83917f8918c6647
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358122"
---
# <a name="cloudsimple-security-overview"></a>Vue d’ensemble de la sécurité CloudSimple

Cet article fournit une vue d’ensemble de l’implémentation de la sécurité sur le service CloudSimple, l’infrastructure et centre de données.  Vous découvrez la protection des données et la sécurité réseau et la gestion des correctifs et des vulnérabilités.

## <a name="shared-responsibility"></a>Responsabilité partagée

La solution VMware Azure par CloudSimple utilise un modèle de responsabilité partagée pour la sécurité. Sécurité de confiance dans le cloud est obtenue via les responsabilités partagées de clients et de Microsoft comme fournisseur de services. Cette matrice de responsabilité garantit une meilleure sécurité et élimine les points de défaillance uniques. 

## <a name="azure-infrastructure"></a>Infrastructure Azure 

Considérations de sécurité d’infrastructure Azure incluent l’emplacement des centres de données et des équipements. 

### <a name="datacenter-security"></a>Sécurité du centre de données 

Microsoft possède une division entière consacré à la conception, création et d’exploitation les installations physiques prenant en charge Azure. Cette équipe est investie dans la conservation d’une sécurité physique à la pointe. Pour plus d’informations sur la sécurité physique, consultez [installations Azure, locaux et la sécurité physique](https://docs.microsoft.com/azure/security/azure-physical-security).

### <a name="equipment-location"></a>Emplacement de l’équipement

L’équipement matériel nu qui exécute vos Clouds privés est hébergée dans des emplacements de centre de données Azure.  Les cages où que le matériel est, requiert biométrique authentification à deux facteurs en fonction d’accéder.

## <a name="dedicated-hardware"></a>Matériel dédié

Dans le cadre du service CloudSimple, tous les clients CloudSimple Obtient des hôtes de métal nus dédiés avec les disques attachés locales qui est physiquement isolés des autres matériel client. Un hyperviseur ESXi avec vSAN s’exécute sur chaque nœud. Les nœuds sont gérées via le client dédié VMware vCenter et NSX. Ne pas partager le matériel entre les locataires fournit une couche supplémentaire d’isolation et de protection de sécurité.

## <a name="data-security"></a>Sécurité des données

Les clients garder le contrôle et la propriété de leurs données. Gestion des données des données client est la responsabilité du client.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Protection des données pour les données au repos et en mouvement au sein des réseaux internes

Pour les données au repos dans l’environnement de Cloud privé, vous pouvez utiliser le chiffrement réseau vSAN. le cryptage de vSAN fonctionne avec VMware certifié serveurs externes de gestion de clés (KMS) dans votre propre réseau virtuel ou l’en local.  Vous contrôlez vous-même les clés de chiffrement de données. Pour les données en mouvement dans le Cloud privé, vSphere prend en charge le chiffrement des données sur le réseau pour tout le trafic de vmkernel (y compris le trafic de vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Protection des données pour les données requises pour vous déplacer dans les réseaux publics

Pour protéger les données qui sont transmises via des réseaux publics, vous pouvez créer un VPN SSL et IPsec tunnels pour vos Clouds privés. Méthodes courantes de chiffrement sont pris en charge, y compris AES 128 et 256 octets. Les données en transit (y compris l’authentification, d’un accès administratif et les données client) sont chiffrées avec les mécanismes de chiffrement standard (SSH, TLS 1.2 et sécuriser RDP). La communication qui transporte des informations sensibles utilise les mécanismes de chiffrement standard.

### <a name="secure-disposal"></a>Élimination en toute sécurité 

Si votre service CloudSimple expire ou est interrompu, vous êtes responsable de la suppression ou la suppression de vos données. CloudSimple coopéreront avec vous pour supprimer ou de retourner toutes les données client comme indiqué dans le contrat de client, sauf dans la mesure CloudSimple est requis par la loi de conserver tout ou partie des données personnelles. Si nécessaire de conserver toutes les données personnelles, CloudSimple sera archiver les données et implémenter des mesures raisonnables pour empêcher que les données client à partir de n’importe quel traitement supplémentaire.

### <a name="data-location"></a>Emplacement des données

Lorsque vous configurez vos Clouds privés, vous choisissez la région Azure où elles seront déployées. Données de machine virtuelle VMware ne sont pas déplacées à partir de ce centre de données physique, sauf si vous effectuez la migration des données ou sauvegarde des données hors site. Vous pouvez également héberger des charges de travail et stocker des données dans plusieurs régions Azure si cela est adapté à vos besoins.

Les données client qui résidentes dans les nœuds hyperconvergés de Cloud privé ne transite pas par emplacements sans l’action explicite de l’administrateur. Il vous incombe de mettre en œuvre de vos charges de travail de manière hautement disponible.

### <a name="data-backups"></a>Sauvegardes de données
CloudSimple ne sauvegarder ou archiver les données client. CloudSimple effectue une sauvegarde périodique de vCenter et les données NSX pour fournir une haute disponibilité des serveurs d’administration. Avant la sauvegarde, toutes les données sont chiffrées à la source de vCenter à l’aide de APIs VMware. Les données chiffrées sont transportées et stockées dans les objets blob Azure. Les clés de chiffrement pour les sauvegardes sont stockées dans un coffre managé CloudSimple hautement sécurisé en cours d’exécution dans le réseau virtuel CloudSimple dans Azure.

## <a name="network-security"></a>Sécurité réseau

La solution CloudSimple s’appuie sur les couches de sécurité réseau.

### <a name="azure-edge-security"></a>Sécurité de périphérie Azure

Les services CloudSimple sont appuient sur la sécurité du réseau de base fournie par Azure. Azure applique des techniques de défense en profondeur pour la détection et de réponse en temps utile pour les attaques réseau associé à entrée anormale ou modèles de trafic de sortie et les attaques de distribuées par déni de service (distribué DDoS). Ce contrôle de sécurité s’applique aux environnements de Cloud privé et le logiciel de plan de contrôle développé par CloudSimple.

### <a name="segmentation"></a>Segmentation

Le service CloudSimple possède des réseaux de couche 2 logiquement distincts qui limitent l’accès à vos propres réseaux privés dans votre environnement de Cloud privé. Vous pouvez protéger davantage vos réseaux Cloud privé à l’aide d’un pare-feu. Le CloudSimple portail vous permet de définir les Nouv et NS contrôles règles de trafic réseau pour tout le trafic réseau, y compris intra le trafic de Cloud privé, le trafic de Cloud entre privé, le trafic vers Internet, général et le trafic réseau vers en local via VPN IPsec ou Connexion ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Une vulnérabilité et gestion des correctifs 

CloudSimple est responsable de la mise à jour corrective de sécurité des logiciels VMware gérés (ESXi vCenter et NSX).

## <a name="identity-and-access-management"></a>Gestion de l'identité et de l'accès

Les clients peuvent s’authentifier à leur compte Azure (dans Azure AD) à l’aide de l’authentification multifacteur ou l’authentification unique comme préféré. À partir du portail Azure, vous pouvez lancer le portail CloudSimple sans avoir à retaper les informations d’identification.

CloudSimple prend en charge la configuration facultative d’une source d’identité pour le vCenter du Cloud privé. Vous pouvez utiliser un [source d’identité locale](https://docs.azure.cloudsimple.com/set-vcenter-identity), une nouvelle source d’identité pour le Cloud privé, ou [Azure AD](https://docs.azure.cloudsimple.com/azure-ad).

Par défaut, les clients reçoivent les privilèges nécessaires pour les opérations quotidiennes de vCenter dans le Cloud privé. Ce niveau d’autorisation n’inclut pas l’accès administratif au serveur vCenter. Si l’accès administratif est temporairement requis, vous pouvez [élever vos privilèges](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) pendant une période limitée pendant que vous effectuez les tâches d’administration.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un service CloudSimple sur Azure](quickstart-create-cloudsimple-service.md)
* Découvrez comment [créer un cloud privé](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Découvrez comment [configurer un environnement de cloud privé](quickstart-create-private-cloud.md)
