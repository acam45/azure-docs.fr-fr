---
title: Utiliser les modèles Azure Resource Manager dans Azure Stack | Microsoft Docs
description: Découvrez comment utiliser les modèles Azure Resource Manager dans Azure Stack pour approvisionner des ressources.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: 38e85ede1e984aa3d26fe509d68b4582d11b6c26
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636218"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Utiliser les modèles Azure Resource Manager dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez utiliser les modèles Azure Resource Manager pour déployer et provisionner toutes les ressources de votre application en une seule opération coordonnée. Vous pouvez également redéployer des modèles pour apporter des changements aux ressources d’un groupe de ressources.

Ces modèles peuvent être déployés à l’aide du portail Microsoft Azure Stack, de PowerShell, de l’interface de ligne de commande et de Visual Studio.

Les modèles de démarrage rapide suivants sont disponibles sur [GitHub](https://aka.ms/azurestackgithub) :

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Déployer SharePoint Server (déploiement sans haute disponibilité)

Utilisez l’extension DSC PowerShell pour [créer une batterie de serveurs SharePoint Server 2013](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha) comprenant les ressources suivantes :

* Un réseau virtuel
* Trois comptes de stockage
* Deux équilibreurs de charge externes
* Une machine virtuelle configurée comme contrôleur de domaine dans une nouvelle forêt avec un domaine unique
* Une machine virtuelle configurée sous la forme d’un serveur autonome SQL Server 2014
* Une machine virtuelle configurée comme batterie de serveurs SharePoint Server 2013 composée d’une seule machine

## <a name="deploy-ad-non-high-availability-deployment"></a>Déployer AD (déploiement sans haute disponibilité)

Utilisez l’extension PowerShell DSC pour [créer un serveur de contrôleurs de domaine AD](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha) comprenant les ressources suivantes :

* Un réseau virtuel
* Un compte de stockage
* Un équilibreur de charge externe
* Une machine virtuelle configurée comme contrôleur de domaine dans une nouvelle forêt avec un domaine unique

## <a name="deploy-adsql-non-high-availability-deployment"></a>Déployer AD/SQL (déploiement sans haute disponibilité)

Utilisez l’extension PowerShell DSC pour [créer un serveur autonome SQL Server 2014](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha) comprenant les ressources suivantes :

* Un réseau virtuel
* deux comptes de stockage ;
* Un équilibreur de charge externe
* Une machine virtuelle configurée comme contrôleur de domaine dans une nouvelle forêt avec un domaine unique
* Une machine virtuelle configurée sous la forme d’un serveur autonome SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Utilisez l’extension PowerShell DSC pour configurer un gestionnaire local de configuration (LCM) de machines virtuelles existant et enregistrez-le dans un serveur Pull DSC de compte Azure Automation.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Création d’une machine virtuelle à partir d’une image utilisateur

[Créez une machine virtuelle à partir d’une image utilisateur personnalisée](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-from-user-image). Ce modèle déploie également un réseau virtuel (avec DNS), une adresse IP publique et une interface réseau.

## <a name="basic-virtual-machine"></a>Machine virtuelle classique

[Déployez une machine virtuelle Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm) comprenant un réseau virtuel (avec DNS), une adresse IP publique et une interface réseau.

## <a name="cancel-a-running-template-deployment"></a>Annuler le déploiement d’un modèle en cours d’exécution

Pour annuler le déploiement d’un modèle en cours d’exécution, utilisez la cmdlet PowerShell [Stop-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment).

## <a name="next-steps"></a>Étapes suivantes

* [Déployer des modèles avec le portail](azure-stack-deploy-template-portal.md)
* [Présentation d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
