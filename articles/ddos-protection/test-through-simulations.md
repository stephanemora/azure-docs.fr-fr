---
title: Test de simulation Azure DDoS Protection
description: Découvrez comment tester à l’aide de simulations
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: e95495e48725a68ab1fe3f37d235e5765b2c8015
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806237"
---
# <a name="test-through-simulations"></a>Tester à l’aide de simulations

Nous vous conseillons de tester vos hypothèses sur la façon dont vos services répondront à une attaque au moyen de simulations périodiques. Ces tests permettent de vérifier que vos services ou applications continuent à fonctionner comme prévu et que l’expérience des utilisateurs n’est pas interrompue. Identifiez les lacunes du point de vue de la technologie et du processus, et incorporez-les dans la stratégie de réponse DDoS. Nous vous recommandons d’effectuer ces tests dans des environnements intermédiaires ou durant les heures creuses afin de minimiser l’impact sur l’environnement de production.

Nous travaillons en collaboration avec [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud), un générateur de trafic en libre service, pour créer une interface à l’aide de laquelle les clients Azure peuvent générer du trafic sur les points de terminaison publics dotés de DDoS Protection à des fins de simulation. Vous pouvez utiliser la simulation pour :

- Valider la façon dont Azure DDoS Protection contribue à protéger vos ressources Azure contre les attaques DDoS.
- Optimiser votre processus de réponse aux incidents en cas d’attaque DDoS.
- Documenter la conformité DDoS.
- Former des équipes de sécurité réseau.

## <a name="prerequisites"></a>Prérequis

- Avant de pouvoir exécuter la procédure de ce tutoriel, vous devez créer un [plan de protection Azure DDoS Standard](manage-ddos-protection.md) avec des adresses IP publiques protégées.
- Vous devez d’abord créer un compte avec [BreakingPoint Cloud](http://breakingpoint.cloud/). 

## <a name="configure-a-ddos-test-attack"></a>Configurer une attaque de test DDoS

1. Entrez ou sélectionnez les valeurs suivantes, puis sélectionnez **Démarrer le test** :

    |Paramètre        |Valeur                                              |
    |---------      |---------                                          |
    |Adresse IP cible           | Entrez l’une de vos adresses IP publiques que vous voulez tester.                     |
    |Numéro de port   | Entrez _443_.                       |
    |Profil DDoS | Les valeurs possibles sont `DNS Flood`, `NTPv2 Flood`, `SSDP Flood`, `TCP SYN Flood`, `UDP 64B Flood`, `UDP 128B Flood`, `UDP 256B Flood`, `UDP 512B Flood`, `UDP 1024B Flood`, `UDP 1514B Flood`, `UDP Fragmentation`, `UDP Memcached`.|
    |Taille du test       | Les valeurs possibles sont `100K pps, 50 Mbps and 4 source IPs`, `200K pps, 100 Mbps and 8 source IPs`, `400K pps, 200Mbps and 16 source IPs`, `800K pps, 400 Mbps and 32 source IPs`.                                  |
    |Durée du test | Les valeurs possibles sont `10 Minutes`, `15 Minutes`, `20 Minutes`, `25 Minutes`, `30 Minutes`.|

Cela devrait maintenant ressembler à ceci :

![Exemple de simulation d’attaque DDoS : BreakingPoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Surveiller et valider

1. Connectez-vous à https://portal.azure.com et accédez à votre abonnement.
1. Sélectionnez l’adresse IP publique sur laquelle vous avez testé l’attaque.
1. Sous **Supervision**, sélectionnez **Métriques**.
1. Pour **Métrique**, sélectionnez _Sous attaque DDoS ou non_.

Une fois la ressource attaquée, vous devez voir que la valeur passe de **0** à **1**, comme dans l’image suivante :

![Exemple de simulation d’attaque DDoS : Portail](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

### <a name="breakingpoint-cloud-api-script"></a>Script de l’API BreakingPoint Cloud

Ce [script d’API](https://aka.ms/ddosbreakingpoint) peut être utilisé pour automatiser les tests DDoS en exécutant une seule fois ou à l’aide de cron pour planifier des tests réguliers. Cela permet de vérifier que votre journalisation est correctement configurée et que les procédures de détection et de réponse sont efficaces. Les scripts requièrent un système d’exploitation Linux (testé avec Ubuntu 18.04 LTS) et Python 3. Installez les composants requis et le client API à l’aide du script inclus ou à l’aide de la documentation sur le site Web [BreakingPoint Cloud](http://breakingpoint.cloud/) .

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [consulter et configurer la télémétrie de la protection DDoS](telemetry.md).
- Découvrez comment [afficher et configurer la journalisation des diagnostics DDoS](diagnostic-logging.md).
- Découvrez comment [faire appel à une réponse rapide DDoS](ddos-rapid-response.md).
