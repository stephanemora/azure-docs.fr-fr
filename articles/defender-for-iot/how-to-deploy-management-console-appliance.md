---
title: Déployer la console de gestion dans Azure Defender pour IoT
description: En savoir plus sur le déploiement de la console de gestion dans Azure Defender pour IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2020
ms.author: rkarlin
ms.openlocfilehash: 6af943951b2dba0c4a11b08ad1bcd961cf1c0fc2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094121"
---
# <a name="deploy-the-management-console"></a>Déployer la console de gestion
Cet article explique comment déployer la console de gestion locale Azure Defender pour IoT.

## <a name="the-on-premises-management-console"></a>Console de gestion locale

La console de gestion locale offre une vue consolidée de toutes les ressources réseau et fournit une vue en temps réel des principaux indicateurs de risque de l’Internet des objets et de la formation organisationnelle ainsi que des alertes pour toutes vos installations. Étroitement intégrée à vos workflows SOC et à vos runbooks, elle permet de hiérarchiser facilement les activités d’atténuation et de corréler les menaces entre les différents sites.

- Holistique : Réduisez la complexité grâce à une seule plateforme unifiée pour la gestion des ressources, la gestion des risques et des vulnérabilités, ainsi que l’analyse des menaces avec réponse aux incidents.

- Agrégation et corrélation : Affichez, agrégez et analysez les données et les alertes collectées sur tous les sites.

- Contrôler tous les capteurs : configurez et surveillez tous les capteurs à partir d’un emplacement unique.

   ![Vue Gestion des sites d’Azure Defender pour IoT](media/updates/image2.png)

## <a name="deploy-the-on-premises-management-console-appliance"></a>Déployer l’appliance de console de gestion locale

Ce processus nécessite l’acquisition de votre propre matériel et l’installation d’un logiciel. La solution s’exécute sur des appliances certifiées. Reportez-vous au [guide des spécifications matérielles d’Azure Defender pour IoT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) comme référence lors de l’achat de votre appliance certifiée.

Pour plus d’informations sur le téléchargement de l’image ISO et sur l’installation du logiciel de capteur, reportez-vous au [guide d’installation d’Azure Defender pour IoT](https://aka.ms/AzureDefenderforIoTInstallSensorISO).

**Pour déployer la console de gestion locale :**

1. Accédez à Microsoft Azure Defender pour IoT.

2. Sélectionnez **Console de gestion locale** .

   ![Vue Console de gestion locale d’Azure Defender pour IoT](media/updates/image15.png)

3. Sélectionnez la version 3.1 dans le menu **Sélectionner une version** .

4. Sélectionnez **Télécharger** et enregistrez le fichier.

5. Une fois le logiciel installé, effectuez les tâches de configuration du réseau. Pour plus d’informations, consultez le [guide de configuration du réseau d’Azure Defender pour IoT](https://aka.ms/AzureDefenderForIoTNetworkSetup).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les options de configuration, accédez au guide pratique de configuration du module.
> [!div class="nextstepaction"]
> [Guide pratique de configuration du module](./how-to-agent-configuration.md)
