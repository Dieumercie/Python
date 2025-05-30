# Amazon_analysis_pandas

    import pandas as pd

    amazon_data=pd.read_csv('C:\\Users\\XXXX\\Downloads\\amazon\\amazon.csv', sep=",")
    print(amazon_data)
Charge le fichier CSV contenant les données d'Amazon dans un DataFrame amazon_data et afficher les lignes du jeu de données.

    amazon_data.dropna(inplace=True)
Supprime toutes les lignes contenant des valeurs NaN (valeurs manquantes).

    amazon_data["discounted_price"] = (
    amazon_data["discounted_price"].astype(str)  # Convertir en string
    .str.replace(r"[^\d.]", "", regex=True)  # Supprimer les caractères spéciaux
    .astype(float)  # Convertir en float
    )
Convertit les prix réduits en str, nettoie les caractères spéciaux (comme ₹ ou ,), puis les reconvertit en float sur une colonne.

    cols_to_clean = ["discounted_price", "actual_price"]
    amazon_data[cols_to_clean] = amazon_data[cols_to_clean].astype(str).apply(lambda x: x.str.replace(r"[^\d.]", "", regex=True)).astype(float)
Applique le même nettoyage que précédemment, mais sur deux colonnes à la fois (discounted_price et actual_price).

    delimiters = r"[|,/&]+"  
    amazon_data_split=amazon_data["category"].str.split(delimiters,n=2, expand=True)
    amazon_data["Category1"] = amazon_data_split[0]
    amazon_data["Sub_Category"] = amazon_data_split[1]
Sépare la colonne category en deux nouvelles colonnes :
      Category1 : Catégorie principale.
      Sub_Category : Sous-catégorie.
Utilise des délimiteurs comme |, / ou &.

    amazon_data.drop(["category"], axis=1, inplace=True)
Supprime la colonne category, car elle est remplacée par Category1 et Sub_Category.

    amazon_data["rating"] = amazon_data["rating"].astype(str).str.replace(r"[^\d.]", "", regex=True)
    amazon_data["rating"] = pd.to_numeric(amazon_data["rating"], errors="coerce")

Nettoie la colonne rating en supprimant les caractères non numériques.
Convertit la colonne en valeurs numériques, en remplaçant les erreurs par NaN.

    user_name_split=amazon_data["user_name"].str.split(',', n=1, expand=True)
    print(user_name_split)
Sépare la colonne user_name en deux parties, en prenant le premier élément avant la virgule.

    amazon_data["user_name1"] = user_name_split[0]
    amazon_data.drop(["user_name"], axis=1, inplace=True)
Stocke la première partie du nom d'utilisateur dans user_name1 et supprime l’ancienne colonne user_name.

    amazon_data.rename(columns={"user_name1": "user_name", "Category1": "category"}, inplace=True)
Renomme user_name1 en user_name et Category1 en category.

    result = amazon_data.groupby("category")["product_id"].nunique().reset_index()
    result.columns = ["category", "distinct_product_count"]
    display(result)
Compte le nombre de produits distincts (product_id) dans chaque catégorie.

    product_category_bar = result.plot.barh(x='category', y='distinct_product_count', rot=0)
Génère un graphique en barres horizontales représentant le nombre de produits distincts par catégorie.

    revenue_per_category=amazon_data.groupby(by=["category"])[["discounted_price","actual_price"]].sum().reset_index()
    revenue_per_category.columns=["category", "Revenue", "first_price"]
    display(revenue_per_category)
Calcule la somme totale des prix réduits (discounted_price) et des prix d'origine (actual_price) par catégorie.
Renomme discounted_price en Revenue et actual_price en first_price.

    ax = revenue_per_category.plot.bar(x='category')
Génère un graphique en barres pour afficher les revenus par catégorie.

    most_expensive_product = amazon_data.loc[amazon_data["discounted_price"].idxmax(), ["product_name", "discounted_price"]]
    display(most_expensive_product)
Identifie le produit le plus cher en fonction de discounted_price et l'affiche.

    best_rated_product = amazon_data.loc[amazon_data["rating"].idxmax(), ["product_name", "rating"]]
    display(best_rated_product)
Identifie le produit avec la meilleure note et l'affiche.

    users = amazon_data.groupby(by=["user_name"])["product_id"].nunique().reset_index()
    users.columns = ["user name", "distinct_product_count"]
    display(users)
Compte le nombre de produits distincts achetés par chaque utilisateur.

    best_buyers = users.loc[users["distinct_product_count"].idxmax(), ["user name","distinct_product_count"]]
    display(best_buyers)
Trouve l’utilisateur qui a acheté le plus de produits distincts.

