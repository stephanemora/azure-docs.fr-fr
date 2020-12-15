---
title: Inscrire une source Teradata et configurer des analyses (préversion)
description: Cet article explique comment inscrire une source Teradata dans Azure Purview et configurer une analyse.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 12/06/2020
ms.openlocfilehash: 170ece293f8d24f3a29774c64c14f9334fe0930c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841650"
---
# <a name="register-and-scan-teradata-source-preview"></a>Inscrire et analyser une source Teradata (préversion)

Cet article explique comment inscrire une source Teradata dans Purview et configurer une analyse.

> [!IMPORTANT]
> Cette source de données est actuellement en préversion. Essayez-le et envoyez-nous vos commentaires.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

La source Teradata gère l’**Analyse complète** pour extraire les métadonnées d’une base de données Teradata, et récupère la **Traçabilité** entre les ressources de données.

## <a name="prerequisites"></a>Prérequis

- Le connecteur ne prend en charge que le magasin de données situé au sein d’un réseau local, d’un réseau virtuel Azure ou d’un cloud privé virtuel Amazon. Par conséquent, vous devez configurer un [runtime d’intégration auto-hébergé](manage-integration-runtimes.md) pour vous y connecter.

- Assurez-vous que le Java Runtime Environment (JRE) est installé sur la machine virtuelle où le runtime d’intégration auto-hébergé est installé.
 
- Vérifiez que le package « Visual C++ Redistribuable 2012 Update 4 » est installé sur la machine du runtime d’intégration auto-hébergé. S’il n’est pas déjà installé, vous pouvez le télécharger [ici](https://www.microsoft.com/download/details.aspx?id=30679).

- Vous devrez installer manuellement un pilote nommé Teradata JDBC Driver sur votre machine virtuelle locale. Le fichier JAR exécutable peut être téléchargé à partir du [site web Teradata](https://downloads.teradata.com/).

    > [!Note] 
    > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne procédez pas à l’installation dans un compte d’utilisateur.

- Les versions de base de données Teradata reconnues sont **12.x à 16.x**. Assurez-vous d’avoir un accès en lecture à la source Teradata qui est analysée.

### <a name="feature-flag"></a>Indicateur de fonctionnalité

L’inscription et l’analyse d’une source Teradata sont disponibles derrière un indicateur de fonctionnalité. Ajoutez ce qui suit à votre URL : *?feature.ext.datasource=%7b"teradata":"true"%7d* 

Par exemple, une URL complète [https://web.purview.azure.com/?feature.ext.datasource=%7b"metadata":"true"%7d](https://web.purview.azure.com/?feature.ext.datasource=%7b"teradata":"true"%7d)

## <a name="setting-up-authentication-for-a-scan"></a>Configuration de l’authentification pour une analyse
Seule l’authentification **Base de données de base** permet de configurer l’authentification d’une source Teradata.

## <a name="register-a-teradata-source"></a>Inscription d’une source Teradata

Pour inscrire une nouvelle source Teradata dans votre catalogue de données, effectuez les actions suivantes :

1. Accédez à votre compte Purview.
2. Sélectionnez **Sources** dans la barre de navigation à gauche.
3. Sélectionnez **Inscrire**.
4. Dans **Inscrire des sources**, sélectionnez **Teradata**.
5. Sélectionnez **Continue** (Continuer)

Dans l’écran **Inscrire des sources (Teradata)** , effectuez les actions suivantes :

1. Entrez le **Nom** avec lequel la source de données sera listée dans le catalogue.
2. Entrez le nom d’**hôte** pour la connexion à une source Teradata. Il peut également s’agir d’une adresse IP ou d’une chaîne de connexion complète au serveur.
3. Sélectionnez une collection ou créez-en une (facultatif).
4. Utilisez **Terminer** pour inscrire la source de données.

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="options pour inscrire des sources" border="true":::

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :
1. Dans le centre d’administration, cliquez sur *Runtimes d’intégration*. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](manage-integration-runtimes.md) afin de créer un runtime d’intégration auto-hébergé pour les analyses sur une machine virtuelle locale ou Azure qui a accès à votre réseau local.

2. Ensuite, accédez à **Sources**.

3. Sélectionnez la source Teradata inscrite.

4. Sélectionnez + Nouvelle analyse.
 
5. Fournissez les renseignements ci-dessous :

    - Nom : nom de l’analyse.

    - Se connecter via le runtime d’intégration : sélectionnez le runtime d’intégration auto-hébergé configuré.

    - Méthode d’authentification : l’authentification de base de données est la seule option prise en charge pour l’instant. Elle est sélectionnée par défaut.

    - Nom d’utilisateur : nom d’utilisateur pour se connecter au serveur de base de données. Ce nom d’utilisateur doit disposer d’un accès en lecture au serveur.

    - Mot de passe : mot de passe utilisateur utilisé pour la connexion au serveur de base de données.

    - Schéma : liste le sous-ensemble de schémas à importer, exprimé sous la forme d’une liste séparée par des points-virgules. Par exemple, schéma1; schéma2. Tous les schémas utilisateur sont importés si cette liste est vide. Tous les schémas système (par exemple, SysAdmin) et les objets sont ignorés par défaut.

        Les modèles de nom de schéma acceptables utilisant la syntaxe d’expressions de type SQL LIKE incluent l’utilisation de %, par exemple A%; %B; %C%; D
        - commençant par A ou    
        - se terminant par B ou    
        - contenant C ou    
        - égalant D

        L’utilisation de NOT et des caractères spéciaux n’est pas autorisée.

    - Emplacement du pilote : chemin complet de l’emplacement du pilote Teradata sur la machine virtuelle du client. Le nom du pilote Teradata JDBC doit être : com.teradata.jdbc.TeraDriver.

    - Mémoire maximale disponible : mémoire maximale (en Go) disponible sur la machine virtuelle du client pour son utilisation par les processus d’analyse. Elle dépend de la taille de la source Teradata à analyser.

    > [!Note] 
    > En règle générale, prévoyez 2 Go de mémoire pour 1 000 tables.

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="configuration de l’analyse" border="true":::

6. Cliquez sur *Continuer*.

7. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

    :::image type="content" source="media/register-scan-teradata-source/scan-trigger.png" alt-text="déclencheur d’analyse" border="true":::

8. Vérifiez votre analyse et cliquez sur *Enregistrer et exécuter*.

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

- [Parcourir le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Effectuer une recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
