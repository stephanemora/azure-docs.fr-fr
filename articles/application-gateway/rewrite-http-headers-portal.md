---
title: Réécrire les en-têtes de demande et de réponse HTTP avec Azure Application Gateway - portail Azure | Microsoft Docs
description: Découvrez comment utiliser le portail Azure pour configurer une passerelle d’Application Azure pour réécrire les en-têtes HTTP dans les demandes et réponses passant par la passerelle
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/10/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: e144214a58f9fe383cf4edd878554792d9d6a6f9
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947161"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Réécrire les en-têtes de demande et de réponse HTTP avec Azure Application Gateway - portail Azure

Cet article explique comment utiliser le portail Azure pour configurer un [référence (SKU) de passerelle d’Application v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) instance à réécrire les en-têtes HTTP dans les demandes et réponses.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="before-you-begin"></a>Avant de commencer

Vous devez disposer d’une instance de référence (SKU) de v2 de passerelle d’Application pour effectuer les étapes décrites dans cet article. Réécriture des en-têtes n’est pas pris en charge dans la référence (SKU) v1. Si vous n’avez pas la référence (SKU) v2, créez un [référence (SKU) de passerelle d’Application v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) instance avant de commencer.

## <a name="create-required-objects"></a>Créer des objets requis

Pour configurer la réécriture d’en-tête HTTP, vous devez suivre ces étapes.

1. Créer les objets qui sont requis pour la réécriture d’en-tête HTTP :

   - **Réécriture d’action**: Permet de spécifier la demande et de champs d’en-tête de demande que vous souhaitez réécrire et de la nouvelle valeur pour les en-têtes. Vous pouvez associer un ou plusieurs conditions avec une action de réécriture de réécriture.

   - **Réécrivez la condition**: Une configuration facultative. Conditions de réécriture évaluent le contenu des requêtes HTTP (S) et des réponses. L’action de réécriture se produira si la demande de HTTP (S) ou la réponse correspond à la condition de réécriture.

     Si vous associez plusieurs conditions à une action, l’action se produit uniquement lorsque toutes les conditions sont remplies. En d’autres termes, l’opération est une opération AND logique.

   - **Règle de réécriture**: Contient plusieurs réécriture action / réécrire les combinaisons de condition.

   - **Séquence de règle**: Permet de déterminer l’ordre dans lequel les règles de réécriture s’exécutent. Cette configuration est utile lorsque vous avez plusieurs règles de réécriture dans un ensemble de réécriture. Une règle de réécriture qui a une valeur de séquence de règle inférieure s’exécute en premier. Si vous affectez la même valeur de séquence de règle à deux règles de réécriture, l’ordre d’exécution est non déterministe.

   - **Réécrire ensemble**: Contient plusieurs règles de réécriture qui seront associés à une règle de routage de demande.

2. Attacher la réécriture de la valeur est une règle de routage. La configuration de la réécriture est attachée à l’écouteur source via la règle de routage. Lorsque vous utilisez une règle de routage de base, la configuration de réécriture de l’en-tête est associée à un écouteur de la source et est une réécriture de l’en-tête global. Lorsque vous utilisez une règle de routage basée sur le chemin d’accès, la configuration de réécriture de l’en-tête est définie sur le mappage de chemin d’accès d’URL. Dans ce cas, il s’applique uniquement à la zone de chemin d’accès spécifique d’un site.

Vous pouvez créer plusieurs jeux de réécriture d’en-tête HTTP et appliquer chaque réécriture définie sur plusieurs écouteurs. Mais vous pouvez appliquer uniquement un ensemble à un port d’écoute spécifique de réécriture.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com/) avec votre compte Azure.

## <a name="configure-header-rewrite"></a>Configurer la réécriture de l’en-tête

Dans cet exemple, nous allons modifier une URL de redirection par la réécriture de l’en-tête d’emplacement dans la réponse HTTP envoyée par une application back-end.

1. Sélectionnez **toutes les ressources**, puis sélectionnez votre passerelle d’application.

2. Sélectionnez **réécrit** dans le volet gauche.

3. Sélectionnez **réécrire ensemble**:

   ![Ajouter le jeu de réécriture](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Fournissez un nom pour le jeu de réécriture et l’associer à une règle de routage :

   - Entrez le nom de la réécriture définie le **nom** boîte.
   - Sélectionnez une ou plusieurs des règles présentées dans le **associées des règles de routage** liste. Vous pouvez sélectionner uniquement les règles qui n’ont pas été associés avec d’autres ensembles de réécriture. Les règles qui ont déjà été associées avec d’autres ensembles de réécriture sont estompées.
   - Sélectionnez **Suivant**.
   
     ![Ajouter le nom et l’association](media/rewrite-http-headers-portal/name-and-association.png)

5. Créer une règle de réécriture :

   - Sélectionnez **ajouter une règle de réécriture**.

     ![Ajouter une règle de réécriture](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Entrez un nom pour la règle de réécriture dans le **nom de règle de réécriture** boîte. Entrez un numéro dans le **règle séquence** boîte.

     ![Ajouter le nom de règle de réécriture](media/rewrite-http-headers-portal/rule-name.png)

6. Dans cet exemple, nous allons réécrire l’en-tête d’emplacement que lorsqu’elle contient une référence à azurewebsites.net. Pour ce faire, ajoutez une condition à évaluer si l’en-tête d’emplacement dans la réponse contient azurewebsites.net :

   - Sélectionnez **ajouter une condition** , puis sélectionnez la zone contenant le **si** obtenir des instructions pour le développer.

     ![Ajouter une condition](media/rewrite-http-headers-portal/add-condition.png)

   - Dans le **Type de variable à vérifier** liste, sélectionnez **en-tête HTTP**.

   - Dans le **type d’en-tête** liste, sélectionnez **réponse**.

   - Étant donné que dans cet exemple, nous évaluons l’en-tête location, qui est un en-tête commun, sélectionnez **en-tête commun** sous **nom d’en-tête**.

   - Dans le **en-tête commun** liste, sélectionnez **emplacement**.

   - Sous **respect de la casse**, sélectionnez **non**.

   - Dans le **opérateur** liste, sélectionnez **signe égal (=)**.

   - Entrez un modèle d’expression régulière. Dans cet exemple, nous allons utiliser le modèle `(https?):\/\/.*azurewebsites\.net(.*)$`.

   - Sélectionnez **OK**.

     ![Configurer un If condition](media/rewrite-http-headers-portal/condition.png)

7. Ajouter une action pour réécrire l’en-tête d’emplacement :

   - Dans le **type d’Action** liste, sélectionnez **définir**.

   - Dans le **type d’en-tête** liste, sélectionnez **réponse**.

   - Sous **nom d’en-tête**, sélectionnez **en-tête commun**.

   - Dans le **en-tête commun** liste, sélectionnez **emplacement**.

   - Entrez la valeur d’en-tête. Dans cet exemple, nous allons utiliser `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` en tant que la valeur d’en-tête. Cette valeur remplacera *azurewebsites.net* avec *contoso.com* dans l’en-tête location.

   - Sélectionnez **OK**.

     ![Ajouter une action](media/rewrite-http-headers-portal/action.png)

8. Sélectionnez **créer** pour créer la réécriture jeu :

   ![Sélectionner Créer](media/rewrite-http-headers-portal/create.png)

9. La vue d’ensemble de réécriture s’ouvre. Vérifiez que le jeu de réécriture que vous avez créé est dans la liste des jeux de réécriture :

   ![Vue d’ensemble de réécriture](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la façon de configurer certains scénarios courants d’utilisation, consultez [scénarios de réécriture d’en-tête commun](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).