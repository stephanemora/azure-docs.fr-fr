---
title: Guide pratique pour gérer les secrets en utilisant un espace Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, conteneurs
ms.openlocfilehash: 49f53683b2499e790414d139dcb0bc0833005647
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280005"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Guide pratique pour gérer les secrets en utilisant un espace Azure Dev Spaces

Vos services peuvent exiger certains mots de passe, chaînes de connexion et autres secrets, comme c’est le cas avec les bases de données ou d’autres services Azure sécurisés. En définissant les valeurs de ces secrets dans des fichiers de configuration, vous pouvez les rendre accessibles dans votre code en tant que variables d’environnement.  Celles-ci doivent être traitées avec soin pour éviter de compromettre la sécurité des secrets.

Azure Dev Spaces propose deux options rationalisées recommandées pour le stockage des secrets dans les graphiques Helm générés par les outils client Azure Dev Spaces : dans le fichier values.dev.yaml ou directement inclus dans azds.yaml. Il est déconseillé de stocker les secrets dans values.yaml. En dehors des deux approches pour les graphiques Helm générés par les outils client définis dans cet article, si vous créez votre propre graphique Helm, vous pouvez l’utiliser directement pour gérer et stocker des secrets.

## <a name="method-1-valuesdevyaml"></a>Méthode 1 : values.dev.yaml
1. Ouvrez VS Code avec votre projet, qui est activé pour Azure Dev Spaces.
2. Ajoutez un fichier nommé _values.dev.yaml_ dans le même dossier que le fichier _azds.yaml_ existant, puis définissez votre clé secrète et les valeurs comme dans l’exemple suivant :

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds.yaml_ fait déjà référence au fichier _values.dev.yaml_ s’il existe. Si vous préférez un nom de fichier différent, mettez à jour la section install.values :

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
    ```
 
4. Modifiez le code de votre service pour référencer ces secrets en tant que variables d’environnement, comme dans l’exemple suivant :

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Mettez à jour les services en cours d’exécution dans votre cluster avec ces modifications. Sur la ligne de commande, exécutez la commande suivante :

    ```
    azds up
    ```
 
6. (Facultatif) À partir de la ligne de commande, vérifiez que ces secrets ont été créés :

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Veillez à ajouter _values.dev.yaml_ au fichier _.gitignore_ pour éviter de valider les secrets dans le contrôle de code source.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Méthode 2 : inclusion directe dans azds.yaml
1.  Dans _azds.yaml_, définissez les secrets sous la section yaml configurations/develop/install. Même s’il est possible d’entrer directement les valeurs des secrets à cet emplacement, cela est déconseillé, car _azds.yaml_ est archivé dans le contrôle de code source. Ajoutez plutôt des espaces réservés en utilisant la syntaxe « $PLACEHOLDER ».

    ```yaml
    configurations:
      develop:
        ...
        install:
          set:
            secrets:
              redis:
                port: "$REDIS_PORT"
                host: "$REDIS_HOST"
                key: "$REDIS_KEY"
    ```
     
2.  Créez un fichier _.env_ dans le même dossier que _azds.yaml_. Entrez les secrets en utilisant la notation standard clé=valeur. Ne validez pas le fichier _.env_ dans le contrôle de code source. (Pour le soustraire au contrôle de code source dans les systèmes de gestion de versions basés sur git, ajoutez-le au fichier _.gitignore_.) L’exemple suivant représente un fichier _.env_ :

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  Modifiez le code source de votre service pour référencer ces secrets dans le code, comme dans l’exemple suivant :

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Mettez à jour les services en cours d’exécution dans votre cluster avec ces modifications. Sur la ligne de commande, exécutez la commande suivante :

    ```
    azds up
    ```

4.  (Facultatif) Examinez les secrets à partir de kubectl :

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>Étapes suivantes

Grâce à ces méthodes, vous pouvez maintenant vous connecter de manière sécurisée à une base de données, un cache Azure pour Redis ou accéder à des services Azure sécurisés.
 
