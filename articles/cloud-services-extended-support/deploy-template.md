---
title: Déployer un service cloud Azure (support étendu) - Modèles
description: Déployer un service cloud Azure (support étendu) à l’aide de modèles ARM
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 6d54216d8992b5bb233c79919284f96b24385651
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865585"
---
# <a name="deploy-a-cloud-service-extended-support-using-arm-templates"></a>Déployer un service cloud (support étendu) à l’aide de modèles ARM

Ce tutoriel explique comment créer un déploiement d’un service cloud (support étendu) à l’aide de [modèles ARM](../azure-resource-manager/templates/overview.md). 

> [!IMPORTANT]
> Cloud Services (support étendu) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="before-you-begin"></a>Avant de commencer

1. Consultez les [prérequis du déploiement](deploy-prerequisite.md) de Cloud Services (support étendu) et créez les ressources associées.

2. Créez un groupe de ressources à l’aide du [portail Azure](/azure/azure-resource-manager/management/manage-resource-groups-portal) ou de [PowerShell](/azure/azure-resource-manager/management/manage-resource-groups-powershell). Cette étape est facultative si vous utilisez un groupe de ressources existant.
 
3. Créez un compte de stockage en utilisant le [portail Azure](/azure/storage/common/storage-account-create?tabs=azure-portal) ou [PowerShell](/azure/storage/common/storage-account-create?tabs=azure-powershell). Cette étape est facultative si vous utilisez un compte de stockage existant.

4. Chargez vos fichiers de définition de service (.csdef) et de configuration de service (.cscfg) dans le compte de stockage en utilisant le [portail Azure](/azure/storage/blobs/storage-quickstart-blobs-portal#upload-a-block-blob), [AzCopy](/azure/storage/common/storage-use-azcopy-blobs-upload?toc=/azure/storage/blobs/toc.json) ou [PowerShell](/azure/storage/blobs/storage-quickstart-blobs-powershell#upload-blobs-to-the-container). Obtenez les URI SAS des deux fichiers à ajouter au modèle ARM plus loin dans ce tutoriel.

5. (Facultatif) Créez un coffre de clés et chargez les certificats.

    -  Les certificats peuvent être joints aux services cloud pour sécuriser les communications à destination et en provenance du service. Pour que des certificats puissent être utilisés, leurs empreintes numériques doivent être spécifiées dans votre fichier de configuration de service (.cscfg) et chargées dans un coffre de clés. Il est possible de créer un coffre de clés par le biais du [portail Azure](/azure/key-vault/general/quick-create-portal) ou de [PowerShell](/azure/key-vault/general/quick-create-powershell).
    - Le coffre de clés associé doit se trouver dans la même région et le même abonnement que le service cloud.
    - Les autorisations appropriées doivent être activées pour le coffre de clés associé afin que la ressource Cloud Services (support étendu) puisse récupérer le certificat à partir de Key Vault. Pour plus d’informations, consultez [Certificats et coffre de clés](certificates-and-key-vault.md)
    - Le coffre de clés doit être référencé dans la section OsProfile du modèle ARM présenté dans les étapes ci-dessous.

## <a name="deploy-a-cloud-service-extended-support"></a>Déployer un service cloud (support étendu)

> [!NOTE]
> Le [portail Azure](https://portal.azure.com) est un moyen plus simple et plus rapide de générer votre modèle ARM et le fichier de paramètres. Vous pouvez [télécharger le modèle ARM généré](generate-template-portal.md) par le biais du portail pour créer votre service cloud via PowerShell
 
1. Créez un réseau virtuel. Le nom du réseau virtuel doit correspondre aux références figurant dans le fichier de configuration de service (.cscfg). Si vous utilisez un réseau virtuel existant, omettez cette section dans le modèle ARM.

    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/virtualNetworks", 
          "name": "[parameters('vnetName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "addressSpace": { 
              "addressPrefixes": [ 
                "10.0.0.0/16" 
              ] 
            }, 
            "subnets": [ 
              { 
                "name": "WebTier", 
                "properties": { 
                  "addressPrefix": "10.0.0.0/24" 
                } 
              } 
            ] 
          } 
        } 
    ] 
    ```
    
     Si vous créez un réseau virtuel, ajoutez ce qui suit à la section `dependsOn` pour garantir que la plateforme crée le réseau virtuel avant de créer le service cloud.

    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]" 
     ] 
    ```
 
2. Créez une adresse IP publique et (éventuellement) définissez sa propriété d’étiquette DNS. Si vous utilisez une adresse IP statique, vous devez la référencer comme adresse IP réservée dans le fichier de configuration de service (.cscfg). Si vous utilisez une adresse IP existante, ignorez cette étape et ajoutez les informations d’adresse IP directement dans les paramètres de configuration de l’équilibreur de charge de votre modèle ARM.
 
    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/publicIPAddresses", 
          "name": "[parameters('publicIPName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "publicIPAllocationMethod": "Dynamic", 
            "idleTimeoutInMinutes": 10, 
            "publicIPAddressVersion": "IPv4", 
            "dnsSettings": { 
              "domainNameLabel": "[variables('dnsName')]" 
            } 
          }, 
          "sku": { 
            "name": "Basic" 
          } 
        } 
    ] 
    ```
     
     Si vous créez une adresse IP, ajoutez ce qui suit à la section `dependsOn` pour garantir que la plateforme crée l’adresse IP avant de créer le service cloud.
    
    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
          ] 
    ```
 
3. Créez un objet de profil réseau et associez l’adresse IP publique au serveur front-end de l’équilibreur de charge. Un équilibreur de charge est automatiquement créé par la plateforme.

    ```json
    "networkProfile": { 
        "loadBalancerConfigurations": [ 
          { 
            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]", 
            "name": "[variables('lbName')]", 
            "properties": { 
              "frontendIPConfigurations": [ 
                { 
                  "name": "[variables('lbFEName')]", 
                  "properties": { 
                    "publicIPAddress": { 
                      "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
                    } 
                  } 
                } 
              ] 
            } 
          } 
        ] 
      } 
    ```
 

4. Ajoutez votre référence de coffre de clés dans la section `OsProfile` du modèle ARM. Key Vault est utilisé pour stocker les certificats associés à Cloud Services (support étendu). Ajoutez les certificats à Key Vault, puis référencez leurs empreintes numériques dans le fichier de configuration de service (.cscfg). Vous devez également activer les « Stratégies d’accès » Key Vault pour « Machines virtuelles Azure pour le déploiement » (dans le portail) afin que la ressource Cloud Services (support étendu) puisse récupérer les certificats stockés sous forme de secrets à partir de Key Vault. Le coffre de clés doit se trouver dans la même région et le même abonnement que le service cloud et avoir un nom unique. Pour plus d’informations, consultez l’[utilisation de certificats avec Cloud Services (support étendu)](certificates-and-key-vault.md).
     
    ```json
    "osProfile": { 
          "secrets": [ 
            { 
              "sourceVault": { 
                "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}" 
              }, 
              "vaultCertificates": [ 
                { 
                  "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}" 
                } 
              ] 
            } 
          ] 
        } 
    ```
  
    > [!NOTE]
    > SourceVault est l’ID de ressource ARM pour votre coffre de clés. Pour trouver ces informations, localisez l’ID de ressource dans la section Propriétés de votre coffre de clés.
    > - certificateUrl est accessible en accédant au certificat dans le coffre de clés intitulé **Identificateur de secret**.  
   >  - certificateUrl doit se présenter sous la forme https://{keyvault-endpoint}/secrets/{secretname}/{secret-id}

5. Créez un profil de rôle. Vérifiez que le nombre de rôles, le nom des rôles, le nombre d’instances dans chaque rôle et les tailles sont identiques dans la configuration de service (.cscfg), la définition de service (.csdef) et la section de profil de rôle du modèle ARM.
    
    ```json
    "roleProfile": {
      "roles": {
        "value": [
          {
            "name": "WebRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            }
          },
          {
            "name": "WorkerRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            } 
          } 
        ]
      }
    }   
    ```

6. (Facultatif) Créez un profil d’extension pour ajouter des extensions à votre service cloud. Pour cet exemple, nous ajoutons l’extension de diagnostic Windows Azure et de bureau à distance.
   > [!Note] 
   > Le mot de passe pour le bureau à distance doit comprendre entre 8 et 123 caractères, et doit remplir au moins trois critères de complexité de mot de passe parmi les suivants : 1) Contenir un caractère majuscule 2) Contenir un caractère minuscule 3) Contenir un chiffre 4) Contenir un caractère spécial 5) Les caractères de contrôle ne sont pas autorisés

    ```json
        "extensionProfile": {
          "extensions": [
            {
              "name": "RDPExtension",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Windows.Azure.Extensions",
                "type": "RDP",
                "typeHandlerVersion": "1.2.1",
                "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
              }
            },
            {
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Azure.Diagnostics",
                "type": "PaaSDiagnostics",
                "typeHandlerVersion": "1.5",
                "settings": "[parameters('wadPublicConfig_WebRole1')]",
                "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                "rolesAppliedTo": [
                  "WebRole1"
                ]
              }
            }
          ]
        }
    ```

7. Passez en revue l’ensemble du modèle.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "cloudServiceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the cloud service"
          }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Location of the cloud service"
          }
        },
        "deploymentLabel": {
          "type": "string",
          "metadata": {
            "description": "Label of the deployment"
          }
        },
        "packageSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the CSPKG file to deploy"
          }
        },
        "configurationSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the service configuration (.cscfg)"
          }
        },
        "roles": {
          "type": "array",
          "metadata": {
            "description": "Roles created in the cloud service application"
          }
        },
        "wadPublicConfig_WebRole1": {
          "type": "string",
          "metadata": {
             "description": "Public configuration of Windows Azure Diagnostics extension"
          }
        },
        "wadPrivateConfig_WebRole1": {
          "type": "securestring",
          "metadata": {
            "description": "Private configuration of Windows Azure Diagnostics extension"
          }
        },
        "vnetName": {
          "type": "string",
          "defaultValue": "[concat(parameters('cloudServiceName'), 'VNet')]",
          "metadata": {
            "description": "Name of vitual network"
          }
        },
        "publicIPName": {
          "type": "string",
          "defaultValue": "contosocsIP",
          "metadata": {
            "description": "Name of public IP address"
          }
        },
        "upgradeMode": {
          "type": "string",
          "defaultValue": "Auto",
          "metadata": {
            "UpgradeMode": "UpgradeMode of the CloudService"
          }
        }
      },
      "variables": {
        "cloudServiceName": "[parameters('cloudServiceName')]",
        "subscriptionID": "[subscription().subscriptionId]",
        "dnsName": "[variables('cloudServiceName')]",
        "lbName": "[concat(variables('cloudServiceName'), 'LB')]",
        "lbFEName": "[concat(variables('cloudServiceName'), 'LBFE')]",
        "resourcePrefix": "[concat('/subscriptions/', variables('subscriptionID'), '/resourceGroups/', resourceGroup().name, '/providers/')]"
      },
      "resources": [
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('vnetName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "10.0.0.0/16"
              ]
            },
            "subnets": [
              {
                "name": "WebTier",
                "properties": {
                  "addressPrefix": "10.0.0.0/24"
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[parameters('publicIPName')]",
          "location": "[parameters('location')]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "idleTimeoutInMinutes": 10,
            "publicIPAddressVersion": "IPv4",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName')]"
            }
          },
          "sku": {
            "name": "Basic"
          }
        },
        {
          "apiVersion": "2020-10-01-preview",
          "type": "Microsoft.Compute/cloudServices",
          "name": "[variables('cloudServiceName')]",
          "location": "[parameters('location')]",
          "tags": {
            "DeploymentLabel": "[parameters('deploymentLabel')]",
            "DeployFromVisualStudio": "true"
          },
          "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
          ],
          "properties": {
            "packageUrl": "[parameters('packageSasUri')]",
            "configurationUrl": "[parameters('configurationSasUri')]",
            "upgradeMode": "[parameters('upgradeMode')]",
            "roleProfile": {
              "roles": [
                {
                  "name": "WebRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                },
                {
                  "name": "WorkerRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                }
              ]
            },
            "networkProfile": {
              "loadBalancerConfigurations": [
                {
                  "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]",
                  "name": "[variables('lbName')]",
                  "properties": {
                    "frontendIPConfigurations": [
                      {
                        "name": "[variables('lbFEName')]",
                        "properties": {
                          "publicIPAddress": {
                            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
                          }
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "osProfile": {
              "secrets": [
                {
                  "sourceVault": {
                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}"
                  },
                  "vaultCertificates": [
                    {
                      "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}"
                    }
                  ]
                }
              ]
            },
            "extensionProfile": {
              "extensions": [
                {
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                    "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
                  }
                },
                {
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "PaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "settings": "[parameters('wadPublicConfig_WebRole1')]",
                    "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                    "rolesAppliedTo": [
                      "WebRole1"
                  ]
                }
              }
            ]
          }
        }
       }
      ]
    }
    ```

8. Déployez le fichier de modèle et de paramètres (définition des paramètres dans le fichier de modèle) pour créer le déploiement de service cloud (prise en charge étendue). Veuillez vous reporter à ces [exemples de modèles](https://github.com/Azure-Samples/cloud-services-extended-support) si nécessaire.

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "ContosOrg" -TemplateFile "file path to your template file" -TemplateParameterFile "file path to your parameter file"
    ```

## <a name="next-steps"></a>Étapes suivantes 

- Consultez les [questions fréquentes (FAQ)](faq.md) concernant Cloud Services (support étendu).
- Déployez un service cloud (support étendu) avec le [portail Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), un [modèle](deploy-template.md) ou [Visual Studio](deploy-visual-studio.md).
- Visitez le [dépôt d’exemples de Cloud Services (support étendu)](https://github.com/Azure-Samples/cloud-services-extended-support).
