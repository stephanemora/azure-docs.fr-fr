---
title: Exécuter manuellement une fonction Azure non déclenchée via HTTP
description: Utiliser une requête HTTP pour exécuter une fonction Azure non déclenchée via HTTP
author: craigshoemaker
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 4ce7b8590e4718585fe841921466e049dc204928
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75769130"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Exécuter manuellement une fonction non déclenchée via HTTP

Cet article explique comment exécuter manuellement une fonction non déclenchée via une requête HTTP spécialement formatée.

Vous pourrez être amené à exécuter « à la demande » une fonction Azure déclenchée indirectement.  Parmi les exemples de déclencheurs indirects figurent les [fonctions selon une planification](./functions-create-scheduled-function.md) ou les fonctions dont l'exécution résulte de l'[action d’une autre ressource](./functions-create-storage-blob-triggered-function.md). 

[Postman](https://www.getpostman.com/) est utilisé dans l’exemple suivant, mais vous pouvez utiliser [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) ou tout autre outil pour envoyer des requêtes HTTP.

## <a name="define-the-request-location"></a>Définir l’emplacement de la requête

Pour exécuter une fonction non déclenchée via HTTP, vous devez trouver un moyen d’envoyer une requête vers Azure. L’URL utilisée pour effectuer cette requête présente une forme spécifique.

![Définir l’emplacement de la requête : nom d'hôte + chemin d’accès du dossier + nom de fonction](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Nom d’hôte :** emplacement public de l’application de fonction composé du nom de l’application plus *azurewebsites.net* ou votre domaine personnalisé.
- **Chemin d’accès du dossier :** pour accéder aux fonctions non déclenchées via HTTP via une requête HTTP, vous devez envoyer cette requête via les dossiers *admin/fonctions*.
- **Nom de fonction :** Nom de la fonction que vous souhaitez exécuter.

Vous utilisez cet emplacement de requête dans Postman, de même que la clé principale de la fonction dans la requête vers Azure pour exécuter la fonction.

> [!NOTE]
> Quand vous exécutez localement, la clé principale de la fonction n’est pas nécessaire. Vous pouvez directement [appeler la fonction](#call-the-function) en omettant l’en-tête `x-functions-key`.

## <a name="get-the-functions-master-key"></a>Obtenir la clé principale de la fonction

Accédez à votre fonction dans le portail Azure. Cliquez sur **Gérer**, puis recherchez la section **Clés d’hôte**. Cliquez sur le Bouton **Copier** de la ligne *_master* pour copier la clé principale dans le Presse-papiers.

![Copier la clé principale à partir de l’écran Gestion des fonctions](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

Après avoir copié la clé principale, cliquez sur le nom de fonction pour revenir à la fenêtre du fichier de code. Cliquez ensuite sur l’onglet **Journaux d’activité**. Vous noterez ici la présence de messages émanant de la fonction consignée lors de l'exécution de la fonction à partir de Postman.

> [!CAUTION]  
> En raison des autorisations élevées dans votre application de fonction accordées par la clé principale, vous ne devez pas partager celle-ci avec des tiers, ou la distribuer dans une application.

## <a name="call-the-function"></a>Appeler la fonction

Ouvrez Postman et procédez comme suit :

1. Entrez l'**emplacement de la requête dans la zone de texte de l'URL**.
2. Vérifiez que la méthode HTTP est définie sur **POST**.
3. **Cliquez** sur l'onglet **En-têtes**.
4. Entrez **x-fonctions-key** en tant que première **clé** et collez la clé principale (à partir du Presse-papiers) dans la zone **Valeur**.
5. Entrez **Content-Type** en tant que deuxième **clé** et entrez **application/json** en tant que **valeur**.

    ![Paramètres des en-têtes Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **Cliquez** sur l'onglet **Corps**.
7. Entrez **{ "input": "test" }** en tant que corps de la requête.

    ![Paramètres de corps Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. Cliquez sur **Envoyer**.

    ![Envoi d’une requête avec Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

Postman indique alors un état **202 Accepté**.

Revenez ensuite à votre fonction sur le Portail Azure. Reportez-vous à la fenêtre *Journaux d’activité* afin de consulter les messages émanant de l’appel manuel de la fonction.

![Résultats du journal de la fonction à partir d'un appel manuel](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>Étapes suivantes

- [Stratégies permettant de tester votre code dans Azure Functions](./functions-test-a-function.md)
- [Débogage local lors du déclenchement de fonctions Azure Event Grid](./functions-debug-event-grid-trigger-local.md)
