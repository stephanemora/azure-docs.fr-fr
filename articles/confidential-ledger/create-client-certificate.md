---
title: Création d’un certificat client avec le Registre confidentiel Microsoft Azure
description: Création d’un certificat client avec le Registre confidentiel Microsoft Azure
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 6d384ea07c49a8b7936e3cdf634b9775847018f0
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385524"
---
# <a name="creating-a-client-certificate"></a>Création d’un certificat client

Les API du registre confidentiel requièrent l’authentification basée sur les certificats clients. Seuls les certificats ajoutés à une liste d’autorisation lors de la création ou de la mise à jour du registre peuvent être utilisés pour appeler les API fonctionnelles du registre confidentiel.

Vous aurez besoin d’un certificat au format PEM. Vous pouvez créer plusieurs certificats et les ajouter ou les supprimer à l’aide de l’API de mise à jour du registre.

## <a name="openssl"></a>OpenSSL

Nous vous recommandons d’utiliser OpenSSL pour générer les certificats. Si git est installé, vous pouvez exécuter OpenSSL dans le shell git. Dans le cas contraire, vous pouvez installer OpenSSL pour votre système d’exploitation.

- **Windows** : Installez [chocolatey pour Windows](https://chocolatey.org/install), ouvrez une fenêtre de terminal PowerShell en mode administrateur, puis exécutez `choco install openssl`. Vous pouvez également installer OpenSSL pour Windows directement à partir d’[ici](http://gnuwin32.sourceforge.net/packages/openssl.htm).
- **Linux** : Exécutez `sudo apt-get install openssl`

Vous pouvez ensuite générer un certificat en exécutant `openssl` dans une fenêtre de terminal Bash ou PowerShell :

```bash
openssl req -new -newkey rsa:1024 -days 365 -nodes -x509 -keyout user_privk.pem -out user_cert.pem -subj=/CN="User Client Certificate"
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du Registre confidentiel Microsoft Azure](overview.md)