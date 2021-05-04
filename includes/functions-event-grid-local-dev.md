---
title: Fichier include
description: Fichier include
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f2dcb92d0a600c57d96348413704863285cea3f0
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987185"
---
## <a name="allow-azure-to-call-your-local-function"></a>Autoriser Azure à appeler votre fonction locale

Pour arrêter une fonction en cours de débogage sur votre ordinateur, vous devez permettre à Azure de communiquer avec votre fonction locale à partir du cloud.

L’utilitaire [ngrok](https://ngrok.com/) permet à Azure d’appeler la fonction en cours d’exécution sur votre ordinateur. Démarrez *ngrok* en utilisant la commande suivante :

```bash
ngrok http -host-header=localhost 7071
```
Pendant la configuration de l’utilitaire, la fenêtre de commande doit ressembler à la capture d’écran suivante :

![Capture d’écran de l’invite de commandes après le lancement de l’utilitaire « ngrok »](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-ngrok.png)

Copiez l’URL **HTTPS** générée lorsque *ngrok* est exécuté. Cette valeur est utilisée lors de la configuration du point de terminaison de l’événement Event Grid.

## <a name="add-a-storage-event"></a>Ajouter une clé de stockage

1. Ouvrez le portail Azure et accédez à votre compte de stockage, puis sélectionnez l’option **Événements** dans le menu de gauche.

    ![Ajouter un événement de compte de stockage](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-add-event.png)

1. Dans la fenêtre *Événements*, sélectionnez le bouton **Abonnement à un événement**. 

1. Dans la fenêtre *Abonnement à un événement*, sélectionnez la liste déroulante *Type de point de terminaison* et sélectionnez **Webhook**.

    ![Sélectionner un type d’abonnement](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-event-subscription-type.png)

1. Une fois le type de point de terminaison configuré, cliquez sur **Sélectionner un point de terminaison** pour configurer la valeur du point de terminaison.

    ![Sélectionner un type de point de terminaison](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-event-subscription-endpoint.png)

    La valeur *Point de terminaison de l’abonné* se compose de trois valeurs distinctes. Le préfixe correspond à l’adresse URL HTTPS générée par *ngrok*. Le reste de l’URL provient de l’URL localhost copiée précédemment dans le guide pratique, avec le nom de la fonction ajouté à la fin. À partir de l’URL localhost, l’URL *ngrok* remplace `http://localhost:7071` et le nom de la fonction remplace `{functionname}`.

1. La capture d’écran suivante montre un exemple de la façon dont l’URL finale doit se présenter lors de l’utilisation d’un type de déclencheur `Event Grid`.

    ![Sélection de point de terminaison](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-event-subscription-endpoint-selection.png)

1. Une fois que vous avez entré la valeur appropriée, cliquez sur **Confirmer la sélection**.

> [!IMPORTANT]
> Chaque fois que vous démarrez *ngrok*, l’URL HTTPS est régénérée et la valeur change. Par conséquent, vous devez créer un abonnement d’événement chaque fois que vous exposez votre fonction à Azure via *ngrok*.

## <a name="upload-a-file"></a>Charger un fichier

Vous pouvez maintenant charger un fichier sur votre compte de stockage afin de déclencher un événement Event Grid pour votre fonction locale à gérer. 

Ouvrez l’[Explorateur Stockage](https://azure.microsoft.com/features/storage-explorer/) et connectez-le à votre compte de stockage. 

- Développer **Conteneurs d'objets blob** 
- Cliquez avec le bouton droit et sélectionnez **Conteneurs d’objets blob**.
- Nommez le conteneur **samples-workitems**
- Sélectionnez le conteneur *samples-workItems*
- Cliquez sur le bouton **Télécharger**
- Cliquez sur **Charger des fichiers**
- Sélectionnez un fichier et chargez-le dans le conteneur d’objets blob

