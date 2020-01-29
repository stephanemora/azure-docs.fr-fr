---
title: Guide pratique pour configurer OpenSSL pour Linux
titleSuffix: Azure Cognitive Services
description: Découvrez comment configurer OpenSSL pour Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: cadf31dede8ee81323076013d00b9431f597bda6
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156486"
---
# <a name="configure-openssl-for-linux"></a>Configurer OpenSSL pour Linux

Quand vous utilisez le kit SDK Speech dans une version antérieure à 1.9.0, [OpenSSL](https://www.openssl.org) est configuré de façon dynamique en fonction de la version du système hôte. Dans les versions ultérieures du kit SDK Speech, OpenSSL (version [1.1.1b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) est lié de manière statique à la bibliothèque principale du kit SDK Speech.

## <a name="troubleshoot-connectivity"></a>Résoudre les problèmes de connectivité

En cas d’échec de la connexion lors de l’utilisation de la version 1.9.0 du kit SDK Speech, vérifiez que le répertoire `ssl/certs` existe dans le répertoire `/usr/lib`, qui se trouve dans le système de fichiers Linux. Si le répertoire `ssl/certs` *n’existe pas*, vérifiez où est installé OpenSSL sur votre système en utilisant la commande suivante :

```bash
which openssl
```

Ensuite, localisez le répertoire `certs` OpenSSL, puis copiez le contenu de ce répertoire dans le répertoire `/usr/lib/ssl/certs`. Après quoi, essayez de voir à nouveau si les problèmes de connectivité ont été résolus.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [À propos du kit SDK Speech](speech-sdk.md)