---
title: Authentification de nœuds du Registre confidentiel Azure
description: Authentification de nœuds du Registre confidentiel Azure
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 0a8f033aa540d2a469141c4c529981a0cbeef3ec
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385527"
---
# <a name="authenticating-azure-confidential-ledger-nodes"></a>Authentification de nœuds du Registre confidentiel Azure

Les nœuds du Registre confidentiel Azure peuvent être authentifiés par des exemples de code et par des utilisateurs.

## <a name="code-samples"></a>Exemples de code

Lors de l’initialisation, les exemples de code obtiennent le certificat de nœud en interrogeant le service d’identité. Une fois le certificat de nœud récupéré, un exemple de code interroge le réseau du Registre pour obtenir une citation qui est ensuite validée à l’aide des binaires de vérification de l’hôte. Si la vérification est réussie, l’exemple de code passe aux opérations du Registre.

## <a name="users"></a>Utilisateurs

Les utilisateurs peuvent valider l’authenticité des nœuds de Registre confidentiel pour vérifier qu’ils s’interfacent avec l’enclave de leur Registre. Vous pouvez instaurer la confiance dans les nœuds de Registre confidentiel de plusieurs façons qui peuvent être empilées les unes sur les autres pour augmenter le niveau de confiance total. Ainsi, les étapes 1-2 aident à créer la confiance dans cette enclave de Registre confidentiel dans le cadre de l’établissement d’une liaison TLS initiale et de l’authentification dans les flux de travail fonctionnels. Au-delà de cela, une connexion client permanente est maintenue entre le client de l’utilisateur et le Registre confidentiel.

- **Validation du nœud de Registre confidentiel** : cette opération s’effectue en interrogeant le service d’identité hébergé par Microsoft, qui fournit un certificat réseau et permet ainsi de vérifier que le nœud de Registre présente un certificat approuvé/signé par le certificat réseau pour cette instance spécifique. À l’instar du protocole HTTPS basé sur PKI, le certificat d’un serveur est signé par une autorité de certification ou une autorité de certification intermédiaire connue. Dans le cas d’un Registre confidentiel, le certificat de l’autorité de certification est renvoyé par un service d’identité sous la forme d’un certificat réseau. Il s’agit d’une mesure de création de confiance importante pour les utilisateurs du Registre confidentiel. Si ce certificat de nœud n’est pas signé par le certificat réseau retourné, la connexion client (telle qu’implémentée dans l’exemple de code) devrait échouer.
- **Validation de l’enclave de Registre confidentiel** : le Registre confidentiel s’exécute dans une enclave Intel® SGX représentée par une citation, un blob de données généré à l’intérieur de cette enclave. Elle peut être utilisée par n’importe quelle autre entité pour vérifier que la citation a été générée à partir d’une application s’exécutant avec des protections Intel® SGX. La citation est structurée de manière à permettre une vérification facile. Elle contient des revendications qui permettent d’identifier différentes propriétés de l’enclave et de l’application en cours d’exécution. Il s’agit d’un mécanisme important de création de confiance pour les utilisateurs du Registre confidentiel. Il est possible de le mettre en œuvre en appelant une API de flux de travail fonctionnel pour obtenir une citation d’enclave. La connexion client devrait échouer si la citation n’est pas valide. La citation récupérée peut ensuite être validée avec l’outil open_enclaves Host_Verify. Pour plus d’informations à ce sujet, cliquez ici.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du Registre confidentiel Microsoft Azure](overview.md)
- [Architecture du Registre confidentiel Azure](architecture.md)
