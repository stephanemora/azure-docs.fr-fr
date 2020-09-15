---
title: Présentation d’Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Azure Key Vault est un magasin de secrets sécurisé, qui fournit des solutions de gestion des secrets, des clés et des certificats et ce, en s’appuyant sur les modules de sécurité matériels.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 500648b3037a81b39f474538ec062ef922b6e2df
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89421643"
---
# <a name="about-azure-key-vault"></a>À propos d’Azure Key Vault

Azure Key Vault aide à résoudre les problèmes suivants :

- **Gestion des secrets** : Azure Key Vault peut être utilisé pour stocker en toute sécurité les jetons, mots de passe, certificats, clés API et autres secrets, et pour en contrôler étroitement l’accès.
- **Gestion des clés** : Azure Key Vault peut également servir de solution de gestion de clés. Azure Key Vault simplifie la création et le contrôle des clés de chiffrement utilisées pour chiffrer vos données. 
- **Gestion des certificats** : Azure Key Vault est également un service qui vous permet de provisionner, gérer et déployer facilement des certificats SSL/TLS publics et privés pour une utilisation avec Azure et vos ressources connectées internes. 
- **Stocker les secrets sauvegardés avec les modules de sécurité matériels** : les secrets, les clés et les certificats en magasin sont chiffrés avec une clé logicielle (niveau Standard) ou une clé des modules de sécurité matériels certifiés FIPS 140-2 de niveau 2 (niveau Premium) 

## <a name="why-use-azure-key-vault"></a>Pourquoi utiliser Azure Key Vault ?

### <a name="centralize-application-secrets"></a>Centraliser les secrets d’application

La centralisation du stockage des secrets d’application dans Azure Key Vault vous permet de contrôler la distribution de ces secrets. Key Vault réduit considérablement les risques de fuite accidentelle des secrets. Grâce à Key Vault, les développeurs d’applications n’ont plus besoin de stocker les informations de sécurité dans leur application. Ne pas avoir à stocker les informations de sécurité dans les applications élimine le besoin d’intégrer ces informations au code. Considérons l’exemple d’une application ayant besoin de se connecter à une base de données. Dans ce cas, plutôt que d’inclure la chaîne de connexion dans le code de l’application, vous pouvez simplement la stocker en toute sécurité dans Key Vault.

Vos applications peuvent accéder en toute sécurité aux informations nécessaires en utilisant des URI. Ces URI permettent aux applications de récupérer des versions spécifiques d’un secret. Aucune écriture de code personnalisé n’est nécessaire pour protéger les informations secrètes stockées dans Key Vault.

### <a name="securely-store-secrets-and-keys"></a>Stocker en toute sécurité les secrets et clés

Les secrets et les clés sont protégés par Azure, à l’aide d’algorithmes standards, de longueurs de clé et de modules de sécurité matériel (HSM). Les HSM utilisés sont garantis conformes aux normes FIPS (Federal Information Processing Standard) 140-2 de niveau 2.

L’accès à un coffre de clés par un appelant (utilisateur ou application) requiert une authentification et une autorisation adéquates. L’authentification établit l’identité de l’appelant, tandis que l’autorisation détermine les opérations que ce dernier est autorisé à effectuer.

L’authentification s’effectue par le biais d’Azure Active Directory. L’autorisation peut être assurée par l’intermédiaire du mécanisme de contrôle d’accès en fonction du rôle (RBQC) ou d’une stratégie d’accès à Key Vault. RBAC est utilisé dans le cadre de la gestion des coffres, et la stratégie d’accès au coffre de clés est appliquée lors d’une tentative d’accès aux données stockées dans un coffre.

Azure Key Vault peut être protégé par un logiciel ou par un HSM matériel. Dans les situations qui nécessitent un surcroît de vigilance, vous pouvez importer ou générer des clés dans des HSM dont les clés ne franchissent jamais les limites. Microsoft utilise les modules de sécurité matériels nCipher. Pour déplacer une clé de votre HSM vers Azure Key Vault, vous pouvez utiliser les outils nCipher.

Enfin, Azure Key Vault a été conçu de façon que Microsoft ne puisse pas visualiser ni extraire vos données.

### <a name="monitor-access-and-use"></a>Surveiller les accès et l’utilisation

Après avoir créé plusieurs coffres de clés, vous voudrez surveiller le mode et le moment des accès à vos clés et secrets. Vous pouvez surveiller l’activité en activant la journalisation pour vos coffres. La solution Azure Key Vault est configurable pour l’exécution des opérations suivantes :

- archivage dans un compte de stockage ;
- diffusion sur un Event Hub ;
- envoi des journaux aux journaux Azure Monitor.

Vous pouvez contrôler vos journaux d’activité et les sécuriser en limitant l’accès, ainsi que supprimer les journaux d’activité dont vous n’avez plus besoin.

### <a name="simplified-administration-of-application-secrets"></a>Administration simplifiée des secrets d’application

Lorsque vous stockez vos données les plus précieuses, vous devez prendre différentes mesures. Les informations de sécurité doivent être sécurisées, suivre un cycle de vie spécifique et se révéler hautement disponibles. Azure Key Vault simplifie la réponse à ces exigences grâce aux éléments suivants :

- Suppression de la nécessité de connaissances en interne des modules HSM.
- Exécution d’un scale-up dans un délai très court pour répondre aux pics d’utilisation de votre organisation.
- Réplication du contenu de votre coffre de clés au sein d’une région et vers une région secondaire. La réplication des données garantit la haute disponibilité et élimine l’obligation pour l’administrateur de déclencher le basculement.
- Fourniture des options d’administration Azure standard par le biais du Portail, de l’interface de ligne de commande Azure et de PowerShell.
- Automatisation de certaines tâches sur les certificats que vous achetez auprès d’une autorité de certification publique, comme l’inscription et le renouvellement.

En outre, les coffres de clé Azure vous permettent de séparer les secrets d’application. Les applications peuvent accéder uniquement au coffre dont l’accès leur a été accordé, et elles peuvent être restreintes à certaines opérations spécifiques. Vous pouvez créer un coffre de clés Azure par application et restreindre les secrets stockés dans un coffre de clés à une application et une équipe de développeurs spécifiques.

### <a name="integrate-with-other-azure-services"></a>Intégrer à d’autres services Azure

En tant que banque d’informations sécurisée dans Azure, Key Vault a été utilisé pour simplifier les scénarios, tels que :
-  [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)
-  Les fonctionnalités [Always encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) et [Transparent Data Encryption]( https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15) dans SQL Server et Azure SQL Database
- [Azure App Service]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate). 

La solution Key Vault proprement dite peut s’intégrer aux comptes de stockage, ainsi qu’aux services Event Hubs et Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [clés, secrets et certificats](about-keys-secrets-certificates.md)
- [Démarrage rapide : créer un coffre de clés Azure Key Vault à l’aide de l’interface de ligne de commande](../secrets/quick-create-cli.md)
- [Authentification, requêtes et réponses](../general/authentication-requests-and-responses.md)
- [Guide du développeur Key Vault](../general/developers-guide.md)
