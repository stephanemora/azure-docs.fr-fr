---
title: Solution VMware de CloudSimple - Connexions réseau Azure
description: En savoir plus sur la connexion de votre réseau virtuel Azure au réseau de votre région CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2ab82b6c1b4b373c186019eaf96f9864861b9d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497631"
---
# <a name="azure-network-connections-overview"></a>Vue d’ensemble des connexions réseau Azure

Quand vous créez un service CloudSimple dans une région, celui-ci :

* crée un circuit Azure ExpressRoute et l’attache au service dans cette région ;
* autorise la connexion à votre réseau virtuel Azure ou réseau local à partir du réseau de votre région CloudSimple à l’aide d’Azure ExpressRoute ;
* vous donne accès aux services en cours d’exécution dans votre abonnement Azure, ou votre réseau local, à partir de votre environnement de cloud privé.

La connexion présente les caractéristiques suivantes :

* Sécuriser
* Privé
* Bande passante élevée
* Latence faible

## <a name="benefits"></a>Avantages

Une connexion réseau Azure vous permet d’effectuer les tâches suivantes :

* Utiliser Azure comme cible de sauvegarde pour les machines virtuelles sur votre cloud privé.
* Déployer des serveurs KMS dans votre abonnement Azure pour chiffrer la banque de données vSAN de votre cloud privé.
* Utiliser des applications hybrides où la couche web de l’application s’exécute dans le cloud public pendant que les couches application et base de données s’exécutent dans votre cloud privé.

## <a name="azure-virtual-network-connection"></a>Connexion au réseau virtuel Azure

Les clouds privés peuvent être connectés à vos ressources Azure à l’aide d’ExpressRoute.  Vous pouvez utiliser cette connexion pour accéder aux différentes ressources en cours d’exécution dans votre abonnement Azure à partir de votre cloud privé.  Cette connexion vous permet d’étendre le réseau de votre cloud privé à votre réseau virtuel Azure.

![Connexion Azure ExpressRoute au réseau virtuel](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Connexion ExpressRoute au réseau local

Vous pouvez connecter votre circuit Azure ExpressRoute existant à votre région CloudSimple. La fonctionnalité Global Reach d’ExpressRoute permet de connecter les deux circuits entre eux.  Une connexion est établie entre les circuits ExpressRoute locaux et CloudSimple.  Cette connexion vous permet d’étendre vos réseaux locaux au réseau de votre cloud privé.

![Connexion ExpressRoute locale - Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir des informations de peering pour la connexion entre le réseau virtuel Azure et CloudSimple](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Connecter un réseau local à CloudSimple à l’aide d’ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
