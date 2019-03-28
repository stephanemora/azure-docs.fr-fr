---
title: Forum aux questions sur ASC pour IoT Preview | Microsoft Docs
description: Trouvez des réponses à la plupart des questions fréquemment posées sur ASC pour les fonctionnalités de service et IoT.
services: ASCforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.service: ASCforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 1eba32683883e60ae48f4d6fec9c434c27ce55da
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541931"
---
# <a name="asc-for-iot-frequently-asked-questions"></a>Questions fréquentes sur ASC pour IoT  

> [!IMPORTANT]
> ASC pour IoT est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article fournit une liste de questions fréquemment posées et des réponses sur ASC pour IoT. 

## <a name="does-azure-provide-support-for-iot-security"></a>Azure fournit un support pour la sécurité IoT ?

Azure fournit une vue intégrée pour la surveillance et gestion de la sécurité IoT dans le cadre de votre solution de sécurité globale via Azure Security Center. Si vous êtes un développeur d’applications, vous pouvez utiliser la vue IoT Hub pour gérer la sécurité de vos applications IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Quelle est la proposition de valeur unique d’Azure pour la sécurité IoT ?

ASC pour IoT permet aux entreprises d’étendre leur vue de sécurité cyber existante à la totalité des solutions IoT. Azure fournit une vue de bout en bout de votre solution d’entreprise, ce qui vous permet de prendre des mesures professionnelles et prendre des décisions en fonction de votre posture de sécurité d’entreprise et les données collectées. Associé à l’aide d’Azure IoT, Azure IoT Edge, Azure Sphere, Azure Central de sécurité, et Azure Security Center permettent de créer la solution que vous souhaitez avec la sécurité que vous avez besoin.

## <a name="who-is-asc-for-iot-made-for"></a>Qui est ASC pour IoT apportées ? 

ASC pour IoT est intégré au sein de la sécurité d’Azure IoT Hub et fournit la solution activités quotidiennes de gestion pour les opérations de sécurité. ASC pour IoT est également intégré aux fonctionnalités Azure Security Center et fournissent une vue intégrée pour la surveillance et gestion de la sécurité IoT dans le cadre de votre solution de sécurité globale.

## <a name="how-does-asc-for-iot-compare-to-the-competition"></a>Comment ASC pour IoT peut-il être comparé à la concurrence ?

Tandis que les autres solutions fournissent un ensemble de fonctionnalités qui permettent aux clients de créer leurs propres solutions, ASC pour IoT fournit une solution de sécurité d’une IoT de bout en bout unique qui fournit un affichage plus large sur la sécurité de toutes vos ressources Azure connexes. Azure permet un déploiement rapide et une intégration complète avec les jumeaux de module IoT Hub pour une intégration aisée avec les outils de gestion des appareils existants.

## <a name="do-i-have-to-be-an-azure-security-center-asc-customer"></a>Je dois être un client Azure Security Center (ASC) ?

Non, mais il est recommandé. Sans ASC, ASC pour IoT reçoit les données de la ressource connectée limitée et fournit une analyse limitée de la surface d’attaque potentielle, des menaces et des attaques potentielles. 

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Je dois être un client Azure IoT ?

Oui. ASC pour IoT s’appuie sur l’infrastructure et connectivité d’Azure IoT.

## <a name="do-i-have-to-install-an-agent"></a>Je dois installer un agent ?

Installation de l’agent sur vos appareils IoT n’est pas obligatoire pour permettre l’ASC Microsoft pour IoT. Vous pouvez choisir entre trois options suivantes, obtenir des fonctionnalités de surveillance et de gestion en fonction de votre sélection de différents niveaux de sécurité :

1. Installez l’ASC pour l’agent de sécurité IoT avec ou sans modifications. Cette option fournit le plus haut niveau de perspectives sur la sécurité améliorée dans le comportement de l’appareil et l’accès.

2. Créer votre propre agent et implémenter Microsoft ASC pour schéma de message de sécurité IoT. Cette option permet l’utilisation de Microsoft ASC pour les outils d’analyse IoT sur votre agent de sécurité de périphérique.

3. Aucune installation de l’agent de sécurité sur vos appareils IoT. Cette option permet de surveiller de communication IoT Hub, avec les fonctionnalités de surveillance et de gestion des problèmes de sécurité. 

## <a name="what-does-the-asc-for-iot-agent-do"></a>À quoi sert l’ASC pour l’agent IoT ?

ASC pour l’agent IoT couvre appareil contre les menaces au niveau de configuration de l’appareil, comportement et (par la configuration de l’analyse), processus de connectivité et d’accès. L’ASC pour l’agent de sécurité IoT n’analyse pas les données professionnelles ou activité.

## <a name="where-i-can-get-the-asc-for-iot-security-agent"></a>Où puis-je obtenir le ASC pour l’agent de sécurité IoT ?

L’ASC pour l’agent de sécurité IoT est open source et disponibles sur GitHub dans 32 bits et les versions de Windows et Linux 64 bits : https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="where-does-the-asc-for-iot-agent-get-installed"></a>Où l’ASC pour l’agent IoT n’est installé ? 

Vous trouverez des informations de déploiement détaillées installation et de l’agent dans GitHub : https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Quelles sont les dépendances et les conditions préalables de l’agent ?

ASC pour IoT prend en charge un large éventail de plateformes. Consultez [plateformes d’appareils pris en charge](select-deploy-agent.md) pour vérifier la prise en charge de vos appareils spécifiques. 

## <a name="which-data-is-collected-by-the-agent"></a>Les données sont collectées par l’agent ?

Connectivité, accès, configuration du pare-feu, liste des processus et ligne de base du système d’exploitation sont collectées par l’agent.

## <a name="how-much-data-will-the-agent-generate"></a>La quantité de données générera l’agent ?

Génération de données de l’agent est déterminée par l’appareil, application, type de connectivité et configuration de l’agent client. En raison d’une forte variabilité entre les appareils et les solutions IoT, nous vous recommandons de premier déploiement de l’agent dans un laboratoire ou un paramètre de test pour observer, en savoir plus et définir la configuration spécifique qui répond à vos besoins, lors de la mesure la quantité de données générées. Après le démarrage du service, l’ASC pour l’agent IoT fournit des recommandations opérationnelles permettant d’optimiser le débit de l’agent pour vous aider dans le processus de configuration et de personnalisation.

## <a name="how-can-i-control-my-billing"></a>Comment puis-je contrôler ma facturation ?

ASC pour IoT fournit agent configurable analyses, les mémoires tampons de données et la possibilité de créer des alertes personnalisées pour augmenter ou réduisent la quantité de données générées par l’agent.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Utilisent les messages de l’agent de quota à partir d’IoT Hub ?

Oui. Données de l’agent transmis sont comptabilisées dans le quota de votre IoT Hub. 

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Étapes suivantes J’ai installé un agent et que vous ne voyez pas toutes les activités ou les journaux

1. Vérifier la [type d’agent correspond à la plateforme du système d’exploitation désignée de votre appareil](select-deploy-agent.md)

1. Confirmer la [agent est en cours d’exécution sur l’appareil](concept-agent-configuration.md).

2. Vérifier le [service a été activé avec succès](quickstart-onboard-iot-hub.md) à **sécurité** dans votre IoT Hub. 

3. Vérifiez que l’appareil est [configuré dans IoT Hub avec l’ASC pour le module IoT](quickstart-create-security-twin.md).  

Si les activités ou les journaux sont toujours pas disponibles, contactez votre ASC pour partenaire IoT pour obtenir une aide supplémentaire.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Que se passe-t-il lorsque la connexion internet cesse de fonctionner ?

L’agent continue à s’exécuter et de stocker des données, que l’appareil est en cours d’exécution. Données sont stockées dans le cache de sécurité de message en fonction de la configuration de la taille. Lorsque l’appareil a regagné connectivité, les messages de sécurité reprend l’envoi. 

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Si l’appareil est redémarré, l’agent de sécurité automatique récupérera ?

L’agent de sécurité est conçu pour réexécuter automatiquement avec chaque redémarrage du périphérique.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>L’agent peut affecter les les performances de l’appareil ou d’autres logiciels installés ?

L’agent consomme des ressources de l’ordinateur en tant que n’importe quel autre application/processus et doit perturbent pas l’activité de l’appareil normal. La consommation des ressources sur l’appareil de que l’agent s’exécute est associée à son installation et la configuration. Nous vous recommandons de tester votre configuration de l’agent dans un environnement de relation contenant-contenu, ainsi que l’interopérabilité avec d’autres applications IoT et fonctionnalités, avant de tenter de le déployer dans un environnement de production.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Je rends une maintenance sur l’appareil. Puis-je désactiver l’agent ?

L’agent ne peut pas être désactivé.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Existe-t-il un moyen de tester si l’agent fonctionne correctement ? 

Si l’agent cesse de communication ou ne parvient pas à envoyer des messages de sécurité, un **appareil est en mode silencieux** alerte est générée.

## <a name="can-i-create-my-own-alerts"></a>Puis-je créer mes propres alertes ?

Oui. Vous pouvez définir une alerte personnalisée sur un ensemble prédéfini de comportements tels que l’adresse IP et les ports ouverts. Consultez [créer des alertes personnalisées](quickstart-create-custom-alerts.md) pour en savoir plus sur les alertes personnalisées et comment les créer. 

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Où puis-je voir les journaux ? Puis-je personnaliser les journaux ?

- Afficher les alertes et recommandations à l’aide de votre espace de travail Analytique de journal connecté. Configurer la taille de stockage et la durée dans l’espace de travail.

- Données brutes à partir de votre agent de sécurité peuvent également être stockées dans votre compte Analytique de journal. Envisagez de taille, la durée, les exigences de stockage et les coûts associés avant de modifier la configuration de cette option. 

## <a name="why-should-i-add-asc-for-iot-to-the-module-identity-what-is-it-used-for"></a>Pourquoi dois-je ajouter ASC pour IoT à l’identité de module ? À quoi sert-il ?

L’ASC pour le module IoT est utilisé pour la gestion et la configuration de l’agent.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la prise en main avec ASC pour IoT, consultez les articles suivants :


- Lire l’ASC pour IoT [vue d’ensemble](overview.md)
- Vérifiez le [conditions préalables de Service](service-prerequisites.md)
- En savoir plus sur comment [prise en main](getting-started.md)
- Comprendre [ASC pour les alertes de sécurité IoT](concept-security-alerts.md)

