---
title: Surveillance et dépannage à partir de HANA sur SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Apprenez à surveiller et dépanner SAP HANA sur Azure (grandes instances) à l'aide des ressources fournies par SAP HANA.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/18/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017, contperf-fy21q4
ms.openlocfilehash: d42304fbd5a37bdae0d40b44466c29ae1cf5b586
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131060399"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Surveillance et dépannage à partir de HANA

Dans cet article, vous allez apprendre à surveiller et dépanner SAP HANA sur Azure (grandes instances) à l'aide des ressources fournies par SAP HANA. 

Pour analyser les problèmes liés à SAP HANA sur Azure (grandes instances), vous devez identifier la cause racine. SAP a publié de nombreux documents pour vous aider. Des FAQ liées aux performances de SAP HANA sont disponibles dans les Notes SAP suivantes :

- [Note SAP #2222200 – FAQ : réseau SAP HANA](https://launchpad.support.sap.com/#/notes/2222200)
- [Note SAP #2100040 – FAQ : processeur SAP HANA](https://launchpad.support.sap.com/#/notes/0002100040)
- [Note SAP #199997 – FAQ : mémoire SAP HANA](https://launchpad.support.sap.com/#/notes/2177064)
- [Note SAP #200000 – FAQ : optimisation des performances de SAP HANA](https://launchpad.support.sap.com/#/notes/2000000)
- [Note SAP #199930 – FAQ : analyse d’E/S SAP HANA](https://launchpad.support.sap.com/#/notes/1999930)
- [Note SAP #2177064 – FAQ : incidents et redémarrage du service SAP HANA](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>Alertes SAP HANA

Tout d'abord, vérifiez les journaux d'alerte SAP HANA. Dans SAP HANA Studio, accédez à **Console d’administration: Alertes: Afficher: Toutes les alertes**. Cet onglet affiche toutes les alertes SAP HANA relatives aux valeurs (mémoire physique disponible, utilisation du processeur, etc.) qui se situent en dehors des seuils minimum et maximum définis. Par défaut, les vérifications sont automatiquement actualisées toutes les 15 minutes.

![Dans SAP HANA Studio, accédez à Console d’administration: Alertes: Afficher: Toutes les alertes](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>UC

Pour résoudre une alerte déclenchée par un paramètre hors du seuil, rétablissez la valeur par défaut ou définissez une valeur de seuil plus raisonnable.

![Rétablir la valeur par défaut ou définir une valeur de seuil plus raisonnable](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Les alertes suivantes peuvent également indiquer des problèmes de ressources de processeur :

- Utilisation du processeur hôte (alerte 5)
- Dernière opération de point de sauvegarde (alerte 28)
- Durée du point de sauvegarde (alerte 54)

Vous constaterez peut-être une utilisation élevée du processeur sur votre base de données SAP HANA :

- L’alerte 5 (Utilisation du processeur hôte) est déclenchée pour l’utilisation actuelle ou passée du processeur
- L’utilisation du processeur affichée sur l’écran Vue d’ensemble

![Utilisation du processeur affichée sur l’écran Vue d’ensemble](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Le graphique de charge peut indiquer une consommation du processeur élevée, ou une consommation élevée par le passé :

![Le graphique de charge peut indiquer une consommation du processeur élevée, ou une consommation élevée par le passé](./media/troubleshooting-monitoring/image4-load-graph.png)

Une alerte déclenchée par une utilisation élevée du processeur peut avoir plusieurs causes :
- Exécution de certaines transactions
- Chargement de données
- Travaux qui ne répondent pas
- Instructions SQL de longue durée
- Mauvaises performances des requêtes (par exemple, avec les cubes BW sur HANA)

Pour obtenir des instructions détaillées sur la résolution des problèmes d'utilisation du processeur, consultez [Dépannage de SAP HANA  : causes et solutions liées au processeur](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/4fbc915462db406aa2fe92b708b95189.html?q=%20SAP%20HANA%20Troubleshooting:%20CPU%20Related%20Causes%20and%20Solutions).

## <a name="operating-system-os"></a>Système d’exploitation (OS)

Sous SAP HANA sur Linux, il est important de vérifier que les pages volumineuses transparentes sont désactivées. Pour plus d'informations, consultez [Note SAP n°2131662 – Pages volumineuses transparentes sur les serveurs SAP HANA](https://launchpad.support.sap.com/#/notes/2131662).

Vous pouvez vérifier si les pages volumineuses transparentes sont activées via la commande Linux suivante : **cat /sys/kernel/mm/transparent\_hugepage/enabled**
- Si _always_ est placé entre crochets, cela signifie que les pages volumineuses transparentes sont activées : [always] madvise never
- Si _never_ est placé entre crochets, cela signifie que les pages volumineuses transparentes sont désactivées : always madvise [never]

La commande Linux suivante ne doit renvoyer aucun résultat : **rpm -qa | grep ulimit.** Si _ulimit_ est installé, désinstallez-le immédiatement.

## <a name="memory"></a>Mémoire

Vous pouvez voir que la quantité de mémoire allouée à la base de données SAP HANA est plus élevée que prévu. Les alertes suivantes indiquent des problèmes liés à une utilisation élevée de la mémoire :

- Utilisation de la mémoire physique de l’hôte (alerte 1)
- Utilisation de la mémoire du serveur de noms (alerte 12)
- Utilisation totale de la mémoire des tables de la banque des colonnes (alerte 40)
- Utilisation de la mémoire des services (alerte 43)
- Utilisation de la mémoire du stockage principal des tables de la banque des colonnes (alerte 45)
- Fichiers de vidage runtime (alerte 46)

Pour obtenir des instructions détaillées sur la résolution des problèmes de mémoire, consultez [Dépannage de SAP HANA : causes racine des problèmes de mémoire](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/3a2ea5c4593b4b8d823b5b48152bd1d4.html).

## <a name="network"></a>Réseau

Reportez-vous à la [Note SAP n°2081065 – Résolution des problèmes de réseau SAP HANA](https://launchpad.support.sap.com/#/notes/2081065) et suivez les étapes de dépannage réseau qui y sont décrites.

1. Analyse des temps d’aller-retour entre le client et le serveur.
    - Exécutez le script SQL [_HANA\_Réseau\_Clients_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analysez la communication entre les nœuds.
    - Exécutez le script SQL [_HANA\_Réseau\_Services_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Exécutez la commande Linux **ifconfig** (la sortie indique si des pertes de paquets se produisent).
4. Exécutez la commande Linux **tcpdump**.

Utilisez également l'outil open source [IPERF](https://iperf.fr/) (ou tout outil similaire) pour mesurer les véritables performances du réseau d'applications.

Pour obtenir des instructions détaillées sur la résolution des problèmes de réseau, consultez [Dépannage de SAP HANA : problèmes de connectivité et de performances réseau](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/a3ccdff1aedc4720acb24ed8826938b6.html?q=Networking%20Performance%20and%20Connectivity%20Problems).

## <a name="storage"></a>Stockage

Supposons que vous ayez des problèmes d'entrée/sortie. Les utilisateurs finaux peuvent alors constater que les applications, ou le système dans son ensemble, sont lents et ne répondent pas/plus. L'onglet **Volumes** de SAP HANA Studio répertorie les volumes associés et les volumes utilisés par chaque service

![L’onglet Volumes de SAP HANA Studio répertorie les volumes associés et les volumes utilisés par chaque service](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Dans la partie inférieure de l'écran (onglet Volumes), vous pouvez voir les détails des volumes, comme les fichiers et les statistiques d'E/S.

![Dans la partie inférieure de l'écran, vous pouvez voir les détails des volumes, comme les fichiers et les statistiques d'E/S](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Pour obtenir des instructions détaillées sur la résolution des problèmes d'E/S, consultez [Dépannage de SAP HANA  : causes racine et solutions liées aux entrées/sorties](https://help.sap.com/viewer/4e9b18c116aa42fc84c7dbfd02111aba/2.0.05/en-US/dc6ff98fa36541e997e4c719a632cbd8.html?q=I%2FO%20Related%20Root%20Causes%20and%20Solutions). Pour obtenir des instructions détaillées sur la résolution des problèmes de disques, consultez [Dépannage de SAP HANA : causes racine et solutions liées aux disques](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/474cb08a715c42fe9f7cc5efdc599959.html?q=Disk%20Related%20Root%20Causes%20and%20Solutions).

## <a name="diagnostic-tools"></a>Outils de diagnostic

Procédez à une vérification de l'intégrité de SAP HANA via HANA\_Configuration\_Minichecks. Cet outil signale les problèmes techniques critiques potentiels qui devraient déjà avoir déclenché une alerte dans SAP HANA Studio.

1. Reportez-vous à la [Note SAP #1969700 – collection d’instructions SQL pour SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) et téléchargez le fichier SQL Statements.zip associé à cette note. Enregistrez ce fichier .zip sur le disque dur local.

2. Dans SAP HANA Studio, dans l’onglet **System Information** (Informations système), cliquez avec le bouton droit de la souris dans la colonne **Name** (Nom) et sélectionnez **Import SQL Statements** (Importer des instructions SQL).

    ![Dans SAP HANA Studio, dans l’onglet System Information (Informations système), cliquez avec le bouton droit de la souris dans la colonne Name (Nom) et sélectionnez Import SQL Statements (Importer des instructions SQL)](./media/troubleshooting-monitoring/image7-import-statements-a.png)

3. Sélectionnez le fichier SQL Statements.zip enregistré sur votre ordinateur ; un dossier contenant les instructions SQL correspondantes est alors importé. À ce stade, plusieurs vérifications de diagnostics peuvent être effectuées avec ces instructions SQL.

    Par exemple, pour tester les besoins en bande passante pour la réplication du système SAP HANA, cliquez avec le bouton droit de la souris sur l’instruction **Bandwidth** (Bande passante) sous **Replication: Bandwidth** (Réplication : bande passante) et sélectionnez **Open** (Ouvrir) dans la console SQL.

    L’instruction SQL s’ouvre. Vous pouvez alors modifier puis exécuter les paramètres d’entrée (section Modification).

    ![L’instruction SQL s’ouvre. Vous pouvez alors modifier puis exécuter les paramètres d’entrée (section Modification)](./media/troubleshooting-monitoring/image8-import-statements-b.png)

4. Autre exemple : cliquez avec le bouton droit de la souris sur les instructions figurant sous **Replication: Overview** (Réplication : vue d'ensemble). Sélectionnez **Execute** (Exécuter) dans le menu contextuel :

    ![Autre exemple : cliquez avec le bouton droit de la souris sur les instructions figurant sous Replication: Overview (Réplication : vue d'ensemble). Sélectionnez Execute (Exécuter) dans le menu contextuel](./media/troubleshooting-monitoring/image9-import-statements-c.png)

    Vous trouverez des informations utiles pour résoudre les problèmes :

    ![Vous trouverez des informations utiles pour résoudre les problèmes.](./media/troubleshooting-monitoring/image10-import-statements-d.png)

5. Faites de même pour HANA\_Configuration\_Minichecks et vérifiez les marques _X_ dans la colonne _C_ (Critique).

    Exemples de sortie :

    **HANA\_Configuration\_MiniChecks\_Rev102.01+1** pour les vérifications SAP HANA générales.

    ![HANA\_Configuration\_MiniChecks\_Rev102.01+1 pour les vérifications SAP HANA générales](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

    **HANA\_Services\_Overview** pour une vue d'ensemble des exécutions actuelles des services SAP HANA.

    ![HANA\_Services\_Overview pour une vue d'ensemble des exécutions actuelles des services SAP HANA](./media/troubleshooting-monitoring/image12-services-overview.png)

    **HANA\_Services\_Statistics** pour les informations de service SAP HANA (processeur, mémoire, etc.).

    ![HANA\_Services\_Statistics pour les informations de service SAP HANA](./media/troubleshooting-monitoring/image13-services-statistics.png)

    **HANA\_Configuration\_Overview\_Rev110 +** pour des informations générales sur l’instance SAP HANA.

    ![HANA\_Configuration\_Overview\_Rev110 + pour des informations générales sur l’instance SAP HANA](./media/troubleshooting-monitoring/image14-configuration-overview.png)

    **HANA\_Configuration\_Parameters\_Rev70+** pour vérifier les paramètres de SAP HANA.

    ![HANA\_Configuration\_Parameters\_Rev70+ pour vérifier les paramètres de SAP HANA](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

## <a name="next-steps"></a>Étapes suivantes

Apprenez à configurer la haute disponibilité sur le système d'exploitation SUSE à l'aide de l’appareil STONITH.

> [!div class="nextstepaction"]
> [Configuration de la haute disponibilité dans SUSE à l’aide de STONITH](ha-setup-with-stonith.md)
