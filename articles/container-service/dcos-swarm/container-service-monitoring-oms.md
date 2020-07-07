---
title: (DÉCONSEILLÉ) Analyser un cluster DC/OS Azure - Gestion des opérations
description: Analysez un cluster DC/OS Azure Container Service avec Log Analytics.
author: keikhara
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 1ab8d1cf3eb38a17f0b3d6c8137e37237498a527
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277321"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(DÉCONSEILLÉ) Analyser un cluster DC/OS Azure Container Service avec Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics est une solution de gestion informatique basée sur le cloud de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et dans le cloud. La solution de conteneurs fait partie intégrante de Log Analytics, qui vous octroie une visibilité sur le stock, les performances et les fichiers journaux d’activité des conteneurs à un emplacement unique. Vous pouvez auditer les conteneurs, résoudre les problèmes en consultant les fichiers journaux d’activité de manière centralisée, et identifier les conteneurs bruyants à consommation supérieure sur un hôte.

![](media/container-service-monitoring-oms/image1.png)

Pour plus d’informations sur la solution Conteneurs, reportez-vous à [Solution Conteneurs (version préliminaire) Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Configuration de Log Analytics à partir de l’univers DC/OS


Cet article suppose que vous avez configuré un univers DC/OS et avez déployé des applications web simples de conteneurs sur le cluster.

### <a name="pre-requisite"></a>Conditions préalables
- [Abonnement Microsoft Azure](https://azure.microsoft.com/free/) : vous pouvez l’obtenir un abonnement gratuitement.  
- Configuration de l’espace de travail Log Analytics : consultez « l’étape 3 » ci-dessous
- [Interface CLI DC/OS](https://docs.mesosphere.com/1.12/cli) installées.

1. Dans le tableau de bord DC/OS, cliquez sur l’univers, puis recherchez « OMS », tel que représenté ci-dessous.

   >[!NOTE]
   >OMS est désormais appelé Log Analytics.

   ![](media/container-service-monitoring-oms/image2.png)

2. Cliquez sur **Installer**. Vous découvrez une fenêtre contextuelle avec les informations de version et un bouton **Installer le package** ou **Installation avancée**. Lorsque vous cliquez sur **Installation avancée**, vous accédez à la page des **propriétés de configuration spécifique OMS**.

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Ici, vous êtes invité à entrer `wsid` (l’ID d’espace de travail Log Analytics) et `wskey` (la clé primaire pour l’ID de l’espace de travail). Pour obtenir les deux `wsid` et `wskey` vous devez créer un compte sur <https://mms.microsoft.com>.
   Suivez la procédure de création de compte. Une fois que vous avez créé le compte, pour obtenir votre `wsid` et votre `wskey`, cliquez sur **Paramètres**, puis sur **Sources connectés**, puis sur **Serveurs Linux**, tel que représenté ci-dessous.

   ![](media/container-service-monitoring-oms/image5.png)

4. Sélectionnez le nombre d’instances souhaité, puis cliquez sur le bouton « Réviser et installer ». Généralement, vous avez intérêt à ce que le nombre d’instances soit équivalent au nombre de machines virtuelles dans votre cluster d’agent. L’Agent Log Analytics pour Linux s’installe en tant que conteneurs individuels sur chaque machine virtuelle qu’il souhaite affecter à la collecte d’informations pour l’analyse et l’enregistrement des informations.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Configuration d’un simple tableau de bord Log Analytics

Une fois que vous avez installé l’Agent Log Analytics pour Linux sur les machines virtuelles, il vous faut configurer le tableau de bord Log Analytics. Vous pouvez configurer le tableau de bord via le portail Azure.

### <a name="azure-portal"></a>Portail Azure 

Connectez-vous au portail Azure à l’adresse <https://portal.microsoft.com/>. Go to **Marketplace**, sélectionnez **Surveillance + gestion**, puis cliquez sur **Afficher tout**. Ensuite, saisissez `containers` dans la recherche. L’indication « conteneurs » apparaît dans les résultats de la recherche. Sélectionnez **Conteneurs**, puis cliquez sur **Créer**.

![](media/container-service-monitoring-oms/image9.png)

Une fois que vous avez cliqué sur **Créer**, vous êtes invité à saisir votre espace de travail. Sélectionnez votre espace de travail. Si vous n’en avez pas, créez-en un.

![](media/container-service-monitoring-oms/image10.PNG)

Une fois la sélection effectuée, cliquez sur **Créer**.

![](media/container-service-monitoring-oms/image11.png)

Pour plus d’informations sur la solution Conteneurs Log Analytics, reportez-vous à la rubrique [Solution Conteneurs Log Analytics](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Mise à l’échelle de l’Agent Log Analytics avec ACS DC/OS 

Si vous devez avoir installé l’Agent Log Analytics en deçà du nombre réel de nœuds ou si vous mettez à l’échelle le groupe de machines virtuelles identiques en ajoutant davantage de machines virtuelles, vous pouvez mettre à l’échelle le service `msoms`.

Pour ce faire, accédez à Marathon ou à l’onglet des services d’interface utilisateur DC/OS, puis augmentez le nombre de nœuds.

![](media/container-service-monitoring-oms/image12.PNG)

Cette action se déploie sur d’autres nœuds qui n’ont pas encore déployé l’Agent Log Analytics.

## <a name="uninstall-ms-oms"></a>Désinstaller MS OMS

Pour désinstaller MS OMS, saisissez la commande suivante :

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Dites-le-nous !
Qu’est-ce qui fonctionne ? Quels sont les manquements ? Quels éléments pourraient vous être utiles ? Faites-le nous savoir sur <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Étapes suivantes

 Maintenant que vous avez configuré Log Analytics pour analyser vos conteneurs, [consultez votre tableau de bord des conteneurs](../../azure-monitor/insights/containers.md).
