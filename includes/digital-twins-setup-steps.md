---
author: baanders
description: Fichier include pour la vue d’ensemble des étapes dans le programme d’installation d’Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b8751685cfd273171cb8a5b500e4bed41ce2f59e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407520"
---
>[!NOTE]
>Ces opérations sont destinées à être effectuées par un utilisateur titulaire d’un rôle *Propriétaire* sur l’abonnement Azure. Bien que certaines opérations puissent être accomplies sans cette autorisation élevée, la coopération d’un propriétaire est requise pour configurer complètement une instance utilisable. Pour plus d’informations à ce sujet, consultez la section [*Conditions préalables : autorisations requises*](#prerequisites-permission-requirements) section ci-dessous.

La configuration complète d’une nouvelle instance Azure Digital Twins se déroule en trois phases :
1. **Création de l’instance**
2. **Configuration d’autorisations d’accès utilisateur** : les utilisateurs Azure doivent avoir le rôle *Propriétaire Azure Digital Twins (préversion)* sur l’instance Azure Digital Twins pour pouvoir gérer celle-ci et ses données. Au cours de cette étape, en tant que Propriétaire dans l’abonnement Azure, vous allez attribuer ce rôle à la personne qui doit gérer votre instance Azure Digital Twins. Il peut s’agir de vous-même ou d’une autre personne au sein de votre organisation.
3. **Configuration des autorisations d’accès pour les applications clientes** : Il est courant d’écrire une application cliente qui sera utilisée pour interagir avec votre instance. Pour que l’application cliente puisse accéder à votre service Azure Digital Twins, vous devez configurer une *inscription d’application* dans [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md), que l’application cliente utilisera pour s’authentifier auprès de l’instance.

Pour poursuivre, vous allez avoir besoin d’un abonnement Azure. Vous pouvez en configurer un gratuitement [ici](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
