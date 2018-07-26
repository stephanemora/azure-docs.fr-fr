---
title: Créer un classifieur avec le service Vision personnalisée - Azure Cognitive Services | Microsoft Docs
description: Découvrez comment utiliser le service Vision personnalisée pour créer un classifieur capable d’identifier des objets dans des photographies.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: c5183078d2f9d5eb16abef4f5df240f77eea6b8b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223367"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Comment créer un classifieur avec Custom Vision

Pour utiliser le service Vision personnalisée, vous devez en premier lieu créer un classifieur. Dans ce document, découvrez comment créer un classifieur par l’intermédiaire de votre navigateur Web.

## <a name="prerequisites"></a>Prérequis

Pour créer un classifieur, vous devez disposer des éléments suivants :

- Un [compte Microsoft](https://account.microsoft.com/account) ou un OrgID (ID d’organisation) Azure Active Directory (un « compte professionnel ou scolaire ») valide, afin de pouvoir vous connecter à customvision.ai et commencer.

    > [!IMPORTANT] 
    > La connexion de l’ID d’organisation des utilisateurs d’Azure Active Directory (Azure AD) à partir de [clouds nationaux](https://www.microsoft.com/en-us/trustcenter/cloudservices/nationalcloud) n’est pas prise en charge actuellement.

- Une série d’images pour entraîner votre classifieur (avec un minimum de 30 images par mot clé).

- Quelques images pour tester votre classifieur après son entraînement.

- Facultatif : un abonnement Azure associé à votre compte Microsoft ou votre OrgID. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

    > [!IMPORTANT]
    > Sans abonnement Azure, vous ne pouvez créer que des projets en __version d’évaluation limitée__. Si vous disposez d’un abonnement Azure, vous êtes à même, lors de la création du projet, de créer des ressources de formation et de prédiction pour le service Vision personnalisée dans le [portail Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision).   

## <a name="create-a-new-project"></a>Création d'un projet

Pour créer un projet, suivez ces étapes :

1. Dans votre navigateur Web, accédez à la [page web Custom Vision](https://customvision.ai). Sélectionnez __Sign in__ (Connexion) pour commencer à utiliser le service.

    ![Image de la page de connexion](./media/getting-started-build-a-classifier/custom-vision-web-ui.png)

    > [!NOTE]
    > Une fois connecté au service Vision personnalisée, une liste de projets vous est proposée. En dehors des deux projets en « version d’évaluation limitée » pour les tests, les projets sont associés à une ressource Azure. En tant qu’utilisateur Azure, vous voyez tous les projets associés aux [ressources Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#grant-access-to-resources) auxquelles vous avez accès. 

2. Pour créer votre premier projet, sélectionnez **New Project** (Nouveau projet). Pour votre premier projet, vous êtes invité à accepter les Conditions d’utilisation. Cochez la case, puis sélectionnez le bouton **I agree** (J’accepte). La boîte de dialogue **New project** (Nouveau projet) apparaît.

    ![La boîte de dialogue du nouveau projet comporte des champs permettant de renseigner le nom, la description et les domaines.](./media/getting-started-build-a-classifier/new-project.png)

3. Entrez un nom et une description pour le projet. Sélectionnez ensuite un des domaines disponibles. Chaque domaine optimise le classifieur pour des types spécifiques d’images, tels que décrits dans le tableau suivant :

    |Domaine|Objectif|
    |---|---|
    |__Générique__| Optimisé pour un large éventail de tâches de classification d’images. Si aucun autre domaine n’est approprié, ou si vous hésitez sur le choix du domaine, sélectionnez le domaine Generic (Général). |
    |__Food__ (Nourriture)|Optimisé pour les photographies de plats, tels que vous pouvez les voir dans un menu de restaurant. Si vous souhaitez classer des photographies de fruits ou de légumes distincts, utilisez le domaine Food (Nourriture).|
    |__Landmarks__ (Monuments et sites)|Optimisé pour les monuments et sites reconnaissables, naturels et artificiels. Ce domaine fonctionne mieux lorsque le monument ou le site est clairement visible dans la photographie. Ce domaine fonctionne même si le monument ou le site est légèrement masqué par des personnes placées devant lui.|
    |__Retail__ (Commerce)|Optimisé pour les images qui se trouvent dans des catalogues de vente ou sur des site Web commerciaux. Si vous souhaitez un classement de grande précision pour des robes, des pantalons et des chemises, utilisez ce domaine.|
    |__Adult__ (Adulte)|Optimisé pour mieux définir le contenu pour adultes et le contenu pour tous. Par exemple, si vous souhaitez bloquer les images de personnes en maillot de bain, ce domaine vous permet de créer un classifieur personnalisé pour ce faire.|
    |__Compact Domains__ (Domaines compacts)| Optimisés en fonction des contraintes de la classification en temps réel sur les appareils mobiles. Les modèles générés par les domaines compacts sont exportables pour s’exécuter localement.|

    Vous pouvez changer de domaine ultérieurement si vous le souhaitez.

4. Sélectionnez un groupe de ressources. La liste déroulante Resource Group (Groupe de ressources) vous montre tous vos groupes de ressources Azure qui comportent une ressource du service Vision personnalisée. Vous pouvez également sélectionner __limited trial__ (version d’évaluation limitée). L’entrée de la version d’évaluation limitée est le seul groupe de ressources qu’un utilisateur non-Azure peut choisir.

    Pour créer le projet, sélectionnez __Create project__ (Créer un projet).

## <a name="upload-and-tag-images"></a>Charger et étiqueter des images

1. Pour ajouter des images au classifieur, utilisez le bouton __Add images__ (Ajouter des images), puis sélectionnez __Browse local files__ (Parcourir les fichiers locaux). Sélectionnez __Open__ (Ouvrir) pour passer à l’étiquetage.

    > [!TIP]
    > Une fois les images sélectionnées, vous devez leur attribuer des mots clés. Le mot clé est appliqué au groupe d’images que vous avez sélectionné pour le chargement, ainsi il peut s’avérer plus facile de charger des images en fonction des mots clés que vous prévoyez d’utiliser. Vous pouvez également modifier le mot clé des images sélectionnées une fois qu’elles ont été étiquetées et chargées.

    > [!TIP]
    > Chargez des images prises selon différents angles, éclairages, arrière-plans, types, styles, groupes, tailles, etc. Utilisez divers types de photos pour vous assurer que votre classifieur n’est pas influencé et qu’il peut généraliser correctement.

    Le service Vision personnalisée accepte des images d’entraînement au format .jpg, .png et .bmp, jusqu’à 6 Mo par image. (La taille des images de prédiction peut atteindre 4 Mo par image.) Nous recommandons une dimension d’image de 256 pixels sur le plus petit bord. Les images inférieures à cette recommandation sont mises à l’échelle par le Service Vision personnalisée.

    ![Le contrôle d’ajout d’images est affiché dans le coin supérieur gauche, et sous la forme d’un bouton dans le bas de la page, au centre.](./media/getting-started-build-a-classifier/add-images01.png)

    >[!NOTE] 
    > L’API REST peut être utilisée pour charger des images d’entraînement à partir d’URL.

2. Pour définir le mot clé, entrez le texte voulu dans le champ __My Tags__ (Mes mots clés), puis utilisez le bouton __+__. Pour charger les images et les étiqueter, utilisez le bouton __Upload [number] files__ (Charger [nombre] fichiers). Vous pouvez ajouter plusieurs mots clés aux images. 

    > [!NOTE]
    > La durée de chargement varie selon le nombre et la taille des images que vous avez sélectionnées.

    ![Image de la page d’attribution des mots clés et du chargement](./media/getting-started-build-a-classifier/add-images03.png)

3. Sélectionnez __Terminé__ lorsque les images ont été chargés.

    ![La barre de progression indique que toutes les tâches sont terminées.](./media/getting-started-build-a-classifier/add-images04.png)

4. Pour charger un autre ensemble d’images, revenez à l’étape 1. Par exemple, si vous souhaitez faire la distinction entre des chiens et des poneys, chargez et étiquetez des images de poneys.

## <a name="train-and-evaluate-the-classifier"></a>Former et évaluer le classifieur

Pour former le classifieur, sélectionnez le bouton **Train** (Entraîner).

![Le bouton Train (Entraîner) se trouve en haut à droite dans la fenêtre du navigateur.](./media/getting-started-build-a-classifier/train01.png)

Quelques minutes seulement sont nécessaires pour entraîner le classifieur. Pendant ce temps, des informations sur le processus d’entraînement s’affichent.

![Le bouton Train (Entraîner) se trouve en haut à droite dans la fenêtre du navigateur.](./media/getting-started-build-a-classifier/train02.png)

Après l’entraînement, les __performances__ s’affichent. Les indicateurs de précision et de rappel vous renseignent sur la qualité de votre classifieur, d’après les tests automatiques réalisés. Le service Vision personnalisée utilise les images que vous avez soumises pour l’entraînement et calcule ces chiffres à l’aide d’un processus appelé la [validation croisée par blocs (« k-fold »)](https://en.wikipedia.org/wiki/Cross-validation_(statistics)).

![Les résultats de l’entraînement affichent les taux de précision et de rappel globaux, ainsi que ceux de chaque mot clé dans le classifieur.](./media/getting-started-build-a-classifier/train03.png)

> [!NOTE] 
> Chaque fois que vous sélectionnez le bouton **Train** (Entraîner), vous créez une nouvelle itération de votre classifieur. Vous pouvez afficher toutes vos anciennes itérations dans l’onglet **Performance** (Performances) et supprimer celles qui peuvent être obsolètes. Lorsque vous supprimez une itération, vous supprimez également toutes les images qui lui sont spécifiquement associées.

Le classifieur utilise toutes les images pour créer un modèle qui identifie chaque mot clé. Pour tester la qualité du modèle, le classifieur essaie chaque image sur le modèle pour voir ce que celui-ci trouve.

Les qualités des résultats du classifieur sont affichées.

|Terme|Définition|
|---|---|
|__Précision__|Lorsque vous classez une image, quelle est la probabilité que votre classifieur classe correctement cette image ? Sur toutes les images qui sont utilisées pour entraîner le classifieur (les chiens et les poneys), quel taux de réussite le modèle a-t-il obtenu ? Sur 100 images, 99 mots clés corrects donnent une précision de 99 %.|
|__Recall__ (Rappel)|Sur toutes les images qui auraient dû être correctement classées, combien votre classifieur en a-t-il identifié convenablement ? Un rappel de 100 % signifie que s’il existe 38 images de chiens dans les images utilisées pour entraîner le classifieur, celui-ci a trouvé 38 chiens.|

## <a name="next-steps"></a>Étapes suivantes

[Tester et ré-entraîner le modèle](test-your-model.md)

