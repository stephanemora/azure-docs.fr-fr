---
title: Recommandations relatives à la configuration et à la sécurité du pare-feu Azure Percept
description: En savoir plus sur les recommandations relatives à la configuration et à la sécurité du pare-feu Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 9564160698b1f1bf17fc89ec13b1e292aa3b6098
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108137542"
---
# <a name="azure-percept-firewall-configuration-and-security-recommendations"></a>Recommandations relatives à la configuration et à la sécurité du pare-feu Azure Percept

Consultez les instructions ci-dessous pour plus d’informations sur la configuration des pare-feu et les meilleures pratiques de sécurité générale avec Azure Percept.

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Configuration des pare-feu pour le DK Azure Percept

Si votre configuration réseau exige l’autorisation explicite des connexions établies à partir d’appareils du DK Azure Percept, consultez la liste suivante de composants IoT Edge.

Cette liste de vérification est un point de départ pour les règles de pare-feu :

|URL (* = caractère générique)|Ports TCP sortants|Usage|
|-------------------|------------------|---------|
|*.auth.azureperceptdk.azure.net|443|Authentification et autorisation Azure AD du SoM Azure DK|
|*.auth.projectsantacruz.azure.net|443|Authentification et autorisation Azure AD du SoM Azure DK|

Ensuite, passez en revue la liste des [connexions utilisées par Azure IoT Edge](../iot-edge/production-checklist.md#allow-connections-from-iot-edge-devices).

## <a name="additional-recommendations-for-deployment-to-production"></a>Recommandations supplémentaires concernant le déploiement en production

Le DK Azure Percept offre une grande variété de fonctionnalités de sécurité prêtes à l’emploi. Outre les puissantes fonctionnalités de sécurité qui sont incluses dans la version actuelle, Microsoft suggère également de suivre les instructions ci-dessous si vous prévoyez des déploiements de production :

- Protection physique renforcée de l’appareil
- Activation du chiffrement des données au repos
- Supervision continue de la posture des appareils et réponse rapide aux alertes
- Limitation du nombre d’administrateurs ayant accès à l’appareil

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez la sécurité Azure Percept](./overview-percept-security.md)