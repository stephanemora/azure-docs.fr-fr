---
title: Inscrire une source Looker et configurer des analyses dans Azure Purview
description: Cet article explique comment inscrire une source Looker dans Azure Purview et configurer une analyse.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 7/16/2021
ms.openlocfilehash: 903435ff45249d0c066b386d84ce060057bac666
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666575"
---
# <a name="register-and-scan-lookerpreview"></a>Inscrire et analyser Looker (préversion)

Cet article explique comment inscrire un serveur Looker dans Purview et configurer une analyse.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

La source Looker prend en charge l’analyse complète pour extraire les métadonnées d’un serveur Looker. Les métadonnées ont été importées à partir d’un serveur Looker, y compris les connexions de base de données, les modèles LookML et les rapports associés (Looker et tableaux de bord). Cette source de données récupère également la traçabilité entre les ressources de données.

> [!Note]
> Looker en tant que source est actuellement pris en charge dans la version préliminaire privée. Inscrivez cette source et configurez vos analyses dans vos comptes Purview de non production et envoyez-nous vos commentaires.

## <a name="prerequisites"></a>Prérequis

1.  Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717).
    Pour plus d’informations, consultez [Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

2.  Assurez-vous que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

3.  Vérifiez que le package \"Visual C++ Redistributable 2012 Update 4\" est installé sur la machine virtuelle sur laquelle s’exécute le runtime d’intégration auto-hébergé. Si vous ne \'l’avez pas installé, téléchargez-le à partir d’[ici](https://www.microsoft.com/download/details.aspx?id=30679).

4.  La version du serveur Looker pris en charge est 7.2

## <a name="setting-up-authentication-for-a-scan"></a>Configuration de l’authentification pour une analyse

Une clé API3 est requise pour se connecter au serveur Looker. La clé API3 se compose d’un client_id public et d’un client_secret privé et suit un modèle d’authentification OAuth2.

## <a name="register-a-looker-server"></a>Inscrire un serveur Looker

Pour inscrire un nouveau serveur Looker dans votre catalogue de données, procédez comme suit :

1. Accédez à votre compte Purview.
2. Sélectionnez **Sources** dans la barre de navigation à gauche.
3. Sélectionnez **Inscrire**.
4. Dans Inscrire des sources, sélectionnez **Looker**. Sélectionnez **Continuer.**
    :::image type="content" source="media/register-scan-looker-source/register-sources.png" alt-text="Inscrire la source Looker" border="true":::


Sur l’écran Inscrire des sources (Looker), procédez comme suit :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.

2. Entrez l’URL de l’API Looker dans le champ **URL de l'API serveur**. Le port par défaut pour les requêtes d’API est le port 19999. En outre, tous les points de terminaison de l’API Looker requièrent une connexion HTTPS. Par exemple, « https://azurepurview.cloud.looker.com  ».

3. Sélectionnez une collection ou créez-en une (facultatif).

4. Terminez pour inscrire la source de données.

    :::image type="content" source="media/register-scan-looker-source/scan-source.png" alt-text="Analyser la source Looker" border="true":::

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Dans le centre d’administration, cliquez sur Runtimes d’intégration. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](./manage-integration-runtimes.md) pour configurer un runtime d’intégration auto-hébergé

2. Accédez aux **Sources**.

3. Sélectionnez le serveur **Looker** inscrit.

4. Sélectionnez **+ Nouvelle analyse**.

5. Fournissez les renseignements ci-dessous :

    a.  **Nom** : nom de l’analyse

    b.  **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré.

    c.  L’**URL de l'API serveur** est renseignée automatiquement en fonction de la valeur entrée lors de l’inscription.

    d.  **Informations d’identification** : lors de la configuration des informations d’identification Looker, veillez à :

    - Sélectionner **Authentification de base** comme méthode d’authentification
    - Indiquez l’ID client de votre clé API3 dans le champ Nom d’utilisateur
    - Enregistrez la clé secrète client de votre clé API3 dans le coffre de clés.

    **Remarque :** Pour accéder à l’ID client et à la clé secrète, accédez à Looker- \> Admin- \> Utilisateurs- \> cliquez sur **Modifier**. Pour un utilisateur \> cliquez sur **Modifier les clés** -\> Utilisez l’ID client et la clé secrète client, ou créez-en un.
    :::image type="content" source="media/register-scan-looker-source/looker-details.png" alt-text="obtenir des détails sur Looker" border="true":::
    

    Pour en savoir plus sur les informations d’identification, reportez-vous au lien [ici](manage-credentials.md)

    e.  **Filtre de projets** : étendez votre analyse en fournissant une liste de chaînes de projets Looker séparées par des points-virgules. Cette option permet de sélectionner des apparences et des tableaux de bord par leur projet parent.

    f.  **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur la machine virtuelle du client pouvant être utilisée par les processus d’analyse. Elle dépend de la taille du serveur erwin Mart à analyser.

    :::image type="content" source="media/register-scan-looker-source/setup-scan.png" alt-text="Déclencher une analyse" border="true":::

6. Cliquez sur **Tester la connexion**.

7. Cliquez sur **Continuer**.

8. Choisissez votre **déclencheur d’analyse**. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

9. Vérifiez votre analyse et cliquez sur **Enregistrer et exécuter**.

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
