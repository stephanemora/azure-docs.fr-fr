---
title: Étiquettes d’image et notes de publication de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Liste de toutes les étiquettes d’image de conteneur de Form Recognizer.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: reference
ms.date: 09/02/2021
ms.author: lajanuar
keywords: Docker, conteneur, images
ms.openlocfilehash: d38fd74324246463e9f74ff8d53c67ccb2ccd039
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123482011"
---
# <a name="form-recognizer-container-image-tags-and-release-notes"></a>Étiquettes d’image conteneur et notes de publication de Form Recognizer

> [!IMPORTANT]
>
> * Les **conteneurs Form Recognizer v2.1** sont en préversion contrôlée. Pour les utiliser, vous devez envoyer une [demande en ligne](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu) et recevoir une approbation. 
>
> * Pour le formulaire de demande en ligne, vous devez fournir une adresse e-mail valide appartenant à l’organisation propriétaire de l’ID d’abonnement Azure et disposer ou bénéficier d’un accès à cet abonnement.

## <a name="feature-containers"></a>Conteneurs de fonctionnalités

Les fonctionnalités de Form Recognizer sont prises en charge par sept conteneurs :

| Nom du conteneur | Nom complet de l’image |
|---|---|
| **Disposition** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout |
| **Carte de visite** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/businesscard |
| **Document d’identité** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/id-document |
| **Réception** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/receipt |
| **Facture** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice |
| **API personnalisée** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-api |
| **Supervisé personnalisé** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-supervised |

## <a name="microsoft-container-registry"></a>Registre de conteneurs Microsoft

Les images conteneurs de Form Recognizer se trouvent dans le **mcr.microsoft** de Microsoft Container Registry. Syndicat du registre de conteneurs **<span></span>com**, le registre principal pour toutes les images Docker publiées par Microsoft.

* L’expérience de découverte pour MCR est fournie par [Docker Hub](https://hub.docker.com/publishers/microsoftowner).

* Vous pouvez également interroger [la liste des référentiels au sein de mcr](https://mcr.microsoft.com/v2/_catalog).

## <a name="form-recognizer-tags"></a>Étiquettes Form Recognizer

Les étiquettes suivantes sont disponibles pour Form Recognizer :

### <a name="latest-version"></a>[Version la plus récente](#tab/current)

Notes de publication pour `v2.1` (version préliminaire contrôlée) :

| Conteneur | Étiquettes |
|------------|:------|
| **Disposition**| &bullet; `latest` </br> &bullet; `2.1-preview` </br> &bullet; `2.1.0.016140001-08108749-amd64-preview`|
| **Carte de visite** | &bullet; `latest` </br> &bullet; `2.1-preview` </br> &bullet; `2.1.016190001-amd64-preview`  </br> &bullet; `2.1.016320001-amd64-preview`  |
| **Document d’identité** | &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **Réception**| &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **Facture**| &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **API personnalisée** | &bullet; `latest` </br> &bullet;`2.1-distroless-20210622013115034-0cc5fcf6`</br>&bullet; `2.1-preview`|
| **Supervisé personnalisé**| &bullet; `latest` </br> &bullet; `2.1-distroless-20210622013149174-0cc5fcf6`</br>&bullet; `2.1-preview`|

### <a name="previous-versions"></a>[Versions précédentes](#tab/previous)

> [!IMPORTANT]
> Le conteneur Form Recognizer v1.0 a été retiré.

---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Installer et exécuter des conteneurs Form Recognizer](form-recognizer-container-install-run.md)
>