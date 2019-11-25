---
title: Configurer votre serveur de configuration dans Azure Spring Cloud | Microsoft Docs
description: Dans ce tutoriel, vous allez découvrir comment configurer un serveur de configuration Spring Cloud pour votre service Azure Spring Cloud sur le portail Azure
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/18/2019
ms.openlocfilehash: 6742e1a5924fdcd1fe00f49ac790209a907d1bac
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132791"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Didacticiel : Configurer un serveur de configuration Spring Cloud pour votre service

Ce tutoriel vous montre comment connecter un serveur de configuration Spring Cloud à votre service Azure Spring Cloud.

La configuration Spring Cloud offre une prise en charge côté serveur et côté client pour une configuration externalisée dans un système distribué. Avec le serveur de configuration, vous disposez d’un emplacement centralisé pour gérer les propriétés externes des applications dans tous les environnements. Pour plus d’informations, consultez les [informations de référence sur le serveur de configuration Spring Cloud](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Prérequis
* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 
* Un service Azure Spring Cloud déjà provisionné et en cours d’exécution.  Suivez ce [guide de démarrage rapide](spring-cloud-quickstart-launch-app-cli.md) pour provisionner et lancer un service Azure Spring Cloud.

## <a name="restriction"></a>Restriction

Certaines restrictions s’appliquent quand vous utilisez le __serveur de configuration__ avec un back-end git. Certaines propriétés sont automatiquement injectées dans votre environnement d’application pour accéder au __serveur de configuration__ et à la __découverte des services__. Si vous configurez aussi ces propriétés dans les fichiers de votre **serveur de configuration**, vous vous exposez à des conflits et à un comportement inattendu. Ces propriétés sont les suivantes : 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```
> [!CAUTION]
> Nous vous __déconseillons__ fortement d’inclure les propriétés ci-dessus dans les fichiers d’application de votre __serveur de configuration__.

## <a name="create-your-config-server-files"></a>Créer les fichiers de votre serveur de configuration

Azure Spring Cloud prend en charge Azure DevOps, GitHub, GitLab et Bitbucket pour le stockage des fichiers de votre serveur de configuration. Une fois que votre dépôt est prêt, créez les fichiers de configuration en suivant les instructions ci-dessous et stockez-les dans celui-ci.

Par ailleurs, certaines propriétés configurables ne sont disponibles que pour certains types. Les sous-sections suivantes listent les propriétés pour chaque type de dépôt.

### <a name="public-repository"></a>Dépôt public

Quand vous utilisez un dépôt public, vos propriétés configurables sont plus limitées.

Toutes les propriétés configurables servant à configurer le dépôt `Git` public sont listées ci-dessous.

> [!NOTE]
> L’utilisation d’un trait d’union (« - ») pour séparer les mots est la seule convention d’affectation de noms actuellement prise en charge. Par exemple, vous pouvez utiliser `default-label` mais pas `defaultLabel`.

| Propriété        | Obligatoire | Fonctionnalité                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | L’`uri` du dépôt `Git` utilisé comme back-end du serveur de configuration doit commencer par `http://`, `https://`, `git@` ou `ssh://`. |
| `default-label` | `no`     | L’étiquette par défaut du dépôt `Git` doit être l’élément `branch name`, `tag name` ou `commit-id` du dépôt. |
| `search-paths`  | `no`     | Tableau de chaînes utilisé pour effectuer une recherche dans les sous-répertoires du dépôt `Git`. |

------

### <a name="private-repository-with-ssh-authentication"></a>Dépôt privé avec authentification SSH

Toutes les propriétés configurables servant à configurer un dépôt `Git` privé avec `Ssh` sont listées ci-dessous.

> [!NOTE]
> L’utilisation d’un trait d’union (« - ») pour séparer les mots est la seule convention d’affectation de noms actuellement prise en charge. Par exemple, vous pouvez utiliser `default-label` mais pas `defaultLabel`.

| Propriété                   | Obligatoire | Fonctionnalité                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | L’`uri` du dépôt `Git` utilisé comme back-end du serveur de configuration doit commencer par `http://`, `https://`, `git@` ou `ssh://`. |
| `default-label`            | `no`     | L’étiquette par défaut du dépôt `Git` doit être l’élément `branch name`, `tag name` ou `commit-id` du dépôt. |
| `search-paths`             | `no`     | Tableau de chaînes utilisé pour effectuer une recherche dans les sous-répertoires du dépôt `Git`. |
| `private-key`              | `no`     | Clé privée `Ssh` pour accéder au dépôt `Git`, __obligatoire__ quand l’`uri` commence par `git@` ou `ssh://`. |
| `host-key`                 | `no`     | La clé hôte du serveur du dépôt Git ne doit pas inclure le préfixe d’algorithme tel qu’il est couvert par `host-key-algorithm`. |
| `host-key-algorithm`       | `no`     | L’algorithme de la clé d’hôte doit être `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` ou `ecdsa-sha2-nistp521`. Il est obligatoire seulement si `host-key` existe. |
| `strict-host-key-checking` | `no`     | Indique si le serveur de configuration ne peut pas démarrer lorsqu'il utilise la `host-key` privée. Doit correspondre à `true` (valeur par défaut) ou `false`. |

-----

### <a name="private-repository-with-basic-authentication"></a>Dépôt privé avec authentification de base

Toutes les propriétés configurables servant à configurer un dépôt Git privé avec l’authentification de base sont listées ci-dessous.

> [!NOTE]
> L’utilisation d’un trait d’union (« - ») pour séparer les mots est la seule convention d’affectation de noms actuellement prise en charge. Par exemple, utilisez `default-label` et pas `defaultLabel`.

| Propriété        | Obligatoire | Fonctionnalité                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | L’`uri` du dépôt `Git` utilisé comme back-end du serveur de configuration doit commencer par `http://`, `https://`, `git@` ou `ssh://`. |
| `default-label` | `no`     | L’étiquette par défaut du dépôt `Git` doit être l’élément `branch name`, `tag name` ou `commit-id` du dépôt. |
| `search-paths`  | `no`     | Tableau de chaînes utilisé pour effectuer une recherche dans les sous-répertoires du dépôt `Git`. |
| `username`      | `no`     | `username` utilisé pour accéder au serveur du dépôt `Git`, __obligatoire__ lorsque le serveur du dépôt `Git` prend en charge `Http Basic Authentication`. |
| `password`      | `no`     | Mot de passe utilisé pour accéder au serveur du dépôt `Git`, __obligatoire__ lorsque le serveur du dépôt `Git` prend en charge `Http Basic Authentication`. |

> [!NOTE]
> De nombreux serveurs de dépôt `Git` prennent en charge l’utilisation des jetons à la place des mots de passe pour `HTTP Basic Authentication`. Certains dépôts, tels que GitHub, permettent aux jetons d’être conservés indéfiniment. Toutefois, certains serveurs de dépôt Git, notamment Azure DevOps, forcent les jetons à expirer en quelques heures. Les dépôts qui entraînent l’expiration des jetons ne doivent pas utiliser l’authentification par jeton avec Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Dépôts Git avec modèle

Toutes les propriétés configurables servant à configurer les dépôts Git avec un modèle sont listées ci-dessous.

> [!NOTE]
> L’utilisation d’un trait d’union (« - ») pour séparer les mots est la seule convention d’affectation de noms actuellement prise en charge. Par exemple, utilisez `default-label` et pas `defaultLabel`.

| Propriété                           | Obligatoire         | Fonctionnalité                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | Carte présentant les paramètres d’un dépôt `Git` avec un nom donné. |
| `repos."uri"`                      | `yes` pour `repos` | L’`uri` du dépôt `Git` utilisé comme back-end du serveur de configuration doit commencer par `http://`, `https://`, `git@` ou `ssh://`. |
| `repos."name"`                     | `yes` pour `repos` | Nom permettant d’identifier un dépôt `Git`, __obligatoire__ uniquement si `repos` existe. Pour l’exemple précédent, `team-A`, `team-B`. |
| `repos."pattern"`                  | `no`             | Tableau de chaînes utilisé pour faire correspondre un nom d’application. Pour chaque modèle, utilisez le format `{application}/{profile}` avec des caractères génériques. |
| `repos."default-label"`            | `no`             | L’étiquette par défaut du dépôt `Git` doit être l’élément `branch name`, `tag name` ou `commit-id` du dépôt. |
| `repos."search-paths`"             | `no`             | Tableau de chaînes utilisé pour effectuer une recherche dans les sous-répertoires du dépôt `Git`. |
| `repos."username"`                 | `no`             | `username` utilisé pour accéder au serveur du dépôt `Git`, __obligatoire__ lorsque le serveur du dépôt `Git` prend en charge `Http Basic Authentication`. |
| `repos."password"`                 | `no`             | Mot de passe utilisé pour accéder au serveur du dépôt `Git`, __obligatoire__ lorsque le serveur du dépôt `Git` prend en charge `Http Basic Authentication`. |
| `repos."private-key"`              | `no`             | Clé privée `Ssh` pour accéder au dépôt `Git`, __obligatoire__ quand l’`uri` commence par `git@` ou `ssh://`. |
| `repos."host-key"`                 | `no`             | La clé hôte du serveur du dépôt Git ne doit pas inclure le préfixe d’algorithme tel qu’il est couvert par `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | `no`             | L’algorithme de la clé d’hôte doit être `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` ou `ecdsa-sha2-nistp521`. N’est __obligatoire__ que si `host-key` existe. |
| `repos."strict-host-key-checking"` | `no`             | Indique si le serveur de configuration ne peut pas démarrer lorsqu'il utilise la `host-key` privée. Doit correspondre à `true` (valeur par défaut) ou `false`. |

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>Attacher le dépôt de votre serveur de configuration à Azure Spring Cloud

Maintenant que vous avez enregistré vos fichiers de configuration dans un dépôt, vous devez connecter Azure Spring Cloud à ce dernier.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez à la page **Vue d’ensemble** de votre instance Azure Spring Cloud.

1. Sélectionnez le service à configurer.

1. Dans la page du service, sélectionnez l’onglet **Config Server** (Serveur de configuration) sous le titre **Settings** (Paramètres) dans le menu de gauche.

![capture d’écran de la fenêtre](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="input-repository-information-directly-to-the-azure-portal"></a>Entrer les informations de dépôt directement dans le Portail Azure

#### <a name="default-repository"></a>Dépôt par défaut

* Dépôt public : Dans la section **Dépôt par défaut**, collez l’URI du dépôt dans la section**URI**.  Définissez l'**Étiquette** sur `config`. Assurez-vous que le paramètre **Authentification** est défini sur **Public**, puis sélectionnez **Appliquer** pour terminer. 

* Dépôt privé : Azure Spring Cloud prend en charge l’authentification par mot de passe/jeton de base et SSH.

    * Authentification de base : Dans la section **Dépôt par défaut**, collez l’URI du dépôt dans la section **URI**, puis cliquez sur **Authentification**. Sélectionnez **De base** comme **Type d’authentification** et entrez votre nom d’utilisateur et votre mot de passe/jeton pour accorder l’accès à Azure Spring Cloud. Cliquez sur **OK** et **Appliquer** pour terminer la configuration de votre serveur de configuration.

    ![capture d’écran de la fenêtre](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Certains serveurs de dépôt Git comme GitHub utilisent un `personal-token` ou un `access-token` comme mot de passe pour l'**Authentification de base**. Vous pouvez utiliser ce type de jeton comme mot de passe dans Azure Spring Cloud car il n’expire jamais. Cela étant, pour les autres serveurs de dépôt git tels que BitBucket et Azure DevOps, le `access-token` expire sous une ou deux heures. Dès lors, cette option n’est pas viable si vous utilisez ces serveurs de dépôt avec Azure Spring Cloud.]

    * SSH : Dans la section **Dépôt par défaut**, collez l’URI du dépôt dans la section **URI**, puis cliquez sur **Authentification**. Sélectionnez **SSH** comme **Type d'authentification**, puis entrez votre **Clé privée**. Vous pouvez également spécifier la **Clé d’hôte** et l'**Algorithme de la clé d'hôte**. Veillez à inclure votre clé publique dans le dépôt de votre serveur de configuration. Cliquez sur **OK** et **Appliquer** pour terminer la configuration de votre serveur de configuration.

    ![capture d’écran de la fenêtre](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Dépôt de modèles

Si vous souhaitez utiliser un **Dépôt de modèles** pour configurer votre service, spécifiez l'**URI** et l'**Authentification** de la même manière que pour le **Dépôt par défaut**. Veillez à inclure un **Nom** pour votre modèle, puis cliquez sur **Appliquer** pour l’attacher à votre instance. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Entrer les informations de dépôt dans un fichier YAML

Si vous avez écrit un fichier YAML avec les paramètres de votre dépôt, vous pouvez importer votre fichier YAML à partir de votre ordinateur local vers Azure Spring Cloud. Un fichier YAML pour un dépôt privé avec authentification de base se présente comme suit :

```yml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Cliquez sur le bouton **Importer les paramètres**, puis sélectionnez le fichier `.yml` dans le répertoire de votre projet. Cliquez sur **Importer**. Une opération `async` à partir de vos **notifications** s’affiche. Après une ou deux minutes, elle doit aboutir.

![capture d’écran de la fenêtre](media/spring-cloud-tutorial-config-server/local-yml-success.png)


Les informations de votre fichier YAML doivent s'afficher dans le portail Azure. Cliquez sur **Appliquer** pour terminer. 


## <a name="delete-your-app-configuration"></a>Supprimer la configuration de votre application

Une fois que vous avez enregistré un fichier de configuration, le bouton **Delete app configuration** (Supprimer la configuration de l’application) apparaît sous l’onglet **Configuration**. Les paramètres existants sont entièrement effacés. Cette opération est à envisager si vous souhaitez connecter votre serveur de configuration à une autre source, par exemple pour passer de GitHub à Azure DevOps.



## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à activer et configurer le serveur de configuration. Pour en savoir plus sur la gestion de votre application, passez au tutoriel sur la mise à l’échelle manuelle d’une application.

> [!div class="nextstepaction"]
> [Découvrez comment mettre à l’échelle manuellement votre application Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md).

