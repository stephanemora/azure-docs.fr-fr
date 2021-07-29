---
title: Latence de connexion des utilisateur de Azure Virtual Desktop – Azure
description: Latence de connexion pour les utilisateurs de Azure Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: ba4db9048154af2562d2c3b7431d2a75ba3c036f
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111747380"
---
# <a name="determine-user-connection-latency-in-azure-virtual-desktop"></a>Déterminer la latence de connexion pour les utilisateurs dans Azure Virtual Desktop

Azure Virtual Desktop est disponible dans le monde entier. Les administrateurs peuvent créer des machines virtuelles (VM) dans la région Azure de leur choix. La latence de connexion varie en fonction de l’emplacement des utilisateurs et des machines virtuelles. Les services de Azure Virtual Desktop se déploient en permanence vers de nouvelles zones géographiques pour améliorer la latence.

[L’outil d’estimation de l’expérience Azure Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/) peut vous aider à déterminer le meilleur emplacement pour optimiser la latence de vos machines virtuelles. Nous vous recommandons d’utiliser l’outil tous les deux à trois mois pour vous assurer que l’emplacement optimal n’a pas changé lors du développement de Azure Virtual Desktop vers de nouvelles zones.

## <a name="interpreting-results-from-the-azure-virtual-desktop-experience-estimator-tool"></a>Interprétation des résultats de l’outil Estimateur d’expérience de Azure Virtual Desktop

Dans Azure Virtual Desktop, une latence de jusqu’à 150 ms ne devrait pas avoir d’incidence sur une expérience utilisateur qui n’implique pas de rendu ou de vidéo. Des latences entre 150 et 200 ms devraient convenir au traitement de texte. Une latence supérieure à 200 ms peut avoir un impact sur l’expérience utilisateur. 

En outre, la connexion Azure Virtual Desktop dépend de la connexion Internet de l’ordinateur à partir duquel l’utilisateur utilise le service. Les utilisateurs risquent de perdre la connexion ou de subir un délai d’entrée dans les des situations suivantes :

 - L’utilisateur ne dispose pas d’une connexion Internet locale stable et la latence est supérieure à 200 ms.
 - Le réseau est saturé ou limité en débit.

Nous vous recommandons de choisir les emplacements de machines virtuelles les plus proches possible de vos utilisateurs. Par exemple, si l’utilisateur se trouve en Inde, mais que la machine virtuelle se trouve aux États-Unis, il y aura une latence qui affectera l’expérience globale de l’utilisateur. 

## <a name="azure-front-door"></a>Azure Front Door

Azure Virtual Desktop utilise [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) pour rediriger la connexion utilisateur vers la passerelle Azure Virtual Desktop la plus proche en fonction de l’adresse IP source. Azure Virtual Desktop utilise toujours la passerelle Azure Virtual Desktop choisie par le client.

## <a name="next-steps"></a>Étapes suivantes

- Pour vérifier le meilleur emplacement pour une latence optimale, voir [l’outil d’estimation de l’expérience Azure Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Pour les plans de tarification, voir [Tarification de Azure Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Pour commencer votre déploiement de Azure Virtual Desktop, suivez [notre didacticiel](./create-host-pools-azure-marketplace.md).