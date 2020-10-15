---
title: Forum aux questions sur Defender for IoT
description: Trouvez des réponses à la plupart des questions fréquemment posées sur les fonctionnalités et le service Azure Defender pour IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 97dd4e13754175e9c81ae308b86faae811e4d554
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90930603"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Forum aux questions sur Azure Defender pour IoT

Cet article fournit une liste de questions fréquemment posées et les réponses correspondantes au sujet de Defender pour IoT.

## <a name="does-azure-provide-support-for-iot-security"></a>Azure fournit-il la prise en charge de la sécurité IoT ?

Azure fournit une vue intégrée pour la surveillance et gestion de la sécurité IoT dans le cadre de votre solution de sécurité globale via Azure Security Center. Si vous êtes un développeur d’applications, vous pouvez utiliser la vue IoT Hub pour gérer la sécurité de vos applications IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Quelle est la proposition de valeur unique d’Azure pour la sécurité IoT ?

Defender pour IoT permet aux entreprises d’étendre leur vue de la cybersécurité existante à la totalité des solutions IoT. Azure fournit une vue de bout en bout de votre solution d’entreprise, ce qui vous permet de prendre des mesures et décisions professionnelles en fonction de la posture de sécurité de votre entreprise et des données collectées. La sécurité combinée d’Azure IoT, Azure IoT Edge et Azure Security Center vous permet de créer la solution que vous souhaitez avec la sécurité dont vous avez besoin.

## <a name="who-is-defender-for-iot-made-for"></a>À qui la solution Defender pour IoT est-elle destinées ?

Defender pour IoT est intégré au sein de la sécurité d’Azure IoT Hub, et assure la gestion des opérations de sécurité quotidiennes de la solution d’entreprise. Defender pour IoT est également intégré aux fonctionnalités d’Azure Security Center et fournit une vue intégrée pour la surveillance et la gestion de la sécurité IoT dans le cadre de votre solution de sécurité globale.

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Comment Defender pour IoT se compare-t-il par rapport à la concurrence ?

Là où les autres solutions fournissent un ensemble de fonctionnalités qui permettent aux clients de créer leurs propres solutions, Defender pour IoT fournit une solution de sécurité IoT de bout en bout unique qui offre une vue plus large sur la sécurité de toutes vos ressources liées à Azure. Azure permet un déploiement rapide et une intégration complète avec les jumeaux de module IoT Hub, pour une intégration aisée avec les outils de gestion des appareils existants.

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>Dois-je être un client Azure Security Center pour utiliser ce service ?

Non, mais cela est recommandé. Sans Azure Security Center, Defender pour IoT reçoit des données de ressources connectées limitées et fournit une analyse limitée de la surface d’attaque potentielle, des menaces et des attaques potentielles.

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Dois-je être un client Azure IoT ?

Oui. Defender pour IoT repose sur l’infrastructure et la connectivité d’Azure IoT.

## <a name="do-i-have-to-install-an-agent"></a>Dois-je installer un agent ?

L’installation de l’agent sur vos appareils IoT n’est pas obligatoire pour activer Defender pour IoT. Vous pouvez choisir entre les trois options suivantes, pour des niveaux de sécurité et des fonctionnalités de surveillance différentes selon votre choix :

1. Installez l’agent de sécurité Defender pour IoT avec ou sans modifications. Cette option fournit le plus haut niveau d’informations de sécurité améliorées pour le comportement et l’accès à l’appareil.

1. Créez votre propre agent et implémentez le schéma de message de sécurité Defender pour IoT. Cette option permet d’utiliser les outils d’analyse de Defender pour IoT en plus de l’agent de sécurité de votre appareil.

1. Aucune installation de l’agent de sécurité sur vos appareils IoT. Cette option permet de surveiller les communications d’IoT Hub, avec des fonctionnalités de surveillance et de gestion de la sécurité limitées.

## <a name="what-does-the-defender-for-iot-agent-do"></a>Que fait l’agent Defender pour IoT ?

L’agent Defender pour IoT couvre les menaces au niveau de l’appareil en matière de configuration, de comportement, d’accès (en analysant la configuration), de traitement et de connectivité. L’agent de sécurité Defender pour IoT n’analyse pas les données ou l’activité de l’entreprise.

## <a name="where-can-i-get-the-defender-for-iot-security-agent"></a>Où puis-je obtenir l’agent de sécurité Defender pour IoT ?

L’agent de sécurité Defender pour IoT est open source et disponible sur GitHub en versions 32 et 64 bits pour Windows et Linux : https://github.com/Azure/Azure-IoT-Security.

## <a name="where-does-the-defender-for-iot-agent-get-installed"></a>Où l’agent Defender pour IoT est-il installé ?

Vous trouverez des informations détaillées sur l’installation et le déploiement de l’agent sur GitHub : https://github.com/Azure/Azure-IoT-Security.

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Quelles sont les dépendances et les conditions requises de l’agent ?

Defender pour IoT prend en charge un vaste éventail de plateformes. Consultez les [plateformes d’appareils pris en charge](how-to-deploy-agent.md) pour vérifier la prise en charge de vos appareils spécifiques.

## <a name="which-data-is-collected-by-the-agent"></a>Quelles données sont collectées par l’agent ?

Des données de connectivité, d’accès, de configuration du pare-feu, de liste de processus et de ligne de base du système d’exploitation sont collectées par l’agent.

## <a name="how-much-data-will-the-agent-generate"></a>Quelle quantité de données l’agent générera-t-il ?

La génération de données de l’agent est déterminée par l’appareil, l’application, le type de connectivité et la configuration de l’agent client. En raison d’une forte variabilité entre les appareils et solutions IoT, nous vous recommandons de commencer par déployer l’agent dans un laboratoire ou un environnement de test pour observer, apprendre et déterminer la configuration spécifique qui répond à vos besoins en mesurant la quantité de données générées. Après le démarrage du service, l’agent Defender pour IoT fournit des recommandations opérationnelles permettant d’optimiser le débit de l’agent pour vous aider dans le processus de configuration et de personnalisation.

## <a name="how-can-i-control-my-billing"></a>Comment puis-je contrôler ma facturation ?

Defender pour IoT fournit des analyses d’agent configurables, des tampons de données et la possibilité de créer des alertes personnalisées pour augmenter ou réduire la quantité de données générées par l’agent.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Les messages de l’agent exploitent-ils le quota d’IoT Hub ?

Oui. Les données transmises par l’agent sont comptabilisées dans le quota de votre IoT Hub.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Étapes suivantes J’ai installé un agent et je ne vois pas d’activités ou de journaux...

1. Vérifiez que le [type d’agent correspond à la plateforme de système d’exploitation désignée de votre appareil](how-to-deploy-agent.md)

1. Confirmez que [l’agent est en cours d’exécution sur l’appareil](how-to-agent-configuration.md).

1. Vérifiez que le [service a été activé avec succès](quickstart-onboard-iot-hub.md) sur **Sécurité** dans votre IoT Hub.

1. Vérifiez que l’appareil est [configuré dans IoT Hub avec le module Defender pour IoT](quickstart-create-security-twin.md).

Si les activités ou les journaux ne sont toujours pas disponibles, contactez votre partenaire Defender pour IoT pour lui demander une aide supplémentaire.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Que se passe-t-il lorsque la connexion Internet cesse de fonctionner ?

L’agent continue à s’exécuter et à stocker des données, tant que l’appareil est en cours d’exécution. Les données sont stockées dans le cache de messages de sécurité en fonction de la configuration de taille. Lorsque l’appareil retrouve sa connectivité, les messages de sécurité sont à nouveau envoyés.

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Si l’appareil est redémarré, l’agent de sécurité récupérera-t-il automatiquement ?

L’agent de sécurité est conçu pour se réexécuter automatiquement avec chaque redémarrage de l’appareil.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>L’agent peut-il affecter les performances de l’appareil ou des autres logiciels installés ?

L’agent consomme des ressources informatiques comme n’importe quel autre processus/application et ne devrait pas perturber l’activité normale de l’appareil. La consommation de ressources sur l’appareil sur lequel l’agent s’exécute dépend de son installation et de sa configuration. Nous vous recommandons de tester votre configuration d’agent dans un environnement contenu, ainsi que son interopérabilité avec les autres applications et fonctionnalités IoT avant de tenter de la déployer dans un environnement de production.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>J’effectue une maintenance sur l’appareil. Puis-je désactiver l’agent ?

L’agent ne peut pas être désactivé.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Existe-t-il un moyen de vérifier que l’agent fonctionne correctement ?

Si l’agent cesse de communiquer ou ne parvient pas à envoyer des messages de sécurité, une alerte **L’appareil est silencieux** est générée.

## <a name="can-i-create-my-own-alerts"></a>Puis-je créer mes propres alertes ?

Oui. Vous pouvez définir une alerte personnalisée pour un ensemble prédéfini de comportements, comme l’adresse IP et les ports ouverts. Consultez [Créer des alertes personnalisées](quickstart-create-custom-alerts.md) pour en savoir plus sur les alertes personnalisées et la façon de les créer.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Où puis-je voir les journaux ? Puis-je personnaliser les journaux ?

- Affichez les alertes et recommandations depuis votre espace de travail Log Analytics. Configurez la taille de stockage et la durée dans l’espace de travail.

- Les données brutes de votre agent de sécurité peuvent également être stockées dans votre compte Log Analytics. Envisagez les exigences de taille, durée et stockage ainsi que les coûts associés avant de modifier la configuration de cette option.

## <a name="why-should-i-add-defender-for-iot-to-the-module-identity-what-is-it-used-for"></a>Pourquoi dois-je ajouter Azure Defender pour IoT à l’identité de module ? À quoi sert-il ?

Le module Defender pour IoT est utilisé pour la configuration et la gestion de l’agent.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la prise en main de Defender pour IoT, consultez la documentation suivante :

- Lire la [Vue d’ensemble de Defender pour IoT](overview.md)
- Vérifier les [conditions préalables du service](service-prerequisites.md)
- En savoir plus sur la [prise en main](getting-started.md)
- Comprendre les [Alertes de sécurité de Defender pour IoT](concept-security-alerts.md)
