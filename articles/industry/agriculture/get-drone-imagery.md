---
title: Obtenir une imagerie par drone
description: Décrit comment obtenir une imagerie par drone auprès des partenaires
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 598248a0efb3322a9c22a5e38e4986f5ba5142ab
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797466"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Obtenir une imagerie par drone auprès des partenaires de drone

Cet article explique comment récupérer, dans un hub de données Azure FarmBeats, des données orthophotographiques de partenaires d’imagerie par drone. Actuellement, les partenaires d’imagerie suivants sont pris en charge :  

  ![Projet FarmBeats](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

L’intégration de données d’imagerie par drone à Azure FarmBeats vous permet de récupérer, dans le hub de données, des données orthophotographiques à partir des vols de drone réalisés sur votre ferme. Une fois les données disponibles, vous pouvez les voir dans FarmBeats Accelerator et les utiliser pour la fusion de données et la création de modèles IA/ML.

## <a name="before-you-begin"></a>Avant de commencer

  - Assurez-vous que vous avez déployé Azure FarmBeats. Pour effectuer le déploiement, consultez [Déployer FarmBeats](prepare-for-deployment.md).
  - Vérifiez que la ferme (pour laquelle vous souhaitez disposer de l’imagerie par drone) est définie dans votre système FarmBeats.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Activer l’intégration de l’imagerie par drone à FarmBeats   

Vous devez fournir les informations suivantes au fournisseur d’appareils pour activer l’intégration à FarmBeats :  
 - Point de terminaison d’API  
 - ID client  
 - ID client  
 - Clé secrète client  

Procédez comme suit :

1. Téléchargez ce [script](https://aka.ms/farmbeatspartnerscript) et extrayez-le sur votre lecteur local. Vous trouverez deux fichiers dans ce fichier ZIP.  
2. Connectez-vous au [portail Azure](https://portal.azure.com/) et ouvrez Cloud Shell (cette option est disponible dans la barre supérieure droite du portail).   

    ![Projet FarmBeats](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Vérifiez que l’environnement est défini sur **PowerShell**.

    ![Projet FarmBeats](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Chargez les deux fichiers que vous avez téléchargés (à l’étape 1 ci-dessus) dans Cloud Shell.  

    ![Projet FarmBeats](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Accédez au répertoire où les fichiers ont été chargés. (Par défaut, le chargement s’effectue dans le répertoire de base > nom d’utilisateur.)  
6. Exécutez le script qui suit :

    ```azurepowershell-interactive 

    PS> ./generateCredentials.ps1   

    ```

7. Suivez les instructions à l’écran pour obtenir les valeurs suivantes : point de terminaison d’API, ID de locataire, ID client, secret client et chaîne de connexion du hub d’événements.

    Après avoir entré les informations d’identification requises dans le système logiciel du drone du partenaire, vous pouvez importer toutes les fermes à partir du système FarmBeats et utiliser les informations sur la ferme pour planifier la trajectoire de vol et collecter les images du drone.

    Une fois les images brutes traitées par le logiciel du fournisseur de drone, le système logiciel du drone charge les orthophotographies assemblées et d’autres images traitées dans le hub de données.

## <a name="view-drone-imagery"></a>Voir l’imagerie par drone

Une fois les données envoyées au hub de données FarmBeats, vous devriez être en mesure d’interroger le magasin de scènes à l’aide des API du hub de données FarmBeats.

Vous devriez également être en mesure de voir la dernière image de drone dans la page **Farm Details**. Pour cela, effectuez les étapes ci-dessous :  

1. Sélectionnez la ferme vers laquelle l’imagerie a été chargée. La page **Farm Details** s’affiche.
2. Faites défiler la page vers le bas jusqu’à la dernière section **Precision Maps** (Cartes de précision).
3. Vous devriez voir l’image dans la section **Drone Imagery**.

    ![Projet FarmBeats](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Télécharger l’imagerie par drone

Quand vous sélectionnez la section Drone Imagery, une fenêtre indépendante s’ouvre avec une image haute résolution de l’orthophotographie du drone.

![Projet FarmBeats](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Voir toutes les cartes de drone

Les fichiers et images chargés par le fournisseur de drone s’affichent dans la section Maps. Sélectionnez la section **Maps**, filtrez sur le critère **Farm** et choisissez les fichiers à afficher et à télécharger :

  ![Projet FarmBeats](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser les [API](references-for-farmbeats.md#rest-api) du hub de données FarmBeats pour obtenir l’imagerie par drone.
