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
ms.openlocfilehash: 8ea98d6493b824bfa232ef8193388e93b97c506b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576999"
---
# <a name="azure-network-connection-overview"></a>Vue d’ensemble de la connexion réseau Azure

Lorsque vous créez un service de CloudSimple dans une région, il :

* Crée le circuit Azure ExpressRoute et l’attache au service dans cette région
* Se connecte votre réseau de la région CloudSimple à votre réseau virtuel Azure ou votre réseau local à l’aide d’Azure ExpressRoute
* Fournit des services d’accès en cours d’exécution dans votre abonnement Azure ou votre réseau local, à partir de votre environnement de cloud privé

Cette connexion est une bande passante élevée avec une faible latence.

## <a name="benefits"></a>Avantages

Connexion de réseau Azure vous permet de :

* Utilisez Azure comme cible de sauvegarde pour les machines virtuelles sur votre Cloud privé.
* Déployer des serveurs KMS dans votre abonnement Azure pour chiffrer votre banque de données vSAN Cloud privé.
* Utiliser des applications hybrides où la couche web de l’application s’exécute dans le cloud public lors de l’application et les niveaux de base de données s’exécutent dans votre Cloud privé.

## <a name="expressroute-connection-to-on-premises-network"></a>Connexion ExpressRoute au réseau local

Vous pouvez vous connecter à votre circuit Azure ExpressRoute existant dans votre région CloudSimple. Fonctionnalité de portée mondiale ExpressRoute permet de connecter les deux circuits entre eux.  Une connexion est établie entre les locaux et les circuits ExpressRoute de CloudSimple.

Cette méthode établit une connexion entre les deux environnements est :

* Sécuriser
* Privé
* Bande passante élevée
* Latence faible

Pour créer une connexion ExpressRoute à un réseau local, [contactez le support technique](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Étapes suivantes

* [Configurer la connexion de réseau virtuel](https://docs.azure.cloudsimple.com/virtual-network-connection)