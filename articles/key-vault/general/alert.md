---
title: Configurer des alertes Azure Key Vault
description: Découvrez comment créer des alertes pour surveiller l’intégrité de votre coffre de clés.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: c7661f91475b85ad8d606770ff74777bb3b8abab
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994268"
---
# <a name="configure-azure-key-vault-alerts"></a>Configurer des alertes Azure Key Vault

Une fois que vous avez commencé à utiliser Azure Key Vault pour stocker vos secrets de production, vous devez impérativement surveiller l’intégrité de votre coffre de clés pour veiller au bon fonctionnement du service. 

Plus votre service se développera, plus le nombre de demandes envoyées à votre coffre de clés augmentera. Cette augmentation est susceptible d’accroître la latence de vos demandes. Dans les cas extrêmes, cela peut entraîner la limitation de vos demandes et avoir un impact sur les performances de votre service. Vous devez également savoir si votre coffre de clés envoie un nombre inhabituel de codes d’erreur, afin de pouvoir traiter rapidement tout problème à l’aide d’une stratégie d’accès ou d’une configuration de pare-feu. 

Cet article vous explique comment configurer des alertes à des seuils spécifiques afin de pouvoir alerter votre équipe de la nécessité de prendre des mesures immédiates si l’état de votre coffre de clés n’est pas sain. Vous pouvez configurer des alertes qui envoient un e-mail (de préférence à une liste de distribution de l’équipe), déclenchent une notification Azure Event Grid, appellent un numéro de téléphone ou lui envoient un SMS. 

Vous pouvez choisir entre les types d’alerte suivants :

- Une alerte statique basée sur une valeur fixe
- Une alerte dynamique qui vous avertit si une métrique surveillée dépasse la limite moyenne de votre coffre de clés un certain nombre de fois dans un intervalle de temps défini

> [!IMPORTANT]
> Il peut s’écouler jusqu’à dix minutes avant que les alertes nouvellement configurées commencent à envoyer des notifications. 

Cet article porte sur les alertes pour Key Vault. Pour plus d’informations sur Key Vault Insights, qui combine les journaux et les métriques pour fournir une solution de surveillance globale, consultez [Superviser votre coffre de clés avec Key Vault Insights](../../azure-monitor/insights/key-vault-insights-overview.md#introduction-to-key-vault-insights).

## <a name="configure-an-action-group"></a>Configurer un groupe d'actions 

Un groupe d'actions est une liste configurable de notifications et de propriétés. La première étape de la configuration des alertes consiste à créer un groupe d’actions et à choisir un type d’alerte :

1. Connectez-vous au portail Azure.
2. Recherchez **Alertes** dans la zone de recherche.
3. Sélectionnez **Gérer les actions**.

   > [!div class="mx-imgBorder"]
   > ![Capture d'écran mettant en évidence le bouton Gérer les actions.](../media/alert-6.png)

4. Sélectionnez **+ Ajouter un groupe d’actions**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran mettant en évidence le bouton permettant d’ajouter un groupe d’actions.](../media/alert-7.png)

5. Choisissez la valeur **Type d’action** à associer à votre groupe d’actions. Dans cet exemple, nous allons créer une alerte par e-mail et par SMS. Sélectionnez **E-mail/SMS/Push/Voix**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran mettant en évidence les sélections pour ajouter un groupe d’actions.](../media/alert-8.png)

6. Dans la boîte de dialogue, entrez les informations relatives à l’e-mail et au SMS, puis sélectionnez **OK**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran mettant en évidence les sélections pour l’ajout d’une alerte par e-mail et par SMS.](../media/alert-9.png)

## <a name="configure-alert-thresholds"></a>Configurer les seuils d'alerte 

Ensuite, créez une règle et configurez les seuils qui déclencheront une alerte :

1. Sélectionnez votre coffre de clés sur le portail Azure, puis choisissez **Alertes** sous **Surveillance**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran illustrant l’option de menu Alertes dans la section Surveillance.](../media/alert-10.png)

2. Sélectionnez **Nouvelle règle d’alerte**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran montrant le bouton permettant d’ajouter une nouvelle règle d’alerte.](../media/alert-11.png)

3. Sélectionnez l'étendue de votre règle d'alerte. Vous pouvez sélectionner un ou plusieurs coffres. 

   > [!IMPORTANT]
   > Lorsque vous sélectionnez plusieurs coffres pour l’étendue de vos alertes, tous les coffres sélectionnés doivent se trouver dans la même région. Vous devez configurer des règles d’alerte distinctes pour les coffres situés dans des régions différentes. 

   > [!div class="mx-imgBorder"]
   > ![Capture d'écran montrant comment sélectionner un coffre.](../media/alert-12.png)

4. Sélectionnez les seuils qui définissent la logique de vos alertes, puis sélectionnez **Ajouter**. L’équipe de Key Vault recommande de configurer les seuils suivants : 

    + La disponibilité de Key Vault passe en dessous de 100 % (seuil statique)
    + La latence de Key Vault est supérieure à 1000 ms (seuil statique) 
    + La saturation globale du coffre est supérieure à 75 % (seuil statique) 
    + La saturation globale du coffre est supérieure à la moyenne (seuil dynamique)
    + Le nombre total de codes d’erreur est supérieur à la moyenne (seuil dynamique) 

   > [!div class="mx-imgBorder"]
   > ![Capture d'écran montrant où sélectionner les conditions des alertes.](../media/alert-13.png)

### <a name="example-configure-a-static-alert-threshold-for-latency"></a>Exemple : Configurer un seuil d’alerte statique pour la latence

1. Sélectionnez **Latence globale de l’API de service** comme nom de signal.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran montrant la sélection d’un nom de signal.](../media/alert-14.png)

1. Utilisez les paramètres de configuration suivants :

   + Définissez **Seuil** sur **Statique**. 
   + Définissez **Opérateur** sur **Supérieur à**.
   + Définissez **Type d’agrégation** sur **Moyenne**.
   + Définissez **Valeur de seuil** sur **1000**.
   + Définissez **Précision d’agrégation (période)** sur **5 minutes**.
   + Définissez **Fréquence d’évaluation** sur **Toutes les minutes**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran montrant la logique configurée pour un seuil d’alerte statique.](../media/alert-15.png)

1. Sélectionnez **Terminé**.  

### <a name="example-configure-a-dynamic-alert-threshold-for-vault-saturation"></a>Exemple : Configurer un seuil d’alerte dynamique pour la saturation du coffre 

Lorsque vous utilisez une alerte dynamique, vous pouvez consulter les données historiques du coffre de clés que vous avez sélectionné. La zone bleue représente l'utilisation moyenne de votre coffre de clés. La zone rouge indique les pics qui auraient déclenché une alerte si d’autres critères de la configuration de l’alerte avaient été respectés. Les points rouges indiquent les cas de violations dans lesquels les critères d’alerte ont été remplis pendant la fenêtre temporelle agrégée. 

> [!div class="mx-imgBorder"]
> ![Capture d'écran d'un graphique représentant la saturation globale du coffre.](../media/alert-16.png)

Vous pouvez définir une alerte pour qu'elle se déclenche après un certain nombre de violations sur une période déterminée. Si vous ne souhaitez pas inclure les données antérieures, une option vous permet de les exclure dans les paramètres avancés. 

1. Utilisez les paramètres de configuration suivants :

   + Définissez **Seuil** sur **Dynamique**. 
   + Définissez **Opérateur** sur **Supérieur à**.
   + Définissez **Type d’agrégation** sur **Moyenne**.
   + Définissez **Sensibilité du seuil** sur **Moyenne**.
   + Définissez **Précision d’agrégation (période)** sur **5 minutes**.
   + Définissez **Fréquence d’évaluation** sur **Toutes les 5 minutes**.
   + Configurez les **paramètres avancés** (facultatif). 

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran montrant la logique configurée pour un seuil d’alerte dynamique.](../media/alert-17.png)

1. Sélectionnez **Terminé**.

1. Sélectionnez **Ajouter** pour ajouter le groupe d’actions que vous avez configuré.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran montrant le bouton permettant d’ajouter un groupe d’actions.](../media/alert-18.png)

1. Dans les détails de l’alerte, activez l’alerte et attribuez une gravité.

   > [!div class="mx-imgBorder"]
   > ![Capture d'écran montrant où activer l'alerte et attribuer une gravité.](../media/alert-19.png)

1. Créez l’alerte. 

### <a name="example-email-alert"></a>Exemple d'alerte par e-mail 

Si vous avez suivi toutes les étapes précédentes, vous recevrez des alertes par e-mail chaque fois que votre coffre de clés répondra aux critères d’alerte que vous avez configurés. L’alerte par e-mail suivante est un exemple. 

> [!div class="mx-imgBorder"]
> ![Capture d'écran mettant en évidence les informations nécessaires à la configuration d'une alerte par e-mail.](../media/alert-20.png)

## <a name="next-steps"></a>Étapes suivantes

Utilisez les outils que vous avez configurés dans cet article pour surveiller activement l’intégrité de votre coffre de clés :

- [Superviser Key Vault](monitor-key-vault.md)
- [Informations de référence sur la supervision des données Key Vault](monitor-key-vault-reference.md)
