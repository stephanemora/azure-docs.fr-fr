---
title: Résoudre les erreurs de l’interface CLI du serveur flexible Azure Database pour PostgreSQL
description: Cette rubrique fournit des conseils sur la résolution des problèmes courants liés à l’interface CLI Azure lors de l’utilisation du serveur flexible PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 08/24/2021
ms.openlocfilehash: dd44e0bf0ffd7ae70cdb2b715561517d5b92a049
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440987"
---
# <a name="troubleshoot-azure-database-for-postgresql-flexible-server-cli-errors"></a>Résoudre les erreurs de l’interface CLI du serveur flexible Azure Database pour PostgreSQL

Ce document vous aidera à résoudre les problèmes courants liés à l’interface CLI Azure lors de l’utilisation du serveur flexible PostgreSQL.

## <a name="command-not-found"></a>Commande introuvable

 Si vous recevez un message d’erreur indiquant qu’une commande **est mal orthographiée ou n’est pas reconnue par le système**. Cela peut signifier que la version de l’interface CLI sur l’ordinateur de votre client n’est peut-être pas à jour. Exécutez ```az upgrade``` pour effectuer une mise à niveau vers la dernière version. La mise à niveau de votre version de l’interface CLI peut aider à résoudre les problèmes d’incompatibilité d’une commande en raison des modifications de l’API.
 
## <a name="debug-deployment-failures"></a>Échecs de déploiement de débogage 
Actuellement, l’interface CLI Azure ne prend pas en charge l’activation de la journalisation du débogage, mais vous pouvez récupérer la journalisation du débogage en suivant les étapes ci-dessous.

>[!NOTE]
> - Remplacez ```examplegroup``` et ```exampledeployment``` par le nom de groupe de ressources et de déploiement corrects pour votre serveur de base de données. 
> - Vous pouvez voir le nom du déploiement dans la page déploiements de votre groupe de ressources. Consultez [Comment trouver le nom du déploiement](../../azure-resource-manager/templates/deployment-history.md?tabs=azure-portal).


1. Répertoriez les déploiements dans le groupe de ressources pour identifier le déploiement du serveur PostgreSQL 
    ```azurecli

        az deployment operation group list \
          --resource-group examplegroup \
          --name exampledeployment
    ```

2. Obtenez le contenu de la demande du déploiement du serveur PostgreSQL 
    ```azurecli

        az deployment operation group list \
          --name exampledeployment \
          -g examplegroup \
          --query [].properties.request
    ```
3. Examinez le contenu de la réponse 
    ```azurecli
    az deployment operation group list \
      --name exampledeployment \
      -g examplegroup \
      --query [].properties.response
    ```

## <a name="error-codes"></a>Codes d’erreur

| Code d'erreur | Limitation des risques |
| ---------- | ---------- | 
|MissingSubscriptionRegistration|Inscrivez votre abonnement auprès du fournisseur de ressources. Exécutez la commande ```az provider register --namespace Microsoft.DBPostgreSQL``` pour résoudre le problème.|
|InternalServerError| Essayez d’afficher les journaux d’activité de votre serveur pour voir s’il y a plus d’informations. Exécutez la commande ```az monitor activity-log list --correlation-id <enter correlation-id>```. Vous pouvez essayer la même commande CLI au bout de quelques minutes. Si les problèmes persistent, [signalez-les](https://github.com/Azure/azure-cli/issues) ou contactez le support Microsoft.|
|ResourceNotFound| La ressource référencée est introuvable.  Vous pouvez vérifier les propriétés de la ressource ou vérifier si la ressource est supprimée ou vérifier si la ressource est un autre abonnement. |
|LocationNotAvailableForResourceType| -Vérifiez la disponibilité de la base de données Azure pour le serveur flexible Postgres dans les [régions Azure](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). <br>- Vérifiez si les types de ressources Azure DB pour PostgreSQL sont enregistrés dans votre abonnement. |
|ResourceGroupBeingDeleted| Le groupe de ressources est en cours de suppression. Attendez que la suppression soit terminée.|
|PasswordTooLong| Le mot de passe est trop long. Il doit contenir entre 8 et 128 caractères. Votre mot de passe doit contenir des caractères de trois des catégories suivantes : Lettres majuscules, lettres minuscules, chiffres (0 à 9) et caractères non alphanumériques (!, $, #, %, etc.).|
|PasswordNotComplex| Le mot de passe fourni n’est pas assez complexe.  Il doit contenir entre 8 et 128 caractères. Votre mot de passe doit contenir des caractères de trois des catégories suivantes : Lettres majuscules, lettres minuscules, chiffres (0 à 9) et caractères non alphanumériques (!, $, #, %, etc.).|
|PasswordTooShort| Il doit contenir entre 8 et 128 caractères. Votre mot de passe doit contenir des caractères de trois des catégories suivantes : Lettres majuscules, lettres minuscules, chiffres (0 à 9) et caractères non alphanumériques (!, $, #, %, etc.).|
|SubscriptionNotFound| L’abonnement demandé est introuvable. Exécutez ```az account list all``` pour afficher tous vos abonnements actuels.|
|InvalidParameterValue| Une valeur non valide a été spécifiée pour un paramètre. Consultez les [documents de référence de l’interface CLI](/cli/azure/postgres/flexible-server?view=azure-cli-latest&preserve-view=true) pour voir quelles sont les valeurs correctes prises en charge pour les arguments.|
|InvalidLocation| Un emplacement non valide a été spécifié. -Vérifiez la disponibilité de la base de données Azure pour le serveur flexible Postgres dans les [régions Azure](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). |
|InvalidServerName|Identification d’un nom de serveur non valide. Vérifier le nom du serveur. Exécutez la commande [az mysql flexible-server list](/cli/azure/mysql/flexible-server?view=azure-cli-latest#az_mysql_flexible_server_list&preserve-view=true) pour afficher la liste des serveurs flexibles disponibles.|
|InvalidResourceIdSegment| Une erreur de syntaxe a été identifiée dans le modèle de votre Gestionnaire de ressource Azure. Utilisez un outil de formatage JSON pour valider le JSON afin d’identifier l’erreur de syntaxe.|
|InvalidUserName| Entrez un nom d'utilisateur valide. Le nom d’utilisateur de l’administrateur ne doit pas être azure_superuser, azure_pg_admin, admin, administrator, root, guest ou public. Il ne peut pas commencer par pg_.|
|BlockedUserName| Le nom d’utilisateur de l’administrateur ne doit pas être azure_superuser, azure_pg_admin, admin, administrator, root, guest ou public. Il ne peut pas commencer par pg_. Évitez d’utiliser ces modèles dans le nom de l’administrateur.|

## <a name="next-steps"></a>Étapes suivantes

- Si vous rencontrez toujours des problèmes, veuillez [signaler le problème](https://github.com/Azure/azure-cli/issues). 
- Si vous avez des questions, visitez notre page Stack Overflow : https://aka.ms/azcli/questions . 
- Donnez votre avis sur notre courte enquête https://aka.ms/azureclihats. 
