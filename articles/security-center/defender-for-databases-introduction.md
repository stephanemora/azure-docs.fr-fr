---
title: Azure Defender pour les bases de données relationnelles open source - Avantages et fonctionnalités
description: Découvrez les avantages et les fonctionnalités d’Azure Defender pour les bases de données relationnelles open source, telles que PostgreSQL, MySQL et MariaDB
author: memildin
ms.author: memildin
ms.date: 05/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: eddab19cb46d27d0d48d1f2376cd9507b28cefc8
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003617"
---
# <a name="introduction-to-azure-defender-for-open-source-relational-databases"></a>Présentation d’Azure Defender pour les bases de données relationnelles open source

Ce plan Azure Defender offre des protections contre les menaces pour les bases de données relationnelles open source suivantes :

- [Base de données Azure pour PostgreSQL](../postgresql/index.yml)
- [Azure Database pour MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

Azure Defender détecte les activités anormales indiquant des tentatives inhabituelles et potentiellement dangereuses pour accéder à des bases de données ou les exploiter. Le plan vous permet de réagir facilement aux menaces potentielles visant les bases de données sans devenir un expert en sécurité ou gérer des systèmes avancés de supervision de la sécurité.

## <a name="availability"></a>Disponibilité

| Aspect                             | Détails                                                                                                                                    |
|------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------|
| État de sortie :                     | Disponibilité générale (GA)                                                     |
| Prix :                           | **Azure Defender pour les bases de données relationnelles open source** est facturé conformément aux [tarifs de Security Center](https://azure.microsoft.com/pricing/details/security-center/)   |
| Versions protégées de PostgreSQL :  | Monoserveur : Usage général et À mémoire optimisée. Découvrez-en plus dans [Niveaux tarifaires de PostgreSQL](../postgresql/concepts-pricing-tiers.md).   |
| Versions protégées de MySQL :       | Monoserveur : Usage général et À mémoire optimisée. Découvrez-en plus dans [Niveaux tarifaires de MySQL](../mysql/concepts-pricing-tiers.md).                        |
| Versions protégées de MariaDB :     | Usage général et À mémoire optimisée. Découvrez-en plus dans [Niveaux tarifaires de MariaDB](../mariadb/concepts-pricing-tiers.md).                      |
| Clouds :                            | ![Oui](./media/icons/yes-icon.png) Clouds commerciaux<br>![Non](./media/icons/no-icon.png) National/souverain (US Gov, Azure Chine) |
|                                    |                                                                                                                                            |

## <a name="what-are-the-benefits-of-azure-defender-for-open-source-relational-databases"></a>Quels sont les avantages d’Azure Defender pour les bases de données relationnelles open source ?

Azure Defender fournit des alertes de sécurité sur les activités anormales afin que vous puissiez détecter les menaces potentielles et y répondre à mesure qu’elles se produisent.

Quand vous activez ce plan, Azure Defender fournit des alertes s’il détecte des modèles de requête et un accès à la base de données anormaux ainsi que des activités de base de données suspectes.

Ces alertes apparaissent dans la page des alertes de sécurité d’Azure Defender et comprennent les informations suivantes :

- Détails de l’activité suspecte qui les a déclenchées
- Tactique MITRE ATT&CK associée
- Actions recommandées pour investiguer et atténuer la menace
- Options pour poursuivre vos investigations avec Azure Sentinel

:::image type="content" source="media/defender-for-databases-introduction/defender-alerts.png" alt-text="Certaines des alertes de sécurité que vous pouvez voir avec vos bases de données protégées par Azure Defender pour les bases de données relationnelles open source." lightbox="./media/defender-for-databases-introduction/defender-alerts.png":::

## <a name="what-kind-of-alerts-does-azure-defender-for-open-source-relational-databases-provide"></a>Quel type d’alertes Azure Defender pour les bases de données relationnelles open source fournit-il ?

Les alertes de sécurité enrichies par le renseignement sur les menaces sont déclenchées dans les cas suivants :

- **Modèles de requête et d’accès à la base de données anormaux**, par exemple, un nombre anormalement élevé d’échecs de tentatives de connexion avec des informations d’identification différentes (tentative d’attaque par force brute)
- **Activités suspectes de la base de données**, par exemple quand un utilisateur légitime accède à un ordinateur SQL Server à partir d’un ordinateur compromis qui a communiqué avec un serveur C&C à exploration crypto
- **Attaques par force brute**, avec la possibilité de distinguer les attaques par force brute simple, sur un utilisateur valide ou réussies

> [!TIP]
> Affichez la liste complète des alertes de sécurité pour les serveurs de base de données [dans la page de référence des alertes](alerts-reference.md#alerts-osrdb).



## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert Azure Defender pour les bases de données relationnelles open source.

> [!div class="nextstepaction"]
> [Activer Azure Defender](enable-azure-defender.md)
