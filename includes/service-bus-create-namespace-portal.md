---
title: Fichier Include
description: Fichier Include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 07/03/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 377ea6629223c365ef3d00c756929bf23ddffde5
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702633"
---
Pour commencer à utiliser des entités de messagerie Service Bus dans Azure, vous devez d’abord créer un espace de noms avec un nom unique dans Azure. Ce dernier fournit un conteneur d’étendue pour l’adressage des ressources Service Bus au sein de votre application.

Pour créer un espace de noms :

1. Connectez-vous au [portail Azure][Azure portal].
2. Dans le volet de navigation gauche du portail, cliquez sur **+ Créer une ressource**, sur **Intégration**, puis sur **Service Bus**.
3. Dans la boîte de dialogue **Créer un espace de noms**, entrez un nom d’espace de noms. Le système vérifie immédiatement si le nom est disponible.
4. Lorsque vous avez vérifié la disponibilité de l’espace de noms, sélectionnez le niveau tarifaire (Basique, Standard ou Premium). Si vous voulez utiliser des [rubriques et des abonnements](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), veillez à choisir Standard ou Premium. Les rubriques/abonnements ne sont pas pris en charge dans le niveau tarifaire De base.
5. Dans le champ **Abonnement**, sélectionnez un abonnement Azure dans lequel créer l’espace de noms.
6. Dans le champ **Groupe de ressources** , choisissez un groupe de ressources existant dans lequel l’espace de noms sera utilisé, ou créez-en un nouveau.      
7. Dans **Emplacement**, sélectionnez le pays ou la région où votre espace de noms doit être hébergé.
   
    ![Créer un espace de noms][create-namespace]
8. Cliquez sur **Créer**. Le système crée l’espace de noms de service et l’active. Vous devrez peut-être attendre plusieurs minutes afin que le système approvisionne des ressources pour votre compte.

### <a name="obtain-the-management-credentials"></a>Obtenir les informations d’identification de gestion

Créer un espace de noms génère automatiquement une règle de signature d’accès partagé (SAS) initiale comprenant une paire de clés primaire et secondaire qui vous offre un contrôle complet sur tous les aspects de l’espace de noms. Voir [Authentification et autorisation Service Bus](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) pour plus d’information sur la façon de créer des règles supplémentaires avec des droits plus restreints pour les expéditeurs et les destinataires réguliers. Pour copier la règle initiale, effectuez les étapes suivantes : 

1. Cliquez sur **Toutes les ressources**, puis sur le nom de l’espace de noms que vous venez de créer.
2. Dans la fenêtre Espace de noms, cliquez sur **Stratégies d’accès partagé**.
3. Dans l’écran **Stratégies d’accès partagé**, cliquez sur **RootManageSharedAccessKey**.
   
    ![informations de connexion][connection-info]
4. Dans la fenêtre **Stratégie : RootManageSharedAccessKey**, cliquez sur le bouton Copier situé en regard de **Chaîne de connexion primaire**, pour copier la chaîne de connexion dans le presse-papiers pour une utilisation ultérieure. Copiez cette valeur dans le Bloc-notes ou un autre emplacement temporaire.
   
    ![connection-string][connection-string]

5. Répétez l’étape précédente, en copiant et collant la valeur de **Clé primaire** dans un emplacement temporaire pour l’utiliser ultérieurement.

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
