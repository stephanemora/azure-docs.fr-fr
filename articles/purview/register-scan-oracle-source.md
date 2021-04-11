---
title: Inscrire une source Oracle et configurer des analyses (préversion) dans Azure Purview
description: Cet article explique comment inscrire une source Oracle dans Azure Purview et configurer une analyse.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 76aadd667691e12c61e0e5e13c13ca0241a9f0ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045499"
---
# <a name="register-and-scan-oracle-source-preview"></a>Inscrire et analyser une source Oracle (préversion)

Cet article explique comment inscrire une base de données Oracle dans Purview et configurer une analyse.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

La source Oracle prend en charge l’**Analyse complète** pour extraire les métadonnées d’une base de données Oracle et récupère la **Traçabilité** entre les ressources de données.

## <a name="prerequisites"></a>Prérequis

1.  Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717).
    Pour plus d’informations, consultez [Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

2.  Assurez-vous que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

3.  Vérifiez que le package \"Visual C++ Redistributable 2012 Update 4\" est installé sur la machine dotée du runtime d’intégration auto-hébergé. S\'il n’est pas déjà installé, vous pouvez le télécharger [ici](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Vous devez télécharger manuellement le pilote Oracle JDBC [ici](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) sur la machine virtuelle où s’exécute le runtime d’intégration auto-hébergé.

    > [!Note] 
    > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne l’installez pas dans un compte d’utilisateur.

5.  Les versions de base de données Oracle prises en charge sont 6i à 19c.

6.  Autorisation de l’utilisateur : pour garantir la réussite de l’analyse pour la première fois, vous devez disposer d’une autorisation de type Administrateur système complet.

    Pour les analyses suivantes, un accès en lecture seule aux tables système est requis. L’utilisateur doit avoir l’autorisation de créer une session, ainsi que le rôle SELECT\_CATALOG\_ROLE attribué. L’utilisateur peut également disposer de l’autorisation SELECT pour chaque table système à partir de laquelle ce connecteur interroge des métadonnées :
       > grant create session to \[utilisateur\];\
        grant select on all\_users to \[utilisateur\];\
        grant select on dba\_objects to \[utilisateur\];\
        grant select on dba\_tab\_comments to \[utilisateur\];\
        grant select on dba\_external\_locations to \[utilisateur\];\
        grant select on dba\_directories to \[utilisateur\];\
        grant select on dba\_mviews to \[utilisateur\];\
        grant select on dba\_clu\_columns to \[utilisateur\];\
        grant select on dba\_tab\_columns to \[utilisateur\];\
        grant select on dba\_col\_comments to \[utilisateur\];\
        grant select on dba\_constraints to \[utilisateur\];\
        grant select on dba\_cons\_columns to \[utilisateur\];\
        grant select on dba\_indexes to \[utilisateur\];\
        grant select on dba\_ind\_columns to \[utilisateur\];\
        grant select on dba\_procedures to \[utilisateur\];\
        grant select on dba\_synonyms to \[utilisateur\];\
        grant select on dba\_views to \[utilisateur\];\
        grant select on dba\_source to \[utilisateur\];\
        grant select on dba\_triggers to \[utilisateur\];\
        grant select on dba\_arguments to \[utilisateur\];\
        grant select on dba\_sequences to \[utilisateur\];\
        grant select on dba\_dependencies to \[utilisateur\];\
        grant select on V\_\$INSTANCE to \[utilisateur\];\
        grant select on v\_\$database to \[utilisateur\];
    
## <a name="setting-up-authentication-for-a-scan"></a>Configuration de l’authentification pour une analyse

La seule authentification prise en charge pour une source Oracle est l’**Authentification de base**.

## <a name="register-an-oracle-source"></a>Inscrire une source Oracle

Pour inscrire une nouvelle source Oracle dans votre catalogue de données, procédez comme suit :

1.  Accédez à votre compte Purview.
2.  Sélectionnez **Sources** dans la barre de navigation à gauche.
3.  Sélectionnez **Inscrire**.
4.  Dans Inscrire des sources, sélectionnez **Oracle**. Sélectionnez **Continuer**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="Inscrire des sources" border="true":::

Dans l’écran **Inscrire des sources (Oracle)** , procédez comme suit :

1.  Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.

2.  Entrez le nom d’**Hôte** pour la connexion à une source Oracle. Cela peut être :
    - Nom d’hôte utilisé par JDBC pour se connecter au serveur de base de données. Par exemple, MyDatabaseServer.com ou
    - Adresse IP. Par exemple, 192.169.1.2 ou
    - Sa chaîne de connexion JDBC complète. Par exemple,\
        jdbc:oracle:thin:@(DESCRIPTION=(LOAD\_BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver1)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver2)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver3)(PORT=1521))(CONNECT\_DATA=(SERVICE\_NAME=orcl)))

3.  Entrez le **Numéro de port** utilisé par JDBC se connecter au serveur de base de données (1521 par défaut pour Oracle).

4.  Entrez le **Nom du service Oracle** utilisé par JDBC pour se connecter au serveur de base de données.

5.  Sélectionnez une collection ou créez-en une (facultatif)

6.  Terminez pour inscrire la source de données.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="options pour inscrire des sources" border="true":::

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1.  Dans le centre d’administration, cliquez sur Runtimes d’intégration. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](./manage-integration-runtimes.md) pour créer un runtime d’intégration auto-hébergé.

2.  Accédez aux **Sources**.

3.  Sélectionnez la source Oracle inscrite.

4.  Sélectionnez **+ Nouvelle analyse**.

5.  Fournissez les renseignements ci-dessous :

    a.  **Nom** : nom de l’analyse

    b.  **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré

    c.  **Informations d’identification** : sélectionnez les informations d’identification pour vous connecter à votre source de données. Veillez à respecter les points suivants :
    - Sélectionnez Authentification de base quand vous créez des informations d’identification.        
    - Indiquez le nom d’utilisateur utilisé par JDBC pour se connecter au serveur de base de données dans le champ d’entrée Nom d’utilisateur        
    - Stockez le nom d’utilisateur utilisé par JDBC pour se connecter au serveur de base de données dans la clé secrète.

    d.  **Schéma** : répertorie le sous-ensemble de schémas à importer, exprimé sous la forme d’une liste séparée par des points-virgules. Par exemple : schema1; schema2. Tous les schémas utilisateur sont importés si cette liste est vide. Tous les schémas système (par exemple, SysAdmin) et les objets sont ignorés par défaut. Si la liste est vide, tous les schémas disponibles sont importés.
        Les modèles de nom de schéma acceptables utilisant la syntaxe d’expressions de type SQL LIKE incluent l’utilisation de % par exemple. A%; %B; %C%; D
       -   commençant par A ou        
       -   se terminant par B ou        
       -   contenant C ou        
       -   égalant D

    L’utilisation de NOT et des caractères spéciaux n’est pas autorisée.

6.  **Emplacement du pilote** : spécifiez le chemin d’accès à l’emplacement du pilote JDBC dans votre machine virtuelle où s’exécute le runtime d’intégration auto-hébergé. Il doit s’agir du chemin vers l’emplacement du dossier JAR valide.

7.  **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur la machine virtuelle du client pouvant être utilisée par les processus d’analyse. Elle dépend de la taille de la source SAP S/4HANA à analyser.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="Analyser Oracle" border="true":::

8.  Cliquez sur **Continuer**.

9.  Choisissez votre **déclencheur d’analyse**. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

10.  Vérifiez votre analyse et cliquez sur **Enregistrer et exécuter**.

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