---
title: Sécurité du conteneur
titleSuffix: Azure Cognitive Services
description: Apprendre à sécuriser votre conteneur
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: dapine
ms.openlocfilehash: d8d069dddbce6ab6ddb541db460634ad3f6fa067
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994943"
---
## <a name="azure-cognitive-services-container-security"></a>Sécurité des conteneurs Azure Cognitive Services

Quand vous développez des applications, la sécurité doit être l’un de vos principaux soucis. L’importance que vous accordez à la sécurité est un indicateur de réussite. Quand vous concevez une solution logicielle qui comprend des conteneurs Cognitive Services, il est essentiel de comprendre les limitations et les capacités disponibles. Pour plus d’informations, consultez [Sécurité Azure][az-security].

> [!IMPORTANT]
> Par défaut, l’API du conteneur Cognitive Services *n’est pas sécurisée*. Cela est dû au fait que le conteneur s’exécute le plus souvent dans le cadre d’un pod qui est protégé de l’extérieur par un pont réseau. Toutefois, il est possible d’activer l’authentification, celle-ci fonctionnant de la même façon que l’authentification utilisée lors de l’accès aux [services Cognitive Services basés sur le cloud][request-authentication].

Le diagramme ci-dessous illustre l’approche **non sécurisée** par défaut :

![Sécurité du conteneur](../media/container-security.svg)

Une autre approche *sécurisée* pour les consommateurs de conteneurs Cognitive Services consiste à ajouter un composant frontal à un conteneur afin de maintenir le point de terminaison de conteneur privé. Prenons l’exemple d’un scénario dans lequel nous utilisons [Istio][istio] comme passerelle d’entrée. Istio prend en charge HTTPS/SSL et l’authentification par certificat client. Dans ce scénario, le composant frontal Istio expose l’accès au conteneur et présente le certificat client qui est préalablement inclus sur liste verte avec Istio.

[Nginx][nginx] est un autre choix populaire dans la même catégorie. Istio et Nginx jouent le rôle de maillage de service et offrent des fonctionnalités supplémentaires, notamment l’équilibrage de charge, le routage et le contrôle du débit.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../../security/fundamentals/overview.md
