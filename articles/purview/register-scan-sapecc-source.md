---
title: Inscrire une source SAP ECC et configurer des analyses (préversion) dans Azure Purview
description: Cet article explique comment inscrire une source SAP ECC dans Azure Purview et configurer une analyse.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: b72538c61d1a68bca655b0d9529f654068727f86
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101696050"
---
# <a name="register-and-scan-sap-ecc-source-preview"></a>Inscrire et analyser la source SAP ECC (préversion)

Cet article explique comment inscrire une source SAP ECC dans Purview et configurer une analyse.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

La source SAP ECC prend en charge l’**Analyse complète** pour extraire les métadonnées d’une instance SAP ECC et récupère la **Traçabilité** entre les ressources de données.

## <a name="prerequisites"></a>Prérequis

1.  Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717).
    Pour plus d’informations, consultez [Créer et configurer un runtime d’intégration auto-hébergé](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

2.  Assurez-vous que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

3.  Vérifiez que le package \"Visual C++ Redistributable 2012 Update 4\" est installé sur la machine dotée du runtime d’intégration auto-hébergé. S\'il n’est pas déjà installé, vous pouvez le télécharger [ici](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Téléchargez la version 64 bits de [SAP Connector for Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) à partir du site web de SAP et installez-la sur la machine dotée du runtime d\'intégration auto-hébergé. Pendant l’installation, veillez à sélectionner l’option **Installer les assemblys dans le GAC** dans la fenêtre des **Étapes de configuration facultatives**.

    :::image type="content" source="media/register-scan-sapecc-source/requirement.png" alt-text="Prérequis" border="true":::

5.  Le connecteur lit les métadonnées à partir de SAP à l’aide de l’API Java Connector (JCo) 3.0. Par conséquent, assurez-vous que Java Connector est disponible sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.
    Assurez-vous que vous utilisez la distribution JCo correcte pour votre environnement. Par exemple, sur une machine Microsoft Windows, assurez-vous que les fichiers sapjco3.jar et sapjco3.dll sont disponibles.

    > [!Note] 
    > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne l’installez pas dans un compte d’utilisateur.

6.  Déployez le module de fonction ABAP d’extraction des métadonnées sur le serveur SAP en suivant les étapes mentionnées dans le [Guide de déploiement des fonctions ABAP](abap-functions-deployment-guide.md). Vous aurez besoin d’un compte de développeur ABAP pour créer le module de fonction RFC sur le serveur SAP. Le compte d’utilisateur requiert des autorisations suffisantes pour se connecter au serveur SAP et exécuter les modules de fonction RFC suivants :
    -   STFC_CONNECTION (vérifier la connectivité)
    -   RFC_SYSTEM_INFO (vérifier les informations système)


## <a name="setting-up-authentication-for-a-scan"></a>Configuration de l’authentification pour une analyse

La seule authentification prise en charge pour une source SAP ECC est l’**Authentification de base**.

## <a name="register-sap-ecc-source"></a>Inscrire une source SAP ECC

Pour inscrire une nouvelle source SAP ECC dans votre catalogue de données, procédez comme suit :

1.  Accédez à votre compte Purview.
2.  Sélectionnez **Sources** dans la barre de navigation à gauche.
3.  Sélectionnez **Inscrire**.
4.  Dans Inscrire des sources, sélectionnez **SAP ECC**. Sélectionnez **Continuer.**

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc.png" alt-text="Options d’inscription de SAP ECC" border="true":::

Dans l’écran **Inscrire des sources (SAP ECC)** , procédez comme suit :

1.  Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.

2.  Entrez le nom du **Serveur d’applications** pour vous connecter à la source SAP ECC.
    Il peut également s’agir d’une adresse IP de l’hôte du serveur d’applications SAP.

3.  Entrez le **Numéro du système** SAP. Il s’agit d’un entier à deux chiffres compris entre 00 et 99.

4.  Sélectionnez une collection ou créez-en une (facultatif)

5.  Terminez pour inscrire la source de données.

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc-2.png" alt-text="Inscrire SAP ECC" border="true":::

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1.  Dans le centre d’administration, cliquez sur Runtimes d’intégration. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](https://docs.microsoft.com/azure/purview/manage-integration-runtimes) pour créer un runtime d’intégration auto-hébergé.

2.  Accédez aux **Sources**

3.  Sélectionnez la source SAP ECC inscrite.

4.  Sélectionnez **+ Nouvelle analyse**.

5.  Fournissez les renseignements ci-dessous :

    a.  **Nom** : nom de l’analyse

    b.  **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré

    c.  **Informations d’identification** : sélectionnez les informations d’identification pour vous connecter à votre source de données. Veillez à respecter les points suivants :

    -   Sélectionnez Authentification de base quand vous créez des informations d’identification.
    -   Indiquez un identifiant d’utilisateur pour la connexion au serveur SAP dans le champ d’entrée Nom d’utilisateur.
    -   Stockez le mot de passe utilisateur utilisé pour se connecter au serveur SAP dans la clé secrète.

    d.  **ID client** : entrez l’identifiant client SAP. Il s’agit d’un nombre à trois chiffres compris entre 000 et 999.

    e.  **Chemin de la bibliothèque JCo** : chemin vers le répertoire contenant les bibliothèques JCo

    f.  **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur la machine virtuelle du client pouvant être utilisée par les processus d’analyse. Elle dépend de la taille de la source SAP ECC à analyser.

    :::image type="content" source="media/register-scan-sapecc-source/scan-sapecc.png" alt-text="Analyser SAP ECC" border="true":::

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