---
title: Utilisation du stockage de files d’attente à partir de Ruby - Stockage Azure
description: Découvrez comment utiliser le Stockage File d'attente Azure pour créer et supprimer des files d'attente, ainsi que pour insérer, récupérer et supprimer des messages. Les exemples sont écrits en Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/08/2016
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8558949e49bcf551c9276458d375fb9ac9636184
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587660"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Utilisation du stockage de files d'attente à partir de Ruby

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Ce guide décrit le déroulement de scénarios courants dans le cadre de l’utilisation du service de stockage de files d’attente Microsoft Azure. Les exemples sont écrits à l'aide de l'API Ruby Azure. Les scénarios traités incluent **l’insertion**, la **lecture furtive**, la **récupération** et la **suppression** des messages de file d’attente, ainsi que la **création et suppression des files d’attente**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Création d'une application Ruby

Créez une application Ruby. Pour obtenir les instructions, consultez [Créer une application Ruby dans App Service sur Linux](../../app-service/quickstart-ruby.md).

## <a name="configure-your-application-to-access-storage"></a>Configuration de votre application pour accéder au stockage

Pour utiliser Stockage Azure, vous devez télécharger et utiliser le package Azure Ruby, qui inclut un ensemble de bibliothèques permettant de communiquer avec les services de stockage REST.

<!-- docutune:ignore Terminal -->

### <a name="use-rubygems-to-obtain-the-package"></a>Utilisation de RubyGems pour obtenir le package

1. Ouvrez une interface de ligne de commande, telle que PowerShell (Windows), Terminal (Mac) ou Bash (Unix).
2. Tapez `gem install Azure` dans la fenêtre de commande pour installer gem et les dépendances.

### <a name="import-the-package"></a>Importation du package

À l'aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser le stockage :

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Configuration d'une connexion Stockage Azure

Le module Azure lit les variables d’environnement `AZURE_STORAGE_ACCOUNT` et `AZURE_STORAGE_ACCESS_KEY` pour obtenir les informations nécessaires à la connexion à votre compte Stockage Azure. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte avant d'utiliser `Azure::QueueService` avec le code suivant :

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Pour obtenir ces valeurs à partir d’un compte de stockage classique ou Resource Manager sur le portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Accédez au compte de stockage que vous souhaitez utiliser.
3. Dans le panneau **Paramètres** à droite, cliquez sur **Clés d’accès**.
4. Dans le panneau **Clés d’accès** qui apparaît, la clé d’accès 1 et la clé d’accès 2 sont affichées. Vous pouvez utiliser les deux.
5. Cliquez sur l'icône de copie pour copier la clé dans le Presse-papiers.

## <a name="how-to-create-a-queue"></a>Procédure : Créer une file d’attente

Le code suivant crée un objet `Azure::QueueService` , ce qui vous permet d'utiliser les files d'attente.

```ruby
azure_queue_service = Azure::QueueService.new
```

Utilisez la méthode `create_queue()` pour créer une file d’attente comportant le nom spécifié.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Procédure : Insertion d'un message dans une file d'attente

Pour insérer un message dans une file d’attente, utilisez la méthode `create_message()` afin de créer un message et l’ajouter à la file d’attente.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Procédure : Lecture furtive du message suivant

Vous pouvez lire furtivement le message au début de la file d'attente sans l'enlever de la file d'attente en appelant la méthode `peek_messages()`. Par défaut, `peek_messages()` lit furtivement un seul message. Vous pouvez également spécifier le nombre de messages que vous souhaitez lire furtivement.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Procédure : Suppression du message suivant de la file d’attente

Vous pouvez supprimer un message d'une file d'attente en deux étapes.

1. Quand vous appelez `list_messages()`, vous obtenez le message suivant dans une file d’attente par défaut. Vous pouvez également spécifier le nombre de messages que vous souhaitez obtenir. Les messages renvoyés par `list_messages()` deviennent invisibles par les autres codes lisant les messages de cette file d'attente. Vous transmettez le délai d'expiration de la visibilité en secondes en tant que paramètre.
2. Pour finaliser la suppression du message de la file d’attente, vous devez aussi appeler `delete_message()` .

Ce processus de suppression d’un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d’une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle `delete_message()` juste après le traitement du message.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procédure : Modification du contenu d'un message en file d'attente

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Le code suivant utilise la méthode `update_message()` pour mettre à jour un message. La méthode renvoie un tuple qui contient l'accusé pop du message de file d'attente et une valeur `DateTime` UTC représentant le moment où le message sera visible dans la file d'attente.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message",
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Procédure : Options supplémentaires pour la suppression des messages dans la file d'attente

Il existe deux façons de personnaliser la récupération des messages à partir d'une file d'attente.

1. Vous pouvez obtenir un lot de messages.
2. Vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message.

L’exemple de code suivant utilise la méthode `list_messages()` pour obtenir 15 messages en un appel. Ensuite, il imprime et supprime chaque message. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Procédure : Obtention de la longueur de la file d'attente

Vous pouvez obtenir une estimation du nombre de messages dans la file d'attente. La méthode `get_queue_metadata()` retourne le nombre de messages approximatif et les autres métadonnées de file d’attente.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Procédure : Suppression d'une file d'attente

Pour supprimer une file d’attente et tous les messages qu’elle contient, appelez la méthode `delete_queue()` sur l’objet file d’attente.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les bases du stockage des files d'attente, consultez les liens suivants pour apprendre à exécuter les tâches de stockage plus complexes.

- Consultez le [blog de l’équipe Azure Storage](/archive/blogs/windowsazurestorage/)
- Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) sur GitHub.

Pour une comparaison entre Stockage File d'attente Azure abordé dans cet article et les files d’attente de Azure Service Bus abordées dans [Comment utiliser les files d’attente de Service Bus](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/), consultez [Stockage File d'attente Azure et Azure Service Bus : similitudes et différences](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
