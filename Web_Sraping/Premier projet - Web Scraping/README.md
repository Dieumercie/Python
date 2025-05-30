# Premier projet - Web Scraping

# 1. Import des Bibliothèques

    import pandas as pd

    import requests as req

    from bs4 import BeautifulSoup

    from pprint import pprint

* pandas : Bibliothèque utilisée pour la manipulation de données sous forme de DataFrame (structure en tableau).

* requests : Permet d'envoyer des requêtes HTTP pour récupérer des informations depuis des pages web.

* BeautifulSoup : Utilisée pour parser et naviguer dans le code HTML des pages web.

* pprint : Permet d'afficher des objets Python de manière plus lisible (formatée).

# 2. Récupération du Contenu Web

    url = "https://en.wikipedia.org/wiki/List_of_countries_by_population_(United_Nations)"

    response = req.get(url)

    soup = BeautifulSoup(response.text, "html.parser")

    print(soup.prettify())

* L'URL de la page Wikipédia est définie.

* req.get(url) : Envoie une requête HTTP GET pour récupérer le contenu de la page.

* BeautifulSoup(response.text, "html.parser") : Parse le contenu HTML de la page récupérée.

* soup.prettify() : Affiche le contenu HTML de la page de manière lisible (formatée).

# 3. Extraction des En-têtes de Table

    data = soup.find("table").find_all("th")
    print(data)

* soup.find("table") : Trouve le premier élément <table> dans le code HTML de la page.

* find_all("th") : Trouve tous les éléments th (cellules d'en-tête) dans cette table.

* print(data) : Affiche toutes les cellules d'en-tête extraites.

# 4. Nettoyage et Extraction des En-têtes de Table

    headers = [header.text.strip() for header in data]
    print(headers)

* header.text.strip() : Extrait le texte de chaque en-tête de table et enlève les espaces avant et après.

* headers : Liste contenant tous les en-têtes nettoyés.

* print(headers) : Affiche la liste des en-têtes.

# 5. Création du DataFrame

    df = pd.DataFrame(columns=headers)
    df

Crée un DataFrame df avec les en-têtes extraites comme noms de colonnes.

![Image](https://github.com/user-attachments/assets/1d146911-3589-4009-87ee-f5dea6e6424c)

# 6. Récupération des Données du Corps de la Table

    rows_data = soup.find("table").find_all('tr')

    pprint(rows_data)

* soup.find("table").find_all('tr') : Trouve toutes les lignes (tr) dans la table.

* pprint(rows_data) : Affiche toutes les lignes de la table.

# 7. Extraction et Ajout des Données au DataFrame

    for row in rows_data[1:]: 

    my_row = row.find_all('td')

    individual_row_data = [data1.text.strip() for data1 in my_row]

    length = len(df)

    df.loc[length] = individual_row_data

* rows_data[1:] : Ignore la première ligne qui contient les en-têtes.

* row.find_all('td') : Trouve toutes les cellules de données (td) dans chaque ligne.

* individual_row_data : Liste contenant les données de chaque cellule, nettoyées des espaces inutiles.

* df.loc[length] = individual_row_data : Ajoute une nouvelle ligne de données au DataFrame df.

![Image](https://github.com/user-attachments/assets/3903f1d8-c0d0-4330-bd1e-8f9601fd8c3e)
