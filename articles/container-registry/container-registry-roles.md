---
title: Rôles et les autorisations RBAC
description: Utilisez le contrôle d’accès en fonction du rôle (RBAC) et la gestion des identités et des accès (IAM) d’Azure pour fournir des autorisations de granularité fine aux ressources dans un registre de conteneurs Azure.
ms.topic: article
ms.date: 03/20/2019
ms.openlocfilehash: 8ef4f26dfd59c7b3b177ef58fa23e08f7e66d328
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456242"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Autorisations et rôles Azure Container Registry

Le service Azure Container Registry prend en charge un ensemble de rôles Azure fournissant des niveaux d’autorisation différents à un registre de conteneurs Azure. Utilisez le [contrôle d’accès en fonction du rôle](../role-based-access-control/index.yml) (RBAC) d’Azure pour attribuer des autorisations spécifiques aux utilisateurs ou aux principaux de service qui doivent interagir avec un registre.

| Rôle/autorisation       | [Accéder à Resource Manager](#access-resource-manager) | [Créer/supprimer le Registre](#create-and-delete-registry) | [Pousser (push) l’image](#push-image) | [Extraire (pull) l’image](#pull-image) | [Supprimer les données d’image](#delete-image-data) | [Changer de stratégies](#change-policies) |   [Signer les images](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Propriétaire | X | X | X | X | X | X |  |  
| Contributeur | X | X | X |  X | X | X |  |  
| Lecteur | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Différencier utilisateurs et services

Chaque fois que des autorisations sont appliquées, une bonne pratique consiste à fournir l’ensemble le plus limité d’autorisations pour qu’un individu ou un service puisse accomplir une tâche. Les ensembles d’autorisations suivants représentent un ensemble de fonctionnalités qui peuvent être utilisées par les humains et les services sans périphérique de contrôle.

### <a name="cicd-solutions"></a>Solutions CI/CD

Lorsque vous automatisez des commandes `docker build` à partir de solutions CI/CD, vous avez besoin de fonctionnalités `docker push`. Pour ces scénarios de service sans périphérique de contrôle, nous suggérons d’attribuer le rôle **AcrPush**. Ce rôle, contrairement au rôle **Contributeur** plus large, empêche le compte d’effectuer d’autres opérations de registre ou d’accéder à Azure Resource Manager.

### <a name="container-host-nodes"></a>Nœuds de l’hôte de conteneur

De même, les nœuds exécutant vos conteneurs ont besoin du rôle **AcrPull**, mais ne devraient pas avoir besoin des fonctionnalités **Lecteur**.

### <a name="visual-studio-code-docker-extension"></a>Extension Docker de Visual Studio Code

Pour des outils comme l’[extension Docker](https://code.visualstudio.com/docs/azure/docker) de Visual Studio Code, un accès supplémentaire au fournisseur de ressources est nécessaire pour dresser la liste des registres de conteneurs Azure disponibles. Dans ce cas, donnez à vos utilisateurs accès au rôle **Lecteur** ou **Contributeur**. Ces rôles permettent d’effectuer des actions telles que `docker pull`, `docker push`, `az acr list` et `az acr build`, entre autres. 

## <a name="access-resource-manager"></a>Accéder à Resource Manager

L’accès à Azure Resource Manager est requis pour le portail Azure et la gestion du registre avec l’[interface de ligne de commande Azure](/cli/azure/). Par exemple, pour obtenir la liste des registres à l’aide de la commande `az acr list`, vous devez disposer de cette autorisation. 

## <a name="create-and-delete-registry"></a>Créer et supprimer le registre

Possibilité de créer et de supprimer les registres de conteneurs Azure.

## <a name="push-image"></a>Pousser (push) l’image

Possibilité de `docker push` une image ou d’envoyer (push) un autre [artefact pris en charge](container-registry-image-formats.md), tel qu’un graphique Helm, à un registre. Nécessite l’[authentification](container-registry-authentication.md) auprès du registre en utilisant l’identité autorisée. 

## <a name="pull-image"></a>Extraire (pull) l’image

Possibilité de `docker pull` une image qui n’est pas en quarantaine ou de tirer (pull) un autre [artefact pris en charge](container-registry-image-formats.md), tel qu’un graphique Helm, d’un registre. Nécessite l’[authentification](container-registry-authentication.md) auprès du registre en utilisant l’identité autorisée.

## <a name="delete-image-data"></a>Supprimer les données d’image

Possibilité de [supprimer des images de conteneur](container-registry-delete.md) ou de supprimer d’autres [artefacts pris en charge](container-registry-image-formats.md) tels que des graphiques Helm, à partir d’un registre.

## <a name="change-policies"></a>Changer de stratégies

Possibilité de configurer des stratégies sur un registre. Les stratégies comprennent la purge d’image, la mise en quarantaine et la signature d’image.

## <a name="sign-images"></a>Signer les images

Capacité de signer des images, habituellement affectées à un processus automatique, qui utiliserait un principal de service. Cette autorisation est typiquement associée à un [envoi (push) d’image](#push-image) pour permettre l’envoi d’une image de confiance à un registre. Pour plus d’informations, consultez [Approbation de contenu dans Azure Container Registry](container-registry-content-trust.md).

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur l’attribution de rôles RBAC à une identité Azure à l’aide du [portail Azure](../role-based-access-control/role-assignments-portal.md), l’[interface de ligne de commande Azure](../role-based-access-control/role-assignments-cli.md) ou d’autres outils Azure.

* Apprenez-en davantage sur les [options d’authentification](container-registry-authentication.md) pour Azure Container Registry.
