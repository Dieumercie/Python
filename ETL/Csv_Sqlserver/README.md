# ETL_csv_python_sqlserver

1. Installation et importation des bibliothèques

       !pip install sqlalchemy
    Installe la bibliothèque SQLAlchemy, utilisée pour interagir avec des bases de données relationnelles en Python.

       import pandas as pd
    Importe la bibliothèque Pandas, qui est utilisée pour manipuler et analyser des données sous forme de DataFrames.

       import requests as req
    Importe la bibliothèque Requests, utilisée pour envoyer des requêtes HTTP (pas utilisée dans ce script).

       import sqlalchemy as sa
    Importe SQLAlchemy pour la gestion des bases de données relationnelles en Python.

2. Chargement des données

       dataset = pd.read_csv(r"C:\Users\xxxx\Downloads\supply_chain_data.csv", sep=",")
    Charge le fichier CSV contenant les données de la chaîne d'approvisionnement dans un DataFrame Pandas.

       print(dataset)
    Affiche le contenu du DataFrame.

       dataset.info()
    Affiche des informations sur la structure du DataFrame, y compris les types de données et les valeurs manquantes.

3. Connexion à la base de données et insertion des données

       engine = sa.create_engine('mssql+pyodbc://./ETL_test_db?driver=ODBC+Driver+17+for+SQL+Server')
    Crée une connexion à une base de données SQL Server appelée ETL_test_db via ODBC.

       dataset.to_sql(name = 'supply_chain', con=engine, index=False, if_exists='fail')
    Exporte le DataFrame Pandas vers la base de données sous la table supply_chain.
        index=False : Exclut l’index du DataFrame.
        if_exists='fail' : Ne crée pas la table si elle existe déjà.

4. Requêtes SQL pour analyser les données

       USE ETL_test_db
    Sélectionne la base de données ETL_test_db pour exécuter les requêtes.

Lecture des données

    SELECT TOP (1000) * FROM [ETL_test_db].[dbo].[supply_chain]
    Sélectionne les 1000 premières lignes de la table supply_chain.

    SELECT * FROM INFORMATION_SCHEMA.COLUMNS
    Liste toutes les colonnes de la base de données, utile pour comprendre la structure des tables.

Analyses des quantités et revenus
 
    Nombre de produits par type de produit

    SELECT [Product type], COUNT([SKU]) AS Quantity_number
    FROM supply_chain
    GROUP BY [Product type]

Compte le nombre de références produits (SKU) pour chaque type de produit.

Nombre total de ventes et revenus générés par type de produit

    SELECT [Product type], SUM([Number of products sold]) AS Quantity_sold, SUM([Revenue generated]) AS Revenu
    FROM supply_chain
    GROUP BY [Product type]

    Agrège le total des ventes et des revenus par type de produit.

Analyse des commandes par localisation

    Quantité totale de commandes par lieu

    SELECT [Location], SUM([Order quantities]) AS TotalOrderQuantities
    FROM supply_chain 
    GROUP BY [Location]
    ORDER BY TotalOrderQuantities DESC;

Affiche les lieux avec le nombre total de commandes trié par ordre décroissant.

    Lieu avec le plus grand nombre de commandes

    SELECT [Location], TotalOrderQuantities
    FROM (
    SELECT [Location], SUM([Order quantities]) AS TotalOrderQuantities
    FROM supply_chain
    GROUP BY [Location]
    ) AS OrderSummary
    WHERE TotalOrderQuantities = (
    SELECT MAX(SumOrderQuantities)
    FROM (
        SELECT SUM([Order quantities]) AS SumOrderQuantities
        FROM supply_chain
        GROUP BY [Location]
    ) AS MaxOrder
    );

Sélectionne l'emplacement avec le plus grand nombre de commandes.

    Revenu total par localisation

    SELECT [Location], SUM([Revenue generated]) AS TotalRevenue
    FROM supply_chain
    GROUP BY [Location]
    ORDER BY TotalRevenue DESC;

    Affiche les revenus générés par localisation.

Analyse des coûts et défauts de fabrication

    Coûts de fabrication par produit

    SELECT [SKU], [Product type], [Manufacturing costs]
    FROM supply_chain
    ORDER BY [Manufacturing costs] DESC;

Trie les produits par coût de fabrication décroissant.

    Taux de défaut moyen par fournisseur

    SELECT [Supplier name], AVG([Defect rates]) AS AvgDefectRate
    FROM supply_chain
    GROUP BY [Supplier name]
    ORDER BY AvgDefectRate ASC;

Affiche les fournisseurs classés selon leur taux de défaut moyen.

Taux de défaut moyen par type de produit

    SELECT [Product type], AVG([Defect rates]) AS AvgDefectRate
    FROM supply_chain
    GROUP BY [Product type]
    ORDER BY AvgDefectRate DESC;

    Affiche les produits ayant les taux de défaut les plus élevés.

Analyse des transports et coûts d'expédition

    Temps moyen d’expédition par transporteur

    SELECT [Shipping carriers], AVG([Shipping times]) AS AvgShippingTime
    FROM supply_chain
    GROUP BY [Shipping carriers]
    ORDER BY AvgShippingTime ASC;

Classe les transporteurs selon le temps moyen d’expédition.

    Coût moyen d’expédition par transporteur

    SELECT [Shipping carriers], AVG([Shipping costs]) AS AvgShippingCost
    FROM supply_chain
    GROUP BY [Shipping carriers]
    ORDER BY AvgShippingCost DESC;
