---
title: Activer le protocole TLS de bout en bout
titleSuffix: Azure Spring Cloud
description: Comment activer le protocole TLS de bout en bout pour une application.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/24/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 647cf6f0b1af6a5858bbf1147cc03ecc4637ed25
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227804"
---
# <a name="enable-end-to-end-tls-for-an-application"></a>Activer le protocole TLS de bout en bout pour une application

Cette rubrique montre comment activer le protocole SSL/TLS de bout en bout pour sécuriser le trafic d’un contrôleur d’entrée vers des applications qui prennent en charge le protocole HTTPS. Une fois que vous avez activé le protocole TLS de bout en bout et que vous avez chargé un certificat à partir de Key Vault, toutes les communications au sein d’Azure Spring Cloud sont sécurisées par TLS.

   ![Graphique des communications sécurisées par TLS.](media/enable-end-to-end-tls/secured-tls.png)

## <a name="prerequisites"></a>Prérequis 

- Une instance Azure Spring Cloud déployée. Pour bien démarrer, suivez notre [guide de démarrage rapide sur le déploiement d’une application via Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli).
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

## <a name="verify-end-to-end-tls-status"></a>Vérifier l’état du protocole TLS de bout en bout

Utilisez la commande `az spring-cloud app show` pour vérifier la valeur de `enableEndToEndTls`.
```
az spring-cloud app show -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Étapes suivantes
* [Accéder à Config Server et Service Registry](how-to-access-data-plane-azure-ad-rbac.md)
