---
title: Inscrire une source Teradata et configurer des analyses (préversion) dans Azure Purview
description: Cet article explique comment inscrire une source Teradata dans Azure Purview et configurer une analyse.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 2008e014e9f160b643ed5f591fff81c0b215e24a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175038"
---
# <a name="register-and-scan-teradata-source-preview"></a>Inscrire et analyser une source Teradata (préversion)

Cet article explique comment inscrire une source Teradata dans Purview et configurer une analyse.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

La source Teradata gère l’**Analyse complète** pour extraire les métadonnées d’une base de données Teradata, et récupère la **Traçabilité** entre les ressources de données.

## <a name="prerequisites"></a>Prérequis

1.  Configurez le dernier [runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717).
    Pour plus d’informations, consultez [Créer et configurer un runtime d’intégration auto-hébergé](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

2.  Assurez-vous que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

3.  Vérifiez que le package \"Visual C++ Redistributable 2012 Update 4\" est installé sur la machine dotée du runtime d’intégration auto-hébergé. S\'il n’est pas déjà installé, vous pouvez le télécharger [ici](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Vous devez télécharger manuellement le pilote JDBC de Teradata sur la machine virtuelle où s’exécute le runtime d’intégration auto-hébergé.
    Le fichier JAR exécutable peut être téléchargé à partir du [site web de Teradata](https://downloads.teradata.com/).

    > [!Note]
    > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne procédez pas à l’installation dans un compte d’utilisateur.

5.  Les versions de base de données Teradata reconnues sont 12.x à 16.x. Assurez-vous d’avoir un accès en lecture à la source Teradata qui est analysée.

## <a name="setting-up-authentication-for-a-scan"></a>Configuration de l’authentification pour une analyse

La seule authentification prise en charge pour une source Teradata est l’**Authentification de base**.

## <a name="register-a-teradata-source"></a>Inscription d’une source Teradata

Pour inscrire une nouvelle source Teradata dans votre catalogue de données, effectuez les actions suivantes :

1.  Accédez à votre compte Purview.
2.  Sélectionnez **Sources** dans la barre de navigation à gauche.
3.  Sélectionnez **Inscrire**.
4.  Dans Inscrire des sources, sélectionnez **Teradata**. Sélectionnez **Continue** (Continuer)

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="Options d’inscription de Teradata" border="true":::

Dans l’écran **Inscrire des sources (Teradata)** , effectuez les actions suivantes :

1.  Entrez le **Nom** avec lequel la source de données sera listée dans le catalogue.

2.  Entrez le nom d’**hôte** pour la connexion à une source Teradata. Il peut également s’agir d’une adresse IP ou d’une chaîne de connexion complète au serveur.

3.  Sélectionnez une collection ou créez-en une (facultatif).

4.  Terminez pour inscrire la source de données.

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="Inscrire Teradata" border="true":::

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1.  Dans le centre d’administration, cliquez sur **Runtimes d’intégration**. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](https://docs.microsoft.com/azure/purview/manage-integration-runtimes) pour configurer un runtime d’intégration auto-hébergé

2.  Accédez aux **Sources**.

3.  Sélectionnez la source Teradata inscrite.

4.  Sélectionnez **+ Nouvelle analyse**.

5.  Fournissez les renseignements ci-dessous :

    a.  **Nom** : nom de l’analyse

    b.  **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré.

    c.  **Informations d’identification** : sélectionnez les informations d’identification pour vous connecter à votre source de données. Veillez à respecter les points suivants :

    -   Sélectionnez Authentification de base quand vous créez des informations d’identification.
    -   Indiquez un nom d’utilisateur pour la connexion au serveur de base de données dans le champ d’entrée Nom d’utilisateur
    -   Stockez le mot de passe du serveur de base de données dans la clé secrète.

        Pour en savoir plus sur les informations d’identification, reportez-vous au lien [ici](https://docs.microsoft.com/azure/purview/manage-credentials)

6.  **Schéma** : répertorie le sous-ensemble de schémas à importer, exprimé sous la forme d’une liste séparée par des points-virgules. Par exemple : schema1; schema2. Tous les schémas utilisateur sont importés si cette liste est vide. Tous les schémas système (par exemple, SysAdmin) et les objets sont ignorés par défaut. Si la liste est vide, tous les schémas disponibles sont importés.

    Les modèles de nom de schéma acceptables utilisant la syntaxe d’expressions de type SQL LIKE incluent l’utilisation de %, par exemple A%; %B; %C%; D
    - commençant par A ou    
    - se terminant par B ou    
    - contenant C ou    
    - égalant D

    L’utilisation de NOT et des caractères spéciaux n’est pas autorisée.

7.  **Emplacement du pilote** : spécifiez le chemin d’accès à l’emplacement du pilote JDBC dans votre machine virtuelle où s’exécute le runtime d’intégration auto-hébergé. Il doit s’agir du chemin vers l’emplacement du dossier JAR valide.

8.  **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur la machine virtuelle du client pouvant être utilisée par les processus d’analyse. Elle dépend de la taille de la source Teradata à analyser.

    > [!Note] 
    > En règle générale, prévoyez 2 Go de mémoire pour 1 000 tables.

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="configuration de l’analyse" border="true":::

6.  Cliquez sur **Continuer**.

7.  Choisissez votre **déclencheur d’analyse**. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

8.  Vérifiez votre analyse et cliquez sur **Enregistrer et exécuter**.

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