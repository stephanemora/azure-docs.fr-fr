---
title: Fichier Include
description: Fichier Include
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75535713"
---
1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com).

1. Mettez à niveau votre espace de travail vers l’édition Enterprise.

    Après la mise à niveau, toutes vos expériences d’interface visuelle seront converties en brouillons de pipeline dans le concepteur.
    
    > [!NOTE]
    > Vous n’avez pas besoin de mettre à niveau vers l’édition Enterprise pour convertir les services web de l’interface visuelle en points de terminaison en temps réel.
    
1. Accédez à la section Concepteur de l’espace de travail pour afficher votre liste de brouillons de pipeline. 
    
    Les services web convertis sont accessibles en accédant à **Points de terminaison** > **Points de terminaison en temps réel**.

1. Sélectionnez un brouillon de pipeline pour l’ouvrir.

    Si une erreur s’est produite pendant le processus de conversion, un message d’erreur s’affiche avec des instructions pour résoudre le problème. 

### <a name="known-issues"></a>Problèmes connus

 Vous trouverez ci-dessous des problèmes de migration connus qui doivent être résolus manuellement :

- Modules **Importer des données** ou **Exporter des données**
        
    Si vous disposez d’un module **Importer des données** ou **Exporter des données** dans l’expérience, vous devez mettre à jour la source de données pour utiliser des magasins de données. Pour savoir comment créer un magasin de données, consultez [Comment accéder aux données dans les services de stockage Azure](../articles/machine-learning/how-to-access-data.md). Les informations de votre compte de stockage cloud ont été ajoutées aux commentaires du module **Importer des données** ou **Exporter des données** pour plus de commodité. 
      