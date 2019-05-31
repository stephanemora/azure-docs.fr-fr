---
title: Déploiements de langage non pris en charge - personnalisé Translator
titleSuffix: Azure Cognitive Services
description: Comment déployer des paires de langages non pris en charge dans les convertisseur personnalisé.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: v-pawal
ms.openlocfilehash: 0938ba2e839be603c557cc2a87dd0c5aa1dfe1e3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390583"
---
# <a name="unsupported-language-deployments"></a>Déploiements de langages non pris en charge

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Avec la mise hors service à venir de Microsoft Translator Hub, Microsoft sera être annulation du déploiement de tous les modèles actuellement déployées via le concentrateur. La plupart d'entre vous ont modèles déployés dans le Hub dont paires de langues ne sont pas pris en charge dans personnalisé Translator.  Nous ne souhaitez pas que les utilisateurs dans ce cas de n’avoir aucun recours pour la traduction de leur contenu.

Nous disposons désormais d’un processus qui vous permet de déployer vos modèles non pris en charge via le traducteur personnalisé.  Ce processus vous permet de continuer à traduire le contenu à l’aide de la dernière API V3.  Ces modèles seront hébergées jusqu'à ce que vous choisissez d’annuler leur déploiement ou la paire de langues est disponible dans le traducteur de personnalisé.  Cet article explique le processus pour déployer des modèles avec des paires de langages non pris en charge.

## <a name="prerequisites"></a>Conditions préalables

Dans l’ordre pour vos modèles candidats pour le déploiement, ils doivent respecter les critères suivants :
* Le projet contenant le modèle doit ont été migré à partir du concentrateur pour le convertisseur personnalisé à l’aide de l’outil de Migration.  Vous pouvez trouver le processus de migration des projets et des espaces de travail [ici](how-to-migrate.md).
* Le modèle doit être dans un état déployé lors de la migration effectuée.  
* La paire de langage du modèle doit être une paire de langage non pris en charge dans personnalisé Translator.  Paires de langues dans laquelle une langue est prise en charge vers ou à partir de l’anglais, mais la paire lui-même n’inclut pas l’anglais, sont des candidats pour les déploiements de langage non pris en charge.  Par exemple, un modèle de Hub pour un Français à une paire de langue allemande est considéré comme une paire de langage pris en charge cependant Français pour l’anglais et l’anglais vers allemand sont même langage non pris en charge paire.

## <a name="process"></a>Process
Une fois que vous avez migré des modèles à partir du Hub sont des candidats pour le déploiement, vous les trouverez en accédant à la **paramètres** page de votre espace de travail et faire défiler vers la fin de la page où vous verrez une **non pris en charge Formations de Hub Translator** section.  Cette section s’affiche uniquement si vous avez des projets qui remplissent les conditions préalables mentionnées ci-dessus.

![Migrer à partir du hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Dans le **formations de Hub non pris en charge Translator** page de sélection, le **non sollicité formations** onglet contient les modèles qui sont éligibles pour le déploiement.  Sélectionnez les modèles que vous souhaitez déployer et d’envoyer une demande.   Avant l’échéance du déploiement du 30 avril, vous pouvez sélectionner autant de modèles comme vous le souhaitez pour le déploiement.
 
![Migrer à partir du hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Une fois envoyée, le modèle ne sera plus disponible sur le **non sollicité formations** onglet et à la place apparaîtra sur la **demandé formations** onglet.  Vous pouvez afficher vos formations demandées à tout moment.

![Migrer à partir du hub](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Et ensuite ?

Les modèles que vous avez sélectionné pour le déploiement sont enregistrés une fois que le Hub est hors service et tous les modèles sont annulés.  Vous avez jusqu’au 24 mai pour soumettre des demandes pour le déploiement de modèles non pris en charge.  Nous allons déployer ces modèles, du 15 juin, à quel point ils sont accessibles via l’API de V3 Translator.  En outre, ils seront disponibles via l’API V2 jusqu’au 1er juillet.  

Pour plus d’informations sur les dates importantes dans la dépréciation de la vérification de Hub [ici](https://www.microsoft.com/translator/business/hub/).
Des frais d’hébergement une fois déployés, normales s’appliquent.  Consultez [tarification](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) pour plus d’informations.  

Contrairement aux modèles de traducteur de personnalisé standards, les modèles de Hub sera uniquement disponibles dans une seule région, afin de frais d’hébergement dans plusieurs régions ne s’applique pas.  Une fois déployé, vous ne pourrez pas annuler le déploiement de votre modèle de Hub à tout moment via le projet personnalisé Translator migré.

## <a name="next-steps"></a>Étapes suivantes

- [Effectuer l’apprentissage d’un modèle](how-to-train-model.md).
- Commencez à utiliser votre modèle Custom Translator déployé via [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
