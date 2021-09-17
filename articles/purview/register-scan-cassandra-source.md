---
title: Inscrire Cassandra en tant que source et configurer des analyses dans Azure Purview
description: Cet article explique comment inscrire le serveur Cassandra dans Azure Purview et configurer une analyse.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 8/06/2021
ms.openlocfilehash: 8e2136cdb4cca53d81412335f7f5878fd8d70b86
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785285"
---
# <a name="register-and-scan-a-cassandra-source"></a>Inscrire et analyser une source Cassandra 

Cet article explique comment inscrire un serveur Cassandra dans Purview et configurer une analyse.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

La source Cassandra prend en charge l’analyse complète pour extraire les métadonnées d’un serveur Cassandra et extrait la traçabilité entre les ressources de données.

## <a name="prerequisites"></a>Prérequis

1.  Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717).
    Pour plus d’informations, consultez [Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

2.  Assurez-vous que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

3.  Vérifiez que le package \"Visual C++ Redistributable 2012 Update 4\" est installé sur la machine dotée du runtime d’intégration auto-hébergé. S\'il n’est pas déjà installé, vous pouvez le télécharger [ici](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Les versions de serveur Cassandra prises en charge sont 3.x à 4.x

## <a name="register-a-cassandra-server"></a>Inscrire un serveur Cassandra

Pour inscrire un nouveau serveur Cassandra dans votre catalogue de données, procédez comme suit :

1.  Accédez à votre compte Purview.
2.  Sélectionnez **Data Map** dans le volet de navigation de gauche.
3.  Sélectionnez **Inscrire**.
4.  Dans Inscrire des sources, sélectionnez **Cassandra**. Sélectionnez **Continuer.** 
    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="Inscrire la source Cassandra" border="true":::
   
Dans l’écran Inscrire des sources (Cassandra), effectuez les actions suivantes :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.

2. Entrez l’adresse du serveur sur lequel le serveur Cassandra est en cours d’exécution dans le champ **Hôte**. Par exemple, 20.190.193.10

3. Entrez le port utilisé par le serveur Cassandra dans le champ **Port**.
4. Sélectionnez une collection ou créez-en une (facultatif).

5.  Cliquez sur **Inscrire**.
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="configurer la source cassandra" border="true":::

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1.  Dans le centre d’administration, cliquez sur Runtimes d’intégration. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](./manage-integration-runtimes.md) pour configurer un runtime d’intégration auto-hébergé

2.  Accédez aux **Sources**.

3.  Sélectionnez le serveur **Cassandra** inscrit.

4.  Sélectionnez **+ Nouvelle analyse**.

5.  Fournissez les renseignements ci-dessous :

    a.  **Nom** : nom de l’analyse

    b.  **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré

    c.  **Informations d’identification** : lors de la configuration des informations d’identification Cassandra, veillez à :

    - Sélectionner **Authentification de base** comme méthode d’authentification
    - Indiquez le nom d’utilisateur pour le compte de la connexion établie dans le champ Nom d’utilisateur. 
    - Enregistrer le mot de passe de l’utilisateur Cassandra au nom duquel la connexion est établie dans le secret du coffre de clés

    Pour plus d’informations sur les informations d’identification, reportez-vous à [ce lien](manage-credentials.md).

    d.  **Espaces de mémoire** : spécifiez une liste d’espaces de clés Cassandra à importer. Les espaces de clés multiples doivent être séparés par des points-virgules. Par exemple, espace de clés1 ; espace de clés2. Si la liste est vide, tous les espaces de clés disponibles sont importés.
    Les modèles de nom d’espaces de clés acceptables utilisant la syntaxe d’expressions de type SQL LIKE incluent l’utilisation de %, 

    par exemple, A%; %B; %C%; D
    - commençant par A ou
    - se terminant par B ou
    - contenant C ou
    - égalant D    
L’utilisation de NOT et des caractères spéciaux n’est pas autorisée.
    
    f. **Utiliser le protocole SSL** : sélectionnez True ou False pour indiquer si le protocole SSL doit être utilisé lors de la connexion au serveur Cassandra. La valeur par défaut est False.

    g. **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur la machine virtuelle du client pouvant être utilisée par les processus d’analyse. Elle dépend de la taille du serveur Cassandra à analyser.
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="analyser la source cassandra" border="true":::

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