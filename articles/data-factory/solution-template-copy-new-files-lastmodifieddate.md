---
title: Copiez les fichiers nouveaux et modifiés par LastModifiedDate & gt ; avec Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser un modèle de solution pour copier les fichiers nouveaux et modifiés par LastModifiedDate & gt ; avec Azure Data Factory.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312706"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Copiez les fichiers nouveaux et modifiés par LastModifiedDate & gt ; avec Azure Data Factory

Cet article décrit un modèle de solution que vous pouvez utiliser pour copier des fichiers nouveaux et modifiés uniquement par LastModifiedDate & gt ; à partir d’un magasin basé sur le fichier vers un magasin de destination. 

## <a name="about-this-solution-template"></a>À propos de ce modèle de solution

Ce modèle sélectionne tout d’abord les fichiers nouveaux et modifiés uniquement par leurs attributs **LastModifiedDate & lt ;**, puis copie les fichiers sélectionnés à partir du magasin de source de données dans le magasin de destination de données.

Le modèle contient une seule activité :
- **Copie** pour copier les fichiers nouveaux et modifiés uniquement par LastModifiedDate & gt ; à partir d’un magasin de fichiers vers un magasin de destination.

Le modèle définit quatre paramètres :
-  *FolderPath_Source* est le chemin d’accès du dossier où vous pouvez lire les fichiers à partir du magasin source. Vous devez remplacer la valeur par défaut avec votre propre chemin d’accès du dossier.
-  *FolderPath_Destination* est le chemin d’accès du dossier où vous souhaitez copier les fichiers dans le magasin de destination. Vous devez remplacer la valeur par défaut avec votre propre chemin d’accès du dossier.
-  *LastModified_From* est utilisé pour sélectionner les fichiers dont l’attribut LastModifiedDate & gt ; est après ou égal à cette valeur datetime.  Afin de sélectionner les nouveaux fichiers uniquement, ce qui n’a pas été copié dernière fois, cette valeur de date/heure peut être le temps lorsque le pipeline a été déclenché heure de la dernière. Vous pouvez remplacer la valeur par défaut ' 2019-02-01T00:00:00Z » à vos LastModifiedDate attendu dans le fuseau horaire UTC. 
-  *LastModified_To* est utilisé pour sélectionner les fichiers dont l’attribut LastModifiedDate & gt ; est avant cette valeur datetime. Afin de sélectionner les nouveaux fichiers uniquement, ce qui n’a pas été copié dernière fois, cette valeur de date/heure peut être l’heure actuelle.  Vous pouvez remplacer la valeur par défaut ' 2019-02-01T00:00:00Z » à vos LastModifiedDate attendu dans le fuseau horaire UTC. 

## <a name="how-to-use-this-solution-template"></a>Utiliser ce modèle de solution

1. Accédez au modèle **copier les nouveaux fichiers uniquement par LastModifiedDate & lt ;**. Créer un **New** connexion à votre magasin de stockage source. Le magasin de stockage source est où vous souhaitez copier les fichiers.

    ![Créer une connexion à la source](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Tout d’abord sélectionner le stockage **Type**. Une fois que l’entrée du stockage **nom du compte** et **clé de compte**. Enfin, sélectionnez **Terminer**.

    ![Entrez une chaîne de connexion](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. Créer un **New** connexion à votre magasin de destination. Le magasin de destination est où vous souhaitez copier les fichiers. Vous devez également entrer les informations de connexion de banque de destination des données similaire comme à l’étape 2.

    ![Créer une connexion à la destination](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. Sélectionnez **utiliser ce modèle**.

    ![Utiliser ce modèle](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. Le pipeline apparaît comme disponible dans le panneau, comme le montre l’exemple suivant :

    ![Afficher le pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. Sélectionnez **déboguer**, écrire la valeur pour le **paramètres** et sélectionnez **Terminer**.  Dans l’image ci-dessous, nous définissons les paramètres comme suit.
   - **FolderPath_Source** = **/source/**
   - **FolderPath_Destination** = **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     L’exemple est indiquant les fichiers qui ont été modifiées dernière au sein de l’intervalle de temps entre *2019-02-01T00:00:00Z* et *2019-03-01T00:00:00Z* seront copiés à partir d’un dossier */source/*  dans un dossier */destination/*.  Vous pouvez remplacer par vos propres paramètres.
    
     ![Exécuter le pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. Vérifiez le résultat. Vous verrez uniquement les fichiers de dernière modifiés au sein de la configuration intervalle de temps a été copié dans le magasin de destination.

    ![Vérifier le résultat](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. Vous pouvez maintenant ajouter un déclencheur de windows bascule pour automatiser ce pipeline, afin que le pipeline peut toujours copier régulièrement les fichiers nouveaux et modifiés uniquement par LastModifiedDate & gt ;.  Sélectionnez **ajouter un déclencheur**, puis sélectionnez **nouveau/modifier**.

    ![Vérifier le résultat](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. Dans la fenêtre **Ajouter des déclencheurs**, sélectionnez **+ Nouveau**.

    ![Vérifier le résultat](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. Sélectionnez **fenêtre bascule** pour le type de déclencheur, définissez **toutes les 15 minutes** en tant que la périodicité (vous pouvez modifier à n’importe quel intervalle de temps), puis sélectionnez **suivant**.

    ![Créer le déclencheur](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. Écrire la valeur pour le **paramètres d’exécution du déclencheur** comme suit, puis sélectionnez **Terminer**.
    - **FolderPath_Source** = **/source/**.  Vous pouvez le remplacer par votre dossier dans le magasin de données source.
    - **FolderPath_Destination** = **/destination/**.  Vous pouvez le remplacer par votre dossier dans le magasin de données de destination.
    - **LastModified_From** =  **@trigger().outputs.windowStartTime**.  Il est une variable système à partir du déclencheur de déterminer le temps lorsque le pipeline a été déclenché dernière fois.
    - **LastModified_To** = **@trigger().outputs.windowEndTime**.  Il est une variable système à partir du déclencheur de déterminer le temps lorsque le pipeline est déclenché pour cette fois.
    
    ![Paramètres d'entrée](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. Sélectionnez **Publier tout**.
    
    ![Publier tout](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. Créer des fichiers dans votre dossier source du magasin de source de données.  Vous attendent du pipeline déclenchées automatiquement, et uniquement les nouveaux fichiers seront copiés dans le magasin de destination.

14. Sélectionnez **surveillance** onglet dans le volet de navigation de gauche et attendez environ 15 minutes si la périodicité du déclencheur a été définie sur toutes les 15 minutes. 

    ![Sélectionnez la surveillance](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. Vérifiez le résultat. Vous verrez votre pipeline est déclenché automatiquement toutes les 15 minutes, et uniquement les fichiers nouveaux ou modifiés à partir de la banque source seront copiés dans le magasin de destination dans chaque exécution du pipeline.

    ![Vérifier le résultat](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Étapes suivantes

- [Présentation du service Azure Data Factory](introduction.md)