---
title: Utiliser le service web dans Excel
titleSuffix: Azure Machine Learning Studio
description: Microsoft Azure Machine Learning Studio permet d’appeler facilement des services web directement à partir de Microsoft Excel sans qu’il soit nécessaire d’écrire du code.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: ef1d8f1a72c5936ff661636c4c51acf439a0a5ea
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855195"
---
# <a name="consuming-an-azure-machine-learning-studio-web-service-from-excel"></a>Utilisation d’un service web Azure Machine Learning Studio depuis Excel

 Microsoft Azure Machine Learning Studio permet d’appeler facilement des services web directement à partir de Microsoft Excel sans qu’il soit nécessaire d’écrire du code.

Si vous utilisez Excel 2013 (ou une version ultérieure) ou Excel Online, nous vous recommandons d’utiliser la [macro complémentaire Excel](excel-add-in-for-web-services.md).



## <a name="steps"></a>Étapes
Publiez un service web. Le [Tutoriel 3 : Déployer un modèle de risque de crédit](tutorial-part3-credit-risk-deploy.md) explique la procédure à suivre. Actuellement, la fonctionnalité de classeur Excel est uniquement prise en charge pour les services de requête/réponse qui produisent une seule sortie (autrement dit, une étiquette de notation unique). 

Quand vous disposez d’un service web, cliquez sur la section **WEB SERVICES** située sur la partie gauche de Microsoft Azure Machine Learning Studio, puis sélectionnez le service web à utiliser à partir de Microsoft Excel.

**Service web classique**

1. Sur l’onglet **TABLEAU DE BORD** du service web figure une ligne pour le service **REQUÊTE/RÉPONSE**. Si ce service produit une sortie unique, le lien **Télécharger un classeur Excel** doit apparaître sur cette ligne.

    ![Télécharger le classeur Excel à l’aide du portail de Service Web de Studio](./media/consuming-from-excel/excellink.png)
2. Cliquez sur **Télécharger un classeur Excel**.

**Nouveau service web**

1. Dans le portail Service web Azure Machine Learning, sélectionnez **Consommer**.
2. Dans la page Consommer, dans les **options de consommation de service Web** cliquez sur l’icône Excel.

**Utilisation du classeur**

1. Ouvrez le classeur.
2. Un avertissement de sécurité s’affiche. Cliquez sur le bouton **Activer la modification**.

    ![Activer la modification supprimer l’avertissement de sécurité de mode protégé](./media/consuming-from-excel/enableeditting.png)
3. Un avertissement de sécurité apparaît. Cliquez sur le bouton **Activer le contenu** pour pouvoir exécuter des macros sur votre feuille de calcul.

    ![Activer le contenu ignorer l’avertissement de sécurité que la désactivation des macros](./media/consuming-from-excel/enablecontent.png)
4. Une fois les macros activées, une table est générée. Les valeurs des colonnes en bleu sont requises en tant qu’entrées dans le service web RRS (Request/Response Service), ou en tant que **PARAMÈTRES**. Notez la sortie du service RRS, appelée **VALEURS PRÉDITES** et affichée en vert. Lorsque toutes les colonnes d’une ligne donnée sont remplies, le classeur appelle automatiquement l’API de notation et affiche les notes résultantes.

    ![Table pour les entrées de paramètre et le résultat prédit des valeurs](./media/consuming-from-excel/sampletable.png)
5. Pour noter plusieurs lignes, remplissez la deuxième ligne avec les données ; des valeurs prédites sont produites. Vous pouvez même coller plusieurs lignes à la fois.

Vous pouvez utiliser toutes les fonctionnalités de Microsoft Excel (graphiques, Power Map, mise en forme conditionnelle, etc.) avec les valeurs prédites pour visualiser les données.

## <a name="sharing-your-workbook"></a>Partage de votre classeur
Pour que les macros fonctionnent, votre Clé API doit faire partie de la feuille de calcul. Cela signifie que vous devez uniquement partager le classeur avec des entités/personnes de confiance.

## <a name="automatic-updates"></a>Mises à jour automatiques
Un appel RRS est initié dans les deux cas suivants :

1. la première fois que du contenu apparaît dans l’ensemble des **PARAMÈTRES**
2. chaque fois que l’un ou l’autre des **PARAMÈTRES** change dans une ligne dont l’ensemble des **PARAMÈTRES** a été entré.