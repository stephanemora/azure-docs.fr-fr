---
title: Glossaire des termes du service Vision personnalisée - Azure Cognitive Services | Microsoft Docs
description: Glossaire des termes du service Vision personnalisée.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/08/2017
ms.author: anroth
ms.openlocfilehash: 871617ce3c1c5a84df746c0c7d87c113b3a6f354
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368845"
---
# <a name="glossary-of-terms-for-custom-vision-service"></a>Glossaire des termes du service Vision personnalisée

Voici quelques termes utilisés dans le service Vision personnalisée et leur signification.

## <a name="classifier"></a>Classifieur

Un classifieur est un modèle que vous créez à l’aide du service Vision personnalisée, en utilisant quelques images d’entraînement. Lorsque vous avez terminé l’entraînement d’un classifieur, vous recevez un point de terminaison d’évaluation (HTTPS) à ajouter à votre application. Tous les classifieurs que vous générez se trouvent dans leur propre projet, et vous pouvez afficher tous les projets une fois que vous êtes connecté.

## <a name="domain"></a>Domaine

Lorsque vous créez un projet, vous sélectionnez un « domaine » pour ce projet. Le domaine optimise un classifieur pour un type spécifique d’objet présent dans vos images. Par exemple, si votre scénario consiste à classer des images de tartes aux pommes et des images de gâteaux à la carotte, choisissez le domaine « Food » (Nourriture). Si vous hésitez sur le choix du domaine, sélectionnez le domaine « Generic » (Général).

- **Le domaine Food (Nourriture).** Optimisé pour les plats que vous pouvez voir dans un menu de restaurant. Il n’a pas été optimisé pour l’identification des fruits ou des légumes séparément. Si vous souhaitez classer des photographies de fruits ou de légumes distincts, utilisez le domaine Generic (Général) pour ce faire.
- **Le domaine Landmark (Monuments et sites).** Optimisé pour les monuments et sites reconnaissables, naturels et artificiels. Ce domaine fonctionne mieux lorsque le monument ou le site est clairement visible dans la photographie, même s’il est légèrement masqué par un groupe de personnes posant devant lui.
- **Le domaine Retail (Commerce).** Optimisé pour la classification d’images dans un catalogue de vente ou sur un site web commercial. Si vous souhaitez une grande précision lors de la classification de robes, de pantalons, de chemises, etc., utilisez le domaine Retail (Commerce).
- **Le domaine Adult (Pour adultes).** Optimisé pour mieux définir le contenu pour adultes par rapport au contenu pour tous. Par exemple, si vous souhaitez bloquer les images de personnes en maillot de bain, ce domaine vous permet de créer un classifieur personnalisé pour ce faire.
- **Le domaine Generic (Général).** Particulièrement adapté à un large éventail de tâches de classification d’images.

Les modèles générés par des **domaines compacts** sont exportables avec la fonction d’exportation d’itérations. Ils sont optimisés en fonction des contraintes de la classification en temps réel sur les appareils mobiles. Pour une même quantité de données d’entraînement, les classifieurs conçus avec un domaine compact sont parfois légèrement moins précis qu’avec un domaine standard. Leur avantage réside dans le fait qu’ils sont suffisamment petits pour être exécutés localement, pratiquement en temps réel. 

## <a name="training-image"></a>Image d’entraînement

Pour créer un classifieur de grande précision, le service Vision personnalisée a besoin de plusieurs images d’entraînement. Une image d’entraînement est une photo de l’image que vous souhaitez que le service Vision personnalisée classe. Par exemple, pour classer des oranges, vous devez charger plusieurs images d’oranges sur le service Vision personnalisée, afin qu’il puisse créer un classifieur capable de reconnaître des oranges. Nous recommandons le chiffre d’au moins 30 images par mot clé.

## <a name="iteration"></a>Itération

Chaque fois que vous entraînez ou ré-entraînez votre classifieur, vous créez une itération de votre modèle. Nous conservons plusieurs itérations passées afin de vous permettre de mesurer votre progression au fil du temps. Vous pouvez supprimer les itérations qui ne vous sont plus utiles. N’oubliez pas que la suppression d’une itération est définitive, et que vous supprimez avec elle toutes les images ou modifications qui lui étaient propres. 

## <a name="workspace"></a>Espace de travail

Votre espace de travail contient toutes vos images d’entraînement, et reflète toutes les modifications effectuées à partir de votre itération la plus récente, telles que des images supprimées ou ajoutées. Lorsque vous entraînez votre classifieur, vous créez une itération de votre classifieur en utilisant les images présentes dans votre espace de travail.

## <a name="tags"></a>Mots clés

Utilisez des mots clés pour étiqueter les objets présents dans vos images d’entraînement. Si vous créez un classifieur pour identifier des chiens et des poneys, vous devez placer un mot clé « chien » sur les images qui contiennent des chiens, un mot clé « poney » sur les images qui contiennent des poneys, et deux mots clés, « chien » et « poney », sur les images qui contiennent à la fois un chien et un poney.

## <a name="evaluation"></a>Évaluation

Une fois votre classifieur entraîné, vous pouvez soumettre n’importe quelle image d’évaluation à l’aide du point de terminaison https généré automatiquement. Le classifieur retourne un jeu de mots clés prédits, dans l’ordre de certitude.

## <a name="predictions"></a>Prédictions

À fur et à mesure que votre classifieur accepte de nouvelles images à classer, il les stocke pour vous. Vous pouvez utiliser ces images pour améliorer la précision de votre classifieur, en étiquetant convenablement les images mal prédites. Vous pouvez ensuite utiliser ces nouvelles images pour ré-entraîner votre classifieur.

## <a name="precision"></a>Précision

Lorsque vous classez une image, quelle est la probabilité que votre classifieur classe correctement cette image ? Sur toutes les images utilisées pour entraîner le classifieur (les chiens et les poneys), quel taux de réussite le modèle a-t-il obtenu ? Sur 100 images, 99 mots clés corrects donnent une précision de 99 %.

## <a name="recall"></a>Rappel

Sur toutes les images qui auraient dû être correctement classées, combien votre classifieur en a-t-il identifié convenablement ? Un rappel de 100 % signifie que s’il existait 38 images de chiens dans les images utilisées pour entraîner le classifieur, 38 chiens ont été trouvés par le classifieur.

## <a name="settings"></a>Paramètres

Il existe deux types de paramètres, les paramètres situés au niveau du projet et ceux qui sont situés au niveau de l’utilisateur.

- Paramètres au niveau du projet : 
  
  Les paramètres au niveau du projet s’appliquent à un projet ou à un classifieur. À savoir :

   - Domaine du projet
   - Nom du projet
   - Description du projet
   - Usage :
      - Nombre d’images d’entraînement
      - Nombre de mots clés créés
      - Nombre d’itérations créées

- Paramètres au niveau de l’utilisateur : 
   - Clés d’abonnement : une pour l’entraînement, l’autre pour l’évaluation/la prédiction.
   - Usage :
      - Nombre de projets créés
      - Nombre d’appels de l’API d’évaluation/de prédiction effectués.
