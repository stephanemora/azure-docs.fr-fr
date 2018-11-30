---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: b6e0e57881154f5885e9f518363eda3c5b1169a0
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52330962"
---
### <a name="install-via-composer"></a>Installation via Composer
1. Créez un fichier nommé **composer.json** à la racine de votre projet et ajoutez-y le code suivant :
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. Téléchargez **[composer.phar][composer-phar]** à la racine du projet.
3. Ouvrez une invite de commande et exécutez la commande suivante à la racine du projet
   
    ```
    php composer.phar install
    ```

Vous pouvez également accéder à la [bibliothèque de client PHP Stockage Azure][php-sdk-github] sur GitHub pour cloner le code source.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar
