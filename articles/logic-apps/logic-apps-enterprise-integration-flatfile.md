---
title: Encoder ou décoder des fichiers plats
description: Encoder ou décoder des fichiers plats pour l’intégration d’entreprise avec Azure Logic Apps et Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 2d6182ba01507c2fb361628e01bb52e1ea821f44
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152650"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Encoder ou décoder des fichiers plats avec Azure Logic Apps et Enterprise Integration Pack

Par exemple, vous pouvez encoder le contenu XML avant de l’envoyer à un partenaire commercial dans un scénario B2B. Dans une application logique, vous pouvez utiliser le connecteur d’encodage de fichier plat pour effectuer cette opération. L’application logique que vous créez peut obtenir son contenu XML de diverses sources y compris à partir d’un déclencheur de requête HTTP, d’une autre application, voire d’un des nombreux [connecteurs](../connectors/apis-list.md). Pour plus d’informations sur les applications logiques, consultez la [documentation sur les applications logiques](logic-apps-overview.md "Apprenez-en davantage sur les applications logiques.").  

## <a name="create-the-flat-file-encoding-connector"></a>Créer le connecteur d’encodage de fichier plat
Suivez ces étapes pour ajouter un connecteur d’encodage de fichier plat à votre application logique.

1. Créez une application logique et [liez-la à votre compte d’intégration](logic-apps-enterprise-integration-accounts.md "Découvrez comment lier un compte d’intégration à une application logique"). Ce compte contient le schéma que vous allez utiliser pour encoder les données XML.  

1. Dans le Concepteur d’application logique, ajoutez le déclencheur **Lors de la réception d’une demande HTTP** à votre application logique.

1. Ajoutez l’action d’encodage de fichier plat en procédant comme suit :

   a. Sélectionnez le signe **plus** .

   b. Sélectionnez le lien **Ajouter une action** (qui s’affiche après que vous avez sélectionné le signe plus).

   c. Dans la zone de recherche, entrez *Plat* pour filtrer toutes les actions et obtenir celle que vous souhaitez utiliser.

   d. Sélectionnez l’option **Encodage du fichier plat** dans la liste.   

      ![Capture d’écran de l’option Codage du fichier plat](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Dans la boîte de dialogue **Encodage du fichier plat**, sélectionnez la zone de texte **Contenu**.  

   ![Capture d’écran de la zone de texte Contenu](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  

1. Sélectionnez la balise body comme contenu à encoder. La balise body permet de renseigner le champ de contenu.     

   ![Capture d’écran de la balise body](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  

1. Sélectionnez la zone de liste **Nom du schéma** et choisissez le schéma que vous souhaitez utiliser pour encoder le contenu d’entrée.    

   ![Capture d’écran de la zone de liste Nom du schéma](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  

1. Enregistrez votre travail.

   ![Capture d’écran de l’icône Enregistrer](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

À ce stade, vous avez terminé de configurer votre connecteur d’encodage de fichier plat. Dans une application réelle, vous souhaiterez peut-être stocker les données encodées dans une application métier, comme Salesforce. Vous pouvez également envoyer ces données encodées à un partenaire commercial. Vous pouvez facilement ajouter une action pour envoyer la sortie de l’action d’encodage à Salesforce ou à votre partenaire commercial en utilisant l’un des autres connecteurs fournis.

Vous pouvez maintenant tester votre connecteur en envoyant une requête au point de terminaison HTTP, en incluant le contenu XML dans le corps de la requête.  

## <a name="create-the-flat-file-decoding-connector"></a>Créer le connecteur de décodage de fichier plat

> [!NOTE]
> Pour effectuer ces étapes, vous devez disposer d’un fichier de schéma déjà chargé sur votre compte d’intégration.

1. Dans le Concepteur d’application logique, ajoutez le déclencheur **Lors de la réception d’une demande HTTP** à votre application logique.

1. Ajoutez l’action de décodage de fichier plat en procédant comme suit :

   a. Sélectionnez le signe **plus** .

   b. Sélectionnez le lien **Ajouter une action** (qui s’affiche après que vous avez sélectionné le signe plus).

   c. Dans la zone de recherche, entrez *Plat* pour filtrer toutes les actions et obtenir celle que vous souhaitez utiliser.

   d. Sélectionnez l’action **Décodage du fichier plat** dans la liste.   

      ![Capture d’écran de l’option Décodage du fichier plat](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Sélectionnez le contrôle **Contenu** . Vous obtenez la liste du contenu des étapes précédentes que vous pouvez utiliser comme contenu à décoder. Notez que le *Corps* de la requête HTTP entrante peut être utilisé comme contenu à décoder. Vous pouvez également entrer le contenu à décoder directement dans le contrôle **Contenu** .     

1. Sélectionnez la balise *Body* . Notez que la balise body apparaît maintenant dans le contrôle **Contenu** .

1. Sélectionnez le nom du schéma que vous souhaitez utiliser pour décoder le contenu. La capture d’écran suivante montre que *OrderFile* est le nom de schéma sélectionné. Ce nom de schéma a été chargé précédemment dans le compte d’intégration.

   ![Capture d’écran de la boîte de dialogue Décodage du fichier plat](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png) 

1. Enregistrez votre travail.  

   ![Capture d’écran de l’icône Enregistrer](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

À ce stade, vous avez terminé de configurer votre connecteur de décodage de fichier plat. Dans une application réelle, vous souhaiterez peut-être stocker les données décodées dans une application métier, comme Salesforce. Vous pouvez facilement ajouter une action pour envoyer la sortie de l'action d'encodage à SalesForce.

Vous pouvez maintenant tester votre connecteur en envoyant une demande au point de terminaison HTTP, en incluant le contenu XML à décoder dans le corps de la demande.  

## <a name="next-steps"></a>Étapes suivantes
* [Apprenez-en davantage sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")  

