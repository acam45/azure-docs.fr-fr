---
title: Accéder aux journaux du serveur dans Azure Database for MariaDB à l’aide d’Azure CLI
description: Cet article décrit comment accéder aux journaux du serveur dans Azure Database pour MariaDB à l’aide de l’utilitaire de ligne de commande Azure CLI.
services: mariadb
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/10/2018
ms.openlocfilehash: b9ae07b88164a830598db791d61b77a6abb7b1df
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516449"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurer et accéder aux journaux du serveur à l’aide d’Azure CLI
Vous pouvez télécharger les journaux du serveur Azure Database for MariaDB à l’aide d’Azure CLI, l’utilitaire de ligne de commande Azure.

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- [Serveur Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) ou Azure Cloud Shell dans le navigateur

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Configuration de la journalisation pour Azure Database for MariaDB
Vous pouvez configurer le serveur afin d’accéder au journal des requêtes lentes MariaDB, comme suit :
1. Activez la journalisation en définissant le paramètre **journal\_des requêtes\_lentes** sur ON.
2. Réglez les autres paramètres tels que **durée\_longue\_d’une requête**  et  **instructions\_admin\_lentes\_du journal**.

Pour savoir comment définir la valeur de ces paramètres via Azure CLI, consultez [Comment configurer les paramètres du serveur](howto-configure-server-parameters-cli.md).

Par exemple, la commande CLI suivante active le journal des requêtes lentes, définit la durée de requête longue sur 10 secondes et désactive la journalisation de l’instruction admin lente. Enfin, elle répertorie les options de configuration à vérifier.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Répertorier les journaux de serveur pour Azure Database for MariaDB
Pour répertorier les fichiers journaux disponibles pour votre serveur, exécutez la commande [az mariadb server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list).

Vous pouvez répertorier les fichiers journaux pour le serveur **mydemoserver.mariadb.database.azure.com** sous le groupe de ressources **myresourcegroup**. Puis, dirigez la liste des fichiers journaux vers un fichier texte appelé **log\_files\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Télécharger des journaux à partir du serveur
La commande [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) permet de télécharger des fichiers journaux individuels pour votre serveur.

Utilisez l’exemple suivant pour télécharger le fichier journal spécifique pour le serveur **mydemoserver.mariadb.database.azure.com** sous le groupe de ressources **myresourcegroup** dans votre environnement local.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [journaux du serveur dans Azure Database for MariaDB](concepts-server-logs.md).
