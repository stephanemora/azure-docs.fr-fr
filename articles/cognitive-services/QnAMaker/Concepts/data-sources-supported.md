---
title: Sources de données prises en charge - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Sources de données prises en charge
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 698f96b15a9387cd30d26e684ed03ff4cc3346a7
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697898"
---
# <a name="data-sources"></a>Sources de données 
QnA Maker peut extraire automatiquement des paires de question-réponse des formats de contenu semi-structuré courants, par exemple des foires aux questions et des manuels de produit. Le contenu peut également être ajouté à la base de connaissances à partir de fichiers structurés.

## <a name="plain-faq-pages"></a>Pages de FAQ simples
Il s’agit du type le plus courant de page de FAQ, dans lequel les réponses suivent immédiatement les questions dans la même page. 

![Page de FAQ simple](../media/qnamaker-concepts-datasources/plain-faq.png) 

 

## <a name="faq-pages-with-section-links"></a>Pages de FAQ avec liens de section 
Dans ce type de page de FAQ, les questions sont regroupées puis associées aux réponses, lesquelles se trouvent dans différentes sections sur la même page.

 ![Page de FAQ avec lien de section](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


## <a name="faq-pages-with-links-to-different-pages"></a>Pages de FAQ avec liens vers différentes pages 
Ce type de page de FAQ ressemble à une page de FAQ avec lien de section, à ceci près que les liens redirigent vers une autre page. QnA Maker analyse toutes les pages associées pour extraire les réponses correspondantes.

 ![Page de FAQ avec lien ciblé](../media/qnamaker-concepts-datasources/deeplink-faq.png) 


## <a name="product-manuals"></a>Manuels de produit

Un manuel comprend habituellement des supports d’orientation qui accompagnent un produit. Il aide l’utilisateur à configurer, utiliser, gérer et dépanner le produit. Lorsque QnA Maker traite un manuel, il extrait les titres et les sous-titres sous forme de questions et le contenu qui suit sous forme de réponses. Un exemple vous est proposé [ici](http://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

> [!NOTE]
> L’extraction est plus efficace sur les manuels qui ont une table des matières et/ou une page d’index, mais aussi une structure clairement définie avec des en-têtes hiérarchiques.


## <a name="structured-data-format-through-file-upload"></a>Format de données structurées via le téléchargement du fichier

Les fichiers structurés du type .tsv, .xlsx avec colonnes de mise en forme peuvent également être chargés vers QnA Maker lors de la création de la base de connaissances. Vous pouvez également charger des fichiers à partir de l’onglet **Paramètres** d’une base de connaissances.

| Question  | Réponse  | Métadonnées                |
|-----------|---------|-------------------------|
| Question1 | Réponse1 | `Clé1:Valeur1|Clé2:Valeur2` |
| Question2 | Réponse2 |      `Key:Value`           |
Toutes les autres colonnes du fichier source sont ignorées.

## <a name="structured-data-format-through-import"></a>Format de données structurées via l’importation
L’importation d’une base de connaissances remplace le contenu de la base de connaissances existante. L’importation nécessite un fichier .tsv structuré qui contient des informations sur la source de données. Ces informations aident QnA Maker à regrouper les paires de question-réponse et à les attribuer à une source de données en particulier.

| Question  | Réponse  | Source| Métadonnées                |
|-----------|---------|----|---------------------|
| Question1 | Réponse1 | Url1|`Clé1:Valeur1|Clé2:Valeur2` |
| Question2 | Réponse2 | Éditorial|    `Key:Value`       |

## <a name="editorial"></a>Éditorial
Si vous ne possédez aucun contenu pour alimenter la base de connaissances, vous pouvez également ajouter du contenu éditorial dans la base de connaissances QnA Maker. Découvrez comment mettre à jour votre base de connaissances [ici](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer un service QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Voir aussi 

[Vue d’ensemble de QnA Maker](../Overview/overview.md)
