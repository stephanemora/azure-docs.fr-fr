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
ms.openlocfilehash: 6afc07f98905469b06622e7829ec4a215b94845e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716217"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Réécrire les en-têtes de demande et de réponse HTTP avec Azure Application Gateway - portail Azure

Cet article vous montre comment utiliser le portail Azure pour configurer un [référence (SKU) de passerelle d’Application v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) pour réécrire les en-têtes HTTP dans les demandes et réponses.

> [!IMPORTANT]
> La référence SKU de la passerelle d’application redondante interzone et avec mise à l’échelle automatique est disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="before-you-begin"></a>Avant de commencer

Vous devez disposer d’un environnement v2 passerelle d’Application étant donné que la capacité de réécriture de l’en-tête dans la référence (SKU) n’est pas pris en charge pour la référence SKU v1. Si vous n’avez pas la référence (SKU) v2, créez un [référence (SKU) de passerelle d’Application v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) avant de commencer.

## <a name="what-is-required-to-rewrite-a-header"></a>Ce qui est nécessaire de réécrire un en-tête

Pour configurer la réécriture d’en-tête HTTP, vous devez :

1. Créer les nouveaux objets requis pour réécrire les en-têtes HTTP :

   - **Réécriture d’Action**: permet de spécifier la demande et les champs d’en-tête de demande que vous avez l’intention de réécrire et la nouvelle valeur que les en-têtes d’origine doivent être réécrites pour. Vous pouvez choisir d’associer à un ou plus réécriture condition avec une action de réécriture.

   - **Réécrivez la Condition**: Il est une configuration facultative. Si une condition de la réécriture est ajoutée, il est évalué le contenu des requêtes HTTP (S) et des réponses. La décision d’exécuter l’action de réécriture associée à la condition de réécriture reposera si la demande de HTTP (S) ou la réponse mise en correspondance avec la condition de réécriture. 

     Si plusieurs conditions sont associé à une action, puis l’action sera exécutée uniquement lorsque toutes les conditions sont remplies, par exemple, une opération AND logique sera effectuée.

   - **Règle de réécriture**: règle de réécriture contient plusieurs réécriture action - réécrire les combinaisons de condition.

   - **Séquence de règle**: permet de déterminer l’ordre dans lequel les différentes règles de réécriture sont exécutées. Cela est utile lorsqu’il existe plusieurs règles de réécriture dans un ensemble de réécriture. La règle de réécriture avec la valeur de séquence de règle inférieure obtient exécutée en premier. Si vous fournissez la même séquence de règle à deux règles de réécriture, l’ordre d’exécution sera non déterministe.

   - **Réécrire ensemble**: contient plusieurs règles de réécriture qui seront associés à une règle de routage de demande.

2. Vous devez attacher la réécriture définie avec une règle de routage. Il s’agit, car la configuration de la réécriture est attachée à l’écouteur source via la règle de routage. Lorsque vous utilisez une règle d’acheminement de base, la configuration de réécriture de l’en-tête est associée à un écouteur de la source et est une réécriture de l’en-tête global. Lorsqu’une règle d’acheminement basée sur le chemin d’accès est utilisée, la configuration de réécriture de l’en-tête est définie sur le mappage de chemin d’accès d’URL. Elle s’applique donc exclusivement à la zone de chemin d’accès spécifique d’un site.

Vous pouvez créer plusieurs jeux de réécriture d’en-tête http et chaque jeu de réécriture peut être appliqué à plusieurs écouteurs. Toutefois, vous pouvez appliquer uniquement un ensemble à un port d’écoute spécifique de réécriture.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com/) avec votre compte Azure.

## <a name="configure-header-rewrite"></a>Configurer la réécriture de l’en-tête

Dans cet exemple, nous allons modifier l’URL de redirection par la réécriture de l’en-tête d’emplacement dans la réponse http envoyée par l’application principale. 

1. Sélectionnez **toutes les ressources**, puis sélectionnez votre passerelle d’application.

2. Sélectionnez **réécrit** dans le menu de gauche.

3. Cliquez sur **+ réécrire ensemble**. 

   ![Ajouter le jeu de réécriture](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Fournissez le nom à l’ensemble de réécriture et l’associer à une règle de routage :

   - Entrez le nom de la réécriture définie le **nom** zone de texte.
   - Sélectionnez une ou plusieurs règles répertoriées dans le **associées des règles de routage** liste. Vous pouvez sélectionner uniquement les règles qui n’ont pas été associés avec d’autres ensembles de réécriture. Les règles qui ont déjà été associées avec d’autres ensembles de réécriture seront grisées.
   - Cliquez sur Suivant.
   
     ![Ajouter le nom et l’association](media/rewrite-http-headers-portal/name-and-association.png)

5. Créer une règle de réécriture :

   - Cliquez sur **+ ajouter une règle de réécriture**.![ Ajouter une règle de réécriture](media/rewrite-http-headers-portal/add-rewrite-rule.png)
   - Fournir un nom pour la règle de réécriture dans la zone de texte Nom de règle réécriture et une séquence de la règle.![Ajouter le nom de la règle](media/rewrite-http-headers-portal/rule-name.png)

6. Dans cet exemple, nous avons réécrit l’en-tête d’emplacement que lorsqu’elle contient une référence à « azurewebsites.net ». Pour ce faire, ajoutez une condition à évaluer si l’en-tête d’emplacement dans la réponse contient azurewebsites.net :

   - Cliquez sur **+ ajouter une condition** , puis cliquez sur la section avec le **si** obtenir des instructions pour développer les y![ Ajouter le nom de la règle](media/rewrite-http-headers-portal/add-condition.png)

   - Sélectionnez **en-tête HTTP** à partir de la **Type de variable à vérifier** liste déroulante. 

   - Sélectionnez **type d’en-tête** comme **réponse**.

   - Étant donné que dans cet exemple, nous évaluons l’en-tête location qui se trouve être un en-tête commun, sélectionnez **en-tête commun** case d’option en tant que le **nom d’en-tête**.

   - Sélectionnez **emplacement** à partir de la **en-tête commun** liste déroulante.

   - Sélectionnez **non** en tant que le **respect de la casse** paramètre.

   - Sélectionnez **signe égal (=)** à partir de la **opérateur** liste déroulante.

   - Entrez le modèle d’expression régulière. Dans cet exemple, nous allons utiliser le modèle `(https?):\/\/.*azurewebsites\.net(.*)$` .

   - Cliquez sur **OK**.

     ![Modifier l’en-tête d’emplacement](media/rewrite-http-headers-portal/condition.png)

7. Ajouter une action pour réécrire l’en-tête d’emplacement :

   - Sélectionnez **définir** en tant que le **type d’Action**.

   - Sélectionnez **réponse** en tant que le **type d’en-tête**.

   - Sélectionnez **en-tête commun** en tant que le **nom d’en-tête**.

   - Sélectionnez **emplacement** à partir de la **en-tête commun** liste déroulante.

   - Entrez la valeur d’en-tête. Dans cet exemple, nous allons utiliser `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` en tant que la valeur d’en-tête. Cette opération va remplacer *azurewebsites.net* avec *contoso.com* dans l’en-tête location.

   - Cliquez sur **OK**.

     ![Modifier l’en-tête d’emplacement](media/rewrite-http-headers-portal/action.png)

8. Cliquez sur **créer** pour créer la réécriture.

   ![Modifier l’en-tête d’emplacement](media/rewrite-http-headers-portal/create.png)

9. Vous serez redirigé vers la vue d’ensemble de réécriture. Vérifiez que le jeu de réécriture que vous avez créé précédemment est présent dans la liste des jeux de réécriture.

   ![Modifier l’en-tête d’emplacement](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la configuration requise pour accomplir certaines courantes cas d’utilisation, consultez [scénarios de réécriture d’en-tête commun](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

   
