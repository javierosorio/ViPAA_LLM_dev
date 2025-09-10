### data_raw/noche_niebla_scraping.ipynb_v1 
scrapes data from the Noche y Niebla database (https://base.nocheyniebla.org/casos).

**Description**<br>
The script automatically queries the database for cases in a given date range.<br>
Default range: 1 December 1999 - 31 December 2024<br>
(You can change this in the overall_start / overall_end variables).<br>
To avoid exceeding the server’s row limit (≈2000 cases per query), the script splits the scraping into ~2-month intervals, then merges the results.<br>

**Output**<br>
By default, results are appended into a single CSV file: "nocheyniebla_casos_1999_2024.csv"<br>
Alternatively, you can save quarterly CSVs in casos_quarterly_csvs/ by setting: APPEND_TO_SINGLE_CSV = False<br>

**Data format**<br>
The output preserves the original table headers from the website:<br>
Fecha del hecho<br>
Ubicaciones<br>
P. Responsables<br>
Tipificación<br>
Víctimas<br>
Descripción<br>
Acciones<br>

**Reproducibility**<br>
To reproduce results, follow the step-by-step comments inside the notebook.<br>
The script uses:<br>
requests + BeautifulSoup for scraping<br>
pandas for data storage/export<br>
datetime for date range handling<br>

