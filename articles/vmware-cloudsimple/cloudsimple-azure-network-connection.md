---
title: Solution de VMware à CloudSimple - connexions réseau Azure
description: En savoir plus sur la connexion de votre réseau virtuel Azure à votre réseau de la région CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2ab82b6c1b4b373c186019eaf96f9864861b9d9
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497631"
---
# <a name="azure-network-connections-overview"></a>Vue d’ensemble des connexions de réseau Azure

Lorsque vous créez un service de CloudSimple dans une région, il :

* Crée un circuit Azure ExpressRoute et l’attache au service dans cette région
* Autorise la connexion à partir de votre région CloudSimple réseau à votre réseau virtuel Azure ou votre réseau local à l’aide d’Azure ExpressRoute
* Fournit des services d’accès en cours d’exécution dans votre abonnement Azure ou votre réseau local, à partir de votre environnement de Cloud privé

La connexion est :

* Sécuriser
* Privé
* Bande passante élevée
* Latence faible

## <a name="benefits"></a>Avantages

Connexion de réseau Azure vous permet de :

* Utilisez Azure comme cible de sauvegarde pour les machines virtuelles sur votre Cloud privé.
* Déployer des serveurs KMS dans votre abonnement Azure pour chiffrer votre banque de données vSAN Cloud privé.
* Utiliser des applications hybrides où la couche web de l’application s’exécute dans le cloud public lors de l’application et les niveaux de base de données s’exécutent dans votre Cloud privé.

## <a name="azure-virtual-network-connection"></a>Connexion de réseau virtuel Azure

Clouds privés peuvent être connectés à vos ressources Azure à l’aide d’ExpressRoute.  Vous pouvez utiliser cette connexion pour accéder aux différentes ressources en cours d’exécution dans votre abonnement Azure à partir de votre Cloud privé.  Cette connexion vous permet d’étendre vous réseau de Cloud privé à votre réseau virtuel Azure.

![Connexion à Azure ExpressRoute au réseau virtuel](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Connexion ExpressRoute au réseau local

Vous pouvez vous connecter à votre circuit Azure ExpressRoute existant dans votre région CloudSimple. Fonctionnalité de portée mondiale ExpressRoute permet de connecter les deux circuits entre eux.  Une connexion est établie entre les locaux et les circuits ExpressRoute de CloudSimple.  Cette connexion vous permet d’étendre vos réseaux locaux au réseau de Cloud privé.

![Connexion à ExpressRoute en local - portée mondiale](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir des informations sur l’homologation pour un réseau virtuel Azure à CloudSimple connexion](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Se connecter en local à CloudSimple à l’aide d’ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
