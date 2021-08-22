---
title: Activer le protocole TLS de bout en bout
titleSuffix: Azure Spring Cloud
description: Comment activer le protocole TLS de bout en bout pour une application.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/24/2021
ms.custom: devx-track-java
ms.openlocfilehash: f4d21c2186706bcdaf92c7ef2b3d3b45c1ccc4f4
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563425"
---
# <a name="enable-end-to-end-tls-for-an-application"></a>Activer le protocole TLS de bout en bout pour une application

Cette rubrique montre comment activer le protocole SSL/TLS de bout en bout pour sécuriser le trafic d’un contrôleur d’entrée vers des applications qui prennent en charge le protocole HTTPS. Une fois que vous avez activé le protocole TLS de bout en bout et que vous avez chargé un certificat à partir de Key Vault, toutes les communications au sein d’Azure Spring Cloud sont sécurisées par TLS.

![Graphique des communications sécurisées par TLS.](media/enable-end-to-end-tls/secured-tls.png)

## <a name="prerequisites"></a>Prérequis

- Une instance Azure Spring Cloud déployée. Pour bien démarrer, suivez notre [guide de démarrage rapide sur le déploiement d’une application via Azure CLI](./quickstart.md).
- Si vous n’êtes pas familiarisé avec le protocole TLS de bout en bout, consultez l’ [exemple de protocole TLS de bout en bout](https://github.com/Azure-Samples/spring-boot-secure-communications-using-end-to-end-tls-ssl).
- Pour charger en toute sécurité les certificats requis dans les applications Spring Boot, vous pouvez utiliser [Keyvault Spring Boot Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-certificates).

## <a name="enable-end-to-end-tls-on-an-existing-app"></a>Activer le protocole TLS de bout en bout sur une application existante

Utilisez la commande `az spring-cloud app update --enable-end-to-end-tls` pour activer ou désactiver le protocole TLS de bout en bout pour une application.

```azurecli
az spring-cloud app update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app update --enable-end-to-end-tls false -n app_name -s service_name -g resource_group_name
```

## <a name="enable-end-to-end-tls-when-you-bind-custom-domain"></a>Activer le protocole TLS de bout en bout lorsque vous liez un domaine personnalisé

Utilisez la commande `az spring-cloud app custom-domain update --enable-end-to-end-tls` ou `az spring-cloud app custom-domain bind --enable-end-to-end-tls` pour activer ou désactiver le protocole TLS de bout en bout pour une application.

```azurecli
az spring-cloud app custom-domain update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app custom-domain bind --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
```

## <a name="enable-end-to-end-tls-using-azure-portal"></a>Activer le protocole TLS de bout en bout à l’aide de Portail Azure
Pour activer le protocole TLS de bout en bout dans le [portail Azure](https://portal.azure.com/), commencez par créer une application, puis activez la fonctionnalité.

1. Créez une application dans le portail, comme vous le feriez normalement. Accédez-y dans le portail.
2. Faites défiler l'écran jusqu'au groupe **Paramètres** dans le volet de navigation gauche.
3. Sélectionnez **Protocole TLS de bout en bout**.
4. Basculez **Protocole TLS de bout en bout** sur *Oui*.

![Activer le protocole TLS de bout en bout dans le portail](./media/enable-end-to-end-tls/enable-tls.png)

## <a name="verify-end-to-end-tls-status"></a>Vérifier l’état du protocole TLS de bout en bout

Utilisez la commande `az spring-cloud app show` pour vérifier la valeur de `enableEndToEndTls`.

```azurecli
az spring-cloud app show -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Étapes suivantes

* [Accéder à Config Server et Service Registry](how-to-access-data-plane-azure-ad-rbac.md)
