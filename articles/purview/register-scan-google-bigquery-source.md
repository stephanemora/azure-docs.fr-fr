---
title: Inscrire le projet Google BigQuery et configurer des analyses dans Azure Purview
description: Cet article explique comment inscrire un projet Google BigQuery dans Azure Purview et configurer une analyse.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 7/15/2021
ms.openlocfilehash: f7b134f14d190e7ef65eb2da897f9c106583d497
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393517"
---
# <a name="register-and-scan-google-bigquery-source-preview"></a>Inscrire et analyser la source Google BigQuery (préversion)

Cet article explique comment inscrire un projet Google BigQuery dans Purview et configurer une analyse.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

La source BigQuery prend en charge l’Analyse complète pour extraire les métadonnées d’un projet BigQuery et récupère la Traçabilité entre les ressources de données.

## <a name="prerequisites"></a>Prérequis

1.  Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717).
    Pour plus d’informations, consultez [Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

2.  Assurez-vous que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

3.  Vérifiez que le package \"Visual C++ Redistributable 2012 Update 4\" est installé sur la machine dotée du runtime d’intégration auto-hébergé. S\'il n’est pas déjà installé, vous pouvez le télécharger [ici](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Vous devez télécharger manuellement le pilote JDBC de BigQuery sur la machine virtuelle où s’exécute le runtime d’intégration auto-hébergé à partir d’[ici](https://cloud.google.com/bigquery/providers/simba-drivers)

    > [!Note]
    > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne l’installez pas dans un compte d’utilisateur.

5.  La version de Google BigQuery prise en charge est 11.0.0

## <a name="register-a-google-bigquery-project"></a>Inscrire un projet Google BigQuery

Pour inscrire un nouveau projet Google BigQuery dans votre catalogue de données, procédez comme suit :

1.  Accédez à votre compte Purview.
2.  Sélectionnez **Sources** dans la barre de navigation à gauche.
3.  Sélectionnez **Inscrire**.
4.  Dans Inscrire des sources, sélectionnez **Google BigQuery**. Sélectionnez **Continuer.** 
    :::image type="content" source="media/register-scan-google-bigquery-source/register-sources.png" alt-text="Inscrire la source BigQuery" border="true":::
   
Sur l’écran Inscrire des sources (Google BigQuery), procédez comme suit :

1.  Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.

2.  Entrez le **ProjectID.** Il doit s’agir d’un ID de projet complet. Par exemple, mydomain.com:myProject

3.  Sélectionnez une collection ou créez-en une (facultatif).

4.  Cliquez sur **Inscrire**.
    :::image type="content" source="media/register-scan-google-bigquery-source/configure-sources.png" alt-text="Configurer la source BigQuery" border="true":::

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1.  Dans le centre d’administration, cliquez sur Runtimes d’intégration. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](./manage-integration-runtimes.md) pour configurer un runtime d’intégration auto-hébergé

2.  Accédez aux **Sources**.

3.  Sélectionnez le projet **BigQuery** inscrit.

4.  Sélectionnez **+ Nouvelle analyse**.

5.  Fournissez les renseignements ci-dessous :

    a.  **Nom** : nom de l’analyse

    b.  **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré

    c.  **Informations d’identification** : lors de la configuration des informations d’identification BigQuery, veillez à :

    - Sélectionner **Authentification de base** comme méthode d’authentification
    - Indiquer l’ID de messagerie du compte de service dans le champ nom d’utilisateur. Par exemple,\xyz\@developer.gserviceaccount.com
    - Enregistrer un fichier de clé privée du compte de service au format JSON dans le coffre de clés

    Pour créer une nouvelle clé privée à partir de la plateforme Cloud de Google, dans le menu de navigation, cliquez sur IAM et Admin- \> Comptes de service- \> Sélectionner un projet- \> cliquez sur l’adresse de messagerie du compte de service pour lequel vous souhaitez créer une clé. Pour cela, \> cliquez sur l’onglet **Clés**\>, cliquez sur le menu déroulant **Ajouter une clé**, puis sélectionnez Créer une clé. Choisissez maintenant le format JSON.

      > [!Note]
      > Le contenu de la clé privée est enregistré dans un fichier temporaire sur la machine virtuelle lorsque les processus d’analyse sont en cours d’exécution. Ce fichier temporaire est supprimé une fois que les analyses sont terminées avec succès. En cas d’échec de l’analyse, le système continue à réessayer jusqu’à ce qu’il réussisse. Assurez-vous que l’accès est correctement limité sur la machine virtuelle où SHIR est en cours d’exécution.**

    Pour plus d’informations sur les informations d’identification, reportez-vous à [ce lien](manage-credentials.md).

    d.  **Emplacement du pilote** : spécifiez le chemin d’accès à l’emplacement du pilote JDBC dans votre machine virtuelle où s’exécute le runtime d’intégration auto-hébergé. Il doit s’agir du chemin vers l’emplacement du dossier JAR valide    
    > [!Note]
    > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne l’installez pas dans un compte d’utilisateur.

    e.  **Jeu de données** : spécifiez une liste de jeux de données BigQuery à importer. Par exemple, dataset1 ; dataset2. Si la liste est vide, tous les jeux de données disponibles sont importés.
        Les modèles de nom de jeu de données acceptables utilisant la syntaxe d’expressions de type SQL LIKE incluent l’utilisation de %, 

    par exemple, A%; %B; %C%; D
    - commençant par A ou
    - se terminant par B ou
    - contenant C ou
    - égalant D    
L’utilisation de NOT et des caractères spéciaux n’est pas autorisée.
    
    f.  **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur la machine virtuelle du client pouvant être utilisée par les processus d’analyse. Cela dépend de la taille du serveur Google BigQuery à analyser.
        :::image type="content" source="media/register-scan-google-bigquery-source/scan.png" alt-text="Analyser la source BigQuery" border="true":::

6.  Cliquez sur **Tester la connexion**.

7.  Cliquez sur **Continuer**.

8.  Choisissez votre **déclencheur d’analyse**. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

9.  Vérifiez votre analyse et cliquez sur **Enregistrer et exécuter**.

## <a name="viewing-your-scans-and-scan-runs"></a>Affichage des analyses et des exécutions d’analyse

1. Accédez au centre d’administration. Sélectionnez **Sources de données** sous la section **Sources et analyse**.

2. Sélectionnez la source de données souhaitée. La liste des analyses existantes sur cette source de données apparaît.

3. Sélectionnez l’analyse dont vous souhaitez afficher les résultats.

4. Cette page affiche toutes les exécutions précédentes de l’analyse ainsi que les métriques et l’état de chaque exécution de l’analyse. Elle indique également si votre analyse a été planifiée ou était manuelle, le nombre de ressources pour lesquelles ont été appliquées des classifications, le nombre total de ressources découvertes, l’heure de début et de fin de l’analyse et la durée totale de l’analyse.

## <a name="manage-your-scans"></a>Gestion de vos analyses

Pour gérer ou supprimer une analyse, effectuez les opérations suivantes :

1. Accédez au centre d’administration. Sélectionnez **Sources de données** sous la section **Sources et analyse**, puis sélectionnez la source de données souhaitée.

2. Sélectionnez l’analyse que vous souhaitez gérer. Vous pouvez modifier l’analyse en sélectionnant **Modifier**.

3. Vous pouvez supprimer votre analyse en sélectionnant **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- [Navigation dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)