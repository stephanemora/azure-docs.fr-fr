---
title: Fichier Include
description: Fichier Include
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 07/23/2021
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 463d251cc46e0a5735b1b5146bac30b3bc506f46
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674367"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Copier vos informations d’identification depuis le portail Azure

Lorsque l’exemple d’application effectue une requête auprès du stockage Azure, il doit être autorisé. Pour autoriser une demande, ajoutez les informations d’identification de votre compte de stockage à l’application sous la forme d’une chaîne de connexion. Pour afficher les informations d’identification de votre compte de stockage, effectuez les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Recherchez votre compte de stockage.
3. Dans le volet de menu du compte de stockage, sous **Sécurité + réseau**, sélectionnez **Clés d’accès**. Ici, vous pouvez afficher les clés d’accès du compte et la chaîne de connexion complète pour chaque clé.

    ![Capture d’écran montrant où se trouvent les paramètres de clé d’accès dans le portail Azure](./media/storage-access-keys-portal/portal-access-key-settings.png)
 
1. Dans le volet **Clés d’accès**, sélectionnez **Afficher les clés**.
1. Dans la section **clé1**, recherchez la valeur **Chaîne de connexion**. Sélectionnez l’icône **Copier dans le Presse-papiers** pour copier la chaîne de connexion. Vous ajouterez la valeur de chaîne de connexion à une variable d’environnement dans la section suivante.

    ![Capture d’écran montrant comment copier une chaîne de connexion à partir du portail Azure](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage

Après avoir copié la chaîne de connexion, écrivez-la dans une variable d’environnement sur l’ordinateur local exécutant l’application. Pour définir la variable d’environnement, ouvrez une fenêtre de console et suivez les instructions pour votre système d’exploitation. Remplacez `<yourconnectionstring>` par votre chaîne de connexion.

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

Après avoir ajouté la variable d’environnement dans Windows, vous devez démarrer une nouvelle instance de la fenêtre de commande.

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Redémarrer des programmes

Après avoir ajouté la variable d’environnement, redémarrez tous les programmes en cours d’exécution qui devront la lire. Par exemple, redémarrez l’environnement ou l’éditeur de développement avant de continuer.
