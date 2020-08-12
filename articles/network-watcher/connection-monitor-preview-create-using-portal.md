---
title: Créer une instance Moniteur de connexion (préversion) – Portail Azure
titleSuffix: Azure Network Watcher
description: Découvrez comment créer une instance Moniteur de connexion (préversion) à l’aide du portail Azure.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: 4e7067e537ce8fb6faf82198f7863957f79ffb22
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567800"
---
# <a name="create-a-connection-monitor-preview-using-the-azure-portal"></a>Créer une instance Moniteur de connexion (préversion) à l’aide du portail Azure

Découvrez comment créer une instance Moniteur de connexion (préversion) pour surveiller la communication entre vos ressources à l’aide du portail Azure. Elle prend en charge les déploiements cloud hybrides et Azure.

## <a name="before-you-begin"></a>Avant de commencer 

Dans les moniteurs de connexion que vous créez à partir de la fonctionnalité Moniteur de connexion (préversion), vous pouvez aussi bien ajouter des machines locales et des machines virtuelles Azure en tant que sources. Ces moniteurs de connexion peuvent également surveiller la connectivité aux points de terminaison. Les points de terminaison peuvent se trouver sur Azure ou sur toute autre URL ou adresse IP.

Le Moniteur de connexion (préversion) inclut les entités suivantes :


* **Ressource de moniteur de connexion** : ressource Azure spécifique à la région. Toutes les entités ci-dessous sont des propriétés d'une ressource de moniteur de connexion.
* **Point de terminaison** : source ou destination qui participe aux vérifications de la connectivité. Les machines virtuelles Azure, les agents locaux, les URL et les adresses IP sont des exemples de points de terminaison.
* **Configuration de test** : configuration spécifique à un protocole dans le cadre d'un test. En fonction du protocole que vous avez choisi, vous pouvez définir le port, les seuils, la fréquence de test et d'autres paramètres.
* **Groupe de tests** : groupe contenant les points de terminaison sources, les points de terminaison de destination et les configurations de test. Un moniteur de connexion peut contenir plusieurs groupes de tests.
* **Test** : combinaison d'un point de terminaison source, d'un point de terminaison de destination et d'une configuration de test. Un test correspond au niveau le plus granulaire auquel les données de surveillance sont disponibles. Les données de surveillance incluent le pourcentage de vérifications qui ont échoué et la durée des boucles.

    ![Diagramme illustrant un moniteur de connexion, avec définition de la relation entre les groupes de tests et les tests](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create"></a>Étapes de création

Pour créer une instance Moniteur de connexion (préversion) à l’aide du portail Azure, procédez comme suit :

1. Sur la page d'accueil du portail Azure, accédez à **Network Watcher**.
1. Sur la gauche, dans la section **Surveillance**, sélectionnez **Moniteur de connexion (préversion)** .
1. Tous les moniteurs de connexion créés dans la fonctionnalité Moniteur de connexion (préversion) sont répertoriés. Pour afficher les moniteurs de connexion créés dans l'expérience utilisateur classique du Moniteur de connexion, accédez à l'onglet **Moniteur de connexion**.

    ![Capture d'écran illustrant des moniteurs de connexion créés dans le Moniteur de connexion (préversion)](./media/connection-monitor-2-preview/cm-resource-view.png)   
    
1. En haut à gauche du tableau de bord **Moniteur de connexion (préversion)** , sélectionnez **Créer**.
1. Sous l'onglet **Informations de base**, entrez les informations relatives à votre moniteur de connexion :
   * **Nom du moniteur de connexion** : ajoutez le nom de votre moniteur de connexion. Appliquez les règles de nommage standard des ressources Azure.
   * **Abonnement** : choisissez un abonnement pour votre moniteur de connexion.
   * **Région** : choisissez une région pour votre moniteur de connexion. Vous ne pouvez sélectionner que les machines virtuelles sources créées dans cette région.
   * **Configuration de l'espace de travail** : votre espace de travail contient vos données de surveillance. Vous pouvez utiliser un espace de travail personnalisé ou l'espace de travail par défaut. 
       * Pour utiliser l'espace de travail par défaut, cochez la case. 
       * Pour choisir un espace de travail personnalisé, décochez la case. Choisissez ensuite l'abonnement et la région de votre espace de travail personnalisé. 
1. En bas de l'onglet, sélectionnez **Suivant : Groupes de tests**.

   ![Capture d'écran illustrant l'onglet Informations de base du Moniteur de connexion](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Sous l'onglet **Groupes de tests**, sélectionnez **+ Groupe de tests**. Pour configurer vos groupes de tests, consultez [Créer des groupes de tests dans le Moniteur de connexion](#create-test-groups-in-a-connection-monitor). 
1. En bas de l'onglet, sélectionnez **Suivant : Examiner + créer** pour examiner votre moniteur de connexion.

   ![Capture d'écran illustrant l'onglet Groupes de tests et le volet dans lequel vous ajoutez les informations relatives au groupe de tests](./media/connection-monitor-2-preview/create-tg.png)

1. Sous l'onglet **Examiner + créer**, passez en revue les informations de base et les groupes de tests avant de créer le moniteur de connexion. Si vous avez besoin d'apporter des modifications au moniteur de connexion :
   * Pour modifier les informations de base, sélectionnez l'icône Crayon.
   * Pour modifier un groupe de tests, sélectionnez-le.

   > [!NOTE] 
   > L'onglet **Examiner + créer** indique le coût mensuel pendant la phase de préversion du Moniteur de connexion. Actuellement, rien n'apparaît dans la colonne **COÛT ACTUEL**. Lorsque le Moniteur de connexion sera mis à la disposition générale, cette colonne affichera les frais mensuels. 
   > 
   > En revanche, des frais d'ingestion de Log Analytics s'appliquent pendant la phase de préversion du Moniteur de connexion.

1. Lorsque vous êtes prêt à créer le moniteur de connexion, sélectionnez **Créer** en bas de l'onglet **Examiner + créer**.

   ![Capture d'écran du Moniteur de connexion illustrant l'onglet Examiner + créer](./media/connection-monitor-2-preview/review-create-cm.png)

Le Moniteur de connexion (préversion) crée le moniteur de connexion en arrière-plan.

## <a name="create-test-groups-in-a-connection-monitor"></a>Créer des groupes de tests dans un moniteur de connexion

Chaque groupe de tests d'un moniteur de connexion comprend des sources et des destinations dont les paramètres réseau sont testés. Les tests concernent le pourcentage de vérifications qui échouent et la durée des boucles sur les configurations de test.

Sur le portail Azure, pour créer un groupe de tests relatif à un moniteur de connexion, vous devez spécifier des valeurs dans les champs suivants :

* **Désactiver le groupe de tests** : vous pouvez sélectionner ce champ pour désactiver la surveillance de toutes les sources et destinations spécifiées par le groupe de tests. Cette sélection est désactivée par défaut.
* **Nom** : donnez un nom à votre groupe de tests.
* **Sources** : vous pouvez spécifier des machines virtuelles Azure et des machines locales en tant que sources si des agents y sont installés. Pour installer un agent pour votre source, consultez [Installer des agents de surveillance](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents).
   * Pour choisir des agents Azure, sélectionnez l'onglet **Agents Azure**. Vous ne voyez ici que les machines virtuelles liées à la région que vous avez spécifiée lors de la création du moniteur de connexion. Par défaut, les machines virtuelles sont regroupées au sein de l'abonnement auquel elles appartiennent. Ces groupes sont réduits. 
   
       Vous pouvez passer du niveau Abonnement à d'autres niveaux de la hiérarchie :

      **Abonnement** > **Groupes de ressources** > **Réseaux virtuels** > **Sous-réseaux** > **Machines virtuelles avec agents**

      Vous pouvez également changer la valeur du champ **Grouper par** pour démarrer l'arborescence à partir d'un autre niveau. Par exemple, si vous effectuez un regroupement par réseau virtuel, vous obtenez la liste des machines virtuelles disposant d'agents dans la hiérarchie **Réseaux virtuels** > **Sous-réseaux** > **Machines virtuelles avec agents**.

      ![Capture d'écran du Moniteur de connexion illustrant le panneau Ajouter des sources et l'onglet Agents Azure](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Pour choisir des agents locaux, sélectionnez l'onglet **Agents non Azure**. Par défaut, les agents sont regroupés par région dans les espaces de travail. La solution Network Performance Monitor est configurée pour tous ces espaces de travail. 
   
       Si nécessaire, procurez-vous Network Performance Monitor à partir de la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) pour l'ajouter à votre espace de travail. Pour plus d'informations sur l'ajout de Network Performance Monitor, consultez [Solutions de supervision dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       Sur la vue **Créer un moniteur de connexion**, accédez à l'onglet **Informations de base**. La région par défaut est sélectionnée. Si vous changez la région, vous pouvez choisir des agents des espaces de travail de la nouvelle région. Vous pouvez également changer la valeur du champ **Grouper par** pour effectuer un regroupement par sous-réseaux.

      ![Capture d'écran du moniteur de connexion illustrant le panneau Ajouter des sources et l'onglet Agents non Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * Pour passer en revue les agents Azure et non Azure que vous avez sélectionnés, accédez à l'onglet **Examiner**.

      ![Capture d'écran du moniteur de connexion illustrant le panneau Ajouter des sources et l'onglet Examiner](./media/connection-monitor-2-preview/review-sources.png)

   * Une fois la configuration des sources terminée, sélectionnez **Terminé** en bas du panneau **Ajouter des sources**.

* **Destinations** : pour surveiller la connectivité aux machines virtuelles Azure ou aux points de terminaison (adresses IP publiques, URL ou FQDN), spécifiez-les en tant que destinations. Au sein d'un même groupe de tests, vous pouvez ajouter des machines virtuelles Azure, des URL Office 365, des URL Dynamics 365 et des points de terminaison personnalisés.

    * Pour choisir des machines virtuelles Azure comme destinations, sélectionnez l'onglet **Machines virtuelles Azure**. Par défaut, les machines virtuelles Azure sont regroupées dans une hiérarchie d'abonnement qui se trouve dans la même région que celle que vous avez sélectionnée dans la vue **Créer un moniteur de connexion**, sous l'onglet **Informations de base**. Vous pouvez changer la région, et choisir des machines virtuelles Azure dans la région que vous venez de sélectionner. Vous pouvez ensuite descendre dans la hiérarchie, du niveau Abonnement vers d'autres niveaux, par exemple le niveau Agents Azure.

       ![Capture d'écran du volet Ajouter des destinations illustrant l'onglet Machines virtuelles Azure](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Capture d'écran du volet Ajouter des destinations illustrant le niveau Abonnement](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * Pour choisir des points de terminaison en tant que destinations, sélectionnez l'onglet **Points de terminaison**. La liste des points de terminaison comprend les URL de test Office 365 et les URL de test Dynamics 365, regroupées par nom. En plus de ces points de terminaison, vous pouvez choisir un point de terminaison qui a été créé dans un autre groupe de tests du même moniteur de connexion. 
    
        Pour ajouter un nouveau point de terminaison, sélectionnez **+ Points de terminaison** en haut à droite. Fournissez ensuite un nom de point de terminaison ainsi qu'une URL, une adresse IP ou un FQDN.

       ![Capture d'écran montrant où ajouter des points de terminaison en tant que destinations dans le Moniteur de connexion](./media/connection-monitor-2-preview/add-endpoints.png)

    * Pour passer en revue les points de terminaison et les machines virtuelles Azure que vous avez choisis, sélectionnez l'onglet **Examiner**.
    * Une fois les destinations choisies, sélectionnez **Terminé**.

* **Configurations de test** : vous pouvez associer des configurations de test au sein d'un groupe de tests. Le portail Azure n'autorise qu'une seule configuration de test par groupe de tests, mais vous pouvez utiliser ARMClient pour en ajouter d'autres.

    * **Nom** : donnez un nom à la configuration de test.
    * **Protocole** : choisissez TCP, ICMP ou HTTP. Pour remplacer HTTP par HTTPS, sélectionnez le protocole **HTTP** et le port **443**.
        * **Créer une configuration de test de réseau** : cette case à cocher n'apparaît que si vous sélectionnez **HTTP** dans le champ **Protocole**. Cochez cette case pour créer une autre configuration de test utilisant les mêmes sources et destinations que celles que vous avez spécifiées ailleurs dans votre configuration. La configuration de test nouvellement créée est nommée `<the name of your test configuration>_networkTestConfig`.
        * **Désactiver traceroute** : ce champ s'applique aux groupes de tests utilisant le protocole TCP ou ICMP. Cochez cette case pour empêcher les sources de découvrir la topologie et la durée des boucles tronçon par tronçon.
    * **Port de destination** : vous pouvez personnaliser ce champ avec le port de destination de votre choix.
    * **Fréquence de test** : utilisez ce champ pour choisir la fréquence à laquelle les sources effectueront un test Ping des destinations à l'aide du protocole et du port que vous avez spécifiés. Vous pouvez choisir 30 secondes, 1 minute, 5 minutes, 15 minutes ou 30 minutes. Les sources testeront la connectivité aux destinations en fonction de la valeur que vous avez choisie.  Par exemple, si vous sélectionnez 30 secondes, les sources vérifieront la connectivité à la destination au moins une fois toutes les 30 secondes.
    * **Seuil de succès** - Vous pouvez définir des seuils sur les paramètres réseau suivants :
       * **Vérifications ayant échoué** : définissez le pourcentage de vérifications qui peuvent échouer lorsque les sources vérifient la connectivité aux destinations à l'aide des critères que vous avez spécifiés. Pour le protocole TCP ou ICMP, le pourcentage de vérifications qui ont échoué peut être égal au pourcentage de perte de paquets. Pour le protocole HTTP, ce champ représente le pourcentage de requêtes HTTP qui n'ont reçu aucune réponse.
       * **Durée des boucles** : définissez la durée des boucles en millisecondes pour déterminer combien de temps les sources peuvent prendre pour se connecter à la destination sur la configuration de test.
    
       ![Capture d'écran montrant où définir une configuration de test dans le Moniteur de connexion](./media/connection-monitor-2-preview/add-test-config.png)


## <a name="scale-limits"></a>Limites de mise à l’échelle

Les moniteurs de connexion présentent les limites suivantes en termes de mise à l'échelle :

* Nombre maximum de moniteurs de connexion par abonnement et par région : 100
* Nombre maximum de groupes de tests par moniteur de connexion : 20
* Nombre maximum de sources et de destinations par moniteur de connexion : 100
* Nombre maximum de configurations de test par moniteur de connexion : 2 via le portail Azure

## <a name="next-steps"></a>Étapes suivantes

* Découvrez [comment analyser les données de surveillance et définir des alertes](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts).
* Découvrez [comment diagnostiquer des problèmes dans votre réseau](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network).
