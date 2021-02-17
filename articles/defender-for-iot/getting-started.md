---
title: Mise en route
description: Découvrez le flux de travail de base de déploiement de Defender pour IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/26/2020
ms.author: shhazam
ms.openlocfilehash: 06cf8b5f35bdebed0ebaa9e6949261d7c0d516f2
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522561"
---
# <a name="get-started-with-defender-for-iot"></a>Bien démarrer avec Defender pour IoT

Cet article donne une vue d’ensemble de la procédure permettant de configurer Azure Defender pour IoT. En voici les prérequis :

- Inscrivez votre abonnement et les capteurs sur le Portail Azure Defender pour IoT.
- Installez le capteur et le logiciel de la console de gestion locale.
- Effectuez l’activation initiale du capteur et de la console de gestion.

## <a name="permission-requirements"></a>Spécifications relatives aux autorisations

Certaines des étapes de configuration exigent des autorisations d’utilisateur spécifiques.

Des autorisations d’utilisateur administratif sont requises pour activer le capteur et la console de gestion, charger des certificats SSL/TLS et générer de nouveaux mots de passe.

Le tableau suivant décrit les autorisations d’accès utilisateur aux outils du portail Azure Defender pour IoT :

| Autorisation | Lecteur de sécurité | Administrateur de sécurité | Collaborateur de l’abonnement | Propriétaire de l’abonnement |
|--|--|--|--|--|
| Afficher tous les écrans et toutes les données Defender pour IoT | ✓ | ✓ | ✓ | ✓ |
| Intégrer un capteur  |  |  ✓ | ✓ | ✓ |
| Mettre à jour le tarif  |  |  ✓ | ✓ | ✓ |
| Récupérer le mot de passe  | ✓  |  ✓ | ✓ | ✓ |

## <a name="1-identify-the-solution-infrastructure"></a>1. Identification de l’infrastructure de la solution

**Clarification des besoins de configuration réseau**

Documentez votre architecture réseau, la bande passante analysée et d’autres informations sur le réseau. Pour plus d’informations, consultez [À propos de la configuration réseau d’Azure Defender pour IoT](how-to-set-up-your-network.md).

**Clarification des capteurs et des appliances de la console de gestion nécessaires pour gérer la charge réseau**

Azure Defender pour IoT prend en charge les déploiements physiques et virtuels. Dans le cas des déploiements physiques, vous pouvez acheter plusieurs appliances certifiées. Pour plus d’informations, consultez [Identification des appliances requises](how-to-identify-required-appliances.md).

Nous vous recommandons de calculer le nombre approximatif d’appareils qui seront analysés. Plus tard, lorsque vous enregistrerez votre abonnement Azure sur le portail, il vous sera demandé d’entrer ce nombre. Ils peuvent être ajoutés par intervalles de 1 000 secondes. Le nombre d’appareils analysés est appelé *appareils validés*.

## <a name="2-register-with-azure-defender-for-iot"></a>2. Inscription auprès d’Azure Defender pour IoT

L’inscription comprend plusieurs opérations :

- Intégration des abonnements Azure à Defender pour IoT
- Définition des appareils validés
- Téléchargement d’un fichier d’activation pour la console de gestion locale

Pour vous inscrire :

1. Accédez au portail Azure Defender pour IoT.
1. Sélectionnez **Intégrer un abonnement**.
1. Sur la page **Tarifs**, sélectionnez un abonnement ou créez-en un, puis ajoutez le nombre d’appareils validés.
1. Sélectionnez l’onglet **Téléchargez la console de gestion locale** et enregistrez le fichier d’activation téléchargé. Ce fichier contient les appareils validés agrégés que vous avez définis. Il sera chargé dans la console de gestion après la connexion initiale.

## <a name="3-install-and-set-up-the-on-premises-management-console"></a>3. Installation et configuration de la console de gestion locale

Après avoir fait l’acquisition de votre appliance de console de gestion locale, procédez comme suit :

- Téléchargez le package ISO sur le portail Azure Defender pour IoT.
- Installez le logiciel.
- Activez la console de gestion et effectuez la configuration initiale.

Pour installer et configurer la console de gestion, procédez comme suit :

1. Sélectionnez **Bien démarrer** sur le portail Defender pour IoT.
1. Sélectionnez l’onglet **Console de gestion locale**.
1. Choisissez une version et sélectionnez **Télécharger**.
1. Installez le logiciel de la console de gestion locale. Pour plus d’informations, consultez [Installation de Defender pour IoT](how-to-install-software.md).
1. Activez et configurez la console de gestion. Pour plus d’informations, consultez [Activation et configuration de la console de gestion locale](how-to-activate-and-set-up-your-on-premises-management-console.md).

## <a name="4-onboard-a-sensor"></a>4. Intégrer un capteur

Intégrez un capteur en l’inscrivant auprès d’Azure Defender pour IoT et en téléchargeant un fichier d’activation de capteur :

1. Définissez un nom de capteur et associez-le à un abonnement.
1. Choisissez un mode d’administration du capteur :

   - **Capteurs connectés au cloud** : les informations détectées par les capteurs s’affichent dans la console de ces derniers. Par ailleurs, les données des alertes, transmises par le biais d’un hub IoT, sont partageables avec d’autres services Azure, comme Azure Sentinel.

   - **Capteurs gérés localement** : les informations détectées par les capteurs s’affichent dans la console de ces derniers. Si vous travaillez sur un réseau en air gap et que vous souhaitez une vue unifiée de toutes les informations détectées par plusieurs capteurs gérés localement, utilisez la console de gestion locale. 

1. Téléchargez un fichier d’activation de capteur.

Pour plus d’informations, consultez [Intégration et gestion des capteurs sur le portail Defender for IoT](how-to-manage-sensors-on-the-cloud.md).

## <a name="5-install-and-set-up-the-sensor"></a>5. Installation et configuration du capteur

Téléchargez le package ISO sur le portail Azure Defender pour IoT, installez le logiciel et configurez le capteur.

1. Sélectionnez **Bien démarrer** sur le portail Defender pour IoT.
1. Sélectionnez **Configurer le capteur**.
1. Choisissez une version et sélectionnez **Télécharger**.
1. Installez le logiciel du capteur. Pour plus d’informations, consultez [Installation de Defender pour IoT](how-to-install-software.md).
1. Activez et configurez votre capteur. Pour plus d’informations, consultez [Connexion à un capteur et activation](how-to-activate-and-set-up-your-sensor.md).

## <a name="6-connect-sensors-to-an-on-premises-management-console"></a>6. Connexion des capteurs à une console de gestion locale

Connectez les capteurs à la console de gestion. L’objectif est double :

- Les capteurs envoient les informations d’inventaire d’alerte et d’inventaire des appareils à la console de gestion locale.

- La console de gestion locale peut effectuer des sauvegardes de capteurs, gérer des alertes détectées par ces derniers, examiner leurs déconnexions et effectuer d’autres activités sur les capteurs connectés.

Nous vous recommandons de regrouper dans une même zone les capteurs qui surveillent les mêmes réseaux. Les informations collectées par ces capteurs seront ainsi fusionnées.

Pour plus d’informations, consultez [Connexion des capteurs à la console de gestion locale](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console).

## <a name="7-populate-azure-sentinel-with-alert-information-optional"></a>7. Remplissage des informations d’alerte dans Azure Sentinel (facultatif)

Envoyez des informations d’alerte à Azure Sentinel en configurant ce dernier. Consultez [Connexion des données de Defender pour IoT à Azure Sentinel](how-to-configure-with-sentinel.md).
## <a name="next-steps"></a>Étapes suivantes

[Bienvenue sur Azure Defender pour IoT](overview.md)

[Architecture de Azure Defender pour IoT](architecture.md)
