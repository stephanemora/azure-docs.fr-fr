---
title: Azure Container Registry - Forum aux questions
description: Réponses aux questions fréquemment posées relatives au service Azure Container Registry
services: container-registry
author: sajayantony
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 5/13/2019
ms.author: sajaya
ms.openlocfilehash: 86efb6b655405500f994a5a5ec7acbd18c645004
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957849"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Forum aux questions sur Azure Container Registry

Cet article traite des questions fréquemment posées et les problèmes connus relatifs à Azure Container Registry.

## <a name="resource-management"></a>Gestion des ressources

- [Puis-je créer un Registre de conteneurs Azure à l’aide d’un modèle Resource Manager ?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Existe-t-il une analyse pour les images dans ACR des vulnérabilités de sécurité ?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Comment configurer des Kubernetes avec Azure Container Registry ?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Comment obtenir des informations d’identification administrateur pour un Registre de conteneurs ?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Comment obtenir des informations d’identification administrateur dans un modèle Resource Manager ?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Suppression de la réplication échoue avec l’état d’interdit, bien que la réplication est supprimée à l’aide de l’interface CLI ou Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Puis-je créer un Registre de conteneurs Azure à l’aide d’un modèle Resource Manager ?

Oui. Voici [un modèle](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json) que vous pouvez utiliser pour créer un Registre.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Existe-t-il une analyse pour les images dans ACR des vulnérabilités de sécurité ?

Oui. Consultez la documentation de [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) et [Aqua](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Comment configurer des Kubernetes avec Azure Container Registry ?

Consultez la documentation de [Kubernetes](http://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) et des étapes de [Azure Kubernetes Service](container-registry-auth-aks.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Comment obtenir des informations d’identification administrateur pour un Registre de conteneurs ?

> [!IMPORTANT]
> Le compte d’utilisateur administrateur est conçu pour un seul utilisateur d’accéder au Registre, principalement pour des tests. Nous ne recommandons pas de partager les informations d’identification du compte d’administrateur avec plusieurs utilisateurs. Une identité individuelle est recommandée pour les utilisateurs et principaux du service pour les scénarios sans périphérique de contrôle. Consultez [vue d’ensemble de l’authentification](container-registry-authentication.md).

Avant d’obtenir les informations d’identification administrateur, assurez-vous que l’utilisateur admin du Registre est activé.

Pour obtenir des informations d’identification à l’aide de l’interface CLI :

```azurecli
az acr credential show -n myRegistry
```

Utilisation d’Azure Powershell :

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Comment obtenir des informations d’identification administrateur dans un modèle Resource Manager ?

> [!IMPORTANT]
> Le compte d’utilisateur administrateur est conçu pour un seul utilisateur d’accéder au Registre, principalement pour des tests. Nous ne recommandons pas de partager les informations d’identification du compte d’administrateur avec plusieurs utilisateurs. Une identité individuelle est recommandée pour les utilisateurs et principaux du service pour les scénarios sans périphérique de contrôle. Consultez [vue d’ensemble de l’authentification](container-registry-authentication.md).

Avant d’obtenir les informations d’identification administrateur, assurez-vous que l’utilisateur admin du Registre est activé.

Pour obtenir le premier mot de passe :

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Pour obtenir le second mot de passe :

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Suppression de la réplication échoue avec l’état d’interdit, bien que la réplication est supprimée à l’aide de l’interface CLI ou Azure PowerShell

L’erreur se produit lorsque l’utilisateur dispose des autorisations sur un Registre, mais ne dispose pas des autorisations au niveau du lecteur sur l’abonnement. Pour résoudre ce problème, affecter des autorisations de lecture sur l’abonnement à l’utilisateur :


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

## <a name="registry-operations"></a>Opérations du registre

- [Comment accéder à Docker Registry HTTP API V2 ?](#how-do-i-access-docker-registry-http-api-v2)
- [Comment supprimer tous les manifestes ne sont pas référencés par n’importe quelle balise dans un référentiel ?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Pourquoi ne l’utilisation du quota du Registre réduit pas après la suppression d’images ?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Comment valider les modifications de quota de stockage ?](#how-do-i-validate-storage-quota-changes)
- [Comment puis-je authentifier avec mon Registre lors de l’exécution de l’interface CLI dans un conteneur ?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Azure Container Registry offertes par configuration de TLS v1.2 uniquement et comment activer TLS v1.2 ?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Azure Container Registry prend-elle en charge le contenu de confiance ?](#does-azure-container-registry-support-content-trust)
- [Comment autoriser l’accès aux images pull ou push sans l’autorisation de gérer la ressource de Registre ?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Comment activer la mise en quarantaine automatique de l’image pour un Registre](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Comment accéder à Docker Registry HTTP API V2 ?

ACR prend en charge Docker Registry HTTP API V2. Les API sont accessibles au `https://<your registry login server>/v2/`. Exemple : `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Comment supprimer tous les manifestes ne sont pas référencés par n’importe quelle balise dans un référentiel ?

Si vous êtes sur bash :

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Pour Powershell :

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Remarque : Vous pouvez ajouter `-y` dans la commande de suppression pour ignorer la confirmation.

Pour plus d’informations, consultez [supprimer des images de conteneur dans Azure Container Registry](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Pourquoi ne l’utilisation du quota du Registre réduit pas après la suppression d’images ?

Cette situation peut se produire si les couches sous-jacentes sont toujours référencés par d’autres images de conteneur. Si vous supprimez une image sans aucune référence, l’utilisation du Registre mises à jour dans quelques minutes.

### <a name="how-do-i-validate-storage-quota-changes"></a>Comment valider les modifications de quota de stockage ?

Créer une image avec une couche de 1 Go à l’aide du fichier docker suivant. Cela garantit que l’image a une couche qui n’est pas partagée par toute autre image dans le Registre.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Générez et envoyez l’image vers votre Registre à l’aide de l’interface CLI docker.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Vous devez être en mesure de voir que l’utilisation du stockage a augmenté dans le portail Azure, ou vous pouvez interroger l’utilisation à l’aide de l’interface CLI.

```bash
az acr show-usage -n myregistry
```

Supprimez l’image à l’aide de l’interface CLI ou le portail et vérifiez l’utilisation de la mise à jour dans quelques minutes.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Comment puis-je authentifier avec mon Registre lors de l’exécution de l’interface CLI dans un conteneur ?

Vous devez exécuter le conteneur Azure CLI en montant le socket Docker :

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Dans le conteneur, vous devez installer `docker`:

```bash
apk --update add docker
```

Puis vous authentifier avec votre Registre :

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Azure Container Registry offertes par configuration de TLS v1.2 uniquement et comment activer TLS v1.2 ?

Oui. Activer TLS à l’aide de n’importe quel client docker récente (version 18.03.0 et versions ultérieures). 

### <a name="does-azure-container-registry-support-content-trust"></a>Azure Container Registry prend-elle en charge le contenu de confiance ?

Oui, vous pouvez utiliser des images approuvés dans Azure Container Registry, étant donné que le [Docker notaire](https://docs.docker.com/notary/getting_started/) a été intégré et peut être activée. Pour plus d’informations, consultez [approuver contenu dans Azure Container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Où se trouve le fichier pour l’empreinte numérique trouvée ?

Sous `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Clés publiques et les certificats de tous les rôles (à l’exception des rôles de délégation) sont stockés dans le `root.json`.
* Clés publiques et les certificats du rôle de délégation sont stockés dans le fichier JSON de son rôle parent (par exemple `targets.json` pour le `targets/releases` rôle).

Il est recommandé de vérifier ces clés publiques et les certificats après la vérification TUF globale effectuée par le client Docker et notaire.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Comment autoriser l’accès aux images pull ou push sans l’autorisation de gérer la ressource de Registre ?

Prend en charge de l’ACR [des rôles personnalisés](container-registry-roles.md) qui fournissent différents niveaux d’autorisations. Plus précisément, `AcrPull` et `AcrPush` rôles permettent aux utilisateurs pour extraction et/ou push images sans l’autorisation de gérer la ressource de Registre dans Azure.

* Portail Azure : Votre Registre -> contrôle d’accès (IAM) -> Ajouter (sélectionnez `AcrPull` ou `AcrPush` pour le rôle).
* Interface de ligne de commande Azure : Rechercher l’ID de ressource du Registre en exécutant la commande suivante :

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Vous pouvez alors assigner le `AcrPull` ou `AcrPush` rôle à un utilisateur (l’exemple suivant utilise `AcrPull`) :

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  Ou bien, si vous attribuez le rôle à un principal du service identifié par son ID d’application :

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

La personne responsable est en mesure d’authentifier et accéder à des images dans le Registre.

* S’authentifier sur un Registre :
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Pour les référentiels de la liste :

  ```azurecli
  az acr repository list -n myRegistry
  ```

 Pour extraire une image :
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

À l’aide de seulement le `AcrPull` ou `AcrPush` rôle, la personne responsable n’est pas autorisé à gérer la ressource de Registre dans Azure. Par exemple, `az acr list` ou `az acr show -n myRegistry` n’affiche pas le Registre.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Comment activer la mise en quarantaine automatique de l’image pour un Registre ?

Mise en quarantaine de l’image est actuellement une fonctionnalité préliminaire d’ACR. Vous pouvez activer le mode de mise en quarantaine d’un Registre afin que que les images qui ont réussi l’analyse de sécurité sont visibles par les utilisateurs normaux. Pour plus d’informations, consultez le [référentiel ACR GitHub](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics"></a>Diagnostics

- [extraction de docker échoue avec l’erreur : net/http : demande annulée en attendant la connexion (Client.Timeout dépassé en attendant les en-têtes)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [push de docker réussit, mais par extraction de docker échoue avec l’erreur : non autorisé : authentification requise](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Activer et obtenir les journaux de débogage du démon docker](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Nouvelles autorisations utilisateur ne peuvent pas être efficaces immédiatement après la mise à jour](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Informations d’authentification ne sont pas spécifiées dans le format correct sur les appels directs de l’API REST](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Pourquoi ne le portail Azure répertorie pas tous mes dépôts ou balises ?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>extraction de docker échoue avec l’erreur : net/http : demande annulée en attendant la connexion (Client.Timeout dépassé en attendant les en-têtes)

 - Si cette erreur est un problème temporaire, nouvelle tentative réussit. 
 - Si `docker pull` échoue en permanence, il peut y avoir un problème avec le démon docker. Le problème peut généralement être résolu en redémarrant le démon docker. 
 - Si vous continuez à voir ce problème après le redémarrage du démon docker, puis le problème est peut-être certains problèmes de connectivité réseau avec l’ordinateur. Pour vérifier si le réseau d’ordre général sur l’ordinateur est intègre, essayez une commande comme `ping www.bing.com`.
 - Vous devez toujours avoir un mécanisme de nouvelle tentative sur toutes les opérations du client docker.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>push de docker réussit, mais par extraction de docker échoue avec l’erreur : non autorisé : authentification requise

Cette erreur peut se produire avec la version de Red Hat du démon docker, où `--signature-verification` est activée par défaut. Vous pouvez vérifier les options du démon docker pour Red Hat Enterprise Linux (RHEL) ou Fedora en exécutant la commande suivante :

```bash
grep OPTIONS /etc/sysconfig/docker
```

Par exemple, Fedora 28 serveur présente les options du démon docker suivantes :

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

Avec `--signature-verification=false` manquant, `docker pull` échoue avec une erreur similaire à :

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Pour résoudre l’erreur :
1. Ajoutez l’option `--signature-verification=false` au fichier de configuration du démon docker `/etc/sysconfig/docker`. Exemple :

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Redémarrez le service du démon docker en exécutant la commande suivante :

  ```bash
  sudo systemctl restart docker.service
  ```

Détails de `--signature-verification` peut être trouvé en exécutant `man dockerd`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Activer et obtenir les journaux de débogage du démon docker  

Démarrer `dockerd` avec la `debug` option. Tout d’abord, créez le fichier de configuration du démon docker (`/etc/docker/daemon.json`) s’il n’existe et ajouter la `debug` option :

```json
{   
    "debug": true   
}
```

Ensuite, redémarrez le démon. Par exemple, avec Ubuntu 14.04 :

```bash
sudo service docker restart
```

Plus de détails, voir la [documentation Docker](https://docs.docker.com/engine/admin/#enable-debugging). 

 * Les journaux peuvent être générés à différents emplacements, selon votre système. Par exemple, pour Ubuntu 14.04, il a `/var/log/upstart/docker.log`.   
Consultez [documentation Docker](https://docs.docker.com/engine/admin/#read-the-logs) pour plus d’informations.    

 * Pour Docker pour Windows, les journaux sont générés sous % LOCALAPPDATA%/docker/. Il ne peut toutefois pas contenir toutes les informations de débogage encore.   

   Pour accéder au journal du démon complète, vous devrez peut-être quelques étapes supplémentaires :

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Maintenant vous avez accès à tous les fichiers de la machine virtuelle exécutant `dockerd`. Le journal est à `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nouvelles autorisations utilisateur ne peuvent pas être efficaces immédiatement après la mise à jour

Lorsque vous accordez des nouvelles autorisations (nouveaux rôles) à un principal de service, la modification peuvent prendront effet immédiatement. Il existe deux raisons possibles :

* Délai attribution de rôle Azure Active Directory. Normalement, il est rapide, mais il peut prendre quelques minutes en raison du délai de propagation.
* Délai d’autorisation sur le serveur de jeton ACR. Cette opération peut prendre jusqu'à 10 minutes. Pour atténuer, vous pouvez `docker logout` et ensuite s’authentifier à nouveau dans le même utilisateur après 1 minute :

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Actuellement ACR ne prend en charge la suppression de réplication de base par les utilisateurs. La solution de contournement consiste à inclure la réplication accueil créer dans le modèle mais ignorer de sa création en ajoutant `"condition": false` comme indiqué ci-dessous :

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Informations d’authentification ne sont pas spécifiées dans le format correct sur les appels directs de l’API REST

Vous pouvez rencontrer un `InvalidAuthenticationInfo` erreur, en particulier à l’aide de la `curl` outil avec l’option `-L`, `--location` (pour suivre les redirections).
Par exemple, l’extraction de l’objet blob en utilisant `curl` avec `-L` option et l’authentification de base :

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

peut entraîner la réponse suivante :

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

La cause en est que certains `curl` implémentations suivent les redirections avec les en-têtes à partir de la demande d’origine.

Pour résoudre le problème, vous devez suivre les redirections manuellement sans les en-têtes. Imprimer les en-têtes de réponse avec le `-D -` option de `curl` puis extrayez : le `Location` en-tête :

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Pourquoi ne le portail Azure répertorie pas tous mes dépôts ou balises ? 

Si vous utilisez le navigateur Microsoft Edge, vous pouvez voir au maximum 100 dépôts ou les balises répertoriées. Si votre Registre a plus de 100 dépôts ou les balises, nous vous recommandons d’utiliser le navigateur Firefox ou Chrome tous les répertorier.

## <a name="tasks"></a>Tâches

- [Comment par lots s’exécute Annuler ?](#how-do-i-batch-cancel-runs)
- [Comment inclure le dossier .git dans la commande de génération az acr ?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>Comment par lots s’exécute Annuler ?

Les commandes suivantes annuler toutes les tâches en cours d’exécution dans le Registre spécifié.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Comment inclure le dossier .git dans la commande de génération az acr ?

Si vous passez d’un dossier local source vers le `az acr build` commande, le `.git` dossier exclu le package chargé par défaut. Vous pouvez créer un `.dockerignore` fichier avec le paramètre suivant. Il indique à la commande pour restaurer tous les fichiers sous `.git` dans le package chargé. 

```
!.git/**
```

Ce paramètre s’applique également à la `az acr run` commande.

## <a name="cicd-integration"></a>Intégration CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)


## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus](container-registry-intro.md) sur Azure Container Registry.
