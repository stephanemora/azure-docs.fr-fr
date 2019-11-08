---
title: Résoudre les problèmes liés à Azure Data Share
description: Découvrez comment résoudre les problèmes avec les invitations et les erreurs rencontrées lors de la création ou réception de partages de données dans Azure Data Share.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 6ad612d56b25da9e092070198e321e7fca8ad96b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490560"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Résoudre les problèmes courants dans Azure Data Share 

Cet article explique comment résoudre les problèmes courants pour Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Invitations Azure Data Share 

Dans certains cas, quand un nouvel utilisateur clique sur **Accepter l’invitation** à partir de l’invitation par e-mail qui a été envoyée, une liste vide d’invitations peut lui être présentée. 

![Aucune invitation](media/no-invites.png)

L’erreur ci-dessus est un problème connu avec le service et est actuellement en cours de résolution. Pour une solution de contournement, suivez les étapes ci-dessous. 

1. Dans le portail Azure, accédez à **Abonnements**
1. Sélectionnez l’abonnement que vous utilisez pour Azure Data Share
1. Cliquez sur **Fournisseurs de ressources**
1. Recherchez Microsoft.DataShare
1. Cliquez sur **S’inscrire**

Vous devez disposer du [rôle RBAC de contributeur Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) pour effectuer ces étapes. 

Si vous ne parvenez toujours pas à voir une invitation de partage de données, contactez votre fournisseur de données et assurez-vous qu’il l’a bien envoyée à votre adresse e-mail de connexion Azure et *pas* à votre alias de messagerie. 

> [!IMPORTANT]
> Si vous avez déjà accepté une invitation Azure Data Share et que vous avez quitté le service avant de configurer le stockage, suivez les instructions détaillées du guide de procédure [Configurer un mappage de jeu de données](how-to-configure-mapping.md) pour savoir comment terminer la configuration de votre partage de données reçues et commencer à recevoir des données. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Erreur lors de la création ou de la réception d’un nouveau partage de données

« Erreur : L’opération a retourné un code d’état non valide "BadRequest" »

« Erreur : AuthorizationFailed »

« Erreur : Attribution de rôle au compte de stockage »

![Erreur de privilège](media/error-write-privilege.png)

Si vous recevez une des erreurs ci-dessus lors de la création d’un nouveau partage de données ou lors de la réception d’un nouveau partage de données, la raison en est que les autorisations sont insuffisantes pour le compte de stockage. L’autorisation requise est *Microsoft.Authorization/attributions de rôle/écrire*, qui existe dans le rôle de propriétaire du stockage ou qui peut être attribuée à un rôle personnalisé. Même si vous avez créé le compte de stockage, cela ne fait PAS automatiquement de vous le propriétaire de celui-ci. Suivez ces étapes pour vous accorder à vous-même la propriété du compte de stockage. Une autre possibilité est de créer un rôle personnalisé avec cette autorisation et de vous y ajouter vous-même.  

1. Accéder à un compte de stockage dans le portail Azure
1. Sélectionnez **Contrôle d’accès (IAM)**
1. Cliquez sur **Ajouter**.
1. Ajoutez-vous en tant que propriétaire.

## <a name="troubleshooting-sql-based-sharing"></a>Résolution des problèmes de partage basé sur SQL

«Erreur : x jeux de données n’ont pas été ajoutés car vous ne disposez pas des autorisations nécessaires pour le partager. »

Si vous recevez ce message d’erreur lors de l’ajout d’un jeu de données à partir d’une source SQL, cela peut être dû au fait que vous n’avez pas créé d’utilisateur pour le fichier MSI Azure Data Share sur votre instance SQL Server.  Pour résoudre ce problème, exécutez le script suivant :

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Notez que *<share_acc_name>* est le nom de votre compte Data Share. Si vous n’avez pas encore créé de compte Data Share, vous pouvez le faire plus tard.         

Assurez-vous que vous avez suivi toutes les conditions préalables indiquées dans le didacticiel [Partager vos données](share-your-data.md).

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment commencer à partager des données, passez au tutoriel [Partager vos données](share-your-data.md).

