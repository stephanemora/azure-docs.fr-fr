---
title: Former un modèle pour la reconnaissance vocale personnalisée - Services de reconnaissance vocale
titlesuffix: Azure Cognitive Services
description: Un-à-texte vocal d’apprentissage est nécessaire pour améliorer la précision de la reconnaissance de modèle de ligne de base de Microsoft ou un modèle personnalisé que vous avez l’intention de créer. Un modèle est formé à l’aide de transcriptions étiquetés humaines et le texte associé. Ces jeux de données, ainsi que les données audio téléchargées précédemment, sont utilisés pour affiner et former le modèle de la parole-texte à reconnaître les mots, les expressions, les acronymes, les noms et les termes spécifiques au produit.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 194ae477bb3cba4ac7e3350da6b793c6fea6ecdb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025856"
---
# <a name="train-a-model-for-custom-speech"></a>Former un modèle pour la reconnaissance vocale personnalisé

Un-à-texte vocal d’apprentissage est nécessaire pour améliorer la précision de la reconnaissance de modèle de ligne de base de Microsoft ou un modèle personnalisé que vous avez l’intention de créer. Un modèle est formé à l’aide de transcriptions étiquetés humaines et le texte associé. Ces jeux de données, ainsi que les données audio téléchargées précédemment, sont utilisés pour affiner et former le modèle de la parole-texte à reconnaître les mots, les expressions, les acronymes, les noms et les termes spécifiques au produit. Le plus dans le domaine jeux de données que vous fournissez (données qui sont associées à ce que les utilisateurs lira et ce que vous attendez à reconnaître), le plus précis sera de votre modèle, ce qui entraîne la reconnaissance améliorée. N’oubliez pas, en acheminant les données non liées dans votre formation, vous pouvez réduire ou nuisent à la précision de votre modèle.

## <a name="use-training-to-resolve-accuracy-issues"></a>Utiliser l’apprentissage pour résoudre les problèmes de précision

Si vous rencontriez quelques problèmes de reconnaissance avec votre modèle, à l’aide de la légende humaines relevés de notes et des données associées pour une formation supplémentaire peuvent aider à pour améliorer la précision. Cette table permet de déterminer le dataset à utiliser pour résoudre votre problème (s) :

| Cas d’utilisation | Type de données | Quantité de données |
|----------|-----------|---------------|
| Noms propres posent | Associer le texte (phrases/énoncés) | 10 Mo à 500 Mo |
| Mots posent en raison d’un non-respect des accents | Texte associé (prononciation) | Fournir les mots |
| Les mots communs sont supprimés ou pas été correctement reconnues | Transcriptions audio + étiquetés humaines | heures de transcription de 10 à 1 000 |

> [!IMPORTANT]
> Si vous n’avez pas chargé un jeu de données, consultez l’article [préparer et tester vos données](how-to-custom-speech-test-data.md). Ce document fournit des instructions pour le chargement des données et des instructions pour la création de jeux de données de haute qualité.

## <a name="train-and-evaluate-a-model"></a>Former et évaluer un modèle

La première étape pour former un modèle consiste à charger les données d’apprentissage. Utilisez [préparer et tester vos données](how-to-custom-speech-test-data.md) pour obtenir des instructions pas à pas préparer les transcriptions étiquetés humaines et texte connexe (énoncés et prononciations). Une fois que vous avez chargé les données d’apprentissage, suivez ces instructions pour commencer l’apprentissage de votre modèle :

1. Accédez à **parole-texte > vocal personnalisé > Formation**.
2. Cliquez sur **former le modèle**.
3. Ensuite, donnez à votre formation un **nom** et **Description**.
4. À partir de la **scénario et la base de référence de modèle** menu déroulant, sélectionnez le scénario qui convient le mieux à votre domaine. Si vous ne savez pas quel scénario à choisir, sélectionnez **général**. Le modèle de ligne de base est le point de départ pour l’apprentissage. Si vous n’avez pas une préférence, vous pouvez utiliser la dernière version.
5. À partir de la **sélectionner les données d’apprentissage** page, choisissez un ou plusieurs datasets de transcription audio + humaines en partie que vous souhaitez utiliser pour l’apprentissage.
6. Une fois la formation terminée, vous pouvez choisir d’effectuer le test sur le modèle nouvellement formé de la précision. Cette étape est facultative.
7. Sélectionnez **créer** pour générer votre modèle personnalisé.

Le tableau de formation affiche une nouvelle entrée qui correspond à ce nouveau modèle. Le tableau affiche également l’état :  Traitement, réussite, échec.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Évaluer la précision d’un modèle formé

Vous pouvez inspecter les données et évaluer la précision du modèle à l’aide de ces documents :

* [Inspectez vos données](how-to-custom-speech-inspect-data.md)
* [Évaluer vos données](how-to-custom-speech-evaluate-data.md)


Si vous avez choisi tester la précision, il est important de sélectionner un jeu de données acoustique qui est différent de celui que vous avez utilisé avec votre modèle pour faire une idée réaliste de performances de modèle.

## <a name="next-steps"></a>Étapes suivantes

* [Déployer votre modèle](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Préparer et tester vos données](how-to-custom-speech-test-data.md)
* [Inspectez vos données](how-to-custom-speech-inspect-data.md)
* [Évaluer vos données](how-to-custom-speech-evaluate-data.md)
* [Formation d’un modèle](how-to-custom-speech-train-model.md)
