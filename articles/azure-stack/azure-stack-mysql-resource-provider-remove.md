---
title: Suppression du fournisseur de ressources MySQL sur Azure Stack | Microsoft Docs
description: Découvrez comment vous pouvez supprimer le fournisseur de ressources MySQL de votre déploiement Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 7d3b0e179972464a1ed857c576ca8a7c8fc2e162
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686794"
---
# <a name="remove-the-mysql-resource-provider"></a>Supprimer le fournisseur de ressources MySQL

Avant de supprimer le fournisseur de ressources MySQL, vous devez supprimer toutes les dépendances de fournisseurs. Vous aurez également besoin d’une copie du package de déploiement qui a été utilisé pour installer le fournisseur de ressources.

  |Version minimale d’Azure Stack|Version du fournisseur de ressources MySQL|
  |-----|-----|
  |Version 1808 (1.1808.0.97)|[MySQL RP version 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
  |Version 1804 (1.0.180513.1)|[Version 1.1.24.0 du fournisseur de ressources MySQL](https://aka.ms/azurestackmysqlrp11240)
  |     |     |

## <a name="dependency-cleanup"></a>Nettoyage de la dépendance

Vous devez effectuer plusieurs tâches de nettoyage avant d’exécuter le script DeployMySqlProvider.ps1 pour supprimer le fournisseur de ressources.

Les utilisateurs d'Azure Stack sont responsables des tâches de nettoyage suivantes :

* Supprimer toutes les bases de données du fournisseur de ressources. (La suppression des bases de données de locataires ne supprime pas les données.)
* Annuler l’inscription auprès de l’espace de noms du fournisseur.

L'opérateur d'Azure Stack est responsable des tâches de nettoyage suivantes :

* Supprimer les serveurs d’hébergement de l’adaptateur MySQL.
* Supprimer tous les plans qui référencent l’adaptateur MySQL.
* Supprimer tous les quotas associés au fournisseur de ressources MySQL.

## <a name="to-remove-the-mysql-resource-provider"></a>Pour supprimer le fournisseur de ressources MySQL

1. Vérifiez que vous avez supprimé toutes les dépendances existantes du fournisseur de ressources MySQL.

   >[!NOTE]
   >La désinstallation du fournisseur de ressources MySQL est effectuée même si des ressources dépendantes utilisent le fournisseur de ressources.
  
2. Obtenez une copie du binaire du fournisseur de ressources MySQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire.
3. Obtenez une copie du binaire du fournisseur de ressources SQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire.
4. Ouvrez une nouvelle fenêtre de console PowerShell avec élévation de privilèges et basculez vers le répertoire où vous avez extrait les fichiers binaires du fournisseur de ressources MySQL.
5. Exécutez le script DeployMySqlProvider.ps1 à l’aide des paramètres suivants :
    - **Désinstaller**. Supprime le fournisseur de ressources et toutes les ressources associées.
    - **PrivilegedEndpoint**. Adresse IP ou nom DNS du point de terminaison privilégié.
    - **AzureEnvironment**. L’environnement Azure utilisé pour le déploiement d’Azure Stack. Nécessaire uniquement pour les déploiements Azure AD.
    - **CloudAdminCredential**. Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié.
    - **DirectoryTenantID**
    - **AzCredential**. Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack.

## <a name="next-steps"></a>Étapes suivantes

[Offrir App Services en tant que PaaS](azure-stack-app-service-overview.md)
