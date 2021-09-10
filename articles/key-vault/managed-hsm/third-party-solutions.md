---
title: HSM managé Azure Key Vault – Solutions tierces | Microsoft Docs
description: En savoir plus sur les solutions tierces intégrées à HSM managé.
services: key-vault
author: mbaldwin
editor: ''
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 06/23/2021
ms.author: mbaldwin
ms.openlocfilehash: e70afc4a89fc0c9ce5c6ec59cd795b83b5e078b0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443466"
---
# <a name="third-party-solutions"></a>Solutions tierces

Azure Key Vault Managed HSM est un service cloud complètement managé, à haut niveau de disponibilité et monolocataire qui vous permet de protéger les clés de chiffrement de vos applications cloud à l’aide de HSM certifiés **FIPS 140-2 de niveau 3**. [Plus d’informations](overview.md)

Plusieurs fournisseurs ont travaillé en étroite collaboration avec Microsoft pour intégrer leurs solutions à HSM managé. Le tableau ci-dessous répertorie ces solutions avec une brève description (fournie par le fournisseur). Des liens vers leur offre Place de marché Azure et leur documentation sont également fournis.


## <a name="third-party-solutions-integrated-with-managed-hsm"></a>Solutions tierces intégrées à HSM managé

| Nom du fournisseur | Description de la solution |
|-------------|-------------------------------------------------|
|[Cloudflare](https://cloudflare.com)|Le protocole SSL sans clé de Cloudflare permet à vos sites web d’utiliser le protocole SSL de Cloudflare tout en conservant vos clés privées dans le HSM managé. Ce service, associé à HSM managé, offre un haut niveau de protection en sauvegardant vos clés privées, en effectuant des opérations de signature et de chiffrement en interne, en fournissant des contrôles d’accès et en stockant les clés dans un HSM inviolable FIPS 140-2 de niveau 3. <br>[Documentation](https://developers.cloudflare.com/ssl/keyless-ssl/hardware-security-modules/azure-managed-hsm)
|[NewNet Communication Technologies](https://newnet.com/)|Secure Transaction Cloud (STC) de NewNet est la première solution informatique de routage, de commutation et de transport de paiements sécurisés, augmentée d’un HSM virtualisé informatique, qui gère les paiements mobiles, web et en magasin. STC permet la transformation cloud des entités de paiement et un déploiement rapide pour les fournisseurs de paiement sur le terrain.<br/>[Offres de la Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/newnetcommunicationtechnologies1589991852134.secure_transaction_cloud?tab=overview)<br/>[Documentation](https://newnet.com/business-units/secure-transactions/products/secure-transaction-cloud-stc/)|
|[PrimeKey](https://www.primekey.com)|EJBCA Enterprise, l’infrastructure à clé publique (PKI) la plus utilisée au monde, fournit les services de sécurité de base pour les identités de confiance et la communication sécurisée pour tous les cas d’usage. Une seule instance d’EJBCA Enterprise prend en charge plusieurs autorités de certification et plusieurs niveaux pour vous permettre de créer une ou plusieurs infrastructures complètes pour plusieurs cas d’usage.<br>[Offres de la Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/primekey.ejbca_enterprise_cloud_2)<br/>[Documentation](https://doc.primekey.com/x/a4z_/)|



## <a name="next-steps"></a>Étapes suivantes
* [Vue d’ensemble du module HSM managé](overview.md)
* [Bonnes pratiques pour HSM managé](best-practices.md)

