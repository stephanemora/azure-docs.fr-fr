---
title: Scénarios courants dans la gestion des droits d’utilisation Azure AD (préversion) - Azure Active Directory
description: Découvrez les étapes de niveau supérieur à suivre pour les scénarios courants dans la gestion des droits d’utilisation Azure AD (préversion).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27d54d51a16f4ed94f24f9afa005c63b22778cf9
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190363"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Scénarios courants dans la gestion des droits d’utilisation Azure AD (préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il y a plusieurs façons de configurer la gestion des droits d’utilisation pour votre organisation. Toutefois, si vous débutez, il est utile de comprendre les scénarios courants pour les administrateurs, les approbateurs et les demandeurs.

## <a name="administrators"></a>Administrateurs

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Je débute dans la gestion des droits d’utilisation et j’aimerais de l’aide

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | [Suivez ce tutoriel pour créer votre premier package d’accès](entitlement-management-access-package-first.md) | [![Icône du Portail Azure](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Je souhaite autoriser les utilisateurs dans mon répertoire à demander l’accès aux groupes, applications ou sites SharePoint

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** [Créer un package d’accès dans un catalogue](entitlement-management-access-package-create.md#start-new-access-package) | ![Créer un package d’accès](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Ajouter des rôles de ressources à un package d’accès](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>Groupes</li><li>APPLICATIONS</li><li>Sites SharePoint</li></ul> | ![Ajouter des rôles de ressources](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Ajouter une stratégie](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>Pour les utilisateurs dans votre répertoire</li><li>Exiger une approbation</li><li>Paramètres d’expiration</li></ul> | ![Add policy](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Je souhaite autoriser les utilisateurs du répertoire de mes partenaires (dont les utilisateurs qui ne sont pas encore dans mon répertoire) à demander l’accès aux groupes, applications ou sites SharePoint

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** [Créer un package d’accès dans un catalogue](entitlement-management-access-package-create.md#start-new-access-package) | ![Créer un package d’accès](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Ajouter des rôles de ressources à un package d’accès](entitlement-management-access-package-edit.md#add-resource-roles) | ![Ajouter des rôles de ressources](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Ajouter une stratégie pour les utilisateurs externes](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>Pour les utilisateurs qui ne sont pas dans votre répertoire</li><li>Exiger une approbation</li><li>Paramètres d’expiration</li></ul> | ![Ajouter une stratégie pour les utilisateurs externes](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [Envoyer le lien vers le portail Mon Accès pour demander le package d’accès à votre partenaire](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>Un partenaire peut partager le lien avec ses utilisateurs</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Je souhaite modifier les groupes, les applications ou les sites SharePoint d’un package d’accès

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** Ouvrir le package d’accès | ![Ajouter des rôles de ressources](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Ajouter ou supprimer des rôles de ressource](entitlement-management-access-package-edit.md#add-resource-roles) | ![Ajouter des rôles de ressources](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Je souhaite consulter qui dispose d’une attribution aux groupes, applications ou sites SharePoint

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** Ouvrir un package d’accès | ![Ajouter des rôles de ressources](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Consulter les attributions](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>Consulter les utilisateurs ayant accès à un package d’accès</li><li>Consulter les utilisateurs dont l’accès a expiré</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Je souhaite consulter les groupes, applications ou sites SharePoint auxquels un utilisateur a accès

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | [Consulter le rapport des attributions utilisateur](entitlement-management-reports.md)<ul><li>Consulter la date de demande et qui a approuvé</li></ul> |  |

## <a name="approvers"></a>Approbateurs

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Je souhaite approuver les demandes d’accès aux groupes, applications ou sites SharePoint

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** [Ouvrir une demande dans le portail Mon Accès](entitlement-management-request-approve.md#open-request) | [![Icône du portail Mon Accès](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [Approuver la demande d’accès](entitlement-management-request-approve.md#approve-or-deny-request) | ![Approuver l’accès](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Demandeurs

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Je souhaite consulter les groupes, applications ou sites SharePoint qui me sont disponibles et y demander l’accès

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** [Se connecter au portail Mon Accès](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Icône du portail Mon Accès](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Trouver un package d’accès |  |
> | **3.** [Demander l’accès](entitlement-management-request-access.md#request-an-access-package) | ![Demander l'accès](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Je suis un utilisateur externe, et je souhaite demande l’accès aux groupes, applications ou sites SharePoint via un lien direct

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** [Trouver le lien du portail Mon Accès que vous avez reçu](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [Se connecter au portail Mon Accès](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Icône du portail Mon Accès](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [Demander l’accès](entitlement-management-request-access.md#request-an-access-package) | ![Demande d’accès d’un utilisateur externe](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Je souhaite consulter les groupes, applications ou sites SharePoint auxquels j’ai déjà accès

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** [Se connecter au portail Mon Accès](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Icône du portail Mon Accès](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Consulter les packages d’accès actifs |  |

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Créer votre premier package d’accès](entitlement-management-access-package-first.md)
- [Déléguer des tâches](entitlement-management-delegate.md)
