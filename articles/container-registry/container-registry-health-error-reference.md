---
title: Référence d’erreur pour le contrôle d’intégrité – Azure Container Registry
description: Codes d’erreur et solutions possibles en lie avec des problèmes détectés en exécutant la commande de diagnostic az acr check-health dans Azure Container Registry
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: fc29b27cbb7eea983140c59529d981ad95c27ae8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67552435"
---
# <a name="health-check-error-reference"></a>Référence d’erreur pour le contrôle d’intégrité

Vous trouverez ci-après des détails sur les codes d’erreur retournés par la commande [az acr check-health][az-acr-check-health]. Pour chaque erreur, les solutions possibles sont répertoriées.

## <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

Cette erreur signifie que client Docker pour l’interface CLI est introuvable. Par conséquent, les contrôles supplémentaires suivants ne sont pas exécutés : recherche de version Docker, évaluation de l’état du démon Docker et exécution de commande pull Docker.

*Solutions potentielles* : installer le client Docker ; ajouter le chemin d’accès de Docker à des variables système.

## <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

Cette erreur signifie que l’état du démon Docker n’est pas disponible, ou qu’il n’était pas accessible via l’interface CLI. Par conséquent, les opérations Docker (tel que `docker login` et `docker pull`) ne sont pas disponibles via l’interface CLI.

*Solutions potentielles* : redémarrer le démon Docker ou vérifier qu’il est correctement installé.

## <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

Cette erreur signifie que l’interface CLI n’a pas pu exécuter la commande `docker --version`.

*Solutions potentielles* : essayer d’exécuter la commande manuellement, s’assurer que dernière version de l’interface CLI est installée, et examiner le message d’erreur.

## <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

Cette erreur signifie que l’interface CLI n’a pas pu extraire d’exemple d’image vers votre environnement.

*Solutions potentielles* : vérifier que tous les composants nécessaires pour extraire une image s’exécutent correctement.

## <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

Cette erreur signifie que l’interface CLI n’a pas trouvé le client Helm, ce qui empêche d’autres opérations Helm.

*Solutions potentielles* : vérifier que le client Helm est installé et que son chemin d’accès a été ajouté aux variables d’environnement système.

## <a name="helmversionerror"></a>HELM_VERSION_ERROR

Cette erreur signifie que l’interface CLI n’a pas pu déterminer la version de Helm installée. Cela peut se produire si la version d’Azure CLI (ou la version de Helm) utilisée est obsolète.

*Solutions potentielles* : mettre à jour vers la dernière version d’Azure CLI ou vers la version recommandée de Helm ; exécuter la commande manuellement et examiner le message d’erreur.

## <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

cette erreur signifie que le DNS pour le serveur de connexion du Registre donné a reçu une requêtes ping mais n’a pas répondu, ce qui signifie qu’il n’est pas disponible. Cela peut indiquer des problèmes de connectivité. Il est également possible que le Registre n’existe pas, que l’utilisateur ne dispose pas des autorisations nécessaires sur le Registre (pour extraire correctement son serveur de connexion) ou que le Registre cible se trouve dans un cloud différent de celui utilisé dans Azure CLI.

*Solutions potentielles* : vérifier la connectivité ; vérifier l’orthographe et l’existence du Registre ; vérifier que l’utilisateur dispose des autorisations appropriées sur le Registre et que le cloud de celui-ci est le même que celui utilisé dans Azure CLI.

## <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

Cette erreur signifie que le point de terminaison de test pour le Registre donné a répondu avec l’État HTTP 403 : Interdit. Cette erreur signifie que les utilisateurs n’ont accès au Registre, probablement en raison d’une configuration de réseau virtuel.

*Solutions potentielles* : supprimer les règles de réseau virtuel, ou ajouter l’adresse IP du client en cours à la liste verte.

## <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

Cette erreur signifie que le point de terminaison de test du Registre cible n’a pas émis de test.

*Solutions potentielles* : réessayer après un certain temps. Si l’erreur persiste, signalez le problème à https://aka.ms/acr/issues.

## <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Cette erreur signifie que le point de terminaison de défi du Registre cible a émis un défi, mais que le Registre ne prend pas en charge l’authentification Azure Active Directory.

*Solutions potentielles* : essayer une autre méthode pour s’authentifier, par exemple, avec des informations d’identification d’administrateur. Si les utilisateurs doivent s’authentifier à l’aide d’Azure Active Directory, signalez un problème à https://aka.ms/acr/issues.

## <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Cette erreur signifie que le serveur de connexion du Registre n’a pas répondu avec un jeton d’actualisation, de sorte que l’accès au Registre cible a été refusé. Cette erreur peut se produire si l’utilisateur ne dispose pas des autorisations appropriées sur le Registre ou si les informations d’identification d’utilisateur pour Azure CLI sont obsolètes.

*Solutions potentielles* : vérifier si l’utilisateur dispose des autorisations appropriées sur le Registre ; exécuter `az login` pour actualiser les autorisations, les jetons et les informations d’identification.

## <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Cette erreur signifie que le serveur de connexion du Registre n’a pas répondu avec un jeton d’accès, de sorte que l’accès au Registre cible a été refusé. Cette erreur peut se produire si l’utilisateur ne dispose pas des autorisations appropriées sur le Registre ou si les informations d’identification d’utilisateur pour Azure CLI sont obsolètes.

*Solutions potentielles* : vérifier si l’utilisateur dispose des autorisations appropriées sur le Registre ; exécuter `az login` pour actualiser les autorisations, les jetons et les informations d’identification.

## <a name="loginservererror"></a>LOGIN_SERVER_ERROR

Cette erreur signifie que l’interface CLI n’a pas pu trouver le serveur de connexion du Registre donné, et qu’aucun suffixe par défaut a été trouvé pour le cloud en cours. Cette erreur peut se produire si le Registre n’existe pas, si l’utilisateur ne dispose pas des autorisations appropriées sur le Registre, si le cloud du Registre et le cloud actuel d’Azure CLI ne correspondent pas, ou si la version d’Azure CLI est obsolète.

*Solutions potentielles* : vérifier que l’orthographe est correcte et que le Registre existe ; vérifier que l’utilisateur dispose des autorisations appropriées sur le Registre et que les clouds du Registre et de l’environnement de l’interface de ligne de commande correspondent ; mettre à jour Azure CLI vers la dernière version.

## <a name="next-steps"></a>Étapes suivantes

Consultez la rubrique [Vérifier l’intégrité d’un registre de conteneurs Azure](container-registry-check-health.md) pour découvrir plus d’options de vérification de l’intégrité d’un Registre.

Consultez la section [FAQ](container-registry-faq.md) pour voir les questions fréquemment posées et d’autres problèmes connus relatifs à Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
