---
title: Configurer votre serveur de configuration dans Azure Spring Cloud | Microsoft Docs
description: Dans ce tutoriel, vous allez découvrir comment configurer un serveur de configuration Spring Cloud pour votre service Azure Spring Cloud sur le portail Azure
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 08/08/2019
ms.openlocfilehash: f959d216859ded54de52de474ee3150f91bcfff1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038651"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Didacticiel : Configurer un serveur de configuration Spring Cloud pour votre service

Ce tutoriel vous montre comment connecter un serveur de configuration Spring Cloud à votre service Azure Spring Cloud.

La configuration Spring Cloud offre une prise en charge côté serveur et côté client pour une configuration externalisée dans un système distribué. Avec le serveur de configuration, vous disposez d’un emplacement centralisé pour gérer les propriétés externes des applications dans tous les environnements. Pour plus d’informations, consultez les [informations de référence sur le serveur de configuration Spring Cloud](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Prérequis
* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 
* Un service Azure Spring Cloud déjà provisionné et en cours d’exécution.  Suivez ce [guide de démarrage rapide](spring-cloud-quickstart-launch-app-cli.md) pour provisionner et lancer un service Azure Spring Cloud.


## <a name="restriction"></a>Restriction

Certaines restrictions s’appliquent quand vous utilisez le __serveur de configuration__ avec un back-end git. Certaines propriétés sont automatiquement injectées dans votre environnement d’application pour accéder au __serveur de configuration__ et à la __découverte des services__. Si vous configurez aussi ces propriétés dans les fichiers de votre **serveur de configuration**, vous vous exposez à des conflits et à un comportement inattendu.  Ces propriétés sont les suivantes : 

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

Azure Spring Cloud prend en charge Azure DevOps, GitHub, GitLab et Bitbucket pour le stockage des fichiers de votre serveur de configuration.  Une fois que votre dépôt est prêt, créez les fichiers de configuration en suivant les instructions ci-dessous et stockez-les dans celui-ci.

Par ailleurs, certaines propriétés configurables ne sont disponibles que pour certains types. Les sous-sections suivantes listent les propriétés pour chaque type de dépôt.


### <a name="public-repository"></a>Dépôt public

Quand vous utilisez un dépôt public, vos propriétés configurables sont plus limitées.

Toutes les propriétés configurables servant à configurer le dépôt `Git` public sont listées ci-dessous.

> [!NOTE]
> Pour l’heure, la convention d’affectation de noms n’accepte que le trait d’union (« - ») comme séparateur de mots. Par exemple, utilisez `default-label` et non `defaultLabel`.

| Propriété        | Obligatoire | Fonctionnalité                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | L’`uri` du dépôt `Git` utilisé comme back-end du serveur de configuration doit commencer par `http://`, `https://`, `git@` ou `ssh://`. |
| `default-label` | `no`     | L’étiquette par défaut du dépôt `Git` doit être l’élément `branch name`, `tag name` ou `commit-id` du dépôt. |
| `search-paths`  | `no`     | Tableau de chaînes utilisé pour effectuer une recherche dans les sous-répertoires d’un dépôt `Git`. |

------

### <a name="private-repository-with-ssh-authentication"></a>Dépôt privé avec authentification SSH

Toutes les propriétés configurables servant à configurer un dépôt `Git` privé avec `Ssh` sont listées ci-dessous.

> [!NOTE]
> Pour l’heure, la convention de nommage n’accepte que le trait d’union (« - ») comme séparateur de mots. Par exemple, utilisez `default-label` et non `defaultLabel`.

| Propriété                   | Obligatoire | Fonctionnalité                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | L’`uri` du dépôt `Git` utilisé comme back-end du serveur de configuration doit commencer par `http://`, `https://`, `git@` ou `ssh://`. |
| `default-label`            | `no`     | L’étiquette par défaut du dépôt `Git` doit être l’élément `branch name`, `tag name` ou `commit-id` du dépôt. |
| `search-paths`             | `no`     | Tableau de chaînes utilisé pour effectuer une recherche dans les sous-répertoires d’un dépôt `Git`. |
| `private-key`              | `no`     | Clé privée `Ssh` pour accéder au dépôt `Git`, __obligatoire__ quand l’`uri` commence par `git@` ou `ssh://`. |
| `host-key`                 | `no`     | La clé hôte du serveur du dépôt Git ne doit pas inclure le préfixe d’algorithme tel qu’il est couvert par `host-key-algorithm`. |
| `host-key-algorithm`       | `no`     | L’algorithme de la clé d’hôte doit être l’un des suivants : `ssh-dss`. `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` et `ecdsa-sha2-nistp521`. Il est obligatoire seulement si `host-key` existe. |
| `strict-host-key-checking` | `no`     | Indique si le serveur de configuration ne peut pas démarrer quand il utilise le `host-key` fourni ; sa valeur doit être `true` (par défaut) ou `false`. |

-----

### <a name="private-repository-with-basic-authentication"></a>Dépôt privé avec authentification de base

Toutes les propriétés configurables servant à configurer un dépôt Git privé avec l’authentification de base sont listées ci-dessous.

> [!NOTE]
> Pour l’heure, la convention de nommage n’accepte que le trait d’union (« - ») comme séparateur de mots. Par exemple, vous pouvez utiliser `default-label` mais pas `defaultLabel`.

| Propriété        | Obligatoire | Fonctionnalité                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | L’`uri` du dépôt `Git` utilisé comme back-end du serveur de configuration doit commencer par `http://`, `https://`, `git@` ou `ssh://`. |
| `default-label` | `no`     | L’étiquette par défaut du dépôt `Git` doit être l’élément `branch name`, `tag name` ou `commit-id` du dépôt. |
| `search-paths`  | `no`     | Tableau de chaînes utilisé pour effectuer une recherche dans les sous-répertoires d’un dépôt `Git`. |
| `username`      | `no`     | `username` utilisé pour accéder au serveur du dépôt `Git`, __obligatoire__ si le serveur du dépôt `Git` prend en charge `Http Basic Authentication`. |
| `password`      | `no`     | Mot de passe utilisé pour accéder au serveur du dépôt `Git`, __obligatoire__ si le serveur du dépôt `Git` prend en charge `Http Basic Authentication`. |

> [!NOTE]
> Certains serveurs de dépôt `Git`, comme GitHub, prennent en charge un jeton personnel (« personal-token ») ou un jeton d’accès (« access-token ») en tant que mot de passe pour `HTTP Basic Authentication`. Vous pouvez ici aussi utiliser ce type de jeton comme mot de passe ; le jeton personnel ou le jeton d’accès n’expirera pas. Mais pour les serveurs de dépôt Git tels que BitBucket et Azure DevOps, le jeton expire au bout d’une ou deux heures, ce qui rend cette option non viable pour une utilisation avec Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Dépôts Git avec modèle

Toutes les propriétés configurables servant à configurer les dépôts Git avec un modèle sont listées ci-dessous.

> [!NOTE]
> Pour l’heure, la convention de nommage n’accepte que le trait d’union (« - ») comme séparateur de mots. Par exemple, vous pouvez utiliser `default-label` mais pas `defaultLabel`.

| Propriété                           | Obligatoire         | Fonctionnalité                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | Un mappage se compose de paramètres de dépôt `Git` avec un nom donné. |
| `repos."uri"`                      | `yes` pour `repos` | L’`uri` du dépôt `Git` utilisé comme back-end du serveur de configuration doit commencer par `http://`, `https://`, `git@` ou `ssh://`. |
| `repos."name"`                     | `yes` pour `repos` | Nom permettant d’identifier un dépôt `Git`, __obligatoire__ uniquement si `repos` existe. Pour l’exemple précédent, `team-A`, `team-B`. |
| `repos."pattern"`                  | `no`             | Tableau de chaînes utilisé pour faire correspondre le nom de l’application ; pour chaque modèle, accepte le format `{application}/{profile}` avec des caractères génériques. |
| `repos."default-label"`            | `no`             | L’étiquette par défaut du dépôt `Git` doit être l’élément `branch name`, `tag name` ou `commit-id` du dépôt. |
| `repos."search-paths`"             | `no`             | Tableau de chaînes utilisé pour effectuer une recherche dans les sous-répertoires d’un dépôt `Git`. |
| `repos."username"`                 | `no`             | `username` utilisé pour accéder au serveur du dépôt `Git`, __obligatoire__ si le serveur du dépôt `Git` prend en charge `Http Basic Authentication`. |
| `repos."password"`                 | `no`             | Mot de passe utilisé pour accéder au serveur du dépôt `Git`, __obligatoire__ si le serveur du dépôt `Git` prend en charge `Http Basic Authentication`. |
| `repos."private-key"`              | `no`             | Clé privée `Ssh` pour accéder au dépôt `Git`, __obligatoire__ quand l’`uri` commence par `git@` ou `ssh://`. |
| `repos."host-key"`                 | `no`             | La clé hôte du serveur du dépôt Git ne doit pas inclure le préfixe d’algorithme tel qu’il est couvert par `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | `no`             | L’algorithme de la clé d’hôte doit être l’un des suivants : `ssh-dss`. `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` et `ecdsa-sha2-nistp521`. N’est __obligatoire__ que si `host-key` existe. |
| `repos."strict-host-key-checking"` | `no`             | Indique si le serveur de configuration ne peut pas démarrer quand il utilise le `host-key` fourni ; sa valeur doit être `true` (par défaut) ou `false`. |

### <a name="import-applicationyml-file-from-spring-cloud-config"></a>Importer un fichier `application.yml` à partir de la configuration Cloud Spring

Vous pouvez importer certains paramètres de serveur de configuration par défaut directement à partir du site web [Spring Cloud Config](https://spring.io/projects/spring-cloud-config). Sachant que vous pouvez faire cela directement à partir du portail Azure, vous n’avez à ce stade aucune préparation à faire sur les fichiers de votre serveur de configuration ou votre dépôt.

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>Attacher le dépôt de votre serveur de configuration à Azure Spring Cloud

Maintenant que vous avez enregistré vos fichiers de configuration dans un dépôt, vous devez connecter Azure Spring Cloud à ce dernier.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez à la page **Vue d’ensemble** de votre instance Azure Spring Cloud.

1. Accédez à l’onglet **Config Server** (Serveur de configuration) sous l’en-tête **Paramètres** dans le menu de gauche.

### <a name="public-repository"></a>Dépôt public

Si votre dépôt est public, cliquez simplement sur le bouton **Public** et collez l’URL.

### <a name="private-repository"></a>Dépôt privé

Azure Spring Cloud prend en charge l’authentification SSH. Suivez les instructions fournies sur le portail Azure pour ajouter la clé publique à votre dépôt. Ensuite, veillez à inclure votre clé privée dans le fichier de configuration.

Cliquez sur **Appliquer** pour terminer la configuration de votre serveur de configuration.


## <a name="delete-your-app-configuration"></a>Supprimer la configuration de votre application

Une fois que vous avez enregistré un fichier de configuration, le bouton **Delete app configuration** (Supprimer la configuration de l’application) apparaît sous l’onglet **Configuration**. Les paramètres existants sont entièrement effacés. Cette opération est à envisager si vous souhaitez connecter votre serveur de configuration à une autre source, par exemple pour passer de GitHub à Azure DevOps.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à activer et configurer le serveur de configuration. Pour en savoir plus sur la gestion de votre application, passez au tutoriel sur la mise à l’échelle manuelle d’une application.

> [!div class="nextstepaction"]
> [Découvrez comment mettre à l’échelle manuellement votre application Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md).

