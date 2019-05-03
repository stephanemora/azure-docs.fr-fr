---
title: Sécurité de Blockchain Service Azure
description: Azure Blockchain données accès et sécurité concepts du Service
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dd0a33364ed9395a85478798e47352c533bd47dc
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028199"
---
# <a name="azure-blockchain-service-security"></a>Sécurité de Blockchain Service Azure

Azure Blockchain Service utilise plusieurs fonctionnalités Azure pour protéger vos données et disponible. Données sont sécurisées à l’aide d’isolation, le chiffrement et l’authentification.

## <a name="isolation"></a>Isolation

Ressources de Blockchain Service Azure sont isolées dans un réseau privé virtuel. Chaque nœud de la transaction et la validation est une machine virtuelle (VM). Machines virtuelles dans un réseau virtuel ne peut pas communiquer directement avec les machines virtuelles dans un autre réseau virtuel. L’isolation garantit que la communication reste privée au sein du réseau virtuel. Pour plus d’informations sur l’isolation de réseau virtuel Azure, consultez [isolation dans le Cloud Public Azure](../../security/azure-isolation.md#networking-isolation).

![Diagramme de réseau virtuel](./media/data-security/vnet.png)

## <a name="encryption"></a>Chiffrement

Données utilisateur sont stockées dans le stockage Azure. Données utilisateur sont chiffrées en mouvement et au repos pour la sécurité et la confidentialité. Pour plus d'informations, consultez les pages suivantes : [Guide de sécurité de stockage Azure](../../storage/common/storage-security-guide.md).

## <a name="authentication"></a>Authentification

Les transactions peuvent être envoyées aux nœuds de blockchain via un point de terminaison RPC. Les clients communiquent avec un nœud de transactions à l’aide d’un serveur proxy inverse qui gère l’authentification d’utilisateur et chiffre les données via le protocole SSL.

![Diagramme d’authentification](./media/data-security/authentication.png)

Il existe trois modes d’authentification pour l’accès RPC.

### <a name="basic-authentication"></a>Authentification de base

L’authentification de base utilise un en-tête d’authentification HTTP contenant le nom d’utilisateur et le mot de passe. Nom d’utilisateur est le nom du nœud blockchain. Mot de passe est défini lors de l’approvisionnement d’un membre ou d’un nœud. Le mot de passe peut être modifié à l’aide du portail Azure ou l’interface CLI.

### <a name="access-keys"></a>Clés d’accès

Clés d’accès utilisent une chaîne générée de manière aléatoire incluse dans l’URL de point de terminaison. Deux clés d’accès vous permettent d’effectuer la rotation des clés. Les clés peuvent être régénérées à partir du portail Azure et le CLI.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) utilise une base de revendications authentification où l’utilisateur est authentifié par Azure AD à l’aide des informations d’identification utilisateur de Azure AD. Azure AD fournit la gestion des identités de cloud et permet aux clients d’utiliser une identité unique dans un ensemble les applications enterprise et des accès sur le cloud. Azure Blockchain Service s’intègre avec Azure AD, activation de la fédération d’ID, seule l’authentification et l’authentification multifacteur. Vous pouvez affecter des utilisateurs, groupes et rôles d’application dans votre organisation pour l’accès de membre et de nœud de blockchain.

Le proxy de client Azure AD est disponible sur [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). Le proxy client dirigeant l’utilisateur vers la page de connexion Azure AD et obtient un jeton de porteur après une authentification réussie. Par la suite, l’utilisateur connecte une application de client Ethereum tels que Geth ou Truffle au point de terminaison du proxy client. Enfin, lorsqu’une transaction est envoyée, le proxy client injecte le jeton du porteur dans l’en-tête http et le proxy inverse valide le jeton à l’aide du protocole OAuth.

## <a name="keys-and-ethereum-accounts"></a>Clés et les comptes Ethereum

Lorsque vous configurez un membre Azure Blockchain Service, un compte Ethereum et une paire de clés publique et privée est générée. La clé privée est utilisée pour envoyer des transactions à la blockchain. Le compte de Ethereum est les 20 derniers octets du hachage de la clé publique. Le compte Ethereum est également appelé un portefeuille.

La paire de clés privée et publique est stockée comme un fichier de clé au format JSON. La clé privée est chiffrée à l’aide du mot de passe entré lorsque le service de comptabilité blockchain est créé.

Clés privées sont utilisées pour signer numériquement des transactions. Dans les chaînes de blocs privé, un contrat smart signé par une clé privée représente l’identité du signataire. Pour vérifier la validité de la signature, le destinataire peut comparer la clé publique du signataire avec l’adresse calculée à partir de la signature.

Clés de constellation sont utilisées pour identifier de façon unique un nœud de Quorum. Clés de constellation sont générés au moment de l’approvisionnement du nœud et sont spécifiés dans le paramètre privateFor d’une transaction privée dans le Quorum.

## <a name="next-steps"></a>Étapes suivantes

[Configurer les nœuds de transaction de Service de Azure Blockchain](configure-transaction-nodes.md)
