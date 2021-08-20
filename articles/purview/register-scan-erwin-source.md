---
title: Inscrire un serveur erwin Mart et configurer des analyses dans Azure Purview
description: Cet article explique comment inscrire un serveur erwin Mart dans Azure Purview et configurer une analyse.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 7/16/2021
ms.openlocfilehash: 55ec355cf28f08618b1c51670b876f93dee69cc8
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393546"
---
# <a name="register-and-scan-erwin-mart-server-preview"></a>Inscrire et analyser un serveur erwin Mart (préversion)

Cet article explique comment inscrire un serveur erwin Mart dans Purview et configurer une analyse.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

La source erwin prend en charge l’analyse complète pour extraire les métadonnées d’un serveur erwin Mart. Les métadonnées comprennent :

1.  Les modèles logiques uniquement avec Entités, Attributs et Domaines OU
2.  Les modèles physiques uniquement avec Tables, Colonnes, Types de données OU
3.  Les modèles logiques ou physiques

Cette source de données récupère également la traçabilité entre les ressources de données.

> [!Note]
> erwin comme source est actuellement prise en charge dans la préversion. Inscrivez cette source et configurez vos analyses dans vos comptes Purview de non production et envoyez-nous vos commentaires.

## <a name="prerequisites"></a>Prérequis

1.  Configurez le dernier [runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717).
    Pour plus d'informations, consultez la rubrique  
    [Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

    > [!Note]
    > Veillez à exécuter le runtime d’intégration auto-hébergé sur la machine virtuelle sur laquelle s’exécute l’instance erwin Mart.

2.  Assurez-vous que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

3.  Vérifiez que le package \"Visual C++ Redistributable 2012 Update 4\" est installé sur la machine virtuelle sur laquelle s’exécute le runtime d’intégration auto-hébergé. Si vous ne l’avez pas installé, téléchargez-le à partir d’[ici](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Les versions d’erwin Mart prises en charge sont 9.x à 2021.

## <a name="setting-up-authentication-for-a-scan"></a>Configuration de l’authentification pour une analyse

La seule authentification prise en charge pour une source erwin Mart est l’**authentification du serveur** sous la forme d’un nom d’utilisateur et d’un mot de passe.

## <a name="register-an-erwin-mart"></a>Inscrire un serveur erwin Mart

Pour inscrire un nouveau serveur erwin Mart dans votre catalogue de données, procédez comme suit :

1.  Accédez à votre compte Purview.
2.  Sélectionnez **Sources** dans la barre de navigation à gauche.
3.  Sélectionnez **Inscrire**.
4.  Dans Inscrire des sources, sélectionnez **erwin**. Sélectionnez **Continuer**.
    :::image type="content" source="media/register-scan-erwin-source/register-sources.png" alt-text="Inscrire la source erwin" border="true":::
    
Sur l’écran Inscrire des sources (erwin), procédez comme suit :

1.  Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.

2.  Entrez le **nom du serveur** erwin Mart. Il s’agit du nom d’hôte réseau utilisé pour se connecter au serveur erwin Mart. Par exemple, localhost.

3.  Entrez le numéro de **port** utilisé lors de la connexion à erwin Mart. Par défaut, cette valeur est définie sur 18170.

4.  Entrez le **nom de l’application**.

    >[!Note]
    > Vous pouvez trouver les détails ci-dessus en accédant jusqu’à votre modèle de données erwin. Cliquez sur Mart -\> Connexion pour afficher les détails relatifs au nom du serveur, au port et au nom de l’application.

    :::image type="content" source="media/register-scan-erwin-source/erwin-details.png" alt-text="Rechercher des détails erwin" border="true":::
    
5.  Sélectionnez une collection ou créez-en une (facultatif).

6.  Terminez pour inscrire la source de données.

    :::image type="content" source="media/register-scan-erwin-source/register-erwin.png" alt-text="Inscrire la source" border="true":::
    

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1.  Dans le centre d’administration, cliquez sur Runtimes d’intégration. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré sur la machine virtuelle sur laquelle s’exécute l’instance erwin Mart. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](./manage-integration-runtimes.md) pour configurer un runtime d’intégration auto-hébergé

2.  Accédez aux **Sources**.

3.  Sélectionnez le serveur **erwin Mart** inscrit.

4.  Sélectionnez **+ Nouvelle analyse**.

5.  Fournissez les renseignements ci-dessous :

    a.  **Nom** : nom de l’analyse

    b.  **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré.

    c.  **Nom du serveur, Port** et **Nom de l’application** sont renseignés automatiquement en fonction des valeurs entrées lors de l’inscription.

    d.  **Informations d’identification :** sélectionnez les informations d’identification configurées pour vous connecter à votre serveur erwin Mart. Lors de la création d’informations d’identification, veillez à :
    - Sélectionner **Authentification de base** comme méthode d’authentification.
    - Indiquer le nom d’utilisateur du serveur erwin Mart dans le champ Nom d’utilisateur.
    - Enregistrer votre mot de passe utilisateur pour l’authentification du serveur dans le secret du coffre de clés.

    Pour plus d’informations sur les informations d’identification, reportez-vous à [ce lien](manage-credentials.md).

    e.  **Utiliser Internet Information Services (IIS)**  : sélectionnez True ou False pour indiquer si Microsoft Internet Information Services (IIS) doit être utilisé lors de la connexion au serveur erwin Mart. La valeur par défaut est False.

    f.  **Utiliser le protocole SSL** : Sélectionnez True ou False pour indiquer si le protocole SSL doit être utilisé lors de la connexion au serveur erwin Mart. La valeur par défaut est False.

    > [!Note]
    > Ce paramètre s’applique uniquement à erwin Mart version 9.1 ou ultérieure.

    g.  **Mode de navigation** : sélectionnez le mode de navigation d’erwin Mart. Les options possibles sont « Bibliothèques et modèles » ou « Bibliothèques uniquement ».

    h.  **Modèles** : étendez votre analyse en fournissant une liste de chaînes de localisateur de modèles erwin séparées par des points-virgules. Par exemple, mart://Mart/Samples/eMovies;mart://Mart/Erwin_Tutorial/AP_Physical.

    i.  **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur la machine virtuelle du client pouvant être utilisée par les processus d’analyse. Elle dépend de la taille du serveur erwin Mart à analyser.
    :::image type="content" source="media/register-scan-erwin-source/setup-scan.png" alt-text="Déclencher une analyse" border="true":::
   

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
