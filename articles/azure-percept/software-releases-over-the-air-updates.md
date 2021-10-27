---
title: Versions logicielles pour les mises à jour Azure Percept DK OTA
description: Informations et liens de téléchargement pour les packages de mise à jour à distance (OTA) Azure Percept DK
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: 1eb6f41d738f7ab5a2dbd68fab341c2b7c9917ad
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001160"
---
# <a name="software-releases-for-ota-updates"></a>Versions de logiciel (mises à jour OTA)

La mise à jour OTA est conçue pour les utilisateurs qui ont tendance à toujours maintenir à jour le kit de développement. C’est pourquoi seules les versions à arrêt fixe et la dernière version sont fournies ici. Pour remplacer votre kit de développement par une version spécifique (plus ancienne), utilisez la mise à jour du câble USB. Découvrez comment [Mettre à jour Azure Percept DK via une connexion par câble USB-C](./how-to-update-via-usb.md). Utilisez également la mise à jour USB si vous souhaitez accéder à une version plus avancée.

>[!CAUTION]
>Le kit de développement ne prend pas en charge la version antérieure de SW avec OTA. La Mise à jour de l’appareil pour l’infrastructure Hub IoT ne bloquera PAS le déploiement d’une image avec une version antérieure à la version actuelle. Toutefois, le faire dans le kit de développement peut entraîner la perte de données et de fonctionnalités.

>[!IMPORTANT]
>Veillez à consulter le document suivant avant de choisir de mettre à jour par câble OTA ou USB.
>
>[Comment déterminer votre stratégie de mise à jour](./how-to-determine-your-update-strategy.md)

## <a name="hard-stop-version-of-ota"></a>Version à arrêt forcé de l’OTA

Microsoft peut traiter chaque version du kit de développement avec des packages OTA. Toutefois, comme il existe des modifications avec rupture pour le système d’exploitation ou le microprogramme du kit de développement, ou la plate-forme OTA, l’utilisation de l’OTA directement d’une ancienne version à une version largement avancée peut être problématique. En général, lorsqu’une modification avec rupture se produit, Microsoft s’assure que le processus de mise à jour OTA passe l’ancien système en toute transparence à **la toute première version qui introduit/remet cette modification avec rupture**. Cette version spécifique devient une version à arrêt forcé pour l’OTA. Prenez une version à arrêt forcé connue : **version de juin** par exemple. L’OTA fonctionne si un utilisateur met à jour le kit de développement de 2104 à 2106, puis de 2106 à 2107. Toutefois, cela ne fonctionnera PAS si un utilisateur tente d’ignorer l’arrêt forcé (2106) et de mettre à jour le kit de développement de 2104 directement à 2107.

:::image type="content" source="./media/azure-percept-devkit-software-releases-ota-update/hard-stop-illustrate.png" alt-text="Version à arrêt forcé de l’OTA":::

## <a name="recommendations-for-applying-the-ota-update"></a>Recommandations pour l’application de la mise à jour OTA

**Scénario 1 :** Mise à jour fréquente (mensuelle) du kit de développement pour vous assurer qu’il est toujours à jour

- Il ne devrait pas y avoir de problème si vous effectuez toujours l’OTA pour mettre à jour le kit de développement de la dernière version vers la version récemment publiée.

**Scénario 2 :** Mise à jour alors que certaines versions peuvent être ignorées.

1. Identifier la version logicielle actuelle du kit de développement.
1. Passez en revue la liste des versions de package OTA pour rechercher toute version à arrêt forcé entre la version actuelle et la version cible.
    - S’il en existe, vous devez déployer séquentiellement la ou les versions à arrêt forcé jusqu’à ce que vous puissiez déployer le dernier package de mise à jour.
    - S’il n’en existe aucune, vous pouvez déployer directement le dernier package OTA dans le kit de développement.

## <a name="identify-the-current-software-version-of-dev-kit"></a>Identifier la version logicielle actuelle du kit de développement

**Option 1 :**

1. Connectez-vous à [Azure Percept Studio](./overview-azure-percept-studio.md).
1. Dans **Appareils**, choisissez votre appareil de kit de développement.
1. Dans l’onglet **Général**, recherchez les informations relatives au **modèle** et à la **version du logiciel**.

**Option 2 :**

1. Affichez l’**appareil Edge IoT** du service **Hub IoT** à partir du portail Microsoft Azure.
1. Choisissez votre appareil de kit de développement dans la liste des appareils.
1. Sélectionnez **Jumeau d’appareil**.
1. Faites défiler les propriétés de représentation d’appareil et recherchez **model** et **swVersion** sous **deviceInformation**, et notez leurs valeurs.

## <a name="identify-the-ota-packages-to-be-deployed"></a>Identifier le ou les package(s) OTA à déployer

>[!IMPORTANT]
>Si la version actuelle de votre kit de développement n’est pas incluse dans les versions ci-dessous, elle n’est pas prise en charge pour la mise à jour OTA. Pour obtenir la dernière version, effectuez une mise à jour du câble USB.

**Dernière version :**

|Libérer|Version(s) applicable(s)|Liens de téléchargement|Notes|
|---|---|---|---|
|Version du service de septembre (2109)|2021.106.111.115,<br>2021.107.129.116|[Package de mise à jour OTA 2021.109.129.108](https://go.microsoft.com/fwlink/?linkid=2174634)||

**Mises en production à arrêt fixe :**

|Libérer|Version(s) applicable(s)|Liens de téléchargement|Notes|
|---|---|---|---|
|Version du service de juin (2106)|2021.102.108.112, 2021.104.110.103, 2021.105.111.122 |[2021.106.111.115 Manifeste OTA (pour PE-101)](https://download.microsoft.com/download/d/f/0/df0f17dc-d2fb-42ff-aaa5-98edf4d6d1e8/aduimportmanifest_PE-101_2021.106.111.115_v3.json)<br>[2021.106.111.115 Manifeste OTA (pour APDK-101)](https://download.microsoft.com/download/d/f/0/df0f17dc-d2fb-42ff-aaa5-98edf4d6d1e8/aduimportmanifest_Azure-Percept-DK_2021.106.111.115_v3.json) <br>[Package de mise à jour OTA 2021.106.111.115](https://download.microsoft.com/download/d/f/0/df0f17dc-d2fb-42ff-aaa5-98edf4d6d1e8/Microsoft-Azure-Percept-DK-2021.106.111.115.swu) |Veillez à utiliser le manifeste approprié en fonction du « nom de modèle » (PE-101/APDK-101)|

## <a name="next-steps"></a>Étapes suivantes

[Mettre à jour votre DK Azure Percept en OTA](./how-to-update-over-the-air.md)
