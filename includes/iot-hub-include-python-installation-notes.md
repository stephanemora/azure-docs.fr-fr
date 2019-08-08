---
title: Fichier Include
description: Fichier Include
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 103ad020b8d9f50ffe690f502b7cac08dab9237a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640432"
---
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes seulement.)

* **Windows**

    * [Python 2.x ou 3.x](https://www.python.org/downloads/). Veillez à utiliser l’installation 32 bits ou 64 bits comme requis par votre programme d’installation. Lorsque vous y êtes invité pendant l’installation, veillez à ajouter Python à votre variable d’environnement propre à la plateforme. Si vous utilisez Python 2.x, vous devrez peut-être [installer ou mettre à niveau *pip*, le système de gestion des packages Python](https://pip.pypa.io/en/stable/installing/).

    * Si vous utilisez le système d’exploitation Windows, vérifiez que vous disposez de la bonne version de [Visual C++ Redistributable Package](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads), afin d’autoriser l’utilisation des DLL natives de Python. Nous vous recommandons d’utiliser la version la plus récente.

    * Le cas échéant, installez le package [azure-iothub-device-client](https://pypi.org/project/azure-iothub-device-client/) à l’aide de la commande `pip install azure-iothub-device-client`

    * Le cas échéant, installez le package [azure-iothub-service-client](https://pypi.org/project/azure-iothub-service-client/) à l’aide de la commande `pip install azure-iothub-service-client`

* **Mac OS**

    Par Mac OS, vous avez besoin des outils Python 3.7.0 (ou 2,7) + libboost-1,67 + curl 7.61.1 (tous installés via Homebrew). Les autres distributions/systèmes d’exploitation incorporeront probablement différentes versions des dépendances Boost et des dépendances qui ne fonctionneront pas et entraîneront une erreur ImportError au moment de l’exécution.

    Les packages *pip* pour `azure-iothub-service-client` et `azure-iothub-device-client` sont actuellement disponibles uniquement pour les systèmes d’exploitation Windows. Pour Linux/Mac OS, reportez-vous aux sections spécifiques de Linux et Mac OS de la publication [Prepare your development environment for Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) (Préparer votre environnement de développement pour Python).

> [!NOTE]
> Plusieurs rapports d’erreurs ont été générés lors de l’importation de iothub_client dans un exemple. Pour savoir comment gérer les erreurs **ImportError**, consultez la section relative à la [gestion des erreurs ImportError](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).
