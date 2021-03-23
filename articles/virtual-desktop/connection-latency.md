---
title: Latence de connexion des utilisateur de Windows Virtual Desktop – Azure
description: Latence de connexion pour les utilisateurs de Windows Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb0ee52d37fe901a610723d5864240b8778d8b17
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574592"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Déterminer la latence de connexion pour les utilisateurs dans Windows Virtual Desktop

Windows Virtual Desktop est disponible dans le monde entier. Les administrateurs peuvent créer des machines virtuelles (VM) dans la région Azure de leur choix. La latence de connexion varie en fonction de l’emplacement des utilisateurs et des machines virtuelles. Les services de Windows Virtual Desktop se déploient en permanence vers de nouvelles zones géographiques pour améliorer la latence.

L’[outil d’estimation de l’expérience Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/) peut vous aider à déterminer le meilleur emplacement pour optimiser la latence de vos machines virtuelles. Nous vous recommandons d’utiliser l’outil tous les deux à trois mois pour vous assurer que l’emplacement optimal n’a pas changé lors du développement de Windows Virtual Desktop vers de nouvelles zones.

## <a name="interpreting-results-from-the-windows-virtual-desktop-experience-estimator-tool"></a>Interprétation des résultats de l’outil Estimateur d’expérience de Windows Virtual Desktop

Dans Windows Virtual Desktop, une latence de jusqu’à 150 ms ne devrait pas avoir d’incidence sur une expérience utilisateur qui n’implique pas de rendu ou de vidéo. Des latences entre 150 et 200 ms devraient convenir au traitement de texte. Une latence supérieure à 200 ms peut avoir un impact sur l’expérience utilisateur. 

En outre, la connexion Windows Virtual Desktop dépend de la connexion Internet de l’ordinateur à partir duquel l’utilisateur utilise le service. Les utilisateurs risquent de perdre la connexion ou de subir un délai d’entrée dans les des situations suivantes :

 - L’utilisateur ne dispose pas d’une connexion Internet locale stable et la latence est supérieure à 200 ms.
 - Le réseau est saturé ou limité en débit.

Nous vous recommandons de choisir les emplacements de machines virtuelles les plus proches possible de vos utilisateurs. Par exemple, si l’utilisateur se trouve en Inde, mais que la machine virtuelle se trouve aux États-Unis, il y aura une latence qui affectera l’expérience globale de l’utilisateur. 

## <a name="azure-front-door"></a>Azure Front Door

Windows Virtual Desktop utilise [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) pour rediriger la connexion utilisateur vers la passerelle Windows Virtual Desktop la plus proche en fonction de l’adresse IP source. Windows Virtual Desktop utilise toujours la passerelle Windows Virtual Desktop choisie par le client.

## <a name="next-steps"></a>Étapes suivantes

- Pour vérifier le meilleur emplacement pour une latence optimale, voir l’[outil d’estimation de l’expérience Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Pour les plans de tarification, voir [Tarification de Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Pour commencer votre déploiement de Windows Virtual Desktop, suivez [notre didacticiel](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
