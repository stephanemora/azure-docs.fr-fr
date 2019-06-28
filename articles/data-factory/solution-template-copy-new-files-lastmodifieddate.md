---
title: Copier les fichiers nouveaux et modifiés par LastModifiedDate avec Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser un modèle de solution pour copier les fichiers nouveaux et modifiés par LastModifiedDate avec Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/8/2019
ms.openlocfilehash: cae75f4d64c8b3f74cc40e94a675c0f10a6bd9ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60312706"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Copier les fichiers nouveaux et modifiés par LastModifiedDate avec Azure Data Factory

Cet article décrit un modèle de solution permettant de copier les fichiers nouveaux et modifiés par LastModifiedDate entre un magasin basé sur des fichiers et un magasin de destination. 

## <a name="about-this-solution-template"></a>À propos de ce modèle de solution

Ce modèle commence par sélectionner les fichiers nouveaux et modifiés en fonction de leur attribut **LastModifiedDate**, puis copie les fichiers sélectionnés entre le magasin de source de données et le magasin de destination des données.

Le modèle contient une activité :
- **Copier** pour copier les fichiers nouveaux et modifiés par LastModifiedDate entre un magasin de fichiers et un magasin de destination.

Le modèle définit quatre paramètres :
-  *FolderPath_Source* correspond au chemin d’accès du dossier où vous pouvez lire les fichiers à partir du magasin source. Vous devez remplacer la valeur par défaut par votre propre chemin d’accès au dossier.
-  *FolderPath_Destination* correspond au chemin d’accès du dossier où vous souhaitez copier les fichiers vers le magasin de destination. Vous devez remplacer la valeur par défaut par votre propre chemin d’accès au dossier.
-  *LastModified_From* est utilisé pour sélectionner les fichiers dont l’attribut LastModifiedDate est postérieur ou égal à cette valeur DateHeure.  Pour sélectionner uniquement les nouveaux fichiers (non copiés précédemment), définissez cette valeur DateHeure sur l'heure du dernier déclenchement du pipeline. Vous pouvez remplacer la valeur par défaut « 2019-02-01T00:00:00Z » par la valeur LastModifiedDate attendue dans le fuseau horaire UTC. 
-  *LastModified_To* est utilisé pour sélectionner les fichiers dont l’attribut LastModifiedDate est antérieur à cette valeur DateHeure. Pour sélectionner uniquement les nouveaux fichiers (non copiés précédemment), cette valeur DateHeure peut correspondre à l'heure actuelle.  Vous pouvez remplacer la valeur par défaut « 2019-02-01T00:00:00Z » par la valeur LastModifiedDate attendue dans le fuseau horaire UTC. 

## <a name="how-to-use-this-solution-template"></a>Utiliser ce modèle de solution

1. Accédez au modèle **Copier les nouveaux fichiers par LastModifiedDate uniquement**. Créez une **nouvelle** connexion à votre magasin de stockage source. C’est à partir de ce magasin que seront copiés les fichiers de votre choix.

    ![Créer une connexion à la source](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Pour commencer, sélectionnez le **Type** de stockage. Notez le **nom du compte** de stockage et la **clé du compte**. Pour finir, sélectionnez **Terminer**.

    ![Entrer une chaîne de connexion](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. Créez une **nouvelle connexion** au magasin de destination. C’est à partir de ce magasin que seront copiés les fichiers de votre choix. Vous devez également entrer les informations de connexion du magasin de destination des données, comme décrit à l’étape 2.

    ![Créer une connexion à la destination](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. Sélectionnez **Utiliser ce modèle**.

    ![Utiliser ce modèle](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. Le pipeline apparaît comme disponible dans le panneau, comme le montre l’exemple suivant :

    ![Afficher le pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. Sélectionnez **Déboguer**, entrez la valeur des **Paramètres** et sélectionnez **Terminer**.  Comme illustré ci-après, nous définissons les paramètres comme suit.
   - **FolderPath_Source** =  **/source/**
   - **FolderPath_Destination** =  **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     L’exemple indique les derniers fichiers modifiés dans un intervalle de temps compris entre *2019-02-01T00:00:00Z* et *2019-03-01T00:00:00Z* copiés entre un dossier */source/*  et un dossier */destination/* .  Vous pouvez remplacer ces valeurs par vos propres paramètres.
    
     ![Exécuter le pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. Vérifiez le résultat. Seuls les derniers fichiers modifiés dans l'intervalle de temps configuré ont été copiés dans le magasin de destination.

    ![Vérifier le résultat](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. Vous pouvez maintenant ajouter un déclencheur de fenêtres bascules pour automatiser ce pipeline et lui permettre de copier systématiquement les fichiers nouveaux et modifiés par LastModifiedDate et ce, de manière régulière.  Sélectionnez **Ajouter un déclencheur**, puis **Nouveau/Modifier**.

    ![Vérifier le résultat](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. Dans la fenêtre **Ajouter des déclencheurs**, sélectionnez **+ Nouveau**.

    ![Vérifier le résultat](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. Sélectionnez **Fenêtre bascule** pour le type de déclencheur, définissez **Toutes les 15 minutes** en tant que périodicité (vous pouvez opter pour l'intervalle de temps de votre choix), puis sélectionnez **Suivant**.

    ![Créer le déclencheur](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. Entrez la valeur correspondant aux **Paramètres d’exécution du déclencheur** comme suit, puis sélectionnez **Terminer**.
    - **FolderPath_Source** =  **/source/** .  Vous pouvez remplacer ce dossier par votre dossier dans le magasin de données sources.
    - **FolderPath_Destination** =  **/destination/** .  Vous pouvez remplacer ce dossier par votre dossier dans le magasin de données de destination.
    - **LastModified_From** =   **@trigger().outputs.windowStartTime**.  Cette variable système issue du déclencheur détermine l'heure du dernier déclenchement du pipeline.
    - **LastModified_To** =  **@trigger().outputs.windowEndTime**.  Cette variable système issue du déclencheur détermine l'heure du déclenchement du pipeline actuel.
    
    ![Paramètres d'entrée](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. Sélectionnez **Publier tout**.
    
    ![Publier tout](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. Créer des fichiers dans le dossier source du magasin source de données.  Vous attendez à présent que le pipeline se déclenche automatiquement, et seuls les nouveaux fichiers seront copiés dans le magasin de destination.

14. Sélectionnez l'onglet **Surveillance** dans le volet de navigation de gauche, et attendez environ 15 minutes si la périodicité du déclencheur a été définie sur Toutes les 15 minutes. 

    ![Sélectionnez Surveillance.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. Vérifiez le résultat. Votre pipeline sera déclenché automatiquement toutes les 15 minutes, et seuls les fichiers nouveaux et modifiés à partir du magasin source seront copiés vers le magasin de destination à chaque exécution du pipeline.

    ![Vérifier le résultat](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Étapes suivantes

- [Présentation du service Azure Data Factory](introduction.md)