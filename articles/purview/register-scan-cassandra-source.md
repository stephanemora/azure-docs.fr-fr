---
title: Inscrire et analyser une source Cassandra
description: Cet article décrit la manière d’inscrire un serveur Cassandra dans Azure Purview et de configurer une analyse pour extraire des métadonnées.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 5f8feffebff71c25f2a0d62d775894e7c4431615
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005313"
---
# <a name="register-and-scan-a-cassandra-source-preview"></a>Inscrire et analyser une source Cassandra (préversion)

Cet article décrit la manière d’inscrire un serveur Cassandra dans Azure Purview et de configurer une analyse.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez utiliser Purview pour effectuer des analyses complètes sur Cassandra afin d’extraire des métadonnées et une traçabilité entre des ressources de données. 

## <a name="prerequisites"></a>Prérequis

1.  Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717).
    Pour plus d’informations, consultez [Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

2.  Vérifiez que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

3.  Vérifiez que le package Redistributable Visual C++ pour Visual Studio 2012 Update 4 est installé sur la machine dotée du runtime d’intégration auto-hébergé. Si cette mise à jour n’est pas installée, [téléchargez-la](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Vérifiez que la version de votre serveur Cassandra est 3.*x* ou 4.*x*.

## <a name="register-a-cassandra-server"></a>Inscrire un serveur Cassandra

Pour inscrire un nouveau serveur Cassandra dans votre catalogue de données :

1.  Accédez à votre compte Purview.
2.  Sélectionnez **Data Map** dans le volet gauche.
3.  Sélectionnez **Inscription**.
4.  Dans l’écran **Inscrire des sources**, sélectionnez **Cassandra**, puis **Continuer** :

    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="Capture d’écran montrant l’écran Inscrire des sources." border="true":::
   
1. Dans l’écran **Inscrire des sources (Cassandra)**  :

   1. Saisissez un **Nom**. La source de données va utiliser ce nom dans le catalogue.

   2. Dans la zone **Hôte**, entrez l’adresse du serveur sur lequel le serveur Cassandra est en cours d’exécution. Par exemple : 20.190.193.10.

   3. Dans la zone **Port**, entrez le port utilisé par le serveur Cassandra.
   4. Sélectionnez une collection ou créez-en une (facultatif).
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="Capture d’écran montrant l’écran Inscrire des sources (Cassandra)." border="true":::
   5.  Sélectionnez **Inscription**.


## <a name="create-and-run-a-scan"></a>Créer et exécuter une analyse

Pour créer et exécuter une analyse :

1.  Dans le centre d’administration, sélectionnez **Runtimes d’intégration**. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, effectuez [ces étapes pour configurer un runtime d’intégration auto-hébergé](./manage-integration-runtimes.md).
    

2.  Accédez à **Sources**.

3.  Sélectionnez le serveur Cassandra inscrit.

4.  Sélectionnez **Nouvelle analyse**.

5.  Fournissez les détails ci-après.

    a.  **Nom** : Spécifiez un nom pour l’analyse.

    b.  **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré.

    c.  **Informations d’identification** : Quand vous configurez les informations d’identification Cassandra, veillez à respecter ces consignes :

    - Sélectionnez **Authentification de base** comme méthode d’authentification.
    - Dans la zone **Nom d’utilisateur**, indiquez le nom de l’utilisateur pour lequel vous établissez la connexion. 
    - Dans le secret du coffre de clés, enregistrez le mot de passe de l’utilisateur Cassandra pour lequel vous établissez la connexion.

    Pour plus d’informations, consultez [Informations d’identification pour l’authentification des sources dans Purview](manage-credentials.md).

    d.  **Espaces de clés** : Spécifiez la liste des espaces de clés Cassandra à importer. Les espaces de clés doivent être séparés par des points-virgules. Par exemple, espace de clés1 ; espace de clés2. Si la liste est vide, tous les espaces de clés disponibles sont importés.
    
    Vous pouvez utiliser des modèles de nom d’espace de clés qui utilisent une syntaxe d’expression de type SQL, contenant %. 

    Par exemple : A%; %B; %C%; D

    Cette expression a la signification suivante :
    - Commence par A ou
    - Se termine par B ou
    - Contient C ou
    - Est égal à D    

    Vous ne pouvez pas utiliser NOT ni des caractères spéciaux.
    
    e. **Utiliser Secure Sockets Layer (SSL)**  : Sélectionnez **True** ou **False** pour indiquer si le protocole SSL doit être utilisé lors de la connexion au serveur Cassandra. Par défaut, cette option a la valeur **False**.

    f. **Mémoire maximale disponible** : Spécifiez la mémoire maximale (en Go) disponible sur la machine virtuelle à utiliser à des fins d’analyse. Cette valeur dépend de la taille du serveur Cassandra à analyser.
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="analyser la source cassandra" border="true":::

6.  Sélectionnez **Test Connection** (Tester la connexion).

7.  Sélectionnez **Continuer**.

8.  Sélectionnez un **déclencheur d’analyse**. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

9.  Passez en revue votre analyse, puis sélectionnez **Enregistrer et exécuter**.

## <a name="view-your-scans-and-scan-runs"></a>Afficher vos analyses et exécutions d’analyse

1. Accédez au Centre d’administration. Sélectionnez **Sources de données** dans la section **Sources et analyse**.

2. Sélectionnez la source de données dont vous voulez consulter les analyses. La liste des analyses existantes sur cette source de données apparaît.

3. Sélectionnez l’analyse dont vous voulez voir les résultats.

   La page qui s’affiche présente toutes les exécutions d’analyse précédentes avec les métriques et l’état de chacune d’elles. 
   Elle indique aussi : 
   - Si votre analyse a été planifiée ou manuelle. 
   - Le nombre de ressources auxquelles des classifications étaient appliquées. 
   - Le nombre total de ressources découvertes. 
   - L’heure de début et l’heure de fin de l’analyse.
   - La durée de l’analyse.

## <a name="manage-your-scans"></a>Gestion de vos analyses

Pour gérer ou supprimer une analyse :

1. Accédez au Centre d’administration. Sélectionnez **Sources de données** dans la section **Sources et analyse**. Sélectionnez ensuite la source de données dont vous voulez gérer l’analyse.

2. Sélectionnez l’analyse que vous voulez gérer. 
   - Vous pouvez modifier l’analyse en sélectionnant **Modifier**.

   - Vous pouvez supprimer l’analyse en sélectionnant **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- [Navigation dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
